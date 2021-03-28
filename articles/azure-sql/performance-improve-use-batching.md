---
title: Batch verwerking gebruiken om de prestaties van toepassingen te verbeteren
description: In het onderwerp wordt beschreven hoe u met batch-database bewerkingen de snelheid en schaal baarheid van uw Azure SQL Database en Azure SQL Managed instance-toepassingen aanzienlijk verbetert. Hoewel deze batch technieken voor elke SQL database werken, is de focus van het artikel op Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: genemi
ms.date: 01/25/2019
ms.openlocfilehash: 7f45e7d1515f0d6fc4467b36d95242ef8697c75d
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105641400"
---
# <a name="how-to-use-batching-to-improve-azure-sql-database-and-azure-sql-managed-instance-application-performance"></a>Batch verwerking gebruiken om de prestaties van Azure SQL Database en Azure SQL Managed instance-toepassingen te verbeteren
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Met batch bewerkingen voor Azure SQL Database en Azure SQL Managed instance worden de prestaties en schaal baarheid van uw toepassingen aanzienlijk verbeterd. Het eerste deel van dit artikel bevat een aantal voor beelden van test resultaten waarmee opeenvolgende en batch-aanvragen worden vergeleken met een data base in Azure SQL Database of Azure SQL Managed instance om de voor delen te begrijpen. In de rest van het artikel ziet u de technieken, scenario's en aandachtspunten om u te helpen bij het gebruik van batch verwerking in uw Azure-toepassingen.

## <a name="why-is-batching-important-for-azure-sql-database-and-azure-sql-managed-instance"></a>Waarom is batching belang rijk voor Azure SQL Database en Azure SQL Managed instance?

Het batch-aanroepen van een externe service is een bekende strategie voor het verbeteren van de prestaties en schaal baarheid. Er zijn vaste verwerkings kosten voor alle interacties met een externe service, zoals serialisatie, netwerk overdracht en deserialisatie. Als u veel afzonderlijke trans acties inpakt in één batch, worden deze kosten geminimaliseerd.

In dit artikel willen we verschillende batch-strategieën en-scenario's onderzoeken. Hoewel deze strategieën ook belang rijk zijn voor on-premises toepassingen die gebruikmaken van SQL Server, zijn er verschillende redenen voor het markeren van het gebruik van batch verwerking voor Azure SQL Database en Azure SQL Managed instance:

* Er is mogelijk een grotere netwerk latentie bij het openen van Azure SQL Database en Azure SQL Managed instance, met name als u toegang hebt tot Azure SQL Database of Azure SQL Managed instance van buiten hetzelfde Microsoft Azure Data Center.
* Met de multi tenant-kenmerken van Azure SQL Database en Azure SQL Managed instance wordt de efficiëntie van de Gegevenstoegangslaag afgestemd op de algehele schaal baarheid van de data base. Als reactie op het gebruik van vooraf gedefinieerde quota overschrijdt Azure SQL Database en Azure SQL Managed instance de door voer te verminderen of te reageren met beperkings uitzonderingen. Met efficiency verbeteringen, zoals batch verwerking, kunt u meer werk doen voordat u deze limieten bereikt.
* Batch verwerking is ook effectief voor architecturen die gebruikmaken van meerdere data bases (sharding). De efficiëntie van uw interactie met elke database eenheid is nog steeds een belang rijke factor in uw totale schaal baarheid.

Een van de voor delen van het gebruik van Azure SQL Database of Azure SQL Managed instance is dat u de servers die als host fungeren voor de-data base niet hoeft te beheren. Deze beheerde infra structuur houdt er echter ook rekening mee dat u anders moet denken over database optimalisaties. Het is niet meer mogelijk om de data base-hardware of netwerk infrastructuur te verbeteren. Microsoft Azure regelt deze omgevingen. In het hoofd gebied dat u kunt bepalen hoe uw toepassing samenwerkt met Azure SQL Database en Azure SQL Managed instance. Batch verwerking is een van deze optimalisaties.

In het eerste deel van dit artikel worden verschillende batch technieken onderzocht voor .NET-toepassingen die gebruikmaken van Azure SQL Database of Azure SQL Managed instance. In de laatste twee secties worden de richt lijnen en scenario's voor batch verwerking behandeld.

## <a name="batching-strategies"></a>Batch-strategieën

### <a name="note-about-timing-results-in-this-article"></a>Opmerking over timing resultaten in dit artikel

> [!NOTE]
> Resultaten zijn geen benchmarks, maar zijn bedoeld om **relatieve prestaties** weer te geven. Tijds instellingen zijn gebaseerd op een gemiddelde van ten minste tien test uitvoeringen. Bewerkingen worden ingevoegd in een lege tabel. Deze tests werden gemeten vóór V12 en ze komen niet noodzakelijkerwijs overeen met de door Voer die u kunt ervaren in een V12-data base met behulp van de nieuwe [DTU-service lagen](database/service-tiers-dtu.md) of [vCore-service lagen](database/service-tiers-vcore.md). Het relatieve voor deel van de batch-techniek moet vergelijkbaar zijn.

### <a name="transactions"></a>Transacties

Het lijkt vreemd te beginnen met het controleren van batch verwerking door trans acties te bespreken. Maar het gebruik van trans acties aan client zijde heeft een subtiele batch-effect aan de server zijde waarmee de prestaties worden verbeterd. En trans acties kunnen met slechts een paar regels code worden toegevoegd, zodat ze een snelle manier bieden om de prestaties van sequentiële bewerkingen te verbeteren.

Bekijk de volgende C#-code die een reeks INSERT-en update-bewerkingen in een eenvoudige tabel bevat.

```csharp
List<string> dbOperations = new List<string>();
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 1");
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 2");
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 3");
dbOperations.Add("insert MyTable values ('new value',1)");
dbOperations.Add("insert MyTable values ('new value',2)");
dbOperations.Add("insert MyTable values ('new value',3)");
```

Met de volgende ADO.NET-code worden deze bewerkingen sequentieel uitgevoerd.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    conn.Open();

    foreach(string commandString in dbOperations)
    {
        SqlCommand cmd = new SqlCommand(commandString, conn);
        cmd.ExecuteNonQuery();
    }
}
```

De beste manier om deze code te optimaliseren, is door een vorm van client-side batching van deze aanroepen te implementeren. Maar er is een eenvoudige manier om de prestaties van deze code te verhogen door de volg orde van de aanroepen in een trans actie te verpakken. Dit is dezelfde code die gebruikmaakt van een trans actie.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    conn.Open();
    SqlTransaction transaction = conn.BeginTransaction();

    foreach (string commandString in dbOperations)
    {
        SqlCommand cmd = new SqlCommand(commandString, conn, transaction);
        cmd.ExecuteNonQuery();
    }

    transaction.Commit();
}
```

Trans acties worden feitelijk gebruikt in beide voor beelden. In het eerste voor beeld is elke afzonderlijke aanroep een impliciete trans actie. In het tweede voor beeld verloopt een expliciete trans actie alle aanroepen. In de documentatie voor het [transactie logboek voor schrijven](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide?view=sql-server-ver15&preserve-view=true#WAL)worden logboek records naar de schijf verwijderd wanneer de trans actie wordt doorgevoerd. Door meer aanroepen in een trans actie op te nemen, kan de schrijf bewerking naar het transactie logboek worden vertraagd totdat de trans actie is doorgevoerd. In feite schakelt u batch verwerking in voor de schrijf bewerkingen naar het transactie logboek van de server.

In de volgende tabel ziet u enkele ad hoc test resultaten. De tests hebben dezelfde sequentiële toevoegingen met en zonder trans acties uitgevoerd. Voor meer perspectief is de eerste set testen op afstand uitgevoerd vanaf een laptop naar de data base in Microsoft Azure. De tweede set tests is uitgevoerd vanuit een Cloud service en data base die zich in hetzelfde Microsoft Azure Data Center (VS-West) bevindt. De volgende tabel toont de duur in milliseconden van opeenvolgende toevoegingen met en zonder trans acties.

**On-premises naar Azure**:

| Operations | Geen trans actie (MS) | Trans actie (MS) |
| --- | --- | --- |
| 1 |130 |402 |
| 10 |1208 |1226 |
| 100 |12662 |10395 |
| 1000 |128852 |102917 |

**Azure naar Azure (hetzelfde Data Center)**:

| Operations | Geen trans actie (MS) | Trans actie (MS) |
| --- | --- | --- |
| 1 |21 |26 |
| 10 |220 |56 |
| 100 |2145 |341 |
| 1000 |21479 |2756 |

> [!NOTE]
> Resultaten zijn geen benchmarks. Zie de [Opmerking over timing resultaten in dit artikel](#note-about-timing-results-in-this-article).

Op basis van de vorige test resultaten vermindert het verpakken van een enkele bewerking in een trans actie de prestaties. Maar naarmate u het aantal bewerkingen binnen één trans actie verhoogt, wordt de prestatie verbetering gemarkeerd. Het prestatie verschil is ook merkbaarer wanneer alle bewerkingen binnen het Microsoft Azure Data Center plaatsvinden. De verbeterde latentie van het gebruik van Azure SQL Database of Azure SQL Managed instance van buiten het Microsoft Azure Data Center overschaduwt de prestatie verbetering van het gebruik van trans acties.

Hoewel het gebruik van trans acties de prestaties kan verhogen, blijven de [Aanbevolen procedures voor trans acties en verbindingen](/previous-versions/sql/sql-server-2008-r2/ms187484(v=sql.105))zien. Bewaar de trans actie zo kort mogelijk en sluit de database verbinding nadat het werk is voltooid. De instructie using in het vorige voor beeld zorgt ervoor dat de verbinding wordt gesloten wanneer het volgende code blok is voltooid.

In het vorige voor beeld ziet u dat u een lokale trans actie kunt toevoegen aan elke ADO.NET code met twee regels. Trans acties bieden een snelle manier om de prestaties te verbeteren van code die sequentiële invoeg-, bijwerk-en verwijder bewerkingen mogelijk maakt. Voor de snelste prestaties kunt u echter het beste de code wijzigen om te profiteren van batch verwerking aan de client zijde, zoals para meters voor tabel waarden.

Zie [lokale trans acties in ADO.net](/dotnet/framework/data/adonet/local-transactions)voor meer informatie over trans acties in ADO.net.

### <a name="table-valued-parameters"></a>Tabelwaardeparameters

Para meters voor tabel waarden ondersteunen door de gebruiker gedefinieerde tabel typen als para meters in Transact-SQL-instructies, opgeslagen procedures en functies. Met deze batch techniek voor client zijde kunt u meerdere rijen met gegevens binnen de tabelwaardeparameter verzenden. Als u para meters met tabel waarden wilt gebruiken, moet u eerst een tabel type definiëren. Met de volgende Transact-SQL-instructie maakt u een tabel type met de naam **MyTableType**.

```sql
    CREATE TYPE MyTableType AS TABLE
    ( mytext TEXT,
      num INT );
```

In code maakt u een **DataTable** met exact dezelfde namen en typen van het tabel type. Geef deze **DataTable** door in een para meter in een tekst query of een opgeslagen procedure aanroep. In het volgende voor beeld ziet u deze techniek:

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    connection.Open();

    DataTable table = new DataTable();
    // Add columns and rows. The following is a simple example.
    table.Columns.Add("mytext", typeof(string));
    table.Columns.Add("num", typeof(int));
    for (var i = 0; i < 10; i++)
    {
        table.Rows.Add(DateTime.Now.ToString(), DateTime.Now.Millisecond);
    }

    SqlCommand cmd = new SqlCommand(
        "INSERT INTO MyTable(mytext, num) SELECT mytext, num FROM @TestTvp",
        connection);

    cmd.Parameters.Add(
        new SqlParameter()
        {
            ParameterName = "@TestTvp",
            SqlDbType = SqlDbType.Structured,
            TypeName = "MyTableType",
            Value = table,
        });

    cmd.ExecuteNonQuery();
}
```

In het vorige voor beeld voegt het **SqlCommand** -object rijen uit een tabelwaardeparameter, **\@ TestTvp**. Het eerder gemaakte **DataTable** -object wordt toegewezen aan deze para meter met de methode **SqlCommand. para meters. add** . Als u de invoeging in één gesprek inschakelt, neemt de prestaties aanzienlijk toe tijdens opeenvolgende invoegingen.

Als u het vorige voor beeld verder wilt verbeteren, gebruikt u een opgeslagen procedure in plaats van een op tekst gebaseerde opdracht. Met de volgende Transact-SQL-opdracht maakt u een opgeslagen procedure die de **SimpleTestTableType** -para meter voor de tabel waarde gebruikt.

```sql
CREATE PROCEDURE [dbo].[sp_InsertRows]
@TestTvp as MyTableType READONLY
AS
BEGIN
INSERT INTO MyTable(mytext, num)
SELECT mytext, num FROM @TestTvp
END
GO
```

Wijzig vervolgens de declaratie van het **SqlCommand** -object in het voor gaande code voorbeeld in het volgende voor beeld.

```csharp
SqlCommand cmd = new SqlCommand("sp_InsertRows", connection);
cmd.CommandType = CommandType.StoredProcedure;
```

In de meeste gevallen hebben para meters met een tabel waarde dezelfde of betere prestaties dan andere batch-technieken. Para meters met tabel waarden zijn vaak de voor keur, omdat ze flexibeler zijn dan andere opties. Bijvoorbeeld: voor andere technieken, zoals SQL bulksgewijze kopie, is het invoegen van nieuwe rijen alleen toegestaan. Maar met tabelwaardeparameter-para meters kunt u de logica in de opgeslagen procedure gebruiken om te bepalen welke rijen moeten worden bijgewerkt en die worden ingevoegd. Het tabel type kan ook worden gewijzigd om een kolom ' bewerking ' te bevatten die aangeeft of de opgegeven rij moet worden ingevoegd, bijgewerkt of verwijderd.

In de volgende tabel ziet u de resultaten van ad hoc tests voor het gebruik van para meters met tabel waarden in milliseconden.

| Operations | On-premises naar Azure (MS) | Azure hetzelfde Data Center (MS) |
| --- | --- | --- |
| 1 |124 |32 |
| 10 |131 |25 |
| 100 |338 |51 |
| 1000 |2615 |382 |
| 10.000 |23830 |3586 |

> [!NOTE]
> Resultaten zijn geen benchmarks. Zie de [Opmerking over timing resultaten in dit artikel](#note-about-timing-results-in-this-article).

De prestatie verbetering van batch verwerking is onmiddellijk zichtbaar. In de vorige sequentiële test duurde 1000 bewerkingen 129 seconden buiten het Data Center en 21 seconden vanaf het Data Center. Maar met tabelwaardeparameter-para meters 1000 bewerkingen alleen 2,6 seconden buiten het Data Center en 0,4 seconden binnen het Data Center.

Zie [para meters voor tabel waarden](/sql/relational-databases/tables/use-table-valued-parameters-database-engine)voor meer informatie over tabelwaardeparameter.

### <a name="sql-bulk-copy"></a>Bulksgewijs kopiëren van SQL

SQL bulksgewijze kopie is een andere manier om grote hoeveel heden gegevens in te voegen in een doel database. .NET-toepassingen kunnen de klasse **SqlBulkCopy** gebruiken om bulksgewijze Insert-bewerkingen uit te voeren. **SqlBulkCopy** is vergelijkbaar met de functie voor het opdracht regel programma, **Bcp.exe** of de Transact-SQL-instructie **Bulk Insert**. In het volgende code voorbeeld ziet u hoe u de rijen in de bron- **DataTable**, tabel, in bulk kunt kopiëren naar de doel tabel mytable.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    connection.Open();

    using (SqlBulkCopy bulkCopy = new SqlBulkCopy(connection))
    {
        bulkCopy.DestinationTableName = "MyTable";
        bulkCopy.ColumnMappings.Add("mytext", "mytext");
        bulkCopy.ColumnMappings.Add("num", "num");
        bulkCopy.WriteToServer(table);
    }
}
```

Er zijn enkele gevallen waarin bulksgewijs kopiëren de voor keur geeft aan de para meters met tabel waarden. Zie de vergelijkings tabel van Table-Valued-para meters versus BULK INSERT bewerkingen in de [tabel waarden para meters](/sql/relational-databases/tables/use-table-valued-parameters-database-engine).

De volgende ad hoc test resultaten tonen de prestaties van batching met **SqlBulkCopy** in milliseconden.

| Operations | On-premises naar Azure (MS) | Azure hetzelfde Data Center (MS) |
| --- | --- | --- |
| 1 |433 |57 |
| 10 |441 |32 |
| 100 |636 |53 |
| 1000 |2535 |341 |
| 10.000 |21605 |2737 |

> [!NOTE]
> Resultaten zijn geen benchmarks. Zie de [Opmerking over timing resultaten in dit artikel](#note-about-timing-results-in-this-article).

In kleinere batch grootten wordt de **SqlBulkCopy** -klasse gebruikt voor het uitvoeren van para meters met tabel waarden. **SqlBulkCopy** heeft 12-31% sneller uitgevoerd dan para meters met tabel waarden voor de tests van 1.000 en 10.000 rijen. Net als tabelwaardeparameter is **SqlBulkCopy** een goede optie voor het invoegen in batches, met name bij het vergelijken van de prestaties van niet-batch bewerkingen.

Zie [bulksgewijze Kopieer bewerkingen](/dotnet/framework/data/adonet/sql/bulk-copy-operations-in-sql-server)voor meer informatie over bulksgewijs kopiëren in ADO.net.

### <a name="multiple-row-parameterized-insert-statements"></a>INSERT-instructies met para meters met meerdere rijen

Een alternatief voor kleine batches bestaat uit het samen stellen van een grote instructie INSERT waarmee meerdere rijen worden ingevoegd. In het volgende code voorbeeld wordt deze techniek gedemonstreerd.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    connection.Open();

    string insertCommand = "INSERT INTO [MyTable] ( mytext, num ) " +
        "VALUES (@p1, @p2), (@p3, @p4), (@p5, @p6), (@p7, @p8), (@p9, @p10)";

    SqlCommand cmd = new SqlCommand(insertCommand, connection);

    for (int i = 1; i <= 10; i += 2)
    {
        cmd.Parameters.Add(new SqlParameter("@p" + i.ToString(), "test"));
        cmd.Parameters.Add(new SqlParameter("@p" + (i+1).ToString(), i));
    }

    cmd.ExecuteNonQuery();
}
```

Dit voor beeld is bedoeld om het basis concept weer te geven. In een realistischer scenario worden de vereiste entiteiten door lopen om de query reeks en de opdracht parameters tegelijk te maken. U bent beperkt tot een totaal van 2100 query parameters, dus Hiermee beperkt u het totale aantal rijen dat op deze manier kan worden verwerkt.

De volgende resultaten van ad hoc tests geven de prestaties van dit type instructie INSERT in milliseconden weer.

| Operations | Tabelwaardeparameter (MS) | INSERT met één instructie (MS) |
| --- | --- | --- |
| 1 |32 |20 |
| 10 |30 |25 |
| 100 |33 |51 |

> [!NOTE]
> Resultaten zijn geen benchmarks. Zie de [Opmerking over timing resultaten in dit artikel](#note-about-timing-results-in-this-article).

Deze benadering kan iets sneller zijn voor batches die minder dan 100 rijen zijn. Hoewel de verbetering klein is, is deze techniek een andere optie die goed kan werken in uw specifieke toepassings scenario.

### <a name="dataadapter"></a>Data

Met de klasse **Data adapter** kunt u een object **DataSet** wijzigen en vervolgens de wijzigingen verzenden als insert-, update-en delete-bewerkingen. Als u de **Data adapter** op deze manier gebruikt, is het belang rijk te weten dat afzonderlijke aanroepen voor elke afzonderlijke bewerking worden uitgevoerd. U kunt de prestaties verbeteren door de eigenschap **UpdateBatchSize** te gebruiken voor het aantal bewerkingen dat per keer moet worden ingebatcheerd. Zie [batch bewerkingen uitvoeren met behulp van data adapters](/dotnet/framework/data/adonet/performing-batch-operations-using-dataadapters)voor meer informatie.

### <a name="entity-framework"></a>Entity Framework

[Entity Framework Core](/ef/efcore-and-ef6/#saving-data) ondersteunt batch verwerking.

### <a name="xml"></a>XML

Voor de volledigheid is het belang rijk om te praten over XML als een batch-strategie. Het gebruik van XML heeft echter geen voor delen ten opzichte van andere methoden en enkele nadelen. De benadering is vergelijkbaar met tabelwaardeparameter, maar er wordt een XML-bestand of-teken reeks door gegeven aan een opgeslagen procedure in plaats van een door de gebruiker gedefinieerde tabel. Met de opgeslagen procedure worden de opdrachten in de opgeslagen procedure geparseerd.

Deze aanpak heeft verschillende nadelen:

* Werken met XML kan lastig zijn en fout gevoelig zijn.
* Het parseren van de XML voor de data base kan CPU-intensief zijn.
* In de meeste gevallen is deze methode langzamer dan tabelwaardeparameter.

Daarom wordt het gebruik van XML voor batch-query's niet aanbevolen.

## <a name="batching-considerations"></a>Overwegingen voor batch verwerking

In de volgende secties vindt u meer informatie over het gebruik van batch verwerking in Azure SQL Database en Azure SQL Managed instance-toepassingen.

### <a name="tradeoffs"></a>Compromissen

Afhankelijk van uw architectuur kan batch verwerking gebruikmaken van een balans tussen prestaties en tolerantie. Denk bijvoorbeeld na over het scenario waarin uw rol onverwacht uitvalt. Als u één rij met gegevens kwijtraakt, is de impact kleiner dan de impact van het verloren gaan van een grote batch met niet-verzonden rijen. Er is een groter risico bij het bufferen van rijen voordat deze naar de Data Base worden verzonden in een opgegeven tijd venster.

Als gevolg van deze balans, moet u het type bewerkingen evalueren dat u batcheert. Batch is agressief (grotere batches en langere tijd Vensters) met gegevens die minder kritiek zijn.

### <a name="batch-size"></a>Batchgrootte

In onze tests is het niet handig om grote batches te verbreken in kleinere segmenten. Dit leidt er vaak toe dat deze indeling langzamer presteert dan het verzenden van één grote batch. Denk bijvoorbeeld aan een scenario waarin u 1000 rijen wilt invoegen. In de volgende tabel ziet u hoe lang het duurt om de para meters met tabel waarden te gebruiken om 1000 rijen in te voegen in kleinere batches.

| Batchgrootte | Iteraties | Tabelwaardeparameter (MS) |
| --- | --- | --- |
| 1000 |1 |347 |
| 500 |2 |355 |
| 100 |10 |465 |
| 50 |20 |630 |

> [!NOTE]
> Resultaten zijn geen benchmarks. Zie de [Opmerking over timing resultaten in dit artikel](#note-about-timing-results-in-this-article).

U kunt zien dat de beste prestaties voor 1000 rijen allemaal tegelijk worden verzonden. In andere tests (hier niet weer gegeven) is er een kleine prestatie verbetering opgetreden bij het opsplitsen van een 10000-tabelrij batch in twee batches van 5000. Maar het tabel schema voor deze tests is relatief eenvoudig. u moet dus testen uitvoeren op uw specifieke gegevens en batch grootten om deze resultaten te controleren.

Een andere factor waarmee u rekening moet houden, is dat als de totale batch te groot wordt, Azure SQL Database of Azure SQL Managed instance de batch mogelijk beperkt en weigert. Test uw specifieke scenario om te bepalen of er sprake is van een ideale Batch grootte voor de beste resultaten. Maak de Batch grootte configureerbaar tijdens runtime om snelle aanpassingen mogelijk te maken op basis van de prestaties of fouten.

Ten slotte kunt u de grootte van de batch afstemmen op de Risico's die zijn gekoppeld aan batch verwerking. Als er sprake is van tijdelijke fouten of als de rol mislukt, kunt u de gevolgen van het opnieuw proberen van de bewerking of het verlies van de gegevens in de batch nadenken.

### <a name="parallel-processing"></a>Parallelle verwerking

Wat gebeurt er als u de manier hebt genomen om de Batch grootte te verminderen, maar meerdere threads hebt gebruikt om het werk uit te voeren? Volgens onze tests bleek het dat verschillende kleinere multithreadde batches doorgaans erger zijn dan een enkele grotere batch. De volgende test probeert 1000 rijen in te voegen in een of meer parallelle batches. Deze test laat zien hoe meerdere gelijktijdige batches de prestaties in werkelijkheid verlaagt.

| Batch grootte [iteraties] | Twee threads (MS) | Vier threads (MS) | Zes threads (MS) |
| --- | --- | --- | --- |
| 1000 [1] |277 |315 |266 |
| 500 [2] |548 |278 |256 |
| 250 [4] |405 |329 |265 |
| 100 [10] |488 |439 |391 |

> [!NOTE]
> Resultaten zijn geen benchmarks. Zie de [Opmerking over timing resultaten in dit artikel](#note-about-timing-results-in-this-article).

Er zijn verschillende mogelijke oorzaken voor het degraderen van prestaties vanwege parallellisme:

* Er zijn meerdere gelijktijdige netwerk aanroepen in plaats van één.
* Meerdere bewerkingen voor één tabel kunnen leiden tot conflicten en blok keringen.
* Er zijn overhead kosten gekoppeld aan meerdere threads.
* De onkosten van het openen van meerdere verbindingen verloopt het voor deel van parallelle verwerking.

Als u verschillende tabellen of data bases aanwijst, is het mogelijk dat er een prestatie verbetering met deze strategie wordt weer geven. Data base sharding of de overkoepelende federatie zou een scenario voor deze aanpak zijn. Sharding maakt gebruik van meerdere data bases en stuurt verschillende gegevens naar elke Data Base. Als elke kleine batch naar een andere data base gaat, kan het efficiënter zijn om de bewerkingen op parallelle wijze uit te voeren. De prestatie verbetering is echter niet significant genoeg om te gebruiken als basis voor een beslissing over het gebruik van data base-sharding in uw oplossing.

In sommige ontwerpen kan parallelle uitvoering van kleinere batches leiden tot een betere door Voer van aanvragen in een systeem onder belasting. In dit geval, zelfs als het sneller is om één grotere batch te verwerken, is het verwerken van meerdere batches parallel mogelijk efficiënter.

Als u parallelle uitvoering wilt gebruiken, kunt u het maximum aantal werkthreads beheren. Een kleiner getal kan leiden tot minder conflicten en een snellere uitvoerings tijd. Houd ook rekening met de extra belasting die op deze locatie in de doel database is opgeslagen in verbindingen en trans acties.

### <a name="related-performance-factors"></a>Gerelateerde prestatie factoren

Typische richt lijnen voor de prestaties van de Data Base zijn ook van invloed op batch verwerking. Zo worden de prestaties voor tabellen met een grote primaire sleutel of een groot aantal niet-geclusterde indexen gereduceerd.

Als de tabelwaardeparameter para meters een opgeslagen procedure gebruiken, kunt u de opdracht **set Count aan** aan het begin van de procedure gebruiken. Deze instructie onderdrukt het retour neren van het aantal beïnvloede rijen in de procedure. Bij onze tests had het gebruik van set- **AANTALARG** echter geen effect of verminderde prestaties. De test opgeslagen procedure is eenvoudig met één opdracht **Invoegen** uit de tabelwaardeparameter. Het is mogelijk dat complexere opgeslagen procedures van deze instructie profiteren. Maar stel dat het toevoegen **van set-tellingen** aan aan uw opgeslagen procedure automatisch betere prestaties verbetert. Als u wilt weten wat het effect is, test u uw opgeslagen procedure met en zonder de instructie **set Count on** .

## <a name="batching-scenarios"></a>Scenario's voor batch verwerking

In de volgende secties wordt beschreven hoe u tabelwaardeparameter kunt gebruiken in drie toepassings scenario's. In het eerste scenario ziet u hoe buffering en batch verwerking kunnen samen werken. In het tweede scenario worden de prestaties verbeterd door hoofd gegevens bewerkingen uit te voeren in één opgeslagen procedure aanroep. In het laatste scenario ziet u hoe u tabelwaardeparameter kunt gebruiken in een ' UPSERT-bewerking.

### <a name="buffering"></a>Buffer

Hoewel er sprake is van een duidelijkere kandidaat voor batch verwerking, zijn er veel scenario's die kunnen profiteren van batch verwerking door vertraagd verwerken. De vertraagde verwerking heeft echter ook een groter risico dat de gegevens verloren gaan in het geval van een onverwachte fout. Het is belang rijk om dit risico te begrijpen en de gevolgen te bepalen.

Denk bijvoorbeeld aan een webtoepassing waarmee de navigatie geschiedenis van elke gebruiker wordt bijgehouden. Op elke pagina-aanvraag kan de toepassing een database aanroep maken om de pagina weergave van de gebruiker vast te leggen. Maar betere prestaties en schaal baarheid kan worden bereikt door de navigatie activiteiten van de gebruikers te bufferen en deze gegevens vervolgens naar de data base in batches te verzenden. U kunt de Data Base-update activeren op basis van de verstreken tijd en/of buffer grootte. Een regel kan bijvoorbeeld aangeven dat de batch na 20 seconden moet worden verwerkt of wanneer de buffer 1000 items bereikt.

Het volgende code voorbeeld maakt gebruik van [reactieve extensies-RX](/previous-versions/dotnet/reactive-extensions/hh242985(v=vs.103)) om gebufferde gebeurtenissen te verwerken die door een bewakings klasse worden gegenereerd. Wanneer de buffer opvulling of een time-out is bereikt, wordt de batch met gebruikers gegevens naar de data base verzonden met een tabelwaardeparameter.

De volgende NavHistoryData-klassen modellen de navigatie gegevens van de gebruiker. Het bevat algemene informatie, zoals de gebruikers-id, de URL die wordt gebruikt en de toegangs tijd.

```csharp
public class NavHistoryData
{
    public NavHistoryData(int userId, string url, DateTime accessTime)
    { UserId = userId; URL = url; AccessTime = accessTime; }
    public int UserId { get; set; }
    public string URL { get; set; }
    public DateTime AccessTime { get; set; }
}
```

De NavHistoryDataMonitor-klasse is verantwoordelijk voor het bufferen van de navigatie gegevens van de gebruiker naar de data base. Het bevat een methode, RecordUserNavigationEntry, die reageert door een **OnAdded** -gebeurtenis te verhogen. De volgende code toont de constructor-logica die gebruikmaakt van RX om een waarneem bare verzameling te maken op basis van de gebeurtenis. Vervolgens wordt u met de buffer methode geabonneerd op deze waarneem bare verzameling. De overbelasting geeft aan dat de buffer elke 20 seconden of 1000 vermeldingen moet worden verzonden.

```csharp
public NavHistoryDataMonitor()
{
    var observableData =
        Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");

    observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);
}
```

Met de handler worden alle gebufferde items naar een type tabel waarde geconverteerd en wordt dit type vervolgens door gegeven aan een opgeslagen procedure waarmee de batch wordt verwerkt. De volgende code toont de volledige definitie voor de klassen NavHistoryDataEventArgs en NavHistoryDataMonitor.

```csharp
public class NavHistoryDataEventArgs : System.EventArgs
{
    public NavHistoryDataEventArgs(NavHistoryData data) { Data = data; }
    public NavHistoryData Data { get; set; }
}

public class NavHistoryDataMonitor
{
    public event EventHandler<NavHistoryDataEventArgs> OnAdded;

    public NavHistoryDataMonitor()
    {
        var observableData =
            Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");

        observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);
    }
```

Met de handler worden alle gebufferde items naar een type tabel waarde geconverteerd en wordt dit type vervolgens door gegeven aan een opgeslagen procedure waarmee de batch wordt verwerkt. De volgende code toont de volledige definitie voor de klassen NavHistoryDataEventArgs en NavHistoryDataMonitor.

```csharp
    public class NavHistoryDataEventArgs : System.EventArgs
    {
        if (OnAdded != null)
            OnAdded(this, new NavHistoryDataEventArgs(data));
    }

    protected void Handler(IList<EventPattern<NavHistoryDataEventArgs>> items)
    {
        DataTable navHistoryBatch = new DataTable("NavigationHistoryBatch");
        navHistoryBatch.Columns.Add("UserId", typeof(int));
        navHistoryBatch.Columns.Add("URL", typeof(string));
        navHistoryBatch.Columns.Add("AccessTime", typeof(DateTime));
        foreach (EventPattern<NavHistoryDataEventArgs> item in items)
        {
            NavHistoryData data = item.EventArgs.Data;
            navHistoryBatch.Rows.Add(data.UserId, data.URL, data.AccessTime);
        }

        using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
        {
            connection.Open();

            SqlCommand cmd = new SqlCommand("sp_RecordUserNavigation", connection);
            cmd.CommandType = CommandType.StoredProcedure;

            cmd.Parameters.Add(
                new SqlParameter()
                {
                    ParameterName = "@NavHistoryBatch",
                    SqlDbType = SqlDbType.Structured,
                    TypeName = "NavigationHistoryTableType",
                    Value = navHistoryBatch,
                });

            cmd.ExecuteNonQuery();
        }
    }
}
```

Als u deze buffer klasse wilt gebruiken, maakt de toepassing een statisch NavHistoryDataMonitor-object. Telkens wanneer een gebruiker een pagina opent, roept de toepassing de methode NavHistoryDataMonitor. RecordUserNavigationEntry aan. De logica voor buffering zorgt ervoor dat deze vermeldingen in batches naar de Data Base worden verzonden.

### <a name="master-detail"></a>Hoofd Details

Para meters met tabel waarden zijn handig voor eenvoudige INVOEG scenario's. Het kan echter lastiger zijn om batch toevoegingen te maken die meer dan een tabel omvatten. Het scenario ' hoofd/detail ' is een goed voor beeld. De hoofd tabel bevat de primaire entiteit. Een of meer detail tabellen bevatten meer gegevens over de entiteit. In dit scenario afdwingen refererende-sleutel relaties de relatie van gegevens met een unieke hoofd entiteit. Overweeg een vereenvoudigde versie van een PurchaseOrder-tabel en de bijbehorende OrderDetail-tabel. Met de volgende Transact-SQL wordt de PurchaseOrder-tabel gemaakt met vier kolommen: OrderID, order date, KlantId en status.

```sql
CREATE TABLE [dbo].[PurchaseOrder](
[OrderID] [int] IDENTITY(1,1) NOT NULL,
[OrderDate] [datetime] NOT NULL,
[CustomerID] [int] NOT NULL,
[Status] [nvarchar](50) NOT NULL,
CONSTRAINT [PrimaryKey_PurchaseOrder]
PRIMARY KEY CLUSTERED ( [OrderID] ASC ))
```

Elke bestelling bevat een of meer product aankopen. Deze informatie wordt vastgelegd in de tabel PurchaseOrderDetail. Met de volgende Transact-SQL wordt de PurchaseOrderDetail-tabel gemaakt met vijf kolommen: OrderID, OrderDetailID, ProductID, UnitPrice en Bestelhoev.

```sql
CREATE TABLE [dbo].[PurchaseOrderDetail](
[OrderID] [int] NOT NULL,
[OrderDetailID] [int] IDENTITY(1,1) NOT NULL,
[ProductID] [int] NOT NULL,
[UnitPrice] [money] NULL,
[OrderQty] [smallint] NULL,
CONSTRAINT [PrimaryKey_PurchaseOrderDetail] PRIMARY KEY CLUSTERED
( [OrderID] ASC, [OrderDetailID] ASC ))
```

De kolom OrderID in de tabel PurchaseOrderDetail moet verwijzen naar een order in de tabel PurchaseOrder. Deze beperking wordt afgedwongen door de volgende definitie van een refererende sleutel.

```sql
ALTER TABLE [dbo].[PurchaseOrderDetail]  WITH CHECK ADD
CONSTRAINT [FK_OrderID_PurchaseOrder] FOREIGN KEY([OrderID])
REFERENCES [dbo].[PurchaseOrder] ([OrderID])
```

Als u para meters met tabel waarden wilt gebruiken, moet u voor elke doel tabel één door de gebruiker gedefinieerd tabel type hebben.

```sql
CREATE TYPE PurchaseOrderTableType AS TABLE
( OrderID INT,
    OrderDate DATETIME,
    CustomerID INT,
    Status NVARCHAR(50) );
GO

CREATE TYPE PurchaseOrderDetailTableType AS TABLE
( OrderID INT,
    ProductID INT,
    UnitPrice MONEY,
    OrderQty SMALLINT );
GO
```

Definieer vervolgens een opgeslagen procedure voor het accepteren van tabellen van deze typen. Met deze procedure kan een toepassing lokale batch een set orders en de volg orde van de gegevens in één aanroep uitvoeren. De volgende Transact-SQL bevat de volledige opgeslagen procedure declaratie voor dit inkoop order voorbeeld.

```sql
CREATE PROCEDURE sp_InsertOrdersBatch (
@orders as PurchaseOrderTableType READONLY,
@details as PurchaseOrderDetailTableType READONLY )
AS
SET NOCOUNT ON;

-- Table that connects the order identifiers in the @orders
-- table with the actual order identifiers in the PurchaseOrder table
DECLARE @IdentityLink AS TABLE (
SubmittedKey int,
ActualKey int,
RowNumber int identity(1,1)
);

-- Add new orders to the PurchaseOrder table, storing the actual
-- order identifiers in the @IdentityLink table
INSERT INTO PurchaseOrder ([OrderDate], [CustomerID], [Status])
OUTPUT inserted.OrderID INTO @IdentityLink (ActualKey)
SELECT [OrderDate], [CustomerID], [Status] FROM @orders ORDER BY OrderID;

-- Match the passed-in order identifiers with the actual identifiers
-- and complete the @IdentityLink table for use with inserting the details
WITH OrderedRows As (
SELECT OrderID, ROW_NUMBER () OVER (ORDER BY OrderID) As RowNumber
FROM @orders
)
UPDATE @IdentityLink SET SubmittedKey = M.OrderID
FROM @IdentityLink L JOIN OrderedRows M ON L.RowNumber = M.RowNumber;

-- Insert the order details into the PurchaseOrderDetail table,
-- using the actual order identifiers of the master table, PurchaseOrder
INSERT INTO PurchaseOrderDetail (
[OrderID],
[ProductID],
[UnitPrice],
[OrderQty] )
SELECT L.ActualKey, D.ProductID, D.UnitPrice, D.OrderQty
FROM @details D
JOIN @IdentityLink L ON L.SubmittedKey = D.OrderID;
GO
```

In dit voor beeld slaat de lokaal gedefinieerde @IdentityLink tabel de werkelijke waarden voor OrderID van de zojuist ingevoegde rijen. Deze order-id's verschillen van de tijdelijke waarden voor OrderID in de @orders @details para meters en tabelwaardeparameter. Daarom @IdentityLink verbindt de tabel vervolgens de waarden voor de OrderID van de @orders para meter naar de werkelijke waarden van de order-waarde voor de nieuwe rijen in de tabel PurchaseOrder. Na deze stap kan de @IdentityLink tabel de details van de order gemakkelijker invoegen met de huidige OrderID die voldoet aan de beperking van de refererende sleutel.

Deze opgeslagen procedure kan worden gebruikt vanuit code of vanuit andere Transact-SQL-aanroepen. Zie de sectie para meters voor tabel waarden in dit artikel voor een code voorbeeld. In de volgende Transact-SQL wordt uitgelegd hoe u de sp_InsertOrdersBatch aanroept.

```sql
declare @orders as PurchaseOrderTableType
declare @details as PurchaseOrderDetailTableType

INSERT @orders
([OrderID], [OrderDate], [CustomerID], [Status])
VALUES(1, '1/1/2013', 1125, 'Complete'),
(2, '1/13/2013', 348, 'Processing'),
(3, '1/12/2013', 2504, 'Shipped')

INSERT @details
([OrderID], [ProductID], [UnitPrice], [OrderQty])
VALUES(1, 10, $11.50, 1),
(1, 12, $1.58, 1),
(2, 23, $2.57, 2),
(3, 4, $10.00, 1)

exec sp_InsertOrdersBatch @orders, @details
```

Met deze oplossing kan elke batch een set OrderID-waarden gebruiken die beginnen bij 1. Met deze tijdelijke order-waarden worden de relaties in de batch beschreven, maar de werkelijke waarden voor OrderID worden bepaald op het moment van de invoeg bewerking. U kunt dezelfde instructies in het vorige voor beeld herhaaldelijk uitvoeren en unieke orders in de-data base genereren. Daarom kunt u overwegen meer code of database logica toe te voegen waarmee dubbele orders worden voor komen wanneer deze batch techniek wordt gebruikt.

In dit voor beeld wordt gedemonstreerd dat zelfs complexere database bewerkingen, zoals hoofd detail bewerkingen, kunnen worden gebatcheerd met para meters met tabel waarden.

### <a name="upsert"></a>UPSERT

Een ander batch scenario omvat het gelijktijdig bijwerken van bestaande rijen en het invoegen van nieuwe rijen. Deze bewerking wordt ook wel een ' UPSERT-bewerking (update + insert) genoemd. In plaats van afzonderlijke aanroepen te maken om in te VOEGen en bij te werken, is de instructie MERGe het meest geschikt voor deze taak. De instructie MERGe kan zowel insert-als update-bewerkingen uitvoeren in één aanroep.

U kunt para meters voor tabel waarden gebruiken met de instructie MERGe om updates uit te voeren en in te VOEGen. Denk bijvoorbeeld aan een vereenvoudigde tabel met werk nemers die de volgende kolommen bevat: EmployeeID, FirstName, LastName, SocialSecurityNumber:

```sql
CREATE TABLE [dbo].[Employee](
[EmployeeID] [int] IDENTITY(1,1) NOT NULL,
[FirstName] [nvarchar](50) NOT NULL,
[LastName] [nvarchar](50) NOT NULL,
[SocialSecurityNumber] [nvarchar](50) NOT NULL,
CONSTRAINT [PrimaryKey_Employee] PRIMARY KEY CLUSTERED
([EmployeeID] ASC ))
```

In dit voor beeld kunt u het feit gebruiken dat de SocialSecurityNumber uniek is om een samen VOEGing van meerdere werk nemers uit te voeren. Maak eerst het door de gebruiker gedefinieerde tabel type:

```sql
CREATE TYPE EmployeeTableType AS TABLE
( Employee_ID INT,
    FirstName NVARCHAR(50),
    LastName NVARCHAR(50),
    SocialSecurityNumber NVARCHAR(50) );
GO
```

Maak vervolgens een opgeslagen procedure of schrijf code die gebruikmaakt van de instructie MERGe om de update uit te voeren en in te voegen. In het volgende voor beeld wordt de instructie MERGe gebruikt voor een tabelwaardeparameter, @employees , van het type EmployeeTableType. De inhoud van de @employees tabel wordt hier niet weer gegeven.

```sql
MERGE Employee AS target
USING (SELECT [FirstName], [LastName], [SocialSecurityNumber] FROM @employees)
AS source ([FirstName], [LastName], [SocialSecurityNumber])
ON (target.[SocialSecurityNumber] = source.[SocialSecurityNumber])
WHEN MATCHED THEN
UPDATE SET
target.FirstName = source.FirstName,
target.LastName = source.LastName
WHEN NOT MATCHED THEN
    INSERT ([FirstName], [LastName], [SocialSecurityNumber])
    VALUES (source.[FirstName], source.[LastName], source.[SocialSecurityNumber]);
```

Zie voor meer informatie de documentatie en voor beelden voor de instructie MERGe. Hoewel hetzelfde werk kan worden uitgevoerd in een opgeslagen procedure aanroep met meerdere stappen met afzonderlijke INSERT-en UPDATE-bewerkingen, is de instructie MERGe efficiënter. Met database code kunnen ook Transact-SQL-aanroepen worden gemaakt die de instructie MERGe direct gebruiken zonder dat er twee database aanroepen hoeven te worden uitgevoerd voor INSERT en UPDATE.

## <a name="recommendation-summary"></a>Samen vatting aanbeveling

De volgende lijst bevat een overzicht van de aanbevelingen voor batch verwerking die in dit artikel worden besproken:

* Gebruik buffering en batch verwerking om de prestaties en schaal baarheid van Azure SQL Database en Azure SQL Managed instance-toepassingen te verbeteren.
* Meer informatie over de compromissen tussen batch verwerking/buffering en tolerantie. Tijdens een storing van een functie kan het risico dat een niet-verwerkte batch kritieke gegevens verloren gaan, de prestaties van de batch verwerking ten zwaarder afnemen.
* Poging om alle aanroepen naar de data base binnen één Data Center te houden om de latentie te verminderen.
* Als u één batch-techniek kiest, bieden para meters voor de tabel waarden de beste prestaties en flexibiliteit.
* Volg de volgende algemene richt lijnen voor de snelste prestaties, maar test uw scenario:
  * Gebruik voor < 100-rijen één para meter opdracht invoegen.
  * Gebruik voor < 1000-rijen para meters met tabel waarden.
  * Gebruik SqlBulkCopy voor >= 1000 rijen.
* Voor bijwerk-en verwijder bewerkingen gebruikt u tabelwaardeparameter-para meters met opgeslagen procedure logica waarmee de juiste bewerking wordt bepaald op elke rij in de tabel parameter.
* Richt lijnen voor Batch grootte:
  * Gebruik de grootste batch grootten die zinvol zijn voor uw toepassing en zakelijke vereisten.
  * Behaal de prestatie winst van grote batches met de Risico's van tijdelijke of onherstelbare fouten. Wat is het gevolg van nieuwe pogingen of verlies van de gegevens in de batch?
  * Test de grootste Batch grootte om te controleren of Azure SQL Database of Azure SQL Managed instance deze niet afwijst.
  * Maak configuratie-instellingen voor het beheren van batch verwerking, zoals de Batch grootte of het buffer tijd venster. Deze instellingen bieden flexibiliteit. U kunt het batch gedrag voor productie wijzigen zonder de Cloud service opnieuw te implementeren.
* Vermijd parallelle uitvoering van batches die op één tabel in één Data Base worden uitgevoerd. Als u ervoor kiest om één batch te verdelen over meerdere werk threads, voert u tests uit om het ideale aantal threads te bepalen. Na een niet-opgegeven drempel waarde, zullen meer threads de prestaties verlagen in plaats van deze te verg Roten.
* Overweeg bufferen op grootte en tijd als een manier om batches te implementeren voor meer scenario's.

## <a name="next-steps"></a>Volgende stappen

Dit artikel is gericht op het verbeteren van de prestaties en schaal baarheid van uw toepassingen met bemiddelende technieken voor database ontwerp en-code ring. Maar dit is slechts één factor in uw algemene strategie. Zie [richt lijnen voor database prestaties](database/performance-guidance.md) en [prijs-en prestatie overwegingen voor een elastische pool](database/elastic-pool-overview.md)voor meer manieren om de prestaties en schaal baarheid te verbeteren.
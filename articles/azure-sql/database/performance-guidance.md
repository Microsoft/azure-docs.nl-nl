---
title: Richt lijnen voor het afstemmen van prestaties voor toepassingen en data bases
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Meer informatie over het afstemmen van database toepassingen en-data bases voor prestaties in Azure SQL Database en Azure SQL Managed instance.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 03/10/2020
ms.openlocfilehash: 40657ad2f3b69d62e0e0d9c7d9e0f0be7343547b
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96490598"
---
# <a name="tune-applications-and-databases-for-performance-in-azure-sql-database-and-azure-sql-managed-instance"></a>Toepassingen en data bases voor prestaties afstemmen in Azure SQL Database en Azure SQL Managed instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Wanneer u een prestatie probleem hebt vastgesteld dat u hebt gefacing met Azure SQL Database en Azure SQL Managed instance, is dit artikel ontworpen om u te helpen bij het volgende:

- Stel uw toepassing af en pas aanbevolen procedures toe waarmee de prestaties kunnen worden verbeterd.
- Stem de data base af door indexen en query's te wijzigen om efficiënter met gegevens te werken.

In dit artikel wordt ervan uitgegaan dat u de Azure SQL Database [Data Base Advisor-aanbevelingen](database-advisor-implement-performance-recommendations.md) en de [aanbevelingen voor het automatisch afstemmen](automatic-tuning-overview.md)van de Azure SQL database al hebt door lopen, indien van toepassing. Ook wordt ervan uitgegaan dat u [een overzicht van het controleren en afstemmen](monitor-tune-overview.md) en de bijbehorende artikelen hebt bekeken met betrekking tot het oplossen van prestatie problemen. Daarnaast wordt er in dit artikel van uitgegaan dat u geen CPU-resources hebt, een probleem met betrekking tot prestaties dat kan worden opgelost door de berekenings grootte of de servicelaag te verhogen om meer resources aan uw data base toe te voegen.

## <a name="tune-your-application"></a>Uw toepassing afstemmen

Bij traditionele on-premises SQL Server is het proces van het plannen van de eerste capaciteit vaak gescheiden van het proces van het uitvoeren van een toepassing in de productie omgeving. Hardware-en product licenties worden eerst aangeschaft en het afstemmen van de prestaties wordt later gedaan. Wanneer u Azure SQL gebruikt, is het een goed idee om het proces van het uitvoeren van een toepassing en het afstemmen ervan te verfijnen. Met het model van de betaling voor capaciteit op aanvraag kunt u uw toepassing afstemmen op het gebruik van de mini maal benodigde resources, in plaats van de inrichting op hardware op basis van schattingen van toekomstige groei plannen voor een toepassing, die vaak onjuist zijn. Sommige klanten kunnen ervoor kiezen om een toepassing niet af te stemmen en in plaats daarvan te kiezen voor het inrichten van hardwarebronnen. Deze aanpak kan een goed idee zijn als u een belang rijke toepassing niet wilt wijzigen tijdens een drukke periode. Maar het afstemmen van een toepassing kan de resource vereisten minimaliseren en de maandelijkse facturen verlagen wanneer u de service lagen in Azure SQL Database en Azure SQL Managed instance gebruikt.

### <a name="application-characteristics"></a>Toepassings kenmerken

Hoewel Azure SQL Database en Azure SQL Managed instance service-lagen zijn ontworpen om de stabiliteit en voorspel baarheid van de prestaties van een toepassing te verbeteren, kunt u met een aantal aanbevolen procedures uw toepassing afstemmen om optimaal gebruik te maken van de resources met een berekenings grootte. Hoewel veel toepassingen aanzienlijke prestatie verbeteringen hebben door te scha kelen naar een hogere reken grootte of servicelaag, hebben sommige toepassingen extra afstemming nodig om te profiteren van een hoger service niveau. Voor betere prestaties moet u extra fijnafstelling van toepassingen overwegen voor toepassingen met de volgende kenmerken:

- **Toepassingen met trage prestaties vanwege het gedrag van ' intensieve '**

  Intensieve-toepassingen maken buitensporige Data Access-bewerkingen die gevoelig zijn voor netwerk latentie. Mogelijk moet u dit soort toepassingen wijzigen om het aantal bewerkingen voor gegevens toegang tot de data base te verminderen. U kunt bijvoorbeeld de prestaties van toepassingen verbeteren door gebruik te maken van technieken als batch-hoc query's of het verplaatsen van query's naar opgeslagen procedures. Zie [batch-query's](#batch-queries)voor meer informatie.

- **Data bases met een intensieve werk belasting die niet kan worden ondersteund door een hele enkele computer**

   Data bases die de grootste Premium-reken grootte overschrijden, kunnen voor deel zijn van het schalen van de werk belasting. Zie sharding en [functionele partitionering](#functional-partitioning)voor [meerdere data bases](#cross-database-sharding) voor meer informatie.

- **Toepassingen met sub-optimale query's**

  Toepassingen, met name de Data Access-laag, die slecht afgestemde query's hebben, zijn mogelijk niet in aanmerking voor een hogere reken grootte. Dit omvat query's die geen WHERE-component bevatten, geen indexen hebben of verouderde statistieken hebben. Deze toepassingen profiteren van standaard technieken voor het afstemmen van query's. Zie [ontbrekende indices](#identifying-and-adding-missing-indexes) en [query tuning and Hinting](#query-tuning-and-hinting)(Engelstalig) voor meer informatie.

- **Toepassingen met een suboptimaal ontwerp voor gegevens toegang**

   Toepassingen met inherente problemen met Data Access-gelijktijdigheid, zoals deadlock, kunnen mogelijk niet profiteren van een hogere reken grootte. Overweeg het verminderen van de retouren voor de data base door gegevens op de client te cachen met de Azure-cache service of een andere cache technologie. Zie [caching in de toepassings laag](#application-tier-caching).

## <a name="tune-your-database"></a>Uw data base afstemmen

In deze sectie kijken we naar enkele technieken die u kunt gebruiken voor het afstemmen van de data base om de beste prestaties voor uw toepassing te krijgen en deze op de laagst mogelijke reken grootte uit te voeren. Sommige van deze technieken komen overeen met de traditionele SQL Server afstemming aanbevolen procedures, maar andere zijn specifiek voor Azure SQL Database en Azure SQL Managed instance. In sommige gevallen kunt u de verbruikte resources voor een Data Base onderzoeken om te zoeken naar gebieden voor het verder afstemmen en uitbreiden van traditionele SQL Server technieken om te werken in Azure SQL Database en Azure SQL Managed instance.

### <a name="identifying-and-adding-missing-indexes"></a>Ontbrekende indexen identificeren en toevoegen

Een veelvoorkomend probleem in de OLTP-database prestaties is gekoppeld aan het ontwerp van de fysieke data base. Database schema's zijn vaak ontworpen en verzonden zonder dat ze op schaal worden getest (in de belasting of in het gegevens volume). Helaas is het mogelijk dat de prestaties van een query plan acceptabel zijn op een kleine schaal, maar aanzienlijk lager zijn dan gegevens volumes op productie niveau. De meest voorkomende bron van dit probleem is het ontbreken van geschikte indexen om te voldoen aan filters of andere beperkingen in een query. Vaak ontbreken indexen van indexen als tabel scan wanneer een zoek opdracht in de index toereikend zou zijn.

In dit voor beeld gebruikt het geselecteerde query plan een scan wanneer een zoek actie voldoende is:

```sql
DROP TABLE dbo.missingindex;
CREATE TABLE dbo.missingindex (col1 INT IDENTITY PRIMARY KEY, col2 INT);
DECLARE @a int = 0;
SET NOCOUNT ON;
BEGIN TRANSACTION
    WHILE @a < 20000
    BEGIN
        INSERT INTO dbo.missingindex(col2) VALUES (@a);
        SET @a += 1;
    END
    COMMIT TRANSACTION;
    GO
SELECT m1.col1
    FROM dbo.missingindex m1 INNER JOIN dbo.missingindex m2 ON(m1.col1=m2.col1)
    WHERE m1.col2 = 4;
```

![Een query plan met ontbrekende indexen](./media/performance-guidance/query_plan_missing_indexes.png)

Azure SQL Database en Azure SQL Managed instance kunnen u helpen bij het vinden en corrigeren van algemene ontbrekende index voorwaarden. Dmv's die zijn ingebouwd in Azure SQL Database en Azure SQL Managed instance kijken naar compilaties van query's waarbij een index de geschatte kosten aanzienlijk reduceert voor het uitvoeren van een query. Tijdens het uitvoeren van query's, wordt door de data base-engine bijgehouden hoe vaak elk query plan wordt uitgevoerd, en wordt de geschatte tussen ruimte tussen het uitvoeren van het query plan en de Voorst Ellen voor de index bijgehouden. U kunt deze Dmv's gebruiken om snel te raden welke wijzigingen in het ontwerp van uw fysieke data base de totale werk belasting voor een Data Base en de werkelijke werk belasting kunnen verbeteren.

U kunt deze query gebruiken om mogelijke ontbrekende indexen te evalueren:

```sql
SELECT
   CONVERT (varchar, getdate(), 126) AS runtime
   , mig.index_group_handle
   , mid.index_handle
   , CONVERT (decimal (28,1), migs.avg_total_user_cost * migs.avg_user_impact *
        (migs.user_seeks + migs.user_scans)) AS improvement_measure
   , 'CREATE INDEX missing_index_' + CONVERT (varchar, mig.index_group_handle) + '_' +
        CONVERT (varchar, mid.index_handle) + ' ON ' + mid.statement + '
        (' + ISNULL (mid.equality_columns,'')
        + CASE WHEN mid.equality_columns IS NOT NULL
        AND mid.inequality_columns IS NOT NULL
        THEN ',' ELSE '' END + ISNULL (mid.inequality_columns, '') + ')'
        + ISNULL (' INCLUDE (' + mid.included_columns + ')', '') AS create_index_statement
   , migs.*
   , mid.database_id
   , mid.[object_id]
FROM sys.dm_db_missing_index_groups AS mig
   INNER JOIN sys.dm_db_missing_index_group_stats AS migs
      ON migs.group_handle = mig.index_group_handle
   INNER JOIN sys.dm_db_missing_index_details AS mid
      ON mig.index_handle = mid.index_handle
 ORDER BY migs.avg_total_user_cost * migs.avg_user_impact * (migs.user_seeks + migs.user_scans) DESC
```

In dit voor beeld heeft de query de volgende suggestie:

```sql
CREATE INDEX missing_index_5006_5005 ON [dbo].[missingindex] ([col2])  
```

Nadat de SELECT-instructie is gemaakt, wordt er een ander plan gekozen, waarbij een zoek opdracht wordt gebruikt in plaats van een scan, waarna het plan efficiënter wordt uitgevoerd:

![Een query plan met gecorrigeerde indexen](./media/performance-guidance/query_plan_corrected_indexes.png)

Het belangrijkste inzicht is dat de i/o-capaciteit van een gedeeld, grondstoffen systeem meer beperkt is dan dat van een dedicated server machine. Er is een Premium-fout opgetreden bij het minimaliseren van onnodige IO om Maxi maal te profiteren van het systeem in de resources van elke reken grootte van de service lagen. De juiste mogelijkheden voor het ontwerpen van fysieke data bases kunnen de latentie van afzonderlijke query's aanzienlijk verbeteren, de door Voer van gelijktijdige aanvragen die per schaal eenheid worden verwerkt, verbeteren en de kosten die nodig zijn om aan de query te voldoen, minimaliseren. Zie [sys.dm_db_missing_index_details](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-details-transact-sql)voor meer informatie over de ontbrekende index dmv's.

### <a name="query-tuning-and-hinting"></a>Verfijning van query's en hints

De query Optimizer in Azure SQL Database en Azure SQL Managed instance is vergelijkbaar met de traditionele SQL Server query optimalisatie. De meeste aanbevolen procedures voor het afstemmen van query's en het leren van de beperkingen van het model voor de query optimalisatie zijn ook van toepassing op Azure SQL Database en Azure SQL Managed instance. Als u query's afstemt in Azure SQL Database en Azure SQL Managed instance, kunt u het extra voor deel krijgen van het verminderen van geaggregeerde resource vereisten. Uw toepassing kan mogelijk tegen lagere kosten worden uitgevoerd dan een niet-afgestemd equivalent omdat het kan worden uitgevoerd op een lagere reken grootte.

Een voor beeld dat gebruikelijk is in SQL Server en die ook van toepassing is op Azure SQL Database en Azure SQL Managed instance is de para meters voor query optimalisatie ' sniffes '. Tijdens de compilatie evalueert de query Optimizer de huidige waarde van een para meter om te bepalen of het een optimaal query plan kan genereren. Hoewel deze strategie vaak kan leiden tot een query plan dat aanzienlijk sneller is dan een plan dat is gecompileerd zonder bekende parameter waarden, werkt momenteel niet perfect in SQL Server, in Azure SQL Database en Azure SQL Managed instance. Soms wordt de para meter niet gesniffd en soms wordt de para meter gesniffd, maar wordt het gegenereerde plan suboptimaal voor de volledige set parameter waarden in een werk belasting. Micro soft bevat query hints (instructies), zodat u een doel bewuster opzet kunt opgeven en het standaard gedrag van parameter-sniffing wilt overschrijven. Als u bijvoorbeeld hints gebruikt, kunt u aanvragen oplossen waarin de standaard SQL Server, Azure SQL Database en het gedrag van Azure SQL Managed instance niet ideaal zijn voor een specifieke klant belasting.

In het volgende voor beeld ziet u hoe de query processor een plan kan genereren dat zowel voor prestatie-als resource vereisten een subsysteem is. In dit voor beeld ziet u ook dat als u een query Hint gebruikt, u de uitvoerings tijd van query's en de resource vereisten voor uw data base kunt beperken:

```sql
DROP TABLE psptest1;
CREATE TABLE psptest1(col1 int primary key identity, col2 int, col3 binary(200));
DECLARE @a int = 0;
SET NOCOUNT ON;
BEGIN TRANSACTION
   WHILE @a < 20000
   BEGIN
     INSERT INTO psptest1(col2) values (1);
     INSERT INTO psptest1(col2) values (@a);
     SET @a += 1;
   END
   COMMIT TRANSACTION
   CREATE INDEX i1 on psptest1(col2);
GO

CREATE PROCEDURE psp1 (@param1 int)
   AS
   BEGIN
      INSERT INTO t1 SELECT * FROM psptest1
      WHERE col2 = @param1
      ORDER BY col2;
    END
    GO

CREATE PROCEDURE psp2 (@param2 int)
   AS
   BEGIN
      INSERT INTO t1 SELECT * FROM psptest1 WHERE col2 = @param2
      ORDER BY col2
      OPTION (OPTIMIZE FOR (@param2 UNKNOWN))
   END
   GO

CREATE TABLE t1 (col1 int primary key, col2 int, col3 binary(200));
GO
```

Met de installatie code wordt een tabel gemaakt die gescheefe gegevens distributie heeft. Het optimale query plan wijkt af van de para meter die is geselecteerd. Helaas wordt de query niet altijd opnieuw gecompileerd op basis van de meest voorkomende parameter waarde. Het is dus mogelijk dat een suboptimaal plan in de cache wordt opgeslagen en wordt gebruikt voor veel waarden, zelfs wanneer een ander abonnement een beter plan is dat het beste kan worden gekozen. Vervolgens worden in het query plan twee opgeslagen procedures gemaakt die identiek zijn, behalve dat er een speciale query Hint is.

```sql
-- Prime Procedure Cache with scan plan
EXEC psp1 @param1=1;
TRUNCATE TABLE t1;

-- Iterate multiple times to show the performance difference
DECLARE @i int = 0;
WHILE @i < 1000
   BEGIN
      EXEC psp1 @param1=2;
      TRUNCATE TABLE t1;
      SET @i += 1;
    END
```

We raden u aan om ten minste tien minuten te wachten voordat u begint deel 2 van het voor beeld, zodat de resultaten in de gegevens van de telemetrie verschillen.

```sql
EXEC psp2 @param2=1;
TRUNCATE TABLE t1;

DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp2 @param2=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END
```

Elk deel van dit voor beeld probeert een geparametriseerde INSERT-instructie van 1.000 keer uit te voeren (om een voldoende belasting te genereren om te gebruiken als test gegevensset). Wanneer er opgeslagen procedures worden uitgevoerd, onderzoekt de query processor de parameter waarde die wordt door gegeven aan de procedure tijdens de eerste compilatie (para meter "sniffing"). De processor slaat het resulterende plan op en gebruikt dit voor latere aanroepen, zelfs als de waarde van de para meter niet overeenkomt. Het optimale plan wordt mogelijk niet in alle gevallen gebruikt. Soms moet u het optimalisatie programma gebruiken om een plan te kiezen dat beter is voor het gemiddelde hoofdletter gebruik in plaats van het specifieke geval van de eerste compilatie van de query. In dit voor beeld wordt het eerste plan een ' scan-plan ' gegenereerd dat alle rijen leest om elke waarde te vinden die overeenkomt met de para meter:

![Afstemmen van query's met behulp van een scan plan](./media/performance-guidance/query_tuning_1.png)

Omdat we de procedure met behulp van de waarde 1 hebben uitgevoerd, is het resulterende plan voor de waarde 1 optimaal, maar is het voor alle andere waarden in de tabel het meest optimaal. Het resultaat is waarschijnlijk niet wat u zou doen als u elk plan wille keurig wilt kiezen, omdat het plan langzamer wordt uitgevoerd en meer bronnen gebruikt.

Als u de test uitvoert met `SET STATISTICS IO` ingesteld op `ON` , wordt de logische scan in dit voor beeld uitgevoerd achter de schermen. U kunt zien dat er 1.148-Lees bewerkingen worden uitgevoerd door het plan (dat is inefficiënt als het gemiddelde hoofdletter gebruik slechts één rij retourneert):

![Afstemmen van query's met behulp van een logische scan](./media/performance-guidance/query_tuning_2.png)

In het tweede gedeelte van het voor beeld wordt een query Hint gebruikt om te geven dat de Optimizer een specifieke waarde tijdens het compilatie proces gebruikt. In dit geval wordt de query processor geforceerd de waarde die wordt door gegeven als de para meter, genegeerd en wordt er in plaats daarvan aangenomen `UNKNOWN` . Dit verwijst naar een waarde die de gemiddelde frequentie in de tabel bevat (scheef trekken wordt genegeerd). Het resulterende plan is een op een zoek gebaseerd plan dat sneller is en gebruikmaakt van minder resources, gemiddeld dan het plan in deel 1 van dit voor beeld:

![Afstemmen van query's met behulp van een query Hint](./media/performance-guidance/query_tuning_3.png)

U kunt het effect in de tabel **sys.resource_stats** bekijken (er is een vertraging van de tijd dat u de test uitvoert en wanneer de gegevens in de tabel worden ingevuld). Voor dit voor beeld wordt deel 1 uitgevoerd tijdens het 22:25:00-tijd venster en deel 2 uitgevoerd om 22:35:00. In het vorige tijd venster werden meer resources in dat tijd venster gebruikt dan in een later stadium (vanwege verbeteringen in de plan efficiëntie).

```sql
SELECT TOP 1000 *
FROM sys.resource_stats
WHERE database_name = 'resource1'
ORDER BY start_time DESC
```

![Voorbeeld resultaten voor het afstemmen van query's](./media/performance-guidance/query_tuning_4.png)

> [!NOTE]
> Hoewel het volume in dit voor beeld opzettelijk klein is, kan het effect van suboptimale para meters aanzienlijk zijn, met name voor grotere data bases. Het verschil kan in uitzonderlijke gevallen tussen seconden liggen voor snelle gevallen en uren voor trage gevallen.

U kunt **sys.resource_stats** onderzoeken om te bepalen of de resource voor een test meer of minder resources gebruikt dan een andere test. Wanneer u gegevens vergelijkt, moet u de timing van tests scheiden, zodat ze zich niet in hetzelfde venster van vijf minuten bevinden in de weer gave **sys.resource_stats** . Het doel van de oefening is het minimaliseren van de totale hoeveelheid gebruikte resources en niet om de piek bronnen te minimaliseren. Over het algemeen is het optimaliseren van een stukje code voor latentie het verbruik van resources verminderd. Zorg ervoor dat de wijzigingen die u aanbrengt in een toepassing nood zakelijk zijn en dat de wijzigingen geen negatieve invloed hebben op de klant ervaring voor iemand die mogelijk query hints in de toepassing gebruikt.

Als een werk belasting een set herhalende query's heeft, is het vaak zinvol om de optimale plannings opties vast te leggen en te valideren, omdat deze de minimale resource grootte-eenheid die is vereist voor het hosten van de data base. Nadat u het hebt gevalideerd, kunt u de plannen opnieuw onderzoeken om u ervan te verzekeren dat ze niet zijn gedegradeerd. U kunt meer te weten komen over [query hints (Transact-SQL)](/sql/t-sql/queries/hints-transact-sql-query).

### <a name="very-large-database-architectures"></a>Zeer grote database architecturen

Vóór de release van de [grootschalige](service-tier-hyperscale.md) -service tier voor afzonderlijke data bases in Azure SQL database, hebben klanten de capaciteits limieten voor individuele data bases bereikt. Deze capaciteits limieten bestaan nog voor gegroepeerde Data bases in Azure SQL Database elastische Pools en instantie-data bases in Azure SQL Managed instances. In de volgende twee secties worden twee opties besproken voor het oplossen van problemen met zeer grote data bases in Azure SQL Database en Azure SQL Managed instance wanneer u de grootschalige-servicelaag niet kunt gebruiken.

### <a name="cross-database-sharding"></a>Sharding voor meerdere data bases

Omdat Azure SQL Database en Azure SQL Managed instance worden uitgevoerd op grondstoffen producten, zijn de capaciteits limieten voor een afzonderlijke data base lager dan voor een traditionele on-premises SQL Server installatie. Sommige klanten gebruiken sharding-technieken om database bewerkingen te spreiden over meerdere data bases wanneer de bewerkingen niet passen binnen de limieten van een afzonderlijke data base in Azure SQL Database en Azure SQL Managed instance. De meeste klanten die gebruikmaken van sharding-technieken in Azure SQL Database en Azure SQL Managed instance hebben hun gegevens verdeeld over één dimensie over meerdere data bases. Voor deze aanpak moet u weten dat OLTP-toepassingen vaak trans acties uitvoeren die van toepassing zijn op één rij of op een kleine groep rijen in het schema.

> [!NOTE]
> Azure SQL Database biedt nu een bibliotheek om u te helpen bij sharding. Zie Elastic Database Overzicht van de [client bibliotheek](elastic-database-client-library.md)voor meer informatie.

Als een Data Base bijvoorbeeld de naam van de klant, de order en de order gegevens bevat (zoals de traditionele voorbeeld database noorden wind die bij SQL Server is geleverd), kunt u deze gegevens in meerdere data bases splitsen door een klant te groeperen met de gerelateerde order-en order detail informatie. U kunt garanderen dat de gegevens van de klant in een afzonderlijke data base blijven. De toepassing zou verschillende klanten in data bases splitsen, waardoor de belasting in meerdere data bases effectief kan worden verdeeld. Met sharding kunnen klanten niet alleen de maximale grootte van de data base vermijden, maar Azure SQL Database en Azure SQL Managed instance kunnen werk belastingen verwerken die aanzienlijk groter zijn dan de limieten van de verschillende reken grootten, zolang elke afzonderlijke data base past in de limieten van de servicelaag.

Hoewel de sharding van de data base de totale resource capaciteit voor een oplossing niet reduceert, is deze zeer effectief bij het ondersteunen van zeer grote oplossingen die over meerdere data bases zijn verdeeld. Elke Data Base kan worden uitgevoerd met een andere reken grootte ter ondersteuning van zeer grote, ' effectief ' data bases met hoge resource vereisten.

#### <a name="functional-partitioning"></a>Functionele partitionering

Gebruikers combi neren vaak veel functies in een afzonderlijke data base. Als een toepassing bijvoorbeeld logica heeft voor het beheren van de inventarisatie voor een archief, heeft die data base mogelijk logica die is gekoppeld aan de inventarisatie, het bijhouden van inkoop orders, opgeslagen procedures en geïndexeerde of gerealiseerde weer gaven waarmee de rapportage van eind maanden wordt beheerd. Deze techniek maakt het gemakkelijker om de data base te beheren voor bewerkingen als back-up, maar u moet er ook voor zorgen dat u de grootte van de hardware verwerkt om de piek belasting over alle functies van een toepassing af te handelen.

Als u een scale-out-architectuur gebruikt in Azure SQL Database en Azure SQL Managed instance, is het een goed idee om verschillende functies van een toepassing te splitsen in verschillende data bases. Met deze techniek schaalt elke toepassing onafhankelijk van elkaar. Als een toepassing wordt busier (en de belasting van de data base neemt toe), kan de beheerder onafhankelijke reken grootten kiezen voor elke functie in de toepassing. Met deze architectuur kan een toepassing groter zijn dan één computer met een groot aantal dat kan worden verwerkt, omdat de belasting over meerdere machines kan worden verdeeld.

### <a name="batch-queries"></a>Batch-query's

Voor toepassingen die toegang hebben tot gegevens met behulp van hoog volume, frequente ad hoc query's, wordt een aanzienlijke hoeveelheid reactie tijd besteed aan netwerk communicatie tussen de toepassingslaag en de gegevenslaag. Zelfs wanneer zowel de toepassing als de data base zich in hetzelfde Data Center bevinden, kan de netwerk latentie tussen de twee worden verg root door een groot aantal bewerkingen voor gegevens toegang. Als u de netwerk round trips voor de gegevens toegangs bewerkingen wilt reduceren, kunt u de optie gebruiken om de ad-hoc-query's te batch of te compileren als opgeslagen procedures. Als u de ad-hoc query's batcheert, kunt u meerdere query's verzenden als één grote batch in één reis naar de data base. Als u ad-hoc query's in een opgeslagen procedure compileert, kunt u hetzelfde resultaat opleveren als bij de batch-subquery's. Het gebruik van een opgeslagen procedure biedt u het voor deel van het verg Roten van de kans op het in de cache plaatsen van de query plannen in de data base, zodat u de opgeslagen procedure opnieuw kunt gebruiken.

Sommige toepassingen zijn write-intensief. Soms kunt u de totale i/o-belasting voor een Data Base verminderen door te overwegen hoe u batches tegelijk schrijft. Vaak is dit net zo eenvoudig als het gebruik van expliciete trans acties in plaats van trans acties met automatische door Voer in opgeslagen procedures en ad hoc batches. Zie [batch-technieken voor database toepassingen in azure](../performance-improve-use-batching.md)voor een evaluatie van de verschillende technieken die u kunt gebruiken. Experimenteer met uw eigen werk belasting om het juiste model voor batch verwerking te vinden. Houd er rekening mee dat een model weinig verschillende garanties voor transactionele consistentie kan hebben. Het vinden van de juiste werk belasting voor het minimaliseren van het resource gebruik vereist het vinden van de juiste combi natie van consistentie en prestatie verhouding.

### <a name="application-tier-caching"></a>Caching op toepassings niveau

Sommige database toepassingen hebben lees-zware werk belastingen. Cache lagen kunnen de belasting van de data base verminderen en mogelijk de berekenings grootte verminderen die nodig is om een Data Base te ondersteunen met behulp van Azure SQL Database en Azure SQL Managed instance. Als u [Azure cache voor redis](https://azure.microsoft.com/services/cache/)hebt, kunt u, als u een lees bare hoeveelheid werk belasting hebt, de gegevens eenmaal lezen (of misschien eenmaal per toepassingslaag, afhankelijk van de configuratie van de computer) en vervolgens die gegevens opslaan buiten uw data base. Dit is een manier om de belasting van de data base te reduceren (CPU en lees-IO), maar er is een effect op transactionele consistentie, omdat de gegevens die vanuit de cache worden gelezen, mogelijk niet synchroon zijn met de gegevens in de data base. Hoewel er in veel toepassingen een inconsistentie niveau is toegestaan, is dat niet waar voor alle werk belastingen. U moet volledige inzicht krijgen in alle toepassings vereisten voordat u een cache strategie op het niveau van de toepassingslaag implementeert.

## <a name="next-steps"></a>Volgende stappen

- Zie voor meer informatie over op DTU gebaseerde service lagen [het op DTU gebaseerde aankoop model](service-tiers-dtu.md).
- Zie voor meer informatie over vCore-gebaseerde service lagen [op vCore gebaseerd aankoop model](service-tiers-vcore.md).
- Zie [Wat is een elastische Azure-pool?](elastic-pool-overview.md) voor meer informatie over elastische Pools.
- Voor informatie over prestaties en elastische Pools raadpleegt u [Wanneer u een elastische pool overweegt](elastic-pool-overview.md)
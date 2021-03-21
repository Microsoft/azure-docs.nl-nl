---
title: Zoeken in Azure SQL-gegevens
titleSuffix: Azure Cognitive Search
description: Gegevens importeren uit Azure SQL Database of SQL Managed instance met Indexeer functies, voor zoeken in volledige tekst in azure Cognitive Search. In dit artikel worden de verbindingen, de configuratie van de Indexeer functie en gegevens opname beschreven.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/12/2020
ms.openlocfilehash: 04e4801c26b0ac8ef91af0b028d9dc2bb9a3cd1c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94358623"
---
# <a name="connect-to-and-index-azure-sql-content-using-an-azure-cognitive-search-indexer"></a>Verbinding maken met Azure SQL-inhoud en deze indexeren met behulp van een Azure Cognitive Search Indexeer functie

Voordat u een query kunt uitvoeren op een [Azure Cognitive search-index](search-what-is-an-index.md), moet u deze vullen met uw gegevens. Als de gegevens zich in Azure SQL Database of SQL Managed instance bevinden, kan een **Azure Cognitive Search Indexeer functie voor Azure SQL database** (of **Azure SQL Indexeer functie** ) het indexerings proces automatiseren, wat betekent dat er minder code moet worden geschreven en dat de infra structuur minder goed is.

In dit artikel worden de mechanismen besproken van het gebruik van [Indexeer](search-indexer-overview.md)functies, maar hierin worden ook de onderdelen beschreven die alleen beschikbaar zijn voor Azure SQL database of SQL Managed instance (bijvoorbeeld geïntegreerde wijzigingen bijhouden). 

Naast Azure SQL Database en SQL Managed instance biedt Azure Cognitive Search Indexeer functies voor [Azure Cosmos DB](search-howto-index-cosmosdb.md), [Azure Blob-opslag](search-howto-indexing-azure-blob-storage.md)en Azure- [tabel opslag](search-howto-indexing-azure-tables.md). Als u ondersteuning voor andere gegevens bronnen wilt aanvragen, geeft u uw feedback op het [Feedback forum van Azure Cognitive Search](https://feedback.azure.com/forums/263029-azure-search/).

## <a name="indexers-and-data-sources"></a>Indexeer functies en gegevens bronnen

Een **gegevens bron** specificeert welke gegevens moeten worden geïndexeerd, referenties voor de toegang tot gegevens en het beleid waarmee de wijzigingen in de gegevens (nieuwe, gewijzigde of verwijderde rijen) efficiënt worden geïdentificeerd. Het wordt gedefinieerd als een onafhankelijke resource, zodat deze kan worden gebruikt door meerdere Indexeer functies.

Een **Indexeer functie** is een resource die één gegevens bron verbindt met een doel gerichte zoek index. Een Indexeer functie wordt op de volgende manieren gebruikt:

* Voer een eenmalige kopie van de gegevens uit om een index te vullen.
* Een index bijwerken met wijzigingen in de gegevens bron volgens een schema.
* Voer op aanvraag uit om een index naar behoefte bij te werken.

Eén Indexeer functie kan slechts één tabel of weer gave gebruiken, maar u kunt meerdere Indexeer functies maken als u meerdere zoek indexen wilt vullen. Zie voor meer informatie over concepten [index bewerkingen: typische werk stroom](/rest/api/searchservice/Indexer-operations#typical-workflow).

U kunt een Azure SQL-indexer instellen en configureren met:

* Wizard gegevens importeren in de [Azure Portal](https://portal.azure.com)
* Azure Cognitive Search [.NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexer)
* Azure Cognitive Search [rest API](/rest/api/searchservice/indexer-operations)

In dit artikel gebruiken we de REST API om **Indexeer functies** en **gegevens bronnen** te maken.

## <a name="when-to-use-azure-sql-indexer"></a>Wanneer u Azure SQL Indexeer functie wilt gebruiken
Afhankelijk van verschillende factoren die betrekking hebben op uw gegevens, is het gebruik van Azure SQL Indexeer functie mogelijk of niet geschikt. Als uw gegevens aan de volgende vereisten voldoen, kunt u Azure SQL Indexeer functie gebruiken.

| Criteria | Details |
|----------|---------|
| De gegevens zijn afkomstig uit één tabel of weer gave | Als de gegevens worden verspreid over meerdere tabellen, kunt u één weer gave van de gegevens maken. Als u echter een weer gave gebruikt, kunt u SQL Server geïntegreerde wijzigings detectie niet gebruiken om een index met incrementele wijzigingen te vernieuwen. Zie voor meer informatie [vastleggen van gewijzigde en verwijderde rijen](#CaptureChangedRows) hieronder. |
| Gegevens typen zijn compatibel | De meeste SQL-typen worden niet ondersteund in een Azure Cognitive Search-index. Zie [gegevens typen toewijzen](#TypeMapping)voor een lijst. |
| Realtime gegevens synchronisatie is niet vereist | Een Indexeer functie kan de tabel Maxi maal elke vijf minuten opnieuw indexeren. Als uw gegevens regel matig worden gewijzigd en de wijzigingen binnen enkele seconden of enkele minuten moeten worden weer gegeven in de index, kunt u het beste de [rest API](/rest/api/searchservice/AddUpdate-or-Delete-Documents) of [.NET SDK](./search-get-started-dotnet.md) gebruiken om bijgewerkte rijen rechtstreeks te pushen. |
| Incrementele indexering is mogelijk | Als u een grote gegevensset hebt en de Indexeer functie volgens een planning wilt uitvoeren, moet Azure Cognitive Search de nieuwe, gewijzigde of verwijderde rijen efficiënt kunnen identificeren. Niet-incrementele indexering is alleen toegestaan als u op aanvraag indexeert (niet op schema) of als u minder dan 100.000 rijen indexeert. Zie voor meer informatie [vastleggen van gewijzigde en verwijderde rijen](#CaptureChangedRows) hieronder. |

> [!NOTE] 
> Azure Cognitive Search ondersteunt alleen SQL Server-verificatie. Als u ondersteuning nodig hebt voor wachtwoord verificatie voor Azure Active Directory, kunt u stemmen voor deze [UserVoice-suggestie](https://feedback.azure.com/forums/263029-azure-search/suggestions/33595465-support-azure-active-directory-password-authentica).

## <a name="create-an-azure-sql-indexer"></a>Een Azure SQL-Indexeer functie maken

1. De gegevens bron maken:

   ```
    POST https://myservice.search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "name of the table or view that you want to index" }
    }
   ```

   Het connection string kan een van de volgende indelingen volgen:
    1. U kunt de connection string ophalen via de [Azure Portal](https://portal.azure.com); Gebruik de `ADO.NET connection string` optie.
    1. Een beheerde identiteit connection string die geen account sleutel bevat met de volgende indeling: `Initial Catalog|Database=<your database name>;ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.Sql/servers/<your SQL Server name>/;Connection Timeout=connection timeout length;` . Als u deze connection string wilt gebruiken, volgt u de instructies voor het [instellen van een indexer-verbinding met een Azure SQL database met behulp van een beheerde identiteit](search-howto-managed-identities-sql.md).

2. Maak de doel-Azure-Cognitive Search index als u er nog geen hebt. U kunt een index maken met behulp van de [Portal](https://portal.azure.com) of de [Create Index-API](/rest/api/searchservice/Create-Index). Zorg ervoor dat het schema van uw doel index compatibel is met het schema van de bron tabel. Zie [toewijzing tussen SQL en Azure cognitieve Zoek gegevens typen](#TypeMapping).

3. Maak de Indexeer functie door deze een naam te geven en te verwijzen naar de gegevens bron en doel index:

   ```
    POST https://myservice.search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }
   ```

Een Indexeer functie die op deze manier is gemaakt, heeft geen planning. Het wordt automatisch uitgevoerd wanneer het wordt gemaakt. U kunt dit op elk gewenst moment opnieuw uitvoeren met behulp van een **Run indexer** -aanvraag:

```
    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2020-06-30
    api-key: admin-key
```

U kunt verschillende aspecten van het gedrag van de Indexeer functie aanpassen, zoals de Batch grootte en het aantal documenten dat kan worden overgeslagen voordat de uitvoering van een index mislukt. Zie [Create Indexing API](/rest/api/searchservice/Create-Indexer)(Engelstalig) voor meer informatie.

Mogelijk moet u Azure-Services toestaan verbinding te maken met uw data base. Zie [verbinding maken vanuit Azure](../azure-sql/database/firewall-configure.md) voor instructies over hoe u dit doet.

Als u de Indexeer functie en uitvoerings geschiedenis wilt bewaken (aantal geïndexeerde items, fouten enz.), gebruikt u een **Indexeer functie status** aanvraag:

```
    GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2020-06-30
    api-key: admin-key
```

Het antwoord moet er ongeveer als volgt uitzien:

```
    {
        "\@odata.context":"https://myservice.search.windows.net/$metadata#Microsoft.Azure.Search.V2015_02_28.IndexerExecutionInfo",
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2015-02-21T00:23:24.957Z",
            "endTime":"2015-02-21T00:36:47.752Z",
            "errors":[],
            "itemsProcessed":1599501,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        },
        "executionHistory":
        [
            {
                "status":"success",
                "errorMessage":null,
                "startTime":"2015-02-21T00:23:24.957Z",
                "endTime":"2015-02-21T00:36:47.752Z",
                "errors":[],
                "itemsProcessed":1599501,
                "itemsFailed":0,
                "initialTrackingState":null,
                "finalTrackingState":null
            },
            ... earlier history items
        ]
    }
```

De uitvoerings geschiedenis bevat tot 50 van de meest recent voltooide uitvoeringen, die in de omgekeerde chronologische volg orde worden gesorteerd (zodat de meest recente uitvoering eerst in het antwoord komt).
Meer informatie over het antwoord vindt u in de [status van de Indexeer functie ophalen](/rest/api/searchservice/get-indexer-status)

## <a name="run-indexers-on-a-schedule"></a>Indexeer functies uitvoeren volgens een schema
U kunt de Indexeer functie ook zo rangschikken dat deze periodiek volgens een planning wordt uitgevoerd. Als u dit wilt doen, voegt u de eigenschap **schema** toe wanneer u de Indexeer functie maakt of bijwerkt. In het onderstaande voor beeld ziet u een PUT-aanvraag om de Indexeer functie bij te werken:

```
    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }
```

De **interval** parameter is vereist. Het interval verwijst naar de tijd tussen het begin van twee opeenvolgende indexerings uitvoeringen. Het kleinste toegestane interval is 5 minuten. de langste is één dag. Deze moet worden ingedeeld als een XSD ' dayTimeDuration-waarde (een beperkte subset van een [ISO 8601 duration](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) -waarde). Het patroon hiervoor is: `P(nD)(T(nH)(nM))` . Voor beelden: elke `PT15M` 2 uur voor elke 15 minuten `PT2H` .

Zie [Indexeer functies plannen voor Azure Cognitive Search](search-howto-schedule-indexers.md)voor meer informatie over het definiëren van de planningen voor de Indexeer functie.

<a name="CaptureChangedRows"></a>

## <a name="capture-new-changed-and-deleted-rows"></a>Nieuwe, gewijzigde en verwijderde rijen vastleggen

Azure Cognitive Search gebruikt **incrementele indexering** om te voor komen dat de volledige tabel of weer gave telkens opnieuw moet worden geïndexeerd wanneer een Indexeer functie wordt uitgevoerd. Azure Cognitive Search biedt twee beleids regels voor wijzigingen detectie ter ondersteuning van incrementele indexering. 

### <a name="sql-integrated-change-tracking-policy"></a>Wijzigingen bijhouden-beleid voor SQL-integratie
Als uw SQL database het [bijhouden van wijzigingen](/sql/relational-databases/track-changes/about-change-tracking-sql-server)ondersteunt, kunt u het beste **SQL Integrated wijzigingen bijhouden-beleid** gebruiken. Dit is het meest efficiënte beleid. Daarnaast kunt u met Azure Cognitive Search verwijderde rijen identificeren zonder dat u een expliciete kolom ' voorlopig verwijderen ' aan de tabel hoeft toe te voegen.

#### <a name="requirements"></a>Vereisten 

+ Database versie vereisten:
  * SQL Server 2012 SP3 en hoger als u SQL Server op virtuele Azure-machines gebruikt.
  * Azure SQL Database of SQL Managed instance.
+ Alleen tabellen (geen weer gaven). 
+ Schakel op de data base het [bijhouden van wijzigingen](/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server) in voor de tabel. 
+ Er is geen samengestelde primaire sleutel (een primaire sleutel met meer dan één kolom) in de tabel.  

#### <a name="usage"></a>Gebruik

Als u dit beleid wilt gebruiken, maakt of werkt u de gegevens bron als volgt bij:

```
    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy"
      }
    }
```

Wanneer u het geïntegreerde beleid voor het bijhouden van wijzigingen van SQL gebruikt, moet u geen afzonderlijk detectie beleid voor gegevens verwijdering opgeven. dit beleid heeft ingebouwde ondersteuning voor het identificeren van verwijderde rijen. Als de verwijderingen echter "automagiceel" worden gedetecteerd, moet de document sleutel in uw zoek index hetzelfde zijn als de primaire sleutel in de SQL-tabel. 

> [!NOTE]  
> Wanneer u [Truncate Table](/sql/t-sql/statements/truncate-table-transact-sql) gebruikt om een groot aantal rijen uit een SQL-tabel te verwijderen, moet de Indexeer functie [opnieuw worden ingesteld](/rest/api/searchservice/reset-indexer) om de status van het bijhouden van wijzigingen opnieuw in te stellen om het verwijderen van rijen te herstellen.

<a name="HighWaterMarkPolicy"></a>

### <a name="high-water-mark-change-detection-policy"></a>Detectie beleid voor het wijzigen van hoog water merken

Dit beleid voor wijzigings detectie is afhankelijk van een kolom met een hoog water merk waarin de versie of het tijdstip wordt vastgelegd waarop een rij voor het laatst is bijgewerkt. Als u een weer gave gebruikt, moet u een beleid voor hoog water merk gebruiken. De kolom hoog water merk moet voldoen aan de volgende vereisten.

#### <a name="requirements"></a>Vereisten 

* Alle inserts geven een waarde voor de kolom op.
* Alle updates van een item wijzigen ook de waarde van de kolom.
* De waarde van deze kolom neemt toe met elke INSERT of update.
* Query's met de volgende WHERE-en ORDER BY-componenten kunnen efficiënt worden uitgevoerd: `WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`

> [!IMPORTANT] 
> We raden u ten zeerste aan het gegevens type [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) te gebruiken voor de grote water markerings kolom. Als een ander gegevens type wordt gebruikt, kan het bijhouden van wijzigingen niet worden gegarandeerd om alle veranderingen te registreren in de aanwezigheid van trans acties die gelijktijdig worden uitgevoerd met een indexerings query. Wanneer u **rowversion** gebruikt in een configuratie met alleen-lezen replica's, moet u de Indexeer functie op de primaire replica aanwijzen. Alleen een primaire replica kan worden gebruikt voor gegevens synchronisatie scenario's.

#### <a name="usage"></a>Gebruik

Als u een beleid met een hoog water merk wilt gebruiken, maakt of werkt u de gegevens bron als volgt bij:

```
    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
           "highWaterMarkColumnName" : "[a rowversion or last_updated column name]"
      }
    }
```

> [!WARNING]
> Als de bron tabel geen index heeft op de bovengrens kolom, kan er een time-out optreden bij query's die worden gebruikt door de SQL-Indexeer functie. In het bijzonder moet de- `ORDER BY [High Water Mark Column]` component een index efficiënt uitvoeren wanneer de tabel veel rijen bevat.
>
>

<a name="convertHighWaterMarkToRowVersion"></a>

##### <a name="converthighwatermarktorowversion"></a>convertHighWaterMarkToRowVersion

Als u een [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) -gegevens type gebruikt voor de kolom hoog water merk, kunt u overwegen de configuratie-instelling voor de `convertHighWaterMarkToRowVersion` Indexeer functie te gebruiken. `convertHighWaterMarkToRowVersion` heeft twee dingen:

* Gebruik het gegevens type rowversion voor de kolom hoog water merk in de SQL-query van de Indexeer functie. Met het juiste gegevens type verbetert de prestaties van index query's.
* Trek 1 af van de waarde van rowversion voordat de indexer-query wordt uitgevoerd. Weer gaven met 1 tot veel samen voegingen kunnen rijen met dubbele rowversion-waarden bevatten. Als u 1 aftrekt, zorgt u ervoor dat de Indexeer functie-query deze rijen niet mist.

Als u deze functie wilt inschakelen, moet u de indexer maken of bijwerken met de volgende configuratie:

```
    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "convertHighWaterMarkToRowVersion" : true } }
    }
```

<a name="queryTimeout"></a>

##### <a name="querytimeout"></a>queryTimeout

Als er time-outfouten optreden, kunt u de instelling van de `queryTimeout` Indexeer functie gebruiken om de querytime-out in te stellen op een waarde die hoger is dan de standaard time-out van 5 minuten. Als u de time-out bijvoorbeeld wilt instellen op 10 minuten, maakt u de Indexeer functie of werkt u deze bij met de volgende configuratie:

```
    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }
```

<a name="disableOrderByHighWaterMarkColumn"></a>

##### <a name="disableorderbyhighwatermarkcolumn"></a>disableOrderByHighWaterMarkColumn

U kunt de component ook uitschakelen `ORDER BY [High Water Mark Column]` . Dit wordt echter niet aanbevolen omdat als de uitvoering van de Indexeer functie wordt onderbroken door een fout, de Indexeer functie alle rijen opnieuw moet verwerken als deze later wordt uitgevoerd, zelfs als de Indexeer functie bijna alle rijen al heeft verwerkt op het moment dat deze werd onderbroken. Als u de `ORDER BY` component wilt uitschakelen, gebruikt u de `disableOrderByHighWaterMarkColumn` instelling in de definitie van de Indexeer functie:  

```
    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "disableOrderByHighWaterMarkColumn" : true } }
    }
```

### <a name="soft-delete-column-deletion-detection-policy"></a>Voorlopig verwijderings beleid voor het verwijderen van kolommen
Wanneer rijen uit de bron tabel worden verwijderd, wilt u waarschijnlijk ook deze rijen verwijderen uit de zoek index. Als u het geïntegreerde beleid voor het bijhouden van wijzigingen van SQL gebruikt, is dit van belang voor u. Het beleid voor het bijhouden van een hoog water merk biedt echter geen ondersteuning voor verwijderde rijen. Wat u moet doen?

Als de rijen fysiek uit de tabel worden verwijderd, heeft Azure Cognitive Search geen manier om de aanwezigheid van records die niet meer bestaan af te leiden.  U kunt echter de techniek ' zacht-delete ' gebruiken om rijen op logische wijze te verwijderen zonder ze uit de tabel te verwijderen. Een kolom toevoegen aan de tabel of rijen weer geven en markeren als verwijderd met behulp van die kolom.

Wanneer u de techniek voor zacht verwijderen gebruikt, kunt u het beleid voor voorlopig verwijderen als volgt opgeven bij het maken of bijwerken van de gegevens Bron:

```
    {
        …,
        "dataDeletionDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]",
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]"
        }
    }
```

De **softDeleteMarkerValue** moet een teken reeks zijn: gebruik de teken reeks representatie van de werkelijke waarde. Als u bijvoorbeeld een kolom met gehele getallen hebt waarin verwijderde rijen worden gemarkeerd met de waarde 1, gebruikt u `"1"` . Als u een BIT-kolom hebt waarin verwijderde rijen worden gemarkeerd met de Booleaanse waarde True, gebruikt u de letterlijke teken reeks `True` of `true` is het hoofdletter gebruik.

<a name="TypeMapping"></a>

## <a name="mapping-between-sql-and-azure-cognitive-search-data-types"></a>Toewijzing tussen SQL-en Azure Cognitive Search-gegevens typen
| SQL-gegevenstype | Toegestane doel index veld typen | Notities |
| --- | --- | --- |
| bit |EDM. Boolean, EDM. String | |
| int, smallint, tinyint |EDM. Int32, EDM. Int64, EDM. String | |
| bigint |EDM. Int64, EDM. String | |
| Real, float |Edm.Double, Edm.String | |
| smallmoney, geld decimaal numeriek |Edm.String |Azure Cognitive Search biedt geen ondersteuning voor het converteren van decimale typen naar EDM. double, omdat dit de precisie zou verliezen |
| char, NCHAR, varchar, nvarchar |Edm.String<br/>Collection(EDM.String) |Een SQL-teken reeks kan worden gebruikt om een verzameling (EDM. String) in te vullen als de teken reeks een JSON-matrix met teken reeksen vertegenwoordigt: `["red", "white", "blue"]` |
| Smalldatetime, datetime, DATETIME2, date, date time offset |Edm.DateTimeOffset, Edm.String | |
| uniqueidentifer |Edm.String | |
| Geografie |Edm.GeographyPoint |Alleen geografie-exemplaren van het type POINT met SRID 4326 (dit is de standaard instelling) worden ondersteund |
| rowversion |N.v.t. |Rij-versie kolommen kunnen niet worden opgeslagen in de zoek index, maar kunnen worden gebruikt voor het bijhouden van wijzigingen |
| tijd, time span, binary, varbinary, afbeelding, XML, geometrie, CLR-typen |N.v.t. |Niet ondersteund |

## <a name="configuration-settings"></a>Configuratie-instellingen
SQL Indexeer functie maakt verschillende configuratie-instellingen beschikbaar:

| Instelling | Gegevenstype | Doel | Standaardwaarde |
| --- | --- | --- | --- |
| queryTimeout |tekenreeks |Hiermee stelt u de time-out voor de uitvoering van SQL-query's |5 minuten ("00:05:00") |
| disableOrderByHighWaterMarkColumn |booleaans |Zorgt ervoor dat de SQL-query die wordt gebruikt door het beleid voor hoog water merk, de component ORDER BY weglaat. Zie [beleid voor hoog water merk](#HighWaterMarkPolicy) |onjuist |

Deze instellingen worden gebruikt in het `parameters.configuration` object in de definitie van de Indexeer functie. Als u de time-out van de query bijvoorbeeld wilt instellen op 10 minuten, moet u de Indexeer functie maken of bijwerken met de volgende configuratie:

```
    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }
```

## <a name="faq"></a>Veelgestelde vragen

**V: kan ik Azure SQL indexer gebruiken met SQL-data bases die worden uitgevoerd op IaaS Vm's in azure?**

Ja. U moet de zoek service echter toestaan om verbinding te maken met uw data base. Zie [een verbinding van een azure Cognitive Search Indexeer functie configureren op SQL Server op een Azure VM](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)voor meer informatie.

**V: kan ik Azure SQL indexer gebruiken met SQL-data bases die on-premises worden uitgevoerd?**

Niet rechtstreeks. Het wordt niet aanbevolen om een directe verbinding te ondersteunen. als dit niet het geval is, moet u uw data bases openen voor Internet verkeer. Klanten hebben het volgende met behulp van Bridge-technologieën, zoals Azure Data Factory, geslaagd. Zie [gegevens pushen naar een Azure Cognitive search-index met behulp van Azure Data Factory](../data-factory/v1/data-factory-azure-search-connector.md)voor meer informatie.

**V: kan ik Azure SQL Indexeer functie gebruiken met andere data bases dan SQL Server die worden uitgevoerd in IaaS op Azure?**

Nee. Dit scenario wordt niet ondersteund omdat de Indexeer functie niet is getest met andere data bases dan SQL Server.  

**V: kan ik meerdere Indexeer functies maken die op een planning worden uitgevoerd?**

Ja. Er kan echter maar één Indexeer functie tegelijk op één knoop punt worden uitgevoerd. Als u meerdere Indexeer functies nodig hebt die gelijktijdig worden uitgevoerd, kunt u overwegen om uw zoek service naar meer dan één Zoek eenheid te schalen.

**V: het uitvoeren van een Indexeer functie is van invloed op de werk belasting van mijn query**

Ja. Indexeer functie wordt uitgevoerd op een van de knoop punten in uw zoek service, en de resources van dat knoop punt worden gedeeld tussen het indexeren en het leveren van query verkeer en andere API-aanvragen. Als u intensieve indexerings-en query werk belastingen uitvoert, een hoog tempo van 503 fouten ondervindt of de reactie tijden toeneemt, kunt u overwegen [uw zoek service omhoog te schalen](search-capacity-planning.md).

**V: kan ik een secundaire replica in een [failovercluster](../azure-sql/database/auto-failover-group-overview.md) als gegevens bron gebruiken?**

Dat hangt ervan af. Voor een volledige indexering van een tabel of weer gave kunt u een secundaire replica gebruiken. 

Voor incrementele indexering ondersteunt Azure Cognitive Search twee beleids regels voor wijzigingen detectie: geïntegreerde wijzigingen bijhouden in SQL en hoge water merken.

Bij alleen-lezen replica's ondersteunt SQL Database geïntegreerde wijzigingen bijhouden niet. Daarom moet u beleid voor hoog water merk gebruiken. 

Onze standaard aanbeveling is het gegevens type rowversion te gebruiken voor de kolom hoog water merk. Het gebruik van rowversion is echter afhankelijk van de `MIN_ACTIVE_ROWVERSION` functie, wat niet wordt ondersteund voor alleen-lezen replica's. Daarom moet u de Indexeer functie naar een primaire replica verwijzen als u rowversion gebruikt.

Als u probeert rowversion te gebruiken op een alleen-lezen replica, ziet u de volgende fout: 

Het gebruik van een rowversion-kolom voor het bijhouden van wijzigingen wordt niet ondersteund voor secundaire (alleen-lezen) beschikbaarheids replica's. Werk de gegevens bron bij en geef een verbinding met de primaire beschikbaarheids replica op. De huidige eigenschap voor het bijwerken van de data base is READ_ONLY.

**V: kan ik een alternatieve, niet-rowversion-kolom gebruiken voor het bijhouden van hoog water merk wijzigingen?**

Het wordt niet aanbevolen. Alleen **rowversion** biedt ondersteuning voor betrouw bare gegevens synchronisatie. Afhankelijk van uw toepassings logica kan het echter veilig zijn als:

+ U kunt ervoor zorgen dat wanneer de Indexeer functie wordt uitgevoerd, er geen openstaande trans acties zijn in de tabel die wordt geïndexeerd (bijvoorbeeld: alle tabel updates worden als een batch volgens een planning en het Azure Cognitive Search indexer schema is ingesteld om te voor komen dat de planning voor de tabel update overlapt).  

+ U voert regel matig een volledige REINDEX uit om gemiste rijen op te halen.
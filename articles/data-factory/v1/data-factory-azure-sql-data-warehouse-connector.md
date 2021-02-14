---
title: Gegevens kopiëren van/naar Azure Synapse Analytics
description: Meer informatie over het kopiëren van gegevens naar/van Azure Synapse Analytics met behulp van Azure Data Factory
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: aa364ec434db980bf226008537ca928628fcac1b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100392082"
---
# <a name="copy-data-to-and-from-azure-synapse-analytics-using-azure-data-factory"></a>Gegevens kopiëren van en naar Azure Synapse Analytics met behulp van Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](data-factory-azure-sql-data-warehouse-connector.md)
> * [Versie 2 (huidige versie)](../connector-azure-sql-data-warehouse.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u de [Azure Synapse Analytics-connector in v2](../connector-azure-sql-data-warehouse.md).

In dit artikel wordt uitgelegd hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens te verplaatsen van/naar Azure Synapse Analytics. Het is gebaseerd op het artikel [activiteiten voor gegevens verplaatsing](data-factory-data-movement-activities.md) , dat een algemeen overzicht geeft van de verplaatsing van gegevens met de Kopieer activiteit.

> [!TIP]
> Gebruik poly Base om gegevens te laden in azure Synapse Analytics om de beste prestaties te krijgen. De sectie [poly Base gebruiken voor het laden van gegevens in azure Synapse Analytics](#use-polybase-to-load-data-into-azure-synapse-analytics) bevat details. Zie voor een overzicht met een use-case [1 TB in azure Synapse Analytics onder 15 minuten laden met Azure Data Factory](data-factory-load-sql-data-warehouse.md).

## <a name="supported-scenarios"></a>Ondersteunde scenario's
U kunt gegevens **uit Azure Synapse Analytics** kopiëren naar de volgende gegevens archieven:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

U kunt gegevens uit de volgende gegevens archieven kopiëren **naar Azure Synapse Analytics**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!TIP]
> Wanneer u gegevens van SQL Server of Azure SQL Database naar Azure Synapse Analytics kopieert en u de tabel niet in het doel archief bevindt, Data Factory kunt u de tabel in azure Synapse Analytics automatisch maken met behulp van het schema van de tabel in de gegevens opslag van de bron. Zie [automatische tabel maken](#auto-table-creation) voor meer informatie.

## <a name="supported-authentication-type"></a>Ondersteund verificatie type
Azure Synapse Analytics-connector ondersteunen basis verificatie.

## <a name="getting-started"></a>Aan de slag
U kunt een pijp lijn maken met een Kopieer activiteit die gegevens verplaatst van/naar een Azure Synapse-analyse met behulp van verschillende hulpprogram ma's/Api's.

De eenvoudigste manier om een pijp lijn te maken waarmee gegevens naar/van Azure Synapse Analytics worden gekopieerd, is door de wizard gegevens kopiëren te gebruiken. Zie [zelf studie: gegevens laden in azure Synapse Analytics met Data Factory](../load-azure-sql-data-warehouse.md) voor een snelle walkthrough over het maken van een pijp lijn met behulp van de wizard gegevens kopiëren.

U kunt ook de volgende hulpprogram ma's gebruiken om een pijp lijn te maken: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager sjabloon**, **.net API** en **rest API**. Zie [zelf studie Kopieer activiteit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stapsgewijze instructies voor het maken van een pijp lijn met een Kopieer activiteit.

Ongeacht of u de hulpprogram ma's of Api's gebruikt, voert u de volgende stappen uit om een pijp lijn te maken waarmee gegevens uit een brongegevens archief naar een Sink-gegevens archief worden verplaatst:

1. Maak een **Data Factory**. Een data factory kan een of meer pijp lijnen bevatten. 
2. Maak **gekoppelde services** om invoer-en uitvoer gegevens archieven te koppelen aan uw Data Factory. Als u bijvoorbeeld gegevens kopieert van een Azure Blob-opslag naar een Azure Synapse Analytics, maakt u twee gekoppelde services om uw Azure-opslag account en Azure Synapse Analytics te koppelen aan uw data factory. Zie de sectie [Eigenschappen van gekoppelde service](#linked-service-properties) voor gekoppelde service-eigenschappen die specifiek zijn voor Azure Synapse Analytics. 
3. Gegevens **sets** maken om invoer-en uitvoer gegevens voor de Kopieer bewerking weer te geven. In het voor beeld in de laatste stap maakt u een gegevensset om de BLOB-container en de map op te geven die de invoer gegevens bevat. En u maakt een andere gegevensset om de tabel op te geven in de Azure Synapse Analytics die de gegevens bevat die zijn gekopieerd uit de Blob-opslag. Zie de sectie [Eigenschappen](#dataset-properties) van gegevensset voor eigenschappen van een gegevensset die specifiek zijn voor Azure Synapse Analytics.
4. Maak een **pijp lijn** met een Kopieer activiteit die een gegevensset als invoer en een gegevensset als uitvoer gebruikt. In het eerder genoemde voor beeld gebruikt u BlobSource als bron en SqlDWSink als Sink voor de Kopieer activiteit. En als u kopieert van Azure Synapse Analytics naar Azure Blob Storage, gebruikt u SqlDWSource en BlobSink in de Kopieer activiteit. Zie de sectie [Eigenschappen van Kopieer](#copy-activity-properties) activiteit voor meer informatie over de eigenschappen van de Kopieer activiteit die specifiek zijn voor Azure Synapse Analytics. Voor meer informatie over het gebruik van een gegevens archief als een bron of sink klikt u op de koppeling in de vorige sectie voor uw gegevens archief.

Wanneer u de wizard gebruikt, worden automatisch JSON-definities voor deze Data Factory entiteiten (gekoppelde services, gegevens sets en de pijp lijn) gemaakt. Wanneer u hulpprogram ma's/Api's (met uitzonde ring van .NET API) gebruikt, definieert u deze Data Factory entiteiten met behulp van de JSON-indeling. Zie het gedeelte [JSON-voor beelden](#json-examples-for-copying-data-to-and-from-azure-synapse-analytics) in dit artikel voor steek proeven met JSON-definities voor Data Factory entiteiten die worden gebruikt om gegevens te kopiëren van/naar Azure Synapse Analytics.

De volgende secties bevatten informatie over de JSON-eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor Azure Synapse Analytics:

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service
De volgende tabel bevat een beschrijving van de JSON-elementen die specifiek zijn voor de gekoppelde service van Azure Synapse Analytics.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| type |De eigenschap type moet worden ingesteld op: **AzureSqlDW** |Ja |
| connectionString |Geef de gegevens op die nodig zijn om verbinding te maken met het Azure Synapse Analytics-exemplaar voor de Connections Tring-eigenschap. Alleen basis verificatie wordt ondersteund. |Yes |

> [!IMPORTANT]
> Configureer [Azure SQL database firewall](/previous-versions/azure/ee621782(v=azure.100)#ConnectingFromAzure) en de database server zodanig dat [Azure-Services toegang krijgen tot de server](/previous-versions/azure/ee621782(v=azure.100)#ConnectingFromAzure). Daarnaast kunt u, als u gegevens kopieert naar Azure Synapse Analytics van buiten Azure, inclusief van on-premises gegevens bronnen met data factory gateway, het juiste IP-adres bereik configureren voor de computer die gegevens naar Azure Synapse Analytics verzendt.

## <a name="dataset-properties"></a>Eigenschappen van gegevensset
Zie het artikel [gegevens sets maken](data-factory-create-datasets.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Secties zoals structuur, Beschik baarheid en beleid van een gegevensset-JSON zijn vergelijkbaar voor alle typen gegevens sets (Azure SQL, Azure Blob, Azure Table, enzovoort).

De sectie typeProperties verschilt voor elk type gegevensset en bevat informatie over de locatie van de gegevens in het gegevens archief. De sectie **typeProperties** voor de gegevensset van het type **AzureSqlDWTable** heeft de volgende eigenschappen:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| tableName |De naam van de tabel of weer gave in de Azure Synapse Analytics-Data Base waarnaar de gekoppelde service verwijst. |Yes |

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit
Zie het artikel [pijp lijnen maken](data-factory-create-pipelines.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Eigenschappen zoals naam, beschrijving, invoer-en uitvoer tabellen en beleid zijn beschikbaar voor alle typen activiteiten.

> [!NOTE]
> De Kopieer activiteit heeft slechts één invoer en produceert slechts één uitvoer.

Terwijl de eigenschappen die beschikbaar zijn in de sectie typeProperties van de activiteit, verschillen per activiteitstype. Voor kopieer activiteiten zijn ze afhankelijk van de typen bronnen en Sinks.

### <a name="sqldwsource"></a>SqlDWSource
Wanneer bron van het type **SqlDWSource** is, zijn de volgende eigenschappen beschikbaar in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| sqlReaderQuery |Gebruik de aangepaste query om gegevens te lezen. |SQL-query teken reeks. Bijvoorbeeld: Select * from MyTable. |No |
| sqlReaderStoredProcedureName |De naam van de opgeslagen procedure waarmee gegevens uit de bron tabel worden gelezen. |De naam van de opgeslagen procedure. De laatste SQL-instructie moet een instructie SELECT in de opgeslagen procedure zijn. |No |
| storedProcedureParameters |Para meters voor de opgeslagen procedure. |Naam/waarde-paren. Namen en hoofdletter gebruik van para meters moeten overeenkomen met de namen en de behuizing van de opgeslagen procedure parameters. |No |

Als de **sqlReaderQuery** is opgegeven voor de SqlDWSource, voert de Kopieer activiteit deze query uit op basis van de Azure Synapse Analytics-bron om de gegevens op te halen.

U kunt ook een opgeslagen procedure opgeven door de **sqlReaderStoredProcedureName** en **storedProcedureParameters** op te geven (als voor de opgeslagen procedure para meters worden gebruikt).

Als u sqlReaderQuery of sqlReaderStoredProcedureName niet opgeeft, worden de kolommen die in het gedeelte structuur van de JSON van de gegevensset zijn gedefinieerd, gebruikt voor het maken van een query die wordt uitgevoerd op Azure Synapse Analytics. Bijvoorbeeld: `select column1, column2 from mytable`. Als de definitie van de gegevensset niet de structuur heeft, worden alle kolommen geselecteerd in de tabel.

#### <a name="sqldwsource-example"></a>SqlDWSource-voor beeld

```JSON
"source": {
    "type": "SqlDWSource",
    "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
    "storedProcedureParameters": {
        "stringData": { "value": "str3" },
        "identifier": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
    }
}
```
**De definitie van de opgeslagen procedure:**

```SQL
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
    select *
    from dbo.UnitTestSrcTable
    where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="sqldwsink"></a>SqlDWSink
**SqlDWSink** ondersteunt de volgende eigenschappen:

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |Geef een query op voor de Kopieer activiteit die moet worden uitgevoerd, zodat de gegevens van een specifiek segment worden opgeruimd. Zie de [sectie REPEAT baarheid](#repeatability-during-copy)voor meer informatie. |Een query-instructie. |No |
| allowPolyBase |Hiermee wordt aangegeven of poly base (indien van toepassing) moet worden gebruikt in plaats van BULKINSERT-mechanisme. <br/><br/> **Het gebruik van poly Base is de aanbevolen manier om gegevens in azure Synapse Analytics te laden.** Zie [poly Base gebruiken om gegevens te laden in de sectie Azure Synapse Analytics](#use-polybase-to-load-data-into-azure-synapse-analytics) voor beperkingen en Details. |Waar <br/>False (standaard) |No |
| polyBaseSettings |Een groep eigenschappen die kan worden opgegeven wanneer de eigenschap **allowPolybase** is ingesteld op **True**. |&nbsp; |No |
| rejectValue |Hiermee geeft u het aantal of percentage rijen op dat kan worden afgewezen voordat de query mislukt. <br/><br/>Meer informatie over de opties voor het afwijzen van poly bases vindt u in de sectie **argumenten** van het onderwerp [externe tabel maken (Transact-SQL)](/sql/t-sql/statements/create-external-table-transact-sql) . |0 (standaard), 1, 2,... |No |
| rejectType |Hiermee wordt aangegeven of de optie rejectValue is opgegeven als een letterlijke waarde of een percentage. |Waarde (standaard), percentage |No |
| rejectSampleValue |Bepaalt het aantal rijen dat moet worden opgehaald voordat de poly base het percentage geweigerde rijen opnieuw berekent. |1, 2,... |Ja, als **rejectType** een **percentage** is |
| useTypeDefault |Hiermee geeft u op hoe ontbrekende waarden in tekst bestanden met scheidings tekens moeten worden verwerkt wanneer poly base gegevens ophaalt uit het tekst bestand.<br/><br/>Meer informatie over deze eigenschap vindt u in de sectie argumenten in [externe BESTANDS indeling maken (Transact-SQL)](/sql/t-sql/statements/create-external-file-format-transact-sql). |True, False (standaard) |No |
| writeBatchSize |Hiermee worden gegevens in de SQL-tabel ingevoegd wanneer de buffer grootte writeBatchSize bereikt |Geheel getal (aantal rijen) |Nee (standaard: 10000) |
| writeBatchTimeout |Wacht tijd voordat de batch INSERT-bewerking is voltooid voordat er een time-out optreedt. |tijdsbestek<br/><br/> Voor beeld: "00:30:00" (30 minuten). |No |

#### <a name="sqldwsink-example"></a>SqlDWSink-voor beeld

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true
}
```

## <a name="use-polybase-to-load-data-into-azure-synapse-analytics"></a>Poly Base gebruiken voor het laden van gegevens in azure Synapse Analytics
Het gebruik van **[poly base](/sql/relational-databases/polybase/polybase-guide)** is een efficiënte manier om grote hoeveel heden gegevens in azure Synapse Analytics te laden met een hoge door voer. U kunt een grote toename in de door Voer zien door poly Base te gebruiken in plaats van het standaard BULKINSERT-mechanisme. Zie [prestatie referentie nummer kopiëren](data-factory-copy-activity-performance.md#performance-reference) met gedetailleerde vergelijking. Zie voor een overzicht met een use-case [1 TB in azure Synapse Analytics onder 15 minuten laden met Azure Data Factory](data-factory-load-sql-data-warehouse.md).

* Als uw bron gegevens zich in **Azure Blob of Azure data Lake Store** bevindt en de indeling compatibel is met poly Base, kunt u rechtstreeks naar Azure Synapse Analytics kopiëren met poly base. Zie **[direct kopiëren met poly base](#direct-copy-using-polybase)** met details.
* Als uw brongegevens archief en-indeling niet oorspronkelijk worden ondersteund door poly Base, kunt u in plaats daarvan de **[gefaseerde kopie gebruiken met](#staged-copy-using-polybase)** de functie voor het maken van een poly base. Het biedt ook een betere door voer door de gegevens automatisch te converteren naar een indeling die compatibel is met poly base en de gegevens op te slaan in Azure Blob Storage. Vervolgens worden gegevens geladen in azure Synapse Analytics.

Stel de `allowPolyBase` eigenschap in op **True (waar** ), zoals wordt weer gegeven in het volgende voor beeld voor Azure Data Factory om poly Base te gebruiken om gegevens te kopiëren naar Azure Synapse Analytics. Wanneer u allowPolyBase instelt op True, kunt u polybase-specifieke eigenschappen opgeven met behulp van de `polyBaseSettings` Eigenschappen groep. Zie de sectie [SqlDWSink](#sqldwsink) voor meer informatie over eigenschappen die u kunt gebruiken met polyBaseSettings.

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

### <a name="direct-copy-using-polybase"></a>Direct kopiëren met poly base
Azure Synapse Analytics poly Base ondersteunt direct Azure Blob en Azure Data Lake Store (met Service-Principal) als bron en met specifieke vereisten voor bestands indelingen. Als uw bron gegevens voldoen aan de criteria die in deze sectie worden beschreven, kunt u rechtstreeks vanuit de bron gegevens opslag naar Azure Synapse Analytics kopiëren met poly base. Anders kunt u een [gefaseerde kopie gebruiken met poly base](#staged-copy-using-polybase).

> [!TIP]
> Als u gegevens wilt kopiëren van Data Lake Store naar Azure Synapse Analytics, kunt u meer leren van [Azure Data Factory het nog eenvoudiger en handig maken om inzichten van gegevens te ontdekken wanneer u data Lake Store met Azure Synapse Analytics gebruikt](/archive/blogs/azuredatalake/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse).

Als niet aan de vereisten wordt voldaan, worden de instellingen door Azure Data Factory gecontroleerd en wordt automatisch teruggeleid naar het BULKINSERT-mechanisme voor het verplaatsen van gegevens.

1. De **gekoppelde bron service** is van het type: **opslag** of **AzureDataLakeStore met Service-Principal-verificatie**.
2. De **invoer-gegevensset** is van het type: **AzureBlob** of **AzureDataLakeStore**, en de notatie type onder `type` Eigenschappen is **OrcFormat**, **ParquetFormat** of **TextFormat** met de volgende configuraties:

   1. `rowDelimiter` moet **\n** zijn.
   2. `nullValue` is ingesteld op een **lege teken reeks** ("") of `treatEmptyAsNull` is ingesteld op **True**.
   3. `encodingName` is ingesteld op **UTF-8**, de **standaard** waarde.
   4. `escapeChar`, `quoteChar` , `firstRowAsHeader` en `skipLineCount` zijn niet opgegeven.
   5. `compression` kan **geen compressie**, **gzip** of **Deflate** zijn.

      ```JSON
      "typeProperties": {
       "folderPath": "<blobpath>",
       "format": {
           "type": "TextFormat",
           "columnDelimiter": "<any delimiter>",
           "rowDelimiter": "\n",
           "nullValue": "",
           "encodingName": "utf-8"
       },
       "compression": {
           "type": "GZip",
           "level": "Optimal"
       }
      },
      ```

3. Er is geen `skipHeaderLineCount` instelling onder **BlobSource** of **AzureDataLakeStore** voor de Kopieer activiteit in de pijp lijn.
4. Er is geen `sliceIdentifierColumnName` instelling onder **SqlDWSink** voor de Kopieer activiteit in de pijp lijn. (Poly base garandeert dat alle gegevens worden bijgewerkt of er niets wordt bijgewerkt in één run. U kunt voor **Herhaal baarheid** gebruikmaken van `sqlWriterCleanupScript` ).
5. Er wordt geen `columnMapping` gebruik gemaakt van de koppeling in de Kopieer activiteit.

### <a name="staged-copy-using-polybase"></a>Gefaseerde kopie met poly base
Als uw bron gegevens niet voldoen aan de criteria die in de vorige sectie zijn geïntroduceerd, kunt u het kopiëren van gegevens via een tijdelijke Azure Blob Storage voor fase ring inschakelen (kan niet Premium Storage zijn). In dit geval voert Azure Data Factory automatisch trans formaties uit op de gegevens om te voldoen aan de vereisten van de gegevens indeling van poly base. vervolgens gebruikt u poly Base om gegevens te laden in azure Synapse Analytics en bij het laatste opschonen van de tijdelijke gegevens uit de Blob-opslag. Zie [gefaseerde kopie](data-factory-copy-activity-performance.md#staged-copy) voor meer informatie over de manier waarop het kopiëren van gegevens via een faserings-Azure-Blob in het algemeen werkt.

> [!NOTE]
> Bij het kopiëren van gegevens uit een on-premises gegevens opslag naar Azure Synapse Analytics met poly base en fase ring, als uw Data Management Gateway-versie onder 2,4 is, is JRE (Java Runtime Environment) vereist op uw gateway computer die wordt gebruikt om de bron gegevens in de juiste indeling te zetten. U kunt het beste een upgrade uitvoeren van uw gateway naar de nieuwste om deze afhankelijkheid te voor komen.
>

Als u deze functie wilt gebruiken, moet u een [Azure Storage gekoppelde service](data-factory-azure-blob-connector.md#azure-storage-linked-service) maken die verwijst naar het Azure Storage-account met de tussenliggende Blob-opslag. vervolgens geeft u de `enableStaging` `stagingSettings` Eigenschappen en op voor de Kopieer activiteit, zoals wordt weer gegeven in de volgende code:

```json
"activities":[
{
    "name": "Sample copy activity from SQL Server to Azure Synapse Analytics via PolyBase",
    "type": "Copy",
    "inputs": [{ "name": "OnpremisesSQLServerInput" }],
    "outputs": [{ "name": "AzureSQLDWOutput" }],
    "typeProperties": {
        "source": {
            "type": "SqlSource",
        },
        "sink": {
            "type": "SqlDwSink",
            "allowPolyBase": true
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyStagingBlob"
        }
    }
}
]
```

## <a name="best-practices-when-using-polybase"></a>Aanbevolen procedures voor het gebruik van poly base
De volgende secties bevatten aanvullende aanbevolen procedures voor de methoden die worden genoemd in [Aanbevolen procedures voor Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Vereiste database machtiging
Als u poly Base wilt gebruiken, moet de gebruiker die wordt gebruikt om gegevens in azure Synapse Analytics te laden, beschikken over de [machtiging ' beheren '](/sql/relational-databases/security/permissions-database-engine) voor de doel database. Een manier om dat te doen, is om die gebruiker toe te voegen als lid van de rol db_owner. Meer informatie over hoe u dit doet door [deze sectie](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization)te volgen.

### <a name="row-size-and-data-type-limitation"></a>Beperking van Rijgrootte en gegevens type
Poly base-belastingen zijn beperkt tot het laden van rijen die kleiner zijn dan **1 MB** en kunnen niet worden geladen in VARCHR (max), NVARCHAR (max) of varbinary (max). Raadpleeg [hier](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Als u Bron gegevens hebt met een grootte die groter is dan 1 MB, wilt u de bron tabellen wellicht verticaal splitsen in verschillende kleine items waar de grootste Rijgrootte van elk daarvan de limiet niet overschrijdt. De kleinere tabellen kunnen vervolgens worden geladen met poly base en samen worden samengevoegd in azure Synapse Analytics.

### <a name="azure-synapse-analytics-resource-class"></a>Azure Synapse Analytics-resource klasse
Overweeg om een grotere resource klasse toe te wijzen aan de gebruiker die wordt gebruikt voor het laden van gegevens in azure Synapse Analytics via Poly Base om de best mogelijke door voer te verzorgen. Meer informatie over hoe u dit doet door [een voor beeld van een gebruikers resource klasse te wijzigen](../../synapse-analytics/sql-data-warehouse/resource-classes-for-workload-management.md).

### <a name="tablename-in-azure-synapse-analytics"></a>TableName in azure Synapse Analytics
De volgende tabel bevat voor beelden van de manier waarop u de eigenschap **TableName** opgeeft in de JSON van de gegevensset voor verschillende combi Naties van schema en tabel naam.

| DB-schema | Tabelnaam | tabel naam JSON-eigenschap |
| --- | --- | --- |
| dbo |MyTable |MyTable of dbo. MyTable of [dbo]. MyTable |
| dbo1 |MyTable |dbo1. MyTable of [dbo1]. MyTable |
| dbo |Mijn. table |[Mijn. table] of [dbo]. [Mijn. tabel] |
| dbo1 |Mijn. table |[dbo1]. [Mijn. tabel] |

Als u de volgende fout ziet, kan het zijn dat er een probleem is met de waarde die u hebt opgegeven voor de eigenschap TableName. Zie de tabel voor de juiste manier om waarden op te geven voor de JSON-eigenschap TableName.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Kolommen met standaard waarden
Op dit moment kan de poly base-functie in Data Factory alleen hetzelfde aantal kolommen accepteren als in de doel tabel. Stel dat u een tabel hebt met vier kolommen en een van de tabellen is gedefinieerd met een standaard waarde. De invoer gegevens moeten nog steeds vier kolommen bevatten. Als u een gegevensset met een invoer van drie kolommen opgeeft, wordt er een fout met de volgende strekking weer gegeven:

```
All columns of the table must be specified in the INSERT BULK statement.
```
NULL-waarde is een speciale vorm van standaard waarde. Als de kolom null-waarden bevat, kunnen de invoer gegevens (in BLOB) voor die kolom leeg zijn (de invoer gegevensset kan niet ontbreken). Met poly Base worden NULL-waarden ingevoegd in azure Synapse Analytics.

## <a name="auto-table-creation"></a>Automatische tabel maken
Als u de wizard kopiëren gebruikt om gegevens te kopiëren van SQL Server of Azure SQL Database naar Azure Synapse Analytics en de tabel die overeenkomt met de bron tabel niet bestaat in het doel archief, Data Factory kunt u de tabel in het Data Warehouse automatisch maken met behulp van het schema van de bron tabel.

Data Factory maakt de tabel in het doel archief met dezelfde tabel naam in de gegevens opslag van de bron. De gegevens typen voor kolommen worden gekozen op basis van de volgende type toewijzing. Indien nodig voert het type conversies uit om eventuele incompatibiliteiten tussen bron-en doel archieven op te lossen. Er wordt ook Round Robin Table-distributie gebruikt.

| Type bron SQL Database kolom | Doel van Azure Synapse Analytics-kolom Type (beperking van grootte) |
| --- | --- |
| Int | Int |
| BigInt | BigInt |
| SmallInt | SmallInt |
| TinyInt | TinyInt |
| Bitmask | Bitmask |
| Decimaal | Decimaal |
| Numeriek | Decimaal |
| Float | Float |
| Money | Money |
| Realistische | Realistische |
| SmallMoney | SmallMoney |
| Binair | Binair |
| Varbinary | Varbinary (Maxi maal 8000) |
| Date | Date |
| DateTime | DateTime |
| DateTime2 | DateTime2 |
| Tijd | Tijd |
| Date time offset | Date time offset |
| SmallDateTime | SmallDateTime |
| Tekst | Varchar (Maxi maal 8000) |
| NText | NVarChar (Maxi maal 4000) |
| Installatiekopie | VarBinary (Maxi maal 8000) |
| Unieke | Unieke |
| Char | Char |
| NChar | NChar |
| VarChar | VarChar (Maxi maal 8000) |
| NVarChar | NVarChar (Maxi maal 4000) |
| Xml | Varchar (Maxi maal 8000) |

[!INCLUDE [data-factory-type-repeatability-for-sql-sources](../../../includes/data-factory-type-repeatability-for-sql-sources.md)]

## <a name="type-mapping-for-azure-synapse-analytics"></a>Type toewijzing voor Azure Synapse Analytics
Zoals vermeld in het artikel [activiteiten voor gegevens verplaatsing](data-factory-data-movement-activities.md) voert Kopieer activiteit automatisch type conversies uit van bron typen naar Sink-typen met de volgende twee stappen:

1. Converteren van systeem eigen bron typen naar .NET-type
2. Converteren van .NET-type naar systeem eigen Sink-type

Bij het verplaatsen van gegevens naar & vanuit Azure Synapse Analytics, worden de volgende toewijzingen gebruikt van SQL-type naar .NET-type en vice versa.

De toewijzing is hetzelfde als de [SQL Server gegevens type toewijzing voor ADO.net](/dotnet/framework/data/adonet/sql-server-data-type-mappings).

| Type SQL Server data base-engine | .NET Framework type |
| --- | --- |
| bigint |Int64 |
| binair |Byte [] |
| bit |Booleaans |
| char |Teken reeks, char [] |
| date |DateTime |
| Datum/tijd |DateTime |
| datetime2 |DateTime |
| Date time offset |Date time offset |
| Decimaal |Decimaal |
| FILESTREAM-kenmerk (varbinary (max)) |Byte [] |
| Float |Dubbel |
| image |Byte [] |
| int |Int32 |
| money |Decimaal |
| nchar |Teken reeks, char [] |
| ntext |Teken reeks, char [] |
| numeriek |Decimaal |
| nvarchar |Teken reeks, char [] |
| werkelijk |Enkelvoudig |
| rowversion |Byte [] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimaal |
| sql_variant |Object |
| tekst |Teken reeks, char [] |
| tijd |TimeSpan |
| tijdstempel |Byte [] |
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte [] |
| varchar |Teken reeks, char [] |
| xml |Xml |

U kunt ook kolommen van de bron-gegevensset toewijzen aan kolommen uit Sink-gegevensset in de definitie van de Kopieer activiteit. Zie [gegevensset-kolommen toewijzen in azure Data Factory](data-factory-map-columns.md)voor meer informatie.

## <a name="json-examples-for-copying-data-to-and-from-azure-synapse-analytics"></a>JSON-voor beelden voor het kopiëren van gegevens van en naar Azure Synapse Analytics
De volgende voor beelden bieden voor beeld van JSON-definities die u kunt gebruiken om een pijp lijn te maken met behulp van [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) of [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ze laten zien hoe u gegevens kopieert van en naar Azure Synapse Analytics en Azure Blob Storage. Gegevens kunnen echter **rechtstreeks** vanuit een wille keurige bron worden gekopieerd naar een van de [hier](data-factory-data-movement-activities.md#supported-data-stores-and-formats) opgegeven sinks met behulp van de Kopieer activiteit in azure Data Factory.

### <a name="example-copy-data-from-azure-synapse-analytics-to-azure-blob"></a>Voor beeld: gegevens kopiëren van Azure Synapse Analytics naar Azure Blob
Het voor beeld definieert de volgende Data Factory entiteiten:

1. Een gekoppelde service van het type [AzureSqlDW](#linked-service-properties).
2. Een gekoppelde service van het type [opslag](data-factory-azure-blob-connector.md#linked-service-properties).
3. Een invoer- [gegevensset](data-factory-create-datasets.md) van het type [AzureSqlDWTable](#dataset-properties).
4. Een uitvoer [gegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Een [pijp lijn](data-factory-create-pipelines.md) met een Kopieer activiteit die gebruikmaakt van [SqlDWSource](#copy-activity-properties) en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

In het voor beeld worden elk uur tijd reeksen (per uur, dagelijks, enzovoort) gekopieerd van een tabel in azure Synapse Analytics-Data Base naar een blob. De JSON-eigenschappen die in deze steek proeven worden gebruikt, worden beschreven in secties die volgen op de voor beelden.

**Gekoppelde Azure Synapse Analytics-service:**

```JSON
{
  "name": "AzureSqlDWLinkedService",
  "properties": {
    "type": "AzureSqlDW",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Gekoppelde Azure Blob Storage-service:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Invoer gegevensset voor Azure Synapse Analytics:**

In het voor beeld wordt ervan uitgegaan dat u een tabel ' MyTable ' hebt gemaakt in azure Synapse Analytics en deze een kolom bevat met de naam ' timestampcolumn ' voor tijdreeks gegevens.

Als u ' Extern ' instelt, informeert de Data Factory-service dat de gegevensset extern is voor de data factory en wordt deze niet geproduceerd door een activiteit in de data factory.

```JSON
{
  "name": "AzureSqlDWInput",
  "properties": {
    "type": "AzureSqlDWTable",
    "linkedServiceName": "AzureSqlDWLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Azure Blob-uitvoer gegevensset:**

Gegevens worden elk uur naar een nieuwe BLOB geschreven (frequentie: uur, interval: 1). Het mappad voor de BLOB wordt dynamisch geëvalueerd op basis van de begin tijd van het segment dat wordt verwerkt. Het mappad gebruikt delen van het jaar, de maand, de dag en het uur van de begin tijd.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Kopieer activiteit in een pijp lijn met SqlDWSource en BlobSink:**

De pijp lijn bevat een Kopieer activiteit die is geconfigureerd voor het gebruik van de invoer-en uitvoer gegevens sets en die is gepland om elk uur te worden uitgevoerd. In de JSON-definitie van de pijp lijn is het **bron** type ingesteld op **SqlDWSource** en het **sink** -type is ingesteld op **BlobSink**. Met de SQL-query die is opgegeven voor de eigenschap **SqlReaderQuery** selecteert u de gegevens in het afgelopen uur om te kopiëren.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "AzureSQLDWtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSqlDWInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlDWSource",
            "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
> [!NOTE]
> In het voor beeld is **sqlReaderQuery** opgegeven voor de SqlDWSource. De Kopieer activiteit voert deze query uit op basis van de Azure Synapse Analytics-bron om de gegevens op te halen.
>
> U kunt ook een opgeslagen procedure opgeven door de **sqlReaderStoredProcedureName** en **storedProcedureParameters** op te geven (als voor de opgeslagen procedure para meters worden gebruikt).
>
> Als u sqlReaderQuery of sqlReaderStoredProcedureName niet opgeeft, worden de kolommen die in het gedeelte structuur van de JSON van de gegevensset zijn gedefinieerd, gebruikt om een query te maken (Selecteer Kolom1, kolom2 van mytable) om uit te voeren op Azure Synapse Analytics. Als de definitie van de gegevensset niet de structuur heeft, worden alle kolommen geselecteerd in de tabel.
>
>

### <a name="example-copy-data-from-azure-blob-to-azure-synapse-analytics"></a>Voor beeld: gegevens kopiëren van Azure Blob naar Azure Synapse Analytics
Het voor beeld definieert de volgende Data Factory entiteiten:

1. Een gekoppelde service van het type [AzureSqlDW](#linked-service-properties).
2. Een gekoppelde service van het type [opslag](data-factory-azure-blob-connector.md#linked-service-properties).
3. Een invoer- [gegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Een uitvoer [gegevensset](data-factory-create-datasets.md) van het type [AzureSqlDWTable](#dataset-properties).
5. Een [pijp lijn](data-factory-create-pipelines.md) met een Kopieer activiteit die gebruikmaakt van [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) en [SqlDWSink](#copy-activity-properties).

In het voor beeld worden elk uur gegevens van tijd reeksen (elk uur, dagelijks, enzovoort) gekopieerd van Azure Blob naar een tabel in een Azure Synapse Analytics-Data Base. De JSON-eigenschappen die in deze steek proeven worden gebruikt, worden beschreven in secties die volgen op de voor beelden.

**Gekoppelde Azure Synapse Analytics-service:**

```JSON
{
  "name": "AzureSqlDWLinkedService",
  "properties": {
    "type": "AzureSqlDW",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Gekoppelde Azure Blob Storage-service:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Invoer gegevensset voor Azure Blob:**

Gegevens worden elk uur uit een nieuwe BLOB opgehaald (frequentie: uur, interval: 1). Het mappad en de bestands naam voor de BLOB worden dynamisch geëvalueerd op basis van de begin tijd van het segment dat wordt verwerkt. Het mappad gebruikt het gedeelte Year, month en Day van de begin tijd en de bestands naam maakt gebruik van het uur gedeelte van de begin tijd. met de instelling ' Extern ': ' waar ' wordt de Data Factory-service informeert dat deze tabel extern is voor de data factory en niet wordt geproduceerd door een activiteit in de data factory.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Uitvoer gegevensset Azure Synapse Analytics:**

In het voor beeld worden gegevens gekopieerd naar een tabel met de naam ' MyTable ' in azure Synapse Analytics. Maak de tabel in azure Synapse Analytics met hetzelfde aantal kolommen als u verwacht dat het CSV-bestand van de BLOB bevat. Nieuwe rijen worden elk uur aan de tabel toegevoegd.

```JSON
{
  "name": "AzureSqlDWOutput",
  "properties": {
    "type": "AzureSqlDWTable",
    "linkedServiceName": "AzureSqlDWLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Kopieer activiteit in een pijp lijn met BlobSource en SqlDWSink:**

De pijp lijn bevat een Kopieer activiteit die is geconfigureerd voor het gebruik van de invoer-en uitvoer gegevens sets en die is gepland om elk uur te worden uitgevoerd. In de JSON-definitie van de pijp lijn is het **bron** type ingesteld op **BlobSource** en het **sink** -type is ingesteld op **SqlDWSink**.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoSQLDW",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlDWOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource",
            "blobColumnSeparators": ","
          },
          "sink": {
            "type": "SqlDWSink",
            "allowPolyBase": true
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
Zie voor een overzicht het gedeelte Zie [Load 1 TB in azure Synapse Analytics onder 15 minuten met Azure Data Factory](data-factory-load-sql-data-warehouse.md) en [gegevens laden met Azure Data Factory](../load-azure-sql-data-warehouse.md) artikel in de Azure Synapse Analytics-documentatie.

## <a name="performance-and-tuning"></a>Prestaties en afstemming
Zie [Kopieer activiteit prestaties & afstemmings handleiding](data-factory-copy-activity-performance.md) voor meer informatie over de belangrijkste factoren die invloed hebben op de prestaties van het verplaatsen van gegevens (Kopieer activiteit) in azure Data Factory en verschillende manieren om deze te optimaliseren.
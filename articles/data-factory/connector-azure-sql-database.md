---
title: Gegevens kopiëren en transformeren in Azure SQL Database
description: Informatie over het kopiëren van gegevens van en naar Azure SQL Database en het transformeren van gegevens in Azure SQL Database met behulp van Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: 75615b4bb8773d0c0b8f72278e5598462c779ceb
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365226"
---
# <a name="copy-and-transform-data-in-azure-sql-database-by-using-azure-data-factory"></a>Gegevens kopiëren en transformeren in Azure SQL Database met behulp van Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie Azure Data Factory die u gebruikt:"]
>
> - [Versie 1:](v1/data-factory-azure-sql-connector.md)
> - [Huidige versie](connector-azure-sql-database.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u kopieeractiviteit in Azure Data Factory gebruikt om gegevens van en naar Azure SQL Database te kopiëren, en hoe u Gegevensstroom gebruikt om gegevens in Azure SQL Database. Lees het inleidende Azure Data Factory voor [meer informatie over deze informatie.](introduction.md)

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Azure SQL Database-connector wordt ondersteund voor de volgende activiteiten:

- [Copy-activiteit](copy-activity-overview.md) [ondersteunde bron-/sinkmatrixtabel](copy-activity-overview.md)
- [Toewijzingsgegevensstroom](concepts-data-flow-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)
- [GetMetadata-activiteit](control-flow-get-metadata-activity.md)

Deze Copy-activiteit biedt Azure SQL Database ondersteuning voor deze functies:

- Gegevens kopiëren met behulp van SQL-verificatie en Azure Active Directory (Azure AD) Verificatie van toepassing-token met een service-principal of beheerde identiteiten voor Azure-resources.
- Als bron kunt u gegevens ophalen met behulp van een SQL-query of een opgeslagen procedure. U kunt er ook voor kiezen om parallel te kopiëren vanuit Azure SQL Database bron. Zie de sectie Parallel [kopiëren SQL database](#parallel-copy-from-sql-database) voor meer informatie.
- Als sink maakt u automatisch een doeltabel als deze niet bestaat op basis van het bronschema; gegevens toevoegen aan een tabel of een opgeslagen procedure aanroepen met aangepaste logica tijdens het kopiëren.

Als u Azure SQL Database [serverloze](../azure-sql/database/serverless-tier-overview.md)laag gebruikt, moet u er rekening mee houden dat wanneer de server is onderbroken, de activiteit niet kan worden uitgevoerd in plaats van te wachten tot het automatisch hervatten gereed is. U kunt activiteiten opnieuw proberen of extra activiteiten aan een keten toevoegen om ervoor te zorgen dat de server live is wanneer deze daadwerkelijk wordt uitgevoerd.

>[!NOTE]
> Azure SQL Database [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) wordt nu niet ondersteund door deze connector. U kunt een algemene [ODBC-connector](connector-odbc.md) en een SQL Server ODBC-stuurprogramma gebruiken via een zelf-hostende Integration Runtime. Meer informatie in [de sectie Always Encrypted](#using-always-encrypted) gebruiken. 

> [!IMPORTANT]
> Als u gegevens kopieert met behulp van de Azure Integration Runtime, configureert u een firewallregel op serverniveau zodat [Azure-services](../azure-sql/database/firewall-configure.md) toegang hebben tot de server.
> Als u gegevens kopieert met behulp van een zelf-hostende Integration Runtime, configureert u de firewall om het juiste IP-bereik toe te staan. Dit bereik omvat het IP-adres van de computer dat wordt gebruikt om verbinding te maken met Azure SQL Database.

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bieden details over eigenschappen die worden gebruikt voor het definiëren van Azure Data Factory entiteiten die specifiek zijn voor Azure SQL Database connector.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

Deze eigenschappen worden ondersteund voor een Azure SQL Database gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De **eigenschap type** moet worden ingesteld op **AzureSqlDatabase.** | Ja |
| connectionString | Geef de informatie op die nodig is om verbinding te maken Azure SQL Database de **connectionString-eigenschap.** <br/>U kunt ook een wachtwoord of service-principalsleutel in uw Azure Key Vault. Als het SQL-verificatie is, haalt u de `password` configuratie uit de connection string. Zie het JSON-voorbeeld na de tabel en Referenties opslaan [in Azure Key Vault voor meer Azure Key Vault.](store-credentials-in-key-vault.md) | Yes |
| servicePrincipalId | Geef de client-id van de toepassing op. | Ja, wanneer u Azure AD-verificatie gebruikt met een service-principal |
| servicePrincipalKey | Geef de sleutel van de toepassing op. Markeer dit veld als **SecureString** om het veilig op te slaan in Azure Data Factory of te verwijzen naar een geheim dat is opgeslagen [in Azure Key Vault](store-credentials-in-key-vault.md). | Ja, wanneer u Azure AD-verificatie gebruikt met een service-principal |
| tenant | Geef de tenantgegevens op, zoals de domeinnaam of tenant-id, waaronder uw toepassing zich bevindt. Haal deze op door de muisaanwijzer in de rechterbovenhoek van de Azure Portal. | Ja, wanneer u Azure AD-verificatie gebruikt met een service-principal |
| azureCloudType | Geef voor verificatie van de service-principal het type Azure-cloudomgeving op waarbij uw Azure AD-toepassing is geregistreerd. <br/> Toegestane waarden **zijn AzurePublic,** **AzureChina,** **AzureUsGovernment** en **AzureGermany.** Standaard wordt de data factory cloudomgeving van de data factory gebruikt. | No |
| connectVia | Deze [integratieruntime wordt](concepts-integration-runtime.md) gebruikt om verbinding te maken met het gegevensopslag. U kunt de Azure Integration Runtime of een zelf-hostende Integration Runtime gebruiken als uw gegevensopslag zich in een particulier netwerk bevindt. Als dit niet wordt opgegeven, wordt de standaard Azure Integration Runtime gebruikt. | No |

Raadpleeg voor verschillende verificatietypen respectievelijk de volgende secties over vereisten en JSON-voorbeelden:

- [SQL-verificatie](#sql-authentication)
- [Verificatie van Azure AD-toepassings token: Service-principal](#service-principal-authentication)
- [Verificatie van Azure AD-toepassings token: Beheerde identiteiten voor Azure-resources](#managed-identity)

>[!TIP]
>Als u een fout krijgt met de foutcode UserErrorFailedToConnectToSqlServer en een bericht als 'De sessielimiet voor de database is XXX en is bereikt', voegt u toe aan uw connection string en probeert u het `Pooling=false` opnieuw. `Pooling=false` wordt ook aanbevolen voor het instellen van een **gekoppelde service van het type SHIR (Integration Runtime** zelf-hostend). Groeperen en andere verbindingsparameters kunnen worden toegevoegd als nieuwe parameternamen en -waarden **in** de sectie Aanvullende verbindingseigenschappen van het formulier voor het maken van een gekoppelde service.

### <a name="sql-authentication"></a>SQL-verificatie

**Voorbeeld: SQL-verificatie gebruiken**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Voorbeeld: wachtwoord in Azure Key Vault**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
            "password": {
                "type": "AzureKeyVaultSecret",
                "store": {
                    "referenceName": "<Azure Key Vault linked service name>",
                    "type": "LinkedServiceReference"
                },
                "secretName": "<secretName>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a>Verificatie van service-principal

Volg deze stappen voor het gebruik van een azure AD-toepassings-tokenverificatie op basis van een service-principal:

1. [Maak een Azure Active Directory van](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) de Azure Portal. Noteer de naam van de toepassing en de volgende waarden die de gekoppelde service definiëren:

    - Toepassings-id
    - Toepassingssleutel
    - Tenant-id

2. [Een Azure Active Directory-beheerder](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database) inrichten voor uw server op Azure Portal als u dit nog niet hebt gedaan. De Azure AD-beheerder moet een Azure AD-gebruiker of Azure AD-groep zijn, maar dit kan geen service-principal zijn. Deze stap wordt uitgevoerd zodat u in de volgende stap een Azure AD-identiteit kunt gebruiken om een ingesloten databasegebruiker voor de service-principal te maken.

3. [Maak ingesloten databasegebruikers voor](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities) de service-principal. Maak verbinding met de database van of waaruit u gegevens wilt kopiëren met behulp van hulpprogramma's zoals SQL Server Management Studio, met een Azure AD-identiteit die ten minste de machtiging ALTER ANY USER heeft. Voer de volgende T-SQL uit:
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. Verleen de service-principal machtigingen die u normaal gesproken nodig hebt voor SQL-gebruikers of anderen. Voer de volgende code uit. Zie dit document voor [meer opties.](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your application name];
    ```

5. Configureer Azure SQL Database gekoppelde service in Azure Data Factory.

#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Voorbeeld van gekoppelde service die gebruikmaakt van verificatie van service-principals

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;Connection Timeout=30",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a> Beheerde identiteiten voor verificatie van Azure-resources

Een data factory kan worden gekoppeld aan een beheerde identiteit voor [Azure-resources](data-factory-service-identity.md) die de specifieke data factory. U kunt deze beheerde identiteit gebruiken voor Azure SQL Database verificatie. De aangewezen factory kan gegevens van of naar uw database openen en kopiëren met behulp van deze identiteit.

Volg deze stappen om verificatie van beheerde identiteiten te gebruiken.

1. [Inrichten Azure Active Directory-beheerder](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database) voor uw server op de Azure Portal als u dit nog niet hebt gedaan. De Azure AD-beheerder kan een Azure AD-gebruiker of een Azure AD-groep zijn. Als u de groep met beheerde identiteit een beheerdersrol verleent, slaat u stap 3 en 4 over. De beheerder heeft volledige toegang tot de database.

2. [Maak ingesloten databasegebruikers](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities) voor de Azure Data Factory beheerde identiteit. Maak verbinding met de database van of waaruit u gegevens wilt kopiëren met behulp van hulpprogramma's zoals SQL Server Management Studio, met een Azure AD-identiteit met ten minste ALTER ANY USER-machtigingen. Voer de volgende T-SQL uit:
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. Verleen de Data Factory beheerde identiteit machtigingen die u normaal gesproken voor SQL-gebruikers en anderen nodig hebt. Voer de volgende code uit. Zie dit [document voor meer opties.](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your Data Factory name];
    ```

4. Configureer Azure SQL Database gekoppelde service in Azure Data Factory.

**Voorbeeld**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie Gegevenssets voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van [gegevenssets.](./concepts-datasets-linked-services.md)

De volgende eigenschappen worden ondersteund voor Azure SQL Database gegevensset:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De **eigenschap type** van de gegevensset moet worden ingesteld op **AzureSqlTable.** | Yes |
| schema | Naam van het schema. |Nee voor bron, Ja voor sink  |
| tabel | Naam van de tabel/weergave. |Nee voor bron, Ja voor sink  |
| tableName | Naam van de tabel/weergave met schema. Deze eigenschap wordt ondersteund voor achterwaartse compatibiliteit. Gebruik en voor een nieuwe `schema` `table` workload. | Nee voor bron, Ja voor sink |

### <a name="dataset-properties-example"></a>Voorbeeld van eigenschappen van gegevensset

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie Pijplijnen voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren [van activiteiten.](concepts-pipelines-activities.md) Deze sectie bevat een lijst met eigenschappen die worden ondersteund door Azure SQL Database bron en sink.

### <a name="azure-sql-database-as-the-source"></a>Azure SQL Database als de bron

>[!TIP]
>Als u gegevens uit een Azure SQL Database wilt laden met behulp van gegevenspartities, kunt u meer leren van [Parallel kopiëren van SQL database](#parallel-copy-from-sql-database).

Als u gegevens wilt kopiëren Azure SQL Database, worden de volgende eigenschappen ondersteund in de sectie bron van **kopieeractiviteit:**

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De **eigenschap type** van de bron van de kopieeractiviteit moet worden ingesteld op **AzureSqlSource.** Het type SqlSource wordt nog steeds ondersteund voor achterwaartse compatibiliteit. | Yes |
| sqlReaderQuery | Deze eigenschap maakt gebruik van de aangepaste SQL-query om gegevens te lezen. Een voorbeeld is `select * from MyTable`. | No |
| sqlReaderStoredProcedureName | De naam van de opgeslagen procedure die gegevens uit de brontabel leest. De laatste SQL-instructie moet een SELECT-instructie zijn in de opgeslagen procedure. | No |
| storedProcedureParameters | Parameters voor de opgeslagen procedure.<br/>Toegestane waarden zijn naam- of waardeparen. De namen en het casing van parameters moeten overeenkomen met de namen en het casing-gebruik van de opgeslagen procedureparameters. | No |
| isolationLevel | Hiermee geeft u het transactievergrendelingsgedrag voor de SQL-bron op. De toegestane waarden zijn: **ReadCommitted**, **ReadUncommitted**, **RepeatableRead,** **Serializable**, **Snapshot**. Als dit niet wordt opgegeven, wordt het standaardisolatieniveau van de database gebruikt. Raadpleeg dit [document voor](/dotnet/api/system.data.isolationlevel) meer informatie. | No |
| partitionOptions | Hiermee geeft u de opties voor gegevenspartities op die worden gebruikt voor het laden van gegevens Azure SQL Database. <br>Toegestane waarden **zijn: Geen** (standaard), **PhysicalPartitionsOfTable** en **DynamicRange.**<br>Wanneer een partitieoptie is ingeschakeld (dat wil zeggen, niet ), wordt de mate van parallelle activiteit voor het gelijktijdig laden van gegevens uit een Azure SQL Database bepaald door de instelling van de `None` [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) kopieeractiviteit. | No |
| partitionSettings | Geef de groep instellingen voor gegevenspartities op. <br>Pas toe wanneer de partitieoptie niet `None` is. | No |
| ***Onder `partitionSettings` :*** | | |
| partitionColumnName | Geef de naam op van de bronkolom in geheel getal of **datum/datum-tijdtype** ( , , , , , , , of ) die wordt gebruikt door bereikpartities voor `int` `smallint` parallelle `bigint` `date` `smalldatetime` `datetime` `datetime2` `datetimeoffset` kopie. Als dit niet wordt opgegeven, wordt de index of de primaire sleutel van de tabel automatisch gedetecteerd en gebruikt als de partitiekolom.<br>Pas toe wanneer de partitieoptie `DynamicRange` is. Als u een query gebruikt om de brongegevens op te halen, sluit u  `?AdfDynamicRangePartitionCondition ` de WHERE-component aan. Zie de sectie Parallel kopiëren [uit](#parallel-copy-from-sql-database) SQL database voorbeeld. | No |
| partitionUpperBound | De maximumwaarde van de partitiekolom voor het splitsen van het partitiebereik. Deze waarde wordt gebruikt om de partitie te bepalen, niet om de rijen in tabel te filteren. Alle rijen in de tabel of het queryresultaat worden gepartitiefd en gekopieerd. Als dit niet is opgegeven, detecteert de kopieeractiviteit automatisch de waarde.  <br>Pas toe wanneer de partitieoptie `DynamicRange` is. Zie de sectie Parallel kopiëren [uit](#parallel-copy-from-sql-database) SQL database voorbeeld. | No |
| partitionLowerBound | De minimumwaarde van de partitiekolom voor het splitsen van het partitiebereik. Deze waarde wordt gebruikt om de partitie te bepalen, niet om de rijen in tabel te filteren. Alle rijen in de tabel of het queryresultaat worden gepartitief en gekopieerd. Als dit niet wordt opgegeven, wordt de waarde automatisch gedetecteerd door de kopieeractiviteit.<br>Pas toe wanneer de partitieoptie `DynamicRange` is. Zie de sectie Parallel [kopiëren](#parallel-copy-from-sql-database) uit SQL database voorbeeld. | No |

**Houd rekening met de volgende punten:**

- Als **sqlReaderQuery** is opgegeven voor **AzureSqlSource,** voert de kopieeractiviteit deze query uit op de Azure SQL Database bron om de gegevens op te halen. U kunt ook een opgeslagen procedure opgeven door **sqlReaderStoredProcedureName** en **opgeslagenProcedureParameters** op te geven als de opgeslagen procedure parameters gebruikt.
- Wanneer u opgeslagen procedure in de bron gebruikt om gegevens op te halen, let op: als uw opgeslagen procedure is ontworpen als het retourneren van een ander schema wanneer er een andere parameterwaarde wordt doorgegeven, kan er een fout of onverwacht resultaat worden weergegeven bij het importeren van een schema uit de gebruikersinterface of bij het kopiëren van gegevens naar SQL database met het automatisch maken van een tabel.

#### <a name="sql-query-example"></a>Voorbeeld van SQL-query

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AzureSqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="stored-procedure-example"></a>Voorbeeld van een opgeslagen procedure

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AzureSqlSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="stored-procedure-definition"></a>Definitie van opgeslagen procedure

```sql
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

### <a name="azure-sql-database-as-the-sink"></a>Azure SQL Database als de sink

> [!TIP]
> Meer informatie over het ondersteunde schrijfgedrag, configuraties en best practices van [Best practice voor het laden](#best-practice-for-loading-data-into-azure-sql-database)van gegevens in Azure SQL Database .

Als u gegevens wilt kopiëren naar Azure SQL Database, worden de volgende eigenschappen ondersteund in de sectie sink **voor** kopieeractiviteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De **eigenschap type** van de sink voor kopieeractiviteit moet worden ingesteld op **AzureSqlSink.** Het type SqlSink wordt nog steeds ondersteund voor achterwaartse compatibiliteit. | Yes |
| preCopyScript | Geef een SQL-query op voor de kopieeractiviteit die moet worden uitgevoerd voordat u gegevens naar Azure SQL Database. Deze wordt slechts één keer per kopieerrun aangeroepen. Gebruik deze eigenschap om de vooraf geladen gegevens op te schonen. | No |
| tableOption | Hiermee geeft u op [of de sinktabel automatisch moet worden gemaakt](copy-activity-overview.md#auto-create-sink-tables) als deze niet bestaat op basis van het bronschema. <br>Het automatisch maken van een tabel wordt niet ondersteund wanneer sink de opgeslagen procedure specificeert. <br>Toegestane waarden zijn: `none` (standaard), `autoCreate` . | No |
| sqlWriterStoredProcedureName | De naam van de opgeslagen procedure die definieert hoe brongegevens moeten worden toegepast op een doeltabel. <br/>Deze opgeslagen procedure wordt *aangeroepen per batch*. Gebruik de eigenschap voor bewerkingen die slechts één keer worden uitgevoerd en niets te maken hebben met brongegevens, bijvoorbeeld verwijderen of `preCopyScript` afkapen.<br>Zie voorbeeld van [Een opgeslagen procedure aanroepen vanuit een SQL-sink.](#invoke-a-stored-procedure-from-a-sql-sink) | No |
| storedProcedureTableTypeParameterName |De parameternaam van het tabeltype dat is opgegeven in de opgeslagen procedure.  |No |
| sqlWriterTableType |De tabeltypenaam die moet worden gebruikt in de opgeslagen procedure. De kopieeractiviteit maakt de gegevens die worden verplaatst beschikbaar in een tijdelijke tabel met dit tabeltype. Opgeslagen procedurecode kan vervolgens de gegevens samenvoegen die worden gekopieerd met bestaande gegevens. |No |
| storedProcedureParameters |Parameters voor de opgeslagen procedure.<br/>Toegestane waarden zijn naam- en waardeparen. Namen en casing van parameters moeten overeenkomen met de namen en het casing van de opgeslagen procedureparameters. | No |
| writeBatchSize | Het aantal rijen dat in de SQL-tabel *moet worden invoegt per batch*.<br/> De toegestane waarde is **een geheel getal** (aantal rijen). Standaard bepaalt Azure Data Factory dynamisch de juiste batchgrootte op basis van de rijgrootte. | No |
| writeBatchTimeout | De wachttijd voor de batch-invoegbewerking voordat er een time-out is.<br/> De toegestane waarde is **periode**. Een voorbeeld is '00:30:00' (30 minuten). | No |
| disableMetricsCollection | Data Factory verzamelt metrische gegevens, zoals Azure SQL Database's voor optimalisatie van kopieerprestaties en aanbevelingen, waardoor extra hoofd-DB-toegang wordt toegevoegd. Als u zich zorgen maakt over dit gedrag, geeft u `true` op om dit uit te schakelen. | Nee (de standaardwaarde is `false` ) |
| maxConcurrentConnections |De bovengrens van gelijktijdige verbindingen die tijdens de activiteitsrun tot stand zijn gebracht met het gegevensopslag. Geef alleen een waarde op als u gelijktijdige verbindingen wilt beperken.| No |

**Voorbeeld 1: Gegevens appen**

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSqlSink",
                "tableOption": "autoCreate",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**Voorbeeld 2: Een opgeslagen procedure aanroepen tijdens het kopiëren**

Meer informatie kunt u vinden in [Een opgeslagen procedure aanroepen vanuit een SQL-sink.](#invoke-a-stored-procedure-from-a-sql-sink)

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "storedProcedureTableTypeParameterName": "MyTable",
                "sqlWriterTableType": "MyTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="parallel-copy-from-sql-database"></a>Parallel kopiëren van SQL database

De Azure SQL Database-connector in de kopieeractiviteit biedt ingebouwde gegevenspartities om gegevens parallel te kopiëren. U vindt opties voor het partitioneren van gegevens op **het tabblad Bron** van de kopieeractiviteit.

![Schermopname van partitieopties](./media/connector-sql-server/connector-sql-partition-options.png)

Wanneer u gepartitief kopiëren inschakelen, kopieeractiviteit parallelle query's uitgevoerd op uw Azure SQL Database om gegevens te laden door partities. De parallelle graad wordt bepaald door de [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) instelling van de kopieeractiviteit. Als u bijvoorbeeld in stelt op vier, genereert Data Factory gelijktijdig vier query's op basis van de opgegeven partitieoptie en instellingen en haalt elke query een deel van de gegevens op uit uw `parallelCopies` Azure SQL Database.

U wordt aangeraden om parallel kopiëren met gegevenspartities in te stellen, met name wanneer u grote hoeveelheden gegevens uit uw Azure SQL Database. Hier volgen voorgestelde configuraties voor verschillende scenario's. Wanneer u gegevens kopieert naar een gegevensopslag op basis van bestanden, is het raadzaam om als meerdere bestanden naar een map te schrijven (alleen mapnaam opgeven). In dat geval zijn de prestaties beter dan het schrijven naar één bestand.

| Scenario                                                     | Aanbevolen instellingen                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Volledige belasting van grote tabel, met fysieke partities.        | **Partitieoptie:** fysieke partities van tabel. <br><br/>Tijdens de uitvoering Data Factory automatisch de fysieke partities gedetecteerd en worden gegevens gekopieerd per partitie. <br><br/>Als u wilt controleren of uw tabel al dan niet een fysieke partitie heeft, kunt u deze [query verwijzen.](#sample-query-to-check-physical-partition) |
| Volledige belasting van grote tabel, zonder fysieke partities, met een geheel getal of datum/tijd-kolom voor gegevenspartities. | **Partitieopties:** partitie dynamisch bereik.<br>**Partitiekolom** (optioneel): geef de kolom op die wordt gebruikt om gegevens te partitioneren. Als dit niet wordt opgegeven, wordt de kolom met de index of primaire sleutel gebruikt.<br/>**Bovengrens voor partitie** **en ondergrens voor partitie** (optioneel): geef op of u de stap van de partitie wilt bepalen. Dit is niet voor het filteren van de rijen in tabel. Alle rijen in de tabel worden gepartitiefd en gekopieerd. Als dit niet is opgegeven, worden de waarden automatisch gedetecteerd door de kopieeractiviteit.<br><br>Als de partitiekolom 'ID' bijvoorbeeld waarden heeft van 1 tot 100 en u de ondergrens in stelt op 20 en de bovengrens op 80, met parallelle kopie als 4, haalt Data Factory gegevens op met 4 partities - id's in bereik <= 20, [21, 50], [51, 80] en >=81. |
| Laad een grote hoeveelheid gegevens met behulp van een aangepaste query, zonder fysieke partities, en met een geheel getal of een datum/datum/tijd-kolom voor het partitioneren van gegevens. | **Partitieopties:** partitie dynamisch bereik.<br>**Query:** `SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>` .<br>**Partitiekolom:** geef de kolom op die wordt gebruikt om gegevens te partitioneren.<br>**Bovengrens voor partitie** **en ondergrens voor partitie** (optioneel): geef op of u de stap van de partitie wilt bepalen. Dit is niet voor het filteren van de rijen in tabel. Alle rijen in het queryresultaat worden gepartitiefd en gekopieerd. Als dit niet is opgegeven, detecteert de kopieeractiviteit automatisch de waarde.<br><br>Tijdens de uitvoering Data Factory vervangen door de werkelijke kolomnaam en waardebereiken voor elke partitie en worden `?AdfRangePartitionColumnName` ze naar Azure SQL Database. <br>Als de partitiekolom 'ID' bijvoorbeeld waarden heeft van 1 tot 100 en u de ondergrens in stelt op 20 en de bovengrens op 80, met parallelle kopie als 4, haalt Data Factory gegevens op met 4 partities- id's in bereik <= 20, [21, 50], [51, 80] en >=81. <br><br>Hier zijn meer voorbeeldquery's voor verschillende scenario's:<br> 1. Een query uitvoeren op de hele tabel: <br>`SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition`<br> 2. Query's uitvoeren vanuit een tabel met kolomselectie en aanvullende where-componentfilters: <br>`SELECT <column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 3. Query's uitvoeren met subquery's: <br>`SELECT <column_list> FROM (<your_sub_query>) AS T WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 4. Query uitvoeren met partitie in subquery: <br>`SELECT <column_list> FROM (SELECT <your_sub_query_column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition) AS T`
|

Best practices voor het laden van gegevens met de partitieoptie:

1. Kies een kolom met kolommen als partitiekolom (zoals primaire sleutel of unieke sleutel) om scheefheid van gegevens te voorkomen. 
2. Als de tabel een ingebouwde partitie heeft, gebruikt u partitieoptie Fysieke partities van tabel voor betere prestaties.    
3. Als u Azure Integration Runtime gebruikt om gegevens te kopiëren, kunt u grotere '[Data Integration Units (DIU)](copy-activity-performance-features.md#data-integration-units)(>4) instellen om meer rekenresources te gebruiken. Controleer hier de toepasselijke scenario's.
4. "[](copy-activity-performance-features.md#parallel-copy)De mate van parallellisme bij kopiëren" bepalen de partitienummers, het instellen van dit aantal is te hoog, wat de prestaties kan beïnvloeden. U wordt aangeraden dit aantal in te stellen als (DIU of het aantal zelf-hostende IR-knooppunten) * (2 tot 4).

**Voorbeeld: volledige belasting van grote tabel met fysieke partities**

```json
"source": {
    "type": "AzureSqlSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Voorbeeld: query met dynamische bereikpartitie**

```json
"source": {
    "type": "AzureSqlSource",
    "query": "SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column (optional) to decide the partition stride, not as data filter>",
        "partitionLowerBound": "<lower_value_of_partition_column (optional) to decide the partition stride, not as data filter>"
    }
}
```

### <a name="sample-query-to-check-physical-partition"></a>Voorbeeldquery om fysieke partitie te controleren

```sql
SELECT DISTINCT s.name AS SchemaName, t.name AS TableName, pf.name AS PartitionFunctionName, c.name AS ColumnName, iif(pf.name is null, 'no', 'yes') AS HasPartition
FROM sys.tables AS t
LEFT JOIN sys.objects AS o ON t.object_id = o.object_id
LEFT JOIN sys.schemas AS s ON o.schema_id = s.schema_id
LEFT JOIN sys.indexes AS i ON t.object_id = i.object_id 
LEFT JOIN sys.index_columns AS ic ON ic.partition_ordinal > 0 AND ic.index_id = i.index_id AND ic.object_id = t.object_id 
LEFT JOIN sys.columns AS c ON c.object_id = ic.object_id AND c.column_id = ic.column_id 
LEFT JOIN sys.partition_schemes ps ON i.data_space_id = ps.data_space_id 
LEFT JOIN sys.partition_functions pf ON pf.function_id = ps.function_id 
WHERE s.name='[your schema]' AND t.name = '[your table name]'
```

Als de tabel een fysieke partitie heeft, ziet u 'HasPartition' als 'ja' zoals hieronder.

![Sql-queryresultaat](./media/connector-azure-sql-database/sql-query-result.png)

## <a name="best-practice-for-loading-data-into-azure-sql-database"></a>Best practice voor het laden van gegevens in Azure SQL Database

Wanneer u gegevens naar een Azure SQL Database, hebt u mogelijk ander schrijfgedrag nodig:

- [Append:](#append-data)Mijn brongegevens hebben alleen nieuwe records.
- [Upsert:](#upsert-data)Mijn brongegevens bevatten zowel invoegingen als updates.
- [Overschrijven:](#overwrite-the-entire-table)ik wil elke keer een volledige dimensietabel opnieuw laden.
- [Schrijven met aangepaste logica:](#write-data-with-custom-logic)ik heb extra verwerking nodig vóór de laatste invoeging in de doeltabel.

Raadpleeg de desbetreffende secties over het configureren in Azure Data Factory en best practices.

### <a name="append-data"></a>Gegevens appen

Het gebruik van gegevens is het standaardgedrag van Azure SQL Database sink-connector. Azure Data Factory maakt een bulkinvoeging om efficiënt naar uw tabel te schrijven. U kunt de bron en sink dienovereenkomstig configureren in de kopieeractiviteit.

### <a name="upsert-data"></a>Upsert-gegevens

**Optie 1:** Wanneer u een grote hoeveelheid gegevens hebt om te kopiëren, kunt u alle records bulksgewijs laden in [](/sql/t-sql/statements/merge-transact-sql) een faseringstabel met behulp van de kopieeractiviteit en vervolgens een opgeslagen procedureactiviteit uitvoeren om in één keer een MERGE- of INSERT/UPDATE-instructie toe te passen. 

Copy-activiteit biedt momenteel geen systeemeigen ondersteuning voor het laden van gegevens in een tijdelijke tabel van een database. Er is een geavanceerde manier om deze in te stellen met een combinatie van meerdere activiteiten. Raadpleeg Optimize Azure SQL Database Bulk Upsert scenarios (Scenario's voor [bulksgewijs upsert optimaliseren).](https://github.com/scoriani/azuresqlbulkupsert) Hieronder ziet u een voorbeeld van het gebruik van een permanente tabel als fasering.

Zo kunt u in Azure Data Factory een pijplijn maken met een **Copy-activiteit** met een **opgeslagen procedureactiviteit**. De eerstgenoemde kopieert gegevens uit uw bronopslag naar een Azure SQL Database faseringstabel, bijvoorbeeld **UpsertStagingTable,** als de tabelnaam in de gegevensset. Vervolgens roept de laatste een opgeslagen procedure aan om brongegevens uit de faseringstabel samen te voegen in de doeltabel en de faseringstabel op te schonen.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

Definieer in uw database een opgeslagen procedure met MERGE-logica, zoals in het volgende voorbeeld, waar naar wordt gewezen vanuit de vorige opgeslagen procedureactiviteit. Stel dat het doel de **tabel Marketing** is met drie kolommen: **ProfileID,** **State** en **Category.** Doe de upsert op basis van de **kolom ProfileID.**

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
   MERGE TargetTable AS target
   USING UpsertStagingTable AS source
   ON (target.[ProfileID] = source.[ProfileID])
   WHEN MATCHED THEN
      UPDATE SET State = source.State
    WHEN NOT matched THEN
       INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    TRUNCATE TABLE UpsertStagingTable
END
```

**Optie 2:** U kunt ervoor kiezen om [een opgeslagen procedure aan te roepen binnen de kopieeractiviteit](#invoke-a-stored-procedure-from-a-sql-sink). Met deze methode wordt elke batch (zoals bepaald door de eigenschap) in de brontabel uitgevoerd in plaats van bulksgewijs invoegen te gebruiken als de `writeBatchSize` standaardbenadering in de kopieeractiviteit.

**Optie 3:** U kunt [Toewijzingstoewijzing gebruiken Gegevensstroom](#sink-transformation) ingebouwde methoden voor invoegen/upsert/bijwerken biedt.

### <a name="overwrite-the-entire-table"></a>De hele tabel overschrijven

U kunt de eigenschap **preCopyScript configureren** in de sink voor kopieeractiviteiten. In dit geval voert Azure Data Factory eerst het script uit voor elke kopieeractiviteit die wordt uitgevoerd. Vervolgens wordt de kopie uitgevoerd om de gegevens in te voegen. Als u bijvoorbeeld de hele tabel wilt overschrijven met de meest recente gegevens, geeft u een script op om eerst alle records te verwijderen voordat u de nieuwe gegevens bulksgewijs uit de bron laadt.

### <a name="write-data-with-custom-logic"></a>Gegevens schrijven met aangepaste logica

De stappen voor het schrijven van gegevens met aangepaste logica zijn vergelijkbaar met de stappen die worden beschreven in de [sectie Upsert-gegevens.](#upsert-data) Wanneer u extra verwerking moet toepassen vóór de laatste invoeging van brongegevens in de doeltabel, kunt u laden naar een faseringstabel en vervolgens opgeslagen procedureactiviteit aanroepen, of een opgeslagen procedure aanroepen in de sink voor kopieeractiviteit om gegevens toe te passen of Toewijzingsgegevens Gegevensstroom.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Een opgeslagen procedure aanroepen vanuit een SQL-sink

Wanneer u gegevens naar Azure SQL Database kopieert, kunt u ook een door de gebruiker opgegeven opgeslagen procedure configureren en aanroepen met aanvullende parameters voor elke batch van de brontabel. De opgeslagen procedurefunctie maakt gebruik van [tabelwaardeparameters.](/dotnet/framework/data/adonet/sql/table-valued-parameters)

U kunt een opgeslagen procedure gebruiken wanneer ingebouwde kopieermechanismen niet het doel zijn. Een voorbeeld hiervan is wanneer u extra verwerking wilt toepassen vóór de laatste invoeging van brongegevens in de doeltabel. Enkele extra verwerkingsvoorbeelden zijn wanneer u kolommen wilt samenvoegen, extra waarden wilt zoeken en in meer dan één tabel wilt invoegen.

In het volgende voorbeeld ziet u hoe u een opgeslagen procedure gebruikt om een upsert uit te voeren in een tabel in Azure SQL Database. Stel dat de invoergegevens en de **sinktabel Marketing** elk drie kolommen hebben: **ProfileID,** **State** en **Category.** Doe de upsert op basis van de **kolom ProfileID** en pas deze alleen toe voor een specifieke categorie met de naam 'ProductA'.

1. Definieer in uw database het tabeltype met dezelfde naam als **sqlWriterTableType.** Het schema van het tabeltype is hetzelfde als het schema dat wordt geretourneerd door uw invoergegevens.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. Definieer in uw database de opgeslagen procedure met dezelfde naam als **sqlWriterStoredProcedureName.** Hiermee worden invoergegevens van de opgegeven bron verwerkt en samengevoegd in de uitvoertabel. De parameternaam van het tabeltype in de opgeslagen procedure is hetzelfde als **tableName die** in de gegevensset is gedefinieerd.

    ```sql
    CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
    AS
    BEGIN
    MERGE [dbo].[Marketing] AS target
    USING @Marketing AS source
    ON (target.ProfileID = source.ProfileID and target.Category = @category)
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT MATCHED THEN
        INSERT (ProfileID, State, Category)
        VALUES (source.ProfileID, source.State, source.Category);
    END
    ```

3. Definieer Azure Data Factory **sql-sinksectie** in de kopieeractiviteit als volgt:

    ```json
    "sink": {
        "type": "AzureSqlSink",
        "sqlWriterStoredProcedureName": "spOverwriteMarketing",
        "storedProcedureTableTypeParameterName": "Marketing",
        "sqlWriterTableType": "MarketingType",
        "storedProcedureParameters": {
            "category": {
                "value": "ProductA"
            }
        }
    }
    ```

## <a name="mapping-data-flow-properties"></a>Eigenschappen van toewijzingsgegevensstromen

Wanneer u gegevens transformeert in de toewijzingsgegevensstroom, kunt u vanuit de Azure SQL Database. Zie de brontransformatie en [sinktransformatie](data-flow-source.md) [in](data-flow-sink.md) toewijzingsgegevensstromen voor meer informatie.

### <a name="source-transformation"></a>Brontransformatie

Instellingen die specifiek Azure SQL Database zijn beschikbaar op **het tabblad Bronopties** van de brontransformatie.

**Invoer:** Selecteer of u uw bron naar een tabel (equivalent van ) wilt laten wijzen of ```Select * from <table-name>``` een aangepaste SQL-query moet invoeren.

**Query:** als u Query selecteert in het invoerveld, voert u een SQL-query voor uw bron in. Deze instelling overschrijven alle tabel die u hebt gekozen in de gegevensset. **Order** by-components worden hier niet ondersteund, maar u kunt een volledige SELECT FROM-instructie instellen. U kunt ook door de gebruiker gedefinieerde tabelfuncties gebruiken. **select * from udfGetData()** is een UDF in SQL die een tabel retourneert. Met deze query wordt een brontabel geproduceerd die u in uw gegevensstroom kunt gebruiken. Het gebruik van query's is ook een uitstekende manier om het aantal rijen voor testen of op zoekups te verminderen.

**Opgeslagen procedure:** kies deze optie als u een projectie en brongegevens wilt genereren op basis van een opgeslagen procedure die wordt uitgevoerd vanuit uw brondatabase. U kunt het schema, de procedurenaam en parameters typen of op Vernieuwen klikken om ADF te vragen de schema's en procedurenamen te ontdekken. Vervolgens kunt u op Importeren klikken om alle procedureparameters te importeren met behulp van het formulier ``@paraName`` .

![Opgeslagen procedure](media/data-flow/stored-procedure-2.png "Opgeslagen procedure")

- SQL-voorbeeld: ```Select * from MyTable where customerId > 1000 and customerId < 2000```
- Geparameteriseerde SQL-voorbeeld: ``"select * from {$tablename} where orderyear > {$year}"``

**Batchgrootte:** voer een batchgrootte in om grote gegevens in lees- en leesgegevens te segmenten.

**Isolatieniveau:** de standaardinstelling voor SQL-bronnen in de toewijzingsgegevensstroom is niet-opgenomen lezen. U kunt het isolatieniveau hier wijzigen in een van deze waarden:

- Vastgelegd lezen
- Niet-opgenomen lezen
- Herhaalbare leesbare tekst
- Serializable
- Geen (isolatieniveau negeren)

![Isolatieniveau](media/data-flow/isolationlevel.png "Isolatieniveau")

### <a name="sink-transformation"></a>Sinktransformatie

Instellingen die specifiek zijn Azure SQL Database zijn beschikbaar op **het tabblad Instellingen** van de sinktransformatie.

**Methode bijwerken:** Hiermee bepaalt u welke bewerkingen zijn toegestaan op uw databasebestemming. De standaardwaarde is om alleen invoegingen toe te staan. Als u rijen wilt bijwerken, upsert of verwijderen, is een alter-row-transformatie vereist om rijen voor deze acties te taggen. Voor updates, upserts en verwijderen moet een sleutelkolom of kolommen worden ingesteld om te bepalen welke rij moet worden gewijzigd.

![Sleutelkolommen](media/data-flow/keycolumn.png "Sleutelkolommen")

De kolomnaam die u hier als sleutel kiest, wordt door ADF gebruikt als onderdeel van de volgende update, upsert, delete. Daarom moet u een kolom kiezen die bestaat in de Sink-toewijzing. Als u de waarde niet naar deze sleutelkolom wilt schrijven, klikt u op 'Sleutelkolommen schrijven overslaan'.

U kunt de sleutelkolom die hier wordt gebruikt voor het bijwerken van de doeltabel Azure SQL Database parameteriseren. Als u meerdere kolommen voor een samengestelde sleutel hebt, klikt u op Aangepaste expressie en kunt u dynamische inhoud toevoegen met behulp van de ADF-gegevensstroomexpressietaal, die een matrix met tekenreeksen met kolomnamen voor een samengestelde sleutel kan bevatten.

**Tabelactie:** Hiermee bepaalt u of alle rijen uit de doeltabel moeten worden gemaakt of verwijderd vóór het schrijven.

- Geen: er wordt geen actie uitgevoerd op de tabel.
- Opnieuw maken: de tabel wordt uitgevallen en opnieuw gemaakt. Vereist als u een nieuwe tabel dynamisch maakt.
- Afkapen: alle rijen uit de doeltabel worden verwijderd.

**Batchgrootte:** hiermee bepaalt u hoeveel rijen in elke bucket worden geschreven. Grotere batchgrootten verbeteren compressie en geheugenoptimalisatie, maar lopen het risico op geheugen-uitzonderingen bij het opslaan van gegevens in de caching.

**TempDB gebruiken:** Standaard gebruikt Data Factory globale tijdelijke tabel om gegevens op te slaan als onderdeel van het laadproces. U kunt ook de optie TempDB gebruiken uitvinken en in plaats daarvan Data Factory vragen om de tijdelijke opslagtabel op te slaan in een gebruikersdatabase die zich in de database bevindt die wordt gebruikt voor deze sink.

![Temp DB gebruiken](media/data-flow/tempdb.png "Temp DB gebruiken")

**SQL-scripts** vooraf en achteraf: voer SQL-scripts met meerdere lijnen in die worden uitgevoerd vóór (voorverwerking) en nadat (naverwerking) gegevens naar uw Sink-database worden geschreven

![scripts voor sql-verwerking vooraf en achteraf](media/data-flow/prepost1.png "SQL-verwerkingsscripts")

### <a name="error-row-handling"></a>Verwerking van foutrijen

Bij het schrijven naar Azure SQL DB kunnen bepaalde rijen met gegevens mislukken vanwege beperkingen die zijn ingesteld door de bestemming. Enkele veelvoorkomende fouten zijn:

*    Tekenreeks- of binaire gegevens worden afgekapt in tabel
*    Kan de waarde NULL niet invoegen in kolom
*    De INSERT-instructie is in strijd met de CHECK-beperking

Standaard mislukt het uitvoeren van een gegevensstroom bij de eerste fout die wordt weergegeven. U kunt ervoor kiezen om **door te gaan bij een fout** waardoor uw gegevensstroom kan worden voltooid, zelfs als afzonderlijke rijen fouten bevatten. Azure Data Factory biedt verschillende opties voor het afhandelen van deze foutrijen.

**Transactie commit:** Kies of uw gegevens worden geschreven in één transactie of in batches. Eén transactie levert slechtere prestaties op, maar er zijn geen geschreven gegevens zichtbaar voor anderen totdat de transactie is voltooid.  

**Geweigerde uitvoergegevens:** Als deze optie is ingeschakeld, kunt u de foutrijen naar een CSV-bestand in Azure Blob Storage of een Azure Data Lake Storage Gen2 account van uw keuze. Hiermee worden de foutrijen met drie extra kolommen geschreven: de SQL-bewerking zoals INSERT of UPDATE, de foutcode van de gegevensstroom en het foutbericht in de rij.

**Rapporteren dat de fout is geslaagd:** Als deze functie is ingeschakeld, wordt de gegevensstroom gemarkeerd als geslaagd, zelfs als er foutrijen worden gevonden. 

![Verwerking van foutrijen](media/data-flow/sql-error-row-handling.png "Verwerking van foutrijen")


## <a name="data-type-mapping-for-azure-sql-database"></a>Toewijzing van gegevenstype voor Azure SQL Database

Wanneer gegevens worden gekopieerd van of naar Azure SQL Database, worden de volgende toewijzingen gebruikt van Azure SQL Database gegevenstypen om tijdelijke Azure Data Factory te maken. Zie Schema- en gegevenstypetoewijzingen voor meer informatie over hoe de kopieeractiviteit het bronschema en gegevenstype [toewijzingen](copy-activity-schema-and-type-mapping.md)aan de sink.

| Azure SQL Database gegevenstype | Azure Data Factory tussentijds gegevenstype |
|:--- |:--- |
| bigint |Int64 |
| binair |Byte[] |
| bit |Booleaans |
| char |Tekenreeks, Char[] |
| date |DateTime |
| Datum/tijd |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimaal |Decimaal |
| FILESTREAM-kenmerk (varbinary(max)) |Byte[] |
| Float |Dubbel |
| image |Byte[] |
| int |Int32 |
| money |Decimaal |
| nchar |Tekenreeks, Char[] |
| ntext |Tekenreeks, Char[] |
| numeriek |Decimaal |
| nvarchar |Tekenreeks, Char[] |
| werkelijk |Enkelvoudig |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimaal |
| sql_variant |Object |
| tekst |Tekenreeks, Char[] |
| tijd |TimeSpan |
| tijdstempel |Byte[] |
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |Tekenreeks, Char[] |
| xml |Tekenreeks |

>[!NOTE]
> Voor gegevenstypen die zijn toe te staan aan het tussentijdse decimale type, ondersteunt Copy-activiteit precisie tot 28. Als u gegevens hebt met een precisie die groter is dan 28, kunt u overwegen om te converteren naar een tekenreeks in SQL-query.

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoekactiviteit

Zie Opzoekactiviteit voor meer informatie [over de eigenschappen.](control-flow-lookup-activity.md)

## <a name="getmetadata-activity-properties"></a>Eigenschappen van GetMetadata-activiteit

Zie [GetMetadata-activiteit](control-flow-get-metadata-activity.md) voor meer informatie over de eigenschappen

## <a name="using-always-encrypted"></a>Met behulp van Always Encrypted

Wanneer u gegevens kopieert van/naar Azure SQL Database [met Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine), gebruikt u een algemene [ODBC-connector](connector-odbc.md) en SQL Server ODBC-stuurprogramma via zelf-hostend Integration Runtime. Deze Azure SQL Database-connector biedt nu geen Always Encrypted ondersteuning. 

Met name:

1. Stel een zelf-hostend Integration Runtime als u er nog geen hebt. Zie [het artikel Zelf-hostend Integration Runtime](create-self-hosted-integration-runtime.md) voor meer informatie.

2. Download hier het 64-bits ODBC-stuurprogramma voor SQL Server [en](/sql/connect/odbc/download-odbc-driver-for-sql-server)installeer op de Integration Runtime machine. Meer informatie over hoe dit stuurprogramma werkt vanuit [Always Encrypted gebruiken met het ODBC-stuurprogramma voor SQL Server.](/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver#using-the-azure-key-vault-provider)

3. Raadpleeg de volgende voorbeelden om een gekoppelde service met ODBC-type SQL database verbinding te maken met uw service:

    - **SQL-verificatie gebruiken:** geef de ODBC-connection string zoals hieronder en selecteer Basisverificatie **om** de gebruikersnaam en het wachtwoord in te stellen.

        ```
        Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>
        ```

    - Als u zelf-hostende Integration Runtime azure virtual machine, kunt u Managed **Identity-verificatie** gebruiken met de identiteit van de virtuele Azure-machine:

        1. Volg dezelfde vereisten [om een](#managed-identity) databasegebruiker te maken voor de beheerde identiteit en de juiste rol in uw database te verlenen.
        2. Geef in de gekoppelde service de ODBC connection string  op zoals hieronder en selecteer Anonieme verificatie als de connection string zelf `Authentication=ActiveDirectoryMsi` aangeeft.

        ```
        Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>; Authentication=ActiveDirectoryMsi;
        ```

4. Maak gegevensset en kopieeractiviteit met ODBC-type dienovereenkomstig. Meer informatie in het [artikel over de ODBC-connector.](connector-odbc.md)

## <a name="next-steps"></a>Volgende stappen

Zie Ondersteunde gegevensopslag en -indelingen voor een lijst met gegevensopslagexemplaars die worden ondersteund als bronnen en sinks door de kopieeractiviteit in [Azure Data Factory.](copy-activity-overview.md#supported-data-stores-and-formats)

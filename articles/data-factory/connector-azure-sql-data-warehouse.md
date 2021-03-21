---
title: Gegevens kopiëren en transformeren in azure Synapse Analytics
description: Meer informatie over het kopiëren van gegevens van en naar Azure Synapse Analytics en het transformeren van gegevens in azure Synapse Analytics met behulp van Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: 9c843ededd1fa863cc5eb4dc0db3a6da3478466d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104597518"
---
# <a name="copy-and-transform-data-in-azure-synapse-analytics-by-using-azure-data-factory"></a>Gegevens in azure Synapse Analytics kopiëren en transformeren met behulp van Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van Data Factory service die u gebruikt:"]
>
> - [Version1](v1/data-factory-azure-sql-data-warehouse-connector.md)
> - [Huidige versie](connector-azure-sql-data-warehouse.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens te kopiëren van en naar Azure Synapse Analytics en gegevens stroom te gebruiken om gegevens te transformeren in Azure Data Lake Storage Gen2. Lees het [artikel Inleiding](introduction.md)voor meer informatie over Azure Data Factory.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Azure Synapse Analytics-connector wordt ondersteund voor de volgende activiteiten:

- De tabel [copy-activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Gegevens stroom toewijzen](concepts-data-flow-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)
- [GetMetadata-activiteit](control-flow-get-metadata-activity.md)

Voor kopieer activiteiten ondersteunt deze Azure Synapse Analytics-connector deze functies:

- Gegevens kopiëren met behulp van SQL-verificatie en Azure Active Directory (Azure AD) verificatie van het toepassings token met een service-principal of beheerde identiteiten voor Azure-resources.
- Haal als bron gegevens op met behulp van een SQL-query of opgeslagen procedure. U kunt er ook voor kiezen om een parallelle kopie te kopiëren van een Azure Synapse Analytics-bron. Zie de sectie [parallel exemplaar van Azure Synapse Analytics](#parallel-copy-from-azure-synapse-analytics) voor meer informatie.
- Laad als Sink gegevens met behulp van [poly base](#use-polybase-to-load-data-into-azure-synapse-analytics) of [copy-instructie](#use-copy-statement) of bulksgewijze invoeging. U kunt het beste een poly base-of kopieer instructie voor betere Kopieer prestaties aanbevelen. De connector biedt ook ondersteuning voor het automatisch maken van een doel tabel als deze niet bestaat op basis van het bron schema.

> [!IMPORTANT]
> Als u gegevens kopieert met behulp van Azure Data Factory Integration Runtime, configureert u een [firewall regel op server niveau](../azure-sql/database/firewall-configure.md) zodat Azure-Services toegang hebben tot de [logische SQL-Server](../azure-sql/database/logical-servers.md).
> Als u gegevens kopieert met behulp van een zelf-hostende Integration runtime, moet u de firewall zo configureren dat het juiste IP-bereik is toegestaan. Dit bereik bevat het IP-adres van de computer dat wordt gebruikt om verbinding te maken met Azure Synapse Analytics.

## <a name="get-started"></a>Aan de slag

> [!TIP]
> Als u de beste prestaties wilt, gebruikt u de instructie poly base of COPY om gegevens te laden in azure Synapse Analytics. Het [gebruik van poly Base om gegevens te laden in azure Synapse Analytics](#use-polybase-to-load-data-into-azure-synapse-analytics) en de [instructie copy te gebruiken voor het laden van gegevens in azure Synapse Analytics](#use-copy-statement) -secties bevatten details. Zie voor een overzicht met een use-case [1 TB in azure Synapse Analytics onder 15 minuten laden met Azure Data Factory](load-azure-sql-data-warehouse.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die Data Factory entiteiten definiëren die specifiek zijn voor een Azure Synapse Analytics-connector.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor een gekoppelde Azure Synapse Analytics-service:

| Eigenschap            | Beschrijving                                                  | Vereist                                                     |
| :------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| type                | De eigenschap type moet worden ingesteld op **AzureSqlDW**.             | Ja                                                          |
| connectionString    | Geef de gegevens op die nodig zijn om verbinding te maken met het Azure Synapse Analytics-exemplaar voor de **Connections Tring** -eigenschap. <br/>Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory. U kunt ook een wacht woord/Service-Principal-sleutel in Azure Key Vault plaatsen en als de SQL-verificatie de `password` configuratie uit de Connection String halen. Zie het JSON-voor beeld onder de tabel en [Sla referenties op in azure Key Vault](store-credentials-in-key-vault.md) artikel met meer informatie. | Yes                                                          |
| servicePrincipalId  | Geef de client-ID van de toepassing op.                         | Ja, wanneer u Azure AD-verificatie gebruikt met een service-principal. |
| servicePrincipalKey | Geef de sleutel van de toepassing op. Markeer dit veld als SecureString om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | Ja, wanneer u Azure AD-verificatie gebruikt met een service-principal. |
| tenant              | Geef de Tenant gegevens op (domein naam of Tenant-ID) waaronder uw toepassing zich bevindt. U kunt deze ophalen door de muis in de rechter bovenhoek van de Azure Portal aan te wijzen. | Ja, wanneer u Azure AD-verificatie gebruikt met een service-principal. |
| azureCloudType | Voor Service-Principal-verificatie geeft u het type van de Azure-cloud omgeving op waarvoor uw Azure AD-toepassing is geregistreerd. <br/> Toegestane waarden zijn `AzurePublic` , `AzureChina` , `AzureUsGovernment` en `AzureGermany` . De cloud omgeving van de data factory wordt standaard gebruikt. | No |
| connectVia          | De [Integration runtime](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevens archief. U kunt Azure Integration Runtime of een zelf-hostende Integration runtime gebruiken (als uw gegevens archief zich in een particulier netwerk bevindt). Als u niets opgeeft, wordt de standaard Azure Integration Runtime gebruikt. | No                                                           |

Raadpleeg de volgende secties over respectievelijk de vereisten en JSON-voor beelden voor verschillende verificatie typen:

- [SQL-verificatie](#sql-authentication)
- Verificatie van Azure AD-toepassings token: [Service-Principal](#service-principal-authentication)
- Verificatie van Azure AD-toepassings tokens: [beheerde identiteiten voor Azure-resources](#managed-identity)

>[!TIP]
>Wanneer u een gekoppelde service voor Azure Synapse **serverloze** SQL-groep maakt vanuit de gebruikers interface, kiest u hand matig invoeren in plaats van te bladeren vanuit het abonnement.

>[!TIP]
>Als u de fout code ' UserErrorFailedToConnectToSqlServer ' aanmeldt en er een bericht wordt weer gegeven als ' de sessie limiet voor de data base is XXX en is bereikt. ', voegt u toe `Pooling=false` aan uw Connection String en probeert u het opnieuw.

### <a name="sql-authentication"></a>SQL-verificatie

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Voor beeld van een gekoppelde service dat gebruikmaakt van SQL-verificatie

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Wacht woord in Azure Key Vault:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
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

Voer de volgende stappen uit om verificatie van Azure AD-toepassings tokens op basis van de service-principal te gebruiken:

1. **[Maak een Azure Active Directory-toepassing](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)** vanuit de Azure Portal. Noteer de naam van de toepassing en de volgende waarden die de gekoppelde service definiëren:

   - Toepassings-id
   - Toepassings sleutel
   - Tenant-id

2. **[Richt een Azure Active Directory beheerder](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database)** in voor uw server in de Azure portal als u dat nog niet hebt gedaan. De Azure AD-beheerder kan een Azure AD-gebruiker of een Azure AD-groep zijn. Als u de groep toewijst met beheerde identiteit een beheerdersrol, slaat u stap 3 en 4 over. De beheerder heeft volledige toegang tot de data base.

3. **[Maak Inge sloten database gebruikers](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)** voor de Service-Principal. Maak verbinding met het Data Warehouse van of naar waarnaar u gegevens wilt kopiëren met behulp van hulpprogram ma's als SSMS, met een Azure AD-identiteit die ten minste een gebruikers machtiging heeft gewijzigd. Voer de volgende T-SQL uit:
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Verleen de Service-Principal machtigingen die** voor SQL-gebruikers of anderen gelden. Voer de volgende code uit of Raadpleeg [hier](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)meer opties. Als u poly Base wilt gebruiken om de gegevens te laden, leest u de [vereiste database machtiging](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your application name];
    ```

5. **Een gekoppelde Azure Synapse Analytics-service configureren** in azure Data Factory.

#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Voor beeld van een gekoppelde service die gebruikmaakt van Service-Principal-verificatie

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30",
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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a> Beheerde identiteiten voor Azure-bronnen verificatie

Een data factory kan worden gekoppeld aan een [beheerde identiteit voor Azure-resources](data-factory-service-identity.md) die de specifieke Factory vertegenwoordigt. U kunt deze beheerde identiteit voor Azure Synapse Analytics-verificatie gebruiken. De aangewezen Factory heeft toegang tot en kopiëren van gegevens van of naar uw data warehouse met behulp van deze identiteit.

Als u beheerde identiteits verificatie wilt gebruiken, voert u de volgende stappen uit:

1. **[Richt een Azure Active Directory beheerder](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database)** in voor uw server op de Azure portal als u dit nog niet hebt gedaan. De Azure AD-beheerder kan een Azure AD-gebruiker of een Azure AD-groep zijn. Als u de groep toewijst met beheerde identiteit een beheerdersrol, slaat u stap 3 en 4 over. De beheerder heeft volledige toegang tot de data base.

2. **[Inge sloten database gebruikers maken](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)** voor de Data Factory beheerde identiteit. Maak verbinding met het Data Warehouse van of naar waarnaar u gegevens wilt kopiëren met behulp van hulpprogram ma's als SSMS, met een Azure AD-identiteit die ten minste een gebruikers machtiging heeft gewijzigd. Voer de volgende T-SQL uit.
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. **Verleen de Data Factory beheerde identiteit de benodigde machtigingen** zoals u dat normaal doet voor SQL-gebruikers en anderen. Voer de volgende code uit of Raadpleeg [hier](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)meer opties. Als u poly Base wilt gebruiken om de gegevens te laden, leest u de [vereiste database machtiging](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your Data Factory name];
    ```

4. **Een gekoppelde Azure Synapse Analytics-service configureren** in azure Data Factory.

**Voorbeeld:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets.

De volgende eigenschappen worden ondersteund voor Azure Synapse Analytics-gegevensset:

| Eigenschap  | Beschrijving                                                  | Vereist                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | De eigenschap **type** van de DataSet moet worden ingesteld op **AzureSqlDWTable**. | Yes                         |
| schema | De naam van het schema. |Nee voor bron, ja voor Sink  |
| tabel | De naam van de tabel/weer gave. |Nee voor bron, ja voor Sink  |
| tableName | De naam van de tabel/weer gave met schema. Deze eigenschap wordt ondersteund voor achterwaartse compatibiliteit. Gebruik en voor nieuwe werk `schema` belasting `table` . | Nee voor bron, ja voor Sink |

### <a name="dataset-properties-example"></a>Voor beeld van eigenschappen van gegevensset

```json
{
    "name": "AzureSQLDWDataset",
    "properties":
    {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "<Azure Synapse Analytics linked service name>",
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

## <a name="copy-activity-properties"></a>Eigenschappen van Kopieer activiteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de Azure Synapse Analytics-bron en Sink.

### <a name="azure-synapse-analytics-as-the-source"></a>Azure Synapse Analytics als de bron

>[!TIP]
>Als u gegevens van Azure Synapse Analytics efficiënt wilt laden met behulp van gegevens partitioneren, kunt u meer informatie vinden op basis van [parallelle kopieën van Azure Synapse Analytics](#parallel-copy-from-azure-synapse-analytics).

Als u gegevens wilt kopiëren uit Azure Synapse Analytics, stelt u de eigenschap **type** in de bron voor het kopiëren van de activiteit in op **SqlDWSource**. De volgende eigenschappen worden ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap                     | Beschrijving                                                  | Vereist |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | De eigenschap **type** van de bron van de Kopieer activiteit moet zijn ingesteld op **SqlDWSource**. | Yes      |
| sqlReaderQuery               | Gebruik de aangepaste SQL-query om gegevens te lezen. Bijvoorbeeld: `select * from MyTable`. | No       |
| sqlReaderStoredProcedureName | De naam van de opgeslagen procedure waarmee gegevens uit de bron tabel worden gelezen. De laatste SQL-instructie moet een instructie SELECT in de opgeslagen procedure zijn. | No       |
| storedProcedureParameters    | Para meters voor de opgeslagen procedure.<br/>Toegestane waarden zijn naam-of waardeparen. Namen en hoofdletter gebruik van para meters moeten overeenkomen met de namen en de behuizing van de opgeslagen procedure parameters. | No       |
| isolationLevel | Hiermee geeft u het vergrendelings gedrag van de trans actie voor de SQL-bron op. De toegestane waarden zijn: **ReadCommitted**, **ReadUncommitted**, **RepeatableRead**, **Serializable**, **snap shot**. Als u niets opgeeft, wordt het standaard isolatie niveau van de data base gebruikt. Zie [System. data. IsolationLevel](/dotnet/api/system.data.isolationlevel)voor meer informatie. | No |
| partitionOptions | Hiermee geeft u de opties voor gegevens partities op die worden gebruikt voor het laden van gegevens uit Azure Synapse Analytics. <br>Toegestane waarden zijn: **geen** (standaard), **PhysicalPartitionsOfTable** en **DynamicRange**.<br>Wanneer een partitie optie is ingeschakeld (dat wil zeggen, niet `None` ), is de mate van parallelle uitvoering om gegevens van een Azure Synapse Analytics gelijktijdig te laden, bepaald door de [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) instelling van de Kopieer activiteit. | No |
| partitionSettings | Geef de groep van de instellingen voor het partitioneren van gegevens op. <br>Toep assen wanneer de partitie optie niet is `None` . | No |
| ***Onder `partitionSettings` :*** | | |
| partitionColumnName | Geef de naam op van de bron kolom **in een geheel getal of datum/tijd-type** (,,,,,, `int` `smallint` `bigint` `date` `smalldatetime` `datetime` `datetime2` of `datetimeoffset` ) dat wordt gebruikt voor het partitioneren van het bereik voor parallelle kopieën. Als u niets opgeeft, wordt de index of de primaire sleutel van de tabel automatisch gedetecteerd en gebruikt als de partitie kolom.<br>Toep assen wanneer de partitie optie is `DynamicRange` . Als u een query gebruikt om de bron gegevens op te halen, Hook  `?AdfDynamicRangePartitionCondition ` in de component WHERE. Zie de sectie [parallel kopiëren van SQL database](#parallel-copy-from-azure-synapse-analytics) voor een voor beeld. | No |
| partitionUpperBound | De maximum waarde van de partitie kolom voor het splitsen van het partitie bereik. Deze waarde wordt gebruikt om de partitie stride te bepalen, niet voor het filteren van de rijen in de tabel. Alle rijen in het tabel-of query resultaat worden gepartitioneerd en gekopieerd. Als deze optie niet is opgegeven, wordt de waarde automatisch gedetecteerd met de Kopieer activiteit.  <br>Toep assen wanneer de partitie optie is `DynamicRange` . Zie de sectie [parallel kopiëren van SQL database](#parallel-copy-from-azure-synapse-analytics) voor een voor beeld. | No |
| partitionLowerBound | De minimum waarde van de partitie kolom voor het splitsen van een partitie bereik. Deze waarde wordt gebruikt om de partitie stride te bepalen, niet voor het filteren van de rijen in de tabel. Alle rijen in het tabel-of query resultaat worden gepartitioneerd en gekopieerd. Als deze optie niet is opgegeven, wordt de waarde automatisch gedetecteerd met de Kopieer activiteit.<br>Toep assen wanneer de partitie optie is `DynamicRange` . Zie de sectie [parallel kopiëren van SQL database](#parallel-copy-from-azure-synapse-analytics) voor een voor beeld. | No |

**Let op het volgende punt:**

- Wanneer de opgeslagen procedure in de bron wordt gebruikt om gegevens op te halen, moet u er rekening mee houden dat uw opgeslagen procedure is ontworpen als een ander schema wanneer een andere parameter waarde wordt door gegeven, er mogelijk een fout optreedt of onverwachte resultaten krijgen bij het importeren van het schema vanuit de gebruikers interface of bij het kopiëren van gegevens naar SQL database met het maken van automatische tabellen.

#### <a name="example-using-sql-query"></a>Voor beeld: SQL-query gebruiken

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Synapse Analytics input dataset name>",
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
                "type": "SqlDWSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="example-using-stored-procedure"></a>Voor beeld: opgeslagen procedure gebruiken

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Synapse Analytics input dataset name>",
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
                "type": "SqlDWSource",
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

#### <a name="sample-stored-procedure"></a>Voor beeld van opgeslagen procedure:

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

### <a name="azure-synapse-analytics-as-sink"></a><a name="azure-sql-data-warehouse-as-sink"></a> Azure Synapse Analytics als Sink

Azure Data Factory ondersteunt drie manieren om gegevens in azure Synapse Analytics te laden.

![Kopieer opties voor de Azure Synapse Analytics-Sink](./media/connector-azure-sql-data-warehouse/sql-dw-sink-copy-options.png)

- [Poly Base gebruiken](#use-polybase-to-load-data-into-azure-synapse-analytics)
- [Instructie COPY gebruiken](#use-copy-statement)
- Bulksgewijs invoegen gebruiken

De snelste en meest schaal bare manier om gegevens te laden, is via [poly base](/sql/relational-databases/polybase/polybase-guide) of de [instructie Copy](/sql/t-sql/statements/copy-into-transact-sql).

Als u gegevens wilt kopiëren naar Azure Synapse Analytics, stelt u het sink-type in de Kopieer activiteit in op **SqlDWSink**. De volgende eigenschappen worden ondersteund in het gedeelte **sink** van de Kopieer activiteit:

| Eigenschap          | Beschrijving                                                  | Vereist                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| type              | De eigenschap **type** van de Sink voor kopieer activiteiten moet worden ingesteld op **SqlDWSink**. | Yes                                           |
| allowPolyBase     | Hiermee wordt aangegeven of poly Base moet worden gebruikt voor het laden van gegevens in azure Synapse Analytics. `allowCopyCommand` en `allowPolyBase` kan niet beide zijn ingesteld op ' True '. <br/><br/>Zie [poly Base gebruiken om gegevens te laden in de sectie Azure Synapse Analytics](#use-polybase-to-load-data-into-azure-synapse-analytics) voor beperkingen en Details.<br/><br/>Toegestane waarden zijn **True** en **False** (standaard). | Nee.<br/>Toep assen bij het gebruik van poly base.     |
| polyBaseSettings  | Een groep eigenschappen die kan worden opgegeven wanneer de `allowPolybase` eigenschap is ingesteld op **True**. | Nee.<br/>Toep assen bij het gebruik van poly base. |
| allowCopyCommand | Hiermee wordt aangegeven of de [instructie Copy](/sql/t-sql/statements/copy-into-transact-sql) moet worden gebruikt om gegevens in azure Synapse Analytics te laden. `allowCopyCommand` en `allowPolyBase` kan niet beide zijn ingesteld op ' True '. <br/><br/>Zie de [instructie Copy gebruiken om gegevens te laden in de sectie Azure Synapse Analytics](#use-copy-statement) voor beperkingen en Details.<br/><br/>Toegestane waarden zijn **True** en **False** (standaard). | Nee.<br>Toep assen bij het gebruik van COPY. |
| copyCommandSettings | Een groep eigenschappen die kan worden opgegeven wanneer `allowCopyCommand` eigenschap is ingesteld op True. | Nee.<br/>Toep assen bij het gebruik van COPY. |
| writeBatchSize    | Het aantal rijen dat in de SQL-tabel **per batch** moet worden ingevoegd.<br/><br/>De toegestane waarde is een **geheel getal** (aantal rijen). Standaard bepaalt Data Factory dynamisch de juiste Batch grootte op basis van de Rijgrootte. | Nee.<br/>Toep assen bij het gebruik van bulksgewijs invoegen.     |
| writeBatchTimeout | Wacht tijd voordat de batch INSERT-bewerking is voltooid voordat er een time-out optreedt.<br/><br/>De toegestane waarde is **time span**. Voor beeld: "00:30:00" (30 minuten). | Nee.<br/>Toep assen bij het gebruik van bulksgewijs invoegen.        |
| preCopyScript     | Geef een SQL-query voor de Kopieer activiteit op die moet worden uitgevoerd voordat er in elke uitvoering gegevens naar Azure Synapse Analytics worden geschreven. Gebruik deze eigenschap om de vooraf geladen gegevens op te schonen. | No                                            |
| tableOption | Hiermee wordt aangegeven of [de Sink-tabel automatisch](copy-activity-overview.md#auto-create-sink-tables) moet worden gemaakt als deze niet bestaat op basis van het bron schema. Toegestane waarden zijn: `none` (standaard), `autoCreate` . |No |
| disableMetricsCollection | Data Factory verzamelt metrische gegevens zoals Azure Synapse Analytics Dwu's voor het optimaliseren van de Kopieer prestaties en aanbevelingen, die extra toegang tot de hoofd database bieden. Als u zich zorgen maakt over dit gedrag, geeft u `true` op dat u deze functie wilt uitschakelen. | Nee (standaard instelling `false` ) |
| maxConcurrentConnections |De bovengrens van gelijktijdige verbindingen die tot het gegevens archief zijn gemaakt tijdens de uitvoering van de activiteit. Geef alleen een waarde op als u gelijktijdige verbindingen wilt beperken.| No |

#### <a name="azure-synapse-analytics-sink-example"></a>Voor beeld van Azure Synapse Analytics-Sink

```json
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

## <a name="parallel-copy-from-azure-synapse-analytics"></a>Parallelle kopie van Azure Synapse Analytics

De Azure Synapse Analytics-connector in de Kopieer activiteit biedt ingebouwde gegevens partities voor het parallel kopiëren van gegevens. U kunt opties voor gegevens partities vinden op het tabblad **bron** van de Kopieer activiteit.

![Scherm opname van partitie opties](./media/connector-sql-server/connector-sql-partition-options.png)

Wanneer u gepartitioneerde kopie inschakelt, voert Kopieer activiteit parallelle query's uit op uw Azure Synapse Analytics-bron om gegevens per partitie te laden. De parallelle graad wordt bepaald door de [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) instelling in de Kopieer activiteit. Als u bijvoorbeeld hebt ingesteld `parallelCopies` op vier, worden met Data Factory gelijktijdig vier query's gegenereerd en uitgevoerd op basis van uw opgegeven partitie optie en instellingen, en elke query haalt een deel van de gegevens op uit uw Azure Synapse Analytics.

U wordt aangeraden om parallelle kopieën in te scha kelen met gegevens partities met name wanneer u grote hoeveel heden gegevens uit uw Azure Synapse Analytics laadt. Hieronder vindt u de aanbevolen configuraties voor verschillende scenario's. Bij het kopiëren van gegevens naar gegevens opslag op basis van een bestand kunt u het beste naar een map schrijven als meerdere bestanden (Geef alleen de mapnaam op). in dat geval is de prestaties beter dan het schrijven naar één bestand.

| Scenario                                                     | Aanbevolen instellingen                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Volledige belasting van een grote tabel met fysieke partities.        | **Partitie optie**: fysieke partities van tabel. <br><br/>Tijdens de uitvoering worden de fysieke partities automatisch door Data Factory gedetecteerd en worden de gegevens gekopieerd op partities. <br><br/>Als u wilt controleren of de tabel fysieke partitie heeft, kunt u naar [deze query](#sample-query-to-check-physical-partition)verwijzen. |
| Volledige belasting van een grote tabel, zonder fysieke partities, met een geheel getal of datum/tijd-kolom voor het partitioneren van gegevens. | **Partitie opties**: partitie met dynamisch bereik.<br>**Partitie kolom** (optioneel): Geef de kolom op die wordt gebruikt om gegevens te partitioneren. Als u niets opgeeft, wordt de kolom index of primaire sleutel gebruikt.<br/>De **bovengrens** van de partitie en de **ondergrens** van de partitie (optioneel): Geef op of u de partitie-stride wilt bepalen. Dit is niet voor het filteren van de rijen in de tabel, alle rijen in de tabel worden gepartitioneerd en gekopieerd. Indien niet opgegeven, kopieer activiteit automatische detectie van de waarden.<br><br>Als uw partitie kolom ' ID ' bijvoorbeeld waarden bereik van 1 tot 100 heeft en u de ondergrens instelt op 20 en de bovengrens als 80, met parallelle kopie als 4, Data Factory worden gegevens opgehaald met vier partities-Id's in bereik <= 20, [21, 50], [51, 80] en >= 81, respectievelijk. |
| Laad een grote hoeveelheid gegevens met behulp van een aangepaste query, zonder fysieke partities, terwijl een kolom met een geheel getal of datum/tijd is voor het partitioneren van gegevens. | **Partitie opties**: partitie met dynamisch bereik.<br>**Query**: `SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>` .<br>**Partitie kolom**: Geef de kolom op die wordt gebruikt om gegevens te partitioneren.<br>De **bovengrens** van de partitie en de **ondergrens** van de partitie (optioneel): Geef op of u de partitie-stride wilt bepalen. Dit is niet voor het filteren van de rijen in de tabel, alle rijen in het query resultaat worden gepartitioneerd en gekopieerd. Als deze optie niet is opgegeven, wordt de waarde automatisch gedetecteerd met de Kopieer activiteit.<br><br>Tijdens de uitvoering wordt Data Factory vervangen `?AdfRangePartitionColumnName` door de werkelijke kolom naam en waardeparen voor elke partitie, en verzonden naar Azure Synapse Analytics. <br>Als uw partitie kolom ' ID ' bijvoorbeeld waarden bereik van 1 tot 100 heeft en u de ondergrens instelt op 20 en de bovengrens als 80, met parallelle kopie als 4, Data Factory worden gegevens opgehaald met vier partities-Id's in bereik <= 20, [21, 50], [51, 80] en >= 81, respectievelijk. <br><br>Hier vindt u meer voorbeeld query's voor verschillende scenario's:<br> 1. query uitvoeren op de hele tabel: <br>`SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition`<br> 2. query uitvoeren vanuit een tabel met kolom selectie en extra WHERE-component filters: <br>`SELECT <column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 3. query met subquery's: <br>`SELECT <column_list> FROM (<your_sub_query>) AS T WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 4. query met partitie in subquery: <br>`SELECT <column_list> FROM (SELECT <your_sub_query_column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition) AS T`
|

Aanbevolen procedures voor het laden van gegevens met de optie partitie:

1. Kies een onderscheidende kolom als partitie kolom (zoals primaire sleutel of unieke sleutel) om te voor komen dat gegevens scheef trekken. 
2. Als de tabel een ingebouwde partitie heeft, gebruikt u partitie optie fysieke partities van tabel om betere prestaties te krijgen.
3. Als u Azure Integration Runtime gebruikt om gegevens te kopiëren, kunt u grotere '[Data Integration units (DIU)](copy-activity-performance-features.md#data-integration-units)' (>4) instellen om meer computer bronnen te gebruiken. Controleer de toepasselijke scenario's.
4. "[Mate van kopiëren van parallellisme](copy-activity-performance-features.md#parallel-copy)" bepalen van de partitie nummers, het instellen van dit getal is te groot. Dit kan de prestaties nadelig beïnvloeden, het is raadzaam om dit aantal in te stellen als (DIU of het aantal zelf-HOSTende IR-knoop punten) * (2 tot 4).
5. Opmerking Azure Synapse Analytics kan Maxi maal 32 query's tegelijk uitvoeren. Als u het aantal ' mate van kopiëren van parallellisme ' te groot instelt, kan dit leiden tot een Synapse-beperkings probleem.

**Voor beeld: volledige belasting van een grote tabel met fysieke partities**

```json
"source": {
    "type": "SqlDWSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Voor beeld: query met een dynamische bereik partitie**

```json
"source": {
    "type": "SqlDWSource",
    "query": "SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column (optional) to decide the partition stride, not as data filter>",
        "partitionLowerBound": "<lower_value_of_partition_column (optional) to decide the partition stride, not as data filter>"
    }
}
```

### <a name="sample-query-to-check-physical-partition"></a>Voorbeeld query voor het controleren van de fysieke partitie

```sql
SELECT DISTINCT s.name AS SchemaName, t.name AS TableName, c.name AS ColumnName, CASE WHEN c.name IS NULL THEN 'no' ELSE 'yes' END AS HasPartition
FROM sys.tables AS t
LEFT JOIN sys.objects AS o ON t.object_id = o.object_id
LEFT JOIN sys.schemas AS s ON o.schema_id = s.schema_id
LEFT JOIN sys.indexes AS i ON t.object_id = i.object_id
LEFT JOIN sys.index_columns AS ic ON ic.partition_ordinal > 0 AND ic.index_id = i.index_id AND ic.object_id = t.object_id
LEFT JOIN sys.columns AS c ON c.object_id = ic.object_id AND c.column_id = ic.column_id
LEFT JOIN sys.types AS y ON c.system_type_id = y.system_type_id
WHERE s.name='[your schema]' AND t.name = '[your table name]'
```

Als de tabel fysieke partitie heeft, ziet u "HasPartition" als "ja".

## <a name="use-polybase-to-load-data-into-azure-synapse-analytics"></a>Poly Base gebruiken voor het laden van gegevens in azure Synapse Analytics

Het gebruik van [poly base](/sql/relational-databases/polybase/polybase-guide) is een efficiënte manier om een grote hoeveelheid gegevens in azure Synapse Analytics te laden met een hoge door voer. U ziet een grote toename in de door voer door poly Base te gebruiken in plaats van het standaard BULKINSERT-mechanisme. Zie voor een overzicht met een use-case [1 TB laden in azure Synapse Analytics](v1/data-factory-load-sql-data-warehouse.md).

- Als uw bron gegevens zich in **Azure Blob, Azure data Lake Storage gen1 of Azure data Lake Storage Gen2** bevindt en de **indeling poly base-compatibel is**, kunt u de Kopieer activiteit gebruiken om direct poly Base te activeren, zodat Azure Synapse Analytics de gegevens van de bron kan ophalen. Zie voor meer informatie **[direct kopiëren met poly base](#direct-copy-by-using-polybase)**.
- Als uw brongegevens archief en-indeling niet oorspronkelijk worden ondersteund door poly Base, gebruikt u in plaats daarvan de functie **[voor gefaseerde kopie door gebruik te maken van poly base](#staged-copy-by-using-polybase)** . De functie voor gefaseerd kopiëren biedt u ook een betere door voer. De gegevens worden automatisch geconverteerd naar een indeling die compatibel is met poly Base, de gegevens worden opgeslagen in Azure Blob Storage en vervolgens poly base aangeroepen om gegevens in azure Synapse Analytics te laden.

> [!TIP]
> Meer informatie over [Best practices voor het gebruik van poly base](#best-practices-for-using-polybase). Wanneer u poly base gebruikt met Azure Integration Runtime, zijn efficiënte [gegevens integratie-eenheden (DIU)](copy-activity-performance-features.md#data-integration-units) voor directe of gefaseerde opslag-naar-Synapse altijd 2. Het afstemmen van de DIU heeft geen invloed op de prestaties, omdat het laden van gegevens uit de opslag wordt aangedreven door de Synapse-engine.

De volgende poly base-instellingen worden ondersteund onder `polyBaseSettings` in de Kopieer activiteit:

| Eigenschap          | Beschrijving                                                  | Vereist                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| rejectValue       | Hiermee geeft u het aantal of percentage rijen op dat kan worden afgewezen voordat de query mislukt.<br/><br/>Meer informatie over de afwijzings opties van poly Base vindt u in de sectie argumenten van een [externe tabel maken (Transact-SQL)](/sql/t-sql/statements/create-external-table-transact-sql). <br/><br/>Toegestane waarden zijn 0 (standaard), 1, 2, enzovoort. | No                                            |
| rejectType        | Hiermee wordt aangegeven of de **rejectValue** -optie een letterlijke waarde of een percentage is.<br/><br/>Toegestane waarden zijn **Value** (standaard) en **percentage**. | No                                            |
| rejectSampleValue | Bepaalt het aantal rijen dat moet worden opgehaald voordat poly base het percentage geweigerde rijen opnieuw berekent.<br/><br/>Toegestane waarden zijn 1, 2, enzovoort. | Ja, als de **rejectType** een **percentage** is. |
| useTypeDefault    | Hiermee geeft u op hoe ontbrekende waarden in tekst bestanden met scheidings tekens moeten worden verwerkt wanneer poly base gegevens ophaalt uit het tekst bestand.<br/><br/>Meer informatie over deze eigenschap vindt u in de sectie argumenten in [externe BESTANDS indeling maken (Transact-SQL)](/sql/t-sql/statements/create-external-file-format-transact-sql).<br/><br/>Toegestane waarden zijn **True** en **False** (standaard).<br><br> | No                                            |

### <a name="direct-copy-by-using-polybase"></a>Direct kopiëren door poly Base te gebruiken

Azure Synapse Analytics poly Base ondersteunt direct Azure Blob, Azure Data Lake Storage Gen1 en Azure Data Lake Storage Gen2. Als uw bron gegevens voldoen aan de criteria die in deze sectie worden beschreven, gebruikt u poly Base om rechtstreeks vanuit de brongegevens opslag naar Azure Synapse Analytics te kopiëren. Als dat niet het geval is, gebruikt u de [gefaseerde kopie met poly base](#staged-copy-by-using-polybase).

> [!TIP]
> Als u gegevens efficiënt wilt kopiëren naar Azure Synapse Analytics, kunt u met Azure Data Factory meer informatie [vinden over het eenvoudiger en handig maken van inzichten op basis van gegevens met behulp van data Lake Store met Azure Synapse Analytics](/archive/blogs/azuredatalake/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse).

Als niet aan de vereisten wordt voldaan, worden de instellingen door Azure Data Factory gecontroleerd en wordt automatisch teruggeleid naar het BULKINSERT-mechanisme voor het verplaatsen van gegevens.

1. De **gekoppelde bron service** heeft de volgende typen en verificatie methoden:

    | Ondersteund type brongegevens archief                             | Ondersteund type bron verificatie                        |
    | :----------------------------------------------------------- | :---------------------------------------------------------- |
    | [Azure Blob](connector-azure-blob-storage.md)                | Verificatie van account sleutels, beheerde identiteits verificatie |
    | [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | Verificatie van service-principal                            |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | Verificatie van account sleutels, beheerde identiteits verificatie |

    >[!IMPORTANT]
    >- Wanneer u beheerde identiteits verificatie voor uw gekoppelde opslag service gebruikt, moet u de benodigde configuraties voor [Azure-Blob](connector-azure-blob-storage.md#managed-identity) en [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md#managed-identity) .
    >- Als uw Azure Storage is geconfigureerd met het VNet-service-eind punt, moet u beheerde identiteits verificatie gebruiken met ' vertrouwde micro soft-service toestaan ' die is ingeschakeld voor het opslag account, raadpleegt u de [gevolgen van het gebruik van VNet-service-eind punten met Azure Storage](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage).

2. De **indeling van de bron gegevens** is van **Parquet**, **Orc** of **tekst met scheidings tekens**, met de volgende configuraties:

   1. Mappad bevat geen filter voor joker tekens.
   2. De bestands naam is leeg of verwijst naar één bestand. Als u de naam van het Joker teken opgeeft in de Kopieer activiteit, kan deze alleen zijn `*` of `*.*` .
   3. `rowDelimiter` is **standaard**, **\n**, **\r\n** of **\r**.
   4. `nullValue` is standaard **ingesteld op '** "' ('"), en `treatEmptyAsNull` wordt als standaard waarde ingevuld of ingesteld op True.
   5. `encodingName` is standaard ingesteld op **UTF-8**.
   6. `quoteChar`, `escapeChar` en `skipLineCount` niet opgegeven. Poly base-ondersteuning koptekst rij overs Laan, die in ADF kan worden geconfigureerd `firstRowAsHeader` .
   7. `compression` kan **geen compressie**, **``GZip``** of **verkleinen**.

3. Als uw bron een map is, `recursive` moet u de Kopieer activiteit instellen op True.

4. `wildcardFolderPath` ,,,,, en `wildcardFilename` `modifiedDateTimeStart` `modifiedDateTimeEnd` `prefix` `enablePartitionDiscovery` `additionalColumns` zijn niet opgegeven.

>[!NOTE]
>Als uw bron een map is, noteert u met poly base bestanden ophalen uit de map en alle bijbehorende submappen en worden er geen gegevens opgehaald uit bestanden waarvoor de bestands naam begint met een onderstreping (_) of een punt (.), zoals [hier wordt beschreven: locatie argument](/sql/t-sql/statements/create-external-table-transact-sql#arguments-2).

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "ParquetDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "ParquetSource",
                "storeSettings":{
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            }
        }
    }
]
```

### <a name="staged-copy-by-using-polybase"></a>Gefaseerde kopie door gebruik te maken van poly base

Als uw bron gegevens niet systeem eigen compatibel zijn met poly Base, kunt u het kopiëren van gegevens inschakelen via een tijdelijke Azure-Blob of-Azure Data Lake Storage Gen2 (dit kan niet Premium Storage van Azure zijn). In dit geval worden de gegevens in Azure Data Factory automatisch geconverteerd om te voldoen aan de vereisten voor gegevens formaat van poly base. Vervolgens wordt poly base aangeroepen om gegevens te laden in azure Synapse Analytics. Ten slotte worden de tijdelijke gegevens uit de opslag opgeschoond. Zie [gefaseerde kopie](copy-activity-performance-features.md#staged-copy) voor informatie over het kopiëren van gegevens via een fase ring.

Als u deze functie wilt gebruiken, maakt u een [gekoppelde azure Blob Storage-service](connector-azure-blob-storage.md#linked-service-properties) of [Azure data Lake Storage Gen2 gekoppelde service](connector-azure-data-lake-storage.md#linked-service-properties) met **account sleutel of beheerde identiteits verificatie** die verwijst naar het Azure Storage-account als tijdelijke opslag.

>[!IMPORTANT]
>- Wanneer u beheerde identiteits verificatie gebruikt voor uw gekoppelde staging-service, kunt u de benodigde configuraties voor [Azure-Blob](connector-azure-blob-storage.md#managed-identity) en [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md#managed-identity) .
>- Als uw staging-Azure Storage is geconfigureerd met het VNet-service-eind punt, moet u beheerde identiteits verificatie gebruiken met ' vertrouwde micro soft-service toestaan ' die is ingeschakeld voor het opslag account, raadpleegt u de [invloed van het gebruik van VNet-service-eind punten met Azure Storage](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage). 

>[!IMPORTANT]
>Als uw staging-Azure Storage is geconfigureerd met een beheerd privé-eind punt en de opslag firewall is ingeschakeld, moet u beheerde identiteits verificatie gebruiken en machtigingen voor toegang tot de opslag-BLOB-gegevens verlenen aan de Synapse-SQL Server om ervoor te zorgen dat het de gefaseerde bestanden kan openen tijdens het laden van poly base.

```json
"activities":[
    {
        "name": "CopyFromSQLServerToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "SQLServerDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingStorage",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
]
```

### <a name="best-practices-for-using-polybase"></a>Aanbevolen procedures voor het gebruik van poly base

De volgende secties bevatten aanbevolen procedures naast de procedures die worden genoemd in [Aanbevolen procedures voor Azure Synapse Analytics](../synapse-analytics/sql/best-practices-dedicated-sql-pool.md).

#### <a name="required-database-permission"></a>Vereiste database machtiging

Als u poly Base wilt gebruiken, moet de gebruiker die gegevens laadt in azure Synapse Analytics beschikken over de [machtiging ' beheren '](/sql/relational-databases/security/permissions-database-engine) voor de doel database. Een manier om dat te doen, is om de gebruiker toe te voegen als lid van de rol **db_owner** . Meer informatie over hoe u dit doet in het [overzicht van Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

#### <a name="row-size-and-data-type-limits"></a>Limieten voor Rijgrootte en gegevens typen

Poly base-belastingen zijn beperkt tot rijen die kleiner zijn dan 1 MB. Het kan niet worden gebruikt om te laden naar VARCHR (MAX), NVARCHAR (MAX) of VARBINARY (MAX). Zie [capaciteits limieten voor Azure Synapse Analytics-Service](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads)voor meer informatie.

Als de bron gegevens rijen hebben die groter zijn dan 1 MB, wilt u de bron tabellen wellicht verticaal splitsen in meerdere kleinen. Zorg ervoor dat de maximale grootte van elke rij de limiet niet overschrijdt. De kleinere tabellen kunnen vervolgens worden geladen door poly Base te gebruiken en samen te voegen in azure Synapse Analytics.

Voor gegevens met een brede kolom kunt u ook gebruikmaken van niet-poly Base om de gegevens te laden met behulp van ADF, door de instelling ' poly base toestaan ' uit te scha kelen.

#### <a name="azure-synapse-analytics-resource-class"></a>Azure Synapse Analytics-resource klasse

Wijs een grotere resource klasse toe aan de gebruiker die gegevens laadt in azure Synapse Analytics via Poly Base om de best mogelijke door voer te verzorgen.

#### <a name="polybase-troubleshooting"></a>Poly base-probleem oplossing

#### <a name="loading-to-decimal-column"></a>Laden naar decimale kolom

Als uw bron gegevens in tekst indeling of andere niet-poly base-compatibele archieven (met gefaseerde kopie en poly base) staan en een lege waarde bevat die moet worden geladen in de decimale kolom van Azure Synapse Analytics, wordt de volgende fout weer gegeven:

```output
ErrorCode=FailedDbOperation, ......HadoopSqlException: Error converting data type VARCHAR to DECIMAL.....Detailed Message=Empty string can't be converted to DECIMAL.....
```

De oplossing bestaat uit het opheffen van de selectie van de optie **type standaard gebruiken**(als onwaar) in Sink voor kopieer activiteit-> poly base-instellingen. "[USE_TYPE_DEFAULT](/sql/t-sql/statements/create-external-file-format-transact-sql#arguments)" is een poly base systeem eigen configuratie, waarmee wordt aangegeven hoe ontbrekende waarden in tekst bestanden met scheidings tekens moeten worden verwerkt wanneer poly base gegevens ophaalt uit het tekst bestand.

#### <a name="check-the-tablename-property-in-azure-synapse-analytics"></a>Controleer de eigenschap TableName in azure Synapse Analytics

De volgende tabel bevat voor beelden van de manier waarop u de eigenschap **TableName** in de JSON-gegevensset kunt opgeven. Er worden verschillende combi Naties van schema-en tabel namen weer gegeven.

| DB-schema | Tabelnaam | **TableName** JSON-eigenschap               |
| --------- | ---------- | ----------------------------------------- |
| dbo       | MyTable    | MyTable of dbo. MyTable of [dbo]. MyTable |
| dbo1      | MyTable    | dbo1. MyTable of [dbo1]. MyTable          |
| dbo       | Mijn. table   | [Mijn. table] of [dbo]. [Mijn. tabel]            |
| dbo1      | Mijn. table   | [dbo1]. [Mijn. tabel]                         |

Als de volgende fout wordt weer gegeven, is het probleem mogelijk de waarde die u hebt opgegeven voor de eigenschap **TableName** . Zie de voor gaande tabel voor de juiste manier om waarden op te geven voor de JSON-eigenschap **TableName** .

```output
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

#### <a name="columns-with-default-values"></a>Kolommen met standaard waarden

Op dit moment accepteert de functie poly base in Data Factory alleen hetzelfde aantal kolommen als in de doel tabel. Een voor beeld is een tabel met vier kolommen waarvan een van deze is gedefinieerd met een standaard waarde. De invoer gegevens moeten nog vier kolommen hebben. Een invoer-gegevensset met drie kolommen resulteert in een fout die vergelijkbaar is met het volgende bericht:

```output
All columns of the table must be specified in the INSERT BULK statement.
```

De NULL-waarde is een speciale vorm van de standaard waarde. Als de kolom null-waarden bevat, kunnen de invoer gegevens in de BLOB voor die kolom leeg zijn. Maar dit kan niet ontbreken in de invoer gegevensset. Poly base voegt NULL toe voor ontbrekende waarden in azure Synapse Analytics.

#### <a name="external-file-access-failed"></a>Toegang tot extern bestand is mislukt

Als u het volgende fout bericht ontvangt, moet u ervoor zorgen dat u beheerde identiteits verificatie gebruikt en de machtigingen voor de opslag-BLOB-gegevens lezer hebt toegewezen aan de beheerde identiteit van de Azure Synapse-werk ruimte.

```output
Job failed due to reason: at Sink '[SinkName]': shaded.msdataflow.com.microsoft.sqlserver.jdbc.SQLServerException: External file access failed due to internal error: 'Error occurred while accessing HDFS: Java exception raised on call to HdfsBridge_IsDirExist. Java exception message:\r\nHdfsBridge::isDirExist 
```

Zie [machtigingen verlenen voor beheerde identiteit na het maken van de werk ruimte](../synapse-analytics/security/how-to-grant-workspace-managed-identity-permissions.md#grant-permissions-to-managed-identity-after-workspace-creation)voor meer informatie.

## <a name="use-copy-statement-to-load-data-into-azure-synapse-analytics"></a><a name="use-copy-statement"></a> De instructie COPY gebruiken om gegevens te laden in azure Synapse Analytics

De Azure Synapse Analytics [copy-instructie](/sql/t-sql/statements/copy-into-transact-sql) biedt ondersteuning voor het laden van gegevens uit **Azure Blob en Azure data Lake Storage Gen2**. Als uw bron gegevens voldoen aan de criteria die in deze sectie zijn beschreven, kunt u de instructie COPY gebruiken in ADF om gegevens te laden in azure Synapse Analytics. Azure Data Factory controleert de instellingen en mislukt de uitvoering van de Kopieer activiteit als niet aan de criteria wordt voldaan.

>[!NOTE]
>Er wordt momenteel alleen Data Factory ondersteund voor het kopiëren van een COPY-instructie met compatibele bronnen die hieronder worden beschreven.

>[!TIP]
>Bij gebruik van de instructie COPY met Azure Integration Runtime, zijn [DIU (effectief Data Integration units)](copy-activity-performance-features.md#data-integration-units) altijd 2. Het afstemmen van de DIU heeft geen invloed op de prestaties, omdat het laden van gegevens uit de opslag wordt aangedreven door de Synapse-engine.

De instructie COPY gebruiken ondersteunt de volgende configuratie:

1. De **gekoppelde bron-service en-indeling** zijn met de volgende typen en verificatie methoden:

    | Ondersteund type brongegevens archief                             | Ondersteunde indeling           | Ondersteund type bron verificatie                         |
    | :----------------------------------------------------------- | -------------------------- | :----------------------------------------------------------- |
    | [Azure Blob](connector-azure-blob-storage.md)                | [Tekst met scheidings tekens](format-delimited-text.md)             | Verificatie van account sleutels, verificatie van de Shared Access-hand tekening, Service-Principal-verificatie, beheerde identiteits verificatie |
    | &nbsp;                                                       | [Parquet](format-parquet.md)                    | Verificatie van account sleutels, verificatie van de Shared Access-hand tekening |
    | &nbsp;                                                       | [ORC](format-orc.md)                        | Verificatie van account sleutels, verificatie van de Shared Access-hand tekening |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | [Tekst met scheidings tekens](format-delimited-text.md)<br/>[Parquet](format-parquet.md)<br/>[ORC](format-orc.md) | Account sleutel verificatie, Service-Principal-verificatie, beheerde identiteits verificatie |

    >[!IMPORTANT]
    >- Wanneer u beheerde identiteits verificatie voor uw gekoppelde opslag service gebruikt, moet u de benodigde configuraties voor [Azure-Blob](connector-azure-blob-storage.md#managed-identity) en [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md#managed-identity) .
    >- Als uw Azure Storage is geconfigureerd met het VNet-service-eind punt, moet u beheerde identiteits verificatie gebruiken met ' vertrouwde micro soft-service toestaan ' die is ingeschakeld voor het opslag account, raadpleegt u de [gevolgen van het gebruik van VNet-service-eind punten met Azure Storage](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage).

2. Indelings instellingen zijn met het volgende:

   1. Voor **Parquet**: `compression` kan **geen compressie**, **Snappy** of zijn **``GZip``** .
   2. Voor **Orc**: `compression` kan **geen compressie**, **```zlib```** of **Snappy** zijn.
   3. Voor **tekst met scheidings tekens**:
      1. `rowDelimiter` is expliciet ingesteld als **één teken** of als '**\r\n**', de standaard waarde wordt niet ondersteund.
      2. `nullValue` is standaard ingesteld op een **lege teken reeks** ("").
      3. `encodingName` is standaard ingesteld op **UTF-8 of UTF-16**.
      4. `escapeChar` moet hetzelfde zijn als `quoteChar` , en is niet leeg.
      5. `skipLineCount` is standaard ingesteld op 0.
      6. `compression` kan **geen compressie** of zijn **``GZip``** .

3. Als uw bron een map is, `recursive` moet u in de Kopieer activiteit de waarde true instellen en `wildcardFilename` moet dit zijn `*` . 

4. `wildcardFolderPath` , `wildcardFilename` (andere dan `*` ),,, `modifiedDateTimeStart` `modifiedDateTimeEnd` `prefix` , `enablePartitionDiscovery` en `additionalColumns` zijn niet opgegeven.

De volgende instellingen voor de Kopieer instructie worden ondersteund onder `allowCopyCommand` in de Kopieer activiteit:

| Eigenschap          | Beschrijving                                                  | Vereist                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| Standaard waarde | Hiermee geeft u de standaard waarden voor elke doel kolom in azure Synapse Analytics op.  De standaard waarden in de eigenschap overschrijven de standaard beperking die is ingesteld in het Data Warehouse en de identiteits kolom kan geen standaard waarde hebben. | No |
| additionalOptions | Aanvullende opties die worden door gegeven aan een Azure Synapse Analytics-instructie COPY direct in de component with voor [kopiëren](/sql/t-sql/statements/copy-into-transact-sql). Quote de waarde waar nodig om uit te lijnen met de vereisten voor het kopiëren van de instructie. | No |

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaCOPY",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "ParquetDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "ParquetSource",
                "storeSettings":{
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowCopyCommand": true,
                "copyCommandSettings": {
                    "defaultValues": [
                        {
                            "columnName": "col_string",
                            "defaultValue": "DefaultStringValue"
                        }
                    ],
                    "additionalOptions": {
                        "MAXERRORS": "10000",
                        "DATEFORMAT": "'ymd'"
                    }
                }
            },
            "enableSkipIncompatibleRow": true
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>Eigenschappen van gegevens stroom toewijzen

Wanneer gegevens worden getransformeerd in de toewijzing van gegevens stromen, kunt u tabellen lezen en ernaar schrijven vanuit Azure Synapse Analytics. Zie voor meer informatie de [bron transformatie](data-flow-source.md) en [sink-trans](data-flow-sink.md) formatie in gegevens stromen toewijzen.

### <a name="source-transformation"></a>Bron transformatie

Instellingen die specifiek zijn voor Azure Synapse Analytics, zijn beschikbaar op het tabblad **bron opties** van de bron transformatie.

**Invoer** Selecteer of u uw bron op een tabel (equivalent van) wilt aanwijzen ```Select * from <table-name>``` of voer een aangepaste SQL-query in.

**Fase ring inschakelen** Het wordt nadrukkelijk aanbevolen om deze optie te gebruiken in productie werkbelastingen met Azure Synapse Analytics-bronnen. Wanneer u een [gegevens stroom activiteit](control-flow-execute-data-flow-activity.md) met Azure Synapse Analytics-bronnen vanuit een pijp lijn uitvoert, wordt u door ADF gevraagd om een opslag account voor de staging-locatie en wordt gebruikt voor het laden van gegevens. Het is het snelste mechanisme voor het laden van gegevens uit Azure Synapse Analytics.

- Wanneer u beheerde identiteits verificatie voor uw gekoppelde opslag service gebruikt, moet u de benodigde configuraties voor [Azure-Blob](connector-azure-blob-storage.md#managed-identity) en [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md#managed-identity) .
- Als uw Azure Storage is geconfigureerd met het VNet-service-eind punt, moet u beheerde identiteits verificatie gebruiken met ' vertrouwde micro soft-service toestaan ' die is ingeschakeld voor het opslag account, raadpleegt u de [gevolgen van het gebruik van VNet-service-eind punten met Azure Storage](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage).
- Wanneer u een Azure Synapse **serverloze** SQL-groep als bron gebruikt, wordt fase ring inschakelen niet ondersteund.

**Query**: als u in het invoer veld query selecteert, voert u een SQL-query in voor uw bron. Deze instelling overschrijft elke tabel die u in de gegevensset hebt gekozen. **Order by** -componenten worden hier niet ondersteund, maar u kunt een volledige Select from-instructie instellen. U kunt ook door de gebruiker gedefinieerde tabel functies gebruiken. **Select * from udfGetData ()** is een UDF in SQL die een tabel retourneert. Met deze query wordt een bron tabel geproduceerd die u in uw gegevens stroom kunt gebruiken. Het gebruik van query's is ook een uitstekende manier om rijen te verminderen voor het testen of voor Zoek opdrachten.

SQL-voor beeld: ```Select * from MyTable where customerId > 1000 and customerId < 2000```

**Batch grootte**: Voer een batch grootte in om grote hoeveel heden gegevens in Lees bewerkingen te segmenteren. In gegevens stromen gebruikt ADF deze instelling om in te stellen Spark in cache opslaan. Dit is een optie veld waarin de standaard waarden van Spark worden gebruikt als deze leeg blijven.

**Isolatie niveau**: de standaard waarde voor SQL-bronnen in de toewijzings gegevens stroom is niet-vastgelegd. U kunt het isolatie niveau hier wijzigen in een van deze waarden:

- Doorgevoerde lezen
- Lezen niet-doorgevoerd
- Herhaal bare Lees bewerking
- Serializable
- Geen (isolatie niveau negeren)

![Isolatie niveau](media/data-flow/isolationlevel.png)

### <a name="sink-transformation"></a>Sink-trans formatie

Instellingen die specifiek zijn voor Azure Synapse Analytics, zijn beschikbaar op het tabblad **instellingen** van de Sink-trans formatie.

**Update methode:** Hiermee wordt bepaald welke bewerkingen zijn toegestaan voor uw database bestemming. De standaard instelling is alleen invoegen toestaan. Als u rijen wilt bijwerken, upsert of verwijderen, moet u een alter-Row trans formatie voor deze acties labelen. Voor updates, upsert en verwijderen moet een sleutel kolom of-kolommen worden ingesteld om te bepalen welke rij moet worden gewijzigd.

**Tabel actie:** Hiermee wordt bepaald of alle rijen van de doel tabel opnieuw moeten worden gemaakt of verwijderd voordat er wordt geschreven.

- Geen: er wordt geen actie uitgevoerd voor de tabel.
- Opnieuw maken: de tabel wordt verwijderd en opnieuw gemaakt. Vereist als er dynamisch een nieuwe tabel wordt gemaakt.
- Afkappen: alle rijen uit de doel tabel worden verwijderd.

**Fase ring inschakelen:** Hierdoor kunnen Azure Synapse Analytics SQL-groepen worden geladen met behulp van de Kopieer opdracht en wordt aanbevolen voor de meeste Synpase-Sinks. De staging-opslag wordt geconfigureerd in de [activiteit gegevens stroom uitvoeren](control-flow-execute-data-flow-activity.md). 

- Wanneer u beheerde identiteits verificatie voor uw gekoppelde opslag service gebruikt, moet u de benodigde configuraties voor [Azure-Blob](connector-azure-blob-storage.md#managed-identity) en [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md#managed-identity) .
- Als uw Azure Storage is geconfigureerd met het VNet-service-eind punt, moet u beheerde identiteits verificatie gebruiken met ' vertrouwde micro soft-service toestaan ' die is ingeschakeld voor het opslag account, raadpleegt u de [gevolgen van het gebruik van VNet-service-eind punten met Azure Storage](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage).

**Batch grootte**: bepaalt hoeveel rijen er worden geschreven in elke Bucket. Grotere batch grootten verbeteren de compressie en Optima Lise ring van het geheugen, maar er zijn geen uitzonde ringen in het geheugen bij het opslaan van gegevens.

**SQL-scripts vooraf en post**: Voer SQL-scripts met meerdere regels in die moeten worden uitgevoerd vóór (vóór verwerking) en nadat (na verwerking) gegevens naar uw Sink-Data Base worden geschreven

![scripts voor SQL-verwerking vooraf en na](media/data-flow/prepost1.png "SQL-verwerkings scripts")

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.

## <a name="getmetadata-activity-properties"></a>Eigenschappen van GetMetadata-activiteit

Als u meer wilt weten over de eigenschappen, controleert u de [GetMetadata-activiteit](control-flow-get-metadata-activity.md)

## <a name="data-type-mapping-for-azure-synapse-analytics"></a>Toewijzing van gegevens type voor Azure Synapse Analytics

Wanneer u gegevens kopieert vanuit of naar Azure Synapse Analytics, worden de volgende toewijzingen gebruikt vanuit gegevens typen van Azure Synapse Analytics om tussenliggende gegevens typen te Azure Data Factory. Zie [schema-en gegevens type toewijzingen](copy-activity-schema-and-type-mapping.md) voor meer informatie over hoe kopieer activiteiten het bron schema en het gegevens type aan de Sink toewijzen.

>[!TIP]
>Raadpleeg de [tabel gegevens typen in het Azure Synapse Analytics](../synapse-analytics/sql/develop-tables-data-types.md) -artikel over door Azure Synapse Analytics ondersteunde gegevens typen en de tijdelijke oplossingen voor niet-ondersteunde.

| Azure Synapse Analytics-gegevens type    | Data Factory tussentijds gegevens type |
| :------------------------------------ | :----------------------------- |
| bigint                                | Int64                          |
| binair                                | Byte []                         |
| bit                                   | Booleaans                        |
| char                                  | Teken reeks, char []                 |
| date                                  | DateTime                       |
| Datum/tijd                              | DateTime                       |
| datetime2                             | DateTime                       |
| Date time offset                        | Date time offset                 |
| Decimaal                               | Decimaal                        |
| FILESTREAM-kenmerk (varbinary (max)) | Byte []                         |
| Float                                 | Dubbel                         |
| image                                 | Byte []                         |
| int                                   | Int32                          |
| money                                 | Decimaal                        |
| nchar                                 | Teken reeks, char []                 |
| numeriek                               | Decimaal                        |
| nvarchar                              | Teken reeks, char []                 |
| werkelijk                                  | Enkelvoudig                         |
| rowversion                            | Byte []                         |
| smalldatetime                         | DateTime                       |
| smallint                              | Int16                          |
| smallmoney                            | Decimaal                        |
| tijd                                  | TimeSpan                       |
| tinyint                               | Byte                           |
| uniqueidentifier                      | Guid                           |
| varbinary                             | Byte []                         |
| varchar                               | Teken reeks, char []                 |

## <a name="next-steps"></a>Volgende stappen

Zie [ondersteunde gegevens archieven en-indelingen](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit in azure Data Factory.

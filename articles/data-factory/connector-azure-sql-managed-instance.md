---
title: Gegevens kopiëren en transformeren in Azure SQL Managed instance
description: Meer informatie over het kopiëren en transformeren van gegevens in Azure SQL Managed instance met behulp van Azure Data Factory.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 12/18/2020
ms.openlocfilehash: d24eea36d45e49f19625c260f2518fb5ae0369e0
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/19/2020
ms.locfileid: "97695075"
---
# <a name="copy-and-transform-data-in-azure-sql-managed-instance-by-using-azure-data-factory"></a>Gegevens in Azure SQL Managed instance kopiëren en transformeren met behulp van Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens te kopiëren van en naar Azure SQL Managed instance en gegevens stroom te gebruiken om gegevens te transformeren in een door Azure SQL beheerd exemplaar. Lees het [artikel Inleiding](introduction.md)voor meer informatie over Azure Data Factory.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze connector voor SQL Managed instance wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Gegevens stroom toewijzen](concepts-data-flow-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)
- [GetMetadata-activiteit](control-flow-get-metadata-activity.md)

Voor kopieer activiteit ondersteunt deze Azure SQL Database-Connector de volgende functies:

- Kopiëren van gegevens met behulp van SQL-verificatie en Azure Active Directory (Azure AD) verificatie van het toepassings token met een service-principal of beheerde identiteiten voor Azure-resources.
- Als bron, waarbij gegevens worden opgehaald met behulp van een SQL-query of een opgeslagen procedure. U kunt er ook voor kiezen om een parallelle kopie te kopiëren van de SQL MI-bron. Zie de sectie [parallelle kopie van SQL mi](#parallel-copy-from-sql-mi) voor meer informatie.
- Als sink wordt er automatisch een doel tabel gemaakt als deze niet bestaat op basis van het bron schema. het toevoegen van gegevens aan een tabel of het aanroepen van een opgeslagen procedure met aangepaste logica tijdens het kopiëren.

>[!NOTE]
> De door SQL beheerde instantie [Always encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) wordt nu niet ondersteund door deze connector. U kunt een [algemene ODBC-Connector](connector-odbc.md) en een SQL Server ODBC-stuur programma gebruiken via een zelf-hostende Integration runtime. Meer informatie over het [gebruik van always encrypted](#using-always-encrypted) sectie. 

## <a name="prerequisites"></a>Vereisten

Om toegang te krijgen tot het [open bare eind punt](../azure-sql/managed-instance/public-endpoint-overview.md)van het SQL-beheerde exemplaar, kunt u een Azure Data Factory beheerde Azure Integration runtime gebruiken. Zorg ervoor dat u het open bare eind punt inschakelt en ook openbaar eindpunt verkeer op de netwerk beveiligings groep toestaat, zodat Azure Data Factory verbinding kan maken met uw data base. Zie [deze richt lijnen](../azure-sql/managed-instance/public-endpoint-configure.md)voor meer informatie.

Stel een [zelf-hostende Integration runtime](create-self-hosted-integration-runtime.md) in die toegang heeft tot de data base om toegang te krijgen tot het privé-eind punt van het SQL Managed instance. Als u de zelf-hostende Integration runtime in hetzelfde virtuele netwerk als uw beheerde exemplaar inricht, moet u ervoor zorgen dat uw Integration runtime-machine zich in een ander subnet bevindt dan uw beheerde exemplaar. Als u uw zelf-hostende Integration runtime inricht in een ander virtueel netwerk dan uw beheerde exemplaar, kunt u een peering van een virtueel netwerk of een virtueel netwerk naar een virtueel netwerk verbinding maken. Raadpleeg [Uw toepassing verbinden met SQL Managed Instance](../azure-sql/managed-instance/connect-application-instance.md) voor meer informatie.

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Azure Data Factory entiteiten die specifiek zijn voor de SQL Managed instance connector.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor de gekoppelde service van het SQL Managed instance:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op **AzureSqlMI**. | Ja |
| connectionString |Met deze eigenschap geeft u de **Connections Tring** -gegevens die nodig zijn om verbinding te maken met een SQL-beheerd exemplaar met behulp van SQL-verificatie. Zie de volgende voor beelden voor meer informatie. <br/>De standaardpoort is 1433. Als u SQL Managed instance met een openbaar eind punt gebruikt, geeft u expliciet poort 3342 op.<br> U kunt ook een wacht woord in Azure Key Vault plaatsen. Als de SQL-verificatie wordt uitgevoerd, haalt u de `password` configuratie uit het Connection String. Zie voor meer informatie het JSON-voor beeld dat volgt op de tabel en [referenties opslaan in azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| servicePrincipalId | Geef de client-ID van de toepassing op. | Ja, wanneer u Azure AD-verificatie gebruikt met een Service-Principal |
| servicePrincipalKey | Geef de sleutel van de toepassing op. Markeer dit veld als **SecureString** om het veilig op te slaan in azure Data Factory of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | Ja, wanneer u Azure AD-verificatie gebruikt met een Service-Principal |
| tenant | Geef de Tenant gegevens op, zoals de domein naam of Tenant-ID, waaronder uw toepassing zich bevindt. U kunt deze ophalen door de muis in de rechter bovenhoek van de Azure Portal aan te wijzen. | Ja, wanneer u Azure AD-verificatie gebruikt met een Service-Principal |
| azureCloudType | Voor Service-Principal-verificatie geeft u het type van de Azure-cloud omgeving op waarvoor uw Azure AD-toepassing is geregistreerd. <br/> Toegestane waarden zijn **AzurePublic**, **AzureChina**, **AzureUsGovernment** en **AzureGermany**. De cloud omgeving van de data factory wordt standaard gebruikt. | Nee |
| connectVia | Deze [Integration runtime](concepts-integration-runtime.md) wordt gebruikt om verbinding te maken met het gegevens archief. U kunt een zelf-hostende Integration runtime of een Azure Integration runtime gebruiken als uw beheerde exemplaar een openbaar eind punt heeft en Azure Data Factory toegang heeft. Als dat niet is opgegeven, wordt de standaard Azure Integration runtime gebruikt. |Ja |

Raadpleeg de volgende secties over respectievelijk de vereisten en JSON-voor beelden voor verschillende verificatie typen:

- [SQL-verificatie](#sql-authentication)
- [Verificatie van Azure AD-toepassings token: Service-Principal](#service-principal-authentication)
- [Verificatie van Azure AD-toepassings tokens: beheerde identiteiten voor Azure-resources](#managed-identity)

### <a name="sql-authentication"></a>SQL-verificatie

**Voor beeld 1: SQL-verificatie gebruiken**

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Voor beeld 2: SQL-verificatie gebruiken met een wacht woord in Azure Key Vault**

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;",
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

Voer de volgende stappen uit om een Azure AD-toepassings token verificatie op basis van een service-principal te gebruiken:

1. Volg de stappen om [een Azure Active Directory beheerder in te richten voor uw beheerde exemplaar](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).

2. [Maak een Azure Active Directory-toepassing](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) vanuit de Azure Portal. Noteer de naam van de toepassing en de volgende waarden die de gekoppelde service definiëren:

    - Toepassings-id
    - Toepassings sleutel
    - Tenant-id

3. [Aanmeldingen maken](/sql/t-sql/statements/create-login-transact-sql) voor de door de Azure Data Factory beheerde identiteit. Maak in SQL Server Management Studio (SSMS) verbinding met uw beheerde exemplaar met behulp van een SQL Server account dat een **sysadmin** is. Voer de volgende T-SQL uit in de **hoofd** database:

    ```sql
    CREATE LOGIN [your application name] FROM EXTERNAL PROVIDER
    ```

4. [Inge sloten database gebruikers maken](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities) voor de Azure Data Factory beheerde identiteit. Maak verbinding met de data base van of naar waarnaar u gegevens wilt kopiëren en voer de volgende T-SQL: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER
    ```

5. Verleen de Data Factory beheerde identiteit de benodigde machtigingen zoals u dat normaal doet voor SQL-gebruikers en anderen. Voer de volgende code uit. Zie [dit document](/sql/t-sql/statements/alter-role-transact-sql)voor meer opties.

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your application name]
    ```

6. Een gekoppelde service van een door SQL beheerde instantie configureren in Azure Data Factory.

**Voor beeld: Service-Principal-verificatie gebruiken**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;",
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

Een data factory kan worden gekoppeld aan een [beheerde identiteit voor Azure-resources](data-factory-service-identity.md) die de specifieke Data Factory vertegenwoordigt. U kunt deze beheerde identiteit voor SQL Managed instance-verificatie gebruiken. De aangewezen Factory heeft toegang tot en kopiëren van gegevens van of naar uw data base met behulp van deze identiteit.

Voer de volgende stappen uit om beheerde identiteits verificatie te gebruiken.

1. Volg de stappen om [een Azure Active Directory beheerder in te richten voor uw beheerde exemplaar](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).

2. [Aanmeldingen maken](/sql/t-sql/statements/create-login-transact-sql) voor de door de Azure Data Factory beheerde identiteit. Maak in SQL Server Management Studio (SSMS) verbinding met uw beheerde exemplaar met behulp van een SQL Server account dat een **sysadmin** is. Voer de volgende T-SQL uit in de **hoofd** database:

    ```sql
    CREATE LOGIN [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

3. [Inge sloten database gebruikers maken](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities) voor de Azure Data Factory beheerde identiteit. Maak verbinding met de data base van of naar waarnaar u gegevens wilt kopiëren en voer de volgende T-SQL: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

4. Verleen de Data Factory beheerde identiteit de benodigde machtigingen zoals u dat normaal doet voor SQL-gebruikers en anderen. Voer de volgende code uit. Zie [dit document](/sql/t-sql/statements/alter-role-transact-sql)voor meer opties.

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your Data Factory name]
    ```

5. Een gekoppelde service van een door SQL beheerde instantie configureren in Azure Data Factory.

**Voor beeld: beheerde identiteits verificatie gebruiken**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel gegevens sets voor een volledige lijst met secties en eigenschappen die kunnen worden gebruikt voor het definiëren van gegevens sets. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door de door de SQL Managed instance-gegevensset.

De volgende eigenschappen worden ondersteund voor het kopiëren van gegevens naar en van een SQL Managed instance:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de DataSet moet worden ingesteld op **AzureSqlMITable**. | Ja |
| schema | De naam van het schema. |Nee voor bron, ja voor Sink  |
| table | De naam van de tabel/weer gave. |Nee voor bron, ja voor Sink  |
| tableName | De naam van de tabel/weer gave met schema. Deze eigenschap wordt ondersteund voor achterwaartse compatibiliteit. Gebruik en voor nieuwe werk `schema` belasting `table` . | Nee voor bron, ja voor Sink |

**Voorbeeld**

```json
{
    "name": "AzureSqlMIDataset",
    "properties":
    {
        "type": "AzureSqlMITable",
        "linkedServiceName": {
            "referenceName": "<SQL Managed Instance linked service name>",
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

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die u kunt gebruiken om activiteiten te definiëren. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door de SQL Managed Instance-bron en Sink.

### <a name="sql-managed-instance-as-a-source"></a>SQL-beheerd exemplaar als een bron

>[!TIP]
>Als u gegevens van SQL MI efficiënt wilt laden door gebruik te maken van gegevenspartitionering, kunt u meer informatie vinden op basis van een [parallelle kopie van SQL mi](#parallel-copy-from-sql-mi).

Als u gegevens wilt kopiëren van een SQL-beheerd exemplaar, worden de volgende eigenschappen ondersteund in de sectie bron van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op **SqlMISource**. | Ja |
| sqlReaderQuery |Deze eigenschap maakt gebruik van de aangepaste SQL-query om gegevens te lezen. Een voorbeeld is `select * from MyTable`. |Nee |
| sqlReaderStoredProcedureName |Deze eigenschap is de naam van de opgeslagen procedure waarmee gegevens uit de bron tabel worden gelezen. De laatste SQL-instructie moet een instructie SELECT in de opgeslagen procedure zijn. |Nee |
| storedProcedureParameters |Deze para meters zijn voor de opgeslagen procedure.<br/>Toegestane waarden zijn naam-of waardeparen. De namen en de behuizing van de para meters moeten overeenkomen met de namen en de behuizing van de opgeslagen procedure parameters. |Nee |
| isolationLevel | Hiermee geeft u het vergrendelings gedrag van de trans actie voor de SQL-bron op. De toegestane waarden zijn: **ReadCommitted**, **ReadUncommitted**, **RepeatableRead**, **Serializable**, **snap shot**. Als u niets opgeeft, wordt het standaard isolatie niveau van de data base gebruikt. Raadpleeg [dit document](/dotnet/api/system.data.isolationlevel) voor meer informatie. | Nee |
| partitionOptions | Hiermee geeft u de opties voor gegevenspartitionering op die worden gebruikt voor het laden van gegevens van SQL MI. <br>Toegestane waarden zijn: **geen** (standaard), **PhysicalPartitionsOfTable** en **DynamicRange**.<br>Wanneer een partitie optie is ingeschakeld (dat wil zeggen, niet `None` ), is de mate van parallelle uitvoering om gegevens van SQL mi gelijktijdig te laden, bepaald door [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) de instelling in de Kopieer activiteit. | Nee |
| partitionSettings | Geef de groep van de instellingen voor het partitioneren van gegevens op. <br>Toep assen wanneer de partitie optie niet is `None` . | Nee |
| **_Onder `partitionSettings` :_* _ | | |
| partitionColumnName | Geef de naam van de bron kolom _ *op in geheel getal of datum/tijd type** (,,,,,, `int` `smallint` `bigint` `date` `smalldatetime` `datetime` `datetime2` of `datetimeoffset` ) dat wordt gebruikt voor het partitioneren van het bereik voor parallelle kopieën. Als u niets opgeeft, wordt de index of de primaire sleutel van de tabel automatisch gedetecteerd en gebruikt als de partitie kolom.<br>Toep assen wanneer de partitie optie is `DynamicRange` . Als u een query gebruikt om de bron gegevens op te halen, Hook  `?AdfDynamicRangePartitionCondition ` in de component WHERE. Zie de sectie [parallel kopiëren van SQL database](#parallel-copy-from-sql-mi) voor een voor beeld. | Nee |
| partitionUpperBound | De maximum waarde van de partitie kolom voor het splitsen van het partitie bereik. Deze waarde wordt gebruikt om de partitie stride te bepalen, niet voor het filteren van de rijen in de tabel. Alle rijen in het tabel-of query resultaat worden gepartitioneerd en gekopieerd. Als deze optie niet is opgegeven, wordt de waarde automatisch gedetecteerd met de Kopieer activiteit.  <br>Toep assen wanneer de partitie optie is `DynamicRange` . Zie de sectie [parallel kopiëren van SQL database](#parallel-copy-from-sql-mi) voor een voor beeld. | Nee |
| partitionLowerBound | De minimum waarde van de partitie kolom voor het splitsen van een partitie bereik. Deze waarde wordt gebruikt om de partitie stride te bepalen, niet voor het filteren van de rijen in de tabel. Alle rijen in het tabel-of query resultaat worden gepartitioneerd en gekopieerd. Als deze optie niet is opgegeven, wordt de waarde automatisch gedetecteerd met de Kopieer activiteit.<br>Toep assen wanneer de partitie optie is `DynamicRange` . Zie de sectie [parallel kopiëren van SQL database](#parallel-copy-from-sql-mi) voor een voor beeld. | Nee |

**Houd rekening met de volgende punten:**

- Als **sqlReaderQuery** is opgegeven voor **SqlMISource**, voert de Kopieer activiteit deze query uit op basis van de bron van het SQL Managed instance om de gegevens op te halen. U kunt ook een opgeslagen procedure opgeven door **sqlReaderStoredProcedureName** en **storedProcedureParameters** op te geven als voor de opgeslagen procedure para meters worden gebruikt.
- Wanneer de opgeslagen procedure in de bron wordt gebruikt om gegevens op te halen, moet u er rekening mee houden dat uw opgeslagen procedure is ontworpen als een ander schema wanneer een andere parameter waarde wordt door gegeven, er mogelijk een fout optreedt of onverwachte resultaten krijgen bij het importeren van het schema vanuit de gebruikers interface of bij het kopiëren van gegevens naar SQL database met het maken van automatische tabellen.

**Voor beeld: een SQL-query gebruiken**

```json
"activities":[
    {
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Managed Instance input dataset name>",
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
                "type": "SqlMISource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Voor beeld: een opgeslagen procedure gebruiken**

```json
"activities":[
    {
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Managed Instance input dataset name>",
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
                "type": "SqlMISource",
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

**De definitie van de opgeslagen procedure**

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

### <a name="sql-managed-instance-as-a-sink"></a>SQL Managed instance as a Sink

> [!TIP]
> Meer informatie over het ondersteunde schrijf gedrag, configuraties en aanbevolen procedures voor het [laden van gegevens in een SQL Managed instance](#best-practice-for-loading-data-into-sql-managed-instance).

Als u gegevens wilt kopiëren naar een SQL Managed instance, worden de volgende eigenschappen ondersteund in het gedeelte Sink van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de Sink voor kopieer activiteiten moet worden ingesteld op **SqlMISink**. | Ja |
| preCopyScript |Met deze eigenschap geeft u een SQL-query op voor het uitvoeren van de Kopieer activiteit voordat u gegevens naar een SQL-beheerd exemplaar schrijft. Het wordt slechts één keer per Kopieer bewerking aangeroepen. U kunt deze eigenschap gebruiken om vooraf geladen gegevens op te schonen. |Nee |
| tableOption | Hiermee wordt aangegeven of [de Sink-tabel automatisch](copy-activity-overview.md#auto-create-sink-tables) moet worden gemaakt als deze niet bestaat op basis van het bron schema. Het automatisch maken van tabellen wordt niet ondersteund wanneer Sink de opgeslagen procedure specificeert. Toegestane waarden zijn: `none` (standaard), `autoCreate` . |Nee |
| sqlWriterStoredProcedureName | De naam van de opgeslagen procedure die definieert hoe bron gegevens in een doel tabel worden toegepast. <br/>Deze opgeslagen procedure wordt *per batch aangeroepen*. Voor bewerkingen die slechts één keer worden uitgevoerd en niets te doen met bron gegevens, bijvoorbeeld verwijderen of afkappen, gebruikt u de `preCopyScript` eigenschap.<br>Bekijk het voor beeld van [het aanroepen van een opgeslagen procedure vanuit een SQL-Sink](#invoke-a-stored-procedure-from-a-sql-sink). | Nee |
| storedProcedureTableTypeParameterName |De parameter naam van het tabel type dat is opgegeven in de opgeslagen procedure.  |Nee |
| sqlWriterTableType |De naam van het tabel type dat moet worden gebruikt in de opgeslagen procedure. Met de Kopieer activiteit worden de gegevens in een tijdelijke tabel met dit tabel type beschikbaar gemaakt. Met de opgeslagen procedure code kunt u vervolgens de gegevens samen voegen die worden gekopieerd met bestaande gegevens. |Nee |
| storedProcedureParameters |Para meters voor de opgeslagen procedure.<br/>Toegestane waarden zijn naam-en waardeparen. Namen en hoofdletter gebruik van para meters moeten overeenkomen met de namen en de behuizing van de opgeslagen procedure parameters. | Nee |
| writeBatchSize |Het aantal rijen dat *per batch* in de SQL-tabel moet worden ingevoegd.<br/>Toegestane waarden zijn gehele getallen voor het aantal rijen. Standaard bepaalt Azure Data Factory dynamisch de juiste Batch grootte op basis van de Rijgrootte.  |Nee |
| writeBatchTimeout |Met deze eigenschap geeft u de wacht tijd op waarna de batch INSERT-bewerking moet worden voltooid voordat er een time-out optreedt.<br/>Toegestane waarden zijn voor de time span. Een voor beeld is ' 00:30:00 '. Dit is 30 minuten. |Nee |

**Voor beeld 1: gegevens toevoegen**

```json
"activities":[
    {
        "name": "CopyToAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Managed Instance output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlMISink",
                "tableOption": "autoCreate",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**Voor beeld 2: een opgeslagen procedure aanroepen tijdens het kopiëren**

Meer informatie over [het aanroepen van een opgeslagen procedure vanuit een SQL mi-Sink](#invoke-a-stored-procedure-from-a-sql-sink).

```json
"activities":[
    {
        "name": "CopyToAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Managed Instance output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlMISink",
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

## <a name="parallel-copy-from-sql-mi"></a>Parallelle kopie van SQL MI

De Azure SQL Managed instance-connector in de Kopieer activiteit biedt ingebouwde gegevens partities voor het parallel kopiëren van gegevens. U kunt opties voor gegevens partities vinden op het tabblad **bron** van de Kopieer activiteit.

![Scherm opname van partitie opties](./media/connector-sql-server/connector-sql-partition-options.png)

Wanneer u gepartitioneerde kopie inschakelt, voert Kopieer activiteit parallelle query's uit op uw SQL MI-bron om gegevens op partities te laden. De parallelle graad wordt bepaald door de [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) instelling in de Kopieer activiteit. Als u bijvoorbeeld hebt ingesteld `parallelCopies` op vier, worden met Data Factory gelijktijdig vier query's gegenereerd en uitgevoerd op basis van uw opgegeven partitie optie en instellingen, en elke query haalt een deel van de gegevens op uit de SQL mi.

U wordt aangeraden om parallelle kopieën in te scha kelen met gegevens partities met name wanneer u grote hoeveel heden gegevens uit de SQL-MI laadt. Hieronder vindt u de aanbevolen configuraties voor verschillende scenario's. Bij het kopiëren van gegevens naar gegevens opslag op basis van een bestand kunt u het beste naar een map schrijven als meerdere bestanden (Geef alleen de mapnaam op). in dat geval is de prestaties beter dan het schrijven naar één bestand.

| Scenario                                                     | Aanbevolen instellingen                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Volledige belasting van een grote tabel met fysieke partities.        | **Partitie optie**: fysieke partities van tabel. <br><br/>Tijdens de uitvoering worden de fysieke partities automatisch door Data Factory gedetecteerd en worden de gegevens gekopieerd op partities. <br><br/>Als u wilt controleren of de tabel fysieke partitie heeft, kunt u naar [deze query](#sample-query-to-check-physical-partition)verwijzen. |
| Volledige belasting van een grote tabel, zonder fysieke partities, met een geheel getal of datum/tijd-kolom voor het partitioneren van gegevens. | **Partitie opties**: partitie met dynamisch bereik.<br>**Partitie kolom** (optioneel): Geef de kolom op die wordt gebruikt om gegevens te partitioneren. Als u niets opgeeft, wordt de kolom index of primaire sleutel gebruikt.<br/>De **bovengrens** van de partitie en de **ondergrens** van de partitie (optioneel): Geef op of u de partitie-stride wilt bepalen. Dit is niet voor het filteren van de rijen in de tabel, alle rijen in de tabel worden gepartitioneerd en gekopieerd. Indien niet opgegeven, kopieer activiteit automatische detectie van de waarden.<br><br>Als uw partitie kolom ' ID ' bijvoorbeeld waarden bereik van 1 tot 100 heeft en u de ondergrens instelt op 20 en de bovengrens als 80, met parallelle kopie als 4, Data Factory worden gegevens opgehaald met vier partities-Id's in bereik <= 20, [21, 50], [51, 80] en >= 81, respectievelijk. |
| Laad een grote hoeveelheid gegevens met behulp van een aangepaste query, zonder fysieke partities, terwijl een kolom met een geheel getal of datum/tijd is voor het partitioneren van gegevens. | **Partitie opties**: partitie met dynamisch bereik.<br>**Query**: `SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>` .<br>**Partitie kolom**: Geef de kolom op die wordt gebruikt om gegevens te partitioneren.<br>De **bovengrens** van de partitie en de **ondergrens** van de partitie (optioneel): Geef op of u de partitie-stride wilt bepalen. Dit is niet voor het filteren van de rijen in de tabel, alle rijen in het query resultaat worden gepartitioneerd en gekopieerd. Als deze optie niet is opgegeven, wordt de waarde automatisch gedetecteerd met de Kopieer activiteit.<br><br>Tijdens de uitvoering wordt Data Factory vervangen `?AdfRangePartitionColumnName` door de werkelijke kolom naam en het waardebereik voor elke partitie, en verzonden naar SQL mi. <br>Als uw partitie kolom ' ID ' bijvoorbeeld waarden bereik van 1 tot 100 heeft en u de ondergrens instelt op 20 en de bovengrens als 80, met parallelle kopie als 4, Data Factory worden gegevens opgehaald met vier partities-Id's in bereik <= 20, [21, 50], [51, 80] en >= 81, respectievelijk. <br><br>Hier vindt u meer voorbeeld query's voor verschillende scenario's:<br> 1. query uitvoeren op de hele tabel: <br>`SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition`<br> 2. query uitvoeren vanuit een tabel met kolom selectie en extra WHERE-component filters: <br>`SELECT <column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 3. query met subquery's: <br>`SELECT <column_list> FROM (<your_sub_query>) AS T WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 4. query met partitie in subquery: <br>`SELECT <column_list> FROM (SELECT <your_sub_query_column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition) AS T`
|

Aanbevolen procedures voor het laden van gegevens met de optie partitie:

1. Kies een onderscheidende kolom als partitie kolom (zoals primaire sleutel of unieke sleutel) om te voor komen dat gegevens scheef trekken. 
2. Als de tabel een ingebouwde partitie heeft, gebruikt u partitie optie fysieke partities van tabel om betere prestaties te krijgen.    
3. Als u Azure Integration Runtime gebruikt om gegevens te kopiëren, kunt u grotere '[Data Integration units (DIU)](copy-activity-performance-features.md#data-integration-units)' (>4) instellen om meer computer bronnen te gebruiken. Controleer de toepasselijke scenario's.
4. "[Mate van kopiëren van parallellisme](copy-activity-performance-features.md#parallel-copy)" bepalen van de partitie nummers, het instellen van dit getal is te groot. Dit kan de prestaties nadelig beïnvloeden, het is raadzaam om dit aantal in te stellen als (DIU of het aantal zelf-HOSTende IR-knoop punten) * (2 tot 4).

**Voor beeld: volledige belasting van een grote tabel met fysieke partities**

```json
"source": {
    "type": "SqlMISource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Voor beeld: query met een dynamische bereik partitie**

```json
"source": {
    "type": "SqlMISource",
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

Als de tabel een fysieke partitie heeft, ziet u "HasPartition" als "ja" zoals in het volgende voor komt.

![SQL-query resultaat](./media/connector-azure-sql-database/sql-query-result.png)

## <a name="best-practice-for-loading-data-into-sql-managed-instance"></a>Aanbevolen procedure voor het laden van gegevens in een SQL-beheerd exemplaar

Wanneer u gegevens naar een SQL Managed instance kopieert, hebt u mogelijk een ander schrijf gedrag nodig:

- [Toevoegen](#append-data): mijn bron gegevens hebben alleen nieuwe records.
- [Upsert](#upsert-data): mijn bron gegevens hebben zowel toevoegingen als updates.
- [Overschrijven](#overwrite-the-entire-table): Ik wil de hele dimensie tabel elke keer opnieuw laden.
- [Schrijven met aangepaste logica](#write-data-with-custom-logic): Ik heb extra verwerking nodig vóór de laatste invoeging in de doel tabel. 

Zie de betreffende secties voor informatie over het configureren van Azure Data Factory en aanbevolen procedures.

### <a name="append-data"></a>Gegevens toevoegen

Het toevoegen van gegevens is het standaard gedrag van de SQL Managed instance Sink-connector. Azure Data Factory voert een bulksgewijze invoer uit om uw tabel efficiënt te schrijven. U kunt de bron en Sink dienovereenkomstig configureren in de Kopieer activiteit.

### <a name="upsert-data"></a>Upsert-gegevens

**Optie 1:** Wanneer u een grote hoeveelheid gegevens te kopiëren hebt, kunt u alle records bulksgewijs laden in een faserings tabel met behulp van de Kopieer activiteit en vervolgens een opgeslagen procedure-activiteit uitvoeren om een instructie [Merge](/sql/t-sql/statements/merge-transact-sql) of insert/update toe te passen in één afbeelding. 

De Kopieer activiteit biedt momenteel geen systeem eigen ondersteuning voor het laden van gegevens in een tijdelijke data base-tabel. Er is een geavanceerde manier om deze in te stellen met een combi natie van meerdere activiteiten. Raadpleeg [SQL database scenario's voor bulksgewijs Upsert optimaliseren](https://github.com/scoriani/azuresqlbulkupsert). Hieronder ziet u een voor beeld van het gebruik van een permanente tabel als fase ring.

In Azure Data Factory kunt u bijvoorbeeld een pijp lijn maken met een **Kopieer activiteit** die is gekoppeld aan een **opgeslagen procedure activiteit**. De vorige kopie kopieert gegevens uit uw bron archief naar een faserings tabel van Azure SQL Managed instance, bijvoorbeeld **UpsertStagingTable**, als de tabel naam in de gegevensset. Vervolgens roept de laatste een opgeslagen procedure aan om bron gegevens van de faserings tabel samen te voegen in de doel tabel en de faserings tabel op te schonen.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

Definieer in uw data base een opgeslagen procedure met SAMENVOEG logica, zoals in het volgende voor beeld, dat verwijst naar de vorige opgeslagen procedure activiteit. Stel dat het doel de **marketing** tabel is met drie kolommen: **ProfileID**, **State** en **Category**. Voer de upsert uit op basis van de kolom **ProfileID** .

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

**Optie 2:** U kunt ervoor kiezen om [een opgeslagen procedure binnen de Kopieer activiteit](#invoke-a-stored-procedure-from-a-sql-sink)aan te roepen. Met deze benadering wordt elke batch (zoals bepaald door de `writeBatchSize` eigenschap) in de bron tabel uitgevoerd in plaats van bulksgewijs invoegen als de standaard benadering in de Kopieer activiteit.

### <a name="overwrite-the-entire-table"></a>De volledige tabel overschrijven

U kunt de eigenschap **preCopyScript** in een Sink voor kopieer activiteiten configureren. In dit geval voert Azure Data Factory voor elke Kopieer activiteit die wordt uitgevoerd eerst het script uit. Vervolgens wordt de kopie uitgevoerd om de gegevens in te voegen. Als u bijvoorbeeld de volledige tabel wilt overschrijven met de meest recente gegevens, geeft u een script op om eerst alle records te verwijderen voordat u de nieuwe gegevens uit de bron laadt.

### <a name="write-data-with-custom-logic"></a>Gegevens schrijven met aangepaste logica

De stappen voor het schrijven van gegevens met aangepaste logica zijn vergelijkbaar met die in de sectie [Upsert-gegevens](#upsert-data) . Wanneer u extra verwerking wilt Toep assen voordat de laatste invoeging van bron gegevens in de doel tabel, kunt u laden naar een faserings tabel en vervolgens de opgeslagen procedure-activiteit aanroepen of een opgeslagen procedure aanroepen in Sink voor kopieer activiteiten om gegevens toe te passen.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Een opgeslagen procedure aanroepen vanuit een SQL-Sink

Wanneer u gegevens naar een SQL Managed instance kopieert, kunt u ook een door de gebruiker opgegeven opgeslagen procedure met aanvullende para meters voor elke batch van de bron tabel configureren en aanroepen. De functie opgeslagen procedure maakt gebruik van [para meters met tabel waarden](/dotnet/framework/data/adonet/sql/table-valued-parameters).

U kunt een opgeslagen procedure gebruiken wanneer ingebouwde Kopieer mechanismen het doel niet behouden. Een voor beeld hiervan is wanneer u een extra verwerking wilt Toep assen vóór het uiteindelijke invoegen van bron gegevens in de doel tabel. Sommige extra verwerkings voorbeelden zijn wanneer u kolommen wilt samen voegen, aanvullende waarden wilt opzoeken en in meer dan één tabel wilt invoegen.

In het volgende voor beeld ziet u hoe u een opgeslagen procedure gebruikt om een upsert te maken in een tabel in de SQL Server-Data Base. Stel dat de invoer gegevens en de provinciale **marketing** tabel drie kolommen hebben: **ProfileID**, **State** en **Category**. Voer de upsert uit op basis van de kolom **ProfileID** en pas deze alleen toe voor een specifieke categorie met de naam ProductA.

1. Definieer in uw data base het tabel type met de naam **sqlWriterTableType**. Het schema van het tabel type is hetzelfde als het schema dat wordt geretourneerd door de invoer gegevens.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. Definieer in uw data base de opgeslagen procedure met de naam **sqlWriterStoredProcedureName**. De invoer gegevens van de opgegeven bron worden verwerkt en samen voegingen in de uitvoer tabel. De parameter naam van het tabel type in de opgeslagen procedure is hetzelfde als **TableName** gedefinieerd in de gegevensset.

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

3. In Azure Data Factory definieert u de sectie **SQL mi Sink** in de Kopieer activiteit als volgt:

    ```json
    "sink": {
        "type": "SqlMISink",
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

## <a name="mapping-data-flow-properties"></a>Eigenschappen van gegevens stroom toewijzen

Wanneer gegevens worden getransformeerd in de toewijzing van gegevens stromen, kunt u tabellen lezen en ernaar schrijven vanuit een Azure SQL Managed instance. Zie voor meer informatie de [bron transformatie](data-flow-source.md) en [sink-trans](data-flow-sink.md) formatie in gegevens stromen toewijzen.

> [!NOTE]
> De Azure SQL Managed instance connector in de toewijzing van gegevens stroom is momenteel beschikbaar als open bare preview. U kunt nu verbinding maken met een openbaar eind punt van SQL Managed instance, maar nog geen persoonlijk eind punt.

### <a name="source-transformation"></a>Bron transformatie

De onderstaande tabel geeft een lijst van de eigenschappen die worden ondersteund door Azure SQL Managed instance source. U kunt deze eigenschappen bewerken op het tabblad **bron opties** .

| Naam | Beschrijving | Vereist | Toegestane waarden | Eigenschap gegevens stroom script |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Tabel | Als u tabel als invoer selecteert, haalt de gegevens stroom alle gegevens op uit de tabel die is opgegeven in de gegevensset. | Nee | - |- |
| Query’s uitvoeren | Als u query als invoer selecteert, geeft u een SQL-query op om gegevens op te halen uit de bron, waardoor elke tabel die u in dataset opgeeft, wordt overschreven. Het gebruik van query's is een uitstekende manier om rijen te verminderen voor testen of lookups.<br><br>**Order by** -component wordt niet ondersteund, maar u kunt een volledige Select from-instructie instellen. U kunt ook door de gebruiker gedefinieerde tabel functies gebruiken. **Select * from udfGetData ()** is een UDF in SQL waarmee een tabel wordt geretourneerd die u in de gegevens stroom kunt gebruiken.<br>Query voorbeeld: `Select * from MyTable where customerId > 1000 and customerId < 2000`| Nee | Tekenreeks | query |
| Batchgrootte | Geef een batch grootte op om grote gegevens in Lees bewerkingen te segmenteren. | Nee | Geheel getal | batchSize |
| Isolatie niveau | Kies een van de volgende isolatie niveaus:<br>-Doorgevoerde lezen<br>-Niet-doorgevoerde lezen (standaard)<br>-Herhaal bare Lees bewerking<br>-Serialiseerbaar<br>-Geen (isolatie niveau negeren) | Nee | <small>READ_COMMITTED<br/>READ_UNCOMMITTED<br/>REPEATABLE_READ<br/>SERIALIZABLE<br/>GEEN</small> |isolationLevel |

#### <a name="azure-sql-managed-instance-source-script-example"></a>Voor beeld van een bron script voor Azure SQL Managed instance

Wanneer u Azure SQL Managed instance als bron type gebruikt, is het gekoppelde gegevensstroom script:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    isolationLevel: 'READ_UNCOMMITTED',
    query: 'select * from MYTABLE',
    format: 'query') ~> SQLMISource
```

### <a name="sink-transformation"></a>Sink-trans formatie

De onderstaande tabel geeft een lijst van de eigenschappen die worden ondersteund door de Azure SQL Managed instance sink. U kunt deze eigenschappen bewerken op het tabblad **sink-opties** .

| Naam | Beschrijving | Vereist | Toegestane waarden | Eigenschap gegevens stroom script |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Update methode | Geef op welke bewerkingen zijn toegestaan voor uw database bestemming. De standaard instelling is alleen invoegen toestaan.<br>Als u rijen wilt bijwerken, upsert of verwijderen, is een [ALTER Row Transform](data-flow-alter-row.md) vereist om rijen voor die acties te labelen. | Ja | `true` of `false` | verwijderd <br/>invoegen <br/>bij te werken <br/>upsertable |
| Sleutel kolommen | Voor updates, upsert en verwijderen moeten de sleutel kolom (men) worden ingesteld om te bepalen welke rij moet worden gewijzigd.<br>De kolom naam die u kiest als de sleutel, wordt gebruikt als onderdeel van de volgende update, upsert, verwijderen. Daarom moet u een kolom kiezen die in de Sink-toewijzing voor komt. | Nee | Matrix | keys |
| Schrijf sleutel kolommen overs Laan | Als u de waarde niet naar de sleutel kolom wilt schrijven, selecteert u "schrijven van sleutel kolommen overs Laan". | Nee | `true` of `false` | skipKeyWrites |
| Tabel actie |Hiermee wordt bepaald of alle rijen van de doel tabel opnieuw moeten worden gemaakt of verwijderd voordat er wordt geschreven.<br>- **Geen**: er wordt geen actie uitgevoerd voor de tabel.<br>- **Opnieuw maken**: de tabel wordt verwijderd en opnieuw gemaakt. Vereist als er dynamisch een nieuwe tabel wordt gemaakt.<br>- **Afkappen**: alle rijen uit de doel tabel worden verwijderd. | Nee | `true` of `false` | opnieuw maken<br/>afkappen |
| Batchgrootte | Geef op hoeveel rijen er in elke batch worden geschreven. Grotere batch grootten verbeteren de compressie en Optima Lise ring van het geheugen, maar er zijn geen uitzonde ringen in het geheugen bij het opslaan van gegevens. | Nee | Geheel getal | batchSize |
| SQL-scripts vooraf en na | Geef meerdere SQL-scripts op die moeten worden uitgevoerd vóór (vóór verwerking) en nadat (na verwerking) gegevens naar uw Sink-Data Base zijn geschreven. | Nee | Tekenreeks | preSQLs<br>postSQLs |

#### <a name="azure-sql-managed-instance-sink-script-example"></a>Voor beeld van Azure SQL Managed instance-Sink-script

Wanneer u Azure SQL Managed instance als Sink-type gebruikt, is het gekoppelde gegevensstroom script:

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    deletable:false,
    insertable:true,
    updateable:true,
    upsertable:true,
    keys:['keyColumn'],
    format: 'table',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> SQLMISink
```

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.

## <a name="getmetadata-activity-properties"></a>Eigenschappen van GetMetadata-activiteit

Als u meer wilt weten over de eigenschappen, controleert u de [GetMetadata-activiteit](control-flow-get-metadata-activity.md) 

## <a name="data-type-mapping-for-sql-managed-instance"></a>Toewijzing van gegevens type voor door SQL beheerd exemplaar

Wanneer gegevens worden gekopieerd naar en van een SQL-beheerd exemplaar met behulp van Kopieer activiteit, worden de volgende toewijzingen gebruikt vanuit gegevens typen van het SQL-beheerde exemplaar om Azure Data Factory tussenliggende gegevens typen. Zie [schema en gegevens type toewijzingen](copy-activity-schema-and-type-mapping.md)voor meer informatie over hoe de Kopieer activiteit wordt toegewezen vanuit het bron schema en het gegevens type naar de sink.

| Gegevens type voor SQL-beheerde exemplaren | Azure Data Factory tussentijds gegevens type |
|:--- |:--- |
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
| tinyint |Int16 |
| uniqueidentifier |Guid |
| varbinary |Byte [] |
| varchar |Teken reeks, char [] |
| xml |Tekenreeks |

>[!NOTE]
> Voor gegevens typen die worden toegewezen aan het type van de decimale waarde, wordt op dat moment de functie voor het kopiëren van de precisie ondersteund tot 28. Als u gegevens hebt die een grotere nauw keurigheid dan 28 vereisen, kunt u overwegen om te converteren naar een teken reeks in een SQL-query.

## <a name="using-always-encrypted"></a>Always Encrypted gebruiken

Wanneer u gegevens kopieert van/naar Azure SQL Managed instance met [Always encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine), gebruikt u de [algemene ODBC-Connector](connector-odbc.md) en SQL Server ODBC-stuur programma via zelf-hostende Integration runtime. Deze Azure SQL Managed instance connector ondersteunt Always Encrypted nu niet. 

Met name:

1. Stel een zelf-hostende Integration Runtime in als u er nog geen hebt. Zie [zelf-hostende Integration runtime](create-self-hosted-integration-runtime.md) artikel voor meer informatie.

2. Down load hier het 64-bits ODBC-stuur programma voor SQL Server en Installeer [Dit](/sql/connect/odbc/download-odbc-driver-for-sql-server)op de Integration runtime machine. Meer informatie over hoe dit stuur programma kan [Always encrypted gebruiken met het ODBC-stuur programma voor SQL Server](/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver#using-the-azure-key-vault-provider).

3. Maak een gekoppelde service met een ODBC-type om verbinding te maken met uw SQL database. Raadpleeg de volgende voor beelden:

    - SQL- **verificatie** gebruiken: Geef de ODBC-Connection String op onder en selecteer **basis** verificatie om de gebruikers naam en het wacht woord in te stellen.

        ```
        Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>
        ```

    - **Data Factory beheerde identiteits verificatie** gebruiken: 

        1. Volg dezelfde [vereisten](#managed-identity) voor het maken van een database gebruiker voor de beheerde identiteit en het verlenen van de juiste rol in uw data base.
        2. Geef in gekoppelde service de ODBC-connection string op onder en selecteer **anonieme** verificatie als de connection string zelf aangeeft `Authentication=ActiveDirectoryMsi` .

        ```
        Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>; Authentication=ActiveDirectoryMsi;
        ```

4. Maak de gegevensset en kopieer activiteit met het ODBC-type dienovereenkomstig. Meer informatie in het artikel over [ODBC-connectors](connector-odbc.md) .

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit in azure Data Factory.
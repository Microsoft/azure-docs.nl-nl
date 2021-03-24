---
title: Gegevens kopiëren naar of van Azure Data Explorer
description: Meer informatie over het kopiëren van gegevens naar of van Azure Data Explorer met behulp van een Kopieer activiteit in een Azure Data Factory-pijp lijn.
ms.author: orspodek
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/24/2020
ms.openlocfilehash: f343cf820632c8b53f74a938a039820ea4f56eac
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105027394"
---
# <a name="copy-data-to-or-from-azure-data-explorer-by-using-azure-data-factory"></a>Gegevens kopiëren van of naar Azure Data Explorer met behulp van Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens te kopiëren van of naar [Azure Data Explorer](/azure/data-explorer/data-explorer-overview). Het is gebaseerd op het artikel overzicht van de [Kopieer activiteit](copy-activity-overview.md) . Dit biedt een algemeen overzicht van de Kopieer activiteit.

>[!TIP]
>Voor Azure Data Factory-en Azure Data Explorer-integratie in het algemeen leert u meer over het [integreren van azure Data Explorer met Azure Data Factory](/azure/data-explorer/data-factory-integration).

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Azure Data Explorer-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

U kunt gegevens van elk ondersteund bron gegevens archief kopiëren naar Azure Data Explorer. U kunt ook gegevens uit Azure Data Explorer kopiëren naar een ondersteunde Sink-gegevens opslag. Zie de tabel [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die door de Kopieer activiteit worden ondersteund als bronnen of Sinks.

>[!NOTE]
>Het kopiëren van gegevens naar of van Azure Data Explorer via een on-premises gegevens opslag met behulp van zelf-hostende Integration runtime wordt ondersteund in versie 3,14 en hoger.

Met de Azure Data Explorer-connector kunt u het volgende doen:

* Gegevens kopiëren met behulp van Azure Active Directory (Azure AD) verificatie van het toepassings token met een **Service-Principal**.
* Haal als bron gegevens op met behulp van een KQL (Kusto)-query.
* Gegevens toevoegen aan een doel tabel als sink.

## <a name="getting-started"></a>Aan de slag

>[!TIP]
>Zie voor een overzicht van de Azure Data Explorer-connector [gegevens kopiëren naar/van azure Data Explorer met behulp van Azure Data Factory](/azure/data-explorer/data-factory-load-data) en [bulksgewijs kopiëren van een data base naar Azure Data Explorer](/azure/data-explorer/data-factory-template).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor Azure Data Explorer-connector.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De Azure Data Explorer-connector ondersteunt de volgende verificatie typen. Zie de betreffende secties voor meer informatie:

- [Service-Principal-verificatie](#service-principal-authentication)
- [Beheerde identiteiten voor Azure-bronnen verificatie](#managed-identity)

### <a name="service-principal-authentication"></a>Verificatie van service-principal

Als u Service-Principal-verificatie wilt gebruiken, volgt u deze stappen om een service-principal op te halen en machtigingen te verlenen:

1. Registreer een toepassings entiteit in Azure Active Directory door de stappen te volgen in [uw toepassing registreren bij een Azure AD-Tenant](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Noteer de volgende waarden, die u gebruikt om de gekoppelde service te definiëren:

    - Toepassings-id
    - Toepassings sleutel
    - Tenant-id

2. Verleen de service-principal de juiste machtigingen in azure Data Explorer. Zie [Azure Data Explorer-database machtigingen beheren](/azure/data-explorer/manage-database-permissions) voor gedetailleerde informatie over rollen en machtigingen en over het beheren van machtigingen. Over het algemeen moet u het volgende doen:

    - Geef ten minste de rol van **Database Viewer** voor uw Data Base op **als bron**.
    - Ken **als Sink** ten minste de rol van **database opname** toe aan uw data base

>[!NOTE]
>Wanneer u de Data Factory gebruikers interface gebruikt om te schrijven, wordt uw aanmeldings gebruikers account standaard gebruikt om Azure Data Explorer clusters, data bases en tabellen weer te geven. U kunt ervoor kiezen om de objecten weer te geven met behulp van de Service-Principal door te klikken op de vervolg keuzelijst naast de knop Vernieuwen of door de naam hand matig in te voeren als u geen machtiging hebt voor deze bewerkingen.

De volgende eigenschappen worden ondersteund voor de gekoppelde Azure Data Explorer-service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** moet worden ingesteld op **AzureDataExplorer**. | Ja |
| endpoint | Eind punt-URL van het Azure Data Explorer-cluster, met de indeling als `https://<clusterName>.<regionName>.kusto.windows.net` . | Ja |
| database | De naam van de data base. | Ja |
| tenant | Geef de Tenant gegevens op (domein naam of Tenant-ID) waaronder uw toepassing zich bevindt. Dit staat bekend als ' Authority-ID ' in [Kusto Connection String](/azure/kusto/api/connection-strings/kusto#application-authentication-properties). U kunt deze ophalen door de muis aanwijzer in de rechter bovenhoek van de Azure Portal aan te wijzen. | Ja |
| servicePrincipalId | Geef de client-ID van de toepassing op. Dit wordt ook wel ' AAD-toepassings client-ID ' genoemd in [Kusto Connection String](/azure/kusto/api/connection-strings/kusto#application-authentication-properties). | Ja |
| servicePrincipalKey | Geef de sleutel van de toepassing op. Dit wordt aangeduid als ' AAD-toepassings sleutel ' in [Kusto Connection String](/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Markeer dit veld als een **SecureString** om het veilig op te slaan in Data Factory of om te [verwijzen naar beveiligde gegevens die zijn opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| connectVia | De [Integration runtime](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevens archief. U kunt de Azure Integration runtime of een zelf-hostende Integration runtime gebruiken als uw gegevens archief zich in een particulier netwerk bevindt. Als dat niet is opgegeven, wordt de standaard Azure Integration runtime gebruikt. |Nee |

**Voor beeld: verificatie met Service-Principal-sleutel**

```json
{
    "name": "AzureDataExplorerLinkedService",
    "properties": {
        "type": "AzureDataExplorer",
        "typeProperties": {
            "endpoint": "https://<clusterName>.<regionName>.kusto.windows.net ",
            "database": "<database name>",
            "tenant": "<tenant name/id e.g. microsoft.onmicrosoft.com>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            }
        }
    }
}
```

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a> Beheerde identiteiten voor Azure-bronnen verificatie

Voer de volgende stappen uit om machtigingen te verlenen voor het gebruik van beheerde identiteiten voor Azure-resource verificatie:

1. [Haal de Data Factory beheerde identiteits gegevens](data-factory-service-identity.md#retrieve-managed-identity) op door de waarde van de **beheerde ID-object-id** die samen met uw fabriek is gegenereerd, te kopiëren.

2. Verleen de beheerde identiteit de juiste machtigingen in azure Data Explorer. Zie [Azure Data Explorer-database machtigingen beheren](/azure/data-explorer/manage-database-permissions) voor gedetailleerde informatie over rollen en machtigingen en over het beheren van machtigingen. Over het algemeen moet u het volgende doen:

    - Geef ten minste de rol van **Database Viewer** voor uw Data Base op **als bron**.
    - Ken **als Sink** ten minste de rol van **database opname** toe aan uw data base

>[!NOTE]
>Wanneer u de Data Factory gebruikers interface gebruikt om te schrijven, wordt uw aanmeldings gebruikers account gebruikt voor het weer geven van Azure Data Explorer clusters, data bases en tabellen. Voer de naam hand matig in als u niet bent gemachtigd voor deze bewerkingen.

De volgende eigenschappen worden ondersteund voor de gekoppelde Azure Data Explorer-service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** moet worden ingesteld op **AzureDataExplorer**. | Ja |
| endpoint | Eind punt-URL van het Azure Data Explorer-cluster, met de indeling als `https://<clusterName>.<regionName>.kusto.windows.net` . | Ja |
| database | De naam van de data base. | Ja |
| connectVia | De [Integration runtime](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevens archief. U kunt de Azure Integration runtime of een zelf-hostende Integration runtime gebruiken als uw gegevens archief zich in een particulier netwerk bevindt. Als dat niet is opgegeven, wordt de standaard Azure Integration runtime gebruikt. |Nee |

**Voor beeld: beheerde identiteits verificatie gebruiken**

```json
{
    "name": "AzureDataExplorerLinkedService",
    "properties": {
        "type": "AzureDataExplorer",
        "typeProperties": {
            "endpoint": "https://<clusterName>.<regionName>.kusto.windows.net ",
            "database": "<database name>",
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie [gegevens sets in azure Data Factory](concepts-datasets-linked-services.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. In deze sectie vindt u de eigenschappen die door de Azure Data Explorer-gegevensset worden ondersteund.

Als u gegevens wilt kopiëren naar Azure Data Explorer, stelt u de eigenschap type van de gegevensset in op **AzureDataExplorerTable**.

De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** moet worden ingesteld op **AzureDataExplorerTable**. | Ja |
| tabel | De naam van de tabel waarnaar de gekoppelde service verwijst. | Ja voor Sink; Nee voor bron |

**Voor beeld van eigenschappen van gegevensset:**

```json
{
   "name": "AzureDataExplorerDataset",
    "properties": {
        "type": "AzureDataExplorerTable",
        "typeProperties": {
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Data Explorer linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten, Zie [pijp lijnen en activiteiten in azure Data Factory](concepts-pipelines-activities.md). In deze sectie vindt u een lijst met eigenschappen die door Azure Data Explorer-bronnen en-sinks worden ondersteund.

### <a name="azure-data-explorer-as-source"></a>Azure Data Explorer als bron

Als u gegevens wilt kopiëren uit Azure Data Explorer, stelt u de eigenschap **type** in de bron voor het kopiëren van de activiteit in op **AzureDataExplorerSource**. De volgende eigenschappen worden ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** van de bron van de Kopieer activiteit moet zijn ingesteld op: **AzureDataExplorerSource** | Ja |
| query | Een alleen-lezen aanvraag in een [KQL-indeling](/azure/kusto/query/). Gebruik de aangepaste KQL-query als referentie. | Ja |
| queryTimeout | De wacht tijd voordat de query aanvraag een time-out heeft. De standaard waarde is 10 minuten (00:10:00); de toegestane maximum waarde is 1 uur (01:00:00). | Nee |
| geen Afkap ping | Hiermee wordt aangegeven of de geretourneerde resultatenset moet worden afgekapt. Standaard wordt het resultaat afgekapt na 500.000 records of 64 mega bytes (MB). Afkap ping wordt sterk aanbevolen om te zorgen voor het juiste gedrag van de activiteit. |Nee |

>[!NOTE]
>Data Explorer bron van Azure heeft standaard een maximale grootte van 500.000 records of 64 MB. Als u alle records zonder afkap Ping wilt ophalen, kunt u `set notruncation;` aan het begin van de query opgeven. Zie [query limieten](/azure/kusto/concepts/querylimits)voor meer informatie.

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "AzureDataExplorerSource",
                "query": "TestTable1 | take 10",
                "queryTimeout": "00:10:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        },
        "inputs": [
            {
                "referenceName": "<Azure Data Explorer input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

### <a name="azure-data-explorer-as-sink"></a>Azure Data Explorer als Sink

Als u gegevens wilt kopiëren naar Azure Data Explorer, stelt u de eigenschap type in de Sink van de Kopieer activiteit in op **AzureDataExplorerSink**. De volgende eigenschappen worden ondersteund in het gedeelte **sink** van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** van de Sink voor kopieer activiteiten moet worden ingesteld op: **AzureDataExplorerSink**. | Ja |
| ingestionMappingName | Naam van een vooraf gemaakte [toewijzing](/azure/kusto/management/mappings#csv-mapping) in een Kusto-tabel. Als u de kolommen van de bron wilt toewijzen aan Azure Data Explorer (wat van toepassing is op [alle ondersteunde bron archieven en-indelingen](copy-activity-overview.md#supported-data-stores-and-formats), waaronder CSV/JSON-AVRO), kunt u de [kolom toewijzing](copy-activity-schema-and-type-mapping.md) van de Kopieer activiteit (impliciet op naam of expliciet als geconfigureerd) en/of Azure Data Explorer toewijzingen gebruiken. | Nee |
| additionalProperties | Een eigenschappen verzameling die kan worden gebruikt voor het opgeven van de opname-eigenschappen die niet al worden ingesteld door de Azure Data Explorer sink. Het is met name handig om opname Tags op te geven. Meer informatie over het [gegevens opname document van Azure data verkennen](/azure/data-explorer/ingestion-properties). | Nee |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyToAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataExplorerSink",
                "ingestionMappingName": "<optional Azure Data Explorer mapping name>",
                "additionalProperties": {<additional settings for data ingestion>}
            }
        },
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Explorer output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Zie [lookup activity](control-flow-lookup-activity.md)(Engelstalig) voor meer informatie over de eigenschappen.

## <a name="next-steps"></a>Volgende stappen

* Zie [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die de Kopieer activiteit in azure Data Factory ondersteunt als bronnen en Sinks.

* Meer informatie over het [kopiëren van gegevens van Azure Data Factory naar Azure Data Explorer](/azure/data-explorer/data-factory-load-data).
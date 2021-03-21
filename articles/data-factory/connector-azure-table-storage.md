---
title: Gegevens kopiëren van en naar Azure-tabel opslag
description: Informatie over het kopiëren van gegevens van ondersteunde bron archieven naar Azure-tabel opslag of van tabel opslag naar ondersteunde Sink-archieven met behulp van Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: 5674d656385d356f596f96cd7851d2cc05839761
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104597399"
---
# <a name="copy-data-to-and-from-azure-table-storage-by-using-azure-data-factory"></a>Gegevens kopiëren van en naar Azure Table Storage met behulp van Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](v1/data-factory-azure-table-connector.md)
> * [Huidige versie](connector-azure-table-storage.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de kopieeractiviteit in Azure Data Factory kunt gebruiken om gegevens te kopiëren vanuit en naar Azure Table-opslag. Het is gebaseerd op het artikel overzicht van de [Kopieer activiteit](copy-activity-overview.md) . Dit geeft een algemeen overzicht van de Kopieer activiteit.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Azure Table Storage-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

U kunt gegevens uit elk ondersteund brongegevens archief kopiëren naar de tabel opslag. U kunt ook gegevens uit de tabel opslag kopiëren naar elk ondersteund Sink-gegevens archief. Zie de tabel [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als bronnen of sinks op basis van de Kopieer activiteit.

Met name deze Azure Table-connector ondersteunt het kopiëren van gegevens met behulp van verificatie van de account sleutel en de service voor gedeelde toegang.

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor tabel opslag.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

### <a name="use-an-account-key"></a>Een account sleutel gebruiken

U kunt een Azure Storage gekoppelde service maken met behulp van de account sleutel. Het biedt de data factory wereld wijde toegang tot opslag. De volgende eigenschappen worden ondersteund.

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op **AzureTableStorage**. |Ja |
| connectionString | Geef de gegevens op die nodig zijn om verbinding te maken met opslag voor de Connections Tring-eigenschap. <br/>U kunt ook account sleutel in Azure Key Vault plaatsen en de `accountKey` configuratie uit de Connection String halen. Raadpleeg de volgende voor beelden en [Sla referenties op in azure Key Vault](store-credentials-in-key-vault.md) artikel met meer informatie. |Yes |
| connectVia | De [Integration runtime](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevens archief. U kunt Azure Integration Runtime of zelf-hostende Integration Runtime gebruiken (als uw gegevens archief zich in een particulier netwerk bevindt). Als u niets opgeeft, wordt de standaard Azure Integration Runtime gebruikt. |No |

>[!NOTE]
>Als u de gekoppelde service van het type ' opslag ' gebruikt, wordt deze nog steeds ondersteund als-is. u wordt aangeraden dit nieuwe ' AzureTableStorage ' gekoppelde Service Type vooruit te gebruiken.

**Voorbeeld:**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Voor beeld: account sleutel opslaan in Azure Key Vault**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;",
            "accountKey": { 
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

### <a name="use-shared-access-signature-authentication"></a>Verificatie van de Shared Access-hand tekening gebruiken

U kunt ook een gekoppelde opslag service maken met behulp van een hand tekening voor gedeelde toegang. Het biedt de data factory met beperkte/gebonden toegang tot alle/specifieke resources in de opslag.

Een SAS (een handtekening voor gedeelde toegang) biedt gedelegeerde toegang tot resources in uw opslagaccount. U kunt het gebruiken om een bepaalde en een opgegeven set machtigingen toe te kennen aan een client met beperkte machtigingen voor objecten in uw opslag account. U hoeft de toegangs sleutels van uw account niet te delen. De Shared Access-hand tekening is een URI die in de query parameters omvat alle informatie die nodig is voor geverifieerde toegang tot een opslag resource. Om toegang te krijgen tot opslag resources met de hand tekening voor gedeelde toegang, hoeft de client alleen de gedeelde toegangs handtekening door te geven aan de juiste constructor of methode. Zie voor meer informatie over hand tekeningen voor gedeelde toegang, [Shared Access Signatures: inzicht in het model voor de Shared Access-hand tekening](../storage/common/storage-sas-overview.md).

> [!NOTE]
> Data Factory ondersteunt nu zowel **gedeelde toegangs handtekeningen van services** als **hand tekeningen voor gedeelde toegang van accounts**. Zie voor meer informatie over gedeelde toegangs handtekeningen [beperkte toegang verlenen tot Azure storage-resources met behulp van Shared Access signatures (SAS)](../storage/common/storage-sas-overview.md). 

> [!TIP]
> U kunt de volgende Power shell-opdrachten uitvoeren om een hand tekening voor gedeelde toegang van een service te genereren voor uw opslag account. Vervang de tijdelijke aanduidingen en Ken de benodigde machtiging toe.
> `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

De volgende eigenschappen worden ondersteund om verificatie van de Shared Access-hand tekening te gebruiken.

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op **AzureTableStorage**. |Ja |
| sasUri | Geef de SAS-URI van de URI van de Shared Access-hand tekening op in de tabel. <br/>Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory. U kunt ook SAS-token in Azure Key Vault plaatsen om automatisch te draaien en het token gedeelte te verwijderen. Raadpleeg de volgende voor beelden en [Sla referenties op in azure Key Vault](store-credentials-in-key-vault.md) artikel met meer informatie. | Yes |
| connectVia | De [Integration runtime](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevens archief. U kunt de Azure Integration Runtime of de zelf-hostende Integration Runtime gebruiken (als uw gegevens archief zich in een particulier netwerk bevindt). Als u niets opgeeft, wordt de standaard Azure Integration Runtime gebruikt. |No |

>[!NOTE]
>Als u de gekoppelde service van het type ' opslag ' gebruikt, wordt deze nog steeds ondersteund als-is. u wordt aangeraden dit nieuwe ' AzureTableStorage ' gekoppelde Service Type vooruit te gebruiken.

**Voorbeeld:**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource e.g. https://<account>.table.core.windows.net/<table>?sv=<storage version>&amp;st=<start time>&amp;se=<expire time>&amp;sr=<resource>&amp;sp=<permissions>&amp;sip=<ip range>&amp;spr=<protocol>&amp;sig=<signature>>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Voor beeld: account sleutel opslaan in Azure Key Vault**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource without token e.g. https://<account>.table.core.windows.net/<table>>"
            },
            "sasToken": { 
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

Houd rekening met de volgende punten wanneer u een URI voor een Shared Access-hand tekening maakt:

- Stel de juiste machtigingen voor lezen/schrijven op objecten in op basis van de manier waarop de gekoppelde service (lezen, schrijven, lezen/schrijven) wordt gebruikt in uw data factory.
- Stel de **verlooptijd** op de juiste wijze in. Zorg ervoor dat de toegang tot opslag objecten niet verloopt binnen de actieve periode van de pijp lijn.
- De URI moet op het juiste tabel niveau worden gemaakt op basis van de nood zaak.

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de Azure Table-gegevensset.

Als u gegevens wilt kopiëren van en naar de Azure-tabel, stelt u de eigenschap type van de gegevensset in op **AzureTable**. De volgende eigenschappen worden ondersteund.

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de DataSet moet worden ingesteld op **AzureTable**. |Yes |
| tableName |De naam van de tabel in het data base-exemplaar van Table-opslag waarnaar de gekoppelde service verwijst. |Ja |

**Voorbeeld:**

```json
{
    "name": "AzureTableDataset",
    "properties":
    {
        "type": "AzureTable",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Table storage linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Schema door Data Factory

Voor gegevens archieven zonder schema, zoals Azure-tabel, wordt het schema door Data Factory op een van de volgende manieren afleiden:

* Als u de kolom toewijzing in de Kopieer activiteit opgeeft, gebruikt Data Factory de lijst met kolommen aan de bron zijde om gegevens op te halen. Als in dit geval een rij geen waarde voor een kolom bevat, wordt er een null-waarde voor gegeven.
* Als u de kolom toewijzing in de Kopieer activiteit niet opgeeft, wordt door Data Factory het schema afleiden door de eerste rij in de gegevens te gebruiken. Als in dit geval de eerste rij niet het volledige schema bevat (bijvoorbeeld sommige kolommen hebben een null-waarde), worden sommige kolommen niet in het resultaat van de Kopieer bewerking gemist.

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de Azure Table-bron en Sink.

### <a name="azure-table-as-a-source-type"></a>Azure-tabel als bron type

Als u gegevens wilt kopiëren uit een Azure-tabel, stelt u het bron type in de Kopieer activiteit in op **AzureTableSource**. De volgende eigenschappen worden ondersteund in de sectie **bron** van de Kopieer activiteit.

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op **AzureTableSource**. |Yes |
| azureTableSourceQuery |Gebruik de aangepaste Table-opslag query om gegevens te lezen.<br/>De bron query is een directe kaart van de `$filter` query optie die wordt ondersteund door Azure Table Storage, meer informatie over de syntaxis van [dit document](/rest/api/storageservices/querying-tables-and-entities#supported-query-options)en de voor beelden in de volgende [sectie met azureTableSourceQuery-voor beelden](#azuretablesourcequery-examples). |No |
| azureTableSourceIgnoreTableNotFound |Hiermee wordt aangegeven of de uitzonde ring van de tabel mag worden toegestaan.<br/>Toegestane waarden zijn **True** en **False** (standaard). |No |

### <a name="azuretablesourcequery-examples"></a>azureTableSourceQuery-voor beelden

>[!NOTE]
>De bewerking van een Azure Table-query verkeert een time-out van 30 seconden, die wordt [afgedwongen door Azure Table service](/rest/api/storageservices/setting-timeouts-for-table-service-operations). Meer informatie over het optimaliseren van de query van het [ontwerp voor het uitvoeren van query's](../storage/tables/table-storage-design-for-query.md) .

In Azure Data Factory, als u de gegevens wilt filteren op basis van een datum type kolom, raadpleegt u dit voor beeld:

```json
"azureTableSourceQuery": "LastModifiedTime gt datetime'2017-10-01T00:00:00' and LastModifiedTime le datetime'2017-10-02T00:00:00'"
```

Als u de gegevens wilt filteren op basis van een kolom voor het type teken reeks, raadpleegt u dit voor beeld:

```json
"azureTableSourceQuery": "LastModifiedTime ge '201710010000_0000' and LastModifiedTime le '201710010000_9999'"
```

Als u de para meter pijplijn gebruikt, moet u de datum-/tijdwaarde casten naar de juiste notatie volgens de voor gaande voor beelden.

### <a name="azure-table-as-a-sink-type"></a>Azure-tabel als Sink-type

Als u gegevens wilt kopiëren naar de Azure-tabel, stelt u het sink-type in de Kopieer activiteit in op **AzureTableSink**. De volgende eigenschappen worden ondersteund in het gedeelte **sink** van de Kopieer activiteit.

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de Sink voor kopieer activiteiten moet worden ingesteld op **AzureTableSink**. |Yes |
| azureTableDefaultPartitionKeyValue |De standaard waarde voor de partitie sleutel die door de Sink kan worden gebruikt. |No |
| azureTablePartitionKeyName |Geef de naam op van de kolom waarvan de waarden worden gebruikt als partitie sleutels. Als u niets opgeeft, wordt ' AzureTableDefaultPartitionKeyValue ' gebruikt als de partitie sleutel. |No |
| azureTableRowKeyName |Geef de naam op van de kolom waarvan de kolom waarden worden gebruikt als de rij-sleutel. Als u deze niet opgeeft, gebruikt u een GUID voor elke rij. |No |
| azureTableInsertType |De modus om gegevens in de Azure-tabel in te voegen. Met deze eigenschap bepaalt u of bestaande rijen in de uitvoer tabel met overeenkomende partitie-en rijlabels hun waarden vervangen of samengevoegd hebben. <br/><br/>Toegestane waarden zijn **samen voegen** (standaard) en **vervangen**. <br/><br> Deze instelling is van toepassing op het niveau van de rij, niet op het tabel niveau. Geen van beide opties verwijdert rijen in de uitvoer tabel die niet voor komen in de invoer. Zie [entiteit invoegen of samen voegen](/rest/api/storageservices/Insert-Or-Merge-Entity) en [entiteit invoegen of vervangen](/rest/api/storageservices/Insert-Or-Replace-Entity)voor meer informatie over het werken met de instellingen voor samen voegen en vervangen. |No |
| writeBatchSize |Voegt gegevens in de Azure-tabel in wanneer writeBatchSize of writeBatchTimeout wordt bereikt.<br/>Toegestane waarden zijn integer (aantal rijen). |Nee (de standaard waarde is 10.000) |
| writeBatchTimeout |Voegt gegevens in de Azure-tabel in wanneer writeBatchSize of writeBatchTimeout wordt bereikt.<br/>Toegestane waarden zijn time span. Een voor beeld is ' 00:20:00 ' (20 minuten). |Nee (de standaard waarde is 90 seconden, standaard time-out van de opslaglaag van de client) |
| maxConcurrentConnections |De bovengrens van gelijktijdige verbindingen die tot het gegevens archief zijn gemaakt tijdens de uitvoering van de activiteit. Geef alleen een waarde op als u gelijktijdige verbindingen wilt beperken.| No |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyToAzureTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Table output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureTableSink",
                "azureTablePartitionKeyName": "<column name>",
                "azureTableRowKeyName": "<column name>"
            }
        }
    }
]
```

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName

Wijs een bron kolom toe aan een doel kolom met behulp van de eigenschap **Translator** voordat u de doel kolom als azureTablePartitionKeyName kunt gebruiken.

In het volgende voor beeld wordt bron kolom DivisionID toegewezen aan de doel kolom DivisionID:

```json
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```

' DivisionID ' is opgegeven als de partitie sleutel.

```json
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID"
}
```

## <a name="data-type-mapping-for-azure-table"></a>Toewijzing van gegevens type voor Azure-tabel

Wanneer u gegevens kopieert vanuit en naar een Azure-tabel, worden de volgende toewijzingen gebruikt vanuit gegevens typen van Azure-tabel om tussenliggende gegevens typen te Data Factory. Zie [schema en gegevens type toewijzingen](copy-activity-schema-and-type-mapping.md)voor meer informatie over hoe de Kopieer activiteit het bron schema en het gegevens type aan de Sink toewijst.

Wanneer u gegevens naar en van een Azure-tabel verplaatst, worden de volgende toewijzingen die zijn [gedefinieerd door Azure](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model) Table, gebruikt vanuit de Azure Table OData-typen naar .net-type en vice versa.

| Gegevens type van Azure-tabel | Data Factory tussentijds gegevens type | Details |
|:--- |:--- |:--- |
| EDM. binary |byte [] |Een byte matrix van Maxi maal 64 KB. |
| Edm.Boolean |booleaans |Een booleaanse waarde. |
| EDM. DateTime |DateTime |Een waarde van 64 bits, uitgedrukt als Coordinated Universal Time (UTC). Het ondersteunde DateTime-bereik begint middernacht, 1 januari 1601 n. (C.E.), UTC. Het bereik eindigt op 31 december 9999. |
| Edm.Double |double |Een 64-bits drijvende-komma waarde. |
| EDM. GUID |Guid |Een 128-bits Globally Unique Identifier. |
| Edm.Int32 |Int32 |Een 32-bits geheel getal. |
| Edm.Int64 |Int64 |Een 64-bits geheel getal. |
| Edm.String |Tekenreeks |Een UTF-16-gecodeerde waarde. Teken reeks waarden kunnen Maxi maal 64 KB groot zijn. |

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit in Data Factory.
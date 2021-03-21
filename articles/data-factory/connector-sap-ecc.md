---
title: Gegevens kopiëren van SAP ECC
description: Informatie over het kopiëren van gegevens van SAP ECC naar ondersteunde Sink-gegevens opslag met behulp van een Kopieer activiteit in een Azure Data Factory-pijp lijn.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/28/2020
ms.openlocfilehash: a3e701f3d433b5b52d8992035ac4ad75b78cb795
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100386693"
---
# <a name="copy-data-from-sap-ecc-by-using-azure-data-factory"></a>Gegevens van SAP ECC kopiëren met behulp van Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens te kopiëren van het SAP Enter prise Central-onderdeel (ECC). Zie [overzicht van Kopieer activiteiten](copy-activity-overview.md)voor meer informatie.

>[!TIP]
>Zie [SAP Data Integration using Azure Data Factory White Paper](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) (Engelstalig) voor meer informatie over de algemene ondersteuning van de ADF in het scenario voor SAP-gegevens integratie met een gedetailleerde inleiding op elke SAP-connector, comparsion en richt lijnen.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze SAP ECC-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

U kunt gegevens van SAP ECC kopiëren naar elk ondersteund Sink-gegevens archief. Zie de tabel [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als bronnen of sinks op basis van de Kopieer activiteit.

Deze SAP ECC-connector ondersteunt met name:

- Gegevens kopiëren van SAP ECC op SAP NetWeaver versie 7,0 en hoger.
- Gegevens kopiëren van objecten die worden blootgesteld door SAP ECC OData-Services, zoals:

  - SAP-tabellen of-weer gaven.
  - Zakelijke BAPI-objecten (Application Programming Interface).
  - Gegevens ophalen.
  - Gegevens of tussenliggende documenten (IDOCs) die worden verzonden naar SAP Process Integration (PI) en kunnen worden ontvangen als OData via relatieve adapters.

- Kopiëren van gegevens met behulp van basis verificatie.

Versie 7,0 of hoger verwijst naar SAP NetWeaver-versie in plaats van SAP ECC-versie. Bijvoorbeeld: SAP ECC 6,0 EHP 7 in het algemeen heeft NetWeaver versie >= 7,4. Als u twijfelt over uw omgeving, kunt u de volgende stappen uitvoeren om de versie van uw SAP-systeem te bevestigen:

1. Gebruik de SAP-GUI om verbinding te maken met het SAP-systeem. 
2. Ga naar de **systeem**  ->  **status**. 
3. Controleer de versie van de SAP_BASIS, Controleer of deze gelijk is aan of groter is dan 701.  
      ![SAP_BASIS controleren](./media/connector-sap-table/sap-basis.png)

>[!TIP]
>Als u gegevens wilt kopiëren van SAP ECC via een SAP-tabel of-weer gave, gebruikt u de [SAP-tabel](connector-sap-table.md) connector, die sneller en schaalbaar is.

## <a name="prerequisites"></a>Vereisten

Als u deze SAP ECC-connector wilt gebruiken, moet u de SAP ECC-entiteiten via OData-services beschikbaar maken via de SAP-gateway. Met name:

- **SAP-gateway instellen**. Voor servers met SAP NetWeaver-versies hoger dan 7,4, is SAP gateway al geïnstalleerd. Voor eerdere versies moet u de Inge sloten SAP-gateway of het SAP gateway-hub systeem installeren voordat u SAP ECC-gegevens via OData-Services weergeeft. Zie de [installatie handleiding](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm)voor het instellen van SAP gateway.

- **De SAP OData-service activeren en configureren**. U kunt de OData-service binnen enkele seconden activeren via TCODE SICF. U kunt ook configureren welke objecten moeten worden weer gegeven. Raadpleeg de [Stapsgewijze richt lijnen](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/)voor meer informatie.

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van de Data Factory entiteiten die specifiek zijn voor de SAP ECC-connector.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor de SAP ECC-gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| `type` | De `type` eigenschap moet worden ingesteld op `SapEcc` . | Ja |
| `url` | De URL van de SAP ECC OData-service. | Ja |
| `username` | De gebruikers naam die wordt gebruikt om verbinding te maken met SAP ECC. | Nee |
| `password` | Het Lees bare wacht woord dat wordt gebruikt om verbinding te maken met SAP ECC. | Nee |
| `connectVia` | De [Integration runtime](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevens archief. Meer informatie vindt u in de sectie [vereisten](#prerequisites) . Als u geen runtime opgeeft, wordt de standaard Azure Integration runtime gebruikt. | Nee |

### <a name="example"></a>Voorbeeld

```json
{
    "name": "SapECCLinkedService",
    "properties": {
        "type": "SapEcc",
        "typeProperties": {
            "url": "<SAP ECC OData URL, e.g., http://eccsvrname:8000/sap/opu/odata/sap/zgw100_dd02l_so_srv/>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    },
    "connectVia": {
        "referenceName": "<name of integration runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie [gegevens sets](concepts-datasets-linked-services.md)voor een volledige lijst met de secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. De volgende sectie bevat een lijst met de eigenschappen die worden ondersteund door de SAP ECC-gegevensset.

Als u gegevens wilt kopiëren uit SAP ECC, stelt `type` u de eigenschap van de gegevensset in op `SapEccResource` .

De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| `path` | Pad van de SAP ECC OData-entiteit. | Ja |

### <a name="example"></a>Voorbeeld

```json
{
    "name": "SapEccDataset",
    "properties": {
        "type": "SapEccResource",
        "typeProperties": {
            "path": "<entity path, e.g., dd04tentitySet>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP ECC linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie [pijp lijnen](concepts-pipelines-activities.md)voor een volledige lijst met de secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. De volgende sectie bevat een lijst met de eigenschappen die worden ondersteund door de SAP ECC-bron.

### <a name="sap-ecc-as-a-source"></a>SAP ECC als een bron

Als u gegevens wilt kopiëren uit SAP ECC, stelt u de `type` eigenschap in het `source` gedeelte van de Kopieer activiteit in op `SapEccSource` .

De volgende eigenschappen worden ondersteund in de sectie Kopieer activiteit `source` :

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| `type` | De `type` eigenschap van de sectie van de Kopieer activiteit `source` moet worden ingesteld op `SapEccSource` . | Ja |
| `query` | De OData-query opties voor het filteren van de gegevens. Bijvoorbeeld:<br/><br/>`"$select=Name,Description&$top=10"`<br/><br/>De SAP ECC-connector kopieert gegevens van de gecombineerde URL:<br/><br/>`<URL specified in the linked service>/<path specified in the dataset>?<query specified in the copy activity's source section>`<br/><br/>Zie [ODATA URL Components](https://www.odata.org/documentation/odata-version-3-0/url-conventions/)(Engelstalig) voor meer informatie. | Nee |
| `sapDataColumnDelimiter` | Het enkelvoudige teken dat wordt gebruikt als scheidings tekens dat wordt door gegeven aan SAP RFC om de uitvoer gegevens te splitsen. | Nee |
| `httpRequestTimeout` | De time-out (de time **span** -waarde) voor de HTTP-aanvraag om een antwoord te krijgen. Deze waarde is de time-out voor het verkrijgen van een reactie, niet de time-out voor het lezen van antwoord gegevens. Als niet wordt opgegeven, is de standaard waarde **00:30:00** (30 minuten). | Nee |

### <a name="example"></a>Voorbeeld

```json
"activities":[
    {
        "name": "CopyFromSAPECC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP ECC input dataset name>",
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
                "type": "SapEccSource",
                "query": "$top=10"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mappings-for-sap-ecc"></a>Toewijzing van gegevens typen voor SAP ECC

Wanneer u gegevens van SAP ECC kopieert, worden de volgende toewijzingen gebruikt vanuit OData-gegevens typen voor SAP ECC-gegevens om Azure Data Factory tussenliggende gegevens typen. Zie [schema en gegevens type toewijzingen](copy-activity-schema-and-type-mapping.md)voor meer informatie over hoe de Kopieer activiteit het bron schema en het gegevens type aan de Sink koppelt.

| OData-gegevens type | Data Factory tussentijds gegevens type |
|:--- |:--- |
| `Edm.Binary` | `String` |
| `Edm.Boolean` | `Bool` |
| `Edm.Byte` | `String` |
| `Edm.DateTime` | `DateTime` |
| `Edm.Decimal` | `Decimal` |
| `Edm.Double` | `Double` |
| `Edm.Single` | `Single` |
| `Edm.Guid` | `String` |
| `Edm.Int16` | `Int16` |
| `Edm.Int32` | `Int32` |
| `Edm.Int64` | `Int64` |
| `Edm.SByte` | `Int16` |
| `Edm.String` | `String` |
| `Edm.Time` | `TimeSpan` |
| `Edm.DateTimeOffset` | `DateTimeOffset` |

> [!NOTE]
> Complexe gegevens typen worden momenteel niet ondersteund.

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.

## <a name="next-steps"></a>Volgende stappen

Zie [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit in azure Data Factory.

---
title: Gegevens kopiëren van SAP Business Warehouse via open hub
description: Informatie over het kopiëren van gegevens van SAP Business Warehouse (BW) via open hub naar ondersteunde Sink-gegevens archieven met behulp van een Kopieer activiteit in een Azure Data Factory-pijp lijn.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/02/2020
ms.openlocfilehash: ca8fad59e581ef3f5a3ebf585356564d539f0bbd
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430727"
---
# <a name="copy-data-from-sap-business-warehouse-via-open-hub-using-azure-data-factory"></a>Gegevens kopiëren van SAP Business Warehouse via open hub met behulp van Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens uit een SAP Business Warehouse (BW) te kopiëren via open hub. Het is gebaseerd op het artikel overzicht van de [Kopieer activiteit](copy-activity-overview.md) . Dit geeft een algemeen overzicht van de Kopieer activiteit.

>[!TIP]
>Zie [SAP Data Integration using Azure Data Factory White Paper](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) (Engelstalig) voor meer informatie over de algemene ondersteuning van de ADF in het scenario voor SAP-gegevens integratie met een gedetailleerde inleiding op elke SAP-connector, comparsion en richt lijnen.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze SAP Business Warehouse via open hub-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

U kunt gegevens van SAP Business Warehouse kopiëren via open hub naar een ondersteunde Sink-gegevens opslag. Zie de tabel [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als bron/sinks door de Kopieer activiteit.

Met name de open hub-connector van SAP Business Warehouse ondersteunt:

- SAP Business Warehouse **versie 7,01 of hoger (in een recente SAP-ondersteunings pakket stack uitgebracht na het jaar 2015)**. SAP BW/4HANA wordt niet ondersteund door deze connector.
- Het kopiëren van gegevens via de lokale open hub-doel tabel, die onder andere de naam DSO, InfoCube, multilevering, Data Source, enzovoort kan zijn.
- Kopiëren van gegevens met behulp van basis verificatie.
- Verbinding maken met een SAP-toepassings server of SAP-berichten server.
- Gegevens ophalen via RFC.

## <a name="sap-bw-open-hub-integration"></a>SAP BW hub-integratie openen 

[SAP BW open hub-service](https://wiki.scn.sap.com/wiki/display/BI/Overview+of+Open+Hub+Service) is een efficiënte manier om gegevens op te halen uit SAP BW. Het volgende diagram toont een van de typische stromen die klanten hebben in hun SAP-systeem, in welk geval gegevens stromen van SAP ECC-> PSA-> DSO-> kubus.

SAP BW open hub Destination (OHD) definieert het doel waarnaar de SAP-gegevens worden doorgestuurd. Objecten die worden ondersteund door SAP Gegevensoverdracht process (DTP) kunnen worden gebruikt als open hub-gegevens bronnen, bijvoorbeeld DSO, InfoCube, Data Source, enzovoort. Open hub-doel type: waar de door gegeven gegevens worden opgeslagen: kunnen database tabellen (lokaal of extern) en platte bestanden zijn. Deze SAP BW open hub-connector ondersteuning voor het kopiëren van gegevens uit een lokale OHD-tabel in BW. Als u andere typen gebruikt, kunt u rechtstreeks verbinding maken met de data base of het bestands systeem met behulp van andere connectors.

![SAP BW hub openen](./media/connector-sap-business-warehouse-open-hub/sap-bw-open-hub.png)

## <a name="delta-extraction-flow"></a>Delta-extractie stroom

ADF SAP BW open hub-connector biedt twee optionele eigenschappen: `excludeLastRequest` en `baseRequestId` die kunnen worden gebruikt voor het afhandelen van de Delta belasting van open hub. 

- **excludeLastRequestId**: Hiermee wordt aangegeven of de records van de laatste aanvraag moeten worden uitgesloten. De standaardwaarde is Waar. 
- **baseRequestId**: de id van de aanvraag voor het laden van verschillen. Als deze eenmaal is ingesteld, worden alleen gegevens opgehaald met de waarde-naam groter dan die van deze eigenschap. 

Over het algemeen bestaat de extractie van SAP InfoProviders naar Azure Data Factory (ADF) uit twee stappen: 

1. **SAP BW gegevensoverdracht proces (DTP)** Met deze stap worden de gegevens van een SAP BW InfoProvider gekopieerd naar een SAP BW open hub-tabel 

1. **ADF-gegevens kopie** In deze stap wordt de tabel open hub gelezen door de ADF-connector 

![Delta-extractie stroom](media/connector-sap-business-warehouse-open-hub/delta-extraction-flow.png)

In de eerste stap wordt een DTP uitgevoerd. Bij elke uitvoering wordt een nieuwe SAP-aanvraag-ID gemaakt. De aanvraag-ID wordt opgeslagen in de open hub-tabel en wordt vervolgens gebruikt door de ADF-connector om de Delta te identificeren. De twee stappen worden asynchroon uitgevoerd: de DTP wordt geactiveerd door SAP en de ADF-gegevens kopie wordt geactiveerd via ADF. 

Standaard wordt de laatste Delta van de open hub-tabel niet door ADF gelezen (de optie ' laatste aanvraag uitsluiten ' is ingesteld op ' True '). Dat wil zeggen dat de gegevens in ADF niet 100% up-to-date zijn met de gegevens in de open hub-tabel (de laatste Delta ontbreekt). Als resultaat wordt met deze procedure gegarandeerd dat er geen rijen verloren gaan die worden veroorzaakt door de asynchrone extractie. Het werkt ook prima wanneer ADF de open hub-tabel leest terwijl de DTP nog in dezelfde tabel wordt geschreven. 

Normaal gesp roken slaat u de Maxi maal gekopieerde aanvraag-ID in de laatste keer uitgevoerd door ADF in een faserings gegevensopslag (zoals Azure Blob in bovenstaand diagram). Daarom wordt dezelfde aanvraag niet een tweede keer door ADF gelezen tijdens de volgende uitvoering. De gegevens worden ondertussen niet automatisch verwijderd uit de tabel open hub.

Voor een correcte Delta verwerking is het niet toegestaan om aanvraag-Id's van verschillende DTPs in dezelfde open hub-tabel te hebben. Daarom moet u niet meer dan één DTP maken voor elke open hub-bestemming (OHD). Wanneer u de volledige en Delta-extractie van hetzelfde InfoProvider nodig hebt, moet u twee OHDs voor dezelfde InfoProvider maken. 

## <a name="prerequisites"></a>Vereisten

Als u deze SAP Business Warehouse open hub-connector wilt gebruiken, moet u het volgende doen:

- Stel een zelf-hostende Integration Runtime in met versie 3,13 of hoger. Zie [zelf-hostende Integration runtime](create-self-hosted-integration-runtime.md) artikel voor meer informatie.

- Down load de **64-bits [SAP .net connector 3,0](https://support.sap.com/en/product/connectors/msnet.html)** van de SAP-website en installeer deze op de zelf-hostende IR-computer. Zorg er bij de installatie in het venster optionele installatie stappen voor dat u de optie **Assembly's installeren op GAC** selecteert, zoals wordt weer gegeven in de volgende afbeelding. 

    ![SAP .NET-connector installeren](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- SAP-gebruiker die wordt gebruikt in de Data Factory BW connector moet de volgende machtigingen hebben: 

    - Autorisatie voor RFC en SAP BW. 
    - Machtigingen voor de activiteit ' uitvoeren ' van het autorisatie object ' S_SDSAUTH '.

- Maak SAP open hub doel type als **database tabel** met de optie "technische sleutel" ingeschakeld.  Het is ook raadzaam om het verwijderen van gegevens uit de tabel als uitgeschakeld te laten, hoewel dit niet vereist is. Gebruik de DTP (rechtstreeks uitvoeren of integreren in bestaande proces keten) om gegevens uit het bron object (zoals kubus) te gebruiken die u hebt gekozen voor de open hub-doel tabel.

## <a name="getting-started"></a>Aan de slag

> [!TIP]
>
> Zie [gegevens laden van SAP Business Warehouse (BW) met behulp van Azure Data Factory](load-sap-bw-data.md)voor een overzicht van het gebruik van SAP BW open hub-connector.

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor SAP Business Warehouse open hub-connector.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor de gekoppelde service van SAP Business Warehouse open hub:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op: **SapOpenHub** | Ja |
| server | De naam van de server waarop het SAP BW-exemplaar zich bevindt. | Ja |
| systemNumber | Systeem nummer van het SAP BW systeem.<br/>Toegestane waarde: decimaal getal van twee cijfers dat wordt weer gegeven als een teken reeks. | Ja |
| messageServer | De hostnaam van de SAP-berichten server.<br/>Gebruiken om verbinding te maken met een SAP-berichten server. | Nee |
| messageServerService | De service naam of het poort nummer van de berichten server.<br/>Gebruiken om verbinding te maken met een SAP-berichten server. | Nee |
| systemId | De ID van het SAP-systeem waarin de tabel zich bevindt.<br/>Gebruiken om verbinding te maken met een SAP-berichten server. | Nee |
| logonGroup | De aanmeldings groep voor het SAP-systeem.<br/>Gebruiken om verbinding te maken met een SAP-berichten server. | Nee |
| clientId | Client-ID van de client in het SAP W-systeem.<br/>Toegestane waarde: decimaal getal met drie cijfers dat wordt weer gegeven als een teken reeks. | Ja |
| language | De taal die door het SAP-systeem wordt gebruikt. | Nee (standaard waarde is **en**)|
| userName | De naam van de gebruiker die toegang heeft tot de SAP-server. | Ja |
| wachtwoord | Het wachtwoord voor de gebruiker. Markeer dit veld als SecureString om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| connectVia | Het [Integration runtime](concepts-integration-runtime.md) dat moet worden gebruikt om verbinding te maken met het gegevens archief. Een zelf-hostende Integration Runtime is vereist zoals vermeld in de [vereisten](#prerequisites). |Ja |

**Voorbeeld:**

```json
{
    "name": "SapBwOpenHubLinkedService",
    "properties": {
        "type": "SapOpenHub",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de SAP BW open hub-gegevensset.

Als u gegevens wilt kopiëren van en naar SAP BW geopende hub, stelt u de eigenschap type van de gegevensset in op **SapOpenHubTable**. De volgende eigenschappen worden ondersteund.

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op **SapOpenHubTable**.  | Ja |
| openHubDestinationName | De naam van het open hub-doel waaruit de gegevens moeten worden gekopieerd. | Ja |

Als u de instelling `excludeLastRequest` en `baseRequestId` in de gegevensset hebt ingesteld, wordt deze nog steeds ondersteund als-is. u wordt aangeraden het nieuwe model in de activiteit bron te gebruiken.

**Voorbeeld:**

```json
{
    "name": "SAPBWOpenHubDataset",
    "properties": {
        "type": "SapOpenHubTable",
        "typeProperties": {
            "openHubDestinationName": "<open hub destination name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP BW Open Hub linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door SAP BW open hub-bron.

### <a name="sap-bw-open-hub-as-source"></a>SAP BW hub als bron openen

Als u gegevens wilt kopiëren van SAP BW geopende hub, worden de volgende eigenschappen ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** van de bron van de Kopieer activiteit moet zijn ingesteld op **SapOpenHubSource**. | Ja |
| excludeLastRequest | Hiermee wordt aangegeven of de records van de laatste aanvraag moeten worden uitgesloten. | Nee (standaard waarde is **waar**) |
| baseRequestId | De ID van de aanvraag voor het laden van verschillen. Als deze eenmaal is ingesteld, worden alleen gegevens opgehaald met de waarde-naam **groter dan** die van deze eigenschap.  | Nee |

>[!TIP]
>Als uw open hub-tabel alleen de gegevens bevat die zijn gegenereerd op basis van een enkele aanvraag-ID, kunt u bijvoorbeeld altijd volledige belasting doen en de bestaande gegevens in de tabel overschrijven, of u kunt de DTP slechts eenmaal uitvoeren voor de test, de optie ' excludeLastRequest ' uitschakelen om de gegevens uit te kunnen kopiëren.

Als u het laden van gegevens wilt versnellen, kunt u [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) de Kopieer activiteit zo instellen dat gegevens van SAP BW hub parallel worden geladen. Als u bijvoorbeeld hebt ingesteld `parallelCopies` op vier, Data Factory gelijktijdig vier rfc's-aanroepen uitvoeren en elke RFC-aanroep haalt een deel van de gegevens op uit uw SAP BW open hub Table gepartitioneerd door de DTP-aanvraag-id en de pakket-id. Dit geldt wanneer het aantal unieke DTP-aanvraag-ID + pakket-ID groter is dan de waarde van `parallelCopies` . Bij het kopiëren van gegevens naar gegevens opslag op basis van een bestand, is het ook opnieuw opdracht om naar een map te schrijven als meerdere bestanden (Geef alleen de mapnaam op). in dat geval zijn de prestaties beter dan het schrijven naar één bestand.

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromSAPBWOpenHub",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW Open Hub input dataset name>",
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
                "type": "SapOpenHubSource",
                "excludeLastRequest": true
            },
            "sink": {
                "type": "<sink type>"
            },
            "parallelCopies": 4
        }
    }
]
```

## <a name="data-type-mapping-for-sap-bw-open-hub"></a>Toewijzing van gegevens type voor SAP BW open hub

Bij het kopiëren van gegevens uit SAP BW geopende hub, worden de volgende toewijzingen gebruikt van SAP BW gegevens typen om tussenliggende gegevens typen te Azure Data Factory. Zie [schema-en gegevens type toewijzingen](copy-activity-schema-and-type-mapping.md) voor meer informatie over hoe kopieer activiteit het bron schema en het gegevens type aan de Sink koppelt.

| SAP ABAP-type | Data Factory-gegevens type interim |
|:--- |:--- |
| C (teken reeks) | Tekenreeks |
| I (geheel getal) | Int32 |
| F (float) | Dubbel |
| D (datum) | Tekenreeks |
| T (tijd) | Tekenreeks |
| P (door BCD verpakt, valuta, decimaal, hoeveelheid) | Decimaal |
| N (Numc) | Tekenreeks |
| X (binair en onbewerkt) | Tekenreeks |

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.

## <a name="troubleshooting-tips"></a>Tips voor probleemoplossing

**Symptomen:** Als u SAP BW uitvoert in HANA en alleen een subset van gegevens kopieert met behulp van de ADF Copy-activiteit (1.000.000 rijen), is de mogelijke oorzaak dat u de optie ' SAP HANA Execution ' inschakelt in uw DTP, in welk geval ADF alleen de eerste batch met gegevens kan ophalen.

**Oplossing:** Schakel de optie voor het uitvoeren van SAP HANA uit in DTP, Verwerk de gegevens opnieuw en probeer de Kopieer activiteit opnieuw uit te voeren.

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit in azure Data Factory.

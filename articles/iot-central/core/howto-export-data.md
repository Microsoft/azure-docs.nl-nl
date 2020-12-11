---
title: Gegevens exporteren uit Azure IoT Central | Microsoft Docs
description: De nieuwe gegevens export gebruiken om uw IoT-gegevens te exporteren naar Azure en aangepaste Cloud bestemmingen.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 11/05/2020
ms.topic: how-to
ms.service: iot-central
ms.custom: contperf-fy21q1
ms.openlocfilehash: b84f1efd77ca757fd2ceaa8bb5605e3fc78297d0
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97032368"
---
# <a name="export-iot-data-to-cloud-destinations-using-data-export"></a>IoT-gegevens exporteren naar Cloud bestemmingen met behulp van gegevens export

> [!Note]
> In dit artikel worden de functies voor gegevens export in IoT Central beschreven.
>
> - Zie [IOT-gegevens naar Cloud bestemmingen exporteren met behulp van gegevens export (verouderd)](./howto-export-data-legacy.md)voor meer informatie over de verouderde functies voor gegevens export.
> - Zie de onderstaande [vergelijkings tabel](#comparison-of-legacy-data-export-and-data-export) voor meer informatie over de verschillen tussen de functies voor het exporteren van gegevens en verouderde gegevens export.

In dit artikel wordt beschreven hoe u de nieuwe functie voor gegevens export kunt gebruiken in azure IoT Central. Gebruik deze functie om voortdurend gefilterde en verrijkt IoT-gegevens te exporteren uit uw IoT Central-toepassing. Gegevens export pusht wijzigingen in bijna realtime aan andere onderdelen van uw Cloud oplossing voor inzichten, analyses en opslag van warme paden.

U kunt bijvoorbeeld:

- Doorlopend telemetrie-gegevens en eigenschaps wijzigingen in de JSON-indeling in vrijwel realtime exporteren.
- Filter de gegevens stromen om gegevens te exporteren die overeenkomen met aangepaste voor waarden.
- Verrijkt de gegevens stromen met aangepaste waarden en eigenschaps waarden van het apparaat.
- De gegevens verzenden naar bestemmingen zoals Azure Event Hubs, Azure Service Bus, Azure Blob Storage en webhook-eind punten.

> [!Tip]
> Wanneer u het exporteren van gegevens inschakelt, worden er vanaf dat moment alleen gegevens opgehaald. Op dit moment kunnen geen gegevens worden opgehaald voor een tijdstip waarop het exporteren van gegevens is uitgeschakeld. Als u meer historische gegevens wilt behouden, schakelt u de optie gegevens export voor tijdig in.

## <a name="prerequisites"></a>Vereisten

Als u functies voor gegevens export wilt gebruiken, moet u een [v3-toepassing](howto-get-app-info.md)hebben en moet u de machtiging [gegevens exporteren](howto-manage-users-roles.md) hebben.

## <a name="set-up-export-destination"></a>Export bestemming instellen

Het export doel moet bestaan voordat u uw gegevens export kunt configureren. De volgende doel typen zijn momenteel beschikbaar:

- Azure Event Hubs
- Azure Service Bus-wachtrij
- Azure Service Bus-onderwerp
- Azure Blob Storage
- Webhook

### <a name="create-an-event-hubs-destination"></a>Een Event Hubs bestemming maken

Als u geen bestaande Event Hubs naam ruimte hebt om naar te exporteren, voert u de volgende stappen uit:

1. Maak een [nieuwe event hubs naam ruimte in de Azure Portal](https://ms.portal.azure.com/#create/Microsoft.EventHub). Meer informatie vindt u in [Azure Event hubs docs](../../event-hubs/event-hubs-create.md).

1. Maak een Event Hub in uw Event Hubs naam ruimte. Ga naar uw naam ruimte en selecteer **+ Event hub** aan de bovenkant om een event hub-exemplaar te maken.

1. Genereer een sleutel om te gebruiken bij het instellen van uw gegevens export in IoT Central:

    - Selecteer het Event Hub exemplaar dat u hebt gemaakt.
    - Selecteer **instellingen > beleid voor gedeelde toegang**.
    - Maak een nieuwe sleutel of kies een bestaande sleutel die machtigingen voor **verzenden** heeft.
    - Kopieer de primaire of secundaire connection string. U gebruikt deze connection string om een nieuwe bestemming in IoT Central in te stellen.
    - U kunt ook een connection string genereren voor de volledige Event Hubs naam ruimte:
        1. Ga naar de naam ruimte van uw Event Hubs in de Azure Portal.
        2. Onder **instellingen** selecteert u **beleid voor gedeelde toegang**
        3. Maak een nieuwe sleutel of kies een bestaande sleutel die machtigingen voor **verzenden** heeft.
        4. De primaire of secundaire connection string kopiëren
        
### <a name="create-a-service-bus-queue-or-topic-destination"></a>Een Service Bus wachtrij of onderwerp bestemming maken

Als u geen bestaande Service Bus naam ruimte hebt om naar te exporteren, voert u de volgende stappen uit:

1. Maak een [nieuwe service bus naam ruimte in de Azure Portal](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Meer informatie vindt u in [Azure service bus docs](../../service-bus-messaging/service-bus-create-namespace-portal.md).

1. Als u een wachtrij of onderwerp wilt maken om naar te exporteren, gaat u naar uw Service Bus-naam ruimte en selecteert u **+ wachtrij** of **+ onderwerp**.

1. Genereer een sleutel om te gebruiken bij het instellen van uw gegevens export in IoT Central:

    - Selecteer de wachtrij of het onderwerp dat u hebt gemaakt.
    - Selecteer **instellingen/beleid voor gedeelde toegang**.
    - Maak een nieuwe sleutel of kies een bestaande sleutel die machtigingen voor **verzenden** heeft.
    - Kopieer de primaire of secundaire connection string. U gebruikt deze connection string om een nieuwe bestemming in IoT Central in te stellen.
    - U kunt ook een connection string genereren voor de volledige Service Bus naam ruimte:
        1. Ga naar de naam ruimte van uw Service Bus in de Azure Portal.
        2. Onder **instellingen** selecteert u **beleid voor gedeelde toegang**
        3. Maak een nieuwe sleutel of kies een bestaande sleutel die machtigingen voor **verzenden** heeft.
        4. De primaire of secundaire connection string kopiëren

### <a name="create-an-azure-blob-storage-destination"></a>Een Azure Blob Storage-bestemming maken

Als u geen bestaand Azure Storage-account hebt om naar te exporteren, voert u de volgende stappen uit:

1. Maak een [Nieuw opslag account in de Azure Portal](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Meer informatie over het maken van nieuwe [Azure Blob Storage-accounts](../../storage/blobs/storage-quickstart-blobs-portal.md) of [Azure data Lake Storage v2-opslag accounts](../../storage/common/storage-account-create.md). Gegevens export kan alleen gegevens schrijven naar opslag accounts die ondersteuning bieden voor blok-blobs. De volgende lijst bevat de bekende compatibele opslag account typen:

    |Prestatie niveau|Accounttype|
    |-|-|
    |Standard|Algemeen v2|
    |Standard|Algemeen v1|
    |Standard|Blob Storage|
    |Premium|Blob-opslag blok keren|

1. Als u een container in uw opslag account wilt maken, gaat u naar uw opslag account. Selecteer onder **BLOB**-service **Bladeren door blobs**. Selecteer **+ container** aan de bovenkant om een nieuwe container te maken.

1. Genereer een connection string voor uw opslag account door naar **instellingen > toegangs sleutels** te gaan. Kopieer een van de twee verbindings reeksen.

### <a name="create-a-webhook-endpoint"></a>Een webhook-eind punt maken

U kunt gegevens exporteren naar een openbaar beschik bare HTTP-webhook-eind punt. U kunt een test webhook-eind punt maken met behulp van [RequestBin](https://requestbin.net/). RequestBin-beperkings aanvraag wanneer de aanvraag limiet is bereikt:

1. Open [RequestBin](https://requestbin.net/).
2. Maak een nieuwe RequestBin en kopieer de **URL van de opslag locatie**. U gebruikt deze URL bij het testen van uw gegevens export.

## <a name="set-up-data-export"></a>Gegevens export instellen

Nu u een bestemming hebt voor het exporteren van uw gegevens naar, moet u de gegevens export instellen in uw IoT Central-toepassing:

1. Meld u aan bij uw IoT Central-toepassing.

1. Selecteer in het linkerdeel venster **gegevens export**.

    > [!Tip]
    > Als het exporteren van **gegevens** niet in het linkerdeel venster wordt weer gegeven, bent u niet gemachtigd om de gegevens export in uw app te configureren. Neem contact op met een beheerder om het exporteren van gegevens in te stellen.

1. Selecteer **+ nieuwe export**.

1. Voer een weergave naam in voor de nieuwe export en controleer of de gegevens export is **ingeschakeld**.

1. Kies het type gegevens dat u wilt exporteren. De volgende tabel bevat de ondersteunde typen gegevens export:

    | Gegevenstype | Beschrijving | Gegevensindeling |
    | :------------- | :---------- | :----------- |
    |  Telemetrie | Telemetrie-berichten van apparaten in bijna realtime exporteren. Elk geëxporteerd bericht bevat de volledige inhoud van het bericht van het oorspronkelijke apparaat, genormaliseerd.   |  [Indeling voor telemetrie-berichten](#telemetry-format)   |
    | Eigenschaps wijzigingen | Wijzigingen in de eigenschappen van het apparaat en de cloud in bijna realtime exporteren. Wijzigingen in de gerapporteerde waarden worden geëxporteerd voor eigenschappen van alleen-lezen apparaten. Voor eigenschappen voor lezen/schrijven worden zowel gerapporteerde als de gewenste waarden geëxporteerd. | [Bericht indeling voor wijzigen van eigenschap](#property-changes-format) |

<a name="DataExportFilters"></a>
1. Voeg eventueel filters toe om de hoeveelheid geëxporteerde gegevens te verminderen. Er zijn verschillende typen filters beschikbaar voor elk type gegevens export:

    Als u telemetrie wilt filteren, kunt u het volgende doen:

    - **Filter** de geëxporteerde stroom zodanig dat deze alleen telemetrie bevat van apparaten die overeenkomen met de apparaatnaam, de apparaat-id en de filter voorwaarde voor de Apparaatbeheer.
    - Mogelijkheden **filteren** : als u een telemetrie-item in de vervolg keuzelijst **naam** kiest, bevat de geëxporteerde stroom alleen telemetriegegevens die voldoen aan de filter voorwaarde. Als u een apparaat-of Cloud eigenschaps item in de vervolg keuzelijst **naam** kiest, bevat de geëxporteerde stroom alleen telemetrie van apparaten met eigenschappen die overeenkomen met de filter voorwaarde.
    - **Filter voor bericht eigenschappen**: apparaten die gebruikmaken van de apparaat-sdk's kunnen *bericht eigenschappen* of *toepassings eigenschappen* voor elk telemetrie-bericht verzenden. De eigenschappen zijn een verzameling sleutel-waardeparen die het bericht labelen met aangepaste id's. Als u een bericht eigenschaps filter wilt maken, voert u de bericht eigenschap sleutel in die u zoekt en geeft u een voor waarde op. Alleen telemetrie-berichten met eigenschappen die overeenkomen met de opgegeven filter voorwaarde, worden geëxporteerd. De volgende teken reeks vergelijkings operatoren worden ondersteund: is gelijk aan, is niet gelijk aan, bevat, niet bevat, bestaat niet. Meer [informatie over toepassings eigenschappen van IOT hub docs](../../iot-hub/iot-hub-devguide-messages-construct.md).

    Als u de wijzigingen wilt filteren op Eigenschappen, gebruikt u een **functie filter**. Kies een eigenschaps item in de vervolg keuzelijst. De geëxporteerde stroom bevat alleen wijzigingen in de geselecteerde eigenschap die voldoen aan de filter voorwaarde.

<a name="DataExportEnrichmnents"></a>
1. Eventueel verrijkte geëxporteerde berichten met aanvullende meta gegevens van het sleutel/waarde-paar. De volgende verrijkingen zijn beschikbaar voor de gegevens export typen telemetrie en eigenschaps wijzigingen:

    - **Aangepaste teken reeks**: Hiermee voegt u een aangepaste statische teken reeks aan elk bericht toe. Voer een sleutel in en voer een wille keurige teken reeks waarde in.
    - **Eigenschap**: Hiermee wordt de huidige waarde van de eigenschap voor het apparaat of de eigenschap van de cloud aan elk bericht toegevoegd. Voer een wille keurige sleutel in en kies een apparaat-of Cloud eigenschap. Als het geëxporteerde bericht afkomstig is van een apparaat dat niet de opgegeven eigenschap heeft, wordt de verrijking niet door het geëxporteerde bericht opgehaald.

1. Voeg een nieuwe bestemming toe of Voeg een bestemming toe die u al hebt gemaakt. Selecteer de koppeling **een nieuwe maken** en voeg de volgende gegevens toe:

    - **Doel naam**: de weergave naam van de bestemming in IOT Central.
    - **Doel type**: Kies het type bestemming. Als u uw bestemming nog niet hebt ingesteld, raadpleegt u [export bestemming instellen](#set-up-export-destination).
    - Plak voor Azure Event Hubs, Azure Service Bus wachtrij of onderwerp, de connection string voor uw resource en voer indien nodig de naam van het hoofdletter gevoelige Event Hub, de wachtrij of het onderwerp in.
    - Plak voor Azure Blob Storage de connection string voor uw resource en voer zo nodig de hoofdletter gevoelige container naam in.
    - Plak voor webhook de call back-URL voor het webhook-eind punt. U kunt desgewenst webhook-autorisatie (OAuth 2,0 en autorisatie token) configureren en aangepaste headers toevoegen. 
        - Voor OAuth 2,0 wordt alleen de client referentie stroom ondersteund. Wanneer de bestemming wordt opgeslagen, wordt IoT Central met uw OAuth-provider gecommuniceerd om een autorisatie token op te halen. Dit token wordt gekoppeld aan de header Authorization voor elk bericht dat naar deze bestemming wordt verzonden.
        - Voor autorisatie token kunt u een token waarde opgeven die direct wordt gekoppeld aan de header Authorization voor elk bericht dat naar deze bestemming wordt verzonden.
    - Selecteer **Maken**.

1. Selecteer **+ bestemming** en kies een bestemming in de vervolg keuzelijst. U kunt Maxi maal vijf bestemmingen aan één export toevoegen.

1. Wanneer u klaar bent met het instellen van uw export, selecteert u **Opslaan**. Na een paar minuten worden uw gegevens weer gegeven in uw bestemming.

## <a name="export-contents-and-format"></a>Inhoud en indeling exporteren

### <a name="azure-blob-storage-destination"></a>Doel van Azure Blob Storage

Gegevens worden eenmaal per minuut geëxporteerd, waarbij elk bestand met de batch wijzigingen sinds de vorige export is. Geëxporteerde gegevens worden opgeslagen in JSON-indeling. De standaard paden naar de geëxporteerde gegevens in uw opslag account zijn:

- Telemetrie: _{container}/{app-id}/{partition_id}/{yyyy}/{mm}/{dd}/{hh}/{mm}/{filename}_
- Eigenschaps wijzigingen: _{container}/{app-id}/{partition_id}/{yyyy}/{mm}/{dd}/{hh}/{mm}/{filename}_

Als u door de geëxporteerde bestanden in de Azure Portal wilt bladeren, gaat u naar het bestand en selecteert u **BLOB bewerken**.

### <a name="azure-event-hubs-and-azure-service-bus-destinations"></a>Azure-Event Hubs en Azure Service Bus doelen

De gegevens worden bijna in realtime geëxporteerd. De gegevens bevindt zich in de hoofd tekst van het bericht en bevindt zich in JSON-indeling als UTF-8.

De aantekeningen of de verzameling systeem eigenschappen van het bericht bevat `iotcentral-device-id` de `iotcentral-application-id` velden,, `iotcentral-message-source` en, `iotcentral-message-type` die dezelfde waarden hebben als de overeenkomende velden in de hoofd tekst van het bericht.

### <a name="webhook-destination"></a>Webhook-doel

Voor webhooks-bestemmingen worden gegevens ook bijna in realtime geëxporteerd. De gegevens in de hoofd tekst van het bericht hebben dezelfde indeling als voor Event Hubs en Service Bus.

### <a name="telemetry-format"></a>Telemetrie-indeling

Elk geëxporteerd bericht bevat een genormaliseerde vorm van het volledige bericht dat het apparaat in de hoofd tekst van het bericht is verzonden. Het bericht bevindt zich in JSON-indeling en is gecodeerd als UTF-8. De informatie in elk bericht bevat:

- `applicationId`: De ID van de IoT Central-toepassing.
- `messageSource`: De bron voor het bericht- `telemetry` .
- `deviceId`: De ID van het apparaat dat het telemetrie-bericht heeft verzonden.
- `schema`: De naam en versie van het payload-schema.
- `templateId`: De ID van de Device-sjabloon die aan het apparaat is gekoppeld.
- `enrichments`: Eventuele verrijkingen die zijn ingesteld voor de export.
- `messageProperties`: Extra eigenschappen die het apparaat met het bericht heeft verzonden. Deze eigenschappen worden ook wel *toepassings eigenschappen* genoemd. [Meer informatie over IOT hub docs](../../iot-hub/iot-hub-devguide-messages-construct.md).

Voor Event Hubs en Service Bus, IoT Central een nieuw bericht snel exporteren nadat het bericht van een apparaat is ontvangen. In de gebruikers eigenschappen (ook wel toepassings eigenschappen genoemd) van elk bericht, de `iotcentral-device-id` , en `iotcentral-application-id` `iotcentral-message-source` worden automatisch opgenomen.

Voor Blob-opslag worden berichten per minuut in batch verzonden en geëxporteerd.

In het volgende voor beeld ziet u een geëxporteerd telemetrie-bericht:

```json

{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "Activity": "running",
        "BloodPressure": {
            "Diastolic": 7,
            "Systolic": 71
        },
        "BodyTemperature": 98.73447010562934,
        "HeartRate": 88,
        "HeartRateVariability": 17,
        "RespiratoryRate": 13
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```

## <a name="property-changes-format"></a>Opmaak van eigenschaps wijzigingen

Elk bericht of record vertegenwoordigt een wijziging van een apparaat of Cloud eigenschap. Voor apparaateigenschappen worden alleen wijzigingen in de gerapporteerde waarde geëxporteerd als een afzonderlijk bericht. De informatie in het geëxporteerde bericht bevat het volgende:

- `applicationId`: De ID van de IoT Central-toepassing.
- `messageSource`: De bron voor het bericht- `properties` .
- `messageType`: Ofwel `cloudPropertyChange` , `devicePropertyDesiredChange` of `devicePropertyReportedChange` .
- `deviceId`: De ID van het apparaat dat het telemetrie-bericht heeft verzonden.
- `schema`: De naam en versie van het payload-schema.
- `templateId`: De ID van de Device-sjabloon die aan het apparaat is gekoppeld.
- `enrichments`: Eventuele verrijkingen die zijn ingesteld voor de export.

IoT Central exporteert voor Event Hubs en Service Bus nieuwe bericht gegevens naar uw Event Hub-of Service Bus-wachtrij of-onderwerp in bijna realtime. In de gebruikers eigenschappen (ook wel toepassings eigenschappen genoemd) van elk bericht, de `iotcentral-device-id` ,, `iotcentral-application-id` en `iotcentral-message-source` `iotcentral-message-type` worden automatisch opgenomen.

Voor Blob-opslag worden berichten per minuut in batch verzonden en geëxporteerd.

In het volgende voor beeld ziet u het wijzigings bericht voor een geëxporteerde eigenschap dat is ontvangen in Azure Blob Storage.

```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "properties",
    "messageType": "cloudPropertyChange",
    "deviceId": "18a985g1fta",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:37:32.942Z",
    "properties": [{
        "name": "MachineSerialNumber",
        "value": "abc"
    }],
    "enrichments": {
        "userSpecifiedKey" : "sampleValue"
    }
}
```

## <a name="comparison-of-legacy-data-export-and-data-export"></a>Vergelijking van verouderde gegevens export en gegevens export

In de volgende tabel ziet u de verschillen tussen de [verouderde gegevens export](howto-export-data-legacy.md) en de nieuwe functies voor gegevens export:

| Functies  | Verouderde gegevens export | Nieuwe gegevens export |
| :------------- | :---------- | :----------- |
| Beschik bare gegevens typen | Telemetrie, apparaten, apparaatprofielen | Telemetrie, eigenschaps wijzigingen |
| Filteren | Geen | Is afhankelijk van het geëxporteerde gegevens type. Filteren op telemetrie, bericht eigenschappen, eigenschaps waarden |
| Verrijken | Geen | Verrijken met een aangepaste teken reeks of een eigenschaps waarde op het apparaat |
| Bestemmingen | Azure Event Hubs, Azure Service Bus wacht rijen en onderwerpen, Azure Blob Storage | Hetzelfde als voor verouderde gegevens export plus webhooks|
| Ondersteunde toepassings versies | V2, V3 | Alleen v3 |
| Opvallende limieten | 5 uitvoer per app, 1 doel per export | 10 export-doel verbindingen per app |

## <a name="next-steps"></a>Volgende stappen

Nu u weet hoe u de nieuwe gegevens export kunt gebruiken, is een voorgestelde volgende stap informatie [over het gebruik van Analytics in IOT Central](./howto-create-analytics.md)

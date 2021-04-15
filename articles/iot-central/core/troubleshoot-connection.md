---
title: Problemen met apparaatverbindingen met Azure IoT Central | Microsoft Docs
description: Problemen oplossen waarom u geen gegevens van uw apparaten ziet in IoT Central
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/13/2020
ms.topic: troubleshooting
ms.service: iot-central
ms.custom: device-developer, devx-track-azurecli
ms.openlocfilehash: 494608f9dd8fbf986dcda6eeb782a64f6a2ca008
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378564"
---
# <a name="troubleshoot-why-data-from-your-devices-isnt-showing-up-in-azure-iot-central"></a>Probleem oplossen waarbij gegevens van uw apparaten niet worden weergegeven in Azure IoT Central

Dit document helpt apparaatontwikkelaars erachter te komen waarom de gegevens die hun apparaten verzenden naar IoT Central mogelijk niet worden weergegeven in de toepassing.

Er zijn twee belangrijke gebieden om te onderzoeken:

- Problemen met apparaatconnectiviteit
  - Verificatieproblemen, zoals het apparaat, hebben ongeldige referenties
  - Problemen met de netwerkverbinding
  - Het apparaat is niet goedgekeurd of geblokkeerd
- Problemen met de vorm van de nettolading van het apparaat

Deze gids voor probleemoplossing richt zich op verbindingsproblemen met apparaten en problemen met de vorm van de nettolading van het apparaat.

## <a name="device-connectivity-issues"></a>Problemen met apparaatconnectiviteit

In deze sectie kunt u bepalen of uw gegevens IoT Central.

Als u dit nog niet hebt gedaan, installeert u het `az cli` hulpprogramma en de `azure-iot` extensie.

Zie Azure CLI installeren voor meer `az cli` informatie over het installeren [van](/cli/azure/install-azure-cli)de .

Voer [de volgende](/cli/azure/azure-cli-reference-for-IoT#extension-reference-installation) opdracht uit om de extensie te `azure-iot` installeren:

```azurecli
az extension add --name azure-iot
```

> [!NOTE]
> U wordt mogelijk gevraagd om de bibliotheek te installeren wanneer u de `uamqp` eerste keer een extensieopdracht uit te voeren.

Wanneer u de extensie hebt geïnstalleerd, start u het apparaat om te zien of de berichten die worden verzonden, naar de `azure-iot` IoT Central.

Gebruik de volgende opdrachten om u aan te melden bij het abonnement waar u uw IoT Central hebt:

```azurecli
az login
az set account --subscription <your-subscription-id>
```

Gebruik de volgende opdracht om de telemetrie te bewaken die door uw apparaat wordt verzonden:

```azurecli
az iot central diagnostics monitor-events --app-id <app-id> --device-id <device-name>
```

Als het apparaat is verbonden met IoT Central, ziet u uitvoer die er ongeveer als volgt uit ziet:

```output
Monitoring telemetry.
Filtering on device: device-001
{
    "event": {
        "origin": "device-001",
        "module": "",
        "interface": "",
        "component": "",
        "payload": {
            "temp": 65.57910343679293,
            "humid": 36.16224660107426
        }
    }
}
```

Gebruik de volgende preview-opdracht om de updates van eigenschappen te controleren IoT Central apparaat wordt uitgewisseld:

```azurecli
az iot central diagnostics monitor-properties --app-id <app-id> --device-id <device-name>
```

Als het apparaat updates van eigenschappen verzendt, ziet u uitvoer die vergelijkbaar is met de volgende:

```output
Changes in reported properties:
version : 32
{'state': 'true', 'name': {'value': {'value': 'Contoso'}, 'status': 'completed', 'desiredVersion': 7, 'ad': 'completed', 'av': 7, 'ac
': 200}, 'brightness': {'value': {'value': 2}, 'status': 'completed', 'desiredVersion': 7, 'ad': 'completed', 'av': 7, 'ac': 200}, 'p
rocessorArchitecture': 'ARM', 'swVersion': '1.0.0'}
```

Als er gegevens worden weergegeven in uw terminal, worden deze gegevens tot uw IoT Central toepassing.

Als er na enkele minuten geen gegevens worden weergegeven, drukt u op de toets of op het toetsenbord, voor het geval `Enter` `return` de uitvoer vastloopt.

Als er nog steeds geen gegevens worden weergegeven op de terminal, heeft uw apparaat waarschijnlijk problemen met de netwerkverbinding of worden er geen gegevens correct naar de IoT Central.

### <a name="check-the-provisioning-status-of-your-device"></a>De inrichtingsstatus van uw apparaat controleren

Als uw gegevens niet worden weergegeven op de monitor, controleert u de inrichtingsstatus van uw apparaat door de volgende opdracht uit te voeren:

```azurecli
az iot central device registration-info --app-id <app-id> --device-id <device-name>
```

In de volgende uitvoer ziet u een voorbeeld van een apparaat dat geen verbinding kan maken:

```json
{
  "@device_id": "v22upeoqx6",
  "device_registration_info": {
    "device_status": "blocked",
    "display_name": "Environmental Sensor - v22upeoqx6",
    "id": "v22upeoqx6",
    "instance_of": "urn:krhsi_k0u:modelDefinition:w53jukkazs",
    "simulated": false
  },
  "dps_state": {
    "error": "Device is blocked from connecting to IoT Central application. Unblock the device in IoT Central and retry. Learn more:
https://aka.ms/iotcentral-docs-dps-SAS",
    "status": null
  }
}
```

| Inrichtingsstatus van apparaat | Description | Mogelijke beperking |
| - | - | - |
| Ingericht | Er is geen direct herkenbare probleem. | N.v.t. |
| Geregistreerd | Het apparaat is nog niet verbonden met IoT Central. | Controleer uw apparaatlogboeken op verbindingsproblemen. |
| Geblokkeerd | Het apparaat kan geen verbinding maken met IoT Central. | Het apparaat kan geen verbinding maken met de IoT Central toepassing. Deblokk het apparaat in IoT Central en het opnieuw proberen. Zie Apparaten blokkeren voor [meer informatie.](concepts-get-connected.md#device-status-values) |
| Niet-goedgekeurd | Het apparaat is niet goedgekeurd. | Het apparaat is niet goedgekeurd om verbinding te maken met de IoT Central toepassing. Keur het apparaat in IoT Central opnieuw. Zie Apparaten goedkeuren voor [meer informatie](concepts-get-connected.md#device-registration) |
| Niet-verbonden | Het apparaat is niet gekoppeld aan een apparaatsjabloon. | Koppel het apparaat aan een apparaatsjabloon zodat IoT Central de gegevens kan parseren. |

Meer informatie over [apparaatstatuscodes.](concepts-get-connected.md#device-status-values)

### <a name="error-codes"></a>Foutcodes

Als u nog steeds niet kunt vaststellen waarom uw gegevens niet worden weergegeven in , is de volgende stap het zoeken naar foutcodes die door uw `monitor-events` apparaat zijn gerapporteerd.

Start een debugging-sessie op uw apparaat of verzamel logboeken van uw apparaat. Controleer op foutcodes die het apparaat rapporteert.

In de volgende tabellen worden de algemene foutcodes en mogelijke acties weergegeven die kunnen worden beperkt.

Als u problemen ziet met betrekking tot uw verificatiestroom:

| Foutcode | Beschrijving | Mogelijke beperking |
| - | - | - |
| 400 | De body van de aanvraag is ongeldig. Het kan bijvoorbeeld niet worden geparseerd of het object kan niet worden gevalideerd. | Zorg ervoor dat u de juiste aanvraag body als onderdeel van de attestation-stroom verstuurt of gebruik een apparaat-SDK. |
| 401 | Het autorisatie-token kan niet worden gevalideerd. Deze is bijvoorbeeld verlopen of is niet van toepassing op de URI van de aanvraag. Deze foutcode wordt ook geretourneerd naar apparaten als onderdeel van de TPM-attestation-stroom. | Zorg ervoor dat uw apparaat de juiste referenties heeft. |
| 404 | Het Device Provisioning Service-exemplaar of een resource zoals een inschrijving bestaat niet. | [Een ticket indienen bij de klantondersteuning.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) |
| 412 | De `ETag` in de aanvraag komt niet overeen met de van de bestaande `ETag` resource, volgens RFC7232. | [Een ticket indienen bij de klantondersteuning.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) |
| 429 | Bewerkingen worden beperkt door de service. Zie limieten voor specifieke [servicelimieten IoT Hub Device Provisioning Service servicelimieten.](../../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-device-provisioning-service-limits) | Verlaag de berichtfrequentie, splits verantwoordelijkheden over meer apparaten. |
| 500 | Er is een interne fout opgetreden. | [Een ticket indienen bij de klantondersteuning om](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) te zien of ze u verder kunnen helpen. |

### <a name="file-upload-error-codes"></a>Foutcodes voor bestand uploaden

Hier ziet u een lijst met veelvoorkomende foutcodes die u kunt zien wanneer een apparaat probeert een bestand naar de cloud te uploaden. Voordat uw apparaat een bestand kan uploaden, moet u het uploaden van apparaatbestand [in](howto-configure-file-uploads.md) uw toepassing configureren.

| Foutcode | Beschrijving | Mogelijke beperking |
| - | - | - |
| 403006  | U hebt het aantal gelijktijdige bestandsuploadbewerkingen overschreden. Elke apparaatclient is beperkt tot 10 gelijktijdige bestanduploads. | Zorg ervoor dat het apparaat onmiddellijk een melding IoT Central dat de bestandsuploadbewerking is voltooid. Als dat niet werkt, vermindert u de time-out van de aanvraag. |

## <a name="payload-shape-issues"></a>Problemen met de vorm van de nettolading

Wanneer u hebt vastgesteld dat uw apparaat gegevens naar IoT Central, is de volgende stap ervoor te zorgen dat uw apparaat gegevens in een geldige indeling verstuurt.

Er zijn twee hoofdcategorieën met veelvoorkomende problemen die ertoe leiden dat apparaatgegevens niet worden weergegeven in IoT Central:

- Apparaatsjabloon naar apparaatgegevens komen niet overeen:
  - Komt niet overeen in naamgeving, zoals typfouten of problemen met overeenkomende case's.
  - Niet-gemodelleerde eigenschappen waarbij het schema niet is gedefinieerd in de apparaatsjabloon.
  - Schema komt niet overeen, zoals een type dat in de sjabloon is gedefinieerd als `boolean` , maar de gegevens zijn een tekenreeks.
  - Dezelfde telemetrienaam wordt gedefinieerd in meerdere interfaces, maar het apparaat is niet compatibel Plug en Play IoT.
- De gegevensvorm is ongeldig. Zie Nettoladingen [voor telemetrie, eigenschappen en opdrachten voor meer informatie.](concepts-telemetry-properties-commands.md)

Als u wilt detecteren in welke categorieën uw probleem zich voordeed, moet u de meest geschikte opdracht uitvoeren voor uw scenario:

- Gebruik de preview-opdracht om telemetrie te valideren:

    ```azurecli
    az iot central diagnostics validate-messages --app-id <app-id> --device-id <device-name>
    ```

- Als u updates van eigenschappen wilt valideren, gebruikt u de preview-opdracht

    ```azurecli
    az iot central diagnostics validate-properties --app-id <app-id> --device-id <device-name>
    ```

U wordt mogelijk gevraagd om de bibliotheek te installeren wanneer u de `uamqp` eerste keer een opdracht uit te `validate` voeren.

In de volgende uitvoer ziet u voorbeeldfout- en waarschuwingsberichten van de validatieopdracht:

```output
Validating telemetry.
Filtering on device: v22upeoqx6.
Exiting after 300 second(s), or 10 message(s) have been parsed (whichever happens first).
[WARNING] [DeviceId: v22upeoqx6] No encoding found. Expected encoding 'utf-8' to be present in message header.

[WARNING] [DeviceId: v22upeoqx6] Content type '' is not supported. Expected Content type is 'application/json'.

[ERROR] [DeviceId: v22upeoqx6] [TemplateId: urn:krhsi_k0u:modelDefinition:w53jukkazs] Datatype of field 'humid' does not match the da
tatype 'double'. Data '56'. All dates/times/datetimes/durations must be ISO 8601 compliant.
```

Als u liever een GUI gebruikt, gebruikt u de IoT Central **onbewerkte** gegevensweergave om te zien of er iets niet wordt gemodelleerd. In **de weergave** Onbewerkte gegevens wordt niet gedetecteerd of het apparaat een misvormde JSON verstuurt.

:::image type="content" source="media/troubleshoot-connection/raw-data-view.png" alt-text="Schermopname van de weergave Onbewerkte gegevens":::

Wanneer u het probleem hebt gedetecteerd, moet u mogelijk apparaatfirmware bijwerken of een nieuwe apparaatsjabloon maken die eerder niet-gemodelleerde gegevens modelleerde.

Als u ervoor hebt gekozen om een nieuwe sjabloon te maken die de gegevens correct modeleert, migreert u apparaten van uw oude sjabloon naar de nieuwe sjabloon. Zie Apparaten beheren [in uw Azure IoT Central toepassing voor meer informatie.](howto-manage-devices.md)

## <a name="next-steps"></a>Volgende stappen

Als u meer hulp nodig hebt, kunt u contact opnemen met de Azure-experts op de [MSDN Azure-](https://azure.microsoft.com/support/community/)en Stack Overflow forums. U kunt ook een ticket ondersteuning voor Azure [indienen.](https://portal.azure.com/#create/Microsoft.Support)

Zie Ondersteunings- en [helpopties voor Azure IoT voor meer informatie.](../../iot-fundamentals/iot-support-help.md)

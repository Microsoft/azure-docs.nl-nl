---
title: Problemen met een apparaat oplossen met Azure IoT Central | Microsoft Docs
description: Problemen oplossen waarom u geen gegevens van uw apparaten in IoT Central ziet
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/13/2020
ms.topic: troubleshooting
ms.service: iot-central
ms.custom: device-developer, devx-track-azurecli
ms.openlocfilehash: ae40571b958897b5f06c4ae72a9049a585561872
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106064712"
---
# <a name="troubleshoot-why-data-from-your-devices-isnt-showing-up-in-azure-iot-central"></a>Probleem oplossen waarbij gegevens van uw apparaten niet worden weergegeven in Azure IoT Central

Dit document helpt ontwikkel aars van apparaten om erachter te komen waarom de gegevens die hun apparaten verzenden naar IoT Central mogelijk niet worden weer gegeven in de toepassing.

Er zijn twee belang rijke gebieden om te onderzoeken:

- Problemen met connectiviteit van apparaten
  - Verificatie problemen zoals het apparaat heeft ongeldige referenties
  - Problemen met de netwerk verbinding
  - Het apparaat is niet goedgekeurd of geblokkeerd
- Problemen met shape payload van apparaat

Deze probleemoplossings handleiding richt zich op problemen met de connectiviteit van apparaten en de shape payload van een apparaat.

## <a name="device-connectivity-issues"></a>Problemen met connectiviteit van apparaten

In deze sectie kunt u bepalen of uw gegevens IoT Central.

Als u dit nog niet hebt gedaan, installeert u het `az cli` hulp programma en de `azure-iot` uitbrei ding.

`az cli`Zie [de Azure cli installeren](/cli/azure/install-azure-cli)voor meer informatie over het installeren van de.

Als [](/cli/azure/azure-cli-reference-for-IoT#extension-reference-installation) u de `azure-iot` uitbrei ding wilt installeren, voert u de volgende opdracht uit:

```azurecli
az extension add --name azure-iot
```

> [!NOTE]
> U wordt mogelijk gevraagd de bibliotheek te installeren `uamqp` Wanneer u voor het eerst een extensie opdracht uitvoert.

Wanneer u de `azure-iot` uitbrei ding hebt geïnstalleerd, start u uw apparaat om te zien of de berichten die worden verzonden, hun manier om IOT Central.

Gebruik de volgende opdrachten om u aan te melden bij het abonnement waar u uw IoT Central-toepassing hebt:

```azurecli
az login
az set account --subscription <your-subscription-id>
```

Als u de telemetrie die het apparaat verzendt, wilt bewaken, gebruikt u de volgende opdracht:

```azurecli
az iot central diagnostics monitor-events --app-id <app-id> --device-id <device-name>
```

Als het apparaat is verbonden met IoT Central, ziet u uitvoer die lijkt op het volgende:

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

Als u de eigenschaps updates wilt controleren die door het apparaat worden uitgewisseld met IoT Central, gebruikt u de volgende opdracht Preview:

```azurecli
az iot central diagnostics monitor-properties --app-id <app-id> --device-id <device-name>
```

Als het apparaat geslaagde updates van eigenschappen verzendt, ziet u uitvoer die er ongeveer als volgt uitzien:

```output
Changes in reported properties:
version : 32
{'state': 'true', 'name': {'value': {'value': 'Contoso'}, 'status': 'completed', 'desiredVersion': 7, 'ad': 'completed', 'av': 7, 'ac
': 200}, 'brightness': {'value': {'value': 2}, 'status': 'completed', 'desiredVersion': 7, 'ad': 'completed', 'av': 7, 'ac': 200}, 'p
rocessorArchitecture': 'ARM', 'swVersion': '1.0.0'}
```

Als er gegevens worden weer gegeven in uw Terminal, worden de gegevens zo ver mogelijk gemaakt als uw IoT Central-toepassing.

Als er na een paar minuten geen gegevens worden weer gegeven, drukt u `Enter` op de `return` toets of op het toetsen bord, voor het geval de uitvoer is vastgelopen.

Als u nog steeds geen gegevens ziet op uw Terminal, is het waarschijnlijk dat uw apparaat problemen met de netwerk verbinding heeft of dat er geen gegevens naar IoT Central worden verzonden.

### <a name="check-the-provisioning-status-of-your-device"></a>Controleer de inrichtings status van het apparaat

Als uw gegevens niet worden weer gegeven op de monitor, controleert u de inrichtings status van het apparaat door de volgende opdracht uit te voeren:

```azurecli
az iot central device registration-info --app-id <app-id> --device-id <device-name>
```

In de volgende uitvoer ziet u een voor beeld van een apparaat dat geen verbinding kan maken:

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

| Inrichtings status van apparaten | Description | Mogelijke beperking |
| - | - | - |
| Ingericht | Er is geen onmiddellijk herkenbaar probleem. | N.v.t. |
| Geregistreerd | Het apparaat is nog niet verbonden met IoT Central. | Controleer de logboeken van uw apparaten op verbindings problemen. |
| Geblokkeerd | Het apparaat kan geen verbinding maken met IoT Central. | Het apparaat is geblokkeerd om verbinding te maken met de IoT Central-toepassing. Deblokkeren van het apparaat in IoT Central en probeer het opnieuw. Zie [apparaten blok keren](concepts-get-connected.md#device-status-values)voor meer informatie. |
| Goedgekeurde | Het apparaat is niet goedgekeurd. | Het apparaat is niet goedgekeurd om verbinding te maken met de IoT Central-toepassing. Keur het apparaat goed in IoT Central en probeer het opnieuw. Zie [apparaten goed keuren](concepts-get-connected.md#device-registration) voor meer informatie |
| Gekoppelde | Het apparaat is niet gekoppeld aan een sjabloon voor het apparaat. | Koppel het apparaat aan een apparaatprofiel zodat IoT Central weet hoe de gegevens moeten worden geparseerd. |

Meer informatie over [Apparaatstatus](concepts-get-connected.md#device-status-values).

### <a name="error-codes"></a>Foutcodes

Als u nog steeds niet kunt vaststellen waarom uw gegevens niet worden weer gegeven in `monitor-events` , is de volgende stap het zoeken naar fout codes die door het apparaat worden gerapporteerd.

Start een foutopsporingssessie op uw apparaat of Verzamel logboeken van uw apparaat. Controleer op de fout codes die het apparaat rapporteert.

De volgende tabellen bevatten de algemene fout codes en mogelijke acties om te beperken.

Als u problemen met uw verificatie stroom ziet:

| Foutcode | Beschrijving | Mogelijke beperking |
| - | - | - |
| 400 | De hoofd tekst van de aanvraag is ongeldig. Het kan bijvoorbeeld niet worden geparseerd of het object kan niet worden gevalideerd. | Zorg ervoor dat u de juiste aanvraag tekst verzendt als onderdeel van de Attestation-stroom of gebruik een apparaat-SDK. |
| 401 | Het autorisatie token kan niet worden gevalideerd. Het is bijvoorbeeld verlopen of is niet van toepassing op de URI van de aanvraag. Deze fout code wordt ook geretourneerd naar apparaten als onderdeel van de TPM-attest stroom. | Zorg ervoor dat uw apparaat de juiste referenties heeft. |
| 404 | De Device Provisioning Service-instantie of een bron, zoals een registratie, bestaat niet. | [Een ticket indienen bij de klant ondersteuning](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). |
| 412 | De `ETag` in de aanvraag komt niet overeen met de `ETag` bestaande resource, conform RFC7232. | [Een ticket indienen bij de klant ondersteuning](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). |
| 429 | Bewerkingen worden beperkt door de service. Zie [IOT hub Device Provisioning Service limieten](../../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-device-provisioning-service-limits)voor specifieke service limieten. | Verminder de bericht frequentie, splits de verantwoordelijkheden op meerdere apparaten. |
| 500 | Er is een interne fout opgetreden. | Neem [een ticket met klant ondersteuning](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) om te zien of ze u verder kunnen helpen. |

## <a name="payload-shape-issues"></a>Problemen met shape Payload

Wanneer u hebt vastgesteld dat uw apparaat gegevens naar IoT Central verzendt, is de volgende stap het controleren of het apparaat gegevens in een geldige indeling verzendt.

Er zijn twee hoofd categorieën met veelvoorkomende problemen die ervoor zorgen dat apparaatgegevens niet worden weer gegeven in IoT Central:

- Device-sjabloon op apparaatgegevens komt niet overeen:
  - De naam komt niet overeen met de naamgeving, zoals type fouten of problemen met de hoofdletter aanpassing.
  - Niet-gemodelleerde eigenschappen waarbij het schema niet is gedefinieerd in de sjabloon voor het apparaat.
  - Schema komt niet overeen met het type dat in de sjabloon `boolean` is gedefinieerd als, maar de gegevens zijn een teken reeks.
  - Dezelfde telemetrie-naam wordt gedefinieerd in meerdere interfaces, maar het apparaat is niet compatibel met IoT Plug en Play.
- De gegevens vorm is ongeldig JSON. Zie [telemetrie, Property en Command payloads](concepts-telemetry-properties-commands.md)voor meer informatie.

Als u wilt detecteren in welke categorieën uw probleem zich bevindt, voert u de meest geschikte opdracht uit voor uw scenario:

- Als u telemetrie wilt valideren, gebruikt u de opdracht Preview:

    ```azurecli
    az iot central diagnostics validate-messages --app-id <app-id> --device-id <device-name>
    ```

- Als u de updates van eigenschappen wilt valideren, gebruikt u de opdracht preview

    ```azurecli
    az iot central diagnostics validate-properties --app-id <app-id> --device-id <device-name>
    ```

`uamqp`De eerste keer dat u een opdracht uitvoert, wordt u mogelijk gevraagd om de bibliotheek te installeren `validate` .

In de volgende uitvoer ziet u een voor beeld van fout-en waarschuwings berichten van de opdracht validate:

```output
Validating telemetry.
Filtering on device: v22upeoqx6.
Exiting after 300 second(s), or 10 message(s) have been parsed (whichever happens first).
[WARNING] [DeviceId: v22upeoqx6] No encoding found. Expected encoding 'utf-8' to be present in message header.

[WARNING] [DeviceId: v22upeoqx6] Content type '' is not supported. Expected Content type is 'application/json'.

[ERROR] [DeviceId: v22upeoqx6] [TemplateId: urn:krhsi_k0u:modelDefinition:w53jukkazs] Datatype of field 'humid' does not match the da
tatype 'double'. Data '56'. All dates/times/datetimes/durations must be ISO 8601 compliant.
```

Als u liever een GUI gebruikt, gebruikt u de IoT Central **onbewerkte gegevens** weergave om te zien of er iets niet wordt gemodelleerd. De **onbewerkte gegevens** weergave detecteert niet of het apparaat een onjuist gemanipuleerde JSON verzendt.

:::image type="content" source="media/troubleshoot-connection/raw-data-view.png" alt-text="Scherm afbeelding van onbewerkte gegevens weergave":::

Wanneer u het probleem hebt gedetecteerd, moet u mogelijk de firmware van het apparaat bijwerken of een nieuwe apparaatprofiel maken waarmee eerder niet-gemodelleerde gegevens worden gemodelleerd.

Als u ervoor hebt gekozen om een nieuwe sjabloon te maken waarin de gegevens op de juiste manier worden gemodelleerd, migreert u apparaten van uw oude sjabloon naar de nieuwe sjabloon. Zie [apparaten beheren in uw Azure IOT Central-toepassing](howto-manage-devices.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Als u meer hulp nodig hebt, kunt u contact opnemen met de Azure-experts op [MSDN Azure en stack overflow forums](https://azure.microsoft.com/support/community/). U kunt ook een [Azure-ondersteunings ticket](https://portal.azure.com/#create/Microsoft.Support)indienen.

Zie [Azure IOT-ondersteuning en Help-opties](../../iot-fundamentals/iot-support-help.md)voor meer informatie.

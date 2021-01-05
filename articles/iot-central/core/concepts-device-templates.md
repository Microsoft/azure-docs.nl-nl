---
title: Wat zijn Device-sjablonen in azure IoT Central | Microsoft Docs
description: Met Azure IoT Central Device-sjablonen kunt u het gedrag opgeven van de apparaten die zijn verbonden met uw toepassing. Een sjabloon voor een apparaat geeft de telemetrie, eigenschappen en opdrachten op die het apparaat moet implementeren. Een sjabloon voor een apparaat definieert ook de gebruikers interface voor het apparaat in IoT Central zoals de formulieren en dash boards die een operator gebruikt.
author: dominicbetts
ms.author: dobett
ms.date: 12/19/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 04c2330ffee396f5fc30b85640e992df77c08263
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/28/2020
ms.locfileid: "97795425"
---
# <a name="what-are-device-templates"></a>Wat zijn apparaatsjablonen?

_Dit artikel is van toepassing op ontwikkel aars van apparaten en oplossingen bouwers._

Een Device-sjabloon in azure IoT Central is een blauw druk die de kenmerken en het gedrag definieert van een type apparaat dat verbinding maakt met uw toepassing. Zo definieert de sjabloon voor het apparaat de telemetrie die een apparaat verzendt, zodat IoT Central visualisaties kunt maken die gebruikmaken van de juiste eenheden en gegevens typen.

Met een oplossings functie voor oplossingen worden apparaatprofielen toegevoegd aan een IoT Central-toepassing. Een ontwikkelaar van het apparaat schrijft de apparaatcode die het gedrag implementeert dat is gedefinieerd in de sjabloon voor het apparaat.

Een sjabloon voor een apparaat bestaat uit de volgende secties:

- _Een model voor apparaten_. In dit deel van de sjabloon voor het apparaat wordt gedefinieerd hoe het apparaat samenwerkt met uw toepassing. Een ontwikkelaar van het apparaat implementeert het gedrag dat in het model is gedefinieerd.
    - _Standaard onderdeel_. Elk model van het apparaat heeft een standaard onderdeel. De interface van het standaard onderdeel beschrijft mogelijkheden die specifiek zijn voor het model van het apparaat.
    - _Onderdelen_. Een model kan naast het standaard onderdeel ook onderdelen bevatten om de mogelijkheden van het apparaat te beschrijven. Elk onderdeel heeft een interface die de mogelijkheden van het onderdeel beschrijft. Onderdeel interfaces kunnen opnieuw worden gebruikt in andere apparaat modellen. Voor beelden van verschillende modellen voor telefoon apparaten kunnen dezelfde camera-interface gebruiken.
    - _Overgenomen interfaces_. Een apparaatprofiel bevat een of meer interfaces waarmee de mogelijkheden van het standaard onderdeel worden uitgebreid.
- _Eigenschappen_ van de Cloud. In dit deel van de sjabloon kunt u de oplossings ontwikkelaar de meta gegevens van het apparaat opgeven die moeten worden opgeslagen. Cloud eigenschappen worden nooit gesynchroniseerd met apparaten en bestaan alleen in de toepassing. Cloud eigenschappen hebben geen invloed op de code die een ontwikkelaar van het apparaat schrijft om het model te implementeren.
- _Aanpassingen_. In dit deel van de sjabloon voor het apparaat kan de oplossings ontwikkelaar sommige definities in het model van het apparaat overschrijven. Aanpassingen zijn handig als de oplossings ontwikkelaar wil verfijnen hoe de toepassing een waarde verwerkt, zoals het wijzigen van de weergave naam voor een eigenschap of de kleur die wordt gebruikt om een telemetrie-waarde weer te geven. Aanpassingen hebben geen invloed op de code die een ontwikkelaar van het apparaat schrijft om het model te implementeren.
- _Weer gaven_. In dit deel van de sjabloon kunt u met de oplossings ontwikkelaar visualisaties definiëren om gegevens van het apparaat weer te geven en formulieren voor het beheren en controleren van een apparaat. De weer gaven gebruiken het model, de eigenschappen van de Cloud en aanpassingen. Weer gaven hebben geen invloed op de code die een ontwikkelaar van het apparaat schrijft om het model te implementeren.

## <a name="device-models"></a>Apparaatmodellen

Een apparaatprofiel definieert hoe een apparaat samenwerkt met uw IoT Central-toepassing. De ontwikkelaar van het apparaat moet ervoor zorgen dat het apparaat het gedrag implementeert dat is gedefinieerd in het model apparaat, zodat IoT Central het apparaat kunt bewaken en beheren. Een apparaatprofiel bestaat uit een of meer _interfaces_, en elke interface kan een verzameling _telemetrie_ -typen, _Apparaateigenschappen_ en _opdrachten_ definiëren. Een oplossings ontwikkelaar kan een JSON-bestand importeren waarmee het model van het apparaat in een apparaatprofiel wordt gedefinieerd, of u kunt de Web-UI in IoT Central gebruiken om een model te maken of te bewerken. Wijzigingen in een model dat is gemaakt met behulp van de Web-UI, hebben een [versie nummer](./howto-version-device-template.md)van de sjabloon.

Een oplossings ontwikkelaar kan ook een JSON-bestand met het model van het apparaat exporteren. Een ontwikkelaar van een apparaat kan dit JSON-document gebruiken om te begrijpen hoe het apparaat moet communiceren met de IoT Central-toepassing.

Het JSON-bestand dat het model van het apparaat definieert, maakt gebruik van de [Digital-taal versie 2 (DTDL) v2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md). IoT Central verwacht dat het JSON-bestand het apparaatprofiel bevat met de interfaces die in line zijn gedefinieerd, in plaats van afzonderlijke bestanden.

Een typisch IoT-apparaat bestaat uit:

- Aangepaste onderdelen, die de dingen zijn die uw apparaat uniek maken.
- Standaard onderdelen, die voor alle apparaten gebruikelijk zijn.

Deze onderdelen worden _interfaces_ genoemd in een model voor apparaten. Interfaces definiëren de details van elk onderdeel dat door uw apparaat wordt geïmplementeerd. Interfaces kunnen worden gebruikt in verschillende modellen. In de DTDL verwijst een onderdeel naar een interface die is gedefinieerd in een afzonderlijk DTDL-bestand.

In het volgende voor beeld ziet u het overzicht van het model van het apparaat voor een temperatuur controller apparaat. Het standaard onderdeel bevat definities voor `workingSet` , `serialNumber` en `reboot` . Het model van het apparaat omvat ook `thermostat` de `deviceInformation` interfaces en:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "description": "Device with two thermostats and remote reboot.",
  "contents": [
    {
      "@type": [
        "Telemetry", "DataSize"
      ],
      "name": "workingSet",
      "displayName": "Working Set",
      "description": "Current working set of the device memory in KiB.",
      "schema": "double",
      "unit" : "kibibyte"
    },
    {
      "@type": "Property",
      "name": "serialNumber",
      "displayName": "Serial Number",
      "description": "Serial number of the device.",
      "schema": "string"
    },
    {
      "@type": "Command",
      "name": "reboot",
      "displayName": "Reboot",
      "description": "Reboots the device after waiting the number of seconds specified.",
      "request": {
        "name": "delay",
        "displayName": "Delay",
        "description": "Number of seconds to wait before rebooting the device.",
        "schema": "integer"
      }
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat",
      "displayName": "Thermostat",
      "description": "Thermostat One."
    },
    {
      "@type": "Component",
      "schema": "dtmi:azure:DeviceManagement:DeviceInformation;1",
      "name": "deviceInformation",
      "displayName": "Device Information interface",
      "description": "Optional interface with basic device hardware information."
    }
  ]
}
```

Een interface bevat enkele vereiste velden:

- `@id`: een unieke ID in de vorm van een eenvoudige, uniforme resource naam.
- `@type`: declareert dat dit object een interface is.
- `@context`: Hiermee geeft u de DTDL-versie op die voor de interface wordt gebruikt.
- `contents`: hier worden de eigenschappen, telemetrie en opdrachten vermeld die het apparaat vormen. De mogelijkheden kunnen in meerdere interfaces worden gedefinieerd.

Er zijn enkele optionele velden die u kunt gebruiken om meer informatie toe te voegen aan het mogelijkheidsprofiel, zoals weergave naam en beschrijving.

Elk item in de lijst met interfaces in de sectie implements heeft een:

- `name`: de programmeer naam van de interface.
- `schema`: de interface die het functionaliteits model implementeert.

## <a name="interfaces"></a>Interfaces

Met de DTDL kunt u de mogelijkheden van uw apparaat beschrijven. Gerelateerde mogelijkheden zijn gegroepeerd in interfaces. Interfaces beschrijven de eigenschappen, telemetrie en opdrachten die een deel van uw apparaat implementeert:

- `Properties`. Eigenschappen zijn gegevens velden die de status van uw apparaat vertegenwoordigen. Gebruik eigenschappen om de duurzame status van het apparaat aan te geven, zoals de status aan van een pomp van een koel vloeistof. Eigenschappen kunnen ook basis eigenschappen van apparaten vertegenwoordigen, zoals de firmware versie van het apparaat. U kunt eigenschappen declareren als alleen-lezen of beschrijfbaar. Alleen apparaten kunnen de waarde van een alleen-lezen eigenschap bijwerken. Een operator kan de waarde van een Beschrijf bare eigenschap voor verzen ding naar een apparaat instellen.
- `Telemetry`. Telemetrie-velden vertegenwoordigen metingen van Sens oren. Wanneer uw apparaat een sensor meting afneemt, moet er een telemetrie-gebeurtenis worden verzonden die de sensor gegevens bevat.
- `Commands`. Opdrachten vertegenwoordigen methoden die gebruikers van uw apparaat op het apparaat kunnen uitvoeren. Bijvoorbeeld een reset opdracht of een opdracht om een ventilator in of uit te scha kelen.

In het volgende voor beeld wordt de Thermo staat-interface definitie weer gegeven:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName" : "Temperature",
      "description" : "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "targetTemperature",
      "schema": "double",
      "displayName": "Target Temperature",
      "description": "Allows to remotely specify the desired target temperature.",
      "unit" : "degreeCelsius",
      "writable": true
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "maxTempSinceLastReboot",
      "schema": "double",
      "unit" : "degreeCelsius",
      "displayName": "Max temperature since last reboot.",
      "description": "Returns the max temperature since last device reboot."
    },
    {
      "@type": "Command",
      "name": "getMaxMinReport",
      "displayName": "Get Max-Min report.",
      "description": "This command returns the max, min and average temperature from the specified time to the current time.",
      "request": {
        "name": "since",
        "displayName": "Since",
        "description": "Period to return the max-min report.",
        "schema": "dateTime"
      },
      "response": {
        "name" : "tempReport",
        "displayName": "Temperature Report",
        "schema": {
          "@type": "Object",
          "fields": [
            {
              "name": "maxTemp",
              "displayName": "Max temperature",
              "schema": "double"
            },
            {
              "name": "minTemp",
              "displayName": "Min temperature",
              "schema": "double"
            },
            {
              "name" : "avgTemp",
              "displayName": "Average Temperature",
              "schema": "double"
            },
            {
              "name" : "startTime",
              "displayName": "Start Time",
              "schema": "dateTime"
            },
            {
              "name" : "endTime",
              "displayName": "End Time",
              "schema": "dateTime"
            }
          ]
        }
      }
    }
  ]
}
```

In dit voor beeld worden twee eigenschappen (één alleen-lezen en één beschrijfbaar), een type telemetrie en een opdracht weer gegeven. Een minimale veld Beschrijving heeft een:

- `@type` het type mogelijkheid opgeven: `Telemetry` , `Property` of `Command` .  In sommige gevallen bevat het type een semantisch type om IoT Central in te scha kelen om een aantal veronderstellingen te maken over het afhandelen van de waarde.
- `name` voor de telemetrische waarde.
- `schema` om het gegevens type voor de telemetrie of de eigenschap op te geven. Deze waarde kan een primitief type zijn, zoals double, integer, Boolean of string. Complexe object typen en Maps worden ook ondersteund.

Met optionele velden, zoals weergave naam en-beschrijving, kunt u meer details toevoegen aan de interface en de mogelijkheden.

## <a name="properties"></a>Eigenschappen

Eigenschappen zijn standaard alleen-lezen. Alleen-lezen eigenschappen betekenen dat het apparaat de waarde van de eigenschapwaarde bijwerkt naar uw IoT Central-toepassing. Uw IoT Central toepassing kan de waarde van een alleen-lezen eigenschap niet instellen.

U kunt een eigenschap ook markeren als beschrijfbaar op een interface. Een apparaat kan een update ontvangen van een Beschrijf bare eigenschap van uw IoT Central toepassing, evenals het rapporteren van eigenschaps waarden die worden bijgewerkt naar uw toepassing.

Apparaten hoeven geen verbinding te zijn om eigenschaps waarden in te stellen. De bijgewerkte waarden worden overgebracht wanneer het apparaat de volgende keer verbinding maakt met de toepassing. Dit gedrag is van toepassing op zowel alleen-lezen als schrijf bare eigenschappen.

Gebruik eigenschappen niet voor het verzenden van telemetrie van uw apparaat. Een ReadOnly-eigenschap zoals bijvoorbeeld `temperatureSetting=80` moet betekenen dat de temperatuur van het apparaat is ingesteld op 80 en dat het apparaat probeert te krijgen, of op deze Tempe ratuur blijft.

Voor Beschrijf bare eigenschappen retourneert de apparaatnaam een gewenste status code, versie en beschrijving om aan te geven of de toepassing de eigenschaps waarde heeft ontvangen en toegepast.

## <a name="telemetry"></a>Telemetrie

Met IoT Central kunt u telemetrie in dash boards en grafieken weer geven en regels gebruiken om acties te activeren wanneer de drempel waarden zijn bereikt. IoT Central gebruikt de gegevens in het model van het apparaat, zoals gegevens typen, eenheden en weergave namen, om te bepalen hoe telemetrische waarden moeten worden weer gegeven.

U kunt de functie gegevens export van IoT Central gebruiken om telemetrie te streamen naar andere bestemmingen, zoals opslag of Event Hubs.

## <a name="commands"></a>Opdrachten

Een opdracht moet standaard binnen 30 seconden worden uitgevoerd en het apparaat moet zijn verbonden als de opdracht binnenkomt. Als het apparaat op tijd reageert of als het apparaat niet is verbonden, mislukt de opdracht.

Opdrachten kunnen aanvraag parameters hebben en een antwoord retour neren.

### <a name="offline-commands"></a>Offline opdrachten

U kunt wachtrij opdrachten kiezen als een apparaat momenteel offline is door de optie **wachtrij als offline** in te scha kelen voor een opdracht in de sjabloon voor het apparaat.

Offline opdrachten zijn eenrichtings meldingen naar het apparaat vanuit uw oplossing. Offline opdrachten kunnen aanvraag parameters hebben, maar retour neren geen antwoord.

> [!NOTE]
> Deze optie is alleen beschikbaar in de gebruikers interface van IoT Central web. Deze instelling is niet opgenomen als u een model of interface uit de sjabloon voor het apparaat exporteert.

## <a name="cloud-properties"></a>Cloudeigenschappen

Cloud eigenschappen maken deel uit van de sjabloon van het apparaat, maar maken geen deel uit van het model van het apparaat. Met Cloud eigenschappen kunnen de oplossings ontwikkelaar de meta gegevens van een apparaat opgeven om op te slaan in de IoT Central-toepassing. Cloud eigenschappen hebben geen invloed op de code die een ontwikkelaar van het apparaat schrijft om het model te implementeren.

Een oplossings ontwikkelaar kan Cloud eigenschappen toevoegen aan dash boards en weer gaven naast apparaateigenschappen om een operator in staat te stellen de apparaten te beheren die zijn verbonden met de toepassing. Een oplossings ontwikkelaar kan ook Cloud eigenschappen gebruiken als onderdeel van een regel definitie om een drempel waarde te maken die kan worden bewerkt door een operator.

## <a name="customizations"></a>Aanpassingen

Aanpassingen maken deel uit van de sjabloon voor het apparaat, maar maken geen deel uit van het model van het apparaat. Met aanpassingen kunt u de oplossings ontwikkelaar een aantal definities in het model van het apparaat verbeteren of negeren. Een oplossings ontwikkelaar kan bijvoorbeeld de weergave naam voor een type telemetrie of een eigenschap wijzigen. Ontwikkel aars van oplossingen kunnen ook aanpassingen gebruiken om validatie toe te voegen, zoals een minimum-of maximum lengte voor een eigenschap van een teken reeks apparaat.

Aanpassingen kunnen van invloed zijn op de code die een ontwikkelaar van het apparaat schrijft om het model te implementeren. Een aanpassing kan bijvoorbeeld de minimum-en maximum lengte van teken reeksen of minimale en maximale numerieke waarden voor telemetrie instellen.

## <a name="views"></a>Weergaven

Een oplossings ontwikkelaar maakt weer gaven waarmee Opera tors verbonden apparaten kunnen controleren en beheren. Weer gaven maken deel uit van de sjabloon van het apparaat, zodat een weer gave is gekoppeld aan een specifiek apparaattype. Een weer gave kan het volgende omvatten:

- Grafieken voor het uitzetten van telemetrie.
- Tegels om eigenschappen van alleen-lezen apparaten weer te geven.
- Tegels zodat de operator Beschrijf bare apparaateigenschappen kan bewerken.
- Tegels om de operator Cloud eigenschappen te laten bewerken.
- Tegels waarmee de operator opdrachten kan aanroepen, inclusief opdrachten die een Payload verwachten.
- Tegels voor het weer geven van labels, afbeeldingen of tekst verlaging.

De telemetrie, eigenschappen en opdrachten die u aan een weer gave kunt toevoegen, worden bepaald door het model, de eigenschappen van de Cloud en aanpassingen in de sjabloon voor het apparaat.

## <a name="next-steps"></a>Volgende stappen

Als ontwikkel aars van apparaten, nu dat u hebt geleerd over sjablonen voor apparaten, kunt u de volgende stappen uitvoeren om de [telemetrie-, Property-en Command-payloads](./concepts-telemetry-properties-commands.md) te lezen voor meer informatie over de gegevens die een apparaat moet uitwisselen met IOT Central.

Als ontwikkelaar van oplossingen is een voorgestelde volgende stap het lezen [van een nieuw IOT-apparaattype in uw Azure IOT Central-toepassing](./howto-set-up-template.md) . meer informatie over het maken van een sjabloon voor een apparaat.

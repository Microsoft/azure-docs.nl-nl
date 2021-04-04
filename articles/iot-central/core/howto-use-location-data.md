---
title: Locatie gegevens gebruiken in een Azure IoT Central-oplossing
description: Meer informatie over het gebruik van locatie gegevens die zijn verzonden vanaf een apparaat dat is verbonden met uw IoT Central-toepassing. Plaats de locatie gegevens op een kaart of maak geoomheinings regels.
author: dominicbetts
ms.author: dobett
ms.date: 01/08/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: c909fd1438488e3625f3674dd26f959cf6fad79f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98128006"
---
# <a name="use-location-data-in-an-azure-iot-central-solution"></a>Locatie gegevens gebruiken in een Azure IoT Central-oplossing

Dit artikel laat u zien hoe u locatie gegevens kunt gebruiken in een IoT Central-toepassing. Een apparaat dat is verbonden met IoT Central kan locatie gegevens verzenden als een telemetrie-stroom of een apparaat-eigenschap gebruiken om locatie gegevens te rapporteren.

Een oplossings bouwer kan de locatie gegevens gebruiken voor het volgende:

* De gerapporteerde locatie op een kaart uitzetten.
* De geschiedenis van de telemetrie-locatie voor een kaart tekenen.
* Maak geoomheinings regels om een operator op de hoogte te stellen wanneer een apparaat een bepaald gebied binnengaat of verlaat.

## <a name="add-location-capabilities-to-a-device-template"></a>Locatie mogelijkheden toevoegen aan een sjabloon voor een apparaat

In de volgende scherm afbeelding ziet u een sjabloon met voor beelden van de eigenschap apparaat en telemetrie die gebruikmaken van locatie gegevens. De definities gebruiken het semantische **locatie** type en het schema type **geolocatie** :

:::image type="content" source="media/howto-use-location-data/location-device-template.png" alt-text="Scherm opname van de definitie van de locatie-eigenschap in de sjabloon voor het apparaat":::

Ter referentie moeten de [DTDL-definities (Digital Apparaatdubbels Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) voor deze mogelijkheden eruitzien als in het volgende code fragment:

```json
{
  "@type": [
    "Property",
    "Location"
  ],
  "displayName": {
    "en": "DeviceLocation"
  },
  "name": "DeviceLocation",
  "schema": "geopoint",
  "writable": false
},
{
  "@type": [
    "Telemetry",
    "Location"
  ],
  "displayName": {
    "en": "Tracking"
  },
  "name": "Tracking",
  "schema": "geopoint"
}
```

> [!NOTE]
> Het type **geopunt** schema maakt geen deel uit van de [DTDL-specificatie](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md). IoT Central ondersteunt momenteel het type van het **geopunt** schema en het semantische **locatie** type voor achterwaartse compatibiliteit.

## <a name="send-location-data-from-a-device"></a>Locatie gegevens van een apparaat verzenden

Wanneer een apparaat gegevens verzendt voor de eigenschap **DeviceLocation** die wordt weer gegeven in de vorige sectie, ziet de payload eruit als in het volgende JSON-fragment:

```json
{
  "DeviceLocation": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

Wanneer een apparaat gegevens verzendt voor het **traceren** van telemetrie die in de vorige sectie zijn weer gegeven, ziet de payload eruit als in het volgende JSON-fragment:

```json
{
  "Tracking": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

## <a name="display-device-location"></a>Locatie van apparaat weer geven

U kunt locatie gegevens weer geven op meerdere locaties in uw IoT Central-toepassing. Bijvoorbeeld voor weer gaven die zijn gekoppeld aan een afzonderlijk apparaat of op Dash boards.

Wanneer u een weer gave voor een apparaat maakt, kunt u ervoor kiezen om de locatie op een kaart uit te zetten of de afzonderlijke waarden weer te geven:

:::image type="content" source="media/howto-use-location-data/location-views.png" alt-text="Scherm afbeelding met voorbeeld weergave met locatie gegevens":::

U kunt kaart tegels toevoegen aan een dash board om de locatie van een of meer apparaten te tekenen. Wanneer u een kaart tegel toevoegt om de locatie-telemetrie weer te geven, kunt u de locatie in een bepaalde periode uitzetten. In de volgende scherm afbeelding ziet u de locatie die wordt gerapporteerd door een gesimuleerd apparaat in de laatste 30 minuten:

:::image type="content" source="media/howto-use-location-data/location-dashboard.png" alt-text="Scherm afbeelding met voor beeld van dash board met locatie gegevens":::

## <a name="create-a-geofencing-rule"></a>Een geoomheinings regel maken

U kunt de locatie-telemetrie gebruiken om een geoomheinings regel te maken die een waarschuwing genereert wanneer een apparaat in of uit een rechthoekig gebied wordt verplaatst. De volgende scherm afbeelding toont een regel die vier voor waarden gebruikt om een rechthoekig gebied te definiëren met behulp van de waarden voor breedte graad en lengte graad. De regel genereert een e-mail wanneer het apparaat wordt verplaatst naar het rechthoekige gebied:

:::image type="content" source="media/howto-use-location-data/geofence-rule.png" alt-text="Scherm opname van de definitie van een geoomheinings regel":::

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u eigenschappen in uw Azure IoT Central-toepassing kunt gebruiken, raadpleegt u:

* [Nettoladingen](concepts-telemetry-properties-commands.md)
* [Een clienttoepassing maken en verbinden met uw Azure IoT Central-toepassing](tutorial-connect-device.md)
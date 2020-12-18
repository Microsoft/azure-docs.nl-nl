---
title: Verkeers gegevens weer geven op Android-kaarten | Microsoft Azure kaarten
description: In dit artikel leert u hoe u verkeers gegevens op een kaart kunt weer geven met behulp van de Microsoft Azure Maps Android SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 12/04/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 113f39ac2976b870c9e07851cdd0919e2578940f
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680463"
---
# <a name="show-traffic-data-on-the-map-android-sdk"></a>Verkeers gegevens weer geven op de kaart (Android SDK)

Gegevens over stroom gegevens en incidenten zijn de twee typen verkeers gegevens die op de kaart kunnen worden weer gegeven. In deze hand leiding wordt beschreven hoe u beide typen verkeers gegevens kunt weer geven. Gegevens over incidenten bestaan uit punt-en lijn gegevens voor dingen, zoals constructies, wegsluitingen en ongel ukken. In stroom gegevens worden statistieken over de stroom van verkeer onderweg weer gegeven.

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u de stappen in de [Snelstartgids uitvoert: een Android-app](quick-android-map.md) -document maken. Code blokken in dit artikel kunnen worden toegevoegd aan de gebeurtenis-handler voor Maps `onReady` .

## <a name="show-traffic-on-the-map"></a>Verkeer weergeven op de kaart

Er zijn twee soorten verkeers gegevens beschikbaar in Azure Maps:

- Incident gegevens: bestaat uit gegevens op basis van een punt en lijn voor dingen, zoals bouw, wegsluitingen en ongel ukken.
- Stroom gegevens: voorziet in metrische informatie over de stroom van verkeer op de wegen. De verkeers stroom gegevens worden vaak gebruikt om de wegen te kleuren. De kleuren zijn gebaseerd op de hoeveelheid verkeer die de stroom vertraagt, ten opzichte van de snelheids limiet of een andere metriek. Er zijn vier waarden die kunnen worden door gegeven aan de verkeers `flow` optie van de kaart.

    |Stroom waarde | Beschrijving|
    | :-- | :-- |
    | Verkeers stroom. NONE | Verkeers gegevens worden niet weer gegeven op de kaart |
    | Verkeers stroom. RELATIVE | Geeft verkeers gegevens weer die relatief zijn ten opzichte van de vrije stroom snelheid van de weg |
    | TrafficFlow.RELATIVE_DELAY | Hiermee worden gebieden weer gegeven die langzamer zijn dan de gemiddelde verwachte vertraging |
    | Verkeers stroom. ABSOLUTE | Toont de absolute snelheid van alle Voer tuigen op de weg |

De volgende code laat zien hoe verkeers gegevens op de kaart worden weer gegeven.

```java
//Show traffic on the map using the traffic options.
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
);
```

In de volgende scherm afbeelding ziet u de bovenstaande code rending in realtime verkeer op de kaart.

![Toewijzing met gegevens over realtime verkeer](media/how-to-show-traffic-android/android-show-traffic.png)

## <a name="get-traffic-incident-details"></a>Details van verkeers incident ophalen

Meer informatie over een verkeers incident vindt u in de eigenschappen van de functie die wordt gebruikt om het incident weer te geven op de kaart. Verkeers incidenten worden toegevoegd aan de kaart met behulp van de tegel service Azure Maps traffic incident vector. De indeling van de gegevens in deze vector tegels als [hier wordt beschreven](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles). Met de volgende code wordt een gebeurtenis Click toegevoegd aan de kaart en wordt de functie voor het verkeers incident opgehaald waarop is geklikt en wordt er een pop-upbericht weer gegeven met een aantal details.

```java
//Show traffic information on the map.
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
);

//Add a click event to the map.
map.events.add((OnFeatureClick) (features) -> {

    if (features != null && features.size() > 0) {
        Feature incident = features.get(0);

        //Ensure that the clicked feature is an traffic incident feature.
        if (incident.properties() != null && incident.hasProperty("incidentType")) {

            StringBuilder sb = new StringBuilder();
            String incidentType = incident.getStringProperty("incidentType");

            if (incidentType != null) {
                sb.append(incidentType);
            }

            if (sb.length() > 0) {
                sb.append("\n");
            }

            //If the road is closed, find out where it is closed from.
            if ("Road Closed".equals(incidentType)) {
                String from = incident.getStringProperty("from");

                if (from != null) {
                    sb.append(from);
                }
            } else {
                //Get the description of the traffic incident.
                String description = incident.getStringProperty("description");

                if (description != null) {
                    sb.append(description);
                }
            }

            String message = sb.toString();

            if (message.length() > 0) {
                Toast.makeText(this, message, Toast.LENGTH_LONG).show();
            }
        }
    }
});
```

In de volgende scherm afbeelding ziet u informatie over het rending in realtime verkeer op de kaart met een pop-upbericht waarin de details van het incident worden weer gegeven.

![Toewijzing met gegevens over realtime verkeer met een pop-upbericht met details over incidenten](media/how-to-show-traffic-android/android-traffic-details.png)

## <a name="next-steps"></a>Volgende stappen

Bekijk de volgende hand leidingen voor meer informatie over het toevoegen van meer gegevens aan uw kaart:

> [!div class="nextstepaction"]
> [Een titellaag toevoegen](how-to-add-tile-layer-android-map.md)

---
title: Reageren op Azure Maps gebeurtenissen met behulp van Event Grid
description: Meer informatie over hoe u kunt reageren op Azure Maps-gebeurtenissen waarbij geofences worden betrokken. Lees hoe u gebeurtenissen kunt belui Steren en hoe u Event Grid kunt gebruiken om gebeurtenissen om te leiden naar gebeurtenis-handlers.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/16/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 44b2cab814d4e51c4eb0c3ce3322b898f5b26414
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92889763"
---
# <a name="react-to-azure-maps-events-by-using-event-grid"></a>Reageren op Azure Maps gebeurtenissen met behulp van Event Grid

Azure Maps integreert met Azure Event Grid, zodat gebruikers gebeurtenis meldingen kunnen verzenden naar andere services en downstream-processen activeren. Het doel van dit artikel is om u te helpen uw zakelijke toepassingen te configureren om te Luis teren naar Azure Maps-gebeurtenissen. Hierdoor kunnen gebruikers reageren op kritieke gebeurtenissen op een betrouw bare, schaal bare en veilige manier. Gebruikers kunnen bijvoorbeeld een toepassing bouwen om een Data Base bij te werken, een ticket te maken en een e-mail melding te leveren, telkens wanneer een apparaat een geofence binnenkomt.

Azure Event Grid is een volledig beheerde service voor gebeurtenisrouting, die gebruikmaakt van een publicatie/abonnementmodel. Event Grid heeft ingebouwde ondersteuning voor Azure-Services, zoals [Azure Functions](../azure-functions/functions-overview.md) en [Azure Logic-apps](../azure-functions/functions-overview.md). Het kan gebeurteniswaarschuwingen leveren aan niet-Azure-Services met behulp van webhooks. Zie [Een inleiding tot Azure Event Grid](../event-grid/overview.md) voor een volledige lijst met gebeurtenisverwerkers die Event Grid ondersteunt.


![Azure Event Grid functionele model](./media/azure-maps-event-grid-integration/azure-event-grid-functional-model.png)


## <a name="azure-maps-events-types"></a>Azure Maps typen gebeurtenissen

Gebeurtenisraster maakt gebruik van [gebeurtenisabonnementen](../event-grid/concepts.md#event-subscriptions) om gebeurtenisberichten te routen naar abonnees. Een Azure Maps account verzendt de volgende gebeurtenis typen: 

| Gebeurtenistype | Beschrijving |
| ---------- | ----------- |
| Micro soft. Maps. GeofenceEntered | Deze gebeurtenis treedt op wanneer ontvangen coördinaten van buiten een bepaalde geofence zijn verplaatst naar in |
| Micro soft. Maps. GeofenceExited | Deze gebeurtenis treedt op wanneer ontvangen coördinaten van binnen een bepaalde geofence naar buiten zijn verplaatst |
| Micro soft. Maps. GeofenceResult | Deze gebeurtenis treedt elke keer op wanneer een geoomheinings query een resultaat retourneert, ongeacht de status |

## <a name="event-schema"></a>Gebeurtenisschema

In het volgende voor beeld ziet u het schema voor GeofenceResult:

```JSON
{
    "id":"451675de-a67d-4929-876c-5c2bf0b2c000",
    "topic":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}",
    "subject":"/spatial/geofence/udid/{udid}/id/{eventId}",
    "data":{
        "geometries":[
            {
                "deviceId":"device_1",
                "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169",
                "geometryId":"1",
                "distance":999.0,
                "nearestLat":47.609833,
                "nearestLon":-122.148274
            }
        ],
        "expiredGeofenceGeometryId":[
        ],
        "invalidPeriodGeofenceGeometryId":[
        ]
    },
    "eventType":"Microsoft.Maps.GeofenceResult",
    "eventTime":"2018-11-08T00:52:08.0954283Z",
    "metadataVersion":"1",
    "dataVersion":"1.0"
}

```

## <a name="tips-for-consuming-events"></a>Tips voor het gebruik van gebeurtenissen

Toepassingen die Azure Maps geofence-gebeurtenissen afhandelen, moeten een aantal aanbevolen procedures volgen:

* Meerdere abonnementen configureren voor het door sturen van gebeurtenissen naar dezelfde gebeurtenis-handler. Het is belang rijk dat u ervan uitgaat dat gebeurtenissen van een bepaalde bron afkomstig zijn. Controleer altijd het onderwerp bericht om er zeker van te zijn dat het bericht afkomstig is van de bron die u verwacht.
* Gebruik het `X-Correlation-id` veld in de antwoord header om te begrijpen of uw informatie over objecten up-to-date is. Berichten arriveren soms in de verkeerde volgorde of na een vertraging.
* Wanneer een GET-of POST-aanvraag in de geofence-API wordt aangeroepen met de para meter mode ingesteld op `EnterAndExit` , wordt er een Enter-of exit-gebeurtenis gegenereerd voor elke geometrie in de geofence waarvoor de status is gewijzigd ten opzichte van de vorige geofence API-aanroep.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het gebruik van geoomheining om bewerkingen op een bouw site te beheren:

> [!div class="nextstepaction"] 
> [Een geofence instellen met behulp van Azure Maps](tutorial-geofence.md)
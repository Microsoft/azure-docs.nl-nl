---
title: Een signaal Gate configureren voor het op gebeurtenissen gebaseerde video-opname-Azure
description: In dit artikel vindt u informatie over het configureren van een signaal poort in een media grafiek.
ms.topic: how-to
ms.date: 11/3/2020
ms.openlocfilehash: afcec7c03f1353f08b58311278f5a533e0c911bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94410790"
---
# <a name="configure-a-signal-gate-for-event-based-video-recording"></a>Een signaal poort configureren voor video-opname op basis van gebeurtenissen

Binnen een media grafiek kunt u met een [signaal poort-processor knooppunt](media-graph-concept.md#signal-gate-processor) media door sturen van het ene naar het andere knoop punt wanneer de poort wordt geactiveerd door een gebeurtenis. Wanneer het wordt geactiveerd, wordt de poort geopend en kan de media stroom gedurende een opgegeven duur worden uitgevoerd. Als er geen gebeurtenissen zijn om de Gate te activeren, wordt de Gate gesloten en stopt de media stroom. U kunt de Signal Gate-processor gebruiken voor het opnemen van video op basis van gebeurtenissen.

In dit artikel leert u hoe u een Signal Gate-processor kunt configureren.

## <a name="suggested-prereading"></a>Voorgestelde voor lezen
-   [Mediagrafiek](media-graph-concept.md)
-   [Video-opname op basis van gebeurtenissen](event-based-video-recording-concept.md)


## <a name="problem"></a>Probleem
Het is mogelijk dat een gebruiker op een bepaald moment begint met het opnemen van of na het activeren van de gate door een gebeurtenis. De gebruiker weet de acceptabele latentie binnen hun systeem. Daarom wilt u de latentie van de signaal Gate-processor opgeven. Ze willen ook de minimale en maximale duur van de registratie opgeven, ongeacht het aantal nieuwe gebeurtenissen dat is ontvangen.
 
### <a name="use-case-scenario"></a>Use-casescenario
Stel dat u video wilt opnemen wanneer de voor deur van uw gebouw wordt geopend. U wilt de opname: 

- Neem de *X* seconden op voordat de deur wordt geopend. 
- Ten minste de laatste *Y* seconden als de deur niet opnieuw wordt geopend. 
- Laatste Maxi maal *Z* seconden als de deur herhaaldelijk wordt geopend. 
 
U weet dat uw deur sensor een latentie van *K* seconden heeft. Om ervoor te zorgen dat de kans op gebeurtenissen die worden genegeerd als late aankomsten, moet u ten minste *K* seconden wachten tot de gebeurtenissen arriveren.


## <a name="solution"></a>Oplossing

Om het probleem op te lossen, wijzigt u de para meters van de signaal Gate-processor.

Als u een signaal Gate-processor wilt configureren, gebruikt u deze vier para meters:
- Evaluatie venster activering
- Offset van activerings signaal
- Minimale activerings venster
- Maximum activerings venster

Wanneer de Signal Gate-processor wordt geactiveerd, blijft deze geopend gedurende de minimale activerings tijd. De activerings gebeurtenis begint bij het tijds tempel voor de eerste gebeurtenis, plus de offset van het activerings signaal. 

Als de signaal Gate-processor opnieuw wordt geactiveerd terwijl deze is geopend, wordt de timer opnieuw ingesteld en wordt de poort ten minste de minimale activerings tijd geopend. De signaal poort processor blijft nooit langer open dan de maximale activerings tijd. 

Een gebeurtenis (gebeurtenis 1) die zich voordoet vóór een andere gebeurtenis (gebeurtenis 2), op basis van de tijds tempels van het medium, kan worden genegeerd als de systeem-lags en gebeurtenis 1 arriveren op de signaal Gate-processor na gebeurtenis 2. Als gebeurtenis 1 niet kan worden ontvangen tussen de ontvangst van gebeurtenis 2 en het activerings evaluatie venster, wordt gebeurtenis 1 genegeerd. Het wordt niet door gegeven via de signaal Gate-processor. 

Correlatie-Id's worden ingesteld voor elke gebeurtenis. Deze Id's worden ingesteld op basis van de eerste gebeurtenis. Ze zijn opeenvolgend voor elke volgende gebeurtenis.

> [!IMPORTANT]
> De media tijd is gebaseerd op het tijds tempel van de media wanneer een gebeurtenis zich op de media bevindt. De volg orde van gebeurtenissen die op signaal gate arriveren, kan mogelijk niet overeenkomen met de volg orde van gebeurtenissen die in de media tijd arriveren.


### <a name="parameters-based-on-the-physical-time-that-events-arrive-at-the-signal-gate"></a>Para meters, op basis van de fysieke tijds duur dat gebeurtenissen bij de signaal poort arriveren

* **minimumActivationTime (kortst mogelijke duur van een opname)**: het minimum aantal seconden dat de signaal Gate-processor open moet blijven nadat deze is geactiveerd om nieuwe gebeurtenissen te ontvangen, tenzij deze wordt onderbroken door de maximumActivationTime.
* **maximumActivationTime (langst mogelijke duur van een opname)**: het maximum aantal seconden vanaf de eerste gebeurtenis dat de signaal Gate-processor open blijft nadat deze is geactiveerd om nieuwe gebeurtenissen te ontvangen, ongeacht welke gebeurtenissen er worden ontvangen.
* **activationSignalOffset**: het aantal seconden tussen de activering van de signaal Gate-processor en het begin van de video-opname. Normaal gesp roken is deze waarde negatief omdat hiermee de opname wordt gestart voordat de gebeurtenis wordt geactiveerd.
* **activationEvaluationWindow**: vanaf de eerste activerings gebeurtenis, het aantal seconden waarin een gebeurtenis die heeft plaatsgevonden vóór de eerste gebeurtenis, in de media tijd, moet arriveren op de signaal Gate-processor voordat deze wordt genegeerd en wordt beschouwd als een late aankomst.

> [!NOTE]
> Een *late aankomst* is een gebeurtenis die aankomt nadat het evaluatie venster voor activering is verstreken, maar dat aankomt vóór de eerste gebeurtenis in de media tijd.

### <a name="limits-of-parameters"></a>Limieten van para meters

* **activationEvaluationWindow**: 0 seconden tot 10 seconden
* **activationSignalOffset**:-1 minuut tot 1 minuut
* **minimumActivationTime**: 1 seconde tot 1 uur
* **maximumActivationTime**: 1 seconde tot 1 uur


In het geval kunt u de para meters als volgt instellen:

* **activationEvaluationWindow**: *K* seconden
* **activationSignalOffset**: *-X* seconden
* **minimumActivationWindow**: *Y* seconden
* **maximumActivationWindow**: *Z* seconden


Hier volgt een voor beeld van hoe de sectie van het knoop punt van de **signaal poort processor** eruit ziet in een media grafiek topologie voor de volgende parameter waarden:
* **activationEvaluationWindow**: 1 seconde
* **activationSignalOffset**:-5 seconden
* **minimumActivationTime**: 20 seconden
* **maximumActivationTime**: 40 seconden

> [!IMPORTANT]
> De [ISO 8601-duur notatie](https://en.wikipedia.org/wiki/ISO_8601#Durations
) wordt verwacht voor elke parameter waarde. Bijvoorbeeld, PT1S = 1 seconde.


```
"processors":              
[
          {
            "@type": "#Microsoft.Media.MediaGraphSignalGateProcessor",
            "name": "signalGateProcessor",
            "inputs": [
              {
                "nodeName": "iotMessageSource"
              },
              {
                "nodeName": "rtspSource"
              }
            ],
            "activationEvaluationWindow": "PT1S",
            "activationSignalOffset": "-PT5S",
            "minimumActivationTime": "PT20S",
            "maximumActivationTime": "PT40S"
          }
]
```


Denk nu na over hoe deze signaal Gate-processor configuratie in verschillende opname scenario's werkt.

### <a name="recording-scenarios"></a>Scenario's vastleggen

**Eén gebeurtenis van één bron (*normale activering*)**

Een Signal Gate-processor die één gebeurtenis ontvangt, resulteert in een opname die 5 seconden begint (activerings signaal = 5 seconden) voordat de gebeurtenis op de poort arriveert. De rest van de opname is 20 seconden (minimale activerings tijd = 20 seconden), omdat er geen andere gebeurtenissen arriveren vóór het einde van de minimale activerings tijd om de poort opnieuw te activeren.

Voorbeeld diagram:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/normal-activation.svg" alt-text="Diagram waarin de normale activering van één gebeurtenis van één bron wordt weer gegeven.":::

* Duur van de opname =-offset + minimumActivationTime = [E1 + verschuiving, E1 + minimumActivationTime]


**Twee gebeurtenissen van één bron (*geactiveerde activering*)**

Een Signal Gate-processor die twee gebeurtenissen ontvangt, resulteert in een opname die 5 seconden begint (offset van activerings signaal = 5 seconden) voordat de gebeurtenis op de poort arriveert. Daarnaast arriveert gebeurtenis 2 vijf seconden na gebeurtenis 1. Omdat gebeurtenis 2 arriveert vóór het einde van de minimale activerings tijd van gebeurtenis 1 (20 seconden), wordt de poort opnieuw geactiveerd. De rest van de opname is 20 seconden (minimale activerings tijd = 20 seconden), omdat er geen andere gebeurtenissen arriveren vóór het einde van de minimale activerings tijd van gebeurtenis 2 om de poort opnieuw te activeren.

Voorbeeld diagram:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/retriggering-activation.svg" alt-text="Diagram waarin de opnieuw geactiveerde activering van twee gebeurtenissen van één bron wordt weer gegeven.":::

* Duur van de opname =-offset + (aankomst van gebeurtenis 2-binnenkomst van gebeurtenis 1) + minimumActivationTime


***N* gebeurtenissen van de ene bron (*maximum activering*)**

Een Signal Gate-processor die *N* gebeurtenissen ontvangt, resulteert in een opname die 5 seconden begint (offset van activerings signaal = 5 seconden) voordat de eerste gebeurtenis bij de poort arriveert. Wanneer elke gebeurtenis arriveert vóór het einde van de minimale activerings tijd van 20 seconden vanaf de vorige gebeurtenis, wordt de poort continu opnieuw geactiveerd. Het blijft open tot de maximale activerings tijd van 40 seconden na de eerste gebeurtenis. Vervolgens wordt de Gate gesloten en worden er geen nieuwe gebeurtenissen meer geaccepteerd.

Voorbeeld diagram:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/maximum-activation.svg" alt-text="Diagram met de maximale activering van N gebeurtenissen van één bron.":::
 
* Duur van de opname =-offset + maximumActivationTime

> [!IMPORTANT]
> In de voor gaande diagrammen wordt ervan uitgegaan dat elke gebeurtenis op hetzelfde moment in de fysieke tijd en media tijd arriveert. Dat wil zeggen dat ze ervan uitgaan dat er geen late aankomsten zijn.

## <a name="next-steps"></a>Volgende stappen

Probeer de [zelf studie voor video-opname op basis van gebeurtenissen](event-based-video-recording-tutorial.md)uit. Begin met het bewerken [ van detopology.jsop](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json). Wijzig de para meters voor het knoop punt signalgateProcessor en volg de rest van de zelf studie. Bekijk de video-opnames om het effect van de para meters te analyseren.




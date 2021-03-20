---
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 11/09/2018
ms.author: juliako
ms.openlocfilehash: 065cb4daa9501ee658d364dad43b9e03798e4083
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "67176449"
---
De taak produceert een JSON-uitvoer bestand dat meta gegevens bevat over gedetecteerde en getraceerde gezichten. De meta gegevens bevatten coördinaten die de locatie van de gezichten aangeven, evenals een face ID-nummer dat het bijhouden van die persoon aangeeft. Gezichts-ID-nummers zijn gevoelig voor het opnieuw instellen van de voor kant wanneer het frontale gezicht verloren is gegaan of wordt overlapt in het kader, wat betekent dat sommige personen meerdere Id's krijgen toegewezen.

De JSON van de uitvoer bevat de volgende elementen:

### <a name="root-json-elements"></a>Wortel-JSON-elementen

| Element | Beschrijving |
| --- | --- |
| versie |Dit verwijst naar de versie van de video-API. |
| lijnen |' Ticks ' per seconde van de video. |
| offset |Dit is de tijds verschuiving voor tijds tempels. In versie 1,0 van video-Api's is dit altijd 0. Deze waarde kan worden gewijzigd in toekomstige scenario's die worden ondersteund. |
| breedte, Hoogt |De breedte en de hoogte van het video kader voor de uitvoer, in pixels.|
| snelheid |Aantal frames per seconde video. |
| [fragmenten](#fragments-json-elements) |De meta gegevens worden gesegmenteerd in verschillende segmenten die fragmenten worden genoemd. Elk fragment bevat een start, een duur, een intervalnummer en een of meer gebeurtenissen. |

### <a name="fragments-json-elements"></a>Fragmenten JSON-elementen

|Element|Beschrijving|
|---|---|
| starten |De begin tijd van de eerste gebeurtenis in Ticks. |
| duur |De lengte van het fragment, in Ticks. |
| index | (Alleen van toepassing op Azure Media Redactor) Hiermee definieert u de kader-index van de huidige gebeurtenis. |
| interval |Het interval van elke gebeurtenis vermelding in het fragment, in Ticks. |
| events |Elke gebeurtenis bevat de gezichten die binnen die tijds duur zijn gedetecteerd en bijgehouden. Het is een matrix met gebeurtenissen. De buitenste matrix vertegenwoordigt een tijdsinterval. De binnenste matrix bestaat uit 0 of meer gebeurtenissen die hebben plaatsgevonden gedurende de interval. Een lege beugel [] betekent dat er geen gezichten zijn gedetecteerd. |
| id |De ID van het gezicht dat wordt bijgehouden. Dit aantal kan per ongeluk worden gewijzigd als een gezicht niet wordt gedetecteerd. Een bepaalde persoon moet dezelfde ID hebben in de gehele video, maar dit kan niet worden gegarandeerd vanwege beperkingen in het detectie algoritme (bedekking, enzovoort). |
| x, y |De linkerbovenhoek van de X-en Y-coördinaten van het begrenzingsvak van het gezicht in een genormaliseerde schaal van 0,0 tot 1,0. <br/>-X-en Y-coördinaten zijn relatief ten opzichte van liggend, dus als u een staande video (of ondersteboven, in het geval van iOS) hebt, moet u de coördinaten dienovereenkomstig transponeren. |
| breedte, hoogte |De breedte en hoogte van het begrenzingsvak van het gezicht in een genormaliseerde schaal van 0,0 tot 1,0. |
| facesDetected |Aan het einde van de JSON-resultaten vindt u een samen vatting van het aantal gezichten dat de algoritme tijdens de video heeft gedetecteerd. Omdat de Id's per ongeluk opnieuw kunnen worden ingesteld als een gezicht niet wordt gedetecteerd (bijvoorbeeld omdat het gezicht niet meer wordt weer gegeven, ziet het scherm er uit), is dit aantal mogelijk niet altijd gelijk aan het werkelijke aantal gezichten in de video. |

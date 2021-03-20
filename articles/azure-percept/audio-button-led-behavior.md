---
title: Azure percept audio-knop en LED-gedrag
description: Meer informatie over de knop en LED-status van Azure percept-audio
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: 1d956e33a84b5509c16400c8f5f8e813d116411a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102095745"
---
# <a name="azure-percept-audio-button-and-led-behavior"></a>Azure percept audio-knop en LED-gedrag

Raadpleeg de volgende richt lijnen voor informatie over de knop en LED-status van de Azure percept-audio.

## <a name="button-behavior"></a>Gedrag van knop

U kunt de knoppen gebruiken om het gedrag van het apparaat te bepalen.

|Knop status|  Gedrag|
|------------|----------|
|Dempen|  Druk op om de Mic-matrix te dempen/dempen opheffen. De knop gebeurtenis is release-wordt geactiveerd wanneer er wordt ingedrukt.|
|PTT/PTS|   Druk op PTT om de status van het sleutel woord herkennen over te slaan en de opdracht Luister status te activeren. Druk nogmaals op om het actieve dialoog venster van de agent te stoppen en terug te keren naar de status van het sleutel woord herkennen. De knop gebeurtenis is release-wordt geactiveerd wanneer er wordt ingedrukt. PTS werkt alleen wanneer de knop wordt ingedrukt terwijl de agent spreekt, niet wanneer de agent luistert of overweegt.|

## <a name="led-behavior"></a>LED-gedrag

U kunt LED-indica toren gebruiken om te begrijpen in welke staat het apparaat zich bevindt.

|GELEID|   LED-status|  De SoM-status van het oormerk|
|---|------------|----------------| 
|L02|   1x-wit, statisch op |Inschakelen |
|L02|   1x-wit, 0,5 Hz flashen|  Verificatie wordt uitgevoerd |
|L01 & L02 & L03|   3x blauw, statisch op|     Wachten op sleutel woord|
|L01 & L02 & L03|   LED voor matrix flitsen, 20fps | Luis teren of spreken|
|L01 & L02 & L03|   LED array Racing, 20fps|    Gedachten|
|L01 & L02 & L03|   3x rood, statisch op | Dempen|

## <a name="next-steps"></a>Volgende stappen

Zie deze [hand leiding](./troubleshoot-audio-accessory-speech-module.md)voor tips voor het oplossen van problemen met het audio apparaat van Azure percept.
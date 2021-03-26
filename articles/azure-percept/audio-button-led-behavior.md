---
title: Azure percept audio-knop en LED-gedrag
description: Meer informatie over de knop en LED-status van Azure percept-audio
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: fa919acb527084d19ab88b2e7895d4e6ab0b72d3
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105609068"
---
# <a name="azure-percept-audio-button-and-led-behavior"></a>Azure percept audio-knop en LED-gedrag

Raadpleeg de volgende richt lijnen voor informatie over de knop en LED-status van het Azure percept-audio apparaat.

## <a name="button-behavior"></a>Gedrag van knop

Gebruik de knoppen om het gedrag van het apparaat te bepalen.

|Knop status|Gedrag|
|------------|----------|
|Dempen|Druk op om de Mic-matrix te dempen/dempen opheffen. De knop gebeurtenis is release-wordt geactiveerd wanneer er wordt ingedrukt.|
|PTT/PTS|Druk op PTT om de status van het sleutel woord herkennen over te slaan en de opdracht Luister status te activeren. Druk opnieuw om het actieve dialoog venster van de agent te stoppen en terug te keren naar de status van het tref woord herkennen. De knop gebeurtenis is release-wordt geactiveerd wanneer er wordt ingedrukt. PTS werkt alleen wanneer de knop wordt ingedrukt terwijl de agent spreekt, niet wanneer de agent luistert of overweegt.|

## <a name="led-behavior"></a>LED-gedrag

Gebruik de LED-Indica tors om te begrijpen in welke staat uw apparaat zich bevindt.

|GELEID|LED-status|De SoM-status van het oormerk|
|---|------------|----------------|
|L02|1x-wit, statisch op|Inschakelen |
|L02|1x-wit, 0,5 Hz flashen|Verificatie wordt uitgevoerd |
|L01 & L02 & L03|3x blauw, statisch op|Wachten op sleutel woord|
|L01 & L02 & L03|LED voor matrix flitsen, 20fps |Luis teren of spreken|
|L01 & L02 & L03|LED array Racing, 20fps|Gedachten|
|L01 & L02 & L03|3x rood, statisch op |Dempen|

## <a name="next-steps"></a>Volgende stappen

Zie deze [hand leiding](./troubleshoot-audio-accessory-speech-module.md)voor tips voor het oplossen van problemen met het audio apparaat van Azure percept.
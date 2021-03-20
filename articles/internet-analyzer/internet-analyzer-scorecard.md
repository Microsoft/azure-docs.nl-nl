---
title: Uw Score Card interpreteren | Microsoft Docs
description: Meer informatie over hoe u uw Score Card kunt interpreteren. Het tabblad Score Card bevat de geaggregeerde en geanalyseerde resultaten van uw tests.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: how-to
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: f43d094193fb266d1ecec7089b44d8b3fd5e9b43
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91330210"
---
# <a name="interpreting-your-scorecard"></a>Uw scorecard interpreteren

Het tabblad Score Card bevat de geaggregeerde en geanalyseerde resultaten van uw tests. Elke test heeft zijn eigen score cards. Score cards bieden snelle en zinvolle samen vattingen van meet resultaten om gegevensgestuurde resultaten te bieden voor uw netwerk vereisten. Internet Analyzer zorgt voor de analyse, zodat u zich kunt concentreren op de beslissing.

Het tabblad Score Card kan worden gevonden in het menu Internet Analyzer resource. 


## <a name="filters"></a>Filters

* ***Testen:*** Selecteer de test waarvoor u de resultaten wilt bekijken: elke test heeft een eigen score card. Test gegevens worden weer gegeven wanneer er voldoende gegevens zijn om de analyse te volt ooien – in de meeste gevallen moet dit binnen 24 uur liggen. 
* ***Eind datum van de tijds periode &:*** Er worden dagelijks drie Score cards gegenereerd: elke Score Card weerspiegelt een andere aggregatie periode – de 24 uur vóór (dag), de zeven dagen vóór (week) en de 30 dagen vóór (maand). Gebruik het filter eind datum om de laatste dag van de periode te selecteren die u wilt weer geven. 
* ***Land:*** Voor elk land dat u eind gebruikers hebt, wordt een score card gegenereerd. Het globale filter bevat alle eind gebruikers.

## <a name="measurement-count"></a>Aantal metingen

Het aantal metingen is van invloed op het vertrouwen van de analyse. Hoe hoger het aantal, des te nauw keuriger het resultaat. Tests moeten mini maal zijn gericht op mini maal 100 metingen per eind punt per dag. Als de meet aantallen te laag zijn, moet u de Java script-client zo configureren dat deze vaker wordt uitgevoerd in uw toepassing. De meet aantallen voor eind punten A en B moeten erg vergelijkbaar zijn, hoewel kleine verschillen worden verwacht en juist zijn. In het geval van grote verschillen moeten de resultaten niet worden vertrouwd.

## <a name="percentiles"></a>Percentielen

Latentie, gemeten in milliseconden, is een populaire meet waarde voor het meten van snelheid tussen een bron en bestemming op internet. Latentie gegevens worden normaal gesp roken niet gedistribueerd (dat wil zeggen: geen "klok curve") omdat er een "lange staart" van grote latentie waarden is die de resultaten scheef trekken wanneer statistieken zoals het reken kundige gemiddelde worden gebruikt. Als alternatief bieden percentielen een ' distributie vrije ' manier om gegevens te analyseren. Als voor beeld is het mediaan-of 50e percentiel een samen vatting van het midden van de verdeling-helft van de waarden. Een waarde voor een 75e percentiel betekent dat deze groter is dan 75% van alle waarden in de distributie. Internet Analyzer verwijst naar percentielen in steno als P50, P75 en P95.

Internet Analyzer-percentielen zijn _voorbeeld metrieken_. Dit is in tegens telling tot de werkelijke _metrische gegevens_ van de populatie. Zo is de dagelijkse gemiddelde latentie van de populatie tussen studenten in de University of Zuid Californië en micro soft de mediaan latentie waarde van alle aanvragen gedurende die dag. In de praktijk is het meten van de waarde van alle aanvragen niet praktisch, dus we gaan ervan uit dat een redelijkerwijs grote steek proef representatief is voor de echte populatie.

Voor analyse doeleinden is P50 (mediaan) nuttig als een verwachte waarde voor een latentie distributie. Hogere percentielen, zoals P95, zijn handig om te bepalen hoe hoge latentie in de slechtste gevallen is. Als u geïnteresseerd bent in informatie over de latentie van klanten in het algemeen, is P50 de juiste meet waarde om zich te richten op. Als u zich zorgen maakt over de prestaties van de slechtste klanten, moet P95 de focus zijn. P75 is een evenwicht tussen deze twee.


## <a name="deltas"></a>Delta's

Een Delta is het verschil in metrische waarden voor eind punten A en B. er worden Deltas berekend om het voor deel van B over A te laten zien. positieve waarden geven aan dat B beter is uitgevoerd dan A. negatieve waarden geven aan dat de prestaties van B verergerd zijn. Deltas kunnen absoluut zijn (bijvoorbeeld 10 milliseconden) of relatief (5%).

## <a name="confidence-interval"></a>Betrouwbaarheidsinterval 

Betrouwbaarheids intervallen (CI) zijn een reeks waarden die een kans hebben dat de populatie-metriek, zoals mediaan, P75 of gemiddeld, wordt geretourneerd. We volgen de algemene statistische Conventie van het gebruik van de 95% CI.

Voor Internet Analyzer is een beperkt betrouwbaarheids interval goed, omdat het laat zien dat de metrische gegevens van de steek proef waarschijnlijk zeer dicht bij de werkelijke metrische gegevens van de populatie vallen. Een breed betrouwbaarheids interval betekent minder zekerheid dat de metrische metrische gegevens van de steek proef overeenkomen met de werkelijke waarde van de populatie. De beste manier om de CI te verbeteren is door het verhogen van de meet aantallen.

## <a name="time-series"></a>Tijdreeks 

Een tijd reeks laat zien hoe een metriek in de loop van de tijd verandert. Op het Internet zijn er veel tijdelijke factoren die van invloed zijn op de prestaties, zoals piek perioden, weken in weekends en feest dagen.


## <a name="next-steps"></a>Volgende stappen

Zie het [overzicht van Internet Analyzer](internet-analyzer-overview.md)voor meer informatie.

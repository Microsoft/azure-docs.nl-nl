---
title: Voertuig modellen voor route ring | Microsoft Azure kaarten
description: 'Meer informatie over de verbruiks modellen die Azure Maps ondersteunt: verbranding en elektrisch. Zie welke para meters elk model gebruikt en parameter beperkingen weer geven.'
author: subbarayudukamma
ms.author: skamma
ms.date: 05/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: b44186d783a249192a8c13ee97063034ee319df7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013136"
---
# <a name="consumption-model"></a>Verbruiksmodel

De Routing-service voorziet in een set para meters voor een gedetailleerde beschrijving van het model voor het specifieke verbruik van het Voer tuig.
Afhankelijk van de waarde van **vehicleEngineType** worden twee hoofd verbruiks modellen ondersteund: _verbranding_ en _elektrisch_. Het is niet juist om para meters op te geven die bij verschillende modellen horen in dezelfde aanvraag. De para meters voor verbruiks model kunnen niet worden gebruikt met de volgende **travelMode** -waarden: _fiets_ en _voetgangers_.

## <a name="parameter-constraints-for-consumption-model"></a>Parameter beperkingen voor verbruiks model

In beide verbruiks modellen zijn er een aantal afhankelijkheden bij het opgeven van para meters. Dit betekent dat het expliciet opgeven van sommige para meters vereist dat sommige andere para meters worden opgegeven. Hier volgen enkele van de volgende afhankelijkheden:

* Voor alle para meters moet **constantSpeedConsumption** worden opgegeven door de gebruiker. Er is een fout opgetreden bij het opgeven van een andere para meter voor een verbruiks model als **constantSpeedConsumption** niet is opgegeven. De **vehicleWeight** -para meter is een uitzonde ring voor deze vereiste.
* **accelerationEfficiency** en **decelerationEfficiency** moeten altijd worden opgegeven als een combi natie (dat wil zeggen, beide of geen).
* Als **accelerationEfficiency** en **decelerationEfficiency** zijn opgegeven, mag het product van hun waarden niet groter zijn dan 1 (om permanente beweging te voor komen).
* **uphillEfficiency** en **downhillEfficiency** moeten altijd worden opgegeven als een combi natie (dat wil zeggen, beide of geen).
* Als **uphillEfficiency** en **downhillEfficiency** zijn opgegeven, mag het product van hun waarden niet groter zijn dan 1 (om permanente beweging te voor komen).
* Als de \* __efficiëntie__ parameters worden opgegeven door de gebruiker, moet **vehicleWeight** ook worden opgegeven. Wanneer **vehicleEngineType** is _verbrand_, moet ook **fuelEnergyDensityInMJoulesPerLiter** worden opgegeven.
* **maxChargeInkWh** en **currentChargeInkWh** moeten altijd worden opgegeven als een combi natie (dat wil zeggen, beide of geen).

> [!NOTE]
> Als er alleen **constantSpeedConsumption** is opgegeven, worden er geen andere verbruiks aspecten, zoals hellingen en voertuig versnelling, in rekening gebracht voor verbruiks berekeningen.

## <a name="combustion-consumption-model"></a>Model voor verbrandings consumptie

Het model voor stook verbruik wordt gebruikt wanneer **vehicleEngineType** is ingesteld op _verbranding_.
De lijst met para meters die deel uitmaken van dit model vindt u hieronder. Raadpleeg de sectie para meters voor een gedetailleerde beschrijving.

* constantSpeedConsumptionInLitersPerHundredkm
* vehicleWeight
* currentFuelInLiters
* auxiliaryPowerInLitersPerHour
* fuelEnergyDensityInMJoulesPerLiter
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="electric-consumption-model"></a>Model voor elektriciteits verbruik

Het elektriciteits verbruiks model wordt gebruikt wanneer **vehicleEngineType** is ingesteld op _Electric_.
De lijst met para meters die deel uitmaken van dit model vindt u hieronder. Raadpleeg de sectie para meters voor een gedetailleerde beschrijving.

* constantSpeedConsumptionInkWhPerHundredkm
* vehicleWeight
* currentChargeInkWh
* maxChargeInkWh
* auxiliaryPowerInkW
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="sensible-values-of-consumption-parameters"></a>Praktische waarden van verbruiks parameters

Een bepaalde set para meters kan worden afgewezen, zelfs als de set kan voldoen aan alle expliciete vereisten. Wanneer de waarde van een specifieke para meter, of een combi natie van waarden van verschillende para meters, wordt beschouwd als een onredelijke omvang van verbruiks waarden. Als dat gebeurt, duidt dit meestal op een invoer fout, omdat er voldoende aandacht wordt besteed aan alle goed werkende waarden van verbruiks parameters. Als een bepaalde set verbruiks parameters wordt afgewezen, bevat het bijbehorende fout bericht een beschrijving van de reden (en).
De gedetailleerde beschrijvingen van de para meters hebben voor beelden van de voor waarden voor beide modellen.

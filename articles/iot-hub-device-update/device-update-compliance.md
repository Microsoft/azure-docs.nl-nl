---
title: Informatie over het bijwerken van het apparaat voor Azure IoT Hub naleving | Microsoft Docs
description: Meer informatie over het bijwerken van de naleving van apparaten met updates voor Azure IoT Hub.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: ac6094efde8a32b1fcc04c55bbc537afeb4166f7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101662560"
---
# <a name="device-update-compliance"></a>Apparaat Updatenaleving

Bij het bijwerken van het apparaat voor IoT Hub, meet de naleving op hoeveel apparaten de hoogste versie-compatibele update is geïnstalleerd. Een apparaat is compatibel als er de hoogste beschik bare update versie is geïnstalleerd die hiervoor compatibel is. 

Denk bijvoorbeeld aan een exemplaar van het bijwerken van het apparaat met de volgende updates:

|Update naam|Update versie|Model compatibel apparaat|
|-----------|--------------|-----------------------|
|Update1    |1.0    |Model1|
|Updatum2    |1.0    |Model2|
|Update3    |2,0    |Model1|

Stel dat de volgende implementaties zijn gemaakt:

|Implementatie naam    |Update naam    |Doel groep|
|-----------|--------------|-------------------|
|Deployment1    |Update1    |Group1|
|Deployment2    |Updatum2    |Group2|
|Deployment3    |Update3    |Groep3|

Bekijk nu de volgende apparaten, met hun groepslid maatschappen en geïnstalleerde versies:

|DeviceId   |Model apparaat   |Geïnstalleerde update versie|Groep |Naleving|
|-----------|--------------|-----------------------|-----|---------|
|Device1    |Model1 |1.0    |Group1 |Er zijn nieuwe updates beschikbaar</span>|
|Device2    |Model1 |2,0    |Groep3 |Bij laatste update|
|Device3    |Model2 |1.0    |Group2 |Bij laatste update|
|Device4    |Model1 |1.0    |Groep3 |De update wordt uitgevoerd|

Device1 en Device4 zijn niet compatibel omdat er versie 1,0 is geïnstalleerd, ook al is er een hogere versie-update, Update3, compatibel met hun model in de update-instantie van het apparaat. Device2 en Device3 zijn beide compatibel omdat ze de hoogste versie-updates hebben die compatibel zijn voor hun modellen geïnstalleerd.

Naleving houdt geen rekening met het feit of een update wordt geïmplementeerd op de groep van een apparaat of niet. Er wordt gekeken naar de updates die zijn gepubliceerd voor het bijwerken van het apparaat. In het bovenstaande voor beeld, hoewel Device1 de update heeft geïnstalleerd, wordt deze als niet-compatibel beschouwd. Device1 wordt als niet-compatibel beschouwd en wordt Update3 geïnstalleerd. De nalevings status kan u helpen om te bepalen of er nieuwe implementaties nodig zijn. 

Zoals hierboven wordt weer gegeven, zijn er drie nalevings statussen bij het bijwerken van het apparaat voor IoT Hub:

*   **Bij de nieuwste update: op** het apparaat is de hoogste versie-compatibele update voor het bijwerken van het apparaat geïnstalleerd.
*   Er wordt een **update uitgevoerd** : een actieve implementatie is het proces van het leveren van de hoogste versie-compatibele update voor het apparaat.
*   Er **zijn nieuwe updates beschikbaar** : een apparaat heeft nog niet de hoogste versie-compatibele update geïnstalleerd en bevindt zich niet in een actieve implementatie voor die update.

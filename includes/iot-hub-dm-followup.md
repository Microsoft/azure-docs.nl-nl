---
title: bestand opnemen
description: bestand opnemen
services: iot-hub
ms.service: iot-hub
author: robinsh
ms.topic: include
ms.date: 02/17/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 7f1f7d6f9ab6036fbcfcd1d19e175302bbd1a2a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87298707"
---
## <a name="customize-and-extend-the-device-management-actions"></a>De acties voor Apparaatbeheer aanpassen en uitbreiden

Uw IoT-oplossingen kunnen de gedefinieerde set met Apparaatbeheer patronen uitbreiden of aangepaste patronen inschakelen met behulp van de twee-en Cloud-naar-Device-primitieve methoden van het apparaat. Andere voor beelden van acties voor Apparaatbeheer zijn de fabrieks instellingen, firmware-updates, software-updates, energie beheer, netwerk-en verbindings beheer en gegevens versleuteling.

## <a name="device-maintenance-windows"></a>Onderhouds Vensters voor apparaten

Normaal gesp roken configureert u apparaten voor het uitvoeren van acties op een moment dat onderbrekingen en downtime minimaliseert. Onderhouds Vensters voor apparaten zijn een veelgebruikt patroon voor het definiëren van de tijd waarop de configuratie van een apparaat moet worden bijgewerkt. Uw back-end-oplossingen kunnen de gewenste eigenschappen van het apparaat twee gebruiken om een beleid op uw apparaat te definiëren en te activeren waarmee een onderhouds venster wordt ingeschakeld. Wanneer een apparaat het onderhouds venster beleid ontvangt, kan dit de gerapporteerde eigenschap van het apparaat dubbele gebruiken om de status van het beleid te rapporteren. De back-end-app kan vervolgens Device-dubbele query's gebruiken om te voldoen aan de naleving van apparaten en elk beleid.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een directe methode gebruikt om een apparaat op afstand opnieuw op te starten. U hebt de gerapporteerde eigenschappen gebruikt voor het rapporteren van de tijd van de laatste keer opnieuw opstarten vanaf het apparaat en er is een query uitgevoerd op het apparaat tussen de laatste keer opnieuw opstarten van het apparaat in de Cloud.

Zie [een firmware-update uitvoeren](../articles/iot-hub/tutorial-firmware-update.md)als u wilt door gaan met IOT hub en patronen voor Apparaatbeheer, zoals extern via de Air firmware-update.

Zie [taken plannen en uitzenden](../articles/iot-hub/iot-hub-node-node-schedule-jobs.md)voor meer informatie over het uitbreiden van uw IOT-oplossing en het plannen van methode aanroepen op meerdere apparaten.
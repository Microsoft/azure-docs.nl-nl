---
title: De preview-modus inschakelen voor Azure IoT Hub
description: Meer informatie over het inschakelen van de preview-modus voor IoT Hub, waarom u dit wilt en enkele waarschuwingen
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: jlian
ms.openlocfilehash: 864870c4392b12477c321c86afd9da848120490c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96621702"
---
# <a name="turn-on-preview-mode-for-iot-hub-to-try-select-new-features"></a>Schakel de preview-modus voor IoT Hub om nieuwe functies te selecteren

<!-- 
- We are working hard to bring you new features
- Some of these features require a brand new iot hub with preview mode on
- some features may not work at all or have unexpected behavior
- "Normal preview features" do NOT require preview mode 
- Support opt-in at creation time only
- Customer cannot opt back out post creation
- If customer wants to evaluate, they must use new hub dedicated for the preview
- Banners, documentations and all materials indicate preview quality: no GA guarantee at all
-->

Nieuwe functies zijn beschikbaar in de open bare Preview voor IoT Hub, waaronder:

- MQTT 5
- ECC-server certificaat
- Onderhandeling TLS-fragment lengte
- Ondersteuning voor x509-CA-keten voor HTTPS/WebSocket

Deze functies zijn verbeterd op het IoT Hub protocol en verificatie lagen, zodat ze nu alleen beschikbaar zijn voor **nieuwe** IOT hub. Ze zijn nog *niet* beschikbaar voor bestaande IOT-hubs. Als u een voor beeld van deze functies wilt bekijken, moet u de IoT-hub maken met de preview-modus ingeschakeld.

## <a name="turn-on-preview-mode-for-a-new-iot-hub"></a>De preview-modus inschakelen voor een nieuwe IoT hub

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer in de Azure-startpagina de knop **+ Een resource maken** en voer vervolgens *IoT Hub* in het veld **Marketplace doorzoeken** in.

1. Selecteer **IoT Hub** in de zoekresultaten en selecteer vervolgens **Maken**.

1. Op het tabblad **basis beginselen** vult u de velden [zoals gebruikelijk](iot-hub-create-through-portal.md) in, met uitzonde ring van de **regio**. Selecteer een van deze regio's:
    
    - VS - centraal
    - Europa -west
    - Azië - zuidoost

1. Selecteer tabblad **beheer** en vouw vervolgens de sectie **Geavanceerde instellingen** uit.

1. Selecteer **aan in** de **voorbeeld modus**. Controleer de waarschuwings tekst zorgvuldig.

    :::image type="content" source="media/iot-hub-preview-mode/turn-on-preview-mode-at-create.png" alt-text="Afbeelding die laat zien waar de optie voor de voorbeeld modus moet worden geselecteerd bij het maken van een nieuwe IoT hub":::

1. Selecteer **volgende: controleren + maken** en **maken**.

Wanneer een IoT Hub in de preview-modus is gemaakt, wordt deze banner altijd weer gegeven, zodat u deze IoT hub alleen kunt gebruiken voor preview-doel einden: 

:::image type="content" source="media/iot-hub-preview-mode/banner.png" alt-text="Afbeelding met banner voor de preview-modus IoT hub":::

## <a name="using-an-iot-hub-in-preview-mode"></a>Een IoT-hub gebruiken in de preview-modus

Gebruik *geen* IOT-hub in de preview-modus voor productie. De preview-modus is *alleen* bedoeld voor een voor beeld van de Select-functies die boven aan deze pagina worden weer gegeven. Enkele andere beperkingen voor IoT Hub preview-modus zijn

- Sommige bestaande IoT Hub functies, zoals IP-filter, persoonlijke koppeling, beheerde identiteit, apparaatgegevens en failover, werken mogelijk onverwacht of helemaal niet.
- Een IoT-hub in de preview-modus kan niet worden gewijzigd of bijgewerkt naar een normale IoT-hub.
- Het is niet mogelijk om de normale [IOT hub Sla](https://azure.microsoft.com/support/legal/sla/iot-hub/v1_2/) te garanderen. Gebruik deze niet voor productie.

> [!TIP]
> De preview-modus is niet vereist voor [streams van apparaten](iot-hub-device-streams-overview.md) en [gedistribueerde tracering](iot-hub-distributed-tracing.md). Als u deze oudere preview-functies wilt gebruiken, volgt u de documentatie als normaal. 

## <a name="next-steps"></a>Volgende stappen

- Zie [IOT hub MQTT 5-ondersteunings overzicht (preview)](iot-hub-mqtt-5.md) voor een voor beeld van de MQTT 5-ondersteuning
- Als u een voor beeld van het ECC-server certificaat wilt bekijken, raadpleegt u het [ECC-server TLS-certificaat (voor beeld) (preview)](iot-hub-tls-support.md#elliptic-curve-cryptography-ecc-server-tls-certificate-preview)
- Zie voor meer informatie over TLS-fragment grootte onderhandelen voor onderhandeling over [maximale fragment lengte TLS (preview-versie)](iot-hub-tls-support.md#tls-maximum-fragment-length-negotiation-preview)
---
title: Ondersteuning voor niet-verbonden apparaat bijwerken met micro soft Connected cache | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Ondersteuning voor niet-verbonden apparaat bijwerken met micro soft Connected cache
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: e2b27934f58402ecfb7dabf5560dc43e45f3f7dd
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679260"
---
# <a name="understand-support-for-disconnected-device-updates"></a>Ondersteuning voor niet-verbonden apparaat-updates

In een transparant Gateway scenario kunnen een of meer apparaten hun berichten door geven via één gateway apparaat dat de verbinding met Azure IoT Hub onderhoudt. In dergelijke gevallen is het mogelijk dat de onderliggende apparaten geen verbinding met internet hebben of dat ze mogelijk geen inhoud van het Internet mogen downloaden. In de micro soft Connected cache preview IoT Edge-module wordt het apparaat bijgewerkt voor Azure IoT Hub klanten met de mogelijkheid van een intelligente in-Network cache, waarmee op installatie kopieën gebaseerde en op pakketten gebaseerde updates van op Linux-OS gebaseerde apparaten achter en IoT Edge gateway (downstream IoT-apparaten) kunnen worden IoT Hub bespaard.

## <a name="how-does-microsoft-connected-cache-preview-for-device-update-for-azure-iot-hub-work"></a>Hoe werkt micro soft verbonden cache Preview voor het bijwerken van apparaten met Azure IoT Hub?

Micro soft Connected cache is een intelligente, transparante cache voor inhoud die is gepubliceerd voor het bijwerken van apparaten voor Azure IoT Hub inhoud en kan worden aangepast aan het opslaan van inhoud van andere bronnen, zoals pakket opslagplaatsen. Micro soft Connected cache is een koude cache die door client aanvragen wordt geheten voor de exacte bestands bereiken die worden aangevraagd door de Delivery Optimization-client en geen pre-seeding inhoud heeft. In het diagram en de stapsgewijze beschrijving hieronder wordt uitgelegd hoe micro soft Connected cache werkt in de apparaat bijwerken voor Azure IoT Hub-infra structuur.

>[!Note]
>Bij het definiëren van deze stroom is ervan uitgegaan dat de IoT Edge gateway een Internet verbinding heeft. Voor het scenario downstream IoT Edge gateway (geneste rand) kan de ' Content Delivery Network ' (CDN) worden beschouwd als de MCC die worden gehost op de bovenliggende IoT Edge gateway.

  :::image type="content" source="media/connected-cache-overview/disconnected-device-update.png" alt-text="Update van niet-verbonden apparaat" lightbox="media/connected-cache-overview/disconnected-device-update.png":::

1. Micro soft Connected cache wordt geïmplementeerd als een IoT Edge module naar de on-premises server.
2. Apparaat-update voor Azure IoT Hub-clients zijn geconfigureerd voor het downloaden van inhoud van micro soft Connected cache door middel van het kenmerk GatewayHostName van het apparaat connection string voor IoT-Leaf-apparaten **of** parent_hostname ingesteld in het bestand config. toml voor IOT Edge onderliggende apparaten.
3. Update van het apparaat voor Azure IoT Hub-clients in beide gevallen ontvangen update-opdrachten voor het downloaden van inhoud van de update voor de Azure IoT Hub-service en het bijwerken van inhoud aan de met micro soft verbonden cache vragen in plaats van het CDN. Micro soft Connected cache is standaard geconfigureerd om te Luis teren op http-poort 80 en de Delivery Optimization-client maakt de inhouds aanvraag op poort 80 zodat het bovenliggende item moet worden geconfigureerd om op deze poort te Luis teren.  Op dit moment wordt alleen het HTTP-protocol ondersteund.
4. De micro soft Connected cache server downloadt inhoud van het CDN, zaait de lokale cache op schijf en levert de inhoud aan de update van het apparaat voor Azure IoT Hub client.
   
>[!Note]
>Wanneer u updates op basis van pakketten gebruikt, wordt de micro soft Connected cache-server door de beheerder geconfigureerd met de vereiste pakket-hostnaam.

5. Volgende aanvragen van andere updates voor Azure IoT Hub-clients voor dezelfde update-inhoud zijn nu afkomstig uit de cache en micro soft Connected cache maakt geen aanvragen voor dezelfde inhoud aan het CDN.

### <a name="supporting-industrial-iot-iiot-with-parentchild-hosting-scenarios"></a>Industriële IoT (IIoT) ondersteunen met scenario's voor het hosten van bovenliggende en onderliggende toepassingen

Wanneer een downstream-of onderliggend IoT Edge gateway als host fungeert voor de micro soft Connected cache-server, wordt deze geconfigureerd om update-inhoud aan te vragen bij de bovenliggende IoT Edge gateway, waarbij de micro soft Connected cache-server wordt gehost. Dit is vereist voor zo veel niveaus als nodig is voordat de bovenliggende IoT Edge gateway die als host fungeert voor een micro soft Connected cache-server met Internet toegang wordt bereikt. Vanaf de met internet verbonden server wordt de inhoud aangevraagd vanuit het CDN waarop de inhoud wordt teruggestuurd naar de onderliggende IoT Edge gateway die de inhoud oorspronkelijk heeft aangevraagd. De inhoud wordt op elk niveau op schijf opgeslagen.

## <a name="access-to-the-microsoft-connected-cache-preview-for-device-update-for-azure-iot-hub"></a>Toegang tot de door micro soft verbonden cache Preview voor het bijwerken van apparaten voor Azure IoT Hub

De micro soft Connected cache IoT Edge-module wordt uitgebracht als een preview voor klanten die oplossingen implementeren met behulp van de update van het apparaat voor Azure IoT Hub. Toegang tot de preview is per uitnodiging. [Vraag toegang](https://aka.ms/MCCForDeviceUpdateForIoT) aan tot de micro soft Connected cache Preview voor het bijwerken van updates voor IOT uitschakelen en geef de gevraagde informatie op als u toegang wilt tot de module.

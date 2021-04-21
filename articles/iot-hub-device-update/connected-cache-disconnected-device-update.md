---
title: Meer informatie over ondersteuning voor niet-verbonden apparaatupdates met Microsoft Verbonden cache | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Meer informatie over ondersteuning voor niet-verbonden apparaatupdates met Microsoft Verbonden cache
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: e216d42ff1f279d87e657126514fcfb50960f806
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811900"
---
# <a name="understand-support-for-disconnected-device-updates"></a>Ondersteuning voor niet-verbonden apparaatupdates begrijpen

In een scenario met een transparante gateway kunnen een of meer apparaten hun berichten doorgeven via één gatewayapparaat dat de verbinding met de Azure IoT Hub. In dergelijke gevallen hebben de onderliggende apparaten mogelijk geen internetverbinding of mogen ze mogelijk geen inhoud van internet downloaden. De module Microsoft Verbonden cache Preview IoT Edge biedt apparaatupdates voor Azure IoT Hub-klanten met de mogelijkheid van een intelligente in-netwerkcache, waarmee updates op basis van afbeeldingen en pakketten van linux-besturingssysteemapparaten achter en IoT Edge-gateway (downstream-IoT-apparaten) mogelijk zijn en waarmee ook bandbreedte wordt besparen voor apparaatupdates voor Azure IoT Hub-klanten.

## <a name="how-does-microsoft-connected-cache-preview-for-device-update-for-azure-iot-hub-work"></a>Hoe werkt Microsoft Verbonden cache preview voor apparaatupdates Azure IoT Hub werken?

Microsoft Verbonden cache Preview is een intelligente, transparante cache voor inhoud die is gepubliceerd voor Apparaatupdate voor Azure IoT Hub-inhoud en kan worden aangepast om inhoud uit andere bronnen, zoals pakketopslagopslag, in de cache op te nemen. Microsoft Verbonden cache is een koude cache die wordt opgewarmd door clientaanvragen voor de exacte bestandsbereiken die zijn aangevraagd door de Delivery Optimization-client en die geen inhoud vooraf seedt. In het diagram en de stapsgewijle beschrijving hieronder wordt uitgelegd hoe Microsoft Verbonden cache werkt in de apparaatupdate voor Azure IoT Hub infrastructuur.

>[!Note]
>Bij het definiëren van deze stroom wordt ervan uitgegaan dat de IoT Edge gateway internetverbinding heeft. Voor het downstream IoT Edge-gatewayscenario (Nested Edge) kan 'Content Delivery Network' (CDN) worden beschouwd als de MCC die wordt gehost op de bovenliggende IoT Edge gateway.

  :::image type="content" source="media/connected-cache-overview/disconnected-device-update.png" alt-text="Apparaatupdate is verbroken" lightbox="media/connected-cache-overview/disconnected-device-update.png":::

1. Microsoft Verbonden cache wordt geïmplementeerd als een IoT Edge-module op de on-prem-server.
2. Apparaatupdates voor Azure IoT Hub-clients zijn geconfigureerd voor het downloaden van inhoud van Microsoft Verbonden cache op basis van het kenmerk GatewayHostName van het apparaat connection string voor IoT Leaf-apparaten OF **parent_hostname** ingesteld in config.toml voor onderliggende IoT Edge-apparaten.
3. Apparaatupdate voor Azure IoT Hub-clients ontvangen in beide gevallen opdrachten voor het downloaden van update-inhoud van de apparaatupdate voor Azure IoT Hub-service en vragen update-inhoud aan bij microsoft Verbonden cache in plaats van het CDN. Microsoft Verbonden cache is standaard geconfigureerd om te luisteren op HTTP-poort 80 en de Delivery Optimization-client doet de inhoudsaanvraag op poort 80, zodat de bovenliggende poort moet worden geconfigureerd om te luisteren op deze poort.  Op dit moment wordt alleen het HTTP-protocol ondersteund.
4. De Microsoft Verbonden cache-server downloadt inhoud van het CDN, verwerkt de lokale cache die op schijf is opgeslagen en levert de inhoud aan de apparaatupdate voor Azure IoT Hub client.
   
>[!Note]
>Wanneer u updates op basis van pakketten gebruikt, wordt de Microsoft Verbonden cache-server geconfigureerd door de beheerder met de vereiste hostnaam van het pakket.

5. Volgende aanvragen van andere apparaatupdates voor Azure IoT Hub-clients voor dezelfde update-inhoud zijn nu afkomstig uit de cache en Microsoft Verbonden cache zal geen aanvragen indienen bij het CDN voor dezelfde inhoud.

### <a name="supporting-industrial-iot-iiot-with-parentchild-hosting-scenarios"></a>IIoT (Industrial IoT) ondersteunen met bovenliggende/onderliggende hostingscenario's

Wanneer een downstream- of onderliggende IoT Edge-gateway de Microsoft Verbonden cache-server host, wordt deze geconfigureerd om update-inhoud aan te vragen bij de bovenliggende IoT Edge-gateway, waar de Microsoft Verbonden cache-server wordt gehost. Dit is vereist voor zoveel niveaus als nodig is voordat u de bovenliggende IoT Edge gateway bereikt die als host Verbonden cache Microsoft-server met internettoegang. Vanaf de met internet verbonden server wordt de inhoud aangevraagd bij het CDN. Op dat moment wordt de inhoud terug geleverd aan de onderliggende IoT Edge-gateway die de inhoud oorspronkelijk heeft aangevraagd. De inhoud wordt op elk niveau op schijf opgeslagen.

## <a name="access-to-the-microsoft-connected-cache-preview-for-device-update-for-azure-iot-hub"></a>Toegang tot de Microsoft Verbonden cache preview voor apparaatupdates voor Azure IoT Hub

De Microsoft Verbonden cache IoT Edge-module wordt uitgebracht als preview-versie voor klanten die oplossingen implementeren met apparaatupdates voor Azure IoT Hub. Toegang tot de preview is via een uitnodiging. [Vraag toegang](https://aka.ms/MCCForDeviceUpdateForIoT) tot de Microsoft Verbonden cache Preview voor apparaatupdate voor IoT- en geef de gevraagde informatie op als u toegang wilt tot de module.

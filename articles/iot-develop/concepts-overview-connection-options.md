---
title: Meer informatie over verbindings opties voor Azure IoT-apparaat-ontwikkel aars
description: Meer informatie over veelgebruikte verbindings opties voor apparaten en hulpprogram ma's voor ontwikkel aars van Azure IoT-apparaten.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: 6bbd7d37418af68065daa194d4ff4bd80f6fd09c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100654154"
---
# <a name="overview-connection-options-for-azure-iot-device-developers"></a>Overzicht: verbindings opties voor Azure IoT-apparaat-ontwikkel aars
Als ontwikkelaar die met apparaten werkt, hebt u verschillende mogelijkheden om verbinding te maken met Azure IoT-apparaten en deze te beheren. In dit artikel vindt u een overzicht van de meest gebruikte opties en hulpprogram ma's voor het maken en beheren van apparaten.

Wanneer u Azure IoT-verbindings opties voor uw apparaten evalueert, is het handig om de volgende items te vergelijken:
- Platform toepassings platforms voor Azure IoT
- Hulpprogram ma's voor het verbinden en beheren van apparaten

## <a name="application-platforms-iot-central-and-iot-hub"></a>Toepassings platforms: IoT Central en IoT Hub
Azure IoT bevat twee services die platformen zijn voor Cloud toepassingen die geschikt zijn voor apparaten: Azure IoT Central en Azure IoT Hub. U kunt één van beide gebruiken om een IoT-toepassing te hosten en apparaten te verbinden.
- [IOT Central](../iot-central/core/overview-iot-central.md) is ontworpen om de complexiteit en kosten van het werken met IOT-oplossingen te verminderen. Het is een SaaS-toepassing (Software-as-a-Service) die een volledig platform biedt voor het hosten van IoT-toepassingen. U kunt de webgebruikersinterface van IoT Central gebruiken om de hele levens cyclus van het maken en beheren van IoT-toepassingen te stroom lijnen. De Web-UI vereenvoudigt de taken voor het maken van toepassingen en het maken en beheren van een aantal tot miljoenen apparaten. IoT Central gebruikt IoT Hub om toepassingen te maken en te beheren, maar blijft de details zichtbaar voor de gebruiker. Over het algemeen biedt IoT Central minder complexiteits-en ontwikkelings inspanningen en vereenvoudigd Apparaatbeheer via de webgebruikersinterface.
- [IOT hub](../iot-hub/about-iot-hub.md) fungeert als een centrale Message hub voor bidirectionele communicatie tussen IOT-toepassingen en verbonden apparaten. Het is een PaaS-toepassing (platform-as-a-Service) die ook een platform biedt voor het hosten van IoT-toepassingen. Net als IoT Central kan het worden geschaald om miljoenen apparaten te ondersteunen. Over het algemeen biedt IoT Hub meer controle en aanpassing van het ontwerp van uw toepassing en meer opties voor ontwikkel aars voor het werken met de service, tegen de kosten van een toename in de complexiteit van ontwikkeling en beheer.

## <a name="tools-to-connect-and-manage-devices"></a>Hulpprogram ma's om apparaten te verbinden en te beheren
Nadat u IoT Hub of IoT Central hebt geselecteerd om uw IoT-toepassing te hosten, kunt u kiezen uit verschillende hulpprogram ma's voor ontwikkel aars. U kunt deze hulpprogram ma's gebruiken om verbinding te maken met het geselecteerde IoT-toepassings platform en om toepassingen en apparaten te creëren en te beheren. De volgende tabel bevat een overzicht van de algemene opties voor het hulp programma. 

> [!NOTE]
> Naast de volgende hulpprogram ma's kunt u via programma code IoT-toepassingen maken en beheren met behulp van REST API, Azure Sdk's of Azure Resource Manager sjablonen. Meer informatie vindt u in de documentatie van [IOT hub](../iot-hub/about-iot-hub.md) en [IOT Central](../iot-central/core/overview-iot-central.md) service.

|Hulpprogramma  |Ondersteunt IoT-platform &nbsp; &nbsp; &nbsp;&nbsp; |Documentatie  |Description  |
|---------|---------|---------|---------|
|Centrale Web-UI     | Midden | [Centrale Snelstartgids](../iot-central/core/quick-deploy-iot-central.md) | Portal op basis van een browser voor IoT Central. |
|Azure Portal     | Hub, centraal      | [Een IOT-hub maken met Azure Portal](../iot-hub/iot-hub-create-through-portal.md), [IOT Central beheren vanuit de Azure Portal](../iot-central/core/howto-manage-iot-central-from-portal.md)| Portal op basis van een browser voor IoT Hub en apparaten. Werkt ook met andere Azure-resources, waaronder IoT Central. |
|Azure CLI     | Hub, centraal          | [Een IOT-hub maken met CLI](../iot-hub/iot-hub-create-using-cli.md), [IOT Central beheren vanuit Azure cli](../iot-central/core/howto-manage-iot-central-from-cli.md) | Opdracht regel interface voor het maken en beheren van IoT-toepassingen. |
|Azure PowerShell     | Hub, centraal   | [Een IOT-hub maken met Power shell](../iot-hub/iot-hub-create-using-powershell.md), [IoT Central beheren vanuit Azure PowerShell](../iot-central/core/howto-manage-iot-central-from-powershell.md) | Power shell-interface voor het maken en beheren van IoT-toepassingen |
|Azure IoT Tools voor VS Code  | Hub | [Een IoT-hub maken met Hulpprogram Ma's voor VS code](../iot-hub/iot-hub-create-use-iot-toolkit.md) | VS-code-extensie voor IoT Hub toepassingen. |
|Azure IoT Explorer     | Hub | [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer) | Kan IoT-hubs niet maken. Maakt verbinding met een bestaande IoT-hub voor het beheren van apparaten. Vaak gebruikt met CLI of portal.|

## <a name="next-steps"></a>Volgende stappen
Meer informatie over de opties voor het verbinden van apparaten met Azure IoT vindt u in de volgende Quick starts:
- IoT Central: [een Azure IOT Central-toepassing maken](../iot-central/core/quick-deploy-iot-central.md)
- IoT Hub: [telemetrie van een apparaat naar een IOT-hub verzenden en dit controleren met de Azure cli](../iot-hub/quickstart-send-telemetry-cli.md)
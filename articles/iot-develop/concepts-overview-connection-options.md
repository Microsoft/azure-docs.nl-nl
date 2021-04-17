---
title: Meer informatie over verbindingsopties voor ontwikkelaars van Azure IoT-apparaten
description: Meer informatie over veelgebruikte verbindingsopties en hulpprogramma's voor Azure IoT-apparaatontwikkelaars.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: 8669919192b1e6394043842d7d23f8829ec7c71e
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589547"
---
# <a name="overview-connection-options-for-azure-iot-device-developers"></a>Overzicht: Verbindingsopties voor ontwikkelaars van Azure IoT-apparaten
Als ontwikkelaar die met apparaten werkt, hebt u verschillende opties voor het verbinden en beheren van Azure IoT-apparaten. In dit artikel vindt u een overzicht van de meestgebruikte opties en hulpprogramma's waarmee u apparaten kunt verbinden en beheren.

Wanneer u Azure IoT-verbindingsopties voor uw apparaten evalueert, is het handig om de volgende items te vergelijken:
- Toepassingsplatforms voor Azure IoT-apparaten
- Hulpprogramma's voor het verbinden en beheren van apparaten

## <a name="application-platforms-iot-central-and-iot-hub"></a>Toepassingsplatforms: IoT Central en IoT Hub
Azure IoT bevat twee services die platforms zijn voor cloudtoepassingen met apparaatmogelijkheden: Azure IoT Central en Azure IoT Hub. U kunt beide gebruiken om een IoT-toepassing te hosten en apparaten te verbinden.
- [IoT Central](../iot-central/core/overview-iot-central.md) is ontworpen om de complexiteit en kosten van het werken met IoT-oplossingen te verminderen. Het is een SaaS-toepassing (Software-as-a-Service) die een volledig platform biedt voor het hosten van IoT-toepassingen. U kunt de IoT Central webinterface gebruiken om de volledige levenscyclus van het maken en beheren van IoT-toepassingen te stroomlijnen. De webinterface vereenvoudigt de taken voor het maken van toepassingen en het verbinden en beheren van enkele tot miljoenen apparaten. IoT Central maakt gebruik IoT Hub voor het maken en beheren van toepassingen, maar houdt de details transparant voor de gebruiker. Over het algemeen IoT Central minder complexiteit en ontwikkeling en vereenvoudigd apparaatbeheer via de webinterface.
- [IoT Hub](../iot-hub/about-iot-hub.md) fungeert als een centrale berichtenhub voor bi-directionele communicatie tussen IoT-toepassingen en verbonden apparaten. Het is een PaaS-toepassing (Platform-as-a-Service) die ook een platform biedt voor het hosten van IoT-toepassingen. Net IoT Central kan het worden geschaald om miljoenen apparaten te ondersteunen. Over het algemeen biedt IoT Hub meer controle en aanpassing van uw toepassingsontwerp en meer opties voor ontwikkelhulpprogramma's voor het werken met de service, ten koste van een toename van de complexiteit van ontwikkeling en beheer.

## <a name="tools-to-connect-and-manage-devices"></a>Hulpprogramma's voor het verbinden en beheren van apparaten
Nadat u een IoT Hub of IoT Central voor het hosten van uw IoT-toepassing hebt geselecteerd, hebt u verschillende opties voor ontwikkelhulpprogramma's. U kunt deze hulpprogramma's gebruiken om verbinding te maken met het geselecteerde IoT-toepassingsplatform en om toepassingen en apparaten te maken en beheren. De volgende tabel bevat een overzicht van algemene opties voor hulpprogramma's. 

> [!NOTE]
> Naast de volgende hulpprogramma's kunt u programmatisch IoT-toepassingen maken en beheren met behulp van REST API-, Azure-SDK's of Azure Resource Manager-sjablonen. Meer informatie vindt u in de [documentatie IoT Hub](../iot-hub/about-iot-hub.md) [en IoT Central](../iot-central/core/overview-iot-central.md) service.

|Hulpprogramma  |Ondersteunt het IoT-platform &nbsp; &nbsp; &nbsp;&nbsp; |Documentatie  |Description  |
|---------|---------|---------|---------|
|Centrale webinterface     | Midden | [Centrale snelstart](../iot-central/core/quick-deploy-iot-central.md) | Browserportal voor IoT Central. |
|Azure Portal     | Hub, Centraal      | [Een IoT-hub maken met Azure Portal](../iot-hub/iot-hub-create-through-portal.md), [IoT Central beheren vanuit de Azure Portal](../iot-central/core/howto-manage-iot-central-from-portal.md)| Browserportal voor IoT Hub en apparaten. Werkt ook met andere Azure-resources, waaronder IoT Central. |
|Azure IoT Explorer     | Hub | [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer#azure-iot-explorer-preview) | Kan geen IoT-hubs maken. Maakt verbinding met een bestaande IoT-hub om apparaten te beheren. Wordt vaak gebruikt met CLI of portal.|
|Azure CLI     | Hub, Centraal          | [Een IoT-hub maken met CLI,](../iot-hub/iot-hub-create-using-cli.md) [IoT Central beheren vanuit Azure CLI](../iot-central/core/howto-manage-iot-central-from-cli.md) | Opdrachtregelinterface voor het maken en beheren van IoT-toepassingen. |
|Azure PowerShell     | Hub, Centraal   | [Een IoT-hub maken met PowerShell,](../iot-hub/iot-hub-create-using-powershell.md) [IoT Central beheren vanuit Azure PowerShell](../iot-central/core/howto-manage-iot-central-from-powershell.md) | PowerShell-interface voor het maken en beheren van IoT-toepassingen |
|Azure IoT Tools voor VS Code  | Hub | [Een IoT-hub maken met Hulpprogramma's voor VS Code](../iot-hub/iot-hub-create-use-iot-toolkit.md) | VS Code-extensie voor IoT Hub toepassingen. |

## <a name="next-steps"></a>Volgende stappen
Bekijk de volgende quickstarts voor meer informatie over uw opties voor het verbinden van apparaten met Azure IoT:
- IoT Central: Een [Azure IoT Central maken](../iot-central/core/quick-deploy-iot-central.md)
- IoT Hub: [Telemetrie vanaf een apparaat verzenden naar een IoT-hub en bewaken met de Azure CLI](../iot-hub/quickstart-send-telemetry-cli.md)
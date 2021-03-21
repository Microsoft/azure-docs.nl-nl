---
title: Azure IoT Hub-service en resource Health controleren | Microsoft Docs
description: Azure Service Health en Azure Resource Health gebruiken om uw IoT Hub te bewaken
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: robinsh
ms.custom:
- amqp
- 'Role: Cloud Development'
- 'Role: Technical Support'
- devx-track-csharp
ms.openlocfilehash: 27fca7b76ab148fc355eb7d52ee0cbcbd3540458
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92548486"
---
# <a name="check-iot-hub-service-and-resource-health"></a>Controleren IoT Hub service en resource status

Azure IoT Hub integreert met de [Azure service Health-Service](../service-health/overview.md) om u de mogelijkheid te bieden om de status op service niveau van de IOT hub-service en afzonderlijke IOT-hubs te bewaken. U kunt ook waarschuwingen instellen om te worden gewaarschuwd wanneer de status van de IoT Hub service of een IoT-hub wordt gewijzigd. De Azure Service Health-Service is een combi natie van drie kleinere Services: Azure Resource Health, Azure Service Health en de Azure-status pagina. In de secties in dit artikel worden de verschillende services en de bijbehorende relaties beschreven om meer details te IoT Hub.

Azure Service Health-Service helpt u bij het bewaken van gebeurtenissen op service niveau zoals storingen en upgrades die van invloed kunnen zijn op de beschik baarheid van de IoT Hub-service en uw afzonderlijke IoT-hubs. IoT Hub integreert ook met Azure Monitor om IoT Hub platform metrieken en IoT Hub bron logboeken te bieden die u kunt gebruiken voor het bewaken van operationele fouten en voor waarden die optreden op een specifieke IoT-hub. Zie [Azure IoT Hub controleren](monitor-iot-hub.md) voor meer informatie.

## <a name="check-health-of-an-iot-hub-with-azure-resource-health"></a>Controleer de status van een IoT-hub met Azure Resource Health

Azure Resource Health maakt deel uit van Azure Service Health Service die de status van afzonderlijke resources bijhoudt. U kunt de integriteits status van uw IoT-hub rechtstreeks vanuit de portal controleren.

Ga als volgt te werk om de geschiedenis van de status en de status van uw IoT-hub met behulp van de portal te bekijken:

1. Ga in [Azure Portal](https://portal.azure.com)naar uw IOT-hub in azure Portal.

1. Selecteer **resource Health** in het linkerdeel venster onder **ondersteuning en probleem oplossing**.

    :::image type="content" source="./media/iot-hub-azure-service-health-integration/iot-hub-resource-health.png" alt-text="Resource status pagina voor een IoT-hub":::

Zie [resource Health Overview](../service-health/resource-health-overview.md) in de Azure service Health documentatie voor meer informatie over Azure resource Health en het interpreteren van status gegevens.

U kunt ook **resource Health alert toevoegen** selecteren om waarschuwingen in te stellen om te activeren wanneer de integriteits status van uw IOT-hub wordt gewijzigd. Zie [waarschuwingen configureren voor service status gebeurtenissen](../service-health/alerts-activity-log-service-notifications-portal.md) en verwante onderwerpen in de Azure service Health documentatie voor meer informatie.

## <a name="check-health-of-iot-hubs-in-your-subscription-with-azure-service-health"></a>Controleer de status van IoT-hubs in uw abonnement met Azure Service Health

Met Azure Service Health kunt u de integriteits status van alle IoT-hubs in uw abonnement controleren.

Voer de volgende stappen uit om de status van uw IoT-hubs te controleren:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Navigeer naar **service Health**  >  **resource Health**.

3. Selecteer uw abonnement in de vervolg keuzelijsten en selecteer vervolgens **IOT hub** als resource type.

Zie [service Health Overview](../service-health/service-health-overview.md) in de Azure service Health documentatie voor meer informatie over Azure service Health en het interpreteren van status gegevens.

Zie [waarschuwingen configureren voor service status gebeurtenissen](../service-health/alerts-activity-log-service-notifications-portal.md) en verwante onderwerpen in de Azure service Health documentatie voor meer informatie over het instellen van waarschuwingen met Azure service health.

## <a name="check-health-of-the-iot-hub-service-by-region-on-azure-status-page"></a>Controleer de status van de IoT Hub-service per regio op de Azure-status pagina

Als u de status van IoT Hub en andere services per regio wereld wijd wilt bekijken, kunt u de [Azure-status pagina](https://status.azure.com/status)gebruiken. Zie [overzicht van Azure-status](../service-health/azure-status-overview.md) in de Azure service Health-documentatie voor meer informatie over de Azure-status pagina.

## <a name="next-steps"></a>Volgende stappen

* Zie [Azure service Health-Service](../service-health/overview.md) voor meer informatie over de pagina Azure Service Health, Azure resource Health en Azure status.
* Zie [azure IOT hub bewaken](monitor-iot-hub.md) voor een beschrijving van het controleren van Azure IOT hub.

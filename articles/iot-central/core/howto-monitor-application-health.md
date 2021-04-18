---
title: De status van een Azure IoT Central toepassing | Microsoft Docs
description: Als operator of beheerder controleert u de algehele status van de apparaten die zijn verbonden met uw IoT Central toepassing.
author: dominicbetts
ms.author: dobett
ms.date: 01/27/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: df89d53e6b5043c1ef3caa1c92f2abaae542d6ec
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599006"
---
# <a name="monitor-the-overall-health-of-an-iot-central-application"></a>De algehele status van een IoT Central bewaken

> [!NOTE]
> Metrische gegevens zijn alleen beschikbaar voor versie 3 IoT Central toepassingen. Zie Over uw toepassing voor meer informatie over het controleren [van de versie van uw toepassing.](./howto-get-app-info.md)

*Dit artikel is van toepassing op operators en beheerders.*

In dit artikel leert u hoe u de set met metrische gegevens van IoT Central kunt gebruiken om de status te beoordelen van apparaten die zijn verbonden met uw IoT Central-toepassing en de status van uw export van gegevens.

Metrische gegevens zijn standaard ingeschakeld voor uw IoT Central toepassing en u kunt ze openen vanuit [Azure Portal](https://portal.azure.com/). Het [Azure Monitor gegevensplatform maakt deze metrische](../../azure-monitor/essentials/data-platform-metrics.md) gegevens beschikbaar en biedt verschillende manieren om metrische gegevens te gebruiken. U kunt bijvoorbeeld grafieken gebruiken in de Azure Portal, een REST API of query's in PowerShell of de Azure CLI.

### <a name="trial-applications"></a>Proeftoepassingen

Toepassingen die gebruikmaken van het gratis proefabonnement hebben geen gekoppeld Azure-abonnement en bieden dus geen ondersteuning voor Azure Monitor metrische gegevens. U kunt [een toepassing converteren naar een standaardprijsplan](./howto-view-bill.md#move-from-free-to-standard-pricing-plan) en toegang krijgen tot deze metrische gegevens.

## <a name="view-metrics-in-the-azure-portal"></a>Metrische gegevens weergeven in de Azure Portal

In de volgende stappen wordt ervan uitgenomen dat [u een IoT Central hebt](./quick-deploy-iot-central.md) met een aantal verbonden [apparaten](./tutorial-connect-device.md) of een [gegevensexport die wordt uitgevoerd.](howto-export-data.md)

Metrische gegevens IoT Central weergeven in de portal:

1. Navigeer naar IoT Central toepassingsresource in de portal. Standaard bevinden IoT Central zich in een resourcegroep met de naam **IOTC.**
1. Als u een grafiek wilt maken op basis van de metrische gegevens van uw toepassing, selecteert u **Metrische** gegevens in de **sectie** Bewaking.

![Metrische gegevens van Azure](media/howto-monitor-application-health/metrics.png)

### <a name="azure-portal-permissions"></a>Azure Portal machtigingen

Toegang tot metrische gegevens in Azure Portal wordt beheerd door [op rollen gebaseerd toegangsbeheer van Azure.](../../role-based-access-control/overview.md) Gebruik de Azure Portal om gebruikers toe te voegen aan IoT Central toepassing,resourcegroep/abonnement om hen toegang te verlenen. U moet een gebruiker in de portal toevoegen, zelfs als deze al is toegevoegd aan de IoT Central toepassing. Gebruik [ingebouwde Azure-rollen](../../role-based-access-control/built-in-roles.md) voor fijner toegangsbeheer.

## <a name="iot-central-metrics"></a>IoT Central metrische gegevens

Zie Ondersteunde metrische gegevens met Azure Monitor voor een lijst met metrische gegevens die momenteel beschikbaar zijn voor [IoT Central.](../../azure-monitor/essentials/metrics-supported.md#microsoftiotcentraliotapps)

### <a name="metrics-and-invoices"></a>Metrische gegevens en facturen

Metrische gegevens kunnen verschillen van de getallen die worden weergegeven op uw Azure IoT Central factuur. Deze situatie doet zich voor om een aantal redenen, zoals:

- IoT Central [standard-prijsplannen](https://azure.microsoft.com/pricing/details/iot-central/) bevatten twee apparaten en gratis verschillende berichtquota. Hoewel de gratis items worden uitgesloten van facturering, worden ze nog steeds meegetelde in de metrische gegevens.

- IoT Central wordt automatisch één testapparaat-id gegenereerd voor elke apparaatsjabloon in de toepassing. Deze apparaat-id is zichtbaar op de **pagina Testapparaat beheren** voor een apparaatsjabloon. Oplossingsbouwers kunnen ervoor kiezen om hun apparaatsjablonen te valideren voordat ze worden gepubliceerd door code te genereren die gebruikmaakt van deze testapparaat-ID's. Hoewel deze apparaten worden uitgesloten van facturering, worden ze nog steeds meegetelde in de metrische gegevens.

- Hoewel metrische gegevens een subset van apparaat-naar-cloud-communicatie kunnen tonen, telt alle communicatie tussen het apparaat en de cloud als een bericht [voor facturering](https://azure.microsoft.com/pricing/details/iot-central/).

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u toepassingssjablonen gebruikt, is de voorgestelde volgende stap om te leren hoe u IoT Central beheren vanuit [de Azure Portal](howto-manage-iot-central-from-portal.md).
---
title: De status van een Azure IoT Central-toepassing controleren | Microsoft Docs
description: Als operator of beheerder bewaakt u de algehele status van de apparaten die zijn verbonden met uw IoT Central-toepassing.
author: dominicbetts
ms.author: dobett
ms.date: 05/14/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 1fb17d56c546511fee291f30b103a5310c8015cc
ms.sourcegitcommit: b8a175b6391cddd5a2c92575c311cc3e8c820018
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96122268"
---
# <a name="monitor-the-overall-health-of-the-devices-connected-to-an-iot-central-application"></a>De algemene status van de apparaten die zijn verbonden met een IoT Central-toepassing bewaken

> [!NOTE]
> Metrische gegevens zijn alleen beschikbaar voor versie 3-IoT Central toepassingen. Zie [over uw toepassing](./howto-get-app-info.md)voor meer informatie over het controleren van de versie van uw toepassing.

*Dit artikel is van toepassing op Opera tors en beheerders.*

In dit artikel leert u hoe u de set metrische gegevens van IoT Central kunt gebruiken om de algemene status van de apparaten die zijn verbonden met uw IoT Central-toepassing te beoordelen.

Metrische gegevens zijn standaard ingeschakeld voor uw IoT Central-toepassing en u opent deze vanuit de [Azure Portal](https://portal.azure.com/). Het [Azure monitor data platform geeft deze metrische gegevens](../../azure-monitor/platform/data-platform-metrics.md) weer en biedt verschillende manieren waarop u ermee kunt werken. U kunt bijvoorbeeld grafieken gebruiken in de Azure Portal, een REST API of query's in Power shell of de Azure CLI.

### <a name="trial-applications"></a>Proef toepassingen

Toepassingen die gebruikmaken van het gratis proef abonnement hebben geen bijbehorend Azure-abonnement en ondersteunen dus geen Azure Monitor metrische gegevens. U kunt [een toepassing converteren naar een Standard-prijs plan](./howto-view-bill.md#move-from-free-to-standard-pricing-plan) en toegang krijgen tot deze metrische gegevens.

## <a name="view-metrics-in-the-azure-portal"></a>Metrische gegevens weer geven in de Azure Portal

Bij de volgende stappen wordt ervan uitgegaan dat u een [IOT Central toepassing](./quick-deploy-iot-central.md) hebt met een aantal [verbonden apparaten](./tutorial-connect-device.md).

IoT Central metrische gegevens weer geven in de portal:

1. Navigeer naar uw IoT Central-toepassings resource in de portal. IoT Central resources bevinden zich standaard in een resource groep met de naam **IOTC**.
1. Als u een grafiek wilt maken op basis van de metrische gegevens van uw toepassing, selecteert u **metrische gegevens** in de sectie **bewaking** .

![Metrische gegevens van Azure](media/howto-monitor-application-health/metrics.png)

### <a name="azure-portal-permissions"></a>Azure Portal machtigingen

Toegang tot metrische gegevens in het Azure Portal wordt beheerd door [Azure Role based Access Control](../../role-based-access-control/overview.md). Gebruik de Azure Portal om gebruikers toe te voegen aan de IoT Central toepassing/resource groep/abonnement om ze toegang te verlenen. U moet een gebruiker toevoegen in de portal, zelfs als deze al aan de IoT Central-toepassing zijn toegevoegd. Gebruik [ingebouwde rollen van Azure](../../role-based-access-control/built-in-roles.md) voor een nauw keurig toegangs beheer.

## <a name="iot-central-metrics"></a>IoT Central metrische gegevens

Zie [ondersteunde metrische gegevens met Azure monitor](../../azure-monitor/platform/metrics-supported.md#microsoftiotcentraliotapps)voor een lijst met metrische gegevens die momenteel beschikbaar zijn voor IOT Central.

### <a name="metrics-and-invoices"></a>Metrische gegevens en facturen

Metrische gegevens kunnen verschillen van de getallen die worden weer gegeven op uw Azure IoT Central-factuur. Deze situatie doet zich voor een aantal redenen voor:

- IoT Central [Standard-prijs abonnementen](https://azure.microsoft.com/pricing/details/iot-central/) zijn twee apparaten en zijn gratis voor verschillende bericht quota's. Terwijl de gratis artikelen worden uitgesloten van facturering, worden ze nog steeds geteld in de metrische gegevens.

- IoT Central genereert automatisch één test apparaat-ID voor elke apparaatprofiel in de toepassing. Deze apparaat-ID wordt weer gegeven op de pagina **test apparaat beheren** voor een sjabloon. Oplossingen bouwers kunnen ervoor kiezen om [hun Device-sjablonen te valideren](./overview-iot-central.md#create-device-templates) voordat ze worden gepubliceerd door code te genereren die gebruikmaakt van deze test apparaat-id's. Hoewel deze apparaten worden uitgesloten van facturering, worden ze nog steeds geteld in de metrische gegevens.

- Hoewel bij metrische gegevens een subset van apparaat-naar-Cloud communicatie kan worden weer gegeven, telt alle communicatie tussen het apparaat en de Cloud [als een bericht voor facturering](https://azure.microsoft.com/pricing/details/iot-central/).

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u toepassings sjablonen kunt gebruiken, is de voorgestelde volgende stap informatie over het [beheren van IOT Central van de Azure Portal](howto-manage-iot-central-from-portal.md)
---
title: IoT Central beheren via de Azure Portal | Microsoft Docs
description: In dit artikel wordt beschreven hoe u uw IoT Central-toepassingen kunt maken en beheren via de Azure Portal.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2020
ms.topic: how-to
manager: philmea
ms.openlocfilehash: 2af97206db00d683ab409710bc71a3b5048bf6ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "104658462"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>IoT Central beheren via de Azure Portal

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

In plaats van IoT Central-toepassingen te maken en te beheren op de website van [Azure IOT Central Application Manager](https://aka.ms/iotcentral) , kunt u de [Azure Portal](https://portal.azure.com) gebruiken om uw toepassingen te beheren.

## <a name="create-iot-central-applications"></a>IoT Central-toepassingen maken

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]


Als u een toepassing wilt maken, gaat u naar de [Azure Portal](https://ms.portal.azure.com) en selecteert u **een resource maken**.

In **de Marketplace** -balk zoeken, typt u *IOT Central*:

![Beheer Portal: zoeken](media/howto-manage-iot-central-from-portal/image0a1.png)

Selecteer de tegel **IOT Central toepassing** in de zoek resultaten:

![Beheerportal: Zoek resultaten](media/howto-manage-iot-central-from-portal/image0b1.png)

Selecteer nu **maken**:

![Beheer Portal: IoT Central resource](media/howto-manage-iot-central-from-portal/image0c1.png)

Vul alle velden in het formulier in. Dit formulier is vergelijkbaar met het formulier dat u invult om toepassingen te maken op de [Azure IOT Central Application Manager](https://aka.ms/iotcentral) -website. Zie de Snelstartgids [een IOT Central-toepassing maken](quick-deploy-iot-central.md) voor meer informatie.

![IoT Central formulier maken](media/howto-manage-iot-central-from-portal/image6a.png)

De **locatie** is de [geografie](https://azure.microsoft.com/global-infrastructure/geographies/) waar u de toepassing wilt maken. Gewoonlijk kiest u de locatie die zich het dichtst in de buurt van uw apparaten bevindt om de beste prestaties te verkrijgen. Azure IoT Central is momenteel beschikbaar in de geografi **Australia**, **Azië en Stille Oceaan**, **Europa**, **Verenigde Staten**, het **Verenigd Konink rijk** en **Japan** . Wanneer u een locatie hebt gekozen, kunt u de toepassing later niet meer naar een andere locatie verplaatsen.

Nadat u alle velden hebt ingevuld, selecteert u **maken**.

## <a name="manage-existing-iot-central-applications"></a>Bestaande IoT Central-toepassingen beheren

Als u al een Azure IoT Central-toepassing hebt, kunt u deze verwijderen of verplaatsen naar een ander abonnement of een andere resource groep in de Azure Portal.

> [!NOTE]
> Voor toepassingen die zijn gemaakt met het *gratis* abonnement zijn geen Azure-abonnementen vereist en daarom vindt u deze niet in uw Azure-abonnement op de Azure Portal. U kunt alleen gratis apps uit de IoT Central Portal bekijken en beheren.

Selecteer **alle resources** in de portal om aan de slag te gaan. Selecteer **verborgen typen weer geven** en typ de naam van uw toepassing in **filteren op naam** om deze te zoeken. Selecteer vervolgens de IoT Central toepassing die u wilt beheren.

Als u naar de toepassing wilt navigeren, selecteert u de **IOT Central toepassings-URL**:

![Scherm opname van de pagina ' overzicht ' met de ' IoT Central-toepassings-URL ' gemarkeerd.](media/howto-manage-iot-central-from-portal/image3.png)

Als u de toepassing wilt verplaatsen naar een andere resource groep, selecteert u **wijzigen** naast de resource groep. Kies op de pagina **resources verplaatsen** de resource groep waar u deze toepassing naartoe wilt verplaatsen:

![Scherm opname waarin de pagina overzicht wordt weer gegeven met de ' resource groep (wijzigen) ' gemarkeerd.](media/howto-manage-iot-central-from-portal/image4a.png)

Als u de toepassing wilt verplaatsen naar een ander abonnement, selecteert u  **wijzigen** naast het abonnement. Kies op de pagina **resources verplaatsen** het abonnement waarnaar u deze toepassing wilt verplaatsen:

![Beheer Portal: resource beheer](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u Azure IoT Central-toepassingen kunt beheren vanuit de Azure Portal, volgt u de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Uw toepassing beheren](howto-administer.md)
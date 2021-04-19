---
title: Beheer IoT Central vanuit de Azure Portal | Microsoft Docs
description: In dit artikel wordt beschreven hoe u uw IoT Central maakt en beheert vanuit Azure Portal.
services: iot-central
ms.service: iot-central
author: vishwam
ms.author: vishwams
ms.date: 04/17/2021
ms.topic: how-to
manager: philmea
ms.openlocfilehash: ed65e85c7428bf59fe770534e97afdd53564086a
ms.sourcegitcommit: 089c2bd1ac4861f43c4b89396d3d056a6eef4913
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107601981"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Beheer IoT Central vanuit de Azure Portal

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

U kunt de [Azure Portal](https://portal.azure.com) gebruiken voor het maken en beheren van IoT Central-toepassingen, vergelijkbaar met de functionaliteit in IoT Central application manager van [IoT Central.](https://apps.azureiotcentral.com/myapps)

## <a name="create-iot-central-applications"></a>Een IoT Central maken

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

Als u een toepassing wilt maken, gaat u naar de pagina [Create IoT Central Application](https://ms.portal.azure.com/#create/Microsoft.IoTCentral) in Azure Portal en vult u het formulier in.

![Een IoT Central maken](media/howto-manage-iot-central-from-portal/image6a.png)

* **Resourcenaam** is een unieke naam die u kunt kiezen voor IoT Central toepassing in uw Azure-resourcegroep.

* **Toepassings-URL** is de URL die u kunt gebruiken voor toegang tot uw toepassing.

* De **locatie** is de [geografie](https://azure.microsoft.com/global-infrastructure/geographies/) waar u de toepassing wilt maken. Gewoonlijk kiest u de locatie die zich het dichtst in de buurt van uw apparaten bevindt om de beste prestaties te verkrijgen. Azure IoT Central is momenteel beschikbaar op de volgende locaties:
    * Azië en Stille Oceaan
    * Australië
    * Europa
    * Japan
    * Verenigd Koninkrijk
    * Verenigde Staten

  Zodra u een locatie hebt gekozen, kunt u uw toepassing later niet verplaatsen naar een andere locatie.

Nadat u alle velden invult, selecteert u **Maken.** Zie de quickstart Een IoT Central [maken voor meer](quick-deploy-iot-central.md) informatie.

## <a name="manage-existing-iot-central-applications"></a>Bestaande IoT Central beheren

Als u al een Azure IoT Central hebt, kunt u deze verwijderen of verplaatsen naar een ander abonnement of een andere resourcegroep in de Azure Portal.

> [!NOTE]
> Voor toepassingen die *zijn* gemaakt met behulp van het gratis abonnement zijn geen Azure-abonnementen vereist. Daarom vindt u deze niet in uw Azure-abonnement op de Azure Portal. U kunt gratis apps alleen bekijken en beheren vanuit de IoT Central portal.

Om aan de slag te gaan, zoekt u uw toepassing in de zoekbalk bovenaan de Azure Portal. U kunt ook al uw toepassingen weergeven door te zoeken naar 'IoT Central Applications' en de service te selecteren:

![Schermopname van de zoekresultaten voor 'IoT Central Applications' met de eerste service geselecteerd.](media/howto-manage-iot-central-from-portal/search-iot-central.png)

Wanneer u een toepassing in de zoekresultaten selecteert, ziet u Azure Portal overzicht ervan. U kunt naar de werkelijke toepassing navigeren door de toepassings-URL **IoT Central selecteren:**

![Schermopname van de pagina 'Overzicht' met de 'IoT Central Application URL' gemarkeerd.](media/howto-manage-iot-central-from-portal/image3.png)

Als u de toepassing wilt verplaatsen naar een andere resourcegroep, selecteert **u Wijzigen** naast de resourcegroep. Kies op **de pagina Resources** verplaatsen de resourcegroep waar u deze toepassing naar wilt verplaatsen:

![Schermopname van de pagina Overzicht met de 'Resourcegroep (wijzigen)' gemarkeerd.](media/howto-manage-iot-central-from-portal/image4a.png)

Als u de toepassing wilt verplaatsen naar een ander abonnement, selecteert  **u Wijzigen** naast het abonnement. Kies op **de pagina Resources** verplaatsen het abonnement waar u deze toepassing naar wilt verplaatsen:

![Beheerportal: resourcebeheer](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u uw toepassingen Azure IoT Central de Azure Portal, is dit de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Uw toepassing beheren](howto-administer.md)
---
title: Beheer IoT Central vanuit de Azure Portal | Microsoft Docs
description: In dit artikel wordt beschreven hoe u uw IoT Central maakt en beheert vanuit Azure Portal.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 04/17/2021
ms.topic: how-to
ms.openlocfilehash: 3e5e126815d0171a6c1627a08419b05b9a3c0c23
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719201"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Beheer IoT Central vanuit de Azure Portal

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

U kunt de Azure Portal gebruiken [om](https://portal.azure.com) uw toepassingen IoT Central beheren.

## <a name="create-iot-central-applications"></a>Toepassingen IoT Central maken

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

Als u een toepassing wilt maken, gaat u [naar IoT Central toepassingspagina](https://ms.portal.azure.com/#create/Microsoft.IoTCentral) in de Azure Portal:

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

  Als u eenmaal een locatie hebt gekozen, kunt u de toepassing later niet meer naar een andere locatie verplaatsen.

Nadat u alle velden invult, selecteert u **Maken.** Zie de quickstart Een IoT Central [maken voor meer](quick-deploy-iot-central.md) informatie.

## <a name="manage-existing-iot-central-applications"></a>Bestaande IoT Central beheren

Als u al een Azure IoT Central hebt, kunt u deze verwijderen of verplaatsen naar een ander abonnement of een andere resourcegroep in de Azure Portal.

> [!NOTE]
> Voor toepassingen die *zijn* gemaakt met behulp van het gratis abonnement, zijn geen Azure-abonnementen vereist. U vindt ze daarom niet in uw Azure-abonnement op de Azure Portal. U kunt gratis apps alleen bekijken en beheren vanuit de IoT Central portal.

Om aan de slag te gaan, zoekt u uw toepassing in de zoekbalk boven aan de Azure Portal. U kunt ook al uw toepassingen weergeven door te zoeken _naar IoT Central Toepassingen_ en de service te selecteren:

![Schermopname van de zoekresultaten voor 'IoT Central', met de eerste service geselecteerd.](media/howto-manage-iot-central-from-portal/search-iot-central.png)

Wanneer u een toepassing selecteert in de zoekresultaten, ziet u Azure Portal overzicht van de toepassing. U kunt naar de toepassing navigeren door de **toepassings-URL IoT Central selecteren:**

![Schermopname van de pagina 'Overzicht' met de 'IoT Central Application URL' gemarkeerd.](media/howto-manage-iot-central-from-portal/image3.png)

Als u de toepassing wilt verplaatsen naar een andere resourcegroep, selecteert **u Wijzigen** naast de resourcegroep. Kies op **de pagina Resources** verplaatsen de resourcegroep waar u deze toepassing naar wilt verplaatsen:

![Schermopname van de pagina Overzicht met de 'Resourcegroep (wijzigen)' gemarkeerd.](media/howto-manage-iot-central-from-portal/image4a.png)

Als u de toepassing wilt verplaatsen naar een ander abonnement, selecteert  **u Wijzigen** naast het abonnement. Kies op **de pagina Resources** verplaatsen het abonnement waar u deze toepassing naar wilt verplaatsen:

![Beheerportal: resourcebeheer](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u uw toepassingen Azure IoT Central de Azure Portal, is dit de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Uw toepassing beheren](howto-administer.md)
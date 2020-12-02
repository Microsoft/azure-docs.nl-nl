---
author: msftradford
ms.service: spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 80685dee7907b81832c94044d1feb8fcf2e41bde
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185280"
---
### <a name="open-the-publish-wizard"></a>Open de publicatiewizard

Klik in **Solution Explorer** met de rechtermuisknop op het project **SharingService** en selecteer vervolgens **Publiceren**.

De publicatiewizard start. 

Selecteer **App Service** > **Publiceren** om het deelvenster **App Service maken** te openen.

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal.

Selecteer **Een account toevoegen** in het deelvenster **App Service maken** en meld u vervolgens aan bij uw Azure-abonnement. Als u al bent aangemeld, selecteert u het account dat u wilt in de vervolgkeuzelijst.

   > [!NOTE]
   > Als u al bent aangemeld, selecteert u **Maken** nog niet.
   >

### <a name="create-a-resource-group"></a>Een resourcegroep maken

[!INCLUDE [resource group intro text](resource-group.md)]

Selecteer **Nieuw** naast **Resourcegroep**.

Geef de resourcegroep de naam **myResourceGroup** en selecteer vervolgens **OK**.

### <a name="create-an-app-service-plan"></a>Een App Service-plan maken

[!INCLUDE [app-service-plan](app-service-plan.md)]

Selecteer bij **Hostingabonnement****Nieuw**.

Gebruik deze instellingen in het deelvenster **Hostingabonnement configureren**:

| Instelling | Voorgestelde waarde | Beschrijving |
|-|-|-|
|App Service-plan| MySharingServicePlan | De naam van het App Service-plan |
| Locatie | VS - west | Het datacentrum waar de web-app wordt gehost |
| Grootte | Gratis | De [prijscategorie](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) die de hosting-functies bepaalt |

Selecteer **OK**.

### <a name="create-and-publish-the-web-app"></a>De web-app maken en publiceren

Voer bij **App-naam** een unieke app-naam in. Geldige tekens zijn a-z, 0-9, en streepjes (-), of accepteer de automatisch gegenereerde unieke naam. De URL van de web-app is `https://<app_name>.azurewebsites.net`, waarbij `<app_name>` de naam van uw app is.

Selecteer **Maken** om de Azure-resources te gaan maken.

   Nadat de wizard is voltooid, wordt de ASP.NET Core-web-app gepubliceerd in Azure. Vervolgens wordt de app geopend in de standaardbrowser.

  ![Schermopname van een gepubliceerde ASP.NET-web-app in Azure.](./media/spatial-anchors-azure/web-app-running-live.png)

De app-naam die u in deze sectie hebt gebruikt, wordt gebruikt als de URL-voorvoegsel in de indeling `https://<app_name>.azurewebsites.net`. Kopieer deze URL naar een teksteditor voor later gebruik.

---
title: De Azure IoT-oplossingen site gebruiken-Azure | Microsoft Docs
description: Hierin wordt beschreven hoe u de AzureIoTSolutions.com-website gebruikt voor het implementeren van uw oplossings versneller.
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: dobett
ms.openlocfilehash: e3c9296a909ba23ee8a3475149911af629a01b1a
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106167461"
---
# <a name="use-the-azureiotsolutionscom-site-to-deploy-your-solution-accelerator"></a>De azureiotsolutions.com-site gebruiken voor het implementeren van uw oplossings versneller

U kunt Azure IoT-oplossings Accelerators implementeren voor uw Azure-abonnement vanuit [AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators). AzureIoTSolutions.com fungeert als host voor de micro soft open source-en Partner Solution Accelerators. Deze oplossings versnellers worden uitgelijnd met de [Azure IOT-referentie architectuur](/azure/architecture/reference-architectures/iot). U kunt de site gebruiken om snel een oplossings versneller te implementeren als een demo-of productie omgeving.

:::image type="content" source="media/iot-accelerators-permissions/iotsolutionscom.png" alt-text="Start pagina van IoT-oplossingen":::

> [!TIP]
> Als u meer controle over het implementatie proces nodig hebt, kunt u de CLI gebruiken voor het implementeren van een oplossings versneller.

U kunt de oplossings versnellers implementeren in de volgende configuraties:

* **Standard**: een uitgebreide infrastructuur implementatie voor het ontwikkelen van een productie omgeving. De Azure Container Service implementeert de microservices in diverse virtuele Azure-machines. Kubernetes deelt de Docker-containers in die de afzonderlijke microservices hosten.
* **Basic**: een gereduceerde kosten versie voor een demonstratie of voor het testen van een implementatie. Alle microservices worden geïmplementeerd op een enkele virtuele Azure-machine.
* **Lokaal**: een lokale machine-implementatie voor testen en ontwikkeling. Deze benadering implementeert de micro Services naar een lokale docker-container en maakt verbinding met de IoT Hub, Azure Cosmos DB en Azure Storage-services in de Cloud.

Elk van de oplossings Accelerators gebruikt een andere combi natie van Azure-Services, zoals IoT Hub, Azure Stream Analytics en Cosmos DB. Ga naar [AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators) en selecteer een oplossings versneller voor meer informatie.

## <a name="sign-in-at-azureiotsolutionscom"></a>Meld u aan bij azureiotsolutions.com

Voordat u een oplossings versneller kunt implementeren, moet u zich aanmelden bij AzureIoTSolutions.com met de referenties die zijn gekoppeld aan een Azure-abonnement. Als uw account is gekoppeld aan meer dan één Microsoft Azure Active Directory (AD)-Tenant, kunt u de **vervolg keuzelijst** voor het selecteren van accounts gebruiken om de map te kiezen die u wilt gebruiken.

Uw machtigingen voor het implementeren van oplossings accelerators, het beheren van gebruikers en het beheren van Azure-Services zijn afhankelijk van uw rol in de geselecteerde map. Algemene Azure AD-rollen die zijn gekoppeld aan de oplossings versnellers zijn onder andere:

* **Globale beheerder**: er kunnen veel [globale beheerders](../active-directory/roles/permissions-reference.md) per Azure AD-Tenant zijn:

  * Wanneer u een Azure AD-Tenant maakt, bent u standaard de globale beheerder van die Tenant.
  * De globale beheerder kan basis-en standaard oplossings versnellers implementeren.

* **Domein gebruiker**: er kunnen veel domein gebruikers per Azure AD-Tenant zijn. Een domein gebruiker kan een elementaire oplossings versneller implementeren.

* **Gast gebruiker**: er kunnen veel gast gebruikers per Azure AD-Tenant zijn. Gast gebruikers kunnen geen oplossings versneller implementeren in de Azure AD-Tenant.

Raadpleeg de volgende bronnen voor meer informatie over gebruikers en rollen in azure AD:

* [Gebruikers maken in Azure Active Directory](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
* [Gebruikers toewijzen aan apps](../active-directory/manage-apps/assign-user-or-group-access-portal.md)

## <a name="choose-your-device"></a>Uw apparaat kiezen

De AzureIoTSolutions.com-site is gekoppeld aan het [Azure Certified for IOT-apparaat Catalog](https://devicecatalog.azure.com/).

De catalogus bevat honderden gecertificeerde IoT-hardwareapparaten waarmee u verbinding kunt maken met uw oplossings Accelerators om uw IoT-oplossing te bouwen.

![Catalogus met apparaten](media/iot-accelerators-permissions/devicecatalog.png)

Als u een hardwarefabrikant bent, klikt u op **een partner** om meer te weten te komen over micro soft-partners in het programma Certified for IOT.

## <a name="next-steps"></a>Volgende stappen

Als u een van de IoT-oplossings accelerators wilt uitproberen, bekijkt u de Snelstartgids: [een verbonden fabrieks oplossing proberen](quickstart-connected-factory-deploy.md).

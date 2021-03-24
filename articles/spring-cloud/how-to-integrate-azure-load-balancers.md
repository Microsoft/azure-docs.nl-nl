---
title: Zelf studie-Azure lente-Cloud integreren met Azure Load Balancing-oplossingen
description: Azure lente-Cloud integreren met Azure Load Balancing-oplossingen
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 04/20/2020
ms.custom: devx-track-java
ms.openlocfilehash: 50b09fd82461221ae6cd008f6918ac2f3a26fd94
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104877966"
---
# <a name="integrate-azure-spring-cloud-with-azure-load-balance-solutions"></a>Azure Spring Cloud integreren met Azure-oplossingen voor taakverdeling

**Dit artikel is van toepassing op:** ✔️ Java ✔️ C#

Azure lente Cloud ondersteunt micro Services op Azure.  Het verhogen van het bedrijf kan meerdere data centers met beheer van meerdere exemplaren van Azure lente-Cloud vereisen.

Azure biedt al andere oplossingen voor Load Balancing. Er zijn drie opties voor het integreren van Azure lente-Cloud met Azure-oplossingen voor taak verdeling:

1.  Azure lente-Cloud integreren met Azure Traffic Manager
2.  Azure lente-Cloud integreren met Azure-app gateway
3.  Azure lente-Cloud integreren met Azure front-deur

## <a name="prerequisites"></a>Vereisten

* Azure lente-Cloud: [een Azure lente-Cloud service maken](./spring-cloud-quickstart.md)
* Azure Traffic Manager: [Traffic Manager maken](../traffic-manager/quickstart-create-traffic-manager-profile.md)
* Azure-app gateway: [een toepassings gateway maken](../application-gateway/quick-create-portal.md)
* Azure front deur: [een voor deur maken](../frontdoor/quickstart-create-front-door.md)

## <a name="integrate-azure-spring-cloud-with-azure-traffic-manager"></a>Azure lente-Cloud integreren met Azure Traffic Manager

Als u Azure lente-Cloud wilt integreren met Traffic Manager, voegt u de open bare eind punten toe als eind punten van Traffic Manager en configureert u vervolgens aangepast domein voor zowel Traffic Manager als Azure veer Cloud.

### <a name="add-endpoint-in-traffic-manager"></a>Eind punt toevoegen in Traffic Manager
Eind punten toevoegen in Traffic Manager:
1.  Geef het **type** op dat het *externe eind punt* moet worden.
1.  Invoer Fully Qualified Domain Name (FQDN) van elk openbaar eind punt in de cloud van Azure lente.
1. Klik op **OK**.

    ![Traffic Manager 1 ](media/spring-cloud-load-balancers/traffic-manager-1.png) ![ Traffic Manager 2](media/spring-cloud-load-balancers/traffic-manager-2.png)

### <a name="configure-custom-domain"></a>Aangepast domein configureren
De configuratie volt ooien:
1.  Meld u aan bij de website van uw domein provider en maak een CNAME-record toewijzing van uw aangepaste domein aan de naam van het Azure-standaard domein van Traffic Manager.
1.  Volg de instructies [voor het toevoegen van een aangepast domein aan Azure lente-Cloud](spring-cloud-tutorial-custom-domain.md).
1. Voeg boven aangepast domein binding aan Traffic Manager toe aan Azure veer Cloud die overeenkomt met de app service en upload het SSL-certificaat daar.

    ![Traffic Manager 3](media/spring-cloud-load-balancers/traffic-manager-3.png)

## <a name="integrate-azure-spring-cloud-with-azure-app-gateway"></a>Azure lente-Cloud integreren met Azure-app gateway

Als u wilt integreren met Azure lente-Cloud service, voert u de volgende configuraties uit:

### <a name="configure-backend-pool"></a>Back-end-groep configureren
1. Geef het **doel type** op als *IP-adres* of *FQDN*-naam.
1. Voer uw open bare eind punten in de cloud in voor Azure.

    ![App-gateway 1](media/spring-cloud-load-balancers/app-gateway-1.png)

### <a name="add-custom-probe"></a>Aangepaste test toevoegen
1. Selecteer **status controles** en **toevoegen** om het dialoog venster aangepaste **test** te openen. 
1. Het sleutel punt is om *Ja* te selecteren voor de optie **hostnaam van de back-end-http-instellingen** .

    ![App-gateway 2](media/spring-cloud-load-balancers/app-gateway-2.png)

### <a name="configure-http-setting"></a>Http-instelling configureren
1.  Selecteer **http-instellingen** en **Voeg** vervolgens een http-instelling toe.
1.  **Overschrijven door nieuwe hostnaam:** Selecteer *Ja*.
1.  **Hostnaam negeren**: Selecteer de **hostnaam in het back-end-doel** selecteren.
1.  **Aangepaste test gebruiken**: Selecteer *Ja* en kies de aangepaste test die hierboven is gemaakt.

    ![App-gateway 3](media/spring-cloud-load-balancers/app-gateway-3.png)

### <a name="configure-rewrite-set"></a>Set voor herschrijven configureren
1.  Selecteer opnieuw **schrijven** en vervolgens **opnieuw schrijven om** een set opnieuw schrijven toe te voegen.
1.  Selecteer de routerings regels die aanvragen naar Azure lente-open bare eind punten van de Cloud routeren.
1.  Op het tabblad **regel configuratie opnieuw schrijven** selecteert u **regel voor herschrijven toevoegen**.
1.  **Herschrijf type**: **aanvraag header** selecteren
1.  **Actie type**: Selecteer **verwijderen**
1.  **Header naam**: Selecteer **algemene koptekst**
1.  **Common header**: Selecteer **X-doorgestuurd-proto**

    ![App-Gateway 4](media/spring-cloud-load-balancers/app-gateway-4.png)

## <a name="integrate-azure-spring-cloud-with-azure-front-door"></a>Azure lente-Cloud integreren met Azure front-deur

Voor integratie met Azure lente-Cloud service en het configureren van de back-end-groep 
1. **Back-end-groep toevoegen**.
1. Geef het back-end eind punt op door een host toe te voegen.

    ![Voor deur 1](media/spring-cloud-load-balancers/front-door-1.png)

1.  Geef het **type back-end** op als *aangepaste host*.
1.  Geef een FQDN-naam op voor de open bare eind punten van de Azure lente-Cloud in de **back-endserver**.
1.  Accepteer de standaard waarde voor de **backend-host** , die hetzelfde is als de naam van de **back-end**.

    ![Voor deur 2](media/spring-cloud-load-balancers/front-door-2.png)

## <a name="next-steps"></a>Volgende stappen
* [Hoe kan ik een Traffic Manager maken?](../traffic-manager/quickstart-create-traffic-manager-profile.md)
* [Een toepassings gateway maken](../application-gateway/quick-create-portal.md)
* [Een voor deur maken](../frontdoor/quickstart-create-front-door.md)
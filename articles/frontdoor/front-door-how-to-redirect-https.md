---
title: Maak een voor deur met HTTP-naar-HTTPS-omleiding met behulp van de Azure Portal
description: Meer informatie over het maken van een voor deur met omgeleid verkeer van HTTP naar HTTPS met behulp van de Azure Portal.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: 19908b3cba63bc76a205097ef8d16e612d58503b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91626627"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>Maak een voor deur met HTTP-naar-HTTPS-omleiding met behulp van de Azure Portal

U kunt de Azure Portal gebruiken om [een voor deur te maken](quickstart-create-front-door.md) met een certificaat voor het beëindigen van TLS. Een routerings regel wordt gebruikt om HTTP-verkeer om te leiden naar HTTPS.

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>Een voor deur maken met een bestaande web app-resource

1. Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

1. Selecteer **een resource maken** gevonden in de linkerbovenhoek van de Azure Portal.

1. Zoek naar de voor **deur** met de zoek balk en klik op **maken** zodra u het resource type hebt gevonden.

1. Kies een *abonnement* en gebruik vervolgens een bestaande resource groep of maak een nieuwe. Selecteer **volgende** om het tabblad Configuratie in te voeren.

    > [!NOTE]
    > De locatie die in de gebruikers interface wordt gevraagd, is alleen voor de resource groep. De configuratie van uw front-deur wordt geïmplementeerd op alle POP-locaties van de [Azure front deur](front-door-faq.md#what-are-the-pop-locations-for-azure-front-door).

    :::image type="content" source="./media/front-door-url-redirect/front-door-create-basics.png" alt-text="Basis principes configureren voor nieuwe front-deur":::

1. De configuratie voor de voor deur gebeurt in drie stappen: het toevoegen van een standaard frontend-host, het toevoegen van back-endservers in een back-end-groep en het maken van routerings regels om het routerings gedrag voor de frontend-host toe te wijzen. Selecteer het **+** pictogram ' ' op de _frontend-hosts_ om een frontend-host te maken.

    :::image type="content" source="./media/front-door-url-redirect/front-door-designer.png" alt-text="Configuratie ontwerp voor de voor deur":::

1. Voer een wereld wijd unieke naam in voor de standaard-frontend-host voor uw front-deur. Selecteer **toevoegen** om door te gaan naar de volgende stap.

    :::image type="content" source="./media/front-door-url-redirect/front-door-create-frontend-host.png" alt-text="Een frontend-host toevoegen":::

### <a name="create-backend-pool"></a>Back-end-groep maken

1. Selecteer het **+** pictogram ' ' op de _back-endservers_ om een back-end-groep te maken. Geef een naam op voor de back-end-groep en selecteer vervolgens **een back-end toevoegen**.

    :::image type="content" source="./media/front-door-url-redirect/front-door-designer-backend-pool.png" alt-text="Back-end van front-deur configuratie ontwerpen":::

1. Selecteer het type back-end als _app service_. Selecteer het abonnement waarin uw web-app wordt gehost en selecteer vervolgens de specifieke Web-app in de vervolg keuzelijst voor de **back-end-hostnaam**.

    :::image type="content" source="./media/front-door-url-redirect/front-door-create-backend-pool.png" alt-text="Een back-end toevoegen aan een back-end-groep":::

1. Selecteer **toevoegen** om de back-end op te slaan en selecteer opnieuw **toevoegen** om de back-end-pool configuratie op te slaan. 

## <a name="create-http-to-https-redirect-rule"></a>HTTP-naar-HTTPS-omleidings regel maken

1. Selecteer het **+** pictogram ' ' op de *routerings regels* om een route te maken. Geef een naam op voor de route, bijvoorbeeld ' HttpToHttpsRedirect ', en stel het veld *geaccepteerd protocol* vervolgens in op **' http only '**. Zorg ervoor dat de juiste *frontend/domeinen* zijn geselecteerd.  

    :::image type="content" source="./media/front-door-url-redirect/front-door-designer-routing-rule.png" alt-text="Routerings regel voor configuratie ontwerp van front-deur":::

1. Stel in het gedeelte *route Details* het *route type* in op **omleiden**. Zorg ervoor dat de *omleidings type* Get set to **found (302)** en *Redirect protocol* Get is ingesteld op **alleen https**. 

    :::image type="content" source="./media/front-door-url-redirect/front-door-redirect-config-example.png" alt-text="Een HTTP-naar-HTTPS-omleidings route toevoegen":::

1. Selecteer **toevoegen** om de routerings regel voor http-naar-https-omleiding op te slaan.

## <a name="create-forwarding-rule"></a>Doorstuur regel maken

1. Voeg nog een regel voor route ring toe om het HTTPS-verkeer te verwerken. Selecteer het ' **+** '-aanmeldings *regels* en geef een naam op voor de route, bijvoorbeeld ' DefaultForwardingRoute '. Stel het veld *geaccepteerde protocollen* vervolgens in op **alleen https**. Zorg ervoor dat de juiste *frontend/domeinen* zijn geselecteerd.

1. Stel in het gedeelte route Details het *route type* in op **door sturen**. Zorg ervoor dat de juiste back-end-pool wordt geselecteerd en dat het *doorstuur protocol* alleen is ingesteld op **https**. 

    :::image type="content" source="./media/front-door-url-redirect/front-door-forward-route-example.png" alt-text="Een doorstuur route voor HTTPS-verkeer toevoegen" border="false":::

1. Selecteer **toevoegen** om de routerings regel voor het door sturen van aanvragen op te slaan.

1. Selecteer **controleren + maken** en vervolgens **maken** om het profiel voor de voor deur te maken. Ga naar de resource nadat deze is gemaakt.

## <a name="next-steps"></a>Volgende stappen

- Lees [hoe Front Door werkt](front-door-routing-architecture.md).
- Meer informatie over [URL-omleiding op de voor deur](front-door-url-redirect.md).

---
title: Verkeer naar multi tenant-apps beheren via de portal
titleSuffix: Azure Application Gateway
description: Dit artikel bevat richt lijnen voor het configureren van Azure-app service-Web-apps als leden in back-end-pool op een bestaande of nieuwe toepassings gateway.
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: how-to
ms.date: 01/02/2021
ms.author: victorh
ms.openlocfilehash: aadd4904ff218613c0dd24daff784ad5b8b90fbb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97854907"
---
# <a name="configure-app-service-with-application-gateway"></a>App Service configureren met Application Gateway

Omdat App Service een multitenant-service is en geen speciale implementatie, wordt de hostheader in de inkomende aanvraag gebruikt om de aanvraag in te stellen op in het juiste App Service-eindpunt. Normaal gesp roken is de DNS-naam van de toepassing, die op zijn beurt de DNS-naam die is gekoppeld aan de toepassings gateway voor de app service, afwijkt van de domein naam van de back-end-app service. Daarom is de host-header in de oorspronkelijke aanvraag die wordt ontvangen door de toepassings gateway niet hetzelfde als de hostnaam van de back-end-service. Als gevolg hiervan, tenzij de host-header in de aanvraag van de toepassings gateway naar de back-end is gewijzigd in de hostnaam van de back-end-service, kunnen de back-endservers van meerdere tenants de aanvraag niet omzetten naar het juiste eind punt.

Application Gateway biedt een switch die wordt aangeroepen `Pick host name from backend target` en die de host-header in de aanvraag overschrijft met de hostnaam van de back-end wanneer de aanvraag wordt doorgestuurd van de Application Gateway naar de back-end. Deze mogelijkheid biedt ondersteuning voor back-ends met meerdere tenants, zoals Azure app service en API management. 

In dit artikel leert u het volgende:

- Een back-end-pool bewerken en er een App Service aan toevoegen
- HTTP-instellingen bewerken waarbij de schakel optie hostname selecteren is ingeschakeld

## <a name="prerequisites"></a>Vereisten

- Toepassings gateway: een toepassings gateway maken zonder een back-end-pool doel. Voor meer informatie raadpleegt u [Quick Start: direct Web Traffic with Azure-toepassing gateway-Azure Portal](quick-create-portal.md)

- App service: als u geen bestaande app service hebt, raadpleegt u [app service-documentatie](../app-service/index.yml).

## <a name="add-app-service-as-backend-pool"></a>App service toevoegen als back-end-groep

1. Selecteer uw toepassings gateway in het Azure Portal.

2. Selecteer onder **back-endservers** de back-end-pool.

4. Onder **doel type** selecteert u **app Services**.

5. Onder **doel** selecteert u uw app service.

   :::image type="content" source="./media/configure-web-app-portal/backend-pool.png" alt-text="App service-back-end":::
   
   > [!NOTE]
   > In de vervolg keuzelijst worden alleen de app-Services ingevuld die zich in hetzelfde abonnement bevinden als uw Application Gateway. Als u een app-service wilt gebruiken die zich in een ander abonnement bevindt dan de versie waarin de Application Gateway is, kiest u in plaats van **app Services** in de vervolg keuzelijst **doel** de optie **IP-adres of hostnaam** en voert u de hostnaam (voor beeld in. azurewebsites.net) van de app service.
1. Selecteer **Opslaan**.

## <a name="edit-http-settings-for-app-service"></a>HTTP-instellingen voor App Service bewerken

1. Selecteer onder **http-instellingen** de bestaande http-instelling.

2. Selecteer bij **vervangen door nieuwe hostnaam** de optie **Ja**.
3. Onder **hostnaam negeren** selecteert u **hostnaam kiezen uit back-end doel**.
4. Selecteer **Opslaan**.

   :::image type="content" source="./media/configure-web-app-portal/http-settings.png" alt-text="Kies een hostnaam uit de back-end-http-instellingen":::

## <a name="additional-configuration-in-case-of-redirection-to-app-services-relative-path"></a>Aanvullende configuratie in het geval van omleiding naar het relatieve pad van de app service

Wanneer de app-service een omleidings reactie naar de client verzendt om om te leiden naar het relatieve pad (bijvoorbeeld een omleiding van `contoso.azurewebsites.net/path1` naar `contoso.azurewebsites.net/path2` ), wordt dezelfde hostnaam gebruikt in de locatie header van de reactie van de toepassing als het antwoord in de aanvraag die is ontvangen van de toepassings gateway. De client zal de aanvraag dus rechtstreeks laten `contoso.azurewebsites.net/path2` door lopen in plaats van de Application Gateway ( `contoso.com/path2` ). Het overs laan van de toepassings gateway is niet gewenst.

Als u in uw gebruiks voorbeeld scenario's hebt waarbij de app service een omleidings antwoord naar de client moet verzenden, voert [u de extra stappen uit om de locatie header te herschrijven](./troubleshoot-app-service-redirection-app-service-url.md#sample-configuration).

## <a name="restrict-access"></a>Toegang beperken

In de web-apps die in deze voor beelden zijn geïmplementeerd, worden open bare IP-adressen gebruikt die rechtstreeks via internet toegankelijk zijn. Dit helpt bij het oplossen van problemen met een nieuwe functie en nieuwe dingen. Maar als u van plan bent een functie in productie te implementeren, wilt u meer beperkingen toevoegen.

Een manier om de toegang tot uw web-apps te beperken, is door [Azure app service statische IP-beperkingen](../app-service/app-service-ip-restrictions.md)te gebruiken. Zo kunt u de Web-App zodanig beperken dat alleen verkeer van de toepassings gateway wordt ontvangen. Gebruik de functie IP-beperking van app service om de Application Gateway-VIP weer te geven als het enige adres met Access.

## <a name="next-steps"></a>Volgende stappen

Zie [ondersteuning voor multi tenant-Services met toepassings gateway](./application-gateway-web-app-overview.md)voor meer informatie over de app service en andere ondersteuning voor meerdere tenants met Application Gateway.

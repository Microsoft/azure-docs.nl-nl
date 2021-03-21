---
title: Google-verificatie configureren
description: Meer informatie over het configureren van Google-verificatie als een id-provider voor uw App Service-of Azure Functions-app.
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.topic: article
ms.date: 09/02/2019
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: e8a9fbe6072f3628d755ad3ad5aa5a623fc3ab23
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "80519949"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-google-login"></a>Uw App Service-of Azure Functions-app configureren voor het gebruik van Google-aanmelding

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

In dit onderwerp wordt beschreven hoe u Azure App Service of Azure Functions kunt configureren om Google als verificatie provider te gebruiken.

Als u de procedure in dit onderwerp wilt volt ooien, moet u een Google-account hebben met een bevestigd e-mail adres. Als u een nieuw Google-account wilt maken, gaat u naar [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).

## <a name="register-your-application-with-google"></a><a name="register"> </a>Uw toepassing registreren bij Google

1. Volg de Google-documentatie op [google Sign-In voor apps aan de server zijde](https://developers.google.com/identity/sign-in/web/server-side-flow) om een client-id en client geheim te maken. U hoeft geen code wijzigingen aan te brengen. Gebruik hiervoor de volgende informatie:
    - Gebruik met de naam van uw app in voor **geautoriseerde java script-oorsprong** `https://<app-name>.azurewebsites.net` *\<app-name>* .
    - Gebruik voor **geautoriseerde omleidings-URI** `https://<app-name>.azurewebsites.net/.auth/login/google/callback` .
1. Kopieer de App-ID en de geheime waarden van de app.

    > [!IMPORTANT]
    > Het app-geheim is een belang rijke beveiligings referentie. Deel dit geheim niet met iemand of distribueer het in een client toepassing.

## <a name="add-google-information-to-your-application"></a><a name="secrets"> </a>Google-gegevens toevoegen aan uw toepassing

1. Ga in het [Azure Portal]naar uw app service-app.
1. Selecteer **instellingen**  >  **verificatie/autorisatie** en zorg ervoor dat **app service-verificatie** is **ingeschakeld**.
1. Selecteer **Google** en plak de waarden van de App-ID en het app-geheim die u eerder hebt verkregen. Schakel de scopes in die nodig zijn voor uw toepassing.
1. Selecteer **OK**.

   App Service biedt verificatie, maar beperkt geen geautoriseerde toegang tot uw site-inhoud en Api's. Zie [gebruikers machtigen of weigeren](app-service-authentication-how-to.md#authorize-or-deny-users)voor meer informatie.

1. Beschrijving Als u de toegang tot de site alleen wilt beperken voor gebruikers die zijn geverifieerd door Google, stelt **u een actie in die moet worden uitgevoerd wanneer de aanvraag niet is geverifieerd** bij **Google**. Wanneer u deze functionaliteit instelt, vereist uw app dat alle aanvragen worden geverifieerd. Ook worden alle niet-geverifieerde aanvragen voor verificatie doorgestuurd naar Google.

    > [!CAUTION]
    > Het beperken van de toegang op deze manier is van toepassing op alle aanroepen naar uw app. Dit is mogelijk niet wenselijk voor apps met een openbaar beschik bare start pagina, zoals in veel toepassingen met één pagina. Voor dergelijke toepassingen kunt u **anonieme aanvragen (geen actie) toestaan** , zodat de verificatie zelf door de app hand matig wordt gestart. Zie voor meer informatie [verificatie stroom](overview-authentication-authorization.md#authentication-flow).

1. Selecteer **Opslaan**.

U bent nu klaar om Google te gebruiken voor verificatie in uw app.

## <a name="next-steps"></a><a name="related-content"> </a>Volgende stappen

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure-portal]: https://portal.azure.com/


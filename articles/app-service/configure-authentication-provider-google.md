---
title: Google-verificatie configureren
description: Meer informatie over het configureren van Google-verificatie als een id-provider voor uw App Service-of Azure Functions-app.
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.topic: article
ms.date: 03/29/2021
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: f6bec32fa928e840569ed95c35a056db91ea9737
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077989"
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

1. Meld u aan bij de [Azure Portal] en navigeer naar uw app.
1. Selecteer **verificatie** in het menu aan de linkerkant. Klik op **ID-provider toevoegen**.
1. Selecteer **Google** in de vervolg keuzelijst ID-provider. Plak de App-ID en de geheime waarden van de app die u eerder hebt verkregen.

    Het geheim wordt opgeslagen als een sleuf-plak [toepassings instelling](./configure-common.md#configure-app-settings) met de naam `GOOGLE_PROVIDER_AUTHENTICATION_SECRET` . U kunt deze instelling later bijwerken om [Key Vault verwijzingen](./app-service-key-vault-references.md) te gebruiken als u het geheim in azure Key Vault wilt beheren.

1. Als dit de eerste ID-provider is die voor de toepassing is geconfigureerd, wordt u ook gevraagd om een App Service de sectie **verificatie-instellingen** . Als dat niet het geval is, kunt u door gaan met de volgende stap.
    
    Deze opties bepalen hoe uw toepassing reageert op niet-geverifieerde aanvragen en de standaard selecties omleiden alle aanvragen om u aan te melden bij deze nieuwe provider. U kunt dit gedrag nu aanpassen of deze instellingen later aanpassen via het hoofd scherm voor **verificatie** door **bewerken** naast verificatie- **instellingen** te kiezen. Zie voor meer informatie over deze opties [verificatie stroom](overview-authentication-authorization.md#authentication-flow).

1. Beschrijving Klik op **volgende: bereiken** en voeg de scopes toe die nodig zijn voor de toepassing. Deze worden tijdens de aanmeldings tijd aangevraagd voor op de browser gebaseerde stromen.
1. Klik op **Add**.

U bent nu klaar om Google te gebruiken voor verificatie in uw app. De provider wordt weer gegeven in het scherm **verificatie** . Hier kunt u deze provider configuratie bewerken of verwijderen.

## <a name="next-steps"></a><a name="related-content"> </a>Volgende stappen

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure-portal]: https://portal.azure.com/


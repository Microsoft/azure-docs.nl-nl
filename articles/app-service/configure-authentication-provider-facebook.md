---
title: Facebook-verificatie configureren
description: Meer informatie over het configureren van Facebook-verificatie als een id-provider voor uw App Service of Azure Functions app.
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.topic: article
ms.date: 03/29/2021
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: bc639eaea76b3309d6ed047e73c726040da19639
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078006"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-facebook-login"></a>Uw App Service of Azure Functions app configureren voor het gebruik van Facebook-aanmelding

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

In dit artikel wordt beschreven hoe u Azure App Service of Azure Functions kunt configureren om Facebook als verificatie provider te gebruiken.

Voor het volt ooien van de procedure in dit artikel hebt u een Facebook-account nodig met een bevestigd e-mail adres en een mobiel telefoon nummer. Als u een nieuw Facebook-account wilt maken, gaat u naar [Facebook.com].

## <a name="register-your-application-with-facebook"></a><a name="register"> </a>Uw toepassing registreren bij Facebook

1. Ga naar de website van [Facebook-ontwikkel aars] en meld u aan met de referenties van uw Facebook-account.

   Als u geen Facebook-account voor ontwikkel aars hebt, selecteert u **aan de slag** en volgt u de registratie stappen.
1. **Mijn apps** selecteren  >  **nieuwe app toevoegen**.
1. In het veld **weergave naam** :
   1. Typ een unieke naam voor uw app.
   1. Geef uw **contact-e-mail**.
   1. Selecteer **App-ID maken**.
   1. Voltooi de beveiligings controle.

   Het dash board voor ontwikkel aars voor uw nieuwe Facebook-app wordt geopend.
1. Selecteer het **dash board**  >  **Facebook-aanmelding**  >  **instellen**  >  **Web**.
1. Selecteer in het navigatie venster links onder **Facebook-aanmelding** de optie **instellingen**.
1. Voer in het veld **geldige OAuth omleidings-uri's** in `https://<app-name>.azurewebsites.net/.auth/login/facebook/callback` . Vergeet niet door `<app-name>` de naam van uw Azure app service-app te vervangen.
1. Selecteer **Save changes**.
1. Selecteer in het linkerdeel venster **instellingen**  >  **basis**. 
1. In het veld **app-geheim** selecteert u **weer geven**. Kopieer de waarden van de **App-ID** en het **app-geheim**. U kunt deze later gebruiken om uw App Service-app te configureren in Azure.

   > [!IMPORTANT]
   > Het app-geheim is een belang rijke beveiligings referentie. Deel dit geheim niet met iemand of distribueer het in een client toepassing.
   >

1. Het Facebook-account dat u hebt gebruikt om de toepassing te registreren, is een beheerder van de app. Op dit moment kunnen alleen beheerders zich aanmelden bij deze toepassing.

   Als u andere Facebook-accounts wilt verifiëren, selecteert u **app controleren** en schakelt u **\<your-app-name> openbaar maken** in om het algemene publiek toegang tot de app te geven met behulp van Facebook-verificatie.

## <a name="add-facebook-information-to-your-application"></a><a name="secrets"> </a>Facebook-gegevens toevoegen aan uw toepassing

1. Meld u aan bij de [Azure Portal] en navigeer naar uw app.
1. Selecteer **verificatie** in het menu aan de linkerkant. Klik op **ID-provider toevoegen**.
1. Selecteer **Facebook** in de vervolg keuzelijst ID-provider. Plak de App-ID en de geheime waarden van de app die u eerder hebt verkregen.

    Het geheim wordt opgeslagen als een sleuf-plak [toepassings instelling](./configure-common.md#configure-app-settings) met de naam `FACEBOOK_PROVIDER_AUTHENTICATION_SECRET` . U kunt deze instelling later bijwerken om [Key Vault verwijzingen](./app-service-key-vault-references.md) te gebruiken als u het geheim in azure Key Vault wilt beheren.

1. Als dit de eerste ID-provider is die voor de toepassing is geconfigureerd, wordt u ook gevraagd om een App Service de sectie **verificatie-instellingen** . Als dat niet het geval is, kunt u door gaan met de volgende stap.
    
    Deze opties bepalen hoe uw toepassing reageert op niet-geverifieerde aanvragen en de standaard selecties omleiden alle aanvragen om u aan te melden bij deze nieuwe provider. U kunt dit gedrag nu aanpassen of deze instellingen later aanpassen via het hoofd scherm voor **verificatie** door **bewerken** naast verificatie- **instellingen** te kiezen. Zie voor meer informatie over deze opties [verificatie stroom](overview-authentication-authorization.md#authentication-flow).

1. Beschrijving Klik op **volgende: bereiken** en voeg de scopes toe die nodig zijn voor de toepassing. Deze worden tijdens de aanmeldings tijd aangevraagd voor op de browser gebaseerde stromen.
1. Klik op **Add**.

U bent nu klaar om Facebook te gebruiken voor verificatie in uw app. De provider wordt weer gegeven in het scherm **verificatie** . Hier kunt u deze provider configuratie bewerken of verwijderen.

## <a name="next-steps"></a><a name="related-content"> </a>Volgende stappen

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->
[Facebook-ontwikkel aars]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure-portal]: https://portal.azure.com/

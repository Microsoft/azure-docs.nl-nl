---
title: Twitter-verificatie configureren
description: Meer informatie over het configureren van Twitter-verificatie als een id-provider voor uw App Service of Azure Functions app.
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.topic: article
ms.date: 03/29/2021
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: 6ecce954991d9f3901c54a6f87fc803b32469862
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077972"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-twitter-login"></a>Uw App Service of Azure Functions app configureren voor het gebruik van Twitter-aanmelding

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

In dit artikel wordt beschreven hoe u Azure App Service of Azure Functions configureert om Twitter als verificatie provider te gebruiken.

Voor het volt ooien van de procedure in dit artikel hebt u een Twitter-account nodig met een bevestigd e-mail adres en telefoon nummer. Als u een nieuw Twitter-account wilt maken, gaat u naar [Twitter.com].

## <a name="register-your-application-with-twitter"></a><a name="register"> </a>Uw toepassing registreren bij Twitter

1. Meld u aan bij de [Azure Portal] en ga naar uw toepassing. Kopieer uw **URL**. U gebruikt deze om uw Twitter-app te configureren.
1. Ga naar de website van de [Twitter-ontwikkel aars] , Meld u aan met de referenties van uw Twitter-account en selecteer **een app maken**.
1. Voer de **naam** van de app en de **toepassings beschrijving** in voor uw nieuwe app. Plak de **URL** van uw toepassing in het veld URL van de **website** . Voer in de sectie **url's van aanroepen** de HTTPS-URL van uw app service-app in en voeg het pad toe `/.auth/login/twitter/callback` . Bijvoorbeeld `https://contoso.azurewebsites.net/.auth/login/twitter/callback`.
1. Typ aan de onderkant van de pagina ten minste 100 tekens in **vertel ons hoe deze app wordt gebruikt** en selecteer vervolgens **maken**. Klik opnieuw op **maken** in het pop-upvenster. De details van de toepassing worden weer gegeven.
1. Selecteer het tabblad **sleutels en toegangs tokens** .

   Noteer deze waarden:
   - API-sleutel
   - Geheime API-sleutel

   > [!IMPORTANT]
   > De geheime API-sleutel is een belang rijke beveiligings referentie. Deel dit geheim niet met iemand of distribueer het met uw app.

## <a name="add-twitter-information-to-your-application"></a><a name="secrets"> </a>Twitter-gegevens toevoegen aan uw toepassing

1. Meld u aan bij de [Azure Portal] en navigeer naar uw app.
1. Selecteer **verificatie** in het menu aan de linkerkant. Klik op **ID-provider toevoegen**.
1. Selecteer **Twitter** in de vervolg keuzelijst ID-provider. Plak de `API key` en `API secret key` waarden die u eerder hebt verkregen.

    Het geheim wordt opgeslagen als een sleuf-plak [toepassings instelling](./configure-common.md#configure-app-settings) met de naam `TWITTER_PROVIDER_AUTHENTICATION_SECRET` . U kunt deze instelling later bijwerken om [Key Vault verwijzingen](./app-service-key-vault-references.md) te gebruiken als u het geheim in azure Key Vault wilt beheren.

1. Als dit de eerste ID-provider is die voor de toepassing is geconfigureerd, wordt u ook gevraagd om een App Service de sectie **verificatie-instellingen** . Als dat niet het geval is, kunt u door gaan met de volgende stap.
    
    Deze opties bepalen hoe uw toepassing reageert op niet-geverifieerde aanvragen en de standaard selecties omleiden alle aanvragen om u aan te melden bij deze nieuwe provider. U kunt dit gedrag nu aanpassen of deze instellingen later aanpassen via het hoofd scherm voor **verificatie** door **bewerken** naast verificatie- **instellingen** te kiezen. Zie voor meer informatie over deze opties [verificatie stroom](overview-authentication-authorization.md#authentication-flow).

1. Klik op **Add**.

U bent nu klaar om Twitter te gebruiken voor verificatie in uw app. De provider wordt weer gegeven in het scherm **verificatie** . Hier kunt u deze provider configuratie bewerken of verwijderen.

## <a name="next-steps"></a><a name="related-content"> </a>Volgende stappen

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[Twitter-ontwikkel aars]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[twitter.com]: https://go.microsoft.com/fwlink/p/?LinkID=268287
[Azure-portal]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md

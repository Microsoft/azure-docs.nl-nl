---
title: Micro soft-verificatie configureren
description: Meer informatie over het configureren van micro soft-account verificatie als een id-provider voor uw App Service of Azure Functions app.
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.topic: article
ms.date: 08/08/2019
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: 9ec9c102680496407106a3bf9b7683890c7a63ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93043250"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-microsoft-account-login"></a>Uw App Service of Azure Functions app configureren voor het gebruik van aanmelding met een micro soft-account

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

In dit onderwerp wordt beschreven hoe u Azure App Service of Azure Functions kunt configureren om AAD te gebruiken ter ondersteuning van persoonlijke Microsoft-account-aanmeldingen.

> [!NOTE]
> Zowel persoonlijke micro soft-accounts als organisatie accounts gebruiken de AAD-ID-provider. Op dit moment is het niet mogelijk om deze id-provider zo te configureren dat beide typen logboeken worden ondersteund.

## <a name="register-your-app-with-microsoft-account"></a><a name="register-microsoft-account"> </a>Uw app registreren bij micro soft-account

1. Ga naar [**app-registraties**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) in het Azure Portal. Meld u indien nodig aan met uw Microsoft-account.
1. Selecteer **nieuwe registratie** en voer vervolgens een toepassings naam in.
1. Onder **ondersteunde account typen** selecteert u **accounts in elke organisatie Directory (een Azure AD-adres lijst-multi tenant) en persoonlijke micro soft-accounts (bijvoorbeeld Skype, Xbox)**
1. In **omleidings-uri's** selecteert u **Web** en voert u in `https://<app-domain-name>/.auth/login/aad/callback` . Vervang door *\<app-domain-name>* de domein naam van uw app.  Bijvoorbeeld `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Zorg ervoor dat u het HTTPS-schema in de URL gebruikt.

1. Selecteer **Registreren**.
1. Kopieer de **toepassings-id (client)**. U hebt deze later nodig.
1. Selecteer in het linkerdeel venster **certificaten & geheimen**  >  **Nieuw client geheim**. Voer een beschrijving in, selecteer de geldigheids duur en selecteer **toevoegen**.
1. Kopieer de waarde die wordt weer gegeven op de pagina **certificaten & geheimen** . Nadat u de pagina verlaat, wordt deze niet meer weer gegeven.

    > [!IMPORTANT]
    > De waarde van het client geheim (wacht woord) is een belang rijke beveiligings referentie. Deel het wacht woord niet met iemand of distribueer het in een client toepassing.

## <a name="add-microsoft-account-information-to-your-app-service-application"></a><a name="secrets"> </a>Micro soft-account gegevens toevoegen aan uw app service-toepassing

1. Ga naar uw toepassing in de [Azure Portal].
1. Selecteer **instellingen**  >  **verificatie/autorisatie** en zorg ervoor dat **app service-verificatie** is **ingeschakeld**.
1. Selecteer onder **Azure Active Directory** de optie **Verificatieproviders**. Selecteer **Geavanceerd** onder **beheer modus**. Plak de client-ID en het client geheim die u eerder hebt verkregen. Gebruiken **`https://login.microsoftonline.com/9188040d-6c67-4c5b-b112-36a304b66dad/v2.0`** voor het **URL** -veld van de uitgever.
1. Selecteer **OK**.

   App Service biedt verificatie, maar beperkt de toegang tot uw site-inhoud en Api's niet. U moet gebruikers in uw app-code autoriseren.

1. Beschrijving Als u de toegang tot Microsoft-account gebruikers wilt beperken, stelt **u een actie in die moet worden uitgevoerd wanneer de aanvraag niet is geverifieerd** om u aan te **melden met Azure Active Directory**. Wanneer u deze functionaliteit instelt, vereist uw app dat alle aanvragen worden geverifieerd. Ook worden alle niet-geverifieerde aanvragen voor authenticatie via AAD omgeleid. Omdat u de URL van uw **certificaat verlener** hebt geconfigureerd voor het gebruik van de Tenant van het micro soft-account, kunnen alleen persoonlijke accounts worden geverifieerd.

   > [!CAUTION]
   > Het beperken van de toegang op deze manier is van toepassing op alle aanroepen naar uw app. Dit is mogelijk niet wenselijk voor apps met een openbaar beschik bare start pagina, zoals in veel toepassingen met één pagina. Voor dergelijke toepassingen kunt u **anonieme aanvragen (geen actie) toestaan** , zodat de verificatie zelf door de app hand matig wordt gestart. Zie voor meer informatie [verificatie stroom](overview-authentication-authorization.md#authentication-flow).

1. Selecteer **Opslaan**.

U bent nu klaar om micro soft-account te gebruiken voor verificatie in uw app.

## <a name="next-steps"></a><a name="related-content"> </a>Volgende stappen

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure-portal]: https://portal.azure.com/

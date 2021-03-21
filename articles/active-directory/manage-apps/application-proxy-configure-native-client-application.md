---
title: Systeem eigen client-apps publiceren-Azure AD | Microsoft Docs
description: In dit artikel wordt beschreven hoe u systeem eigen client-apps kunt gebruiken om te communiceren met de Azure AD-toepassingsproxy-connector om veilige externe toegang tot uw on-premises apps te bieden.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/12/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e830b52accefa0bcb76cc3434cae2fe94eed97db
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258659"
---
# <a name="how-to-enable-native-client-applications-to-interact-with-proxy-applications"></a>Systeem eigen client toepassingen inschakelen voor interactie met proxy toepassingen

U kunt Azure Active Directory (Azure AD)-toepassings proxy gebruiken om web-apps te publiceren, maar dit kan ook worden gebruikt voor het publiceren van systeem eigen client toepassingen die zijn geconfigureerd met de micro soft Authentication Library (MSAL). Systeemeigen client toepassingen verschillen van web-apps omdat ze zijn geïnstalleerd op een apparaat, terwijl web-apps via een browser worden geopend.

Voor het ondersteunen van systeem eigen client toepassingen accepteert toepassings proxy Azure AD-uitgegeven tokens die in de header worden verzonden. De toepassings proxy service heeft de verificatie voor de gebruikers. In deze oplossing worden geen toepassings tokens gebruikt voor verificatie.

![Relatie tussen eind gebruikers, Azure AD en gepubliceerde toepassingen](./media/application-proxy-configure-native-client-application/richclientflow.png)

Als u systeem eigen toepassingen wilt publiceren, gebruikt u de micro soft-verificatie bibliotheek, die zorgt voor verificatie en veel client omgevingen ondersteunt. Toepassings proxy past in de [desktop-app die een web-API aanroept namens een ondertekend gebruikers](../develop/authentication-flows-app-scenarios.md#desktop-app-that-calls-a-web-api-on-behalf-of-a-signed-in-user) scenario.

Dit artikel begeleidt u stapsgewijs door de vier stappen voor het publiceren van een systeem eigen toepassing met toepassings proxy en de Azure AD-verificatie bibliotheek.

## <a name="step-1-publish-your-proxy-application"></a>Stap 1: uw proxy toepassing publiceren

Publiceer uw proxy toepassing, net zoals elke andere toepassing en wijs gebruikers toe om toegang te krijgen tot uw toepassing. Zie [toepassingen publiceren met toepassings proxy](application-proxy-add-on-premises-application.md)voor meer informatie.

## <a name="step-2-register-your-native-application"></a>Stap 2: uw systeem eigen toepassing registreren

U moet uw toepassing nu als volgt registreren in azure AD:

1. Meld u aan bij de [Azure Active Directory Portal](https://aad.portal.azure.com/). Het **dash board** voor het **Azure Active Directory-beheer centrum** wordt weer gegeven.
1. Selecteer **Azure Active Directory** in de zijbalk. De pagina overzicht van **Azure Active Directory** wordt weer gegeven.
1. Selecteer **app-registraties** in de terzijde van Azure AD-overzicht. De lijst met alle app-registraties wordt weer gegeven.
1. Selecteer **Nieuwe registratie**. De pagina **een toepassing registreren** wordt weer gegeven.

   ![Een nieuwe app-registratie maken in de Azure Portal](./media/application-proxy-configure-native-client-application/create.png)

1. Geef in de kop **naam** een door de gebruiker gerichte weergave naam op voor uw toepassing.
1. Selecteer onder de kop **ondersteunde account typen** een toegangs niveau met behulp van de volgende richt lijnen:

   - Als u alleen accounts wilt richten die intern zijn voor uw organisatie, selecteert u **alleen accounts in deze organisatie Directory**.
   - Als u alleen zakelijke of educatieve klanten wilt richten, selecteert u **accounts in een organisatorische Directory**.
   - Als u de breedste set met micro soft-identiteiten wilt richten, selecteert u **accounts in een organisatorische map en persoonlijke micro soft-accounts**.
1. Onder **omleidings-URI** selecteert u **open bare client (mobiele & bureau blad)** en typt u de omleidings-URI `https://login.microsoftonline.com/common/oauth2/nativeclient` voor uw toepassing.
1. Selecteer en lees het **micro soft-platform beleid** en selecteer vervolgens **registreren**. Er wordt een overzichts pagina voor de nieuwe toepassings registratie gemaakt en weer gegeven.

Zie [toepassingen integreren met Azure Active Directory](../develop/quickstart-register-app.md)voor meer gedetailleerde informatie over het maken van een nieuwe toepassings registratie.

## <a name="step-3-grant-access-to-your-proxy-application"></a>Stap 3: toegang verlenen tot uw proxy toepassing

Nu u uw systeem eigen toepassing hebt geregistreerd, kunt u deze toegang verlenen tot andere toepassingen in uw directory, in dit geval voor toegang tot de proxy toepassing. De systeem eigen toepassing kan worden blootgesteld aan de proxy toepassing:

1. Selecteer in de zijbalk van de pagina nieuwe toepassings registratie de optie **API-machtigingen**. De pagina **API-machtigingen** voor de nieuwe toepassings registratie wordt weer gegeven.
1. Selecteer **een machtiging toevoegen**. De pagina **API-machtigingen voor aanvragen** wordt weer gegeven.
1. Selecteer onder de instelling **een API selecteren** de optie **api's mijn organisatie gebruikt**. Er wordt een lijst weer gegeven met de toepassingen in uw directory die Api's beschikbaar maken.
1. Typ in het zoekvak of blader naar de proxy toepassing die u in [stap 1: uw proxy](#step-1-publish-your-proxy-application)toepassing hebt gepubliceerd en selecteer vervolgens de proxy toepassing.
1. Selecteer in de sectie **Wat is het type machtigingen dat uw toepassing vereist?** het machtigings type. Als uw systeem eigen toepassing toegang moet hebben tot de proxy toepassings-API als de aangemelde gebruiker, kiest u **gedelegeerde machtigingen**.
1. Selecteer in de kop **machtigingen selecteren** de gewenste machtiging en selecteer vervolgens **machtigingen toevoegen**. De pagina **API-machtigingen** voor uw systeem eigen toepassing toont nu de API voor proxy toepassing en de machtiging die u hebt toegevoegd.

## <a name="step-4-add-the-microsoft-authentication-library-to-your-code-net-c-sample"></a>Stap 4: Voeg de micro soft-verificatie bibliotheek toe aan uw code (.NET C#-voor beeld)

Bewerk de systeem eigen toepassings code in de verificatie context van de micro soft Authentication Library (MSAL) om de volgende tekst op te laten: 

```         
// Acquire Access Token from AAD for Proxy Application
IPublicClientApplication clientApp = PublicClientApplicationBuilder
.Create(<App ID of the Native app>)
.WithDefaultRedirectUri() // will automatically use the default Uri for native app
.WithAuthority("https://login.microsoftonline.com/{<Tenant ID>}")
.Build();

AuthenticationResult authResult = null;
var accounts = await clientApp.GetAccountsAsync();
IAccount account = accounts.FirstOrDefault();

IEnumerable<string> scopes = new string[] {"<Scope>"};

try
 {
    authResult = await clientApp.AcquireTokenSilent(scopes, account).ExecuteAsync();
 }
    catch (MsalUiRequiredException ex)
 {
     authResult = await clientApp.AcquireTokenInteractive(scopes).ExecuteAsync();                
 }

if (authResult != null)
 {
  //Use the Access Token to access the Proxy Application

  HttpClient httpClient = new HttpClient();
  HttpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
  HttpResponseMessage response = await httpClient.GetAsync("<Proxy App Url>");
 }
```

De vereiste informatie in de voorbeeld code vindt u als volgt in de Azure AD-portal:

| Info vereist | Hoe u het kunt vinden in de Azure AD-Portal |
| --- | --- |
| \<Tenant ID> | **Azure Active Directory**  >  **Eigenschappen**  >  **Map-id** |
| \<App ID of the Native app> | **Toepassings registratie**  >  *uw systeem eigen toepassing*  >  **Overzicht**  >  **Toepassings-id** |
| \<Scope> | **Toepassings registratie**  >  *uw systeem eigen toepassing*  >  **API-machtigingen** > Klik op de machtigings-api (user_impersonation) > een deel venster met het bijschrift **user_impersonation** aan de rechter kant wordt weer gegeven. > het bereik de URL in het invoervak.
| \<Proxy App Url> | de externe URL en het pad naar de API

Nadat u de MSAL-code met deze para meters hebt bewerkt, kunnen uw gebruikers zich verifiëren bij native client toepassingen, zelfs als ze zich buiten het bedrijfs netwerk bevinden.

## <a name="next-steps"></a>Volgende stappen

Zie [systeem eigen apps in azure Active Directory](../azuread-dev/native-app.md)voor meer informatie over de systeem eigen toepassings stroom.

Meer informatie over het instellen van [eenmalige aanmelding voor toepassingen in azure Active Directory](sso-options.md#choosing-a-single-sign-on-method).
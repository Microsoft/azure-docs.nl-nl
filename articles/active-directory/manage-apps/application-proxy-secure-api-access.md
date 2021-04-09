---
title: Toegang tot on-premises Api's met Azure AD-toepassingsproxy
description: Met de toepassings proxy van Azure Active Directory kunnen systeem eigen apps veilig toegang krijgen tot Api's en bedrijfs logica die u on-premises of op Cloud-Vm's host.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/12/2020
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 9341646f32f6a2e05397b072d3f63186964fbd88
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99258979"
---
# <a name="secure-access-to-on-premises-apis-with-azure-ad-application-proxy"></a>Veilige toegang tot on-premises Api's met Azure AD-toepassingsproxy

U hebt mogelijk bedrijfs logica-Api's die on-premises worden uitgevoerd of die worden gehost op virtuele machines in de Cloud. Uw systeem eigen Android-, iOS-, Mac-of Windows-apps moeten communiceren met de API-eind punten om gegevens te gebruiken of gebruikers interactie te bieden. Met Azure AD-toepassingsproxy en de [micro soft Authentication Library (MSAL)](../azuread-dev/active-directory-authentication-libraries.md) kunnen uw eigen apps veilig toegang krijgen tot uw on-premises api's. Azure Active Directory-toepassingsproxy is een snellere en veiliger oplossing dan het openen van Firewall poorten en het beheren van verificatie en autorisatie op de app-laag.

Dit artikel begeleidt u bij het instellen van een Azure AD-toepassingsproxy-oplossing voor het hosten van een web-API-service waarmee systeem eigen apps toegang hebben.

## <a name="overview"></a>Overzicht

Het volgende diagram toont een traditionele manier om on-premises Api's te publiceren. Deze benadering vereist het openen van binnenkomende poorten 80 en 443.

![Traditionele API-toegang](./media/application-proxy-secure-api-access/overview-publish-api-open-ports.png)

In het volgende diagram ziet u hoe u Azure AD-toepassingsproxy kunt gebruiken om Api's veilig te publiceren zonder binnenkomende poorten te openen:

![Toegang tot Azure AD-toepassingsproxy-API](./media/application-proxy-secure-api-access/overview-publish-api-app-proxy.png)

De Azure AD-toepassingsproxy vormt de ruggen graat van de oplossing, werkt als een openbaar eind punt voor API-toegang en biedt verificatie en autorisatie. U kunt toegang krijgen tot uw Api's vanuit een groot aantal platformen met behulp van de [micro soft Authentication Library (MSAL)-](../azuread-dev/active-directory-authentication-libraries.md) bibliotheken.

Omdat Azure AD-toepassingsproxy-verificatie en-autorisatie zijn gebouwd op Azure AD, kunt u voorwaardelijke toegang van Azure AD gebruiken om ervoor te zorgen dat alleen vertrouwde apparaten toegang hebben tot Api's die zijn gepubliceerd via de toepassings proxy. Gebruik Azure AD join of Azure AD hybride gekoppeld voor Desk tops en intune-beheer voor apparaten. U kunt ook profiteren van Azure Active Directory Premium functies zoals Azure AD Multi-Factor Authentication en de beveiliging van de machine learning-back-up van [Azure Identity Protection](../identity-protection/overview-identity-protection.md).

## <a name="prerequisites"></a>Vereisten

Als u dit scenario wilt volgen, hebt u het volgende nodig:

- Beheerders toegang tot een Azure-map, met een account dat apps kan maken en registreren
- De voor beeld-Web-API en native client-apps uit [https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp](https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp)

## <a name="publish-the-api-through-application-proxy"></a>De API publiceren via toepassings proxy

Als u een API buiten uw intranet wilt publiceren via toepassings proxy, volgt u hetzelfde patroon als voor het publiceren van web-apps. Zie [zelf studie: een on-premises toepassing toevoegen voor externe toegang via toepassings proxy in azure Active Directory](application-proxy-add-on-premises-application.md)voor meer informatie.

De SecretAPI-Web-API publiceren via toepassings proxy:

1. Bouw en publiceer het voorbeeld SecretAPI-project als een ASP.NET-Web-app op uw lokale computer of intranet. Zorg ervoor dat u lokaal toegang tot de Web-App kunt krijgen.

1. Selecteer **Azure Active Directory** in de [Azure-portal](https://portal.azure.com). Selecteer vervolgens **bedrijfs toepassingen**.

1. Selecteer boven aan de pagina **bedrijfs toepassingen-alle toepassingen** de optie **nieuwe toepassing**.

1. Selecteer op de pagina **een toepassing toevoegen** de optie **on-premises toepassingen**. De pagina **uw eigen on-premises toepassing toevoegen** wordt weer gegeven.

1. Als er geen toepassings proxy connector is geïnstalleerd, wordt u gevraagd deze te installeren. Selecteer **toepassings proxy connector downloaden** om de connector te downloaden en te installeren.

1. Nadat u de toepassings proxy connector hebt geïnstalleerd, op de pagina **uw eigen on-premises toepassing toevoegen** :

   1. Voer *SecretAPI* in bij **naam**.

   1. Voer naast **interne URL** de URL in die u gebruikt voor toegang tot de API vanuit uw intranet.

   1. Zorg ervoor dat **verificatie vooraf** is ingesteld op **Azure Active Directory**.

   1. Selecteer boven aan de pagina **toevoegen** en wacht totdat de app is gemaakt.

   ![API-app toevoegen](./media/application-proxy-secure-api-access/3-add-api-app.png)

1. Selecteer op de pagina **bedrijfs toepassingen-alle toepassingen** de **SecretAPI** -app.

1. Selecteer op de pagina **SecretAPI-overzicht** de optie **Eigenschappen** in het linkernavigatievenster.

1. U wilt niet dat Api's beschikbaar zijn voor eind gebruikers in het deel venster **MyApps** , dus stel de optie **zichtbaar voor gebruikers** in op **Nee** onder aan de pagina **Eigenschappen** en selecteer vervolgens **Opslaan**.

   ![Niet zichtbaar voor gebruikers](./media/application-proxy-secure-api-access/5-not-visible-to-users.png)

U hebt uw web-API gepubliceerd via Azure AD-toepassingsproxy. Voeg nu gebruikers toe die toegang hebben tot de app.

1. Selecteer op de pagina **overzicht van SecretAPI** **gebruikers en groepen** in de linkernavigatiebalk.

1. Selecteer op de pagina **gebruikers en groepen** de optie **gebruiker toevoegen**.

1. Selecteer op de pagina **toewijzing toevoegen** de optie **gebruikers en groepen**.

1. Zoek en selecteer op de pagina **gebruikers en groepen** de gebruikers die toegang hebben tot de app, inclusief ten minste uzelf. Nadat u alle gebruikers hebt geselecteerd, selecteert u **selecteren**.

   ![Gebruiker selecteren en toewijzen](./media/application-proxy-secure-api-access/7-select-admin-user.png)

1. Selecteer op de pagina **toewijzing toevoegen** de optie **toewijzen**.

> [!NOTE]
> Voor Api's die gebruikmaken van geïntegreerde Windows-verificatie zijn mogelijk [extra stappen](./application-proxy-configure-single-sign-on-with-kcd.md)vereist.

## <a name="register-the-native-app-and-grant-access-to-the-api"></a>De systeem eigen app registreren en toegang verlenen tot de API

Systeem eigen apps zijn Program ma's die zijn ontwikkeld voor gebruik op een bepaald platform of apparaat. Voordat uw systeem eigen app verbinding kan maken en een API kan openen, moet u deze registreren in azure AD. De volgende stappen laten zien hoe u een systeem eigen app kunt registreren en toegang krijgt tot de Web-API die u via de toepassings proxy hebt gepubliceerd.

De systeem eigen app AppProxyNativeAppSample registreren:

1. Selecteer op de pagina **overzicht** van Azure Active Directory **app-registraties**, en selecteer boven in het deel venster **app-registraties** de optie **nieuwe registratie**.

1. Op de pagina **Een toepassing registreren** gaat u als volgt te werk:

   1. Voer onder **naam** *AppProxyNativeAppSample* in.

   1. Selecteer onder **Ondersteunde accounttypen** de optie **Accounts in een organisatieadreslijst en persoonlijke Microsoft-account**.

   1. Onder **omleidings-URL**, vervolg keuzelijst en selecteer **open bare client (mobiele & bureau blad)** en voer vervolgens in *https://login.microsoftonline.com/common/oauth2/nativeclient* .

   1. Selecteer **registreren** en wacht totdat de app is geregistreerd.

      ![Nieuwe toepassing registreren](./media/application-proxy-secure-api-access/8-create-reg-ga.png)

U hebt de AppProxyNativeAppSample-app nu geregistreerd in Azure Active Directory. Om uw eigen app toegang te geven tot de SecretAPI-Web-API:

1.   >  Selecteer de **AppProxyNativeAppSample** -app op de pagina overzicht van **app-registraties** Azure Active Directory.

1. Selecteer op de pagina **AppProxyNativeAppSample** de optie **API-machtigingen** in het linkernavigatievenster.

1. Selecteer op de pagina **API-machtigingen** de optie **een machtiging toevoegen**.

1. Selecteer op de eerste pagina **API-machtigingen voor aanvragen** de **api's mijn organisatie tabblad gebruikt** en zoek naar en selecteer **SecretAPI**.

1. Schakel op de pagina volgende **aanvraag API-machtigingen** het selectie vakje naast **user_impersonation** in en selecteer vervolgens **machtigingen toevoegen**.

    ![Een API selecteren](./media/application-proxy-secure-api-access/10-secretapi-added.png)

1. Terug op de pagina **API-machtigingen** kunt u **toestemming geven voor de beheerder toestaan** om te voor komen dat andere gebruikers individueel toestemming voor de app moeten krijgen.

## <a name="configure-the-native-app-code"></a>De systeem eigen app-code configureren

De laatste stap is het configureren van de systeem eigen app. Het volgende code fragment uit het bestand *Form1. cs* in de NativeClient-voor beeld-app veroorzaakt de MSAL-bibliotheek om het token te verkrijgen voor het aanvragen van de API-aanroep en koppelt dit aan de app-header.

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

Als u de systeem eigen app wilt configureren om verbinding te maken met Azure Active Directory en de API-app-proxy aan te roepen, moet u de waarden van de tijdelijke aanduidingen bijwerken in het *App.config* bestand van de voor beeld-app van de NativeClient met waarden van

- Plak de **Directory-id (Tenant)** in het `<add key="ida:Tenant" value="" />` veld. U kunt deze waarde (een GUID) zoeken en kopiëren op de pagina **overzicht** van een van uw apps.

- Plak de ID van de AppProxyNativeAppSample **-toepassing (client)** in het `<add key="ida:ClientId" value="" />` veld. U kunt deze waarde (een GUID) zoeken en kopiëren op de pagina **overzicht** van AppProxyNativeAppSample.

- Plak de **omleidings-URI** AppProxyNativeAppSample in het `<add key="ida:RedirectUri" value="" />` veld. U kunt deze waarde (een URI) zoeken en kopiëren van de AppProxyNativeAppSample- **verificatie** pagina.

- Plak de URI van de SecretAPI **-toepassings-id** in het `<add key="todo:TodoListResourceId" value="" />` veld. U kunt deze waarde (een URI) zoeken en kopiëren van de SecretAPI **een API** -pagina beschikbaar maken.

- Plak de URL van de SecretAPI- **Start pagina** in het `<add key="todo:TodoListBaseAddress" value="" />` veld. U kunt deze waarde (URL) zoeken en kopiëren van de SecretAPI **huisstijl** pagina.

Nadat u de para meters hebt geconfigureerd, bouwt u de systeem eigen app en voert u deze uit. Wanneer u de knop **Aanmelden** selecteert, kunt u zich aanmelden met de app en vervolgens een geslaagd scherm weer geven om te bevestigen dat het verbinding heeft gemaakt met de SecretAPI.

![Scherm afbeelding toont een bericht geheim dat ik is geslaagd en een knop OK.](./media/application-proxy-secure-api-access/success.png)

## <a name="next-steps"></a>Volgende stappen

- [Zelfstudie: Een on-premises toepassing voor externe toegang toevoegen via Application Proxy in Azure Active Directory](application-proxy-add-on-premises-application.md)
- [Snelstart: Een clienttoepassing configureren voor toegang tot web-API's](../develop/quickstart-configure-app-access-web-apis.md)
- [Systeem eigen client toepassingen inschakelen voor interactie met proxy toepassingen](application-proxy-configure-native-client-application.md)
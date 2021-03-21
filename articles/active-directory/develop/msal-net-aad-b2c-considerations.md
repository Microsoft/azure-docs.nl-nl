---
title: Azure AD B2C en MSAL.NET
titleSuffix: Microsoft identity platform
description: Overwegingen bij het gebruik van Azure AD B2C met de micro soft Authentication Library voor .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2020
ms.author: jeferrie
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: bdb9e12fdf721204ce98d23e5d5aeea535ddf23d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100574809"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>MSAL.NET gebruiken om gebruikers aan te melden met sociale identiteiten

U kunt MSAL.NET gebruiken om gebruikers met sociale identiteiten aan te melden met behulp van [Azure Active Directory B2C (Azure AD B2C)](../../active-directory-b2c/overview.md). Azure AD B2C is gebaseerd op het principe van beleid. In MSAL.NET, waarmee een beleid wordt gezet om een instantie te leveren.

- Wanneer u een instantie van de open bare client toepassing maakt, geeft u het beleid op als onderdeel van de-instantie.
- Als u een beleid wilt Toep assen, roept u een onderdrukking van `AcquireTokenInteractive` die de `authority` para meter accepteert.

Dit artikel is van toepassing op MSAL.NET 3. x. Zie [Azure AD B2C specifieke informatie in MSAL 2. x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x) in de MSAL.net-wiki op GitHub voor MSAL.net 2. x.

## <a name="authority-for-an-azure-ad-b2c-tenant-and-policy"></a>Instantie voor een Azure AD B2C Tenant en beleid

De indeling van de instantie voor Azure AD B2C is: `https://{azureADB2CHostname}/tfp/{tenant}/{policyName}`

- `azureADB2CHostname` -De naam van de Azure AD B2C Tenant plus de host. Bijvoorbeeld *contosob2c.b2clogin.com*.
- `tenant` -De domein naam of de Directory-ID (Tenant) van de Azure AD B2C Tenant. Bijvoorbeeld *contosob2c.onmicrosoft.com* of een GUID.
- `policyName` -De naam van de gebruikers stroom of het aangepaste beleid dat moet worden toegepast. Bijvoorbeeld een aanmeldings-of aanmeldings beleid zoals *b2c_1_susi*.

Zie [omleidings-Url's instellen op b2clogin.com](../../active-directory-b2c/b2clogin.md)voor meer informatie over Azure AD B2C-instanties.

## <a name="instantiating-the-application"></a>De toepassing instantiëren

Geef de instantie op door aan te roepen `WithB2CAuthority()` Wanneer u het toepassings object maakt:

```csharp
// Azure AD B2C Coordinates
public static string Tenant = "fabrikamb2c.onmicrosoft.com";
public static string AzureADB2CHostname = "fabrikamb2c.b2clogin.com";
public static string ClientID = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
public static string PolicySignUpSignIn = "b2c_1_susi";
public static string PolicyEditProfile = "b2c_1_edit_profile";
public static string PolicyResetPassword = "b2c_1_reset";

public static string AuthorityBase = $"https://{AzureADB2CHostname}/tfp/{Tenant}/";
public static string Authority = $"{AuthorityBase}{PolicySignUpSignIn}";
public static string AuthorityEditProfile = $"{AuthorityBase}{PolicyEditProfile}";
public static string AuthorityPasswordReset = $"{AuthorityBase}{PolicyResetPassword}";

application = PublicClientApplicationBuilder.Create(ClientID)
               .WithB2CAuthority(Authority)
               .Build();
```

## <a name="acquire-a-token-to-apply-a-policy"></a>Een token verkrijgen om een beleid toe te passen

Voor het verkrijgen van een token voor een met Azure AD B2C beveiligde API in een open bare client toepassing moet u de onderdrukkingen gebruiken met een instantie:

```csharp
AuthenticationResult authResult = null;
IEnumerable<IAccount> accounts = await application.GetAccountsAsync(policy);
IAccount account = accounts.FirstOrDefault();
try
{
    authResult = await application.AcquireTokenSilent(scopes, account)
                      .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
    authResult = await application.AcquireTokenInteractive(scopes)
                        .WithAccount(account)
                        .WithParentActivityOrWindow(ParentActivityOrWindow)
                        .ExecuteAsync();
}  
```

In het voor gaande code fragment:

- `policy` is een teken reeks met de naam van uw Azure AD B2C gebruikers stroom of aangepast beleid (bijvoorbeeld `PolicySignUpSignIn` ).
- `ParentActivityOrWindow` is vereist voor Android (de activiteit) en is optioneel voor andere platforms die ondersteuning bieden voor een bovenliggende UI zoals Windows in micro soft Windows en UIViewController in iOS. Zie [WithParentActivityOrWindow](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively#withparentactivityorwindow) op de MSAL-wiki voor meer informatie over het dialoog venster voor de gebruikers interface.

Het Toep assen van een gebruikers stroom of aangepast beleid (bijvoorbeeld voor het bewerken van het profiel van de gebruiker of het opnieuw instellen van het wacht woord), wordt momenteel uitgevoerd door aan te roepen `AcquireTokenInteractive` . Voor deze twee beleids regels gebruikt u niet het geretourneerde token/verificatie resultaat.

## <a name="profile-edit-policies"></a>Beleid voor profiel bewerking

Als u uw gebruikers wilt toestaan zich aan te melden met een sociale identiteit en vervolgens hun profiel te bewerken, past u het beleid Azure AD B2C profiel bewerken toe.

Dit doet u door `AcquireTokenInteractive` met de autoriteit voor dat beleid aan te roepen. Omdat de gebruiker al is aangemeld en een actieve cookie sessie heeft, gebruikt `Prompt.NoPrompt` u om te voor komen dat het dialoog venster voor het selecteren van accounts wordt weer gegeven.

```csharp
private async void EditProfileButton_Click(object sender, RoutedEventArgs e)
{
    IEnumerable<IAccount> accounts = await application.GetAccountsAsync(PolicyEditProfile);
    IAccount account = accounts.FirstOrDefault();
    try
    {
        var authResult = await application.AcquireTokenInteractive(scopes)
                            .WithPrompt(Prompt.NoPrompt),
                            .WithAccount(account)
                            .WithB2CAuthority(AuthorityEditProfile)
                            .ExecuteAsync();
     }
    catch
    {
    }
}
```

## <a name="resource-owner-password-credentials-ropc"></a>Wachtwoord referenties van de resource-eigenaar (ROPC)

Zie voor meer informatie over de ROPC-stroom [Aanmelden met wachtwoord referenties](v2-oauth-ropc.md)voor de resource-eigenaar.

De ROPC-stroom wordt **niet aanbevolen** omdat het vragen van een gebruiker voor het wacht woord in uw toepassing niet veilig is. Zie [Wat is de oplossing voor het groeiende probleem van wacht woorden?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)voor meer informatie over dit probleem.

Door gebruikers naam/wacht woord te gebruiken in een ROPC-stroom, worden er diverse dingen gedood:

- Kern principes van moderne identiteit: het wacht woord kan worden gevist of geplayd omdat het gedeelde geheim kan worden onderschept. De ROPC is per definitie incompatibel met niet-wacht woorden.
- Gebruikers die MFA nodig hebben, kunnen zich niet aanmelden (omdat er geen interactie is).
- Gebruikers kunnen eenmalige aanmelding (SSO) niet gebruiken.

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>De ROPC-stroom configureren in Azure AD B2C

Maak in uw Azure AD B2C-Tenant een nieuwe gebruikers stroom en selecteer **Aanmelden met ROPC** om ROPC in te scha kelen voor de gebruikers stroom. Zie [Configure the resource owner password data flow](../../active-directory-b2c/add-ropc-policy.md)voor meer informatie.

`IPublicClientApplication` bevat de- `AcquireTokenByUsernamePassword` methode:

```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

Deze `AcquireTokenByUsernamePassword` methode heeft de volgende para meters:

- De *bereiken* waarvoor een toegangs token moet worden verkregen.
- Een *gebruikers naam*.
- Een SecureString- *wacht woord* voor de gebruiker.

### <a name="limitations-of-the-ropc-flow"></a>Beperkingen van de ROPC-stroom

De ROPC-stroom **werkt alleen voor lokale accounts**, waarbij uw gebruikers zijn geregistreerd bij Azure AD B2C met behulp van een e-mail adres of gebruikers naam. Deze stroom werkt niet wanneer federeren naar een externe ID-provider die wordt ondersteund door Azure AD B2C (Facebook, Google, enzovoort).

## <a name="google-auth-and-embedded-webview"></a>Google auth en embedded webweergave

Als u Google als id-provider gebruikt, raden we u aan om de systeem browser te gebruiken als Google [verificatie van Inge sloten webweergave](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html)niet toestaat. Is momenteel `login.microsoftonline.com` een vertrouwde instantie met Google en werkt met een Inge sloten webweergave. Is echter `b2clogin.com` geen vertrouwde instantie met Google, zodat gebruikers geen verificatie kunnen uitvoeren.

Er wordt een update voor dit [probleem](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688) geboden als er iets wordt gewijzigd.

## <a name="token-caching-in-msalnet"></a>Token-caching in MSAL.NET

### <a name="known-issue-with-azure-ad-b2c"></a>Bekend probleem met Azure AD B2C

MSAL.NET ondersteunt een [token cache](/dotnet/api/microsoft.identity.client.tokencache). De cache sleutel voor tokens is gebaseerd op de claims die zijn geretourneerd door de ID-provider (IdP).

Momenteel heeft MSAL.NET twee claims nodig om een token cache sleutel te bouwen:

- `tid` (de Azure AD-Tenant-ID)
- `preferred_username`

Beide claims ontbreken mogelijk in Azure AD B2C scenario's, omdat niet alle leveranciers van sociale identiteiten (Facebook, Google en anderen) ze retour neren in de tokens die ze retour neren naar Azure AD B2C.

Een symptoom van een dergelijk scenario is dat MSAL.NET retourneert `Missing from the token response` Wanneer u de `preferred_username` claim waarde opent in tokens die zijn uitgegeven door Azure AD B2C. MSAL gebruikt de `Missing from the token response` waarde voor voor het `preferred_username` onderhouden van cache Kruis compatibiliteit tussen bibliotheken.

### <a name="workarounds"></a>Tijdelijke oplossingen

#### <a name="mitigation-for-missing-tenant-id"></a>Beperking voor ontbrekende Tenant-ID

De voorgestelde tijdelijke oplossing is het gebruik [van caching op basis van beleid](#acquire-a-token-to-apply-a-policy) dat eerder is beschreven.

U kunt de claim ook gebruiken `tid` Als u [aangepaste beleids regels](../../active-directory-b2c/custom-policy-get-started.md) gebruikt in azure AD B2C. Aangepaste beleids regels kunnen aanvullende claims naar uw toepassing retour neren met behulp van de [trans formatie van claims](../../active-directory-b2c/claims-transformation-technical-profile.md).

#### <a name="mitigation-for-missing-from-the-token-response"></a>Risico beperking voor ' ontbreekt in het token antwoord '

Een optie is om de claim te gebruiken `name` in plaats van `preferred_username` . Als u de claim wilt toevoegen aan de `name` ID-tokens die zijn uitgegeven door Azure AD B2C, selecteert u **weergave naam** wanneer u de gebruikers stroom configureert.

Zie [zelf studie: gebruikers stromen maken in azure AD B2C](../../active-directory-b2c/tutorial-create-user-flows.md)voor meer informatie over het opgeven welke claims door uw gebruikers stromen worden geretourneerd.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het interactief verkrijgen van tokens met MSAL.NET voor Azure AD B2C-toepassingen vindt u in het volgende voor beeld.

| Voorbeeld | Platform | Beschrijving|
|------ | -------- | -----------|
|[Active-Directory-B2C-xamarin-native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin iOS, Xamarin Android, UWP | Een Xamarin Forms-app die gebruikmaakt van MSAL.NET om gebruikers te verifiëren via Azure AD B2C en vervolgens toegang krijgt tot een web-API met de geretourneerde tokens.|

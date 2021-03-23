---
title: MSAL.NET-client toepassingen initialiseren | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het initialiseren van open bare client-en vertrouwelijke client toepassingen met behulp van de micro soft Authentication Library voor .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/18/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 7ff61811e8b736f8f6d104a253cfe5dc5e76c428
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104771359"
---
# <a name="initialize-client-applications-using-msalnet"></a>Client toepassingen initialiseren met MSAL.NET
In dit artikel wordt beschreven hoe u open bare client-en vertrouwelijke client toepassingen initialiseert met behulp van de micro soft Authentication Library voor .NET (MSAL.NET).  Zie [open bare client-en vertrouwelijke client toepassingen](msal-client-applications.md)voor meer informatie over de typen client toepassingen.

Met MSAL.NET 3. x is de aanbevolen manier om een toepassing te instantiëren met behulp van de toepassings bouwers: `PublicClientApplicationBuilder` en `ConfidentialClientApplicationBuilder` . Ze bieden een krachtig mechanisme om de toepassing te configureren, hetzij vanuit de code, hetzij vanuit een configuratie bestand, of zelfs door beide benaderingen te combi neren.

[API-referentie documentatie](/dotnet/api/microsoft.identity.client)  |  [Pakket op NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client/)  |  [Bron code](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)  |  van bibliotheek [Code voorbeelden](sample-v2-code.md)

## <a name="prerequisites"></a>Vereisten
Voordat u een toepassing initialiseert, moet u [deze eerst registreren](quickstart-register-app.md) , zodat uw app kan worden geïntegreerd met het micro soft Identity-platform.  Na de registratie hebt u mogelijk de volgende informatie nodig (die kan worden gevonden in de Azure Portal):

- De client-ID (een teken reeks die een GUID vertegenwoordigt)
- De URL van de identiteits provider (de naam van het exemplaar) en de aanmeldings doel groep voor uw toepassing. Deze twee para meters worden gezamenlijk bekend als de-instantie.
- De Tenant-ID als u alleen een line-of-Business-toepassing schrijft voor uw organisatie (ook wel een toepassing met één Tenant genoemd).
- Het toepassings geheim (client Secret String) of certificaat (van het type X509Certificate2) als het een vertrouwelijke client-app is.
- Voor web-apps en soms voor open bare client-apps (met name wanneer uw app een Broker moet gebruiken), moet u ook de redirectUri instellen waar de ID-provider verbinding maakt met uw toepassing met de beveiligings tokens.

## <a name="ways-to-initialize-applications"></a>Manieren om toepassingen te initialiseren
Er zijn veel verschillende manieren om client toepassingen te instantiëren.

### <a name="initializing-a-public-client-application-from-code"></a>Een open bare client toepassing vanuit code initialiseren

Met de volgende code wordt een open bare client toepassing geïnstantieerd, waarbij gebruikers zich aanmelden in Microsoft Azure de open bare Cloud, met hun werk-en school accounts of hun persoonlijke micro soft-accounts.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-code"></a>Een vertrouwelijke client toepassing initialiseren vanuit code

Op dezelfde manier maakt de volgende code een instantie van een vertrouwelijke toepassing (een web-app die zich bevindt bij `https://myapp.azurewebsites.net` ) tokens van gebruikers in de Microsoft Azure open bare Cloud, met hun werk-en school accounts of hun persoonlijke micro soft-accounts. De toepassing wordt geïdentificeerd met de ID-provider door een client geheim te delen:

```csharp
string redirectUri = "https://myapp.azurewebsites.net";
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithClientSecret(clientSecret)
    .WithRedirectUri(redirectUri )
    .Build();
```

Zoals u mogelijk weet, wilt u in productie in plaats van een client geheim te gebruiken, dan kunt u delen met Azure AD een certificaat. De code ziet er dan als volgt uit:

```csharp
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithCertificate(certificate)
    .WithRedirectUri(redirectUri )
    .Build();
```

### <a name="initializing-a-public-client-application-from-configuration-options"></a>Een open bare client toepassing vanuit configuratie opties initialiseren

Met de volgende code wordt een open bare client toepassing geïnstantieerd vanuit een configuratie object, die via een programma kan worden ingevuld of gelezen vanuit een configuratie bestand:

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-configuration-options"></a>Een vertrouwelijke client toepassing vanuit configuratie opties initialiseren

Hetzelfde soort patroon is van toepassing op vertrouwelijke client toepassingen. U kunt ook andere para meters toevoegen met behulp van `.WithXXX` wijzigings functies (hier een certificaat).

```csharp
ConfidentialClientApplicationOptions options = GetOptions(); // your own method
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(options)
    .WithCertificate(certificate)
    .Build();
```

## <a name="builder-modifiers"></a>Builder-aanpassingen

In de code fragmenten die gebruikmaken van toepassings bouwers, kan een aantal `.With` methoden worden toegepast als para meters (bijvoorbeeld `.WithCertificate` en `.WithRedirectUri` ). 

### <a name="modifiers-common-to-public-and-confidential-client-applications"></a>Gewijzigde opties voor open bare en vertrouwelijke client toepassingen

De opties die u kunt instellen op een open bare client of de opbouw functie voor vertrouwelijke client toepassingen zijn:

|Modifier | Beschrijving|
|--------- | --------- |
|`.WithAuthority()` 7 onderdrukkingen | Hiermee stelt u de standaard instantie van de toepassing in op een Azure AD-instantie, met de mogelijkheid om de Azure-Cloud, de doel groep, de Tenant (Tenant-ID of domein naam) te kiezen of rechtstreeks de CA-URI op te geven.|
|`.WithAdfsAuthority(string)` | Hiermee stelt u de standaard instantie van de toepassing als ADFS-instantie.|
|`.WithB2CAuthority(string)` | Hiermee stelt u de standaard instantie van de toepassing als Azure AD B2C-instantie.|
|`.WithClientId(string)` | Hiermee wordt de client-ID overschreven.|
|`.WithComponent(string)` | Hiermee stelt u de naam van de bibliotheek met behulp van MSAL.NET (voor de redenen van telemetrie). |
|`.WithDebugLoggingCallback()` | Als deze wordt aangeroepen, wordt de toepassing aangeroepen om `Debug.Write` fout opsporings traceringen in te scha kelen. Zie [logboek registratie](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) voor meer informatie.|
|`.WithExtraQueryParameters(IDictionary<string,string> eqp)` | Stel de extra query parameters op toepassings niveau in die in alle verificatie aanvragen worden verzonden. Dit is Overschrijf bare op elk niveau van de methode voor het verkrijgen van tokens (met hetzelfde `.WithExtraQueryParameters pattern` ).|
|`.WithHttpClientFactory(IMsalHttpClientFactory httpClientFactory)` | Hiermee schakelt u geavanceerde scenario's in, zoals het configureren van een HTTP-proxy of het afdwingen van MSAL om een bepaalde httpclient maakt te gebruiken (bijvoorbeeld in ASP.NET Core web apps/Api's).|
|`.WithLogging()` | Als u de toepassing aanroept, wordt een call back aangeroepen met traceringen voor fout opsporing. Zie [logboek registratie](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) voor meer informatie.|
|`.WithRedirectUri(string redirectUri)` | Onderdrukt de standaard omleidings-URI. In het geval van open bare client toepassingen is dit handig voor scenario's met betrekking tot de Broker.|
|`.WithTelemetry(TelemetryCallback telemetryCallback)` | Hiermee stelt u de gemachtigde in die wordt gebruikt voor het verzenden van telemetrie.|
|`.WithTenantId(string tenantId)` | Onderdrukt de Tenant-ID of de beschrijving van de Tenant.|

### <a name="modifiers-specific-to-xamarinios-applications"></a>Aanpassingen specifiek voor Xamarin. iOS-toepassingen

De opties die u kunt instellen voor een open bare-client toepassings Builder op Xamarin. iOS zijn:

|Modifier | Beschrijving|
|--------- | --------- |
|`.WithIosKeychainSecurityGroup()` | **Alleen Xamarin. IOS**: Hiermee stelt u de beveiligings groep IOS-sleutel keten in (voor de cache persistentie).|

### <a name="modifiers-specific-to-confidential-client-applications"></a>Opties die specifiek zijn voor vertrouwelijke client toepassingen

De opties die u kunt instellen voor een vertrouwelijk client toepassings Builder zijn:

|Modifier | Beschrijving|
|--------- | --------- |
|`.WithCertificate(X509Certificate2 certificate)` | Hiermee stelt u het certificaat dat de toepassing identificeert met Azure AD.|
|`.WithClientSecret(string clientSecret)` | Hiermee stelt u het client geheim (app-wacht woord) voor het identificeren van de toepassing met Azure AD.|

Deze wijzigings functies sluiten elkaar wederzijds uit. Als u beide opgeeft, genereert MSAL een zinvolle uitzonde ring.

### <a name="example-of-usage-of-modifiers"></a>Voor beeld van het gebruik van para meters

We gaan ervan uit dat uw toepassing een line-of-Business-toepassing is, die alleen voor uw organisatie is.  Vervolgens kunt u het volgende schrijven:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAuthority(AzureCloudInstance.AzurePublic, tenantId)
        .Build();
```

Het is belang rijk dat de programmering van nationale Clouds nu wordt vereenvoudigd. Als u wilt dat uw toepassing een multi tenant toepassing in een nationale Cloud is, kunt u bijvoorbeeld het volgende schrijven:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAuthority(AzureCloudInstance.AzureUsGovernment, AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

Er is ook een onderdrukking voor ADFS (ADFS 2019 wordt momenteel niet ondersteund):
```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Ten slotte, als u een Azure AD B2C-ontwikkelaar bent, kunt u uw Tenant als volgt opgeven:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

## <a name="next-steps"></a>Volgende stappen

Nadat u de client toepassing hebt geïnitialiseerd, is uw volgende taak het toevoegen van ondersteuning voor gebruikers aanmelding, geautoriseerde API-toegang of beide.

Onze documentatie voor toepassings scenario's bevat richt lijnen voor het aanmelden van een gebruiker en het verkrijgen van een toegangs token voor toegang tot een API namens die gebruiker:

- [Web-app die gebruikers aanmeldt: aanmelden en afmelden](scenario-web-app-sign-user-sign-in.md)
- [Web-app die web-Api's aanroept: een Token ophalen](scenario-web-app-call-api-acquire-token.md)

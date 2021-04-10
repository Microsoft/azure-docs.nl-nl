---
title: Overzicht van de micro soft Identity Web-verificatie bibliotheek
titleSuffix: Microsoft identity platform
description: Meer informatie over micro soft Identity Web, een verificatie-en autorisatie bibliotheek voor ASP.NET Core toepassingen die worden geïntegreerd met Azure Active Directory, Azure AD B2C en Microsoft Graph en andere web-Api's.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/09/2020
ms.author: jmprieur
ms.reviewer: marsma
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 8bc9773fa7e1b3db2e249e803f4d2a3cf39edb48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100102543"
---
# <a name="microsoft-identity-web-authentication-library"></a>Micro soft Identity Web authentication-bibliotheek

Micro soft Identity web is een reeks ASP.NET Core bibliotheken waarmee u ondersteuning voor verificatie en autorisatie kunt toevoegen aan Web apps en Web-Api's die worden geïntegreerd met het micro soft Identity-platform. Het biedt een multisurface API-gebruiks vriendelijke laag die samen ASP.NET Core, de bijbehorende verificatie-middleware en de [micro soft Authentication Library (MSAL) voor .net](https://github.com/azuread/microsoft-authentication-library-for-dotnet)bindt.

## <a name="supported-application-scenarios"></a>Ondersteunde toepassings scenario's

Als u ASP.NET Core web-apps of Web-Api's bouwt en Azure Active Directory (Azure AD) of Azure AD B2C voor Identity and Access Management (IAM) wilt gebruiken, raden wij u aan om micro soft Identity web te gebruiken voor al deze scenario's:

- [Web-app waarmee gebruikers worden aangemeld](scenario-web-app-sign-user-overview.md)
- [Een web-app die gebruikers aantekent en een web-API namens hun naam aanroept](scenario-web-app-call-api-overview.md)
- [Beveiligde web-API waarmee alleen geverifieerde gebruikers toegang hebben](scenario-protected-web-api-overview.md)
- [Beveiligde web-API die een andere (downstream) Web-API aanroept namens de aangemelde gebruiker](scenario-web-api-call-api-overview.md)

## <a name="get-the-library"></a>De bibliotheek ophalen

U kunt micro soft Identity web ophalen via [NuGet](#nuget), [.net core project-sjablonen](#project-templates)en [github](#github).

#### <a name="nuget"></a>NuGet

Micro soft Identity web is beschikbaar op NuGet als een reeks pakketten die modulaire functionaliteit bieden op basis van de behoeften van uw app. Gebruik de `dotnet add` opdracht of Visual Studio van het NuGet- **pakket beheer** van .net CLI om de pakketten te installeren die geschikt zijn voor uw project:

- [Micro soft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web) : het belangrijkste pakket. Vereist voor alle apps die gebruikmaken van micro soft Identity web.
- [Micro soft. Identity. Web. UI](https://www.nuget.org/packages/Microsoft.Identity.Web.UI) -optioneel. Voegt gebruikers interface toe voor het aanmelden en afmelden van gebruikers en een gekoppelde controller voor web-apps.
- [Micro soft. Identity. Web. MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) -optioneel. Biedt vereenvoudigde interactie met de Microsoft Graph-API.
- [Micro soft. Identity. Web. MicrosoftGraphBeta](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraphBeta) -optioneel. Biedt vereenvoudigde interactie met het bèta- [eind punt](/graph/api/overview?view=graph-rest-beta&preserve-view=true)van Microsoft Graph-API.

#### <a name="project-templates"></a>Project sjablonen

Micro soft Identity web project-sjablonen zijn opgenomen in .NET 5,0 en kunnen worden gedownload voor ASP.NET Core 3,1-projecten.

Als u ASP.NET Core 3,1 gebruikt, installeert u de sjablonen met de .NET CLI:

```dotnetcli
dotnet new --install Microsoft.Identity.Web.ProjectTemplates::1.0.0
```

Het volgende diagram toont een weer gave op hoog niveau van de ondersteunde typen apps en de bijbehorende argumenten:

:::image type="content" source="media/microsoft-identity-web-overview/diagram-microsoft-identity-web-templates.png" lightbox="media/microsoft-identity-web-overview/diagram-microsoft-identity-web-templates.png" alt-text="Diagram van de beschik bare dot net CLI-project sjablonen voor micro soft Identity Web":::
<br /><sup><b>*</b></sup>`MultiOrg`wordt niet ondersteund met `webapi2` , maar kan worden ingeschakeld in *appsettings.js* door een Tenant in te stellen op `common` of`organizations`
<br /><sup><b>**</b></sup>`--calls-graph`wordt niet ondersteund voor Azure AD B2C

Dit voor beeld van een .NET CLI-opdracht die is opgehaald uit onze [zelf studie voor de razendsnelle server](tutorial-blazor-server.md), genereert een nieuw razendsnelle server project met daarin de juiste pakketten en begin code (waarden van de tijdelijke aanduidingen weer gegeven):

```dotnetcli
dotnet new blazorserver2 --auth SingleOrg --calls-graph --client-id "00000000-0000-0000-0000-000000000000" --tenant-id "11111111-1111-1111-1111-111111111111" --output my-blazor-app
```

#### <a name="github"></a>GitHub

Micro soft Identity web is een open-source project dat wordt gehost op GitHub: <a href="https://github.com/AzureAD/microsoft-identity-web" target="_blank">AzureAD/micro soft-Identity-Web</a>

De [wiki van de opslag plaats](https://github.com/AzureAD/microsoft-identity-web/wiki) bevat aanvullende documentatie en als u hulp nodig hebt of een bug ontdekt, kunt u [een probleem oplossen](https://github.com/AzureAD/microsoft-identity-web/issues).

## <a name="features"></a>Functies

Micro soft Identity web bevat verschillende functies die niet worden gegeven als u de standaard ASP.NET 3,1-project sjablonen gebruikt.

| Functie                                                                                  | ASP.NET Core 3,1                                                     | Micro soft Identity Web                                                                                  |
|------------------------------------------------------------------------------------------|----------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
| [Gebruikers aanmelden](scenario-web-app-sign-user-app-configuration.md) in web-apps             | <li>Werk- of schoolaccounts<li>Sociale identiteiten (met Azure AD B2C) | <li>Werk- of schoolaccounts<li>Persoonlijk Microsoft-account<li>Sociale identiteiten (met Azure AD B2C)     |
| [Web-Api's beveiligen](scenario-protected-web-api-app-configuration.md#microsoftidentityweb) | <li>Werk- of schoolaccounts<li>Sociale identiteiten (met Azure AD B2C) | <li>Werk- of schoolaccounts<li>Persoonlijk Microsoft-account<li>Sociale identiteiten (met Azure AD B2C)     |
| Validatie van de verlener in apps met meerdere tenants                                                   | No                                                                   | Ja, voor [alle Clouds](authentication-national-cloud.md) en [Azure AD B2C](../../active-directory-b2c/index.yml) |
| Web-app/API [aanroepen van micro soft Graph] [scenario-API-aanroep-Graph]                             | Nee                                                                   | Ja                                                                                                     |
| Web-app/API [aanroepen van Web-API] [scenario-API-aanroep-API]                                       | Nee                                                                   | Ja                                                                                                     |
| Ondersteunt certificaat referenties                                                         | No                                                                   | Ja, met inbegrip van Azure Key Vault                                                                          |
| Ondersteuning voor incrementele toestemming en voorwaardelijke toegang in web-apps                           | No                                                                   | Ja, in MVC, haar pagina's en ultra meer                                                                    |
| Token versleutelings certificaten in Web-Api's                                                | Nee                                                                   | Ja                                                                                                     |
| [Bereiken/validatie van app-rollen] [scenario-API-validatie] in Web-Api's                        | Nee                                                                   | Ja                                                                                                     |
| `WWW-Authenticate` genereren van header in Web-Api's                                         | Nee                                                                   | Ja                                                                                                     |

## <a name="next-steps"></a>Volgende stappen

Als u micro soft Identity web in actie wilt zien, kunt u de zelf studie voor de razendsnelle server proberen:

[Zelfstudie: Een Blazor Server-app maken die gebruikmaakt van het Microsoft-identiteitsplatform voor verificatie](tutorial-blazor-server.md)

De micro soft Identity Web-wiki op GitHub bevat een uitgebreide referentie documentatie voor verschillende aspecten van de bibliotheek. U kunt bijvoorbeeld de naslag informatie over het gebruik van certificaten, incrementele toestemming en voorwaardelijke toegang vinden op de volgende locatie:

- <a href="https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates" target="_blank">Certificaten gebruiken met micro soft. Identity. Web</a> (github)
- <a href="https://github.com/AzureAD/microsoft-identity-web/wiki/Managing-incremental-consent-and-conditional-access" target="_blank">Incrementele toestemming en voorwaardelijke toegang</a> (github)

<!-- LINKS -->
<!--  [miw-certs]: microsoft-identity-web-certificates.md  -->
<!--  [miw-certs-decrypt]: microsoft-identity-web-certificates.md#decryption-certificates  -->
<!--  [miw-inc-consent-ca-header]: microsoft-identity-web-consent-conditional-access.md#handling-incremental-consent-or-conditional-access-in-web-apis  -->
<!--  [miw-inc-consent-ca]: microsoft-identity-web-consent-conditional-access.md  -->
[scenario-API-call-API]: scenario-Web-API-call-API-call-API. MD # optie-1-call-micro soft-Graph-with-the-SDK  
[scenario-API-aanroep-Graph]: scenario-Web-API-call-API-call-API. MD # optie-1-call-micro soft-Graph-with-the-SDK  
[scenario-API-validatie]: scenario-protected-web-api-verification-scope-app-roles.md  

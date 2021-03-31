---
title: Machtigingen in Azure Active Directory | Microsoft Docs
description: Leer alles over machtigingen in Azure Active Directory en hoe u deze gebruikt.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: jesakowi
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 2b85115d905cb6a7eb7c6aed64a4834425d2f1d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92366391"
---
# <a name="permissions-and-consent-in-the-azure-active-directory-v10-endpoint"></a>Machtigingen en toestemming in het Azure Active Directory v 1.0-eind punt

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Azure Active Directory (Azure AD) maakt uitgebreid gebruik van machtigingen voor zowel OAuth- als OpenID Connect (OIDC)-stromen. Als uw app een toegangstoken van Azure AD ontvangt, zal het token claims bevatten die de machtigingen beschrijven die uw app heeft voor een specifieke resource.

*Machtigingen*, ook bekend als *scopes*, maken de autorisatie eenvoudig voor de resource omdat de resource alleen moet controleren of het token de juiste machtiging bevat voor de wille keurige API die de app aanroept.

## <a name="types-of-permissions"></a>Typen machtigingen

Azure AD definieert twee soorten machtigingen:

* **Gedelegeerde machtigingen** - Deze worden gebruikt door apps met een aangemelde gebruiker. Voor deze apps geeft de gebruiker of een beheerder toestemming voor de machtigingen die de app aanvraagt. De app krijgt vervolgens machtigingen om als de aangemelde gebruiker te handelen wanneer een API wordt aangeroepen. Afhankelijk van de API kan de gebruiker mogelijk niet rechtstreeks toestemming geven voor de API en moet er in plaats daarvan [een beheerder zijn om "toestemming van de beheerder" te bieden](../develop/howto-convert-app-to-be-multi-tenant.md).
* **Toepassingsmachtigingen** - Deze worden gebruikt door apps die worden uitgevoerd zonder aangemelde gebruiker, zoals apps die als achtergrondservices of daemons worden uitgevoerd. Toepassings machtigingen kunnen alleen worden [gezonden door beheerders](../develop/v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant) , omdat ze doorgaans krachtig zijn en toegang hebben tot gegevens op gebruikers grenzen of gegevens die anders zouden worden beperkt tot beheerders. Gebruikers die zijn gedefinieerd als eigen aren van de bron toepassing (de API die de machtigingen publiceert), kunnen ook toepassings machtigingen verlenen voor de Api's waarvan ze eigenaar zijn.

Effectieve machtigingen zijn de machtigingen die uw app zal hebben wanneer deze aanvragen bij een API indient. 

* Voor gedelegeerde machtigingen vormen de effectieve machtigingen van uw app de minst geprivilegieerde kruising van de gedelegeerde machtigingen die aan de app zijn verleend (via toestemming) en de machtigingen van de gebruiker die op dit moment is aangemeld. Uw app kan nooit meer machtigingen hebben dan de aangemelde gebruiker. De machtigingen van de aangemelde gebruiker kunnen in organisaties worden bepaald door beleid of door lidmaatschap in een of meer beheerdersrollen. Zie [Administrator role permissions in azure AD](../roles/permissions-reference.md)(Engelstalig) voor meer informatie over welke beheerders rollen toestemming kunnen geven voor gedelegeerde machtigingen.
    Stel bijvoorbeeld dat in Microsoft Graph de gedelegeerde machtiging `User.ReadWrite.All` aan uw app is verleend. Deze machtiging verleent uw app in feite machtigingen om het profiel van elke gebruiker in een organisatie te lezen en bij te werken. Als de aangemelde gebruiker een globale beheerder is, kan uw app het profiel van elke gebruiker in de organisatie bijwerken. Als de aangemelde gebruiker echter geen beheerdersrol heeft, zal uw app alleen het profiel van de aangemelde gebruiker kunnen bijwerken. De app kan geen profielen van andere gebruikers in de organisatie bijwerken omdat de gebruiker namens welke de app machtigingen heeft om te handelen niet over deze rechten beschikt.
* De effectieve machtigingen van uw app bestaan in het geval van toepassingsmachtigingen uit de volledige rechten die door de machtiging zijn geïmpliceerd. Een app met de toepassingsmachtiging `User.ReadWrite.All` kan bijvoorbeeld het profiel van elke gebruiker in de organisatie bijwerken.

## <a name="permission-attributes"></a>Machtigingskenmerken
Machtigingen in Azure AD hebben een aantal eigenschappen die gebruikers, beheerders of app-ontwikkelaars helpen om weloverwogen beslissingen te nemen over datgene waar de machtiging toegang toe verleent.

> [!NOTE]
> U kunt de machtigingen die een Azure AD-toepassing of service-principal beschikbaar maakt, weergeven via Azure Portal of PowerShell. Probeer dit script om de machtigingen weer te geven die door Microsoft Graph beschikbaar worden gemaakt.
> ```powershell
> Connect-AzureAD
> 
> # Get OAuth2 Permissions/delegated permissions
> (Get-AzureADServicePrincipal -filter "DisplayName eq 'Microsoft Graph'").OAuth2Permissions
> 
> # Get App roles/application permissions
> (Get-AzureADServicePrincipal -filter "DisplayName eq 'Microsoft Graph'").AppRoles
> ```

| Naam van eigenschap | Beschrijving | Voorbeeld |
| --- | --- | --- |
| `ID` | Een GUID-waarde met een unieke identificatie voor deze machtiging. | 570282fd-fa5c-430d-a7fd-fc8dc98a9dca |
| `IsEnabled` | Geeft aan of deze machtiging beschikbaar is voor gebruik. | true |
| `Type` | Geeft aan of deze machtiging toestemming van een gebruiker of beheerder vereist. | Gebruiker |
| `AdminConsentDescription` | Een beschrijving die aan beheerders wordt weergegeven wanneer toestemming van de beheerder nodig is | Hiermee kan e-mail in de postvakken van gebruikers worden gelezen met de app. |
| `AdminConsentDisplayName` | De beschrijvende naam die aan beheerders wordt weergegeven wanneer toestemming van de beheerder nodig is. | E-mail van de gebruiker lezen |
| `UserConsentDescription` | Een beschrijving die aan gebruikers wordt weergegeven wanneer toestemming van een gebruiker nodig is. |  Hiermee kan e-mail in uw postvak worden gelezen met de app. |
| `UserConsentDisplayName` | De beschrijvende naam die aan gebruikers wordt weergegeven wanneer toestemming van een gebruiker nodig is. | Uw e-mail lezen |
| `Value` | De tekenreeks die wordt gebruikt om de machtiging te identificeren tijdens OAuth 2.0-autorisatiestromen. `Value` kan ook worden gecombineerd met de URI-tekenreeks met de app-ID om een volledig gekwalificeerde machtigingsnaam te vormen. | `Mail.Read` |

## <a name="types-of-consent"></a>Soorten toestemming

Toepassingen in Azure AD zijn afhankelijk van toestemming om toegang te krijgen tot benodigde resources of API’s. Er zijn een aantal soorten toestemming die uw app mogelijk nodig heeft om succesvol te kunnen worden uitgevoerd. Als u machtigingen definieert, moet u ook begrijpen hoe uw gebruikers toegang tot uw app of API zullen krijgen.

* **Statische toestemming van gebruikers** - Vindt automatisch plaats via de [OAuth 2.0-autorisatiestroom](v1-protocols-oauth-code.md#request-an-authorization-code) als u de resource opgeeft waarmee uw app wil communiceren. In het scenario met statische toestemming van de gebruiker moet uw app al de benodigde machtigingen hebben opgegeven in de configuratie van de app in de Azure Portal. Als de gebruiker (of eventueel de beheerder) geen toestemming voor deze app heeft gegeven, zal Azure AD de gebruiker op dit moment vragen om toestemming te geven. 

    Kom meer te weten over het registreren van een Azure AD-app die toegang tot een statische set API’s vraagt.
* **Dynamische toestemming van de gebruiker** - Is een functie in v2 van het Azure ADD-appmodel. In dit scenario vraagt uw app een reeks machtigingen aan die nodig zijn in de [OAuth 2.0-autorisatiestroom voor v2-apps](../develop/v2-permissions-and-consent.md#requesting-individual-user-consent). Als de gebruiker nog geen toestemming heeft gegeven, krijgt hij op dit moment een melding om toestemming te geven. [Meer informatie over dynamische toestemming](./azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent).

    > [!IMPORTANT]
    > Dynamische toestemming kan handig zijn, maar brengt een grote uitdaging met zich mee voor machtigingen die toestemming van een beheerder vereisen omdat de toestemmingservaring van de beheerder op het moment van toestemming niets weet over deze machtigingen. Als u machtigingen met beheerders rechten nodig hebt of als uw app gebruikmaakt van dynamische toestemming, moet u alle machtigingen in de Azure Portal (niet alleen de subset van machtigingen waarvoor beheerders toestemming nodig heeft) registreren. Hierdoor kunnen Tenant beheerders toestemming geven namens al hun gebruikers.
  
* **Toestemming van de beheerder** - Is vereist als uw app toegang nodig heeft tot bepaalde machtigingen met hoge privileges. Deze toestemming waarborgt dat beheerders een aantal extra controles hebben voordat ze apps of gebruikers autoriseren om toegang te krijgen tot uiterst geprivilegieerde gegevens van de organisatie. [Meer informatie over het verlenen van toestemming van de beheerder](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint).

## <a name="best-practices"></a>Aanbevolen procedures

### <a name="client-best-practices"></a>Aanbevolen procedures voor clients

- Vraag alleen machtigingen aan die uw app echt nodig heeft. Apps met te veel machtigingen lopen het gevaar om gegevens van gebruikers bloot te leggen als ze worden aangetast.
- Maak een keuze tussen gedelegeerde machtigingen en toepassingsmachtigingen op basis van het scenario dat uw app ondersteunt.
    - Gebruik altijd gedelegeerde machtigingen als de aanroep wordt uitgevoerd namens een gebruiker.
    - Gebruik alleen toepassingsmachtigingen als de app niet-interactief is en geen aanroepen uitvoert namens een specifieke gebruiker. Toepassingsmachtigingen hebben hoge bevoegdheden en mogen alleen worden gebruikt als dit absoluut noodzakelijk is.
- Wanneer u een app gebruikt die is gebaseerd op versie 2.0 van het eindpunt, moet u de statische machtigingen (de machtigingen opgegeven in de registratie van de toepassing) altijd instellen als de superset van de dynamische machtigingen die u aanvraagt op het moment van runtime (de machtigingen opgegeven in code en verzonden als queryparameters in uw autorisatieaanvraag), zodat scenario's zoals toestemming van de beheerder op de juiste manier werken.

### <a name="resourceapi-best-practices"></a>Aanbevolen procedures voor resources/API

- Resources die API’s beschikbaar maken, moeten machtigingen definiëren die specifiek zijn voor de gegevens of acties die ze beschermen. Het volgen van deze aanbevolen procedure helpt om te garanderen dat clients niet eindigen met machtigingen om toegang te krijgen tot gegevens die ze niet nodig hebben en dat gebruikers goed op de hoogte zijn van de gegevens waar ze toestemming voor geven.
- Resources moeten `Read`- en `ReadWrite`-machtigingen expliciet en afzonderlijk definiëren.
- Resources moeten alle machtigingen die toegang tot gegevens buiten de grenzen van gebruikers toestaan markeren als `Admin`-machtigingen.
- Resources moeten het naamgevingspatroon `Subject.Permission[.Modifier]` volgen, waarbij:
  - `Subject` komt overeen met het type gegevens dat beschikbaar is
  - `Permission` komt overeen met de actie die een gebruiker kan uitvoeren op die gegevens
  - `Modifier` wordt optioneel gebruikt voor het beschrijven van specialisaties van een andere machtiging
    
    Bijvoorbeeld:
  - Mail.Read - Staat gebruikers toe om e-mail te lezen.
  - Mail.ReadWrite - Staat gebruikers toe om e-mail te lezen of te schrijven.
  - Mail.ReadWrite.All - Staat een beheerder of gebruiker toe om toegang te krijgen tot alle e-mail in de organisatie.
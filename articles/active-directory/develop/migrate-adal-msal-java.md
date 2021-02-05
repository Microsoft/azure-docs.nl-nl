---
title: ADAL naar MSAL-migratie handleiding (MSAL4j) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het migreren van uw ADAL-java-app (Azure Active Directory Authentication Library) naar de micro soft Authentication Library (MSAL).
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Java
ms.workload: identity
ms.date: 11/04/2019
ms.author: sagonzal
ms.reviewer: nacanuma, twhitney
ms.custom: aaddev, devx-track-java
ms.openlocfilehash: 620f77655f8281919ba0831a7e53af8cd28bc5f2
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99583990"
---
# <a name="adal-to-msal-migration-guide-for-java"></a>Migratie handleiding voor ADAL naar MSAL voor Java

Dit artikel bevat een overzicht van wijzigingen die u moet aanbrengen voor het migreren van een app die gebruikmaakt van de Azure Active Directory Authentication Library (ADAL) om de micro soft Authentication Library (MSAL) te gebruiken.

Zowel de micro soft-verificatie bibliotheek voor Java (MSAL4J) als Azure AD-verificatie bibliotheek voor Java (ADAL4J) wordt gebruikt voor het verifiëren van Azure AD-entiteiten en het aanvragen van tokens van Azure AD. Tot nu toe hebben de meeste ontwikkel aars met Azure AD voor ontwikkel aars platform (v 1.0) gewerkt voor het verifiëren van Azure AD-identiteiten (werk-en school accounts) door tokens aan te vragen met behulp van Azure AD Authentication Library (ADAL).

MSAL biedt de volgende voor delen:

- Omdat er gebruik wordt gemaakt van het nieuwere micro soft-identiteits platform, kunt u een uitgebreidere set micro soft-identiteiten, zoals Azure AD-identiteiten, micro soft-accounts en sociale en lokale accounts, verifiëren via Azure AD Business to consumer (B2C).
- Uw gebruikers krijgen de beste ervaring voor eenmalige aanmelding.
- Uw toepassing kan stapsgewijze toestemming bieden en het ondersteunen van voorwaardelijke toegang is eenvoudiger.

MSAL voor Java is de verificatie bibliotheek die u kunt gebruiken met het micro soft Identity-platform. Er worden geen nieuwe functies geïmplementeerd op ADAL4J. Alle inspanningen die worden door lopen, zijn gericht op het verbeteren van MSAL.

## <a name="differences"></a>Verschillen

Als u met het eind punt (en ADAL4J) van Azure AD voor ontwikkel aars (v 1.0) hebt gewerkt, wilt u mogelijk [een andere informatie over het micro soft-identiteits platform lezen?](../azuread-dev/azure-ad-endpoint-comparison.md).

## <a name="scopes-not-resources"></a>Scopes die geen resources zijn

ADAL4J schaft tokens aan voor resources, terwijl MSAL voor Java tokens ophaalt voor scopes. Een aantal MSAL voor Java-klassen vereist een scope-para meter. Deze para meter is een lijst met teken reeksen die de gewenste machtigingen en benodigde bronnen declareren. Bekijk [de bereiken van Microsoft Graph](/graph/permissions-reference) om voorbeeld bereik te bekijken.

U kunt het `/.default` bereik achtervoegsel toevoegen aan de resource om uw apps te migreren van de ADAL naar MSAL. Bijvoorbeeld: voor de resource waarde van `https://graph.microsoft.com` is de equivalente bereik waarde `https://graph.microsoft.com/.default` .  Als de bron zich niet in het URL-formulier bevindt, maar een resource-ID van het formulier `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX` , kunt u de waarde van bereik nog steeds gebruiken als `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX/.default` .

Voor meer informatie over de verschillende typen bereiken, verwijzen wij u naar [machtigingen en toestemming in het micro soft-identiteits platform](./v2-permissions-and-consent.md) en de [scopes voor een web API die v 1.0-tokens accepteert](./msal-v1-app-scopes.md) .

## <a name="core-classes"></a>Kern klassen

In ADAL4J vertegenwoordigt de `AuthenticationContext` klasse uw verbinding met de beveiligings token service (STS) of autorisatie server via een-instantie. MSAL voor Java is echter ontworpen rond client toepassingen. Het biedt twee afzonderlijke klassen: `PublicClientApplication` en `ConfidentialClientApplication` om client toepassingen weer te geven.  De laatste, `ConfidentialClientApplication` , vertegenwoordigt een toepassing die is ontworpen om veilig een geheim te onderhouden, zoals een toepassings-id voor een daemon-app.

De volgende tabel laat zien hoe ADAL4J-functies worden toegewezen aan de nieuwe MSAL voor Java-functies:

| Methode ADAL4J| Methode MSAL4J|
|------|-------|
|acquireToken (string resource, ClientCredential Credential, AuthenticationCallback call back) | acquireToken(ClientCredentialParameters)|
|acquireToken (string resource, ClientAssertion Assertion, AuthenticationCallback call back)|acquireToken(ClientCredentialParameters)|
|acquireToken (string resource, AsymmetricKeyCredential Credential, AuthenticationCallback call back)|acquireToken(ClientCredentialParameters)|
|acquireToken (string resource, String clientId, String username, String password, AuthenticationCallback call back)| acquireToken(UsernamePasswordParameters)|
|acquireToken (string resource, String clientId, String username, String password = NULL, AuthenticationCallback call back)|acquireToken(IntegratedWindowsAuthenticationParameters)|
|acquireToken (string resource, UserAssertion userAssertion, ClientCredential Credential, AuthenticationCallback call back)| acquireToken(OnBehalfOfParameters)|
|acquireTokenByAuthorizationCode() | acquireToken(AuthorizationCodeParameters) |
| acquireDeviceCode () en acquireTokenByDeviceCode ()| acquireToken(DeviceCodeParameters)|
|acquireTokenByRefreshToken()| acquireTokenSilently(SilentParameters)|

## <a name="iaccount-instead-of-iuser"></a>IAccount in plaats van IUser

Gemanipuleerde gebruikers ADAL4J. Hoewel een gebruiker één persoon of software-agent vertegenwoordigt, kan deze een of meer accounts hebben in het micro soft-identiteits systeem. Een gebruiker kan bijvoorbeeld meerdere Azure AD-, Azure AD B2C-of micro soft-accounts hebben.

MSAL voor Java definieert het concept van het account via de `IAccount` interface. Dit is een belang rijke wijziging ten opzichte van ADAL4J, maar het is wel een goed idee omdat er wordt vastgelegd dat dezelfde gebruiker meerdere accounts kan hebben, en zelfs in verschillende Azure AD-directory's. MSAL voor Java biedt betere informatie in gast scenario's, omdat informatie over het privé-account wordt verstrekt.

## <a name="cache-persistence"></a>Cache persistentie

ADAL4J heeft geen ondersteuning voor token cache.
MSAL voor Java voegt een [token cache](msal-acquire-cache-tokens.md) toe om het beheer van de levens duur van tokens te vereenvoudigen door het automatisch vernieuwen van verlopen tokens, wanneer dat mogelijk is en om te voor komen dat de gebruiker om referenties wordt gevraagd wanneer dat mogelijk is.

## <a name="common-authority"></a>Algemene instantie

Als u in v 1.0 de bevoegdheid gebruikt `https://login.microsoftonline.com/common` , kunnen gebruikers zich aanmelden met een Azure Active Directory (Aad)-account (voor elke organisatie).

Als u de `https://login.microsoftonline.com/common` instantie in v 2.0 gebruikt, kunnen gebruikers zich aanmelden met een Aad-organisatie of zelfs een persoonlijk micro soft-account (MSA). Als u de aanmelding voor een AAD-account wilt beperken, gebruikt u in MSAL voor Java de- `https://login.microsoftonline.com/organizations` instantie (die hetzelfde gedrag heeft als met ADAL4J). Als u een instantie wilt opgeven, stelt `authority` u de para meter in de methode [PublicClientApplication. Builder](https://javadoc.io/doc/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.Builder.html) in wanneer u de `PublicClientApplication` klasse maakt.

## <a name="v10-and-v20-tokens"></a>v 1.0 en v 2.0-tokens

Het eind punt v 1.0 (gebruikt door ADAL) verzendt alleen tokens van v 1.0.

Het v 2.0-eind punt (gebruikt door MSAL) kan v 1.0-en v 2.0-tokens verzenden. Een eigenschap van het toepassings manifest van de Web-API stelt ontwikkel aars in staat om te kiezen welke versie van het token wordt geaccepteerd. Zie `accessTokenAcceptedVersion` de documentatie over [toepassings manifesten](./reference-app-manifest.md) .

Zie voor meer informatie over de tokens v 1.0 en v 2.0 [Azure Active Directory toegangs tokens](./access-tokens.md).

## <a name="adal-to-msal-migration"></a>Migratie van ADAL naar MSAL

In ADAL4J zijn de vernieuwings tokens weer gegeven, waardoor ontwikkel aars deze in de cache kunnen opslaan. Ze `AcquireTokenByRefreshToken()` worden gebruikt om oplossingen in te scha kelen, zoals het implementeren van langlopende services die Dash boards vernieuwen namens de gebruiker wanneer de gebruiker niet meer is verbonden.

MSAL voor Java maakt geen vernieuwings tokens om veiligheids redenen. In plaats daarvan zorgt MSAL voor het vernieuwen van tokens voor u.

MSAL voor Java heeft een API waarmee u vernieuwings tokens die u hebt verkregen met ADAL4j, kunt migreren naar de ClientApplication: [acquireToken (RefreshTokenParameters)](https://javadoc.io/static/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.html#acquireToken-com.microsoft.aad.msal4j.RefreshTokenParameters-). Met deze methode kunt u het eerder gebruikte vernieuwings token samen met eventuele bereiken (resources) opgeven die u wenst. Het vernieuwings token wordt uitgewisseld voor een nieuw en in cache opgeslagen voor gebruik door uw toepassing.

Het volgende code fragment toont een aantal migratie code in een vertrouwelijke client toepassing:

```java
String rt = GetCachedRefreshTokenForSignedInUser(); // Get refresh token from where you have them stored
Set<String> scopes = Collections.singleton("SCOPE_FOR_REFRESH_TOKEN");

RefreshTokenParameters parameters = RefreshTokenParameters.builder(scopes, rt).build();

PublicClientApplication app = PublicClientApplication.builder(CLIENT_ID) // ClientId for your application
                .authority(AUTHORITY)  //plug in your authority
                .build();

IAuthenticationResult result = app.acquireToken(parameters);
```

Het `IAuthenticationResult` retourneert een toegangs token en ID-token, terwijl uw nieuwe vernieuwings token in de cache wordt opgeslagen.
De toepassing bevat nu ook een IAccount:

```java
Set<IAccount> accounts =  app.getAccounts().join();
```

Als u de tokens wilt gebruiken die zich nu in de cache bevinden, roept u het volgende aan:

```java
SilentParameters parameters = SilentParameters.builder(scope, accounts.iterator().next()).build();
IAuthenticationResult result = app.acquireToken(parameters);
```

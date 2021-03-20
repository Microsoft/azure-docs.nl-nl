---
title: Eenmalige aanmelding (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het bouwen van eenmalige aanmelding met behulp van de micro soft-verificatie bibliotheek voor Java script (MSAL.js).
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 8080d4cf4c3f0091f7837b3fccead5474c42db55
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84690775"
---
# <a name="single-sign-on-with-msaljs"></a>Eenmalige aanmelding met MSAL.js

Met single Sign-On (SSO) kunnen gebruikers hun referenties eenmaal invoeren om zich aan te melden en een sessie tot stand te brengen die opnieuw kan worden gebruikt in meerdere toepassingen zonder dat ze opnieuw moeten worden geverifieerd. Dit biedt een naadloze ervaring voor de gebruiker en vermindert de herhaalde prompts voor referenties.

Azure AD biedt SSO-mogelijkheden voor toepassingen door een sessie cookie in te stellen wanneer de gebruiker de eerste keer verifieert. Met de MSAL.js-bibliotheek kunnen toepassingen op een aantal manieren gebruikmaken.

## <a name="sso-between-browser-tabs"></a>SSO tussen browser tabbladen

Wanneer uw toepassing is geopend op meerdere tabbladen en u zich voor het eerst aanmeldt op een tabblad, wordt de gebruiker ook aangemeld op de andere tabbladen zonder dat daarom wordt gevraagd. MSAL.js slaat de ID-token voor de gebruiker in de browser op in de cache `localStorage` en wordt de gebruiker bij de toepassing ondertekend op de andere geopende tabbladen.

MSAL.js maakt standaard gebruik van `sessionStorage` waarmee de sessie niet tussen tabbladen kan worden gedeeld. Als u SSO tussen tabbladen wilt ophalen, moet u de `cacheLocation` in-MSAL.js instellen op `localStorage` zoals hieronder wordt weer gegeven.

```javascript
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

## <a name="sso-between-apps"></a>SSO tussen apps

Wanneer een gebruiker zich verifieert, wordt een sessie cookie ingesteld op het Azure AD-domein in de browser. MSAL.js is afhankelijk van deze sessie cookie om SSO te bieden voor de gebruiker tussen verschillende toepassingen. MSAL.js ook de ID-tokens en toegangs tokens van de gebruiker in de browser opslag per toepassings domein in de cache opslaat. Als gevolg hiervan varieert het SSO-gedrag voor verschillende gevallen:  

### <a name="applications-on-the-same-domain"></a>Toepassingen in hetzelfde domein

Wanneer toepassingen worden gehost op hetzelfde domein, kan de gebruiker zich eenmaal aanmelden bij een app en vervolgens worden geverifieerd naar de andere apps zonder dat hiervoor een prompt wordt gevraagd. MSAL.js maakt gebruik van de tokens in de cache voor de gebruiker op het domein om SSO te bieden.

### <a name="applications-on-different-domain"></a>Toepassingen op een ander domein

Wanneer toepassingen worden gehost in verschillende domeinen, zijn de tokens in het cache geheugen van domein A niet toegankelijk door MSAL.js in domein B.

Dit betekent dat wanneer gebruikers zich hebben aangemeld op domein A naar een toepassing in het domein B navigeert, ze worden omgeleid of wordt gevraagd met de pagina Azure AD. Omdat Azure AD de cookie van de gebruikers sessie nog steeds heeft, wordt de gebruiker aangemeld en hoeven de referenties niet opnieuw te worden opgegeven. Als de gebruiker meerdere gebruikers accounts heeft in een sessie met Azure AD, wordt de gebruiker gevraagd het betreffende account te kiezen om zich aan te melden.

### <a name="automatically-select-account-on-azure-ad"></a>Automatisch accounts selecteren in azure AD

In bepaalde gevallen heeft de toepassing toegang tot de verificatie context van de gebruiker en wil de Azure AD-account selectie prompt voor komen wanneer er meerdere accounts zijn aangemeld.  Dit kan op verschillende manieren worden gedaan:

**Sessie-ID (SID) gebruiken**

Sessie-ID is een [optionele claim](active-directory-optional-claims.md) die kan worden geconfigureerd in de id-tokens. Met deze claim kan de toepassing de Azure AD-sessie van de gebruiker identificeren onafhankelijk van de account naam of gebruikers naam van de gebruiker. U kunt de SID in de aanvraag parameters door geven aan de `acquireTokenSilent` aanroep. Hierdoor kan Azure AD de account selectie overs Laan. SID is gebonden aan de sessie cookie en biedt geen verdere browser contexten.

```javascript
var request = {
    scopes: ["user.read"],
    sid: sid
}

userAgentApplication.acquireTokenSilent(request).then(function(response) {
        const token = response.accessToken
    }
).catch(function (error) {  
        //handle error
});
```

> [!Note]
> SID kan alleen worden gebruikt met Silent verificatie aanvragen die zijn gedaan door de `acquireTokenSilent` aanroep in MSAL.js.
[Hier](active-directory-optional-claims.md)vindt u de stappen voor het configureren van optionele claims in het manifest van uw toepassing.

**Aanmeldings Hint gebruiken**

Als u geen sid-claim hebt geconfigureerd of de account selectie prompt wilt overs laan in interactieve verificatie aanroepen, kunt u dit doen door een `login_hint` in de aanvraag parameters en eventueel een op te geven in `domain_hint` `extraQueryParameters` de MSAL.js interactieve methoden ( `loginPopup` , `loginRedirect` , `acquireTokenPopup` en `acquireTokenRedirect` ). Bijvoorbeeld:

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.loginRedirect(request);
```

U kunt de waarden voor login_hint en domain_hint ophalen door de claims te lezen die zijn geretourneerd in het ID-token voor de gebruiker.

* **loginHint** moet worden ingesteld op de `preferred_username` claim in het id-token.

* **domain_hint** moet alleen worden door gegeven wanneer de/veelvoorkomende-instantie wordt gebruikt. De domein Hint wordt bepaald door de Tenant-ID (TID).  Als de `tid` claim in het ID-token `9188040d-6c67-4c5b-b112-36a304b66dad` consumenten is. Anders is het organisaties.

Lees [hier](v2-oauth2-implicit-grant-flow.md) meer informatie over de waarden voor aanmeldings hint en domein hint.

> [!Note]
> U kunt de SID en login_hint op hetzelfde moment niet door geven. Dit resulteert in een fout melding.

## <a name="sso-without-msaljs-login"></a>SSO zonder MSAL.js aanmelding

MSAL.js vereist dat een aanmeldings methode wordt aangeroepen om een gebruikers context in te richten voordat tokens voor Api's worden opgehaald. Aangezien de aanmeldings methoden interactief zijn, ziet de gebruiker een prompt.

Er zijn bepaalde gevallen waarin toepassingen toegang hebben tot de context van de geverifieerde gebruiker of ID-token via authenticatie die is gestart in een andere toepassing en SSO wilt gebruiken om tokens te verkrijgen zonder u eerst aan te melden via MSAL.js.

Een voor beeld hiervan is: een gebruiker is aangemeld bij een bovenliggende webtoepassing die als host fungeert voor een andere Java script-toepassing die wordt uitgevoerd als een invoeg toepassing of invoeg toepassing.

De SSO-ervaring in dit scenario kan als volgt worden bereikt:

Geef de `sid` indien beschikbaar (of `login_hint` en optioneel `domain_hint` ) als aanvraag parameters aan de MSAL.js `acquireTokenSilent` aanroep als volgt door:

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.acquireTokenSilent(request).then(function(response) {
        const token = response.accessToken
    }
).catch(function (error) {  
        //handle error
});
```

## <a name="sso-in-adaljs-to-msaljs-update"></a>Eenmalige aanmelding in ADAL.js MSAL.js update

MSAL.js maakt functie pariteit met ADAL.js voor Azure AD-verificatie scenario's. Om de migratie van ADAL.js naar MSAL.js eenvoudig te maken en om te voor komen dat gebruikers zich opnieuw kunnen aanmelden, leest de bibliotheek het ID-token dat de gebruikers sessie in ADAL.js cache vertegenwoordigt en wordt de gebruiker naadloos ondertekend in MSAL.js.  

Als u gebruik wilt maken van het gedrag van eenmalige aanmelding (SSO) bij het bijwerken van ADAL.js, moet u ervoor zorgen dat de bibliotheken worden gebruikt `localStorage` voor cache-tokens. Stel de `cacheLocation` to `localStorage` in in zowel de MSAL.js als ADAL.js configuratie bij initialisatie als volgt:


```javascript
// In ADAL.js
window.config = {
   clientId: 'g075edef-0efa-453b-997b-de1337c29185',
   cacheLocation: 'localStorage'
};

var authContext = new AuthenticationContext(config);


// In latest MSAL.js version
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

Zodra dit is geconfigureerd, kan MSAL.js de status in de cache van de geverifieerde gebruiker in ADAL.js lezen en gebruiken om SSO in MSAL.js op te geven.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de waarden voor [eenmalige aanmelding en levens duur van tokens](active-directory-configurable-token-lifetimes.md) in azure AD.

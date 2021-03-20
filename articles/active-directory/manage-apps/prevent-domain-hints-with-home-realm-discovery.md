---
title: Voor komen dat automatische versnelling voor aanmelden in azure AD wordt gebruikt voor het beleid voor het detecteren van de basis van realms
description: Meer informatie over het voor komen van domain_hint automatische versnelling naar federatieve id.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/12/2021
ms.author: hirsin
ms.openlocfilehash: 53dfdfaf37695059d6d52428c2ba109970d9f7f7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589375"
---
# <a name="disable-auto-acceleration-to-a-federated-idp-during-user-sign-in-with-home-realm-discovery-policy"></a>Automatische versnelling naar een federatieve IDP uitschakelen tijdens het aanmelden van de gebruiker met het beleid voor het detecteren van een thuis domein

[Home realm Discovery-beleid](/graph/api/resources/homeRealmDiscoveryPolicy) (hrd) biedt beheerders meerdere manieren om te bepalen hoe en waar gebruikers worden geverifieerd. De `domainHintPolicy` sectie van het HRD-beleid wordt gebruikt om federatieve gebruikers te migreren naar beheerde referenties voor de Cloud, zoals [Fido](../authentication/howto-authentication-passwordless-security-key.md), door ervoor te zorgen dat ze altijd de aanmeldings pagina van Azure AD bezoeken en niet automatisch worden versneld naar een gefedereerde IDP vanwege domein hints.

Dit beleid is vereist in situaties waarin toepassingen een beheerder tijdens het aanmelden geen hints voor domein toevoegen kan beheren of bijwerken.  Bijvoorbeeld, `outlook.com/contoso.com` verzendt de gebruiker naar een aanmeldings pagina met de `&domain_hint=contoso.com` para meter toegevoegd, zodat de gebruiker rechtstreeks kan worden versneld naar het federatieve IDP voor het `contoso.com` domein. Gebruikers met beheerde referenties die worden verzonden naar een federatieve IDP kunnen zich niet aanmelden met hun beheerde referenties, waardoor beveiligings-en frustrerende gebruikers worden gereduceerd met wille keurige aanmeld ervaring. Beheerders die beheerde referenties implementeren, [moeten dit beleid ook instellen](#suggested-use-within-a-tenant) om ervoor te zorgen dat gebruikers altijd hun beheerde referenties kunnen gebruiken.

## <a name="domainhintpolicy-details"></a>Details van DomainHintPolicy

De sectie DomainHintPolicy van het HRD-beleid is een JSON-object waarmee een beheerder bepaalde domeinen en toepassingen kan afmelden van het gebruik van de domein hint.  Dit betekent dat de aanmeldings pagina van Azure AD werkt alsof er geen `domain_hint` para meter voor de aanmeldings aanvraag aanwezig is.

### <a name="the-respect-and-ignore-policy-sections"></a>De secties beleid respecteren en negeren

|Sectie | Betekenis | Waarden |
|--------|---------|--------|
|`IgnoreDomainHintForDomains` |Als deze domein Hint in de aanvraag wordt verzonden, negeert u deze. |Matrix van domein adressen (bijvoorbeeld `contoso.com` ). Ondersteunt ook `all_domains`|
|`RespectDomainHintForDomains`| Als deze domein Hint in de aanvraag wordt verzonden, moet u deze ook respecteren als `IgnoreDomainHintForApps` geeft aan dat de app in de aanvraag niet automatisch moet worden versneld. Dit wordt gebruikt om de implementatie van domein hints in uw netwerk te vertragen. u kunt aangeven dat sommige domeinen nog steeds moeten worden versneld. | Matrix van domein adressen (bijvoorbeeld `contoso.com` ). Ondersteunt ook `all_domains`|
|`IgnoreDomainHintForApps`| Als een aanvraag van deze toepassing wordt geleverd met een domein Hint, negeert u deze. | Matrix van toepassings-Id's (GUID'S). Ondersteunt ook `all_apps`|
|`RespectDomainHintForApps` |Als een aanvraag van deze toepassing wordt geleverd met een domein Hint, respecteert u deze zelfs als `IgnoreDomainHintForDomains` dat domein bevat. Wordt gebruikt om ervoor te zorgen dat sommige apps blijven werken als u detecteert dat ze worden onderbroken zonder domein hints. | Matrix van toepassings-Id's (GUID'S). Ondersteunt ook `all_apps`|

### <a name="policy-evaluation"></a>Beleids evaluatie

De DomainHintPolicy-logica wordt uitgevoerd op elke binnenkomende aanvraag die een domein Hint bevat en versnelt op basis van twee stukjes gegevens in de aanvraag: het domein in de domein hint en de client-ID (de app). Kortom: ' respecteren ' voor een domein of app heeft voor rang op een instructie om een domein hint te ' negeren ' voor een opgegeven domein of toepassing.

1. Als er geen domein Hint beleid is, of als geen van de vier secties verwijst naar de app of domein hint die wordt vermeld, [wordt de rest van het HRD-beleid geëvalueerd](configure-authentication-for-federated-users-portal.md#priority-and-evaluation-of-hrd-policies).
1. Als een van de (of beide) `RespectDomainHintForApps` of `RespectDomainHintForDomains` secties de app-of domein Hint in de aanvraag bevat, wordt de gebruiker automatisch versneld naar de federatieve IDP zoals aangevraagd.
1. Als een van beide (of beide) van `IgnoreDomainHintsForApps` of `IgnoreDomainHintsForDomains` verwijst naar de app of de domein Hint in de aanvraag, en er niet naar wordt verwezen door de secties "respecteren", wordt de aanvraag niet automatisch versneld en blijft de gebruiker op de aanmeldings pagina van Azure AD om een gebruikers naam op te geven.

Wanneer een gebruiker op de aanmeldings pagina een gebruikers naam heeft ingevoerd, kunnen ze hun beheerde referenties gebruiken als ze zijn geregistreerd.  Als ze ervoor kiezen om geen beheerde referentie te gebruiken, of ze hebben geen geregistreerde referenties, worden ze naar hun federatieve IDP voor referentie vermelding zoals gebruikelijk.

## <a name="suggested-use-within-a-tenant"></a>Voorgesteld gebruik binnen een Tenant

Beheerders van federatieve domeinen moeten deze sectie van het HRD-beleid instellen in een abonnement met vier fasen. Het doel van dit plan is om uiteindelijk alle gebruikers in een Tenant de mogelijkheid te hebben hun beheerde referenties te gebruiken, ongeacht het domein of de toepassing, sla de apps op die harde afhankelijkheden hebben voor `domain_hint` gebruik.  Dit abonnement helpt beheerders bij het vinden van deze apps, het uitsluiten van het nieuwe beleid en het implementeren van de wijziging in de rest van de Tenant.

1. Kies een domein om deze wijziging in eerste instantie uit te vouwen.  Dit is uw test domein, dus kies er een die meer juiste kan zijn voor wijzigingen in UX (dat wil zeggen een andere aanmeldings pagina bekijken).  Hiermee worden alle domein hints genegeerd voor alle toepassingen die gebruikmaken van deze domein naam. Dit beleid [instellen](#configuring-policy-through-graph-explorer) in uw Tenant-standaard hrd-beleid:

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com" ], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": [] 
} 
```

2. Verzamel feedback van de test domein gebruikers. Details verzamelen voor toepassingen die als gevolg van deze wijziging zijn afgebroken: ze hebben een afhankelijkheid van het gebruik van domein hints en moeten worden bijgewerkt. Voeg deze nu toe aan de `RespectDomainHintForApps` sectie:

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com" ], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

3. Ga verder met het uitbreiden van de implementatie van het beleid voor nieuwe domeinen, zodat u meer feedback kunt verzamelen.

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com", "otherDomain.com", "anotherDomain.com"], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

4. Voltooi uw implementatie-doel alle domeinen en sluit de computers die moeten worden versneld:

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "*" ], 
    "RespectDomainHintForDomains": ["guestHandlingDomain.com"], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

Nadat stap 4 is voltooid, kunnen alle gebruikers, met uitzonde ring van die in `guestHandlingDomain.com` , zich aanmelden op de aanmeldings pagina van Azure AD, zelfs wanneer domein hints anders een automatische versnelling naar een GEFEDEREERDE IDP zouden veroorzaken.  De uitzonde ring hierop is als de app die het aanmelden aanvraagt een van de uitgesloten gebruikers is voor die apps, worden alle domein hints nog steeds geaccepteerd.

## <a name="configuring-policy-through-graph-explorer"></a>Beleid configureren via Graph Explorer

Stel het [hrd-beleid](/graph/api/resources/homeRealmDiscoveryPolicy) zoals gebruikelijk in met behulp van Microsoft Graph.  

1. Verleen de machtiging beleid. ReadWrite. ApplicationConfiguration in [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).  
1. De URL gebruiken `https://graph.microsoft.com/v1.0/policies/homeRealmDiscoveryPolicies`
1. POST het nieuwe beleid naar deze URL of PATCH ernaar bij het `/policies/homerealmdiscoveryPolicies/{policyID}` overschrijven van een bestaande.

POST of PATCH-inhoud:

```json
{
    "displayName":"Home Realm Discovery Domain Hint Exclusion Policy",
    "definition":[
        "{\"HomeRealmDiscoveryPolicy\" : {\"DomainHintPolicy\": { \"IgnoreDomainHintForDomains\": [ \"Contoso.com\" ], \"RespectDomainHintForDomains\": [], \"IgnoreDomainHintForApps\": [\"sample-guid-483c-9dea-7de4b5d0a54a\"], \"RespectDomainHintForApps\": [] } } }"
    ],
    "isOrganizationDefault":true
}
```

Zorg ervoor dat u slashes gebruikt om de JSON-sectie te escapepen `Definition` Wanneer u Graph gebruikt.  

`isOrganizationDefault` moet True zijn, maar de displayName en de definitie kunnen veranderen.

## <a name="next-steps"></a>Volgende stappen

* [Aanmeldings wachtwoord zonder wacht woord inschakelen](../authentication/howto-authentication-passwordless-security-key.md)
* [Aanmelden zonder wacht woord inschakelen met de app Microsoft Authenticator](../authentication/howto-authentication-passwordless-phone.md)
---
title: Automatische versnelling van aanmelding in Azure AD voorkomen met behulp van het detectiebeleid voor thuis realms
description: Meer informatie over het voorkomen domain_hint van automatische versnelling naar federatie-IDP's.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/12/2021
ms.author: iangithinji
ms.reviewer: hirsin
ms.openlocfilehash: b89e0e1c8bd8109fac8b4b7c05a845a3e234b617
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375547"
---
# <a name="disable-auto-acceleration-to-a-federated-idp-during-user-sign-in-with-home-realm-discovery-policy"></a>Automatische versnelling naar een federatief IDP uitschakelen tijdens het aanmelden van gebruikers met thuis realmdetectiebeleid

[Home Realm Discovery Policy](/graph/api/resources/homeRealmDiscoveryPolicy) (HRD) biedt beheerders meerdere manieren om te bepalen hoe en waar hun gebruikers zich verifiëren. De sectie van het HRD-beleid wordt gebruikt voor het migreren van federatief gebruikers naar in de cloud beheerde referenties, zoals FIDO, door ervoor te zorgen dat ze altijd de aanmeldingspagina van Azure AD bezoeken en niet automatisch worden versneld naar een federatief IDP vanwege `domainHintPolicy` domeinhints. [](../authentication/howto-authentication-passwordless-security-key.md)

Dit beleid is nodig in situaties waarin toepassingen een beheerder geen domeinhints kan beheren of bijwerken tijdens het aanmelden.  Verzendt de gebruiker bijvoorbeeld naar een aanmeldingspagina met de parameter toegevoegd, zodat de gebruiker automatisch rechtstreeks naar de federatief-IDP voor het domein `outlook.com/contoso.com` `&domain_hint=contoso.com` wordt `contoso.com` versneld. Gebruikers met beheerde referenties die naar een federatief IDP zijn verzonden, kunnen zich niet aanmelden met hun beheerde referenties, waardoor de beveiliging en gefrustreerde gebruikers met gerandomiseerde aanmeldingservaringen worden verkleind. Beheerders die beheerde referenties uitrollen, moeten dit beleid [ook](#suggested-use-within-a-tenant) instellen om ervoor te zorgen dat gebruikers altijd hun beheerde referenties kunnen gebruiken.

## <a name="domainhintpolicy-details"></a>Details van DomainHintPolicy

De sectie DomainHintPolicy van het HRD-beleid is een JSON-object waarmee een beheerder bepaalde domeinen en toepassingen kan uit-kiezen voor gebruik van domeinhints.  Functioneel betekent dit dat de aanmeldingspagina van Azure AD zich gedraagt alsof er geen parameter voor de `domain_hint` aanmeldingsaanvraag aanwezig is.

### <a name="the-respect-and-ignore-policy-sections"></a>De beleidssecties Respect and Ignore

|Sectie | Betekenis | Waarden |
|--------|---------|--------|
|`IgnoreDomainHintForDomains` |Als deze domeinhint wordt verzonden in de aanvraag, negeert u deze. |Matrix van domeinadressen (bijvoorbeeld `contoso.com` ). Ondersteunt ook `all_domains`|
|`RespectDomainHintForDomains`| Als deze domeinhint in de aanvraag wordt verzonden, moet u deze zelfs respecteren als aangeeft dat de app in de aanvraag niet `IgnoreDomainHintForApps` automatisch moet worden versneld. Dit wordt gebruikt om de implementatie van afgeschafte domeinhints in uw netwerk te vertragen. U kunt aangeven dat sommige domeinen nog steeds moeten worden versneld. | Matrix van domeinadressen (bijvoorbeeld `contoso.com` ). Ondersteunt ook `all_domains`|
|`IgnoreDomainHintForApps`| Als een aanvraag van deze toepassing wordt geleverd met een domeinhint, negeert u deze. | Matrix van toepassings-ID's (GUID's). Ondersteunt ook `all_apps`|
|`RespectDomainHintForApps` |Als een aanvraag van deze toepassing wordt geleverd met een domeinhint, respecteert u deze zelfs als `IgnoreDomainHintForDomains` dat domein bevat. Wordt gebruikt om ervoor te zorgen dat sommige apps blijven werken als u ontdekt dat ze niet werken zonder domeinhints. | Matrix van toepassings-ID's (GUID's). Ondersteunt ook `all_apps`|

### <a name="policy-evaluation"></a>Beleidsevaluatie

De Logica van DomainHintPolicy wordt uitgevoerd op elke binnenkomende aanvraag die een domeinhint bevat en versnelt op basis van twee gegevens in de aanvraag: het domein in de domeinhint en de client-id (de app). Kortom: 'Respect' voor een domein of app heeft voorrang op een instructie om een domeinhint voor een bepaald domein of bepaalde toepassing te negeren.

1. Als er geen beleid voor domeinhints is of als geen van de vier secties verwijst naar de vermelde app of domeinhint, wordt de rest van het [HRD-beleid geëvalueerd.](configure-authentication-for-federated-users-portal.md#priority-and-evaluation-of-hrd-policies)
1. Als een (of beide) van of de sectie de app of domeinhint in de aanvraag bevat, wordt de gebruiker automatisch versneld naar de federatie-IDP zoals `RespectDomainHintForApps` `RespectDomainHintForDomains` aangevraagd.
1. Als een (of beide) van of verwijst naar de app of de domeinhint in de aanvraag en er niet naar wordt verwezen door de sectie 'Respect', wordt de aanvraag niet automatisch versneld en blijft de gebruiker op de aanmeldingspagina van Azure AD om een gebruikersnaam op te `IgnoreDomainHintsForApps` `IgnoreDomainHintsForDomains` geven.

Zodra een gebruiker een gebruikersnaam heeft ingevoerd op de aanmeldingspagina, kan deze de beheerde referenties gebruiken, als deze zijn geregistreerd.  Als ze ervoor kiezen geen beheerde referentie te gebruiken of als ze er geen hebben geregistreerd, worden ze op de gebruikelijke manier naar hun federatief IDP geleid voor referentie-invoer.

## <a name="suggested-use-within-a-tenant"></a>Voorgesteld gebruik binnen een tenant

Beheerders van federatief domeinen moeten deze sectie van het HRD-beleid instellen in een vierfaseplan. Het doel van dit plan is om uiteindelijk alle gebruikers in een tenant de mogelijkheid te bieden om hun beheerde referenties te gebruiken, ongeacht het domein of de toepassing, om die apps op te slaan die harde afhankelijk zijn van `domain_hint` het gebruik.  Met dit plan kunnen beheerders deze apps vinden, ze vrijstellen van het nieuwe beleid en doorgaan met het uitrollen van de wijziging naar de rest van de tenant.

1. Kies een domein om deze wijziging in eerste instantie uit te rollen.  Dit is uw testdomein, dus kies een domein dat mogelijk meer gevoelig is voor wijzigingen in UX (dat wil zeggen dat u een andere aanmeldingspagina ziet).  Hiermee worden alle domeinhints van alle toepassingen die gebruikmaken van deze domeinnaam genegeerd. [Stel](#configuring-policy-through-graph-explorer) dit beleid in uw standaard HRD-beleid voor tenants in:

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com" ], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": [] 
} 
```

2. Feedback verzamelen van de testdomeingebruikers. Verzamel gegevens voor toepassingen die als gevolg van deze wijziging zijn uit elkaar gezakt. Ze zijn afhankelijk van het gebruik van domeinhints en moeten worden bijgewerkt. Voeg deze nu toe aan de `RespectDomainHintForApps` sectie :

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com" ], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

3. Ga door met het uitbreiden van de implementatie van het beleid naar nieuwe domeinen, en verzamel meer feedback.

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com", "otherDomain.com", "anotherDomain.com"], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

4. Voltooi de implementatie - richt u op alle domeinen, met uitzondering van de domeinen die moeten worden versneld:

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "*" ], 
    "RespectDomainHintForDomains": ["guestHandlingDomain.com"], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

Nadat stap 4 is voltooid, kunnen alle gebruikers, met uitzondering van die in , zich aanmelden op de aanmeldingspagina van Azure AD, zelfs wanneer domeinhints anders zouden leiden tot een automatische versnelling naar een federatief `guestHandlingDomain.com` IDP.  De uitzondering hierop is als de app die zich wil aanmelden een van de uitgesloten apps is. Voor deze apps worden alle domeinhints nog steeds geaccepteerd.

## <a name="configuring-policy-through-graph-explorer"></a>Beleid configureren via Graph Explorer

Stel het [HRD-beleid op](/graph/api/resources/homeRealmDiscoveryPolicy) de gebruikelijke manier in met behulp Microsoft Graph.  

1. Verleen de machtiging Policy.ReadWrite.ApplicationConfiguration in [Graph Explorer.](https://developer.microsoft.com/graph/graph-explorer)  
1. De URL gebruiken `https://graph.microsoft.com/v1.0/policies/homeRealmDiscoveryPolicies`
1. Plaats het nieuwe beleid op deze URL of PATCH `/policies/homerealmdiscoveryPolicies/{policyID}` als een bestaand beleid wordt overschreven.

POST- of PATCH-inhoud:

```json
{
    "displayName":"Home Realm Discovery Domain Hint Exclusion Policy",
    "definition":[
        "{\"HomeRealmDiscoveryPolicy\" : {\"DomainHintPolicy\": { \"IgnoreDomainHintForDomains\": [ \"Contoso.com\" ], \"RespectDomainHintForDomains\": [], \"IgnoreDomainHintForApps\": [\"sample-guid-483c-9dea-7de4b5d0a54a\"], \"RespectDomainHintForApps\": [] } } }"
    ],
    "isOrganizationDefault":true
}
```

Zorg ervoor dat u slashes gebruikt om de `Definition` JSON-sectie te escapen bij het gebruik van Graph.  

`isOrganizationDefault` moet waar zijn, maar de displayName en definitie kunnen worden gewijzigd.

## <a name="next-steps"></a>Volgende stappen

* [Aanmelden met een beveiligingssleutel zonder wachtwoord inschakelen](../authentication/howto-authentication-passwordless-security-key.md)
* [Aanmelden zonder wachtwoord inschakelen met de Microsoft Authenticator app](../authentication/howto-authentication-passwordless-phone.md)
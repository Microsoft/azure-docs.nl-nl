---
title: SSO-sessies in Azure Active Directory B2C | Microsoft Docs
description: Een overzicht van het configureren van sessie gedrag in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5e02323df3a12c4a74de1fb62b36762fc739e9e5
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96750436"
---
# <a name="azure-ad-b2c-session"></a>Azure AD B2C sessie

Eenmalige aanmelding (SSO) voegt beveiligings-en gebruiks gemak toe wanneer gebruikers zich aanmelden bij verschillende toepassingen in Azure Active Directory B2C (Azure AD B2C). In dit artikel worden de methoden voor eenmalige aanmelding beschreven die worden gebruikt in Azure AD B2C en kunt u de meest geschikte SSO-methode kiezen voor het configureren van uw beleid.

Met eenmalige aanmelding kunnen gebruikers zich eenmaal aanmelden met één account en toegang krijgen tot meerdere toepassingen. De toepassing kan een web-, mobiele of toepassing met één pagina zijn, ongeacht het platform of de domein naam.

Wanneer de gebruiker zich voor het eerst aanmeldt bij een toepassing, wordt Azure AD B2C een cookie op basis van cookies. Bij volgende verificatie aanvragen Azure AD B2C leest en valideert de cookie op basis van cookies, en wordt een toegangs token uitgegeven zonder dat de gebruiker wordt gevraagd om zich opnieuw aan te melden. Als de cookie-gebaseerde sessie verloopt of ongeldig wordt, wordt de gebruiker gevraagd zich opnieuw aan te melden.  

## <a name="sso-session-types"></a>SSO-sessie typen

Integratie met Azure AD B2C omvat drie typen SSO-sessies:

- **Azure AD B2C** -sessie beheerd door Azure AD B2C
- **Federatieve id-provider** : sessie beheerd door de ID-provider, bijvoorbeeld Facebook, sales force of Microsoft-account
- **Toepassings** sessie beheerd door de toepassing Web, mobiel of Eén pagina

![SSO-sessie](media/session-overview/sso-session-types.png)

### <a name="azure-ad-b2c-sso"></a>Azure AD B2C SSO 

Wanneer een gebruiker is geverifieerd met een lokaal of sociaal account, Azure AD B2C een cookie op basis van cookies opgeslagen op de browser van de gebruiker. De cookie wordt opgeslagen onder de domein naam van de Azure AD B2C Tenant, zoals `https://contoso.b2clogin.com` .

Als een gebruiker zich voor het eerst aanmeldt met een federatief account en vervolgens tijdens het sessie tijd venster (time-to-Live of TTL) zich aanmeldt bij dezelfde app of een andere app, probeert Azure AD B2C een nieuw toegangs token op te halen uit de federatieve id-provider. Als de sessie van de federatieve id-provider is verlopen of ongeldig is, vraagt de federatieve id-provider de gebruiker om referenties. Als de sessie nog steeds actief is (of als de gebruiker zich heeft aangemeld met een lokaal account in plaats van een Federatie account), wordt de gebruiker door Azure AD B2C geautoriseerd en worden verdere prompts overbodig.

U kunt het gedrag van de sessie configureren, met inbegrip van de sessie-TTL en hoe Azure AD B2C de sessie over beleids regels en toepassingen deelt.

### <a name="federated-identity-provider-sso"></a>SSO van federatieve id-provider

Een sociaal-of ENTER prise-ID-provider beheert zijn eigen sessie. De cookie wordt opgeslagen onder de domein naam van de identiteits provider, zoals `https://login.salesforce.com` . Azure AD B2C heeft geen invloed op de sessie van de federatieve id-provider. In plaats daarvan wordt het gedrag van de sessie bepaald door de federatieve id-provider. 

Denkt u zich het volgende scenario eens in:

1. Een gebruiker meldt zich aan bij Facebook om de feed te controleren.
2. Later opent de gebruiker uw toepassing en start het aanmeldings proces. De gebruiker wordt door de toepassing omgeleid naar Azure AD B2C om het aanmeldings proces te volt ooien.
3. Op de Azure AD B2C registratie-of aanmeldings pagina wordt de gebruiker gekozen om u aan te melden met hun Facebook-account. De gebruiker wordt omgeleid naar Facebook. Als er een actieve sessie op Facebook is, wordt de gebruiker niet gevraagd om de referenties op te geven en wordt deze direct omgeleid naar Azure AD B2C met een Facebook-token.

### <a name="application-sso"></a>SSO van de toepassing

Een web-, mobiele of toepassing met één pagina kan worden beveiligd door OAuth-toegang, ID-tokens of SAML-tokens. Wanneer een gebruiker probeert toegang te krijgen tot een beveiligde bron op de app, controleert de app of er een actieve sessie aan de kant van de toepassing is. Als er geen app-sessie is of de sessie is verlopen, wordt de gebruiker door de app Azure AD B2C op de aanmeldings pagina.

De toepassings sessie kan een op cookies gebaseerde sessie zijn die is opgeslagen onder de naam van het toepassings domein, zoals `https://contoso.com` . Mobiele toepassingen kunnen de sessie op een andere manier opslaan, maar met een vergelijk bare methode.

## <a name="azure-ad-b2c-session-configuration"></a>Configuratie van Azure AD B2C-sessie

### <a name="session-scope"></a>Sessie bereik

De Azure AD B2C-sessie kan worden geconfigureerd met de volgende bereiken:

- **Tenant** : dit is de standaard instelling. Met deze instelling kunnen meerdere toepassingen en gebruikers stromen in uw B2C-Tenant dezelfde gebruikers sessie delen. Wanneer een gebruiker zich bijvoorbeeld bij een toepassing aanmeldt, kan de gebruiker zich bij het openen van de app ook probleemloos aanmelden.
- **Toepassing** : met deze instelling kunt u een gebruikers sessie alleen voor een toepassing, onafhankelijk van andere toepassingen, onderhouden. U kunt deze instelling bijvoorbeeld gebruiken als u wilt dat de gebruiker zich aanmeldt bij Contoso apotheek, ongeacht of de gebruiker al is aangemeld bij Contoso-boodschappen.
- **Beleid** : met deze instelling kunt u een gebruikers sessie alleen voor een gebruikers stroom onderhouden, onafhankelijk van de toepassingen die er gebruik van maken. Als de gebruiker zich al heeft aangemeld en een MFA-stap (multi-factor Authentication) heeft voltooid, kan de gebruiker toegang krijgen tot hogere beveiligings onderdelen van meerdere toepassingen, zolang de sessie die aan de gebruikers stroom is gekoppeld, niet verloopt.
- **Uitgeschakeld** : deze instelling zorgt ervoor dat de gebruiker tijdens elke uitvoering van het beleid de hele gebruikers stroom uitvoert.

### <a name="session-life-time"></a>Duur van de sessie

De **duur** van de sessie is de tijd dat de cookie van de Azure AD B2C sessie wordt opgeslagen in de browser van de gebruiker nadat de verificatie is geslaagd. U kunt de tijds duur van de sessie instellen op een waarde tussen 15 en 720 minuten.

### <a name="keep-me-signed-in"></a>Aangemeld blijven

De functie [aangemeld blijven](custom-policy-keep-me-signed-in.md) is de duur van de sessie langer door het gebruik van een permanente cookie. De sessie blijft actief nadat de gebruiker de browser heeft gesloten en opnieuw opent. De sessie wordt alleen ingetrokken wanneer een gebruiker zich afmeldt. De functie aangemeld blijven is alleen van toepassing op aanmelden met lokale accounts.

De functie aangemeld blijven heeft voor rang op de duur van de sessie. Als de functie aangemeld blijven is ingeschakeld en de gebruiker deze selecteert, wordt in deze functie bepaald wanneer de sessie verloopt. 

### <a name="session-expiry-type"></a>Type sessie verloop

Het **type sessie verloop** geeft aan hoe een sessie wordt uitgebreid met de instelling voor de levens duur van de sessie of de instelling aangemeld blijven.

- **Rolling** -geeft aan dat de sessie wordt uitgebreid telkens wanneer de gebruiker een verificatie op basis van cookies uitvoert (standaard).
- **Absoluut** : geeft aan dat de gebruiker na de opgegeven tijds periode opnieuw moet worden geverifieerd.

## <a name="sign-out"></a>Afmelden

Wanneer u de gebruiker van de toepassing wilt ondertekenen, is het niet voldoende om de cookies van de toepassing te wissen of de sessie te beëindigen met de gebruiker. U moet de gebruiker omleiden naar Azure AD B2C om u af te melden. Anders is het mogelijk dat de gebruiker zich opnieuw kan aanmelden bij uw toepassingen zonder dat u de referenties opnieuw hoeft in te voeren.

Bij een afmeldings aanvraag Azure AD B2C:

1. Hiermee wordt de Azure AD B2C cookie op basis van cookies ongeldig gemaakt.
1. Pogingen om u af te melden bij federatieve id-providers:
   - OpenID Connect Connect-als het-eind punt van de ID-provider met de bekende configuratie een `end_session_endpoint` locatie aangeeft.
   - OAuth2: als de [meta gegevens van de identiteits provider](oauth2-technical-profile.md#metadata) de `end_session_endpoint` locatie bevatten.
   - SAML: als de [meta gegevens van de identiteits provider](saml-identity-provider-technical-profile.md#metadata) de `SingleLogoutService` locatie bevatten.
1. U kunt zich optioneel afmelden bij andere toepassingen. Zie de sectie voor [eenmalige afmelding](#single-sign-out) voor meer informatie.

> [!NOTE]
> Met [aangepaste beleids regels](custom-policy-overview.md)kunt u afmelden bij federatieve id-providers uitschakelen door de meta gegevens van het technische profiel van de identiteits provider `SingleLogoutEnabled` in te stellen op `false` .

Met de afmelding wordt de status voor eenmalige aanmelding van de gebruiker met Azure AD B2C gewist, maar de gebruiker kan de gebruikers naam niet van de provider sessie van de sociale id ondertekenen. Als de gebruiker tijdens een volgende aanmelding dezelfde id-provider selecteert, kunnen ze opnieuw worden geverifieerd zonder hun referenties in te voeren. Als een gebruiker zich wil afmelden bij de toepassing, betekent dit niet noodzakelijkerwijs dat ze zich willen afmelden bij hun Facebook-account. Als lokale accounts echter worden gebruikt, wordt de sessie van de gebruiker op de juiste wijze beëindigd.

### <a name="single-sign-out"></a>Eenmalige afmelding 


> [!NOTE]
> Deze functie is beperkt tot [aangepast beleid](custom-policy-overview.md).

Wanneer u de gebruiker omleidt naar het eind punt van de Azure AD B2C-afmelding (voor zowel OAuth2-als SAML-protocollen), Azure AD B2C de gebruikers sessie wissen uit de browser. De gebruiker kan echter nog steeds zijn aangemeld bij andere toepassingen die Azure AD B2C voor verificatie gebruiken. Om ervoor te zorgen dat deze toepassingen de gebruiker tegelijk kunnen ondertekenen, stuurt Azure AD B2C een HTTP GET-aanvraag naar de registratie `LogoutUrl` van alle toepassingen waarbij de gebruiker momenteel is aangemeld.


Toepassingen moeten reageren op deze aanvraag door een wille keurige sessie te wissen waarmee de gebruiker wordt geïdentificeerd en een antwoord wordt geretourneerd `200` . Als u eenmalige afmelding in uw toepassing wilt ondersteunen, moet u een `LogoutUrl` in de code van uw toepassing implementeren. 

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [configureren van het gedrag van sessies in de gebruikers stroom](session-behavior.md).
- Meer informatie over het [configureren van het gedrag van sessies in aangepaste beleids regels](session-behavior-custom-policy.md).

---
title: Voorwaardelijke toegang voor gebruikers van B2B-samenwerking - Azure AD
description: Meer informatie over het afdwingen van beleid voor meervoudige verificatie Azure Active Directory B2B-gebruikers.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22a5388d15b18180539eb95990a29f7ddf4f1951
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739544"
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>Voorwaardelijke toegang voor gebruikers van B2B-samenwerking

In dit artikel wordt beschreven hoe organisaties beleidsregels voor voorwaardelijke toegang (CA) voor B2B-gastgebruikers kunnen gebruiken om toegang te krijgen tot hun resources.
>[!NOTE]
>Deze verificatie- of autorisatiestroom is iets anders voor gastgebruikers dan voor de bestaande gebruikers van die id-provider (IdP).

## <a name="authentication-flow-for-b2b-guest-users-from-an-external-directory"></a>Verificatiestroom voor B2B-gastgebruikers vanuit een externe directory

Het volgende diagram illustreert de stroom: ![ afbeelding toont verificatiestroom voor B2B-gastgebruikers vanuit een externe directory](./media/conditional-access-b2b/authentication-flow-b2b-guests.png)

| Stap | Beschrijving |
|--------------|-----------------------|
| 1. | De B2B-gastgebruiker vraagt toegang tot een resource aan. De resource leidt de gebruiker om naar de resourceten tenant, een vertrouwde IdP.|
| 2. | De resourceten tenant identificeert de gebruiker als extern en leidt de gebruiker om naar de IdP van de B2B-gastgebruiker. De gebruiker voert primaire verificatie uit in de IdP.
| 3. | De IdP van de B2B-gastgebruiker geeft een token uit aan de gebruiker. De gebruiker wordt teruggeleid naar de resource-tenant met het token. De resourceten tenant valideert het token en evalueert vervolgens de gebruiker op de ca-beleidsregels. De resource-tenant kan bijvoorbeeld vereisen dat de gebruiker Azure Active Directory (AD) Multi-Factor Authentication moet uitvoeren.
| 4. | Zodra aan alle CA-beleidsregels voor resourcetendentens is voldaan, geeft de resourcetender een eigen token uit en wordt de gebruiker omgeleid naar de resource.

## <a name="authentication-flow-for-b2b-guest-users-with-one-time-passcode"></a>Verificatiestroom voor B2B-gastgebruikers met een een time-wachtwoordcode

Het volgende diagram illustreert de stroom: ![ afbeelding toont verificatiestroom voor B2B-gastgebruikers met een een time-wachtwoordcode](./media/conditional-access-b2b/authentication-flow-b2b-guests-otp.png)

| Stap | Beschrijving |
|--------------|-----------------------|
| 1. |De gebruiker vraagt toegang aan tot een resource in een andere tenant. De resource leidt de gebruiker om naar de resourceten tenant, een vertrouwde IdP.|
| 2. | De resource-tenant identificeert de gebruiker als een externe gebruiker met een een [time-e-mail met een wachtwoordcode (OTP)](./one-time-passcode.md) en verzendt een e-mailbericht met de OTP naar de gebruiker.|
| 3. | De gebruiker haalt de OTP op en verstuurt de code. De resourceten tenant evalueert de gebruiker op de ca-beleidsregels.
| 4. | Zodra aan alle CA-beleidsregels is voldaan, geeft de resourcetender een token uit en wordt de gebruiker omgeleid naar de resource. |

>[!NOTE]
>Als de gebruiker afkomstig is van een externe resource-tenant, kan het IDP-CA-beleid van de B2B-gastgebruiker niet ook worden geëvalueerd. Vanaf dit moment zijn alleen de CA-beleidsregels van de resource-tenant van toepassing op de gasten.

## <a name="azure-ad-multi-factor-authentication-for-b2b-users"></a>Azure AD Multi-Factor Authentication voor B2B-gebruikers

Organisaties kunnen meerdere Azure AD Multi-Factor Authentication-beleidsregels afdwingen voor hun B2B-gastgebruikers. Deze beleidsregels kunnen op tenant-, app- of afzonderlijke gebruikersniveau op dezelfde manier worden afgedwongen als voor full-time werknemers en leden van de organisatie.
De resourcetender is altijd verantwoordelijk voor Azure AD Multi-Factor Authentication voor gebruikers, zelfs als de organisatie van de gastgebruiker multi-factor Authentication-mogelijkheden heeft. Hier is een voorbeeld:

1. Een beheerder of informatiemedewerker in een bedrijf met de naam Fabrikam nodigt de gebruiker uit van een ander bedrijf met de naam Contoso om de toepassing Woodgrove te gebruiken.

2. De Woodgrove-app in Fabrikam is geconfigureerd om Azure AD Multi-Factor Authentication te vereisen voor toegang.

3. Wanneer de B2B-gastgebruiker van Contoso toegang probeert te krijgen tot Woodgrove in de Fabrikam-tenant, wordt hij gevraagd om de Azure AD Multi-Factor Authentication-uitdaging te voltooien.

4. De gastgebruiker kan vervolgens zijn Azure AD Multi-Factor Authentication instellen met Fabrikam en de opties selecteren.

5. Dit scenario werkt voor elke identiteit: Azure AD of persoonlijk Microsoft-account (MSA). Bijvoorbeeld als de gebruiker in Contoso verifieert met behulp van sociale id.

6. Fabrikam moet voldoende Premium Azure AD-licenties hebben die ondersteuning bieden voor Azure AD Multi-Factor Authentication. De gebruiker van Contoso gebruikt deze licentie vervolgens van Fabrikam. Zie [factureringsmodel voor externe Azure AD-identiteiten](./external-identities-pricing.md) voor informatie over de B2B-licentieverlening.

>[!NOTE]
>Azure AD Multi-Factor Authentication wordt uitgevoerd op resource-tenancy om voorspelbaarheid te garanderen. Wanneer gastgebruikers zich aanmelden, zien ze de aanmeldingspagina van de resourcetender op de achtergrond en hun eigen aanmeldingspagina voor de startten tenant en het bedrijfslogo op de voorgrond.

### <a name="set-up-azure-ad-multi-factor-authentication-for-b2b-users"></a>Azure AD Multi-Factor Authentication instellen voor B2B-gebruikers

Bekijk deze video om Azure AD Multi-Factor Authentication in te stellen voor gebruikers van B2B-samenwerking:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-azure-ad-multi-factor-authentication-for-offer-redemption"></a>B2B-gebruikers Azure AD Multi-Factor Authentication voor het inwisselen van een aanbieding

Bekijk deze video voor meer informatie over de inwisselervaring van Azure AD Multi-Factor Authentication:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="azure-ad-multi-factor-authentication-reset-for-b2b-users"></a>Azure AD Multi-Factor Authentication opnieuw instellen voor B2B-gebruikers

Nu zijn de volgende PowerShell-cmdlets beschikbaar om B2B-gastgebruikers te schalen:

1. Verbinding maken met Azure AD

   ```
   $cred = Get-Credential
   Connect-MsolService -Credential $cred
   ```
2. Alle gebruikers met methoden voor proof-up krijgen

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```
   Hier volgt een voorbeeld:

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```

3. Stel de Azure AD Multi-Factor Authentication-methode voor een specifieke gebruiker opnieuw in om te vereisen dat de B2B-samenwerkingsgebruiker opnieuw controlemethoden in stelt. 
   Hier volgt een voorbeeld:

   ```
   Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
   ```

## <a name="conditional-access-for-b2b-users"></a>Voorwaardelijke toegang voor B2B-gebruikers

Er zijn verschillende factoren die van invloed zijn op CA-beleid voor B2B-gastgebruikers.

### <a name="device-based-conditional-access"></a>Voorwaardelijke toegang op basis van het apparaat

In CA is er een optie om te vereisen dat het apparaat van een gebruiker [compatibel of hybride Azure AD-lid is.](../conditional-access/concept-conditional-access-conditions.md#device-state-preview) B2B-gastgebruikers kunnen alleen voldoen aan de naleving als de resource-tenant hun apparaat kan beheren. Apparaten kunnen niet door meer dan één organisatie tegelijk worden beheerd. B2B-gastgebruikers kunnen niet voldoen aan de Hybrid Azure AD-join omdat ze geen on-premises AD-account hebben. Alleen als het apparaat van de gastgebruiker onbeheerd is, kan het apparaat worden geregistreerd of geregistreerd bij de resource-tenant en vervolgens het apparaat compatibel maken. De gebruiker kan vervolgens voldoen aan het toekenningsbesturingselement.

>[!Note]
>Het wordt afgeraden om een beheerd apparaat voor externe gebruikers te vereisen.

### <a name="mobile-application-management-policies"></a>Beleidsregels voor Mobile Application Management

Voor de CA-toekenningsbesturingselementen, zoals Goedgekeurde **client-apps vereisen** en App-beveiligingsbeleid **vereisen,** moet het apparaat worden geregistreerd in de tenant. Deze besturingselementen kunnen alleen worden toegepast op [iOS- en Android-apparaten.](../conditional-access/concept-conditional-access-conditions.md#device-platforms) Geen van deze besturingselementen kan echter worden toegepast op B2B-gastgebruikers als het apparaat van de gebruiker al wordt beheerd door een andere organisatie. Een mobiel apparaat kan niet in meer dan één tenant tegelijk worden geregistreerd. Als het mobiele apparaat wordt beheerd door een andere organisatie, wordt de gebruiker geblokkeerd. Alleen als het apparaat van de gastgebruiker onbeheerd is, kan het apparaat worden geregistreerd in de resource-tenant. De gebruiker kan vervolgens voldoen aan het toekenningsbesturingselement.  

>[!NOTE]
>Het wordt afgeraden om een app-beveiligingsbeleid voor externe gebruikers te vereisen.

### <a name="location-based-conditional-access"></a>Voorwaardelijke toegang op basis van locatie

Het beleid op basis van locatie op basis van IP-adresbereiken kan worden afgedwongen als de uitnodigende organisatie een vertrouwd [IP-adresbereik](../conditional-access/concept-conditional-access-conditions.md#locations) kan maken dat de partnerorganisaties definieert.

Beleid kan ook worden afgedwongen op basis van **geografische locaties.**

### <a name="risk-based-conditional-access"></a>Voorwaardelijke toegang op basis van risico

Het [beleid voor aanmeldingsrisico's](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk) wordt afgedwongen als de B2B-gastgebruiker voldoet aan het toekenningsbeheer. Een organisatie kan bijvoorbeeld Azure AD Multi-Factor Authentication vereisen voor gemiddeld of hoog aanmeldingsrisico. Als een gebruiker zich echter nog niet eerder heeft geregistreerd voor Azure AD Multi-Factor Authentication in de resource-tenant, wordt de gebruiker geblokkeerd. Dit wordt gedaan om te voorkomen dat kwaadwillende gebruikers hun eigen Azure AD Multi-Factor Authentication-referenties registreren in het geval ze het wachtwoord van een legitieme gebruiker in gevaar brengen.

Het [beleid voor gebruikersrisico's](../conditional-access/concept-conditional-access-conditions.md#user-risk) kan echter niet worden opgelost in de resource-tenant. Als u bijvoorbeeld een wachtwoordwijziging nodig hebt voor gastgebruikers met een hoog risico, worden ze geblokkeerd vanwege het onvermogen om wachtwoorden opnieuw in te stellen in de resourcemap.

### <a name="conditional-access-client-apps-condition"></a>Voorwaarde voor client-apps voor voorwaardelijke toegang

[Voorwaarden van client-apps](../conditional-access/concept-conditional-access-conditions.md#client-apps) gedragen zich hetzelfde voor B2B-gastgebruikers als voor elk ander type gebruiker. U kunt bijvoorbeeld voorkomen dat gastgebruikers verouderde verificatieprotocollen gebruiken.

### <a name="conditional-access-session-controls"></a>Sessiebesturingselementen voor voorwaardelijke toegang

[Sessiebesturingselementen](../conditional-access/concept-conditional-access-session.md) gedragen zich hetzelfde voor B2B-gastgebruikers als voor elk ander type gebruiker.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen over Azure AD B2B-samenwerking voor meer informatie:

- [Wat is Azure AD B2B-samenwerking?](./what-is-b2b.md)
- [Identiteitsbeveiliging en B2B-gebruikers](../identity-protection/concept-identity-protection-b2b.md)
- [Prijzen van externe identiteiten](https://azure.microsoft.com/pricing/details/active-directory/)
- [Veelgestelde vragen](./faq.md)

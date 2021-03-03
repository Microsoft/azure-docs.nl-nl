---
title: Voorwaardelijke toegang voor B2B-samenwerkings gebruikers-Azure AD
description: Meer informatie over het afdwingen van multi-factor Authentication-beleids regels voor Azure Active Directory B2B-gebruikers.
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
ms.openlocfilehash: 74bfa4987f584bbd3490bc5f4f187dee5bc1bd87
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101646279"
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>Voorwaardelijke toegang voor B2B-samenwerkings gebruikers

In dit artikel wordt beschreven hoe organisaties beleid voor voorwaardelijke toegang (CA) kunnen bereiken voor B2B-gast gebruikers om toegang te krijgen tot hun resources.
>[!NOTE]
>Deze verificatie-of autorisatie stroom is een andere bit voor gast gebruikers dan voor de bestaande gebruikers van die id-provider (IdP).

## <a name="authentication-flow-for-b2b-guest-users-from-an-external-directory"></a>Verificatie stroom voor B2B-gast gebruikers vanuit een externe map

In het volgende diagram ziet u de stroom: ![ afbeelding toont de verificatie stroom voor B2B-gast gebruikers vanuit een externe map](./media/conditional-access-b2b/authentication-flow-b2b-guests.png)

| Stap | Beschrijving |
|--------------|-----------------------|
| 1. | De B2B-gast gebruiker vraagt toegang tot een bron. De resource leidt de gebruiker naar de resource Tenant, een vertrouwde IdP.|
| 2. | De resource-Tenant identificeert de gebruiker als extern en leidt de gebruiker om naar de IdP van de B2B-gast gebruiker. De gebruiker voert primaire verificatie uit in het IdP.
| 3. | De IdP van de B2B-gast gebruiker geeft een token door aan de gebruiker. De gebruiker wordt terug omgeleid naar de resource Tenant met het token. De resource-Tenant valideert het token en evalueert de gebruiker vervolgens op basis van het CA-beleid. De resource-Tenant kan bijvoorbeeld vereisen dat de gebruiker Azure Active Directory (AD) Multi-Factor Authentication uitvoert.
| 4. | Als aan alle beleids regels voor de Tenant-CA van de resource is voldaan, wordt door de resource Tenant een eigen token uitgegeven en wordt de gebruiker omgeleid naar de bijbehorende resource.

## <a name="authentication-flow-for-b2b-guest-users-with-one-time-passcode"></a>Verificatie stroom voor B2B-gast gebruikers met een eenmalige wachtwoord code

In het volgende diagram ziet u de stroom: ![ afbeelding toont de verificatie stroom voor B2B-gast gebruikers met een eenmalige wachtwoord code](./media/conditional-access-b2b/authentication-flow-b2b-guests-otp.png)

| Stap | Beschrijving |
|--------------|-----------------------|
| 1. |De gebruiker vraagt om toegang tot een bron in een andere Tenant. De resource leidt de gebruiker naar de resource Tenant, een vertrouwde IdP.|
| 2. | De resource-Tenant identificeert de gebruiker als een [externe e-mail met een eenmalige wachtwoord code (OTP)](./one-time-passcode.md) en stuurt een e-mail met het otp-e-mail bericht naar de gebruiker.|
| 3. | De gebruiker haalt de OTP op en verzendt de code. De resource-Tenant evalueert de gebruiker aan de hand van het CA-beleid.
| 4. | Als aan alle CA-beleids regels wordt voldaan, geeft de resource-Tenant een token door en wordt de gebruiker omgeleid naar de bijbehorende resource. |

>[!NOTE]
>Als de gebruiker afkomstig is van een externe bron Tenant, is het niet mogelijk om de IdP CA-beleids regels van de B2B-gast gebruiker ook te evalueren. Vanaf nu is alleen het CA-beleid van de resource Tenant van toepassing op de gasten.

## <a name="azure-ad-multi-factor-authentication-for-b2b-users"></a>Azure AD-Multi-Factor Authentication voor B2B-gebruikers

Organisaties kunnen meerdere Azure AD Multi-Factor Authentication-beleid afdwingen voor hun B2B-gast gebruikers. Deze beleids regels kunnen worden afgedwongen op het niveau van de Tenant, app of individuele gebruiker op dezelfde manier als die voor fulltime werk nemers en leden van de organisatie.
De resource-Tenant is altijd verantwoordelijk voor Azure AD-Multi-Factor Authentication voor gebruikers, zelfs als de organisatie van de gast gebruiker Multi-Factor Authentication mogelijkheden heeft. Hier volgt een voor beeld:

1. Een beheerder of informatie medewerker in een bedrijf met de naam fabrikam nodigt een gebruiker van een ander bedrijf met de naam contoso uit om hun toepassing Woodgrove te gebruiken.

2. De Woodgrove-app in Fabrikam is zodanig geconfigureerd dat Azure AD-Multi-Factor Authentication op de toegang is vereist.

3. Wanneer de B2B-gast gebruiker van Contoso probeert toegang te krijgen tot Woodgrove in de fabrikam-Tenant, wordt deze gevraagd om de Azure AD-Multi-Factor Authentication uitdaging te volt ooien.

4. De gast gebruiker kan vervolgens hun Azure AD-Multi-Factor Authentication instellen met Fabrikam en de opties selecteren.

5. Dit scenario werkt voor elke identiteit: Azure AD of persoonlijk micro soft-account (MSA). Bijvoorbeeld, als gebruiker in contoso verifieert met sociale ID.

6. Fabrikam moet voldoende Premium Azure AD-licenties hebben die Azure AD-Multi-Factor Authentication ondersteunen. De gebruiker van Contoso gebruikt deze licentie vervolgens van fabrikam. Zie het [facturerings model voor externe Azure AD-identiteiten](./external-identities-pricing.md) voor informatie over de B2B-licentie verlening.

>[!NOTE]
>Azure AD Multi-Factor Authentication wordt uitgevoerd bij de resource pacht om voorspel baarheid te garanderen.

### <a name="set-up-azure-ad-multi-factor-authentication-for-b2b-users"></a>Azure AD-Multi-Factor Authentication instellen voor B2B-gebruikers

Bekijk deze video voor het instellen van Azure AD-Multi-Factor Authentication voor gebruikers van B2B-samen werking:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-azure-ad-multi-factor-authentication-for-offer-redemption"></a>B2B-gebruikers Azure AD Multi-Factor Authentication voor aanbiedings inwisseling

Bekijk de volgende video voor meer informatie over de inwisseling van Azure AD Multi-Factor Authentication:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="azure-ad-multi-factor-authentication-reset-for-b2b-users"></a>Azure AD Multi-Factor Authentication opnieuw instellen voor B2B-gebruikers

Nu zijn de volgende Power shell-cmdlets beschikbaar om B2B-gast gebruikers te controleren:

1. Verbinding maken met Azure AD

   ```
   $cred = Get-Credential
   Connect-MsolService -Credential $cred
   ```
2. Alle gebruikers ophalen met methoden voor testen

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```
   Hier volgt een voorbeeld:

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```

3. Stel de Azure AD Multi-Factor Authentication-methode voor een specifieke gebruiker opnieuw in, zodat de B2B-samenwerkings gebruiker opnieuw proef methoden kan instellen. 
   Hier volgt een voorbeeld:

   ```
   Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
   ```

## <a name="conditional-access-for-b2b-users"></a>Voorwaardelijke toegang voor B2B-gebruikers

Er zijn verschillende factoren die van invloed zijn op CA-beleid voor B2B-gast gebruikers.

### <a name="device-based-conditional-access"></a>Voorwaardelijke toegang op basis van het apparaat

In CA is er een optie vereist om te vereisen dat het apparaat van een gebruiker [compatibel of hybride Azure AD is toegevoegd](../conditional-access/concept-conditional-access-conditions.md#device-state-preview). B2B-gast gebruikers kunnen alleen voldoen aan de naleving als de resource Tenant het apparaat kan beheren. Apparaten kunnen niet door meer dan één organisatie tegelijk worden beheerd. B2B-gast gebruikers kunnen niet voldoen aan de hybride Azure AD-deelname omdat ze geen on-premises AD-account hebben. Alleen als het apparaat van de gast gebruiker niet-beheerd is, kunnen ze hun apparaat registreren of inschrijven bij de resource Tenant en vervolgens het apparaat compatibel maken. De gebruiker kan dan voldoen aan het besturings element Grant.

>[!Note]
>Het is niet raadzaam een beheerd apparaat voor externe gebruikers te vereisen.

### <a name="mobile-application-management-policies"></a>Beleidsregels voor Mobile Application Management

De CA verleent besturings elementen zoals **goedgekeurde client-apps vereisen** en **vereisen dat** het apparaat in de Tenant wordt geregistreerd. Deze besturings elementen kunnen alleen worden toegepast op [IOS-en Android-apparaten](../conditional-access/concept-conditional-access-conditions.md#device-platforms). Geen van deze besturings elementen kan echter worden toegepast op B2B-gast gebruikers als het apparaat van de gebruiker al wordt beheerd door een andere organisatie. Een mobiel apparaat kan niet in meer dan één Tenant tegelijk worden geregistreerd. Als het mobiele apparaat wordt beheerd door een andere organisatie, wordt de gebruiker geblokkeerd. Alleen als het apparaat van de gast gebruiker niet-beheerd is, kunnen ze hun apparaat registreren in de resource-Tenant. De gebruiker kan dan voldoen aan het besturings element Grant.  

>[!NOTE]
>Het is niet raadzaam om een beveiligings beleid voor apps voor externe gebruikers te vereisen.

### <a name="location-based-conditional-access"></a>Voorwaardelijke toegang op basis van locatie

Het op [locatie gebaseerde beleid](../conditional-access/concept-conditional-access-conditions.md#locations) op basis van IP-adresbereiken kan worden afgedwongen als de uitgenodigde organisatie een vertrouwd IP-adres bereik kan maken dat de partner organisaties definieert.

Beleids regels kunnen ook worden afgedwongen op basis van **geografische locaties**.

### <a name="risk-based-conditional-access"></a>Voorwaardelijke toegang op basis van risico

Het [beleid voor aanmeldings Risico's](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk) wordt afgedwongen als de B2B-gast gebruiker voldoet aan de granting Control. Een organisatie kan bijvoorbeeld Azure AD Multi-Factor Authentication vereisen voor gemiddeld of hoog aanmeld risico. Als een gebruiker echter niet eerder is geregistreerd voor Azure AD-Multi-Factor Authentication in de resource-Tenant, wordt de gebruiker geblokkeerd. Dit wordt gedaan om te voor komen dat kwaadwillende gebruikers hun eigen Azure AD-Multi-Factor Authentication referenties registreren in de gebeurtenis die ze het wacht woord van een rechtmatig gebruikers veroorzaken.

Het [beleid voor gebruikers Risico's](../conditional-access/concept-conditional-access-conditions.md#user-risk) kan echter niet worden omgezet in de resource-Tenant. Als u bijvoorbeeld een wacht woord moet wijzigen voor gast gebruikers met een hoog risico, worden ze geblokkeerd omdat het niet mogelijk is om wacht woorden opnieuw in te stellen in de resource directory.

### <a name="conditional-access-client-apps-condition"></a>Voor waarde voor client-apps voor voorwaardelijke toegang

De voor [waarden voor client-apps](../conditional-access/concept-conditional-access-conditions.md#client-apps) gedragen zich hetzelfde voor B2B-gast gebruikers als voor elk ander type gebruiker. U kunt bijvoorbeeld voor komen dat gast gebruikers verouderde verificatie protocollen gebruiken.

### <a name="conditional-access-session-controls"></a>Sessie besturings elementen voor voorwaardelijke toegang

[Sessie besturings elementen](../conditional-access/concept-conditional-access-session.md) gedragen zich hetzelfde voor B2B-gast gebruikers als voor elk ander type gebruiker.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie de volgende artikelen over Azure AD B2B-samen werking:

- [Wat is Azure AD B2B-samenwerking?](./what-is-b2b.md)
- [Identiteitsbeveiliging en B2B-gebruikers](../identity-protection/concept-identity-protection-b2b.md)
- [Prijzen van externe identiteiten](https://azure.microsoft.com/pricing/details/active-directory/)
- [Veelgestelde vragen (FAQ)](./faq.md)
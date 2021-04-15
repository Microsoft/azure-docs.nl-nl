---
title: Toestemming voor toepassingen beheren en toestemmingsaanvragen evalueren in Azure Active Directory
description: Meer informatie over het beheren van toestemmingsaanvragen wanneer toestemming van de gebruiker is uitgeschakeld of beperkt, en hoe u een aanvraag evalueert voor tenantbrede beheerders toestemming voor een toepassing in Azure Active Directory.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 12/27/2019
ms.author: iangithinji
ms.reviewer: phsignor
ms.openlocfilehash: 3405181f9bace023950e583dfe1a334216bf0aa0
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107373941"
---
# <a name="managing-consent-to-applications-and-evaluating-consent-requests"></a>Toestemming voor toepassingen beheren en toestemmingsaanvragen evalueren

Microsoft [raadt aan](../../security/fundamentals/steps-secure-identity.md#restrict-user-consent-operations) om toestemming van eindgebruikers voor toepassingen uit te uitschakelen. Dit centraliseert het besluitvormingsproces met het beveiligings- en identiteitsbeheerdersteam van uw organisatie.

Nadat toestemming van eindgebruikers is uitgeschakeld of beperkt, zijn er enkele belangrijke overwegingen om ervoor te zorgen dat uw organisatie veilig blijft terwijl bedrijfskritieke toepassingen nog steeds kunnen worden gebruikt. Deze stappen zijn essentieel om de impact op het ondersteuningsteam en IT-beheerders van uw organisatie te minimaliseren, terwijl het gebruik van niet-beherende accounts in toepassingen van derden wordt voorkomen.

## <a name="process-changes-and-education"></a>Wijzigingen en onderwijs verwerken

 1. Overweeg de werkstroom [voor beheerdersgoedkeuring](configure-admin-consent-workflow.md) in te stellen zodat gebruikers goedkeuring van de beheerder rechtstreeks vanuit het toestemmingsscherm kunnen aanvragen.

 2. Zorg ervoor dat alle beheerders het [machtigingen- en toestemmingskader](../develop/consent-framework.md)begrijpen, hoe de toestemmingsprompt [werkt](../develop/application-consent-experience.md) en hoe ze een aanvraag voor toestemming van een [tenantbrede beheerder kunnen evalueren.](#evaluating-a-request-for-tenant-wide-admin-consent)
 3. Controleer de bestaande processen van uw organisatie, zodat gebruikers goedkeuring van de beheerder voor een toepassing kunnen aanvragen en indien nodig updates kunnen aanbrengen. Als processen worden gewijzigd:
    * Werk de relevante documentatie, bewaking, automatisering, en meer bij.
    * Communiceer proceswijzigingen naar alle betrokken gebruikers, ontwikkelaars, ondersteuningsteams en IT-beheerders.

## <a name="auditing-and-monitoring"></a>Controleren en bewaken

1. [Controleer apps en verleende machtigingen](../../security/fundamentals/steps-secure-identity.md#audit-apps-and-consented-permissions) in uw organisatie om ervoor te zorgen dat er eerder geen onoplettbare of verdachte toepassingen toegang tot gegevens zijn verleend.

2. Bekijk Het detecteren en herstellen van onrechtmatige toestemmingsverlening [in Office 365](/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants) voor aanvullende best practices en veiligheidsmaatregelen tegen verdachte toepassingen die OAuth-toestemming aanvragen.

3. Als uw organisatie de juiste licentie heeft:

    * Gebruik aanvullende [controlefuncties voor OAuth-toepassingen in Microsoft Cloud App Security](/cloud-app-security/investigate-risky-oauth).
    * Gebruik [Azure Monitor Workbooks om machtigingen en activiteit met betrekking tot toestemming](../reports-monitoring/howto-use-azure-monitor-workbooks.md) te bewaken. De *Consent Insights-werkmap* biedt een weergave van apps op aantal mislukte toestemmingsaanvragen. Dit kan handig zijn bij het prioriteren van toepassingen die beheerders kunnen beoordelen en bepalen of ze toestemming van de beheerder moeten verlenen.

### <a name="additional-considerations-for-reducing-friction"></a>Aanvullende overwegingen voor het verminderen van frictie

Als u de gevolgen voor vertrouwde, bedrijfskritische toepassingen die al in gebruik zijn wilt minimaliseren, kunt u overwegen om proactief beheerders toestemming te geven voor toepassingen met een groot aantal gebruikersverklaringen:

1. Inventariseren van de apps die al zijn toegevoegd aan uw organisatie met een hoog gebruik, op basis van aanmeldingslogboeken of activiteit voor toestemming verlenen. Een [PowerShell-script](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09) kan worden gebruikt om snel en eenvoudig toepassingen te ontdekken met een groot aantal toestemmingsaanvragen van gebruikers.

2. Evalueer de belangrijkste toepassingen die nog geen beheerdersmachtiging hebben gekregen.

   > [!IMPORTANT]
   > Evalueer een toepassing zorgvuldig voordat u tenantbrede beheerdersmachtiging verleent, zelfs als veel gebruikers in de organisatie al toestemming voor zichzelf hebben gegeven.

3. Voor elke toepassing die is goedgekeurd, verleent u beheerders voor de hele tenant toestemming met behulp van een van de onderstaande methoden.

4. Overweeg voor elke goedgekeurde toepassing [gebruikerstoegang te beperken.](configure-user-consent.md)

## <a name="evaluating-a-request-for-tenant-wide-admin-consent"></a>Evaluatie van een aanvraag voor beheerdersmachtiging voor de hele tenant

Het verlenen van beheerdersmachtiging voor de hele tenant is een gevoelige bewerking.  Machtigingen worden verleend namens de hele organisatie en kunnen machtigingen bevatten om bewerkingen met hoge bevoegdheden uit te voeren. Bijvoorbeeld rolbeheer, volledige toegang tot alle postvakken of alle sites en volledige gebruikers imitatie.

Voordat u tenantbrede beheerderstoegang verleent, moet u ervoor zorgen dat u de toepassing en de uitgever van de toepassing vertrouwt voor het toegangsniveau dat u verleent. Als u niet zeker weet wie de toepassing beheert en waarom de toepassing de machtigingen aanvraagt, *verleent u geen toestemming.*

De volgende lijst bevat enkele aanbevelingen die u kunt overwegen bij het evalueren van een aanvraag om beheerders toestemming te verlenen.

* **Meer informatie over [het machtigingen- en toestemmingskader](../develop/consent-framework.md) in het Microsoft Identity Platform.**

* **Het verschil tussen [gedelegeerde machtigingen en toepassingsmachtigingen begrijpen.](../develop/v2-permissions-and-consent.md#permission-types)**

   Met toepassingsmachtigingen heeft de toepassing toegang tot de gegevens voor de hele organisatie, zonder tussenkomst van de gebruiker. Met gedelegeerde machtigingen kan de toepassing handelen namens een gebruiker die op een bepaald moment is aangemeld bij de toepassing.

* **Meer informatie over de machtigingen die worden aangevraagd.**

   De machtigingen die door de toepassing worden aangevraagd, worden vermeld in de [toestemmingsprompt.](../develop/application-consent-experience.md) Als u de titel van de machtiging uit breidt, wordt de beschrijving van de machtiging weergegeven. De beschrijving voor toepassingsmachtigingen eindigt doorgaans op 'zonder een aangemelde gebruiker'. De beschrijving voor gedelegeerde machtigingen eindigt doorgaans op 'namens de aangemelde gebruiker'. Machtigingen voor de Microsoft Graph API worden beschreven in [Microsoft Graph Permissions Reference](/graph/permissions-reference) (Naslag voor machtigingen). Raadpleeg de documentatie voor andere API's voor meer informatie over de machtigingen die ze beschikbaar maken.

   Als u niet begrijpt dat een machtiging wordt aangevraagd, *verleent u geen toestemming.*

* **Begrijpen welke toepassing machtigingen aanvraagt en wie de toepassing heeft gepubliceerd.**

   Wees op uw hoede voor schadelijke toepassingen die op andere toepassingen lijken.

   Als u twijfelt over de echtheid van een toepassing of de uitgever ervan, *verleent u geen toestemming.* Zoek in plaats daarvan naar extra bevestiging (bijvoorbeeld rechtstreeks van de uitgever van de toepassing).

* **Zorg ervoor dat de aangevraagde machtigingen zijn afgestemd op de functies die u van de toepassing verwacht.**

   Een toepassing die SharePoint-sitebeheer aanbiedt, kan bijvoorbeeld gedelegeerde toegang vereisen om alle siteverzamelingen te lezen, maar hoeft niet noodzakelijkerwijs volledige toegang te hebben tot alle postvakken of volledige imitatiebevoegdheden in de map.

   Als u vermoedt dat de toepassing meer machtigingen aanvraagt dan nodig is, *verleent u geen toestemming.* Neem contact op met de uitgever van de toepassing voor meer informatie.

## <a name="granting-consent-as-an-administrator"></a>Toestemming verlenen als beheerder

### <a name="granting-tenant-wide-admin-consent"></a>Tenantbrede beheerdersmachtiging verlenen
Zie Beheerdersmachtiging voor de hele [tenant](grant-admin-consent.md) verlenen aan een toepassing voor stapsgewijs instructies voor het verlenen van beheerdersmachtiging voor de hele tenant vanuit de Azure Portal, met behulp van Azure AD PowerShell of vanuit de toestemmingsprompt zelf.

### <a name="granting-consent-on-behalf-of-a-specific-user"></a>Toestemming verlenen namens een specifieke gebruiker
In plaats van toestemming te verlenen aan de hele organisatie, kan een beheerder ook de [Microsoft Graph-API](/graph/use-the-api) gebruiken om toestemming te verlenen voor gedelegeerde machtigingen namens één gebruiker. Zie Toegang krijgen namens een [gebruiker voor meer informatie.](/graph/auth-v2-user)

## <a name="limiting-user-access-to-applications"></a>Gebruikerstoegang tot toepassingen beperken
De toegang van gebruikers tot toepassingen kan nog steeds worden beperkt, zelfs wanneer toestemming van de tenantbrede beheerder is verleend. Zie Methoden voor het toewijzen van gebruikers en groepen voor meer informatie over het vereisen van gebruikerstoewijzing [aan een toepassing.](./assign-user-or-group-access-portal.md)

Zie Azure AD gebruiken voor toegangsbeheer voor toepassingen voor meer informatie over het afhandelen van aanvullende complexe [scenario's.](what-is-access-management.md)

## <a name="disable-all-future-user-consent-operations-to-any-application"></a>Alle toekomstige toestemmingsbewerkingen van gebruikers uitschakelen voor elke toepassing
Als u toestemming van de gebruiker voor uw hele directory uit te keurt, kunnen eindgebruikers geen toestemming geven voor een toepassing. Beheerders kunnen namens de gebruiker nog steeds toestemming geven. Lees Understanding user and admin consent (Toestemming van gebruiker en beheerder begrijpen) voor meer informatie over toestemming voor toepassingen en waarom u wel of niet [toestemming wilt geven.](../develop/howto-convert-app-to-be-multi-tenant.md)

Als u alle toekomstige toestemmingsbewerkingen van gebruikers in uw hele directory wilt uitschakelen, volgt u deze stappen:
1.  Open het [**Azure Portal**](https://portal.azure.com/) meld u aan als **globale beheerder.**
2.  Open de **Azure Active Directory door** te klikken op **Alle services** bovenaan het navigatiemenu linksboven.
3.  Typ **'Azure Active Directory'** in het filterzoekvak en selecteer het **Azure Active Directory** item.
4.  Selecteer **Gebruikers en groepen** in het navigatiemenu.
5.  Selecteer **Gebruikersinstellingen**.
6.  Schakel alle toekomstige toestemmingsbewerkingen voor gebruikers uit door de schakelknop Gebruikers **kunnen apps** toegang geven tot hun gegevens in te stellen op Nee **en** op de knop **Opslaan te** klikken.

## <a name="next-steps"></a>Volgende stappen
* [Vijf stappen voor het beveiligen van uw identiteitsinfrastructuur](../../security/fundamentals/steps-secure-identity.md#before-you-begin-protect-privileged-accounts-with-mfa)
* [De werkstroom voor beheerders toestemming configureren](configure-admin-consent-workflow.md)
* [Configureren hoe eindgebruikers toestemming geven voor toepassingen](configure-user-consent.md)
* [Machtigingen en toestemming in het Microsoft Identity Platform](../develop/v2-permissions-and-consent.md)

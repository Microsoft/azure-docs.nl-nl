---
title: Het beheren van toestemming voor toepassingen en het evalueren van toestemming aanvragen in Azure Active Directory
description: Meer informatie over het beheren van toestemming aanvragen wanneer de gebruikers toestemming is uitgeschakeld of beperkt, en hoe u een aanvraag voor de beheerder van de hele Tenant kunt evalueren in Azure Active Directory.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 12/27/2019
ms.author: kenwith
ms.reviewer: phsignor
ms.openlocfilehash: 189a89276d922665dd1ad0fbacc77ba499137048
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99253099"
---
# <a name="managing-consent-to-applications-and-evaluating-consent-requests"></a>Het beheren van toestemming voor toepassingen en het evalueren van toestemming aanvragen

Micro soft [raadt](../../security/fundamentals/steps-secure-identity.md#restrict-user-consent-operations) aan om de toestemming van de eind gebruiker voor toepassingen uit te scha kelen. Hiermee wordt het besluitvormings proces gecentraliseerd met het beveiligings-en identiteits beheerders team van uw organisatie.

Nadat de toestemming van de eind gebruiker is uitgeschakeld of beperkt, zijn er enkele belang rijke aandachtspunten om ervoor te zorgen dat uw organisatie veilig blijft terwijl bedrijfs kritieke toepassingen nog steeds kunnen worden gebruikt. Deze stappen zijn van cruciaal belang om de impact op het ondersteunings team van uw organisatie en IT-beheerders te minimaliseren, en het gebruik van niet-beheerde accounts in toepassingen van derden te voor komen.

## <a name="process-changes-and-education"></a>Proces wijzigingen en onderwijs

 1. Overweeg de [beheerder toestemming werk stroom (preview)](configure-admin-consent-workflow.md) in te scha kelen, zodat gebruikers rechtstreeks vanuit het venster voor toestemming beheerder kunnen vragen.

 2. Zorg ervoor dat alle beheerders inzicht hebben in het [toestemmings-en toestemmings raamwerk](../develop/consent-framework.md), hoe de [toestemming wordt gevraagd](../develop/application-consent-experience.md) en hoe [een aanvraag voor Tenant-beheerders toestemming](#evaluating-a-request-for-tenant-wide-admin-consent)moet worden geëvalueerd.
 3. Bekijk de bestaande processen van uw organisatie voor gebruikers om goed keuring van de beheerder aan te vragen voor een toepassing en breng zo nodig updates aan. Als processen worden gewijzigd:
    * Werk de relevante documentatie, bewaking, automatisering, enzovoort bij.
    * Wijzigingen in het proces door geven aan alle betrokken gebruikers, ontwikkel aars, ondersteunings teams en IT-beheerders.

## <a name="auditing-and-monitoring"></a>Controleren en bewaken

1. [Apps controleren en machtigingen verlenen](../../security/fundamentals/steps-secure-identity.md#audit-apps-and-consented-permissions) in uw organisatie om ervoor te zorgen dat er geen onzekere of verdachte toepassingen toegang hebben gekregen tot gegevens.

2. Bekijk [en herstel illegale toestemming subsidies in Office 365](/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants) voor extra aanbevolen procedures en beveiliging tegen verdachte toepassingen die OAuth-toestemming aanvragen.

3. Als uw organisatie de juiste licentie heeft:

    * Gebruik extra [controle functies voor OAuth-toepassingen in Microsoft Cloud app Security](/cloud-app-security/investigate-risky-oauth).
    * Gebruik [Azure monitor werkmappen om machtigingen en gerelateerde activiteiten te bewaken](../reports-monitoring/howto-use-azure-monitor-workbooks.md) . De *instemming* -werkmap van de toestemming biedt een overzicht van de apps op basis van het aantal mislukte toestemming aanvragen. Dit kan handig zijn om de prioriteit van toepassingen te bepalen en te bepalen of ze toestemming geven voor de beheerder.

### <a name="additional-considerations-for-reducing-friction"></a>Aanvullende overwegingen voor het verminderen van wrijving

Als u de gevolgen van vertrouwde, bedrijfskritische toepassingen die al in gebruik zijn, wilt minimaliseren, kunt u de toestemming van de beheerder proactief toekennen aan toepassingen met een groot aantal subsidies voor gebruikers toestemming:

1. Maak een inventarisatie van de apps die al zijn toegevoegd aan uw organisatie met een hoog gebruik, op basis van aanmeldings Logboeken of activiteit voor toestemming verlenen. Een Power shell- [script](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09) kan worden gebruikt om snel en eenvoudig toepassingen te detecteren met een groot aantal subsidies voor gebruikers toestemming.

2. De belangrijkste toepassingen evalueren waarvoor de beheerder nog geen toestemming heeft verleend.

   > [!IMPORTANT]
   > Evalueer zorgvuldig een toepassing voordat u toestemming verleent aan de beheerder voor de hele Tenant, zelfs als veel gebruikers in de organisatie al zelf hebben ingestemd.

3. Voor elke goedgekeurde toepassing verleent u toestemming van de beheerder voor de hele Tenant met een van de hieronder beschreven methoden.

4. Voor elke goedgekeurde toepassing kunt u de [gebruikers toegang beperken](configure-user-consent.md).

## <a name="evaluating-a-request-for-tenant-wide-admin-consent"></a>Een aanvraag voor Tenant beheerders toestemming evalueren

Het verlenen van beheerders toestemming voor de hele Tenant is een gevoelige bewerking.  Machtigingen worden verleend namens de hele organisatie en kunnen machtigingen bevatten voor het uitvoeren van zeer bevoegde bewerkingen. Bijvoorbeeld Role Management, volledige toegang tot alle post vakken of alle sites, en volledige gebruikers imitatie.

Voordat u toestemming van de beheerder voor de hele Tenant verleent, moet u ervoor zorgen dat u de toepassing en de uitgever van de toepassing vertrouwt voor het toegangs niveau dat u wilt verlenen. Als u niet zeker weet wie de toepassing bestuurt en waarom de toepassing de machtigingen aanvraagt, *mag u geen toestemming verlenen*.

De volgende lijst bevat enkele aanbevelingen waarmee u rekening moet houden bij het evalueren van een aanvraag om beheerders toestemming te verlenen.

* **Meer informatie over de [machtigingen en het toestemmings raamwerk](../develop/consent-framework.md) in het micro soft Identity-platform.**

* **Meer informatie over het verschil tussen [gedelegeerde machtigingen en toepassings machtigingen](../develop/v2-permissions-and-consent.md#permission-types).**

   Met toepassings machtigingen kan de toepassing toegang krijgen tot de gegevens voor de hele organisatie, zonder tussen komst van de gebruiker. Met gedelegeerde machtigingen kan de toepassing namens een gebruiker optreden die op een bepaald moment is aangemeld bij de toepassing.

* **Meer informatie over de aangevraagde machtigingen.**

   De machtigingen die door de toepassing worden aangevraagd, worden weer gegeven in de [toestemming prompt](../develop/application-consent-experience.md). Als u de machtigings titel uitbreidt, wordt de beschrijving van de machtiging weer gegeven. De beschrijving voor toepassings machtigingen eindigt doorgaans op zonder aangemelde gebruiker. De beschrijving voor gedelegeerde machtigingen eindigt doorgaans met ' namens de aangemelde gebruiker '. Machtigingen voor de Microsoft Graph-API worden beschreven in [Microsoft Graph machtigingen referentie](/graph/permissions-reference) -Raadpleeg de documentatie voor andere api's om inzicht te krijgen in de machtigingen die ze beschikbaar maken.

   Als u een aangevraagde machtiging niet begrijpt, *mag u geen toestemming verlenen*.

* **Begrijp welke toepassing machtigingen aanvraagt en wie de toepassing heeft gepubliceerd.**

   Wees op de hoede wanneer kwaad aardige toepassingen proberen te zoeken zoals andere toepassingen.

   Als u twijfelt over de geldigheid van een toepassing of de uitgever, mag u *geen toestemming verlenen*. Zoek in plaats daarvan aanvullende bevestiging (bijvoorbeeld rechtstreeks vanuit de uitgever van de toepassing).

* **Zorg ervoor dat de aangevraagde machtigingen zijn afgestemd op de functies die u van de toepassing verwacht.**

   Een toepassing die share point-site beheer biedt, kan bijvoorbeeld gedelegeerde toegang hebben om alle site verzamelingen te lezen, maar hoeft niet altijd volledige toegang te hebben tot alle post vakken of volledige imitatie bevoegdheden in de Directory.

   Als u vermoedt dat de toepassing meer machtigingen aanvraagt dan het nodig heeft, mag u *geen toestemming verlenen*. Neem contact op met de uitgever van de toepassing voor meer informatie.

## <a name="granting-consent-as-an-administrator"></a>Toestemming verlenen als beheerder

### <a name="granting-tenant-wide-admin-consent"></a>Toestemming van de beheerder voor de hele Tenant verlenen
Zie [machtigingen voor Tenant-brede beheerder toestaan voor een toepassing](grant-admin-consent.md) voor stapsgewijze instructies voor het verlenen van toestemming voor de beheerder voor de hele Tenant via de Azure Portal, met behulp van Azure AD Power shell of via de toestemming prompt zelf.

### <a name="granting-consent-on-behalf-of-a-specific-user"></a>Toestemming verlenen namens een specifieke gebruiker
In plaats van toestemming te verlenen voor de hele organisatie, kan een beheerder ook de [Microsoft Graph-API](/graph/use-the-api) gebruiken om toestemming te geven aan gedelegeerde machtigingen namens één gebruiker. Zie [toegang namens een gebruiker verkrijgen](/graph/auth-v2-user)voor meer informatie.

## <a name="limiting-user-access-to-applications"></a>Gebruikers toegang tot toepassingen beperken
De toegang van gebruikers tot toepassingen kan nog steeds worden beperkt, zelfs wanneer toestemming van de beheerder voor de hele Tenant is verleend. Zie [methoden voor het toewijzen van gebruikers en groepen](./assign-user-or-group-access-portal.md)voor meer informatie over het vereisen van een gebruikers toewijzing aan een toepassing.

Zie [Azure AD gebruiken voor toegang tot toepassingen voor](what-is-access-management.md)meer informatie over hoe u extra complexe scenario's kunt afhandelen.

## <a name="disable-all-future-user-consent-operations-to-any-application"></a>Alle toekomstige bewerkingen van de gebruikers toestemming voor elke toepassing uitschakelen
Door de toestemming van de gebruiker voor uw hele map uit te scha kelen, voor komt u dat eind gebruikers aan elke toepassing worden doorgestuurd. Beheerders kunnen namens de gebruiker nog steeds toestemming geven. Meer informatie over de toestemming van de toepassing en waarom u dit mogelijk niet wilt doen, kunt u lezen [over de toestemming van gebruikers en beheerders](../develop/howto-convert-app-to-be-multi-tenant.md).

Voer de volgende stappen uit om alle toekomstige bewerkingen voor gebruikers toestemming in uw hele directory uit te scha kelen:
1.  Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**
2.  Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.
3.  Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.
4.  Selecteer **gebruikers en groepen** in het navigatie menu.
5.  Selecteer **Gebruikersinstellingen**.
6.  Schakel alle toekomstige acties voor gebruikers toestemming uit door de gebruikers in staat te stellen **apps toegang te geven tot hun gegevens** in-of uit te scha **kelen en op** de knop **Opslaan** te klikken.

## <a name="next-steps"></a>Volgende stappen
* [Vijf stappen voor het beveiligen van uw identiteits infrastructuur](../../security/fundamentals/steps-secure-identity.md#before-you-begin-protect-privileged-accounts-with-mfa)
* [De beheerder toestemming werk stroom configureren](configure-admin-consent-workflow.md)
* [Configureren hoe eindgebruikers toestemming geven voor toepassingen](configure-user-consent.md)
* [Machtigingen en toestemming in het micro soft Identity-platform](../develop/v2-permissions-and-consent.md)
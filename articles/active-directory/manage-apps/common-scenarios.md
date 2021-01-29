---
title: Algemene scenario's voor toepassingsbeheer voor Azure Active Directory | Microsoft Docs
description: Toepassingsbeheer centraliseren met Microsoft Azure AD
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/02/2019
ms.author: kenwith
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 882770fefbb48524ad5f11a56089132b319470aa
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99053343"
---
# <a name="centralize-application-management-with-azure-ad"></a>Toepassingsbeheer centraliseren met Microsoft Azure AD

Wachtwoorden, zowel een nachtmerrie voor IT als een gedoe voor werknemers over de hele wereld. Daarom maken steeds meer bedrijven gebruik van Azure Active Directory, de oplossing voor identiteits- en toegangsbeheer van Microsoft voor de cloud en al uw andere resources. Schakel tussen toepassingen zonder dat u voor elke toepassing een wachtwoord hoeft in te voeren. Ga van Outlook naar Workday, naar ADP, zo snel als u ze kunt openen, snel en veilig. Vervolgens kunt u met partners en zelfs anderen buiten uw organisatie samenwerken, zonder dat u een beroep hoeft te doen op IT. Bovendien helpt Microsoft Azure AD risico's te beheren door de apps die u gebruikt te beveiligen met zaken als meervoudige verificatie om te controleren wie u bent, continue adaptieve machine learning en beveiligingsinformatie te gebruiken om verdachte aanmeldingen te detecteren, wat u veilige toegang biedt tot de apps die u nodig hebt, waar u ook bent. Het is niet alleen geweldig voor gebruikers, maar ook voor IT. Met Just-in-Time-toegangsbeoordelingen en een omvangrijk governancepakket helpt Microsoft Azure AD u bovendien met de naleving en het afdwingen van beleid. U kunt zelfs de inrichting van gebruikersaccounts automatiseren, zodat toegangsbeheer een peulenschil is. Bekijk enkele algemene scenario's waarvoor klanten de mogelijkheden voor toepassingsbeheer van Azure Active Directory gebruiken.

**Algemene scenario's**


> [!div class="checklist"]
> * Eenmalige aanmelding voor al uw toepassingen
> * Inrichting en het ongedaan maken van de inrichting automatiseren 
> * Uw toepassingen beveiligen
> * Toegang tot uw toepassingen beheren
> * Hybride beveiligde toegang

## <a name="scenario-1-set-up-sso-for-all-your-applications"></a>Scenario 1: Eenmalige aanmelding instellen voor al uw toepassingen

U hoeft geen wachtwoorden meer te beheren. Beveilig de toegang tot alle resources die u nodig hebt met uw bedrijfsreferenties. 

|Functie  | Beschrijving | Aanbeveling |
|---------|---------|---------|
|Eenmalige aanmelding|Op standaarden gebaseerde federatieve SSO met betrouwbare industriële standaarden.|Gebruik altijd [SAML/OIDC](../develop/v2-howto-app-gallery-listing.md) om SSO in te schakelen wanneer uw toepassing dit ondersteunt.|
|Mijn apps|Bied uw gebruikers een eenvoudige hub voor de detectie van en toegang tot al hun toepassingen. Stel hen in staat om productiever te zijn met selfservice mogelijkheden, zoals het aanvragen van toegang tot apps en groepen, of het beheren van toegang tot resources namens anderen.| Implementeer [Mijn apps](my-apps-deployment-plan.md) in uw organisatie wanneer u uw apps hebt geïntegreerd met Azure AD voor SSO.|

## <a name="scenario-2-automate-provisioning-and-deprovisioning"></a>Scenario 2: Inrichting en het ongedaan maken van de inrichting automatiseren 


Voor de meeste toepassingen moet een gebruiker in de toepassing zijn ingericht voordat hij/zij toegang heeft tot de benodigde resources. Het beheer van het gebruik van CSV-bestanden of complexe scripts kan kostbaar en moeilijk zijn. Bovendien moeten klanten ervoor zorgen dat accounts worden verwijderd wanneer iemand geen toegang meer mag hebben. Gebruik onderstaande hulpprogramma's om de inrichting en het ongedaan maken van een inrichting te automatiseren. 


|Functie  |Beschrijving|Aanbeveling |
|---------|---------|---------|
|SCIM-inrichting|[SCIM](https://aka.ms/SCIMOverview) is een industriële best practice voor het automatiseren van de gebruikersinrichting. Elke toepassing die compatibel is met SCIM, kan worden geïntegreerd met Azure AD. U kunt automatisch gebruikersaccounts maken, bijwerken en verwijderen zonder CSV-bestanden, aangepaste scripts of on-premises oplossingen te moeten onderhouden.|Bekijk de groeiende lijst met [vooraf geïntegreerde](../saas-apps/tutorial-list.md) apps in de galerie met Azure AD-apps|
|Microsoft Graph|Maak gebruik van het volle potentieel van de gegevens waarmee Azure AD uw toepassing kan verrijken met de gegevens die de toepassing nodig heeft.|Maak gebruik van [Microsoft Graph](https://developer.microsoft.com/graph/) om gegevens op te halen uit het Microsoft-ecosysteem. |


## <a name="scenario-3-secure-your-applications"></a>Scenario 3: Uw toepassingen beveiligen
Identiteit is de spil voor beveiliging. Als een identiteit wordt gecompromitteerd, is het ongelooflijk moeilijk om het domino-effect te stoppen voordat het te laat is. Het duurt gemiddeld ruim 100 dagen voordat organisaties ontdekken dat er een inbreuk is geweest. Gebruik de hulpprogramma's van Azure AD om het beveiligingspostuur van uw toepassingen te verbeteren. 

|Functie  |Beschrijving| Aanbeveling |
|---------|---------| ---------|
|Azure AD MFA|Azure AD MFA (Multi-Factor Authentication) is een Microsoft-oplossing voor verificatie in twee stappen. Met behulp van door een beheerder goedgekeurde verificatiemethoden, helpt Azure AD MFA met het beveiligen van de toegang tot uw gegevens en toepassingen, terwijl tegelijkertijd aan de behoefte aan een eenvoudige aanmeldingsprocedure wordt voldaan.| [Schakel MFA](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124) in voor uw gebruikers.  |
|Voorwaardelijke toegang|Met voorwaardelijke toegang kunt u geautomatiseerde beslissingen voor toegangsbeheer implementeren voor wie toegang heeft tot uw cloud-apps op basis van voorwaarden.| Bekijk de [standaardinstellingen voor beveiliging](../fundamentals/concept-fundamentals-security-defaults.md) en de [algemene beleidsinstellingen](../conditional-access/concept-conditional-access-policy-common.md) die klanten gebruiken. | 
|Identiteitsbeveiliging|Identity Protection maakt gebruik van de informatie die Microsoft heeft verkregen dankzij zijn positie in organisaties met Azure AD, in de consumentenwereld met Microsoft-accounts en in gaming met Xbox om uw gebruikers te beschermen. Microsoft analyseert 6.500.000.000.000 signalen per dag om bedreigingen te identificeren en klanten ertegen te beveiligen.|Schakel het [standaardbeleid voor identiteitsbeveiliging](../identity-protection/concept-identity-protection-policies.md) in dat door onze service wordt geleverd. | 

## <a name="scenario-4-govern-access-to-your-applications"></a>Scenario 4: Toegang tot uw toepassingen beheren
Met Identity Governance kunnen organisaties een balans vinden tussen productiviteit: hoe snel hebben personen toegang tot de toepassingen die ze nodig hebben, bijvoorbeeld wanneer ze deel gaan uitmaken van mijn organisatie? En veiligheid: hoe moet de toegang in de loop van de tijd worden aangepast, bijvoorbeeld als gevolg van wijzigingen in de werknemersstatus van die persoon? 

|Functie  |Beschrijving|Aanbeveling |
|---------|---------| ---------|
|ELM|Rechtenbeheer van Azure AD kan gebruikers binnen en buiten uw organisatie helpen de toegang tot hun toepassingen op efficiëntere wijze te beheren.| Sta niet-beheerders toe om de toegang tot hun toepassingen te beheren met [toegangspakketten](../governance/entitlement-management-access-package-first.md).|
|Toegangsbeoordelingen|De toegang van gebruikers tot apps kan regelmatig worden herzien om te controleren of nog steeds enkel de juiste mensen toegang hebben.| [Beoordeel de toegang](../governance/access-reviews-overview.md) tot uw meest gevoelige toepassingen. |
|Log Analytics|Genereer rapporten over wie toegang heeft tot welke toepassingen en sla ze op in het SIEM-hulpprogramma van uw voorkeur, om gegevens tussen gegevensbronnen en in de loop van de tijd naast elkaar te leggen.| Schakel [logboekanalyse](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md) in en stel waarschuwingen in voor kritieke gebeurtenissen met betrekking tot uw toepassingen. |


## <a name="scenario-5-hybrid-secure-access"></a>Scenario 5: Hybride beveiligde toegang
Identiteit kan alleen uw besturingsvlak zijn als deze kan worden gekoppeld aan alle cloud- en on-premises toepassingen. Maak gebruik van de hulpprogramma's van Azure AD en bijbehorende partners om toegang tot op verouderde verificatie gebaseerde toepassingen te beveiligen.

|Functie  |Beschrijving|Aanbeveling |
|---------|---------|---------|
|Toepassingsproxy|De huidige werknemer wil overal, op elke plek en op elk apparaat productief kunnen zijn. Hij of zij moet toegang hebben tot SaaS-apps in de cloud en zakelijke apps on-premises. Azure AD-toepassingsproxy maakt deze robuuste toegang mogelijk zonder kostbare en complexe VPN's (virtuele particuliere netwerken) of DMZ's (gedemilitariseerde zones).|Stel [externe toegang](./application-proxy.md) in voor uw on-premises apps. |
|F5, Akamai, Zscaler|Met uw bestaande netwerk- en bezorgingscontroller kunt u eenvoudig verouderde toepassingen beveiligen die nog essentieel zijn voor uw bedrijfsprocessen, maar die u niet kon beveiligen voordat u met Azure AD werkte. Waarschijnlijk hebt u al alles wat u nodig hebt om deze toepassingen te beveiligen.| Gebruikt u Akamai, Citrix, F5 of Zscaler? Bekijk onze [vooraf samengestelde oplossingen](./secure-hybrid-access.md). | 

## <a name="related-articles"></a>Verwante artikelen:

- [Toepassingsbeheer](./index.yml)
- [Toepassingsinrichting](../app-provisioning/user-provisioning.md)
- [Hybride beveiligde toegang](./secure-hybrid-access.md)
- [Identiteitsbeheer](../governance/identity-governance-overview.md)
- [Microsoft-identiteitsplatform](../develop/v2-overview.md)
- [Identiteitsbeveiliging](../conditional-access/index.yml)
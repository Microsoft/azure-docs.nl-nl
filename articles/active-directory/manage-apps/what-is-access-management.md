---
title: Toegang tot apps beheren met Azure AD
description: Hierin wordt beschreven hoe u met beAzure Active Directory organisaties de apps kunt opgeven waarvoor elke gebruiker toegang heeft.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/16/2017
ms.author: kenwith
ms.openlocfilehash: 5afc6aa8f52011eba6d7cfdfaa09b0ab995183e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99257420"
---
# <a name="managing-access-to-apps"></a>Toegang tot apps beheren

Doorlopend toegangs beheer, evaluatie van het gebruik en rapportage blijven een uitdaging nadat een app is geïntegreerd in het identiteits systeem van uw organisatie. In veel gevallen moeten IT-beheerders of Help Desk een voortdurende actieve rol nemen bij het beheren van de toegang tot uw apps. Soms wordt de toewijzing uitgevoerd door een algemeen of divisie IT-team. De toewijzings beslissing is vaak bedoeld om te worden gedelegeerd aan de bedrijfs beslissings Maker, waardoor de goed keuring wordt vereist voordat de toewijzing wordt gemaakt.  Andere organisaties investeren in integratie met een bestaand geautomatiseerd systeem voor identiteits-en toegangs beheer, zoals Role-Based Access Control (RBAC) of Attribute-Based Access Control (ABAC). Zowel de integratie-als regel ontwikkeling is vaak gespecialiseerd en kostbaar. Bewaking of rapportage over beide beheer methoden is een eigen afzonderlijke, dure en complexe investering.

## <a name="how-does-azure-active-directory-help"></a>Hoe helpt Azure Active Directory?

Azure AD biedt ondersteuning voor uitgebreid toegangs beheer voor geconfigureerde toepassingen, waardoor organisaties eenvoudig het juiste toegangs beleid kunnen bereiken, variërend van automatische, toewijzing op basis van kenmerken (ABAC of RBAC-scenario's) via overdracht en beheerder beheer. Met Azure AD kunt u eenvoudig complexe beleids regels bereiken, meerdere beheer modellen combi neren voor één toepassing en beheer regels zelfs opnieuw gebruiken voor verschillende toepassingen met dezelfde doel groepen.

Met Azure AD is het rapporteren van gebruik en toewijzing volledig geïntegreerd, waardoor beheerders eenvoudig kunnen rapporteren over de toewijzings status, toewijzings fouten en zelfs gebruik.

### <a name="assigning-users-and-groups-to-an-app"></a>Gebruikers en groepen toewijzen aan een app

De toepassings toewijzing van Azure AD richt zich op twee primaire toewijzings modi:

* **Afzonderlijke toewijzing** Een IT-beheerder met globale mappen beheerder machtigingen kan afzonderlijke gebruikers accounts selecteren en toegang verlenen tot de toepassing.

* **Toewijzing op basis van een groep (vereist Azure AD Premium P1 of P2)** Een IT-beheerder met de machtigingen van de globale Directory beheerder kan een groep toewijzen aan de toepassing. De toegang van specifieke gebruikers wordt bepaald door het feit of ze lid zijn van de groep op het moment dat ze toegang proberen te krijgen tot de toepassing. Met andere woorden, een beheerder kan effectief een toewijzings regel maken met de tekst ' elk huidig lid van de toegewezen groep heeft toegang tot de toepassing '. Met deze toewijzings optie kunnen beheerders profiteren van de opties voor Azure AD-groeps beheer, waaronder [dynamische groepen op basis van kenmerken](../fundamentals/active-directory-groups-create-azure-portal.md), externe systeem groepen (bijvoorbeeld on-premises Active Directory of workday), of door de beheerder beheerde of selfservice beheerde groepen. Eén groep kan eenvoudig worden toegewezen aan meerdere apps, zodat toepassingen met toewijzings affiniteit toewijzings regels kunnen delen, waardoor de algehele beheer complexiteit wordt verminderd. Houd er rekening mee dat geneste groepslid maatschappen op dit moment niet worden ondersteund voor toewijzing op basis van een groep.

Met behulp van deze twee toewijzings modi kunnen beheerders een wenselijk toewijzings beheer methode bereiken.

### <a name="requiring-user-assignment-for-an-app"></a>Gebruikers toewijzing voor een app vereisen

Met bepaalde typen toepassingen hebt u de mogelijkheid [om gebruikers toe te staan om te worden toegewezen aan de toepassing](assign-user-or-group-access-portal.md#configure-an-application-to-require-user-assignment). Op die manier voor komt u dat iedereen zich kan aanmelden, behalve de gebruikers die u expliciet aan de toepassing toewijst. De volgende typen toepassingen ondersteunen deze optie:

* Toepassingen die zijn geconfigureerd voor federatieve eenmalige aanmelding (SSO) met verificatie op basis van SAML
* Toepassings proxy toepassingen die gebruikmaken van Azure Active Directory verificatie vooraf
* Toepassingen die zijn gebouwd op het Azure AD-toepassings platform die gebruikmaken van OAuth 2,0/OpenID Connect Connect-verificatie nadat een gebruiker of beheerder heeft ingestemd met die toepassing. Bepaalde zakelijke toepassingen bieden extra controle over wie zich mag aanmelden.

Wanneer de gebruikers toewijzing *niet is vereist*, worden niet-toegewezen gebruikers de app niet weer geven voor hun apps, maar kunnen ze zich wel aanmelden bij de toepassing zelf (ook wel met SP-started sign-on) of ze kunnen de **gebruikers toegangs-URL** gebruiken op de **Eigenschappen** pagina van de toepassing (ook wel bekend als IDP-started sign on).

Voor sommige toepassingen is de optie voor het vereisen van gebruikers toewijzing niet beschikbaar in de eigenschappen van de toepassing. In dergelijke gevallen kunt u Power shell gebruiken om de eigenschap appRoleAssignmentRequired in te stellen voor de Service-Principal.

### <a name="determining-the-user-experience-for-accessing-apps"></a>De gebruikers ervaring voor het openen van apps bepalen

Azure AD biedt [verschillende aanpas bare manieren om toepassingen te implementeren](end-user-experiences.md) voor eind gebruikers in uw organisatie:

* Mijn apps in azure AD
* Start programma voor toepassing Microsoft 365
* Directe aanmelding bij federatieve apps (Service-PR)
* Dieptekoppelingen naar federatieve apps, op basis van wachtwoorden, of bestaande apps

U kunt bepalen of gebruikers die zijn toegewezen aan een zakelijke app deze kunnen zien in mijn apps en Microsoft 365 start programma voor toepassingen.

## <a name="example-complex-application-assignment-with-azure-ad"></a>Voor beeld: complexe toepassings toewijzing met Azure AD
Overweeg een toepassing zoals Sales Force. In veel organisaties wordt Sales Force voornamelijk gebruikt door de marketing-en verkoop teams. Vaak hebben leden van het marketing team zeer uitgebreide toegang tot Sales Force, terwijl leden van het verkoop team beperkte toegang hebben. In veel gevallen heeft een brede populatie aan informatie medewerkers de toegang tot de toepassing beperkt. Uitzonde ringen op deze regels zijn belang rijk voor het bemoeilijken. Het is vaak de prerogative van de marketing-of verkoop leiders om een gebruiker toegang te verlenen of hun rollen onafhankelijk van deze algemene regels te wijzigen.

Met Azure AD kunnen toepassingen zoals Sales Force vooraf worden geconfigureerd voor eenmalige aanmelding (SSO) en geautomatiseerde inrichting. Zodra de toepassing is geconfigureerd, kan een beheerder de eenmalige actie ondernemen om de juiste groepen te maken en toe te wijzen. In dit voor beeld kan een beheerder de volgende toewijzingen uitvoeren:

* [Dynamische groepen](../fundamentals/active-directory-groups-create-azure-portal.md) kunnen worden gedefinieerd om automatisch alle leden van de marketing-en verkoop teams weer te geven met behulp van kenmerken als afdeling of rol:
  
  * Alle leden van de marketing groepen worden toegewezen aan de rol ' Marketing ' in Sales Force
  * Alle leden van de verkoop team groepen worden toegewezen aan de rol ' Sales ' in Sales Force. Een verdere verfijning kan gebruikmaken van meerdere groepen die regionale verkoop teams vertegenwoordigen die aan verschillende Sales Force-rollen zijn toegewezen.

* Om het uitzonderings mechanisme in te scha kelen, kan voor elke rol een self-service groep worden gemaakt. Bijvoorbeeld, de groep ' Sales Force marketing Exception ' kan worden gemaakt als een self-service groep. De groep kan worden toegewezen aan de marketing functie van Sales Force en het marketing leiderschaps team kan eigenaar zijn. Leden van het marketing leiderschaps team kunnen gebruikers toevoegen of verwijderen, een lidmaatschaps beleid instellen of zelfs aanvragen voor individuele gebruikers goed keuren of weigeren om lid te worden. Dit mechanisme wordt ondersteund door een informatie medewerker die de juiste ervaring heeft die geen gespecialiseerde training vereist voor eigen aren of leden.

In dit geval worden alle toegewezen gebruikers automatisch ingericht voor Sales Force, omdat ze worden toegevoegd aan verschillende groepen hun roltoewijzing wordt bijgewerkt in Sales Force. Gebruikers kunnen Sales Force ontdekken en openen via mijn apps, Office-webclients of zelfs door te navigeren naar de aanmeldings pagina van de organisatie Sales Force. Beheerders kunnen de gebruiks-en toewijzings status eenvoudig weer geven met Azure AD-rapportage.

Beheerders kunnen [voorwaardelijke toegang tot Azure AD](../conditional-access/concept-conditional-access-users-groups.md) gebruiken om toegangs beleid in te stellen voor specifieke rollen. Deze beleids regels kunnen omvatten of toegang is toegestaan buiten de bedrijfs omgeving en zelfs Multi-Factor Authentication-of apparaats vereisten om in verschillende gevallen toegang te verkrijgen.

## <a name="access-to-microsoft-applications"></a>Toegang tot micro soft-toepassingen

Micro soft-toepassingen (zoals Exchange, share point, Yammer, enz.) zijn toegewezen en worden beheerd op een andere manier dan SaaS-toepassingen van derden of andere toepassingen die u met Azure AD integreert voor eenmalige aanmelding.

Er zijn drie belang rijke manieren waarop een gebruiker toegang kan krijgen tot een door micro soft gepubliceerde toepassing.

- Voor toepassingen in de Microsoft 365 of andere betaalde suites, krijgen gebruikers toegang via **licentie toewijzing** rechtstreeks aan hun gebruikers account, of via een groep via onze toewijzing van licentie toewijzingen op basis van groepen.
- Voor toepassingen die door micro soft of een derde partij vrij worden gepubliceerd voor iedereen, kunnen gebruikers toegang krijgen via [toestemming](configure-user-consent.md)van de gebruiker. Dit betekent dat ze zich bij de toepassing aanmelden met hun Azure AD-werk-of school account en toegang hebben tot een beperkt aantal gegevens op hun account.
- Voor toepassingen die door micro soft of een derde partij vrij worden gepubliceerd voor iedereen, kunnen gebruikers ook toegang krijgen via [toestemming](manage-consent-requests.md)van de beheerder. Dit betekent dat een beheerder heeft vastgesteld dat de toepassing door iedereen in de organisatie kan worden gebruikt, zodat deze zich bij de toepassing aanmeldt met een account van de globale beheerder en toegang verleent aan iedereen in de organisatie.

Sommige toepassingen combi neren deze methoden. Bepaalde micro soft-toepassingen maken bijvoorbeeld deel uit van een Microsoft 365-abonnement, maar vereisen nog wel toestemming.

Gebruikers hebben toegang tot Microsoft 365 toepassingen via hun Office 365-portals. U kunt ook Microsoft 365-toepassingen weer geven of verbergen in mijn apps met de [Office 365-zicht baarheid](hide-application-from-user-portal.md) in de **gebruikers instellingen** van uw Directory. 

Net als bij zakelijke apps kunt u [gebruikers toewijzen](assign-user-or-group-access-portal.md) aan bepaalde micro soft-toepassingen via de Azure portal of, als de portal optie niet beschikbaar is, met behulp van Power shell.

## <a name="next-steps"></a>Volgende stappen
* [Apps beveiligen met voorwaardelijke toegang](../conditional-access/concept-conditional-access-cloud-apps.md)
* [Self-service voor groeps beheer/SSAA](../enterprise-users/groups-self-service-management.md)

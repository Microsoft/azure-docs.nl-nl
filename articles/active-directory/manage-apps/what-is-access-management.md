---
title: Toegang tot apps beheren met Azure AD
description: Beschrijft hoe Azure Active Directory organisaties de apps kunnen opgeven waarvoor elke gebruiker toegang heeft.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/16/2017
ms.author: iangithinji
ms.openlocfilehash: 6a1ae7dd1da2c7666c007194bf22bd980e41dc22
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376372"
---
# <a name="managing-access-to-apps"></a>Toegang tot apps beheren

Doorlopend toegangsbeheer, gebruiksevaluatie en rapportage blijven een uitdaging nadat een app is geïntegreerd in het identiteitssysteem van uw organisatie. In veel gevallen moeten IT-beheerders of de helpdesk een actieve rol spelen bij het beheren van de toegang tot uw apps. Soms wordt de toewijzing uitgevoerd door een algemeen of afdelings IT-team. De toewijzingsbeslissing is vaak bedoeld om te worden gedelegeerd aan de zakelijke besluiter, waarvoor goedkeuring is vereist voordat IT de toewijzing maakt.  Andere organisaties investeren in integratie met een bestaand geautomatiseerd systeem voor identiteits- en toegangsbeheer, zoals Role-Based Access Control (RBAC) of Attribute-Based Access Control (ABAC). Zowel de integratie als de ontwikkeling van regels zijn vaak gespecialiseerd en duur. Bewaking of rapportage over beide beheerbenaderingen is een eigen afzonderlijke, kostbare en complexe investering.

## <a name="how-does-azure-active-directory-help"></a>Hoe kan Azure Active Directory helpen?

Azure AD biedt ondersteuning voor uitgebreid toegangsbeheer voor geconfigureerde toepassingen, waardoor organisaties eenvoudig het juiste toegangsbeleid kunnen bereiken, variërend van automatische toewijzing op basis van kenmerken (ABAC- of RBAC-scenario's) tot delegering en inclusief beheerdersbeheer. Met Azure AD kunt u eenvoudig complexe beleidsregels bereiken door meerdere beheermodellen voor één toepassing te combineren en kunt u zelfs beheerregels hergebruiken voor toepassingen met dezelfde doelgroepen.

Met Azure AD is rapportage over gebruik en toewijzing volledig geïntegreerd, waardoor beheerders eenvoudig kunnen rapporteren over toewijzingstoestand, toewijzingsfouten en zelfs gebruik.

### <a name="assigning-users-and-groups-to-an-app"></a>Gebruikers en groepen toewijzen aan een app

De toepassingstoewijzing van Azure AD is gericht op twee primaire toewijzingsmodi:

* **Afzonderlijke toewijzing** Een IT-beheerder met globale beheerdersmachtigingen voor de directory kan afzonderlijke gebruikersaccounts selecteren en toegang verlenen tot de toepassing.

* **Toewijzing op basis van groepen (vereist Azure AD Premium P1 of P2)** Een IT-beheerder met globale beheerdersmachtigingen voor directory's kan een groep toewijzen aan de toepassing. De toegang van specifieke gebruikers wordt bepaald door of ze lid zijn van de groep op het moment dat ze toegang proberen te krijgen tot de toepassing. Met andere woorden, een beheerder kan effectief een toewijzingsregel maken met de mededeling dat elk huidig lid van de toegewezen groep toegang heeft tot de toepassing. Met deze toewijzingsoptie kunnen beheerders profiteren van elk van de opties voor Groepsbeheer van Azure AD, waaronder dynamische groepen op basis van [kenmerken,](../fundamentals/active-directory-groups-create-azure-portal.md)externe systeemgroepen (bijvoorbeeld on-premises Active Directory of Workday) of door een beheerder beheerde of selfservice beheerde groepen. Eén groep kan eenvoudig worden toegewezen aan meerdere apps, zodat toepassingen met toewijzings-affiniteit toewijzingsregels kunnen delen, waardoor de algehele beheercomplexiteit wordt verkleind. Houd er rekening mee dat geneste groepslidmaatschap op dit moment niet wordt ondersteund voor toewijzing op basis van groepen aan toepassingen.

Met behulp van deze twee toewijzingsmodi kunnen beheerders elke gewenste benadering voor toewijzingsbeheer bereiken.

### <a name="requiring-user-assignment-for-an-app"></a>Gebruikerstoewijzing vereisen voor een app

Met bepaalde typen toepassingen kunt u vereisen dat gebruikers [worden toegewezen aan de toepassing](assign-user-or-group-access-portal.md#configure-an-application-to-require-user-assignment). Zo voorkomt u dat iedereen zich aanmeldt, behalve de gebruikers die u expliciet aan de toepassing toewijst. De volgende typen toepassingen ondersteunen deze optie:

* Toepassingen die zijn geconfigureerd voor federatief eenmalige aanmelding (SSO) met verificatie op basis van SAML
* toepassingsproxy toepassingen die gebruikmaken van Azure Active Directory verificatie vooraf
* Toepassingen die zijn gebouwd op het Azure AD-toepassingsplatform en die gebruikmaken van OAuth 2.0/OpenID Connect-verificatie nadat een gebruiker of beheerder toestemming heeft gegeven voor die toepassing. Bepaalde bedrijfstoepassingen bieden extra controle over wie zich mag aanmelden.

Wanneer gebruikerstoewijzing niet vereist *is,* zien niet-toegewezen gebruikers de app niet op hun Mijn apps, maar kunnen ze zich wel aanmelden bij de toepassing zelf (ook  wel bekend als door SP geïnitieerde aanmelding) of kunnen ze de **URL** voor gebruikerstoegang gebruiken op de pagina Eigenschappen van de toepassing (ook wel bekend als door IDP geïnitieerde aanmelding).

Voor sommige toepassingen is de optie om gebruikerstoewijzing te vereisen niet beschikbaar in de eigenschappen van de toepassing. In dergelijke gevallen kunt u PowerShell gebruiken om de eigenschap appRoleAssignmentRequired in te stellen op de service-principal.

### <a name="determining-the-user-experience-for-accessing-apps"></a>De gebruikerservaring voor toegang tot apps bepalen

Azure AD biedt [verschillende aanpasbare manieren om toepassingen te implementeren](end-user-experiences.md) voor eindgebruikers in uw organisatie:

* Azure AD-Mijn apps
* Microsoft 365 starten van toepassingen
* Directe aanmelding bij federatief apps (service-pr)
* Dieptekoppelingen naar federatieve apps, op basis van wachtwoorden, of bestaande apps

U kunt bepalen of gebruikers die zijn toegewezen aan een bedrijfs-app deze kunnen zien in Mijn apps en Microsoft 365 starten van toepassingen.

## <a name="example-complex-application-assignment-with-azure-ad"></a>Voorbeeld: Complexe toepassingstoewijzing met Azure AD
Overweeg een toepassing zoals Salesforce. In veel organisaties wordt Salesforce voornamelijk gebruikt door de marketing- en verkoopteams. Vaak hebben leden van het marketingteam zeer bevoegde toegang tot Salesforce, terwijl leden van het verkoopteam beperkte toegang hebben. In veel gevallen heeft een brede populatie informatiemedewerkers beperkte toegang tot de toepassing. Uitzonderingen op deze regels bemoeilijken de zaken. Het is vaak het prerogatief van de marketing- of verkoopleidingsteams om een gebruiker toegang te verlenen of hun rollen onafhankelijk van deze algemene regels te wijzigen.

Met Azure AD kunnen toepassingen zoals Salesforce vooraf worden geconfigureerd voor eenmalige aanmelding (SSO) en geautomatiseerde inrichting. Zodra de toepassing is geconfigureerd, kan een beheerder de een time-action uitvoeren om de juiste groepen te maken en toe te wijzen. In dit voorbeeld kan een beheerder de volgende toewijzingen uitvoeren:

* [Dynamische groepen](../fundamentals/active-directory-groups-create-azure-portal.md) kunnen worden gedefinieerd om alle leden van de marketing- en verkoopteams automatisch te vertegenwoordigen met kenmerken zoals afdeling of rol:
  
  * Alle leden van marketinggroepen worden toegewezen aan de rol 'marketing' in Salesforce
  * Alle leden van verkoopteamgroepen worden toegewezen aan de rol 'verkoop' in Salesforce. Een verdere verfijning kan meerdere groepen gebruiken die regionale verkoopteams vertegenwoordigen die zijn toegewezen aan verschillende Salesforce-rollen.

* Als u het uitzonderingsmechanisme wilt inschakelen, kan er voor elke rol een selfservicegroep worden gemaakt. De groep 'Salesforce-marketing-uitzondering' kan bijvoorbeeld worden gemaakt als een selfservicegroep. De groep kan worden toegewezen aan de Salesforce-marketingrol en het marketingmanagerteam kan eigenaar worden. Leden van het marketingteam kunnen gebruikers toevoegen of verwijderen, een join-beleid instellen of zelfs aanvragen van afzonderlijke gebruikers goedkeuren of weigeren om lid te worden. Dit mechanisme wordt ondersteund door een ervaring die geschikt is voor informatiewerker en waarvoor geen speciale training voor eigenaren of leden is vereist.

In dit geval worden alle toegewezen gebruikers automatisch ingericht voor Salesforce, omdat ze worden toegevoegd aan verschillende groepen, wordt hun roltoewijzing bijgewerkt in Salesforce. Gebruikers kunnen Salesforce ontdekken en openen via Mijn apps, Office-web-clients of zelfs door te navigeren naar de aanmeldingspagina van Salesforce van hun organisatie. Beheerders kunnen eenvoudig de gebruiks- en toewijzingsstatus bekijken met behulp van Azure AD-rapportage.

Beheerders kunnen voorwaardelijke [toegang van Azure AD gebruiken om](../conditional-access/concept-conditional-access-users-groups.md) toegangsbeleid in te stellen voor specifieke rollen. Deze beleidsregels kunnen omvatten of toegang is toegestaan buiten de bedrijfsomgeving en zelfs Multi-Factor Authentication of apparaatvereisten om in verschillende gevallen toegang te krijgen.

## <a name="access-to-microsoft-applications"></a>Toegang tot Microsoft-toepassingen

Microsoft-toepassingen (zoals Exchange, SharePoint, Yammer, enzovoort) worden iets anders toegewezen en beheerd dan SaaS-toepassingen van derden of andere toepassingen die u integreert met Azure AD voor een enkele aanmelding.

Er zijn drie belangrijke manieren waarop een gebruiker toegang kan krijgen tot een door Microsoft gepubliceerde toepassing.

- Voor toepassingen in de Microsoft 365 of andere betaalde suites  krijgen gebruikers toegang via licentietoewijzing, rechtstreeks aan hun gebruikersaccount of via een groep met behulp van onze groepslicentietoewijzingsmogelijkheid.
- Voor toepassingen die Microsoft of een externe partij vrijelijk publiceert voor iedereen om te gebruiken, kunnen gebruikers toegang krijgen via [toestemming van de gebruiker.](configure-user-consent.md) Dit betekent dat ze zich aanmelden bij de toepassing met hun Azure AD-werk- of schoolaccount en dat ze toegang hebben tot een beperkte set gegevens in hun account.
- Voor toepassingen die Microsoft of een externe partij vrijelijk publiceert voor iedereen om te gebruiken, kunnen gebruikers ook toegang krijgen via [beheerders toestemming.](manage-consent-requests.md) Dit betekent dat een beheerder heeft vastgesteld dat de toepassing kan worden gebruikt door iedereen in de organisatie, zodat ze zich aanmelden bij de toepassing met een globale beheerdersaccount en toegang verlenen aan iedereen in de organisatie.

Sommige toepassingen combineren deze methoden. Bepaalde Microsoft-toepassingen maken bijvoorbeeld deel uit van een Microsoft 365 abonnement, maar hebben nog steeds toestemming nodig.

Gebruikers hebben toegang Microsoft 365 toepassingen via hun Office 365-portals. U kunt ook de Microsoft 365 weergeven of verbergen in de Mijn apps met de schakelknop Zichtbaarheid van [Office 365](hide-application-from-user-portal.md) in de gebruikersinstellingen van **uw directory.** 

Net als bij zakelijke [](assign-user-or-group-access-portal.md) apps kunt u gebruikers toewijzen aan bepaalde Microsoft-toepassingen via de Azure Portal of, als de portaloptie niet beschikbaar is, met behulp van PowerShell.

## <a name="next-steps"></a>Volgende stappen
* [Apps beveiligen met voorwaardelijke toegang](../conditional-access/concept-conditional-access-cloud-apps.md)
* [Selfservice voor groepsbeheer/SSAA](../enterprise-users/groups-self-service-management.md)

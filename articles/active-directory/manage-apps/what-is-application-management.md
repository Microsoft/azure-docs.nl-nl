---
title: Wat is toepassingsbeheer in Azure Active Directory?
description: Een overzicht van het gebruik van Azure Active Directory (AD) als een IAM-systeem (identiteits- en toegangsbeheer) voor uw cloud- en on-premises toepassingen.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 01/22/2021
ms.author: iangithinji
ms.reviewer: ''
ms.openlocfilehash: 8bdb8eb9cf176f8e190769e38c81d02ad2985196
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376389"
---
# <a name="what-is-application-management"></a>Wat is toepassingsbeheer?

Azure AD is een IAM-systeem (identiteits- en toegangsbeheer). Het biedt één plaats voor het opslaan van informatie over digitale identiteiten. U kunt uw softwaretoepassingen configureren voor het gebruik van Azure AD als de locatie waar de gebruikersgegevens worden opgeslagen. 

Azure AD moet worden geconfigureerd om te integreren met een toepassing. Met andere woorden, het moet weten welke apps het gebruiken voor identiteiten. Azure AD bewust maken van deze apps en hoe deze moeten worden verwerkt, wordt toepassingsbeheer genoemd.

U beheert toepassingen op de **pagina Bedrijfstoepassingen** in de sectie Beheren van de Azure Active Directory portal.

![De optie Ondernemingstoepassingen onder het gedeelte Beheren van de Azure AD-portal.](media/what-is-application-management/enterprise-applications-in-nav.png)

## <a name="what-is-an-identity-and-access-management-iam-system"></a>Wat is een IAM-systeem (identiteits- en toegangsbeheer)?
Een toepassing is software die voor een bepaald doel wordt gebruikt. Voor de meeste apps moeten gebruikers zich aanmelden.

Een gecentraliseerd identiteitssysteem biedt één plek voor het opslaan van gebruikersgegevens die vervolgens door alle toepassingen kunnen worden gebruikt. Deze systemen worden aangeduid als identiteits-en toegangsbeheer(IAM)-systemen. Azure Active Directory is het IAM-systeem voor de Microsoft-cloud.

>[!TIP]
>Een IAM-systeem biedt één locatie voor het bijhouden van gebruikersidentiteiten. Azure AD is het IAM-systeem voor de Microsoft-cloud.

## <a name="why-manage-applications-with-a-cloud-solution"></a>Waarom toepassingen beheren met een cloud-oplossing?

Organisaties hebben vaak honderden toepassingen waar gebruikers afhankelijk van zijn voor hun werk. Gebruikers hebben toegang tot deze toepassingen van groot aantal apparaten en -locaties. Er worden steeds nieuwe toepassingen toegevoegd, ontwikkeld en in gebruik nemen. Met zoveel apps en toegangspunten is het belangrijk om een identiteitsoplossing te gebruiken die met alle apps werkt.

>[!TIP]
>De galerie met Azure AD-apps bevat veel populaire toepassingen die al vooraf zijn geconfigureerd voor gebruik met Azure AD als id-provider.

## <a name="how-does-azure-ad-work-with-apps"></a>Hoe werkt Azure AD met apps?

Azure AD bevindt zich in het midden en biedt identiteitsbeheer voor cloud- en on-premises apps. 

![Diagram waarin de apps worden weer gegeven die zijn gefedereerd via Azure AD](media/what-is-application-management/app-management-overview.png)

>[!TIP]
>Beheerkosten verlagen door [het inrichten van](../app-provisioning/user-provisioning.md) gebruikers te automatiseren, zodat gebruikers automatisch worden toegevoegd aan Azure AD wanneer u ze toevoegt aan het HR-systeem van uw bedrijf. 

## <a name="what-types-of-applications-can-i-integrate-with-azure-ad"></a>Welke typen toepassingen kan ik integreren met Azure AD?

U kunt Azure AD gebruiken als uw identiteitssysteem voor ongeveer elke app. Veel apps zijn al vooraf geconfigureerd en kunnen met minimale inspanning worden ingesteld. Deze vooraf geconfigureerde apps worden gepubliceerd in de [Azure AD-app Gallery](/azure/active-directory/saas-apps/). 

U kunt de meeste apps handmatig configureren voor een aanmelding als deze nog niet in de galerie staan. Azure AD biedt verschillende opties voor eenmalige aanmelding. Enkele van de populairste zijn eenmalige aanmelding op basis van SAML en eenmalige aanmelding op basis van OIDC. Zie Opties voor eenmalige aanmelding voor meer informatie over het integreren van apps voor het inschakelen van [eenmalige aanmelding.](sso-options.md) 

Gebruikt uw organisatie on-premises apps? U kunt ze integreren met behulp van app-proxy. Zie Externe toegang bieden tot on-premises toepassingen via de [Azure AD-toepassingsproxy voor meer toepassingsproxy.](application-proxy.md)

>[!TIP]
>Wanneer u uw eigen Line-Of-Business-toepassingen bouwt, kunt u deze integreren met Azure AD ter ondersteuning van een aanmelding. Zie Microsoft Identity Platform voor meer informatie over het ontwikkelen van apps [voor Azure AD.](..//develop/v2-overview.md)

## <a name="manage-risk-with-conditional-access-policies"></a>Risico beheersen met beleid voor voorwaardelijke toegang

Het koppelen van eenmalige aanmelding van Azure AD aan beleid voor [voorwaardelijke toegang](../conditional-access/concept-conditional-access-cloud-apps.md) biedt een hoge mate van beveiliging voor toegang tot toepassingen. Beleid voor voorwaardelijke toegang biedt gedetailleerde controle voor apps op basis van voorwaarden die u in stelt. 

## <a name="improve-productivity-with-single-sign-on"></a>Verhoog de productiviteit met eenmalige aanmelding

Eenmalige aanmelding (SSO) biedt een uniforme gebruikerservaring tussen Microsoft 365 en alle andere apps die u gebruikt. Zeg tot ziens dat u voortdurend uw gebruikersnaam en wachtwoord moet invoeren.

Zie Wat is een een aanmelding? voor meer informatie over [een aanmelding.](what-is-single-sign-on.md)

## <a name="address-governance-and-compliance"></a>Voeg governance en naleving toe

Apps bewaken via rapporten die gebruikmaken van SIEM-hulpprogramma's (Security Incident and Event Monitoring). U kunt de rapporten openen vanuit de portal of via API's. Programmatisch controleren wie toegang heeft tot uw toepassingen en toegang verwijderen van niet-actieve gebruikers via toegangsbeoordelingen.

## <a name="manage-costs"></a>Kosten beheren

Door te migreren naar Azure AD, kunt u kosten besparen en wordt het beheer van on-premises infrastructuur makkelijk. Azure AD biedt ook self-service tot toepassingen, en bespaart daarmee tijd voor beheerders en gebruikers. Eenmalige aanmelding elimineert de noodzaak van toepassingsspecifieke wachtwoorden. De mogelijkheid u slechts eenmaal te hoeven aanmelden, bespaart op kosten in verband met wachtwoordherstel voor toepassingen en wordt productieverlies voorkomen bij het ophalen van wachtwoorden.

Voor Human Resources-gerichte toepassingen of andere toepassingen met een grote set gebruikers kunt u app-inrichting gebruiken om uw leven gemakkelijker te maken. App-inrichting automatiseert het proces van het toevoegen en verwijderen van gebruikers. Zie Wat [is toepassings inrichten? voor meer informatie.](../app-provisioning/user-provisioning.md)

## <a name="next-steps"></a>Volgende stappen

- [Quickstartreeks over toepassingsbeheer](view-applications-portal.md)
- [Aan de slag met toepassingsintegratie](plan-an-application-integration.md)
- [Meer informatie over het automatiseren van inrichting](../app-provisioning/user-provisioning.md)
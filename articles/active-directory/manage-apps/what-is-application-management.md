---
title: Wat is toepassingsbeheer in Azure Active Directory?
description: Een overzicht van het gebruik van Azure Active Directory (AD) als een IAM-systeem (identiteits- en toegangsbeheer) voor uw cloud- en on-premises toepassingen.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 01/22/2021
ms.author: kenwith
ms.reviewer: ''
ms.openlocfilehash: ad572188ceb15a948e4242d0521b8304db45e65b
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98732338"
---
# <a name="what-is-application-management"></a>Wat is toepassingsbeheer?

Azure AD is een IAM-systeem (identiteits- en toegangsbeheer). Het biedt één plaats voor het opslaan van informatie over digitale identiteiten. U kunt uw softwaretoepassingen configureren voor het gebruik van Azure AD als de locatie waar de gebruikersgegevens worden opgeslagen. 

Azure AD moet worden geconfigureerd om te integreren met een toepassing. Met andere woorden, de IT-afdeling moet weten welke apps deze gebruiken voor identiteiten. Als u Azure AD op de hoogte stelt van deze apps, en hoe deze moeten worden verwerkt, wordt toepassings beheer genoemd.

U beheert toepassingen op de pagina **bedrijfs toepassingen** , die zich bevinden in de sectie beheren van de Azure Active Directory Portal.

![De optie Ondernemingstoepassingen onder het gedeelte Beheren van de Azure AD-portal.](media/what-is-application-management/enterprise-applications-in-nav.png)

## <a name="what-is-an-identity-and-access-management-iam-system"></a>Wat is een IAM-systeem (identiteits- en toegangsbeheer)?
Een toepassing is software die voor een bepaald doel wordt gebruikt. Voor de meeste apps moeten gebruikers zich aanmelden.

Een gecentraliseerd identiteits systeem biedt één locatie voor het opslaan van gebruikers informatie die vervolgens door alle toepassingen kan worden gebruikt. Deze systemen worden aangeduid als identiteits-en toegangsbeheer(IAM)-systemen. Azure Active Directory is het IAM-systeem voor de micro soft-Cloud.

>[!TIP]
>Een IAM-systeem biedt één locatie voor het bijhouden van gebruikersidentiteiten. Azure AD is het IAM-systeem voor de Microsoft-cloud.

## <a name="why-manage-applications-with-a-cloud-solution"></a>Waarom toepassingen beheren met een cloud-oplossing?

Organisaties hebben vaak honderden toepassingen waar gebruikers afhankelijk van zijn voor hun werk. Gebruikers hebben toegang tot deze toepassingen van groot aantal apparaten en -locaties. Nieuwe toepassingen worden altijd toegevoegd, ontwikkeld en Zons ondergang. Met zoveel apps en toegangs punten is het belang rijk dat u een identiteits oplossing gebruikt die met hen samenwerkt.

>[!TIP]
>De galerie met Azure AD-apps bevat veel populaire toepassingen die al vooraf zijn geconfigureerd voor gebruik met Azure AD als id-provider.

## <a name="how-does-azure-ad-work-with-apps"></a>Hoe werkt Azure AD met apps?

Azure AD bevindt zich in het midden en biedt identiteits beheer voor Cloud-en on-premises apps. 

![Diagram waarin de apps worden weer gegeven die zijn gefedereerd via Azure AD](media/what-is-application-management/app-management-overview.png)

>[!TIP]
>Verminder de administratieve kosten door [gebruikers inrichting te automatiseren](../app-provisioning/user-provisioning.md) zodat gebruikers automatisch worden toegevoegd aan Azure AD wanneer u deze toevoegt aan het HR-systeem van uw bedrijf. 

## <a name="what-types-of-applications-can-i-integrate-with-azure-ad"></a>Welke typen toepassingen kan ik integreren met Azure AD?

U kunt Azure AD gebruiken als uw identiteits systeem voor vrijwel alle apps. Veel apps zijn al vooraf geconfigureerd en kunnen worden ingesteld met minimale inspanning. Deze vooraf geconfigureerde apps worden gepubliceerd in de [Galerie met Azure AD-App](/azure/active-directory/saas-apps/). 

U kunt de meeste apps voor eenmalige aanmelding hand matig configureren als deze nog niet in de galerie staan. Azure AD biedt verschillende opties voor eenmalige aanmelding. Enkele van de populairste systemen zijn op SAML gebaseerde SSO en OIDC SSO. Zie [Opties voor eenmalige aanmelding](sso-options.md)voor meer informatie over het integreren van apps om SSO in te scha kelen. 

Maakt uw organisatie gebruik van on-premises apps? U kunt deze integreren met behulp van app proxy. Zie [externe toegang bieden tot on-premises toepassingen via de toepassings proxy van Azure AD](application-proxy.md)voor meer informatie.

>[!TIP]
>Wanneer u uw eigen line-of-business-toepassingen bouwt, kunt u ze integreren met Azure AD ter ondersteuning van eenmalige aanmelding. Zie [micro soft Identity platform](..//develop/v2-overview.md)voor meer informatie over het ontwikkelen van apps voor Azure AD.

## <a name="manage-risk-with-conditional-access-policies"></a>Risico beheersen met beleid voor voorwaardelijke toegang

Het koppelen van eenmalige aanmelding van Azure AD aan beleid voor [voorwaardelijke toegang](../conditional-access/concept-conditional-access-cloud-apps.md) biedt een hoge mate van beveiliging voor toegang tot toepassingen. Beleid voor voorwaardelijke toegang biedt gedetailleerde controle over apps op basis van de voor waarden die u hebt ingesteld. 

## <a name="improve-productivity-with-single-sign-on"></a>Verhoog de productiviteit met eenmalige aanmelding

Eenmalige aanmelding (SSO) biedt een uniforme gebruikers ervaring tussen Microsoft 365 en alle andere apps die u gebruikt. Stel dat u uw gebruikers naam en wacht woord voortdurend wilt invoeren.

Zie [Wat is eenmalige](what-is-single-sign-on.md)aanmelding? voor meer informatie over eenmalige aanmelding.

## <a name="address-governance-and-compliance"></a>Voeg governance en naleving toe

Bewaak apps via rapporten die gebruikmaken van hulpprogram ma's voor beveiligings incidenten en Event monitoring (SIEM). U kunt de rapporten openen vanuit de portal of via API's. Programmatisch controleren wie toegang heeft tot uw toepassingen en toegang verwijderen van niet-actieve gebruikers via toegangsbeoordelingen.

## <a name="manage-costs"></a>Kosten beheren

Door te migreren naar Azure AD, kunt u kosten besparen en wordt het beheer van on-premises infrastructuur makkelijk. Azure AD biedt ook self-service tot toepassingen, en bespaart daarmee tijd voor beheerders en gebruikers. Eenmalige aanmelding elimineert de noodzaak van toepassingsspecifieke wachtwoorden. De mogelijkheid u slechts eenmaal te hoeven aanmelden, bespaart op kosten in verband met wachtwoordherstel voor toepassingen en wordt productieverlies voorkomen bij het ophalen van wachtwoorden.

Voor human resources gericht op toepassingen, of andere toepassingen met een grote set gebruikers, kunt u app-inrichting gebruiken om uw leven eenvoudiger te maken. Door app-inrichting wordt het proces voor het toevoegen en verwijderen van gebruikers geautomatiseerd. Zie [Wat is toepassings inrichting?](../app-provisioning/user-provisioning.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [Quickstartreeks over toepassingsbeheer](view-applications-portal.md)
- [Aan de slag met toepassingsintegratie](plan-an-application-integration.md)
- [Meer informatie over het automatiseren van inrichting](../app-provisioning/user-provisioning.md)
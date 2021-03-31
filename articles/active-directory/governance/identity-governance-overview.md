---
title: Identiteitsbeheer - Azure Active Directory | Microsoft Docs
description: Met Azure Active Directory Identity Governance kunt u een goede balans vinden tussen de behoeften van uw organisatie op het gebied van veiligheid en de productiviteit van werknemers met de juiste processen en zichtbaarheid.
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: e02df83d4b7874a1d158aae45f1619eb543e0aec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92362447"
---
# <a name="what-is-azure-ad-identity-governance"></a>Wat is Azure AD Identity Governance?

Met Azure Active Directory (Azure AD) Identity Governance kunt u een goede balans vinden tussen de behoeften van uw organisatie op het gebied van veiligheid en de productiviteit van werknemers met de juiste processen en zichtbaarheid. Het biedt u de instrumenten om ervoor te zorgen dat de juiste mensen toegang hebben tot de juiste resources. Met deze en gerelateerde Azure AD- en Enterprise Mobility + Security-functies kunt u het toegangsrisico beperken door de toegang tot essentiële assets te beveiligen, te bewaken en te controleren. Tegelijkertijd wordt de productiviteit van werknemers en zakenpartners gewaarborgd.  

Identity Governance biedt organisaties de mogelijkheid om de volgende taken uit te voeren voor werknemers, zakelijke partners en leveranciers, en in verschillende services en toepassingen, zowel on-premises als in de cloud:

- De identiteitslevenscyclus beheren
- De toegangslevenscyclus beheren
- Bevoegde toegang voor beheer beveiligen

Het is met name bedoeld om organisaties te helpen bij het oplossen van deze vier belangrijke vragen:

- Welke gebruikers moeten toegang hebben tot welke resources?
- Wat doen gebruikers met die toegang?
- Beschikt de organisatie over effectieve beheeropties voor het beheren van toegang?
- Kunnen auditors controleren of de beheeropties werken?

## <a name="identity-lifecycle"></a>Identiteitslevenscyclus

Met Identity Governance kunnen organisaties een balans vinden tussen *productiviteit*: hoe snel kunnen personen toegang krijgen tot de resources die ze nodig hebben, bijvoorbeeld wanneer ze deel uit gaan maken van mijn organisatie? En *veiligheid*: hoe moet de toegang in de loop van de tijd worden aangepast, bijvoorbeeld als gevolg van wijzigingen in de werknemersstatus van die persoon?  Het beheer van identiteitslevenscycli vormt de basis voor Identity Governance en voor een effectief beheer op schaal moet de infrastructuur voor het identiteitslevenscyclusbeheer voor toepassingen worden gemoderniseerd.

![Identiteitslevenscyclus](./media/identity-governance-overview/identity-lifecycle.png)

Voor veel organisaties is de identiteitslevenscyclus voor werknemers gekoppeld aan de positie van die gebruikers in een HCM-systeem (Human Capital Management).  Azure AD Premium onderhoudt automatisch gebruikersidentiteiten voor personen die in Workday en SuccessFactors zijn opgenomen in zowel Active Directory als Azure Active Directory, zoals wordt beschreven in de [planningsgids voor HR-cloudtoepassing naar Azure Active Directory-gebruikersinrichting](../app-provisioning/plan-cloud-hr-provision.md)  Azure AD Premium omvat ook [Microsoft Identity Manager](/microsoft-identity-manager/), waarmee u records kunt importeren uit on-premises HCM-systemen zoals SAP HCM, Oracle eBusiness en Oracle PeopleSoft.

In toenemende mate moet worden samengewerkt met mensen buiten uw organisatie. [Azure AD B2B](/azure/active-directory/b2b/)-samenwerking stelt u in staat om de toepassingen en services van uw organisatie veilig te delen met gastgebruikers en externe partners van andere organisaties, zonder dat u de controle verliest over uw eigen bedrijfsgegevens.  Met [Azure AD-rechtenbeheer](entitlement-management-overview.md) kunt u selecteren welke gebruikers van een organisatie toegang mogen aanvragen en kunnen worden toegevoegd als B2B-gasten aan de directory van uw organisatie en kunt u ervoor zorgen dat deze gasten worden verwijderd wanneer ze geen toegang meer nodig hebben.

## <a name="access-lifecycle"></a>De toegangslevenscyclus

Organisaties hebben een proces nodig waarmee de toegang wordt beheerd nadat deze oorspronkelijk is ingericht voor een gebruiker toen de identiteit van die gebruiker werd gemaakt.  Daarnaast moeten bedrijfsorganisaties efficiënt kunnen schalen om het toegangsbeleid en de beheeropties doorlopend te kunnen ontwikkelen en afdwingen.

![De toegangslevenscyclus](./media/identity-governance-overview/access-lifecycle.png)

Normaal gesproken worden goedkeuringsbeslissingen met betrekking tot de toegang door de IT-afdeling overgelaten aan besluitvormers van het bedrijf.  Daarnaast kan de IT-afdeling de gebruikers hierbij betrekken.  Gebruikers die toegang hebben tot vertrouwelijke klantgegevens in de marketingtoepassing van een bedrijf in Europa moeten bijvoorbeeld op de hoogte zijn van het bedrijfsbeleid. Gastgebruikers zijn mogelijk niet op de hoogte van de verwerkingsvereisten voor gegevens in een organisatie waardoor ze zijn uitgenodigd.

Organisaties kunnen het toegangslevenscyclusproces automatiseren via technologieën als [dynamische groepen](../enterprise-users/groups-dynamic-membership.md), in combinatie met het inrichten van gebruikers voor [SaaS-apps](../saas-apps/tutorial-list.md) of [apps die zijn geïntegreerd met SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md).  Organisaties kunnen ook bepalen welke [gastgebruikers toegang hebben tot on-premises toepassingen](../external-identities/hybrid-cloud-to-on-premises.md).  Deze toegangsrechten kunnen vervolgens regelmatig worden geëvalueerd met behulp van herhaaldelijke [Azure AD-toegangsbeoordelingen](access-reviews-overview.md).   [Azure AD-rechtenbeheer](entitlement-management-overview.md) stelt u ook in staat om te definiëren hoe gebruikers toegang aanvragen in pakketten van groeps- en teamlidmaatschappen, toepassingsrollen en SharePoint Online-rollen.

Wanneer een gebruiker toegang probeert te krijgen tot toepassingen, dwingt Azure AD beleid voor [voorwaardelijke toegang](../conditional-access/index.yml) af. Het beleid voor voorwaardelijke toegang kan bijvoorbeeld bestaan uit het weergeven van [gebruiksvoorwaarden](../conditional-access/terms-of-use.md) en [het afdwingen dat de gebruiker akkoord gaat met deze voorwaarden](../conditional-access/require-tou.md) voordat ze toegang kunnen krijgen tot een toepassing.

## <a name="privileged-access-lifecycle"></a>Levenscyclus voor bevoegde toegang

In het verleden werd bevoegde toegang door andere leveranciers als een aparte mogelijkheid naast identiteitsbeheer omschreven. Bij Microsoft menen we echter dat het beheer van bevoegde toegang een belangrijk onderdeel is van identiteitsbeheer, met name vanwege het mogelijke misbruik dat is gekoppeld aan deze beheerdersrechten waarmee een organisatie kan worden geconfronteerd. De werknemers, leveranciers en ingehuurd personeel die gebruikmaken van beheerdersrechten moeten worden beheerd.

![Levenscyclus voor bevoegde toegang](./media/identity-governance-overview/privileged-access-lifecycle.png)

[Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) biedt aanvullende beheeropties die zijn afgestemd op het beveiligen van de toegangsrechten voor resources, in Azure AD, Azure en andere online services van Microsoft.  De JIT-toegang (Just-In-Time) en waarschuwingsfuncties die naast meervoudige verificatie en voorwaardelijke toegang voor het wijzigen van rollen worden geboden door Azure AD PIM, bieden een uitgebreide verzameling beheeropties waarmee u de resources van uw bedrijf (AD Directory-, Microsoft 365- en Azure-resourcerollen) kunt beveiligen. Net als bij andere vormen van toegang kunnen organisaties gebruikmaken van toegangsbeoordelingen om het opnieuw certificeren van herhaaldelijke toegang te configureren voor alle gebruikers in beheerdersrollen.

## <a name="governance-capabilities-in-other-azure-ad-features"></a>Beheermogelijkheden in andere Azure AD-functies

Naast de functies die hierboven worden vermeld, zijn extra Azure AD-functies die vaak worden gebruikt om identiteitsbeheerscenario's te bieden:

| Mogelijkheid | Scenario |Functie
| ------- | --------------------- |-----|
|Identiteitslevencyclus (werknemers)|Beheerders kunnen het inrichten van gebruikersaccounts inschakelen vanuit Workday of SuccessFactors HR, in de cloud of on premises.|[Cloud-HR naar Azure AD-gebruikersinrichting](../app-provisioning/plan-cloud-hr-provision.md)|
|Identiteitslevenscyclus (gasten)|Beheerders kunnen selfservice voor gastgebruikers van een andere Azure AD-tenant, directe federatie, eenmalige wachtwoordcode (OTP) of Google-accounts inschakelen.  Gastgebruikers worden automatisch ingericht, het ongedaan maken van de inrichting valt onder het levenscyclusbeleid.|[Rechtenbeheer](entitlement-management-overview.md) met behulp van [B2B](../external-identities/what-is-b2b.md)|
|Rechtenbeheer|Resource-eigenaren kunnen toegangspakketten maken met apps, Teams, Azure AD, Microsoft 365-groepen en SharePoint Online-sites.|[Rechtenbeheer](entitlement-management-overview.md)|
|Toegangsaanvragen|Eindgebruikers kunnen lidmaatschap voor een groep of toegang tot toepassingen aanvragen. Eindgebruikers, met inbegrip van gasten van andere organisaties, kunnen toegang tot pakketten vragen.|[Rechtenbeheer](entitlement-management-overview.md)|
|Werkstroom|Resource-eigenaren kunnen de goedkeurders en escalatie-goedkeurders definiëren voor toegangsaanvragen en goedkeurders definiëren voor aanvragen om rollen te activeren.  |[Rechtenbeheer](entitlement-management-overview.md) en [PIM](../privileged-identity-management/pim-configure.md)|
|Beleids- en rolbeheer|De beheerder kan beleidsregels voor voorwaardelijke toegang voor runtime-toegang definiëren voor toepassingen.  Resource-eigenaren kunnen beleid definiëren voor gebruikerstoegang via toegangspakketten.|Beleid voor [Voorwaardelijke toegang](../conditional-access/overview.md) en [Rechtenbeheer](entitlement-management-overview.md)|
|Toegangscertificering|Beheerders kunnen hercertificering voor terugkerende toegang inschakelen voor: SaaS-apps of groepslidmaatschappen in de cloud, toewijzingen van Azure AD- of Azure-resourcerollen. Toegang tot resources automatisch verwijderen, toegang voor gasten blokkeren en gastaccounts verwijderen.|[Toegangsbeoordelingen](access-reviews-overview.md), ook aanwezig in [PIM](../privileged-identity-management/pim-how-to-start-security-review.md)|
|Voltooien en inrichten|Automatisch inrichten en ongedaan maken van de inrichting in met Azure AD verbonden apps, inclusief via SCIM en op SharePoint Online-sites. |[inrichten van gebruikers](../app-provisioning/user-provisioning.md)|
|Rapportage en analyse|Beheerders kunnen auditlogboeken van recente gebruikersinrichtingen en aanmeldingsactiviteiten bekijken. Integratie met Azure Monitor en 'wie heeft toegang' via toegangspakketten.|[Azure AD-rapporten](../reports-monitoring/overview-reports.md) en [-controle](../reports-monitoring/overview-monitoring.md)|
|Bevoegde toegang|Just-in-time- en geplande toegang, waarschuwingen en goedkeuringswerkstromen voor Azure AD-rollen (waaronder aangepaste rollen) en Azure-resourcerollen.|[Azure AD PIM](../privileged-identity-management/pim-configure.md)|
|Controle|Beheerders kunnen een waarschuwing ontvangen wanneer er beheerdersaccounts worden gemaakt.|[Azure AD PIM-waarschuwingen](../privileged-identity-management/pim-how-to-configure-security-alerts.md)|

## <a name="getting-started"></a>Aan de slag

Ga naar het tabblad Aan de slag van **Identity Governance** in Azure Portal om rechtenbeheer, toegangsbeoordelingen, Privileged Identity Management en gebruiksvoorwaarden te gaan gebruiken.

![Aan de slag met Identity Governance](./media/identity-governance-overview/getting-started.png)


Als u feedback hebt over Identity Governance-functies, klikt u op **Hebt u feedback?** in Azure Portal om uw feedback te verzenden. Het team beoordeelt regelmatig uw feedback.

Hoewel er geen perfecte oplossing of aanbeveling bestaat voor elke klant, bevatten de volgende configuratiehandleidingen ook het minimale beleid dat door Microsoft wordt aanbevolen zodat u over beter beveiligd en productiever personeel beschikt.

- [Configuraties voor identiteiten en apparaattoegang](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Uitgebreide toegang beveiligen](../roles/security-planning.md)

## <a name="appendix---least-privileged-roles-for-managing-in-identity-governance-features"></a>Bijlage: minst bevoorrechte rollen voor het uitvoeren van beheertaken in Identity Governance-functies

U kunt het beste de minst bevoorrechte rol gebruiken om beheertaken in Identity Governance uit te voeren. Het is raadzaam Azure AD PIM te gebruiken om zo nodig een rol te activeren om deze taken uit te voeren. Hieronder vindt u de minst bevoorrechte Azure AD-rollen voor het configureren van Identity Governance-functies:

| Functie | Minst bevoorrechte rol |
| ------- | --------------------- |
| Rechtenbeheer | Beheerder (met uitzondering van het toevoegen van SharePoint Online-sites aan catalogi, waarvoor Globale beheerder vereist is) |
| Toegangsbeoordelingen | Gebruikersbeheerder (met uitzondering van toegangsbeoordelingen van Azure- of Azure AD-rollen, waarvoor Beheerder voor bevoorrechte rollen is vereist) |
|Privileged Identity Management | Beheerder voor bevoorrechte rollen |
| Gebruiksvoorwaarden | Beveiligingsbeheerder of Beheerder voor voorwaardelijke toegang |

## <a name="next-steps"></a>Volgende stappen

- [Wat is Azure AD-rechtenbeheer?](entitlement-management-overview.md)
- [Wat zijn toegangsbeoordelingen in Azure Active Directory?](access-reviews-overview.md)
- [Wat is Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)
- [Wat kan ik doen met Gebruiksvoorwaarden?](../conditional-access/terms-of-use.md)
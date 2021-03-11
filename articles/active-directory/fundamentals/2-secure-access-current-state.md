---
title: De huidige status van externe samen werking met Azure Active Directory ontdekken
description: Leer methoden om de huidige status van uw samen werking te ontdekken.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 30858e9978f7e8857c5f8a2dcdfd7455f6e97b60
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102553422"
---
# <a name="discover-the-current-state-of-external-collaboration-in-your-organization"></a>De huidige status van externe samen werking in uw organisatie detecteren 

Voordat u de huidige status van uw externe samen werking detecteert, moet u [de gewenste beveiligings postuur vaststellen](1-secure-access-posture.md). U wordt gekeken naar de behoeften van uw organisatie voor gecentraliseerd versus gedelegeerd beheer en alle relevante governance-, regelgevende en nalevings doelen. 

Personen in uw organisatie kunnen waarschijnlijk al samen werken met gebruikers van andere organisaties. Samen werking kan worden door functies in productiviteits toepassingen zoals Microsoft 365, door e-mail te verzenden of door op andere wijze resources te delen met externe gebruikers. De pijlers van uw governance-abonnement vormen een formulier dat wordt gedetecteerd 
*   de gebruikers die externe samen werking initiëren.
*   de externe gebruikers en organisaties waarmee u samenwerkt.
*   de toegang wordt verleend aan externe gebruikers.


## <a name="users-initiating-external-collaboration"></a>Gebruikers die externe samen werking initiëren

De gebruikers die externe samen werking initiëren, hebben het beste inzicht in de toepassingen die het meest relevant zijn voor externe samen werking en wanneer die toegang moet eindigen. Meer informatie over deze gebruikers kunt u bepalen wie moet worden gemachtigd voor het uitnodigen van externe gebruikers, het maken van toegangs pakketten en het volt ooien van toegangs Beoordelingen.

Als u wilt zoeken naar gebruikers die momenteel samen werken, raadpleegt u het [Microsoft 365 audit logboek voor delen en activiteiten voor toegangs aanvragen](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance#sharing-and-access-request-activities). U kunt ook het [controle logboek van Azure AD bekijken voor meer informatie over wie B2B](../external-identities/auditing-and-reporting.md) -gebruikers naar uw Directory hebben uitgenodigd.

## <a name="find-current-collaboration-partners"></a>Huidige samenwerkings partners zoeken

Externe gebruikers kunnen [Azure AD B2B-gebruikers](../external-identities/what-is-b2b.md) (voor keur) met door partners beheerde referenties of externe gebruikers met lokaal ingerichte referenties zijn. Deze gebruikers zijn doorgaans (maar niet altijd) gemarkeerd met een User type van gast. U kunt gast gebruikers opsommen via de [Microsoft Graph-API](/graph/api/user-list?tabs=http), [Power shell](/graph/api/user-list?tabs=http)of de [Azure Portal](../enterprise-users/users-bulk-download.md).

### <a name="use-email-domains-and-companyname-property"></a>E-mail domeinen en eigenschap companyName gebruiken

Externe organisaties kunnen worden bepaald door de domein namen van e-mail adressen van externe gebruikers. Als gebruikers-id-providers zoals Google worden ondersteund, is dit mogelijk niet mogelijk. In dit geval is het raadzaam om het kenmerk companyName te schrijven om de externe organisatie van de gebruiker duidelijk te identificeren.

### <a name="use-allow-or-deny-lists"></a>Lijsten voor toestaan of weigeren gebruiken

Bekijk of uw organisatie alleen samen werking met specifieke organisaties wil toestaan. U kunt ook overwegen of uw organisatie de samen werking met specifieke organisaties wil blok keren.  Op Tenant niveau is er sprake van een [lijst voor toestaan of weigeren](../external-identities/allow-deny-list.md)die kan worden gebruikt voor het beheren van algemene B2B-uitnodigingen en-inzendingen, ongeacht de bron (bijvoorbeeld teams, share point en Azure Portal).
Als u gebruikmaakt van rechten beheer, kunt u ook toegangs pakketten bereiken met een subset van uw partners met behulp van de specifieke instellingen voor verbonden organisaties, zoals hieronder wordt weer gegeven.


![Scherm opname van de lijst Deny toestaan in het maken van een nieuw toegangs pakket.](media/secure-external-access/2-new-access-package.png)


## <a name="find-access-being-granted-to-external-users"></a>Toegang krijgen tot externe gebruikers

Zodra u een inventaris van externe gebruikers en organisaties hebt, kunt u de toegang tot deze gebruikers bepalen met behulp van de Microsoft Graph-API om het [lidmaatschap](/graph/api/resources/groups-overview) van Azure ad-groepen of een [directe toepassings toewijzing](/graph/api/resources/approleassignment) in azure ad te bepalen.


### <a name="enumerate-application-specific-permissions"></a>Toepassingsspecifieke machtigingen opsommen

U kunt ook inventarisatie van toepassingsspecifieke machtigingen uitvoeren. U kunt bijvoorbeeld programmatisch een machtigings rapport voor share point online genereren met behulp van [Dit script](https://gallery.technet.microsoft.com/office/SharePoint-Online-c9ec4f64).

Onderzoek specifiek de toegang tot al uw zakelijke gevoelige en bedrijfskritische apps, zodat u volledig op de hoogte bent van alle externe toegang.

### <a name="detect-ad-hoc-sharing"></a>Ad-hoc delen detecteren
Als uw e-mail en netwerk abonnementen dit inschakelen, kunt u inhoud onderzoeken die via e-mail wordt gedeeld of via SaaS-apps (software als een service voor onbevoegden). [Microsoft 365 bescherming tegen gegevens verlies](/microsoft-365/compliance/data-loss-prevention-policies) helpt u bij het identificeren, voor komen en bewaken van onbedoelde delen van gevoelige informatie over uw Microsoft 365-infra structuur. [Microsoft Cloud app Security](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/cloud-app-security) kunt u helpen bij het identificeren van het gebruik van niet-geautoriseerde SaaS-apps in uw omgeving.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen over het beveiligen van externe toegang tot bronnen. U wordt aangeraden de acties in de vermelde volg orde uit te voeren.

1. [Uw beveiligings postuur voor externe toegang bepalen](1-secure-access-posture.md)

2. [Ontdek de huidige status](2-secure-access-current-state.md) (u bent hier.)

3. [Een governance-plan maken](3-secure-access-plan.md)

4. [Groepen gebruiken voor beveiliging](4-secure-access-groups.md)

5. [Overgang naar Azure AD B2B](5-secure-access-b2b.md)

6. [Veilige toegang met het rechten beheer](6-secure-access-entitlement-managment.md)

7. [Veilige toegang met beleid voor voorwaardelijke toegang](7-secure-access-conditional-access.md)

8. [Veilige toegang met gevoeligheids labels](8-secure-access-sensitivity-labels.md)

9. [Beveiligde toegang tot micro soft teams, OneDrive en share point](9-secure-access-teams-sharepoint.md)
---
title: Wat zijn toegangsbeoordelingen? - Azure Active Directory | Microsoft Azure
description: Met Azure Active Directory-toegangsbeoordelingen kunt u groepslidmaatschap en de toegang tot toepassingen controleren, zodat ze voldoen aan governance, risicobeheer en nalevingsinitiatieven in uw organisatie.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.subservice: compliance
ms.date: 10/29/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.custom: contperf-fy21q1
ms.openlocfilehash: 22bfef17f68b2e83e4f7462d8e9af6fb8aacc284
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98246737"
---
# <a name="what-are-azure-ad-access-reviews"></a>Wat zijn toegangsbeoordelingen in Azure AD?

Met Azure AD-toegangsbeoordelingen (Azure Active Directory) kunnen organisaties op efficiënte wijze groepslidmaatschappen, de toegang tot bedrijfstoepassingen en de toewijzing van rollen beheren. De gebruikerstoegang kan regelmatig herzien worden om ervoor te zorgen dat enkel de juiste mensen toegang blijven hebben.

Deze video biedt een snel overzicht van toegangsbeoordelingen:

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>Waarom zijn toegangsbeoordelingen belangrijk?

Met Azure AD kunt u samenwerken met gebruikers binnen uw organisatie en met externe gebruikers. Gebruikers kunnen lid worden van groepen, gasten uitnodigen, verbinding maken met apps in de cloud en op afstand werken vanaf hun zakelijke of persoonlijke apparaten. De inzetbaarheid van krachtige zelfhulpopties heeft ertoe geleid dat er betere mogelijkheden voor toegangsbeheer nodig zijn.

- Hoe zorgt u ervoor dat nieuwe medewerkers de toegang hebben die nodig is om productief te zijn?
- Hoe zorgt u ervoor dat mensen die van team veranderen of het bedrijf verlaten hun oude toegangsrechten verliezen?
- Overmatige toegangsrechten kunnen leiden tot inbreuken.
- Overmatige toegangsrechten kunnen ook bevindingen opleveren tijdens een audit waaruit een gebrekkige toegangscontrole blijkt.
- U moet proactief samenwerken met eigenaars van resources om ervoor te zorgen dat ze regelmatig controleren wie toegang heeft tot hun resources.

## <a name="when-should-you-use-access-reviews"></a>Wanneer moet u toegangsbeoordelingen gebruiken?

- **Te veel gebruikers met een bevoorrechte rol:** Het is een goed idee om te controleren hoeveel gebruikers beheerderstoegang hebben, hoeveel er Globale beheerders zijn, en of er uitgenodigde gasten of partners zijn die niet zijn verwijderd na de toewijzing van een beheerstaak. U kunt de roltoewijzingsgebruikers opnieuw certificeren in [Azure AD-rollen](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json), zoals Globale beheerders, of [Azure-resourcerollen](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) zoals Gebruikerstoegangsbeheerder in de ervaring [Azure Active Directory Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md).
- **Wanneer automatisering niet mogelijk is:** U kunt regels maken voor een dynamisch lidmaatschap van beveiligingsgroepen of Microsoft 365-groepen, maar wat als de personeelsgegevens zich niet in Azure AD bevinden of als gebruikers nadat ze de groep hebben verlaten nog steeds toegang nodig hebben om hun vervanging te trainen? U kunt vervolgens een beoordeling maken voor die groep om ervoor te zorgen dat gebruikers die nog toegang nodig hebben, die ook behouden.
- **Wanneer een groep gebruikt wordt voor een nieuw doeleinde:** Als u een groep heeft die wordt gesynchroniseerd met Azure AD of als u van plan bent de toepassing SalesForce in te schakelen voor iedereen in de groep van het verkoopteam, dan is het zinvol om de groepseigenaar te vragen het groepslidmaatschap te controleren voordat de groep wordt gebruikt voor inhoud in een andere risicogroep.
- **Bedrijfskritieke gegevenstoegang:** voor bepaalde resources kan het nodig zijn om gebruikers buiten IT te vragen om zich regelmatig af te melden en een motivering te geven voor waarom ze toegang nodig hebben voor controledoeleinden.
- **Om een uitzonderingslijst voor het beleid te onderhouden:** In een ideale wereld volgen alle gebruikers het toegangsbeleid om de toegang tot de resources van uw organisatie te beveiligen. Soms zijn er echter zakelijke cases die uitzonderingen vereisen. Als IT-beheerder kunt u deze taak beheren, het toezicht op beleidsuitzonderingen voorkomen en auditeurs bewijs aanleveren dat deze uitzonderingen regelmatig gecontroleerd worden.
- **Groepseigenaren vragen om te bevestigen dat ze nog gasten in hun groepen nodig hebben:** Werknemerstoegang kan worden geautomatiseerd voor bepaald on-premises identiteits- en toegangsbeheer (IAM), maar niet voor uitgenodigde gasten. Als een groep gasten toegang verleent tot bedrijfsgevoelige inhoud, dan is het de verantwoordelijkheid van de groepseigenaar om te bevestigen dat de gasten nog steeds een legitieme zakelijke reden hebben voor hun toegang.
- **Regelmatig opnieuw controles uitvoeren:** U kunt toegangsbeoordeling van gebruikers regelmatig herhalen zoals elke week, elke maand, elk kwartaal of elk jaar. De beoordelaars worden bij aanvang van elke beoordelingsronde op de hoogte gebracht. Beoordelaars kunnen toegang goedkeuren of weigeren met behulp van een gebruiksvriendelijke interface en slimme aanbevelingen.

>[!NOTE]
>Zie [Create an access review of groups or applications](create-access-review.md) (Een toegangsbeoordeling maken van groepen of toepassingen) als u klaar bent voor het proberen van toegangsbeoordelingen

## <a name="where-do-you-create-reviews"></a>Waar kunt u controles doen?

Afhankelijk van wat u wilt controleren, maakt u uw toegangsbeoordelingen in Azure AD-toegangsbeoordelingen, Azure AD-bedrijfstoepassingen (in preview) of Azure AD PIM.

| Toegangsrechten van gebruikers | Beoordelaars kunnen | Beoordeling gemaakt in | Ervaring van beoordelaar |
| --- | --- | --- | --- |
| Leden beveiligingsgroep</br>Leden Office-groep | Opgegeven beoordelaars</br>Groepseigenaren</br>Zelf controleren | Azure AD-toegangsbeoordelingen</br>Azure Active Directory-groepen | Toegangsvenster |
| Toegewezen aan een verbonden app | Opgegeven beoordelaars</br>Zelf controleren | Azure AD-toegangsbeoordelingen</br>Azure AD-bedrijfsapps (in preview) | Toegangsvenster |
| Azure AD-rol | Opgegeven beoordelaars</br>Zelf controleren | [Azure AD PIM](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Azure Portal |
| Azure-resourcerol | Opgegeven beoordelaars</br>Zelf controleren | [Azure AD PIM](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Azure Portal |

## <a name="license-requirements"></a>Licentievereisten

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

### <a name="how-many-licenses-must-you-have"></a>Hoeveel licenties heeft u nodig?

Uw directory moet ten minste evenveel Azure AD Premium P2-licenties hebben als het aantal werknemers die de volgende taken zullen uitvoeren:

-   Leden die zijn toegewezen als beoordelaars
-   Leden die een zelfbeoordeling uitvoeren
-   Leden als groepseigenaren die een toegangsbeoordeling uitvoeren
-   Leden als toepassingseigenaren die een toegangsbeoordeling uitvoeren

Voor gastgebruikers is de licentiebehoefte afhankelijk van het licentiemodel dat u gebruikt. De onderstaande activiteiten van gastgebruikers worden echter beschouwd als Azure AD Premium P2-gebruik:

-   Gastgebruikers die zijn toegewezen als beoordelaars
-   Gastgebruikers die een zelfbeoordeling uitvoeren
-   Gastgebruikers als groepseigenaren die een toegangsbeoordeling uitvoeren
-   Gastgebruikers als toepassingseigenaren die een toegangsbeoordeling uitvoeren


Azure AD Premium P2-licenties zijn **niet** vereist voor Globale beheerders of Gebruikersbeheerders die toegangsbeoordelingen instellen, instellingen configureren of de beslissingen van de beoordelingen toepassen.

Azure AD-toegang voor gastgebruikers is gebaseerd op het factureringsmodel MAU (maandelijks actieve gebruiker), dat het factureringsmodel 1:5-verhouding vervangt. Zie [Prijzen voor externe Azure AD-identiteiten](../external-identities/external-identities-pricing.md) voor meer informatie.

Zie [Licenties toewijzen en verwijderen met behulp van het Azure Active Directory-portal](../fundamentals/license-users-groups.md) voor meer informatie over licenties.

### <a name="example-license-scenarios"></a>Voorbeelden van licentiescenario's

Hier volgen enkele voorbeelden van licentiescenario's om te helpen bepalen hoeveel licenties u nodig heeft.

| Scenario | Berekening | Aantal licenties |
| --- | --- | --- |
| Een beheerder maakt een toegangsbeoordeling van groep A met 75 gebruikers en 1 groepseigenaar en wijst de groepseigenaar toe als de beoordelaar. | 1 licentie voor de groepseigenaar als beoordelaar | 1 |
| Een beheerder maakt een toegangsbeoordeling van groep B met 500 gebruikers en 3 groepseigenaar en wijst de 3 groepseigenaars toe als de beoordelaars. | 3 licenties voor alle groepseigenaars als beoordelaars | 3 |
| Een beheerder maakt een toegangsbeoordeling van groep B met 500 gebruikers. Maakt hier een zelfbeoordeling van. | 500 licenties voor alle gebruikers als zelfbeoordelaars | 500 |
| Een beheerder maakt een toegangsbeoordeling van Groep C met 50 leden en 25 gastgebruikers. Maakt hier een zelfbeoordeling van. | 50 licenties voor alle gebruikers als zelfbeoordelaars.* | 50 |
| Een beheerder maakt een toegangsbeoordeling van Groep D met 6 leden en 108 gastgebruikers. Maakt hier een zelfbeoordeling van. | 6 licenties voor alle gebruikers als zelfbeoordelaars. Gastgebruikers worden gefactureerd op basis van maandelijks actieve gebruiker (MAU). Er zijn geen extra licenties nodig. *  | 6 |

\* De prijzen voor externe Azure AD-identiteiten (gastgebruiker) zijn gebaseerd op maandelijks actieve gebruikers (MAU), d.w.z. het aantal unieke gebruikers met verificatie-activiteit in een kalendermaand. Dit model vervangt het factureringsmodel met een verhouding van 1:5, waarbij vijf gastgebruikers voor elke Azure AD Premium-licentie in uw tenant waren toegestaan. Wanneer uw tenant aan een abonnement is gekoppeld en u functies van Externe identiteiten gebruikt om met gastgebruikers samen te werken, wordt u automatisch gefactureerd met behulp van het MAU-gebaseerde factureringsmodel. Zie ‘Factureringsmodel voor externe Azure AD-identiteiten’ voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [Een toegangsbeoordeling van groepen of toepassingen maken](create-access-review.md)
- [Een toegangsbeoordeling maken van gebruikers met een Azure AD-beheerderrol](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [Toegang tot groepen of toepassingen beoordelen](perform-access-review.md)
- [Een toegangsbeoordeling van groepen of toepassingen voltooien](complete-access-review.md)

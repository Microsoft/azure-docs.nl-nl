---
title: Wat is rechtenbeheer? - Azure AD
description: Krijg een overzicht van Azure Active Directory-rechtenbeheer en hoe u dit kunt gebruiken om toegang tot groepen, toepassingen en SharePoint Online-sites voor interne en externe gebruikers.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.subservice: compliance
ms.date: 11/23/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.custom: contperf-fy21q1
ms.openlocfilehash: 7efaaf9cbfdb13c80ce653c99d05da451c2b8968
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100547312"
---
# <a name="what-is-azure-ad-entitlement-management"></a>Wat is Azure AD-rechtenbeheer?

Azure Active Directory-rechtenbeheer (Azure AD) is een functie voor [identiteitsbeheer](identity-governance-overview.md) waarmee organisaties de levenscyclus van identiteiten en toegang op schaal kunnen beheren, door werkstromen voor het aanvragen van toegang te automatiseren en toewijzingen, controles en vervaldatums te bekijken.

Werknemers in organisaties moeten toegang hebben tot verschillende groepen, toepassingen en sites om hun werk te doen. Deze toegang beheren is moeilijk omwille van veranderende behoeften - er worden nieuwe toepassingen toegevoegd, of gebruikers hebben aanvullende toegangsrechten nodig.  Dit scenario wordt nog complexer wanneer u samenwerkt met externe organisaties. U weet niet altijd wie in de andere organisatie toegang nodig heeft tot de resources van uw organisatie, en zij weten niet welke toepassingen, groepen of sites uw organisatie gebruikt.

Azure AD-rechtenbeheer kan u helpen om toegang tot groepen, toepassingen en SharePoint Online-sites efficiënter te beheren voor interne gebruikers en gebruikers buiten uw organisatie die toegang nodig hebben tot die resources.

## <a name="why-use-entitlement-management"></a>Waarom rechtenbeheer gebruiken?

Bedrijven hebben vaak moeite met het toegangsbeheer van werknemers voor resources zoals:

- Gebruikers weten niet altijd welke toegang ze nodig hebben, en zelfs als dat wel het geval is, kan het moeilijk zijn om de juiste personen te vinden die hun toegang moeten goedkeuren
- Zodra gebruikers de toegang tot een resource hebben gevonden en gekregen, kunnen ze die langer behouden dan nodig is voor zakelijke doeleinden

Deze problemen zijn nog groter voor gebruiker die toegang nodig hebben vanaf een andere organisatie, zoals externe gebruikers van toeleveranciers of andere zakelijke partners. Bijvoorbeeld:

- Soms is er niemand die alle specifieke personen binnen een andere organisatie kent die uitgenodigd moeten worden
- Zelfs als ze deze gebruikers kunnen uitnodigen, dan is er soms niemand in die andere organisatie die de toegang van alle gebruikers consistent kan beheren

Azure AD-rechtenbeheer kan helpen om deze uitdagingen aan te pakken.  Lees de [casestudy van Avanade](https://customers.microsoft.com/story/avanade-professional-services-azure-canada) en de [casestudy van Centrica](https://customers.microsoft.com/story/757467-centrica-energy-azure) en ontdek hoe klanten Azure AD-rechtenbeheer gebruiken.  Deze video biedt een overzicht van rechtenbeheer en welke meerwaarde het biedt:

>[!VIDEO https://www.youtube.com/embed/_Lss6bFrnQ8]

## <a name="what-can-i-do-with-entitlement-management"></a>Wat kan ik doen met rechtenbeheer?

Hier zijn enkele mogelijkheden van rechtenbeheer:

- Niet-beheerders de mogelijkheid geven om toegangspakketten te maken. Deze toegangspakketten bevatten resources die gebruikers kunnen aanvragen en de gedelegeerde beheerders van de toegangspakketten kunnen beleidsregels bepalen over welke gebruikers toegang kunnen aanvragen, wie hun toegang moet goedkeuren en wanneer toegang verloopt.
- Verbonden organisaties selecteren waarvan gebruikers toegang kunnen aanvragen.  Wanneer een gebruiker die zich nog niet in uw gebruikerslijst bevindt toegang vraagt, en die krijgt, wordt deze automatisch uitgenodigd voor uw gebruikerslijst en krijgt deze toegang toegewezen.  Wanneer hun toegang verloopt, en als ze geen andere toegewezen toegangspakketten hebben, dan kan hun B2B-account in uw gebruikerslijst automatisch verwijderd worden.

>[!NOTE]
>Als u klaar bent om Rechtenbeheer uit te proberen, kunt u aan de slag gaan met onze [zelfstudie om uw eerste toegangspakket te maken](entitlement-management-access-package-first.md).

U kunt ook de [veelvoorkomende scenario's](entitlement-management-scenarios.md) lezen of video's bekijken, waaronder

- [Azure AD-rechtenbeheer implementeren in uw organisatie](https://www.youtube.com/watch?v=zaaKvaaYwI4)
- [Uw gebruik van Azure AD-rechtenbeheer bewaken en schalen](https://www.youtube.com/watch?v=omtNJ7ySjS0)
- [Delegeren in rechtenbeheer](https://www.youtube.com/watch?v=Fmp1eBxzrqw)

## <a name="what-are-access-packages-and-what-resources-can-i-manage-with-them"></a>Wat zijn toegangspakketten en welke resources kan ik ermee beheren?

Rechtenbeheer introduceert het concept van een *toegangspakket* in Azure AD. Een toegangspakket is een bundel van alle resources met de toegang die een gebruiker nodig heeft om aan een project te werken of een taak uit te voeren. Toegangspakketten worden gebruikt om toegang te beheren voor uw interne werknemers, en ook gebruikers buiten uw organisatie.

 Dit zijn de soorten resources waarmee u de toegang van de gebruiker kunt beheren met rechtenbeheer:

- Lidmaatschap van Azure AD-beveiligingsgroepen
- Lidmaatschap van Microsoft 365-groepen en -teams
- Toewijzing aan Azure AD-bedrijfstoepassingen, waaronder SaaS-toepassingen en toepassingen met aangepaste integratie die ondersteuning bieden voor federatie/eenmalige aanmelding en/of inrichting
- Lidmaatschap van SharePoint Online-sites

U kunt ook de toegang beheren tot andere resources die afhankelijk zijn van Azure AD-beveiligingsgroepen of Microsoft 365-groepen.  Bijvoorbeeld:

- U kunt gebruikers licenties geven voor Microsoft 365 met behulp van een Azure AD-beveiligingsgroep in een toegangspakket en [groepslicenties](../enterprise-users/licensing-groups-assign.md) configureren voor die groep.
- U kunt gebruikers in staat stellen om Azure-resources te beheren met behulp van een Azure AD-beveiligingsgroep in een toegangspakket en een [Azure-roltoewijzing](../../role-based-access-control/role-assignments-portal.md) maken voor die groep.
- U kunt gebruikers toegang geven tot het beheren van Azure AD-rollen door gebruik te maken van groepen die kunnen worden toegewezen aan Azure AD-rollen in een toegangspakket en [een Azure AD-rol toe te wijzen aan die groep](../roles/groups-assign-role.md).

## <a name="how-do-i-control-who-gets-access"></a>Hoe kan ik controleren wie toegang krijgt?

Met een toegangspakket geeft een beheerder of een gedelegeerde beheerder de resources (groepen, apps en sites) en de rollen op die de gebruikers nodig hebben voor die resources.

Toegangspakketten bestaan ook uit een of meer *beleidsregels*. Een beleid definieert de regels of richtlijnen voor toewijzing tot een toegangspakket. Elke beleidsregel kan gebruikt worden om ervoor te zorgen dat enkel de juiste gebruikers toegang kunnen aanvragen, dat er fiatteurs zijn voor hun verzoek en dat hun toegang tot die resources beperkt is in de tijd en verloopt wanneer dit niet vernieuwd wordt.

![Toegangspakket en beleidsregels](./media/entitlement-management-overview/elm-overview-access-package.png)

Binnen elk beleid bepaalt een beheerder of een beheerder van het toegangspakket

- De bestaande gebruikers (meestal werknemers of reeds bestaande gasten) of de partnerorganisaties van externe gebruikers die in aanmerking komen om toegang aan te vragen
- Het goedkeuringsproces en de gebruikers die toegang kunnen goedkeuren of weigeren
- Hoelang een gebruiker toegang heeft zodra deze is goedgekeurd, voor de toewijzing verloopt

Het volgende diagram toont een voorbeeld van de verschillende elementen in rechtenbeheer. Het toont één catalogus met twee voorbeelden van toegangspakketten.

- **Toegangspakket 1** bevat één groep als resource. Toegang wordt bepaald met een beleid dat een groep gebruikers in staat stelt om toegang aan te vragen.
- **Toegangspakket 2** bevat een groep, een toepassing en een SharePoint Online-site als resources. Toegang wordt bepaald door twee verschillende beleidsregels. De eerste beleidsregel stel een groep gebruikers in staat om toegang aan te vragen. De tweede beleidsregel stelt gebruikers in een externe gebruikerslijst in staat om toegang aan te vragen.

![Overzicht rechtenbeheer](./media/entitlement-management-overview/elm-overview.png)

## <a name="when-should-i-use-access-packages"></a>Wanneer moet ik toegangspakketten gebruiken?

Toegangspakketten vormen geen vervanging voor andere mechanismen om toegang toe te wijzen.  Ze zijn het meest geschikt in situaties zoals:

- Werknemers hebben in de tijd beperkte toegang nodig voor een bepaalde taak.  U kunt bijvoorbeeld groepslicenties en een dynamische groep gebruiken om ervoor te zorgen dat alle werknemers een Exchange Online-postvak hebben, en daarnaast toegangspakketten gebruiken voor situaties waarbij werknemers aanvullende toegang nodig hebben, zoals om afdelingsspecifieke resources van een ander departement te lezen.
- Toegang die moet worden goedgekeurd door de manager van een werknemer of een andere aangewezen persoon.
- Afdelingen willen hun eigen toegangsbeleid voor hun resources beheren zonder dat IT hierbij betrokken moet worden.  
- Twee of meer organisaties werken samen aan een project. Bijgevolg moeten meerdere gebruikers van één organisatie via Azure AD B2B binnengebracht worden om de resources van een andere organisatie te bekijken.

## <a name="how-do-i-delegate-access"></a>Hoe kan ik toegang delegeren?

 Toegangspakketten worden gedefinieerd in containers die *catalogussen* worden genoemd.  U kunt één catalogus hebben voor al uw toegangspakketten, of mensen aanduiden om hun eigen catalogi te maken en beheren. Een beheerder kan resources toevoegen aan elke catalogus, maar een niet-beheerder kan enkel de resources waarvan ze eigenaar zijn toevoegen aan een catalogus. De eigenaar van een catalogus kan andere gebruikers toevoegen als mede-eigenaars, of als beheerders van een toegangspakket.  Deze scenario's worden verder beschreven in het artikel [delegering en rollen in Azure AD-rechtenbeheer](entitlement-management-delegate.md).

## <a name="summary-of-terminology"></a>Overzicht van terminologie

Als u meer inzicht wilt krijgen in rechtenbeheer en de bijbehorende documentatie, raadpleeg dan de volgende lijst met termen.

| Term | Beschrijving |
| --- | --- |
| toegangspakket | Een bundel resources die een team of project nodig heeft en die wordt beheerd met beleid. Een toegangspakket bevindt zich altijd in een catalogus. U maakt een nieuw toegangspakket voor een scenario waarin gebruikers toegang moeten aanvragen.  |
| toegangsaanvraag | Een verzoek om toegang te krijgen tot de resources in een toegangspakket. Een aanvraag verloopt meestal via een goedkeuringsstroom.  Als deze is goedgekeurd, krijgt de gebruiker een toegangspakket toegewezen. |
| toewijzing | Een toewijzing van een toegangspakket aan een gebruiker zorgt ervoor dat de gebruiker over alle resourcerollen van dat toegangspakket beschikt.  Op de toewijzing van toegangspakketten zit meestal een tijdslimiet. |
| catalogus | Een container met verwante resources en toegangspakketten.  Catalogi worden gebruikt voor delegering, zodat niet-beheerders hun eigen toegangspakketten kunnen maken. Eigenaars van een catalogus kunnen resources die ze bezitten toevoegen aan een catalogus. |
| catalogusmaker | Een groep gebruikers die gemachtigd zijn om nieuwe catalogussen te maken.  Wanneer een niet-beheerder die gemachtigd is om een catalogusmaker te zijn een nieuwe catalogus maakt, wordt deze automatisch de eigenaar van die catalogus. |
| verbonden organisatie | Een externe Azure AD-map of -domein waar u een relatie mee hebt. In een beleid kan worden opgegeven dat de gebruikers van een verbonden organisatie toegang mogen aanvragen. |
| policy | Een set regels die de levenscyclus voor toegang definieert, zoals hoe gebruikers toegang krijgen, wie dit kan goedkeuren en hoelang gebruikers toegang krijgen via een toewijzing. Een beleid is gekoppeld aan een toegangspakket. Een toegangspakket kan bijvoorbeeld twee beleidsregels hebben: één voor werknemers om toegang te vragen en een ander voor externe gebruikers om toegang te vragen. |
| resource | Een asset, zoals een Office-groep, een beveiligingsgroep, een toepassing of een SharePoint Online-site, met een rol waarvoor een gebruiker machtiging kan krijgen. |
| resourcedirectory | Een directory die een of meer resources bevat om te delen. |
| resourcerol | Een verzameling van machtigingen die horen bij en gedefinieerd zijn door een resource. Een groep heeft twee rollen: lid en eigenaar. SharePoint-sites hebben doorgaans drie rollen, maar kunnen extra aangepaste rollen hebben. Toepassingen kunnen aangepaste rollen hebben. |


## <a name="license-requirements"></a>Licentievereisten

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Gespecialiseerde clouds, zoals Azure Duitsland en Azure China 21Vianet, zijn momenteel niet beschikbaar voor gebruik.

### <a name="how-many-licenses-must-you-have"></a>Hoeveel licenties heeft u nodig?

Zorg ervoor dat uw directory ten minste evenveel Azure AD Premium P2-licenties heeft als u:

- Leden heeft die een toegangspakket **kunnen** aanvragen.
- Leden heeft die een toegangspakket <u>aanvragen</u>.
- Leden die <u>aanvragen voor een toegangspakket goedkeuren</u>.
- Leden die <u>toewijzingen bekijken</u> voor een toegangspakket. 
- Leden heeft die een <u>directe toewijzing</u> aan een toegangspakket hebben.

Voor gastgebruikers is de licentiebehoefte afhankelijk van het [licentiemodel](../external-identities/external-identities-pricing.md) dat u gebruikt. De onderstaande activiteiten van gastgebruikers worden echter beschouwd als Azure AD Premium P2-gebruik:
- Gastgebruikers heeft die een toegangspakket <u>aanvragen</u>. 
- Gastgebruikers die <u>aanvragen voor een toegangspakket goedkeuren</u>.
- Gastgebruikers die <u>toewijzingen bekijken</u> voor een toegangspakket.
- Gastgebruikers heeft die een <u>directe toewijzing</u> aan een toegangspakket hebben. 

Azure AD Premium P2-licenties zijn **niet** vereist voor de volgende taken:

- Er zijn geen licenties vereist voor gebruikers met de rol Globale beheerder die initiële catalogi, toegangspakketten en beleidsregels instellen en administratieve taken delegeren aan andere gebruikers.
- Er zijn geen licenties vereist voor gebruikers die administratieve taken gedelegeerd krijgen, zoals een catalogusmaker, cataloguseigenaar en een beheerder van een toegangspakket.
- Er zijn geen licenties vereist voor gasten die toegangspakketten **kunnen** aanvragen, maar dat **niet** doen.

Zie [Licenties toewijzen en verwijderen met behulp van het Azure Active Directory-portal](../fundamentals/license-users-groups.md) voor meer informatie over licenties.

### <a name="example-license-scenarios"></a>Voorbeelden van licentiescenario's

Hier volgen enkele voorbeelden van licentiescenario's om te helpen bepalen hoeveel licenties u nodig heeft.

| Scenario | Berekening | Aantal licenties |
| --- | --- | --- |
| Een Globale beheerder bij Woodgrove Bank maakt initiële catalogi en delegeert administratieve taken aan zes andere gebruikers. Een van de beleidsregels geeft aan dat **Alle werknemers** (2.000 werknemers) een specifieke set toegangspakketten kunnen aanvragen. 150 werknemers vragen de toegangspakketten aan. | 2\.000 werknemers die de toegangspakketten **kunnen** aanvragen | 2.000 |
| Een Globale beheerder bij Woodgrove Bank maakt initiële catalogi en delegeert administratieve taken aan zes andere gebruikers. Een van de beleidsregels geeft aan dat **Alle werknemers** (2.000 werknemers) een specifieke set toegangspakketten kunnen aanvragen. Een ander beleid geeft aan dat sommige gebruikers van **partner Contoso** (gasten) dezelfde toegangspakketten kunnen aanvragen als ze goedkeuring krijgen. Contoso heeft 30.000 gebruikers. 150 werknemers vragen de toegangspakketten en 10.500 gebruikers van Contoso vragen toegang. | 2\.000 werknemers + 500 gastgebruikers van Contoso die de verhouding 1:5 overschrijden (10.500 - (2.000 * 5)) | 2500 |

## <a name="next-steps"></a>Volgende stappen

- Als u geïnteresseerd bent in het gebruik van de Azure Portal om de toegang tot resources te beheren, raadpleegt u [zelf studie: toegang tot resources beheren-Azure Portal](entitlement-management-access-package-first.md).
- Als u geïnteresseerd bent in het gebruik van Microsoft Graph voor het beheren van de toegang tot bronnen, raadpleegt u [zelf studie: toegang tot resources beheren-Microsoft Graph](/graph/tutorial-access-package-api?toc=/azure/active-directory/governance/toc.json&bc=/azure/active-directory/governance/breadcrumb/toc.json)
- [Algemene scenario's](entitlement-management-scenarios.md)

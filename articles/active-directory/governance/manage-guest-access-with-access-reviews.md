---
title: Gast toegang beheren met toegangs beoordelingen-Azure AD
description: Gast gebruikers beheren als leden van een groep of toegewezen aan een toepassing met Azure Active Directory toegangs beoordelingen
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76aeb63feb006e6962efeb3687363f18acb59904
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97746691"
---
# <a name="manage-guest-access-with-azure-ad-access-reviews"></a>Gasttoegang beheren met Azure AD-toegangsbeoordelingen


Met Azure Active Directory (Azure AD) kunt u op eenvoudige wijze samen werking in de grenzen van de organisatie inschakelen met behulp van de [functie B2B van Azure AD](../external-identities/what-is-b2b.md). Gast gebruikers van andere tenants kunnen worden [uitgenodigd door beheerders](../external-identities/add-users-administrator.md) of door [andere gebruikers](../external-identities/what-is-b2b.md). Deze mogelijkheid is ook van toepassing op sociale identiteiten, zoals micro soft-accounts.

U kunt ook gemakkelijk controleren of gast gebruikers de juiste toegang hebben. U kunt de gasten zelf of een beslissings Maker vragen om deel te nemen aan een toegangs beoordeling en de toegang tot de gasten te bevestigen (of verklaard). De beoordelaars kunnen op basis van suggesties uit Azure AD hun mening geven over de mate waarin een gebruiker per se toegang moet krijgen. Wanneer een toegangs beoordeling is voltooid, kunt u wijzigingen aanbrengen en de toegang verwijderen voor gasten die deze niet meer nodig hebben.

> [!NOTE]
> Dit document richt zich op het beoordelen van de toegang van gast gebruikers. Als u de toegang van alle gebruikers, niet alleen gasten, wilt bekijken, raadpleegt u [gebruikers toegang beheren met toegangs beoordelingen](manage-user-access-with-access-reviews.md). Als u het lidmaatschap van gebruikers wilt beoordelen in beheerders rollen, zoals globale beheerder, raadpleegt u [een toegangs beoordeling starten in azure AD privileged Identity Management](../privileged-identity-management/pim-how-to-start-security-review.md).

## <a name="prerequisites"></a>Vereisten

- Azure AD Premium P2

Voor meer informatie, [licentie vereisten](access-reviews-overview.md#license-requirements).

## <a name="create-and-perform-an-access-review-for-guests"></a>Een toegangs beoordeling voor gasten maken en uitvoeren

Eerst moet u een van de volgende rollen toewijzen:
- globale beheerder
- Gebruikersbeheerder
- Evaluatie Eigenaar van de M365 of AAD-beveiligings groep van de groep die moet worden gecontroleerd

Ga vervolgens naar de pagina voor het beheer van [identiteiten](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) om ervoor te zorgen dat toegangs beoordelingen gereed zijn voor uw organisatie.

Azure AD biedt verschillende scenario's voor het controleren van gast gebruikers.

U kunt het volgende bekijken:

 - Een groep in azure AD die een of meer gasten als leden heeft.
 - Een toepassing die is verbonden met Azure AD en waaraan een of meer gast gebruikers zijn toegewezen. 

U kunt vervolgens beslissen of elke gast moet worden gevraagd om hun eigen toegang te controleren of om een of meer gebruikers te vragen om de toegang van elke gast te controleren.

 Deze scenario's worden behandeld in de volgende secties.

### <a name="ask-guests-to-review-their-own-membership-in-a-group"></a>Gasten vragen hun eigen lidmaatschap in een groep te bekijken

U kunt toegangs beoordelingen gebruiken om ervoor te zorgen dat gebruikers die zijn uitgenodigd en toegevoegd aan een groep, nog steeds toegang nodig hebben. U kunt gasten gemakkelijk vragen hun eigen lidmaatschap in die groep te bekijken.

1. Als u een toegangs beoordeling voor de groep wilt maken, selecteert u de optie controleren om alleen gast gebruikers leden op te nemen en die leden zelf controleren. Zie [een toegangs beoordeling van groepen of toepassingen maken](create-access-review.md)voor meer informatie.

2. Vraag elke gast om hun eigen lidmaatschap te controleren. Standaard ontvangt elke gast die een uitnodiging heeft geaccepteerd een e-mail van Azure AD met een koppeling naar de toegangs beoordeling. Azure AD bevat instructies voor gasten voor het controleren van de [toegang tot groepen of toepassingen](perform-access-review.md).

3. Wanneer alle beoordelaars feedback hebben gegeven, kunt u de toegangsbeoordeling stoppen en de wijzigingen toepassen. Zie [een toegangs beoordeling van groepen of toepassingen volt ooien](complete-access-review.md)voor meer informatie.

4. Naast de gebruikers die hun eigen behoefte voor voortdurende toegang hebben geweigerd, kunt u ook gebruikers verwijderen die niet hebben gereageerd. Niet-reagerende gebruikers die mogelijk geen e-mail meer ontvangen.

5. Als de groep niet wordt gebruikt voor toegangs beheer, kunt u ook gebruikers verwijderen die niet zijn geselecteerd om deel te nemen aan de beoordeling omdat ze hun uitnodiging niet hebben geaccepteerd. Niet geaccepteerd kan erop wijzen dat het e-mail adres van de uitgenodigde gebruiker een type fout heeft. Als een groep wordt gebruikt als distributie lijst, kunnen sommige gast gebruikers niet zijn geselecteerd om deel te nemen omdat ze contact opnemen met objecten.

### <a name="ask-a-sponsor-to-review-a-guests-membership-in-a-group"></a>Een sponsor vragen om het lidmaatschap van een gast in een groep te controleren

U kunt een sponsor vragen, zoals de eigenaar van een groep, om de nood zaak van een gast te bekijken voor het blijvend lidmaatschap van een groep.

1. Als u een toegangs beoordeling voor de groep wilt maken, selecteert u de optie controleren om alleen gast gebruikers leden op te nemen. Geef vervolgens een of meer revisoren op. Zie [een toegangs beoordeling van groepen of toepassingen maken](create-access-review.md)voor meer informatie.

2. Vraag de beoordelaars feedback te geven. Standaard ontvangen ze allemaal een e-mail van Azure AD met een koppeling naar het toegangs venster, waar ze de [toegang tot groepen of toepassingen controleren](perform-access-review.md).

3. Wanneer alle beoordelaars feedback hebben gegeven, kunt u de toegangsbeoordeling stoppen en de wijzigingen toepassen. Zie [een toegangs beoordeling van groepen of toepassingen volt ooien](complete-access-review.md)voor meer informatie.

### <a name="ask-guests-to-review-their-own-access-to-an-application"></a>Gasten vragen hun eigen toegang tot een toepassing te controleren

U kunt toegangs beoordelingen gebruiken om ervoor te zorgen dat gebruikers die zijn uitgenodigd voor een bepaalde toepassing, nog steeds toegang nodig hebben. U kunt de gasten zelf gemakkelijk vragen hun eigen behoeften voor toegang te controleren.

1. Als u een toegangs beoordeling voor de toepassing wilt maken, selecteert u de optie controleren om gasten alleen op te nemen en die gebruikers hun eigen toegang controleren. Zie [een toegangs beoordeling van groepen of toepassingen maken](create-access-review.md)voor meer informatie.

2. Vraag elke gast om hun eigen toegang tot de toepassing te controleren. Standaard ontvangt elke gast die een uitnodiging heeft geaccepteerd een e-mail van Azure AD. Dit e-mail bericht bevat een koppeling naar de toegangs beoordeling in het toegangs venster van uw organisatie. Azure AD bevat instructies voor gasten voor het controleren van de [toegang tot groepen of toepassingen](perform-access-review.md).

3. Wanneer alle beoordelaars feedback hebben gegeven, kunt u de toegangsbeoordeling stoppen en de wijzigingen toepassen. Zie [een toegangs beoordeling van groepen of toepassingen volt ooien](complete-access-review.md)voor meer informatie.

4. Naast gebruikers die hun eigen behoefte voor voortdurende toegang hebben geweigerd, kunt u ook gast gebruikers verwijderen die niet hebben gereageerd. Niet-reagerende gebruikers die mogelijk geen e-mail meer ontvangen. U kunt ook gast gebruikers verwijderen die niet zijn geselecteerd om deel te nemen, met name als ze onlangs niet zijn uitgenodigd. Deze gebruikers hebben hun uitnodiging niet geaccepteerd en hebben daarom geen toegang tot de toepassing. 

### <a name="ask-a-sponsor-to-review-a-guests-access-to-an-application"></a>Een sponsor vragen een gast toegang te geven tot een toepassing

U kunt een sponsor vragen, zoals de eigenaar van een toepassing, om de behoeften van de gast te bekijken voor verdere toegang tot de toepassing.

1. Als u een toegangs beoordeling voor de toepassing wilt maken, selecteert u de optie controleren om gasten alleen op te nemen. Geef vervolgens een of meer gebruikers op als revisoren. Zie [een toegangs beoordeling van groepen of toepassingen maken](create-access-review.md)voor meer informatie.

2. Vraag de beoordelaars feedback te geven. Standaard ontvangen ze allemaal een e-mail van Azure AD met een koppeling naar het toegangs venster, waar ze de [toegang tot groepen of toepassingen controleren](perform-access-review.md).

3. Wanneer alle beoordelaars feedback hebben gegeven, kunt u de toegangsbeoordeling stoppen en de wijzigingen toepassen. Zie [een toegangs beoordeling van groepen of toepassingen volt ooien](complete-access-review.md)voor meer informatie.

### <a name="ask-guests-to-review-their-need-for-access-in-general"></a>Stel gasten in om hun behoefte aan toegang te controleren, in het algemeen

In sommige organisaties is het mogelijk dat gasten zich niet bewust zijn van hun groepslid maatschappen.

> [!NOTE]
> Eerdere versies van de Azure Portal hebben geen beheerders toegang voor gebruikers met het User type van gast. In sommige gevallen is het mogelijk dat een beheerder in uw Directory de User type waarde van een gast heeft gewijzigd in een lid met behulp van Power shell. Als deze wijziging eerder in uw directory is opgetreden, bevat de vorige query mogelijk niet alle gast gebruikers die historische beheerders toegangs rechten hadden. In dit geval moet u de User type van de gast wijzigen of de gast in het groepslid maatschap hand matig opnemen.

1. Maak een beveiligings groep in azure AD met de gasten als leden als er al een geschikte groep bestaat. U kunt bijvoorbeeld een groep maken met een hand matig onderhouden lidmaatschap van gasten. U kunt ook een dynamische groep met een naam als ' gasten van Contoso ' maken voor gebruikers in de contoso-Tenant die de User type kenmerk waarde gast hebben.  Zorg ervoor dat de groep is voor een efficiëntere gast, en selecteer geen groep met gebruikers die lid zijn van een gebruiker, omdat gebruikers niet hoeven te worden gecontroleerd.  Houd er ook voor dat een gast gebruiker die lid is van de groep, de andere leden van de groep kan zien.

2. Als u een toegangs beoordeling voor die groep wilt maken, selecteert u de controleurs zelf als leden. Zie [een toegangs beoordeling van groepen of toepassingen maken](create-access-review.md)voor meer informatie.

3. Vraag elke gast om hun eigen lidmaatschap te controleren. Standaard ontvangt elke gast die een uitnodiging heeft geaccepteerd een e-mail van Azure AD met een koppeling naar de toegangs beoordeling in het toegangs venster van uw organisatie. Azure AD bevat instructies voor gasten voor het controleren van de [toegang tot groepen of toepassingen](perform-access-review.md).  Gasten die hun uitnodiging niet hebben geaccepteerd, worden als ' niet gemeld ' weer gegeven in de beoordelings resultaten.

4. Nadat de controleurs invoer hebben opgegeven, stopt u de toegangs beoordeling. Zie [een toegangs beoordeling van groepen of toepassingen volt ooien](complete-access-review.md)voor meer informatie.

5. Gast toegang verwijderen voor gasten die zijn afgewezen, de beoordeling niet hebben voltooid of de uitnodiging niet eerder heeft geaccepteerd. Als sommige gasten contact personen zijn die zijn geselecteerd om deel te nemen aan de beoordeling of als ze eerder geen uitnodiging hebben geaccepteerd, kunt u hun accounts uitschakelen met behulp van de Azure Portal of Power shell. Als de gast niet langer toegang nodig heeft en geen contact persoon is, kunt u het gebruikers object uit uw Directory verwijderen door de Azure Portal of Power shell te gebruiken om het gast gebruikers object te verwijderen.

## <a name="next-steps"></a>Volgende stappen

[Een toegangsbeoordeling van groepen of toepassingen maken](create-access-review.md)
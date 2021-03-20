---
title: Een toegangs beoordeling maken van een toegangs pakket in het beheer van rechten van Azure AD
description: Meer informatie over het maken van een toegangs beoordelings beleid voor rechten op het niveau van toegangs beheer in Azure Active Directory toegangs beoordelingen (preview).
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 012eefe9140703a62d7bb1074ab763191a0976cb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87798508"
---
# <a name="create-an-access-review-of-an-access-package-in-azure-ad-entitlement-management"></a>Een toegangs beoordeling maken van een toegangs pakket in het beheer van rechten van Azure AD

Om het risico van verouderde toegang te verminderen, moet u periodieke beoordelingen inschakelen van gebruikers die actieve toewijzingen hebben met een toegangs pakket in het recht van Azure AD-beheer. U kunt beoordelingen inschakelen wanneer u een nieuw toegangs pakket maakt of een bestaand toegangs pakket bewerkt. In dit artikel wordt beschreven hoe u toegangs beoordelingen van toegangs pakketten inschakelt.

## <a name="prerequisites"></a>Vereisten

Als u beoordelingen van toegangs pakketten wilt inschakelen, moet u voldoen aan de vereisten voor het maken van een toegangs pakket:
- Azure AD Premium P2
- Globale beheerder, gebruikers beheerder, catalogus eigenaar of toegangs pakket beheer

Zie [Licentievereisten](entitlement-management-overview.md#license-requirements) voor meer informatie.


## <a name="create-an-access-review-of-an-access-package"></a>Een toegangs beoordeling van een toegangs pakket maken

U kunt toegangs beoordelingen inschakelen bij [het maken van een nieuw toegangs pakket](entitlement-management-access-package-create.md) of [het bewerken van een bestaand toegangs pakket](entitlement-management-access-package-lifecycle-policy.md) beleid. Volg deze stappen om toegangs beoordelingen van een toegangs pakket in te scha kelen:

1. Open het tabblad **levenscyclus** voor een toegangs pakket en schuif omlaag naar **toegangs beoordelingen**.

1. Verplaats de schakel optie **toegangs beoordelingen vereisen** in **Ja**.

    ![De toegangs beoordeling toevoegen](./media/entitlement-management-access-reviews/access-reviews-pane.png)

1. Geef de datum op waarop de beoordelingen worden gestart na het **begin van**.

1. Stel vervolgens de frequentie van de **beoordeling** in op **jaarlijks**, **bi-jaarlijks**, **kwar taal** of **maandelijks**.
Met deze instelling bepaalt u hoe vaak toegangs beoordelingen worden uitgevoerd.

1. Stel de **duur** in om op te geven hoeveel dagen elke beoordeling van de terugkerende serie voor invoer van revisors is geopend. U kunt bijvoorbeeld een jaarlijks beoordeling plannen die begint op 1 januari en gedurende 30 dagen is geopend, zodat de revisoren tot het einde van de maand kunnen reageren.

1. Selecteer in het veld **revisoren** de optie **zelf beoordeling** als u wilt dat gebruikers hun eigen toegangs beoordeling uitvoeren of **specifieke revisor (en)** selecteren als u een revisor wilt aanwijzen.

    ![Revisoren toevoegen selecteren](./media/entitlement-management-access-reviews/access-reviews-add-reviewer.png)

1. Als u **specifieke revisoren (en)** hebt geselecteerd, geeft u op welke gebruikers de toegangs beoordeling moeten uitvoeren:
    1. Selecteer **revisoren toevoegen**.
    1. Zoek in het deel venster **revisoren selecteren** naar de gebruiker (s) die u als revisor wilt maken en selecteer deze.
    1. Wanneer u uw revisor (en) hebt geselecteerd, klikt u op de knop **selecteren** .

    ![De revisoren opgeven](./media/entitlement-management-access-reviews/access-reviews-select-reviewer.png)

1. Klik op **controleren + maken** als u een nieuw toegangs pakket of **Update** maakt als u een toegangs pakket bewerkt, onder aan de pagina.

## <a name="view-the-status-of-the-access-review"></a>Bekijk de status van de toegangs beoordeling

Na de begin datum wordt een toegangs beoordeling weer gegeven in de sectie **toegangs beoordelingen** . Volg deze stappen om de status van een toegangs beoordeling weer te geven:

1. In **Identity governance** klikt u op **toegangs pakketten** en selecteert u vervolgens het toegangs pakket met de status van de toegangs beoordeling die u wilt controleren.   

1. Klik op **toegangs beoordelingen** in het linkermenu zodra u het overzicht van het toegangs pakket hebt geopend.
    
    ![Toegangs beoordelingen selecteren](./media/entitlement-management-access-reviews/access-review-status-access-package-overview.png)

1. Er wordt een lijst weer gegeven met daarin alle beleids regels waaraan toegangs beoordelingen zijn gekoppeld. Klik op beoordeling om het rapport weer te geven.

    ![Lijst met toegangs beoordelingen](./media/entitlement-management-access-reviews/access-review-status-select-access-reviews.png)
   
1. Wanneer u het rapport bekijkt, wordt het aantal gebruikers weer gegeven dat wordt gecontroleerd en de acties die door de revisor worden uitgevoerd.

    ![Beoordelings status weer geven](./media/entitlement-management-access-reviews/access-review-status.png)
 

## <a name="access-reviews-email-notifications"></a>E-mail meldingen voor toegangs beoordelingen
U kunt revisoren aanwijzen of gebruikers kunnen hun toegang zelf controleren. Standaard stuurt Azure AD een e-mail naar revisors of eigen revisoren kort nadat de controle is gestart.

Het e-mail bericht bevat instructies over het controleren van de toegang tot toegangs pakketten. Als de controle is voor gebruikers om hun toegang te controleren, geeft u de instructies weer voor het uitvoeren van een zelf beoordeling van de toegangs pakketten.
  
Als u gast gebruikers hebt toegewezen als revisoren en ze hun Azure AD-gast uitnodiging niet hebben geaccepteerd, ontvangen ze geen e-mail berichten van Azure AD-toegangs Beoordelingen. Ze moeten eerst de uitnodiging accepteren en een account maken met Azure AD voordat ze de e-mail berichten kunnen ontvangen. 

## <a name="next-steps"></a>Volgende stappen

- [Toegang tot toegangs pakketten controleren](entitlement-management-access-reviews-review-access.md)
- [Zelf beoordeling van toegangs pakketten](entitlement-management-access-reviews-self-review.md)

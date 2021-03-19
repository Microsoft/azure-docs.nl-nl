---
title: Een groep toevoegen aan of verwijderen uit een andere groep-Azure AD
description: Instructies voor het toevoegen of verwijderen van een groep uit een andere groep met behulp van Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 10/19/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: cbd7eecd80b528b1d20d016d51c985bc24d1301d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92371797"
---
# <a name="add-or-remove-a-group-from-another-group-using-azure-active-directory"></a>Een groep toevoegen aan of verwijderen uit een andere groep met behulp van Azure Active Directory
Dit artikel helpt u bij het toevoegen en verwijderen van een groep uit een andere groep met behulp van Azure Active Directory.

>[!Note]
>Als u probeert de bovenliggende groep te verwijderen, raadpleegt u [een groep en de bijbehorende leden bijwerken of verwijderen](active-directory-groups-delete-group.md).

## <a name="add-a-group-to-another-group"></a>Een groep toevoegen aan een andere groep
U kunt een bestaande beveiligings groep toevoegen aan een andere bestaande beveiligings groep (ook wel geneste groepen genoemd), een leden groep (subgroep) en een bovenliggende groep maken. De leden groep neemt de kenmerken en eigenschappen van de bovenliggende groep over en bespaart u configuratie tijd.

>[!Important]
>We bieden momenteel geen ondersteuning voor:<ul><li>Groepen toevoegen aan een groep die is gesynchroniseerd met on-premises Active Directory.</li><li>Beveiligings groepen worden toegevoegd aan Microsoft 365 groepen.</li><li>Microsoft 365 groepen toevoegen aan beveiligings groepen of andere Microsoft 365 groepen.</li><li>Apps toewijzen aan geneste groepen.</li><li>Licenties Toep assen op geneste groepen.</li><li>Distributie groepen toevoegen in geneste scenario's.</li></ul>

### <a name="to-add-a-group-as-a-member-of-another-group"></a>Een groep toevoegen als lid van een andere groep

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met het account van een globale administrator voor de map.

2. Selecteer **Azure Active Directory** en selecteer vervolgens **groepen**.

3. Op de pagina **groepen-alle groepen** zoekt en selecteert u de groep die lid moet worden van een andere groep. Voor deze oefening gebruiken we het **MDM-beleid-westelijke** groep.

    >[!Note]
    >U kunt uw groep als lid toevoegen aan slechts één groep per keer. Daarnaast filtert het vak **groep selecteren** op basis van het vergelijken van uw invoer op een deel van een gebruiker of apparaatnaam. Joker tekens worden echter niet ondersteund.

    ![De pagina groepen-alle groepen met MDM-beleid-westelijke groep geselecteerd](media/active-directory-groups-membership-azure-portal/group-all-groups-screen.png)

4. Selecteer op de pagina **MDM-beleid-West-groepslid maatschappen** de **optie groepslid maatschappen**, selecteer **toevoegen**, zoek de groep waarvan u de groep lid wilt maken en kies vervolgens **selecteren**. Voor deze oefening gebruiken we het **MDM-beleid-alle organisatie** groep.

    Het **MDM-beleid-westelijke** groep is nu lid van het **MDM-beleid-alle organisatie** groep, waarbij alle eigenschappen en configuratie van het MDM-beleid-alle organisatie groep worden overgenomen.

    ![Een groepslid maatschap maken door een groep toe te voegen aan een andere groep](media/active-directory-groups-membership-azure-portal/group-add-group-membership.png)

5. Raadpleeg de pagina **MDM-beleid-West-groepslid maatschappen** voor een overzicht van de relatie tussen de groep en het lid.

6. Voor een meer gedetailleerde weer gave van de relatie groep en lid selecteert u de groeps naam (**MDM-beleid-alle org**) en bekijkt u de details van het **MDM-beleid-West-** pagina.

## <a name="remove-a-group-from-another-group"></a>Een groep uit een andere groep verwijderen
U kunt een bestaande beveiligings groep verwijderen uit een andere beveiligings groep. Het verwijderen van de groep verwijdert echter ook alle overgenomen kenmerken en eigenschappen van de leden.

### <a name="to-remove-a-member-group-from-another-group"></a>Een leden groep verwijderen uit een andere groep
1. Op de pagina **groepen-alle groepen** zoekt en selecteert u de groep die moet worden verwijderd als lid van een andere groep. Voor deze oefening gebruiken we het **MDM-beleid-westelijke** groep opnieuw.

2. Selecteer op de pagina **MDM-beleid-West-overzicht** de optie **groepslid maatschappen**.

3. Selecteer de groep **MDM-beleid-alle organisatie** op de pagina **MDM-beleid-West-groepslid maatschappen** en selecteer vervolgens **verwijderen** in het **MDM-beleid-westelijke** pagina Details.

    ![Pagina groepslid maatschap met zowel het lid als de groeps gegevens](media/active-directory-groups-membership-azure-portal/group-membership-remove.png)

## <a name="additional-information"></a>Aanvullende informatie
Deze artikelen bevatten aanvullende informatie over Azure Active Directory.

- [Groepen en leden weergeven](active-directory-groups-view-azure-portal.md)

- [Een basisgroep maken en leden toevoegen](active-directory-groups-create-azure-portal.md)

- [Leden toevoegen aan of verwijderen uit een groep](active-directory-groups-members-azure-portal.md)

- [Instellingen van uw groep bewerken](active-directory-groups-settings-azure-portal.md)

- [Toegang tot SaaS-toepassingen beheren met behulp van een groep](../enterprise-users/groups-saasapps.md)

- [Scenario's, beperkingen en bekende problemen met behulp van groepen voor het beheren van licenties in Azure Active Directory](../enterprise-users/licensing-group-advanced.md#limitations-and-known-issues)

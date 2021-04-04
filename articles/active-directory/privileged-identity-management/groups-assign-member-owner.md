---
title: In aanmerking komende eigen aren en leden toewijzen voor geprivilegieerde toegangs groepen-Azure Active Directory
description: Meer informatie over het toewijzen van in aanmerking komende eigen aren of leden van een door een functie toewijs bare groep in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 08/18/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48c4473cfafce1215219251c47bce1d5730645fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91534417"
---
# <a name="assign-eligibility-for-a-privileged-access-group-preview-in-privileged-identity-management"></a>Wijs geschiktheid toe voor een bevoegde toegangs groep (preview) in Privileged Identity Management

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) kan u helpen bij het beheren van de geschiktheid en activering van toewijzingen aan geprivilegieerde toegangs groepen in azure AD. U kunt de geschiktheid toewijzen aan leden of eigen aars van de groep.

>[!NOTE]
>Elke gebruiker die in aanmerking komt voor lidmaatschap van of eigendom van een bevoegde toegangs groep, moet een Azure AD Premium P2-licentie hebben. Zie [licentie vereisten voor het gebruik van privileged Identity Management](subscription-requirements.md)voor meer informatie.

## <a name="assign-an-owner-or-member-of-a-group"></a>Een eigenaar of lid van een groep toewijzen

Volg deze stappen om een gebruiker in aanmerking te laten komen als lid of eigenaar van een bevoegde toegangs groep.

1. [Meld u aan bij Azure AD](https://aad.portal.azure.com) met de machtigingen van de globale beheerder of groeps eigenaar.
1. Selecteer **groepen** en selecteer vervolgens de groep die u wilt beheren. U kunt de lijst zoeken of filteren.

    ![een door een functie toewijs bare groep zoeken om te beheren in PIM](./media/groups-assign-member-owner/groups-list-in-azure-ad.png)

1. Open de groep en selecteer **bevoorrechte toegang (preview)**.

    ![De Privileged Identity Management-ervaring openen](./media/groups-assign-member-owner/groups-discover-groups.png)

1. Selecteer **toewijzingen toevoegen**.

    ![Deel venster nieuwe toewijzing](./media/groups-assign-member-owner/groups-add-assignment.png)

1. Selecteer de leden of eigen aren die u in aanmerking wilt komen voor de groep privileged Access.

    ![Scherm opname waarin de pagina toewijzingen toevoegen wordt weer gegeven met het deel venster een lid of groep selecteren geopend en de knop selecteren gemarkeerd.](./media/groups-assign-member-owner/add-assignments.png)

1. Selecteer **volgende** om de duur van het lidmaatschap of de eigendom in te stellen.

    ![Een lid of groeps deel venster selecteren](./media/groups-assign-member-owner/assignment-duration.png)

1. Selecteer in **aanmerking komend** of **actief** in de lijst **toewijzings type** . Geprivilegieerde toegangs groepen bieden twee verschillende toewijzings typen:

    - Voor **in aanmerking komende** toewijzingen moet het lid van de rol een actie uitvoeren om de rol te gebruiken. Acties kunnen bijvoorbeeld een meervoudige verificatiecontrole, het opgeven van een zakelijke reden of het vragen om toestemming bij aangewezen fiatteurs zijn.

    - Voor **actieve** toewijzingen hoeft het lid geen actie te ondernemen om de rol te gebruiken. Aan leden die als actief zijn toegewezen, zijn de bevoegdheden altijd toegewezen aan de rol.

1. Als de toewijzing permanent moet zijn (permanent in aanmerking komend of permanent toegewezen), schakelt u het selectie vakje **permanent** in. Afhankelijk van de instellingen van uw organisatie wordt het selectie vakje mogelijk niet weer gegeven of kan het niet worden bewerkt.

1. Wanneer u klaar bent, selecteert u **toewijzen**.

1. Als u de nieuwe roltoewijzing wilt maken, selecteert u **toevoegen**. Er wordt een melding van de status weer gegeven.

    ![Nieuwe toewijzing-melding](./media/groups-assign-member-owner/groups-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Een bestaande roltoewijzing bijwerken of verwijderen

Volg deze stappen om een bestaande roltoewijzing bij te werken of te verwijderen.

1. [Meld u aan bij Azure AD](https://aad.portal.azure.com) met de machtigingen van de globale beheerder of groeps eigenaar.
1. Selecteer **groepen** en selecteer vervolgens de groep die u wilt beheren. U kunt de lijst zoeken of filteren.

    ![een door een functie toewijs bare groep zoeken om te beheren in PIM](./media/groups-assign-member-owner/groups-list-in-azure-ad.png)

1. Open de groep en selecteer **bevoorrechte toegang (preview)**.

    ![De Privileged Identity Management-ervaring openen](./media/groups-assign-member-owner/groups-discover-groups.png)

1. Selecteer de rol die u wilt bijwerken of verwijderen.

1. Zoek naar de roltoewijzing op de tabbladen **in aanmerking komende rollen** of **actieve rollen** .

    ![Roltoewijzing bijwerken of verwijderen](./media/groups-assign-member-owner/groups-bring-under-management.png)

1. Selecteer **bijwerken** of **verwijderen** om de roltoewijzing bij te werken of te verwijderen.

    Zie [Azure-resource rollen uitbreiden of vernieuwen in privileged Identity Management](pim-resource-roles-renew-extend.md)voor meer informatie over het uitbreiden van een roltoewijzing.

## <a name="next-steps"></a>Volgende stappen

- [Azure-resource rollen in Privileged Identity Management uitbreiden of vernieuwen](pim-resource-roles-renew-extend.md)
- [Instellingen voor Azure-resource-rollen configureren in Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Azure AD-rollen toewijzen in Privileged Identity Management](pim-how-to-add-role-to-user.md)

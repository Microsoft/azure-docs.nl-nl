---
title: Toegang verlenen voor het beheren van PIM-Azure Active Directory | Microsoft Docs
description: Meer informatie over het verlenen van toegang aan andere instanties om Azure AD Privileged Identity Management (PIM) te beheren.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 08/06/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: fba46ee5632f7411c433e4bba29201c59c552f21
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94835200"
---
# <a name="delegate-access-to-privileged-identity-management"></a>Toegang tot Privileged Identity Management delegeren

Als u de toegang tot Privileged Identity Management (PIM) wilt delegeren, kan een globale beheerder andere gebruikers toewijzen aan de rol van beheerdersrol voor bevoegde rollen. Beveiligings beheerders en beveiligings lezers hebben standaard alleen-lezen toegang tot Privileged Identity Management. Als u toegang wilt verlenen aan Privileged Identity Management, kan de eerste gebruiker andere gebruikers toewijzen aan de rol van **beheerdersrol voor bevoegde rollen** . De rol beheerdersrol is vereist voor het beheren van Azure AD-rollen. Beheerders machtigingen voor geprivilegieerde rollen zijn niet vereist voor het beheren van instellingen voor Azure-resources.

> [!NOTE]
> Voor het beheren van Privileged Identity Management is Azure AD-Multi-Factor Authentication vereist. Omdat micro soft-accounts zich niet kunnen registreren voor Azure AD Multi-Factor Authentication, kan een gebruiker die zich aanmeldt met een Microsoft-account geen toegang krijgen tot Privileged Identity Management.

Zorg ervoor dat er altijd ten minste twee gebruikers in een rol voor geprivilegieerde rol beschikken, voor het geval één gebruiker is vergrendeld of het account wordt verwijderd.

## <a name="delegate-access-to-manage-pim"></a>Toegang delegeren om PIM te beheren

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Open **privileged Identity Management** in azure AD.

1. Selecteer **Azure AD-rollen**.

1. Selecteer **Rollen**.

    ![Privileged Identity Management Azure AD-rollen-rollen](./media/pim-how-to-give-access-to-pim/pim-directory-roles-roles.png)

1. Selecteer de beheerdersrol **privileged Role** om de pagina leden te openen.

    ![Beheerder van geprivilegieerde rol-leden](./media/pim-how-to-give-access-to-pim/pim-pra-members.png)

1. Selecteer **lid toevoegen**  om het deel venster **beheerde leden toevoegen** te openen.

1. Selecteer **leden selecteren** om het deel venster **leden selecteren** te openen.

    ![Beheerder van geprivilegieerde rol-leden selecteren](./media/pim-how-to-give-access-to-pim/pim-pra-select-members.png)

1. Selecteer een lid en klik vervolgens op **selecteren**.

1. Selecteer **OK** om het lid in aanmerking te laten komen voor de beheerdersrol van de **beveiligingsrol** .

    Wanneer u een nieuwe rol toewijst aan iemand in Privileged Identity Management, worden deze automatisch geconfigureerd als **in aanmerking komend** om de rol te activeren.

1. Als u het lid permanent wilt maken, selecteert u de gebruiker in de lijst Administrator leden van geprivilegieerde rol.

1. Selecteer **meer** en vervolgens **permanent maken** om de toewijzing permanent te maken.

    ![Beheerder van geprivilegieerde rol: permanent maken](./media/pim-how-to-give-access-to-pim/pim-pra-make-permanent.png)

1. Stuur de gebruiker een koppeling om te [beginnen met privileged Identity Management](pim-getting-started.md).

## <a name="remove-access-to-manage-pim"></a>Toegang tot het beheer van PIM verwijderen

Zorg er altijd voor dat er nog mini maal twee gebruikers aan zijn toegewezen voordat u iemand verwijdert uit de beheerderrol.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Open **Azure AD privileged Identity Management**.

1. Selecteer **Azure AD-rollen**.

1. Selecteer **Rollen**.

1. Selecteer de beheerdersrol **privileged Role** om de pagina leden te openen.

1. Schakel het selectie vakje in naast de gebruiker die u wilt verwijderen en selecteer **lid verwijderen**.

    ![Beheerder van geprivilegieerde rol-lid verwijderen](./media/pim-how-to-give-access-to-pim/pim-pra-remove-member.png)

1. Als u wordt gevraagd om te bevestigen dat u het lid uit de rol wilt verwijderen, selecteert u **Ja**.

## <a name="next-steps"></a>Volgende stappen

- [Aan de slag met Privileged Identity Management](pim-getting-started.md)

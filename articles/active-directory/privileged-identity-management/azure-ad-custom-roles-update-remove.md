---
title: Aangepaste Azure AD-functie-Privileged Identity Management (PIM) bijwerken of verwijderen
description: Een Privileged Identity Management voor aangepaste rollen van Azure AD bijwerken of verwijderen (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a35442dd8af1cd4acf22de453c8d10460e1e39f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92371525"
---
# <a name="update-or-remove-an-assigned-azure-ad-custom-role-in-privileged-identity-management"></a>Een toegewezen Azure AD-aangepaste rol bijwerken of verwijderen in Privileged Identity Management

In dit artikel leest u hoe u Privileged Identity Management (PIM) kunt gebruiken voor het bijwerken of verwijderen van just-in-time-en tijdgebonden toewijzing aan aangepaste rollen die zijn gemaakt voor toepassings beheer in de beheer ervaring van Azure Active Directory (Azure AD). 

- Zie [aangepaste beheerders rollen in azure Active Directory (preview)](../roles/custom-overview.md)voor meer informatie over het maken van aangepaste rollen voor het delegeren van toepassings beheer in azure AD. 
- Als u nog geen Privileged Identity Management hebt gebruikt, kunt u aan de slag [met privileged Identity Management](pim-getting-started.md)meer informatie.

> [!NOTE]
> Aangepaste Azure AD-rollen worden tijdens de preview-fase niet geïntegreerd met de ingebouwde Directory rollen. Zodra de mogelijkheid algemeen beschikbaar is, wordt het beheer van rollen uitgevoerd in de ingebouwde functie-ervaring. Als u de volgende banner ziet, moeten deze rollen worden beheerd [in de ingebouwde functie-ervaring](pim-how-to-add-role-to-user.md) en dit artikel is niet van toepassing:
>
> [![Selecteer Azure AD > Privileged Identity Management.](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

## <a name="update-or-remove-an-assignment"></a>Een toewijzing bijwerken of verwijderen

Volg deze stappen om een bestaande toewijzing van een aangepaste rol bij te werken of te verwijderen.

1. Meld u aan bij [privileged Identity Management](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) in het Azure Portal met een gebruikers account dat is toegewezen aan de rol van beheerdersrol voor bevoegde rol.
1. Selecteer **aangepaste Azure AD-rollen (preview-versie)**.

    ![Selecteer voor beeld van aangepaste rollen van Azure AD om de in aanmerking komende roltoewijzingen te bekijken](./media/azure-ad-custom-roles-assign/view-custom.png)

1. Selecteer **rollen** om een lijst met **toewijzingen** weer te geven met aangepaste rollen voor Azure AD-toepassingen.

    ![Rollen selecteren Zie de lijst met in aanmerking komende roltoewijzingen](./media/azure-ad-custom-roles-update-remove/assignments-list.png)

1. Selecteer de rol die u wilt bijwerken of verwijderen.
1. Zoek naar de roltoewijzing op de tabbladen **in aanmerking komende rollen** of **actieve rollen** .
1. Selecteer **bijwerken** of **verwijderen** om de roltoewijzing bij te werken of te verwijderen.

    ![Selecteer verwijderen of bijwerken in de in aanmerking komende roltoewijzing](./media/azure-ad-custom-roles-update-remove/remove-update.png)

## <a name="next-steps"></a>Volgende stappen

- [Een aangepaste Azure AD-rol activeren](azure-ad-custom-roles-assign.md)
- [Een aangepaste Azure AD-rol toewijzen](azure-ad-custom-roles-assign.md)
- [Een aangepaste functie toewijzing voor Azure AD configureren](azure-ad-custom-roles-configure.md)

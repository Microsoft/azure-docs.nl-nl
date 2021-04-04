---
title: Aangepaste Azure AD-rol-Privileged Identity Management (PIM) activeren
description: Een aangepaste Azure AD-rol activeren voor toewijzings Privileged Identity Management (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
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
ms.openlocfilehash: 6c0d98641f8e2040de8350b7dd0231c2e7c889c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92371610"
---
# <a name="activate-an-azure-ad-custom-role-in-privileged-identity-management"></a>Een aangepaste Azure AD-rol activeren in Privileged Identity Management

Privileged Identity Management in Azure Active Directory (Azure AD) biedt nu ondersteuning voor Just-in-time-en tijdgebonden toewijzing aan aangepaste rollen die zijn gemaakt voor toepassings beheer in de beheer ervaring voor identiteits-en toegangs beheer. Zie [aangepaste beheerders rollen in azure Active Directory (preview)](../roles/custom-overview.md)voor meer informatie over het maken van aangepaste rollen voor het delegeren van toepassings beheer in azure AD.

> [!NOTE]
> Aangepaste Azure AD-rollen worden tijdens de preview-fase niet geïntegreerd met de ingebouwde Directory rollen. Zodra de mogelijkheid algemeen beschikbaar is, wordt het beheer van rollen uitgevoerd in de ingebouwde functie-ervaring. Als u de volgende banner ziet, moeten deze rollen worden beheerd [in de ingebouwde functie-ervaring](pim-how-to-activate-role.md) en dit artikel is niet van toepassing:
>
> :::image type="content" source="media/pim-how-to-add-role-to-user/pim-new-version.png" alt-text="Selecteer Privileged Identity Management in azure AD." lightbox="media/pim-how-to-add-role-to-user/pim-new-version.png":::

## <a name="activate-a-role"></a>Een rol activeren

Wanneer u een aangepaste Azure AD-rol moet activeren, kunt u de activering aanvragen door de optie mijn rollen navigatie in Privileged Identity Management te selecteren.

1. Meld u aan bij [de Azure Portal](https://portal.azure.com).
1. Open Azure AD- [privileged Identity Management](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart).

1. Selecteer **aangepaste Azure AD-rollen** om een lijst weer te geven met de aangepaste roltoewijzingen in azure AD.

   ![Bekijk de lijst met aangepaste roltoewijzingen die in aanmerking komen voor Azure AD](./media/azure-ad-custom-roles-activate/view-preview-roles.png)

> [!Note] 
> Voordat u een rol toewijst, moet u een rol maken/configureren. Zie [aangepaste rollen van Azure AD configureren in privileged Identity Management](azure-ad-custom-roles-configure.md)voor meer informatie over het configureren van aangepaste Aad-rollen.

1. Zoek op de pagina **aangepaste rollen van Azure AD (preview)** de toewijzing die u nodig hebt.
1. Selecteer **uw rol activeren** om de pagina **activeren** te openen.
1. Als voor uw rol multi-factor Authentication is vereist, selecteert u **uw identiteit verifiëren voordat u doorgaat**. U hoeft slechts één keer per sessie te verifiëren.
1. Selecteer **Mijn identiteit verifiëren** en volg de instructies om een aanvullende beveiligings verificatie te geven.
1. Als u een aangepast toepassings bereik wilt opgeven, selecteert u **bereik** om het deel venster filter te openen. U moet toegang tot een rol aanvragen op het minimale bereik dat nodig is. Als uw toewijzing zich in een toepassings bereik bevindt, kunt u alleen activeren bij dat bereik.

   ![Een Azure AD-resource bereik toewijzen aan de roltoewijzing](./media/azure-ad-custom-roles-activate/assign-scope.png)

1. Geef indien nodig een aangepaste begin tijd voor de activering op. Wanneer u dit gebruikt, wordt het rollidmaatschap geactiveerd op het opgegeven tijdstip.
1. Voer in het vak **reden** de reden voor de activerings aanvraag in. Deze kunnen worden opgegeven, of niet in de instelling van de rol.
1. Selecteer **Activate**.

Als voor de rol geen goed keuring is vereist, wordt deze geactiveerd volgens uw instellingen en wordt deze toegevoegd aan de lijst met actieve rollen. Als u de geactiveerde functie wilt gebruiken, begint u met de stappen in [een aangepaste Azure AD-rol toewijzen in privileged Identity Management](azure-ad-custom-roles-assign.md).

Als voor de rol goed keuring vereist is, ontvangt u een melding van Azure dat de aanvraag goed keuring in behandeling is.

## <a name="next-steps"></a>Volgende stappen

- [Een aangepaste Azure AD-rol toewijzen](azure-ad-custom-roles-assign.md)
- [Een aangepaste gebruikersrol toewijzing van Azure AD verwijderen of bijwerken](azure-ad-custom-roles-update-remove.md)
- [Een aangepaste functie toewijzing voor Azure AD configureren](azure-ad-custom-roles-configure.md)
- [Roldefinities in azure AD](../roles/permissions-reference.md)

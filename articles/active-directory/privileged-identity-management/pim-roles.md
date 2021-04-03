---
title: Rollen die u niet kunt beheren in Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Hierin worden de functies beschreven die u niet kunt beheren in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 05/11/2020
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: c72587c5486ed61215fd20c215a1dd194f4b7bc4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92372409"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>Rollen die u niet kunt beheren in Privileged Identity Management

Met Azure Active Directory (Azure AD) Privileged Identity Management (PIM) kunt u alle [Azure AD-rollen](../roles/permissions-reference.md) en alle [Azure-rollen](../../role-based-access-control/built-in-roles.md)beheren. Azure-rollen kunnen ook uw aangepaste rollen bevatten die zijn gekoppeld aan uw beheer groepen, abonnementen, resource groepen en resources. Er zijn echter enkele rollen die u niet kunt beheren. In dit artikel worden de functies beschreven die u niet kunt beheren in Privileged Identity Management.

## <a name="classic-subscription-administrator-roles"></a>Klassieke abonnementsbeheerdersrollen

U kunt de volgende klassieke abonnements beheerders rollen niet beheren in Privileged Identity Management:

- Accountbeheerder
- Servicebeheerder
- Medebeheerder

Zie voor meer informatie over de beheerders rollen van klassieke abonnementen [klassieke abonnements beheerders rollen, Azure-rollen en Azure AD-beheerders rollen](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-microsoft-365-admin-roles"></a>Hoe zit het met Microsoft 365 beheerders rollen?

We ondersteunen alle Microsoft 365 rollen in de Azure AD-functies en de portal-ervaring voor beheerders, zoals Exchange Administrator en share point Administrator, maar bieden geen ondersteuning voor specifieke rollen binnen Exchange RBAC of share point RBAC. Zie [Microsoft 365 admin roles](/office365/admin/add-users/about-admin-roles)(Engelstalig) voor meer informatie over deze Microsoft 365 Services.

> [!NOTE]
> Gebruikers die in aanmerking komen voor de share point-beheerdersrol, de rol van het apparaat en alle rollen die toegang proberen te krijgen tot het micro soft Security and Compliance Center kunnen vertragingen van een paar uur ondervinden na het activeren van hun rol. We werken samen met die teams om de problemen op te lossen.

## <a name="next-steps"></a>Volgende stappen

- [Azure AD-rollen toewijzen in Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Azure-resource rollen toewijzen in Privileged Identity Management](pim-resource-roles-assign-roles.md)
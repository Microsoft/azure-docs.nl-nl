---
title: Een groep identificeren die moet worden beheerd in Privileged Identity Management-Azure AD | Microsoft Docs
description: Meer informatie over het opheffen van met rollen toewijs bare groepen om te beheren als geprivilegieerde toegangs groepen in Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 08/03/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c9e2580d2a88fbbab755f0c3df2f923bdc45548
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101688345"
---
# <a name="bring-privileged-access-groups-preview-into-privileged-identity-management"></a>Privileged Access groups (preview) in Privileged Identity Management opnemen

In Azure Active Directory (Azure AD) kunt u ingebouwde rollen van Azure AD toewijzen aan Cloud groepen om te vereenvoudigen hoe u roltoewijzingen beheert. Als u Azure AD-rollen wilt beveiligen en de toegang wilt beveiligen, kunt u Privileged Identity Management (PIM) nu gebruiken voor het beheren van just-in-time-toegang voor leden of eigen aren van deze groepen. Voor het beheren van een functie-toewijs bare groep voor Azure AD als een bevoegde toegangs groep in Privileged Identity Management, moet u deze onder beheer in PIM plaatsen.

## <a name="identify-groups-to-manage"></a>Te beheren groepen identificeren

U kunt een door een functie toewijs bare groep maken in azure AD, zoals wordt beschreven in [een functie groep maken die kan worden toegewezen in azure Active Directory](../roles/groups-create-eligible.md). U bent een eigenaar van de groep om deze onder beheer te brengen met Privileged Identity Management.

1. [Meld u aan bij Azure AD](https://aad.portal.azure.com) met machtigingen voor de rol Administrator voor geprivilegieerd.
1. Selecteer **groepen** en selecteer vervolgens de groep die u wilt beheren in Pim. U kunt de lijst zoeken en filteren.

    ![een door een functie toewijs bare groep zoeken om te beheren in PIM](./media/groups-discover-groups/groups-list-in-azure-ad.png)

1. Open de groep en selecteer **bevoorrechte toegang (preview)**.

    ![De Privileged Identity Management-ervaring openen](./media/groups-discover-groups/groups-discover-groups.png)

1. Begin met het beheren van toewijzingen in PIM.

    ![Toewijzingen in Privileged Identity Management beheren](./media/groups-discover-groups/groups-bring-under-management.png)

> [!NOTE]
> Zodra een privileged Access-groep wordt beheerd, kan deze niet buiten het beheer worden gehaald. Zo voor komt u dat een andere resource beheerder Privileged Identity Management-instellingen kan verwijderen.
>

> [!IMPORTANT]
> Als een privileged Access-groep wordt verwijderd uit Azure Active Directory, kan het tot 24 uur duren voordat de groep wordt verwijderd uit de Blade geprivilegieerde toegangs groepen (preview-versie). 
>


## <a name="next-steps"></a>Volgende stappen

- [De toewijzingen van groepen met uitgebreide toegang configureren in Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Geprivilegieerde toegangs groepen toewijzen in Privileged Identity Management](pim-resource-roles-assign-roles.md)

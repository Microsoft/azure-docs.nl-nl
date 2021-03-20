---
title: Controle rapport weer geven voor gemachtigde toegangs groeps toewijzingen in Privileged Identity Management (PIM)-Azure AD | Microsoft Docs
description: Bekijk de activiteit en de controle geschiedenis voor gemachtigde toegangs groeps toewijzingen in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: pim
ms.date: 07/27/2020
ms.author: curtand
ms.reviewer: shaunliu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 94dd5c2579eb7ce96dea70c6354df8ec84125bd9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88141666"
---
# <a name="audit-activity-history-for-privileged-access-group-assignments-preview-in-privileged-identity-management"></a>Geschiedenis van de controle activiteit voor geprivilegieerde toewijzingen van de toegangs groep (preview) in Privileged Identity Management

Met Privileged Identity Management (PIM) kunt u activiteiten, activeringen en controle geschiedenis weer geven voor leden en eigen aren van gebruikers met verhoogde bevoegdheden in uw Azure Active Directory Azure AD-organisatie.

> [!NOTE]
> Als uw organisatie uitbestede beheer functies heeft voor een service provider die gebruikmaakt van [Azure delegated resource management](../../lighthouse/concepts/azure-delegated-resource-management.md), worden roltoewijzingen die door die service provider worden toegestaan, hier niet weer gegeven.

Volg deze stappen om de controle geschiedenis voor geprivilegieerde toegangs groepen weer te geven.

## <a name="view-resource-audit-history"></a>Controle geschiedenis van resources weer geven

**Resource controle** geeft u een overzicht van alle activiteiten die zijn gekoppeld aan uw geprivilegieerde toegangs groepen.

1. Open **Azure AD privileged Identity Management**.

1. Selecteer **geprivilegieerde toegangs groepen (preview-versie)**.

1. Selecteer de groep met bevoegde toegang waarvoor u de controle geschiedenis wilt weer geven.

1. Onder **activiteit** selecteert u **resource audit**.

1. Filter de geschiedenis met een vooraf gedefinieerde datum of aangepast bereik.

    ![Resource audit lijst met filters](media/groups-audit/groups-resource-audit.png)

## <a name="view-my-audit"></a>Mijn controle weer geven

Met **mijn controle** kunt u uw activiteiten voor persoonlijke rollen weer geven voor een bevoegde toegangs groep.

1. Open **Azure AD privileged Identity Management**.

1. Selecteer **geprivilegieerde toegangs groepen (preview-versie)**.

1. Selecteer de groep met bevoegde toegang waarvoor u de controle geschiedenis wilt weer geven.

1. Onder **activiteit** selecteert u **mijn audit**.

1. Filter de geschiedenis met een vooraf gedefinieerde datum of aangepast bereik.

    ![Controle lijst voor de huidige gebruiker](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="next-steps"></a>Volgende stappen

- [Groepslid maatschap en eigendom toewijzen in Privileged Identity Management](groups-assign-member-owner.md)
- [Aanvragen voor geprivilegieerde toegangs groepen in PIM goed keuren of weigeren](groups-approval-workflow.md)
- [De controle geschiedenis voor Azure AD-rollen in PIM weer geven](groups-audit.md)

---
title: Activerings aanvragen voor groeps leden en eigen aren in Privileged Identity Management-Azure AD goed keuren
description: Meer informatie over het goed keuren of weigeren van aanvragen voor door rollen toewijs bare groepen in Azure AD Privileged Identity Management (PIM).
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
ms.date: 07/27/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a3b6dad33ce678478a201dc8b7b612f4b8507c0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91536984"
---
# <a name="approve-activation-requests-for-privileged-access-group-members-and-owners-preview"></a>Activerings aanvragen voor leden en eigen aren van bevoegde toegangs groep goed keuren (preview)

Met Privileged Identity Management (PIM) in Azure Active Directory (Azure AD) kunt u leden en eigen aren van bevoegde toegangs groepen configureren om goed keuring voor activering te vereisen en gebruikers of groepen uit uw Azure AD-organisatie kiezen als gedelegeerde goed keurders. We raden u aan twee of meer goed keurders voor elke groep te selecteren om de werk belasting voor de beheerder van de bevoegde rol te verminderen. Gedelegeerde goed keurders hebben 24 uur nodig om aanvragen goed te keuren. Als een aanvraag niet binnen 24 uur wordt goedgekeurd, moet de in aanmerking komende gebruiker een nieuwe aanvraag opnieuw indienen. Het venster goedkeurings tijd 24 uur kan niet worden geconfigureerd.

Volg de stappen in dit artikel voor het goed keuren of weigeren van aanvragen voor Azure-resource rollen.

## <a name="view-pending-requests"></a>Aanvragen in behandeling weergeven

Als gedelegeerde fiatteur ontvangt u een e-mail melding wanneer een Azure resource Role-aanvraag in afwachting is van uw goed keuring. U kunt openstaande aanvragen in Privileged Identity Management weer geven.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Open **Azure AD privileged Identity Management**.

1. Selecteer **aanvragen goed keuren**.

    ![Aanvragen goed keuren-Azure-bronnen pagina waarin de aanvraag voor beoordeling wordt weer gegeven](./media/groups-approval-workflow/groups-select-request.png)

    In de sectie **aanvragen voor functie activeringen** ziet u een lijst met aanvragen die wachten op uw goed keuring.

## <a name="approve-requests"></a>Aanvragen goedkeuren

1. Zoek en selecteer de aanvraag die u wilt goed keuren en selecteer **goed keuren**.

    ![Scherm opname van de pagina ' aanvragen goed keuren ' met de knoppen goed keuren en bevestigen.](./media/groups-approval-workflow/groups-confirm-approval.png)

1. Voer in het vak **motivering** de zakelijke rechtvaardiging in.

1. Selecteer **bevestigen**. Er wordt een Azure-melding gegenereerd door uw goed keuring.

## <a name="deny-requests"></a>Aanvragen weigeren

1. Zoek en selecteer de aanvraag die u wilt weigeren en selecteer **weigeren**.

    ![Verzoeken goed keuren: deel venster goed keuren of weigeren met details en motivering](./media/groups-approval-workflow/groups-confirm-denial.png)

1. Voer in het vak **motivering** de zakelijke rechtvaardiging in.

1. Selecteer **bevestigen**. Een Azure-melding wordt gegenereerd door de weigering.

## <a name="workflow-notifications"></a>Workflowmeldingen

Hier vindt u informatie over werk stroom meldingen:

- Goed keurders worden per e-mail op de hoogte gesteld wanneer een aanvraag voor een groeps toewijzing in afwachting is van de beoordeling. E-mail meldingen bevatten een rechtstreekse koppeling naar de aanvraag, waarbij de goed keurder kan goed keuren of weigeren.
- Aanvragen worden omgezet door de eerste fiatteur die het goed keurt of weigert.
- Wanneer een goed keurder reageert op de aanvraag, worden alle goed keurders hiervan op de hoogte gesteld van de actie.

>[!Note]
>Een beheerder die meent dat een goedgekeurde gebruiker niet actief mag zijn, kan de actieve groeps toewijzing in Privileged Identity Management verwijderen. Hoewel resource beheerders geen meldingen ontvangen over aanvragen in behandeling, tenzij ze een goed keurder zijn, kunnen ze openstaande aanvragen voor alle gebruikers weer geven en annuleren door in behandeling zijnde aanvragen in Privileged Identity Management weer te geven.

## <a name="next-steps"></a>Volgende stappen

- [Groeps toewijzingen in Privileged Identity Management uitbreiden of vernieuwen](pim-resource-roles-renew-extend.md)
- [E-mail meldingen in Privileged Identity Management](pim-email-notifications.md)
- [Aanvragen voor groeps toewijzingen in Privileged Identity Management goed keuren of weigeren](azure-ad-pim-approval-workflow.md)

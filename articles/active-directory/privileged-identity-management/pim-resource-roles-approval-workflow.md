---
title: Aanvragen voor Azure-resource rollen goed keuren in PIM-Azure AD | Microsoft Docs
description: Meer informatie over het goed keuren of weigeren van aanvragen voor Azure-resource rollen in Azure AD Privileged Identity Management (PIM).
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
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 096da0547806680f62b569f7c7809a7bbb04cb86
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84742076"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-privileged-identity-management"></a>Aanvragen voor Azure-resource rollen in Privileged Identity Management goed keuren of weigeren

Met Privileged Identity Management (PIM) in Azure Active Directory (Azure AD) kunt u rollen configureren om goed keuring voor activering te vereisen en gebruikers of groepen uit uw Azure AD-organisatie kiezen als gedelegeerde goed keurders. We raden u aan twee of meer goed keurders voor elke rol te selecteren om de werk belasting voor de beheerder van de bevoegde rol te verminderen. Gedelegeerde goed keurders hebben 24 uur nodig om aanvragen goed te keuren. Als een aanvraag niet binnen 24 uur wordt goedgekeurd, moet de in aanmerking komende gebruiker een nieuwe aanvraag opnieuw indienen. Het venster goedkeurings tijd 24 uur kan niet worden geconfigureerd.

Volg de stappen in dit artikel voor het goed keuren of weigeren van aanvragen voor Azure-resource rollen.

## <a name="view-pending-requests"></a>Aanvragen in behandeling weergeven

Als gedelegeerde fiatteur ontvangt u een e-mail melding wanneer een Azure resource Role-aanvraag in afwachting is van uw goed keuring. U kunt deze aanvragen in behandeling bekijken in Privileged Identity Management.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Open **Azure AD privileged Identity Management**.

1. Selecteer **aanvragen goed keuren**.

    ![Aanvragen goed keuren-Azure-bronnen pagina waarin de aanvraag voor beoordeling wordt weer gegeven](./media/pim-resource-roles-approval-workflow/resources-approve-requests.png)

    In de sectie **aanvragen voor functie activeringen** ziet u een lijst met aanvragen die wachten op uw goed keuring.

## <a name="approve-requests"></a>Aanvragen goedkeuren

1. Zoek en selecteer de aanvraag die u wilt goed keuren. Er wordt een pagina goed keuren of weigeren weer gegeven.

    ![Verzoeken goed keuren: deel venster goed keuren of weigeren met details en motivering](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Voer in het vak **motivering** de zakelijke rechtvaardiging in.

1. Selecteer **Goedkeuren**. U ontvangt een Azure-melding van uw goed keuring.

    ![Melding goed keuren met een aanvraag is goedgekeurd](./media/pim-resource-roles-approval-workflow/resources-approve-notification.png)

## <a name="deny-requests"></a>Aanvragen weigeren

1. Zoek en selecteer de aanvraag die u wilt weigeren. Er wordt een pagina goed keuren of weigeren weer gegeven.

    ![Verzoeken goed keuren: deel venster goed keuren of weigeren met details en motivering](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Voer in het vak **motivering** de zakelijke rechtvaardiging in.

1. Selecteer **weigeren**. Er wordt een melding weer gegeven met uw weigering.

## <a name="workflow-notifications"></a>Workflowmeldingen

Hier vindt u informatie over werk stroom meldingen:

- Goed keurders worden per e-mail op de hoogte gesteld wanneer een aanvraag voor een rol in afwachting is van de beoordeling. E-mail meldingen bevatten een rechtstreekse koppeling naar de aanvraag, waarbij de goed keurder kan goed keuren of weigeren.
- Aanvragen worden omgezet door de eerste fiatteur die het goed keurt of weigert.
- Wanneer een goed keurder reageert op de aanvraag, worden alle goed keurders hiervan op de hoogte gesteld van de actie.
- Resource beheerders worden gewaarschuwd wanneer een goedgekeurde gebruiker actief wordt in hun rol.

>[!Note]
>Een resource beheerder die meent dat een goedgekeurde gebruiker niet actief mag zijn, kan de toewijzing van de actieve rol in Privileged Identity Management verwijderen. Hoewel resource beheerders geen meldingen ontvangen over aanvragen in behandeling, tenzij ze een goed keurder zijn, kunnen ze openstaande aanvragen voor alle gebruikers weer geven en annuleren door in behandeling zijnde aanvragen in Privileged Identity Management weer te geven.

## <a name="next-steps"></a>Volgende stappen

- [Azure-resource rollen in Privileged Identity Management uitbreiden of vernieuwen](pim-resource-roles-renew-extend.md)
- [E-mail meldingen in Privileged Identity Management](pim-email-notifications.md)
- [Aanvragen voor Azure AD-rollen in Privileged Identity Management goed keuren of weigeren](azure-ad-pim-approval-workflow.md)

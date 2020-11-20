---
title: Aanvragen voor Azure AD-rollen in PIM goed keuren of weigeren voor Azure AD | Microsoft Docs
description: Meer informatie over het goed keuren of weigeren van aanvragen voor Azure AD-rollen in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/07/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: d74a243ab10d0fffca960dc5149999560a7b54cd
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94980583"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-privileged-identity-management"></a>Aanvragen voor Azure AD-rollen in Privileged Identity Management goed keuren of weigeren

Met Azure Active Directory (Azure AD) Privileged Identity Management (PIM) kunt u rollen configureren voor het vereisen van goed keuring voor activering en een of meer gebruikers of groepen kiezen als gedelegeerde goed keurders. Gedelegeerde goed keurders hebben 24 uur nodig om aanvragen goed te keuren. Als een aanvraag niet binnen 24 uur wordt goedgekeurd, moet de in aanmerking komende gebruiker een nieuwe aanvraag opnieuw indienen. Het venster goedkeurings tijd 24 uur kan niet worden geconfigureerd.

## <a name="determine-your-version-of-pim"></a>Uw versie van PIM bepalen

Vanaf november 2019 wordt het gedeelte van de Azure AD-functies van Privileged Identity Management bijgewerkt naar een nieuwe versie die overeenkomt met de ervaringen van Azure-rollen. Hiermee maakt u aanvullende functies en [wijzigingen in de bestaande API](azure-ad-roles-features.md#api-changes). Terwijl de nieuwe versie wordt geïmplementeerd, zijn de procedures die u in dit artikel volgt, afhankelijk van de versie van Privileged Identity Management die u momenteel hebt. Volg de stappen in deze sectie om te bepalen welke versie van Privileged Identity Management u hebt. Nadat u uw versie van Privileged Identity Management weet, kunt u de procedures in dit artikel selecteren die overeenkomen met die versie.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met een gebruiker die zich in de beheerdersrol met [geprivilegieerde rol](../roles/permissions-reference.md#privileged-role-administrator) bevindt.
1. Open **Azure AD privileged Identity Management**. Als u een banner aan de bovenkant van de overzichts pagina hebt, volgt u de instructies op het tabblad **nieuwe versie** van dit artikel. Als dat niet het geval is, volgt u de instructies op het tabblad **vorige versie** .

    [![Selecteer Azure AD > Privileged Identity Management.](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Volg de stappen in dit artikel voor het goed keuren of weigeren van aanvragen voor Azure AD-rollen.

## <a name="new-version"></a>[Nieuwe versie](#tab/new)

### <a name="view-pending-requests"></a>Aanvragen in behandeling weergeven

Als gedelegeerde fiatteur ontvangt u een e-mail melding wanneer een Azure AD-toepassingsrol in afwachting is van uw goed keuring. U kunt deze aanvragen in behandeling bekijken in Privileged Identity Management.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Open **Azure AD privileged Identity Management**.

1. Selecteer **aanvragen goed keuren**.

    ![Aanvragen goed keuren-pagina met verzoek voor het controleren van Azure AD-rollen](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

    In de sectie **aanvragen voor functie activeringen** ziet u een lijst met aanvragen die wachten op uw goed keuring.

### <a name="approve-requests"></a>Aanvragen goedkeuren

1. Zoek en selecteer de aanvraag die u wilt goed keuren. Er wordt een pagina goed keuren of weigeren weer gegeven.

    ![Scherm afbeelding met de pagina aanvragen goed keuren-Azure AD-rollen.](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

1. Voer in het vak **motivering** de zakelijke rechtvaardiging in.

1. Selecteer **Goedkeuren**. U ontvangt een Azure-melding van uw goed keuring.

    ![Melding goed keuren met een aanvraag is goedgekeurd](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png))

### <a name="deny-requests"></a>Aanvragen weigeren

1. Zoek en selecteer de aanvraag die u wilt weigeren. Er wordt een pagina goed keuren of weigeren weer gegeven.

    ![Verzoeken goed keuren: deel venster goed keuren of weigeren met details en motivering](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Voer in het vak **motivering** de zakelijke rechtvaardiging in.

1. Selecteer **weigeren**. Er wordt een melding weer gegeven met uw weigering.

### <a name="workflow-notifications"></a>Workflowmeldingen

Hier vindt u informatie over werk stroom meldingen:

- Goed keurders worden per e-mail op de hoogte gesteld wanneer een aanvraag voor een rol in afwachting is van de beoordeling. E-mail meldingen bevatten een rechtstreekse koppeling naar de aanvraag, waarbij de goed keurder kan goed keuren of weigeren.
- Aanvragen worden omgezet door de eerste fiatteur die het goed keurt of weigert.
- Wanneer een goed keurder reageert op de aanvraag, worden alle goed keurders hiervan op de hoogte gesteld van de actie.
- Globale beheerders en beheerders met geprivilegieerde rollen worden gewaarschuwd wanneer een goedgekeurde gebruiker actief wordt in hun rol.

>[!NOTE]
>Een globale beheerder of een bevoegde beheerdersrol die meent dat een goedgekeurde gebruiker niet actief mag zijn, kan de toewijzing van de actieve rol in Privileged Identity Management verwijderen. Hoewel beheerders niet op de hoogte worden gesteld van aanvragen die in behandeling zijn, tenzij ze een goed keurder zijn, kunnen ze alle openstaande aanvragen voor alle gebruikers weer geven en annuleren door in behandeling zijnde aanvragen in Privileged Identity Management weer te geven.

## <a name="previous-version"></a>[Vorige versie](#tab/previous)

### <a name="view-pending-requests"></a>Aanvragen in behandeling weergeven

Als gedelegeerde fiatteur ontvangt u een e-mail melding wanneer een Azure AD-toepassingsrol in afwachting is van uw goed keuring. U kunt deze aanvragen in behandeling bekijken in Privileged Identity Management.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Open **Azure AD privileged Identity Management**.

1. Klik op **Azure AD-rollen**.

1. Klik op **aanvragen goed keuren**.

    ![Azure AD-rollen: aanvragen goed keuren](./media/azure-ad-pim-approval-workflow/approve-requests.png)

    U ziet een lijst met aanvragen die in afwachting zijn van uw goed keuring.

### <a name="approve-requests"></a>Aanvragen goedkeuren

1. Selecteer de aanvragen die u wilt goed keuren en klik vervolgens op **goed keuren** om het deel venster geselecteerde aanvragen goed keuren te openen.

    ![Lijst met goedgekeurde aanvragen met goedkeurings optie gemarkeerd](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. Typ een reden in het vak **reden goed keuring** .

    ![Deel venster geselecteerde aanvragen goed keuren met een goedkeurings reden](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. Klik op **goed keuren**.

    Het status symbool wordt bijgewerkt met uw goed keuring.

    ![Deel venster geselecteerde aanvragen goed keuren na goed keuren op knop](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

### <a name="deny-requests"></a>Aanvragen weigeren

1. Selecteer de aanvragen die u wilt weigeren en klik vervolgens op **weigeren** om het deel venster geselecteerde aanvragen weigeren te openen.

    ![Lijst met aanvragen goed keuren met de optie weigeren gemarkeerd](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. Typ een reden in het vak **reden van weigering** .

    ![Deel venster geselecteerde aanvragen weigeren met een reden voor weigeren](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. Selecteer **weigeren**.

    Het status symbool wordt bijgewerkt met de weigering.

---

## <a name="next-steps"></a>Volgende stappen

- [E-mail meldingen in Privileged Identity Management](pim-email-notifications.md)
- [Aanvragen voor Azure-resource rollen in Privileged Identity Management goed keuren of weigeren](pim-resource-roles-approval-workflow.md)

---
title: Klassieke abonnementsbeheerders van Azure
description: Hierin wordt beschreven hoe u de rollen Azure Co-Administrator en service beheerder toevoegt of wijzigt, en hoe u de account beheerder kunt weer geven.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/30/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: b61636d6048f63ae962d4f755a29c02e6785d5e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100557530"
---
# <a name="azure-classic-subscription-administrators"></a>Klassieke abonnementsbeheerders van Azure

Micro soft raadt u aan om de toegang tot Azure-resources te beheren met behulp van Azure op rollen gebaseerd toegangs beheer (Azure RBAC). Als u nog steeds gebruikmaakt van het klassieke implementatie model, moet u echter een klassieke rol voor abonnements beheerders gebruiken: Service beheerder en mede beheerder. Zie [Azure Resource Manager vs. klassieke implementatie](../azure-resource-manager/management/deployment-models.md)voor meer informatie.

In dit artikel wordt beschreven hoe u de Co-Administrator-en service beheerders rollen toevoegt of wijzigt, en hoe u de account beheerder kunt weer geven.

## <a name="add-a-co-administrator"></a>Een co-beheerder toevoegen

> [!TIP]
> U hoeft alleen een Co-Administrator toe te voegen als de gebruiker klassieke Azure-implementaties moet beheren met behulp van de [Azure Service Management Power shell-module](/powershell/module/servicemanagement/azure.service). Als de gebruiker alleen de Azure Portal gebruikt voor het beheren van de klassieke resources, hoeft u de klassieke beheerder niet toe te voegen aan de gebruiker.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als service beheerder of mede beheerder.

1. Open [Abonnementen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) en selecteer een abonnement.

    Co-Administrators kan alleen worden toegewezen aan het abonnements bereik.

1. Klik op **Toegangsbeheer (IAM)**.

1. Klik op het tabblad **klassieke beheerders** .

    ![Scherm afbeelding waarmee klassieke beheerders worden geopend](./media/classic-administrators/classic-administrators.png)

1. Klik op **toevoegen**  >  **mede beheerder** toevoegen om het deel venster CO-Administrators toevoegen te openen.

    Als de optie co-beheerder toevoegen is uitgeschakeld, bent u niet gemachtigd.

1. Selecteer de gebruiker die u wilt toevoegen en klik op **toevoegen**.

    ![Scherm afbeelding waarmee co-beheerder wordt toegevoegd](./media/classic-administrators/add-coadmin.png)

## <a name="add-a-guest-user-as-a-co-administrator"></a>Een gast gebruiker als Co-Administrator toevoegen

Als u een gast gebruiker wilt toevoegen als co-beheerder, voert u dezelfde stappen uit als in het vorige gedeelte [een mede beheerder toevoegen](#add-a-co-administrator) . De gast gebruiker moet voldoen aan de volgende criteria:

- De gast gebruiker moet een aanwezigheid hebben in uw Directory. Dit betekent dat de gebruiker is uitgenodigd voor de Directory en de uitnodiging heeft geaccepteerd.

Zie [Azure Active Directory B2B-samenwerkings gebruikers toevoegen in de Azure Portal](../active-directory/external-identities/add-users-administrator.md)voor meer informatie over het toevoegen van een gast gebruiker aan uw Directory.

### <a name="differences-for-guest-users"></a>Verschillen voor gast gebruikers

Gast gebruikers aan wie de rol Co-Administrator is toegewezen, kunnen enkele verschillen zien ten opzichte van gebruikers die lid zijn van de Co-Administrator rol. Denkt u zich het volgende scenario eens in:

- Gebruiker A met een Azure AD-account (werk-of school account) is een service beheerder voor een Azure-abonnement.
- Gebruiker B heeft een Microsoft-account.
- Gebruiker A wijst de Co-Administrator rol toe aan gebruiker B.
- Gebruiker B kan vrijwel alles doen, maar kan geen toepassingen registreren of gebruikers opzoeken in de Azure AD-adres lijst.

U verwacht dat gebruiker B alles kan beheren. De reden hiervoor is dat de Microsoft-account wordt toegevoegd aan het abonnement als gast gebruiker in plaats van een lid van een gebruiker. Gast gebruikers hebben een andere standaard machtiging in azure AD, vergeleken met gebruikers van leden. Gebruikers kunnen bijvoorbeeld andere gebruikers in azure AD en gast gebruikers lezen. Gebruikers van leden kunnen nieuwe service-principals in azure AD en gast gebruikers registreren.

Als een gast gebruiker deze taken moet kunnen uitvoeren, is een mogelijke oplossing om de specifieke Azure AD-rollen toe te wijzen die de gast gebruiker nodig heeft. In het vorige scenario kunt u bijvoorbeeld de rol van [Directory lezers](../active-directory/roles/permissions-reference.md#directory-readers) toewijzen om andere gebruikers te lezen en de rol [toepassings ontwikkelaar](../active-directory/roles/permissions-reference.md#application-developer) toe te wijzen om service-principals te kunnen maken. Zie [Wat zijn de standaard machtigingen voor gebruikers in azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md)voor meer informatie over lid-en gast gebruikers en de bijbehorende machtigingen. Zie voor meer informatie over het verlenen van toegang voor gast gebruikers [Azure-rollen toewijzen aan externe gast gebruikers met behulp van de Azure Portal](role-assignments-external-users.md).

Houd er rekening mee dat de [ingebouwde rollen van Azure](../role-based-access-control/built-in-roles.md) verschillen van de [Azure AD-functies](../active-directory/roles/permissions-reference.md). De ingebouwde rollen verlenen geen toegang tot Azure AD. Zie [inzicht krijgen in de verschillende rollen](../role-based-access-control/rbac-and-directory-admin-roles.md)voor meer informatie.

Zie [Wat zijn de standaard machtigingen voor gebruikers in azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md)voor informatie waarmee leden van gebruikers en gast gebruikers worden vergeleken.

## <a name="remove-a-co-administrator"></a>Een Co-Administrator verwijderen

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als service beheerder of mede beheerder.

1. Open [Abonnementen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) en selecteer een abonnement.

1. Klik op **Toegangsbeheer (IAM)**.

1. Klik op het tabblad **klassieke beheerders** .

1. Voeg een vinkje toe naast de Co-Administrator die u wilt verwijderen.

1. Klik op **Verwijderen**.

1. Klik op **Ja** in het bericht venster dat wordt weer gegeven.

    ![Scherm afbeelding waarmee co-beheerder wordt verwijderd](./media/classic-administrators/remove-coadmin.png)

## <a name="change-the-service-administrator"></a>De servicebeheerder wijzigen

Alleen de account beheerder kan de service beheerder voor een abonnement wijzigen. Wanneer u zich aanmeldt voor een Azure-abonnement, is de service beheerder standaard hetzelfde als de account beheerder. De gebruiker met de rol account beheerder heeft geen toegang tot de Azure Portal. De gebruiker met de rol van service beheerder heeft volledige toegang tot de Azure Portal. Als de account beheerder en service beheerder dezelfde gebruiker zijn en u de service beheerder wijzigt in een andere gebruiker, verliest de account beheerder de toegang tot Azure Portal. De account beheerder kan echter altijd het account centrum gebruiken om de service beheerder weer op zichzelf te zetten.

Volg deze stappen om de service beheerder in de Azure Portal te wijzigen.

1. Zorg ervoor dat uw scenario wordt ondersteund door de [beperkingen voor het wijzigen van de service beheerder](#limitations-for-changing-the-service-administrator)te controleren.

1. Meld u als accountbeheerder aan bij [Azure Portal](https://portal.azure.com).

1. Open **Cost Management en facturering** en selecteer een abonnement.

1. Klik in de linkernavigatiebalk op **Eigenschappen**.

1. Klik op **service beheerder**.

    ![Scherm opname met de eigenschappen van het abonnement in de Azure Portal](./media/classic-administrators/service-admin.png)

1. Voer op de pagina **Service beheer bewerken** het e-mail adres voor de nieuwe service beheerder in.

    ![Scherm opname van de pagina Service beheer bewerken](./media/classic-administrators/service-admin-edit.png)

1. Klik op **OK** om de wijziging op te slaan.

### <a name="limitations-for-changing-the-service-administrator"></a>Beperkingen voor het wijzigen van de service beheerder

Er kan slechts één service beheerder per Azure-abonnement zijn. Het wijzigen van de service beheerder werkt anders, afhankelijk van het feit of de account beheerder een Microsoft-account is of dat het een Azure AD-account is (werk-of school account).

| Account beheerders account | Kan de service beheerder wijzigen in een andere Microsoft-account? | Kan de service beheerder wijzigen in een Azure AD-account in dezelfde map? | Kan de service beheerder wijzigen in een Azure AD-account in een andere adres lijst? |
| --- | --- | --- | --- |
| Microsoft-account | Ja | Nee | Nee |
| Azure AD-account | Ja | Ja | Nee |

Als de account beheerder een Azure AD-account is, kunt u de service beheerder wijzigen in een Azure AD-account in dezelfde map, maar niet in een andere map. Bijvoorbeeld, abby@contoso.com kan de service beheerder wijzigen in bob@contoso.com , maar kan de service beheerder niet wijzigen in, john@notcontoso.com tenzij john@notcontoso.com er een aanwezigheid is in de contoso.com-map.

Zie [Wat is Azure Active Directory?](../active-directory/fundamentals/active-directory-whatis.md)voor meer informatie over micro soft-accounts en Azure AD-accounts.

## <a name="view-the-account-administrator"></a>De accountbeheerder weergeven

De account beheerder is de gebruiker die zich voor het eerst aanmeldt voor het Azure-abonnement en is verantwoordelijk als de facturerings eigenaar van het abonnement. Zie [eigendom van een Azure-abonnement overdragen aan een ander account](../cost-management-billing/manage/billing-subscription-transfer.md)om de account beheerder van een abonnement te wijzigen.

Volg deze stappen om de account beheerder weer te geven.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Open **Cost Management en facturering** en selecteer een abonnement.

1. Klik in de linkernavigatiebalk op **Eigenschappen**.

    De account beheerder van het abonnement wordt weer gegeven in het vak **account beheerder** .

    ![Scherm opname van de account beheerder](./media/classic-administrators/account-admin.png)

## <a name="next-steps"></a>Volgende stappen

* [Inzicht in de verschillende rollen](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Azure-rollen toewijzen met behulp van de Azure Portal](../role-based-access-control/role-assignments-portal.md)
* [Azure-abonnementsbeheerders toevoegen of wijzigen](../cost-management-billing/manage/add-change-subscription-administrator.md)
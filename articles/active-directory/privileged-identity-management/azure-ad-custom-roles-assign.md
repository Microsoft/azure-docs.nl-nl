---
title: Aangepaste Azure AD-rol-Privileged Identity Management (PIM) toewijzen
description: Een aangepaste Azure AD-rol toewijzen voor toewijzings Privileged Identity Management (PIM)
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
ms.openlocfilehash: 75e004980ada23a616b57e3c7eb1afb241e09598
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92371644"
---
# <a name="assign-an-azure-ad-custom-role-in-privileged-identity-management"></a>Een aangepaste Azure AD-rol toewijzen in Privileged Identity Management

In dit artikel leest u hoe u Privileged Identity Management (PIM) kunt gebruiken voor het maken van just-in-time-en tijdgebonden toewijzing aan aangepaste rollen die zijn gemaakt voor het beheren van toepassingen in de beheer ervaring van Azure Active Directory (Azure AD).

- Zie [aangepaste beheerders rollen in azure Active Directory (preview)](../roles/custom-overview.md)voor meer informatie over het maken van aangepaste rollen voor het delegeren van toepassings beheer in azure AD.
- Als u nog geen Privileged Identity Management hebt gebruikt, kunt u aan de slag [met privileged Identity Management](pim-getting-started.md)meer informatie.
- Voor informatie over het verlenen van een andere beheerder toegang tot het beheren van Privileged Identity Management raadpleegt u [toegang verlenen aan andere beheerders om privileged Identity Management te beheren](pim-how-to-give-access-to-pim.md).

> [!NOTE]
> Aangepaste Azure AD-rollen worden tijdens de preview-fase niet geïntegreerd met de ingebouwde Directory rollen. Zodra de mogelijkheid algemeen beschikbaar is, wordt het beheer van rollen uitgevoerd in de ingebouwde functie-ervaring. Als u de volgende banner ziet, moeten deze rollen worden beheerd [in de ingebouwde functie-ervaring](pim-how-to-activate-role.md) en dit artikel is niet van toepassing:
>
> [![Selecteer Azure AD > Privileged Identity Management.](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

## <a name="assign-a-role"></a>Een rol toewijzen

Privileged Identity Management kunt aangepaste rollen beheren die u kunt maken in Azure Active Directory (Azure AD)-toepassings beheer.  Met de volgende stappen maakt u een in aanmerking komende toewijzing aan een aangepaste Directory-rol.

1. Meld u aan bij [privileged Identity Management](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) in het Azure Portal met een gebruikers account dat is toegewezen aan de rol van beheerdersrol voor bevoegde rol.
1. Selecteer **aangepaste Azure AD-rollen (preview-versie)**.

    ![Selecteer voor beeld van aangepaste rollen van Azure AD om de in aanmerking komende roltoewijzingen te bekijken](./media/azure-ad-custom-roles-assign/view-custom.png)

1. Selecteer **rollen** om een lijst met aangepaste rollen voor Azure AD-toepassingen weer te geven.

    ![Rollen selecteren Zie de lijst met in aanmerking komende roltoewijzingen](./media/azure-ad-custom-roles-assign/view-roles.png)

1. Selecteer **lid toevoegen** om de toewijzings pagina te openen.
1. Als u het bereik van de roltoewijzing wilt beperken tot één toepassing, selecteert u **bereik** om een toepassings bereik op te geven.

    ![het bereik van in aanmerking komende roltoewijzingen in azure AD beperken](./media/azure-ad-custom-roles-assign/set-scope.png)

1. Selecteer **een rol selecteren** om de lijst **een rol selecteren** te openen.

    ![Selecteer de in aanmerking komende rol die aan een gebruiker moet worden toegewezen](./media/azure-ad-custom-roles-assign/select-role.png)

1. Selecteer een rol die u wilt toewijzen en klik vervolgens op **selecteren**. De lijst **een leden selecteren** wordt geopend.

    ![Selecteer de gebruiker aan wie u de rol wilt toewijzen](./media/azure-ad-custom-roles-assign/select-member.png)

1. Selecteer een gebruiker die u aan de rol wilt toewijzen en klik vervolgens op **selecteren**. De lijst **lidmaatschaps instellingen** wordt geopend.

    ![Het roltoewijzing-type instellen op in aanmerking komend of actief](./media/azure-ad-custom-roles-assign/membership-settings.png)

1. Selecteer in **aanmerking komend** of **actief** op de pagina **lidmaatschaps instellingen** :

    - Voor **in aanmerking komende** toewijzingen moet de gebruiker aan de rol zijn toegewezen om een actie uit te voeren voordat de rol kan worden gebruikt. Acties kunnen omvatten het door geven van een multi-factor Authentication-controle, het leveren van een zakelijke reden of het aanvragen van goed keuring van aangewezen goed keurders.
    - Voor **actieve** toewijzingen hoeft de toegewezen gebruiker geen actie te ondernemen om de rol te gebruiken. Actieve gebruikers hebben te allen tijde de bevoegdheden toegewezen aan de rol.

1. Als het selectie vakje **permanent** is ingeschakeld en beschikbaar is (afhankelijk van rolinstellingen), kunt u opgeven of de toewijzing permanent is. Schakel het selectie vakje in om de toewijzing permanent in aanmerking te laten komen of permanent toe te wijzen. Schakel het selectie vakje uit om de duur van een toewijzing op te geven.
1. Als u de nieuwe roltoewijzing wilt maken, klikt u op **Opslaan** en vervolgens op **toevoegen**. Er wordt een melding weer gegeven van de status van het toewijzings proces.

Als u de roltoewijzing wilt controleren, selecteert u in een openstaande rol de optie **toewijzingen**  >  **toewijzen** en controleert u of uw roltoewijzing correct is geïdentificeerd als in aanmerking komend of actief.

 ![Controleren of de roltoewijzing wordt weer gegeven als in aanmerking komend of actief](./media/azure-ad-custom-roles-assign/verify-assignments.png)

## <a name="next-steps"></a>Volgende stappen

- [Een aangepaste Azure AD-rol activeren](azure-ad-custom-roles-assign.md)
- [Een aangepaste gebruikersrol toewijzing van Azure AD verwijderen of bijwerken](azure-ad-custom-roles-update-remove.md)
- [Een aangepaste functie toewijzing voor Azure AD configureren](azure-ad-custom-roles-configure.md)
- [Roldefinities in azure AD](../roles/permissions-reference.md)

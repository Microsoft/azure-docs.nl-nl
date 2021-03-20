---
title: Aangepaste Azure AD-rol-Privileged Identity Management (PIM) configureren
description: Aangepaste Azure AD-rollen configureren in Privileged Identity Management (PIM)
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
ms.openlocfilehash: fb23e60539c704dac457ab6e8706ec0cfe350ed9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94835319"
---
# <a name="configure-azure-ad-custom-roles-in-privileged-identity-management"></a>Aangepaste Azure AD-rollen configureren in Privileged Identity Management

Een beheerder van een bevoegde rol kan de rolinstellingen wijzigen die van toepassing zijn op een gebruiker wanneer ze hun toewijzing activeren voor een aangepaste rol en voor andere toepassings beheerders die aangepaste rollen toewijzen.

> [!NOTE]
> Aangepaste Azure AD-rollen worden tijdens de preview-fase niet geïntegreerd met de ingebouwde Directory rollen. Zodra de mogelijkheid algemeen beschikbaar is, wordt het beheer van rollen uitgevoerd in de ingebouwde functie-ervaring. Als u de volgende banner ziet, moeten deze rollen worden beheerd [in de ingebouwde functie-ervaring](pim-how-to-activate-role.md) en dit artikel is niet van toepassing:
>
> [![Selecteer Azure AD-> Privileged Identity Management](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

## <a name="open-role-settings"></a>Rolinstellingen openen

Volg deze stappen om de instellingen voor een Azure AD-functie te openen.

1. Meld u aan bij [privileged Identity Management](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) in het Azure Portal met een gebruikers account dat is toegewezen aan de rol van beheerdersrol voor bevoegde rol.
1. Selecteer **aangepaste Azure AD-rollen (preview-versie)**.

    ![Selecteer voor beeld van aangepaste rollen van Azure AD om de in aanmerking komende roltoewijzingen te bekijken](./media/azure-ad-custom-roles-configure/settings-list.png)

1. Selecteer **instelling** om de pagina **instellingen** te openen. Selecteer de rol voor de instellingen die u wilt configureren.
1. Selecteer **bewerken** om de pagina **functie-instellingen** te openen.

    ![Scherm opname van de pagina ' Details van rolinstellingen ' waarvoor de actie ' bewerken ' is geselecteerd.](./media/azure-ad-custom-roles-configure/edit-settings.png)

## <a name="role-settings"></a>Rolinstellingen

Er zijn verschillende instellingen die u kunt configureren.

### <a name="assignment-duration"></a>Toewijzings duur

U kunt kiezen uit twee opties voor de toewijzings duur voor elk toewijzings type (in aanmerking komend of actief) wanneer u instellingen voor een rol configureert. Deze opties worden de standaard maximale duur wanneer een lid wordt toegewezen aan de rol in Privileged Identity Management.

U kunt kiezen uit een van *deze opties* voor de gewenste duur van de toewijzing.

- **Permanente toewijzing in aanmerking komend toestaan**: beheerders kunnen een permanent, in aanmerking komend lidmaatschap toewijzen.
- **In aanmerking komende toewijzing verlopen na**: beheerders kunnen vereisen dat alle in aanmerking komende toewijzingen een opgegeven begin-en eind datum hebben.

U kunt ook een van deze *actieve* toewijzings duur opties kiezen:

- **Permanente actieve toewijzing toestaan**: beheerders kunnen permanent actief lidmaatschap toewijzen.
- **Actieve toewijzing laten verlopen na**: beheerders kunnen vereisen dat alle actieve toewijzingen een opgegeven begin-en eind datum hebben.

### <a name="require-azure-ad-multi-factor-authentication"></a>Azure AD Multi-Factor Authentication vereisen

Privileged Identity Management biedt een optionele afdwinging van Azure AD-Multi-Factor Authentication voor twee verschillende scenario's.

- **Multi-Factor Authentication vereisen voor actieve toewijzing**

  Als u een lid wilt toewijzen aan een rol voor een korte duur (bijvoorbeeld één dag), is het mogelijk te langzaam om de toegewezen leden te vereisen om activering aan te vragen. In dit scenario kan Privileged Identity Management multi-factor Authentication niet afdwingen wanneer de gebruiker de roltoewijzing activeert, omdat deze al actief zijn in de rol vanaf het moment dat ze worden toegewezen. Schakel het selectie vakje **multi-factor Authentication op actieve toewijzing vereisen** in om ervoor te zorgen dat de beheerder aan de toewijzing voldoet.

- **Multi-Factor Authentication vereisen bij activering**

  U kunt in aanmerking komende gebruikers die zijn toegewezen aan een rol voor inschrijving in azure AD Multi-Factor Authentication vereisen voordat ze kunnen activeren. Dit proces zorgt ervoor dat de gebruiker die de activering aanvraagt, een redelijke zekerheid krijgt. Het afdwingen van deze optie beschermt kritieke rollen in situaties waarin het gebruikers account mogelijk is aangetast. Als u wilt dat een in aanmerking komend lid een Azure AD-Multi-Factor Authentication uitvoert vóór activering, schakelt u het selectie vakje **vereisen multi-factor Authentication bij activering** in.

Zie [multi-factor Authentication en privileged Identity Management](pim-how-to-require-mfa.md)voor meer informatie.

### <a name="activation-maximum-duration"></a>Maximale activerings duur

Gebruik de schuif regelaar **maximale duur activering** om de maximum tijd in uren in te stellen dat een rol actief blijft voordat deze verloopt. Deze waarde kan tussen, 1 en 24 uur liggen.

### <a name="require-justification"></a>Reden vereisen

U kunt vereisen dat leden een reden voor een actieve toewijzing invoeren of wanneer ze worden geactiveerd. Als u dit wilt doen, schakelt u het selectie vakje **uitvulling vereisen voor actieve toewijzing** in of het vakje **uitvulling vereisen bij activering** .

### <a name="require-approval-to-activate"></a>Goed keuring vereist om te activeren

Als u wilt dat goed keuring vereist is om een rol te activeren, voert u de volgende stappen uit.

1. Selecteer het selectie vakje **goed keuring vereisen om te activeren** .
1. Selecteer **goed keurders selecteren** om de lijst **een lid of groep selecteren** te openen.

    ![Open de aangepaste Azure AD-rol om de instellingen te bewerken](./media/azure-ad-custom-roles-configure/select-approvers.png)

1. Selecteer ten minste één lid of groep en klik vervolgens op **selecteren**. U moet ten minste één fiatteur selecteren. Er zijn geen standaard fiatteurs. Uw selecties worden weer gegeven in de lijst met geselecteerde goed keurders.
1. Wanneer u de rolinstellingen hebt opgegeven, selecteert u **bijwerken** om uw wijzigingen op te slaan.

## <a name="next-steps"></a>Volgende stappen

- [Een aangepaste Azure AD-rol activeren](azure-ad-custom-roles-activate.md)
- [Een aangepaste Azure AD-rol toewijzen](azure-ad-custom-roles-assign.md)
- [Een aangepaste gebruikersrol toewijzing van Azure AD verwijderen of bijwerken](azure-ad-custom-roles-update-remove.md)
- [Roldefinities in azure AD](../roles/permissions-reference.md)

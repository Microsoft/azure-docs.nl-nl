---
title: Instellingen voor geprivilegieerde toegangs groepen configureren in PIM-Azure Active Directory | Microsoft Docs
description: Meer informatie over het configureren van instellingen voor door rollen toewijs bare groepen in Azure AD Privileged Identity Management (PIM).
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
ms.date: 07/27/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2cfb09f383d8425a644d3e2e87d190b350f5f41a
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105564633"
---
# <a name="configure-privileged-access-group-settings-preview-in-privileged-identity-management"></a>Instellingen voor geprivilegieerde toegangs groep (preview) configureren in Privileged Identity Management

Rolinstellingen zijn de standaard instellingen die worden toegepast op de toewijzing van de groeps eigenaar en de bevoegdheid groepslid maatschap voor toegang in Privileged Identity Management (PIM). Voer de volgende stappen uit om de goedkeurings werk stroom in te stellen om op te geven wie aanvragen kan goed keuren of weigeren voor bevoegdheid om bevoegdheden uit te breiden.

## <a name="open-role-settings"></a>Rolinstellingen openen

Volg deze stappen om de instellingen voor een groep met uitgebreide toegang tot Azure te openen.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/) met een gebruiker in de beheerdersrol [privileged Role](../roles/permissions-reference.md#privileged-role-administrator) .

1. Open **Azure AD privileged Identity Management**.

1. Selecteer **bevoorrechte toegang (preview)**.

1. Selecteer de groep die u wilt beheren.

    ![Geprivilegieerde toegangs groepen gefilterd op een groeps naam](./media/groups-role-settings/group-select.png)

1. Selecteer **Instellingen**.

    ![Instellingen pagina weer gegeven groeps instellingen voor de geselecteerde groep](./media/groups-role-settings/group-settings-select-role.png)

1. Selecteer de eigenaar of de rol waarvan u de instellingen wilt weer geven of wijzigen. U kunt de huidige instellingen voor de rol weer geven op de pagina **Details van functie** -instellingen.

    ![Pagina Details van de functie-instelling met een lijst met verschillende toewijzings-en activerings instellingen](./media/groups-role-settings/group-role-setting-details.png)

1. Selecteer **bewerken** om de instellings pagina voor de **rol bewerken** te openen. Op het tabblad **Activering** kunt u de instellingen voor de activering van rollen wijzigen, inclusief of permanente, in aanmerking komende en actieve toewijzingen worden toegestaan.

    ![De pagina functie-instellingen bewerken met het tabblad Activering geopend](./media/groups-role-settings/role-settings-activation-tab.png)

1. Selecteer het tabblad **toewijzing** om het tabblad toewijzings instellingen te openen. Deze instellingen bepalen de Privileged Identity Management toewijzings instellingen voor deze rol.

    ![Het tabblad roltoewijzing in de pagina rolinstellingen](./media/groups-role-settings/role-settings-assignment-tab.png)

1. Gebruik het tabblad **melding** of de knop **volgende: activering** aan de onderkant van de pagina om naar het tabblad meldings instelling voor deze rol te gaan. Deze instellingen bepalen alle e-mail meldingen die betrekking hebben op deze rol.

    ![Tabblad meldingen in de pagina rolinstellingen](./media/groups-role-settings/role-settings-notification-tab.png)

1. Selecteer de knop **bijwerken** op elk gewenst moment om de rolinstellingen bij te werken.

Op het tabblad **meldingen** op de pagina rolinstellingen kunt privileged Identity Management gedetailleerde controle over wie meldingen ontvangen en welke meldingen ze ontvangen.

- **Een e-mail uitzetten**<br>U kunt specifieke e-mail adressen uitschakelen door het selectie vakje standaard ontvanger uit te scha kelen en eventuele extra ontvangers te verwijderen.  
- **E-mails beperken tot opgegeven e-mail adressen**<br>U kunt e-mail berichten die worden verzonden naar standaard ontvangers uitschakelen door het selectie vakje standaard ontvanger uit te scha kelen. U kunt vervolgens extra e-mail adressen toevoegen als extra geadresseerden. Als u meer dan één e-mail adres wilt toevoegen, scheidt u deze met een punt komma (;).
- **E-mail berichten verzenden naar standaard ontvangers en extra ontvangers**<br>U kunt e-mail berichten verzenden naar de standaard ontvanger en de extra ontvanger door het selectie vakje standaard ontvanger te selecteren en e-mail adressen toe te voegen voor extra ontvangers.
- **Alleen essentiële e-mail berichten**<br>Voor elk type e-mail bericht kunt u het selectie vakje selecteren om alleen essentiële e-mail berichten te ontvangen. Dit betekent dat Privileged Identity Management alleen e-mail berichten naar de geconfigureerde ontvangers stuurt wanneer het e-mail bericht een onmiddellijke actie vereist. E-mail berichten waarin gebruikers worden gevraagd om hun roltoewijzing uit te breiden, worden bijvoorbeeld niet geactiveerd wanneer een e-mail berichten die beheerders vereisen om een uitbreidings aanvraag goed te keuren, worden geactiveerd.

## <a name="assignment-duration"></a>Toewijzings duur

U kunt kiezen uit twee opties voor de toewijzings duur voor elk toewijzings type (in aanmerking komend en actief) wanneer u instellingen voor een rol configureert. Deze opties worden de standaard maximale duur wanneer een gebruiker wordt toegewezen aan de rol in Privileged Identity Management.

U kunt kiezen uit een van **deze opties** voor de gewenste duur van de toewijzing:

| | Description |
| --- | --- |
| **Permanente toewijzing in aanmerking komend toestaan** | Resource beheerders kunnen een permanente, in aanmerking komende toewijzing toewijzen. |
| **In aanmerking komende toewijzing laten verlopen na** | Resource beheerders kunnen vereisen dat alle in aanmerking komende toewijzingen een opgegeven begin-en eind datum hebben. |

En u kunt een van deze **actieve** toewijzings duur opties kiezen:

| | Description |
| --- | --- |
| **Permanente actieve toewijzing toestaan** | Resource beheerders kunnen permanente actieve toewijzing toewijzen. |
| **Actieve toewijzing laten verlopen na** | Resource beheerders kunnen vereisen dat alle actieve toewijzingen een opgegeven begin-en eind datum hebben. |

> [!NOTE]
> Alle toewijzingen met een opgegeven eind datum kunnen worden vernieuwd door resource beheerders. Gebruikers kunnen ook selfservice aanvragen initiëren om roltoewijzingen uit te [breiden of te vernieuwen](pim-resource-roles-renew-extend.md).

## <a name="require-multi-factor-authentication"></a>Multi-Factor Authentication vereisen

Privileged Identity Management biedt een optionele afdwinging van Azure AD-Multi-Factor Authentication voor twee verschillende scenario's.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Multi-Factor Authentication vereisen voor actieve toewijzing

In sommige gevallen wilt u mogelijk een gebruiker of groep toewijzen aan een rol voor een korte duur (bijvoorbeeld één dag). In dit geval hoeven de toegewezen gebruikers geen activering aan te vragen. In dit scenario kan Privileged Identity Management multi-factor Authentication niet afdwingen wanneer de gebruiker hun roltoewijzing gebruikt, omdat ze al actief zijn in de rol vanaf het moment dat deze wordt toegewezen.

Als u er zeker van wilt zijn dat de resource beheerder die aan de toewijzing voldoet, weet wie ze zijn, kunt u multi-factor Authentication afdwingen voor actieve toewijzing door het selectie vakje **multi-factor Authentication op actieve toewijzing vereisen** in te scha kelen.

### <a name="require-multi-factor-authentication-on-activation"></a>Multi-Factor Authentication vereisen bij activering

U kunt vereisen dat gebruikers die in aanmerking komen voor een rol bewijzen dat ze Azure AD Multi-Factor Authentication gebruiken voordat ze kunnen activeren. Multi-factor Authentication zorgt ervoor dat de gebruiker er zeker van is dat ze een redelijke zekerheid hebben. Het afdwingen van deze optie beschermt kritieke resources in situaties waarin het gebruikers account mogelijk is aangetast.

Als u multi-factor Authentication vóór activering wilt vereisen, schakelt u het selectie vakje **multi-factor Authentication vereisen bij activering** in.

Zie [multi-factor Authentication en privileged Identity Management](pim-how-to-require-mfa.md)voor meer informatie.

## <a name="activation-maximum-duration"></a>Maximale activerings duur

Gebruik de schuif regelaar **maximale duur activering** om de maximum tijd in uren in te stellen dat een rol actief blijft voordat deze verloopt. Deze waarde kan een tot 24 uur zijn.

## <a name="require-justification"></a>Reden vereisen

U kunt vereisen dat gebruikers een zakelijke reden opgeven wanneer ze het product activeren. Als u dit wilt doen, schakelt u het selectie vakje **uitvulling vereist op actieve toewijzing** of het vakje **uitvulling vereisen bij activering** in.

## <a name="require-approval-to-activate"></a>Goed keuring vereist om te activeren

Als u wilt dat goed keuring vereist is om een rol te activeren, voert u de volgende stappen uit.

1. Controleer het selectie vakje **goed keuring vereisen om te activeren** .

1. Selecteer **goed keurders selecteren** om de pagina **een lid of groep selecteren** te openen.

    ![Selecteer een gebruikers-of groeps deel venster voor het selecteren van goed keurders](./media/groups-role-settings/group-settings-select-approvers.png)

1. Selecteer ten minste één gebruiker of groep en klik vervolgens op **selecteren**. U kunt een wille keurige combi natie van gebruikers en groepen toevoegen. U moet ten minste één fiatteur selecteren. Er zijn geen standaard fiatteurs.

    Uw selecties worden weer gegeven in de lijst met geselecteerde goed keurders.

1. Wanneer u de instellingen van uw rol hebt opgegeven, selecteert u **bijwerken** om uw wijzigingen op te slaan.

## <a name="next-steps"></a>Volgende stappen

- [Groepslid maatschap of eigendom van geprivilegieerde toegang toewijzen in PIM](groups-assign-member-owner.md)

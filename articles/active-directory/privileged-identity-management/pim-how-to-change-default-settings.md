---
title: Instellingen voor Azure AD-functies configureren in PIM-Azure AD | Microsoft Docs
description: Meer informatie over het configureren van instellingen voor Azure AD-functies in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 02/28/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 06eb7e8d1be2e58fd6cf87167a585a2ffeee0d7f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97708480"
---
# <a name="configure-azure-ad-role-settings-in-privileged-identity-management"></a>Instellingen voor Azure AD-functies configureren in Privileged Identity Management

Een beheerder van een bevoegde rol kan Privileged Identity Management (PIM) in hun Azure Active Directory (Azure AD)-organisatie aanpassen, met inbegrip van het wijzigen van de ervaring voor een gebruiker die een in aanmerking komende roltoewijzing activeert.

## <a name="determine-your-version-of-pim"></a>Uw versie van PIM bepalen

Vanaf november 2019 wordt het gedeelte van de Azure AD-functies van Privileged Identity Management bijgewerkt naar een nieuwe versie die overeenkomt met de ervaringen van Azure-resource rollen. Hiermee maakt u aanvullende functies en [wijzigingen in de bestaande API](azure-ad-roles-features.md#api-changes). Terwijl de nieuwe versie wordt geïmplementeerd, zijn de procedures die u in dit artikel volgt, afhankelijk van de versie van Privileged Identity Management die u momenteel hebt. Volg de stappen in deze sectie om te bepalen welke versie van Privileged Identity Management u hebt. Nadat u uw versie van Privileged Identity Management weet, kunt u de procedures in dit artikel selecteren die overeenkomen met die versie.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met een gebruiker die zich in de beheerdersrol met [geprivilegieerde rol](../roles/permissions-reference.md#privileged-role-administrator) bevindt.
1. Open **Azure AD privileged Identity Management**. Als u een banner aan de bovenkant van de overzichts pagina hebt, volgt u de instructies op het tabblad **nieuwe versie** van dit artikel. Als dat niet het geval is, volgt u de instructies op het tabblad **vorige versie** .

  [![Selecteer Azure AD > Privileged Identity Management.](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Volg de stappen in dit artikel voor het goed keuren of weigeren van aanvragen voor Azure AD-rollen.

# <a name="new-version"></a>[Nieuwe versie](#tab/new)

## <a name="open-role-settings"></a>Rolinstellingen openen

Volg deze stappen om de instellingen voor een Azure AD-functie te openen.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/) met een gebruiker in de beheerdersrol [privileged Role](../roles/permissions-reference.md#privileged-role-administrator) .

1. Open **Azure AD privileged Identity Management** &gt; functie-instellingen van **Azure AD-rollen** &gt; .

    ![Pagina rolinstellingen die Azure AD-rollen weer geven](./media/pim-how-to-change-default-settings/role-settings.png)

1. Selecteer de rol waarvan u de instellingen wilt configureren.

    ![Pagina Details van de functie-instelling met een lijst met verschillende toewijzings-en activerings instellingen](./media/pim-how-to-change-default-settings/role-settings-page.png)

1. Selecteer **bewerken** om de pagina functie-instellingen te openen.

    ![Pagina Rolinstellingen bewerken met opties voor het bijwerken van de toewijzings-en activerings instellingen](./media/pim-how-to-change-default-settings/role-settings-edit.png)

    In het instellingen deel venster voor elke rol zijn er verschillende instellingen die u kunt configureren.

## <a name="assignment-duration"></a>Toewijzings duur

U kunt kiezen uit twee opties voor de toewijzings duur voor elk toewijzings type (in aanmerking komend en actief) wanneer u instellingen voor een rol configureert. Deze opties worden de standaard maximale duur wanneer een gebruiker wordt toegewezen aan de rol in Privileged Identity Management.

U kunt kiezen uit een van **deze opties** voor de gewenste duur van de toewijzing:

| | |
| --- | --- |
| **Permanente toewijzing in aanmerking komend toestaan** | Globale beheerders en beheerders met geprivilegieerde rollen kunnen een permanente, in aanmerking komende toewijzing toewijzen. |
| **In aanmerking komende toewijzing laten verlopen na** | Globale beheerders en beheerders met geprivilegieerde rollen kunnen vereisen dat alle in aanmerking komende toewijzingen een opgegeven begin-en eind datum hebben. |

En u kunt een van deze **actieve** toewijzings duur opties kiezen:

| | |
| --- | --- |
| **Permanente actieve toewijzing toestaan** | Globale beheerders en beheerders met geprivilegieerde rollen kunnen permanente actieve toewijzing toewijzen. |
| **Actieve toewijzing laten verlopen na** | Globale beheerders en beheerders met geprivilegieerde rollen kunnen vereisen dat alle actieve toewijzingen een opgegeven begin-en eind datum hebben. |

> [!NOTE]
> Alle toewijzingen met een opgegeven eind datum kunnen worden vernieuwd door globale beheerders en beheerders met verhoogde bevoegdheden. Gebruikers kunnen ook selfservice aanvragen initiëren om roltoewijzingen uit te [breiden of te vernieuwen](pim-resource-roles-renew-extend.md).

## <a name="require-multi-factor-authentication"></a>Multi-Factor Authentication vereisen

Privileged Identity Management biedt een optionele afdwinging van Azure AD-Multi-Factor Authentication voor twee verschillende scenario's.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Multi-Factor Authentication vereisen voor actieve toewijzing

In sommige gevallen wilt u mogelijk een gebruiker toewijzen aan een rol voor een korte duur (bijvoorbeeld één dag). In dit geval hoeven de toegewezen gebruikers geen activering aan te vragen. In dit scenario kan Privileged Identity Management multi-factor Authentication niet afdwingen wanneer de gebruiker hun roltoewijzing gebruikt, omdat ze al actief zijn in de rol vanaf het moment dat deze wordt toegewezen.

Als u er zeker van wilt zijn dat de beheerder die aan de toewijzing voldoet, de vraag is, kunt u multi-factor Authentication afdwingen voor actieve toewijzing door het selectie vakje **multi-factor Authentication op actieve toewijzing vereisen** in te scha kelen.

### <a name="require-multi-factor-authentication-on-activation"></a>Multi-Factor Authentication vereisen bij activering

U kunt vereisen dat gebruikers die in aanmerking komen voor een rol bewijzen dat ze Azure AD Multi-Factor Authentication gebruiken voordat ze kunnen activeren. Multi-factor Authentication zorgt ervoor dat de gebruiker er zeker van is dat ze een redelijke zekerheid hebben. Het afdwingen van deze optie beschermt kritieke resources in situaties waarin het gebruikers account mogelijk is aangetast.

Als u multi-factor Authentication vóór activering wilt vereisen, schakelt u het selectie vakje **multi-factor Authentication vereisen bij activering** in op het tabblad toewijzing van de **instelling rol bewerken**.

Zie [multi-factor Authentication en privileged Identity Management](pim-how-to-require-mfa.md)voor meer informatie.

## <a name="activation-maximum-duration"></a>Maximale activerings duur

Gebruik de schuif regelaar **maximale duur activering** om de maximum tijd in uren in te stellen dat een rol actief blijft voordat deze verloopt. Deze waarde kan een tot 24 uur zijn.

## <a name="require-justification"></a>Reden vereisen

U kunt vereisen dat gebruikers een zakelijke reden opgeven wanneer ze het product activeren. Als u dit wilt doen, schakelt u het selectie vakje **uitvulling vereist op actieve toewijzing** of het vakje **uitvulling vereisen bij activering** in.

## <a name="require-approval-to-activate"></a>Goed keuring vereist om te activeren

Als er meerdere goed keurders worden ingesteld, wordt de goed keuring voltooid zodra een van deze goed keurt of weigert. U kunt geen goed keuring vereisen van ten minste twee gebruikers. Voer de volgende stappen uit om goed keuring te vereisen voor het activeren van een rol.

1. Controleer het selectie vakje **goed keuring vereisen om te activeren** .

1. Selecteer **goed keurders selecteren**.

    ![Selecteer een gebruikers-of groeps deel venster voor het selecteren van goed keurders](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Selecteer ten minste één gebruiker en klik vervolgens op **selecteren**. U moet ten minste één fiatteur selecteren. Er zijn geen standaard fiatteurs.

    Uw selecties worden weer gegeven in de lijst met geselecteerde goed keurders.

1. Wanneer u de instellingen van uw rol hebt opgegeven, selecteert u **bijwerken** om uw wijzigingen op te slaan.

# <a name="previous-version"></a>[Vorige versie](#tab/previous)

## <a name="open-role-settings-previous-version"></a>Rolinstellingen openen (vorige versie)

Volg deze stappen om de instellingen voor een Azure AD-functie te openen.

1. Open **Azure AD privileged Identity Management**.

1. Selecteer **Azure AD-rollen**.

1. Selecteer **Instellingen**.

    ![Azure AD-rollen-instellingen](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. Selecteer **Rollen**.

1. Selecteer de rol waarvan u de instellingen wilt configureren.

    ![Azure AD-rollen-instellingen rollen](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    Op de pagina instellingen voor elke rol zijn verschillende instellingen die u kunt configureren. Deze instellingen zijn alleen van invloed op gebruikers die **in aanmerking komen** voor toewijzingen, niet **permanente** toewijzingen.

## <a name="activations"></a>Activeringen

Gebruik de schuif regelaar **activeringen** om de maximale tijd in uren in te stellen dat een rol actief blijft voordat deze verloopt. Deze waarde kan tussen 1 en 72 uur liggen.

## <a name="notifications"></a>Meldingen

Gebruik de optie **meldingen** om op te geven of beheerders e-mail meldingen zullen ontvangen wanneer rollen worden geactiveerd. Deze melding kan handig zijn voor het detecteren van ongeoorloofde of illegitimate activeringen.

Wanneer deze optie is ingesteld op **inschakelen**, worden meldingen verzonden naar:

- Beheerder voor bevoorrechte rollen
- Beveiligingsbeheerder
- Globale beheerder

Zie [e-mail meldingen in privileged Identity Management](pim-email-notifications.md)voor meer informatie.

## <a name="incidentrequest-ticket"></a>Incident/aanvraag ticket

Gebruik de switch **incident/aanvraag ticket** om in aanmerking komende beheerders te vragen om een ticket nummer op te nemen wanneer ze hun rol activeren. Met deze procedure kunt u toegangs controles voor rollen effectiever uitvoeren.

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Gebruik de schakel optie **multi-factor Authentication** om op te geven of gebruikers hun identiteit moeten verifiëren met MFA voordat ze hun rollen kunnen activeren. Ze hoeven hun identiteit slechts eenmaal per sessie te verifiëren, niet elke keer dat ze een rol activeren. Er zijn twee tips die u moet onthouden wanneer u MFA inschakelt:

- Gebruikers met micro soft-accounts voor hun e-mail adressen (doorgaans @outlook.com , maar niet altijd) kunnen niet worden geregistreerd voor Azure AD-multi-factor Authentication. Als u rollen wilt toewijzen aan gebruikers met micro soft-accounts, moet u deze permanent beheerder maken of multi-factor Authentication uitschakelen voor die rol.
- U kunt Azure AD-Multi-Factor Authentication niet uitschakelen voor rollen met een hoge bevoegdheden voor Azure AD en Microsoft 365. Deze veiligheids functie helpt bij het beveiligen van de volgende rollen:  
  
  - Azure Information Protection beheerder
  - Factureringsbeheerder
  - Beheerder van de Cloud toepassing
  - Beheerder voor naleving
  - Voorwaardelijke-toegangsbeheerder
  - Dynamics 365-beheerder
  - Toegangs fiatteur voor klanten-LockBox
  - Schrijvers van mappen
  - Exchange-beheerder
  - Globale beheerder
  - Intune-beheerder
  - Power BI beheerder
  - Beheerder voor bevoorrechte rollen
  - Beveiligingsbeheerder
  - SharePoint-beheerder
  - Skype voor Bedrijven-beheerder
  - Gebruikersbeheerder

Zie [multi-factor Authentication en privileged Identity Management](pim-how-to-require-mfa.md)voor meer informatie.

## <a name="require-approval"></a>Goed keuring vereisen

Als u de vereiste goed keuring wilt delegeren om een rol te activeren, voert u de volgende stappen uit.

1. Stel de schakel optie **goed keuring vereisen** in op **ingeschakeld**. Het deel venster wordt uitgebreid met opties voor het selecteren van goed keurders.

    ![Scherm afbeelding met de schakel optie goed keuring vereisen als ' inschakelen ' is geselecteerd.](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    Als u geen goed keurders opgeeft, wordt de beheerder van de bevoegde rol de standaard fiatteur. vervolgens moet alle activerings aanvragen voor deze rol worden goedgekeurd.

1. Als u goed keurders wilt toevoegen, klikt u op **fiatteurs selecteren**.

    ![Azure AD-rollen-instellingen-goed keuring vereisen](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. Selecteer een of meer goed keurders naast de beheerder van de geprivilegieerde rol en klik vervolgens op **selecteren**. We raden u aan ten minste twee goed keurders toe te voegen. Zelfs als u uzelf als fiatteur toevoegt, kunt u een functie activering niet zelf goed keuren. Uw selecties worden weer gegeven in de lijst met geselecteerde goed keurders.

1. Nadat u de instellingen van uw rol hebt opgegeven, selecteert u **Opslaan** om uw wijzigingen op te slaan.

---

## <a name="next-steps"></a>Volgende stappen

- [Azure AD-rollen toewijzen in Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Beveiligings waarschuwingen configureren voor Azure AD-rollen in Privileged Identity Management](pim-how-to-configure-security-alerts.md)

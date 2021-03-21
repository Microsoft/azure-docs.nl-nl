---
title: Azure AD-rollen toewijzen in PIM-Azure Active Directory | Microsoft Docs
description: Meer informatie over het toewijzen van Azure AD-rollen in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 02/16/2021
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd4374067fe0070c379a76ef5f59bb6aef5b29fc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102123102"
---
# <a name="assign-azure-ad-roles-in-privileged-identity-management"></a>Azure AD-rollen toewijzen in Privileged Identity Management

Met Azure Active Directory (Azure AD) kan een globale beheerder **permanente** Azure AD-beheerdersrol maken. Deze roltoewijzingen kunnen worden gemaakt met behulp van de [Azure Portal](../roles/permissions-reference.md) of met behulp van [Power shell-opdrachten](/powershell/module/azuread#directory_roles).

Met de service Azure AD Privileged Identity Management (PIM) kunnen beheerders van beschermde rollen ook permanente toewijzingen voor beheerdersrol maken. Daarnaast kunnen beheerders van geprivilegieerde rollen gebruikers die **in aanmerking komen** voor Azure AD-beheerders rollen. Een in aanmerking komende beheerder kan de rol activeren wanneer hij deze nodig heeft en vervolgens de machtigingen verloopt zodra ze zijn voltooid.

## <a name="determine-your-version-of-pim"></a>Uw versie van PIM bepalen

Vanaf november 2019 wordt het gedeelte van de Azure AD-functies van Privileged Identity Management bijgewerkt naar een nieuwe versie die overeenkomt met de ervaringen van Azure-resource rollen. Hiermee maakt u aanvullende functies en [wijzigingen in de bestaande API](azure-ad-roles-features.md#api-changes). Terwijl de nieuwe versie wordt geïmplementeerd, zijn de procedures die u in dit artikel volgt, afhankelijk van de versie van Privileged Identity Management die u momenteel hebt. Volg de stappen in deze sectie om te bepalen welke versie van Privileged Identity Management u hebt. Nadat u uw versie van Privileged Identity Management weet, kunt u de procedures in dit artikel selecteren die overeenkomen met die versie.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met een gebruiker die zich in de beheerdersrol met [geprivilegieerde rol](../roles/permissions-reference.md#privileged-role-administrator) bevindt.
1. Open **Azure AD privileged Identity Management**. Als u een banner aan de bovenkant van de overzichts pagina hebt, volgt u de instructies op het tabblad **nieuwe versie** van dit artikel. Als dat niet het geval is, volgt u de instructies op het tabblad **vorige versie** .

  [![Selecteer Azure AD > Privileged Identity Management.](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[Nieuwe versie](#tab/new)

## <a name="assign-a-role"></a>Een rol toewijzen

Volg deze stappen om een gebruiker in aanmerking te laten komen voor een Azure AD-beheerdersrol.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/) met een gebruiker die lid is van de beheerdersrol [privileged Role](../roles/permissions-reference.md#privileged-role-administrator) .

    Voor informatie over het verlenen van een andere beheerder toegang tot het beheren van Privileged Identity Management raadpleegt u [toegang verlenen aan andere beheerders om privileged Identity Management te beheren](pim-how-to-give-access-to-pim.md).

1. Open **Azure AD privileged Identity Management**.

1. Selecteer **Azure AD-rollen**.

1. Selecteer **rollen** om de lijst met rollen voor Azure AD-machtigingen weer te geven.

    ![Scherm afbeelding van de pagina ' roles ' waarvoor de actie ' toewijzingen toevoegen ' is geselecteerd.](./media/pim-how-to-add-role-to-user/roles-list.png)

1. Selecteer **toewijzingen toevoegen** om de pagina **toewijzingen toevoegen** te openen.

1. Selecteer **een rol selecteren** om de pagina **een rol selecteren** te openen.

    ![Deel venster nieuwe toewijzing](./media/pim-how-to-add-role-to-user/select-role.png)

1. Selecteer een rol die u wilt toewijzen, selecteer een lid waaraan u de rol wilt toewijzen en selecteer vervolgens **volgende**.

1. Selecteer in de lijst **toewijzings type** in het deel venster **lidmaatschaps instellingen** in **aanmerking komend** of **actief**.

    - Voor **in aanmerking komende** toewijzingen moet het lid van de rol een actie uitvoeren om de rol te gebruiken. Acties kunnen bijvoorbeeld een meervoudige verificatiecontrole, het opgeven van een zakelijke reden of het vragen om toestemming bij aangewezen fiatteurs zijn.

    - Voor **actieve** toewijzingen hoeft het lid geen actie te ondernemen om de rol te gebruiken. Aan leden die als actief zijn toegewezen, zijn de bevoegdheden altijd toegewezen aan de rol.

1. Als u een specifieke toewijzings duur wilt opgeven, voegt u een begin-en eind datum en-tijd vakken toe. Wanneer u klaar bent, selecteert u **toewijzen** om de nieuwe roltoewijzing te maken.

    ![Instellingen voor lidmaatschappen-datum en tijd](./media/pim-how-to-add-role-to-user/start-and-end-dates.png)

1. Nadat de rol is toegewezen, wordt er een melding over een toewijzings status weer gegeven.

    ![Nieuwe toewijzing-melding](./media/pim-how-to-add-role-to-user/assignment-notification.png)

## <a name="assign-a-role-with-restricted-scope"></a>Een rol toewijzen met een beperkt bereik

Voor bepaalde rollen geldt dat het bereik van de verleende machtigingen kan worden beperkt tot één beheer eenheid, Service-Principal of toepassing. Deze procedure is een voor beeld van het toewijzen van een rol die het bereik van een administratieve eenheid heeft. Zie [scoped rollen toewijzen aan een beheer eenheid](../roles/admin-units-assign-roles.md)voor een lijst met rollen die bereik ondersteunen via beheer eenheden. Deze functie wordt momenteel geïmplementeerd naar Azure AD-organisaties.

1. Meld u aan bij het [beheer centrum van Azure Active Directory](https://aad.portal.azure.com) met privileged Role Administrator Permissions.

1. Selecteer **Azure Active Directory**-  >  **rollen en-beheerders**.

1. Selecteer de **gebruikers beheerder**.

    ![De opdracht toewijzing toevoegen is beschikbaar wanneer u een rol opent in de portal](./media/pim-how-to-add-role-to-user/add-assignment.png)

1. Selecteer **toewijzingen toevoegen**.

    ![Wanneer een rol bereik ondersteunt, kunt u een bereik selecteren](./media/pim-how-to-add-role-to-user/add-scope.png)

1. Op de pagina **toewijzingen toevoegen** kunt u het volgende doen:

   - Selecteer een gebruiker of groep die aan de rol moet worden toegewezen
   - Het gebruikersrol bereik selecteren (in dit geval administratieve eenheden)
   - Een administratieve eenheid voor de scope selecteren

Zie [beheer eenheden toevoegen en verwijderen](../roles/admin-units-manage.md)voor meer informatie over het maken van administratieve eenheden.

## <a name="update-or-remove-an-existing-role-assignment"></a>Een bestaande roltoewijzing bijwerken of verwijderen

Volg deze stappen om een bestaande roltoewijzing bij te werken of te verwijderen. **Alleen klanten met een licentie voor Azure AD P2**: wijs een groep niet als actief toe aan een rol via Azure ad en PRIVILEGED Identity Management (PIM). Zie [bekende problemen](../roles/groups-concept.md#known-issues)voor een gedetailleerde uitleg.

1. Open **Azure AD privileged Identity Management**.

1. Selecteer **Azure AD-rollen**.

1. Selecteer **rollen** om de lijst met rollen voor Azure AD weer te geven.

1. Selecteer de rol die u wilt bijwerken of verwijderen.

1. Zoek naar de roltoewijzing op de tabbladen **in aanmerking komende rollen** of **actieve rollen** .

    ![Roltoewijzing bijwerken of verwijderen](./media/pim-how-to-add-role-to-user/remove-update-assignments.png)

1. Selecteer **bijwerken** of **verwijderen** om de roltoewijzing bij te werken of te verwijderen.

# <a name="previous-version"></a>[Vorige versie](#tab/previous)

## <a name="make-a-user-eligible-for-a-role"></a>Een gebruiker geschikt maken voor een rol

Volg deze stappen om een gebruiker in aanmerking te laten komen voor een Azure AD-beheerdersrol.

1. Selecteer **functies** of **leden**.

    ![Azure AD-rollen openen](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. Selecteer **lid toevoegen** om **beheerde leden toevoegen** te openen.

1. Selecteer **een rol selecteren**, selecteer een rol die u wilt beheren en selecteer vervolgens **selecteren**.

    ![Een rol selecteren](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. Selecteer **leden selecteren**, selecteer de gebruikers die u aan de rol wilt toewijzen en selecteer vervolgens **selecteren**.

    ![Selecteer een gebruiker of groep die u wilt toewijzen](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. Selecteer in **beheerde leden toevoegen** de optie **OK** om de gebruiker aan de rol toe te voegen.

1. Selecteer in de lijst met rollen de rol die u zojuist hebt toegewezen om de lijst met leden weer te geven.

     Wanneer de rol wordt toegewezen, wordt de gebruiker die u hebt geselecteerd, weer gegeven in de lijst met leden die in **aanmerking komt** voor de rol.

    ![Gebruiker komt in aanmerking voor een rol](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Nu de gebruiker in aanmerking komt voor de rol, laat hij ze weten dat ze deze kunnen activeren volgens de instructies in [mijn Azure AD-rollen activeren in privileged Identity Management](pim-how-to-activate-role.md).

    In aanmerking komende beheerders worden tijdens de activering gevraagd om zich te registreren voor Azure AD Multi-Factor Authentication. Als een gebruiker zich niet kan registreren voor MFA of een Microsoft-account (zoals) gebruikt @outlook.com , moet u deze in al hun rollen blijvend maken.

## <a name="make-a-role-assignment-permanent"></a>Een roltoewijzing permanent maken

Standaard zijn nieuwe gebruikers alleen *in aanmerking* voor een Azure AD-beheerdersrol. Volg deze stappen als u een roltoewijzing permanent wilt maken.

1. Open **Azure AD privileged Identity Management**.

1. Selecteer **Azure AD-rollen**.

1. Selecteer **Leden**.

    ![Lijst met leden](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Selecteer een **in aanmerking komende** rol die u permanent wilt maken.

1. Selecteer **meer** en selecteer vervolgens **machtiging maken**.

    ![Roltoewijzing permanent maken](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    De rol wordt nu als **permanent** weer gegeven.

    ![Lijst met leden met permanente wijziging](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>Een gebruiker uit een rol verwijderen

U kunt gebruikers verwijderen uit roltoewijzingen, maar zorg ervoor dat er altijd ten minste één gebruiker is die een permanente globale beheerder is. Als u niet zeker weet welke gebruikers nog steeds hun roltoewijzingen nodig hebben, kunt u [een toegangs beoordeling starten voor de rol](pim-how-to-start-security-review.md).

Volg deze stappen om een specifieke gebruiker te verwijderen uit een Azure AD-beheerdersrol.

1. Open **Azure AD privileged Identity Management**.

1. Selecteer **Azure AD-rollen**.

1. Selecteer **Leden**.

    ![Lijst met leden](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Selecteer een roltoewijzing die u wilt verwijderen.

1. Selecteer **meer** en selecteer vervolgens **verwijderen**.

    ![Een rol verwijderen](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. Selecteer **Ja** in het bericht waarin u wordt gevraagd om te bevestigen.

    ![Het verwijderen bevestigen](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    De roltoewijzing wordt verwijderd.

## <a name="authorization-error-when-assigning-roles"></a>Autorisatie fout bij het toewijzen van rollen

Als u onlangs Privileged Identity Management hebt ingeschakeld voor een abonnement en er een autorisatie fout optreedt wanneer u een gebruiker wilt maken die in aanmerking komt voor een Azure AD-beheerdersrol, kan dit zijn omdat de MS-PIM-Service-Principal nog niet over de juiste machtigingen beschikt. De MS-PIM-Service-Principal moet de rol [beheerder voor gebruikers toegang](../../role-based-access-control/built-in-roles.md#user-access-administrator) hebben om rollen aan anderen toe te wijzen. In plaats van te wachten totdat MS-PIM is toegewezen aan de rol beheerder van gebruikers toegang, kunt u deze hand matig toewijzen.

Volg deze stappen om de rol gebruikers toegang beheerder toe te wijzen aan de MS-PIM-service-principal voor een abonnement.

1. Meld u aan bij de Azure Portal als globale beheerder.

1. Kies **alle services** en vervolgens **abonnementen**.

1. Kies uw abonnement.

1. Kies **Toegangsbeheer (IAM)**.

1. Kies roltoewijzingen om de huidige lijst met **roltoewijzingen in het** abonnements bereik weer te geven.

   ![Blade toegangs beheer (IAM) voor een abonnement](./media/pim-how-to-add-role-to-user/ms-pim-access-control.png)

1. Controleer of de **MS-PIM** -Service-Principal de rol **beheerder van gebruikers toegang** toegewezen heeft gekregen.

1. Als dat niet het geval is, kiest u **roltoewijzing toevoegen** om het deel venster **roltoewijzing toevoegen** te openen.

1. Selecteer in de vervolg keuzelijst **rol** de rol beheerder voor **gebruikers toegang** .

1. Zoek en selecteer de **MS-PIM-service-** Principal in de **selectie** lijst.

   ![Deel venster roltoewijzing toevoegen-machtigingen voor MS-PIM-Service-Principal toevoegen](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. Kies **Opslaan** om de rol toe te wijzen.

   Na enkele ogen blikken is de MS-PIM-Service-Principal toegewezen aan de rol van beheerder voor gebruikers toegang op het abonnements bereik.

   ![Toegangs beheer pagina met de toewijzing van de beheerderrol voor de MS-PIM-Service-Principal](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)

 ---

## <a name="next-steps"></a>Volgende stappen

- [Instellingen voor Azure AD-beheerdersrol configureren in Privileged Identity Management](pim-how-to-change-default-settings.md)
- [Azure-resource rollen toewijzen in Privileged Identity Management](pim-resource-roles-assign-roles.md)
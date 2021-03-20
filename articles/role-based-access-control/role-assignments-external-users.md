---
title: Azure-rollen toewijzen aan externe gast gebruikers met behulp van de Azure Portal-Azure RBAC
description: Meer informatie over het verlenen van toegang tot Azure-resources voor gebruikers buiten een organisatie met behulp van de Azure Portal en Azure op rollen gebaseerd toegangs beheer (Azure RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/15/2021
ms.author: rolyon
ms.custom: it-pro
ms.openlocfilehash: d834f4ccd8dba26c895e0578f161813fc49332ea
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100556291"
---
# <a name="assign-azure-roles-to-external-guest-users-using-the-azure-portal"></a>Azure-rollen toewijzen aan externe gast gebruikers met behulp van de Azure Portal

[Azure RBAC (op rollen gebaseerd toegangs beheer)](overview.md) biedt betere beveiliging voor grote organisaties en voor kleine en middel grote bedrijven die werken met externe deel nemers, leveranciers of freelancers die toegang nodig hebben tot specifieke bronnen in uw omgeving, maar niet noodzakelijkerwijs op de volledige infra structuur of een facturerings bereik. U kunt de mogelijkheden van [Azure Active Directory B2B](../active-directory/external-identities/what-is-b2b.md) gebruiken om samen te werken met externe gast gebruikers en u kunt Azure RBAC gebruiken om alleen de machtigingen te verlenen die gast gebruikers in uw omgeving nodig hebben.

## <a name="prerequisites"></a>Vereisten

Als u Azure-rollen wilt toewijzen of roltoewijzingen wilt verwijderen, hebt u het volgende nodig:

- Machtigingen voor `Microsoft.Authorization/roleAssignments/write` en `Microsoft.Authorization/roleAssignments/delete`, zoals [Beheerder van gebruikerstoegang](built-in-roles.md#user-access-administrator) of [Eigenaar](built-in-roles.md#owner)


## <a name="when-would-you-invite-guest-users"></a>Wanneer wilt u gast gebruikers uitnodigen?

Hier volgen enkele voor beelden van scenario's waarin u gast gebruikers kunt uitnodigen voor uw organisatie en machtigingen kunt verlenen:

- Een externe selfservice leverancier toestaan die alleen een e-mail account heeft voor toegang tot uw Azure-resources voor een project.
- Een externe partner toestaan bepaalde bronnen of een volledig abonnement te beheren.
- Ondersteunings medewerkers die niet in uw organisatie (zoals micro soft support) zijn, toestaan om tijdelijk toegang te krijgen tot uw Azure-resource om problemen op te lossen.

## <a name="permission-differences-between-member-users-and-guest-users"></a>Machtigings verschillen tussen gebruikers van leden en gast gebruikers

Systeem eigen leden van een map (gebruikers van leden) hebben andere machtigingen dan gebruikers die zijn uitgenodigd vanuit een andere directory als B2B-samenwerkings gast (gast gebruikers). Leden van de gebruiker kunnen bijvoorbeeld bijna alle Directory gegevens lezen terwijl gast gebruikers beperkte mapmachtigingen hebben. Zie [Wat zijn de standaard machtigingen voor gebruikers in azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md)voor meer informatie over gebruikers van leden en gast gebruikers.

## <a name="add-a-guest-user-to-your-directory"></a>Een gastgebruiker toevoegen aan uw map

Volg deze stappen om een gast gebruiker toe te voegen aan uw directory via de pagina Azure Active Directory.

1. Zorg ervoor dat de instellingen voor externe samen werking van uw organisatie zodanig zijn geconfigureerd dat u gasten kunt uitnodigen. Zie [externe B2B-samen werking inschakelen en beheren wie gasten kan uitnodigen](../active-directory/external-identities/delegate-invitations.md)voor meer informatie.

1. Klik in de Azure Portal op **Azure Active Directory**  >  **gebruikers**  >  **nieuwe gast gebruiker**.

    ![Nieuwe functie gast gebruiker in Azure Portal](./media/role-assignments-external-users/invite-guest-user.png)

1. Volg de stappen om een nieuwe gast gebruiker toe te voegen. Zie [Azure Active Directory B2B-samenwerkings gebruikers toevoegen in de Azure Portal](../active-directory/external-identities/add-users-administrator.md#add-guest-users-to-the-directory)voor meer informatie.

Nadat u een gast gebruiker aan de Directory hebt toegevoegd, kunt u de gast gebruiker een rechtstreekse koppeling sturen naar een gedeelde app, of de gast gebruiker kan klikken op de opname-URL in het e-mail bericht.

![E-mail uitnodiging gast gebruiker](./media/role-assignments-external-users/invite-email.png)

De gast gebruiker moet het uitnodigings proces volt ooien om toegang te kunnen krijgen tot uw Directory.

![Machtigingen voor controleren door gast gebruiker uitnodigen](./media/role-assignments-external-users/invite-review-permissions.png)

Zie [Azure Active Directory uitnodiging voor B2B-samen werking](../active-directory/external-identities/redemption-experience.md)voor meer informatie over het uitnodigings proces.

## <a name="assign-a-role-to-a-guest-user"></a>Een rol toewijzen aan een gast gebruiker

In azure RBAC wijst u een rol toe om toegang te verlenen. Als u een rol aan een gast gebruiker wilt toewijzen, voert u [dezelfde stappen uit](role-assignments-portal.md) als voor een gebruiker, groep, Service-Principal of beheerde identiteit. Volg deze stappen om een rol toe te wijzen aan een gast gebruiker in verschillende bereiken.

1. Klik in Azure Portal op **Alle services**.

1.  Selecteer de set resources waarop de toegang van toepassing is, ook wel bekend als het bereik. U kunt bijvoorbeeld **beheer groepen**, **abonnementen**, **resource groepen** of een resource selecteren.

1. Klik op de specifieke resource.

1. Klik op **Toegangsbeheer (IAM)**.

    In de volgende schermopname ziet u een voorbeeld van de blade Toegangsbeheer (IAM) voor een resourcegroep. Als u hier toegangscontrole wijzigingen aanbrengt, zijn deze alleen van toepassing op de resource groep.

    ![Blade toegangs beheer (IAM) voor een resource groep](./media/role-assignments-external-users/access-control-resource-group.png)

1. Klik op **het tabblad roltoewijzingen om** alle roltoewijzingen in dit bereik weer te geven.

1. Klik op **Toevoegen** > **Roltoewijzing toevoegen** om het deelvenster Roltoewijzing toevoegen te openen.

    Als u niet bent gemachtigd voor het toewijzen van rollen, is de optie Roltoewijzing toevoegen uitgeschakeld.

    ![Menu Roltoewijzing toevoegen](./media/shared/add-role-assignment-menu.png)

    Het deelvenster Roltoewijzing toevoegen wordt geopend.

1. Selecteer in de vervolgkeuzelijst **Rol** een rol, zoals **Inzender voor virtuele machines**.

1. Selecteer de gast gebruiker in de **selectie** lijst. Als de gebruiker niet in de lijst wordt weer gegeven, kunt u in het **selectie** vakje typen om de map te doorzoeken op weergave namen, e-mail adressen en object-id's.

   ![Deelvenster Roltoewijzing toevoegen](./media/role-assignments-external-users/add-role-assignment.png)

1. Klik op **Opslaan** om de rol toe te wijzen aan het geselecteerde bereik.

    ![Roltoewijzing voor Inzender voor virtuele machines](./media/role-assignments-external-users/access-control-role-assignments.png)

## <a name="assign-a-role-to-a-guest-user-not-yet-in-your-directory"></a>Een rol toewijzen aan een gast gebruiker die nog niet in uw directory is

Als u een rol aan een gast gebruiker wilt toewijzen, voert u [dezelfde stappen uit](role-assignments-portal.md) als voor een gebruiker, groep, Service-Principal of beheerde identiteit.

Als de gast gebruiker zich nog niet in uw directory bevindt, kunt u de gebruiker rechtstreeks uitnodigen vanuit het deel venster roltoewijzing toevoegen.

1. Klik in Azure Portal op **Alle services**.

1.  Selecteer de set resources waarop de toegang van toepassing is, ook wel bekend als het bereik. U kunt bijvoorbeeld **beheer groepen**, **abonnementen**, **resource groepen** of een resource selecteren.

1. Klik op de specifieke resource.

1. Klik op **Toegangsbeheer (IAM)**.

1. Klik op **het tabblad roltoewijzingen om** alle roltoewijzingen in dit bereik weer te geven.

1. Klik op **Toevoegen** > **Roltoewijzing toevoegen** om het deelvenster Roltoewijzing toevoegen te openen.

    ![Menu Roltoewijzing toevoegen](./media/shared/add-role-assignment-menu.png)

    Het deelvenster Roltoewijzing toevoegen wordt geopend.

1. Selecteer in de vervolgkeuzelijst **Rol** een rol, zoals **Inzender voor virtuele machines**.

1. Typ in de **selectie** lijst het e-mail adres van de persoon die u wilt uitnodigen en selecteer deze persoon.

   ![Gast gebruiker uitnodigen in deel venster roltoewijzing toevoegen](./media/role-assignments-external-users/add-role-assignment-new-guest.png)

1. Klik op **Opslaan** om de gast gebruiker toe te voegen aan uw directory, de rol toe te wijzen en een uitnodiging te verzenden.

    Na enkele ogen blikken ziet u een melding van de roltoewijzing en informatie over de uitnodiging.

    ![Roltoewijzing en uitgenodigde gebruikers meldingen](./media/role-assignments-external-users/invited-user-notification.png)

1. Als u de gast gebruiker hand matig wilt uitnodigen, klikt u met de rechter muisknop op de uitnodiging en kopieert u deze naar de melding. Klik niet op de uitnodiging om het uitnodigings proces te starten.

    De uitnodigings koppeling heeft de volgende indeling:

    `https://invitations.microsoft.com/redeem/...`

1. Verzend de uitnodiging koppeling naar de gast gebruiker om het uitnodigings proces te volt ooien.

    Zie [Azure Active Directory uitnodiging voor B2B-samen werking](../active-directory/external-identities/redemption-experience.md)voor meer informatie over het uitnodigings proces.

## <a name="remove-a-guest-user-from-your-directory"></a>Een gast gebruiker uit uw Directory verwijderen

Voordat u een gast gebruiker uit een Directory verwijdert, moet u eerst eventuele roltoewijzingen voor die gast gebruiker verwijderen. Volg deze stappen om een gast gebruiker uit een directory te verwijderen.

1. **Toegangs beheer (IAM)** openen in een bereik, zoals de beheer groep, het abonnement, de resource groep of de resource, waarbij de gast gebruiker een roltoewijzing heeft.

1. Klik op **het tabblad roltoewijzingen** om alle roltoewijzingen weer te geven.

1. Voeg in de lijst met roltoewijzingen een selectie vakje naast de gast gebruiker toe met de roltoewijzing die u wilt verwijderen.

   ![Roltoewijzing verwijderen](./media/role-assignments-external-users/remove-role-assignment-select.png)

1. Klik op **Verwijderen**.

   ![Bericht bij verwijderen van roltoewijzing](./media/role-assignments-external-users/remove-role-assignment.png)

1. Klik op **Ja** om te bevestigen dat u de roltoewijzing inderdaad wilt verwijderen.

1. Klik in de linker navigatie balk op **Azure Active Directory**  >  **gebruikers**.

1. Klik op de gast gebruiker die u wilt verwijderen.

1. Klik op **Verwijderen**.

   ![Gast gebruiker verwijderen](./media/role-assignments-external-users/delete-guest-user.png)

1. Klik op **Ja** in het bericht verwijderen dat wordt weer gegeven.

## <a name="troubleshoot"></a>Problemen oplossen

### <a name="guest-user-cannot-browse-the-directory"></a>Gast gebruiker kan niet bladeren in de map

Gast gebruikers hebben beperkte mapmachtigingen. Gast gebruikers kunnen bijvoorbeeld niet bladeren in de map en kunnen niet zoeken naar groepen of toepassingen. Zie [Wat zijn de standaard gebruikers machtigingen in azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md)voor meer informatie.

![Gast gebruiker kan niet bladeren naar gebruikers in een map](./media/role-assignments-external-users/directory-no-users.png)

Als een gast gebruiker in de Directory extra bevoegdheden nodig heeft, kunt u een directory-rol toewijzen aan de gast gebruiker. Als u wilt dat een gast gebruiker volledige lees toegang heeft tot uw adres lijst, kunt u de gast gebruiker toevoegen aan de rol van [adreslijst lezers](../active-directory/roles/permissions-reference.md) in azure AD. Zie [machtigingen verlenen aan gebruikers van partner organisaties in uw Azure Active Directory-Tenant](../active-directory/external-identities/add-guest-to-role.md)voor meer informatie.

![Rol van adreslijst lezers toewijzen](./media/role-assignments-external-users/directory-roles.png)

### <a name="guest-user-cannot-browse-users-groups-or-service-principals-to-assign-roles"></a>Gast gebruiker kan niet bladeren naar gebruikers, groepen of service-principals om rollen toe te wijzen

Gast gebruikers hebben beperkte mapmachtigingen. Zelfs als een gast [gebruiker een rol](built-in-roles.md#owner) moet toewijzen om iemand anders toegang te verlenen, kunnen ze niet bladeren in de lijst met gebruikers, groepen of service-principals.

![Gast gebruiker kan niet bladeren in beveiligings-principals om rollen toe te wijzen](./media/role-assignments-external-users/directory-no-browse.png)

Als de gast gebruiker de exacte aanmeldings naam van iemand in de Directory kent, kunnen ze toegang verlenen. Als u wilt dat een gast gebruiker volledige lees toegang heeft tot uw adres lijst, kunt u de gast gebruiker toevoegen aan de rol van [adreslijst lezers](../active-directory/roles/permissions-reference.md) in azure AD. Zie [machtigingen verlenen aan gebruikers van partner organisaties in uw Azure Active Directory-Tenant](../active-directory/external-identities/add-guest-to-role.md)voor meer informatie.

### <a name="guest-user-cannot-register-applications-or-create-service-principals"></a>Gast gebruiker kan toepassingen niet registreren of service-principals maken

Gast gebruikers hebben beperkte mapmachtigingen. Als een gast gebruiker toepassingen kan registreren of service-principals moet maken, kunt u de gast gebruiker toevoegen aan de rol van [toepassings ontwikkelaar](../active-directory/roles/permissions-reference.md) in azure AD. Zie [machtigingen verlenen aan gebruikers van partner organisaties in uw Azure Active Directory-Tenant](../active-directory/external-identities/add-guest-to-role.md)voor meer informatie.

![Gast gebruiker kan geen toepassingen registreren](./media/role-assignments-external-users/directory-access-denied.png)

### <a name="guest-user-does-not-see-the-new-directory"></a>Gast gebruiker ziet de nieuwe map niet

Als een gast gebruiker toegang heeft gekregen tot een directory, maar de nieuwe map die wordt vermeld in de Azure Portal niet wordt weer gegeven wanneer ze overschakelen in hun **Directory-en-abonnements** venster, controleert u of de gast gebruiker het uitnodigings proces heeft voltooid. Zie [Azure Active Directory uitnodiging voor B2B-samen werking](../active-directory/external-identities/redemption-experience.md)voor meer informatie over het uitnodigings proces.

### <a name="guest-user-does-not-see-resources"></a>De gast gebruiker ziet geen resources

Als een gast gebruiker toegang heeft gekregen tot een directory, maar de resources waartoe ze toegang hebben, worden niet weer gegeven in de Azure Portal, moet u ervoor zorgen dat de gast gebruiker de juiste map heeft geselecteerd. Een gast gebruiker heeft mogelijk toegang tot meerdere directory's. Als u wilt scha kelen tussen directory's, klikt u in de linkerbovenhoek op **Directory + abonnement** en klikt u vervolgens op de juiste map.

![Het deel venster mappen en abonnementen in Azure Portal](./media/role-assignments-external-users/directory-subscription.png)

## <a name="next-steps"></a>Volgende stappen

- [Azure Active Directory B2B-samenwerkings gebruikers toevoegen aan de Azure Portal](../active-directory/external-identities/add-users-administrator.md)
- [Eigenschappen van een Azure Active Directory B2B-samenwerkings gebruiker](../active-directory/external-identities/user-properties.md)
- [De elementen van het e-mail adres uitnodiging voor B2B-samen werking-Azure Active Directory](../active-directory/external-identities/invitation-email-elements.md)
- [Een gast gebruiker als co-beheerder toevoegen](classic-administrators.md#add-a-guest-user-as-a-co-administrator)
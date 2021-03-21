---
title: Azure-resource rollen toewijzen aan gasten in PIM-Azure AD | Microsoft Docs
description: Meer informatie over het uitnodigen van externe gast gebruikers en het toewijzen van Azure-resource rollen in Azure AD Privileged Identity Management (PIM).
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
ms.openlocfilehash: f4e2e18f3bb9d1c972d805a60493897d605921e4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96010636"
---
# <a name="invite-guest-users-and-assign-azure-resource-roles-in-privileged-identity-management"></a>Gast gebruikers uitnodigen en Azure-resource rollen toewijzen in Privileged Identity Management

Azure Active Directory (Azure AD) gast gebruikers maken deel uit van de samenwerkings mogelijkheden van Business-to-Business (B2B) in azure AD, zodat u externe gast gebruikers en-leveranciers als gasten kunt beheren in azure AD. Wanneer u B2B-samen werking combineert met Azure AD Privileged Identity Management (PIM), kunt u uw vereisten voor naleving en governance voor gasten uitbreiden. U kunt bijvoorbeeld deze Privileged Identity Management functies gebruiken voor Azure-identiteits taken met gasten:

- Toegang tot specifieke Azure-resources toewijzen
- Just-in-time-toegang inschakelen
- De duur en eind datum van de toewijzing opgeven
- Multi-factor Authentication vereisen voor actieve toewijzing of activering
- Toegangs beoordelingen uitvoeren
- Waarschuwingen en controle Logboeken gebruiken

In dit artikel wordt beschreven hoe u een gast kunt uitnodigen voor uw organisatie en hoe u de toegang tot Azure-resources kunt beheren met Privileged Identity Management.

## <a name="when-would-you-invite-guests"></a>Wanneer wilt u gasten uitnodigen?

Hier volgen enkele voor beelden van wanneer u gasten kunt uitnodigen voor uw organisatie:

- Een externe selfservice leverancier toestaan die alleen een e-mail account heeft voor toegang tot uw Azure-resources voor een project.
- Een externe partner toestaan in een grote organisatie die gebruikmaakt van on-premises Active Directory Federation Services om toegang te krijgen tot uw onkosten toepassing.
- Ondersteunings medewerkers die niet in uw organisatie (zoals micro soft support) zijn, toestaan om tijdelijk toegang te krijgen tot uw Azure-resource om problemen op te lossen.

## <a name="how-does-collaboration-using-b2b-guests-work"></a>Hoe werkt samen werking met B2B-gasten?

Wanneer u B2B-samen werking gebruikt, kunt u een externe gebruiker als gast uitnodigen voor uw organisatie. De gast kan worden beheerd als een gebruiker in uw organisatie, maar een gast moet worden geverifieerd in hun eigen organisatie en niet in uw Azure AD-organisatie. Dit betekent dat als de gast geen toegang meer heeft tot hun thuis organisatie, ook de toegang tot uw organisatie verloren gaat. Als de gast bijvoorbeeld hun organisatie verlaat, verliezen ze automatisch de toegang tot resources die u met hen hebt gedeeld in azure AD zonder dat u iets hoeft te doen. Zie [Wat is gast gebruikers toegang in azure Active Directory B2B?](../external-identities/what-is-b2b.md)voor meer informatie over B2B-samen werking.

![Diagram waarin wordt getoond hoe een gast gebruiker wordt geverifieerd in de hoofdmap](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-guest-collaboration-settings"></a>Instellingen voor samen werking van gast controleren

Om ervoor te zorgen dat u gasten kunt uitnodigen voor uw organisatie, moet u de instellingen voor de samen werking van uw gast controleren.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Selecteer **Azure Active Directory**  >  **gebruikers instellingen**.

1. Selecteer **externe instellingen voor samen werking beheren**.

    ![Pagina instellingen voor externe samen werking met instellingen voor beperkingen voor machtigingen, uitnodigen en samen werking](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. Zorg ervoor dat de **beheerders en gebruikers in de rol gast uitnodigen de schakel optie voor uitnodigingen kunnen** **kiezen.**

## <a name="invite-a-guest-and-assign-a-role"></a>Een gast uitnodigen en een rol toewijzen

Met Privileged Identity Management kunt u een gast uitnodigen en deze in aanmerking komen voor een Azure-resource functie.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/) met een gebruiker die lid is van de rol [privileged Role Administrator](../roles/permissions-reference.md#privileged-role-administrator) of [user Administrator](../roles/permissions-reference.md#user-administrator) .

1. Open **Azure AD privileged Identity Management**.

1. Selecteer **Azure-resources**.

1. Gebruik het **resource filter** om de lijst met beheerde resources te filteren.

1. Selecteer de resource die u wilt beheren, zoals een resource, resource groep, abonnement of beheer groep.

    Stel het bereik in op alleen de vereisten van de gast.

1. Selecteer onder beheren de optie **rollen** om de lijst met rollen voor Azure-resources weer te geven.

    ![De lijst met Azure-resources rollen met het aantal gebruikers dat actief en in aanmerking komt](./media/pim-resource-roles-external-users/resources-roles.png)

1. Selecteer de minimale rol die de gebruiker nodig heeft.

    ![Geselecteerde functie pagina waarop de huidige leden van de rol worden weer gegeven](./media/pim-resource-roles-external-users/selected-role.png)

1. Selecteer **lid toevoegen** op de pagina rol om het deel venster nieuwe toewijzing te openen.

1. Klik op **een lid of groep selecteren**.

    ![Nieuwe toewijzing: een lid of groeps paneel selecteren gebruikers en groepen weer geven samen met een optie voor uitnodigen](./media/pim-resource-roles-external-users/select-member-group.png)

1. Als u een gast wilt uitnodigen, klikt u op **uitnodigen**.

    ![Een gast pagina uitnodigen met vakken om een e-mail adres in te voeren en een persoonlijk bericht op te geven](./media/pim-resource-roles-external-users/invite-guest.png)

1. Nadat u een gast hebt geselecteerd, klikt u op **uitnodigen**.

    De gast moet worden toegevoegd als een geselecteerd lid.

1. Klik in het deel venster **een lid of groep selecteren** op **selecteren**.

1. Selecteer in het deel venster **lidmaatschaps instellingen** het toewijzings type en de duur.

    ![Pagina nieuwe toewijzings instellingen voor lidmaatschap met opties om het toewijzings type, de begin datum en de eind datum op te geven](./media/pim-resource-roles-external-users/membership-settings.png)

1. Als u de toewijzing wilt volt ooien, selecteert u **gereed** en vervolgens **toevoegen**.

    De roltoewijzing van de gast wordt weer gegeven in de lijst met rollen.

    ![Pagina rollen die de gast aanbieden als in aanmerking komend](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-a-guest"></a>Rol activeren als gast

Als u een externe gebruiker bent, moet u de uitnodiging accepteren als gast in de Azure AD-organisatie en mogelijk uw roltoewijzing activeren.

1. Open het e-mail bericht met uw uitnodiging. Het e-mail bericht ziet er ongeveer als volgt uit.

    ![Uitnodiging voor e-mail met mapnaam, persoonlijk bericht en een koppeling aan de slag](./media/pim-resource-roles-external-users/email-invite.png)

1. Selecteer de koppeling **aan de slag** in het e-mail bericht.

1. Nadat u de machtigingen hebt bekeken, klikt u op **accepteren**.

    ![De pagina Machtigingen controleren in een browser met een lijst met machtigingen die de organisatie wil laten beoordelen](./media/pim-resource-roles-external-users/invite-accept.png)

1. U wordt mogelijk gevraagd om een gebruiksrecht overeenkomst te accepteren en op te geven of u aangemeld wilt blijven. Als u in *aanmerking komt* voor een rol, hebt u In de Azure Portal nog geen toegang tot resources.

1. Als u de roltoewijzing wilt activeren, opent u het e-mail bericht met de koppeling rol activeren. Het e-mail bericht ziet er ongeveer als volgt uit.

    ![E-mail die aangeeft dat u in aanmerking komt voor een rol met de koppeling rol activeren](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. Selecteer **rol activeren** om uw in aanmerking komende rollen te openen in privileged Identity Management.

    ![Pagina mijn rollen in Privileged Identity Management uw in aanmerking komende rollen weer geven](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. Selecteer onder actie de koppeling **activeren** .

    Afhankelijk van de rolinstellingen moet u bepaalde informatie opgeven om de rol te activeren.

1. Zodra u de instellingen voor de rol hebt opgegeven, klikt u op **activeren** om de rol te activeren.

    ![Het bereik en de opties van de pagina vermelding activeren om de begin tijd, duur en reden op te geven](./media/pim-resource-roles-external-users/activate-role.png)

    Tenzij de beheerder vereist is om uw aanvraag goed te keuren, moet u toegang hebben tot de opgegeven resources.

## <a name="view-activity-for-a-guest"></a>Activiteit voor een gast weer geven

U kunt audit logboeken bekijken om bij te houden wat gasten doen.

1. Als beheerder opent u Privileged Identity Management en selecteert u de resource die is gedeeld.

1. Selecteer **resource audit** om de activiteit voor die resource weer te geven. Hieronder ziet u een voor beeld van de activiteit voor een resource groep.

    ![Azure-resources-resource audit pagina met een lijst met de tijd, aanvrager en actie](./media/pim-resource-roles-external-users/audit-resource.png)

1. Als u de activiteit voor de gast wilt weer geven, selecteert u **Azure Active Directory**  >  **gebruikers**  >  *naam gast*.

1. Selecteer **audit logboeken** om de audit logboeken voor de organisatie te bekijken. Als dat nodig is, kunt u filters opgeven.

    ![Map audit logboeken datum, doel, geïnitieerd door en activiteit](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>Volgende stappen

- [Azure AD-beheerders rollen toewijzen in Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Wat is gast gebruikers toegang in azure AD B2B-samen werking?](../external-identities/what-is-b2b.md)
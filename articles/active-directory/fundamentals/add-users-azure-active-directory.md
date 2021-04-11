---
title: Gebruikers toevoegen of verwijderen-Azure Active Directory | Microsoft Docs
description: Instructies voor het toevoegen van nieuwe gebruikers of het verwijderen van bestaande gebruikers met behulp van Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 03/05/2021
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36455b3f6395216bd1ed26c8bd193f0e3ca10b5b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104594594"
---
# <a name="add-or-delete-users-using-azure-active-directory"></a>Gebruikers toevoegen of verwijderen met Azure Active Directory

Voeg nieuwe gebruikers toe of verwijder bestaande gebruikers uit uw Azure Active Directory-organisatie (Azure AD). U moet een gebruikers beheerder of globale beheerder zijn om gebruikers toe te voegen of te verwijderen.

## <a name="add-a-new-user"></a>Een nieuwe gebruiker toevoegen

U kunt een nieuwe gebruiker maken met behulp van de Azure Active Directory Portal.

Voer de volgende stappen uit om een nieuwe gebruiker toe te voegen:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) als een gebruikers beheerder voor de organisatie.

1. Zoek en selecteer *Azure Active Directory* op elke pagina.

1. Selecteer **gebruikers** en selecteer vervolgens **nieuwe gebruiker**.

    ![Een gebruiker toevoegen via gebruikers: alle gebruikers in azure AD](media/add-users-azure-active-directory/add-user-in-users-all-users.png)

1. Voer op de pagina **gebruiker** de gegevens voor deze gebruiker in:

   - **Naam**. Vereist. De voor-en achternaam van de nieuwe gebruiker. Bijvoorbeeld *Mary Parker*.

   - **Gebruikers naam**. Vereist. De gebruikers naam van de nieuwe gebruiker. Bijvoorbeeld `mary@contoso.com`.

     Het domein gedeelte van de gebruikers naam moet ofwel de oorspronkelijke standaard domein naam, *\<yourdomainname> . onmicrosoft.com* of een aangepaste domein naam, zoals *contoso.com*, gebruiken. Zie [uw aangepaste domein naam toevoegen met behulp van de Azure Active Directory-Portal](add-custom-domain.md)voor meer informatie over het maken van een aangepaste domein naam.

   - **Groepen**. U kunt de gebruiker eventueel toevoegen aan een of meer bestaande groepen. U kunt de gebruiker ook op een later tijdstip toevoegen aan groepen. Zie [een basis groep maken en leden toevoegen met Azure Active Directory](active-directory-groups-create-azure-portal.md)voor meer informatie over het toevoegen van gebruikers aan groepen.

   - **Directory-rol**: Als u Azure AD-beheermachtigingen nodig hebt voor de gebruiker, kunt u ze aan een Azure AD-rol toevoegen. U kunt de gebruiker toewijzen als globale beheerder of een of meer van de beperkte beheerders rollen in azure AD. Zie [rollen toewijzen aan gebruikers](active-directory-users-assign-role-azure-portal.md)voor meer informatie over het toewijzen van rollen.

   - **Taak info**: u kunt hier meer informatie over de gebruiker toevoegen of dit later doen. Zie [informatie over het toevoegen of wijzigen van gebruikers profielen](active-directory-users-profile-azure-portal.md)voor meer informatie over het toevoegen van gebruikers gegevens.

1. Kopieer het automatisch gegenereerde wacht woord dat is opgegeven in het vak **wacht woord** . U moet dit wacht woord aan de gebruiker geven om zich voor de eerste keer aan te melden.

1. Selecteer **Maken**.

De gebruiker wordt gemaakt en toegevoegd aan uw Azure AD-organisatie.

## <a name="add-a-new-guest-user"></a>Een nieuwe gast gebruiker toevoegen

U kunt ook een nieuwe gast gebruiker uitnodigen om samen te werken met uw organisatie door **gebruikers uitnodigen** te selecteren op de pagina **nieuwe gebruiker** . Als de instellingen voor externe samen werking van uw organisatie zodanig zijn geconfigureerd dat u gasten kunt uitnodigen, wordt de gebruiker een uitnodiging per e-mail verzonden om te beginnen met samen werking. Zie [B2B-gebruikers uitnodigen voor Azure Active Directory](../external-identities/add-users-administrator.md) voor meer informatie over het uitnodigen van B2B-samenwerkings gebruikers

## <a name="add-a-consumer-user"></a>Een consument gebruiker toevoegen

Er zijn mogelijk scenario's waarin u hand matig consumenten accounts wilt maken in de map Azure Active Directory B2C (Azure AD B2C). Zie voor meer informatie over het maken van consumenten accounts [consumenten gebruikers maken en verwijderen in azure AD B2C](../../active-directory-b2c/manage-users-portal.md).

## <a name="add-a-new-user-within-a-hybrid-environment"></a>Een nieuwe gebruiker toevoegen in een hybride omgeving

Als u een omgeving met Azure Active Directory (Cloud) en Windows Server-Active Directory (on-premises) hebt, kunt u nieuwe gebruikers toevoegen door de bestaande gegevens van het gebruikers account te synchroniseren. Zie [uw on-premises Directory's integreren met Azure Active Directory](../hybrid/whatis-hybrid-identity.md)voor meer informatie over hybride omgevingen en gebruikers.

## <a name="delete-a-user"></a>Een gebruiker verwijderen

U kunt een bestaande gebruiker verwijderen via Azure Active Directory Portal.

>[!Note]
>U moet een globale beheerder of beheerderrol hebben om gebruikers in uw organisatie te kunnen verwijderen. Globale beheerders kunnen alle gebruikers, waaronder andere beheerders, verwijderen. Gebruikers beheerders kunnen alle niet-beheerders gebruikers, helpdesk beheerders en andere gebruikers beheerders verwijderen. Zie [Administrator role permissions in azure AD](../roles/permissions-reference.md)(Engelstalig) voor meer informatie.

Voer de volgende stappen uit om een gebruiker te verwijderen:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met behulp van een gebruikers beheerders account voor de organisatie.

1. Zoek en selecteer *Azure Active Directory* op elke pagina.

1. Zoek en selecteer de gebruiker die u wilt verwijderen uit uw Azure AD-Tenant. Bijvoorbeeld _Mary Parker_.

1. Selecteer **Gebruiker verwijderen**.

    ![Gebruikers-pagina alle gebruikers met gemarkeerde gebruiker verwijderen](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

De gebruiker wordt verwijderd en wordt niet meer weer gegeven op de pagina **gebruikers-alle gebruikers** . De gebruiker kan de volgende 30 dagen worden weer gegeven op de pagina **Verwijderde gebruikers** en kan worden hersteld tijdens die tijd. Zie [een onlangs verwijderde gebruiker herstellen of verwijderen met Azure Active Directory](active-directory-users-restore.md)voor meer informatie over het herstellen van een gebruiker.

Wanneer een gebruiker wordt verwijderd, worden alle licenties die door de gebruiker worden gebruikt, beschikbaar gesteld voor andere gebruikers.

>[!Note]
>Als u de identiteit, contact gegevens of taak gegevens wilt bijwerken voor gebruikers waarvan de bron van de autoriteit Windows Server Active Directory is, moet u Windows Server Active Directory gebruiken. Nadat u de update hebt voltooid, moet u wachten tot de volgende synchronisatie cyclus is voltooid voordat u de wijzigingen ziet.

## <a name="next-steps"></a>Volgende stappen

Nadat u uw gebruikers hebt toegevoegd, kunt u de volgende basis processen uitvoeren:

- [Profiel gegevens toevoegen of wijzigen](active-directory-users-profile-azure-portal.md)

- [Rollen toewijzen aan gebruikers](active-directory-users-assign-role-azure-portal.md)

- [Een basisgroep maken en leden toevoegen](active-directory-groups-create-azure-portal.md)

- [Werken met dynamische groepen en gebruikers](../enterprise-users/groups-create-rule.md)

U kunt ook andere gebruikers beheer taken uitvoeren, zoals het [toevoegen van gast gebruikers vanuit een andere map](../external-identities/what-is-b2b.md) of [het herstellen van een verwijderde gebruiker](active-directory-users-restore.md). Zie [Azure Active Directory-documentatie voor gebruikers beheer](../enterprise-users/index.yml)voor meer informatie over andere beschik bare acties.
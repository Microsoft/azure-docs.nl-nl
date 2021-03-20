---
title: Azure AD-rollen toewijzen aan gebruikers-Azure Active Directory | Microsoft Docs
description: Instructies voor het toewijzen van beheerders-en niet-beheerders rollen aan gebruikers met Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 08/31/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe0c7eec5c023fcba3152ad6329d318210a0c2ab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92370930"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>Beheerders-en niet-beheerders rollen toewijzen aan gebruikers met Azure Active Directory

Als een van uw gebruikers toestemming nodig heeft om Azure AD-resources te beheren in Azure Active Directory (Azure AD), moet u deze toewijzen aan een rol die de benodigde machtigingen biedt. Voor informatie over welke rollen Azure-resources beheren en welke rollen Azure AD-resources beheren, Zie [klassieke abonnements beheerders rollen, Azure-rollen en Azure AD-rollen](../../role-based-access-control/rbac-and-directory-admin-roles.md).

Zie [beheerders rollen toewijzen in azure Active Directory](../roles/permissions-reference.md)voor meer informatie over de beschik bare Azure AD-rollen. Zie [nieuwe gebruikers toevoegen aan Azure Active Directory](add-users-azure-active-directory.md)om gebruikers toe te voegen.

## <a name="assign-roles"></a>Rollen toewijzen

Een gemeen schappelijke manier om Azure AD-rollen aan een gebruiker toe te wijzen, is op de pagina **toegewezen rollen** voor een gebruiker. U kunt ook de geschiktheid van de gebruiker zo configureren dat deze alleen maar op tijd kan worden uitgebreid naar een rol met behulp van Privileged Identity Management (PIM). Zie [privileged Identity Management](../privileged-identity-management/index.yml)voor meer informatie over het gebruik van Pim.

> [!Note]
> Als u een licentie voor Azure AD Premium P2 hebt en al gebruik maakt van PIM, worden alle rollen beheer taken uitgevoerd in de [privileged Identity Management-ervaring](../roles/manage-roles-portal.md). Deze functie is momenteel beperkt tot het toewijzen van slechts één rol per keer. U kunt momenteel niet meerdere rollen selecteren en deze aan een gebruiker tegelijk toewijzen.
>
> ![Azure AD-rollen die worden beheerd in PIM voor gebruikers die PIM al gebruiken en een Premium P2-licentie hebben](./media/active-directory-users-assign-role-azure-portal/pim-manages-roles-for-p2.png)

## <a name="assign-a-role-to-a-user"></a>Een rol toewijzen aan een gebruiker

1. Ga naar de [Azure Portal](https://portal.azure.com/) en meld u aan met een account voor globale beheerders voor de Directory.

2. Zoek en selecteer de optie **Azure Active Directory**.

      ![Zoekopdracht in de Azure-portal voor Azure Active Directory](media/active-directory-users-assign-role-azure-portal/search-azure-active-directory.png)

3. Selecteer **Gebruikers**.

4. Zoek en selecteer de gebruiker die de roltoewijzing ophaalt. Bijvoorbeeld _Alain Charon_.

      ![Pagina alle gebruikers: Selecteer de gebruiker](media/active-directory-users-assign-role-azure-portal/directory-role-select-user.png)

5. Selecteer **toegewezen rollen** op de pagina **Alain Charon-profiel** .

    De pagina **Alain Charon-administratieve rollen** wordt weer gegeven.

6. Selecteer **toewijzingen toevoegen**, selecteer de rol die u wilt toewijzen aan Alain (bijvoorbeeld _toepassings beheerder_) en kies vervolgens **selecteren**.

    ![Pagina toegewezen functies: de geselecteerde rol wordt weer gegeven](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    De rol toepassings beheerder is toegewezen aan Alain Charon en deze wordt weer gegeven op de pagina **Alain Charon-administratieve rollen** .

## <a name="remove-a-role-assignment"></a>Roltoewijzing verwijderen

Als u de roltoewijzing van een gebruiker wilt verwijderen, kunt u dit ook doen op de pagina **Alain Charon-administratieve rollen** .

### <a name="to-remove-a-role-assignment-from-a-user"></a>Een roltoewijzing van een gebruiker verwijderen

1. Selecteer **Azure Active Directory**, selecteer **gebruikers**, zoek naar en selecteer de gebruiker die de roltoewijzing heeft verwijderd. Bijvoorbeeld _Alain Charon_.

2. Selecteer **toegewezen rollen**, selecteer **toepassings beheerder** en selecteer vervolgens **toewijzing verwijderen**.

    ![Pagina toegewezen functies, waarin de geselecteerde rol en de optie verwijderen worden weer gegeven](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    De rol toepassings beheerder wordt verwijderd uit Alain Charon en wordt niet meer weer gegeven op de pagina **Alain Charon-administratieve rollen** .

## <a name="next-steps"></a>Volgende stappen

- [Gebruikers toevoegen of verwijderen](add-users-azure-active-directory.md)

- [Profiel gegevens toevoegen of wijzigen](active-directory-users-profile-azure-portal.md)

- [Gastgebruikers uit een andere directory toevoegen](../external-identities/what-is-b2b.md)

Andere beheer taken voor gebruikers die u kunt uitchecken, zijn beschikbaar in de [documentatie voor Azure Active Directory gebruikers beheer](../enterprise-users/index.yml).
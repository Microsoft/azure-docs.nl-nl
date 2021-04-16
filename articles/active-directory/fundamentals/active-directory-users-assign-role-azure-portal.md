---
title: Azure AD-rollen toewijzen aan gebruikers - Azure Active Directory | Microsoft Docs
description: Instructies voor het toewijzen van beheerdersrollen en niet-beheerdersrollen aan gebruikers met Azure Active Directory.
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
ms.openlocfilehash: f49e15984b7a673de1e7d1607f4802c17ebef4e2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531842"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>Beheerdersrollen en niet-beheerdersrollen toewijzen aan gebruikers met Azure Active Directory

In Azure Active Directory (Azure AD) moet u, als een van uw gebruikers toestemming nodig heeft om Azure AD-resources te beheren, deze toewijzen aan een rol die de machtigingen biedt die ze nodig hebben. Zie Klassieke abonnementsbeheerdersrollen, Azure-rollen en Azure AD-rollen voor informatie over welke rollen Azure-resources beheren en welke [rollen Azure AD-resources beheren.](../../role-based-access-control/rbac-and-directory-admin-roles.md)

Zie Beheerdersrollen toewijzen in Azure Active Directory voor meer informatie [over de beschikbare Azure AD-Azure Active Directory.](../roles/permissions-reference.md) Zie Add new users to Azure Active Directory als u [gebruikers wilt Azure Active Directory.](add-users-azure-active-directory.md)

## <a name="assign-roles"></a>Rollen toewijzen

Een veelgebruikte manier om Azure AD-rollen toe te wijzen aan een gebruiker is op de pagina **Toegewezen rollen** voor een gebruiker. U kunt ook configureren dat gebruikers in aanmerking komen om Just-In-Time te worden verhoogd naar een rol met behulp Privileged Identity Management (PIM). Zie voor meer informatie over het gebruik van PIM [Privileged Identity Management.](../privileged-identity-management/index.yml)

Als een directoryrol is toegewezen aan een gastgebruiker, krijgt de gastgebruiker extra machtigingen die bij de rol worden gebruikt, waaronder basismachtigingen voor lezen. Zie [Ingebouwde Azure AD-rollen.](https://docs.microsoft.com/azure/active-directory/roles/permissions-reference)

> [!Note]
> Als u een Azure AD Premium P2-licentie hebt en al gebruik maakt van PIM, worden alle rolbeheertaken uitgevoerd in [Privileged Identity Management ervaring](../roles/manage-roles-portal.md). Deze functie is momenteel beperkt tot het toewijzen van slechts één rol tegelijk. U kunt momenteel niet meerdere rollen selecteren en deze in één keer toewijzen aan een gebruiker.
>
> ![Azure AD-rollen die worden beheerd in PIM voor gebruikers die al gebruikmaken van PIM en een Premium P2-licentie hebben](./media/active-directory-users-assign-role-azure-portal/pim-manages-roles-for-p2.png)

## <a name="assign-a-role-to-a-user"></a>Een rol toewijzen aan een gebruiker

1. Ga naar de [Azure Portal](https://portal.azure.com/) meld u aan met een Globale beheerder-account voor de directory.

2. Zoek en selecteer de optie **Azure Active Directory**.

      ![Zoekopdracht in de Azure-portal voor Azure Active Directory](media/active-directory-users-assign-role-azure-portal/search-azure-active-directory.png)

3. Selecteer **Gebruikers**.

4. Zoek en selecteer de gebruiker die de roltoewijzing krijgt. Bijvoorbeeld _Alain Charon._

      ![Pagina Alle gebruikers: selecteer de gebruiker](media/active-directory-users-assign-role-azure-portal/directory-role-select-user.png)

5. Selecteer op **de pagina Alain Charon - Profile** de optie Toegewezen **rollen.**

    De **pagina Alain Charon - Beheerdersrollen** wordt weergegeven.

6. Selecteer **Toewijzingen toevoegen,** selecteer de rol die moet worden toegewezen aan Alain (bijvoorbeeld _Toepassingsbeheerder_) en kies **vervolgens Selecteren.**

    ![Pagina Toegewezen rollen: de geselecteerde rol wordt weergegeven](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    De Toepassingsbeheerder rol wordt toegewezen aan Alain Charon en wordt weergegeven op de pagina **Alain Charon - Beheerdersrollen.**

## <a name="remove-a-role-assignment"></a>Roltoewijzing verwijderen

Als u de roltoewijzing van een gebruiker wilt verwijderen, kunt u dat ook doen op de pagina **Alain Charon - Beheerdersrollen.**

### <a name="to-remove-a-role-assignment-from-a-user"></a>Een roltoewijzing van een gebruiker verwijderen

1. Selecteer **Azure Active Directory**, selecteer **Gebruikers** en zoek en selecteer vervolgens de gebruiker die de roltoewijzing verwijdert. Bijvoorbeeld _Alain Charon._

2. Selecteer **Toegewezen rollen,** selecteer **Toepassingsbeheerder** en selecteer vervolgens **Toewijzing verwijderen.**

    ![De pagina Toegewezen rollen, met de geselecteerde rol en de optie Verwijderen](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    De Toepassingsbeheerder wordt verwijderd uit Alain Charon en wordt niet meer weergegeven op de pagina **Alain Charon - Beheerdersrollen.**

## <a name="next-steps"></a>Volgende stappen

- [Gebruikers toevoegen of verwijderen](add-users-azure-active-directory.md)

- [Profielgegevens toevoegen of wijzigen](active-directory-users-profile-azure-portal.md)

- [Gastgebruikers uit een andere directory toevoegen](../external-identities/what-is-b2b.md)

Andere gebruikersbeheertaken die u kunt uitchecken, zijn beschikbaar in [Azure Active Directory voor gebruikersbeheer.](../enterprise-users/index.yml)

---
title: Onlangs verwijderde gebruiker herstellen of permanent verwijderen-Azure AD
description: Het weer geven van herstelbare gebruikers, het herstellen van een verwijderde gebruiker of het permanent verwijderen van een gebruiker met Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 10/23/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd4d42e1cafa58aaf6a47188c9e86c24ac175047
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92514386"
---
# <a name="restore-or-remove-a-recently-deleted-user-using-azure-active-directory"></a>Een onlangs verwijderde gebruiker herstellen of verwijderen met Azure Active Directory
Nadat u een gebruiker hebt verwijderd, blijft het account gedurende 30 dagen in een suspend-status. Tijdens deze periode van 30 dagen kan het gebruikers account worden hersteld, samen met alle eigenschappen. Nadat deze periode van 30 dagen is verstreken, wordt het permanente verwijderings proces automatisch gestart.

U kunt uw herstelbare gebruikers weer geven, een verwijderde gebruiker herstellen of een gebruiker permanent verwijderen met Azure Active Directory (Azure AD) in de Azure Portal.

>[!Important]
>U noch de klant ondersteuning van micro soft kan een permanent verwijderde gebruiker herstellen.

## <a name="required-permissions"></a>Vereiste machtigingen
U moet een van de volgende rollen hebben om gebruikers te herstellen en permanent te verwijderen.

- Globale beheerder

- Laag1-ondersteuning voor partner

- Laag2-ondersteuning voor partner

- Gebruikersbeheerder

## <a name="view-your-restorable-users"></a>Uw herstorbare gebruikers weer geven
U kunt alle gebruikers zien die minder dan 30 dagen geleden zijn verwijderd. Deze gebruikers kunnen worden hersteld.

### <a name="to-view-your-restorable-users"></a>Uw herstorbare gebruikers weer geven
1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met een algemeen beheerders account voor de organisatie.

2. Selecteer **Azure Active Directory**, selecteer **gebruikers** en selecteer vervolgens **Verwijderde gebruikers**.

    Bekijk de lijst met gebruikers die beschikbaar zijn voor herstel.

    ![Gebruikers-pagina verwijderde gebruikers, met gebruikers die nog steeds kunnen worden hersteld](media/active-directory-users-restore/users-deleted-users-view-restorable.png)

## <a name="restore-a-recently-deleted-user"></a>Een onlangs verwijderde gebruiker herstellen

Wanneer een gebruikers account wordt verwijderd uit de organisatie, bevindt het account zich in een onderbroken staat en blijven alle gerelateerde organisatie gegevens bewaard. Wanneer u een gebruiker herstelt, wordt deze informatie over de organisatie ook hersteld.

> [!Note]
> Zodra een gebruiker is hersteld, worden de licenties die zijn toegewezen aan de gebruiker op het moment van verwijderen ook hersteld, zelfs als er geen stoelen beschikbaar zijn voor deze licenties. Als u vervolgens meer licenties gebruikt dan u hebt aangeschaft, is uw organisatie mogelijk tijdelijk niet in overeenstemming voor het gebruik van licenties.

### <a name="to-restore-a-user"></a>Een gebruiker herstellen
1. Op de pagina **gebruikers-verwijderde gebruikers** zoekt en selecteert u een van de beschik bare gebruikers. Bijvoorbeeld _Mary Parker_.

2. Selecteer **gebruiker herstellen**.

    ![Gebruikers-pagina verwijderde gebruikers, met de optie gebruiker herstellen gemarkeerd](media/active-directory-users-restore/users-deleted-users-restore-user.png)

## <a name="permanently-delete-a-user"></a>Een gebruiker definitief verwijderen
U kunt een gebruiker permanent verwijderen uit uw organisatie zonder de 30 dagen te wachten voor automatisch verwijderen. Een permanent verwijderde gebruiker kan niet worden hersteld door u, een andere beheerder of door de klant ondersteuning van micro soft.

>[!Note]
>Als u een gebruiker per ongeluk permanent verwijdert, moet u een nieuwe gebruiker maken en hand matig alle voor gaande informatie invoeren. Zie [gebruikers toevoegen of verwijderen](add-users-azure-active-directory.md)voor meer informatie over het maken van een nieuwe gebruiker.

### <a name="to-permanently-delete-a-user"></a>Een gebruiker permanent verwijderen

1. Op de pagina **gebruikers-verwijderde gebruikers** zoekt en selecteert u een van de beschik bare gebruikers. Bijvoorbeeld _Rae Huff_.

2. Selecteer **permanent verwijderen**.

    ![Gebruikers-pagina verwijderde gebruikers, met de optie gebruiker verwijderen gemarkeerd](media/active-directory-users-restore/users-deleted-users-permanent-delete-user.png)

## <a name="next-steps"></a>Volgende stappen
Nadat u uw gebruikers hebt teruggezet of verwijderd, kunt u het volgende doen:

- [Gebruikers toevoegen of verwijderen](add-users-azure-active-directory.md)

- [Rollen toewijzen aan gebruikers](active-directory-users-assign-role-azure-portal.md)

- [Profiel gegevens toevoegen of wijzigen](active-directory-users-profile-azure-portal.md)

- [Gast gebruikers toevoegen vanuit een andere organisatie](../external-identities/what-is-b2b.md)

Voor meer informatie over andere beschik bare taken voor gebruikers beheer, de [documentatie voor Azure AD-gebruikers beheer](../enterprise-users/index.yml).

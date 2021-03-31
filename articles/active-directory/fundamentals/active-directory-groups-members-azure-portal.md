---
title: Groeps leden toevoegen of verwijderen-Azure Active Directory | Microsoft Docs
description: Instructies voor het toevoegen of verwijderen van leden uit een groep met behulp van Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 08/23/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: af5a85bad1e7b2a6bf645084d6b78f77e6c0d8b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92371831"
---
# <a name="add-or-remove-group-members-using-azure-active-directory"></a>Groeps leden toevoegen of verwijderen met Azure Active Directory
Met Azure Active Directory kunt u groeps leden toevoegen en verwijderen.

## <a name="to-add-group-members"></a>Groeps leden toevoegen

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met het account van een globale administrator voor de map.

2. Selecteer **Azure Active Directory** en selecteer vervolgens **groepen**.

3. Zoek op de pagina **groepen-alle groepen** naar de groep waaraan u het lid wilt toevoegen en selecteer deze. In dit geval gebruikt u onze eerder gemaakte groep, **MDM-beleid-West**.

    ![Pagina Groups-All groepen, groeps naam gemarkeerd](media/active-directory-groups-members-azure-portal/group-all-groups-screen.png)

4. Selecteer op de pagina **Overzicht van MDM-beleid - West** de optie **Leden** onder **Beheren**.

    ![MDM-beleid-overzichts pagina, met de optie leden gemarkeerd](media/active-directory-groups-members-azure-portal/group-overview-blade.png)

5. Selecteer **leden toevoegen**, zoek en selecteer vervolgens elk van de leden die u aan de groep wilt toevoegen, en kies vervolgens **selecteren**.

    Er wordt een bericht weer gegeven met de melding dat de leden zijn toegevoegd.

    ![Pagina leden toevoegen met gezocht naar weer gegeven leden](media/active-directory-groups-members-azure-portal/update-members.png)

6. Vernieuw het scherm om alle lidnamen te zien die aan de groep zijn toegevoegd.

## <a name="to-remove-group-members"></a>Groeps leden verwijderen

1. Zoek op de pagina **groepen-alle groepen** naar de groep waarvan u het lid wilt verwijderen en selecteer deze. Vervolgens gebruiken we **MDM-beleid-West**.

2. Selecteer **leden** in het gebied **beheren** , zoek naar en selecteer de naam van het lid dat u wilt verwijderen en selecteer vervolgens **verwijderen**.

    ![De pagina leden info, met de optie verwijderen](media/active-directory-groups-members-azure-portal/remove-members-from-group.png)

## <a name="next-steps"></a>Volgende stappen

- [Groepen en leden weergeven](active-directory-groups-view-azure-portal.md)

- [Instellingen van uw groep bewerken](active-directory-groups-settings-azure-portal.md)

- [Toegang tot resources met behulp van groepen beheren](active-directory-manage-groups.md)

- [Dynamische regels voor gebruikers in een groep beheren](../enterprise-users/groups-create-rule.md)

- [Een Azure-abonnement aan Azure Active Directory koppelen of toevoegen](active-directory-how-subscriptions-associated-directory.md)

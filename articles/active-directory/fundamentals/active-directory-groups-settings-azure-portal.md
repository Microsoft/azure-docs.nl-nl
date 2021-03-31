---
title: Uw groeps gegevens bewerken-Azure Active Directory | Microsoft Docs
description: Instructies voor het bewerken van de gegevens van uw groep met behulp van Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 08/27/2018
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: c346877d29c5fef03b45c895cbf8d462f90d0979
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92369128"
---
# <a name="edit-your-group-information-using-azure-active-directory"></a>Uw groeps gegevens bewerken met behulp van Azure Active Directory

Met Azure Active Directory (Azure AD) kunt u de instellingen van een groep bewerken, waaronder het bijwerken van de naam, beschrijving of type lidmaatschap.

## <a name="to-edit-your-group-settings"></a>De groeps instellingen bewerken
1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met het account van een globale administrator voor de map.

2. Selecteer **Azure Active Directory** en selecteer vervolgens **groepen**.

    De pagina **groepen-alle groepen** wordt weer gegeven, met al uw actieve groepen.

3. Op de pagina **groepen-alle groepen** typt u zoveel van de groeps naam zoals u kunt vinden in het **zoekvak** . Voor de doel einden van dit artikel wordt gezocht naar het **MDM-beleid-westelijke** groep.

    De zoek resultaten worden weer gegeven onder het **zoekvak** en worden bijgewerkt terwijl u meer tekens typt.

    ![Pagina alle groepen, met Zoek tekst in het zoekvak](media/active-directory-groups-settings-azure-portal/search-for-specific-group.png)

4. Selecteer de groep **MDM-beleid-West** en selecteer vervolgens **Eigenschappen** in het gebied **beheren** .

    ![De pagina overzicht van groepen, met de optie en de gemarkeerde informatie](media/active-directory-groups-settings-azure-portal/group-overview-blade.png)

5. Werk de informatie over de **algemene instellingen** naar behoefte bij, waaronder:

    ![Eigenschappen instellingen voor een groep](media/active-directory-groups-settings-azure-portal/group-properties-settings.png)

    - **Groeps naam.** Bewerk de bestaande groeps naam.
    
    - **Groeps beschrijving.** Bewerk de beschrijving van de bestaande groep.

    - **Groeps type.** U kunt het type van de groep niet wijzigen nadat deze is gemaakt. Als u het **groeps type** wilt wijzigen, moet u de groep verwijderen en een nieuwe maken.
    
    - **Type lidmaatschap.** Wijzig het lidmaatschaps type. Zie [procedure: een basis groep maken en leden toevoegen met behulp van de Azure Active Directory Portal](active-directory-groups-create-azure-portal.md)voor meer informatie over de verschillende beschik bare typen lidmaatschappen.
    
    - **Object-ID.** De object-ID kan niet worden gewijzigd, maar u kunt deze kopiëren voor gebruik in uw Power shell-opdrachten voor de groep. Zie [Azure Active Directory-cmdlets voor het configureren van groeps instellingen](../enterprise-users/groups-settings-v2-cmdlets.md)voor meer informatie over het gebruik van Power shell-cmdlets.

## <a name="next-steps"></a>Volgende stappen
Deze artikelen bevatten aanvullende informatie over Azure Active Directory.

- [Groepen en leden weergeven](active-directory-groups-view-azure-portal.md)

- [Een basisgroep maken en leden toevoegen](active-directory-groups-create-azure-portal.md)

- [Leden toevoegen aan of verwijderen uit een groep](active-directory-groups-members-azure-portal.md)

- [Dynamische regels voor gebruikers in een groep beheren](../enterprise-users/groups-create-rule.md)

- [Lidmaatschappen van een groep beheren](active-directory-groups-membership-azure-portal.md)

- [Toegang tot resources met behulp van groepen beheren](active-directory-manage-groups.md)

- [Een Azure-abonnement aan Azure Active Directory koppelen of toevoegen](active-directory-how-subscriptions-associated-directory.md)

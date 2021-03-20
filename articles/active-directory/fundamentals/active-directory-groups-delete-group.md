---
title: Een groep-Azure Active Directory verwijderen | Microsoft Docs
description: Instructies voor het verwijderen van een groep met Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 08/29/2018
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9aed5f282ff7b5d573a6f8511f2fc4dbfd27135d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92371814"
---
# <a name="delete-a-group-using-azure-active-directory"></a>Een groep verwijderen met Azure Active Directory
U kunt een Azure Active Directory-groep (Azure AD) om een aantal redenen verwijderen, maar dit komt meestal doordat:

- Het **groeps type** is onjuist ingesteld op de verkeerde optie.

- Er is per ongeluk een onjuiste of dubbele groep gemaakt. 

- De groep is niet meer nodig.

## <a name="to-delete-a-group"></a>Een groep verwijderen
1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met het account van een globale administrator voor de map.

2. Selecteer **Azure Active Directory** en selecteer vervolgens **groepen**.

3. Zoek en selecteer de groep die u wilt verwijderen op de pagina **groepen-alle groepen** . Voor deze stappen gebruiken we **MDM-beleid-Oost**.

    ![Pagina Groups-All groepen, groeps naam gemarkeerd](media/active-directory-groups-delete-group/group-all-groups-screen.png)

4. Op de **overzichts pagina MDM-beleid-Oost** en selecteer vervolgens **verwijderen**.

    De groep wordt verwijderd uit uw Azure Active Directory-Tenant.

    ![MDM-beleid-Oost-overzicht pagina, optie voor verwijderen gemarkeerd](media/active-directory-groups-delete-group/group-overview-blade.png)

## <a name="next-steps"></a>Volgende stappen

- Als u een groep per ongeluk verwijdert, kunt u deze opnieuw maken. Zie [een basis groep maken en leden toevoegen](active-directory-groups-create-azure-portal.md)voor meer informatie.

- Als u per ongeluk een Microsoft 365 groep verwijdert, kunt u deze mogelijk herstellen. Zie [een verwijderde Office 365-groep herstellen](../enterprise-users/groups-restore-deleted.md)voor meer informatie.

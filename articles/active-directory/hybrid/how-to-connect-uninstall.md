---
title: Azure AD Connect verwijderen
description: In dit document wordt beschreven hoe u Azure AD Connect verwijdert.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/09/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5eb537a70c69745c8067ffb71cfb895a0875945
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96934169"
---
# <a name="uninstall-azure-ad-connect"></a>Azure AD Connect verwijderen

In dit document wordt beschreven hoe u Azure AD Connect correct kunt verwijderen.

## <a name="uninstall-azure-ad-connect-from-the-server"></a>Azure AD Connect van de server verwijderen
Het eerste wat u moet doen, is Azure AD Connect verwijderen van de server waarop het wordt uitgevoerd.  Voer de volgende stappen uit:

 1. Ga op de server met Azure AD Connect naar **het configuratie scherm**.
 2. Klik op **een programma** verwijderen een programma 
  ![ verwijderen](media/how-to-connect-uninstall/uninstall-1.png)</br>
 
 3. Selecteer **Azure AD Connect**.
 ![Azure AD Connect selecteren](media/how-to-connect-uninstall/uninstall-2.png)</br>
 
 4. Wanneer u hierom wordt gevraagd, klikt u op **Ja** om te bevestigen.
 5. Met deze bevestiging wordt het Azure AD Connect scherm weer gegeven.  Klik op **Verwijderen**.
 ![Verwijderen](media/how-to-connect-uninstall/uninstall-3.png)</br>
 
 6. Zodra deze actie is voltooid, klikt u op **Afsluiten**.
 7. ![Afsluiten](media/how-to-connect-uninstall/uninstall-4.png)</br>
 
 8. Klik in **het configuratie scherm** op **vernieuwen** en alle onderdelen moeten worden verwijderd.


## <a name="next-steps"></a>Volgende stappen

- Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).
- [Azure AD Connect installeren met behulp van een bestaande ADSync-database](how-to-connect-install-existing-database.md)
- [Azure AD Connect installeren met SQL-gedelegeerde beheerdersmachtigingen](how-to-connect-install-sql-delegation.md)


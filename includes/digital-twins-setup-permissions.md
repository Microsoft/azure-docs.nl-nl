---
author: baanders
description: bestand insluiten voor vereiste machtiging in azure Digital Apparaatdubbels Setup
ms.service: digital-twins
ms.topic: include
ms.date: 10/14/2020
ms.author: baanders
ms.openlocfilehash: 919cc934920d922a82ceb9e30630006627af44e9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92205477"
---
## <a name="prerequisites-permission-requirements"></a>Vereisten: machtigings vereisten

Als u alle stappen in dit artikel wilt uitvoeren, moet u een [rol hebben in uw abonnement](../articles/role-based-access-control/rbac-and-directory-admin-roles.md) met de volgende machtigingen:
* Azure-resources maken en beheren
* Gebruikers toegang tot Azure-resources beheren (inclusief het verlenen en delegeren van machtigingen)

Algemene rollen die aan deze vereiste voldoen, zijn *eigenaar*, *account beheerder* of de combi natie van beheerder en *mede werker* van de *gebruikers toegang* . Voor een volledige uitleg van de rollen en machtigingen, met inbegrip van de machtigingen die zijn opgenomen in andere rollen, gaat u naar [*klassieke abonnements beheerders rollen, Azure-rollen en Azure AD-rollen*](../articles/role-based-access-control/rbac-and-directory-admin-roles.md) in de documentatie van Azure RBAC.

Als u uw rol in uw abonnement wilt weer geven, gaat u naar de [pagina Abonnementen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) in de Azure Portal (u kunt deze koppeling gebruiken of zoeken naar *abonnementen* met de zoek balk van de portal). Zoek de naam van het abonnement dat u gebruikt en Bekijk uw rol hiervoor in de kolom *mijn rol* :

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png" alt-text="Weer gave van de pagina Abonnementen in de Azure Portal, waarbij de gebruiker wordt weer gegeven als eigenaar" lightbox="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png":::

Als u merkt dat de waarde *Inzender* is of een andere rol waarvoor niet de vereiste machtigingen zijn beschreven, kunt u contact opnemen met de gebruiker in uw abonnement die *wel* over deze machtigingen beschikt (zoals een abonnements eigenaar of account beheerder) en op een van de volgende manieren te werk gaan:
* Vraag de stappen in dit artikel namens u uit te voeren
* Vraag de gebruiker om uw rol over het abonnement uit te breiden zodat u de juiste machtigingen hebt om door te gaan. Of dit geschikt is, is afhankelijk van uw organisatie en uw rol.
---
title: Een organisatie als gast gebruiker laten Azure Active Directory
description: Laat zien hoe een Azure AD B2B-gast gebruiker een organisatie kan verlaten door gebruik te maken van het toegangs venster.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/13/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc3ef7d168c17ec10fe64925adbda7044a2a4c82
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87908590"
---
# <a name="leave-an-organization-as-a-guest-user"></a>Een organisatie verlaten als gastgebruiker

Een Azure Active Directory (Azure AD) B2B-gast gebruiker kan ervoor kiezen om een organisatie op elk gewenst moment te verlaten als ze niet langer apps van die organisatie hoeven te gebruiken of een koppeling te onderhouden. Een gebruiker kan een organisatie zelf verlaten zonder dat hiervoor contact moet worden opgenomen met een beheerder.

> [!NOTE]
> Een gast gebruiker kan geen organisatie verlaten als hun account is uitgeschakeld in de thuis Tenant of de resource-Tenant. Als het account is uitgeschakeld, moet de gast gebruiker contact opnemen met de Tenant beheerder, die het gast account kan verwijderen of het gast account kan inschakelen, zodat de gebruiker de organisatie kan verlaten.

## <a name="leave-an-organization"></a>Een organisatie verlaten

Als u een organisatie wilt verlaten, voert u de volgende stappen uit.

1. Ga op een van de volgende manieren te werk op de profiel pagina van het toegangs venster:
   
   - Klik in de [Azure Portal](https://portal.azure.com)op uw naam in de rechter bovenhoek en selecteer **account weer geven**.
   - Open het [toegangs venster](https://myapps.microsoft.com), klik op uw naam in de rechter bovenhoek en Selecteer naast **organisaties** het pictogram instellingen (tand wiel).
 
   ![Scherm opname van gebruikers instellingen in het toegangs venster](media/leave-the-organization/UserSettings.png) 

   > [!NOTE]
   > Als u nog niet bent aangemeld bij de organisatie die u wilt verlaten, klikt u onder **organisaties** op aanmelden om de koppeling van de organisatie **te verlaten** naast de naam van de organisatie. Nadat u bent aangemeld, klikt u op uw naam in de rechter bovenhoek en naast **organisaties**, selecteert u het pictogram instellingen (tand wiel).

3. Onder **organisaties** gaat u naar de organisatie die u wilt verlaten en selecteert u **organisatie behouden**.

   ![Scherm opname van de optie organisatie verlaten in de gebruikers interface](media/leave-the-organization/LeaveOrg.png)

4. Wanneer u wordt gevraagd om te bevestigen, selecteert u **verlaten**. 

## <a name="account-removal"></a>Account verwijderen

Wanneer een gebruiker een organisatie verlaat, is de gebruikers account ' zacht verwijderd ' in de map. Het gebruikers object wordt standaard verplaatst naar het gebied **Verwijderde gebruikers** in azure AD, maar wordt 30 dagen niet permanent verwijderd. Met deze optie kan de beheerder het gebruikers account (met inbegrip van groepen en machtigingen) herstellen als de gebruiker een aanvraag indient om het account binnen de periode van 30 dagen te herstellen.

Indien gewenst kan een Tenant beheerder het account op elk gewenst moment gedurende de periode van 30 dagen permanent verwijderen. Om dit te doen:

1. Selecteer **Azure Active Directory** in de [Azure-portal](https://portal.azure.com).
2. Selecteer onder **Beheren** de optie **Gebruikers**.
3. Selecteer **Verwijderde gebruikers**.
4. Schakel het selectie vakje naast een verwijderde gebruiker in en selecteer **permanent verwijderen**.

Als u een gebruiker permanent verwijdert, is deze actie onherroepelijk.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Volgende stappen

- Zie [Wat is Azure AD B2B-samen werking?](what-is-b2b.md) voor een overzicht van Azure AD B2B.




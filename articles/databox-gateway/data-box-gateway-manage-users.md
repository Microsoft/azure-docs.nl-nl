---
title: De Azure Portal gebruiken om gebruikers te beheren in Azure Data Box Gateway | Microsoft Docs
description: Beschrijft hoe u Azure Portal gebruikt om gebruikers te beheren in uw Azure Data Box Gateway.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: how-to
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 9e941007ddc27f809de7d43cd33e44c5b521a6bd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96581962"
---
# <a name="use-the-azure-portal-to-manage-users-on-your-azure-data-box-gateway"></a>Azure Portal gebruiken om gebruikers te beheren in uw Azure Data Box Gateway

Dit artikel beschrijft hoe u gebruikers beheert in uw Azure Data Box Gateway. U kunt de Azure Data Box Gateway beheren via Azure Portal of via de lokale webinterface. Gebruik Azure Portal om gebruikers toe te voegen, te wijzigen of te verwijderen. 

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Een gebruiker toevoegen
> * Gebruiker wijzigen
> * Een gebruiker verwijderen

## <a name="about-users"></a>Over gebruikers

Gebruikers kunnen het recht alleen-lezen of volledige bevoegdheid hebben. Zoals de naam al aangeeft, kunnen gebruikers met het recht alleen-lezen de sharegegevens alleen weergeven. Gebruikers met volledige bevoegdheid kunnen sharegegevens lezen, schrijven, wijzigen of verwijderen.

 - **Gebruiker met volledige bevoegdheden** - een lokale gebruiker met volledige toegang.
 - **Alleen-lezengebruiker** - een lokale gebruiker met alleen-lezentoegang. Deze gebruikers zijn gekoppeld aan shares waarmee alleen-lezenbewerkingen mogelijk zijn.

De gebruikersmachtigingen worden eerst gedefinieerd wanneer de gebruiker wordt gemaakt tijdens het maken van de share. Het wijzigen van machtigingen op shareniveau wordt momenteel niet ondersteund.

## <a name="add-a-user"></a>Een gebruiker toevoegen

Voer in Azure Portal de volgende stappen uit om een gebruiker toe te voegen.

1. Ga in het Azure Portal naar uw Data Box Gateway resource en navigeer vervolgens naar **overzicht**. Klik in de opdrachtbalk op **+ Gebruiker toevoegen**.

    ![Klikken op Gebruiker toevoegen](media/data-box-gateway-manage-users/add-user-1.png)

2. Geef de gebruikersnaam en het wachtwoord op voor de gebruiker die u wilt toevoegen. Bevestig het wacht woord en klik op **toevoegen**.

    ![Klik op gebruiker toevoegen 2](media/data-box-gateway-manage-users/add-user-2.png)

    > [!IMPORTANT] 
    > Deze gebruikers zijn gereserveerd door het systeem en moeten niet worden gebruikt: Administrator, EdgeUser, EdgeSupport, HcsSetupUser, WDAGUtilityAccount, CLIUSR, DefaultAccount, Guest.  

3. U krijgt een melding wanneer het maken van de gebruiker wordt gestart en is voltooid. Wanneer de gebruiker is gemaakt, klikt u in de opdrachtbalk op **Vernieuwen** om de bijgewerkte lijst met gebruikers weer te geven.


## <a name="modify-user"></a>Gebruiker wijzigen

U kunt het wachtwoord dat is gekoppeld aan een gebruiker, wijzigen wanneer de gebruiker is gemaakt. Selecteer en klik in de lijst met gebruikers. Geef het nieuwe wachtwoord op en bevestig het. Sla de wijzigingen op.
 
![Gebruiker wijzigen](media/data-box-gateway-manage-users/modify-user-1.png)


## <a name="delete-a-user"></a>Een gebruiker verwijderen

Voer in Azure Portal de volgende stappen uit om een gebruiker te verwijderen.

1. Selecteer en klik op een gebruiker in de lijst met gebruikers en klik vervolgens op **verwijderen**.  

   ![Een gebruiker verwijderen](media/data-box-gateway-manage-users/delete-user-1.png)

2. Bevestig de verwijdering als u daarom wordt gevraagd. 

   ![Een gebruiker verwijderen 2](media/data-box-gateway-manage-users/delete-user-2.png)

De lijst met gebruikers wordt bijgewerkt en de verwijderde gebruiker wordt niet meer weergegeven.

![Een gebruiker verwijderen 3](media/data-box-gateway-manage-users/delete-user-3.png)


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Bandbreedte beheren](data-box-gateway-manage-bandwidth-schedules.md).

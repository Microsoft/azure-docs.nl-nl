---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 03/02/2018
ms.openlocfilehash: 1aabc1b33722c571c6519b6620fb62211a44c2e8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95999536"
---
1. Maak in de [Azure Portal](https://portal.azure.com)een lege logische app. 

2. Voer in de Logic Apps Designer ' github ' in als uw filter. 

3. Selecteer de GitHub-connector en de trigger die u wilt gebruiken.

   ![De GitHub-connector en een trigger selecteren](./media/connectors-create-api-github/github-connector.png)

   > [!NOTE]
   > Alle werk stromen voor logische apps moeten beginnen met een trigger. U kunt alleen acties selecteren wanneer uw logische werk stroom al met een trigger begint. 

4. Als u eerder geen verbinding hebt gemaakt, kiest u **Aanmelden** zodat u uw github-referenties kunt opgeven wanneer u hierom wordt gevraagd.  

   ![Meld u aan met uw GitHub-referenties](./media/connectors-create-api-github/github-connector-sign-in-credentials.png)

   Uw logische app maakt gebruik van deze referenties om verbinding te maken en toegang te krijgen tot gegevens voor uw GitHub-account. 

5. Geef uw GitHub-gebruikers naam en-wacht woord op en bevestig vervolgens uw autorisatie.

   ![Referenties opgeven en autorisatie bevestigen](./media/connectors-create-api-github/github-connector-authorize.png)   

   Uw verbinding wordt nu gemaakt in de Azure Portal en is klaar voor gebruik.

6. Ga door met het definiëren van de werk stroom van uw logische app.

   ![Meer acties toevoegen aan de werk stroom van uw logische app](./media/connectors-create-api-github/github-connector-logic-app.png)


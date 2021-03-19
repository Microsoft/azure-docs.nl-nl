---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 07/21/2020
ms.openlocfilehash: 06a503e800309d8689735417931820d9f16a6e75
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87102601"
---
## <a name="prerequisites"></a>Vereisten

* Een [OneDrive voor bedrijven](https://OneDrive.com) -account 

Voordat u uw OneDrive voor bedrijven-account kunt gebruiken met Logic Apps, moet u Logic Apps toestemming geven om verbinding te maken met uw OneDrive voor bedrijven-account in de Azure Portal.

Voer de volgende stappen uit om Logic Apps om verbinding te maken met uw OneDrive voor bedrijven-account:  

1. Meld u aan bij Azure Portal. 

1. Onder **Azure-Services** selecteert u **Logic apps**. Selecteer vervolgens de naam van uw logische app in de lijst.

1. Selecteer in het menu van de logische app de optie **Logic app-ontwerp** onder **ontwikkelingsprogram ma's**.

1. Selecteer in de Logic Apps Designer **micro soft Managed Api's weer geven** in de vervolg keuzelijst en voer *OneDrive voor bedrijven* in het zoekvak in. Selecteer de trigger of actie die u wilt gebruiken:  

   ![Scherm opname van Logic Apps Designer, met daarin de trigger voor terugkeer patronen met OneDrive voor bedrijven API-acties.](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-1.png)

2. Als u nog geen verbindingen met OneDrive voor bedrijven hebt gemaakt, volgt u de vraag om uw OneDrive voor bedrijven-referenties op te geven. Deze referenties worden gebruikt om uw logische app te autoriseren voor toegang tot de gegevens van uw OneDrive voor bedrijven-account:  

   ![Scherm opname van Logic Apps Designer, met aanmeldings prompt voor OneDrive voor bedrijven.](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-2.png)

3. Geef uw gebruikers naam en wacht woord voor OneDrive voor bedrijven op om uw logische app te autoriseren:  

   ![Scherm afbeelding van de aanmeldings pagina voor OneDrive voor bedrijven waarin de aanmeldings prompt wordt weer gegeven.](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-3.png)   

4. De verbinding wordt nu weer gegeven in de stap. Selecteer opslaan en ga door met het maken van de logische app. 

   ![Scherm opname van Logic Apps Designer, waarin de trigger wordt weer gegeven met de OneDrive voor bedrijven-verbinding.](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-4.png)   

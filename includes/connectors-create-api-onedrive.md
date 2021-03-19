---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 07/21/2020
ms.openlocfilehash: 27df83552b450b7657c0595d1d419001df98148c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87040232"
---
## <a name="prerequisites"></a>Vereisten

* Een [OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) -account 

Voordat u uw OneDrive-account kunt gebruiken met Logic Apps, moet u Logic Apps toestemming geven om verbinding te maken met uw OneDrive-account in de Azure Portal.

Voer de volgende stappen uit om Logic Apps om verbinding te maken met uw OneDrive-account:  

1. Meld u aan bij Azure Portal. 

1. Onder **Azure-Services** selecteert u **Logic apps**. Selecteer vervolgens de naam van uw logische app in de lijst.

1. Selecteer in het menu van de logische app de optie **Logic app-ontwerp** onder **ontwikkelingsprogram ma's**.

1. Selecteer in de Logic Apps Designer **micro soft Managed Api's weer geven** in de vervolg keuzelijst en voer *OneDrive* in het zoekvak in. Selecteer de trigger of actie die u wilt gebruiken:

   ![Scherm opname van Logic Apps Designer, met een lijst met OneDrive API-acties die moeten worden toegevoegd.](./media/connectors-create-api-onedrive/onedrive-1.png)

2. Als u nog geen verbindingen met OneDrive hebt gemaakt, volgt u de prompt om u aan te melden met uw OneDrive-referenties:  

   ![Scherm opname van Logic Apps Designer, waarin de aanmeldings prompt voor de OneDrive API wordt weer gegeven.](./media/connectors-create-api-onedrive/onedrive-2.png)

3. Selecteer **Aanmelden** en voer uw gebruikers naam en wacht woord in. Selecteer **Aanmelden**: 

   ![Scherm afbeelding van Microsoft-account aanmeldings pagina voor de OneDrive API-autorisatie.](./media/connectors-create-api-onedrive/onedrive-3.png)   

    Deze referenties worden gebruikt om uw logische app te autoriseren voor toegang tot de gegevens in uw OneDrive-account. 

4. Selecteer **Ja** om de logische app te autoriseren voor het gebruik van uw OneDrive-account:  

   ![Scherm opname van Microsoft-account autorisatie voor Logic Apps, waarbij de toegestane acties worden weer gegeven.](./media/connectors-create-api-onedrive/onedrive-4.png)   
   
5. De verbinding wordt nu weer gegeven in de stap. Selecteer opslaan en ga door met het maken van de logische app. 

   ![Scherm opname van Logic Apps Designer, met actie-editor met de API-verbinding van OneDrive.](./media/connectors-create-api-onedrive/onedrive-5.png)

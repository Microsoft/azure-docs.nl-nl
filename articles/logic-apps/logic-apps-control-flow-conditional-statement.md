---
title: Voorwaardelijke instructies toevoegen aan werk stromen
description: Voor waarden maken voor het beheren van acties in werk stromen in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/09/2018
ms.openlocfilehash: 19f42270e37c42cf56bbde04f73c01027a56ff63
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89657384"
---
# <a name="create-conditional-statements-that-control-workflow-actions-in-azure-logic-apps"></a>Voorwaardelijke instructies maken waarmee werk stroom acties in Azure Logic Apps worden beheerd

Als u specifieke acties in uw logische app pas wilt uitvoeren nadat u een opgegeven voor waarde hebt door gegeven, voegt u een *voorwaardelijke instructie* toe. Met dit beheermechanisme vergelijkt u de gegevens in uw werkstroom met specifieke waarden of velden. U kunt vervolgens verschillende acties opgeven die worden uitgevoerd op basis van het feit of de gegevens voldoen aan de voor waarde. U kunt voor waarden in elkaar nesten.

Stel dat u een logische app hebt die te veel e-mail berichten verzendt wanneer nieuwe items worden weer gegeven op de RSS-feed van een website. U kunt een voorwaardelijke instructie toevoegen om een e-mail te verzenden wanneer het nieuwe item een specifieke teken reeks bevat. 

> [!TIP]
> Als u verschillende stappen wilt uitvoeren op basis van verschillende specifieke waarden, gebruikt u in plaats daarvan een [*instructie switch*](../logic-apps/logic-apps-control-flow-switch-statement.md) .

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement hebt, [meld u dan aan voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Basis kennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Als u het voor beeld in dit artikel wilt volgen, [maakt u deze voor beeld-logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md) met een Outlook.com-account of een werk-of school account.

## <a name="add-condition"></a>Voorwaarde toevoegen

1. Open in de <a href="https://portal.azure.com" target="_blank">Azure Portal</a>uw logische app in de ontwerp functie voor logische apps.

1. Voeg een voor waarde toe op de gewenste locatie. 

   Als u een voor waarde tussen de stappen wilt toevoegen, plaatst u de muis aanwijzer op de pijl waar u de voor waarde wilt toevoegen. Kies het **plus teken** ( **+** ) dat wordt weer gegeven en kies vervolgens **een actie toevoegen**. Bijvoorbeeld:

   ![Actie tussen stappen toevoegen](./media/logic-apps-control-flow-conditional-statement/add-action.png)

   Wanneer u aan het einde van uw werk stroom een voor waarde wilt toevoegen, kiest u  **nieuwe stap** > **een actie toevoegen** onder aan uw logische app.

1. Voer in het zoekvak voorwaarde in als uw filter. Selecteer deze actie: **voor waarde controle**

   ![Voorwaarde toevoegen](./media/logic-apps-control-flow-conditional-statement/add-condition.png)

1. Bouw uw voor waarde in het vak **voor de voor waarde** . 

   1. Geef in het linkervak de gegevens of het veld op dat u wilt vergelijken.

      Wanneer u in het linkervak klikt, wordt de lijst met dynamische inhoud weer gegeven, zodat u uitvoer kunt selecteren uit de vorige stappen in uw logische app. 
      Selecteer voor dit voor beeld de RSS-feed-samen vatting.

      ![Uw voor waarde maken](./media/logic-apps-control-flow-conditional-statement/edit-condition.png)

   1. Selecteer in het middelste vak de bewerking die u wilt uitvoeren. 
   Voor dit voor beeld selecteert u '**contains**'. 

   1. Geef in het vak rechts een waarde of veld op als criterium. 
   Voor dit voor beeld geeft u deze teken reeks op: **micro soft**

   Dit is de volledige voor waarde:

   ![Voltooide voorwaarde](./media/logic-apps-control-flow-conditional-statement/edit-condition-2.png)

   Als u een andere rij wilt toevoegen aan uw voor waarde **, kiest u**  >  **rij toevoegen** toevoegen. 
   **Kies toevoegen groep toevoegen om** een groep met subvoorwaarden toe te voegen  >  . 
   Als u bestaande rijen wilt groeperen, selecteert u de selectie vakjes voor die rijen, kiest u de knop met weglatings tekens (...) voor elke rij en kiest **u groep maken**.

1. Voeg onder **if True** en **indien onwaar**, de stappen toe die moeten worden uitgevoerd op basis van het feit of aan de voor waarde wordt voldaan. Bijvoorbeeld:

   ![Voor waarde met de paden ' Indien waar ' en ' indien onwaar '](./media/logic-apps-control-flow-conditional-statement/condition-yes-no-path.png)

   > [!TIP]
   > U kunt bestaande acties naar de paden **Indien waar** en **indien onwaar** slepen.

1. Sla uw logische app op.

Deze logische app verzendt nu alleen e-mail wanneer de nieuwe items in de RSS-feed voldoen aan uw voor waarde.

## <a name="json-definition"></a>JSON-definitie

Hier volgt de code definitie op hoog niveau achter een voorwaardelijke instructie:

``` json
"actions": {
  "Condition": {
    "type": "If",
    "actions": {
      "Send_an_email": {
        "inputs": {},
        "runAfter": {}
    },
    "expression": {
      "and": [ 
        { 
          "contains": [ 
            "@triggerBody()?['summary']", 
            "Microsoft"
          ]
        } 
      ]
    },
    "runAfter": {}
  }
},
```

## <a name="get-support"></a>Ondersteuning krijgen

* Ga voor vragen naar de [pagina micro soft Q&een vraag voor Azure Logic apps](/answers/topics/azure-logic-apps.html).
* Ga naar de [site voor Azure Logic Apps gebruikers feedback](https://aka.ms/logicapps-wish)om functies en suggesties te verzenden of hierop te stemmen.

## <a name="next-steps"></a>Volgende stappen

* [Stappen uitvoeren op basis van verschillende waarden (switch-instructies)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Stappen uitvoeren en herhalen (lussen)](../logic-apps/logic-apps-control-flow-loops.md)
* [Parallelle stappen uitvoeren of samen voegen (vertakkingen)](../logic-apps/logic-apps-control-flow-branches.md)
* [Stappen uitvoeren op basis van de gegroepeerde actie status (bereiken)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)

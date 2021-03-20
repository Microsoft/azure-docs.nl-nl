---
title: Switch-instructies toevoegen aan werk stromen
description: Switch instructies maken waarmee werk stroom acties worden beheerd op basis van specifieke waarden in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/08/2018
ms.openlocfilehash: 62f147104cf9e0e1605b02a420cb6d20190361b8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89657449"
---
# <a name="create-switch-statements-that-run-workflow-actions-based-on-specific-values-in-azure-logic-apps"></a>Switch-instructies maken waarmee werk stroom acties worden uitgevoerd op basis van specifieke waarden in Azure Logic Apps

Als u specifieke acties wilt uitvoeren op basis van de waarden van objecten, expressies of tokens, voegt u een instructie *Switch* toe. Deze structuur evalueert het object, de expressie of het token, kiest het geval dat overeenkomt met het resultaat en voert alleen specifieke acties voor die aanvraag uit. Wanneer de switch-instructie wordt uitgevoerd, moet er slechts één geval overeenkomen met het resultaat.

Stel dat u een logische app wilt die verschillende stappen uitvoert op basis van een optie die is geselecteerd in het e-mail bericht. In dit voor beeld controleert de logische app de RSS-feed van een website op nieuwe inhoud. Wanneer een nieuw item wordt weer gegeven in de RSS-feed, verzendt de logische app een e-mail bericht naar een goed keurder. Op basis van het feit of de fiatteur ' goed keuren ' of ' afwijzen ' selecteert, volgt de logische app verschillende stappen.

> [!TIP]
> Net als alle programmeer talen ondersteunen switch-instructies alleen gelijkheids operatoren. Als u andere relationele Opera tors nodig hebt, zoals ' groter dan ', gebruikt u een [voorwaardelijke instructie](../logic-apps/logic-apps-control-flow-conditional-statement.md).
> Om het deterministische uitvoerings gedrag te garanderen, moeten cases een unieke en statische waarde bevatten in plaats van dynamische tokens of expressies.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement hebt, [meld u dan aan voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Als u het voor beeld in dit artikel wilt volgen, [maakt u deze voor beeld-logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md) met een Outlook.com-account of een werk-of school account.

  1. Wanneer u de actie voor het verzenden van e-mail toevoegt, zoekt en selecteert u in plaats daarvan deze actie: **een goedkeurings-e-mail verzenden**

     ![Selecteer e-mail voor goed keuring verzenden](./media/logic-apps-control-flow-switch-statement/send-approval-email-action.png)

  1. Geef de vereiste velden op, zoals het e-mail adres van de persoon die de goedkeurings-e-mail ontvangt. 
  Onder **gebruikers opties** voert u ' goed keuren, afwijzen ' in.

     ![E-mail gegevens invoeren](./media/logic-apps-control-flow-switch-statement/send-approval-email-details.png)

## <a name="add-switch-statement"></a>Switch-instructie toevoegen

1. Voor dit voor beeld voegt u een instructie switch toe aan het einde van uw voorbeeld werk stroom. Klik na de laatste stap op **nieuwe stap**.

   Wanneer u een switch-instructie tussen de stappen wilt toevoegen, plaatst u de muis aanwijzer op de pijl waaraan u de instructie switch wilt toevoegen. Kies het **plus teken** ( **+** ) dat wordt weer gegeven en kies vervolgens **een actie toevoegen**.

1. In het zoekvak voert u "switch" in als uw filter. Selecteer deze actie: **Switch-Control**

   ![Switch toevoegen](./media/logic-apps-control-flow-switch-statement/add-switch-statement.png)

   Een switch-instructie wordt weer gegeven met één geval en een standaard geval. 
   Voor een switch-instructie is standaard ten minste één aanvraag vereist plus het standaard geval. 

   ![Lege standaard Switch-instructie](./media/logic-apps-control-flow-switch-statement/empty-switch.png)

1. Klik in het vak **aan** zodat de lijst met dynamische inhoud wordt weer gegeven. Selecteer in de lijst het veld **token** waarvan de uitvoer bepaalt welke actie moet worden uitgevoerd. 

   ![Selecteer ' token '](./media/logic-apps-control-flow-switch-statement/select-selected-option.png)

1. Voor het afhandelen van de gevallen waarin de fiatteur `Approve` `Reject` een andere case selecteert of een andere aanvraag tussen **Case** en **standaard** toevoegt. 

   ![Een andere case toevoegen](./media/logic-apps-control-flow-switch-statement/switch-plus.png)

1. Voeg deze acties toe aan de bijbehorende cases:

   | Casu # | **Token** | Bewerking |
   |--------|--------------------|--------|
   | Voor beeld 1 | **Goedkeuren** | Voeg de actie Outlook **een E-mail verzenden** toe om alleen gegevens over het RSS-item te verzenden wanneer de fiatteur **goed keuren** heeft geselecteerd. |
   | Case 2 | **Afwijzen** | Voeg de actie Outlook **een E-mail verzenden** toe voor het melden van andere goed keurders die het RSS-item heeft afgewezen. |
   | Standaard | Geen | Geen actie vereist. In dit voor beeld is de **standaard** situatie leeg, omdat **token** slechts twee opties heeft. |
   |||

   ![Instructie switch is voltooid](./media/logic-apps-control-flow-switch-statement/finished-switch.png)

1. Sla uw logische app op. 

   Als u dit voor beeld hand matig wilt testen, kiest u **uitvoeren** totdat de logische app een nieuw RSS-item vindt en een goedkeurings-e-mail verzendt. 
   Selecteer **goed keuren** om de resultaten te bekijken.

## <a name="json-definition"></a>JSON-definitie

Nu u een logische app hebt gemaakt met behulp van een instructie switch, kijken we naar de code definitie op hoog niveau achter de switch-instructie.

``` json
"Switch": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case": {
         "actions": {
           "Send_an_email": {}
         },
         "case" : "Approve"
      },
      "Case_2": {
         "actions": {
           "Send_an_email_2": {}
         },
         "case": "Reject"
      }
   },
   "default": {
      "actions": {}
   },
   "runAfter": {
      "Send_approval_email": [
         "Succeeded"
      ]
   }
}
```

| Label | Beschrijving |
|-------|-------------|
| `"Switch"`         | De naam van de instructie switch, die u de naam van de Lees baarheid kunt wijzigen |
| `"type": "Switch"` | Geeft aan dat de actie een switch instructie is |
| `"expression"`     | In dit voor beeld geeft u de geselecteerde optie van de fiatteur op die wordt geëvalueerd op basis van elke case, zoals later in de definitie wordt gedeclareerd. |
| `"cases"` | Hiermee definieert u een wille keurig aantal cases. Voor elk geval `"Case_*"` is de standaard naam voor dat geval, waarmee u de naam van de Lees baarheid kunt wijzigen |
| `"case"` | Hiermee geeft u de waarde van de case op. dit moet een constante en een unieke waarde zijn die de instructie switch gebruikt voor vergelijking. Als er geen gevallen overeenkomen met het resultaat van de switch-expressie, worden de acties in de `"default"` sectie uitgevoerd. | 
| | | 

## <a name="get-support"></a>Ondersteuning krijgen

* Ga voor vragen naar de [pagina micro soft Q&een vraag voor Azure Logic apps](/answers/topics/azure-logic-apps.html).
* Ga naar de [site voor Azure Logic Apps gebruikers feedback](https://aka.ms/logicapps-wish)om functies of suggesties te verzenden of hierop te stemmen.

## <a name="next-steps"></a>Volgende stappen

* [Stappen uitvoeren op basis van een voor waarde (voorwaardelijke instructies)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Stappen uitvoeren en herhalen (lussen)](../logic-apps/logic-apps-control-flow-loops.md)
* [Parallelle stappen uitvoeren of samen voegen (vertakkingen)](../logic-apps/logic-apps-control-flow-branches.md)
* [Stappen uitvoeren op basis van de gegroepeerde actie status (bereiken)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)

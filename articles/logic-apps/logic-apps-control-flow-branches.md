---
title: Parallelle vertakkingen maken of koppelen voor acties in werk stromen
description: Meer informatie over het maken of samen voegen van parallelle actieve vertakkingen voor onafhankelijke werk stroom acties in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/10/2018
ms.openlocfilehash: 3514ce966e1de9af1741de6b966964aca2599610
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91269230"
---
# <a name="create-or-join-parallel-branches-for-workflow-actions-in-azure-logic-apps"></a>Parallelle vertakkingen maken of koppelen aan werk stroom acties in Azure Logic Apps

Uw acties in logische app-werk stromen worden standaard sequentieel uitgevoerd. Als u onafhankelijke acties tegelijk wilt uitvoeren, kunt u [parallelle vertakkingen](#parallel-branches)maken en deze vertakkingen later in uw stroom [samen voegen](#join-branches) . 

> [!TIP] 
> Als u een trigger hebt die een matrix ontvangt en een werk stroom voor elk matrix item wilt uitvoeren, kunt u die matrix *debatchren* met de [trigger eigenschap **SplitOn**](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch).

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement hebt, [meld u dan aan voor een gratis Azure-account](https://azure.microsoft.com/free/). 

* Basis kennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="parallel-branches"></a>

## <a name="add-parallel-branch"></a>Parallelle vertakking toevoegen

Als u op hetzelfde moment onafhankelijke stappen wilt uitvoeren, kunt u parallelle vertakkingen naast een bestaande stap toevoegen. 

![Stappen parallel uitvoeren](media/logic-apps-control-flow-branches/parallel.png)

Uw logische app wacht totdat alle vertakkingen zijn voltooid voordat u doorgaat met de werk stroom. Parallelle vertakkingen worden alleen uitgevoerd wanneer hun `runAfter` eigenschaps waarden overeenkomen met de voltooide status van de bovenliggende stap. Zo `branchAction1` worden beide en zo `branchAction2` ingesteld dat ze alleen worden uitgevoerd als de `parentAction` status voltooid is `Succeeded` .

> [!NOTE]
> Voordat u begint, moet uw logische app al een stap bevatten waarin u parallelle vertakkingen kunt toevoegen.

1. Open in de <a href="https://portal.azure.com" target="_blank">Azure Portal</a>uw logische app in de ontwerp functie voor logische apps.

1. Beweeg de aanwijzer over de pijl boven de stap waar u parallelle vertakkingen wilt toevoegen. Kies het **plus** teken ( **+** ) dat wordt weer gegeven, en kies vervolgens **een parallelle vertakking toevoegen**. 

   ![Parallelle vertakking toevoegen](media/logic-apps-control-flow-branches/add-parallel-branch.png)

1. Zoek in het zoekvak de gewenste actie en selecteer deze.

   ![Scherm opname van het venster ' Kies een actie ' in de ontwerp functie voor logische apps.](media/logic-apps-control-flow-branches/find-select-parallel-action.png)

   De geselecteerde actie wordt nu weer gegeven in de parallelle vertakking, bijvoorbeeld:

   ![Zoek en selecteer de gewenste actie](media/logic-apps-control-flow-branches/added-parallel-branch.png)

1. Voeg nu in elke parallelle vertakking de gewenste stappen toe. Als u nog een actie aan een vertakking wilt toevoegen, plaatst u de muis aanwijzer op de actie waar u een opeenvolgende actie wilt toevoegen. Kies het **plus** **+** teken () dat wordt weer gegeven en selecteer vervolgens **een actie toevoegen**.

   ![Sequentiële actie toevoegen aan parallelle vertakking](media/logic-apps-control-flow-branches/add-sequential-action.png)

1. Zoek in het zoekvak de gewenste actie en selecteer deze.

   ![Scherm opname van het venster ' Kies een actie ' en het zoekvak in de ontwerp functie voor logische apps.](media/logic-apps-control-flow-branches/find-select-sequential-action.png)

   De geselecteerde actie wordt nu weer gegeven in de huidige vertakking, bijvoorbeeld:

   ![Zoek en selecteer sequentiële actie](media/logic-apps-control-flow-branches/added-sequential-action.png)

Als u vertakkingen samen wilt samen voegen, moet u lid zijn van [uw parallelle branches](#join-branches). 

<a name="parallel-json"></a>

## <a name="parallel-branch-definition-json"></a>Parallelle vertakkings definitie (JSON)

Als u in de code weergave werkt, kunt u in plaats daarvan de parallelle structuur definiëren in de JSON-definitie van de logische app, bijvoorbeeld:

``` json
{
  "triggers": {
    "myTrigger": {}
  },
  "actions": {
    "parentAction": {
      "type": "<action-type>",
      "inputs": {},
      "runAfter": {}
    },
    "branchAction1": {
      "type": "<action-type>",
      "inputs": {},
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "branchAction2": {
      "type": "<action-type>",
      "inputs": {},
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

<a name="join-branches"></a>

## <a name="join-parallel-branches"></a>Parallelle vertakkingen samen voegen

Als u parallelle vertakkingen samen wilt samen voegen, voegt u een stap onderaan onder alle vertakkingen toe. Deze stap wordt uitgevoerd nadat alle parallelle vertakkingen zijn uitgevoerd.

![Parallelle vertakkingen samen voegen](media/logic-apps-control-flow-branches/join.png)

1. Zoek in de [Azure Portal](https://portal.azure.com)uw logische app in de ontwerp functie voor logische apps en open deze. 

1. Kies **nieuwe stap** onder de parallelle vertakkingen die u wilt toevoegen. 

   ![Stap toevoegen om samen te voegen](media/logic-apps-control-flow-branches/add-join-step.png)

1. Zoek en selecteer de actie die u wilt toevoegen aan de vertakkingen in het zoekvak.

   ![Zoek en selecteer de actie die parallelle vertakkingen samenvoegt](media/logic-apps-control-flow-branches/join-steps.png)

   Uw parallelle vertakkingen zijn nu samengevoegd.

   ![Gekoppelde vertakkingen](media/logic-apps-control-flow-branches/joined-branches.png)

<a name="join-json"></a>

## <a name="join-definition-json"></a>Koppelings definitie (JSON)

Als u in de code weergave werkt, kunt u in plaats daarvan de joinlijn definiëren in de JSON-definitie van de logische app, bijvoorbeeld:

``` json
{
  "triggers": {
    "myTrigger": { }
  },
  "actions": {
    "parentAction": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {}
    },
    "branchAction1": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "branchAction2": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "joinAction": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "branchAction1": [
          "Succeeded"
        ],
        "branchAction2": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

## <a name="get-support"></a>Ondersteuning krijgen

* Ga voor vragen naar de [pagina micro soft Q&een vraag voor Azure Logic apps](/answers/topics/azure-logic-apps.html).
* Ga naar de [site voor Azure Logic Apps gebruikers feedback](https://aka.ms/logicapps-wish)om functies en suggesties te verzenden of hierop te stemmen.

## <a name="next-steps"></a>Volgende stappen

* [Stappen uitvoeren op basis van een voor waarde (voorwaardelijke instructies)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Stappen uitvoeren op basis van verschillende waarden (switch-instructies)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Stappen uitvoeren en herhalen (lussen)](../logic-apps/logic-apps-control-flow-loops.md)
* [Stappen uitvoeren op basis van de gegroepeerde actie status (bereiken)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)

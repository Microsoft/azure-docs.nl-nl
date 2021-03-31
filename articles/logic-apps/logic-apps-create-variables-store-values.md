---
title: Variabelen voor het opslaan en door geven van waarden maken en beheren
description: Meer informatie over het opslaan, beheren, gebruiken en door geven van waarden met behulp van variabelen in uw geautomatiseerde taken en werk stroom die u maakt met Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: b486b94a74d98f5630bd0bf40ebf0864c2ec5ab8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91333899"
---
# <a name="store-and-manage-values-by-using-variables-in-azure-logic-apps"></a>Waarden opslaan en beheren met behulp van variabelen in Azure Logic Apps

In dit artikel wordt beschreven hoe u variabelen maakt en gebruikt die u kunt gebruiken om waarden op te slaan in uw logische app. Variabelen kunnen bijvoorbeeld helpen bij het volgen van het aantal keren dat een lus wordt uitgevoerd. Als u een matrix wilt herhalen of een matrix voor een specifiek item wilt controleren, kunt u een variabele gebruiken om te verwijzen naar het index nummer voor elk matrix item.

U kunt variabelen maken voor gegevens typen, zoals een geheel getal, een float, een Booleaanse waarde, een teken reeks, een matrix en een object. Nadat u een variabele hebt gemaakt, kunt u andere taken uitvoeren, bijvoorbeeld:

* De waarde van de variabele ophalen of ernaar verwijzen.
* Verhoog of verlaag de variabele met een constante waarde, ook wel *verhoogd* en *verlagen*.
* Wijs een andere waarde toe aan de variabele.
* De waarde van de variabele invoegen of *toevoegen* als laatste item in een teken reeks of matrix.

Variabelen bestaan en alleen globaal in het logische app-exemplaar dat ze maakt. Ze blijven ook behouden in alle lus-iteraties binnen een logische app-instantie. Wanneer u verwijst naar een variabele, gebruikt u de naam van de variabele als het token, niet de naam van de actie. Dit is de gebruikelijke manier om te verwijzen naar de uitvoer van een actie.

> [!IMPORTANT]
> Standaard worden cycli parallel uitgevoerd in een lus ' voor elke '. Wanneer u variabelen in lussen gebruikt, voert u de lus [sequentieel](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop) uit zodat variabelen voorspel bare resultaten retour neren.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u geen abonnement hebt, [meldt u zich aan voor een gratis Azure-account](https://azure.microsoft.com/free/).

* De logische app waar u de variabele wilt maken

  Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps?](../logic-apps/logic-apps-overview.md) en [Quick Start: uw eerste logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Een [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) als de eerste stap in uw logische app

  Voordat u acties kunt toevoegen voor het maken en werken met variabelen, moet uw logische app beginnen met een trigger.

<a name="create-variable"></a>

## <a name="initialize-variable"></a>Variabele initialiseren

U kunt een variabele maken en het gegevens type en de oorspronkelijke waarde declareren, allemaal binnen één actie in uw logische app. U kunt alleen variabelen declareren op het globale niveau, niet binnen bereiken, voor waarden en lussen.

1. Open in de [Azure Portal](https://portal.azure.com) of Visual Studio uw logische app in de ontwerp functie voor logische apps.

   In dit voor beeld worden de Azure Portal en een logische app met een bestaande trigger gebruikt.

1. Voer een van de volgende stappen uit in uw logische app, onder de stap waarin u een variabele wilt toevoegen: 

   * Selecteer **nieuwe stap** om een actie toe te voegen onder de laatste stap.

     ![Scherm afbeelding met de actie ' nieuwe stap ' die is geselecteerd op de pagina ' Logic app Designer '.](./media/logic-apps-create-variables-store-values/add-action.png)

   * Als u een actie tussen stappen wilt toevoegen, plaatst u de muis aanwijzer op de pijl om verbinding te maken zodat het plus teken ( **+** ) wordt weer gegeven. Selecteer het plus teken en selecteer vervolgens **een actie toevoegen**.

1. Voer in het zoekvak onder **Kies een actie** `variables` in als uw filter. Selecteer in de lijst acties de optie **variabele initialiseren**.

   ![Actie selecteren](./media/logic-apps-create-variables-store-values/select-initialize-variable-action.png)

1. Geef deze informatie over uw variabele zoals hieronder wordt beschreven:

   | Eigenschap | Vereist | Waarde |  Beschrijving |
   |----------|----------|-------|--------------|
   | **Naam** | Ja | <*variabele-naam*> | De naam van de variabele die moet worden verhoogd |
   | **Type** | Ja | <*variabele-type*> | Het gegevens type voor de variabele |
   | **Waarde** | Nee | <*begin waarde*> | De beginwaarde van uw variabele <p><p>**Tip**: Hoewel dit optioneel is, stelt u deze waarde in op best practice zodat u altijd de begin waarde voor de variabele kent. |
   |||||

   Bijvoorbeeld:

   ![Variabele initialiseren](./media/logic-apps-create-variables-store-values/initialize-variable.png)

1. Ga nu verder met het toevoegen van de gewenste acties. Selecteer **Opslaan** op de werkbalk van de ontwerper wanneer u klaar bent.

Als u overschakelt van de ontwerp functie naar de code weergave-editor, is dit de manier waarop de actie **variabele initialiseren** wordt weer gegeven in de definitie van de logische app, in JavaScript object NOTATION (JSON)-indeling:

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "Count",
               "type": "Integer",
               "value": 0
          } ]
      },
      "runAfter": {}
   }
},
```

> [!NOTE]
> Hoewel de actie **variabele initialiseren** een `variables` sectie heeft die is gestructureerd als een matrix, kan de actie slechts één variabele tegelijk maken. Elke nieuwe variabele vereist een afzonderlijke actie voor **het initialiseren van een variabele** .

Hier volgen enkele voor beelden van andere typen variabelen:

*Teken reeks variabele*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myStringVariable",
               "type": "String",
               "value": "lorem ipsum"
          } ]
      },
      "runAfter": {}
   }
},
```

*Booleaanse variabele*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myBooleanVariable",
               "type": "Boolean",
               "value": false
          } ]
      },
      "runAfter": {}
   }
},
```

*Matrix met gehele getallen*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myArrayVariable",
               "type": "Array",
               "value": [1, 2, 3]
          } ]
      },
      "runAfter": {}
   }
},
```

*Matrix met teken reeksen*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myArrayVariable",
               "type": "Array",
               "value": ["red", "orange", "yellow"]
          } ]
      },
      "runAfter": {}
   }
},
```

<a name="get-value"></a>

## <a name="get-the-variables-value"></a>De waarde van de variabele ophalen

Als u de inhoud van een variabele wilt ophalen of ernaar wilt verwijzen, kunt u ook de [functie Varia BLES ()](../logic-apps/workflow-definition-language-functions-reference.md#variables) in de Logic app Designer en de code weergave-editor gebruiken. Bij het verwijzen naar een variabele gebruikt u de naam van de variabele als het token, niet de naam van de actie. Dit is de gebruikelijke manier om te verwijzen naar de uitvoer van een actie.

Met deze expressie worden bijvoorbeeld de items opgehaald uit de matrix variabele die [eerder in dit artikel is gemaakt](#append-value) met behulp van de `variables()` functie. De `string()` functie retourneert de inhoud van de variabele in teken reeks notatie: `"1, 2, 3, red"`

```json
@{string(variables('myArrayVariable'))}
```

<a name="increment-value"></a>

## <a name="increment-variable"></a>Toename variabele 

Als u een variabele wilt verg Roten *of verhogen met een constante* waarde, voegt u de actie voor het **verhogen van variabele** toe aan uw logische app. Deze actie werkt alleen met de variabelen integer en float.

1. Selecteer in de ontwerp functie voor logische apps, onder de stap waarin u een bestaande variabele wilt verg Roten, de optie **nieuwe stap**. 

   Deze logische app heeft bijvoorbeeld al een trigger en een actie die een variabele heeft gemaakt. Voeg dus onder deze stappen een nieuwe actie toe:

   ![Actie toevoegen](./media/logic-apps-create-variables-store-values/add-increment-variable-action.png)

   Als u een actie wilt toevoegen tussen de bestaande stappen, plaatst u de muis aanwijzer op de pijl om verbinding te maken zodat het plus teken (+) wordt weer gegeven. Selecteer het plus teken en selecteer vervolgens **een actie toevoegen**.

1. Voer in het zoekvak ' increment variable ' in als uw filter. Selecteer in de lijst acties de optie **variabele verhogen**.

   ![Selecteer de actie variabele door lopen](./media/logic-apps-create-variables-store-values/select-increment-variable-action.png)

1. Geef deze informatie op voor het verhogen van de variabele:

   | Eigenschap | Vereist | Waarde |  Beschrijving |
   |----------|----------|-------|--------------|
   | **Naam** | Ja | <*variabele-naam*> | De naam van de variabele die moet worden verhoogd |
   | **Waarde** | Nee | <*Increment-waarde*> | De waarde die wordt gebruikt voor het verhogen van de variabele. De standaard waarde is één. <p><p>**Tip**: Hoewel dit optioneel is, stelt u deze waarde in op best practice zodat u altijd weet wat de specifieke waarde is voor het verhogen van de variabele. |
   ||||

   Bijvoorbeeld:

   ![Voor beeld van een incrementele waarde](./media/logic-apps-create-variables-store-values/increment-variable-action-information.png)

1. Selecteer **Opslaan** op de werkbalk van de ontwerper wanneer u klaar bent.

Als u overschakelt van de ontwerp functie naar de code weergave-editor, is dit de manier waarop de actie voor het **verhogen van variabelen** wordt weer gegeven in de definitie van de logische app, in JSON-indeling:

```json
"actions": {
   "Increment_variable": {
      "type": "IncrementVariable",
      "inputs": {
         "name": "Count",
         "value": 1
      },
      "runAfter": {}
   }
},
```

## <a name="example-create-loop-counter"></a>Voor beeld: loop-teller maken

Variabelen worden vaak gebruikt voor het tellen van het aantal keren dat een lus wordt uitgevoerd. In dit voor beeld ziet u hoe u variabelen voor deze taak maakt en gebruikt door een lus te maken waarmee de bijlagen in een e-mail bericht worden geteld.

1. Maak in de Azure Portal een lege logische app. Voeg een trigger toe waarmee wordt gecontroleerd op nieuwe e-mail en eventuele bijlagen.

   In dit voor beeld wordt de Office 365 Outlook-trigger gebruikt voor **wanneer er een nieuwe e-mail binnenkomt**. U kunt deze trigger zo instellen dat deze alleen wordt geactiveerd wanneer het e-mail bericht bijlagen bevat. U kunt echter elke connector gebruiken waarmee wordt gecontroleerd op nieuwe e-mail berichten met bijlagen, zoals de Outlook.com-connector.

1. Selecteer **Ja** bij de volgende eigenschappen in de trigger om te controleren op bijlagen en de bijlagen door te geven aan de werk stroom van de logische app:

   * **Heeft bijlage**
   * **Bijlagen opnemen**

   ![Controleren op bijlagen en deze toevoegen](./media/logic-apps-create-variables-store-values/check-include-attachments.png)

1. Voeg de [actie **variabele initialiseren**](#create-variable)toe. Maak een variabele `Count` met een geheel getal met de naam die een begin waarde van nul heeft.

   ![Actie toevoegen voor ' variabele initialiseren '](./media/logic-apps-create-variables-store-values/initialize-variable.png)

1. Als u elke bijlage wilt door lopen, voegt u een *voor elke* lus toe.

   1. Selecteer **nieuwe stap** onder de actie **variabele initialiseren** .

   1. Selecteer onder **Kies een actie** de optie **Ingebouwd**. Voer in het zoekvak in `for each` als uw zoek filter en selecteer **voor elk**.

      ![Een lus voor elke toevoegen](./media/logic-apps-create-variables-store-values/add-loop.png)

1. Klik in de lus in het vak **een uitvoer van de vorige stappen selecteren** . Wanneer de lijst met dynamische inhoud wordt weer gegeven, selecteert u **bijlagen**.

   ![Selecteer 'Bijlagen'](./media/logic-apps-create-variables-store-values/select-attachments.png)

   De eigenschap **bijlagen** geeft een matrix aan, die de e-mail bijlagen van de uitvoer van de trigger in uw lus bevat.

1. Selecteer **een actie toevoegen** in de lus **voor elke** .

   ![Selecteer een actie toevoegen](./media/logic-apps-create-variables-store-values/add-action-2.png)

1. Voer in het zoekvak ' increment variable ' in als uw filter. Selecteer in de lijst acties de optie **variabele verhogen**.

   > [!NOTE]
   > Zorg ervoor dat de actie **variabele stapsgewijs** wordt weer gegeven in de lus. Als de actie buiten de lus wordt weer gegeven, sleept u de actie naar de lus.

1. Selecteer in de actie **variabele verhogen** in de lijst **naam** de variabele **aantal** .

   ![Selecteer de variabele Count](./media/logic-apps-create-variables-store-values/add-increment-variable-example.png)

1. Voeg onder de lus elke actie toe waarmee u het aantal bijlagen verzendt. Neem in uw actie de waarde van de variabele **Count** op, bijvoorbeeld:

   ![Een actie toevoegen die resultaten verzendt](./media/logic-apps-create-variables-store-values/send-email-results.png)

1. Sla uw logische app op. Selecteer **Opslaan** op de werkbalk van de ontwerper.

### <a name="test-your-logic-app"></a>Uw logische app testen

1. Als uw logische app niet is ingeschakeld, selecteert u **overzicht** in het menu van de logische app. Selecteer **inschakelen** op de werk balk.

1. Selecteer **uitvoeren** op de werk balk van de Logic app-ontwerp functie. Met deze stap wordt de logische app hand matig gestart.

1. Een e-mail met een of meer bijlagen verzenden naar het e-mail account dat u in dit voor beeld hebt gebruikt.

   Met deze stap wordt de trigger van de logische app geactiveerd, waarmee een exemplaar wordt gemaakt en uitgevoerd voor de werk stroom van uw logische app. Als gevolg hiervan verzendt de logische app u een bericht of een e-mail met het aantal bijlagen in de e-mail die u hebt verzonden.

Als u overschakelt van de ontwerp functie naar de code weergave-editor, is dit de manier waarop de **voor elke** lus wordt weer gegeven samen met de actie voor het verhogen van de **variabele** in de definitie van de logische app, in JSON-indeling.

```json
"actions": {
   "For_each": {
      "type": "Foreach",
      "actions": {
         "Increment_variable": {
           "type": "IncrementVariable",
            "inputs": {
               "name": "Count",
               "value": 1
            },
            "runAfter": {}
         }
      },
      "foreach": "@triggerBody()?['Attachments']",
      "runAfter": {
         "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

<a name="decrement-value"></a>

## <a name="decrement-variable"></a>Variabele verlagen

Als u een variabele wilt verkleinen of *verlagen* met een constante waarde, volgt u de stappen voor het [verg Roten van een variabele](#increment-value) , behalve dat u zoekt en selecteert u in plaats daarvan de actie **variabele verlagen** . Deze actie werkt alleen met de variabelen integer en float.

Dit zijn de eigenschappen voor de actie **variabele verlagen** :

| Eigenschap | Vereist | Waarde |  Beschrijving |
|----------|----------|-------|--------------|
| **Naam** | Ja | <*variabele-naam*> | De naam van de variabele die moet worden verminderd | 
| **Waarde** | Nee | <*Increment-waarde*> | De waarde voor decrementing van de variabele. De standaard waarde is één. <p><p>**Tip**: Hoewel dit optioneel is, stelt u deze waarde in als een Best Practice, zodat u altijd weet wat de specifieke waarde voor decrementing uw variabele is. |
||||| 

Als u overschakelt van de ontwerp functie naar de code weergave-editor, is dit de manier waarop de actie **variabele verlagen** wordt weer gegeven in de definitie van de logische app, in JSON-indeling.

```json
"actions": {
   "Decrement_variable": {
      "type": "DecrementVariable",
      "inputs": {
         "name": "Count",
         "value": 1
      },
      "runAfter": {}
   }
},
```

<a name="assign-value"></a>

## <a name="set-variable"></a>Variabele instellen

Als u een andere waarde aan een bestaande variabele wilt toewijzen, volgt u de stappen voor het [verg Roten van een variabele](#increment-value) , behalve dat u:

1. Zoek en selecteer in plaats daarvan de actie **variabele instellen** .

1. Geef de naam en waarde van de variabele op die u wilt toewijzen. Zowel de nieuwe waarde als de variabele moeten hetzelfde gegevens type hebben. De waarde is vereist omdat deze actie geen standaard waarde heeft.

Dit zijn de eigenschappen voor de actie **variabele instellen** :

| Eigenschap | Vereist | Waarde |  Beschrijving |
|----------|----------|-------|--------------|
| **Naam** | Ja | <*variabele-naam*> | De naam van de variabele die moet worden gewijzigd |
| **Waarde** | Yes | <*nieuwe waarde*> | De waarde die u wilt toewijzen aan de variabele. Beide moeten hetzelfde gegevens type hebben. |
||||| 

> [!NOTE]
> Tenzij u de variabelen verdeelt of decrementing, worden door het wijzigen van variabelen in lussen *mogelijk* onverwachte resultaten gemaakt omdat lussen standaard parallel of gelijktijdig worden uitgevoerd. Voor deze gevallen kunt u de lus zo instellen dat deze achter elkaar wordt uitgevoerd. Als u bijvoorbeeld wilt verwijzen naar de waarde van de variabele in de lus en dezelfde waarde aan het begin en einde van die lusinstructie wilt verwachten, volgt u deze stappen om de uitvoering van de lus te wijzigen: 
>
> 1. Selecteer in de rechter bovenhoek van de lus de knop met het weglatings teken (**...**) en selecteer vervolgens **instellingen**.
> 
> 2. Wijzig de standaard instelling voor **overschrijven** onder **Gelijktijdigheids beheer** in **op aan**.
>
> 3. Sleep de schuif regelaar **van de parallelle graad** naar **1**.

Als u overschakelt van de ontwerp functie naar de code weergave-editor, is dit de manier waarop de actie **variabele instellen** wordt weer gegeven in de definitie van de logische app, in JSON-indeling. In dit voor beeld wordt de `Count` huidige waarde van de variabele gewijzigd in een andere waarde.

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "Count",
               "type": "Integer",
               "value": 0
          } ]
      },
      "runAfter": {}
   },
   "Set_variable": {
      "type": "SetVariable",
      "inputs": {
         "name": "Count",
         "value": 100
      },
      "runAfter": {
         "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

<a name="append-value"></a>

## <a name="append-to-variable"></a>Toevoegen aan variabele

Voor variabelen waarin teken reeksen of matrices worden opgeslagen, kunt u de waarde van een variabele als laatste item in die teken reeksen of matrices invoegen of *toevoegen* . U kunt de stappen voor het [verg Roten van een variabele](#increment-value) volgen, behalve dat u deze stappen in plaats daarvan uitvoert: 

1. Zoek en selecteer een van deze acties op basis van het feit of uw variabele een teken reeks of een matrix is: 

   * **Toevoegen aan teken reeks variabele**
   * **Toevoegen aan matrix variabele** 

1. Geef de waarde op die moet worden toegevoegd als het laatste item in de teken reeks of matrix. Deze waarde is verplicht.

Dit zijn de eigenschappen voor de acties **toevoegen aan..** .:

| Eigenschap | Vereist | Waarde |  Beschrijving |
|----------|----------|-------|--------------|
| **Naam** | Ja | <*variabele-naam*> | De naam van de variabele die moet worden gewijzigd |
| **Waarde** | Yes | <*toegevoegde waarde*> | De waarde die u wilt toevoegen, die elk type kan hebben |
|||||

Als u overschakelt van de ontwerp functie naar de code weergave-editor, is dit de manier waarop de actie **toevoegen aan matrix variabele** wordt weer gegeven in de definitie van de logische app, in JSON-indeling. In dit voor beeld wordt een matrix variabele gemaakt en wordt een andere waarde toegevoegd als het laatste item in de matrix. Het resultaat is een bijgewerkte variabele die deze matrix bevat: `[1,2,3,"red"]`

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
            "name": "myArrayVariable",
            "type": "Array",
            "value": [1, 2, 3]
         } ]
      },
      "runAfter": {}
   },
   "Append_to_array_variable": {
      "type": "AppendToArrayVariable",
      "inputs": {
         "name": "myArrayVariable",
         "value": "red"
      },
      "runAfter": {
        "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Logic apps connectors](../connectors/apis-list.md)

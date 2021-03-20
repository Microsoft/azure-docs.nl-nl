---
title: Lussen toevoegen om acties te herhalen
description: Herhalingen maken waarmee werkstroomacties worden herhaald of matrices worden verwerkt in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/05/2019
ms.openlocfilehash: aa4be5852b4f8af00346a3ea9a86b13a85f99824
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93358453"
---
# <a name="create-loops-that-repeat-workflow-actions-or-process-arrays-in-azure-logic-apps"></a>Herhalingen maken waarmee werkstroomacties worden herhaald of matrices worden verwerkt in Azure Logic Apps

Als u een matrix in uw logische app wilt verwerken, kunt u een [' foreach-lus](#foreach-loop)maken. Met deze lus worden een of meer acties herhaald voor elk item in de matrix. Zie voor de limiet voor het aantal matrix items dat door een ' foreach-lus kan worden verwerkt ' [gelijktijdigheid, lussen en limieten voor het debatchren](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits).

Als u acties wilt herhalen totdat aan een voor waarde wordt voldaan of een status verandert, kunt u een [' until '-lus](#until-loop)maken. De logische app voert eerst alle acties in de lus uit en controleert vervolgens de voor waarde of status. Als aan de voor waarde wordt voldaan, wordt de lus gestopt. Anders wordt de lus herhaald. Zie voor de standaard-en maximum limieten voor het aantal ' until ' dat een logische app wordt uitgevoerd, [gelijktijdigheid, lussen en het debatchren van limieten](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits).

> [!TIP]
> Als u een trigger hebt die een matrix ontvangt en een werk stroom voor elk matrix item wilt uitvoeren, kunt u die matrix *debatchren* met de [trigger eigenschap **SplitOn**](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch).

## <a name="prerequisites"></a>Vereisten

* Een Azure-account en -abonnement. Als u nog geen abonnement hebt, [meld u dan aan voor een gratis Azure-account](https://azure.microsoft.com/free/). 

* Basis kennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="foreach-loop"></a>

## <a name="foreach-loop"></a>Foreach-lus

Een ' foreach '-lus herhaalt een of meer acties voor elk matrix item en werkt alleen op matrices. Hier volgen enkele aandachtspunten bij het gebruik van ' foreach ' lussen:

* De foreach-lus kan een beperkt aantal matrix items verwerken. Zie voor deze limiet [gelijktijdigheid, lussen en limieten voor het debatchren](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits).

* Standaard worden herhalingen in een ' foreach '-lus tegelijk uitgevoerd, of parallel. Dit gedrag verschilt van [het automatiseren van de stroom op **elke** lus](/power-automate/apply-to-each) waar iteraties een voor een of twee keer worden uitgevoerd. U kunt echter [opeenvolgende ' foreach ' lussen instellen](#sequential-foreach-loop). Als u de volgende iteratie in een lus ' foreach ' wilt onderbreken met behulp van de [vertragings actie](../connectors/connectors-native-delay.md), moet u de lus zo instellen dat deze sequentieel wordt uitgevoerd.

  De uitzonde ring op het standaard gedrag zijn geneste lussen waarbij iteraties altijd opeenvolgend worden uitgevoerd, niet parallel. Als u bewerkingen parallel wilt uitvoeren voor items in een geneste lus, maakt en [roept u een onderliggende logische app](../logic-apps/logic-apps-http-endpoint.md)aan.

* Als u voorspel bare resultaten wilt krijgen van bewerkingen op variabelen tijdens elke lus iteratie, voert u deze lussen sequentieel uit. Wanneer een lus die gelijktijdig wordt uitgevoerd bijvoorbeeld eindigt, wordt de waarde voor het verhogen, verlagen en toevoegen aan variabele bewerkingen voorspel bare resultaten geretourneerd. Tijdens elke iteratie in de gelijktijdig uitgevoerde lus kunnen deze bewerkingen echter onverwachte resultaten opleveren. 

* Acties in een ' foreach '-lus gebruiken de [`@item()`](../logic-apps/workflow-definition-language-functions-reference.md#item) 
expressie voor het verwijzen naar en verwerken van elk item in de matrix. Als u gegevens opgeeft die zich niet in een matrix bevindt, mislukt de werk stroom van de logische app. 

In deze voorbeeld logische app wordt een dagelijks overzicht verzonden voor een RSS-feed voor een website. De app gebruikt een ' foreach '-lus die een e-mail verzendt voor elk nieuw item.

1. [Maak deze voorbeeld logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md) met een Outlook.com-account of een werk-of school account.

2. Voeg tussen de actie RSS-trigger en e-mail bericht verzenden een ' foreach-lus toe. 

   1. Als u een lus tussen de stappen wilt toevoegen, plaatst u de muis aanwijzer op de pijl tussen deze stappen. 
   Kies het **plus teken** ( **+** ) dat wordt weer gegeven en selecteer vervolgens **een actie toevoegen**.

      ![Selecteer een actie toevoegen](media/logic-apps-control-flow-loops/add-for-each-loop.png)

   1. Kies **Alle** onder het zoekvak. Typ in het zoekvak ' voor elke ' als uw filter. Selecteer in de lijst acties deze actie: **voor elk besturings element**

      ![Lus ' voor elke ' toevoegen](media/logic-apps-control-flow-loops/select-for-each.png)

3. Bouw nu de lus op. Onder **Selecteer een uitvoer van de vorige stappen** nadat de lijst **dynamische inhoud toevoegen** wordt weer gegeven, selecteert u de matrix **feed koppelingen** , die wordt uitgevoerd vanuit de RSS-trigger. 

   ![Selecteren in de lijst met dynamische inhoud](media/logic-apps-control-flow-loops/for-each-loop-dynamic-content-list.png)

   > [!NOTE] 
   > U kunt *alleen* matrix uitvoer selecteren uit de vorige stap.

   De geselecteerde matrix wordt nu hier weer gegeven:

   ![Matrix selecteren](media/logic-apps-control-flow-loops/for-each-loop-select-array.png)

4. Als u een actie op elk matrix item wilt uitvoeren, sleept u de actie **een E-mail verzenden** naar de lus. 

   Uw logische app kan er ongeveer als volgt uitzien:

   ![Stappen toevoegen aan de lus foreach](media/logic-apps-control-flow-loops/for-each-loop-with-step.png)

5. Sla uw logische app op. Als u uw logische app hand matig wilt testen, kiest u **uitvoeren** op de werk balk van de ontwerp functie.

<a name="for-each-json"></a>

## <a name="foreach-loop-definition-json"></a>"Foreach"-loop definitie (JSON)

Als u werkt in de code weergave voor uw logische app, kunt u in `Foreach` plaats daarvan de lus definiëren in de JSON-definitie van de logische app, bijvoorbeeld:

``` json
"actions": {
   "myForEachLoopName": {
      "type": "Foreach",
      "actions": {
         "Send_an_email": {
            "type": "ApiConnection",
            "inputs": {
               "body": {
                  "Body": "@{item()}",
                  "Subject": "New CNN post @{triggerBody()?['publishDate']}",
                  "To": "me@contoso.com"
               },
               "host": {
                  "api": {
                     "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/office365"
                  },
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            },
            "runAfter": {}
         }
      },
      "foreach": "@triggerBody()?['links']",
      "runAfter": {}
   }
}
```

<a name="sequential-foreach-loop"></a>

## <a name="foreach-loop-sequential"></a>"Foreach"-lus: sequentieel

Standaard worden cycli parallel uitgevoerd in een ' foreach '-lus. Als u elke cyclus opeenvolgend wilt uitvoeren, stelt u de **opeenvolgende** optie van de lus in. "Foreach"-lussen moeten sequentieel worden uitgevoerd als u geneste lussen of variabelen in lussen wilt waar u voorspel bare resultaten verwacht. 

1. Kies in de rechter bovenhoek van de lus **weglatings** tekens (**...**) > **instellingen**.

   ![Kies op de lus ' foreach ' de optie '... ' > ' instellingen '](media/logic-apps-control-flow-loops/for-each-loop-settings.png)

1. Schakel onder **Gelijktijdigheids beheer** de instelling voor **Gelijktijdigheids beheer** in **op aan**. Verplaats de schuif regelaar **van de parallelle toestand** naar **1** en kies **gereed**.

   ![Gelijktijdigheids beheer inschakelen](media/logic-apps-control-flow-loops/for-each-loop-sequential-setting.png)

Als u werkt met de JSON-definitie van uw logische app, kunt u de `Sequential` optie gebruiken door de `operationOptions` para meter toe te voegen, bijvoorbeeld:

``` json
"actions": {
   "myForEachLoopName": {
      "type": "Foreach",
      "actions": {
         "Send_an_email": { }
      },
      "foreach": "@triggerBody()?['links']",
      "runAfter": {},
      "operationOptions": "Sequential"
   }
}
```

<a name="until-loop"></a>

## <a name="until-loop"></a>' Until '-lus
  
Als u acties wilt uitvoeren en herhalen totdat aan een voor waarde wordt voldaan of een status verandert, plaatst u deze acties in een lus ' until '. In uw logische app worden eerst alle acties in de lus uitgevoerd en wordt vervolgens de voor waarde of staat gecontroleerd. Als aan de voor waarde wordt voldaan, wordt de lus gestopt. Anders wordt de lus herhaald. Zie voor de standaard-en maximum limieten voor het aantal ' until ' dat een logische app wordt uitgevoerd, [gelijktijdigheid, lussen en het debatchren van limieten](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits).

Hier volgen enkele veelvoorkomende scenario's waarin u een ' until '-lus kunt gebruiken:

* Roep een eind punt aan totdat u het gewenste antwoord hebt ontvangen.

* Een record maken in een Data Base. Wacht totdat een specifiek veld in die record wordt goedgekeurd. Ga verder met de verwerking. 

Vanaf 8:00 uur elke dag wordt in dit voor beeld van de logische app een variabele verhoogd totdat de waarde van de variabele 10 is. De logische app verzendt vervolgens een e-mail die de huidige waarde bevestigt. 

> [!NOTE]
> In deze stappen wordt Office 365 Outlook gebruikt, maar u kunt elke e-mail provider die Logic Apps ondersteunt gebruiken. 
> [Raadpleeg de lijst met connectors hier](/connectors/). Als u een ander e-mail account gebruikt, blijven de algemene stappen hetzelfde, maar uw gebruikers interface kan er iets anders uitzien. 

1. Een lege, logische app maken. In de ontwerp functie voor logische apps selecteert u in het zoekvak **alle**. Zoek naar "recurrence". 
   Selecteer in de lijst triggers deze trigger: **herhalings schema**

   ![Trigger voor terugkeer patroon toevoegen](./media/logic-apps-control-flow-loops/do-until-loop-add-trigger.png)

1. Geef op wanneer de trigger wordt geactiveerd door het interval, de frequentie en het uur van de dag in te stellen. Kies **Geavanceerde opties weer geven** om het uur in te stellen.

   ![Terugkeer schema instellen](./media/logic-apps-control-flow-loops/do-until-loop-set-trigger-properties.png)

   | Eigenschap | Waarde |
   | -------- | ----- |
   | **Interval** | 1 | 
   | **Frequentie** | Dag |
   | **Deze uren** | 8 |
   ||| 

1. Kies **nieuwe stap** onder de trigger. 
   Zoek naar ' variabelen ' en selecteer deze actie: **variabele variabelen initialiseren**

   ![De actie variabele variabelen initialiseren toevoegen](./media/logic-apps-control-flow-loops/do-until-loop-add-variable.png)

1. Stel de variabele in met deze waarden:

   ![Eigenschappen van variabele instellen](./media/logic-apps-control-flow-loops/do-until-loop-set-variable-properties.png)

   | Eigenschap | Waarde | Beschrijving |
   | -------- | ----- | ----------- |
   | **Naam** | Limiet | De naam van de variabele | 
   | **Type** | Geheel getal | Het gegevens type van uw variabele | 
   | **Waarde** | 0 | Begin waarde van de variabele | 
   |||| 

1. Kies **nieuwe stap** onder de actie **variabele initialiseren** . 

1. Kies **Alle** onder het zoekvak. Zoek naar ' until ' en selecteer deze actie: **until-Control**

   ![Lus ' until ' toevoegen](./media/logic-apps-control-flow-loops/do-until-loop-add-until-loop.png)

1. Bouw de voor waarde van de lus op door de **limiet** variabele te selecteren en de operator **is gelijk aan** . 
   Voer **10** in als de vergelijkings waarde.

   ![Uitgangs voorwaarde voor het stoppen van de lus opbouwen](./media/logic-apps-control-flow-loops/do-until-loop-settings.png)

1. Kies in de lus **een actie toevoegen**. 

1. Kies **Alle** onder het zoekvak. Zoek naar "Varia bles" en selecteer deze actie: **variabele variabelen ophogen**

   ![Actie toevoegen voor oplopende variabele](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable.png)

1. Selecteer bij **naam** de **limiet** variabele. Voer voor **waarde**' 1 ' in. 

     ![' Limiet ' verhogen door 1](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable-settings.png)

1. Kies voor buiten en onder de lus **nieuwe stap**. 

1. Kies **Alle** onder het zoekvak. 
     Zoek en voeg een actie toe waarmee e-mail berichten worden verzonden, bijvoorbeeld: 

     ![Actie toevoegen die e-mail verzendt](media/logic-apps-control-flow-loops/do-until-loop-send-email.png)

1. Meld u aan bij uw e-mailaccount als dat wordt gevraagd.

1. De eigenschappen van de e-mail actie instellen. Voeg de **limiet** variabele toe aan het onderwerp. Op die manier kunt u controleren of de huidige waarde van de variabele voldoet aan de opgegeven voor waarde, bijvoorbeeld:

      ![E-mail eigenschappen instellen](./media/logic-apps-control-flow-loops/do-until-loop-send-email-settings.png)

      | Eigenschap | Waarde | Beschrijving |
      | -------- | ----- | ----------- | 
      | **Aan** | *\<email-address\@domain>* | Het e-mail adres van de ontvanger. Gebruik uw eigen e-mail adres om te testen. | 
      | **Onderwerp** | De huidige waarde voor limiet is **limiet** | Geef het onderwerp van de e-mail op. Voor dit voor beeld moet u de **limiet** variabele toevoegen. | 
      | **Hoofdtekst** | <*e-mail: inhoud*> | Geef de inhoud van het e-mail bericht op die u wilt verzenden. Voor dit voor beeld voert u de gewenste tekst in. | 
      |||| 

1. Sla uw logische app op. Als u uw logische app hand matig wilt testen, kiest u **uitvoeren** op de werk balk van de ontwerp functie.

      Nadat de logica is gestart, krijgt u een e-mail met de inhoud die u hebt opgegeven:

      ![Ontvangen e-mail adres](./media/logic-apps-control-flow-loops/do-until-loop-sent-email.png)

<a name="prevent-endless-loops"></a>

## <a name="prevent-endless-loops"></a>Eindeloze lussen voor komen

De lus until stopt de uitvoering op basis van deze eigenschappen. Zorg er dus voor dat u de waarden dienovereenkomstig instelt:

* **Aantal**: deze waarde is het hoogste aantal lussen dat wordt uitgevoerd voordat de lus wordt afgesloten. Zie voor de standaard-en maximum limieten voor het aantal ' until ' dat een logische app wordt uitgevoerd, [gelijktijdigheid, lussen en het debatchren van limieten](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits).

* **Time-out**: deze waarde is de meeste tijd die de lus uitvoert voordat deze wordt afgesloten en is opgegeven in [ISO 8601-indeling](https://en.wikipedia.org/wiki/ISO_8601). Zie voor de standaard-en maximum limieten voor de **time-outwaarde** de [limieten voor gelijktijdigheid, lussen en](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)het afrekenen van batches.

  De time-outwaarde wordt geëvalueerd voor elke lus-cyclus. Als een actie in de lus langer duurt dan de time-outlimiet, wordt de huidige cyclus niet gestopt. De volgende cyclus wordt echter niet gestart omdat niet wordt voldaan aan de limiet voorwaarde.

Als u deze limieten wilt wijzigen, selecteert u in de actie herhalen de optie **limieten wijzigen**.

<a name="until-json"></a>

## <a name="until-definition-json"></a>De ' until ' definitie (JSON)

Als u werkt in de code weergave voor uw logische app, kunt u in `Until` plaats daarvan een lus definiëren in de JSON-definitie van de logische app, bijvoorbeeld:

``` json
"actions": {
   "Initialize_variable": {
      // Definition for initialize variable action
   },
   "Send_an_email": {
      // Definition for send email action
   },
   "Until": {
      "type": "Until",
      "actions": {
         "Increment_variable": {
            "type": "IncrementVariable",
            "inputs": {
               "name": "Limit",
               "value": 1
            },
            "runAfter": {}
         }
      },
      "expression": "@equals(variables('Limit'), 10)",
      // To prevent endless loops, an "Until" loop 
      // includes these default limits that stop the loop. 
      "limit": { 
         "count": 60,
         "timeout": "PT1H"
      },
      "runAfter": {
         "Initialize_variable": [
            "Succeeded"
         ]
      }
   }
}
```

In dit voor beeld ' until ' wordt een HTTP-eind punt aangeroepen, waardoor een resource wordt gemaakt. De lus stopt wanneer de hoofd tekst van het HTTP-antwoord retourneert met de `Completed` status. Om te voor komen dat er eindeloos lussen optreden, stopt de lus ook als een van deze voor waarden zich voordoet:

* De lus heeft 10 keer uitgevoerd zoals opgegeven door het `count` kenmerk. De standaard waarde is 60 keer. 

* De lus is twee uur uitgevoerd, zoals opgegeven door het `timeout` kenmerk in de ISO 8601-indeling. De standaard waarde is een uur.
  
``` json
"actions": {
   "myUntilLoopName": {
      "type": "Until",
      "actions": {
         "Create_new_resource": {
            "type": "Http",
            "inputs": {
               "body": {
                  "resourceId": "@triggerBody()"
               },
               "url": "https://domain.com/provisionResource/create-resource",
               "body": {
                  "resourceId": "@triggerBody()"
               }
            },
            "runAfter": {},
            "type": "ApiConnection"
         }
      },
      "expression": "@equals(triggerBody(), 'Completed')",
      "limit": {
         "count": 10,
         "timeout": "PT2H"
      },
      "runAfter": {}
   }
}
```

## <a name="get-support"></a>Ondersteuning krijgen

* Ga voor vragen naar de [pagina micro soft Q&een vraag voor Azure Logic apps](/answers/topics/azure-logic-apps.html).
* [Azure Logic apps site voor gebruikers feedback](https://aka.ms/logicapps-wish)om functies en suggesties te verzenden of te stemmen.

## <a name="next-steps"></a>Volgende stappen

* [Stappen uitvoeren op basis van een voor waarde (voorwaardelijke instructies)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Stappen uitvoeren op basis van verschillende waarden (switch-instructies)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Parallelle stappen uitvoeren of samen voegen (vertakkingen)](../logic-apps/logic-apps-control-flow-branches.md)
* [Stappen uitvoeren op basis van de gegroepeerde actie status (bereiken)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)

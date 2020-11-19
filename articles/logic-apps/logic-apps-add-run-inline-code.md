---
title: Code fragmenten toevoegen en uitvoeren met inline code
description: Meer informatie over het maken en uitvoeren van code fragmenten met inline code acties voor geautomatiseerde taken en werk stromen die u maakt met Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: article
ms.date: 11/19/2020
ms.custom: devx-track-js
ms.openlocfilehash: 589420d96a3a6dfcc1c17a1b204765022b1ce412
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94916641"
---
# <a name="add-and-run-code-snippets-by-using-inline-code-in-azure-logic-apps"></a>Code fragmenten toevoegen en uitvoeren met inline code in Azure Logic Apps

Als u een stukje code in uw logische app wilt uitvoeren, kunt u de ingebouwde **inline code** actie toevoegen als een stap in de werk stroom van uw logische app. Deze actie werkt het beste als u code wilt uitvoeren die in dit scenario past:

* Wordt uitgevoerd in Java script. Meer talen binnenkort beschikbaar.

* De uitvoering binnen vijf seconden of minder is voltooid.

* Verwerkt gegevens tot 50 MB groot.

* Is niet vereist voor het werken met [ **variabelen** acties](../logic-apps/logic-apps-create-variables-store-values.md), die nog niet worden ondersteund.

* Maakt gebruik van Node.js versie 8.11.1. Zie [standaard ingebouwde objecten](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects)voor meer informatie.

  > [!NOTE]
  > De `require()` functie wordt niet ondersteund door de **inline code** actie voor het uitvoeren van Java script.

Met deze actie wordt het code fragment uitgevoerd en wordt de uitvoer van het fragment geretourneerd als een token met de naam **Result**, dat u in de volgende acties in uw logische app kunt gebruiken. Voor andere scenario's waarin u een functie voor uw code wilt maken, probeert u [een Azure-functie te maken en](../logic-apps/logic-apps-azure-functions.md) aan te roepen in uw logische app.

In dit artikel wordt de logische app geactiveerd wanneer een nieuwe e-mail binnenkomt in een werk-of school account. Het code fragment extraheert en retourneert alle e-mail adressen die worden weer gegeven in de hoofd tekst van de e-mail.

![Voor beeld-overzicht](./media/logic-apps-add-run-inline-code/inline-code-example-overview.png)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* De logische app waaraan u het code fragment wilt toevoegen, inclusief een trigger. Als u geen logische app hebt, raadpleegt u [Quick Start: uw eerste logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md).

   In het voor beeld van een logische app in dit onderwerp wordt deze Office 365 Outlook-trigger gebruikt: **wanneer er een nieuwe e-mail binnenkomt**

* Een [integratie account](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) dat is gekoppeld aan uw logische app. Als u geen integratie account wilt maken of gebruiken, maakt u een logische app in het Azure Portal met behulp van het nieuwe **logische app-resource type (preview)** of in Visual Studio code met behulp van de nieuwe [uitbrei ding voor Azure Logic Apps Preview](../logic-apps/create-stateful-stateless-workflows-visual-studio-code.md).

  > [!NOTE]
  > Zorg ervoor dat u een integratie account gebruikt dat geschikt is voor uw use-case of-scenario. Zo zijn accounts met een [gratis laag](../logic-apps/logic-apps-pricing.md#integration-accounts) integratie alleen bedoeld voor verkennende scenario's en workloads, niet voor productie scenario's, zijn beperkt in het gebruik en de door Voer en worden niet ondersteund door een Sla (Service Level Agreement). Andere lagen nemen kosten in beslag, maar bevatten SLA-ondersteuning, bieden meer door Voer en hebben hogere limieten. Meer informatie over de integratie-en [prijs](https://azure.microsoft.com/pricing/details/logic-apps/) [Categorieën](../logic-apps/logic-apps-pricing.md#integration-accounts)en- [limieten](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="add-inline-code"></a>Inline code toevoegen

1. Als u dat nog niet hebt gedaan, opent u in de [Azure Portal](https://portal.azure.com)de logische app in de ontwerp functie voor logische apps.

1. Voeg in de ontwerp functie de **inline code** actie toe op de locatie die u wilt in de werk stroom van uw logische app.

   * Als u de actie aan het einde van uw werk stroom wilt toevoegen, kiest u **nieuwe stap**.

   * Als u de actie wilt toevoegen aan de hand van de bestaande stappen, plaatst u de muis aanwijzer op de pijl die de stappen verbindt. Kies het plus teken ( **+** ) en selecteer **een actie toevoegen**.

   In dit voor beeld wordt de **inline code** actie toegevoegd onder de Office 365 Outlook-trigger.

   ![Nieuwe stap toevoegen](./media/logic-apps-add-run-inline-code/add-new-step.png)

1. Voer onder **Kies een actie** in het zoekvak ' inline code ' in als uw filter. Selecteer in de lijst acties deze actie: **Java script-code uitvoeren**

   ![Selecteer Java script-code uitvoeren](./media/logic-apps-add-run-inline-code/select-inline-code-action.png)

   De actie wordt weer gegeven in de ontwerp functie en bevat een standaard voorbeeld code, inclusief een instructie return.

   ![Inline code actie met standaard voorbeeld code](./media/logic-apps-add-run-inline-code/inline-code-action-default.png)

1. Verwijder in het vak **code** de voorbeeld code en voer de code in die u wilt uitvoeren. Schrijf code die u in een methode zou plaatsen, maar zonder de methode handtekening te definiëren.

   Wanneer u een herkend tref woord typt, wordt de lijst automatisch aanvullen weer gegeven, zodat u kunt kiezen uit beschik bare tref woorden, bijvoorbeeld:

   ![Lijst met tref woorden voor automatisch aanvullen](./media/logic-apps-add-run-inline-code/auto-complete.png)

   In dit voorbeeld code fragment wordt eerst een variabele gemaakt waarin een *reguliere expressie* wordt opgeslagen. Dit is een patroon dat overeenkomt met de invoer tekst. De code maakt vervolgens een variabele waarin de gegevens van de e-mail tekst van de trigger worden opgeslagen.

   ![Variabelen maken](./media/logic-apps-add-run-inline-code/save-email-body-variable.png)

   De lijst met dynamische inhoud wordt weer gegeven wanneer de cursor zich in het vak **code** bevindt, zodat de resultaten van de trigger en eerdere acties gemakkelijker te verwijzen zijn. In dit voor beeld toont de lijst beschik bare resultaten van de trigger, met inbegrip van het **hoofdtekst** token, dat u nu kunt selecteren.

   Nadat u het **hoofdtekst** token hebt geselecteerd, wordt met de actie inline code het token omgezet in een `workflowContext` object dat verwijst naar de eigenschaps waarde van het e-mail bericht `Body` :

   ![Resultaat selecteren](./media/logic-apps-add-run-inline-code/inline-code-example-select-outputs.png)

   In het vak **code** kan het fragment het object alleen-lezen gebruiken `workflowContext` als invoer. Dit object heeft subeigenschappen die uw code toegang geven tot de resultaten van de trigger en eerdere acties in uw werk stroom. Zie deze sectie verderop in dit onderwerp voor meer informatie: [verwijzings trigger en actie resulteert in uw code](#workflowcontext).

   > [!NOTE]
   >
   > Als uw code fragment verwijst naar actie namen die gebruikmaken van de punt operator (.), moet u deze actie namen toevoegen aan de [para meter **Actions**](#add-parameters). Deze verwijzingen moeten ook de actie namen tussen vier Kante haken ([]) en aanhalings tekens bevatten, bijvoorbeeld:
   >
   > `// Correct`</br> 
   > `workflowContext.actions["my.action.name"].body`</br>
   >
   > `// Incorrect`</br>
   > `workflowContext.actions.my.action.name.body`

   Voor de inline code actie is geen `return` instructie vereist, maar de resultaten van een `return` instructie zijn beschikbaar voor referentie in latere acties via het **resultaat** token. Het code fragment retourneert bijvoorbeeld het resultaat door de `match()` functie aan te roepen, waarmee overeenkomende waarden worden gevonden in de hoofd tekst van de e-mail en de reguliere expressie. De actie **opstellen** maakt gebruik van het **resultaat** token om te verwijzen naar de resultaten van de inline code actie en maakt één resultaat.

   ![Voltooide logische app](./media/logic-apps-add-run-inline-code/inline-code-complete-example.png)

1. Wanneer u klaar bent, slaat u de logische app op.

<a name="workflowcontext"></a>

### <a name="reference-trigger-and-action-results-in-your-code"></a>Verwijzings trigger en actie resultaten in uw code

Het `workflowContext` object heeft deze structuur, die de `actions` `trigger` subeigenschappen, bevat `workflow` :

```json
{
   "workflowContext": {
      "actions": {
         "<action-name-1>": @actions('<action-name-1>'),
         "<action-name-2>": @actions('<action-name-2>')
      },
      "trigger": {
         @trigger()
      },
      "workflow": {
         @workflow()
      }
   }
}
```

Deze tabel bevat meer informatie over deze subeigenschappen:

| Eigenschap | Type | Beschrijving |
|----------|------|-------|
| `actions` | Object verzameling | Resultaat objecten van acties die worden uitgevoerd voordat uw code fragment wordt uitgevoerd. Elk object heeft een *sleutel/waarde-* paar waarbij de sleutel de naam van een actie is en de waarde komt overeen met het aanroepen van de [functie Actions ()](../logic-apps/workflow-definition-language-functions-reference.md#actions) met `@actions('<action-name>')` . De naam van de actie gebruikt dezelfde actie naam die wordt gebruikt in de onderliggende werk stroom definitie, waardoor spaties ("") in de naam van de actie worden vervangen door onderstrepings tekens (_). Dit object biedt toegang tot actie-eigenschaps waarden van het huidige werk stroom exemplaar dat wordt uitgevoerd. |
| `trigger` | Object | Resultaat object van de trigger en komt overeen met het aanroepen van de [trigger ()-functie](../logic-apps/workflow-definition-language-functions-reference.md#trigger). Dit object biedt toegang tot trigger eigenschaps waarden van het huidige werk stroom exemplaar dat wordt uitgevoerd. |
| `workflow` | Object | Het werk stroom object en gelijkwaardig aan het aanroepen van de [functie workflow ()](../logic-apps/workflow-definition-language-functions-reference.md#workflow). Dit object biedt toegang tot waarden van werk stroom-eigenschappen, zoals de werk stroom naam, run-ID, enzovoort, van het huidige werk stroom exemplaar dat wordt uitgevoerd. |
|||

In het voor beeld van dit onderwerp `workflowContext` heeft het object de volgende eigenschappen die de code kan openen:

```json
{
   "workflowContext": {
      "trigger": {
         "name": "When_a_new_email_arrives",
         "inputs": {
            "host": {
               "connection": {
                  "name": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/connections/office365"
               }
            },
            "method": "get",
            "path": "/Mail/OnNewEmail",
            "queries": {
               "includeAttachments": "False"
            }
         },
         "outputs": {
            "headers": {
               "Pragma": "no-cache",
               "Content-Type": "application/json; charset=utf-8",
               "Expires": "-1",
               "Content-Length": "962095"
            },
            "body": {
               "Id": "AAMkADY0NGZhNjdhLTRmZTQtNGFhOC1iYjFlLTk0MjZlZjczMWRhNgBGAAAAAABmZwxUQtCGTqSPpjjMQeD",
               "DateTimeReceived": "2019-03-28T19:42:16+00:00",
               "HasAttachment": false,
               "Subject": "Hello World",
               "BodyPreview": "Hello World",
               "Importance": 1,
               "ConversationId": "AAQkADY0NGZhNjdhLTRmZTQtNGFhOC1iYjFlLTk0MjZlZjczMWRhNgAQ",
               "IsRead": false,
               "IsHtml": true,
               "Body": "Hello World",
               "From": "<sender>@<domain>.com",
               "To": "<recipient-2>@<domain>.com;<recipient-2>@<domain>.com",
               "Cc": null,
               "Bcc": null,
               "Attachments": []
            }
         },
         "startTime": "2019-05-03T14:30:45.971564Z",
         "endTime": "2019-05-03T14:30:50.1746874Z",
         "scheduledTime": "2019-05-03T14:30:45.8778117Z",
         "trackingId": "1cd5ffbd-f989-4df5-a96a-6e9ce31d03c5",
         "clientTrackingId": "08586447130394969981639729333CU06",
         "originHistoryName": "08586447130394969981639729333CU06",
         "code": "OK",
         "status": "Succeeded"
      },
      "workflow": {
         "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<logic-app-workflow-name>",
         "name": "<logic-app-workflow-name>",
         "type": "Microsoft.Logic/workflows",
         "location": "<Azure-region>",
         "run": {
            "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<logic-app-workflow-name>/runs/08586453954668694173655267965CU00",
            "name": "08586453954668694173655267965CU00",
            "type": "Microsoft.Logic/workflows/runs"
         }
      }
   }
}
```

<a name="add-parameters"></a>

## <a name="add-parameters"></a>Parameters toevoegen

In sommige gevallen moet u er wellicht expliciet voor zorgen dat de **inline code** actie resultaten van de trigger of specifieke acties bevat die door uw code worden verwezen als afhankelijkheden door de para meters voor **trigger** of **acties** toe te voegen. Deze optie is handig voor scenario's waarbij de resultaten niet worden gevonden tijdens de uitvoering.

> [!TIP]
> Als u van plan bent om de code opnieuw te gebruiken, voegt u verwijzingen naar eigenschappen toe met behulp van het vak **code** , zodat uw code de omgezette token verwijzingen bevat, in plaats van de trigger of acties als expliciete afhankelijkheden toe te voegen.

Stel dat u code hebt die verwijst naar het **token** resultaat van de actie **e-mail goed keuren verzenden** voor de Office 365 Outlook-Connector. Tijdens het maken analyseert de Logic Apps Engine uw code om te bepalen of er al dan niet naar een trigger of actie resultaat wordt verwezen en dat deze resultaten automatisch worden opgenomen. Bij de uitvoering moet u een fout melding krijgen dat de trigger of actie resultaat waarnaar wordt verwezen, niet beschikbaar is in het opgegeven `workflowContext` object. u kunt deze trigger of actie als een expliciete afhankelijkheid toevoegen. In dit voor beeld voegt u de para meter **acties** toe en geeft u op dat de **inline code** actie expliciet het resultaat van de actie **E-mail voor goed keuring verzenden** bevat.

Als u deze para meters wilt toevoegen, opent u de lijst **nieuwe para meter toevoegen** en selecteert u de gewenste para meters:

   ![Parameters toevoegen](./media/logic-apps-add-run-inline-code/inline-code-action-add-parameters.png)

   | Parameter | Beschrijving |
   |-----------|-------------|
   | **Acties** | Resultaten van vorige acties toevoegen. Zie [actie resultaten toevoegen](#action-results). |
   | **Trigger** | Resultaten van de trigger bevatten. Zie [trigger resultaten toevoegen](#trigger-results). |
   |||

<a name="trigger-results"></a>

### <a name="include-trigger-results"></a>Trigger resultaten toevoegen

Als u **Triggers** selecteert, wordt u gevraagd of u trigger resultaten wilt toevoegen.

* Selecteer in de lijst **trigger** de optie **Ja**.

<a name="action-results"></a>

### <a name="include-action-results"></a>Actie resultaten toevoegen

Als u **acties** selecteert, wordt u gevraagd naar de acties die u wilt toevoegen. Voordat u begint met het toevoegen van acties, hebt u echter de versie van de actie naam nodig die wordt weer gegeven in de onderliggende werk stroom definitie van de logische app.

* Deze functie biedt geen ondersteuning voor variabelen, lussen en iteratie indexen.

* Namen in de werk stroom definitie van de logische app gebruiken een onderstrepings teken (_), geen spatie.

* Voor actie namen die gebruikmaken van de punt operator (.), neemt u deze opera tors op, bijvoorbeeld:

  `My.Action.Name`

1. Kies op de werk balk ontwerpen de optie **code weergave** en zoek in het- `actions` kenmerk naar de naam van de actie.

   `Send_approval_email_`Is bijvoorbeeld de JSON-naam voor de actie **e-mail goed keuren verzenden** .

   ![Actie naam in JSON zoeken](./media/logic-apps-add-run-inline-code/find-action-name-json.png)

1. Als u wilt terugkeren naar de ontwerp weergave, kiest u op de werk balk van de code weergave de optie **ontwerper**.

1. Als u de eerste actie wilt toevoegen, voert u in het vak **actie-item-1** de JSON-naam van de actie in.

   ![Eerste actie invoeren](./media/logic-apps-add-run-inline-code/add-action-parameter.png)

1. Kies **Nieuw item toevoegen** om een andere actie toe te voegen.

## <a name="reference"></a>Verwijzing

Voor meer informatie over de structuur en syntaxis van de actie **Java script-code uitvoeren** in de onderliggende werk stroom definitie van uw logische app met de werk stroom definitie taal, zie de [sectie referentie](../logic-apps/logic-apps-workflow-actions-triggers.md#run-javascript-code)van deze actie.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [connectors voor Azure Logic apps](../connectors/apis-list.md)

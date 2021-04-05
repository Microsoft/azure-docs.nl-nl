---
title: Schema verwijzing werk stroom definitie taal
description: Naslag Gids voor het JSON-schema en de syntaxis voor de werk stroom definitie taal waarmee werk stromen in Azure Logic Apps worden beschreven
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 71929cd449f4a00b91cc6c8620b33b0e0c6d506c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87078136"
---
# <a name="schema-reference-guide-for-the-workflow-definition-language-in-azure-logic-apps"></a>Naslag Gids voor schema's voor de taal van de werk stroom definitie in Azure Logic Apps

Wanneer u een logische app in [Azure Logic apps](../logic-apps/logic-apps-overview.md)maakt, heeft uw logische app een onderliggende werk stroom definitie die de daad werkelijke logica beschrijft die in uw logische app wordt uitgevoerd. Deze werk stroom definitie maakt gebruik van [JSON](https://www.json.org/) en volgt een structuur die wordt gevalideerd door het taal schema voor de werk stroom definitie. Deze Naslag informatie bevat een overzicht van deze structuur en hoe het schema kenmerken in uw workflowdefinitie definieert.

## <a name="workflow-definition-structure"></a>Structuur van werk stroom definitie

Een definitie van een werk stroom bevat altijd een trigger voor het instantiëren van uw logische app, plus een of meer acties die worden uitgevoerd nadat de trigger wordt geactiveerd.

Dit is de structuur op hoog niveau voor een werk stroom definitie:

```json
"definition": {
  "$schema": "<workflow-definition-language-schema-version>",
  "actions": { "<workflow-action-definitions>" },
  "contentVersion": "<workflow-definition-version-number>",
  "outputs": { "<workflow-output-definitions>" },
  "parameters": { "<workflow-parameter-definitions>" },
  "staticResults": { "<static-results-definitions>" },
  "triggers": { "<workflow-trigger-definitions>" }
}
```

| Kenmerk | Vereist | Beschrijving |
|-----------|----------|-------------|
| `definition` | Ja | Het begin element voor uw werk stroom definitie |
| `$schema` | Alleen wanneer extern naar een werk stroom definitie verwijst | De locatie voor het JSON-schema bestand met een beschrijving van de versie van de werk stroom definitie taal, die u hier kunt vinden: <p>`https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json`</p> |
| `actions` | No | De definities voor een of meer acties die tijdens de workflowruntime worden uitgevoerd. Zie [Triggers en acties](#triggers-actions)voor meer informatie. <p><p>Maximum aantal acties: 250 |
| `contentVersion` | No | Het versie nummer voor de werk stroom definitie, die standaard de waarde 1.0.0.0 heeft. Geef een waarde op om te gebruiken voor het identificeren en bevestigen van de juiste definitie bij het implementeren van een werk stroom. |
| `outputs` | No | De definities voor de uitvoer die moeten worden geretourneerd door de uitvoering van een werk stroom. Zie [uitvoer](#outputs)voor meer informatie. <p><p>Maximale uitvoer: 10 |
| `parameters` | No | De definities voor een of meer para meters die de waarden door geven die moeten worden gebruikt tijdens de runtime van de logische app. Zie [para meters](#parameters)voor meer informatie. <p><p>Maximum aantal para meters: 50 |
| `staticResults` | No | De definities voor een of meer statische resultaten die door acties worden geretourneerd als het model van de uitvoer wanneer statische resultaten op deze acties zijn ingeschakeld. In elke actie definitie verwijst het `runtimeConfiguration.staticResult.name` kenmerk naar de bijbehorende definitie in `staticResults` . Zie [static results (statische resultaten](#static-results)) voor meer informatie. |
| `triggers` | No | De definities voor een of meer triggers die uw werk stroom instantiëren. U kunt meer dan één trigger definiëren, maar alleen met de taal van de werk stroom definitie, niet visueel door de Logic Apps Designer. Zie [Triggers en acties](#triggers-actions)voor meer informatie. <p><p>Maximum aantal triggers: 10 |
||||

<a name="triggers-actions"></a>

## <a name="triggers-and-actions"></a>Triggers en acties

In een werk stroom definitie `triggers` definiëren de en- `actions` secties de aanroepen die tijdens de uitvoering van de werk stroom plaatsvinden. Zie [werk stroom triggers en acties](../logic-apps/logic-apps-workflow-actions-triggers.md)voor een syntaxis en meer informatie over deze secties.

<a name="parameters"></a>

## <a name="parameters"></a>Parameters

De levens cyclus van de implementatie heeft doorgaans verschillende omgevingen voor ontwikkeling, testen, faseren en productie. Wanneer u logische apps implementeert in verschillende omgevingen, wilt u waarschijnlijk verschillende waarden gebruiken, zoals verbindings reeksen, op basis van uw implementatie behoeften. Het kan ook zijn dat u waarden wilt gebruiken die u in de logische app wilt hergebruiken zonder hardcoding of die regel matig veranderen. In de sectie werk stroom definitie `parameters` kunt u para meters definiëren of bewerken voor de waarden die uw logische app gebruikt tijdens runtime. U moet deze para meters eerst definiëren voordat u deze para meters ergens anders in uw werk stroom definitie kunt raadplegen.

Hier volgt de algemene structuur voor een parameter definitie:

```json
"parameters": {
   "<parameter-name>": {
      "type": "<parameter-type>",
      "defaultValue": <default-parameter-value>,
      "allowedValues": [ <array-with-permitted-parameter-values> ],
      "metadata": {
         "description": "<parameter-description>"
      }
   }
},
```

| Kenmerk | Vereist | Type | Description |
|-----------|----------|------|-------------|
| <*para meter-naam*> | Ja | Tekenreeks | De naam voor de para meter die u wilt definiëren |
| <*parameter type*> | Yes | int, float, String, BOOL, array, object, securestring, secureobject <p><p>**Opmerking**: gebruik de typen of voor alle wacht woorden, sleutels en geheimen, `securestring` `secureobject` omdat de `GET` bewerking deze typen niet retourneert. Zie [beveiligings aanbevelingen voor actie-en invoer parameters](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)voor meer informatie over het beveiligen van para meters. | Het type voor de para meter |
| <*default-para meter-value*> | Yes | Hetzelfde als `type` | De standaard parameter waarde die moet worden gebruikt als er geen waarde wordt opgegeven bij het instantiëren van de werk stroom. Het `defaultValue` kenmerk is vereist zodat de Logic app Designer de para meter correct kan weer geven, maar u kunt een lege waarde opgeven. |
| <*matrix-met-toegestane-para meter-waarden*> | No | Matrix | Een matrix met waarden die door de para meter kunnen worden geaccepteerd |
| <*para meter-beschrijving*> | No | JSON-object | Eventuele andere parameter Details, zoals een beschrijving van de para meter |
||||

Maak vervolgens een [Azure Resource Manager sjabloon](../azure-resource-manager/templates/overview.md) voor de definitie van uw werk stroom, definieer sjabloon parameters die de waarden accepteren die u tijdens de implementatie wilt gebruiken, vervang hardcoded-waarden door verwijzingen naar sjabloon-of werk stroom definitie parameters en sla de waarden op die moeten worden gebruikt bij de implementatie in een afzonderlijk [parameter bestand](../azure-resource-manager/templates/parameter-files.md). Op die manier kunt u deze waarden gemakkelijker wijzigen via het parameter bestand zonder dat u uw logische app hoeft bij te werken en opnieuw te implementeren. Voor informatie die gevoelig is of die moet worden beveiligd, zoals gebruikers namen, wacht woorden en geheimen, kunt u deze waarden opslaan in Azure Key Vault en uw parameter bestand de waarden laten ophalen uit uw sleutel kluis. Zie [overzicht: implementatie voor Logic apps automatiseren met Azure Resource Manager sjablonen](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)voor meer informatie en voor beelden over het definiëren van para meters in de sjabloon en de definities van werk stromen.

<a name="static-results"></a>

## <a name="static-results"></a>Statische resultaten

Definieer in het `staticResults` -kenmerk een actie model `outputs` en `status` dat de actie retourneert wanneer de statische resultaat instelling van de actie is ingeschakeld. In de definitie van de actie `runtimeConfiguration.staticResult.name` verwijst het kenmerk naar de naam van de statische resultaat definitie in `staticResults` . Meer informatie over hoe u [logische apps kunt testen met gegevens over modellen door statische resultaten in te stellen](../logic-apps/test-logic-apps-mock-data-static-results.md).

```json
"definition": {
   "$schema": "<...>",
   "actions": { "<...>" },
   "contentVersion": "<...>",
   "outputs": { "<...>" },
   "parameters": { "<...>" },
   "staticResults": {
      "<static-result-definition-name>": {
         "outputs": {
            <output-attributes-and-values-returned>,
            "headers": { <header-values> },
            "statusCode": "<status-code-returned>"
         },
         "status": "<action-status>"
      }
   },
   "triggers": { "<...>" }
}
```

| Kenmerk | Vereist | Type | Description |
|-----------|----------|------|-------------|
| <*statisch resultaten-definitie naam*> | Ja | Tekenreeks | De naam voor een statische resultaat definitie waarmee een actie definitie kan verwijzen naar een `runtimeConfiguration.staticResult` object. Zie runtime-configuratie- [instellingen](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-config-options)voor meer informatie. <p>U kunt elke gewenste unieke naam gebruiken. Deze unieke naam wordt standaard toegevoegd met een nummer, dat zo nodig wordt verhoogd. |
| <*uitvoer kenmerken-en-waarden-geretourneerd*> | Yes | Varieert | De vereisten voor deze kenmerken variëren op basis van verschillende voor waarden. Bijvoorbeeld, wanneer het `status` is `Succeeded` , bevat het `outputs` kenmerk kenmerken en waarden die worden geretourneerd als de uitvoer van de bewerking. Als dat het geval `status` is `Failed` , `outputs` bevat het kenmerk het `errors` kenmerk, een matrix met een of meer fout `message` objecten met fout gegevens. |
| <*header-waarden*> | Nee | JSON | Eventuele header waarden die zijn geretourneerd door de actie |
| <*status-code-geretourneerd*> | Ja | Tekenreeks | De status code die wordt geretourneerd door de actie |
| <*actie-status*> | Ja | Tekenreeks | De status van de actie, bijvoorbeeld `Succeeded` of `Failed` |
|||||

In deze HTTP-actie definitie wordt bijvoorbeeld het `runtimeConfiguration.staticResult.name` kenmerk verwezen naar `HTTP0` het `staticResults` kenmerk waarin de uitvoer van de model voor de actie wordt gedefinieerd. Het `runtimeConfiguration.staticResult.staticResultOptions` kenmerk geeft aan dat de statische resultaat instelling is ingesteld `Enabled` op de http-actie.

```json
"actions": {
   "HTTP": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.microsoft.com"
      },
      "runAfter": {},
      "runtimeConfiguration": {
         "staticResult": {
            "name": "HTTP0",
            "staticResultOptions": "Enabled"
         }
      },
      "type": "Http"
   }
},
```

De HTTP-actie retourneert de uitvoer in de `HTTP0` definitie in `staticResults` . In dit voor beeld is voor de status code de uitvoer van de Modeler `OK` . Voor header waarden is de uitvoer van de Detrek `"Content-Type": "application/JSON"` . Voor de status van de actie is de uitvoer van de activiteit `Succeeded` .

```json
"definition": {
   "$schema": "<...>",
   "actions": { "<...>" },
   "contentVersion": "<...>",
   "outputs": { "<...>" },
   "parameters": { "<...>" },
   "staticResults": {
      "HTTP0": {
         "outputs": {
            "headers": {
               "Content-Type": "application/JSON"
            },
            "statusCode": "OK"
         },
         "status": "Succeeded"
      }
   },
   "triggers": { "<...>" }
},
```

<a name="expressions"></a>

## <a name="expressions"></a>Expressies

Met JSON kunt u letterlijke waarden hebben die tijdens de ontwerp fase bestaan, bijvoorbeeld:

```json
"customerName": "Sophia Owen",
"rainbowColors": ["red", "orange", "yellow", "green", "blue", "indigo", "violet"],
"rainbowColorsCount": 7
```

U kunt ook waarden hebben die niet bestaan tot de uitvoerings tijd. Om deze waarden weer te geven, kunt u *expressies* gebruiken die tijdens runtime worden geëvalueerd. Een expressie is een reeks die een of meer [functies](#functions), [Opera tors](#operators), [variabelen](./logic-apps-create-variables-store-values.md), expliciete waarden of constanten kan bevatten. In de definitie van uw werk stroom kunt u een expressie overal in een JSON-teken reeks waarde gebruiken door de expressie te voorzien van het plus teken ( \@ ). Bij het evalueren van een expressie die een JSON-waarde vertegenwoordigt, wordt de hoofd tekst van de expressie geëxtraheerd door het \@ teken te verwijderen en wordt altijd een andere JSON-waarde geretourneerd.

Voor de eerder gedefinieerde `customerName` eigenschap kunt u bijvoorbeeld de waarde van de eigenschap ophalen met behulp van de functie [para meters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) in een expressie en die waarde toewijzen aan de `accountName` eigenschap:

```json
"customerName": "Sophia Owen",
"accountName": "@parameters('customerName')"
```

Met de *interpolatie van teken* reeksen kunt u ook meerdere expressies gebruiken in teken reeksen die worden verpakt door het teken en accolades \@ ( {} ). Dit is de syntaxis:

```json
@{ "<expression1>", "<expression2>" }
```

Het resultaat is altijd een teken reeks, waardoor deze functionaliteit vergelijkbaar is met de `concat()` functie, bijvoorbeeld: 

```json
"customerName": "First name: @{parameters('firstName')} Last name: @{parameters('lastName')}"
```

Als u een letterlijke teken reeks hebt die begint met het teken, plaatst u een \@ voor voegsel \@ van het teken met een ander \@ teken als escape-teken: \@\@

In deze voor beelden ziet u hoe expressies worden geëvalueerd:

| JSON-waarde | Resultaat |
|------------|--------|
| "Sophia Owen" | Deze tekens retour neren: ' Sophia Owen ' |
| matrix [1] | Deze tekens retour neren: matrix [1] |
| "\@\@" | Deze tekens retour neren als een teken reeks van één teken: \@ |
| " \@" | Deze tekens retour neren als een teken reeks van twee tekens: ' \@ ' |
|||

Voor deze voor beelden definieert u "myBirthMonth" gelijk aan "januari" en "myAge" gelijk aan het getal 42:

```json
"myBirthMonth": "January",
"myAge": 42
```

In deze voor beelden ziet u hoe de volgende expressies worden geëvalueerd:

| JSON-expressie | Resultaat |
|-----------------|--------|
| " \@ para meters (' myBirthMonth ')" | Deze teken reeks retour neren: ' januari ' |
| " \@ {para meters (' myBirthMonth ')}" | Deze teken reeks retour neren: ' januari ' |
| " \@ para meters (' myAge ')" | Retour waarde: 42 |
| " \@ {para meters (' myAge ')}" | Dit getal als een teken reeks retour neren: "42" |
| ' Mijn leeftijd is \@ {para meters (' myAge ')} ' | Deze teken reeks retour neren: ' mijn leeftijd is 42 ' |
| " \@ concat (' mijn leeftijd is ', String (para meters (' myAge ')))" | Deze teken reeks retour neren: ' mijn leeftijd is 42 ' |
| ' Mijn leeftijd is \@ \@ {para meters (' myAge ')} ' | Retour neer deze teken reeks, die de volgende expressie bevat: ' mijn leeftijd is \@ {para meters (' myAge ')} ' |
|||

Wanneer u visueel met de Logic Apps Designer werkt, kunt u expressies maken via de opbouw functie van de expressie, bijvoorbeeld:

![Opbouw functie voor Logic Apps Designer > Expression](./media/logic-apps-workflow-definition-language/expression-builder.png)

Wanneer u klaar bent, wordt de expressie weer gegeven voor de bijbehorende eigenschap in de definitie van de werk stroom, bijvoorbeeld de `searchQuery` eigenschap:

```json
"Search_tweets": {
  "inputs": {
    "host": {
      "connection": {
        "name": "@parameters('$connections')['twitter']['connectionId']"
      }
    }
  },
  "method": "get",
  "path": "/searchtweets",
  "queries": {
    "maxResults": 20,
    "searchQuery": "Azure @{concat('firstName','', 'LastName')}"
  }
},
```

<a name="outputs"></a>

## <a name="outputs"></a>Uitvoerwaarden

In de `outputs` sectie definieert u de gegevens die uw werk stroom kan retour neren als de uitvoering is voltooid. Als u bijvoorbeeld een specifieke status of waarde van elke uitvoering wilt bijhouden, geeft u op dat de uitvoer van de werk stroom die gegevens retourneert.

> [!NOTE]
> Gebruik niet voor het beantwoorden van binnenkomende aanvragen van de REST API van een service `outputs` . Gebruik in plaats daarvan het `Response` actie type. Zie [werk stroom triggers en acties](../logic-apps/logic-apps-workflow-actions-triggers.md)voor meer informatie.

Hier volgt de algemene structuur voor een uitvoer definitie:

```json
"outputs": {
  "<key-name>": {
    "type": "<key-type>",
    "value": "<key-value>"
  }
}
```

| Kenmerk | Vereist | Type | Description |
|-----------|----------|------|-------------|
| <*sleutel naam*> | Ja | Tekenreeks | De naam van de sleutel voor de retour waarde van de uitvoer |
| <*sleutel type*> | Yes | int, float, String, securestring, BOOL, array, JSON-object | Het type voor de uitvoer retour waarde |
| <*sleutel waarde*> | Yes | Hetzelfde als <*sleutel type*> | De resultaat waarde van de uitvoer |
|||||

Als u de uitvoer van een werk stroom wilt uitvoeren, controleert u de uitvoerings geschiedenis van de logische app en de details in de Azure Portal of gebruikt u de [werk stroom rest API](/rest/api/logic/workflows). U kunt ook de uitvoer door geven aan externe systemen, bijvoorbeeld Power BI, zodat u Dash boards kunt maken.

<a name="operators"></a>

## <a name="operators"></a>Operators

In [expressies](#expressions) en [functies](#functions)voeren Opera tors specifieke taken uit, zoals verwijzen naar een eigenschap of een waarde in een matrix.

| Operator | Taak |
|----------|------|
| ' | Als u een letterlijke teken reeks wilt gebruiken als invoer of in expressies en functies, plaatst u de teken reeks alleen met enkele aanhalings tekens, bijvoorbeeld `'<myString>'` . Gebruik geen dubbele aanhalings tekens (""), die conflicteren met de JSON-indeling rond een volledige expressie. Bijvoorbeeld: <p>**Ja**: lengte (' Hallo ') </br>**Nee**: lengte ("Hallo") <p>Wanneer u matrices of getallen doorgeeft, hoeft u geen interpunctie in te laten teruglopen. Bijvoorbeeld: <p>**Ja**: lengte ([1, 2, 3]) </br>**Nee**: lengte ("[1, 2, 3]") |
| [] | Als u wilt verwijzen naar een waarde op een specifieke positie (index) in een matrix, gebruikt u vier Kante haken. Als u bijvoorbeeld het tweede item in een matrix wilt ophalen: <p>`myArray[1]` |
| . | Als u wilt verwijzen naar een eigenschap in een object, gebruikt u de punt operator. Als u bijvoorbeeld de `name` eigenschap voor een JSON- `customer` object wilt ophalen: <p>`"@parameters('customer').name"` |
| ? | Als u wilt verwijzen naar null-eigenschappen in een object zonder een runtime-fout, gebruikt u de operator vraag teken. Als u bijvoorbeeld null-uitvoer van een trigger wilt verwerken, kunt u deze expressie gebruiken: <p>`@coalesce(trigger().outputs?.body?.<someProperty>, '<property-default-value>')` |
|||

<a name="functions"></a>

## <a name="functions"></a>Functions

Sommige expressies halen hun waarden uit runtime-acties die mogelijk nog niet bestaan wanneer de definitie van de werk stroom wordt gestart. Als u wilt verwijzen naar of wilt werken met deze waarden in expressies, kunt u [*functies*](../logic-apps/workflow-definition-language-functions-reference.md) gebruiken die door de werk stroom definitie taal zijn geleverd.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [acties en triggers voor de werk stroom definitie taal](../logic-apps/logic-apps-workflow-actions-triggers.md)
* Meer informatie over het programmatisch maken en beheren van logische apps met behulp van de [werk stroom rest API](/rest/api/logic/workflows)

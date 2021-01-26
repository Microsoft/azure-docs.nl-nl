---
title: Schema verwijzing voor de triggers-en actie typen
description: Naslag Gids voor schema's voor de trigger van de werk stroom definitie taal en actie typen in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 09/22/2020
ms.custom: devx-track-js
ms.openlocfilehash: ea4a4a47e91e88c00ca8a4e886d0372a24482907
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98784305"
---
# <a name="schema-reference-guide-for-trigger-and-action-types-in-azure-logic-apps"></a>Naslag Gids voor schema's voor trigger-en actie typen in Azure Logic Apps

Deze verwijzing beschrijft de algemene typen die worden gebruikt voor het identificeren van triggers en acties in de onderliggende werk stroom definitie van uw logische app, die wordt beschreven en gevalideerd door de taal van de [werk stroom definitie](../logic-apps/logic-apps-workflow-definition-language.md). Zie de lijst onder het [overzicht connectors](/connectors/)voor specifieke connector triggers en acties die u kunt gebruiken in uw Logic apps.

<a name="triggers-overview"></a>

## <a name="triggers-overview"></a>Overzicht van triggers

Elke werk stroom bevat een trigger, waarmee de aanroepen worden gedefinieerd waarmee de werk stroom wordt geïnstantieerd en gestart. Dit zijn de algemene trigger Categorieën:

* Een *polling* trigger, waarmee het eind punt van een service op regel matige intervallen wordt gecontroleerd

* Een *Push* trigger die een abonnement op een eind punt maakt en een *call back-URL* biedt zodat het eind punt de trigger kan waarschuwen wanneer de opgegeven gebeurtenis plaatsvindt of gegevens beschikbaar zijn. De trigger wacht op het antwoord van het eind punt voordat deze wordt geactiveerd.

Triggers hebben deze elementen op het hoogste niveau, maar sommige zijn optioneel:  
  
```json
"<trigger-name>": {
   "type": "<trigger-type>",
   "inputs": { "<trigger-inputs>" },
   "recurrence": { 
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>
   },
   "conditions": [ "<array-with-conditions>" ],
   "runtimeConfiguration": { "<runtime-config-options>" },
   "splitOn": "<splitOn-expression>",
   "operationOptions": "<operation-option>"
},
```

*Vereist*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*trigger naam*> | Tekenreeks | De naam voor de trigger | 
| <*trigger-type*> | Tekenreeks | Het trigger type zoals ' http ' of ' ApiConnection ' | 
| <*trigger-invoer*> | JSON-object | De invoer waarmee het gedrag van de trigger wordt gedefinieerd | 
| <*tijds eenheid*> | Tekenreeks | De tijds eenheid die aangeeft hoe vaak de trigger wordt geactiveerd: ' Second ', ' minuut ', ' uur ', ' dag ', ' week ', ' maand ' | 
| <*aantal-tijds eenheden*> | Geheel getal | Een waarde die aangeeft hoe vaak de trigger wordt geactiveerd op basis van de frequentie. Dit is het aantal tijds eenheden dat moet worden gewacht totdat de trigger opnieuw wordt geactiveerd. <p>Dit zijn de minimale en maximale intervallen: <p>-Maand: 1-16 maanden </br>-Dag: 1-500 dagen </br>-Uur: 1-12000 uur </br>-Minuut: 1-72000 minuten </br>-Seconde: 1-9999999 seconden<p>Als het interval bijvoorbeeld 6 is en de frequentie "month" is, is het terugkeer patroon elke 6 maanden. | 
|||| 

*Optioneel*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*matrix-met-voor waarden*> | Matrix | Een matrix die een of meer [voor waarden](#trigger-conditions) bevat die bepalen of de werk stroom moet worden uitgevoerd. Alleen beschikbaar voor triggers. | 
| <*runtime-configuratie-opties*> | JSON-object | U kunt gedrag van trigger runtime wijzigen door eigenschappen in te stellen `runtimeConfiguration` . Zie runtime-configuratie- [instellingen](#runtime-config-options)voor meer informatie. | 
| <*splitOn-expressie*> | Tekenreeks | Voor triggers die een matrix retour neren, kunt u een expressie opgeven waarmee matrix items in meerdere workflowexemplaren voor verwerking worden [gesplitst of *gebatcheerd*](#split-on-debatch) . | 
| <*bewerking-optie*> | Tekenreeks | U kunt het standaard gedrag wijzigen door de eigenschap in te stellen `operationOptions` . Zie [bewerkings opties](#operation-options)voor meer informatie. | 
|||| 

## <a name="trigger-types-list"></a>Lijst met trigger typen

Elk trigger type heeft een andere interface en ingangen waarmee het gedrag van de trigger wordt gedefinieerd. 

### <a name="built-in-triggers"></a>Ingebouwde triggers

| Triggertype | Beschrijving | 
|--------------|-------------| 
| [**HTTP**](#http-trigger) | Hiermee wordt een wille keurig eind punt gecontroleerd of *gepolld* . Dit eind punt moet voldoen aan een specifiek trigger contract, hetzij met behulp van een `202` asynchroon patroon, hetzij door een matrix te retour neren. | 
| [**HTTPWebhook**](#http-webhook-trigger) | Hiermee maakt u een aanroepbaar eind punt voor uw logische app, maar roept u de opgegeven URL op om te registreren of de registratie ervan ongedaan te maken. |
| [**Terugkeerpatroon**](#recurrence-trigger) | Wordt geactiveerd op basis van een gedefinieerd schema. U kunt een datum en tijd voor het starten van deze trigger instellen. Op basis van de frequentie kunt u ook tijden en dagen opgeven voor het uitvoeren van uw werk stroom. | 
| [**Aanvraag**](#request-trigger)  | Hiermee maakt u een aanroepbaar eind punt voor uw logische app. dit wordt ook wel een ' hand matige ' trigger genoemd. Zie bijvoorbeeld [werk stromen aanroepen, activeren of nesten met HTTP-eind punten](../logic-apps/logic-apps-http-endpoint.md). | 
||| 

### <a name="managed-api-triggers"></a>Beheerde API-triggers

| Triggertype | Beschrijving | 
|--------------|-------------| 
| [**ApiConnection**](#apiconnection-trigger) | Controleert of *pollt* een eind punt met behulp van door [micro soft beheerde api's](../connectors/apis-list.md). | 
| [**ApiConnectionWebhook**](#apiconnectionwebhook-trigger) | Hiermee maakt u een aanroepbaar eind punt voor uw logische app door door [micro soft beheerde api's](../connectors/apis-list.md) aan te roepen om zich te abonneren en af te melden. | 
||| 

## <a name="triggers---detailed-reference"></a>Triggers - gedetailleerde naslaginformatie

<a name="apiconnection-trigger"></a>

### <a name="apiconnection-trigger"></a>Trigger voor APIConnection  

Deze trigger *controleert of* doorstuurt een eind punt met behulp van door [micro soft beheerde api's](../connectors/apis-list.md) , zodat de para meters voor deze trigger kunnen variëren op basis van het eind punt. Veel secties in deze trigger definitie zijn optioneel. Het gedrag van de trigger is afhankelijk van het feit of er secties zijn opgenomen.

```json
"<APIConnection_trigger_name>": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<connection-name>']['connectionId']"
         }
      },
      "method": "<method-type>",
      "path": "/<api-operation>",
      "retryPolicy": { "<retry-behavior>" },
      "queries": { "<query-parameters>" }
   },
   "recurrence": { 
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "splitOn": "<splitOn-expression>",
   "operationOptions": "<operation-option>"
}
```

*Vereist*

| Waarde | Type | Beschrijving |
|-------|------|-------------|
| <*APIConnection_trigger_name*> | Tekenreeks | De naam voor de trigger |
| <*verbindings naam*> | Tekenreeks | De naam voor de verbinding met de beheerde API die door de werk stroom wordt gebruikt |
| <*methode-type*> | Tekenreeks | De HTTP-methode voor communicatie met de beheerde API: ' GET ', ' PUT ', ' POST ', ' PATCH ', ' DELETE ' |
| <*API-bewerking*> | Tekenreeks | De API-bewerking die moet worden aangeroepen |
| <*tijds eenheid*> | Tekenreeks | De tijds eenheid die aangeeft hoe vaak de trigger wordt geactiveerd: ' Second ', ' minuut ', ' uur ', ' dag ', ' week ', ' maand ' |
| <*aantal-tijds eenheden*> | Geheel getal | Een waarde die aangeeft hoe vaak de trigger wordt geactiveerd op basis van de frequentie. Dit is het aantal tijds eenheden dat moet worden gewacht totdat de trigger opnieuw wordt geactiveerd. <p>Dit zijn de minimale en maximale intervallen: <p>-Maand: 1-16 maanden </br>-Dag: 1-500 dagen </br>-Uur: 1-12000 uur </br>-Minuut: 1-72000 minuten </br>-Seconde: 1-9999999 seconden<p>Als het interval bijvoorbeeld 6 is en de frequentie "month" is, is het terugkeer patroon elke 6 maanden. |
||||

*Optioneel*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*opnieuw proberen*> | JSON-object | Hiermee past u het gedrag voor opnieuw proberen aan voor periodieke storingen, die de status code 408, 429 en 5XX en eventuele connectiviteits uitzonderingen hebben. Zie [beleid voor opnieuw proberen](../logic-apps/logic-apps-exception-handling.md#retry-policies)voor meer informatie. | 
| <*query-para meters*> | JSON-object | Alle query parameters die moeten worden toegevoegd met de API-aanroep. Het `"queries": { "api-version": "2018-01-01" }` object wordt bijvoorbeeld toegevoegd `?api-version=2018-01-01` aan de aanroep. | 
| <*Max-uitvoeringen*> | Geheel getal | Standaard worden werk stroom exemplaren op hetzelfde moment uitgevoerd (gelijktijdig of parallel) tot de [standaard limiet](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Als u deze limiet wilt wijzigen door een nieuwe <*aantal*> waarde in te stellen, raadpleegt u de [gelijktijdigheid van triggers wijzigen](#change-trigger-concurrency). | 
| <*Max-uitvoeringen-wachtrij*> | Geheel getal | Als voor uw werk stroom al het maximum aantal exemplaren wordt uitgevoerd dat u op basis van de eigenschap kunt wijzigen `runtimeConfiguration.concurrency.runs` , worden nieuwe uitvoeringen in deze wachtrij geplaatst tot de [standaard limiet](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Zie de limiet voor het uitvoeren van een [wacht](#change-waiting-runs)tijd wijzigen om de standaard limiet te wijzigen. | 
| <*splitOn-expressie*> | Tekenreeks | Voor triggers die matrices retour neren, verwijst deze expressie naar de matrix die moet worden gebruikt, zodat u een werk stroom exemplaar voor elk matrix item kunt maken en uitvoeren, in plaats van een ' voor elke ' lus te gebruiken. <p>Deze expressie vertegenwoordigt bijvoorbeeld een item in de matrix die wordt geretourneerd in de hoofd inhoud van de trigger: `@triggerbody()?['value']` |
| <*bewerking-optie*> | Tekenreeks | U kunt het standaard gedrag wijzigen door de eigenschap in te stellen `operationOptions` . Zie [bewerkings opties](#operation-options)voor meer informatie. |
||||

*Uitvoerwaarden*
 
| Element | Type | Beschrijving |
|---------|------|-------------|
| koppen | JSON-object | De kopteksten van het antwoord |
| body | JSON-object | De hoofd tekst van de reactie |
| statuscode | Geheel getal | De status code van het antwoord |
|||| 

*Voorbeeld*

Deze trigger definitie controleert voor elke dag in het postvak in het e-mail adres voor een werk-of school account:

```json
"When_a_new_email_arrives": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "method": "get",
      "path": "/Mail/OnNewEmail",
      "queries": {
          "fetchOnlyWithAttachment": false,
          "folderPath": "Inbox",
          "importance": "Any",
          "includeAttachments": false
      }
   },
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

<a name="apiconnectionwebhook-trigger"></a>

### <a name="apiconnectionwebhook-trigger"></a>Trigger voor ApiConnectionWebhook

Deze trigger verzendt een abonnements aanvraag naar een eind punt met behulp van een door [micro soft beheerde API](../connectors/apis-list.md). Dit is een *call back-URL* waarnaar het eind punt een antwoord kan verzenden en wacht tot het eind punt reageert. Zie [endpoint-abonnementen](#subscribe-unsubscribe)voor meer informatie.

```json
"<ApiConnectionWebhook_trigger_name>": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "body": {
          "NotificationUrl": "@{listCallbackUrl()}"
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<connection-name>']['connectionId']"
         }
      },
      "retryPolicy": { "<retry-behavior>" },
      "queries": "<query-parameters>"
   },
   "runTimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-run-queue>
      }
   },
   "splitOn": "<splitOn-expression>",
   "operationOptions": "<operation-option>"
}
```

*Vereist*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*verbindings naam*> | Tekenreeks | De naam voor de verbinding met de beheerde API die door de werk stroom wordt gebruikt | 
| <*hoofd tekst: inhoud*> | JSON-object | Bericht inhoud die als Payload moet worden verzonden naar de beheerde API | 
||||

*Optioneel*

| Waarde | Type | Beschrijving |
|-------|------|-------------|
| <*opnieuw proberen*> | JSON-object | Hiermee past u het gedrag voor opnieuw proberen aan voor periodieke storingen, die de status code 408, 429 en 5XX en eventuele connectiviteits uitzonderingen hebben. Zie [beleid voor opnieuw proberen](../logic-apps/logic-apps-exception-handling.md#retry-policies)voor meer informatie. |
| <*query-para meters*> | JSON-object | Alle query parameters die moeten worden toegevoegd met de API-aanroep <p>Het `"queries": { "api-version": "2018-01-01" }` object wordt bijvoorbeeld toegevoegd `?api-version=2018-01-01` aan de aanroep. |
| <*Max-uitvoeringen*> | Geheel getal | Standaard worden werk stroom exemplaren op hetzelfde moment uitgevoerd (gelijktijdig of parallel) tot de [standaard limiet](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Als u deze limiet wilt wijzigen door een nieuwe <*aantal*> waarde in te stellen, raadpleegt u de [gelijktijdigheid van triggers wijzigen](#change-trigger-concurrency). |
| <*Max-uitvoeringen-wachtrij*> | Geheel getal | Als voor uw werk stroom al het maximum aantal exemplaren wordt uitgevoerd dat u op basis van de eigenschap kunt wijzigen `runtimeConfiguration.concurrency.runs` , worden nieuwe uitvoeringen in deze wachtrij geplaatst tot de [standaard limiet](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Zie de limiet voor het uitvoeren van een [wacht](#change-waiting-runs)tijd wijzigen om de standaard limiet te wijzigen. | 
| <*splitOn-expressie*> | Tekenreeks | Voor triggers die matrices retour neren, verwijst deze expressie naar de matrix die moet worden gebruikt, zodat u een werk stroom exemplaar voor elk matrix item kunt maken en uitvoeren, in plaats van een ' voor elke ' lus te gebruiken. <p>Deze expressie vertegenwoordigt bijvoorbeeld een item in de matrix die wordt geretourneerd in de hoofd inhoud van de trigger: `@triggerbody()?['value']` |
| <*bewerking-optie*> | Tekenreeks | U kunt het standaard gedrag wijzigen door de eigenschap in te stellen `operationOptions` . Zie [bewerkings opties](#operation-options)voor meer informatie. | 
|||| 

*Voorbeeld*

Deze trigger definitie wordt geabonneerd op de Office 365 Outlook-API, bevat een URL voor terugbellen naar het API-eind punt en wacht tot het eind punt reageert wanneer er een nieuwe e-mail binnenkomt.

```json
"When_a_new_email_arrives_(webhook)": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "body": {
         "NotificationUrl": "@{listCallbackUrl()}" 
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "path": "/MailSubscription/$subscriptions",
      "queries": {
          "folderPath": "Inbox",
          "hasAttachment": "Any",
          "importance": "Any"
      }
   },
   "splitOn": "@triggerBody()?['value']"
}
```

<a name="http-trigger"></a>

### <a name="http-trigger"></a>HTTP-trigger

Deze trigger verzendt een aanvraag naar het opgegeven HTTP-of HTTPS-eind punt op basis van het opgegeven terugkeer patroon. De trigger controleert vervolgens het antwoord om te bepalen of de werk stroom wordt uitgevoerd. Zie [service-eind punten aanroepen via http of https van Azure Logic apps](../connectors/connectors-native-http.md)voor meer informatie.

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "<method-type>",
      "uri": "<HTTP-or-HTTPS-endpoint-URL>",
      "headers": { "<header-content>" },
      "queries": "<query-parameters>",
      "body": "<body-content>",
      "authentication": { "<authentication-type-and-property-values>" },
      "retryPolicy": {
         "type": "<retry-behavior>"
      }
   },
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "operationOptions": "<operation-option>"
}
```

*Vereist*

| Eigenschap | Waarde | Type | Beschrijving |
|----------|-------|------|-------------|
| `method` | <*methode-type*> | Tekenreeks | De methode die moet worden gebruikt voor het verzenden van de uitgaande aanvraag: ' GET ', ' PUT ', ' POST ', ' PATCH ' of ' DELETE ' |
| `uri` | <*HTTP-of-HTTPS-eind punt-URL*> | Tekenreeks | De URL van het HTTP-of HTTPS-eind punt waarnaar u de uitgaande aanvraag wilt verzenden. Maximale teken reeks grootte: 2 KB <p>Voor een Azure-service of-resource bevat deze URI-syntaxis de resource-ID en het pad naar de resource die u wilt openen. |
| `frequency` | <*tijds eenheid*> | Tekenreeks | De tijds eenheid die aangeeft hoe vaak de trigger wordt geactiveerd: ' Second ', ' minuut ', ' uur ', ' dag ', ' week ', ' maand ' |
| `interval` | <*aantal-tijds eenheden*> | Geheel getal | Een waarde die aangeeft hoe vaak de trigger wordt geactiveerd op basis van de frequentie. Dit is het aantal tijds eenheden dat moet worden gewacht totdat de trigger opnieuw wordt geactiveerd. <p>Dit zijn de minimale en maximale intervallen: <p>-Maand: 1-16 maanden </br>-Dag: 1-500 dagen </br>-Uur: 1-12000 uur </br>-Minuut: 1-72000 minuten </br>-Seconde: 1-9999999 seconden<p>Als het interval bijvoorbeeld 6 is en de frequentie "month" is, is het terugkeer patroon elke 6 maanden. |
|||||

*Optioneel*

| Eigenschap | Waarde | Type | Beschrijving |
|----------|-------|------|-------------|
| `headers` | <*header-inhoud*> | JSON-object | Alle kopteksten die u moet toevoegen met de aanvraag <p>Als u bijvoorbeeld de taal en het type wilt instellen: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| `queries` | <*query-para meters*> | JSON-object | Alle query parameters die u moet gebruiken in de aanvraag <p>Het `"queries": { "api-version": "2018-01-01" }` object wordt bijvoorbeeld toegevoegd `?api-version=2018-01-01` aan de aanvraag. |
| `body` | <*hoofd tekst: inhoud*> | JSON-object | De inhoud van het bericht dat moet worden verzonden als Payload met de aanvraag |
| `authentication` | <*verificatie-type-en-eigenschap-waarden*> | JSON-object | Het verificatie model dat de aanvraag gebruikt voor het verifiëren van uitgaande aanvragen. Zie [verificatie toevoegen aan uitgaande oproepen](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)voor meer informatie. Behalve scheduler, `authority` wordt de eigenschap ondersteund. Wanneer deze niet is opgegeven, is de standaard waarde `https://management.azure.com/` , maar u kunt een andere waarde gebruiken. |
| `retryPolicy` > `type` | <*opnieuw proberen*> | JSON-object | Hiermee past u het gedrag voor opnieuw proberen aan voor periodieke storingen, die de status code 408, 429 en 5XX en eventuele connectiviteits uitzonderingen hebben. Zie [beleid voor opnieuw proberen](../logic-apps/logic-apps-exception-handling.md#retry-policies)voor meer informatie. |
| `runs` | <*Max-uitvoeringen*> | Geheel getal | Standaard worden werk stroom exemplaren op hetzelfde moment uitgevoerd (gelijktijdig of parallel) tot de [standaard limiet](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Als u deze limiet wilt wijzigen door een nieuwe <*aantal*> waarde in te stellen, raadpleegt u de [gelijktijdigheid van triggers wijzigen](#change-trigger-concurrency). |
| `maximumWaitingRuns` | <*Max-uitvoeringen-wachtrij*> | Geheel getal | Als voor uw werk stroom al het maximum aantal exemplaren wordt uitgevoerd dat u op basis van de eigenschap kunt wijzigen `runtimeConfiguration.concurrency.runs` , worden nieuwe uitvoeringen in deze wachtrij geplaatst tot de [standaard limiet](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Zie de limiet voor het uitvoeren van een [wacht](#change-waiting-runs)tijd wijzigen om de standaard limiet te wijzigen. |
| `operationOptions` | <*bewerking-optie*> | Tekenreeks | U kunt het standaard gedrag wijzigen door de eigenschap in te stellen `operationOptions` . Zie [bewerkings opties](#operation-options)voor meer informatie. |
|||||

*Uitvoerwaarden*

| Element | Type | Beschrijving |
|---------|------|-------------|
| `headers` | JSON-object | De kopteksten van het antwoord |
| `body` | JSON-object | De hoofd tekst van de reactie |
| `status code` | Geheel getal | De status code van het antwoord |
||||

*Vereisten voor binnenkomende aanvragen*

Voor een goede samen werking met uw logische app moet het eind punt voldoen aan een specifiek trigger patroon of contract, en deze antwoord eigenschappen herkennen:

| Eigenschap | Vereist | Beschrijving |
|----------|----------|-------------|
| Statuscode | Ja | Met de status code ' 200 OK ' wordt een uitvoering gestart. Met een andere status code wordt geen uitvoering gestart. |
| Header opnieuw proberen na | Nee | Het aantal seconden tot de logische app het eind punt opnieuw pollt |
| Locatie header | Nee | De URL die moet worden aangeroepen tijdens het volgende polling-interval. Als u niets opgeeft, wordt de oorspronkelijke URL gebruikt. |
|||| 

*Voorbeeld gedrag voor verschillende aanvragen*

| Statuscode | Opnieuw proberen na | Gedrag | 
|-------------|-------------|----------|
| 200 | geen | Voer de werk stroom uit en controleer opnieuw op meer gegevens na het gedefinieerde terugkeer patroon. | 
| 200 | 10 seconden | Voer de werk stroom uit en controleer de gegevens na 10 seconden opnieuw. |  
| 202 | 60 seconden | Activeer de werk stroom niet. De volgende poging gebeurt in één minuut, afhankelijk van het gedefinieerde terugkeer patroon. Als het gedefinieerde terugkeer patroon kleiner is dan één minuut, heeft de koptekst opnieuw proberen voor rang. Anders wordt het gedefinieerde terugkeer patroon gebruikt. | 
| 400 | geen | Ongeldige aanvraag: Voer de werk stroom niet uit. Als er geen `retryPolicy` is gedefinieerd, wordt het standaard beleid gebruikt. Nadat het aantal pogingen is bereikt, controleert de trigger opnieuw op gegevens na het gedefinieerde terugkeer patroon. | 
| 500 | geen| Server fout, voer de werk stroom niet uit. Als er geen `retryPolicy` is gedefinieerd, wordt het standaard beleid gebruikt. Nadat het aantal pogingen is bereikt, controleert de trigger opnieuw op gegevens na het gedefinieerde terugkeer patroon. | 
|||| 

<a name="http-webhook-trigger"></a>

### <a name="httpwebhook-trigger"></a>Trigger voor HTTPWebhook  

Deze trigger maakt het mogelijk dat uw logische app kan worden aangeroepen door een eind punt te maken dat een abonnement kan registreren door de opgegeven eind punt-URL aan te roepen. Wanneer u deze trigger in uw werk stroom maakt, maakt een uitgaande aanvraag de aanroep om het abonnement te registreren. Op die manier kan de trigger beginnen met Luis teren naar gebeurtenissen. Wanneer een bewerking deze trigger ongeldig maakt, maakt een uitgaande aanvraag automatisch de aanroep om het abonnement te annuleren. Zie [endpoint-abonnementen](#subscribe-unsubscribe)voor meer informatie.

U kunt ook [asynchrone limieten](#asynchronous-limits) opgeven voor een **HTTPWebhook** -trigger. Het gedrag van de trigger is afhankelijk van de secties die u gebruikt of weglaat.

```json
"HTTP_Webhook": {
   "type": "HttpWebhook",
   "inputs": {
      "subscribe": {
         "method": "<method-type>",
         "uri": "<endpoint-subscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" },
         "retryPolicy": { "<retry-behavior>" }
         },
      },
      "unsubscribe": {
         "method": "<method-type>",
         "url": "<endpoint-unsubscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" }
      }
   },
   "runTimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "operationOptions": "<operation-option>"
}
```

Sommige waarden, zoals <*methode-type*>, zijn beschikbaar voor zowel de- `"subscribe"` als- `"unsubscribe"` objecten.

*Vereist*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*methode-type*> | Tekenreeks | De HTTP-methode die moet worden gebruikt voor de abonnements aanvraag: ' GET ', ' PUT ', ' POST ', ' PATCH ' of ' DELETE ' | 
| <*Endpoint-abonneren: URL*> | Tekenreeks | De eind punt-URL waarnaar de abonnements aanvraag moet worden verzonden | 
|||| 

*Optioneel*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*methode-type*> | Tekenreeks | De HTTP-methode die moet worden gebruikt voor de annulerings aanvraag: ' GET ', ' PUT ', ' POST ', ' PATCH ' of ' DELETE ' | 
| <*eind punt-afmelden: URL*> | Tekenreeks | De eind punt-URL waarnaar de annulerings aanvraag moet worden verzonden | 
| <*hoofd tekst: inhoud*> | Tekenreeks | Bericht inhoud die in het abonnement of de annulerings aanvraag moet worden verzonden | 
| <*verificatie-type*> | JSON-object | Het verificatie model dat de aanvraag gebruikt voor het verifiëren van uitgaande aanvragen. Zie [verificatie toevoegen aan uitgaande oproepen](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)voor meer informatie. |
| <*opnieuw proberen*> | JSON-object | Hiermee past u het gedrag voor opnieuw proberen aan voor periodieke storingen, die de status code 408, 429 en 5XX en eventuele connectiviteits uitzonderingen hebben. Zie [beleid voor opnieuw proberen](../logic-apps/logic-apps-exception-handling.md#retry-policies)voor meer informatie. | 
| <*Max-uitvoeringen*> | Geheel getal | Standaard worden werk stroom exemplaren op hetzelfde moment uitgevoerd (gelijktijdig of parallel) tot de [standaard limiet](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Als u deze limiet wilt wijzigen door een nieuwe <*aantal*> waarde in te stellen, raadpleegt u de [gelijktijdigheid van triggers wijzigen](#change-trigger-concurrency). | 
| <*Max-uitvoeringen-wachtrij*> | Geheel getal | Als voor uw werk stroom al het maximum aantal exemplaren wordt uitgevoerd dat u op basis van de eigenschap kunt wijzigen `runtimeConfiguration.concurrency.runs` , worden nieuwe uitvoeringen in deze wachtrij geplaatst tot de [standaard limiet](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Zie de limiet voor het uitvoeren van een [wacht](#change-waiting-runs)tijd wijzigen om de standaard limiet te wijzigen. | 
| <*bewerking-optie*> | Tekenreeks | U kunt het standaard gedrag wijzigen door de eigenschap in te stellen `operationOptions` . Zie [bewerkings opties](#operation-options)voor meer informatie. | 
|||| 

*Uitvoerwaarden* 

| Element | Type | Beschrijving |
|---------|------|-------------| 
| koppen | JSON-object | De kopteksten van het antwoord | 
| body | JSON-object | De hoofd tekst van de reactie | 
| statuscode | Geheel getal | De status code van het antwoord | 
|||| 

*Voorbeeld*

Met deze trigger maakt u een abonnement op het opgegeven eind punt, geeft u een unieke call back-URL en wacht u op nieuw gepubliceerde technologie artikelen.

```json
"HTTP_Webhook": {
   "type": "HttpWebhook",
   "inputs": {
      "subscribe": {
         "method": "POST",
         "uri": "https://pubsubhubbub.appspot.com/subscribe",
         "body": {
            "hub.callback": "@{listCallbackUrl()}",
            "hub.mode": "subscribe",
            "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
         },
      },
      "unsubscribe": {
         "method": "POST",
         "url": "https://pubsubhubbub.appspot.com/subscribe",
         "body": {
            "hub.callback": "@{workflow().endpoint}@{listCallbackUrl()}",
            "hub.mode": "unsubscribe",
            "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
         }
      }
   }
}
```

<a name="recurrence-trigger"></a>

### <a name="recurrence-trigger"></a>Trigger voor terugkeer patroon  

Deze trigger wordt uitgevoerd op basis van het opgegeven terugkeer schema en biedt een eenvoudige manier om een regel matig actieve werk stroom te maken.

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
      "startTime": "<start-date-time-with-format-YYYY-MM-DDThh:mm:ss>",
      "timeZone": "<time-zone>",
      "schedule": {
         // Applies only when frequency is Day or Week. Separate values with commas.
         "hours": [ <one-or-more-hour-marks> ], 
         // Applies only when frequency is Day or Week. Separate values with commas.
         "minutes": [ <one-or-more-minute-marks> ], 
         // Applies only when frequency is Week. Separate values with commas.
         "weekDays": [ "Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday" ] 
      }
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "operationOptions": "<operation-option>"
}
```

*Vereist*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*tijds eenheid*> | Tekenreeks | De tijds eenheid die aangeeft hoe vaak de trigger wordt geactiveerd: ' Second ', ' minuut ', ' uur ', ' dag ', ' week ', ' maand ' | 
| <*aantal-tijds eenheden*> | Geheel getal | Een waarde die aangeeft hoe vaak de trigger wordt geactiveerd op basis van de frequentie. Dit is het aantal tijds eenheden dat moet worden gewacht totdat de trigger opnieuw wordt geactiveerd. <p>Dit zijn de minimale en maximale intervallen: <p>-Maand: 1-16 maanden </br>-Dag: 1-500 dagen </br>-Uur: 1-12000 uur </br>-Minuut: 1-72000 minuten </br>-Seconde: 1-9999999 seconden<p>Als het interval bijvoorbeeld 6 is en de frequentie "month" is, is het terugkeer patroon elke 6 maanden. | 
|||| 

*Optioneel*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*start-date-time-with-Format-JJJJ-MM-DDTuu: mm: SS*> | Tekenreeks | De begin datum en-tijd in deze indeling: <p>JJJJ-MM-DDTuu: mm: SS als u een tijd zone opgeeft <p>-of- <p>JJJJ-MM-DDTuu: mm: ssZ als u geen tijd zone opgeeft <p>Als u bijvoorbeeld 18 september 2017 om 2:00 uur wilt, geeft u "2017-09-18T14:00:00" op en geeft u een tijd zone op zoals "Pacific (standaard tijd), of geeft u" 2017-09-18T14:00:00Z "op zonder tijd zone. <p>**Opmerking:** Deze begin tijd heeft een maximum van 49 jaar in de toekomst en moet voldoen aan de [ISO 8601 date time-specificatie](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) in [UTC datum tijd notatie](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), maar zonder een [UTC-afwijking](https://en.wikipedia.org/wiki/UTC_offset). Als u geen tijd zone opgeeft, moet u de letter ' Z ' aan het einde toevoegen zonder spaties. Deze "Z" verwijst naar de equivalente [zeemijl tijd](https://en.wikipedia.org/wiki/Nautical_time). <p>Voor eenvoudige schema's is de start tijd het eerste voorval, terwijl voor complexe schema's de trigger niet eerder dan de begin tijd wordt geactiveerd. Zie [taken die regel matig worden uitgevoerd maken en plannen](../connectors/connectors-native-recurrence.md)voor meer informatie over begin datums en-tijden. | 
| <*tijd zone*> | Tekenreeks | Is alleen van toepassing wanneer u een start tijd opgeeft, omdat deze trigger geen [UTC-offset](https://en.wikipedia.org/wiki/UTC_offset)accepteert. Geef de tijd zone op die u wilt Toep assen. | 
| <*een of meer uur tekens*> | Geheel getal of gehele matrix | Als u "dag" of "week" opgeeft `frequency` , kunt u een of meer gehele getallen van 0 tot en met 23 opgeven, gescheiden door komma's, als uren van de dag waarop u de werk stroom wilt uitvoeren. <p>Als u bijvoorbeeld "10", "12" en "14" opgeeft, krijgt u een dag van 10 uur, 12 uur en 2 PM. | 
| <*een of meer minuut markeringen*> | Geheel getal of gehele matrix | Als u "dag" of "week" opgeeft `frequency` , kunt u een of meer gehele getallen van 0 tot en met 59 opgeven, gescheiden door komma's, als de minuten van het uur wanneer u de werk stroom wilt uitvoeren. <p>U kunt bijvoorbeeld "30" opgeven als het minuut merk en het vorige voor beeld gebruiken voor uren van de dag, u krijgt 10:30 uur, 12:30 uur en 2:30 uur. | 
| weekDays | Teken reeks of teken reeks matrix | Als u ' week ' opgeeft `frequency` , kunt u een of meer dagen opgeven, gescheiden door komma's, wanneer u de werk stroom wilt uitvoeren: "maandag", "dinsdag", "woensdag", "donderdag", "vrijdag", "zaterdag" en "zondag" | 
| <*Max-uitvoeringen*> | Geheel getal | Standaard worden werk stroom exemplaren op hetzelfde moment uitgevoerd (gelijktijdig of parallel) tot de [standaard limiet](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Als u deze limiet wilt wijzigen door een nieuwe <*aantal*> waarde in te stellen, raadpleegt u de [gelijktijdigheid van triggers wijzigen](#change-trigger-concurrency). | 
| <*Max-uitvoeringen-wachtrij*> | Geheel getal | Als voor uw werk stroom al het maximum aantal exemplaren wordt uitgevoerd dat u op basis van de eigenschap kunt wijzigen `runtimeConfiguration.concurrency.runs` , worden nieuwe uitvoeringen in deze wachtrij geplaatst tot de [standaard limiet](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Zie de limiet voor het uitvoeren van een [wacht](#change-waiting-runs)tijd wijzigen om de standaard limiet te wijzigen. | 
| <*bewerking-optie*> | Tekenreeks | U kunt het standaard gedrag wijzigen door de eigenschap in te stellen `operationOptions` . Zie [bewerkings opties](#operation-options)voor meer informatie. | 
|||| 

*Voorbeeld 1*

Deze eenvoudige terugkeer patroon trigger wordt dagelijks uitgevoerd:

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

*Voorbeeld 2*

U kunt een begin datum en-tijd opgeven voor het activeren van de trigger. Deze terugkeer patroon trigger begint op de opgegeven datum en wordt vervolgens dagelijks geactiveerd:

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Day",
      "interval": 1,
      "startTime": "2017-09-18T00:00:00Z"
   }
}
```

*Voorbeeld 3*

Deze terugkeer patroon trigger begint op 9 september 2017 om 2:00 uur en wordt wekelijks elke maandag geactiveerd om 10:30 uur, 12:30 uur en 2:30 PM Pacific (standaard tijd):

``` json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "hours": [ 10, 12, 14 ],
         "minutes": [ 30 ],
         "weekDays": [ "Monday" ]
      },
      "startTime": "2017-09-07T14:00:00",
      "timeZone": "Pacific Standard Time"
   }
}
```

Zie [taken die regel matig worden uitgevoerd maken en plannen](../connectors/connectors-native-recurrence.md)voor meer informatie en voor beelden voor deze trigger.

<a name="request-trigger"></a>

### <a name="request-trigger"></a>Trigger voor aanvragen

Deze trigger maakt het mogelijk dat uw logische app kan worden aangeroepen door een eind punt te maken dat binnenkomende aanvragen kan accepteren. Geef voor deze trigger een JSON-schema op waarmee de payload of invoer wordt beschreven en gevalideerd die de trigger van de binnenkomende aanvraag ontvangt. Het schema zorgt er ook voor dat de trigger eigenschappen gemakkelijker te verwijzen zijn vanaf latere acties in de werk stroom.

Als u deze trigger wilt aanroepen, moet u de `listCallbackUrl` API gebruiken, die wordt beschreven in de [werk stroom service rest API](/rest/api/logic/workflows). Zie [werk stromen aanroepen, activeren of nesten met HTTP-eind punten](../logic-apps/logic-apps-http-endpoint.md)voor meer informatie over het gebruik van deze trigger als een http-eind punt.

```json
"manual": {
   "type": "Request",
   "kind": "Http",
   "inputs": {
      "method": "<method-type>",
      "relativePath": "<relative-path-for-accepted-parameter>",
      "schema": {
         "type": "object",
         "properties": { 
            "<property-name>": {
               "type": "<property-type>"
            }
         },
         "required": [ "<required-properties>" ]
      }
   },
   "runTimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-run-queue>
      },
   },
   "operationOptions": "<operation-option>"
}
```

*Vereist*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*eigenschap-naam*> | Tekenreeks | De naam van een eigenschap in het JSON-schema, waarmee de nettolading wordt beschreven | 
| <*eigenschap-type*> | Tekenreeks | Het type eigenschap | 
|||| 

*Optioneel*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*methode-type*> | Tekenreeks | De methode die binnenkomende aanvragen moet gebruiken om uw logische app aan te roepen: ' GET ', ' PUT ', ' POST ', ' PATCH ', ' DELETE ' |
| <*relatief pad-voor geaccepteerde para meters*> | Tekenreeks | Het relatieve pad voor de para meter die door de URL van uw eind punt kan worden geaccepteerd | 
| <*vereist-eigenschappen*> | Matrix | Een of meer eigenschappen waarvoor waarden zijn vereist | 
| <*Max-uitvoeringen*> | Geheel getal | Standaard worden werk stroom exemplaren op hetzelfde moment uitgevoerd (gelijktijdig of parallel) tot de [standaard limiet](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Als u deze limiet wilt wijzigen door een nieuwe <*aantal*> waarde in te stellen, raadpleegt u de [gelijktijdigheid van triggers wijzigen](#change-trigger-concurrency). | 
| <*Max-uitvoeringen-wachtrij*> | Geheel getal | Als voor uw werk stroom al het maximum aantal exemplaren wordt uitgevoerd dat u op basis van de eigenschap kunt wijzigen `runtimeConfiguration.concurrency.runs` , worden nieuwe uitvoeringen in deze wachtrij geplaatst tot de [standaard limiet](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Zie de limiet voor het uitvoeren van een [wacht](#change-waiting-runs)tijd wijzigen om de standaard limiet te wijzigen. | 
| <*bewerking-optie*> | Tekenreeks | U kunt het standaard gedrag wijzigen door de eigenschap in te stellen `operationOptions` . Zie [bewerkings opties](#operation-options)voor meer informatie. | 
|||| 

*Voorbeeld*

Met deze trigger wordt aangegeven dat een inkomende aanvraag de HTTP POST-methode moet gebruiken om de trigger aan te roepen en een schema bevat waarmee de invoer van de inkomende aanvraag wordt gevalideerd:

```json
"manual": {
   "type": "Request",
   "kind": "Http",
   "inputs": {
      "method": "POST",
      "schema": {
         "type": "object",
         "properties": {
            "customerName": {
               "type": "String"
            },
            "customerAddress": { 
               "type": "Object",
               "properties": {
                  "streetAddress": {
                     "type": "string"
                  },
                  "city": {
                     "type": "string"
                  }
               }
            }
         }
      }
   }
}
```

<a name="trigger-conditions"></a>

## <a name="trigger-conditions"></a>Triggervoorwaarden

Voor elke trigger en alleen triggers kunt u een matrix met een of meer expressies toevoegen voor voor waarden die bepalen of de werk stroom moet worden uitgevoerd. Als u de `conditions` eigenschap wilt toevoegen aan een trigger in uw werk stroom, opent u de logische app in de code weergave-editor.

U kunt bijvoorbeeld opgeven dat een trigger alleen wordt geactiveerd wanneer een website een interne server fout retourneert door te verwijzen naar de status code van de trigger in de `conditions` eigenschap:

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Hour",
      "interval": 1
   },
   "conditions": [ {
      "expression": "@equals(triggers().code, 'InternalServerError')"
   } ]
}
```

Een trigger wordt standaard pas geactiveerd nadat een ' 200 OK '-antwoord is ontvangen. Wanneer een expressie naar de status code van een trigger verwijst, wordt het standaard gedrag van de trigger vervangen. Als u wilt dat de trigger wordt geactiveerd voor meer dan één status code, zoals de status code ' 200 ' en ' 201 ', moet u deze expressie als voor waarde toevoegen:

`@or(equals(triggers().code, 200),equals(triggers().code, 201))` 

<a name="split-on-debatch"></a>

## <a name="trigger-multiple-runs"></a>Meerdere uitvoeringen activeren

Als uw trigger een matrix retourneert voor de logische app die moet worden verwerkt, kan een lus voor elke wordt te lang duren om elk matrix item te verwerken. In plaats daarvan kunt u de eigenschap **SplitOn** in de trigger gebruiken om de *batch* -matrix uit te stellen. Met de batch verwerking worden de matrix items gesplitst en wordt een nieuw workflowexemplaar gestart dat voor elk matrix item wordt uitgevoerd. Deze benadering is handig, bijvoorbeeld wanneer u een eind punt wilt pollen dat mogelijk meerdere nieuwe items tussen polling intervallen retourneert. Zie [limieten en configuratie](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)voor het maximum aantal matrix items dat **SplitOn** kan verwerken in één logische app-uitvoering. 

> [!NOTE]
> U kunt **SplitOn** niet gebruiken met een synchroon antwoord patroon. Elke werk stroom die gebruikmaakt van **SplitOn** en een antwoord actie bevat, wordt asynchroon uitgevoerd en verzendt onmiddellijk een `202 ACCEPTED` antwoord.
>
> Wanneer de gelijktijdigheid van de trigger is ingeschakeld, wordt de [limiet voor SplitOn](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) aanzienlijk verminderd. Als het aantal items deze limiet overschrijdt, wordt de SplitOn-functionaliteit uitgeschakeld.
 
Als het Swagger-bestand van uw trigger een nettolading beschrijft die een matrix is, wordt de eigenschap **SplitOn** automatisch toegevoegd aan uw trigger. Als dat niet het geval is, voegt u deze eigenschap toe aan de nettolading van het antwoord met de matrix die u wilt debatchren.

*Voorbeeld*

Stel dat u een API hebt die deze reactie retourneert: 
  
```json
{
   "Status": "Succeeded",
   "Rows": [ 
      { 
         "id": 938109380,
         "name": "customer-name-one"
      },
      {
         "id": 938109381,
         "name": "customer-name-two"
      }
   ]
}
```

Uw logische app heeft alleen de inhoud van de matrix in nodig `Rows` , dus u kunt een trigger maken zoals in dit voor beeld:

``` json
"HTTP_Debatch": {
   "type": "Http",
    "inputs": {
        "uri": "https://mydomain.com/myAPI",
        "method": "GET"
    },
   "recurrence": {
      "frequency": "Second",
      "interval": 1
    },
    "splitOn": "@triggerBody()?.Rows"
}
```

> [!NOTE]
> Als u de `SplitOn` opdracht gebruikt, kunt u de eigenschappen buiten de matrix niet ophalen. Voor dit voor beeld is het niet mogelijk om de `status` eigenschap op te halen in de reactie die wordt geretourneerd door de API.
> 
> In `Rows` dit voor beeld wordt de operator gebruikt om te voor komen dat er een fout optreedt als de eigenschap niet bestaat `?` .

De definitie van uw werk stroom kan nu worden gebruikt `@triggerBody().name` om de waarden op te halen `name` `"customer-name-one"` uit de eerste uitvoering en `"customer-name-two"` van de tweede uitvoering. Uw trigger-uitvoer ziet er dus als volgt uit:

```json
{
   "body": {
      "id": 938109380,
      "name": "customer-name-one"
   }
}
```

```json
{
   "body": {
      "id": 938109381,
      "name": "customer-name-two"
   }
}
```

<a name="actions-overview"></a>

## <a name="actions-overview"></a>Overzicht van acties

Azure Logic Apps biedt verschillende actie typen: elk met verschillende invoer voor het definiëren van het unieke gedrag van een actie. Acties hebben deze elementen op hoog niveau, maar sommige zijn optioneel:

```json
"<action-name>": {
   "type": "<action-type>",
   "inputs": { 
      "<input-name>": { "<input-value>" },
      "retryPolicy": "<retry-behavior>" 
   },
   "runAfter": { "<previous-trigger-or-action-status>" },
   "runtimeConfiguration": { "<runtime-config-options>" },
   "operationOptions": "<operation-option>"
},
```

*Vereist*

| Waarde | Type | Beschrijving | 
|-------|------|-------------|
| <*actie naam*> | Tekenreeks | De naam voor de actie | 
| <*actie-type*> | Tekenreeks | Het actie type, bijvoorbeeld ' http ' of ' ApiConnection '| 
| <*invoer naam*> | Tekenreeks | De naam voor een invoer waarmee het gedrag van de actie wordt gedefinieerd | 
| <*invoer waarde*> | Sommige | De invoer waarde, die een teken reeks, geheel getal, JSON-object, enzovoort kan zijn | 
| <*vorige trigger-or-action-status*> | JSON-object | De naam en de resulterende status voor de trigger of actie die onmiddellijk moet worden uitgevoerd voordat deze huidige actie kan worden uitgevoerd | 
|||| 

*Optioneel*

| Waarde | Type | Beschrijving | 
|-------|------|-------------|
| <*opnieuw proberen*> | JSON-object | Hiermee past u het gedrag voor opnieuw proberen aan voor periodieke storingen, die de status code 408, 429 en 5XX en eventuele connectiviteits uitzonderingen hebben. Zie beleid voor opnieuw proberen voor meer informatie. | 
| <*runtime-configuratie-opties*> | JSON-object | Voor sommige acties kunt u het gedrag van de actie tijdens runtime wijzigen door eigenschappen in te stellen `runtimeConfiguration` . Zie runtime-configuratie- [instellingen](#runtime-config-options)voor meer informatie. | 
| <*bewerking-optie*> | Tekenreeks | Voor sommige acties kunt u het standaard gedrag wijzigen door de eigenschap in te stellen `operationOptions` . Zie [bewerkings opties](#operation-options)voor meer informatie. | 
|||| 

## <a name="action-types-list"></a>Lijst met actie typen

Hier volgen enkele veelgebruikte actie typen: 

* [Ingebouwde actie typen](#built-in-actions) zoals deze voor beelden en meer: 

  * [**Http**](#http-action) voor het aanroepen van eind punten via http of https

  * [**Reactie**](#response-action) op het reageren op aanvragen

  * [**Java script-code**](#run-javascript-code) voor het uitvoeren van Java script-code fragmenten uitvoeren

  * [**Functie**](#function-action) voor het aanroepen van Azure functions

  * Bewerkingen voor gegevens bewerkingen, zoals [**samen voegen**](#join-action), [**opstellen**](#compose-action), [**tabel**](#table-action), [**selecteren**](#select-action)en andere die gegevens uit verschillende invoer maken of transformeren

  * [**Werk stroom**](#workflow-action) voor het aanroepen van een andere werk stroom voor logische apps

* [Beheerde API-actie typen](#managed-api-actions) zoals [**ApiConnection**](#apiconnection-action) en [**ApiConnectionWebHook**](#apiconnectionwebhook-action) die verschillende connectors en Api's aanroepen die door micro soft worden beheerd, bijvoorbeeld Azure Service Bus, Office 365 Outlook, Power bi, Azure Blob Storage, OneDrive, github en meer

* [Werk stroom actie typen](#control-workflow-actions) zoals [**if**](#if-action), [**foreach**](#foreach-action), [**Switch**](#switch-action), [**Scope**](#scope-action)en [**until**](#until-action), die andere acties bevatten en helpen bij het organiseren van de uitvoering van werk stromen

<a name="built-in-actions"></a>

### <a name="built-in-actions"></a>Ingebouwde acties

| Actietype | Beschrijving | 
|-------------|-------------| 
| [**Compose**](#compose-action) | Hiermee maakt u één uitvoer van invoer, die verschillende typen kan hebben. | 
| [**Java script-code uitvoeren**](#run-javascript-code) | Java script-code fragmenten uitvoeren die binnen specifieke criteria passen. Zie [code fragmenten toevoegen en uitvoeren met inline code](../logic-apps/logic-apps-add-run-inline-code.md)voor code vereisten en meer informatie. |
| [**Functieassembly**](#function-action) | Hiermee wordt een Azure-functie aangeroepen. | 
| [**HTTP**](#http-action) | Hiermee wordt een HTTP-eind punt aangeroepen. | 
| [**Jointypen**](#join-action) | Hiermee maakt u een teken reeks van alle items in een matrix en scheidt u deze items met een opgegeven scheidings teken. | 
| [**JSON parseren**](#parse-json-action) | Maakt gebruikers vriendelijke tokens van eigenschappen in JSON-inhoud. U kunt vervolgens naar die eigenschappen verwijzen door de tokens in uw logische app op te nemen. | 
| [**Query’s uitvoeren**](#query-action) | Maakt een matrix van items in een andere matrix op basis van een voor waarde of filter. | 
| [**Antwoord**](#response-action) | Hiermee wordt een reactie op een binnenkomende oproep of aanvraag gemaakt. | 
| [**Selecteer**](#select-action) | Hiermee maakt u een matrix met JSON-objecten door items van een andere matrix te transformeren op basis van de opgegeven kaart. | 
| [**Tabel**](#table-action) | Hiermee maakt u een CSV-of HTML-tabel op basis van een matrix. | 
| [**Terminate**](#terminate-action) | Stopt een actief actieve werk stroom. | 
| [**Wait**](#wait-action) | Hiermee wordt uw werk stroom onderbroken voor een opgegeven duur of tot de opgegeven datum en tijd. | 
| [**Werkstroom**](#workflow-action) | Nest een werk stroom in een andere werk stroom. | 
||| 

<a name="managed-api-actions"></a>

### <a name="managed-api-actions"></a>Beheerde API-acties

| Actietype | Beschrijving | 
|-------------|-------------|  
| [**ApiConnection**](#apiconnection-action) | Hiermee wordt een HTTP-eind punt aangeroepen met behulp van een door [micro soft beheerde API](../connectors/apis-list.md). | 
| [**ApiConnectionWebhook**](#apiconnectionwebhook-action) | Werkt als HTTP-webhook, maar maakt gebruik van een door [micro soft beheerde API](../connectors/apis-list.md). | 
||| 

<a name="control-workflow-actions"></a>

### <a name="control-workflow-actions"></a>Werk stroom acties beheren

Met deze acties kunt u de uitvoering van werk stromen beheren en andere acties toevoegen. Van buiten een besturings werk stroom actie kunt u rechtstreeks verwijzen naar acties in de werk stroom actie beheren. Als u bijvoorbeeld een `Http` actie binnen een bereik hebt, kunt u `@body('Http')` vanuit een wille keurige plaats in de werk stroom verwijzen naar de expressie. Acties die in een besturings werk stroom actie bestaan, kunnen echter alleen worden uitgevoerd na andere acties die zich in dezelfde werk stroom structuur bevinden.

| Actietype | Beschrijving | 
|-------------|-------------| 
| [**ForEach**](#foreach-action) | Voer dezelfde acties uit in een lus voor elk item in een matrix. | 
| [**If**](#if-action) | Acties uitvoeren op basis van het feit of de opgegeven voor waarde waar of onwaar is. | 
| [**Bereik**](#scope-action) | Acties uitvoeren op basis van de groeps status uit een reeks acties. | 
| [**/Tijdnotatie**](#switch-action) | Acties worden ingedeeld in gevallen waarin waarden van expressies, objecten of tokens overeenkomen met de waarden die elke case heeft opgegeven. | 
| [**Totdat**](#until-action) | Acties uitvoeren in een lus tot de opgegeven voor waarde waar is. | 
|||  

## <a name="actions---detailed-reference"></a>Acties-gedetailleerde Naslag informatie

<a name="apiconnection-action"></a>

### <a name="apiconnection-action"></a>APIConnection-actie

Met deze actie wordt een HTTP-aanvraag naar een door [micro soft beheerde API](../connectors/apis-list.md) verzonden en is informatie vereist over de API en de para meters plus een verwijzing naar een geldige verbinding. 

``` json
"<action-name>": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<api-name>']['connectionId']"
         },
         "<other-action-specific-input-properties>"        
      },
      "method": "<method-type>",
      "path": "/<api-operation>",
      "retryPolicy": "<retry-behavior>",
      "queries": { "<query-parameters>" },
      "<other-action-specific-properties>"
    },
    "runAfter": {}
}
```

*Vereist*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*actie naam*> | Tekenreeks | De naam van de actie die door de connector wordt gegeven | 
| <*API-naam*> | Tekenreeks | De naam van de door micro soft beheerde API die wordt gebruikt voor de verbinding | 
| <*methode-type*> | Tekenreeks | De HTTP-methode voor het aanroepen van de API: ' GET ', ' PUT ', ' POST ', ' PATCH ' of ' DELETE ' | 
| <*API-bewerking*> | Tekenreeks | De API-bewerking die moet worden aangeroepen | 
|||| 

*Optioneel*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*overige-actie-specifiek-invoer-eigenschappen*> | JSON-object | Andere invoer eigenschappen die van toepassing zijn op deze specifieke actie | 
| <*opnieuw proberen*> | JSON-object | Hiermee past u het gedrag voor opnieuw proberen aan voor periodieke storingen, die de status code 408, 429 en 5XX en eventuele connectiviteits uitzonderingen hebben. Zie [beleid voor opnieuw proberen](../logic-apps/logic-apps-exception-handling.md#retry-policies)voor meer informatie. | 
| <*query-para meters*> | JSON-object | Alle query parameters die moeten worden toegevoegd met de API-aanroep. <p>Het `"queries": { "api-version": "2018-01-01" }` object wordt bijvoorbeeld toegevoegd `?api-version=2018-01-01` aan de aanroep. | 
| <*overige-actie-specifiek-eigenschappen*> | JSON-object | Andere eigenschappen die van toepassing zijn op deze specifieke actie | 
|||| 

*Voorbeeld*

Deze definitie beschrijft de actie **een E-mail verzenden** voor Office 365 Outlook Connector, een door micro soft beheerde API: 

```json
"Send_an_email": {
   "type": "ApiConnection",
   "inputs": {
      "body": {
         "Body": "Thank you for your membership!",
         "Subject": "Hello and welcome!",
         "To": "Sophie.Owen@contoso.com"
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "method": "POST",
      "path": "/Mail"
    },
    "runAfter": {}
}
```

<a name="apiconnection-webhook-action"></a>

### <a name="apiconnectionwebhook-action"></a>APIConnectionWebhook-actie

Met deze actie wordt een abonnements aanvraag via HTTP verzonden naar een eind punt met behulp van een door [micro soft beheerde API](../connectors/apis-list.md). Dit is een *call back-URL* waarnaar het eind punt een antwoord kan verzenden en wacht totdat het eind punt reageert. Zie [endpoint-abonnementen](#subscribe-unsubscribe)voor meer informatie.

```json
"<action-name>": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "subscribe": {
         "method": "<method-type>",
         "uri": "<api-subscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" },
         "retryPolicy": "<retry-behavior>",
         "queries": { "<query-parameters>" },
         "<other-action-specific-input-properties>"
      },
      "unsubscribe": {
         "method": "<method-type>",
         "uri": "<api-unsubscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" },
         "<other-action-specific-properties>"
      },
   },
   "runAfter": {}
}
```

Sommige waarden, zoals <*methode-type*>, zijn beschikbaar voor zowel de- `"subscribe"` als- `"unsubscribe"` objecten.

*Vereist*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*actie naam*> | Tekenreeks | De naam van de actie die door de connector wordt gegeven | 
| <*methode-type*> | Tekenreeks | De HTTP-methode die moet worden gebruikt voor het abonneren of afmelden van een eind punt: ' GET ', ' PUT ', ' POST ', ' PATCH ' of ' DELETE ' | 
| <*API-abonneren: URL*> | Tekenreeks | De URI die moet worden gebruikt voor het abonneren op de API | 
|||| 

*Optioneel*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*API-afmelden: URL*> | Tekenreeks | De URI die moet worden gebruikt voor het afmelden van de API | 
| <*header-inhoud*> | JSON-object | Alle headers die in de aanvraag moeten worden verzonden <p>U kunt bijvoorbeeld de taal en het type van een aanvraag instellen: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*hoofd tekst: inhoud*> | JSON-object | Bericht inhoud die in de aanvraag moet worden verzonden | 
| <*verificatie-type*> | JSON-object | Het verificatie model dat de aanvraag gebruikt voor het verifiëren van uitgaande aanvragen. Zie [verificatie toevoegen aan uitgaande oproepen](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)voor meer informatie. |
| <*opnieuw proberen*> | JSON-object | Hiermee past u het gedrag voor opnieuw proberen aan voor periodieke storingen, die de status code 408, 429 en 5XX en eventuele connectiviteits uitzonderingen hebben. Zie [beleid voor opnieuw proberen](../logic-apps/logic-apps-exception-handling.md#retry-policies)voor meer informatie. | 
| <*query-para meters*> | JSON-object | Alle query parameters die moeten worden toegevoegd met de API-aanroep <p>Het `"queries": { "api-version": "2018-01-01" }` object wordt bijvoorbeeld toegevoegd `?api-version=2018-01-01` aan de aanroep. | 
| <*overige-actie-specifiek-invoer-eigenschappen*> | JSON-object | Andere invoer eigenschappen die van toepassing zijn op deze specifieke actie | 
| <*overige-actie-specifiek-eigenschappen*> | JSON-object | Andere eigenschappen die van toepassing zijn op deze specifieke actie | 
|||| 

U kunt ook limieten opgeven voor een **ApiConnectionWebhook** -actie op dezelfde manier als [asynchrone http-limieten](#asynchronous-limits).

<a name="compose-action"></a>

### <a name="compose-action"></a>Actie opstellen

Met deze actie maakt u één uitvoer van meerdere invoer, met inbegrip van expressies. Zowel de uitvoer als de invoer kan elk type hebben dat Azure Logic Apps systeem eigen ondersteuning biedt, zoals matrices, JSON-objecten, XML en binaire elementen. Vervolgens kunt u de uitvoer van de actie in andere acties gebruiken. 

```json
"Compose": {
   "type": "Compose",
   "inputs": "<inputs-to-compose>",
   "runAfter": {}
},
```

*Vereist* 

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*invoer-naar-opstellen*> | Alle | De invoer voor het maken van één uitvoer | 
|||| 

*Voorbeeld 1*

<!-- markdownlint-disable MD038 -->
Deze actie definitie wordt samengevoegd `abcdefg ` met een spatie en de volgende waarde `1234` :
<!-- markdownlint-enable MD038 -->

```json
"Compose": {
   "type": "Compose",
   "inputs": "abcdefg 1234",
   "runAfter": {}
},
```

Dit is de uitvoer die door deze actie wordt gemaakt:

`abcdefg 1234`

*Voorbeeld 2*

Met deze actie definitie wordt een teken reeks variabele samengevoegd die een `abcdefg` variabele integer bevat die de `1234` volgende bevat:

```json
"Compose": {
   "type": "Compose",
   "inputs": "@{variables('myString')}@{variables('myInteger')}",
   "runAfter": {}
},
```

Dit is de uitvoer die door deze actie wordt gemaakt:

`"abcdefg1234"`

<a name="run-javascript-code"></a>

### <a name="execute-javascript-code-action"></a>Java script-code actie uitvoeren

Met deze actie wordt een Java script-code fragment uitgevoerd en worden de resultaten geretourneerd via een `Result` token waarmee later acties kunnen verwijzen.

```json
"Execute_JavaScript_Code": {
   "type": "JavaScriptCode",
   "inputs": {
      "code": "<JavaScript-code-snippet>",
      "explicitDependencies": {
         "actions": [ <previous-actions> ],
         "includeTrigger": true
      }
   },
   "runAfter": {}
}
```

*Vereist*

| Waarde | Type | Beschrijving |
|-------|------|-------------|
| <*Java script-code-fragment*> | Varieert | De Java script-code die u wilt uitvoeren. Zie [code fragmenten toevoegen en uitvoeren met inline code](../logic-apps/logic-apps-add-run-inline-code.md)voor code vereisten en meer informatie. <p>In het `code` kenmerk kan uw code fragment het object alleen-lezen gebruiken `workflowContext` als invoer. Dit object heeft subeigenschappen die uw code toegang geven tot de resultaten van de trigger en eerdere acties in uw werk stroom. `workflowContext`Zie Naslag informatie over [Triggers en actie resultaten in uw code](../logic-apps/logic-apps-add-run-inline-code.md#workflowcontext)voor meer info over het object. |
||||

*Vereist in sommige gevallen*

Het `explicitDependencies` kenmerk geeft aan dat u de resultaten van de trigger, eerdere acties of beide als afhankelijkheden van het code fragment expliciet wilt toevoegen. Zie [para meters voor inline code toevoegen](../logic-apps/logic-apps-add-run-inline-code.md#add-parameters)voor meer informatie over het toevoegen van deze afhankelijkheden. 

`includeTrigger`U kunt voor het kenmerk waarden opgeven `true` `false` .

| Waarde | Type | Beschrijving |
|-------|------|-------------|
| <*eerdere acties*> | Stringarray | Een matrix met de opgegeven actie namen. Gebruik de namen van de acties die worden weer gegeven in de definitie van de werk stroom waarbij actie namen onderstrepings tekens (_) en geen spaties ("") gebruiken. |
||||

*Voorbeeld 1*

Met deze actie wordt code uitgevoerd waarmee de naam van de logische app wordt opgehaald en wordt de tekst ' Hallo wereld van \<logic-app-name> ' als resultaat geretourneerd. In dit voor beeld verwijst de code naar de naam van de werk stroom door de eigenschap te openen `workflowContext.workflow.name` via het object met het kenmerk alleen-lezen `workflowContext` . Zie voor meer informatie over het gebruik van het `workflowContext` object [referentie-trigger en actie resultaten in uw code](../logic-apps/logic-apps-add-run-inline-code.md#workflowcontext).

```json
"Execute_JavaScript_Code": {
   "type": "JavaScriptCode",
   "inputs": {
      "code": "var text = \"Hello world from \" + workflowContext.workflow.name;\r\n\r\nreturn text;"
   },
   "runAfter": {}
}
```

*Voorbeeld 2*

Met deze actie wordt code uitgevoerd in een logische app die wordt geactiveerd wanneer een nieuwe e-mail binnenkomt in een werk-of school account. De logische app maakt ook gebruik van een e-mail actie voor het verzenden van goed keuring die de inhoud van de ontvangen e-mail samen met een aanvraag voor goed keuring doorstuurt.

De code extraheert de e-mail adressen uit de eigenschap van de trigger `Body` en retourneert de adressen samen met de `SelectedOption` eigenschaps waarde van de goedkeurings actie. De actie bevat expliciet de actie goedkeurings-e-mail verzenden als een afhankelijkheid in het `explicitDependencies`  >  `actions` kenmerk.

```json
"Execute_JavaScript_Code": {
   "type": "JavaScriptCode",
   "inputs": {
      "code": "var re = /(([^<>()\\[\\]\\\\.,;:\\s@\"]+(\\.[^<>()\\[\\]\\\\.,;:\\s@\"]+)*)|(\".+\"))@((\\[[0-9]{1,3}\\.[0-9]{1,3}\\.[0-9]{1,3}\\.[0-9]{1,3}])|(([a-zA-Z\\-0-9]+\\.)+[a-zA-Z]{2,}))/g;\r\n\r\nvar email = workflowContext.trigger.outputs.body.Body;\r\n\r\nvar reply = workflowContext.actions.Send_approval_email_.outputs.body.SelectedOption;\r\n\r\nreturn email.match(re) + \" - \" + reply;\r\n;",
      "explicitDependencies": {
         "actions": [
            "Send_approval_email_"
         ]
      }
   },
   "runAfter": {}
}
```



<a name="function-action"></a>

### <a name="function-action"></a>Functie actie

Met deze actie wordt een eerder gemaakte [Azure-functie](../azure-functions/functions-get-started.md)aangeroepen.

```json
"<Azure-function-name>": {
   "type": "Function",
   "inputs": {
     "function": {
        "id": "<Azure-function-ID>"
      },
      "method": "<method-type>",
      "headers": { "<header-content>" },
      "body": { "<body-content>" },
      "queries": { "<query-parameters>" } 
   },
   "runAfter": {}
}
```

*Vereist*

| Waarde | Type | Beschrijving | 
|-------|------|-------------|  
| <*Azure-function-ID*> | Tekenreeks | De resource-ID voor de Azure-functie die u wilt aanroepen. Dit is de notatie voor deze waarde:<p>"/Subscriptions/<*Azure-abonnement-ID*>/ResourceGroups/<*Azure-resource-group*>/providers/Microsoft.web/sites/<*Azure-function-app-name*>/functions/<*Azure-function-name*>" | 
| <*methode-type*> | Tekenreeks | De HTTP-methode die moet worden gebruikt voor het aanroepen van de functie: ' GET ', ' PUT ', ' POST ', ' PATCH ' of ' DELETE ' <p>Als niet wordt opgegeven, is de standaard waarde de methode POST. | 
||||

*Optioneel*

| Waarde | Type | Beschrijving | 
|-------|------|-------------|  
| <*header-inhoud*> | JSON-object | Alle headers die met de aanroep moeten worden verzonden <p>U kunt bijvoorbeeld de taal en het type van een aanvraag instellen: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*hoofd tekst: inhoud*> | JSON-object | Bericht inhoud die in de aanvraag moet worden verzonden | 
| <*query-para meters*> | JSON-object | Alle query parameters die moeten worden toegevoegd met de API-aanroep <p>Het `"queries": { "api-version": "2018-01-01" }` object wordt bijvoorbeeld toegevoegd `?api-version=2018-01-01` aan de aanroep. | 
| <*overige-actie-specifiek-invoer-eigenschappen*> | JSON-object | Andere invoer eigenschappen die van toepassing zijn op deze specifieke actie | 
| <*overige-actie-specifiek-eigenschappen*> | JSON-object | Andere eigenschappen die van toepassing zijn op deze specifieke actie | 
||||

Wanneer u uw logische app opslaat, voert de Logic Apps-Engine deze controles uit op de functie waarnaar wordt verwezen:

* Uw werk stroom moet toegang hebben tot de functie.

* Uw werk stroom kan alleen gebruikmaken van een standaard HTTP-trigger of een algemene JSON-webhook-trigger. 

  De Logic Apps-Engine haalt de URL van de trigger op en slaat deze op in de cache, die wordt gebruikt tijdens runtime. Als een bewerking de URL in de cache ongeldig maakt, mislukt de **functie** actie tijdens runtime. U kunt dit probleem oplossen door de logische app opnieuw op te slaan, zodat de logische app de trigger-URL opnieuw ophaalt en opslaat in de cache.

* Er kan geen route worden gedefinieerd voor de functie.

* Alleen de autorisatie niveaus function en Anonymous zijn toegestaan. 

*Voorbeeld*

Met deze actie definitie wordt de eerder gemaakte functie ' GetProductID ' aangeroepen:

```json
"GetProductID": {
   "type": "Function",
   "inputs": {
     "function": {
        "id": "/subscriptions/<XXXXXXXXXXXXXXXXXXXX>/resourceGroups/myLogicAppResourceGroup/providers/Microsoft.Web/sites/InventoryChecker/functions/GetProductID"
      },
      "method": "POST",
      "headers": { 
          "x-ms-date": "@utcnow()"
       },
      "body": { 
          "Product_ID": "@variables('ProductID')"
      }
   },
   "runAfter": {}
}
```

<a name="http-action"></a>

### <a name="http-action"></a>HTTP-actie

Met deze actie wordt een aanvraag verzonden naar het opgegeven HTTP-of HTTPS-eind punt en wordt de reactie gecontroleerd om te bepalen of de werk stroom wordt uitgevoerd. Zie [service-eind punten aanroepen via http of https van Azure Logic apps](../connectors/connectors-native-http.md)voor meer informatie.

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "<method-type>",
      "uri": "<HTTP-or-HTTPS-endpoint-URL>",
      "headers": { "<header-content>" },
      "queries": { "<query-parameters>" },
      "body": "<body-content>",
      "authentication": { "<authentication-type-and-property-values>" },
      "retryPolicy": {
         "type": "<retry-behavior>"
      },
   },
   "runAfter": {}
}
```

*Vereist*

| Eigenschap | Waarde | Type | Beschrijving |
|----------|-------|------|-------------|
| `method` | <*methode-type*> | Tekenreeks | De methode die moet worden gebruikt voor het verzenden van de uitgaande aanvraag: ' GET ', ' PUT ', ' POST ', ' PATCH ' of ' DELETE ' |
| `uri` | <*HTTP-of-HTTPS-eind punt-URL*> | Tekenreeks | De URL van het HTTP-of HTTPS-eind punt waarnaar u de uitgaande aanvraag wilt verzenden. Maximale teken reeks grootte: 2 KB <p>Voor een Azure-service of-resource bevat deze URI-syntaxis de resource-ID en het pad naar de resource die u wilt openen. |
|||||

*Optioneel*

| Eigenschap | Waarde | Type | Beschrijving |
|----------|-------|------|-------------|
| `headers` | <*header-inhoud*> | JSON-object | Alle kopteksten die u moet toevoegen met de aanvraag <p>Als u bijvoorbeeld de taal en het type wilt instellen: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| `queries` | <*query-para meters*> | JSON-object | Alle query parameters die u moet gebruiken in de aanvraag <p>Het `"queries": { "api-version": "2018-01-01" }` object wordt bijvoorbeeld toegevoegd `?api-version=2018-01-01` aan de aanroep. |
| `body` | <*hoofd tekst: inhoud*> | JSON-object | De inhoud van het bericht dat moet worden verzonden als Payload met de aanvraag |
| `authentication` | <*verificatie-type-en-eigenschap-waarden*> | JSON-object | Het verificatie model dat de aanvraag gebruikt voor het verifiëren van uitgaande aanvragen. Zie [verificatie toevoegen aan uitgaande oproepen](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)voor meer informatie. Behalve scheduler, `authority` wordt de eigenschap ondersteund. Wanneer deze niet is opgegeven, is de standaard waarde `https://management.azure.com/` , maar u kunt een andere waarde gebruiken. |
| `retryPolicy` > `type` | <*opnieuw proberen*> | JSON-object | Hiermee past u het gedrag voor opnieuw proberen aan voor periodieke storingen, die de status code 408, 429 en 5XX en eventuele connectiviteits uitzonderingen hebben. Zie [beleid voor opnieuw proberen](../logic-apps/logic-apps-exception-handling.md#retry-policies)voor meer informatie. |
| <*overige-actie-specifiek-invoer-eigenschappen*> | <*invoer eigenschap*> | JSON-object | Andere invoer eigenschappen die van toepassing zijn op deze specifieke actie |
| <*overige-actie-specifiek-eigenschappen*> | <*eigenschap-waarde*> | JSON-object | Andere eigenschappen die van toepassing zijn op deze specifieke actie |
|||||

*Voorbeeld*

Met deze actie definitie wordt het meest recente nieuws opgehaald door een aanvraag te verzenden naar het opgegeven eind punt:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://mynews.example.com/latest"
   }
}
```

<a name="join-action"></a>

### <a name="join-action"></a>Actie samen voegen

Met deze actie maakt u een teken reeks van alle items in een matrix en scheidt u deze items met het opgegeven scheidings teken. 

```json
"Join": {
   "type": "Join",
   "inputs": {
      "from": <array>,
      "joinWith": "<delimiter>"
   },
   "runAfter": {}
}
```

*Vereist*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*array*> | Matrix | De matrix of expressie die de bron items levert. Als u een expressie opgeeft, plaatst u deze expressie met dubbele aanhalings tekens. | 
| <*vorm*> | Teken reeks met één teken | Het teken waarmee elk item in de teken reeks wordt gescheiden | 
|||| 

*Voorbeeld*

Stel dat u een eerder gemaakte variabele ' myIntegerArray ' hebt die deze matrix met gehele getallen bevat: 

`[1,2,3,4]` 

Deze actie definitie haalt de waarden van de variabele op met behulp van de `variables()` functie in een expressie en maakt deze teken reeks met deze waarden, gescheiden door een komma: `"1,2,3,4"`

```json
"Join": {
   "type": "Join",
   "inputs": {
      "from": "@variables('myIntegerArray')",
      "joinWith": ","
   },
   "runAfter": {}
}
```

<a name="parse-json-action"></a>

### <a name="parse-json-action"></a>JSON-actie parseren

Met deze actie worden gebruikers vriendelijke velden of *tokens* gemaakt op basis van de eigenschappen in JSON-inhoud. U kunt vervolgens met behulp van de tokens deze eigenschappen in uw logische app benaderen. Als u bijvoorbeeld JSON-uitvoer wilt gebruiken van services zoals Azure Service Bus en Azure Cosmos DB, kunt u deze actie in uw logische app toevoegen, zodat u gemakkelijker kunt verwijzen naar de gegevens in die uitvoer.

```json
"Parse_JSON": {
   "type": "ParseJson",
   "inputs": {
      "content": "<JSON-source>",
         "schema": { "<JSON-schema>" }
      },
      "runAfter": {}
},
```

*Vereist*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*JSON-bron*> | JSON-object | De JSON-inhoud die u wilt parseren | 
| <*JSON-schema*> | JSON-object | Het JSON-schema waarmee de onderliggende JSON-inhoud wordt beschreven, die de actie gebruikt voor het parseren van de bron-JSON-inhoud. <p>**Tip**: in Logic apps Designer kunt u het schema opgeven of een steek proef voor beeld opgeven zodat de actie het schema kan genereren. | 
|||| 

*Voorbeeld*

Deze actie definitie maakt deze tokens die u in uw werk stroom kunt gebruiken, maar alleen in acties die worden uitgevoerd na de actie **JSON parseren** :

`FirstName`, `LastName` en `Email`

```json
"Parse_JSON": {
   "type": "ParseJson",
   "inputs": {
      "content": {
         "Member": {
            "Email": "Sophie.Owen@contoso.com",
            "FirstName": "Sophie",
            "LastName": "Owen"
         }
      },
      "schema": {
         "type": "object",
         "properties": {
            "Member": {
               "type": "object",
               "properties": {
                  "Email": {
                     "type": "string"
                  },
                  "FirstName": {
                     "type": "string"
                  },
                  "LastName": {
                     "type": "string"
                  }
               }
            }
         }
      }
   },
   "runAfter": { }
},
```

In dit voor beeld geeft de eigenschap ' content ' de JSON-inhoud op voor de actie die moet worden geparseerd. U kunt deze JSON-inhoud ook opgeven als de nettolading van de steek proef voor het genereren van het schema.

```json
"content": {
   "Member": { 
      "FirstName": "Sophie",
      "LastName": "Owen",
      "Email": "Sophie.Owen@contoso.com"
   }
},
```

De eigenschap schema geeft u het JSON-schema op dat wordt gebruikt voor het beschrijven van de JSON-inhoud:

```json
"schema": {
   "type": "object",
   "properties": {
      "Member": {
         "type": "object",
         "properties": {
            "FirstName": {
               "type": "string"
            },
            "LastName": {
               "type": "string"
            },
            "Email": {
               "type": "string"
            }
         }
      }
   }
}
```

<a name="query-action"></a>

### <a name="query-action"></a>Query actie

Met deze actie maakt u een matrix van items in een andere matrix op basis van een opgegeven voor waarde of filter.

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": <array>,
      "where": "<condition-or-filter>"
   },
   "runAfter": {}
}
```

*Vereist*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*array*> | Matrix | De matrix of expressie die de bron items levert. Als u een expressie opgeeft, plaatst u deze expressie met dubbele aanhalings tekens. |
| <*voor waarde-of-filter*> | Tekenreeks | De voor waarde die wordt gebruikt voor het filteren van items in de bron matrix <p>**Opmerking**: als er geen waarden aan de voor waarde voldoen, maakt de actie een lege matrix. |
|||| 

*Voorbeeld*

Met deze actie definitie maakt u een matrix met waarden die groter zijn dan de opgegeven waarde. Dit is twee:

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": [ 1, 3, 0, 5, 4, 2 ],
      "where": "@greater(item(), 2)"
   }
}
```

<a name="response-action"></a>

### <a name="response-action"></a>Reactie actie  

Met deze actie wordt de payload voor het antwoord op een HTTP-aanvraag gemaakt. 

```json
"Response" {
    "type": "Response",
    "kind": "http",
    "inputs": {
        "statusCode": 200,
        "headers": { <response-headers> },
        "body": { <response-body> }
    },
    "runAfter": {}
},
```

*Vereist*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*antwoord-status-code*> | Geheel getal | De HTTP-status code die naar de inkomende aanvraag wordt verzonden. De standaard code is ' 200 OK ', maar de code kan een geldige status code zijn die begint met 2xx, 4xx of 5xx, maar niet met 3xxx. | 
|||| 

*Optioneel*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*antwoord-headers*> | JSON-object | Een of meer headers die moeten worden toegevoegd aan het antwoord | 
| <*Antwoord: tekst*> | Sommige | De antwoord tekst, die een teken reeks, JSON-object of zelfs binaire inhoud van een vorige actie kan zijn | 
|||| 

*Voorbeeld*

Met deze actie definitie wordt een reactie gemaakt op een HTTP-aanvraag met de opgegeven status code, bericht tekst en bericht koppen:

```json
"Response": {
   "type": "Response",
   "inputs": {
      "statusCode": 200,
      "body": {
         "ProductID": 0,
         "Description": "Organic Apples"
      },
      "headers": {
         "x-ms-date": "@utcnow()",
         "content-type": "application/json"
      }
   },
   "runAfter": {}
}
```

*Beperkingen*

In tegens telling tot andere acties heeft de **reactie** actie speciale beperkingen: 

* Uw werk stroom kan de **antwoord** actie alleen gebruiken wanneer de werk stroom wordt gestart met een HTTP-aanvraag trigger, wat betekent dat uw werk stroom moet worden geactiveerd door een HTTP-aanvraag.

* Uw werk stroom kan de **reactie** actie overal gebruiken, *behalve* binnen **foreach** -lussen, **tot** lussen, inclusief sequentiële lussen en parallelle vertakkingen. 

* De oorspronkelijke aanvraag haalt de reactie van uw werk stroom alleen op wanneer alle acties die zijn vereist door de **reactie** actie, binnen de [http-time-outlimiet](../logic-apps/logic-apps-limits-and-config.md#http-limits)zijn voltooid.

  Als uw werk stroom echter een andere logische app aanroept als een geneste werk stroom, wacht de bovenliggende werk stroom totdat de geneste werk stroom is voltooid, ongeacht hoeveel tijd verstrijkt voordat de geneste werk stroom is voltooid.

* Als uw werk stroom gebruikmaakt van de **reactie** actie en een synchroon antwoord patroon, kan de werk stroom ook de **splitOn** -opdracht niet gebruiken in de trigger definitie omdat die opdracht meerdere uitvoeringen maakt. Controleer of dit het geval is als de PUT-methode wordt gebruikt, en als deze waarde True is, wordt een antwoord van ' onjuiste aanvraag ' geretourneerd.

  Als uw werk stroom gebruikmaakt van de **splitOn** -opdracht en een **reactie** actie, wordt de werk stroom asynchroon uitgevoerd en wordt direct het antwoord "202 accepted" geretourneerd.

* Wanneer de uitvoering van de werk stroom de **reactie** actie heeft bereikt, maar de inkomende aanvraag al een antwoord heeft ontvangen, wordt de **reactie** actie als ' mislukt ' gemarkeerd vanwege het conflict. Als gevolg hiervan wordt de uitvoering van de logische app ook gemarkeerd met de status mislukt.

<a name="select-action"></a>

### <a name="select-action"></a>Actie selecteren

Met deze actie wordt een matrix met JSON-objecten gemaakt door items van een andere matrix te transformeren op basis van de opgegeven kaart. De uitvoer matrix en de bron matrix hebben altijd hetzelfde aantal items. U kunt het aantal objecten in de uitvoer matrix niet wijzigen, maar het is ook mogelijk om eigenschappen en hun waarden toe te voegen aan of te verwijderen uit deze objecten. De `select` eigenschap geeft ten minste één sleutel/waarde-paar op waarmee de toewijzing voor het transformeren van items in de bron matrix wordt gedefinieerd. Een sleutel/waarde-paar vertegenwoordigt een eigenschap en de waarde ervan in alle objecten in de uitvoer matrix.

```json
"Select": {
   "type": "Select",
   "inputs": {
      "from": <array>,
      "select": { 
          "<key-name>": "<expression>",
          "<key-name>": "<expression>"        
      }
   },
   "runAfter": {}
},
```

*Vereist* 

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*array*> | Matrix | De matrix of expressie die de bron items levert. Zorg ervoor dat u een expressie tussen dubbele aanhalings tekens plaatst. <p>**Opmerking**: als de bron matrix leeg is, maakt de actie een lege matrix. | 
| <*sleutel naam*> | Tekenreeks | De eigenschaps naam die is toegewezen aan het resultaat van <*expressie*> <p>Als u een nieuwe eigenschap wilt toevoegen voor alle objecten in de uitvoer matrix, geeft u een <*sleutel naam*> voor die eigenschap en een <*expressie*> voor de waarde van de eigenschap. <p>Als u een eigenschap van alle objecten in de matrix wilt verwijderen, moet u de <*sleutel naam*> voor die eigenschap weglaten. | 
| <*expressie*> | Tekenreeks | De expressie die het item in de bron matrix transformeert en het resultaat toewijst aan <*sleutel naam*> | 
|||| 

Met de actie **selecteren** wordt een matrix als uitvoer gemaakt, dus elke actie die deze uitvoer wil gebruiken, moet een matrix accepteren of u moet de matrix Converteren naar het type dat door de actie van de gebruiker wordt geaccepteerd. Als u de uitvoer matrix bijvoorbeeld wilt omzetten in een teken reeks, kunt u die matrix door geven aan de actie **opstellen** en vervolgens naar de uitvoer van de actie **opstellen** in uw andere acties verwijzen.

*Voorbeeld*

Met deze actie definitie wordt een JSON-object matrix gemaakt op basis van een matrix met gehele getallen. De actie doorloopt de bron matrix, haalt elk geheel getal op met behulp van de `@item()` expressie en wijst elke waarde toe aan de `number` eigenschap "" in elk JSON-object:

```json
"Select": {
   "type": "Select",
   "inputs": {
      "from": [ 1, 2, 3 ],
      "select": { 
         "number": "@item()" 
      }
   },
   "runAfter": {}
},
```

Dit is de matrix die met deze actie wordt gemaakt:

`[ { "number": 1 }, { "number": 2 }, { "number": 3 } ]`

Als u deze matrix uitvoer in andere acties wilt gebruiken, geeft u deze uitvoer door naar een **opstel** actie:

```json
"Compose": {
   "type": "Compose",
   "inputs": "@body('Select')",
   "runAfter": {
      "Select": [ "Succeeded" ]
   }
},
```

U kunt vervolgens de uitvoer van de actie **opstellen** gebruiken in uw andere acties, bijvoorbeeld de **Office 365 Outlook-een e-mail actie verzenden** :

```json
"Send_an_email": {
   "type": "ApiConnection",
   "inputs": {
      "body": {
         "Body": "@{outputs('Compose')}",
         "Subject": "Output array from Select and Compose actions",
         "To": "<your-email@domain>"
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "method": "post",
      "path": "/Mail"
   },
   "runAfter": {
      "Compose": [ "Succeeded" ]
   }
},
```

<a name="table-action"></a>

### <a name="table-action"></a>Tabel actie

Met deze actie wordt een CSV-of HTML-tabel uit een matrix gemaakt. Voor matrices met JSON-objecten maakt deze actie automatisch de kolom koppen van de eigenschaps namen van de objecten. Voor matrices met andere gegevens typen moet u de kolom koppen en waarden opgeven. Deze matrix bevat bijvoorbeeld de eigenschappen ' ID ' en ' Product_Name ' die deze actie kan gebruiken voor de namen van kolom koppen:

`[ {"ID": 0, "Product_Name": "Apples"}, {"ID": 1, "Product_Name": "Oranges"} ]` 

```json
"Create_<CSV | HTML>_table": {
   "type": "Table",
   "inputs": {
      "format": "<CSV | HTML>",
      "from": <array>,
      "columns": [ 
         {
            "header": "<column-name>",
            "value": "<column-value>"
         },
         {
            "header": "<column-name>",
            "value": "<column-value>"
         } 
      ]
   },
   "runAfter": {}
}
```

*Vereist* 

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| \<CSV *or* HTML>| Tekenreeks | De notatie voor de tabel die u wilt maken | 
| <*array*> | Matrix | De matrix of expressie die de bron items voor de tabel levert <p>**Opmerking**: als de bron matrix leeg is, maakt de actie een lege tabel. | 
|||| 

*Optioneel*

Als u kolom koppen en-waarden wilt opgeven of aanpassen, gebruikt u de `columns` matrix. Wanneer `header-value` paren dezelfde header naam hebben, worden de waarden ervan weer gegeven in dezelfde kolom onder de naam van de header. Anders wordt met elke unieke header een unieke kolom gedefinieerd.

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*kolom naam*> | Tekenreeks | De naam van de header voor een kolom | 
| <*kolom-waarde*> | Alle | De waarde in die kolom | 
|||| 

*Voorbeeld 1*

Stel dat u een eerder gemaakte variabele ' myItemArray ' hebt die momenteel deze matrix bevat:

`[ {"ID": 0, "Product_Name": "Apples"}, {"ID": 1, "Product_Name": "Oranges"} ]`

Met deze actie definitie maakt u een CSV-tabel op basis van de variabele ' myItemArray '. De expressie die door de eigenschap wordt gebruikt, `from` haalt de matrix op uit ' myItemArray ' met behulp van de `variables()` functie:

```json
"Create_CSV_table": {
   "type": "Table",
   "inputs": {
      "format": "CSV",
      "from": "@variables('myItemArray')"
   },
   "runAfter": {}
}
```

Dit is de CSV-tabel die met deze actie wordt gemaakt: 

```
ID,Product_Name 
0,Apples 
1,Oranges 
```

*Voorbeeld 2*

Met deze actie definitie maakt u een HTML-tabel op basis van de variabele ' myItemArray '. De expressie die door de eigenschap wordt gebruikt, `from` haalt de matrix op uit ' myItemArray ' met behulp van de `variables()` functie:

```json
"Create_HTML_table": {
   "type": "Table",
   "inputs": {
      "format": "HTML",
      "from": "@variables('myItemArray')"
   },
   "runAfter": {}
}
```

Dit is de HTML-tabel die door deze actie wordt gemaakt: 

<table><thead><tr><th>Id</th><th>Product_Name</th></tr></thead><tbody><tr><td>0</td><td>Appels</td></tr><tr><td>1</td><td>Sinaasappels</td></tr></tbody></table>

*Voorbeeld 3*

Met deze actie definitie maakt u een HTML-tabel op basis van de variabele ' myItemArray '. In dit voor beeld worden echter de standaard namen van kolom koppen vervangen door ' Stock_ID ' en ' description ', en wordt het woord ' organiek ' toegevoegd aan de waarden in de kolom ' description '.

```json
"Create_HTML_table": {
   "type": "Table",
   "inputs": {
      "format": "HTML",
      "from": "@variables('myItemArray')",
      "columns": [ 
         {
            "header": "Stock_ID",
            "value": "@item().ID"
         },
         {
            "header": "Description",
            "value": "@concat('Organic ', item().Product_Name)"
         }
      ]
    },
   "runAfter": {}
},
```

Dit is de HTML-tabel die door deze actie wordt gemaakt: 

<table><thead><tr><th>Stock_ID</th><th>Beschrijving</th></tr></thead><tbody><tr><td>0</td><td>Organische appels</td></tr><tr><td>1</td><td>Biologische sinaasappels</td></tr></tbody></table>

<a name="terminate-action"></a>

### <a name="terminate-action"></a>Actie beëindigen

Met deze actie wordt het uitvoeren van een werk stroom exemplaar gestopt, worden acties die worden uitgevoerd, geannuleerd en worden eventuele resterende acties overgeslagen en wordt de opgegeven status geretourneerd. U kunt bijvoorbeeld de actie **beëindigen** gebruiken wanneer de logische app volledig moet worden afgesloten met een fout status. Deze actie heeft geen invloed op reeds voltooide acties en kan niet worden weer gegeven in **foreach** en **tot** lussen, inclusief sequentiële lussen.

```json
"Terminate": {
   "type": "Terminate",
   "inputs": {
       "runStatus": "<status>",
       "runError": {
            "code": "<error-code-or-name>",
            "message": "<error-message>"
       }
   },
   "runAfter": {}
}
```

*Vereist*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*hebben*> | Tekenreeks | De status die moet worden geretourneerd voor de uitvoering: "Failed", "geannuleerd" of "geslaagd" |
|||| 

*Optioneel*

De eigenschappen voor het object ' runStatus ' zijn alleen van toepassing wanneer de eigenschap ' runStatus ' is ingesteld op de status ' failed '.

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*fout code-or-name*> | Tekenreeks | De code of naam voor de fout |
| <*fout-bericht*> | Tekenreeks | Het bericht of de tekst waarmee de fout wordt beschreven en alle acties die de gebruiker van de app kan uitvoeren | 
|||| 

*Voorbeeld*

Met deze actie definitie wordt een uitvoering van een werk stroom gestopt, wordt de uitvoerings status ingesteld op ' failed ' en worden de status, een fout code en een fout bericht geretourneerd:

```json
"Terminate": {
    "type": "Terminate",
    "inputs": {
        "runStatus": "Failed",
        "runError": {
            "code": "Unexpected response",
            "message": "The service received an unexpected response. Please try again."
        }
   },
   "runAfter": {}
}
```

<a name="wait-action"></a>

### <a name="wait-action"></a>Actie wachten

Met deze actie wordt de uitvoering van de werk stroom onderbroken voor het opgegeven interval of tot de opgegeven tijd, maar niet beide.

*Opgegeven interval*

```json
"Delay": {
   "type": "Wait",
   "inputs": {
      "interval": {
         "count": <number-of-units>,
         "unit": "<interval>"
      }
   },
   "runAfter": {}
},
```

*Opgegeven tijd*

```json
"Delay_until": {
   "type": "Wait",
   "inputs": {
      "until": {
         "timestamp": "<date-time-stamp>"
      }
   },
   "runAfter": {}
},
```

*Vereist*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*aantal eenheden*> | Geheel getal | Het aantal te wachten eenheden voor de **vertragings** actie | 
| <*bereik*> | Tekenreeks | Voor de **vertragings** actie, het interval dat moet worden gewacht: ' seconde ', ' minuut ', ' uur ', ' dag ', ' week ', ' maand ' | 
| <*datum-tijds tempel*> | Tekenreeks | De datum en tijd voor de **vertraging tot** actie tot de uitvoering. Deze waarde moet gebruikmaken van de [UTC-notatie voor datum en tijd](https://en.wikipedia.org/wiki/Coordinated_Universal_Time). | 
|||| 

*Voorbeeld 1*

Met deze actie definitie wordt de werk stroom 15 minuten onderbroken:

```json
"Delay": {
   "type": "Wait",
   "inputs": {
      "interval": {
         "count": 15,
         "unit": "Minute"
      }
   },
   "runAfter": {}
},
```

*Voorbeeld 2*

Met deze actie definitie wordt de werk stroom onderbroken tot de opgegeven tijd:

```json
"Delay_until": {
   "type": "Wait",
   "inputs": {
      "until": {
         "timestamp": "2017-10-01T00:00:00Z"
      }
   },
   "runAfter": {}
},
```

<a name="workflow-action"></a>

### <a name="workflow-action"></a>Werk stroom actie

Met deze actie wordt een andere eerder gemaakte logische app aangeroepen. Dit betekent dat u andere werk stromen voor logische apps kunt insluiten en opnieuw gebruiken. U kunt ook de uitvoer van de onderliggende of *geneste* logische app gebruiken in acties die volgen op de geneste logische app, op voor waarde dat de onderliggende logische app een antwoord retourneert.

De Logic Apps-Engine controleert de toegang tot de trigger die u wilt aanroepen, dus zorg ervoor dat u toegang hebt tot deze trigger. De geneste logische app moet ook voldoen aan de volgende criteria:

* Met een trigger wordt de geneste logische app aangeroepen, zoals een [aanvraag](#request-trigger) of [http-](#http-trigger) trigger

* Hetzelfde Azure-abonnement als uw bovenliggende logische app

* Als u de uitvoer van de geneste logische app in uw bovenliggende logische app wilt gebruiken, moet de geneste logische app een [reactie](#response-action) actie hebben

```json
"<nested-logic-app-name>": {
   "type": "Workflow",
   "inputs": {
      "body": { "<body-content" },
      "headers": { "<header-content>" },
      "host": {
         "triggerName": "<trigger-name>",
         "workflow": {
            "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group>/providers/Microsoft.Logic/<nested-logic-app-name>"
         }
      }
   },
   "runAfter": {}
}
```

*Vereist*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*nest-Logic-app-name*> | Tekenreeks | De naam voor de logische app die u wilt aanroepen | 
| <*trigger naam*> | Tekenreeks | De naam voor de trigger in de geneste logische app die u wilt aanroepen | 
| <*Azure-abonnement-ID*> | Tekenreeks | De Azure-abonnements-ID voor de geneste logische app |
| <*Azure-resource-group*> | Tekenreeks | De naam van de Azure-resource groep voor de geneste logische app |
| <*nest-Logic-app-name*> | Tekenreeks | De naam voor de logische app die u wilt aanroepen |
||||

*Optioneel*

| Waarde | Type | Beschrijving | 
|-------|------|-------------|  
| <*header-inhoud*> | JSON-object | Alle headers die met de aanroep moeten worden verzonden | 
| <*hoofd tekst: inhoud*> | JSON-object | Bericht inhoud die met de aanroep moet worden verzonden | 
||||

*Uitvoerwaarden*

De uitvoer van deze actie verschilt op basis van de reactie actie van de geneste logische app. Als de geneste logische app geen reactie actie bevat, zijn de uitvoer leeg.

*Voorbeeld*

Nadat de actie ' Start_search ' is voltooid, roept deze werk stroom actie definitie een andere logische app op met de naam ' Get_product_information ', die wordt door gegeven aan de opgegeven invoer:

```json
"actions": {
   "Start_search": { <action-definition> },
   "Get_product_information": {
      "type": "Workflow",
      "inputs": {
         "body": {
            "ProductID": "24601",
         },
         "host": {
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/InventoryManager-RG/providers/Microsoft.Logic/Get_product_information",
            "triggerName": "Find_product"
         },
         "headers": {
            "content-type": "application/json"
         }
      },
      "runAfter": { 
         "Start_search": [ "Succeeded" ]
      }
   }
},
```

## <a name="control-workflow-action-details"></a>Details van werk stroom acties beheren

<a name="foreach-action"></a>

### <a name="foreach-action"></a>Foreach-actie

Deze herhalings actie doorloopt een matrix en voert acties uit voor elk matrix item. Standaard wordt de lus ' voor elke ' parallel uitgevoerd op een maximum aantal lussen. Zie [limieten en configuratie](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)voor dit maximum. Meer informatie [over het maken van voor elke-lus](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop).

```json
"For_each": {
   "type": "Foreach",
   "actions": { 
      "<action-1>": { "<action-definition-1>" },
      "<action-2>": { "<action-definition-2>" }
   },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "runtimeConfiguration": {
      "concurrency": {
         "repetitions": <count>
      }
    },
    "operationOptions": "<operation-option>"
}
```

*Vereist* 

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*actie-1... nvt*> | Tekenreeks | De namen van de acties die op elk matrix item worden uitgevoerd | 
| <*actie-definitie-1... nvt*> | JSON-object | De definities van de acties die worden uitgevoerd | 
| <*for-each-expressie*> | Tekenreeks | De expressie die verwijst naar elk item in de opgegeven matrix | 
|||| 

*Optioneel*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*aantal*> | Geheel getal | Standaard worden de herhalingen voor elke-lus op hetzelfde moment (gelijktijdig of parallel) uitgevoerd tot de [standaard limiet](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Als u deze limiet wilt wijzigen door een nieuwe <*aantal*> waarde in te stellen, raadpleegt u [voor elke lus-gelijktijdigheid wijzigen](#change-for-each-concurrency). | 
| <*bewerking-optie*> | Tekenreeks | Als u een lus voor elke wilt uitvoeren in plaats van parallel, stelt u <*bewerking-optie*> in `Sequential` of <*aantal*> aan `1` , maar niet beide. Zie voor meer informatie [Run ' voor elke ' herhalen](#sequential-for-each). | 
|||| 

*Voorbeeld*

Met deze lus each wordt een e-mail verzonden voor elk item in de matrix, dat bijlagen bevat van een binnenkomend e-mail bericht. De lus verzendt een e-mail bericht, inclusief de bijlage, naar een persoon die de bijlage bekijkt.

```json
"For_each": {
   "type": "Foreach",
   "actions": {
      "Send_an_email": {
         "type": "ApiConnection",
         "inputs": {
            "body": {
               "Body": "@base64ToString(items('For_each')?['Content'])",
               "Subject": "Review attachment",
               "To": "Sophie.Owen@contoso.com"
                },
            "host": {
               "connection": {
                  "id": "@parameters('$connections')['office365']['connectionId']"
               }
            },
            "method": "post",
            "path": "/Mail"
         },
         "runAfter": {}
      }
   },
   "foreach": "@triggerBody()?['Attachments']",
   "runAfter": {}
}
```

Als u alleen een matrix wilt opgeven die als uitvoer van de trigger wordt door gegeven, haalt deze expressie de <*matrix naam*> matrix van de hoofd tekst van de trigger. Om een fout te voor komen als de matrix niet bestaat, gebruikt de expressie de `?` operator:

`@triggerBody()?['<array-name>']` 

<a name="if-action"></a>

### <a name="if-action"></a>Als actie

Deze actie, die een *voorwaardelijke instructie* is, evalueert een expressie die een voor waarde vertegenwoordigt en voert een andere vertakking uit op basis van het feit of de voor waarde waar of onwaar is. Als de voor waarde waar is, is de voor waarde gemarkeerd met de status geslaagd. Meer informatie [over het maken van voorwaardelijke instructies](../logic-apps/logic-apps-control-flow-conditional-statement.md).

``` json
"Condition": {
   "type": "If",
   "expression": { "<condition>" },
   "actions": {
      "<action-1>": { "<action-definition>" }
   },
   "else": {
      "actions": {
        "<action-2>": { "<action-definition" }
      }
   },
   "runAfter": {}
}
```

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*regeling*> | JSON-object | De voor waarde, die een expressie kan zijn, om te evalueren | 
| <*actie-1*> | JSON-object | De actie die moet worden uitgevoerd wanneer <*conditie*> resulteert in waar | 
| <*actie-definitie*> | JSON-object | De definitie voor de actie | 
| <*actie-2*> | JSON-object | De actie die moet worden uitgevoerd wanneer <*conditie*> resulteert in ONWAAR | 
|||| 

De acties in de `actions` of `else` objecten krijgen de volgende statussen:

* "Geslaagd" wanneer ze worden uitgevoerd en geslaagd
* "Failed" wanneer ze worden uitgevoerd en mislukken
* Overgeslagen wanneer de respectieve vertakking niet wordt uitgevoerd

*Voorbeeld*

Deze voor waarde geeft aan dat wanneer de variabele integer een waarde heeft die groter is dan nul, de werk stroom een website controleert. Als de variabele nul of minder is, controleert de werk stroom een andere website.

```json
"Condition": {
   "type": "If",
   "expression": {
      "and": [ {
         "greater": [ "@variables('myIntegerVariable')", 0 ] 
      } ]
   },
   "actions": { 
      "HTTP - Check this website": {
         "type": "Http",
         "inputs": {
         "method": "GET",
            "uri": "http://this-url"
         },
         "runAfter": {}
      }
   },
   "else": {
      "actions": {
         "HTTP - Check this other website": {
            "type": "Http",
            "inputs": {
               "method": "GET",
               "uri": "http://this-other-url"
            },
            "runAfter": {}
         }
      }
   },
   "runAfter": {}
}
```  

#### <a name="how-conditions-use-expressions"></a>Hoe voor waarden expressies gebruiken

Hier volgen enkele voor beelden die laten zien hoe u expressies kunt gebruiken in voor waarden:
  
| JSON | Resultaat | 
|------|--------| 
| "expressie": " @parameters (<*hasSpecialAction*> ')" | Alleen voor Boole-expressies wordt de voor waarde door gegeven voor elke waarde die resulteert in waar. <p>Als u andere typen naar een Booleaanse waarde wilt converteren, gebruikt u deze functies: `empty()` of `equals()` . | 
| "expressie": " @greater (acties (' <*actie*> '). output. Value, para meters (' <*drempelwaarde*> '))" | Voor vergelijkings functies wordt de actie alleen uitgevoerd wanneer de uitvoer van <*actie*> groter is dan de <*drempelwaarde*> waarde. | 
| "expressie": " @or (meer (acties (' <*actie*> '). output. Value, para meters (' <*drempelwaarde*> ')), minder (acties (' <*same-action*> '). uitvoer. waarde, 100))" | Voor logische functies en het maken van geneste Boole-expressies wordt de actie uitgevoerd wanneer de uitvoer van <*actie*> meer is dan de *drempel* waarde voor <>waarde of onder 100. | 
| "expressie": " @equals (length (acties (' <*actie*> '). uitvoer. fouten), 0))" | U kunt matrix functies gebruiken om te controleren of de matrix items bevat. De actie wordt uitgevoerd wanneer de `errors` matrix leeg is. | 
||| 

<a name="scope-action"></a>

### <a name="scope-action"></a>Bereik actie

Met deze actie worden acties logisch gegroepeerd in *bereiken* die een eigen status krijgen nadat de acties in dat bereik zijn uitgevoerd. Vervolgens kunt u de status van het bereik gebruiken om te bepalen of andere acties worden uitgevoerd. Meer informatie [over het maken van scopes](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md).

```json
"Scope": {
   "type": "Scope",
   "actions": {
      "<inner-action-1>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      },
      "<inner-action-2>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      }
   }
}
```

*Vereist*

| Waarde | Type | Beschrijving | 
|-------|------|-------------|  
| <*binnenste actie-1... nvt*> | JSON-object | Een of meer acties die binnen het bereik worden uitgevoerd |
| <*actie-invoer*> | JSON-object | De invoer voor elke actie |
|||| 

<a name="switch-action"></a>

### <a name="switch-action"></a>De actie Switch

Deze actie, ook wel bekend als een *Switch-instructie*, organiseert andere acties in *gevallen* en wijst een waarde toe aan elke case, met uitzonde ring van de standaard situatie als er een bestaat. Wanneer uw werk stroom wordt uitgevoerd, vergelijkt de **Switch** actie de waarde van een expressie, object of Token op basis van de waarden die voor elke case zijn opgegeven. Als de **Schakel** actie een overeenkomende Case vindt, voert uw werk stroom alleen de acties uit voor dat geval. Telkens wanneer de **Switch** actie wordt uitgevoerd, bestaat er slechts één overeenkomende Case of zijn er geen overeenkomsten. Als er geen overeenkomsten bestaan, worden de standaard acties uitgevoerd met de **Schakel** actie. Meer informatie [over het maken van Switch-instructies](../logic-apps/logic-apps-control-flow-switch-statement.md).

``` json
"Switch": {
   "type": "Switch",
   "expression": "<expression-object-or-token>",
   "cases": {
      "Case": {
         "actions": {
           "<action-name>": { "<action-definition>" }
         },
         "case": "<matching-value>"
      },
      "Case_2": {
         "actions": {
           "<action-name>": { "<action-definition>" }
         },
         "case": "<matching-value>"
      }
   },
   "default": {
      "actions": {
         "<default-action-name>": { "<default-action-definition>" }
      }
   },
   "runAfter": {}
}
```

*Vereist*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*expressie-object-or-Token*> | Varieert | De expressie, het JSON-object of het token dat moet worden geëvalueerd | 
| <*actie naam*> | Tekenreeks | De naam van de actie die moet worden uitgevoerd voor de overeenkomende Case | 
| <*actie-definitie*> | JSON-object | De definitie voor de actie die moet worden uitgevoerd voor de overeenkomende Case | 
| <*overeenkomende waarde*> | Varieert | De waarde die moet worden vergeleken met het geëvalueerde resultaat | 
|||| 

*Optioneel*

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*standaard actie naam*> | Tekenreeks | De naam van de standaard actie die moet worden uitgevoerd wanneer er geen overeenkomende Case bestaat | 
| <*standaard-actie-definitie*> | JSON-object | De definitie voor de actie die moet worden uitgevoerd wanneer er geen overeenkomende Case bestaat | 
|||| 

*Voorbeeld*

Met deze actie definitie wordt geëvalueerd of de persoon die op het e-mail bericht voor goedkeurings aanvragen reageert, de optie goed keuren of de optie afwijzen heeft geselecteerd. Op basis van deze keuze voert **de actie voor de handeling** de acties uit voor de overeenkomende case, waarmee een andere e-mail wordt verzonden naar de responder, maar met verschillende woord groepen in elk geval. 

``` json
"Switch": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case": {
         "actions": {
            "Send_an_email": { 
               "type": "ApiConnection",
               "inputs": {
                  "Body": "Thank you for your approval.",
                  "Subject": "Response received", 
                  "To": "Sophie.Owen@contoso.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            },
            "runAfter": {}
         },
         "case": "Approve"
      },
      "Case_2": {
         "actions": {
            "Send_an_email_2": { 
               "type": "ApiConnection",
               "inputs": {
                  "Body": "Thank you for your response.",
                  "Subject": "Response received", 
                  "To": "Sophie.Owen@contoso.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            },
            "runAfter": {}     
         },
         "case": "Reject"
      }
   },
   "default": {
      "actions": { 
         "Send_an_email_3": { 
            "type": "ApiConnection",
            "inputs": {
               "Body": "Please respond with either 'Approve' or 'Reject'.",
               "Subject": "Please respond", 
               "To": "Sophie.Owen@contoso.com"
            },
            "host": {
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
   "runAfter": {
      "Send_approval_email": [ 
         "Succeeded"
      ]
   }
}
```

<a name="until-action"></a>

### <a name="until-action"></a>Tot actie

Deze lus-actie bevat acties die worden uitgevoerd tot de opgegeven voor waarde waar is. De lus controleert de voor waarde als de laatste stap nadat alle andere acties zijn uitgevoerd. U kunt meer dan één actie in het `"actions"` object toevoegen en voor de actie moet ten minste één limiet worden gedefinieerd. Meer informatie [over het maken van ' until '-lussen](../logic-apps/logic-apps-control-flow-loops.md#until-loop). 

```json
 "Until": {
   "type": "Until",
   "actions": {
      "<action-name>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      },
      "<action-name>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      }
   },
   "expression": "<condition>",
   "limit": {
      "count": <loop-count>,
      "timeout": "<loop-timeout>"
   },
   "runAfter": {}
}
```

| Waarde | Type | Beschrijving | 
|-------|------|-------------| 
| <*actie naam*> | Tekenreeks | De naam van de actie die u in de lus wilt uitvoeren | 
| <*actie-type*> | Tekenreeks | Het actie type dat u wilt uitvoeren | 
| <*actie-invoer*> | Sommige | De invoer voor de actie die moet worden uitgevoerd | 
| <*regeling*> | Tekenreeks | De voor waarde of expressie die moet worden geëvalueerd nadat alle acties in de lus zijn uitgevoerd | 
| <*loop-aantal*> | Geheel getal | De limiet voor het aantal lussen dat de actie kan worden uitgevoerd. Zie [limieten en configuratie voor Azure Logic apps](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)voor meer informatie over de standaard limiet en maximum limiet. | 
| <*loop-time-out*> | Tekenreeks | De limiet voor het langste tijdstip waarop de lus kan worden uitgevoerd. De standaard `timeout` waarde is `PT1H` , de vereiste [ISO 8601-indeling](https://en.wikipedia.org/wiki/ISO_8601). |
|||| 

> [!NOTE]
> Als de expressie afhankelijk is van de uitvoer van een wille keurige actie binnen de lus until, controleert u of u een fout hebt die het resultaat is van die actie.

*Voorbeeld*

Met deze lus-actie definitie wordt een HTTP-aanvraag verzonden naar de opgegeven URL tot aan een van deze voor waarden wordt voldaan:

* De aanvraag krijgt een antwoord met de status code ' 200 OK '.
* De lus heeft 60 keer uitgevoerd.
* De lus is één uur actief.

```json
 "Run_until_loop_succeeds_or_expires": {
    "type": "Until",
    "actions": {
        "HTTP": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {}
        }
    },
    "expression": "@equals(outputs('HTTP')['statusCode'], 200)",
    "limit": {
        "count": 60,
        "timeout": "PT1H"
    },
    "runAfter": {}
}
```

<a name="subscribe-unsubscribe"></a>

## <a name="webhooks-and-subscriptions"></a>Webhooks en abonnementen

Met webhook-triggers en acties worden niet regel matig eind punten gecontroleerd, maar er wordt gewacht op specifieke gebeurtenissen of gegevens op deze eind punten. Deze triggers en acties worden *geabonneerd* op de eind punten door een *call back-URL* op te geven waar het eind punt reacties kan sturen.

De `subscribe` aanroep gebeurt wanneer de werk stroom op een wille keurige manier wordt gewijzigd, bijvoorbeeld wanneer de referenties worden vernieuwd of wanneer de invoer parameters voor een trigger of actie worden gewijzigd. Deze aanroep maakt gebruik van dezelfde para meters als standaard HTTP-acties. 

De `unsubscribe` oproep treedt automatisch op wanneer een bewerking de trigger of actie ongeldig maakt, bijvoorbeeld:

* De trigger verwijderen of uitschakelen. 
* De werk stroom verwijderen of uitschakelen. 
* Het abonnement verwijderen of uitschakelen. 

Ter ondersteuning van deze aanroepen `@listCallbackUrl()` retourneert de expressie een unieke call back-URL voor de trigger of actie. Deze URL vertegenwoordigt een unieke id voor de eind punten die gebruikmaken van de REST API van de service. De para meters voor deze functie zijn gelijk aan die van de webhook-trigger of de actie.

<a name="asynchronous-limits"></a>

## <a name="change-asynchronous-duration"></a>Asynchrone duur wijzigen

Voor zowel triggers als acties kunt u de duur van het asynchrone patroon beperken tot een bepaald tijds interval door de eigenschap toe te voegen `limit.timeout` . Op die manier geldt dat als de actie niet is voltooid wanneer het interval is vervallen, de status van de actie is gemarkeerd als `Cancelled` met de `ActionTimedOut` code. De `timeout` eigenschap maakt gebruik van de [ISO 8601-indeling](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations).

``` json
"<trigger-or-action-name>": {
   "type": "Workflow | Webhook | Http | ApiConnectionWebhook | ApiConnection",
   "inputs": {},
   "limit": {
      "timeout": "PT10S"
   },
   "runAfter": {}
}
```

<a name="runtime-config-options"></a>

## <a name="runtime-configuration-settings"></a>Runtime configuratie-instellingen

U kunt het standaard runtime gedrag voor triggers en acties wijzigen door deze eigenschappen toe te voegen `runtimeConfiguration` aan de trigger of actie definitie.

| Eigenschap | Type | Beschrijving | Trigger of actie | 
|----------|------|-------------|-------------------| 
| `runtimeConfiguration.concurrency.runs` | Geheel getal | Wijzig de [*standaard limiet*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) voor het aantal werk stroom exemplaren dat gelijktijdig kan worden uitgevoerd (gelijktijdig of parallel). Het aanpassen van deze waarde kan helpen het aantal aanvragen dat back-end-systemen ontvangen te beperken. <p>Het instellen van de `runs` eigenschap op `1` werkt op dezelfde manier als de `operationOptions` eigenschap instellen op `SingleInstance` . U kunt beide eigenschappen instellen, maar niet beide. <p>Als u de standaard limiet wilt wijzigen, raadpleegt u [trigger-gelijktijdigheid wijzigen](#change-trigger-concurrency) of [instanties opeenvolgend activeren](#sequential-trigger). | Alle triggers | 
| `runtimeConfiguration.concurrency.maximumWaitingRuns` | Geheel getal | Wijzig de [*standaard limiet*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) voor het aantal werk stroom exemplaren dat moet wachten om te worden uitgevoerd wanneer de logische app al het maximum aantal gelijktijdige exemplaren uitvoert. <p>Zie de limiet voor het uitvoeren van een [wacht](#change-waiting-runs)tijd wijzigen om de standaard limiet te wijzigen. | Alle triggers | 
| `runtimeConfiguration.concurrency.repetitions` | Geheel getal | Wijzig de [*standaard limiet*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) voor het aantal herhalingen voor elke lus dat tegelijkertijd kan worden uitgevoerd (gelijktijdig of parallel). <p>Het instellen van de `repetitions` eigenschap op `1` werkt op dezelfde manier als de `operationOptions` eigenschap instellen op `SingleInstance` . U kunt beide eigenschappen instellen, maar niet beide. <p>Als u de standaard limiet wilt wijzigen, raadpleegt u ["voor elke" gelijktijdigheid](#change-for-each-concurrency) "of [" elke "voor elke" elke "wordt opeenvolgend herhaald](#sequential-for-each). | Actie: <p>[Foreach](#foreach-action) | 
| `runtimeConfiguration.paginationPolicy.minimumItemCount` | Geheel getal | Met deze waarde wordt het *minimale* aantal resultaten dat moet worden opgehaald, opgegeven voor specifieke acties die de paginering ondersteunen en inschakelen. <p>Zie [bulk gegevens, items of resultaten ophalen met behulp van paginering](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md) om paginering in te scha kelen | Actie: variërend |
| `runtimeConfiguration.secureData.properties` | Matrix | In veel triggers en acties verbergen deze instellingen invoer, uitvoer of beide van de uitvoerings geschiedenis van de logische app. <p>Zie [invoer en uitvoer verbergen uit uitvoerings geschiedenis](../logic-apps/logic-apps-securing-a-logic-app.md#secure-data-code-view)voor meer informatie over het beveiligen van deze gegevens. | De meeste triggers en acties |
| `runtimeConfiguration.staticResult` | JSON-object | Voor acties die ondersteuning bieden voor de instelling [statisch resultaat](../logic-apps/test-logic-apps-mock-data-static-results.md) , `staticResult` heeft het object de volgende kenmerken: <p>- `name`, die verwijst naar de naam van de statische resultaten definitie van de huidige actie, die wordt weer gegeven in het- `staticResults` kenmerk in het kenmerk van de werk stroom van de logische app `definition` . Zie [statische resultaten-schema verwijzing voor werk stroom definitie taal](../logic-apps/logic-apps-workflow-definition-language.md#static-results)voor meer informatie. <p> - `staticResultOptions`Hiermee wordt aangegeven of statische resultaten `Enabled` voor de huidige actie zijn of niet. <p>Als u statische resultaten wilt inschakelen, raadpleegt u [Logic Apps testen met gegevens model maken door statische resultaten in te stellen](../logic-apps/test-logic-apps-mock-data-static-results.md) | Actie: variërend |
||||| 

<a name="operation-options"></a>

## <a name="operation-options"></a>Bewerkings opties

U kunt het standaard gedrag voor triggers en acties wijzigen met de `operationOptions` eigenschap in de trigger of de actie definitie.

| Bewerkings optie | Type | Beschrijving | Trigger of actie | 
|------------------|------|-------------|-------------------| 
| `DisableAsyncPattern` | Tekenreeks | Voer op HTTP gebaseerde acties synchroon uit, in plaats van asynchroon. <p><p>Zie [acties synchroon uitvoeren](#disable-asynchronous-pattern)om deze optie in te stellen. | Regelen <p>[ApiConnection](#apiconnection-action), <br>[Http](#http-action), <br>[Response](#response-action) | 
| `IncludeAuthorizationHeadersInOutputs` | Tekenreeks | Voor Logic apps waarmee [Azure Active Directory open verificatie (Azure AD OAuth) inschakelt](../logic-apps/logic-apps-securing-a-logic-app.md#enable-oauth) om toegang te verlenen voor binnenkomende oproepen naar een eind punt op basis van een aanvraag, neemt u de `Authorization` header uit het OAuth-toegangs token op in de trigger uitvoer. Zie voor meer informatie de [header ' Authorization ' in de trigger uitvoer van aanvragen](../logic-apps/logic-apps-securing-a-logic-app.md#include-auth-header). | Trigger <p>[Aanvraag](#request-trigger), <br>[HTTP-webhook](#http-webhook-trigger) | 
| `Sequential` | Tekenreeks | Voer voor elke herhalings herhaling een voor een uit in plaats van alle tegelijkertijd parallel. <p>Deze optie werkt op dezelfde manier als het instellen van de `runtimeConfiguration.concurrency.repetitions` eigenschap op `1` . U kunt beide eigenschappen instellen, maar niet beide. <p><p>Als u deze optie wilt instellen, raadpleegt u [' voor elke ' herhalen](#sequential-for-each).| Actie: <p>[Foreach](#foreach-action) | 
| `SingleInstance` | Tekenreeks | Voer de trigger voor elke logische app-instantie sequentieel uit en wacht totdat de eerdere actieve uitvoering is voltooid voordat het volgende logische app-exemplaar wordt geactiveerd. <p><p>Deze optie werkt op dezelfde manier als het instellen van de `runtimeConfiguration.concurrency.runs` eigenschap op `1` . U kunt beide eigenschappen instellen, maar niet beide. <p>Zie [instanties opeenvolgend activeren](#sequential-trigger)om deze optie in te stellen. | Alle triggers | 
||||

<a name="change-trigger-concurrency"></a>

### <a name="change-trigger-concurrency"></a>Gelijktijdigheid van triggers wijzigen

Standaard worden alle werk stroom exemplaren van logische apps tegelijkertijd uitgevoerd (gelijktijdig of parallel). Dit gedrag houdt in dat elke trigger instantie wordt gestart voordat het vorige actieve werk stroom exemplaar is voltooid. Het aantal gelijktijdig uitgevoerde instanties heeft echter een [standaard limiet](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Wanneer het aantal werk stroom exemplaren dat gelijktijdig wordt uitgevoerd deze limiet bereikt, moeten andere nieuwe instanties wachten om te worden uitgevoerd. Deze limiet helpt bij het bepalen van het aantal aanvragen dat door de back-end-systemen wordt ontvangen.

Wanneer u het gelijktijdigheids beheer van de trigger inschakelt, worden trigger exemplaren parallel uitgevoerd tot de [standaard limiet](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Als u deze limiet voor standaard gelijktijdigheid wilt wijzigen, kunt u de code weergave-editor of Logic Apps Designer gebruiken, omdat het wijzigen van de gelijktijdigheids instelling via de ontwerper de `runtimeConfiguration.concurrency.runs` eigenschap in de onderliggende trigger definitie toevoegt of bijwerkt en vice versa. Deze eigenschap bepaalt het maximum aantal nieuwe werk stroom exemplaren dat parallel kan worden uitgevoerd.

Hier volgen enkele overwegingen voor wanneer u gelijktijdigheid wilt inschakelen voor een trigger:

* Als gelijktijdigheid is ingeschakeld, is de [limiet voor SplitOn](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) aanzienlijk beperkt voor het afstellen van batch- [matrices](#split-on-debatch). Als het aantal items deze limiet overschrijdt, wordt de SplitOn-functionaliteit uitgeschakeld.

* U kunt gelijktijdigheid niet uitschakelen nadat u het gelijktijdigheids beheer hebt ingeschakeld.

* Wanneer gelijktijdigheid is ingeschakeld, kan een langlopende Logic-app-instantie ertoe leiden dat nieuwe logische app-exemplaren een wacht status invoeren. Deze status voor komt dat Azure Logic Apps nieuwe instanties maakt en er gebeurt zelfs wanneer het aantal gelijktijdige uitvoeringen kleiner is dan het opgegeven maximum aantal gelijktijdige uitvoeringen.

  * Als u deze status wilt onderbreken, annuleert u de eerste exemplaren die *nog worden uitgevoerd*.

    1. Selecteer **overzicht** in het menu van de logische app.

    1. Selecteer in de sectie **uitvoerings geschiedenis** het eerste exemplaar dat nog steeds actief is, bijvoorbeeld:

       ![Eerste uitgevoerd exemplaar selecteren](./media/logic-apps-workflow-actions-triggers/waiting-runs.png)

       > [!TIP]
       > Als u alleen exemplaren wilt weer geven die nog worden uitgevoerd, opent u de lijst **alle** en selecteert u **uitvoeren**.

    1. Selecteer **uitvoering annuleren** onder **Logic app run**.

       ![Het oudste uitgevoerde exemplaar zoeken](./media/logic-apps-workflow-actions-triggers/cancel-run.png)

  * Als u deze mogelijkheid wilt omzeilen, voegt u een time-out toe aan elke actie die deze uitvoeringen kan bevatten. Zie de [asynchrone duur wijzigen](#asynchronous-limits)als u in de code-editor werkt. Als u de ontwerp functie gebruikt, voert u de volgende stappen uit:

    1. Selecteer in de logische app, op de actie waar u een time-out wilt toevoegen, de knop met weglatings tekens (**...**) in de rechter bovenhoek en selecteer vervolgens **instellingen**.

       ![Actie-instellingen openen](./media/logic-apps-workflow-actions-triggers/action-settings.png)

    1. Geef onder **time-out** de time-outduur op in de [ISO 8601-indeling](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations).

       ![Time-outperiode opgeven](./media/logic-apps-workflow-actions-triggers/timeout.png)

* Als u de logische app opeenvolgend wilt uitvoeren, stelt u de gelijktijdigheid van de trigger in met `1` behulp van de code weergave-editor of de ontwerp functie. Zorg ervoor dat u niet ook de eigenschap van de trigger instelt `operationOptions` `SingleInstance` in de code weergave-editor. Anders krijgt u een validatie fout. Zie [instanties opeenvolgend activeren](#sequential-trigger)voor meer informatie.

#### <a name="edit-in-code-view"></a>Bewerken in de code weergave 

Voeg in de definitie van de onderliggende trigger de `runtimeConfiguration.concurrency.runs` eigenschap toe, die een waarde kan hebben van `1` tot `50` .

Hier volgt een voor beeld waarin de limieten voor gelijktijdige uitvoeringen tot 10 instanties worden beperkt:

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": 10
      }
   }
}
```

Zie runtime-configuratie- [instellingen](#runtime-config-options)voor meer informatie.

#### <a name="edit-in-logic-apps-designer"></a>Bewerken in Logic Apps Designer

1. Selecteer de knop met weglatings tekens (**...**) in de rechter bovenhoek van de trigger en selecteer vervolgens **instellingen**.

1. Stel onder **Gelijktijdigheids beheer** de **waarde** **in op aan**. 

1. Sleep de schuif regelaar **van de parallelle** hoogte naar de gewenste waarde. Als u de logische app opeenvolgend wilt uitvoeren, sleept u de waarde van de schuif regelaar naar **1**.

<a name="change-for-each-concurrency"></a>

### <a name="change-for-each-concurrency"></a>Wijzigen voor elke gelijktijdigheid

Standaard worden elke herhalings herhalingen op hetzelfde moment uitgevoerd (gelijktijdig of parallel) (voor elke). Dit gedrag houdt in dat elke iteratie wordt gestart voordat de vorige herhaling is voltooid. Het aantal gelijktijdig uitgevoerde herhalingen heeft echter een [standaard limiet](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Wanneer het aantal gelijktijdig uitgevoerde iteraties deze limiet bereikt, moeten andere herhalingen wachten om te worden uitgevoerd.

Als u de standaard limiet wilt wijzigen, kunt u de code weergave-editor of Logic Apps Designer gebruiken, omdat het wijzigen van de gelijktijdigheids instelling via de ontwerper de eigenschap toevoegt of bijwerkt `runtimeConfiguration.concurrency.repetitions` in de onderliggende actie definitie voor elke, en vice versa. Deze eigenschap bepaalt het maximum aantal iteraties dat parallel kan worden uitgevoerd.

> [!NOTE] 
> Als u de actie voor elke wilt sequentieel uitvoert met behulp van de ontwerp functie of de code weergave-editor, stelt u de eigenschap van de actie niet `operationOptions` `Sequential` in op de code weergave-editor. Anders krijgt u een validatie fout. Zie voor meer informatie [Run ' voor elke ' herhalen](#sequential-for-each).

#### <a name="edit-in-code-view"></a>Bewerken in de code weergave 

Voeg in de onderliggende definitie ' voor elke ' de eigenschap toe, of werk deze bij `runtimeConfiguration.concurrency.repetitions` , die een waarde kan hebben van `1` en `50` .

Hier volgt een voor beeld waarin het aantal gelijktijdige uitvoeringen tot 10 iteraties wordt beperkt:

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "runtimeConfiguration": {
      "concurrency": {
         "repetitions": 10
      }
   }
}
```

Zie runtime-configuratie- [instellingen](#runtime-config-options)voor meer informatie.

#### <a name="edit-in-logic-apps-designer"></a>Bewerken in Logic Apps Designer

1. Selecteer in de rechter bovenhoek van **elke** actie de weglatings tekens (**...**) en selecteer vervolgens **instellingen**.

1. Stel onder **Gelijktijdigheids beheer** gelijktijdigheids **beheer** **in op aan.**

1. Sleep de schuif regelaar **van de parallelle** hoogte naar de gewenste waarde. Als u de logische app opeenvolgend wilt uitvoeren, sleept u de waarde van de schuif regelaar naar **1**.

<a name="change-waiting-runs"></a>

### <a name="change-waiting-runs-limit"></a>De limiet voor het uitvoeren van een verwerking wijzigen

Standaard worden alle werk stroom exemplaren van logische apps tegelijkertijd uitgevoerd (gelijktijdig of parallel). Dit gedrag houdt in dat elke trigger instantie wordt gestart voordat het vorige actieve werk stroom exemplaar is voltooid. Het aantal gelijktijdig uitgevoerde instanties heeft echter een [standaard limiet](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Wanneer het aantal werk stroom exemplaren dat gelijktijdig wordt uitgevoerd deze limiet bereikt, moeten andere nieuwe instanties wachten om te worden uitgevoerd.

Het aantal wachtende uitvoeringen heeft ook een [standaard limiet](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Wanneer het aantal wachtende uitvoeringen deze limiet bereikt, accepteert de Logic Apps-Engine geen nieuwe uitvoeringen meer. De aanvraag en webhook-triggers retour neren 429 fouten en terugkerende triggers beginnen met het overs laan van polling-pogingen.

U kunt niet alleen [de standaard limiet voor gelijktijdige activeringen wijzigen](#change-trigger-concurrency), maar u kunt ook de standaard limiet voor het uitvoeren van een wacht tijd wijzigen. Voeg in de definitie van de onderliggende trigger de `runtimeConfiguration.concurrency.maximumWaitingRuns` eigenschap toe, die een waarde kan hebben van `1` tot `100` .

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "runtimeConfiguration": {
      "concurrency": {
         "maximumWaitingRuns": 50
      }
   }
}
```

Zie runtime-configuratie- [instellingen](#runtime-config-options)voor meer informatie.

<a name="sequential-trigger"></a>

### <a name="trigger-instances-sequentially"></a>Instanties opeenvolgend activeren

Als u wilt dat elke werk stroom instantie van de logische app pas wordt uitgevoerd nadat het vorige exemplaar is voltooid, stelt u de trigger in op volg orde van uitvoering. U kunt de code weergave-editor of Logic Apps Designer gebruiken omdat door het wijzigen van de gelijktijdigheids instelling via Designer ook de `runtimeConfiguration.concurrency.runs` eigenschap in de onderliggende trigger definitie wordt toegevoegd of bijgewerkt en vice versa.

> [!NOTE] 
> Als u instelt dat een trigger opeenvolgend moet worden uitgevoerd met behulp van de ontwerp functie of de code weergave-editor, stelt u de eigenschap van de trigger niet `operationOptions` `Sequential` in op de code weergave-editor. Anders krijgt u een validatie fout. 

#### <a name="edit-in-code-view"></a>Bewerken in de code weergave

Stel in de trigger definitie een van deze eigenschappen in, maar niet beide. 

Stel de `runtimeConfiguration.concurrency.runs` eigenschap in op `1` :

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": 1
      }
   }
}
```

*of*

Stel de `operationOptions` eigenschap in op `SingleInstance` :

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "operationOptions": "SingleInstance"
}
```

Zie [runtime configuratie-instellingen](#runtime-config-options) en [bewerkings opties](#operation-options)voor meer informatie.

#### <a name="edit-in-logic-apps-designer"></a>Bewerken in Logic Apps Designer

1. Selecteer de knop met weglatings tekens (**...**) in de rechter bovenhoek van de trigger en selecteer vervolgens **instellingen**.

1. Stel onder **Gelijktijdigheids beheer** de **waarde** **in op aan**. 

1. Sleep de schuif regelaar **van de parallelle graad** naar het getal `1` . 

<a name="sequential-for-each"></a>

### <a name="run-for-each-loops-sequentially"></a>Voer ' voor elke ' sequentieel herhalingen uit

Als u een lus voor elke herhaling alleen wilt uitvoeren nadat de vorige herhaling is voltooid, stelt u de actie voor elke uit op volg orde. U kunt de code weergave-editor of Logic Apps Designer gebruiken omdat door het wijzigen van de gelijktijdigheid van de actie via Designer ook de `runtimeConfiguration.concurrency.repetitions` eigenschap in de onderliggende actie definitie wordt toegevoegd of bijgewerkt en vice versa.

> [!NOTE] 
> Wanneer u een ' voor elke ' actie instelt om opeenvolgend te worden uitgevoerd met behulp van de ontwerp-of code weergave-editor, stelt u de eigenschap van de actie niet `operationOptions` `Sequential` in op de code weergave-editor. Anders krijgt u een validatie fout. 

#### <a name="edit-in-code-view"></a>Bewerken in de code weergave

Stel in de definitie van de actie een van deze eigenschappen in, maar niet beide. 

Stel de `runtimeConfiguration.concurrency.repetitions` eigenschap in op `1` :

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "runtimeConfiguration": {
      "concurrency": {
         "repetitions": 1
      }
   }
}
```

*of*

Stel de `operationOptions` eigenschap in op `Sequential` :

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "operationOptions": "Sequential"
}
```

Zie [runtime configuratie-instellingen](#runtime-config-options) en [bewerkings opties](#operation-options)voor meer informatie.

#### <a name="edit-in-logic-apps-designer"></a>Bewerken in Logic Apps Designer

1. Selecteer de knop met weglatings tekens (**...**) in de rechter bovenhoek **van elke** actie en selecteer vervolgens **instellingen**.

1. Stel onder **Gelijktijdigheids beheer** gelijktijdigheids **beheer** **in op aan.**

1. Sleep de schuif regelaar **van de parallelle graad** naar het getal `1` .

<a name="disable-asynchronous-pattern"></a>

### <a name="run-actions-in-a-synchronous-operation-pattern"></a>Acties uitvoeren in een synchrone bewerkings patroon

Standaard volgen de acties HTTP Action en APIConnection in Azure Logic Apps het standaard [*asynchrone bewerkings patroon*](/azure/architecture/patterns/async-request-reply), terwijl de reactie actie het *synchrone bewerkings patroon* volgt. Het asynchrone patroon geeft aan dat nadat een actie een aanvraag naar het opgegeven eind punt, de service, het systeem of de API heeft aangeroepen of verzonden, de ontvanger direct het antwoord ["202 accepted"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.3) retourneert. Met deze code wordt bevestigd dat de ontvanger de aanvraag heeft geaccepteerd, maar nog niet is verwerkt. Het antwoord kan een `location` kop bevatten die de URL en een vernieuwings-id specificeert die de oproepende functie kan gebruiken om voortdurend te controleren of de status van de asynchrone aanvraag, totdat de ontvanger stopt met de verwerking en een ["200 OK"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.1) -antwoord of ander niet-202-antwoord retourneert. Zie asynchrone micro service- [integratie dwingt micro service autonom](/azure/architecture/microservices/design/interservice-communication#synchronous-versus-asynchronous-messaging)af voor meer informatie.

* In de ontwerp functie van de logische app, de HTTP-actie, APIConnection-acties en reactie actie de **asynchrone patroon** instelling. Als deze instelling is ingeschakeld, wordt de aanroeper niet gewacht totdat de verwerking is voltooid en kan de volgende actie worden voortgezet, maar blijft de status controleren totdat de verwerking stopt. Als deze instelling is uitgeschakeld, wordt hiermee aangegeven dat de aanroeper wacht totdat de verwerking is voltooid voordat u verdergaat met de volgende actie. Voer de volgende stappen uit om deze instelling te vinden:

  1. Selecteer op de titel balk van de HTTP-actie de knop met weglatings tekens (**...**) om de actie-instellingen te openen.

  1. Zoek de instelling voor **asynchroon patroon** .

     ![Instelling voor asynchroon patroon](./media/logic-apps-workflow-actions-triggers/asynchronous-pattern-setting.png)

* In de definitie van de onderliggende JavaScript Object Notation van de actie (JSON) volgen de acties HTTP Action en APIConnection impliciet het patroon van de asynchrone bewerking.

In sommige scenario's wilt u mogelijk een actie uitvoeren in plaats daarvan het synchrone patroon te volgen. Als u bijvoorbeeld de HTTP-actie gebruikt, wilt u het volgende doen:

* [HTTP-time-outs voor langlopende taken voor komen](../connectors/connectors-native-http.md#avoid-http-timeouts)
* [Controleren van locatie headers uitschakelen](../connectors/connectors-native-http.md#disable-location-header-check)

In dergelijke gevallen kunt u een actie synchroon uitvoeren met behulp van de volgende opties:

* Vervang de polling-versie van die actie door een webhook-versie, indien beschikbaar.

* Schakel een van de volgende opties uit om het asynchrone gedrag van de actie uit te scha kelen:

  * [Schakel de instelling **asynchroon patroon** uit](#turn-off-asynchronous-pattern-setting)in de ontwerp functie voor logische apps.

  * Voeg in de onderliggende JSON-definitie van de actie [de `"DisableAsyncPattern"` optie bewerking toe](#add-disable-async-pattern-option).

<a name="turn-off-asynchronous-pattern-setting"></a>

#### <a name="turn-off-asynchronous-pattern-setting"></a>Instelling voor **asynchroon patroon** uitschakelen

1. Selecteer in de ontwerp functie van de logische app op de titel balk van de actie de knop met weglatings tekens (**...**) om de actie-instellingen te openen.

1. Zoek de instelling voor **asynchroon patroon** , schakel de instelling in op **uit** als deze optie is ingeschakeld en selecteer **gereed**.

   ![Instelling voor asynchroon patroon uitschakelen](./media/logic-apps-workflow-actions-triggers/disable-asynchronous-pattern-setting.png)

<a name="add-disable-async-pattern-option"></a>

#### <a name="disable-asynchronous-pattern-in-actions-json-definition"></a>Asynchroon patroon in de JSON-definitie van de actie uitschakelen

Voeg in de onderliggende JSON-definitie van de actie de [eigenschap ' operationOptions](#operation-options) ' toe en stel deze in `"DisableAsyncPattern"` onder de `"inputs"` sectie actie, bijvoorbeeld:

```json
"<some-long-running-action>": {
   "type": "Http",
   "inputs": { "<action-inputs>" },
   "operationOptions": "DisableAsyncPattern",
   "runAfter": {}
}
```

<a name="authenticate-triggers-actions"></a>

## <a name="authenticate-triggers-and-actions"></a>Triggers en acties verifiëren

HTTP-en HTTPS-eind punten ondersteunen verschillende soorten verificatie. Op basis van de trigger of actie die u gebruikt om uitgaande aanroepen of aanvragen voor toegang tot deze eind punten te maken, kunt u kiezen uit verschillende bereiken van verificatie typen. Zie [verificatie toevoegen aan uitgaande oproepen](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de taal van de [werk stroom definitie](../logic-apps/logic-apps-workflow-definition-language.md)
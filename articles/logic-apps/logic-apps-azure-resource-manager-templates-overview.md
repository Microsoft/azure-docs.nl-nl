---
title: 'Overzicht: implementatie voor Azure Logic Apps automatiseren'
description: Meer informatie over Azure Resource Manager sjablonen voor het automatiseren van de implementatie voor Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: article
ms.date: 11/06/2020
ms.openlocfilehash: ec72431d927fd59677075e7adfdf7df171574882
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104772940"
---
# <a name="overview-automate-deployment-for-azure-logic-apps-by-using-azure-resource-manager-templates"></a>Overzicht: de implementatie voor Azure Logic Apps automatiseren met behulp van Azure Resource Manager sjablonen

Wanneer u klaar bent om het maken en implementeren van uw logische app te automatiseren, kunt u de onderliggende werk stroom definitie van uw logische app uitbreiden naar een [Azure Resource Manager sjabloon](../azure-resource-manager/management/overview.md). Met deze sjabloon worden de infra structuur, bronnen, para meters en andere gegevens gedefinieerd voor het inrichten en implementeren van uw logische app. Door para meters te definiëren voor waarden die variëren tijdens de implementatie, ook wel bekend als *parameterizing*, kunt u regel matig Logic Apps implementeren op basis van verschillende implementatie behoeften.

Als u bijvoorbeeld in omgevingen implementeert voor ontwikkeling, testen en productie, gebruikt u waarschijnlijk verschillende verbindings reeksen voor elke omgeving. U kunt sjabloon parameters declareren die verschillende verbindings reeksen accepteren en deze teken reeksen vervolgens opslaan in een afzonderlijk [parameter bestand](../azure-resource-manager/templates/parameter-files.md). Op die manier kunt u deze waarden wijzigen zonder dat u de sjabloon hoeft bij te werken en opnieuw te implementeren. Voor scenario's waarbij u parameter waarden hebt die gevoelig zijn of moeten worden beveiligd, zoals wacht woorden en geheimen, kunt u deze waarden opslaan in [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md) en uw para meters bestand laten ophalen die waarden bevatten. In deze scenario's zou u echter opnieuw implementeren om de huidige waarden op te halen.

In dit overzicht worden de kenmerken van een resource manager-sjabloon beschreven die een werk stroom definitie van een logische app bevat. Zowel de sjabloon als uw werk stroom definitie gebruiken JSON-syntaxis, maar er zijn enkele verschillen, omdat de definitie van de werk stroom ook het [taal schema voor de werk stroom definitie](../logic-apps/logic-apps-workflow-definition-language.md)volgt. Sjabloon expressies en werk stroom definitie-expressies verschillen bijvoorbeeld in de manier waarop ze [verwijzen naar para meters](#parameter-references) en de waarden die ze kunnen accepteren.

> [!TIP]
> Voor de eenvoudigste manier om een geldige sjabloon voor logische apps met para meters te maken die het meest geschikt is voor implementatie, gebruikt u Visual Studio (gratis Community-editie of hoger) en de Azure Logic Apps-Hulpprogram Ma's voor Visual Studio. U kunt vervolgens [uw logische app maken in Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) of [een bestaande logische app van Azure naar Visual Studio downloaden](../logic-apps/manage-logic-apps-with-visual-studio.md).
>
> U kunt ook sjablonen voor logische apps maken met behulp van [Azure PowerShell met de module LogicAppTemplate](../logic-apps/logic-apps-create-azure-resource-manager-templates.md#azure-powershell).

In de logische app voor voor beelden in dit onderwerp wordt gebruikgemaakt van een [Office 365 Outlook-trigger](/connectors/office365/) die wordt geactiveerd wanneer er een nieuwe e-mail binnenkomt en een [Azure Blob Storage actie](/connectors/azureblob/) die een BLOB maakt voor de hoofd tekst van het e-mail bericht en die BLOB uploadt naar een Azure storage-container. In de voor beelden ziet u ook hoe u waarden para meters die variëren tijdens de implementatie.

Zie de volgende onderwerpen voor meer informatie over Resource Manager-sjablonen:

* [Structuur en syntaxis van Azure Resource Manager sjabloon](../azure-resource-manager/templates/template-syntax.md)
* [Best practices voor Azure Resource Manager-sjablonen](../azure-resource-manager/templates/template-best-practices.md)
* [Azure Resource Manager-sjablonen voor consistentie van de cloud ontwikkelen](../azure-resource-manager/templates/templates-cloud-consistency.md)

Zie [resource typen van micro soft. Logic](/azure/templates/microsoft.logic/allversions)voor sjabloon resource gegevens die specifiek zijn voor Logic apps, integratie accounts, integratie-account artefacten en integratie service omgevingen.

Voor voor beelden van Logic app-sjablonen raadpleegt u deze voor beelden:

* [Volledige sjabloon](#full-example-template) die wordt gebruikt voor de voor beelden van dit onderwerp
* [Voorbeeld sjabloon voor Quick](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create) start-app in github

Voor de Logic Apps REST API begint u met het [overzicht Azure Logic Apps rest API](/rest/api/logic).

<a name="template-structure"></a>

## <a name="template-structure"></a>Sjabloonstructuur

Op het hoogste niveau volgt een resource manager-sjabloon deze structuur, die volledig wordt beschreven in het onderwerp [Azure Resource Manager sjabloon structuur en syntaxis](../azure-resource-manager/templates/template-syntax.md) :

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "functions": [],
   "resources": [],
   "outputs": {}
}
```

Voor een sjabloon voor een logische app werkt u voornamelijk met deze sjabloon objecten:

| Kenmerk | Beschrijving |
|-----------|-------------|
| `parameters` | Declareert de [sjabloon parameters](../azure-resource-manager/templates/template-syntax.md#parameters) voor het accepteren van de waarden die moeten worden gebruikt bij het maken en aanpassen van resources voor implementatie in Azure. Deze para meters accepteren bijvoorbeeld de waarden voor de naam en locatie, verbindingen en andere resources die nodig zijn voor de implementatie van uw logische app. U kunt deze parameter waarden opslaan in een [parameter bestand](#template-parameter-files), dat later in dit onderwerp wordt beschreven. Zie [para meters-Resource Manager-sjabloon structuur en syntaxis](../azure-resource-manager/templates/template-syntax.md#parameters)voor algemene informatie. |
| `resources` | Definieert de [resources](../azure-resource-manager/templates/template-syntax.md#resources) voor het maken of bijwerken en implementeren van een Azure-resource groep, zoals uw logische app, verbindingen, Azure Storage-accounts, enzovoort. Zie voor algemene informatie [bronnen-Resource Manager-sjabloon structuur en-syntaxis](../azure-resource-manager/templates/template-syntax.md#resources). |
||||

De logische app-sjabloon maakt gebruik van de volgende bestands indeling:

**<*Logic-app-naam* # C0.jsop**

> [!IMPORTANT]
> De sjabloon syntaxis is hoofdletter gevoelig, dus zorg ervoor dat u een consistente behuizing gebruikt. 

<a name="template-parameters"></a>

## <a name="template-parameters"></a>Sjabloonparameters

Een sjabloon voor een logische app heeft meerdere `parameters` objecten die op verschillende niveaus bestaan en die verschillende functies uitvoeren. U kunt bijvoorbeeld op het hoogste niveau [sjabloon parameters](../azure-resource-manager/templates/template-syntax.md#parameters) declareren voor de waarden die u tijdens de implementatie wilt accepteren en gebruiken bij het maken en implementeren van resources in azure, bijvoorbeeld:

* Uw logische app
* Verbindingen die uw logische toepassingen gebruiken om toegang te krijgen tot andere services en systemen via [beheerde connectors](../connectors/apis-list.md)
* Andere resources die door uw logische app moeten worden geïmplementeerd

  Als uw logische app bijvoorbeeld gebruikmaakt van een [integratie account](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) voor Business-to-Business (B2B)-scenario's, declareert het object op het hoogste niveau van de sjabloon `parameters` de para meter waarmee de resource-id voor het integratie account wordt geaccepteerd.

Hier volgt de algemene structuur en syntaxis voor een parameter definitie, die volledig wordt beschreven door [para meters-Resource Manager-sjabloon structuur en syntaxis](../azure-resource-manager/templates/template-syntax.md#parameters):

```json
"<parameter-name>": {
   "type": "<parameter-type>",
   "defaultValue": <default-parameter-value>,
   <other-parameter-attributes>,
   "metadata": {
      "description": "<parameter-description>"
   }
},
```

In dit voor beeld worden alleen de sjabloon parameters weer gegeven voor de waarden die worden gebruikt voor het maken en implementeren van deze resources in Azure:

* Naam en locatie voor uw logische app
* ID die moet worden gebruikt voor een integratie account dat is gekoppeld aan de logische app

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "defaultValue": "MyLogicApp",
         "metadata": {
            "description": "The resource name for the logic app"
         }
      },
      "LogicAppLocation": {
         "type": "string",
         "minLength": 1,
         "defaultValue": "[resourceGroup().location]",
         "metadata": {
            "description": "The resource location for the logic app"
         }
      },
      "LogicAppIntegrationAccount": {
         "type":"string",
         "minLength": 1,
         "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/fabrikam-integration-account-rg/providers/Microsoft.Logic/integrationAccounts/fabrikam-integration-account",
         "metadata": {
            "description": "The ID to use for the integration account"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [],
   "outputs": {}
}
```

Met uitzonde ring van para meters die gevoelige waarden verwerken of moeten worden beveiligd, zoals gebruikers namen, wacht woorden en geheimen, bevatten alle deze para meters `defaultValue` kenmerken, hoewel in sommige gevallen de standaard waarden leeg zijn. De implementatie waarden die moeten worden gebruikt voor deze sjabloon parameters worden opgegeven door het voorbeeld [parameter bestand](#template-parameter-files) dat verderop in dit onderwerp wordt beschreven.

Zie de volgende onderwerpen voor meer informatie over het beveiligen van sjabloon parameters:

* [Beveiligings aanbevelingen voor sjabloon parameters](../azure-resource-manager/templates/template-best-practices.md#parameters)
* [Beveiliging verbeteren voor sjabloon parameters](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)
* [Beveiligde parameter waarden door geven Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

Andere sjabloon objecten verwijzen vaak naar sjabloon parameters, zodat ze de waarden kunnen gebruiken die de sjabloon parameters door geven, bijvoorbeeld:

* Het [resources-object van uw sjabloon](#template-resources), dat verderop in dit onderwerp wordt beschreven, definieert elke resource in azure die u wilt maken en implementeren, zoals de [resource definitie van de logische app](#logic-app-resource-definition). Deze resources gebruiken vaak parameter waarden van de sjabloon, zoals de naam en locatie en verbindings gegevens van uw logische app.

* Op een dieper niveau in de resource definitie van de logische app declareert het [object para meters van de werk stroom definitie](#workflow-definition-parameters) para meters voor de waarden die moeten worden gebruikt bij de runtime van de logische app. U kunt bijvoorbeeld para meters voor de werk stroom definitie declareren voor de gebruikers naam en het wacht woord die een HTTP-trigger gebruikt voor verificatie. Als u de waarden voor de para meters voor de werk stroom definitie wilt opgeven, gebruikt u het `parameters` object *buiten* uw werk stroom definitie, maar nog *in* de resource definitie van de logische app. In dit buitenste `parameters` object kunt u verwijzen naar eerder gedeclareerde sjabloon parameters, waarmee waarden kunnen worden geaccepteerd tijdens de implementatie vanuit een parameter bestand.

Bij het verwijzen naar para meters, gebruiken sjabloon expressies en-functies een andere syntaxis en gedragen zich anders dan expressies en functies van de werk stroom definitie. Zie [verwijzingen naar para meters](#parameter-references) verderop in dit onderwerp voor meer informatie over deze verschillen.

<a name="best-practices-template-parameters"></a>

## <a name="best-practices---template-parameters"></a>Aanbevolen procedures-sjabloon parameters

Hier volgen enkele aanbevolen procedures voor het definiëren van para meters:

* Declareer alleen para meters voor waarden die variëren, afhankelijk van uw implementatie behoeften. Declareer geen para meters voor waarden die hetzelfde blijven in verschillende implementatie vereisten.

* Neem het `defaultValue` kenmerk op, waarmee lege waarden kunnen worden opgegeven voor alle para meters, met uitzonde ring van waarden die gevoelig zijn of moeten worden beveiligd. Gebruik altijd beveiligde para meters voor gebruikers namen, wacht woorden en geheimen. Volg de richt lijnen in de volgende onderwerpen om gevoelige parameter waarden te verbergen of te beveiligen:

  * [Beveiligings aanbevelingen voor sjabloon parameters](../azure-resource-manager/templates/template-best-practices.md#parameters)

  * [Beveiliging verbeteren voor sjabloon parameters](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)

  * [Beveiligde parameter waarden door geven Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

* Als u de namen van sjabloon parameters wilt onderscheiden van para meters van werk stroom definities, kunt u beschrijvende sjabloon parameter namen gebruiken, bijvoorbeeld: `TemplateFabrikamPassword`

Zie [Aanbevolen procedures voor sjabloon parameters](../azure-resource-manager/templates/template-best-practices.md#parameters)voor meer aanbevolen procedures voor sjablonen.

<a name="template-parameter-files"></a>

## <a name="template-parameters-file"></a>Sjabloon parameter bestand

Als u de waarden voor sjabloon parameters wilt opgeven, slaat u deze waarden op in een [parameter bestand](../azure-resource-manager/templates/parameter-files.md). Op die manier kunt u verschillende parameter bestanden gebruiken op basis van uw implementatie behoeften. Dit is de indeling van de bestands naam die moet worden gebruikt:

* Bestands naam van sjabloon van logische app: **< *Logic-app-name* # C0.jsop**
* Bestands naam van de para meters: **< *Logic-app-name* # C0.parameters.jsop**

Hier volgt de structuur in het parameter bestand, met daarin een sleutel kluis referentie voor [het door geven van een beveiligde parameter waarde met Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md):

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "<parameter-name-1>": {
         "value": "<parameter-value>"
      },
      "<parameter-name-2>": {
         "value": "<parameter-value>"
      },
      "<secured-parameter-name>": {
         "reference": {
            "keyVault": {
               "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/<key-vault-name>"
            },
            "secretName: "<secret-name>"
         }
      },
      <other-parameter-values>
   }
}
```

In dit voorbeeld parameter bestand worden de waarden opgegeven voor de sjabloon parameters die eerder in dit onderwerp zijn gedeclareerd:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "LogicAppName": {
         "value": "Email-Processor-Logic-App"
      },
      "LogicAppLocation": {
         "value": "westeurope"
      }
   }
}
```

<a name="template-resources"></a>

## <a name="template-resources"></a>Sjabloonresources

Uw sjabloon heeft een `resources` -object, een matrix die definities bevat voor elke resource die u wilt maken en implementeren in azure, zoals de [resource definitie van de logische app](#logic-app-resource-definition), de [definities van verbindings bronnen](#connection-resource-definitions)en andere bronnen die uw logische app nodig heeft voor implementatie.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Start connection resource definitions
      {
         <connection-resource-definition-1>
      },
      {
         <connection-resource-definition-2>
      }
   ],
   "outputs": {}
}
```

> [!NOTE]
> Sjablonen kunnen resource definities bevatten voor meerdere Logic apps. Zorg er dus voor dat alle logische app-resources dezelfde Azure-resource groep opgeven. Wanneer u de sjabloon implementeert in een Azure-resource groep met behulp van Visual Studio, wordt u gevraagd welke logische app u wilt openen. Het is ook mogelijk dat uw Azure-resource groep-project meer dan één sjabloon bevat. Zorg er dus voor dat u het juiste parameter bestand selecteert wanneer u hierom wordt gevraagd.

<a name="view-resource-definitions"></a>

### <a name="view-resource-definitions"></a>Resource definities weer geven

Als u de resource definities voor alle resources in een Azure-resource groep wilt weer geven, [downloadt u uw logische app van Azure in Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md). Dit is de eenvoudigste manier om een geldige sjabloon voor logische sjablonen met para meters te maken die het meest geschikt is voor implementatie.

Zie de volgende onderwerpen voor algemene informatie over sjabloon bronnen en hun kenmerken:

* [Resources-structuur en syntaxis van Resource Manager-sjabloon](../azure-resource-manager/templates/template-syntax.md#resources)
* [Aanbevolen procedures voor sjabloon resources](../azure-resource-manager/templates/template-best-practices.md#resources)

<a name="logic-app-resource-definition"></a>

### <a name="logic-app-resource-definition"></a>Resource definitie van Logic app

De [werk stroom resource definitie](/azure/templates/microsoft.logic/workflows) van uw logische app in een sjabloon begint met het `properties` -object, dat deze informatie bevat:

* De status van uw logische app tijdens de implementatie
* De ID voor een integratie account dat wordt gebruikt door uw logische app
* De werk stroom definitie van uw logische app
* Een `parameters` object waarmee de waarden worden ingesteld die tijdens runtime worden gebruikt
* Andere resource gegevens over uw logische app, zoals naam, type, locatie, wille keurige runtime configuratie-instellingen, enzovoort

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            "state": "<Enabled or Disabled>",
            "integrationAccount": {
               "id": "[parameters('LogicAppIntegrationAccount')]" // Template parameter reference
            },
            "definition": {<workflow-definition>},
            "parameters": {<workflow-definition-parameter-values>},
            "accessControl": {},
            "runtimeConfiguration": {}
         },
         "name": "[parameters('LogicAppName')]", // Template parameter reference
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]", // Template parameter reference
         "tags": {
           "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "dependsOn": [
         ]
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

Dit zijn de kenmerken die specifiek zijn voor de resource definitie van de logische app:

| Kenmerk | Vereist | Type | Beschrijving |
|-----------|----------|------|-------------|
| `state` | Ja | Tekenreeks | De status van uw logische app bij implementatie waarbij `Enabled` uw logische app Live is en `Disabled` betekent dat uw logische app niet actief is. Als u bijvoorbeeld niet klaar bent voor uw logische app om live te gaan, maar een concept versie wilt implementeren, kunt u de optie gebruiken `Disabled` . |
| `integrationAccount` | No | Object | Als uw logische app een integratie account gebruikt dat artefacten voor Business-to-Business-scenario's (B2B) opslaat, bevat dit object het `id` kenmerk, waarmee de id voor het integratie account wordt opgegeven. |
| `definition` | Ja | Object | De onderliggende werk stroom definitie van uw logische app, die hetzelfde is als het object dat wordt weer gegeven in de code weergave, en dat volledig wordt beschreven in het onderwerp [schema verwijzing voor werk stroom definitie taal](../logic-apps/logic-apps-workflow-definition-language.md) . In deze werk stroom definitie `parameters` declareert het object para meters voor de waarden die moeten worden gebruikt in de runtime van de logische app. Zie [werk stroom definitie en-para meters](#workflow-definition-parameters)voor meer informatie. <p><p>Als u de kenmerken in de werk stroom definitie van de logische app wilt weer geven, schakelt u over naar de code weergave in de Azure Portal of Visual Studio of gebruikt u een hulp programma zoals [Azure resource Explorer](https://resources.azure.com). |
| `parameters` | No | Object | De [waarden van de werk stroom definitie parameter](#workflow-definition-parameters) die moeten worden gebruikt tijdens de runtime van de logische app. De parameter definities voor deze waarden worden weer gegeven in het [Parameter object van uw werk stroom definitie](#workflow-definition-parameters). Als uw logische app [beheerde connectors](../connectors/apis-list.md) gebruikt voor toegang tot andere services en systemen, bevat dit object ook een `$connections` object waarmee de verbindings waarden worden ingesteld die tijdens runtime worden gebruikt. |
| `accessControl` | No | Object | Voor het opgeven van beveiligings kenmerken voor uw logische app, zoals het beperken van IP-toegang tot het aanvragen van triggers of het uitvoeren van de invoer en uitvoer van de geschiedenis. Zie [veilige toegang tot Logic apps](../logic-apps/logic-apps-securing-a-logic-app.md)voor meer informatie. |
| `runtimeConfiguration` | No | Object | Voor het opgeven van `operationOptions` eigenschappen die bepalen hoe uw logische app zich tijdens runtime bevindt. U kunt bijvoorbeeld uw logische app uitvoeren in de [modus voor hoge door Voer](../logic-apps/logic-apps-limits-and-config.md#run-high-throughput-mode). |
|||||

Zie [resource typen micro soft. Logic](/azure/templates/microsoft.logic/allversions)voor meer informatie over resource definities voor deze Logic apps-objecten:

* [Definitie van werk stroom resource](/azure/templates/microsoft.logic/workflows)
* [Resource definitie van de integratie service omgeving](/azure/templates/microsoft.logic/integrationserviceenvironments)
* [Resource definitie van beheerde API voor Integration service Environment](/azure/templates/microsoft.logic/integrationserviceenvironments/managedapis)

* [Resource definitie van integratie account](/azure/templates/microsoft.logic/integrationaccounts)

* Artefacten van integratie-account:

  * [Definitie van de bron van de overeenkomst](/azure/templates/microsoft.logic/integrationaccounts/agreements)

  * [Assembly-resource definitie](/azure/templates/microsoft.logic/integrationaccounts/assemblies)

  * [Resource definitie voor batch configuratie](/azure/templates/microsoft.logic/integrationaccounts/batchconfigurations)

  * [Certificaat bron definitie](/azure/templates/microsoft.logic/integrationaccounts/certificates)

  * [Resource definitie toewijzen](/azure/templates/microsoft.logic/integrationaccounts/maps)

  * [Bron definitie van de partner](/azure/templates/microsoft.logic/integrationaccounts/partners)

  * [Schema resource definitie](/azure/templates/microsoft.logic/integrationaccounts/schemas)

  * [Bron definitie voor de sessie](/azure/templates/microsoft.logic/integrationaccounts/sessions)

<a name="workflow-definition-parameters"></a>

## <a name="workflow-definition-and-parameters"></a>Werk stroom definitie en-para meters

De werk stroom definitie van uw logische app wordt weer gegeven in het `definition` object dat wordt weer gegeven in het object in de `properties` resource definitie van de logische app. Dit `definition` object is hetzelfde object dat wordt weer gegeven in de code weergave en dat volledig wordt beschreven in het onderwerp [schema verwijzing voor werk stroom definitie taal](../logic-apps/logic-apps-workflow-definition-language.md) . Uw werk stroom definitie bevat een binnenste `parameters` declaratie object waar u nieuwe para meters voor de waarden kunt definiëren die worden gebruikt door uw werk stroom definitie tijdens runtime. U kunt vervolgens naar deze para meters in de trigger of acties in uw werk stroom verwijzen. Dit `parameters` object is standaard leeg, tenzij uw logische app verbindingen met andere services en systemen via [beheerde connectors](../connectors/apis-list.md)maakt.

Als u de waarden voor de para meters voor de werk stroom definitie wilt instellen, gebruikt u het `parameters` object *buiten* uw werk stroom definitie, maar nog *in* de resource definitie van de logische app. In dit buitenste `parameters` object kunt u vervolgens verwijzen naar uw eerder gedeclareerde sjabloon parameters, waarmee waarden kunnen worden geaccepteerd tijdens de implementatie vanuit een parameter bestand.

> [!TIP]
>
> Als best practice, kunt u niet rechtstreeks verwijzen naar sjabloon parameters, die tijdens de implementatie worden geëvalueerd, vanuit de werk stroom definitie. Declareer in plaats daarvan een werk stroom definitie parameter, die u vervolgens kunt instellen in het `parameters` object *buiten* uw werk stroom definitie, maar nog steeds *binnen* de resource definitie van de logische app. Zie [verwijzingen naar para meters](#parameter-references)voor meer informatie.

Deze syntaxis laat zien waar u para meters kunt declareren op zowel de sjabloon-als de werk stroom definitie niveaus, waarbij u deze parameter waarden kunt instellen door te verwijzen naar de sjabloon en de para meters van de werk stroom definitie:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "<template-parameter-name>": {
         "type": "<parameter-type>",
         "defaultValue": "<parameter-default-value>",
         "metadata": {
            "description": "<parameter-description>"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <other-logic-app-resource-properties>,
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {<action-definitions>},
               // Workflow definition parameters
               "parameters": {
                  "<workflow-definition-parameter-name>": {
                     "type": "<parameter-type>",
                     "defaultValue": "<parameter-default-value>",
                     "metadata": {
                        "description": "<parameter-description>"
                     }
                  }
               },
               "triggers": {
                  "<trigger-name>": {
                     "type": "<trigger-type>",
                     "inputs": {
                         // Workflow definition parameter reference
                         "<attribute-name>": "@parameters('<workflow-definition-parameter-name')"
                     }
                  }
               },
               <...>
            },
            // Workflow definition parameter value
            "parameters": {
               "<workflow-definition-parameter-name>": { 
                  "value": "[parameters('<template-parameter-name>')]"
               }
            },
            "accessControl": {}
         },
         <other-logic-app-resource-definition-attributes>
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

<a name="secure-workflow-definition-parameters"></a>

### <a name="secure-workflow-definition-parameters"></a>Para meters voor veilige werk stroom definitie

Voor een werk stroom definitie parameter die gevoelige informatie, wacht woorden, toegangs sleutels of geheimen verwerkt tijdens runtime, declareert of bewerkt u de para meter om het- `securestring` of- `secureobject` parameter type te gebruiken. U kunt in de definitie van uw werk stroom verwijzen naar deze para meter. Op het hoogste niveau van de sjabloon declareert u een para meter met hetzelfde type voor het afhandelen van deze gegevens tijdens de implementatie.

Als u de waarde voor de para meter voor de werk stroom definitie wilt instellen, gebruikt u het `parameters` object dat *zich buiten* uw werk stroom definitie bevindt, maar nog *in* de resource definitie van de logische app om te verwijzen naar de sjabloon parameter. Ten slotte, als u de waarde wilt door geven aan de sjabloon parameter tijdens de implementatie, slaat u die waarde op in [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md) en verwijst u naar die sleutel kluis in het [parameter bestand](#template-parameter-files) dat wordt gebruikt door uw sjabloon tijdens de implementatie.

In deze voorbeeld sjabloon ziet u hoe u deze taken kunt volt ooien door zo nodig beveiligde para meters te definiëren, zodat u hun waarden in Azure Key Vault kunt opslaan:

* De beveiligde para meters declareren voor de waarden die worden gebruikt voor het verifiëren van de toegang.
* Gebruik deze waarden zowel in de sjabloon als op de definitie niveaus van de werk stroom.
* Geef deze waarden op met behulp van een parameter bestand.

**Sjabloon**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      <previously-defined-template-parameters>,
      // Additional template parameters for passing values to use in workflow definition
      "TemplateAuthenticationType": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The type of authentication used for the Fabrikam portal"
         }
      },
      "TemplateFabrikamPassword": {
         "type": "securestring",
         "metadata": {
            "description": "The password for the Fabrikam portal"
         }
      },
      "TemplateFabrikamUserName": {
         "type": "securestring",
         "metadata": {
            "description": "The username for the Fabrikam portal"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <other-logic-app-resource-properties>,
            // Start workflow definition
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {<action-definitions>},
               // Workflow definition parameters
               "parameters": {
                  "authenticationType": {
                     "type": "string",
                     "defaultValue": "",
                     "metadata": {
                        "description": "The type of authentication used for the Fabrikam portal"
                     }
                  },
                  "fabrikamPassword": {
                     "type": "securestring",
                     "metadata": {
                        "description": "The password for the Fabrikam portal"
                     }
                  },
                  "fabrikamUserName": {
                     "type": "securestring",
                     "metadata": {
                        "description": "The username for the Fabrikam portal"
                     }
                  }
               },
               "triggers": {
                  "HTTP": {
                     "inputs": {
                        "authentication": {
                           // Reference workflow definition parameters
                           "password": "@parameters('fabrikamPassword')",
                           "type": "@parameters('authenticationType')",
                           "username": "@parameters('fabrikamUserName')"
                        }
                     },
                     "recurrence": {<...>},
                     "type": "Http"
                  }
               },
               <...>
            },
            // End workflow definition
            // Start workflow definition parameter values
            "parameters": {
               "authenticationType": "[parameters('TemplateAuthenticationType')]", // Template parameter reference
               "fabrikamPassword": "[parameters('TemplateFabrikamPassword')]", // Template parameter reference
               "fabrikamUserName": "[parameters('TemplateFabrikamUserName')]" // Template parameter reference
            },
            "accessControl": {}
         },
         <other-logic-app-resource-attributes>
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

**Parameterbestand**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      <previously-defined-template-parameter-values>,
     "TemplateAuthenticationType": {
        "value": "Basic"
     },
     "TemplateFabrikamPassword": {
        "reference": {
           "keyVault": {
              "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
           },
           "secretName": "FabrikamPassword"
        }
     },
     "TemplateFabrikamUserName": {
        "reference": {
           "keyVault": {
              "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
           },
           "secretName": "FabrikamUserName"
        }
     }
   }
}
```

<a name="best-practices-workflow-definition-parameters"></a>

## <a name="best-practices---workflow-definition-parameters"></a>Aanbevolen procedures-werk stroom definitie parameters

Volg de volgende aanbevolen procedures om ervoor te zorgen dat de Logic app Designer de para meters voor de werk stroom definitie correct kan weer geven:

* Neem het `defaultValue` kenmerk op, waarmee lege waarden kunnen worden opgegeven voor alle para meters, met uitzonde ring van waarden die gevoelig zijn of moeten worden beveiligd.

* Gebruik altijd beveiligde para meters voor gebruikers namen, wacht woorden en geheimen. Volg de richt lijnen in de volgende onderwerpen om gevoelige parameter waarden te verbergen of te beveiligen:

  * [Beveiligings aanbevelingen voor actie parameters](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)

  * [Beveiligings aanbevelingen voor para meters in werk stroom definities](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-workflow)

  * [Beveilig parameter waarden door geven Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

Zie [para meters-werk stroom definitie taal](../logic-apps/logic-apps-workflow-definition-language.md#parameters)voor meer informatie over para meters voor de werk stroom definitie.

<a name="connection-resource-definitions"></a>

## <a name="connection-resource-definitions"></a>Verbindings bron definities

Wanneer uw logische app verbindingen met andere services en het systeem maakt en gebruikt met [beheerde connectors](../connectors/apis-list.md), bevat het object van de sjabloon `resources` de resource definities voor deze verbindingen. Hoewel u verbindingen maakt vanuit een logische app, zijn de verbindingen afzonderlijke Azure-resources met hun eigen resource definities. Als u deze resource definities voor verbindingen wilt bekijken, [downloadt u uw logische app van Azure in Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md). Dit is de eenvoudigste manier om een geldige sjabloon voor een logische app met para meters te maken die het meest geschikt is voor implementatie.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Start connection resource definitions
      {
         <connection-resource-definition-1>
      },
      {
         <connection-resource-definition-2>
      }
   ],
   "outputs": {}
}
```

Bron definities voor verbindingen verwijzen naar de para meters op het hoogste niveau van de sjabloon voor hun waarden, zodat u deze waarden tijdens de implementatie kunt opgeven met behulp van een parameter bestand. Zorg ervoor dat verbindingen dezelfde Azure-resource groep en-locatie gebruiken als uw logische app.

Hier volgt een voor beeld van een resource definitie voor een Office 365 Outlook-verbinding en de bijbehorende sjabloon parameters:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "office365_1_Connection_Name": {
         "type": "string",
         "defaultValue": "office365",
         "metadata": {
            "description": "The resource name for the Office 365 Outlook connection"
         }
      },
      "office365_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The display name for the Office 365 Outlook connection"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Office 365 Outlook API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         // Template parameter reference for connection name
         "name": "[parameters('office365_1_Connection_Name')]",
         // Template parameter reference for connection resource location. Must match logic app location.
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               // Connector ID
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            // Template parameter reference for connection display name
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      }
   ],
   "outputs": {}
}
```

De resource definitie van uw logische app werkt ook op de volgende manieren met verbindings bron definities:

* In de definitie van de werk stroom `parameters` declareert het object een `$connections` para meter voor de verbindings waarden die moeten worden gebruikt in de runtime van de logische app. De trigger of actie waarmee een verbinding wordt gemaakt, maakt ook gebruik van de overeenkomende waarden die door deze para meter worden door gegeven `$connections` .

* *Buiten* uw werk stroom definitie, maar nog steeds *binnen* de resource definitie van de logische app, stelt een ander `parameters` object de waarden in die tijdens runtime voor de para meter moeten worden gebruikt `$connections` door te verwijzen naar de bijbehorende sjabloon parameters. Deze waarden maken gebruik van sjabloon expressies om te verwijzen naar resources die de meta gegevens veilig opslaan voor de verbindingen in uw logische app.

  Meta gegevens kunnen bijvoorbeeld verbindings reeksen en toegangs tokens bevatten, die u in [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)kunt opslaan. Als u deze waarden wilt door geven aan de sjabloon parameters, verwijst u naar de sleutel kluis in het [parameter bestand](#template-parameter-files) dat wordt gebruikt door uw sjabloon tijdens de implementatie. Zie [verwijzingen naar para meters](#parameter-references) verderop in dit onderwerp voor meer informatie over verschillen bij het verwijzen naar para meters.

  Wanneer u de werk stroom definitie van de logische app opent in de code weergave via de Azure Portal of Visual Studio, `$connections` wordt het object buiten de werk stroom definitie weer gegeven, maar op hetzelfde niveau. Deze volg orde in de code weergave maakt het gemakkelijker om deze para meters te raadplegen wanneer u de werk stroom definitie hand matig bijwerkt:

  ```json
  {
     "$connections": {<workflow-definition-parameter-connection-values-runtime},
     "definition": {<workflow-definition>}
  }
  ```

* De resource definitie van de logische app bevat een `dependsOn` object dat de afhankelijkheden specificeert op de verbindingen die worden gebruikt door uw logische app.

Elke verbinding die u maakt, heeft een unieke naam in Azure. Wanneer u meerdere verbindingen met dezelfde service of hetzelfde systeem maakt, wordt elke verbindings naam toegevoegd met een nummer, dat wordt verhoogd met elke nieuwe verbinding die wordt gemaakt, bijvoorbeeld,, enzovoort `office365` `office365-1` .

In dit voor beeld ziet u de interacties tussen de resource definitie van uw logische app en een verbindings bron definitie voor Office 365 Outlook:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "office365_1_Connection_Name": {<parameter-definition>},
      "office365_1_Connection_DisplayName": {<parameter-definition>}
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <...>,
            "definition": {
               <...>,
               "parameters": {
                  // Workflow definition "$connections" parameter
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               <...>
            },
            "parameters": {
               // Workflow definition "$connections" parameter values to use at runtime
               "$connections": {
                  "value": {
                     "office365": {
                        // Template parameter references
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]",
                        "connectionName": "[parameters('office365_1_Connection_Name')]"
                     }
                  }
               }
            }
         },
         <other-logic-app-resource-information>,
         "dependsOn": [
            "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]"
         ]
         // End logic app resource definition
      },
      // Office 365 Outlook API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         // Template parameter reference for connection name
         "name": "[parameters('office365_1_Connection_Name')]",
         // Template parameter reference for connection resource location. Must match logic app location.
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               // Connector ID
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            // Template parameter reference for connection display name
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      }
   ],
   "outputs": {}
}
```

<a name="secure-connection-parameters"></a>

### <a name="secure-connection-parameters"></a>Para meters voor veilige verbinding

Voor een verbindings parameter die gevoelige informatie, wacht woorden, toegangs sleutels of geheimen verwerkt, bevat de resource definitie van de verbinding een `parameterValues` object dat deze waarden specificeert in de notatie naam/waarde-paar. Als u deze informatie wilt verbergen, kunt u de sjabloon parameters voor deze waarden declareren of bewerken met behulp van de `securestring` `secureobject` para meters of. U kunt deze informatie vervolgens opslaan in [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md). Als u deze waarden wilt door geven aan de sjabloon parameters, verwijst u naar de sleutel kluis in het [parameter bestand](#template-parameter-files) dat wordt gebruikt door uw sjabloon tijdens de implementatie.

Hier volgt een voor beeld waarin de account naam en toegangs sleutel voor een Azure Blob Storage-verbinding worden geboden:

**Parameterbestand**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "LogicAppName": {
         "value": "Email-Processor-Logic-App"
      },
      "LogicAppLocation": {
         "value": "westeurope"
      },
      "azureblob_1_Connection_Name": {
         "value": "Fabrikam-Azure-Blob-Storage-Connection"
      },
      "azureblob_1_Connection_DisplayName": {
         "value": "Fabrikam-Storage"
      },
      "azureblob_1_accountName": {
         "value": "Fabrikam-Storage-Account"
      },
      "azureblob_1_accessKey": {
         "reference": {
            "keyVault": {
               "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
            },
            "secretName": "FabrikamStorageKey"
         }
      }
   }
}
```

**Sjabloon**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "azureblob_1_Connection_Name": {<parameter-definition>},
      "azureblob_1_Connection_DisplayName": {<parameter-definition>},
      "azureblob_1_accountName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         }
      },
      "azureblob_1_accessKey": {
         "type": "secureobject",
         "metadata": {
            "description": "Specify a valid primary/secondary storage account access key."
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         "properties": {
            "state": "Disabled",
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  // Azure Blob Storage action
                  "Create_blob": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              // Workflow definition parameter reference for values to use at runtime
                              "name": "@parameters('$connections')['azureblob']['connectionId']"
                           }
                        },
                     },
                     "method": "post",
                     "body": "@triggerBody()?['Body']",
                     "path": "/datasets/default/files",
                     "queries": {
                        "folderPath": "/emails",
                        "name": "@triggerBody()?['Subject']",
                        "queryParametersSingleEncoded": true
                     },
                     "runAfter": {},
                     "runtimeConfiguration": {
                        "contentTransfer": {
                           "transferMode": "Chunked"
                        }
                     }
                  }
               },
               "parameters": {
                  // Workflow definition parameter for values to use at runtime
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               "triggers": {<trigger-definition>},
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "$connections": {
                  "value": {
                     // Template parameter references for values to use at runtime
                     "azureblob": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
                        "connectionName": "[parameters('azureblob_1_Connection_Name')]"
                    }
                  }
               }
            },
            "name": "[parameters('LogicAppName')]",
            "type": "Microsoft.Logic/workflows",
            "location": "[parameters('LogicAppLocation')]",
            "tags": {
               "displayName": "LogicApp"
            },
            "apiVersion": "2016-06-01",
            // Template parameter reference for value to use at deployment
            "dependsOn": [
               "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]"
            ]
         }
      },
      // Azure Blob Storage API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('azureblob_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]"
            },
            "displayName": "[parameters('azureblob_1_Connection_DisplayName')]",
            // Template parameter reference for values to use at deployment
            "parameterValues": {
               "accountName": "[parameters('azureblob_1_accountName')]",
               "accessKey": "[parameters('azureblob_1_accessKey')]"
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="authenticate-connections"></a>

### <a name="authenticate-connections"></a>Verbindingen verifiëren

Na de implementatie werkt uw logische app end-to-end met geldige para meters. U moet echter nog steeds OAuth-verbindingen autoriseren om geldige toegangs tokens te genereren voor [het verifiëren van uw referenties](../active-directory/develop/authentication-vs-authorization.md). Zie [OAuth-verbindingen autoriseren](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections)voor meer informatie.

Sommige verbindingen bieden ondersteuning voor het gebruik van een [service-principal](../active-directory/develop/app-objects-and-service-principals.md) Azure Active Directory (Azure AD) voor het autoriseren van verbindingen voor een logische app die is [geregistreerd in azure AD](../active-directory/develop/quickstart-register-app.md). In deze Azure Data Lake bron definitie voor verbinding ziet u bijvoorbeeld hoe u kunt verwijzen naar de sjabloon parameters die de gegevens van de Service-Principal verwerken en hoe de sjabloon deze para meters declareert:

**Bron definitie voor verbinding**

```json
{
   <other-template-objects>
   "type": "MICROSOFT.WEB/CONNECTIONS",
   "apiVersion": "2016-06-01",
   "name": "[parameters('azuredatalake_1_Connection_Name')]",
   "location": "[parameters('LogicAppLocation')]",
   "properties": {
      "api": {
         "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', 'resourceGroup().location', '/managedApis/', 'azuredatalake')]"
      },
      "displayName": "[parameters('azuredatalake_1_Connection_DisplayName')]",
      "parameterValues": {
         "token:clientId": "[parameters('azuredatalake_1_token:clientId')]",
         "token:clientSecret": "[parameters('azuredatalake_1_token:clientSecret')]",
         "token:TenantId": "[parameters('azuredatalake_1_token:TenantId')]",
         "token:grantType": "[parameters('azuredatalake_1_token:grantType')]"
      }
   }
}
```

| Kenmerk | Beschrijving |
|-----------|-------------|
| `token:clientId` | De toepassing of client-ID die is gekoppeld aan uw Service-Principal |
| `token:clientSecret` | De sleutel waarde die aan de Service-Principal is gekoppeld |
| `token:TenantId` | De Directory-ID voor uw Azure AD-Tenant |
| `token:grantType` | Het aangevraagde toekennings type, dat moet zijn `client_credentials` . Zie voor meer informatie [micro soft Identity platform en de OAuth 2,0 client credentials flow](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)(Engelstalig). |
|||

**Definitie van sjabloon parameters**

Het object op het hoogste niveau van de sjabloon `parameters` declareert deze para meters voor de voorbeeld verbinding:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "azuredatalake_1_Connection_Name": {
        "type": "string",
        "defaultValue": "azuredatalake"
      },
      "azuredatalake_1_Connection_DisplayName": {
        "type": "string",
        "defaultValue": "<connection-name>"
      },
      "azuredatalake_1_token:clientId": {
        "type": "securestring",
        "metadata": {
          "description": "Client (or Application) ID of the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:clientSecret": {
        "type": "securestring",
        "metadata": {
          "description": "Client secret of the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:TenantId": {
        "type": "securestring",
        "metadata": {
          "description": "The tenant ID of for the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:resourceUri": {
        "type": "string",
        "metadata": {
          "description": "The resource you are requesting authorization to use."
        }
      },
      "azuredatalake_1_token:grantType": {
        "type": "string",
        "metadata": {
          "description": "Grant type"
        },
        "defaultValue": "client_credentials",
        "allowedValues": [
          "client_credentials"
        ]
      },
      // Other template parameters
   }
   // Other template objects
}
```

Zie de volgende onderwerpen voor meer informatie over het werken met Service-principals:

* [Een service-principal maken met behulp van de Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md)
* [Een Azure-service-principal maken met behulp van Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps)
* [Een service-principal met een certificaat maken met behulp van Azure PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

<a name="parameter-references"></a>

## <a name="references-to-parameters"></a>Verwijzingen naar para meters

Als u wilt verwijzen naar sjabloon parameters, kunt u sjabloon expressies gebruiken met [sjabloon functies](../azure-resource-manager/templates/template-functions.md), die tijdens de implementatie worden geëvalueerd. Sjabloon expressies gebruiken vier Kante haken (**[]**):

`"<attribute-name>": "[parameters('<template-parameter-name>')]"`

Als u wilt verwijzen naar para meters voor de werk stroom definitie, gebruikt u de [definities en functies van de werk stroom definitie taal](../logic-apps/workflow-definition-language-functions-reference.md), die tijdens runtime worden geëvalueerd. U zult merken dat sommige sjabloon functies en werk stroom definitie functies dezelfde naam hebben. Werk stroom definitie-expressies beginnen met het symbool ' at ' ( **@** ):

`"<attribute-name>": "@parameters('<workflow-definition-parameter-name>')"`

U kunt sjabloon parameter waarden door geven aan uw werk stroom definitie voor de logische app die tijdens runtime moet worden gebruikt. Vermijd echter het gebruik van sjabloon parameters, expressies en syntaxis in uw werk stroom definitie omdat de Logic app Designer geen sjabloon elementen ondersteunt. Daarnaast kan de sjabloon syntaxis en expressies uw code bemoeilijken vanwege verschillen in wanneer expressies worden geëvalueerd.

Volg in plaats daarvan deze algemene stappen om de para meters voor de werk stroom definitie te declareren en te raadplegen die tijdens runtime moeten worden gebruikt, Declareer en referentie de sjabloon parameters die tijdens de implementatie moeten worden gebruikt en geef de waarden op die tijdens de implementatie moeten worden door gegeven met een parameter bestand. Zie de sectie [werk stroom definitie en para meters](#workflow-definition-parameters) eerder in dit onderwerp voor meer informatie.

1. Maak uw sjabloon en Declareer de sjabloon parameters voor de waarden die u tijdens de implementatie wilt accepteren en gebruiken.

1. In de definitie van uw werk stroom declareert u de para meters voor de waarden die moeten worden geaccepteerd en gebruikt tijdens runtime. U kunt vervolgens in de werk stroom definitie naar deze waarden verwijzen.

1. In het `parameters` object dat zich *buiten* uw werk stroom definitie bevindt maar nog steeds *binnen* de resource definitie van de logische app, stelt u de waarden voor de para meters voor de werk stroom definitie in door te verwijzen naar de bijbehorende sjabloon parameters. Op die manier kunt u parameter waarden van een sjabloon door geven aan de para meters van de werk stroom definitie.

1. Geef in het parameter bestand de waarden op voor uw sjabloon die tijdens de implementatie moet worden gebruikt.

<a name="full-example-template"></a>

## <a name="full-example-template"></a>Volledige voorbeeld sjabloon

Hier volgt de parameter voorbeeld sjabloon die wordt gebruikt door de voor beelden van dit onderwerp:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "defaultValue": "MyLogicApp",
         "metadata": {
            "description": "The resource name to use for the logic app"
         }
      },
      "LogicAppLocation": {
         "type": "string",
         "minLength": 1,
         "defaultValue": "[resourceGroup().location]",
         "metadata": {
            "description": "The resource location to use for the logic app"
         }
      },
      "office365_1_Connection_Name": {
         "type": "string",
         "defaultValue": "office365",
         "metadata": {
            "description": "The resource name to use for the Office 365 Outlook connection"
         }
      },
      "office365_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The display name to use for the Office 365 Outlook connection"
         }
      },
      "azureblob_1_Connection_Name": {
         "type": "string",
         "defaultValue": "azureblob",
         "metadata": {
            "description": "The resource name to use for the Azure Blob storage account connection"
         }
      },
      "azureblob_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         },

      },
      "azureblob_1_accountName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         }
      },
      "azureblob_1_accessKey": {
         "type": "securestring",
         "metadata": {
            "description": "Specify a valid primary/secondary storage account access key."
         }
      },
      "LogicAppIntegrationAccount": {
         "type":"string",
         "minLength": 1,
         "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/fabrikam-integration-account-rg/providers/Microsoft.Logic/integrationAccounts/fabrikam-integration-account",
         "metadata": {
            "description": "The ID to use for the integration account"
         }
      }
   },
   "variables": {},
   "resources": [
      {
         "properties": {
            "state": "Disabled",
            "integrationAccount": {
              "id": "[parameters('LogicAppIntegrationAccount')]"
            },
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  "Create_blob": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              "name": "@parameters('$connections')['azureblob']['connectionId']"
                           }
                        },
                     },
                     "method": "post",
                     "body": "@triggerBody()?['Body']",
                     "path": "/datasets/default/files",
                     "queries": {
                        "folderPath": "/emails",
                        "name": "@triggerBody()?['Subject']",
                        "queryParametersSingleEncoded": true
                     },
                     "runAfter": {},
                     "runtimeConfiguration": {
                        "contentTransfer": {
                           "transferMode": "Chunked"
                        }
                     }
                  }
               },
               "parameters": {
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               "triggers": {
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
                           "folderPath": "Inbox",
                           "importance": "Any",
                           "fetchOnlyWithAttachment": false,
                           "includeAttachments": false
                        }
                     },
                     "recurrence": {
                        "frequency": "Day",
                        "interval": 1
                     },
                     "splitOn": "@triggerBody()?['value']"
                  }
               },
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "$connections": {
                  "value": {
                     "azureblob": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
                        "connectionName": "[parameters('azureblob_1_Connection_Name')]"
                     },
                     "office365": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]",
                        "connectionName": "[parameters('office365_1_Connection_Name')]"
                     }
                  }
               }
            },
            "accessControl": {}
         },
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "dependsOn": [
            "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
            "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]"
         ]
      },
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('office365_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
                "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      },
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('azureblob_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]"
            },
            "displayName": "[parameters('azureblob_1_Connection_DisplayName')]",
            "parameterValues": {
               "accountName": "[parameters('azureblob_1_accountName')]",
               "accessKey": "[parameters('azureblob_1_accessKey')]"
            }
         }
      }
   ],
   "outputs": {}
}
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Sjablonen voor logische app maken](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)

---
title: Test cases voor test toolkit
description: Beschrijft de tests die worden uitgevoerd door de ARM-sjabloontesttoolkit.
ms.topic: conceptual
ms.date: 04/12/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 7805d6dbdb8b93968a2792ed6dfaf2ac8fea9ae5
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363390"
---
# <a name="default-test-cases-for-arm-template-test-toolkit"></a>Standaardtestgevallen voor arm-sjabloontesttoolkit

In dit artikel worden de standaardtests beschreven die worden uitgevoerd met de [sjabloontesttoolkit](test-toolkit.md) voor Azure Resource Manager -sjablonen (ARM-sjablonen). Het bevat voorbeelden die slagen of mislukken voor de test. Deze bevat de naam van elke test. Zie Testparameters voor het uitvoeren [van een specifieke test.](test-toolkit.md#test-parameters)

## <a name="use-correct-schema"></a>Het juiste schema gebruiken

Testnaam: **DeploymentTemplate-schema is juist**

In uw sjabloon moet u een geldige schemawaarde opgeven.

In het volgende voorbeeld **wordt deze** test doorstaan.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": []
}
```

De schema-eigenschap in de sjabloon moet worden ingesteld op een van de volgende schema's:

* `https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json`

## <a name="parameters-must-exist"></a>Parameters moeten bestaan

Testnaam: **De eigenschap Parameters moet bestaan**

Uw sjabloon moet een element parameters hebben. Parameters zijn essentieel om uw sjablonen herbruikbaar te maken in verschillende omgevingen. Voeg parameters toe aan uw sjabloon voor waarden die veranderen wanneer u implementeert in verschillende omgevingen.

In het volgende voorbeeld **wordt deze** test doorstaan:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
          "type": "string",
          "defaultValue": "linux-vm",
          "metadata": {
            "description": "Name for the Virtual Machine."
          }
      }
  },
  ...
```

## <a name="declared-parameters-must-be-used"></a>Gedeclareerde parameters moeten worden gebruikt

Testnaam: **Er moet naar parameters worden verwezen**

Verwijder alle gedefinieerde maar niet-gebruikte parameters om verwarring in uw sjabloon te verminderen. Met deze test worden parameters gevonden die nergens in de sjabloon worden gebruikt. Door ongebruikte parameters te elimineren, is het ook eenvoudiger om uw sjabloon te implementeren, omdat u geen onnodige waarden hoeft op te geven.

## <a name="secure-parameters-cant-have-hardcoded-default"></a>Voor beveiligde parameters kan geen standaardcode zijn ingesteld

Testnaam: **Parameters voor beveiligde tekenreeksen kunnen geen standaardwaarde hebben**

Geef geen in code gecodeerde standaardwaarde op voor een beveiligde parameter in uw sjabloon. Een lege tekenreeks is prima voor de standaardwaarde.

U gebruikt de typen **SecureString of** **SecureObject** voor parameters die gevoelige waarden bevatten, zoals wachtwoorden. Wanneer een parameter een beveiligd type gebruikt, wordt de waarde van de parameter niet geregistreerd of opgeslagen in de implementatiegeschiedenis. Met deze actie wordt voorkomen dat een kwaadwillende gebruiker de gevoelige waarde kan detecteren.

Wanneer u echter een standaardwaarde op geeft, kan die waarde worden ontdekt door iedereen die toegang heeft tot de sjabloon of de implementatiegeschiedenis.

In het volgende voorbeeld **mislukt deze** test:

```json
"parameters": {
    "adminPassword": {
        "defaultValue": "HardcodedPassword",
        "type": "SecureString"
    }
}
```

In het volgende voorbeeld **wordt deze** test doorstaan:

```json
"parameters": {
    "adminPassword": {
        "type": "SecureString"
    }
}
```

## <a name="environment-urls-cant-be-hardcoded"></a>Omgevings-URL's kunnen niet worden gecodeerd

Testnaam: **DeploymentTemplate mag geen in code gecodeerde URI bevatten**

Codeer geen omgevings-URL's in uw sjabloon. Gebruik in plaats daarvan [de omgevingsfunctie om](template-functions-deployment.md#environment) deze URL's dynamisch op te halen tijdens de implementatie. Zie de testcase voor een lijst met de URL-hosts die [zijn geblokkeerd.](https://github.com/Azure/arm-ttk/blob/master/arm-ttk/testcases/deploymentTemplate/DeploymentTemplate-Must-Not-Contain-Hardcoded-Uri.test.ps1)

In het volgende **voorbeeld mislukt** deze test omdat de URL is gecodeerd.

```json
"variables":{
    "AzureURL":"https://management.azure.com"
}
```

De test mislukt **ook wanneer** deze wordt gebruikt [met concat](template-functions-string.md#concat) of [uri](template-functions-string.md#uri).

```json
"variables":{
    "AzureSchemaURL1": "[concat('https://','gallery.azure.com')]",
    "AzureSchemaURL2": "[uri('gallery.azure.com','test')]"
}
```

In het volgende voorbeeld **wordt deze** test doorstaan.

```json
"variables": {
    "AzureSchemaURL": "[environment().gallery]"
},
```

## <a name="location-uses-parameter"></a>Locatie gebruikt parameter

Testnaam: **Locatie mag niet worden gecodeerd**

Uw sjablonen moeten een parameter met de naam location hebben. Gebruik deze parameter voor het instellen van de locatie van resources in uw sjabloon. In de hoofdsjabloon _(azuredeploy.jsof_ _mainTemplate.jsingeschakeld),_ kan deze parameter standaard worden ingesteld op de locatie van de resourcegroep. In gekoppelde of geneste sjablonen mag de locatieparameter geen standaardlocatie hebben.

Gebruikers van uw sjabloon hebben mogelijk beperkte regio's voor hen beschikbaar. Wanneer u de resourcelocatie in code vast codet, kunnen gebruikers mogelijk geen resource maken in die regio. Gebruikers kunnen worden geblokkeerd, zelfs als u de resourcelocatie in stelt op `"[resourceGroup().location]"` . De resourcegroep is mogelijk gemaakt in een regio waar andere gebruikers geen toegang toe hebben. Deze gebruikers kunnen de sjabloon niet gebruiken.

Door een locatieparameter op te geven die standaard is ingesteld op de locatie van de resourcegroep, kunnen gebruikers de standaardwaarde gebruiken wanneer dit handig is, maar ook een andere locatie opgeven.

In het volgende voorbeeld **mislukt** deze test omdat de locatie van de resource is ingesteld op `resourceGroup().location` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[resourceGroup().location]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ]
}
```

In het volgende voorbeeld wordt een locatieparameter **gebruikt,** maar deze test mislukt omdat de locatieparameter standaard een hardcoded locatie heeft.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "westus"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[parameters('location')]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

Maak in plaats daarvan een parameter die standaard is ingesteld op de locatie van de resourcegroep, maar waarmee gebruikers een andere waarde kunnen opgeven. In het volgende voorbeeld **wordt** deze test uitgevoerd wanneer de sjabloon wordt gebruikt als de hoofdsjabloon.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for the resources."
            }
        }
     },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[parameters('location')]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

Als het voorgaande voorbeeld echter wordt gebruikt als gekoppelde sjabloon, mislukt **de** test. Wanneer u deze als gekoppelde sjabloon gebruikt, verwijdert u de standaardwaarde.

## <a name="resources-should-have-location"></a>Resources moeten een locatie hebben

Testnaam: **Resources moeten een locatie hebben**

De locatie voor een resource moet worden ingesteld op een [sjabloonexpressie](template-expressions.md) of `global` . De sjabloonexpressie gebruikt doorgaans de locatieparameter die in de vorige test is beschreven.

In het volgende **voorbeeld mislukt** deze test omdat de locatie geen expressie of `global` is.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "functions": [],
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "westus",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

In het volgende voorbeeld **wordt deze** test doorstaan.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "functions": [],
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Maps/accounts",
            "apiVersion": "2020-02-01-preview",
            "name": "demoMap",
            "location": "global",
            "sku": {
                "name": "S0"
            }
        }
    ],
    "outputs": {
    }
}
```

In het volgende voorbeeld **wordt deze** test ook doorstaan.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for the resources."
            }
        }
     },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[parameters('location')]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

## <a name="vm-size-uses-parameter"></a>Voor de VM-grootte wordt een parameter gebruikt

Testnaam: **VM-grootte moet een parameter zijn**

Codeer de grootte van de virtuele machine niet. Geef een parameter op zodat gebruikers van uw sjabloon de grootte van de geïmplementeerde virtuele machine kunnen wijzigen.

In het volgende voorbeeld **mislukt** deze test.

```json
"hardwareProfile": {
    "vmSize": "Standard_D2_v3"
}
```

Geef in plaats daarvan een parameter op.

```json
"vmSize": {
    "type": "string",
    "defaultValue": "Standard_A2_v2",
    "metadata": {
        "description": "Size for the Virtual Machine."
    }
},
```

Stel vervolgens de VM-grootte in op die parameter.

```json
"hardwareProfile": {
    "vmSize": "[parameters('vmSize')]"
},
```

## <a name="min-and-max-values-are-numbers"></a>Minimum- en maximumwaarden zijn getallen

Testnaam: **Minimum- en maximumwaarde zijn getallen**

Als u de minimum- en maximumwaarden voor een parameter definieert, geeft u deze op als getallen.

In het volgende voorbeeld **mislukt deze** test:

```json
"exampleParameter": {
    "type": "int",
    "minValue": "0",
    "maxValue": "10"
},
```

Geef in plaats daarvan de waarden op als getallen. In het volgende voorbeeld **wordt deze** test doorstaan:

```json
"exampleParameter": {
    "type": "int",
    "minValue": 0,
    "maxValue": 10
},
```

U krijgt deze waarschuwing ook als u een minimum- of maximumwaarde op geeft, maar niet de andere.

## <a name="artifacts-parameter-defined-correctly"></a>De parameter Artifacts is correct gedefinieerd

Testnaam: **artefactparameter**

Wanneer u parameters voor `_artifactsLocation` en op `_artifactsLocationSasToken` bevat, gebruikt u de juiste standaardinstellingen en typen. Aan de volgende voorwaarden moet worden voldaan om deze test te kunnen doorstaan:

* Als u één parameter opgeeft, moet u de andere opgeven
* `_artifactsLocation` moet een tekenreeks **zijn**
* `_artifactsLocation` moet een standaardwaarde hebben in de hoofdsjabloon
* `_artifactsLocation` kan geen standaardwaarde hebben in een geneste sjabloon
* `_artifactsLocation` moet een of `"[deployment().properties.templateLink.uri]"` de onbewerkte url voor de standaardwaarde hebben
* `_artifactsLocationSasToken` moet een **secureString zijn**
* `_artifactsLocationSasToken` kan alleen een lege tekenreeks hebben voor de standaardwaarde
* `_artifactsLocationSasToken` kan geen standaardwaarde hebben in een geneste sjabloon

## <a name="declared-variables-must-be-used"></a>Gedeclareerde variabelen moeten worden gebruikt

Testnaam: **Naar variabelen moet worden verwezen**

Verwijder variabelen die zijn gedefinieerd maar niet worden gebruikt om verwarring in uw sjabloon te verminderen. Met deze test worden variabelen gevonden die nergens in de sjabloon worden gebruikt.

## <a name="dynamic-variable-should-not-use-concat"></a>Dynamische variabele mag geen gebruik maken van concat

Testnaam: **Verwijzingen naar dynamische variabelen mogen geen gebruik maken van Concat**

Soms moet u een variabele dynamisch maken op basis van de waarde van een andere variabele of parameter. Gebruik de functie [concat niet bij](template-functions-string.md#concat) het instellen van de waarde. Gebruik in plaats daarvan een -object dat de beschikbare opties bevat en dynamisch een van de eigenschappen van het -object krijgt tijdens de implementatie.

In het volgende voorbeeld **wordt deze** test doorstaan. De **variabele currentImage** wordt dynamisch ingesteld tijdens de implementatie.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "osType": {
          "type": "string",
          "allowedValues": [
              "Windows",
              "Linux"
          ]
      }
  },
  "variables": {
    "imageOS": {
        "Windows": {
            "image": "Windows Image"
        },
        "Linux": {
            "image": "Linux Image"
        }
    },
    "currentImage": "[variables('imageOS')[parameters('osType')].image]"
  },
  "resources": [],
  "outputs": {
      "result": {
          "type": "string",
          "value": "[variables('currentImage')]"
      }
  }
}
```

## <a name="use-recent-api-version"></a>Recente API-versie gebruiken

Testnaam: **apiVersions Should Be Recent**

De API-versie voor elke resource moet een recente versie gebruiken. De test evalueert de versie die u gebruikt ten opzichte van de versies die beschikbaar zijn voor dat resourcetype.

## <a name="use-hardcoded-api-version"></a>Hardcoded API-versie gebruiken

Testnaam: **Providers apiVersions is niet toegestaan**

De API-versie voor een resourcetype bepaalt welke eigenschappen beschikbaar zijn. Geef een in code gecodeerde API-versie op in uw sjabloon. Haal geen API-versie op die tijdens de implementatie wordt bepaald. U weet niet welke eigenschappen beschikbaar zijn.

Het volgende voorbeeld **mislukt** deze test.

```json
"resources": [
    {
        "type": "Microsoft.Compute/virtualMachines",
        "apiVersion": "[providers('Microsoft.Compute', 'virtualMachines').apiVersions[0]]",
        ...
    }
]
```

In het volgende voorbeeld **wordt deze** test doorstaan.

```json
"resources": [
    {
       "type": "Microsoft.Compute/virtualMachines",
       "apiVersion": "2019-12-01",
       ...
    }
]
```

## <a name="properties-cant-be-empty"></a>Eigenschappen kunnen niet leeg zijn

Testnaam: **Sjabloon mag geen lege gegevens bevatten**

Codeer eigenschappen niet in een lege waarde. Lege waarden zijn null en lege tekenreeksen, objecten of matrices. Als u een eigenschap hebt ingesteld op een lege waarde, verwijdert u die eigenschap uit uw sjabloon. Het is echter geen probleem om een eigenschap tijdens de implementatie in te stellen op een lege waarde, zoals via een parameter.

## <a name="use-resource-id-functions"></a>Resource-id-functies gebruiken

Testnaam: **de ID's moeten worden afgeleid van resource-ID's**

Wanneer u een resource-id opgeeft, gebruikt u een van de resource-id-functies. De toegestane functies zijn:

* [resourceId](template-functions-resource.md#resourceid)
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)
* [extensionResourceId](template-functions-resource.md#extensionresourceid)

Gebruik de functie concat niet om een resource-id te maken. In het volgende voorbeeld **mislukt** deze test.

```json
"networkSecurityGroup": {
    "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('networkSecurityGroupName'))]"
}
```

In het volgende voorbeeld **wordt deze** test doorstaan.

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

## <a name="resourceid-function-has-correct-parameters"></a>De functie ResourceId heeft de juiste parameters

Testnaam: **ResourceIds mag niet bevatten**

Gebruik bij het genereren van resource-ID's geen onnodige functies voor optionele parameters. De functie [resourceId maakt standaard gebruik](template-functions-resource.md#resourceid) van het huidige abonnement en de resourcegroep. U hoeft deze waarden niet op te geven.

In het volgende **voorbeeld mislukt** deze test, omdat u de huidige abonnements-id en naam van de resourcegroep niet hoeft op te geven.

```json
"networkSecurityGroup": {
    "id": "[resourceId(subscription().subscriptionId, resourceGroup().name, 'Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

In het volgende voorbeeld **wordt deze** test doorstaan.

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

Deze test is van toepassing op:

* [resourceId](template-functions-resource.md#resourceid)
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)
* [extensionResourceId](template-functions-resource.md#extensionresourceid)
* [Verwijzing](template-functions-resource.md#reference)
* [list*](template-functions-resource.md#list)

Voor `reference` en mislukt de test `list*` **wanneer** u gebruikt om `concat` de resource-id te maken.

## <a name="dependson-best-practices"></a>dependsOn best practices

Testnaam: **Best practices voor DependsOn**

Gebruik bij het instellen van de implementatieafhankelijkheden niet de [if-functie](template-functions-logical.md#if) om een voorwaarde te testen. Als één resource afhankelijk is van een resource die voorwaardelijk is [geïmplementeerd,](conditional-resource-deployment.md)stelt u de afhankelijkheid in zoals bij elke resource. Wanneer een voorwaardelijke resource niet wordt geïmplementeerd, Azure Resource Manager automatisch verwijderd uit de vereiste afhankelijkheden.

Het volgende voorbeeld **mislukt** deze test.

```json
"dependsOn": [
    "[if(equals(parameters('newOrExisting'),'new'), variables('storageAccountName'), '')]"
]
```

In het volgende voorbeeld **wordt deze** test doorstaan.

```json
"dependsOn": [
    "[variables('storageAccountName')]"
]
```

## <a name="nested-or-linked-deployments-cant-use-debug"></a>Geneste of gekoppelde implementaties kunnen geen foutopsporing gebruiken

Testnaam: **Implementatiebronnen mogen geen foutopsporing zijn**

Wanneer u een [geneste](linked-templates.md) of gekoppelde sjabloon definieert met het resourcetype **Microsoft.Resources/deployments,** kunt u debugging voor die sjabloon inschakelen. Debugging is prima wanneer u die sjabloon wilt testen, maar moet worden ingeschakeld wanneer u klaar bent om de sjabloon in productie te gebruiken.

## <a name="admin-user-names-cant-be-literal-value"></a>Gebruikersnamen van beheerders kunnen geen letterlijke waarde zijn

Testnaam: **adminUsername should not be a literal**

Gebruik geen letterlijke waarde bij het instellen van een gebruikersnaam van de beheerder.

In het volgende voorbeeld **mislukt deze** test:

```json
"osProfile":  {
    "adminUserName":  "myAdmin"
},
```

Gebruik in plaats daarvan een parameter. In het volgende voorbeeld **wordt deze** test doorstaan:

```json
"osProfile": {
    "adminUsername": "[parameters('adminUsername')]"
}
```

## <a name="use-latest-vm-image"></a>Meest recente VM-afbeelding gebruiken

Testnaam: **VM-afbeeldingen moeten de nieuwste versie gebruiken**

Als uw sjabloon een virtuele machine met een afbeelding bevat, moet u ervoor zorgen dat deze de nieuwste versie van de afbeelding gebruikt.

## <a name="use-stable-vm-images"></a>Stabiele VM-afbeeldingen gebruiken

Testnaam: **Virtual Machines mag geen preview zijn**

Virtuele machines mogen geen preview-afbeeldingen gebruiken.

In het volgende voorbeeld **mislukt** deze test.

```json
"imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest-preview"
}
```

In het volgende voorbeeld **wordt deze** test doorstaan.

```json
"imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest"
},
```

## <a name="dont-use-managedidentity-extension"></a>ManagedIdentity-extensie niet gebruiken

Testnaam: **ManagedIdentityExtension mag niet worden gebruikt**

Pas de ManagedIdentity-extensie niet toe op een virtuele machine. De extensie is in 2019 afgeschaft en mag niet meer worden gebruikt.

## <a name="outputs-cant-include-secrets"></a>Uitvoer kan geen geheimen bevatten

Testnaam: **Uitvoer mag geen geheimen bevatten**

Neem geen waarden op in de sectie outputs die geheimen mogelijk blootstellen. De uitvoer van een sjabloon wordt opgeslagen in de implementatiegeschiedenis, zodat een kwaadwillende gebruiker die informatie kan vinden.

Het volgende voorbeeld **mislukt de** test omdat deze een beveiligde parameter in een uitvoerwaarde bevat.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "secureParam": {
            "type": "securestring"
        }
    },
    "functions": [],
    "variables": {},
    "resources": [],
    "outputs": {
        "badResult": {
            "type": "string",
            "value": "[concat('this is the value ', parameters('secureParam'))]"
        }
    }
}
```

Het volgende voorbeeld **mislukt omdat** er een [list*-functie](template-functions-resource.md#list) in de uitvoer wordt gebruikt.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageName": {
            "type": "string"
        }
    },
    "functions": [],
    "variables": {},
    "resources": [],
    "outputs": {
        "badResult": {
            "type": "object",
            "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageName')), '2019-06-01')]"
        }
    }
}
```

## <a name="use-protectedsettings-for-commandtoexecute-secrets"></a>ProtectedSettings gebruiken voor commandToExecute-geheimen

Testnaam: **CommandToExecute moet ProtectedSettings voor geheimen gebruiken**

Gebruik in een aangepaste scriptextensie de versleutelde eigenschap `protectedSettings` wanneer geheime gegevens zoals een wachtwoord `commandToExecute` bevat. Voorbeelden van geheime gegevenstypen zijn `secureString` , , functies of `secureObject` `list()` scripts.

Zie Windows [,](
/azure/virtual-machines/extensions/custom-script-windows) [Linux](/azure/virtual-machines/extensions/custom-script-linux)en het schema [Microsoft.Compute virtualMachines/extensions](/azure/templates/microsoft.compute/virtualmachines/extensions)voor meer informatie over aangepaste scriptextensie voor virtuele machines.

In dit voorbeeld wordt de test door een sjabloon met een parameter met de naam en het type doorgegeven, omdat `adminPassword` de `secureString`  versleutelde eigenschap `protectedSettings` `commandToExecute` bevat.

```json
"properties": [
  {
    "protectedSettings": {
      "commandToExecute": "[parameters('adminPassword')]"
    }
  }
]
```

De test **mislukt** als de niet-versleutelde eigenschap `settings` `commandToExecute` bevat.

```json
"properties": [
  {
    "settings": {
      "commandToExecute": "[parameters('adminPassword')]"
    }
  }
]
```

## <a name="next-steps"></a>Volgende stappen

* Zie Use ARM template test [toolkit (Arm-sjabloontesttoolkit](test-toolkit.md)gebruiken) voor meer informatie over het uitvoeren van de testtoolkit.
* Zie Preview-Microsoft Learn en valideren van Azure-resources met behulp van what-if en de [ARM-sjabloontesttoolkit](/learn/modules/arm-template-test/)voor een nieuwe module over het gebruik van de testtoolkit.

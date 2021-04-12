---
title: Test cases voor test Toolkit
description: Hierin worden de tests beschreven die worden uitgevoerd door de ARM-sjabloon test Toolkit.
ms.topic: conceptual
ms.date: 12/03/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 31e30b4853da03e28a4a2d15292050805f5bc292
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106064139"
---
# <a name="default-test-cases-for-arm-template-test-toolkit"></a>Standaard test cases voor ARM-sjabloon test Toolkit

In dit artikel worden de standaard tests beschreven die worden uitgevoerd met de [sjabloon test Toolkit](test-toolkit.md) voor Azure Resource Manager sjablonen (arm-sjablonen). Het biedt voor beelden die de test door geven of mislukken. Het bevat de naam van elke test. Zie [test parameters](test-toolkit.md#test-parameters)om een specifieke test uit te voeren.

## <a name="use-correct-schema"></a>Het juiste schema gebruiken

Test naam: **DeploymentTemplate-schema is juist**

In uw sjabloon moet u een geldige schema waarde opgeven.

In het volgende voor beeld wordt deze test **door gegeven** .

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

## <a name="parameters-must-exist"></a>Para meters moeten bestaan

Test naam: de **eigenschap para meters moet bestaan**

Uw sjabloon moet een para meters-element hebben. Para meters zijn essentieel om uw sjablonen in verschillende omgevingen te kunnen hergebruiken. Voeg para meters aan uw sjabloon toe voor waarden die worden gewijzigd wanneer ze in verschillende omgevingen worden geïmplementeerd.

In het volgende voor beeld wordt deze test **door gegeven** :

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

## <a name="declared-parameters-must-be-used"></a>Gedeclareerde para meters moeten worden gebruikt

Test naam: er **moet naar para meters worden verwezen**

Als u Verwar ring in uw sjabloon wilt beperken, verwijdert u de para meters die zijn gedefinieerd, maar niet worden gebruikt. Met deze test worden alle para meters gevonden die nergens in de sjabloon worden gebruikt. Door ongebruikte para meters te verwijderen, is het eenvoudiger om uw sjabloon te implementeren omdat u geen onnodige waarden hoeft op te geven.

## <a name="secure-parameters-cant-have-hardcoded-default"></a>Veilige para meters kunnen geen hardcoded-standaard waarde hebben

Test naam: de **beveiligde teken reeks parameters mogen geen standaard waarde hebben**

Geef geen in code vastgelegde standaard waarde op voor een beveiligde para meter in uw sjabloon. Een lege teken reeks is nauw keurig voor de standaard waarde.

U gebruikt de typen **SecureString** of **SecureObject** in para meters die gevoelige waarden bevatten, zoals wacht woorden. Wanneer een para meter een beveiligd type gebruikt, wordt de waarde van de para meter niet geregistreerd of opgeslagen in de implementatie geschiedenis. Met deze actie wordt voor komen dat een kwaadwillende gebruiker de gevoelige waarde detecteert.

Wanneer u echter een standaard waarde opgeeft, wordt die waarde gedetecteerd door iedereen die toegang heeft tot de sjabloon of de implementatie geschiedenis.

In het volgende voor beeld **mislukt** deze test:

```json
"parameters": {
    "adminPassword": {
        "defaultValue": "HardcodedPassword",
        "type": "SecureString"
    }
}
```

In het volgende voor beeld wordt deze test **door gegeven** :

```json
"parameters": {
    "adminPassword": {
        "type": "SecureString"
    }
}
```

## <a name="environment-urls-cant-be-hardcoded"></a>Omgevings-Url's kunnen niet hardcoded zijn

Test naam: **DeploymentTemplate mag geen hardcoded URI bevatten**

Voorlopig hardcoderen we geen omgevings-Url's in uw sjabloon. Gebruik in plaats daarvan de [functie omgeving](template-functions-deployment.md#environment) om deze url's dynamisch op te halen tijdens de implementatie. Voor een lijst met de geblokkeerde URL-hosts raadpleegt u de [test case](https://github.com/Azure/arm-ttk/blob/master/arm-ttk/testcases/deploymentTemplate/DeploymentTemplate-Must-Not-Contain-Hardcoded-Uri.test.ps1).

In het volgende voor beeld wordt deze test **niet** uitgevoerd omdat de URL hardcoded is.

```json
"variables":{
    "AzureURL":"https://management.azure.com"
}
```

De test **mislukt** ook als deze wordt gebruikt met [concat](template-functions-string.md#concat) of [URI](template-functions-string.md#uri).

```json
"variables":{
    "AzureSchemaURL1": "[concat('https://','gallery.azure.com')]",
    "AzureSchemaURL2": "[uri('gallery.azure.com','test')]"
}
```

In het volgende voor beeld wordt deze test **door gegeven** .

```json
"variables": {
    "AzureSchemaURL": "[environment().gallery]"
},
```

## <a name="location-uses-parameter"></a>Locatie gebruikt para meter

Test naam: **locatie mag niet hardcoded zijn**

Uw sjablonen moeten een para meter met de naam Location hebben. Gebruik deze para meter om de locatie van resources in uw sjabloon in te stellen. In de hoofd sjabloon (met de naam _azuredeploy.jsop_ of _mainTemplate.jsop_), kan deze para meter standaard worden ingesteld op de locatie van de resource groep. In gekoppelde of geneste sjablonen mag de locatie parameter geen standaard locatie hebben.

Voor gebruikers van uw sjabloon zijn mogelijk beperkte regio's beschikbaar. Wanneer u de resource locatie permanent codeert, kunnen gebruikers worden geblokkeerd voor het maken van een resource in die regio. Gebruikers kunnen worden geblokkeerd, zelfs als u de resource locatie instelt op `"[resourceGroup().location]"` . De resource groep is mogelijk gemaakt in een regio waartoe andere gebruikers geen toegang hebben. Deze gebruikers worden geblokkeerd voor het gebruik van de sjabloon.

Door een locatie parameter op te geven die standaard wordt ingesteld op de locatie van de resource groep, kunnen gebruikers de standaard waarde gebruiken als ze handig zijn, maar ook een andere locatie opgeven.

In het volgende voor beeld wordt deze test **niet** uitgevoerd omdat de locatie van de resource is ingesteld op `resourceGroup().location` .

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

In het volgende voor beeld wordt een locatie parameter gebruikt, maar deze test **mislukt** omdat de locatie parameter standaard wordt ingesteld op een hardcoded locatie.

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

Maak in plaats daarvan een para meter die standaard wordt ingesteld op de locatie van de resource groep, maar biedt gebruikers de mogelijkheid om een andere waarde op te geven. In het volgende voor beeld wordt deze test **door gegeven** wanneer de sjabloon als hoofd sjabloon wordt gebruikt.

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

Als het vorige voor beeld echter als een gekoppelde sjabloon wordt gebruikt, **mislukt** de test. Bij gebruik als gekoppelde sjabloon verwijdert u de standaard waarde.

## <a name="resources-should-have-location"></a>Resources moeten locatie hebben

Test naam: **resources moeten de locatie hebben**

De locatie van een resource moet worden ingesteld op een [sjabloon expressie](template-expressions.md) of `global` . De sjabloon expressie gebruikt normaal gesp roken de locatie parameter zoals beschreven in de vorige test.

In het volgende voor beeld wordt deze test **niet** uitgevoerd omdat de locatie geen expressie of is `global` .

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

In het volgende voor beeld wordt deze test **door gegeven** .

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

In het volgende voor beeld wordt deze test ook **door gegeven** .

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

## <a name="vm-size-uses-parameter"></a>VM-grootte maakt gebruik van para meter

Test naam: **VM-grootte moet een para meter zijn**

Voorlopig hardcoderen we de grootte van de virtuele machine niet. Geef een para meter op zodat gebruikers van uw sjabloon de grootte van de geïmplementeerde virtuele machine kunnen wijzigen.

In het volgende voor beeld **mislukt** deze test.

```json
"hardwareProfile": {
    "vmSize": "Standard_D2_v3"
}
```

Geef in plaats daarvan een para meter op.

```json
"vmSize": {
    "type": "string",
    "defaultValue": "Standard_A2_v2",
    "metadata": {
        "description": "Size for the Virtual Machine."
    }
},
```

Stel vervolgens de VM-grootte in op die para meter.

```json
"hardwareProfile": {
    "vmSize": "[parameters('vmSize')]"
},
```

## <a name="min-and-max-values-are-numbers"></a>De minimum-en maximum waarde zijn getallen

Test naam: **min en maximum waarde zijn getallen**

Als u de minimum-en maximum waarde voor een para meter definieert, geeft u ze op als getallen.

In het volgende voor beeld **mislukt** deze test:

```json
"exampleParameter": {
    "type": "int",
    "minValue": "0",
    "maxValue": "10"
},
```

Geef in plaats daarvan de waarden op als getallen. In het volgende voor beeld wordt deze test **door gegeven** :

```json
"exampleParameter": {
    "type": "int",
    "minValue": 0,
    "maxValue": 10
},
```

U krijgt deze waarschuwing ook als u een minimum-of maximum waarde opgeeft, maar niet de andere.

## <a name="artifacts-parameter-defined-correctly"></a>De para meter artefacten correct is gedefinieerd

Test naam: **artefacten para meter**

Wanneer u para meters voor en opneemt `_artifactsLocation` `_artifactsLocationSasToken` , gebruikt u de juiste standaard waarden en typen. Aan de volgende voor waarden moet worden voldaan om deze test door te geven:

* Als u één para meter opgeeft, moet u de andere opgeven
* `_artifactsLocation`moet een **teken reeks** zijn
* `_artifactsLocation` moet een standaard waarde hebben in de hoofd sjabloon
* `_artifactsLocation` kan geen standaard waarde hebben in een geneste sjabloon
* `_artifactsLocation``"[deployment().properties.templateLink.uri]"`de standaard waarde moet van de opslag plaats-URL (of onbewerkte) zijn
* `_artifactsLocationSasToken`moet een **secureString** zijn
* `_artifactsLocationSasToken` kan alleen een lege teken reeks voor de standaard waarde hebben
* `_artifactsLocationSasToken` kan geen standaard waarde hebben in een geneste sjabloon

## <a name="declared-variables-must-be-used"></a>Gedeclareerde variabelen moeten worden gebruikt

Test naam: er **moet naar variabelen worden verwezen**

Als u Verwar ring in uw sjabloon wilt beperken, verwijdert u alle variabelen die zijn gedefinieerd, maar niet worden gebruikt. Met deze test vindt u alle variabelen die nergens in de sjabloon worden gebruikt.

## <a name="dynamic-variable-should-not-use-concat"></a>Dynamische variabele mag geen concat gebruiken

Test naam: **verwijzingen naar dynamische variabelen mogen geen concat gebruiken**

Soms moet u een variabele dynamisch maken op basis van de waarde van een andere variabele of para meter. Gebruik de functie [concat](template-functions-string.md#concat) niet wanneer u de waarde instelt. Gebruik in plaats daarvan een object dat de beschik bare opties bevat en een van de eigenschappen van het object dynamisch ophalen tijdens de implementatie.

In het volgende voor beeld wordt deze test **door gegeven** . De variabele **currentImage** wordt dynamisch ingesteld tijdens de implementatie.

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

Test naam: **ApiVersions moet recent zijn**

De API-versie voor elke resource moet een recente versie gebruiken. De test evalueert de versie die u gebruikt voor de beschik bare versies voor het bron type.

## <a name="use-hardcoded-api-version"></a>Hardcoded API-versie gebruiken

Test naam: **providers ApiVersions is niet toegestaan**

De API-versie voor een resource type bepaalt welke eigenschappen beschikbaar zijn. Geef een in code vastgelegde API-versie op in uw sjabloon. Haal geen API-versie op die wordt bepaald tijdens de implementatie. U weet niet welke eigenschappen er beschikbaar zijn.

In het volgende voor beeld **mislukt** deze test.

```json
"resources": [
    {
        "type": "Microsoft.Compute/virtualMachines",
        "apiVersion": "[providers('Microsoft.Compute', 'virtualMachines').apiVersions[0]]",
        ...
    }
]
```

In het volgende voor beeld wordt deze test **door gegeven** .

```json
"resources": [
    {
       "type": "Microsoft.Compute/virtualMachines",
       "apiVersion": "2019-12-01",
       ...
    }
]
```

## <a name="properties-cant-be-empty"></a>Eigenschappen mogen niet leeg zijn

Test naam: **sjabloon mag geen spaties bevatten**

Geen voorlopig hardcoderen we-eigenschappen naar een lege waarde. Lege waarden zijn Null-en lege teken reeksen, objecten of matrices. Als u een eigenschap hebt ingesteld op een lege waarde, verwijdert u die eigenschap uit uw sjabloon. Het is echter verstandig om een eigenschap in te stellen op een lege waarde tijdens de implementatie, bijvoorbeeld via een para meter.

## <a name="use-resource-id-functions"></a>Resource-ID-functies gebruiken

Test naam: **Id's moeten worden afgeleid van ResourceIDs**

Gebruik een van de resource-ID-functies wanneer u een resource-ID opgeeft. De toegestane functies zijn:

* [resourceId](template-functions-resource.md#resourceid)
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)
* [extensionResourceId](template-functions-resource.md#extensionresourceid)

Gebruik de functie concat niet om een resource-ID te maken. In het volgende voor beeld **mislukt** deze test.

```json
"networkSecurityGroup": {
    "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('networkSecurityGroupName'))]"
}
```

In het volgende voor beeld wordt deze test **door gegeven** .

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

## <a name="resourceid-function-has-correct-parameters"></a>De functie ResourceId heeft de juiste para meters

Test naam: **ResourceIds mag niet bevatten**

Gebruik bij het genereren van resource-Id's geen overbodige functies voor optionele para meters. De functie [resourceId](template-functions-resource.md#resourceid) maakt standaard gebruik van het huidige abonnement en de resource groep. U hoeft deze waarden niet op te geven.

In het volgende voor beeld is deze test **mislukt** , omdat u de huidige abonnements-id en de naam van de resource groep niet hoeft op te geven.

```json
"networkSecurityGroup": {
    "id": "[resourceId(subscription().subscriptionId, resourceGroup().name, 'Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

In het volgende voor beeld wordt deze test **door gegeven** .

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
* [referentielaag](template-functions-resource.md#reference)
* [orderverzamellijst](template-functions-resource.md#list)

Voor `reference` en `list*` , **mislukt** de test wanneer u gebruikt `concat` om de resource-id te maken.

## <a name="dependson-best-practices"></a>Best practices voor dependsOn

Test naam: **Best practices voor DependsOn**

Gebruik bij het instellen van de implementatie afhankelijkheden niet de functie [als](template-functions-logical.md#if) om een voor waarde te testen. Als een resource afhankelijk is van een resource die [voorwaardelijk wordt geïmplementeerd](conditional-resource-deployment.md), stelt u de afhankelijkheid in, net zoals bij elke resource. Wanneer een voorwaardelijke resource niet is geïmplementeerd, wordt deze automatisch door Azure Resource Manager verwijderd uit de vereiste afhankelijkheden.

In het volgende voor beeld **mislukt** deze test.

```json
"dependsOn": [
    "[if(equals(parameters('newOrExisting'),'new'), variables('storageAccountName'), '')]"
]
```

In het volgende voor beeld wordt deze test **door gegeven** .

```json
"dependsOn": [
    "[variables('storageAccountName')]"
]
```

## <a name="nested-or-linked-deployments-cant-use-debug"></a>Geneste of gekoppelde implementaties kunnen geen fout opsporing gebruiken

Test naam: **implementatie bronnen mogen geen fout opsporing zijn**

Wanneer u een [geneste of gekoppelde sjabloon](linked-templates.md) definieert met het resource type **micro soft. resources/implementaties** , kunt u fout opsporing voor die sjabloon inschakelen. Fout opsporing is prima wanneer u die sjabloon moet testen, maar moet worden ingeschakeld wanneer u de sjabloon in productie wilt gebruiken.

## <a name="admin-user-names-cant-be-literal-value"></a>Gebruikers namen van beheerders kunnen niet letterlijke waarden zijn

Test naam: **AdminUsername mag geen letterlijke waarde zijn**

Gebruik bij het instellen van de gebruikers naam van een beheerder geen letterlijke waarde.

In het volgende voor beeld **mislukt** deze test:

```json
"osProfile":  {
    "adminUserName":  "myAdmin"
},
```

Gebruik in plaats daarvan een para meter. In het volgende voor beeld wordt deze test **door gegeven** :

```json
"osProfile": {
    "adminUsername": "[parameters('adminUsername')]"
}
```

## <a name="use-latest-vm-image"></a>Nieuwste VM-installatie kopie gebruiken

Test naam: **VM-installatie kopieën moeten de meest recente versie gebruiken**

Als uw sjabloon een virtuele machine met een installatie kopie bevat, moet u ervoor zorgen dat deze de meest recente versie van de installatie kopie gebruikt.

## <a name="use-stable-vm-images"></a>Stabiele VM-installatie kopieën gebruiken

Test naam: **virtual machines moet niet worden weer** gegeven

Virtuele machines mogen geen preview-installatie kopieën gebruiken.

In het volgende voor beeld **mislukt** deze test.

```json
"imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest-preview"
}
```

In het volgende voor beeld wordt deze test **door gegeven** .

```json
"imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest"
},
```

## <a name="dont-use-managedidentity-extension"></a>Geen ManagedIdentity-extensie gebruiken

Test naam: **ManagedIdentityExtension mag niet worden gebruikt**

Pas de ManagedIdentity-extensie niet toe op een virtuele machine. Zie voor meer informatie [hoe u de extensie voor beheerde identiteiten van de virtuele machine stopt met het gebruik van Azure instance metadata service](../../active-directory/managed-identities-azure-resources/howto-migrate-vm-extension.md).

## <a name="outputs-cant-include-secrets"></a>Uitvoer kan geen geheimen bevatten

Test naam: **uitvoer mag geen geheimen bevatten**

Neem geen waarden op in de sectie outputs die mogelijk geheimen openbaren. De uitvoer van een sjabloon wordt opgeslagen in de implementatie geschiedenis, waardoor een kwaadwillende gebruiker die informatie kan vinden.

In het volgende voor beeld **mislukt** de test omdat het een beveiligde para meter in een uitvoer waarde bevat.

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

Het volgende voor beeld **mislukt** omdat er een [lijst *](template-functions-resource.md#list) -functie wordt gebruikt in de uitvoer.

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

Test naam: **CommandToExecute moet ProtectedSettings gebruiken voor geheimen**

In een aangepaste script extensie gebruikt u de versleutelde eigenschap `protectedSettings` bij het `commandToExecute` opnemen van geheime gegevens zoals een wacht woord. Voor beelden van geheime gegevens typen zijn `secureString` , `secureObject` , `list()` functions of scripts.

Zie voor meer informatie over aangepaste script extensie voor virtuele machines [Windows](
/azure/virtual-machines/extensions/custom-script-windows), [Linux](/azure/virtual-machines/extensions/custom-script-linux)en het schema [micro soft. Compute informatie/Extensions](/azure/templates/microsoft.compute/virtualmachines/extensions).

In dit voor beeld geeft een sjabloon met een para meter met `adminPassword` de naam en `secureString`  het type de test door, omdat de versleutelde eigenschap `protectedSettings` bevat `commandToExecute` .

```json
"properties": [
  {
    "protectedSettings": {
      "commandToExecute": "[parameters('adminPassword')]"
    }
  }
]
```

De test **mislukt** als de niet-versleutelde eigenschap `settings` bevat `commandToExecute` .

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

* Zie [test Toolkit voor arm-sjablonen gebruiken](test-toolkit.md)voor meer informatie over het uitvoeren van de test Toolkit.
* Zie [Preview wijzigingen en Azure-resources valideren met behulp van wat als' en de arm-sjabloon test Toolkit](/learn/modules/arm-template-test/)voor een Microsoft Learn module die de test Toolkit gebruikt.

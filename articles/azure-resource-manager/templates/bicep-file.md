---
title: Bicep-bestandsstructuur en syntaxis
description: Beschrijft de structuur en eigenschappen van een Bicep-bestand met behulp van declaratieve syntaxis.
ms.topic: conceptual
ms.date: 03/31/2021
ms.openlocfilehash: 1b8eddd388878be8f653f963ef967cf2c0af685f
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537868"
---
# <a name="understand-the-structure-and-syntax-of-bicep-files"></a>De structuur en syntaxis van Bicep-bestanden begrijpen

In dit artikel wordt de structuur van een Bicep-bestand beschreven. Het geeft de verschillende secties van het bestand en de eigenschappen die beschikbaar zijn in deze secties.

Dit artikel is bedoeld voor gebruikers die enige bekendheid hebben met Bicep-bestanden. Het bevat gedetailleerde informatie over de structuur van de sjabloon. Voor een stapsgewijze zelfstudie die u door het proces van het maken van een Bicep-bestand leidt, gaat u naar Zelfstudie: eerste maken en implementeren [Azure Resource Manager Bicep-bestand.](bicep-tutorial-create-first-bicep.md)

## <a name="template-format"></a>Sjabloonindeling

Een Bicep-bestand heeft de volgende elementen. De elementen kunnen in elke volgorde worden weergegeven.

```bicep
targetScope = '<scope>'

@<decorator>(<argument>)
param <parameter-name> <parameter-data-type> = <default-value>

var <variable-name> = <variable-value>

resource <resource-symbolic-name> '<resource-type>@<api-version>' = {
  <resource-properties>
}

// conditional deployment
resource <resource-symbolic-name> '<resource-type>@<api-version>' = if (<condition-to-deploy>) {
  <resource-properties>
}

// iterative deployment
@<decorator>(<argument>)
resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <item> in <collection>: {
  <resource-properties>
}]

module <module-symbolic-name> '<path-to-file>' = {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}

// conditional deployment
module <module-symbolic-name> '<path-to-file>' = if (<condition-to-deploy>) {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}

// iterative deployment
module <module-symbolic-name> '<path-to-file>' = [for <item> in <collection>: {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}]

output <output-name> <output-data-type> = <output-value>
```

In het volgende voorbeeld ziet u een implementatie van deze elementen.

```bicep
@minLength(3)
@maxLength(11)
param storagePrefix string

param storageSKU string = 'Standard_LRS'
param location string = resourceGroup().location

var uniqueStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'

resource stg 'Microsoft.Storage/storageAccounts@2019-04-01' = {
  name: uniqueStorageName
  location: location
  sku: {
    name: storageSKU
  }
  kind: 'StorageV2'
  properties: {
    supportsHttpsTrafficOnly: true
  }
}

module webModule './webApp.bicep' = {
  name: 'webDeploy'
  params: {
    skuName: 'S1'
    location: location
  }
}

output storageEndpoint object = stg.properties.primaryEndpoints
```

## <a name="target-scope"></a>Doelbereik

Het doelbereik is standaard ingesteld op `resourceGroup` . Als u implementeert op het niveau van de resourcegroep, hoeft u het doelbereik niet in uw Bicep-bestand in te stellen.

De toegestane waarden zijn:

* **resourceGroup:** standaardwaarde die wordt gebruikt voor [implementaties van resourcegroepen.](deploy-to-resource-group.md)
* **abonnement** : wordt gebruikt voor [abonnementsimplementaties.](deploy-to-subscription.md)
* **managementGroup:** wordt gebruikt voor [implementaties van beheergroepen.](deploy-to-management-group.md)
* **tenant** : wordt gebruikt voor [tenantimplementaties.](deploy-to-tenant.md)

## <a name="parameters"></a>Parameters

Gebruik parameters voor waarden die moeten variëren voor verschillende implementaties. U kunt een standaardwaarde definiëren voor de parameter die wordt gebruikt als er geen waarde wordt opgegeven tijdens de implementatie.

U kunt bijvoorbeeld een SKU-parameter toevoegen om verschillende grootten voor een resource op te geven. U kunt sjabloonfuncties gebruiken voor het maken van de standaardwaarde, zoals het verkrijgen van de locatie van de resourcegroep.

```bicep
param storageSKU string = 'Standard_LRS'
param location string = resourceGroup().location
```

Zie Gegevenstypen in sjablonen voor [de beschikbare gegevenstypen.](data-types.md)

Zie Parameters [in sjablonen voor meer informatie.](template-parameters.md)

## <a name="parameter-decorators"></a>Parameterparameters

U kunt een of meer aaneenvoegers toevoegen voor elke parameter. Deze verantwoordelijken definiëren de waarden die zijn toegestaan voor de parameter. In het volgende voorbeeld worden de SKU's opgegeven die kunnen worden geïmplementeerd via het Bicep-bestand.

```bicep
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
param storageSKU string = 'Standard_LRS'
```

In de volgende tabel worden de beschikbare makers beschreven en wordt beschreven hoe u deze kunt gebruiken.

| Decorator | Van toepassing op | Argument | Description |
| --------- | ---- | ----------- | ------- |
| Toegestaan | all | matrix | Toegestane waarden voor de parameter . Gebruik deze bewaarder om ervoor te zorgen dat de gebruiker de juiste waarden levert. |
| beschrijving | all | tekenreeks | Tekst waarin wordt uitgelegd hoe u de parameter gebruikt. De beschrijving wordt weergegeven voor gebruikers via de portal. |
| Maxlength | matrix, tekenreeks | int | De maximale lengte voor tekenreeks- en matrixparameters. De waarde is inclusief. |
| maxValue | int | int | De maximumwaarde voor de parameter integer. Deze waarde is inclusief. |
| metagegevens | all | object | Aangepaste eigenschappen die moeten worden toegepast op de parameter. Kan een beschrijvings-eigenschap bevatten die gelijk is aan de beschrijvingsdealator. |
| minLength | matrix, tekenreeks | int | De minimale lengte voor tekenreeks- en matrixparameters. De waarde is inclusief. |
| minValue | int | int | De minimumwaarde voor de parameter integer. Deze waarde is inclusief. |
| Veilige | tekenreeks, object | geen | Markeert de parameter als veilig. De waarde voor een beveiligde parameter wordt niet opgeslagen in de implementatiegeschiedenis en wordt niet geregistreerd. Zie Beveiligde tekenreeksen en [objecten voor meer informatie.](data-types.md#secure-strings-and-objects) |

## <a name="variables"></a>Variabelen

Gebruik variabelen voor complexe expressies die worden herhaald in een Bicep-bestand. U kunt bijvoorbeeld een variabele toevoegen voor een resourcenaam die wordt samengesteld door verschillende waarden samen te voegen.

```bicep
var uniqueStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'
```

U geeft geen gegevenstype [op voor](data-types.md) een variabele. In plaats daarvan wordt het gegevenstype afgeleid van de waarde.

Zie Variabelen in [sjablonen voor meer informatie.](template-variables.md)

## <a name="resource"></a>Resource

Gebruik het `resource` trefwoord om een resource te definiëren die moet worden geïmplementeerd. Uw resourcedeclaratie bevat een symbolische naam voor de resource. U gebruikt deze symbolische naam in andere delen van het Bicep-bestand als u een waarde uit de resource wilt krijgen.

De resourcedeclaratie bevat ook het resourcetype en de API-versie.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: uniqueStorageName
  location: location
  sku: {
    name: storageSKU
  }
  kind: 'StorageV2'
  properties: {
    supportsHttpsTrafficOnly: true
  }
}
```

In de resourcedeclaratie gebruikt u eigenschappen voor het resourcetype. Deze eigenschappen zijn uniek voor elk resourcetype.

Zie Resourcedeclaratie [in sjablonen voor meer informatie.](resource-declaration.md)

Als [u een resource voorwaardelijk wilt implementeren,](conditional-resource-deployment.md)voegt u een expressie `if` toe.

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (newOrExisting == 'new') {
  name: uniqueStorageName
  location: location
  sku: {
    name: storageSKU
  }
  kind: 'StorageV2'
  properties: {
    supportsHttpsTrafficOnly: true
  }
}
```

Als [u meer dan één exemplaar van een](https://github.com/Azure/bicep/blob/main/docs/spec/loops.md) resourcetype wilt implementeren, voegt u een expressie `for` toe. De expressie kan worden overgenomen door leden van een matrix.

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = [for storageName in storageAccounts: {
  name: storageName
  location: location
  sku: {
    name: storageSKU
  }
  kind: 'StorageV2'
  properties: {
    supportsHttpsTrafficOnly: true
  }
}]
```

## <a name="modules"></a>Modules

Gebruik modules om een koppeling te maken naar andere Bicep-bestanden die code bevatten die u opnieuw wilt gebruiken. De module bevat een of meer resources die moeten worden geïmplementeerd. Deze resources worden samen met andere resources in uw Bicep-bestand geïmplementeerd.

```bicep
module webModule './webApp.bicep' = {
  name: 'webDeploy'
  params: {
    skuName: 'S1'
    location: location
  }
}
```

Met de symbolische naam kunt u vanuit een andere map in het bestand naar de module verwijzen. U kunt bijvoorbeeld een uitvoerwaarde van een module krijgen met behulp van de symbolische naam en de naam van de uitvoerwaarde.

Net als bij resources kunt u een module voorwaardelijk of iteratief implementeren. De syntaxis is hetzelfde voor modules als resources.

Zie [Bicep-modules gebruiken voor meer informatie.](bicep-modules.md)

## <a name="resource-and-module-decorators"></a>Resource- en moduledelers

U kunt eenator toevoegen aan een resource- of moduledefinitie. De enige ondersteundeator is `batchSize(int)` . U kunt deze alleen toepassen op een resource- of moduledefinitie die gebruikmaakt van een `for` expressie.

Standaard worden resources parallel geïmplementeerd. U weet niet in welke volgorde ze zijn gefinisht. Wanneer u `batchSize` deator toevoegt, implementeert u exemplaren serieel. Gebruik het argument integer om het aantal exemplaren op te geven dat parallel moet worden geïmplementeerd.

```bicep
@batchSize(3)
resource storageAccountResources 'Microsoft.Storage/storageAccounts@2019-06-01' = [for storageName in storageAccounts: {
  ...
}]
```

Zie Serieel of [parallel voor meer informatie.](copy-resources.md#serial-or-parallel)

## <a name="outputs"></a>Uitvoerwaarden

Gebruik uitvoer om de waarde van de implementatie te retourneren. Normaal gesproken retourneert u een waarde van een geïmplementeerde resource wanneer u die waarde opnieuw moet gebruiken voor een andere bewerking.

```bicep
output storageEndpoint object = stg.properties.primaryEndpoints
```

Geef een [gegevenstype op](data-types.md) voor de uitvoerwaarde.

Zie [Outputs in templates (Uitvoer in sjablonen) voor meer informatie.](template-outputs.md)

## <a name="comments"></a>Opmerkingen

Gebruiken `//` voor opmerkingen van één regel of voor opmerkingen van meerdere `/* ... */` lijnen

In het volgende voorbeeld ziet u een opmerking met één regel.

```bicep
// This is your primary NIC.
resource nic1 'Microsoft.Network/networkInterfaces@2020-06-01' = {
   ...
}
```

In het volgende voorbeeld ziet u een opmerking met meerdere lijnen.

```bicep
/*
  This template assumes the key vault already exists and
  is in same subscription and resource group as the deployment.
*/
param existingKeyVaultName string
```

## <a name="multi-line-strings"></a>Tekenreeksen met meerdere lijnen

U kunt een tekenreeks in meerdere regels ops breken. Gebruik drie enkele aangavetekens om de tekenreeks met meerdere regel te `'''` starten en te beëindigen.

Tekens in de tekenreeks met meerdere lijnen worden verwerkt zoals ze zijn. Escapetekens zijn niet nodig. U kunt niet opnemen `'''` in de tekenreeks met meerdere lijnen. Tekenreeksinterpolatie wordt momenteel niet ondersteund.

U kunt de tekenreeks direct na het openen starten of `'''` een nieuwe regel opnemen. In beide gevallen bevat de resulterende tekenreeks geen nieuwe regel. Afhankelijk van de regeleinden in uw Bicep-bestand, worden nieuwe regels geïnterpreteerd als `\r\n` of `\n` .

In het volgende voorbeeld ziet u een tekenreeks met meerdere lijnen.

```bicep
var stringVar = '''
this is multi-line
  string with formatting
  preserved.
'''
```

Het voorgaande voorbeeld is gelijk aan de volgende JSON.

```json
"variables": {
  "stringVar": "this is multi-line\r\n  string with formatting\r\n  preserved.\r\n"
}
```

## <a name="next-steps"></a>Volgende stappen

Zie Wat is Bicep? voor een inleiding [tot Bicep.](bicep-overview.md)

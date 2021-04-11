---
title: Bicep en syntaxis van bestands structuur
description: Hierin worden de structuur en eigenschappen van een Bicep-bestand beschreven met declaratieve syntaxis.
ms.topic: conceptual
ms.date: 03/31/2021
ms.openlocfilehash: 09993ae9c08f53144de8e94e6555ad93bec681f6
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106168685"
---
# <a name="understand-the-structure-and-syntax-of-bicep-files"></a>De structuur en syntaxis van Bicep-bestanden begrijpen

In dit artikel wordt de structuur van een Bicep-bestand beschreven. Het bevat de verschillende secties van het bestand en de eigenschappen die beschikbaar zijn in deze secties.

Dit artikel is bedoeld voor gebruikers met een zekere kennis van Bicep-bestanden. Het bevat gedetailleerde informatie over de structuur van de sjabloon. Voor een stapsgewijze zelf studie die u door het proces van het maken van een Bicep-bestand leidt, raadpleegt u [zelf studie: het eerste Azure Resource Manager Bicep-bestand maken en implementeren](bicep-tutorial-create-first-bicep.md).

## <a name="template-format"></a>Sjabloonindeling

Een Bicep-bestand heeft de volgende elementen. De elementen kunnen in een wille keurige volg orde worden weer gegeven.

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

In het volgende voor beeld ziet u een implementatie van deze elementen.

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

## <a name="target-scope"></a>Doel bereik

Het doel bereik is standaard ingesteld op `resourceGroup` . Als u op het niveau van de resource groep implementeert, hoeft u het doel bereik niet in uw Bicep-bestand in te stellen.

De toegestane waarden zijn:

* **resourceGroup** : de standaard waarde, die wordt gebruikt voor [implementaties van resource groepen](deploy-to-resource-group.md).
* **abonnement** : wordt gebruikt voor [implementaties van abonnementen](deploy-to-subscription.md).
* **managementGroup** : wordt gebruikt voor [implementaties van beheer groepen](deploy-to-management-group.md).
* **Tenant** : wordt gebruikt voor [Tenant implementaties](deploy-to-tenant.md).

## <a name="parameters"></a>Parameters

Gebruik para meters voor waarden die moeten variëren voor verschillende implementaties. U kunt een standaard waarde definiëren voor de para meter die wordt gebruikt als er geen waarde wordt opgegeven tijdens de implementatie.

U kunt bijvoorbeeld een SKU-para meter toevoegen om verschillende grootten voor een resource op te geven. U kunt sjabloon functies gebruiken voor het maken van de standaard waarde, zoals het ophalen van de locatie van de resource groep.

```bicep
param storageSKU string = 'Standard_LRS'
param location string = resourceGroup().location
```

Zie [gegevens typen in sjablonen](data-types.md)voor de beschik bare gegevens typen.

Zie [para meters in sjablonen](template-parameters.md)voor meer informatie.

## <a name="parameter-decorators"></a>Para meters voor Constructors

U kunt een of meer versieers voor elke para meter toevoegen. Deze conversies definiëren de waarden die zijn toegestaan voor de para meter. In het volgende voor beeld worden de Sku's opgegeven die via het Bicep-bestand kunnen worden geïmplementeerd.

```bicep
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
param storageSKU string = 'Standard_LRS'
```

De volgende tabel beschrijft de beschik bare versie van versieer en hoe u deze kunt gebruiken.

| Decorator | Van toepassing op | Argument | Description |
| --------- | ---- | ----------- | ------- |
| toegestaan | all | matrix | Toegestane waarden voor de para meter. Gebruik deze decorator om er zeker van te zijn dat de gebruiker de juiste waarden geeft. |
| beschrijving | all | tekenreeks | Tekst waarin wordt uitgelegd hoe de para meter moet worden gebruikt. De beschrijving wordt weer gegeven voor gebruikers via de portal. |
| Lengte | matrix, teken reeks | int | De maximale lengte voor teken reeks-en matrix parameters. De waarde is inclusief. |
| maxValue | int | int | De maximum waarde voor de para meter integer. Deze waarde is inclusief. |
| metagegevens | all | object | Aangepaste eigenschappen die moeten worden toegepast op de para meter. Kan een eigenschap Description bevatten die gelijk is aan de beschrijving decorator. |
| minLength | matrix, teken reeks | int | De minimale lengte voor teken reeks-en matrix parameters. De waarde is inclusief. |
| minValue | int | int | De minimum waarde voor de para meter integer. Deze waarde is inclusief. |
| beveiligd | teken reeks, object | geen | Markeert de para meter als beveiligd. De waarde voor een beveiligde para meter wordt niet opgeslagen in de implementatie geschiedenis en wordt niet geregistreerd. Zie [beveiligde teken reeksen en objecten](data-types.md#secure-strings-and-objects)voor meer informatie. |

## <a name="variables"></a>Variabelen

Gebruik variabelen voor complexe expressies die worden herhaald in een Bicep-bestand. U kunt bijvoorbeeld een variabele toevoegen voor een resource naam die is gemaakt door verschillende waarden samen te voegen.

```bicep
var uniqueStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'
```

U geeft geen [gegevens type](data-types.md) op voor een variabele. In plaats daarvan wordt het gegevens type afgeleid van de waarde.

Zie [variabelen in sjablonen](template-variables.md)voor meer informatie.

## <a name="resource"></a>Resource

Gebruik het `resource` sleutel woord om een resource te definiëren die u wilt implementeren. De resource declaratie bevat een symbolische naam voor de resource. U gebruikt deze symbolische naam in andere delen van het Bicep-bestand als u een waarde uit de resource moet ophalen.

De resource verklaring bevat ook het resource type en de API-versie.

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

In uw resource verklaring neemt u de eigenschappen voor het resource type op. Deze eigenschappen zijn uniek voor elk resource type.

Zie [resource verklaring in sjablonen](resource-declaration.md)voor meer informatie.

Als u [een resource voorwaardelijk wilt implementeren](conditional-resource-deployment.md), voegt u een `if` expressie toe.

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

Als u [meer dan één exemplaar](https://github.com/Azure/bicep/blob/main/docs/spec/loops.md) van een resource type wilt implementeren, voegt u een `for` expressie toe. De expressie kan worden doorlopend voor leden van een matrix.

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

Met de symbolische naam kunt u naar de module verwijzen vanuit een andere locatie in het bestand. U kunt bijvoorbeeld een uitvoer waarde uit een module ophalen met behulp van de symbolische naam en de naam van de uitvoer waarde.

Net als bij resources kunt u een module voorwaardelijk of iteratief implementeren. De syntaxis is hetzelfde voor modules als resources.

Zie [Bicep-modules gebruiken](bicep-modules.md)voor meer informatie.

## <a name="resource-and-module-decorators"></a>Resource-en module-conversies

U kunt een decorator toevoegen aan een resource of module definitie. De enige ondersteunde decorator is `batchSize(int)` . U kunt deze alleen Toep assen op een resource of module definitie die gebruikmaakt van een `for` expressie.

Standaard worden bronnen parallel geïmplementeerd. U weet niet de volg orde waarin deze zijn voltooid. Wanneer u de `batchSize` decorator toevoegt, implementeert u instanties serieel. Gebruik het argument integer om het aantal exemplaren op te geven dat parallel moet worden geïmplementeerd.

```bicep
@batchSize(3)
resource storageAccountResources 'Microsoft.Storage/storageAccounts@2019-06-01' = [for storageName in storageAccounts: {
  ...
}]
```

Zie [serieel of parallel](copy-resources.md#serial-or-parallel)voor meer informatie.

## <a name="outputs"></a>Uitvoerwaarden

Gebruik uitvoer om de waarde van de implementatie te retour neren. Normaal gesp roken retourneert u een waarde uit een geïmplementeerde resource wanneer u deze waarde opnieuw moet gebruiken voor een andere bewerking.

```bicep
output storageEndpoint object = stg.properties.primaryEndpoints
```

Geef een [gegevens type](data-types.md) op voor de uitvoer waarde.

Zie [uitvoer in sjablonen](template-outputs.md)voor meer informatie.

## <a name="comments"></a>Opmerkingen

Gebruiken `//` voor opmerkingen met één regel of `/* ... */` voor opmerkingen met meerdere regels

In het volgende voor beeld ziet u een opmerking met één regel.

```bicep
// This is your primary NIC.
resource nic1 'Microsoft.Network/networkInterfaces@2020-06-01' = {
   ...
}
```

In het volgende voor beeld ziet u een opmerking met meerdere regels.

```bicep
/*
  This template assumes the key vault already exists and
  is in same subscription and resource group as the deployment.
*/
param existingKeyVaultName string
```

## <a name="multi-line-strings"></a>Reeksen met meerdere regels

U kunt een teken reeks opsplitsen in meerdere regels. Gebruik drie enkele aanhalings tekens `'''` om de teken reeks met meerdere regels te starten en te beëindigen. 

De tekens in de teken reeks met meerdere regels worden verwerkt als-is. Escape-tekens zijn niet nodig. U kunt `'''` in de teken reeks met meerdere regels niet gebruiken. Interpolatie van teken reeksen wordt momenteel niet ondersteund.

U kunt de teken reeks direct starten nadat het openen `'''` of toevoegen van een nieuwe regel. In beide gevallen bevat de resulterende teken reeks geen nieuwe regel. Afhankelijk van de regel die in uw Bicep-bestand eindigt, worden nieuwe regels geïnterpreteerd als `\r\n` of `\n` .

In het volgende voor beeld ziet u een teken reeks met meerdere regels.

```bicep
var stringVar = '''
this is multi-line
  string with formatting
  preserved.
'''
```

Het vorige voor beeld is gelijk aan de volgende JSON.

```json
"variables": {
  "stringVar": "this is multi-line\r\n  string with formatting\r\n  preserved.\r\n"
}
```

## <a name="next-steps"></a>Volgende stappen

Zie [Wat is Bicep?](bicep-overview.md)voor een inleiding tot Bicep.

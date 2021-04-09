---
title: Bicep-modules
description: Hierin wordt beschreven hoe u een module definieert en gebruikt, en hoe u module bereik kunt gebruiken.
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: 7a680e8aa0fa4d5ef9cac7f9e7ba07a3aa4ee1e2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105611732"
---
# <a name="use-bicep-modules-preview"></a>Bicep-modules (preview-versie) gebruiken

Met Bicep kunt u een complexe oplossing in modules opsplitsen. Een Bicep-module is een verzameling van een of meer resources die samen moeten worden geïmplementeerd. Modules abstracten complexe details van de onbewerkte resource declaratie, waardoor de Lees baarheid kan toenemen. U kunt deze modules opnieuw gebruiken en delen met anderen. Gecombineerd met [sjabloon specificaties](./template-specs.md)maakt het een manier om de modulariteit en code hergebruik toe te voegen. Zie [zelf studie: Bicep-modules toevoegen](./bicep-tutorial-add-modules.md)voor een zelf studie.

## <a name="define-modules"></a>Modules definiëren

Elk Bicep-bestand kan worden gebruikt als een module. Een module bevat alleen para meters en uitvoer als contract voor andere Bicep-bestanden. Beide para meters en uitvoer waarden zijn optioneel.

Het volgende Bicep-bestand kan rechtstreeks worden geïmplementeerd om een opslag account te maken of als module te worden gebruikt.  In de volgende sectie ziet u hoe u modules kunt gebruiken:

```bicep
@minLength(3)
@maxLength(11)
param storagePrefix string

@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_RAGRS'
  'Standard_ZRS'
  'Premium_LRS'
  'Premium_ZRS'
  'Standard_GZRS'
  'Standard_RAGZRS'
])
param storageSKU string = 'Standard_LRS'
param location string

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

output storageEndpoint object = stg.properties.primaryEndpoints
```

Uitvoer wordt gebruikt om waarden door te geven aan de bovenliggende Bicep-bestanden.

## <a name="consume-modules"></a>Modules gebruiken

Gebruik het sleutel woord _module_ om een module te gebruiken. Het volgende Bicep-bestand implementeert de resource die is gedefinieerd in het module bestand waarnaar wordt verwezen:

```bicep
@minLength(3)
@maxLength(11)
param namePrefix string
param location string = resourceGroup().location

module stgModule './storageAccount.bicep' = {
  name: 'storageDeploy'
  params: {
    storagePrefix: namePrefix
    location: location
  }
}

output storageEndpoint object = stgModule.outputs.storageEndpoint
```

- **module**: sleutel woord.
- **symbolische naam** (stgModule): id voor de module.
- **module bestand**: het pad naar de module in dit voor beeld wordt opgegeven met een relatief pad (./storageAccount.Bicep). Alle paden in Bicep moeten worden opgegeven met het scheidings teken voor de slash (/) om ervoor te zorgen dat een consistente compilatie van meerdere platformen mogelijk is. Het Windows back slash- \\ teken () wordt niet ondersteund.
- De eigenschap **_name_** (storageDeploy) is vereist bij het gebruiken van een module. Wanneer Bicep de sjabloon IL genereert, wordt dit veld gebruikt als de naam van de geneste implementatie bron, die wordt gegenereerd voor de module:

    ```json
    ...
    ...
    "resources": [
      {
        "type": "Microsoft.Resources/deployments",
        "apiVersion": "2020-10-01",
        "name": "storageDeploy",
        "properties": {
          ...
        }
      }
    ]
    ...
    ```

Als u een uitvoer waarde wilt ophalen uit een module, haalt u de eigenschaps waarde op met de syntaxis like: `stgModule.outputs.storageEndpoint` WHERE `stgModule` de id van de module.

## <a name="configure-module-scopes"></a>Module bereik configureren

Wanneer u een module declareert, kunt u een _Scope_ -eigenschap opgeven om het bereik in te stellen waarmee de module moet worden geïmplementeerd:

```bicep
module stgModule './storageAccount.bicep' = {
  name: 'storageDeploy'
  scope: resourceGroup('someOtherRg') // pass in a scope to a different resourceGroup
  params: {
    storagePrefix: namePrefix
    location: location
  }
}
```

De eigenschap _Scope_ kan worden wegge laten wanneer het doel bereik van de module en het doel bereik van de bovenliggende groep hetzelfde zijn. Wanneer de bereik eigenschap niet wordt gegeven, wordt de module geïmplementeerd in het doel bereik van de bovenliggende.

In het volgende Bicep-bestand ziet u hoe u een resource groep maakt en een module implementeert voor de resource groep:

```bicep
// set the target scope for this file
targetScope = 'subscription'

@minLength(3)
@maxLength(11)
param namePrefix string

param location string = deployment().location

var resourceGroupName = '${namePrefix}rg'
resource myResourceGroup 'Microsoft.Resources/resourceGroups@2020-01-01' = {
  name: resourceGroupName
  location: location
  scope: subscription()
}

module stgModule './storageAccount.bicep' = {
  name: 'storageDeploy'
  scope: myResourceGroup
  params: {
    storagePrefix: namePrefix
    location: location
  }
}

output storageEndpoint object = stgModule.outputs.storageEndpoint
```

## <a name="next-steps"></a>Volgende stappen

- Zie [zelf studie: Bicep-modules toevoegen](./bicep-tutorial-add-modules.md)om een zelf studie te door lopen.

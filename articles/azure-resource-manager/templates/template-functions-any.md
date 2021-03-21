---
title: 'Sjabloon functies: elke'
description: Beschrijft de functie die beschikbaar is in Bicep om typen te converteren.
ms.topic: conceptual
author: tfitzmac
ms.author: tomfitz
ms.service: azure-resource-manager
ms.subservice: templates
ms.date: 03/02/2021
ms.openlocfilehash: b0cb51c9a79d1100de7f1ef32fe326eddcdd6dcc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101745855"
---
# <a name="any-function-for-bicep"></a>Een functie voor Bicep

Bicep ondersteunt een functie die wordt aangeroepen `any()` om type fouten op te lossen in het type systeem Bicep. U gebruikt deze functie als de indeling van de waarde die u opgeeft niet overeenkomt met het type systeem verwacht. Als de eigenschap bijvoorbeeld een getal vereist, maar u moet deze opgeven als een teken reeks, zoals `'0.5'` . Gebruik de `any()` functie om de fout die door het type systeem is gemeld, te onderdrukken.

Deze functie bestaat niet in de runtime van de Azure Resource Manager-sjabloon. Deze wordt alleen gebruikt door Bicep en wordt niet verzonden in de JSON voor de ingebouwde sjabloon.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="any"></a>alle

`any(value)`

Retourneert een waarde die compatibel is met elk gegevens type.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| waarde | Ja | alle typen | De waarde die moet worden geconverteerd naar een compatibel type. |

### <a name="return-value"></a>Retourwaarde

De waarde in een formulier die compatibel is met elk gegevens type.

### <a name="examples"></a>Voorbeelden

In de volgende voorbeeld sjabloon ziet u hoe u de `any()` functie gebruikt om numerieke waarden op te geven als teken reeksen.

```bicep
resource wpAci 'microsoft.containerInstance/containerGroups@2019-12-01' = {
  name: 'wordpress-containerinstance'
  location: location
  properties: {
    containers: [
      {
        name: 'wordpress'
        properties: {
          ...
          resources: {
            requests: {
              cpu: any('0.5')
              memoryInGB: any('0.7')
            }
          }
        }
      }
    ]
  }
}
```

De functie werkt op een toegewezen waarde in Bicep. In het volgende voor beeld wordt `any()` met een ternaire expressie als argument gebruikt.  

```bicep
publicIPAddress: any((pipId == '') ? null : {
  id: pipId
})
```

## <a name="next-steps"></a>Volgende stappen

Zie de volgende voor beelden voor complexere toepassingen van de `any()` functie:

* [Onderliggende resources waarvoor een specifieke naam is vereist](https://github.com/Azure/bicep/blob/main/docs/examples/201/api-management-create-all-resources/main.bicep#L246)
* [Er is geen bron eigenschap gedefinieerd in het type van de resource, zelfs als deze bestaat](https://github.com/Azure/bicep/blob/main/docs/examples/201/log-analytics-with-solutions-and-diagnostics/main.bicep#L26)


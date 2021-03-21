---
title: Sjabloon functies-datum
description: Hierin worden de functies beschreven die u kunt gebruiken in een Azure Resource Manager sjabloon (ARM-sjabloon) om met datums te werken.
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: 58d865f109ecca2629b89eeb55e554743824c195
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96920492"
---
# <a name="date-functions-for-arm-templates"></a>Datum functies voor ARM-sjablonen

Resource Manager biedt de volgende functies voor het werken met datums in uw Azure Resource Manager sjabloon (ARM-sjabloon):

* [dateTimeAdd](#datetimeadd)
* [utcNow](#utcnow)

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="datetimeadd"></a>dateTimeAdd

`dateTimeAdd(base, duration, [format])`

Voegt een tijds duur toe aan een basis waarde. ISO 8601-indeling wordt verwacht.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| base | Ja | tekenreeks | De begin datum/tijd-waarde voor de toevoeging. [ISO 8601-time stamp notatie](https://en.wikipedia.org/wiki/ISO_8601)gebruiken. |
| duur | Ja | tekenreeks | De tijd waarde die aan de basis moet worden toegevoegd. Dit kan een negatieve waarde zijn. Gebruik de [ISO 8601-duur notatie](https://en.wikipedia.org/wiki/ISO_8601#Durations). |
| indeling | Nee | tekenreeks | De uitvoer indeling voor het resultaat van de datum en tijd. Als u dit niet opgeeft, wordt de indeling van de basis waarde gebruikt. Gebruik [standaard notatie teken reeksen](/dotnet/standard/base-types/standard-date-and-time-format-strings) of [teken reeksen met aangepaste notaties](/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="return-value"></a>Retourwaarde

De waarde voor datum/tijd die het resultaat is van het toevoegen van de waarde duration aan de basis waarde.

### <a name="examples"></a>Voorbeelden

In de volgende voorbeeld sjabloon ziet u verschillende manieren om tijd waarden toe te voegen.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "baseTime": {
      "type": "string",
      "defaultValue": "[utcNow('u')]"
    }
  },
  "variables": {
    "add3Years": "[dateTimeAdd(parameters('baseTime'), 'P3Y')]",
    "subtract9Days": "[dateTimeAdd(parameters('baseTime'), '-P9D')]",
    "add1Hour": "[dateTimeAdd(parameters('baseTime'), 'PT1H')]"
  },
  "resources": [],
  "outputs": {
    "add3YearsOutput": {
      "value": "[variables('add3Years')]",
      "type": "string"
    },
    "subtract9DaysOutput": {
      "value": "[variables('subtract9Days')]",
      "type": "string"
    },
    "add1HourOutput": {
      "value": "[variables('add1Hour')]",
      "type": "string"
    },
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param baseTime string = utcNow('u')

var add3Years = dateTimeAdd(baseTime, 'P3Y')
var subtract9Days = dateTimeAdd(baseTime, '-P9D')
var add1Hour = dateTimeAdd(baseTime, 'PT1H')

output add3YearsOutput string = add3Years
output subtract9DaysOutput string = subtract9Days
output add1HourOutput string = add1Hour
```

---

Wanneer de voor gaande sjabloon wordt geïmplementeerd met een basis tijd van `2020-04-07 14:53:14Z` , is de uitvoer:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| add3YearsOutput | Tekenreeks | 4/7/2023 2:53:14 UUR |
| subtract9DaysOutput | Tekenreeks | 3/29/2020 2:53:14 UUR |
| add1HourOutput | Tekenreeks | 4/7/2020 3:53:14 UUR |

In de volgende voorbeeld sjabloon ziet u hoe u de start tijd voor een Automation-schema kunt instellen.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "omsAutomationAccountName": {
      "type": "string",
      "defaultValue": "demoAutomation",
      "metadata": {
        "description": "Use an existing Automation account."
      }
    },
    "scheduleName": {
      "type": "string",
      "defaultValue": "demoSchedule1",
      "metadata": {
        "description": "Name of the new schedule."
      }
    },
    "baseTime": {
      "type": "string",
      "defaultValue": "[utcNow('u')]",
      "metadata": {
        "description": "Schedule will start one hour from this time."
      }
    }
  },
  "variables": {
    "startTime": "[dateTimeAdd(parameters('baseTime'), 'PT1H')]"
  },
  "resources": [
    ...
    {
      "type": "Microsoft.Automation/automationAccounts/schedules",
      "apiVersion": "2015-10-31",
      "name": "[concat(parameters('omsAutomationAccountName'), '/', parameters('scheduleName'))]",

      "properties": {
        "description": "Demo Scheduler",
        "startTime": "[variables('startTime')]",
        "interval": 1,
        "frequency": "Hour"
      }
    }
  ],
  "outputs": {
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param omsAutomationAccountName string = 'demoAutomation'
param scheduleName string = 'demSchedule1'
param baseTime string = utcNow('u')

var startTime = dateTimeAdd(baseTime, 'PT1H')

...

resource scheduler 'Microsoft.Automation/automationAccounts/schedules@2015-10-31' = {
  name: concat(omsAutomationAccountName, '/', scheduleName)
  properties: {
    description: 'Demo Scheduler'
    startTime: startTime
    interval: 1
    frequency: 'Hour'
  }
}
```

---

## <a name="utcnow"></a>utcNow

`utcNow(format)`

Retourneert de huidige (UTC) datum/tijd-waarde in de opgegeven notatie. Als er geen indeling wordt gegeven, wordt de ISO 8601 ( `yyyyMMddTHHmmssZ` )-indeling gebruikt. **Deze functie kan alleen worden gebruikt in de standaard waarde voor een para meter.**

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| indeling |Nee |tekenreeks |De gecodeerde URI-waarde die moet worden geconverteerd naar een teken reeks. Gebruik [standaard notatie teken reeksen](/dotnet/standard/base-types/standard-date-and-time-format-strings) of [teken reeksen met aangepaste notaties](/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="remarks"></a>Opmerkingen

U kunt deze functie alleen gebruiken in een expressie voor de standaard waarde van een para meter. Als u deze functie ergens anders in een sjabloon gebruikt, wordt er een fout geretourneerd. De functie is niet toegestaan in andere onderdelen van de sjabloon omdat deze een andere waarde retourneert telkens wanneer deze wordt aangeroepen. Het implementeren van dezelfde sjabloon met dezelfde para meters zou geen betrouw bare resultaten opleveren.

Als u de [optie voor het terugdraaien van een fout](rollback-on-error.md) naar een eerdere geslaagde implementatie gebruikt en de eerdere implementatie een para meter bevat waarin utcNow wordt gebruikt, wordt de para meter niet opnieuw geëvalueerd. In plaats daarvan wordt de parameter waarde van de eerdere implementatie automatisch opnieuw gebruikt in de rollback-implementatie.

Wees voorzichtig met het opnieuw implementeren van een sjabloon die afhankelijk is van de functie utcNow voor een standaard waarde. Wanneer u opnieuw implementeert en geen waarde opgeeft voor de para meter, wordt de functie opnieuw geëvalueerd. Als u een bestaande resource wilt bijwerken in plaats van een nieuwe te maken, geeft u de parameter waarde van de eerdere implementatie door.

### <a name="return-value"></a>Retourwaarde

De huidige UTC-waarde voor datum/tijd.

### <a name="examples"></a>Voorbeelden

In de volgende voorbeeld sjabloon ziet u verschillende notaties voor de datum/tijd-waarde.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "utcValue": {
      "type": "string",
      "defaultValue": "[utcNow()]"
    },
    "utcShortValue": {
      "type": "string",
      "defaultValue": "[utcNow('d')]"
    },
    "utcCustomValue": {
      "type": "string",
      "defaultValue": "[utcNow('M d')]"
    }
  },
  "resources": [
  ],
  "outputs": {
    "utcOutput": {
      "type": "string",
      "value": "[parameters('utcValue')]"
    },
    "utcShortOutput": {
      "type": "string",
      "value": "[parameters('utcShortValue')]"
    },
    "utcCustomOutput": {
      "type": "string",
      "value": "[parameters('utcCustomValue')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param utcValue string = utcNow()
param utcShortValue string = utcNow('d')
param utcCustomValue string = utcNow('M d')

output utcOutput string = utcValue
output utcShortOutput string = utcShortValue
output utcCustomOutput string = utcCustomValue
```

---

De uitvoer van het voor gaande voor beeld varieert per implementatie, maar lijkt op:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| utcOutput | tekenreeks | 20190305T175318Z |
| utcShortOutput | tekenreeks | 03/05/2019 |
| utcCustomOutput | tekenreeks | 3 5 |

In het volgende voor beeld ziet u hoe u een waarde uit de functie gebruikt wanneer u een tag-waarde instelt.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "utcShort": {
      "type": "string",
      "defaultValue": "[utcNow('d')]"
    },
    "rgName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2018-05-01",
      "name": "[parameters('rgName')]",
      "location": "westeurope",
      "tags": {
        "createdDate": "[parameters('utcShort')]"
      },
      "properties": {}
    }
  ],
  "outputs": {
    "utcShortOutput": {
      "type": "string",
      "value": "[parameters('utcShort')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param utcShort string = utcNow('d')
param rgName string

resource myRg 'Microsoft.Resources/resourceGroups@2018-05-01' = {
  name: rgName
  location: 'westeurope'
  tags: {
    createdDate: utcShort
  }
}

output utcShortOutput string = utcShort
```

---

## <a name="next-steps"></a>Volgende stappen

* Zie [inzicht krijgen in de structuur en syntaxis van arm-sjablonen](template-syntax.md)voor een beschrijving van de secties in een arm-sjabloon.

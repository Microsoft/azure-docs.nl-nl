---
title: Azure-blauw drukken-functies
description: Hierin worden de functies beschreven die beschikbaar zijn voor gebruik met blauw drukken artefacten in definities en toewijzingen van Azure.
ms.date: 01/27/2021
ms.topic: reference
ms.openlocfilehash: 92cb906e87179073b7a69aa0bd4eab22c77087f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98919271"
---
# <a name="functions-for-use-with-azure-blueprints"></a>Functies voor gebruik met Azure-blauw drukken

Azure-blauw drukken biedt functies die een definitie van een blauw druk maken. Deze functies zijn voor gebruik met blauw drukken-definities en blauw drukken-artefacten. Een sjabloon voor Azure Resource Manager (ARM-sjabloon) ondersteunt het volledige gebruik van Resource Manager-functies naast het ophalen van een dynamische waarde via een blauw druk-para meter.

De volgende functies worden ondersteund:

- [artefacten](#artifacts)
- [concat](#concat)
- [instellen](#parameters)
- [resourceGroup](#resourcegroup)
- [resourceGroups](#resourcegroups)
- [abonnees](#subscription)

## <a name="artifacts"></a>artefacten

`artifacts(artifactName)`

Retourneert een object met eigenschappen die zijn ingevuld met de uitvoer van de blauw druk artefacten.

> [!NOTE]
> De `artifacts()` functie kan niet worden gebruikt vanuit een arm-sjabloon. De functie kan alleen worden gebruikt in de JSON van de blauw druk of in de JSON van de artefact bij het beheren van de blauw druk met Azure PowerShell of REST API als onderdeel van [blauw drukken-als-code](https://github.com/Azure/azure-blueprints/blob/master/README.md).

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| artefactnaam |Yes |tekenreeks |De naam van een blauw druk artefact. |

### <a name="return-value"></a>Retourwaarde

Een object met uitvoer eigenschappen. De **uitvoer** eigenschappen zijn afhankelijk van het type blauw druk artefact waarnaar wordt verwezen. Alle typen hebben de volgende indeling:

```json
{
  "outputs": {collectionOfOutputProperties}
}
```

#### <a name="policy-assignment-artifact"></a>Beleids toewijzings artefact

```json
{
    "outputs": {
        "policyAssignmentId": "{resourceId-of-policy-assignment}",
        "policyAssignmentName": "{name-of-policy-assignment}",
        "policyDefinitionId": "{resourceId-of-policy-definition}",
    }
}
```

#### <a name="arm-template-artifact"></a>ARM-sjabloon artefact

De **uitvoer** eigenschappen van het geretourneerde object worden gedefinieerd in de arm-sjabloon en geretourneerd door de implementatie.

#### <a name="role-assignment-artifact"></a>Rollen toewijzings artefact

```json
{
    "outputs": {
        "roleAssignmentId": "{resourceId-of-role-assignment}",
        "roleDefinitionId": "{resourceId-of-role-definition}",
        "principalId": "{principalId-role-is-being-assigned-to}",
    }
}
```

### <a name="example"></a>Voorbeeld

Een ARM-sjabloon artefact met de ID _myTemplateArtifact_ die de volgende voorbeeld uitvoer eigenschap bevat:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    ...
    "outputs": {
        "myArray": {
            "type": "array",
            "value": ["first", "second"]
        },
        "myString": {
            "type": "string",
            "value": "my string value"
        },
        "myObject": {
            "type": "object",
            "value": {
                "myProperty": "my value",
                "anotherProperty": true
            }
        }
    }
}
```

Enkele voor beelden van het ophalen van gegevens uit het _myTemplateArtifact_ -voor beeld zijn:

| Expression | Type | Waarde |
|:---|:---|:---|
|`[artifacts("myTemplateArtifact").outputs.myArray]` | Matrix | \[' First ', ' Second '\] |
|`[artifacts("myTemplateArtifact").outputs.myArray[0]]` | Tekenreeks | instantie |
|`[artifacts("myTemplateArtifact").outputs.myString]` | Tekenreeks | ' mijn teken reeks waarde ' |
|`[artifacts("myTemplateArtifact").outputs.myObject]` | Object | {"myProperty": "mijn waarde", "anotherProperty": True} |
|`[artifacts("myTemplateArtifact").outputs.myObject.myProperty]` | Tekenreeks | ' mijn waarde ' |
|`[artifacts("myTemplateArtifact").outputs.myObject.anotherProperty]` | Booleaanse waarde | True |

## <a name="concat"></a>concat

`concat(string1, string2, string3, ...)`

Combineert meerdere teken reeks waarden en retourneert de aaneengeschakelde teken reeks.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| tekenreeks1 |Yes |tekenreeks |De eerste waarde voor samen voegen. |
| aanvullende argumenten |No |tekenreeks |Aanvullende waarden in sequentiële volg orde voor samen voeging |

### <a name="return-value"></a>Retourwaarde

Een teken reeks met samengevoegde waarden.

### <a name="remarks"></a>Opmerkingen

De functie Azure Blueprint verschilt van de ARM-sjabloon functie in dat deze alleen werkt met teken reeksen.

### <a name="example"></a>Voorbeeld

`concat(parameters('organizationName'), '-vm')`

## <a name="parameters"></a>parameters

`parameters(parameterName)`

Retourneert een waarde voor de para meter blauw druk. De opgegeven parameter naam moet worden gedefinieerd in de definitie van de blauw druk of in de artefacten blauw drukken.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| parameterName |Yes |tekenreeks |De naam van de para meter die moet worden geretourneerd. |

### <a name="return-value"></a>Retourwaarde

De waarde van de opgegeven artefact parameter voor blauw drukken of blauw drukken.

### <a name="remarks"></a>Opmerkingen

De functie Azure Blueprint verschilt van de ARM-sjabloon functie in dat deze alleen werkt met blauw drukken-para meters.

### <a name="example"></a>Voorbeeld

Definieer de para meter _principalIds_ in de definitie van de blauw druk:

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "parameters": {
            "principalIds": {
                "type": "array",
                "metadata": {
                    "displayName": "Principal IDs",
                    "description": "This is a blueprint parameter that any artifact can reference. We'll display these descriptions for you in the info bubble. Supply principal IDs for the users,groups, or service principals for the Azure role assignment.",
                    "strongType": "PrincipalId"
                }
            }
        },
        ...
    }
}
```

Gebruik vervolgens _principalIds_ als argument voor `parameters()` in een blauw druk-artefact:

```json
{
    "type": "Microsoft.Blueprint/blueprints/artifacts",
    "kind": "roleAssignment",
    ...
    "properties": {
        "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
        "principalIds": "[parameters('principalIds')]",
        ...
    }
}
```

## <a name="resourcegroup"></a>resourceGroup

`resourceGroup()`

Retourneert een object dat de huidige resource groep vertegenwoordigt.

### <a name="return-value"></a>Retourwaarde

Het geretourneerde object heeft de volgende indeling:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="remarks"></a>Opmerkingen

De functie Azure Blueprint verschilt van de functie ARM-sjabloon. De `resourceGroup()` functie kan niet worden gebruikt in een artefact op abonnements niveau of de definitie van de blauw druk. Deze kan alleen worden gebruikt in de artefacten blauw drukken die deel uitmaken van een resource groeps artefact.

Een veelvoorkomend gebruik van de `resourceGroup()` functie is het maken van resources op dezelfde locatie als het artefact van de resource groep.

### <a name="example"></a>Voorbeeld

Als u de locatie van de resource groep wilt gebruiken, stelt u in de blauw druk of tijdens toewijzing in als de locatie voor een ander artefact, declareert u een object voor de tijdelijke aanduiding van een resource groep in de definitie van de blauw druk. In dit voor beeld is _NetworkingPlaceholder_ de naam van de tijdelijke aanduiding voor de resource groep.

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "resourceGroups": {
            "NetworkingPlaceholder": {
                "location": "eastus"
            }
        }
    }
}
```

Gebruik vervolgens de `resourceGroup()` functie in de context van een blauw druk artefact dat is gericht op een object met een tijdelijke aanduiding voor een resource groep. In dit voor beeld wordt het sjabloon artefact geïmplementeerd in de resource groep _NetworkingPlaceholder_ en worden para meter _resourceLocation_ dynamisch gevuld met de locatie van de resource groep _NetworkingPlaceholder_ voor de sjabloon. De locatie van de _NetworkingPlaceholder_ -resource groep is mogelijk statisch gedefinieerd voor de definitie van de blauw druk of dynamisch gedefinieerd tijdens de toewijzing. In beide gevallen wordt het sjabloon artefact gegeven met informatie als een para meter en gebruikt om de resources te implementeren op de juiste locatie.

```json
{
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "kind": "template",
  "properties": {
      "template": {
        ...
      },
      "resourceGroup": "NetworkingPlaceholder",
      ...
      "parameters": {
        "resourceLocation": {
          "value": "[resourceGroup().location]"
        }
      }
  }
}
```

## <a name="resourcegroups"></a>resourceGroups

`resourceGroups(placeholderName)`

Retourneert een object dat het opgegeven bron groeps artefact vertegenwoordigt. In tegens telling tot `resourceGroup()` , waarvoor context van het artefact is vereist, wordt deze functie gebruikt om de eigenschappen van een specifieke tijdelijke aanduiding van een resource groep op te halen wanneer deze niet in de context van die resource groep is.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| tijdelijke aanduiding |Yes |tekenreeks |De naam van de tijdelijke aanduiding van het bron groeps artefact dat moet worden geretourneerd. |

### <a name="return-value"></a>Retourwaarde

Het geretourneerde object heeft de volgende indeling:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="example"></a>Voorbeeld

Als u de locatie van de resource groep wilt gebruiken, stelt u in de blauw druk of tijdens toewijzing in als de locatie voor een ander artefact, declareert u een object voor de tijdelijke aanduiding van een resource groep in de definitie van de blauw druk. In dit voor beeld is _NetworkingPlaceholder_ de naam van de tijdelijke aanduiding voor de resource groep.

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "resourceGroups": {
            "NetworkingPlaceholder": {
                "location": "eastus"
            }
        }
    }
}
```

Gebruik vervolgens de `resourceGroups()` functie uit de context van een blauw druk artefact om een verwijzing naar het object van de tijdelijke aanduiding voor de resource groep op te halen. In dit voor beeld wordt het sjabloon artefact geïmplementeerd buiten de resource groep _NetworkingPlaceholder_ en worden para meter _artifactLocation_ dynamisch gevuld met de locatie van de resource groep _NetworkingPlaceholder_ voor de sjabloon. De locatie van de _NetworkingPlaceholder_ -resource groep is mogelijk statisch gedefinieerd voor de definitie van de blauw druk of dynamisch gedefinieerd tijdens de toewijzing. In beide gevallen wordt het sjabloon artefact gegeven met informatie als een para meter en gebruikt om de resources te implementeren op de juiste locatie.

```json
{
  "kind": "template",
  "properties": {
      "template": {
          ...
      },
      ...
      "parameters": {
        "artifactLocation": {
          "value": "[resourceGroups('NetworkingPlaceholder').location]"
        }
      }
  },
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "name": "myTemplate"
}
```

## <a name="subscription"></a>abonnement

`subscription()`

Retourneert Details over het abonnement voor de huidige blauw druk-toewijzing.

### <a name="return-value"></a>Retourwaarde

Het geretourneerde object heeft de volgende indeling:

```json
{
    "id": "/subscriptions/{subscriptionId}",
    "subscriptionId": "{subscriptionId}",
    "tenantId": "{tenantId}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="example"></a>Voorbeeld

Gebruik de weergave naam van het abonnement en de `concat()` functie om een naamgevings Conventie te maken die is door gegeven als parameter _bron_ voor het sjabloon artefact.

```json
{
  "kind": "template",
  "properties": {
      "template": {
          ...
      },
      ...
      "parameters": {
        "resourceName": {
          "value": "[concat(subscription().displayName, '-vm')]"
        }
      }
  },
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "name": "myTemplate"
}
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [levenscyclus van een blauwdruk](../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../concepts/resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../how-to/update-existing-assignments.md).
- Problemen oplossen tijdens de toewijzing van een blauwdruk met [algemene probleemoplossing](../troubleshoot/general.md).
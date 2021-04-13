---
title: Beleid voor het schrijven van matrix eigenschappen voor bronnen
description: Meer informatie over het werken met matrix parameters en matrix-taal expressies, de alias [*] evalueren en elementen toevoegen met Azure Policy definitie regels.
ms.date: 03/31/2021
ms.topic: how-to
ms.openlocfilehash: 18afbee0ca8b1c488e3bd3ce50dacc726bd2ef25
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305188"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Beleid voor het schrijven van matrix eigenschappen op Azure-resources

Azure Resource Manager eigenschappen worden meestal gedefinieerd als teken reeksen en Booleaanse waarden. Als er sprake is van een een-op-veel-relatie, worden complexe eigenschappen in plaats daarvan gedefinieerd als matrices. In Azure Policy worden matrices op verschillende manieren gebruikt:

- Het type [definitie parameter](../concepts/definition-structure.md#parameters), om meerdere opties te bieden
- Onderdeel van een [beleids regel](../concepts/definition-structure.md#policy-rule) met de voor waarden **in** of **notIn**
- Onderdeel van een beleids regel die telt hoeveel matrix leden voldoen aan een voor waarde
- In de effecten [toevoegen](../concepts/effects.md#append) en [wijzigen](../concepts/effects.md#modify) om een bestaande matrix bij te werken

In dit artikel wordt elk gebruik door Azure Policy beschreven en worden verschillende voorbeeld definities geboden.

## <a name="parameter-arrays"></a>Parameter matrices

### <a name="define-a-parameter-array"></a>Een parameter matrix definiëren

Als u een para meter als een matrix definieert, kan het beleid flexibel zijn wanneer er meer dan één waarde nodig is.
Met deze beleids definitie kunt u één locatie voor de para meter **allowedLocations** en de standaard waarden voor _eastus2_:

```json
"parameters": {
    "allowedLocations": {
        "type": "string",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2"
    }
}
```

Als **type** is _teken reeks_, kan slechts één waarde worden ingesteld wanneer het beleid wordt toegewezen. Als dit beleid is toegewezen, zijn resources in het bereik alleen toegestaan binnen één Azure-regio. De meeste beleids definities moeten een lijst met goedgekeurde opties toestaan, zoals het toestaan van _eastus2_, _oostus_ en _westus2_.

Als u de beleids definitie wilt maken om meerdere opties toe te staan, gebruikt u het _matrix_ **type**. Hetzelfde beleid kan als volgt worden herschreven:

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2",
        "allowedValues": [
            "eastus2",
            "eastus",
            "westus2"
        ]

    }
}
```

> [!NOTE]
> Zodra een beleids definitie is opgeslagen, kan de eigenschap **type** van een para meter niet worden gewijzigd.

Deze nieuwe parameter definitie heeft meer dan één waarde tijdens de beleids toewijzing. Met de matrix eigenschap **allowedValues** gedefinieerd, worden de waarden die beschikbaar zijn tijdens de toewijzing, verder beperkt tot de vooraf gedefinieerde lijst met opties. Het gebruik van **allowedValues** is optioneel.

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>Waarden door geven aan een parameter matrix tijdens toewijzing

Wanneer u het beleid toewijst via de Azure Portal, wordt een para meter van het **type** _matrix_ weer gegeven als één tekstvak. De hint zegt ' use; om waarden te scheiden. (bijvoorbeeld Londen; New York) ". Gebruik de volgende teken reeks om de toegestane locatie waarden van _eastus2_, _oostus_ en _westus2_ aan de para meter door te geven:

`eastus2;eastus;westus2`

De notatie voor de parameter waarde is anders wanneer u Azure CLI, Azure PowerShell of de REST API gebruikt. De waarden worden door gegeven via een JSON-teken reeks die ook de naam van de para meter bevat.

```json
{
    "allowedLocations": {
        "value": [
            "eastus2",
            "eastus",
            "westus2"
        ]
    }
}
```

Gebruik de volgende opdrachten om deze teken reeks te gebruiken voor elke SDK:

- Azure CLI: opdracht [AZ Policy Assignment Create](/cli/azure/policy/assignment#az_policy_assignment_create) with para meter **params**
- Azure PowerShell: cmdlet [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) met para meter **PolicyParameter**
- REST API: in de _put_ -bewerking [maken](/rest/api/policy/policyassignments/create) als onderdeel van de aanvraag tekst als de waarde van de **Eigenschappen. para meters** -eigenschap

## <a name="using-arrays-in-conditions"></a>Matrices in voor waarden gebruiken

### <a name="in-and-notin"></a>In en notIn

De `in` en `notIn` voor waarden werken alleen met matrix waarden. Ze controleren het bestaan van een waarde in een matrix. De matrix kan een letterlijke JSON-matrix of een verwijzing naar een matrix parameter zijn. Bijvoorbeeld:

```json
{
      "field": "tags.environment",
      "in": [ "dev", "test" ]
}
```

```json
{
      "field": "location",
      "notIn": "[parameters('allowedLocations')]"
}
```

### <a name="value-count"></a>Aantal waarden

De expressie aantal [waarden](../concepts/definition-structure.md#value-count) telt het aantal matrix leden dat voldoet aan een voor waarde. Het biedt een manier om dezelfde voor waarde meermaals te evalueren, waarbij verschillende waarden worden gebruikt voor elke iteratie. Met de volgende voor waarde wordt bijvoorbeeld gecontroleerd of de resource naam overeenkomt met een patroon uit een matrix met patronen:

```json
{
    "count": {
        "value": [ "test*", "dev*", "prod*" ],
        "name": "pattern",
        "where": {
            "field": "name",
            "like": "[current('pattern')]"
        }
    },
    "greater": 0
}
```

Voor het evalueren van de expressie, Azure Policy de `where` voor waarde drie keer geëvalueerd, eenmaal voor elk lid van `[ "test*", "dev*", "prod*" ]` , tellen hoe vaak het is geëvalueerd `true` . Bij elke iteratie wordt de waarde van het huidige matrixlid gekoppeld aan de `pattern` index naam die is gedefinieerd door `count.name` . In de voor waarde kan naar deze waarde worden verwezen `where` door een speciale sjabloon functie aan te roepen: `current('pattern')` .

| Iteratie | `current('pattern')` geretourneerde waarde |
|:---|:---|
| 1 | `"test*"` |
| 2 | `"dev*"` |
| 3 | `"prod*"` |

De voor waarde is alleen waar als het aantal results groter is dan 0.

Als u de voor waarde boven meer algemeen wilt instellen, gebruikt u parameter verwijzing in plaats van een letterlijke matrix:

 ```json
{
    "count": {
        "value": "[parameters('patterns')]",
        "name": "pattern",
        "where": {
            "field": "name",
            "like": "[current('pattern')]"
        }
    },
    "greater": 0
}
```

Als de expressie **aantal waarden** niet onder een andere expressie **Count** `count.name` valt, is optioneel en `current()` kan de functie zonder argumenten worden gebruikt:

```json
{
    "count": {
        "value": "[parameters('patterns')]",
        "where": {
            "field": "name",
            "like": "[current()]"
        }
    },
    "greater": 0
}
```

**Aantal waarden** biedt ook ondersteuning voor matrices van complexe objecten, waardoor complexere voor waarden worden toegestaan. Met de volgende voor waarde wordt bijvoorbeeld een gewenste label waarde voor elk naam patroon gedefinieerd en wordt gecontroleerd of de resource naam overeenkomt met het patroon, maar heeft de vereiste label waarde niet:

```json
{
    "count": {
        "value": [
            { "pattern": "test*", "envTag": "dev" },
            { "pattern": "dev*", "envTag": "dev" },
            { "pattern": "prod*", "envTag": "prod" },
        ],
        "name": "namePatternRequiredTag",
        "where": {
            "allOf": [
                {
                    "field": "name",
                    "like": "[current('namePatternRequiredTag').pattern]"
                },
                {
                    "field": "tags.env",
                    "notEquals": "[current('namePatternRequiredTag').envTag]"
                }
            ]
        }
    },
    "greater": 0
}
```

Zie voor [beelden van aantal waarden](../concepts/definition-structure.md#value-count-examples)voor nuttige voor beelden.

## <a name="referencing-array-resource-properties"></a>Verwijzen naar eigenschappen van matrix resources

In veel gevallen moet u werken met matrix eigenschappen in de geëvalueerde resource. In sommige scenario's moet worden verwezen naar een volledige matrix (bijvoorbeeld de lengte controleren). Anderen moeten een voor waarde Toep assen op elk afzonderlijk lid van de matrix (Controleer bijvoorbeeld of alle firewall regel toegang vanaf internet blok keren). Informatie over de verschillende manieren waarop Azure Policy kan verwijzen naar resource-eigenschappen, en hoe deze verwijzingen zich gedragen wanneer ze verwijzen naar matrix eigenschappen is de sleutel voor het schrijven van voor waarden die betrekking hebben op deze scenario's.

### <a name="referencing-resource-properties"></a>Verwijzen naar bron eigenschappen

Er kan naar resource-eigenschappen worden verwezen door Azure Policy met behulp van [aliassen](../concepts/definition-structure.md#aliases) er zijn twee manieren om te verwijzen naar de waarden van een bron eigenschap in azure Policy:

- Gebruik [veld](../concepts/definition-structure.md#fields) voorwaarde om te controleren of **alle** geselecteerde bron eigenschappen voldoen aan een voor waarde. Voorbeeld:

  ```json
  {
    "field" : "Microsoft.Test/resourceType/property",
    "equals": "value"
  }
  ```

- Gebruik `field()` de functie om toegang te krijgen tot de waarde van een eigenschap. Voorbeeld:

  ```json
  {
    "value": "[take(field('Microsoft.Test/resourceType/property'), 7)]",
    "equals": "prefix_"
  }
  ```

De veld voorwaarde heeft het impliciete gedrag ' alle '. Als de alias een verzameling waarden vertegenwoordigt, wordt gecontroleerd of alle afzonderlijke waarden voldoen aan de voor waarde. De `field()` functie retourneert de waarden die worden weer gegeven door de alias als-is, die vervolgens door andere sjabloon functies kan worden gemanipuleerd.

### <a name="referencing-array-fields"></a>Verwijzen naar matrix velden

Eigenschappen van matrix bronnen worden meestal vertegenwoordigd door twee verschillende typen aliassen. Een ' normale ' alias-en- [matrix aliassen](../concepts/definition-structure.md#understanding-the--alias) die `[*]` hieraan zijn gekoppeld:

- `Microsoft.Test/resourceType/stringArray`
- `Microsoft.Test/resourceType/stringArray[*]`

#### <a name="referencing-the-array"></a>Verwijzen naar de matrix

De eerste alias vertegenwoordigt één waarde, de waarde van de `stringArray` eigenschap van de inhoud van de aanvraag. Omdat de waarde van die eigenschap een matrix is, is deze niet nuttig in beleids voorwaarden. Bijvoorbeeld:

```json
{
  "field": "Microsoft.Test/resourceType/stringArray",
  "equals": "..."
}
```

Met deze voor waarde wordt de gehele `stringArray` matrix vergeleken met één teken reeks waarde. De meeste voor waarden, waaronder `equals` , alleen teken reeks waarden accepteren, daarom is het niet veel meer om een matrix te vergelijken met een teken reeks. Het hoofd scenario waarin wordt verwezen naar de eigenschap matrix is handig wanneer wordt gecontroleerd of het bestaat:

```json
{
  "field": "Microsoft.Test/resourceType/stringArray",
  "exists": "true"
}
```

Met de `field()` functie is de geretourneerde waarde de matrix van de aanvraag inhoud, die vervolgens kan worden gebruikt met een van de [ondersteunde sjabloon functies](../concepts/definition-structure.md#policy-functions) die matrix argumenten accepteren. Met de volgende voor waarde wordt bijvoorbeeld gecontroleerd of de lengte `stringArray` groter is dan 0:

```json
{
  "value": "[length(field('Microsoft.Test/resourceType/stringArray'))]",
  "greater": 0
}
```

#### <a name="referencing-the-array-members-collection"></a>Verwijzen naar de verzameling matrix leden

Aliassen die gebruikmaken `[*]` van de syntaxis, vertegenwoordigen een **verzameling eigenschaps waarden die zijn geselecteerd uit een matrix eigenschap**, die anders is dan de matrix eigenschap zelf selecteren. In het geval van wordt `Microsoft.Test/resourceType/stringArray[*]` een verzameling met alle leden van weer gegeven `stringArray` . Zoals eerder vermeld, `field` controleert een voor waarde of alle geselecteerde bron eigenschappen voldoen aan de voor waarde, daarom is de volgende voor waarde alleen waar als **alle** leden van `stringArray` gelijk zijn aan ' "waarde" '.

```json
{
  "field": "Microsoft.Test/resourceType/stringArray[*]",
  "equals": "value"
}
```

Als de matrix objecten bevat, `[*]` kan een alias worden gebruikt om de waarde van een specifieke eigenschap van elk matrixlid te selecteren. Voorbeeld:

```json
{
  "field": "Microsoft.Test/resourceType/objectArray[*].property",
  "equals": "value"
}
```

Deze voor waarde is waar als de waarden van alle `property` Eigenschappen in `objectArray` zijn gelijk aan `"value"` . Zie voor meer voor beelden [extra \[ \* \] alias voorbeelden](#additional--alias-examples).

Wanneer u de `field()` functie gebruikt om te verwijzen naar een matrix alias, is de geretourneerde waarde een matrix van alle geselecteerde waarden. Dit gedrag houdt in dat het algemene gebruik van de `field()` functie, de mogelijkheid om sjabloon functies toe te passen op eigenschaps waarden van resources, beperkt is. De enige sjabloon functies die in dit geval kunnen worden gebruikt, zijn de opties die matrix argumenten accepteren. Het is bijvoorbeeld mogelijk om de lengte van de matrix met te verkrijgen `[length(field('Microsoft.Test/resourceType/objectArray[*].property'))]` . Complexere scenario's, zoals het Toep assen van sjabloon functie op elk matrixlid en vergelijken hiervan met een gewenste waarde, zijn alleen mogelijk wanneer u de `count` expressie gebruikt. Zie [veld aantal-expressies](#field-count-expressions)voor meer informatie.

Als u wilt samenvatten, raadpleegt u de volgende voorbeeld bron inhoud en de geselecteerde waarden die door verschillende aliassen worden geretourneerd:

```json
{
  "tags": {
    "env": "prod"
  },
  "properties":
  {
    "stringArray": [ "a", "b", "c" ],
    "objectArray": [
      {
        "property": "value1",
        "nestedArray": [ 1, 2 ]
      },
      {
        "property": "value2",
        "nestedArray": [ 3, 4 ]
      }
    ]
  }
}
```

Wanneer u de veld voorwaarde gebruikt op de voorbeeld bron inhoud, zijn de resultaten als volgt:

| Alias | Geselecteerde waarden |
|:--- |:---|
| `Microsoft.Test/resourceType/missingArray` | `null` |
| `Microsoft.Test/resourceType/missingArray[*]` | Een lege verzameling waarden. |
| `Microsoft.Test/resourceType/missingArray[*].property` | Een lege verzameling waarden. |
| `Microsoft.Test/resourceType/stringArray` | `["a", "b", "c"]` |
| `Microsoft.Test/resourceType/stringArray[*]` | `"a"`, `"b"`, `"c"` |
| `Microsoft.Test/resourceType/objectArray[*]` |  `{ "property": "value1", "nestedArray": [ 1, 2 ] }`,<br/>`{ "property": "value2", "nestedArray": [ 3, 4 ] }`|
| `Microsoft.Test/resourceType/objectArray[*].property` | `"value1"`, `"value2"` |
| `Microsoft.Test/resourceType/objectArray[*].nestedArray` | `[ 1, 2 ]`, `[ 3, 4 ]` |
| `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` | `1`, `2`, `3`, `4` |

Wanneer u de `field()` functie gebruikt op de voorbeeld bron inhoud, zijn de resultaten als volgt:

| Expression | Geretourneerde waarde |
|:--- |:---|
| `[field('Microsoft.Test/resourceType/missingArray')]` | `""` |
| `[field('Microsoft.Test/resourceType/missingArray[*]')]` | `[]` |
| `[field('Microsoft.Test/resourceType/missingArray[*].property')]` | `[]` |
| `[field('Microsoft.Test/resourceType/stringArray')]` | `["a", "b", "c"]` |
| `[field('Microsoft.Test/resourceType/stringArray[*]')]` | `["a", "b", "c"]` |
| `[field('Microsoft.Test/resourceType/objectArray[*]')]` |  `[{ "property": "value1", "nestedArray": [ 1, 2 ] }, { "property": "value2", "nestedArray": [ 3, 4 ] }]`|
| `[field('Microsoft.Test/resourceType/objectArray[*].property')]` | `["value1", "value2"]` |
| `[field('Microsoft.Test/resourceType/objectArray[*].nestedArray')]` | `[[ 1, 2 ], [ 3, 4 ]]` |
| `[field('Microsoft.Test/resourceType/objectArray[*].nestedArray[*]')]` | `[1, 2, 3, 4]` |

### <a name="field-count-expressions"></a>Expressies voor veld tellingen

Expressies voor [veld tellingen](../concepts/definition-structure.md#field-count) tellen hoeveel matrix leden aan een voor waarde voldoen en vergelijken het aantal met een doel waarde. `Count` is intuïtief en veelzijdig voor het evalueren van matrices vergeleken met `field` voor waarden. De syntaxis is:

```json
{
  "count": {
    "field": <[*] alias>,
    "where": <optional policy condition expression>
  },
  "equals|greater|less|any other operator": <target value>
}
```

Als u geen `where` voor waarde gebruikt, `count` wordt alleen de lengte van een matrix geretourneerd. Met de voorbeeld resource-inhoud uit de vorige sectie wordt de volgende `count` expressie geëvalueerd op `true` sinds `stringArray` drie leden:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]"
  },
  "equals": 3
}
```

Dit gedrag werkt ook met geneste matrices. De volgende `count` expressie wordt bijvoorbeeld geëvalueerd naar `true` omdat er vier matrix leden in de `nestedArray` matrices zijn:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]"
  },
  "greaterOrEquals": 4
}
```

De kracht van `count` is in de `where` voor waarde. Wanneer deze is opgegeven, Azure Policy de matrix leden inventariseren en elke voor waarde geëvalueerd, waarbij wordt geteld hoeveel matrix leden er zijn geëvalueerd `true` . In elk herhaling van de evaluatie van de `where` voor waarde, Azure Policy een lid van één matrix selecteren ***i** _ en de resource-inhoud evalueren `where` aan de voor waarde _*, alsof ***i**_ het enige lid van de array_ * is. Als er slechts één matrixlid in elke iteratie beschikbaar is, kunt u complexe voor waarden Toep assen op elk afzonderlijk lid van de matrix.

Voorbeeld:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]",
    "where": {
      "field": "Microsoft.Test/resourceType/stringArray[*]",
      "equals": "a"
    }
  },
  "equals": 1
}
```

Voor het evalueren van de `count` expressie, Azure Policy de `where` voor waarde drie keer geëvalueerd, eenmaal voor elk lid van `stringArray` , tellen hoe vaak het is geëvalueerd `true` .
Wanneer de `where` voor waarde naar de `Microsoft.Test/resourceType/stringArray[*]` matrix leden verwijst, in plaats van alle leden te selecteren `stringArray` , wordt er slechts één lid van een matrix tegelijk geselecteerd:

| Iteratie | Geselecteerde `Microsoft.Test/resourceType/stringArray[*]` waarden | `where` Resultaat van evaluatie |
|:---|:---|:---|
| 1 | `"a"` | `true` |
| 2 | `"b"` | `false` |
| 3 | `"c"` | `false` |

Het `count` resultaat `1` .

Hier volgt een complexere expressie:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "allOf": [
        {
          "field": "Microsoft.Test/resourceType/objectArray[*].property",
          "equals": "value2"
        },
        {
          "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
          "greater": 2
        }
      ]
    }
  },
  "equals": 1
}
```

| Iteratie | Geselecteerde waarden | `where` Resultaat van evaluatie |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value1"` </br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | `false` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value2"` </br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4`| `true` |

Het `count` resultaat `1` .

Het feit dat de `where` expressie wordt geëvalueerd op basis van de **volledige** inhoud van de aanvraag (waarbij alleen wijzigingen worden aangebracht aan het matrixlid dat momenteel wordt geïnventariseerd) betekent dat de `where` voor waarde ook naar velden buiten de matrix kan verwijzen:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "field": "tags.env",
      "equals": "prod"
    }
  },
  "equals": 0
}
```

| Iteratie | Geselecteerde waarden | `where` Resultaat van evaluatie |
|:---|:---|:---|
| 1 | `tags.env` => `"prod"` | `true` |
| 2 | `tags.env` => `"prod"` | `true` |

Expressies met genest aantal kunnen worden gebruikt om voor waarden toe te passen op geneste matrix velden. Met de volgende voor waarde wordt bijvoorbeeld gecontroleerd of de `objectArray[*]` matrix precies twee leden `nestedArray[*]` bevat met een of meer leden:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "count": {
        "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]"
      },
      "greaterOrEquals": 1
    }
  },
  "equals": 2
}
```

| Iteratie | Geselecteerde waarden | Resultaat van evaluatie van genest aantal |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | `nestedArray[*]` heeft 2 leden => `true` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4` | `nestedArray[*]` heeft 2 leden => `true` |

Omdat beide leden van `objectArray[*]` een onderliggende matrix `nestedArray[*]` met twee leden hebben, retourneert de expressie van het buitenste aantal `2` .

Complexere voor beeld: Controleer of de `objectArray[*]` matrix precies twee leden bevat met `nestedArray[*]` alle leden die gelijk zijn aan `2` of `3` :

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "count": {
        "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
        "where": {
            "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
            "in": [ 2, 3 ]
        }
      },
      "greaterOrEquals": 1
    }
  },
  "equals": 2
}
```

| Iteratie | Geselecteerde waarden | Resultaat van evaluatie van genest aantal
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | `nestedArray[*]` daarin `2` => `true` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4` | `nestedArray[*]` daarin `3` => `true` |

Omdat beide leden van `objectArray[*]` een onderliggende matrix hebben met `nestedArray[*]` ofwel `2` of `3` , retourneert de expressie buiten Count een waarde `2` .

> [!NOTE]
> Expressies met geneste veld tellingen kunnen alleen verwijzen naar geneste matrices. Bijvoorbeeld, de Count-expressie `Microsoft.Test/resourceType/objectArray[*]` kan een genest aantal hebben dat is gericht op de geneste matrix `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` , maar kan geen expressie met een genest aantal doelen hebben `Microsoft.Test/resourceType/stringArray[*]` .

#### <a name="accessing-current-array-member-with-template-functions"></a>Huidig matrixlid openen met sjabloon functies

Bij het gebruik van sjabloon functies gebruikt `current()` u de functie om toegang te krijgen tot de waarde van het huidige matrixlid of de waarden van een van de eigenschappen ervan. Als u toegang wilt krijgen tot de waarde van het huidige matrixlid, geeft u de alias op die is gedefinieerd in `count.field` of een van de onderliggende aliassen als een argument voor de `current()` functie. Bijvoorbeeld:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
        "value": "[current('Microsoft.Test/resourceType/objectArray[*].property')]",
        "like": "value*"
    }
  },
  "equals": 2
}

```

| Iteratie | `current()` geretourneerde waarde | `where` Resultaat van evaluatie |
|:---|:---|:---|
| 1 | De waarde van `property` in het eerste lid van `objectArray[*]` : `value1` | `true` |
| 2 | De waarde van `property` in het eerste lid van `objectArray[*]` : `value2` | `true` |

#### <a name="the-field-function-inside-where-conditions"></a>De functie Field binnen where-voor waarden

De `field()` functie kan ook worden gebruikt om toegang te krijgen tot de waarde van het huidige matrixlid, zolang de expressie **Count** zich niet in een **voorbereidings voorwaarde** bevindt ( `field()` functie moet altijd verwijzen naar de resource geëvalueerd in de **if** -voor waarde). Het gedrag `field()` bij het verwijzen naar de geëvalueerde matrix is gebaseerd op de volgende concepten:

1. Matrix aliassen worden omgezet in een verzameling waarden die zijn geselecteerd in alle matrix leden.
1. `field()` functies die verwijzen naar matrix aliassen retour neren een matrix met de geselecteerde waarden.
1. Als u verwijst naar de alias van de getelde matrix binnen de `where` voor waarde, wordt een verzameling geretourneerd met een enkele waarde die is geselecteerd in het matrixlid dat in de huidige iteratie wordt geëvalueerd.

Dit gedrag betekent dat wanneer wordt verwezen naar het lid getelde matrix met een `field()` functie binnen de `where` voor waarde, **een matrix met één lid wordt geretourneerd**. Hoewel dit gedrag niet intuïtief is, is het consistent met het idee dat matrix aliassen altijd een verzameling geselecteerde eigenschappen retour neren. Hier volgt een voorbeeld:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]",
    "where": {
      "field": "Microsoft.Test/resourceType/stringArray[*]",
      "equals": "[field('Microsoft.Test/resourceType/stringArray[*]')]"
    }
  },
  "equals": 0
}
```

| Iteratie | Expressie waarden | `where` Resultaat van evaluatie |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/stringArray[*]` => `"a"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "a" ]` | `false` |
| 2 | `Microsoft.Test/resourceType/stringArray[*]` => `"b"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "b" ]` | `false` |
| 3 | `Microsoft.Test/resourceType/stringArray[*]` => `"c"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "c" ]` | `false` |

Daarom moet u, wanneer u de waarde van de alias van de getelde matrix met een `field()` functie hebt, de manier waarop u dit doet, met een `first()` sjabloon functie Inpakken:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]",
    "where": {
      "field": "Microsoft.Test/resourceType/stringArray[*]",
      "equals": "[first(field('Microsoft.Test/resourceType/stringArray[*]'))]"
    }
  }
}
```

| Iteratie | Expressie waarden | `where` Resultaat van evaluatie |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/stringArray[*]` => `"a"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"a"` | `true` |
| 2 | `Microsoft.Test/resourceType/stringArray[*]` => `"b"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"b"` | `true` |
| 3 | `Microsoft.Test/resourceType/stringArray[*]` => `"c"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"c"` | `true` |

Zie voor beelden van [veld tellingen](../concepts/definition-structure.md#field-count-examples)voor nuttige voor beelden.

## <a name="modifying-arrays"></a>Matrices wijzigen

De [toevoeg](../concepts/effects.md#append) -en [wijzigings](../concepts/effects.md#modify) eigenschappen voor een resource tijdens het maken of bijwerken. Wanneer u werkt met matrix eigenschappen, is het gedrag van deze effecten afhankelijk van of de bewerking probeert de alias te wijzigen **\[\*\]** of niet:

> [!NOTE]
> Het gebruiken van het `modify` effect met aliassen is momenteel beschikbaar als **Preview-versie**.

|Alias |Effect | Resultaat |
|-|-|-|
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | `append` | Azure Policy voegt de volledige matrix toe die is opgegeven in de effect Details als deze ontbreken. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | `modify` met `add` bewerking | Azure Policy voegt de volledige matrix toe die is opgegeven in de effect Details als deze ontbreken. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | `modify` met `addOrReplace` bewerking | Azure Policy voegt de volledige matrix die is opgegeven in de effect Details, toe als de bestaande matrix ontbreekt of wordt vervangen. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `append` | Azure Policy voegt het matrixlid toe dat is opgegeven in de effect Details. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `modify` met `add` bewerking | Azure Policy voegt het matrixlid toe dat is opgegeven in de effect Details. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `modify` met `addOrReplace` bewerking | Azure Policy verwijdert alle bestaande matrix leden en voegt het matrixlid toe dat is opgegeven in de effect Details. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `append` | Azure Policy voegt een waarde toe aan de `action` eigenschap van elk matrixlid. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `modify` met `add` bewerking | Azure Policy voegt een waarde toe aan de `action` eigenschap van elk matrixlid. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `modify` met `addOrReplace` bewerking | Azure Policy voegt de bestaande `action` eigenschap van elk matrixlid of vervangt deze. |

Zie voor meer informatie de [Append-voor beelden](../concepts/effects.md#append-examples).

## <a name="additional--alias-examples"></a>Aanvullende voor beelden van [*] aliassen

Het is raadzaam om de [expressies voor veld tellingen](#field-count-expressions) te gebruiken om te controleren of ' alle ' of ' alle ' leden van een matrix in de aanvraag inhoud voldoen aan een voor waarde. Voor sommige eenvoudige omstandigheden is het echter mogelijk om hetzelfde resultaat te krijgen met behulp van een veld accessor met een matrix alias, zoals beschreven in [de verwijzing naar de verzameling matrix leden](#referencing-the-array-members-collection). Dit patroon kan nuttig zijn in beleids regels die de limiet van expressies voor toegestane **tellingen** overschrijden. Hier volgen enkele voor beelden van algemene gebruiks voorbeelden:

De voorbeeld beleidsregel voor de scenario tabel hieronder:

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            <-- Condition (see table below) -->
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```

De **ipRules** -matrix is als volgt voor de scenario tabel hieronder:

```json
"ipRules": [
    {
        "value": "127.0.0.1",
        "action": "Allow"
    },
    {
        "value": "192.168.1.1",
        "action": "Allow"
    }
]
```

Voor elk voor beeld hieronder, vervangt u door `<field>` `"field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value"` .

De volgende resultaten zijn het resultaat van de combi natie van de voor waarde en de beleids regel voor het voor beeld en de matrix van bestaande waarden hierboven:

|Voorwaarde |Resultaat | Scenario |Uitleg |
|-|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |Niets |Geen overeenkomst |Eén matrix element evalueert als onwaar (127.0.0.1! = 127.0.0.1) en een als waar (127.0.0.1! = 192.168.1.1), zodat de **notEquals** -voor waarde _False_ is en het effect niet wordt geactiveerd. |
|`{<field>,"notEquals":"10.0.4.1"}` |Beleids effect |Geen overeenkomst |Beide matrix elementen evalueren als True (10.0.4.1! = 127.0.0.1 en 10.0.4.1! = 192.168.1.1), dus de **notEquals** -voor waarde is _True_ en het effect wordt geactiveerd. |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |Beleids effect |Een of meer overeenkomsten |Eén matrix element evalueert als onwaar (127.0.0.1! = 127.0.0.1) en een als waar (127.0.0.1! = 192.168.1.1), zodat de **notEquals** -voor waarde _False_ is. De logische operator evalueert als True (**niet** _waar_), zodat het effect wordt geactiveerd. |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |Niets |Een of meer overeenkomsten |Beide matrix elementen evalueren als True (10.0.4.1! = 127.0.0.1 en 10.0.4.1! = 192.168.1.1), dus de **notEquals** -voor waarde is _True_. De logische operator evalueert als onwaar (**niet** _waar_), dus het effect wordt niet geactiveerd. |
|`"not":{<field>,"Equals":"127.0.0.1"}` |Beleids effect |Niet alle overeenkomsten |Eén matrix element evalueert als True (127.0.0.1 = = 127.0.0.1) en een als onwaar (127.0.0.1 = = 192.168.1.1), dus is de waarde **equals** _False_. De logische operator evalueert als True (**niet** _waar_), zodat het effect wordt geactiveerd. |
|`"not":{<field>,"Equals":"10.0.4.1"}` |Beleids effect |Niet alle overeenkomsten |Beide matrix elementen evalueren als onwaar (10.0.4.1 = = 127.0.0.1 en 10.0.4.1 =/192.168.1.1), zodat de waarde **equals is ingesteld** op _False_. De logische operator evalueert als True (**niet** _waar_), zodat het effect wordt geactiveerd. |
|`{<field>,"Equals":"127.0.0.1"}` |Niets |Alle overeenkomsten |Eén matrix element evalueert als True (127.0.0.1 = = 127.0.0.1) en een als onwaar (127.0.0.1 = = 192.168.1.1), dus is de waarde **equals** _False_ en wordt het effect niet geactiveerd. |
|`{<field>,"Equals":"10.0.4.1"}` |Niets |Alle overeenkomsten |Beide matrix elementen evalueren als False (10.0.4.1 = = 127.0.0.1 en 10.0.4.1 = 192.168.1.1), dus de voor waarde **equals** is _False_ en het effect wordt niet geactiveerd. |

## <a name="next-steps"></a>Volgende stappen

- Bekijk voor beelden op [Azure Policy voor beelden](../samples/index.md).
- Lees over de [structuur van Azure Policy-definities](../concepts/definition-structure.md).
- Lees [Informatie over de effecten van het beleid](../concepts/effects.md).
- Meer informatie over het [programmatisch maken van beleids regels](programmatically-create.md).
- Ontdek hoe u [niet-compatibele resources kunt herstellen](remediate-resources.md).
- Bekijk wat een beheer groep is met [het organiseren van uw resources met Azure-beheer groepen](../../management-groups/overview.md).

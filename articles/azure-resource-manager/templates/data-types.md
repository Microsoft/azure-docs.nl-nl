---
title: Gegevens typen in sjablonen
description: Beschrijft de gegevens typen die beschikbaar zijn in Azure Resource Manager sjablonen.
ms.topic: conceptual
ms.author: tomfitz
author: tfitzmac
ms.date: 03/04/2021
ms.openlocfilehash: 7d3f15c8852e6e25c621baad9bc6f20c303ffdb9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102125082"
---
# <a name="data-types-in-arm-templates"></a>Gegevens typen in ARM-sjablonen

In dit artikel worden de gegevens typen beschreven die worden ondersteund in Azure Resource Manager sjablonen (ARM-sjablonen). Dit omvat zowel JSON-als Bicep-gegevens typen.

## <a name="supported-types"></a>Ondersteunde typen

Binnen een ARM-sjabloon kunt u deze gegevens typen gebruiken:

* matrix
* booleaans
* int
* object
* secureObject-aangegeven door modifier in Bicep
* secureString-aangegeven door modifier in Bicep
* tekenreeks

## <a name="arrays"></a>Matrices

Matrices beginnen met een haakje ( `[` ) en eindigend met een haakje ( `]` ).

In JSON kan een matrix worden gedeclareerd in één regel of op meerdere regels. Elk element wordt gescheiden door een komma.

In Bicep moet een matrix worden gedeclareerd in meerdere regels. Gebruik geen komma's tussen waarden.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleArray": {
    "type": "array",
    "defaultValue": [
      1,
      2,
      3
    ]
  }
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleArray array = [
  1
  2
  3
]
```

---

De elementen van een matrix kunnen van hetzelfde type of van verschillende typen zijn.

# <a name="json"></a>[JSON](#tab/json)

```json
"variables": {
  "mixedArray": [
    "[resourceGroup().name]",
    1,
    true,
    "example string"
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var mixedArray = [
  resourceGroup().name
  1
  true
  'example string'
]
```

---

## <a name="booleans"></a>Booleaans

Gebruik of om Booleaanse waarden op te geven `true` `false` . Plaats de waarde niet tussen aanhalings tekens.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleBool": {
    "type": "bool",
    "defaultValue": true
  }
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleBool bool = true
```

---

## <a name="integers"></a>Gehele getallen

Gebruik geen aanhalings tekens wanneer u waarden opgeeft voor een geheel getal.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleInt": {
    "type": "int",
    "defaultValue": 1
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleInt int = 1
```

---

Voor gehele getallen die als inline-para meters worden door gegeven, kan het bereik van waarden worden beperkt door de SDK of het opdracht regel programma dat u voor implementatie gebruikt. Als u bijvoorbeeld Power shell gebruikt voor het implementeren van een sjabloon, kunnen integerwaarden variëren van-2147483648 tot 2147483647. Als u deze beperking wilt vermijden, geeft u grote waarden voor geheel getal op in een [parameter bestand](parameter-files.md). De resource typen hebben hun eigen limieten voor eigenschappen van gehele getallen.

## <a name="objects"></a>Objecten

Objecten beginnen met een accolade ( `{` ) en eindigend met een haakje sluiten ( `}` ). Elke eigenschap in een object bestaat uit sleutel en waarde. De sleutel en waarde worden gescheiden door een dubbele punt ( `:` ).

In JSON bevindt de sleutel zich tussen dubbele aanhalings tekens. Elke eigenschap wordt gescheiden door een komma.

In Bicep bevindt de sleutel zich niet tussen aanhalings tekens. Gebruik geen komma's tussen de eigenschappen.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleObject": {
    "type": "object",
    "defaultValue": {
      "name": "test name",
      "id": "123-abc",
      "isCurrent": true,
      "tier": 1
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleObject object = {
  name: 'test name'
  id: '123-abc'
  isCurrent: true
  tier: 1
}
```

---

## <a name="strings"></a>Tekenreeksen

In JSON worden teken reeksen gemarkeerd met dubbele aanhalings tekens. In Bicep zijn teken reeksen gemarkeerd met enkele aanhalings tekens.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleString": {
    "type": "string",
    "defaultValue": "test value"
  }
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleString string = 'test value'
```
---

## <a name="secure-strings-and-objects"></a>Teken reeksen en objecten beveiligen

De beveiligde teken reeks maakt gebruik van dezelfde indeling als teken reeks en beveiligd object gebruikt dezelfde indeling als object. Wanneer u een para meter instelt op een beveiligde teken reeks of een beveiligd object, wordt de waarde van de para meter niet opgeslagen in de implementatie geschiedenis en niet geregistreerd. Als u deze beveiligde waarde echter instelt op een eigenschap waarvoor geen beveiligde waarde wordt verwacht, is de waarde niet beveiligd. Als u bijvoorbeeld een beveiligde teken reeks instelt op een tag, wordt die waarde opgeslagen als tekst zonder opmaak. Gebruik beveiligde teken reeksen voor wacht woorden en geheimen.

Met Bicep voegt u de `@secure()` aanpassings functie toe aan een teken reeks of object.

In het volgende voor beeld worden twee veilige para meters weer gegeven:

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "password": {
    "type": "secureString"
  },
  "configValues": {
    "type": "secureObject"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@secure()
param password string

@secure()
param configValues object
```

---

## <a name="next-steps"></a>Volgende stappen

Zie [inzicht krijgen in de structuur en syntaxis van arm-sjablonen](template-syntax.md)voor meer informatie over de syntaxis van de sjabloon.

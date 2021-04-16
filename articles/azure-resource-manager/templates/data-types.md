---
title: Gegevenstypen in sjablonen
description: Beschrijft de gegevenstypen die beschikbaar zijn in Azure Resource Manager sjablonen.
ms.topic: conceptual
ms.author: tomfitz
author: tfitzmac
ms.date: 03/04/2021
ms.openlocfilehash: 4d6c8306b3dbdfe895055dc008d81cc0d85d8d6c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538064"
---
# <a name="data-types-in-arm-templates"></a>Gegevenstypen in ARM-sjablonen

In dit artikel worden de gegevenstypen beschreven die worden ondersteund in Azure Resource Manager -sjablonen (ARM-sjablonen). Het bevat informatie over zowel JSON- als Bicep-gegevenstypen.

## <a name="supported-types"></a>Ondersteunde typen

In een ARM-sjabloon kunt u de volgende gegevenstypen gebruiken:

* matrix
* booleaans
* int
* object
* secureObject - aangeduid met modifier in Bicep
* secureString - aangeduid met modifier in Bicep
* tekenreeks

## <a name="arrays"></a>Matrices

Matrices beginnen met een linkerhake ( `[` ) en eindigen met een rechterhake ( `]` ).

In JSON kan een matrix worden gedeclareerd in één regel of meerdere regels. Elk element wordt gescheiden door een komma.

In Bicep moet een matrix in meerdere regels worden gedeclareerd. Gebruik geen komma's tussen waarden.

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

De elementen van een matrix kunnen hetzelfde type of verschillende typen zijn.

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

Gebruik of bij het opgeven van `true` booleaanse `false` waarden. Laat de waarde niet tussen aanhalingstekens staan.

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

Gebruik geen aanhalingstekens wanneer u waarden met gehele getallen opgeeft.

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

Voor gehele getallen die worden doorgegeven als inlineparameters, kan het bereik van waarden worden beperkt door de SDK of het opdrachtregelprogramma dat u voor de implementatie gebruikt. Wanneer u bijvoorbeeld PowerShell gebruikt om een sjabloon te implementeren, kunnen typen gehele getallen variëren van -2147483648 tot 2147483647. Om deze beperking te voorkomen, geeft u grote waarden voor gehele getallen op in een [parameterbestand](parameter-files.md). Resourcetypen passen hun eigen limieten toe voor eigenschappen van gehele getallen.

## <a name="objects"></a>Objecten

Objecten beginnen met een linkerbrace ( `{` ) en eindigen met een accolade ( `}` ). Elke eigenschap in een object bestaat uit sleutel en waarde. De sleutel en waarde worden gescheiden door een dubbele punt ( `:` ).

# <a name="json"></a>[JSON](#tab/json)

In JSON wordt de sleutel tussen dubbele aanhalingstekens ingesloten. Elke eigenschap wordt gescheiden door een komma.

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

In Bicep staat de sleutel niet tussen aanhalingstekens. Gebruik geen komma's tussen eigenschappen.

```bicep
param exampleObject object = {
  name: 'test name'
  id: '123-abc'
  isCurrent: true
  tier: 1
}
```

Accessoires voor eigenschappen worden gebruikt om toegang te krijgen tot eigenschappen van een object. Ze worden samengesteld met behulp van de `.` operator . Bijvoorbeeld:

```bicep
var x = {
  y: {
    z: 'Hello`
    a: true
  }
  q: 42
}
```

Gezien de vorige declaratie, wordt de expressie x.y.z geëvalueerd als de letterlijke tekenreeks 'Hello'. Op dezelfde manier wordt de expressie x.q geëvalueerd als het letterlijke geheel getal 42.

Eigenschapsaccesssoires kunnen worden gebruikt met elk object. Dit omvat parameters en variabelen van objecttypen en letterlijke waarden van objecten. Het gebruik van een eigenschapsaccess in een expressie van een niet-objecttype is een fout.

---

## <a name="strings"></a>Tekenreeksen

In JSON worden tekenreeksen gemarkeerd met dubbele aanhalingstekens. In Bicep worden tekenreeksen gemarkeerd met enkele aanhalingstekens.

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

## <a name="secure-strings-and-objects"></a>Tekenreeksen en objecten beveiligen

Beveiligde tekenreeks gebruikt dezelfde indeling als tekenreeks en beveiligd object gebruikt dezelfde indeling als object. Wanneer u een parameter in stelt op een beveiligde tekenreeks of beveiligd object, wordt de waarde van de parameter niet opgeslagen in de implementatiegeschiedenis en wordt deze niet geregistreerd. Als u die beveiligde waarde echter in stelt op een eigenschap die geen veilige waarde verwacht, wordt de waarde niet beveiligd. Als u bijvoorbeeld een beveiligde tekenreeks in stelt op een tag, wordt die waarde opgeslagen als tekst zonder tekst zonder tekst. Gebruik beveiligde tekenreeksen voor wachtwoorden en geheimen.

Met Bicep voegt u de `@secure()` modifier toe aan een tekenreeks of object.

In het volgende voorbeeld ziet u twee beveiligde parameters:

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

Zie Inzicht in de structuur en syntaxis van ARM-sjablonen voor meer informatie [over de sjabloonsyntaxis.](template-syntax.md)

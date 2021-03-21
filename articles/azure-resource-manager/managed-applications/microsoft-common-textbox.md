---
title: INTERFACE-element TextBox
description: Hierin wordt het gebruikers interface-element micro soft. common. TextBox beschreven voor Azure Portal. Gebruiken voor het toevoegen van niet-opgemaakte tekst.
author: tfitzmac
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: tomfitz
ms.openlocfilehash: 8e4cfcc7fe46c19bf1e58ee5eadf1e0bb8c7bd8e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102124326"
---
# <a name="microsoftcommontextbox-ui-element"></a>Gebruikers interface-element van micro soft. common. TextBox

Een gebruikers interface-element (UI) dat kan worden gebruikt om niet-opgemaakte tekst toe te voegen. Het `Microsoft.Common.TextBox` element is een invoer veld met één regel, maar ondersteunt de invoer van meerdere regels met de `multiLine` eigenschap.

## <a name="ui-sample"></a>UI-voor beeld

Het `TextBox` element maakt gebruik van een tekstvak met één regel of meerdere regels.

:::image type="content" source="media/managed-application-elements/microsoft-common-textbox.png" alt-text="Micro soft. common. TextBox-element tekstvak met één regel.":::

:::image type="content" source="media/managed-application-elements/microsoft-common-textbox-multi-line.png" alt-text="Tekstvak van micro soft. common. TextBox-element met meerdere regels.":::

## <a name="schema"></a>Schema

```json
{
  "name": "nameInstance",
  "type": "Microsoft.Common.TextBox",
  "label": "Name",
  "defaultValue": "contoso123",
  "toolTip": "Use only allowed characters",
  "placeholder": "",
  "multiLine": false,
  "constraints": {
    "required": true,
    "validations": [
      {
        "regex": "^[a-z0-9A-Z]{1,30}$",
        "message": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
      },
      {
        "isValid": "[startsWith(steps('resourceConfig').nameInstance, 'contoso')]",
        "message": "Must start with 'contoso'."
      }
    ]
  },
  "visible": true
}
```

## <a name="sample-output"></a>Voorbeelduitvoer

```json
"contoso123"
```

## <a name="remarks"></a>Opmerkingen

- Gebruik de `toolTip` eigenschap om tekst over het element weer te geven wanneer de muis aanwijzer boven het informatie symbool wordt gehouden.
- De `placeholder` eigenschap is Help-tekst die verdwijnt wanneer de gebruiker begint met het bewerken van. Als de `placeholder` en `defaultValue` beide zijn gedefinieerd, `defaultValue` krijgt de prioriteit en wordt weer gegeven.
- De `multiLine` eigenschap is Booleaans `true` of `false` . Als u een tekstvak met meerdere regels wilt gebruiken, stelt u de eigenschap in op `true` . Als een tekstvak met meerdere regels niet nodig is, stelt u de eigenschap in op `false` of sluit u de eigenschap uit. Voor nieuwe regels wordt in JSON-uitvoer weer gegeven `\n` voor de regel invoer. Het tekstvak met meerdere regels accepteert `\r` een Enter-teken (CR) en `\n` voor een regel invoer (LF). Een standaard waarde kan bijvoorbeeld `\r\n` CRLF bevatten.
- Als `constraints.required` is ingesteld op `true` , moet het tekstvak een waarde hebben om te kunnen valideren. De standaardwaarde is `false`.
- De `validations` eigenschap is een matrix waarin u voor waarden toevoegt voor het controleren van de waarde die is opgegeven in het tekstvak.
- De `regex` eigenschap is een reguliere java script-expressie patroon. Indien opgegeven, moet de waarde van het tekstvak overeenkomen met het patroon om te valideren. De standaardwaarde is `null`. Zie [referentie voor reguliere expressies](/dotnet/standard/base-types/regular-expression-language-quick-reference)voor meer informatie over regex-syntaxis.
- De `isValid` eigenschap bevat een expressie die wordt geëvalueerd naar `true` of `false` . Binnen de expressie definieert u de voor waarde die bepaalt of het tekstvak geldig is.
- De `message` eigenschap is een teken reeks die moet worden weer gegeven wanneer de validatie van de waarde van het tekstvak mislukt.
- Het is mogelijk om een waarde op te geven voor `regex` Wanneer `required` is ingesteld op `false` . In dit scenario is er geen waarde vereist om het tekstvak te valideren. Als er een is opgegeven, moet deze overeenkomen met het reguliere-expressie patroon.

## <a name="examples"></a>Voorbeelden

In de voor beelden ziet u hoe u een tekstvak met één regel en een tekstvak met meerdere regels gebruikt.

### <a name="single-line"></a>Eén regel

In het volgende voor beeld wordt een tekstvak met het [micro soft. Solutions. ArmApiControl](microsoft-solutions-armapicontrol.md) -besturings element gebruikt om de beschik baarheid van een resource naam te controleren.

```json
"basics": [
  {
    "name": "nameApi",
    "type": "Microsoft.Solutions.ArmApiControl",
    "request": {
      "method": "POST",
      "path": "[concat(subscription().id, '/providers/Microsoft.Storage/checkNameAvailability?api-version=2019-06-01')]",
      "body": "[parse(concat('{\"name\": \"', basics('txtStorageName'), '\", \"type\": \"Microsoft.Storage/storageAccounts\"}'))]"
    }
  },
  {
    "name": "txtStorageName",
    "type": "Microsoft.Common.TextBox",
    "label": "Storage account name",
    "constraints": {
      "validations": [
        {
          "isValid": "[not(equals(basics('nameApi').nameAvailable, false))]",
          "message": "[concat('Name unavailable: ', basics('txtStorageName'))]"
        }
      ]
    }
  }
]
```

### <a name="multi-line"></a>Meerdere lijnen

In dit voor beeld wordt met behulp `multiLine` van de eigenschap een tekstvak met tijdelijke tekst gemaakt.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "demoTextBox",
        "type": "Microsoft.Common.TextBox",
        "label": "Multi-line text box",
        "defaultValue": "",
        "toolTip": "Use 1-60 alphanumeric characters, hyphens, spaces, periods, and colons.",
        "placeholder": "This is a multi-line text box:\nLine 1.\nLine 2.\nLine 3.",
        "multiLine": true,
        "constraints": {
          "required": true,
          "validations": [
            {
              "regex": "^[a-z0-9A-Z -.:\n]{1,60}$",
              "message": "Only 1-60 alphanumeric characters, hyphens, spaces, periods, and colons are allowed."
            }
          ]
        },
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "textBox": "[basics('demoTextBox')]"
    }
  }
}
```

## <a name="next-steps"></a>Volgende stappen

- Zie voor een inleiding tot het maken van UI-definities [CreateUiDefinition.jsop voor het maken van de gebruikers ervaring van de door Azure beheerde toepassing](create-uidefinition-overview.md).
- Zie [CreateUiDefinition-elementen](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in UI-elementen.

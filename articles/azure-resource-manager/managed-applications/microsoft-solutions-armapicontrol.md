---
title: ArmApiControl UI-element
description: Hierin wordt het UI-element micro soft. Solutions. ArmApiControl voor Azure Portal beschreven. Wordt gebruikt voor het aanroepen van API-bewerkingen.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: tomfitz
ms.openlocfilehash: fdc299ef1945e3ee0810f1c314fc07edfb4f4873
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313297"
---
# <a name="microsoftsolutionsarmapicontrol-ui-element"></a>Micro soft. Solutions. ArmApiControl UI-element

Met ArmApiControl kunt u resultaten van een Azure Resource Manager API-bewerking ophalen. Gebruik de resultaten om dynamische inhoud in andere besturings elementen in te vullen.

## <a name="ui-sample"></a>UI-voor beeld

Er is geen gebruikers interface voor dit besturings element.

## <a name="schema"></a>Schema

In het volgende voor beeld wordt het schema voor dit besturings element weer gegeven:

```json
{
    "name": "testApi",
    "type": "Microsoft.Solutions.ArmApiControl",
    "request": {
        "method": "{HTTP-method}",
        "path": "{path-for-the-URL}",
        "body": {
            "key1": "val1",
            "key2": "val2"
        }
    }
}
```

## <a name="sample-output"></a>Voorbeelduitvoer

De uitvoer van het besturings element wordt niet weer gegeven voor de gebruiker. In plaats daarvan wordt het resultaat van de bewerking gebruikt in andere besturings elementen.

## <a name="remarks"></a>Opmerkingen

- Met de `request.method` eigenschap wordt de HTTP-methode opgegeven. Alleen `GET` of `POST` zijn toegestaan.
- `request.path`Met de eigenschap geeft u een URL op die een relatief pad naar een arm-eind punt moet zijn. Dit kan een statisch pad zijn of dynamisch worden samengesteld door de uitvoer waarden van de andere besturings elementen te bepalen.

  Bijvoorbeeld een ARM-oproep naar de `Microsoft.Network/expressRouteCircuits` resource provider:

  ```json
  "path": "<subid>/resourceGroup/<resourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<routecircuitName>/?api-version=2020-05-01"
  ```

- De `request.body` eigenschap is optioneel. Gebruik deze functie om een JSON-hoofd tekst op te geven die met de aanvraag wordt verzonden. De hoofd tekst kan statische inhoud zijn of dynamisch worden geconstrueerd door te verwijzen naar uitvoer waarden van andere besturings elementen.

## <a name="example"></a>Voorbeeld

In het volgende voor beeld `providersApi` roept het element een API aan om een matrix van provider objecten op te halen.

De `allowedValues` eigenschap van het `providersDropDown` element is zo geconfigureerd dat de namen van de providers worden opgehaald. Deze worden weer gegeven in de vervolg keuzelijst.

```json
{
    "name": "providersApi",
    "type": "Microsoft.Solutions.ArmApiControl",
    "request": {
        "method": "GET",
        "path": "[concat(subscription().id, '/providers/Microsoft.Network/expressRouteServiceProviders?api-version=2019-02-01')]"
    }
},
{
    "name": "providerDropDown",
    "type": "Microsoft.Common.DropDown",
    "label": "Provider",
    "toolTip": "The provider that offers the express route connection.",
    "constraints": {
        "allowedValues": "[map(steps('settings').providersApi.value, (item) => parse(concat('{\"label\":\"', item.name, '\",\"value\":\"', item.name, '\"}')))]",
        "required": true
    },
    "visible": true
}
```

Zie [micro soft. common. TextBox](microsoft-common-textbox.md)voor een voor beeld van het gebruik van de ArmApiControl om de beschik baarheid van een resource naam te controleren.

## <a name="next-steps"></a>Volgende stappen

- Zie aan de slag [met CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van UI-definities.
- Zie [CreateUiDefinition-elementen](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in UI-elementen.

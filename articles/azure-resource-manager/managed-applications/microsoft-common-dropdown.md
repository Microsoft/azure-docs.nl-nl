---
title: Vervolg keuzelijst GEBRUIKERSINTERFACE element
description: Hierin wordt het micro soft. common. vervolg keuzelijst gebruikers interface-element voor Azure Portal beschreven. Gebruiken om te selecteren uit beschik bare opties bij het implementeren van een beheerde toepassing.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: tomfitz
ms.openlocfilehash: b9b27a432776635290c7e8e796e84d8c1e0e8675
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92168338"
---
# <a name="microsoftcommondropdown-ui-element"></a>Micro soft. common. vervolg keuzelijst UI-element

Een selectie besturings element met een vervolg keuzelijst. U kunt slechts één item of meerdere items selecteren. U kunt eventueel ook een beschrijving van de items toevoegen.

## <a name="ui-sample"></a>UI-voor beeld

Het element DropDown heeft andere opties die de weer gave in de portal bepalen.

Als er slechts één item is toegestaan voor selectie, wordt het besturings element weer gegeven als:

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-1.png" alt-text="Micro soft. common. DropDown, enkelvoudige selectie":::

Wanneer beschrijvingen zijn opgenomen, wordt het besturings element weer gegeven als:

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-2.png" alt-text="Micro soft. common. vervolg keuzelijst met één selectie met beschrijvingen":::

Als meervoudige selectie is ingeschakeld, voegt het besturings element de optie **alle selecteren** en selectie vakjes toe voor het selecteren van meer dan één item:

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-3.png" alt-text="Micro soft. common. DropDown multi-select":::

Beschrijvingen kunnen worden opgenomen in meervoudige selectie ingeschakeld.

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-4.png" alt-text="Scherm afbeelding die laat zien hoe beschrijvingen kunnen worden opgenomen in meervoudige selectie ingeschakeld":::

Wanneer filteren is ingeschakeld, bevat het besturings element een tekstvak voor het toevoegen van de filter waarde.

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-5.png" alt-text="Micro soft. common. DropDown multi-select met beschrijvingen":::

## <a name="schema"></a>Schema

```json
{
    "name": "element1",
    "type": "Microsoft.Common.DropDown",
    "label": "Example drop down",
    "placeholder": "",
    "defaultValue": "Value two",
    "toolTip": "",
    "multiselect": true,  
    "selectAll": true,  
    "filter": true,  
    "filterPlaceholder": "Filter items ...",  
    "multiLine": true,  
    "defaultDescription": "A value for selection",  
    "constraints": {
        "allowedValues": [
            {
                "label": "Value one",
                "description": "The value to select for option 1.",
                "value": "one"
            },
            {
                "label": "Value two",
                "description": "The value to select for option 2.",
                "value": "two"
            }
        ],
        "required": true
    },
    "visible": true
}
```

## <a name="sample-output"></a>Voorbeelduitvoer

```json
"two"
```

## <a name="remarks"></a>Opmerkingen

- Gebruiken `multiselect` om op te geven of gebruikers meer dan één item kunnen selecteren.
- Standaard `selectAll` is `true` Wanneer meervoudige selectie is ingeschakeld.
- Met deze `filter` eigenschap kunnen gebruikers zoeken in een lange lijst met opties.
- Het label voor `constraints.allowedValues` is de weergave tekst voor een item en de waarde is de uitvoer waarde van het element wanneer dit is geselecteerd.
- Indien opgegeven, moet de standaard waarde een label zijn in `constraints.allowedValues` . Als u niets opgeeft, wordt het eerste item in `constraints.allowedValues` geselecteerd. De standaard waarde is **Null**.
- `constraints.allowedValues` moet ten minste één item bevatten.
- Als u een waarde wilt emuleren die niet vereist is, voegt u een item met een label en waarde van `""` (lege teken reeks) toe aan `constraints.allowedValues` .
- De `defaultDescription` eigenschap wordt gebruikt voor items die geen beschrijving hebben.
- De `placeholder` eigenschap is Help-tekst die verdwijnt wanneer de gebruiker begint met het bewerken van. Als de `placeholder` en `defaultValue` beide zijn gedefinieerd, `defaultValue` krijgt de prioriteit en wordt weer gegeven.

## <a name="next-steps"></a>Volgende stappen

* Zie aan de slag [met CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van UI-definities.
* Zie [CreateUiDefinition-elementen](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in UI-elementen.

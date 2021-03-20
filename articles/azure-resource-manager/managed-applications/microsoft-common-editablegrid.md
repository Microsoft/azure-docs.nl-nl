---
title: EditableGrid UI-element
description: Hierin wordt het micro soft. common. EditableGrid UI-element voor Azure Portal beschreven. Hiermee kunnen gebruikers tabellaire invoer verzamelen.
author: tfitzmac
ms.topic: conceptual
ms.date: 08/24/2020
ms.author: tomfitz
ms.openlocfilehash: 04f86883a75110985d1cbe050fe3fd3e0582986a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88893769"
---
# <a name="microsoftcommoneditablegrid-ui-element"></a>Micro soft. common. EditableGrid UI-element

Een besturings element voor het verzamelen van gegevens in tabel vorm. Alle velden in het raster zijn bewerkbaar en het aantal rijen kan variëren.

## <a name="ui-sample"></a>UI-voor beeld

:::image type="content" source="./media/managed-application-elements/microsoft-common-editablegrid.png" alt-text="Micro soft. common. EditableGrid":::

## <a name="schema"></a>Schema

```json
{
  "name": "people",
  "type": "Microsoft.Common.EditableGrid",
  "ariaLabel": "Enter information per person",
  "label": "People",
  "constraints": {
    "width": "Full",
    "rows": {
      "count": {
        "min": 1,
        "max": 10
      }
    },
    "columns": [
      {
        "id": "colName",
        "header": "Name",
        "width": "1fr",
        "element": {
          "type": "Microsoft.Common.TextBox",
          "placeholder": "Full name",
          "constraints": {
            "required": true,
            "validations": [
              {
                "isValid": "[startsWith(last(take(steps('grid').people, $rowIndex)).colName, 'contoso')]",
                "message": "Must start with 'contoso'."
              },
              {
                "regex": "^[a-z0-9A-Z]{1,30}$",
                "message": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
              }
            ]
          }
        }
      },
      {
        "id": "colGender",
        "header": "Gender",
        "width": "1fr",
        "element": {
          "name": "dropDown1",
          "type": "Microsoft.Common.DropDown",
          "placeholder": "Select a gender...",
          "constraints": {
            "allowedValues": [
              {
                "label": "Female",
                "value": "female"
              },
              {
                "label": "Male",
                "value": "male"
              },
              {
                "label": "Other",
                "value": "other"
              }
            ],
            "required": true
          }
        }
      },
      {
        "id": "colContactPreference",
        "header": "Contact preference",
        "width": "1fr",
        "element": {
          "type": "Microsoft.Common.OptionsGroup",
          "constraints": {
            "allowedValues": [
              {
                "label": "Email",
                "value": "email"
              },
              {
                "label": "Text",
                "value": "text"
              }
            ],
            "required": true
          }
        }
      }
    ]
  }
}
```

## <a name="sample-output"></a>Voorbeelduitvoer

```json
{
  "colName": "contoso",
  "colGender": "female",
  "colContactPreference": "email"
}
```

## <a name="remarks"></a>Opmerkingen

- De enige geldige besturings elementen in de matrix columns zijn het [tekstvak](microsoft-common-textbox.md), het [OptionsGroup](microsoft-common-optionsgroup.md)en de [vervolg keuzelijst](microsoft-common-dropdown.md).
- De `$rowIndex` variabele is alleen geldig in expressies die zijn opgenomen in de onderliggende kolommen van het raster. Het is een geheel getal dat de relatieve rij-index van het element vertegenwoordigt en het aantal begint bij één en wordt met één verhoogd. Zoals wordt weer gegeven in de sectie van het schema `"columns":` , `$rowIndex` wordt de gebruikt voor validatie.
- Wanneer validaties worden uitgevoerd met behulp `$rowIndex` van de variabele, is het mogelijk om de waarde van de huidige rij op te halen door de `last()` opdrachten en te combi neren `take()` .

  Bijvoorbeeld:

  `last(take(<reference_to_grid>, $rowIndex))`

- De `label` eigenschap wordt niet weer gegeven als onderdeel van het besturings element, maar wordt weer gegeven op het laatste tabblad Overzicht.
- De `ariaLabel` eigenschap is het toegankelijkheids label voor het raster. Geef nuttige tekst op voor gebruikers die scherm lezers gebruiken.
- De `constraints.width` eigenschap wordt gebruikt om de algehele breedte van het raster in te stellen. De opties zijn _volledig_, _normaal_, _klein_. De standaard waarde is _vol_.
- De `width` eigenschap van onderliggende kolommen bepaalt de kolom breedte. De breedten worden opgegeven met behulp van fractionele eenheden zoals _3FR_, waarbij de totale ruimte wordt toegewezen aan kolommen die proportioneel zijn voor hun eenheden. Als er geen kolom breedte is opgegeven, is de standaard waarde _1fr_.

## <a name="next-steps"></a>Volgende stappen

- Zie aan de slag [met CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van UI-definities.
- Zie [CreateUiDefinition-elementen](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in UI-elementen.

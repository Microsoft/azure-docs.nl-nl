---
title: MultiStorageAccountCombo UI-element
description: Hierin wordt het element micro soft. storage. MultiStorageAccountCombo UI voor Azure Portal beschreven.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: a8172b63039d2d247f30fca4099254cb8fca068e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87073415"
---
# <a name="microsoftstoragemultistorageaccountcombo-ui-element"></a>Micro soft. storage. MultiStorageAccountCombo UI-element

Een groep besturings elementen voor het maken van verschillende opslag accounts met namen die beginnen met een gemeen schappelijk voor voegsel.

## <a name="ui-sample"></a>UI-voor beeld

![Microsoft.Storage.MultiStorageAccountCombo](./media/managed-application-elements/microsoft-storage-multistorageaccountcombo.png)

## <a name="schema"></a>Schema

```json
{
  "name": "element1",
  "type": "Microsoft.Storage.MultiStorageAccountCombo",
  "label": {
    "prefix": "Storage account prefix",
    "type": "Storage account type"
  },
  "toolTip": {
    "prefix": "",
    "type": ""
  },
  "defaultValue": {
    "prefix": "sa",
    "type": "Premium_LRS"
  },
  "constraints": {
    "allowedTypes": [],
    "excludedTypes": []
  },
  "count": 2,
  "visible": true
}
```

## <a name="sample-output"></a>Voorbeelduitvoer

```json
{
  "prefix": "sa",
  "count": 2,
  "resourceGroup": "rg01",
  "type": "Premium_LRS"
}
```

## <a name="remarks"></a>Opmerkingen

- De waarde voor `defaultValue.prefix` wordt samengevoegd met een of meer gehele getallen om de volg orde van de namen van opslag accounts te genereren. Als bijvoorbeeld `defaultValue.prefix` **sa** is en `count` **2** is, worden de namen van opslag accounts **SA1** en **sa2** gegenereerd. De namen van gegenereerde opslag accounts worden automatisch voor uniekheid gevalideerd.
- De namen van de opslag accounts worden gegenereerd lexicographically op basis van `count` . Als bijvoorbeeld `count` 10 is, wordt de naam van het opslag account end met gehele getallen van twee cijfers (01, 02, 03).
- De standaard waarde voor `defaultValue.prefix` is **Null** en voor `defaultValue.type` is **Premium_LRS**.
- Elk type dat niet is opgegeven in `constraints.allowedTypes` , is verborgen en elk type dat niet is opgegeven in `constraints.excludedTypes` wordt weer gegeven. `constraints.allowedTypes` en `constraints.excludedTypes` zijn beide optioneel, maar kunnen niet tegelijkertijd worden gebruikt.
- Naast het genereren van opslag accountnamen, `count` wordt gebruikt om de juiste vermenigvuldiger voor het element in te stellen. Het ondersteunt een statische waarde, zoals **2**, of een dynamische waarde van een ander element, zoals `[steps('step1').storageAccountCount]` . De standaard waarde is **1**.

## <a name="next-steps"></a>Volgende stappen

* Zie aan de slag [met CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van UI-definities.
* Zie [CreateUiDefinition-elementen](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in UI-elementen.

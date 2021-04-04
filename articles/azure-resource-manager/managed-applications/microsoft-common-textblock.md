---
title: TextBlock UI-element
description: Hierin wordt het micro soft. common. TextBlock UI-element voor Azure Portal beschreven. Gebruiken om tekst toe te voegen aan de-interface.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: ba2c4d410891910c29ee1fda1065f8230ab171bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87063860"
---
# <a name="microsoftcommontextblock-ui-element"></a>Micro soft. common. TextBlock UI-element

Een besturings element dat kan worden gebruikt om tekst toe te voegen aan de portal-interface.

## <a name="ui-sample"></a>UI-voor beeld

![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft-common-textblock.png)

## <a name="schema"></a>Schema

```json
{
  "name": "text1",
  "type": "Microsoft.Common.TextBlock",
  "visible": true,
  "options": {
    "text": "Please provide the configuration values for your application.",
    "link": {
      "label": "Learn more",
      "uri": "https://www.microsoft.com"
    }
  }
}
```

## <a name="sample-output"></a>Voorbeelduitvoer

```json
"Please provide the configuration values for your application. Learn more"
```

## <a name="next-steps"></a>Volgende stappen

* Zie aan de slag [met CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van UI-definities.
* Zie [CreateUiDefinition-elementen](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in UI-elementen.

---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/27/2019
ms.author: glenga
ms.openlocfilehash: d697334fe56fb9133a06cee79067c60bc3a37281
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010453"
---
De eenvoudigste manier om bindingextensies te installeren, is door [extensiebundels](../articles/azure-functions/functions-bindings-register.md#extension-bundles)in te schakelen. Wanneer u bundels inschakelt, wordt er automatisch een vooraf gedefinieerde set extensiepakketten geïnstalleerd.

Als u extensiebundels wilt inschakelen, opent u het bestand host.json en werkt u de inhoud daarvan zodanig bij dat deze overeenkomt met de volgende code:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```
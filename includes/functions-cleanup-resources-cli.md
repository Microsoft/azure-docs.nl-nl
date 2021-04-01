---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: 0f560e292ecba8a1186a400e4a40b0552c394298
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93424721"
---
## <a name="clean-up-resources"></a>Resources opschonen

Als u verdergaat met de [volgende stap](#next-steps) en een uitvoerbinding voor een Azure Storage-wachtrij toevoegt, kunt u alle resources het beste op dezelfde plaats laten staan. U gaat ze namelijk gebruiken in deze stap.

Gebruik anders de volgende opdracht om de resourcegroep en alle bijbehorende resources te verwijderen om te voorkomen dat er verdere kosten in rekening worden gebracht.

 # <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name AzureFunctionsQuickstart-rg
```

---
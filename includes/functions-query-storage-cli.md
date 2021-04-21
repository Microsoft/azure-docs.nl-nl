---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 642989df8dca9d4ae121d80e30a9fb6d8dd06286
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107799983"
---
### <a name="query-the-storage-queue"></a>Een query uitvoeren op de opslagwachtrij

U kunt de opdracht [`az storage queue list`](/cli/azure/storage/queue#az_storage_queue_list) gebruiken om de opslagwachtrijen in uw account te bekijken, zoals in het volgende voorbeeld:

```azurecli-interactive
az storage queue list --output tsv
```

De uitvoer van deze opdracht bevat een wachtrij met de naam `outqueue`. Dit is de wachtrij die is gemaakt toen de functie is uitgevoerd.

Gebruik hierna de opdracht [`az storage message peek`](/cli/azure/storage/message#az_storage_message_peek) om de berichten in deze wachtrij weer te geven, zoals in dit voorbeeld:

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

De geretourneerde tekenreeks moet hetzelfde zijn als het bericht dat u hebt verzonden om de functie te testen.

> [!NOTE]  
> In het vorige voorbeeld wordt de geretourneerde tekenreeks gedecodeerd vanuit Base 64. Dit gebeurt omdat bindingen naar/van de opslagwachtrij als [Base 64-tekenreeksen](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#encoding) naar Azure Storage worden geschreven/gelezen.
---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 4fe159660421113e0f0ac0586ae7e4a22d5bcda7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "77474137"
---
### <a name="query-the-storage-queue"></a>Een query uitvoeren op de opslagwachtrij

U kunt de opdracht [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) gebruiken om de opslagwachtrijen in uw account te bekijken, zoals in het volgende voorbeeld:

```azurecli-interactive
az storage queue list --output tsv
```

De uitvoer van deze opdracht bevat een wachtrij met de naam `outqueue`. Dit is de wachtrij die is gemaakt toen de functie is uitgevoerd.

Gebruik hierna de opdracht [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) om de berichten in deze wachtrij weer te geven, zoals in dit voorbeeld:

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

De geretourneerde tekenreeks moet hetzelfde zijn als het bericht dat u hebt verzonden om de functie te testen.

> [!NOTE]  
> In het vorige voorbeeld wordt de geretourneerde tekenreeks gedecodeerd vanuit Base 64. Dit gebeurt omdat bindingen naar/van de opslagwachtrij als [Base 64-tekenreeksen](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#encoding) naar Azure Storage worden geschreven/gelezen.
---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: 9213bdd8e63fc1e8ab7bdba8ac7a569be0dfe6be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93424718"
---
Voer de volgende opdracht uit om bijna in realtime [stroomlogboeken](../articles/azure-functions/functions-run-local.md#enable-streaming-logs) weer te geven:

```console
func azure functionapp logstream <APP_NAME> 
```

Roep in een afzonderlijk terminalvenster of in de browser opnieuw de externe functie aan. In de terminal wordt een uitgebreid logboek weergegeven van de uitvoering van de functie in Azure. 
---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: 9213bdd8e63fc1e8ab7bdba8ac7a569be0dfe6be
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424718"
---
Voer de volgende opdracht uit om bijna in realtime [stroomlogboeken](../articles/azure-functions/functions-run-local.md#enable-streaming-logs) weer te geven:

```console
func azure functionapp logstream <APP_NAME> 
```

Roep in een afzonderlijk terminalvenster of in de browser opnieuw de externe functie aan. In de terminal wordt een uitgebreid logboek weergegeven van de uitvoering van de functie in Azure. 
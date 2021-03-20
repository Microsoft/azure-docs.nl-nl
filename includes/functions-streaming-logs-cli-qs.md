---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: 9213bdd8e63fc1e8ab7bdba8ac7a569be0dfe6be
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93424718"
---
Voer de volgende opdracht uit om bijna in realtime [stroomlogboeken](../articles/azure-functions/functions-run-local.md#enable-streaming-logs) weer te geven:

```console
func azure functionapp logstream <APP_NAME> 
```

Roep in een afzonderlijk terminalvenster of in de browser opnieuw de externe functie aan. In de terminal wordt een uitgebreid logboek weergegeven van de uitvoering van de functie in Azure. 
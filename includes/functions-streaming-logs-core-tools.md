---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 0159ceb6e5d6d64a7a9bda383396607e4ce05b84
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96020341"
---
#### <a name="built-in-log-streaming"></a>Ingebouwde logboekstreaming

Gebruik de optie `logstream` om streaminglogboeken te ontvangen van een specifieke functie-app die wordt uitgevoerd in Azure, zoals in het volgende voorbeeld:

```bash
func azure functionapp logstream <FunctionAppName>
```

>[!NOTE]
>De ingebouwde logboekstreaming is in Core Tools nog niet ingeschakeld voor functie-apps die worden uitgevoerd op Linux in een verbruiksabonnement. Voor deze hostingabonnementen moet u in plaats daarvan Live Metrics Stream gebruiken om de logboeken nagenoeg in realtime weer te geven.

#### <a name="live-metrics-stream"></a>Live Metrics Stream

U kunt de [Live Metrics Stream](../articles/azure-monitor/app/live-stream.md) voor uw functie-app in een nieuw browservenster weergeven door de optie `--browser` op te nemen, zoals in het volgende voorbeeld:

```bash
func azure functionapp logstream <FunctionAppName> --browser
```

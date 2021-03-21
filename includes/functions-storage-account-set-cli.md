---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 4ace70abe0112e0fe27d177c02bcb697746c92cc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "78262020"
---
### <a name="set-the-storage-account-connection"></a>De Storage-accountverbinding instellen

Open het bestand local.settings.json en kopieer de waarde van `AzureWebJobsStorage`. Dit is de verbindingsreeks voor het Storage-account. Stel de `AZURE_STORAGE_CONNECTION_STRING`-omgevingsvariabele in op de verbindingsreeks met behulp van deze Bash-opdracht:

```bash
AZURE_STORAGE_CONNECTION_STRING="<STORAGE_CONNECTION_STRING>"
```

Wanneer u de verbindingsreeks instelt in de `AZURE_STORAGE_CONNECTION_STRING`-omgevingsvariabele, hebt u toegang tot uw Storage-account zonder dat er steeds verificatie nodig is.
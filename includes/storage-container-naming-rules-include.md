---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 3e5afeffa6f47ff79e31f189abc6d5e26d8f2586
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86050477"
---
Elke blob in Azure-opslag moet zich in een container bevinden. De container maakt deel uit van de blob-naam. Bijvoorbeeld, `mycontainer` is de naam van de container in deze voorbeelden van blob-URI's:

```http
https://storagesample.blob.core.windows.net/mycontainer/blob1.txt
https://storagesample.blob.core.windows.net/mycontainer/photos/myphoto.jpg
```

Een containernaam moet een geldige DNS-naam zijn die voldoet aan de volgende naamgevingsregels:

1. Containernamen moeten beginnen met een letter of cijfer en mogen alleen letters, cijfers en het streepje (-) bevatten.
2. Elk streepje (-) moet direct worden voorafgegaan en worden gevolgd door een letter of cijfer; opeenvolgende streepjes zijn in containernamen niet toegestaan.
3. Alle letters in een containernaam moeten kleine letters zijn.
4. Containernamen moeten 3 tot 63 tekens lang zijn.

> [!IMPORTANT]
> De naam van een container bestaat alleen uit kleine letters. Als u een hoofdletter in een containernaam opneemt of de naamgevingsregels op een andere manier schendt, wordt mogelijk een 400-fout (ongeldige aanvraag) weergegeven. 
> 
> 


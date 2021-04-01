---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: 1fc37c6f93fba34944caa7a91c2a89ce5dcdc398
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "78201939"
---
::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
> [!TIP]
> Tijdens het opstarten downloadt en installeert de host de [Storage-bindingsextensie](../articles/azure-functions/functions-bindings-storage-queue.md#functions-2x-and-higher) en andere Microsoft-bindingsextensies. Deze installatie vindt plaats omdat bindingsextensies standaard zijn ingeschakeld in het *host. json*-bestand met de volgende eigenschappen:
>
> ```json
> {
>     "version": "2.0",
>     "extensionBundle": {
>         "id": "Microsoft.Azure.Functions.ExtensionBundle",
>         "version": "[1.*, 2.0.0)"
>     }
> }
> ```
>
> Als u fouten ondervindt met bindingsextensies, controleert u of *host. json* de bovenstaande eigenschappen bevat.
::: zone-end  
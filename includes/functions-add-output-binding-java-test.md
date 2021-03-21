---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: 179ae760f146a5ac3041a54065ae12147f3f9bf0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97739808"
---
Omdat het archetype ook een reeks tests maakt, moet u deze tests bijwerken om de nieuwe parameter `msg` in de handtekening van methode `run` te verwerken.  

Blader naar de locatie van de testcode onder _src/test/java_, open projectbestand *Function.java* en vervang de regel met code onder `//Invoke` door de volgende code.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/test/java/com/function/FunctionTest.java" range="48-50":::

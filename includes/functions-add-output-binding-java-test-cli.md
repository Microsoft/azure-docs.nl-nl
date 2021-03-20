---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 6a359cdd44cc0c0cfbd93bd23b69a67a641c7fbb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "80673284"
---
## <a name="update-the-tests"></a>De tests bijwerken

Omdat het archetype ook een reeks tests maakt, moet u deze tests bijwerken om de nieuwe parameter `msg` in de handtekening van methode `run` te verwerken.  

Blader naar de locatie van de testcode onder _src/test/java_, open projectbestand *Function.java* en vervang de regel met code onder `//Invoke` door de volgende code.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/test/java/com/function/FunctionTest.java" range="48-50":::
---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 8bdd8b9d900cc50fdeb34ff7d233ac4d7e17a45c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "78191016"
---
Werkt *HttpExample\\\_\_init\_\_.py* bij om overeen te komen met de volgende code en voeg de parameter `msg` toe aan de functiedefinitie en `msg.set(name)` onder de instructie `if name:`.

:::code language="python" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

De parameter `msg` is een instantie van de [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest). De methode `set` schrijft een tekenreeksbericht naar de wachtrij, in dit geval de naam die aan de functie is doorgegeven in de URL-querytekenreeks.

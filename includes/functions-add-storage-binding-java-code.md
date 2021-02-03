---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: 9d5bae1aedbd031a0a3c08ba5141aebc22f1c674
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493373"
---
U kunt nu de nieuwe parameter, `msg`, gebruiken om naar de uitvoerbinding van uw functiecode te schrijven. Voordat u het antwoord krijgt dat de bewerking is geslaagd, voegt u de volgende regel code toe om de waarde van `name` aan de `msg`-uitvoerbinding toe te voegen.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="34":::

Als u een uitvoerbinding gebruikt, hoeft u niet de Azure Storage SDK-code voor verificatie te gebruiken, een wachtrijverwijzing op te halen of gegevens te schrijven. Deze taken worden voor u verwerkt via Functions-runtime en Queue Storage-uitvoerbinding.

De methode `run` moet er nu uitzien als in het volgende voorbeeld:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="16-38":::
---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 3705f58a37c109ebe0b774603c60e246fc174f25
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "80673258"
---
U kunt nu de nieuwe parameter, `msg`, gebruiken om naar de uitvoerbinding van uw functiecode te schrijven. Voordat u het antwoord krijgt dat de bewerking is geslaagd, voegt u de volgende regel code toe om de waarde van `name` aan de `msg`-uitvoerbinding toe te voegen.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="34":::

Als u een uitvoerbinding gebruikt, hoeft u niet de Azure Storage SDK-code voor verificatie te gebruiken, een wachtrijverwijzing op te halen of gegevens te schrijven. Deze taken worden voor u verwerkt via Functions-runtime en Queue Storage-uitvoerbinding.

De methode `run` moet er nu uitzien als in het volgende voorbeeld:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="17-38":::
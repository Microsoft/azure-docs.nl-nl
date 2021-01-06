---
title: Een Azure Functions Premium-abonnement maken in de portal
description: Meer informatie over het gebruik van de Azure Portal voor het maken van een functie-app die wordt uitgevoerd in het Premium-abonnement.
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: 20921423247dda3cbb39b58dcc805dac6d367390
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937659"
---
# <a name="create-a-premium-plan-function-app-in-the-azure-portal"></a>Een Premium-plan functie-app maken in de Azure Portal

Azure Functions biedt een schaalbaar Premium-abonnement dat virtuele netwerk connectiviteit biedt, geen koude start en Premium-hardware. Zie [Azure functions Premium-abonnement](functions-premium-plan.md)voor meer informatie. 

In dit artikel leert u hoe u de Azure Portal kunt gebruiken om een functie-app te maken in een Premium-abonnement. 

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u met uw Azure-account aan bij [Azure Portal](https://portal.azure.com).

## <a name="create-a-function-app"></a>Een functie-app maken

U moet een functie-app hebben die als host fungeert voor de uitvoering van uw functies. Met een functie-app kunt u functies groeperen in een logische eenheid, zodat u resources eenvoudiger kunt beheren, implementeren, schalen en delen.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

Op dit moment kunt u functies maken in de nieuwe functie-app. Deze functies kunnen profiteren van de voor delen van het [Premium-abonnement](functions-premium-plan.md).

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een door HTTP geactiveerde functie toevoegen](functions-create-first-azure-function.md#create-function)

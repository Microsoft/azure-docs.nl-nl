---
title: Een intentie bepalen met behulp van de REST API
description: In dit artikel gebruikt u een beschikbare openbare LUIS-app om de intentie van een gebruiker te bepalen aan de hand van beschrijvende tekst.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
zone_pivot_groups: programming-languages-set-one
ms.openlocfilehash: b49d90ffe2a262895727fdd4ef5f8a7950104102
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96437011"
---
# <a name="how-to-get-an-intent-using-the-rest-apis"></a>Een intentie bepalen met behulp van de REST API's

In dit artikel gebruikt u een LUIS-app om de intentie van een gebruiker te bepalen aan de hand van beschrijvende tekst. Verzend de intentie van de gebruiker als tekst naar het HTTP-voorspellingseindpunt van de Pizza-app. Bij het eindpunt wordt via LUIS het model van de Pizza-app toegepast om de betekenis van tekst in natuurlijke taal te analyseren. Hiermee wordt de algehele intentie bepaald en worden gegevens geëxtraheerd die relevant zijn voor het onderwerpdomein van de app.

Voor dit artikel hebt u een gratis [LUIS](https://www.luis.ai)-account nodig.

<a name="create-luis-subscription-key"></a>

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get intent with C# and REST](./includes/get-started-get-intent-rest-csharp.md)]
::: zone-end

::: zone pivot="programming-language-go"
[!INCLUDE [Get intent with Go and REST](./includes/get-started-get-intent-rest-go.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Get intent with Java and REST](./includes/get-started-get-intent-rest-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Get intent with Node.js and REST](./includes/get-started-get-intent-rest-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Get intent with Python and REST](./includes/get-started-get-intent-rest-python.md)]
::: zone-end

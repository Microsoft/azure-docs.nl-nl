---
title: Vereisten voor gegevensschema
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: 4c55c25621df1925b6ed6c374d8af88551eb1e46
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96231426"
---
Metrics Advisor is een service voor het detecteren, diagnosticeren en analyseren van anomalieën in tijdreeksen. Het is een AI-service waarin uw gegevens worden gebruikt voor het trainen van het gebruikte model. De service accepteert tabellen van geaggregeerde gegevens met de volgende kolommen:

* **Meting** (vereist): een of meer kolommen met numerieke waarden.
* **Tijdstempel** (optioneel): nul of een kolom met het type `DateTime` of `String`. Als deze kolom niet is ingesteld, wordt het tijdstempel ingesteld op de begintijd van elke opnameperiode. Geef het tijdstempel op in de indeling `yyyy-MM-ddTHH:mm:ssZ`. 
  * **Uw tijdstempel moet overeenkomen met de granulariteit van de metriek. Voor een dagelijkse metriek moeten bijvoorbeeld het uur, de minuut en de seconde van het tijdstempel worden gelabeld als `00:00:00`** .
* **Dimensie** (optioneel): Kolommen kunnen van elk gegevenstype zijn. Wees voorzichtig bij het werken met grote hoeveelheden kolommen en waarden, om te voorkomen dat er buitensporig veel dimensies worden verwerkt.

> [!Note]
> Voor elke metriek mag er slechts één tijdstempel per meting zijn, die overeenkomt met één dimensiecombinatie. Verzamel uw gegevens vóór het onboarden of gebruik de query om op te geven welke gegevens moeten worden opgenomen.
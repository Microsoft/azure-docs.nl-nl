---
title: Ondersteuning voor containers
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: 037b9746571678e24e80bd76e7e2ed173ab8eb90
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97677592"
---
## <a name="create-an-anomaly-detector-resource"></a>Een Anomaly Detector-resource maken

1. Meld u aan bij de <a href="https://portal.azure.com" target="_blank">Azure Portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
1. Selecteer een <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector" target="_blank">anomalie detector <span class="docon docon-navigate-external x-hidden-focus"></span> </a> -resource maken.
1. Geef alle vereiste instellingen op:

    |Instelling|Waarde|
    |--|--|
    |Naam|Gewenste naam (2-64 tekens)|
    |Abonnement|Selecteer het juiste abonnement|
    |Locatie|Selecteer een locatie in de buurt en beschik bare locaties|
    |Prijscategorie|`F0` -10 aanroepen per seconde, 20.000 trans acties per maand. <br> Of<br> `S0` -80 aanroepen per seconde|
    |Resourcegroep|Een beschik bare resource groep selecteren|

1. Klik op **maken** en wacht tot de resource is gemaakt. Nadat deze is gemaakt, gaat u naar de pagina Resource
1. Geconfigureerde `endpoint` en API-sleutel verzamelen:

    |Sleutels en tabblad eind punt in de portal|Instelling|Waarde|
    |--|--|--|
    |**Overzicht**|Eindpunt|Kopieer het eind punt. Deze lijkt op ` https://<your-resource-name>.cognitiveservices.azure.com/`|
    |**Sleutels**|API-sleutel|Kopieer 1 van de twee sleutels. Het is een teken reeks van 32 alfanumerieke tekens zonder spaties of streepjes `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` .|




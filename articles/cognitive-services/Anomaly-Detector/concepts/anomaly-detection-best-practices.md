---
title: Best practices bij het gebruik van de Anomaly Detector-API
titleSuffix: Azure Cognitive Services
description: Meer informatie over aanbevolen procedures voor het detecteren van afwijkingen met de anomalie detectie-API.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 01/22/2021
ms.author: mbullwin
ms.openlocfilehash: b0869335c386712e6b759bb0ced459ebd1bf383c
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98702723"
---
# <a name="best-practices-for-using-the-anomaly-detector-api"></a>Aanbevolen procedures voor het gebruik van de anomalie detectie-API

De afwijkende detector-API is een staatloze detectie service voor stateless. De nauw keurigheid en prestaties van de resultaten kunnen worden beïnvloed door:

* Hoe uw time series-gegevens worden voor bereid.
* De afwijkende detector API-para meters die zijn gebruikt.
* Het aantal gegevens punten in uw API-aanvraag. 

In dit artikel vindt u meer informatie over de aanbevolen procedures voor het gebruik van de API voor het verkrijgen van de beste resultaten voor uw gegevens. 

## <a name="when-to-use-batch-entire-or-latest-last-point-anomaly-detection"></a>Wanneer u batch (volledig) of laatste (laatste) punt anomalie detectie wilt gebruiken

Met het batch detectie-eind punt van de afwijkings detectie-API kunt u afwijkingen detecteren via uw hele reeks gegevens. In deze detectie modus wordt één statistisch model gemaakt en toegepast op elk punt in de gegevensset. Als uw tijd reeks de onderstaande kenmerken heeft, kunt u het beste batch detectie gebruiken om een voor beeld van uw gegevens in één API-aanroep te bekijken.

* Een seizoen tijd reeks, met incidentele afwijkingen.
* Een reeks vaste trend tijden, met incidentele pieken/spannings dips. 

Het is niet raadzaam om batch anomalie detectie te gebruiken voor realtime gegevens bewaking of het gebruik ervan op tijdreeks gegevens die niet de bovenstaande kenmerken hebben. 

* Batch detectie maakt en past slechts één model toe, de detectie voor elk punt wordt uitgevoerd in de context van de hele reeks. Als de tijdreeks gegevens trends omhoog en omlaag dalen zonder seizoensgebondenheid, kunnen sommige wijzigingen (spannings dips en pieken in de gegevens) worden gemist door het model. Het is ook mogelijk dat sommige wijzigingen die minder belang rijk zijn dan later in de gegevensset, niet als groot genoeg worden beschouwd om in het model te worden opgenomen.

* Batch detectie is langzamer dan het detecteren van de afwijkings status van het laatste punt bij het uitvoeren van real-time gegevens bewaking, vanwege het aantal punten dat wordt geanalyseerd.

Voor realtime gegevens bewaking wordt aangeraden de afwijkings status van het meest recente gegevens punt alleen op te sporen. Door voortdurend de nieuwste punt detectie toe te passen, kan streaming-gegevens bewaking efficiënter en nauw keuriger worden uitgevoerd.

In het volgende voor beeld wordt de impact beschreven die deze detectie modi kunnen hebben op de prestaties. In de eerste afbeelding ziet u het resultaat van het voortdurend detecteren van het laatste punt van de afwijkings status, evenals 28 eerder waargenomen gegevens punten. De rode punten zijn afwijkingen.

![Een afbeelding met een afwijkings detectie met het laatste punt](../media/last.png)

Hieronder ziet u dezelfde gegevensset met behulp van batch anomalie detectie. Het model dat is gemaakt voor de bewerking, heeft verschillende afwijkingen genegeerd, gemarkeerd door rechthoeken.

![Een afbeelding met de afwijkings detectie met de batch-methode](../media/entire.png)

## <a name="data-preparation"></a>Gegevensvoorbereiding

De anomalie detectie-API accepteert tijd reeks gegevens die zijn ingedeeld in een JSON-aanvraag object. Een tijd reeks kan de numerieke gegevens in sequentiële volg orde in de loop van de tijd worden vastgelegd. U kunt Windows van uw tijdreeks gegevens naar het afwijkings detector-API-eind punt verzenden om de prestaties van de API te verbeteren. Het minimum aantal gegevens punten dat u kunt verzenden is 12 en de maximum waarde is 8640 punten. [Granulariteit](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity?view=azure-dotnet-preview) wordt gedefinieerd als de snelheid waarmee uw gegevens worden bemonsterd. 

Gegevens punten die worden verzonden naar de anomalie detectie-API moeten een geldig UTC-tijds tempel (Coordinated Universal Time) en een numerieke waarde hebben. 

```json
{
    "granularity": "daily",
    "series": [
      {
        "timestamp": "2018-03-01T00:00:00Z",
        "value": 32858923
      },
      {
        "timestamp": "2018-03-02T00:00:00Z",
        "value": 29615278
      },
    ]
}
```

Als uw gegevens worden bemonsterd met een niet-standaard tijds interval, kunt u deze opgeven door het kenmerk toe te voegen aan `customInterval` uw aanvraag. Als uw reeks bijvoorbeeld elke vijf minuten steek proeft, kunt u het volgende toevoegen aan uw JSON-aanvraag:

```json
{
    "granularity" : "minutely", 
    "customInterval" : 5
}
```

### <a name="missing-data-points"></a>Ontbrekende gegevens punten

Ontbrekende gegevens punten zijn gebruikelijk in gelijkmatig gedistribueerde tijdreeks gegevens sets, met name met een fijne granulariteit (een klein steekproef interval. Bijvoorbeeld gegevens die elke paar minuten worden sampled). Minder dan 10% van het verwachte aantal punten in uw gegevens mag geen negatieve invloed hebben op de detectie resultaten. Overweeg het invullen van hiaten in uw gegevens op basis van de kenmerken zoals het vervangen van gegevens punten uit een eerdere periode, lineaire interpolatie of een zwevend gemiddelde.

### <a name="aggregate-distributed-data"></a>Gedistribueerde gegevens verzamelen

De anomalie detectie-API werkt het beste op een even gedistribueerde tijd reeks. Als uw gegevens wille keurig worden gedistribueerd, moet u deze samen voegen met een tijds eenheid, zoals per minuut, elk uur of dagelijks.

## <a name="anomaly-detection-on-data-with-seasonal-patterns"></a>Anomalie detectie voor gegevens met seizoensgebonden patronen

Als u weet dat uw time series-gegevens een seizoensgebonden patroon hebben (een met regel matige intervallen), kunt u de nauw keurigheid en API-reactie tijd verbeteren. 

`period`Wanneer u een JSON-aanvraag maakt, kan er een afwijkende detectie latentie van Maxi maal 50% worden opgegeven. Het `period` is een geheel getal dat ongeveer aangeeft hoeveel gegevens punten de tijd reeks duurt om een patroon te herhalen. Een tijd reeks met één gegevens punt per dag zou bijvoorbeeld een `period` as hebben `7` en een tijd reeks met één punt per uur (met hetzelfde wekelijkse patroon) zou een `period` van hebben  `7*24` . Als u niet zeker bent van de patronen van uw gegevens, hoeft u deze para meter niet op te geven.

Voor de beste resultaten geeft u vier `period` gegevens punten op, plus een extra. Zo moeten gegevens per uur met een wekelijks patroon, zoals hierboven wordt beschreven, 673 gegevens punten leveren in de aanvraag tekst ( `7 * 24 * 4 + 1` ).

### <a name="sampling-data-for-real-time-monitoring"></a>Bemonsterings gegevens voor realtime-bewaking

Als uw streaminggegevens worden bemonsterd met een kort interval (bijvoorbeeld seconden of minuten), kan het verzenden van het aanbevolen aantal gegevens punten het Maxi maal toegestane maximum aantal (8640 gegevens punten) van de anomalie detectie API overschrijden. Als uw gegevens een stabiel seizoensgebonden patroon tonen, kunt u overwegen om een voor beeld van uw time series-gegevens te verzenden met een groter tijds interval, zoals uren. Het op deze manier bemonsteren van uw gegevens kan de reactie tijd van de API aanzienlijk verbeteren. 

## <a name="next-steps"></a>Volgende stappen

* [Wat is de Anomaly Detector-API?](../overview.md)
* [Snelstart: Anomalieën detecteren in uw tijdreeksgegevens met behulp van de Anomaly Detector](../quickstarts/client-libraries.md)
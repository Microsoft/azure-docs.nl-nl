---
title: Configureer bare regels op basis van drempel waarden in Azure Stream Analytics
description: In dit artikel wordt beschreven hoe u referentie gegevens gebruikt voor het bezorgen van een waarschuwings oplossing met geconfigureerde regels voor drempel waarden in Azure Stream Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 04/30/2018
ms.openlocfilehash: 2f9d132084f0254486be533daea6b54239f4e450
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019972"
---
# <a name="process-configurable-threshold-based-rules-in-azure-stream-analytics"></a>Configureer bare op drempel waarden gebaseerde regels verwerken in Azure Stream Analytics
In dit artikel wordt beschreven hoe u referentie gegevens gebruikt voor het bezorgen van een waarschuwings oplossing die gebruikmaakt van Configureer bare regels op basis van drempel waarden in Azure Stream Analytics.

## <a name="scenario-alerting-based-on-adjustable-rule-thresholds"></a>Scenario: waarschuwingen op basis van drempel waarden voor aanpas bare regels
Mogelijk moet u een waarschuwing maken als uitvoer wanneer binnenkomende gestreamde gebeurtenissen een bepaalde waarde hebben bereikt of wanneer een geaggregeerde waarde op basis van de binnenkomende gestreamde gebeurtenissen een bepaalde drempel overschrijdt. Het is eenvoudig om een Stream Analytics query in te stellen waarmee de waarde wordt vergeleken met een statische en vooraf vastgestelde drempel. Een vaste drempel waarde kan worden vastgelegd in de query syntaxis voor streamen met behulp van eenvoudige numerieke vergelijkingen (groter dan, kleiner dan en gelijk aan).

In sommige gevallen moeten de drempel waarden eenvoudiger kunnen worden geconfigureerd zonder de query syntaxis te bewerken telkens wanneer een drempel waarde wordt gewijzigd. In andere gevallen hebt u mogelijk talloze apparaten of gebruikers nodig die door dezelfde query worden verwerkt, waarbij elk daarvan een andere drempel waarde heeft op elk type apparaat. 

Dit patroon kan worden gebruikt om drempel waarden dynamisch te configureren, selectief te kiezen welk type apparaat de drempel waarde toepast door de invoer gegevens te filteren en selectief te kiezen welke velden in de uitvoer moeten worden meegenomen.

## <a name="recommended-design-pattern"></a>Aanbevolen ontwerp patroon
Gebruik een invoer voor referentie gegevens naar een Stream Analytics taak als zoek opdracht van de drempel waarden voor waarschuwingen:
- Sla de drempel waarden op in de referentie gegevens, één waarde per sleutel.
- Voeg de streaming-gegevens invoer gebeurtenissen toe aan de referentie gegevens in de sleutel kolom.
- Gebruik de waarde van het sleutel veld van de referentie gegevens als drempel waarde.

## <a name="example-data-and-query"></a>Voorbeeld gegevens en query's
In het voor beeld worden waarschuwingen gegenereerd wanneer de samen voeging van gegevens stromen in van apparaten in een minuut-lang-venster overeenkomt met de opgegeven waarden in de regel die als referentie gegevens wordt verstrekt.

In de query, voor elk deviceId en elke metrische waarde onder het apparaat-id, kunt u van 0 tot 5 dimensies instellen op GROUP BY. Alleen de gebeurtenissen met de bijbehorende filter waarden worden gegroepeerd. Eenmaal gegroepeerde aggregaties van min, Max, Gem, worden berekend op basis van een tumblingvenstertriggers-venster van 60 seconden. Filters voor de geaggregeerde waarden worden vervolgens berekend op basis van de geconfigureerde drempel waarde in de verwijzing om de uitvoer gebeurtenis voor waarschuwingen te genereren.

Stel dat er een Stream Analytics-taak is met een referentie gegevens invoer met de naam **regels** en de gegevens invoer streamen met de naam **Metrics**. 

## <a name="reference-data"></a>Referentie gegevens
In dit voor beeld wordt uitgelegd hoe een regel op basis van een drempel waarde kan worden weer gegeven. Een JSON-bestand bevat de referentie gegevens en wordt opgeslagen in Azure Blob Storage en die Blob Storage-container wordt gebruikt als referentie gegevens invoer met de naam **regels**. U kunt dit JSON-bestand overschrijven en de regel configuratie vervangen wanneer de tijd verloopt, zonder de streaming-taak te stoppen of te starten.

- De voorbeeld regel wordt gebruikt om een aanpas bare waarschuwing weer te geven wanneer de CPU groter is dan of gelijk is aan de waarde `90` percentage. Het `value` veld kan zo nodig worden geconfigureerd.
- U ziet dat de regel een **operator** veld heeft, die dynamisch wordt geïnterpreteerd in de query syntaxis verderop in `AVGGREATEROREQUAL` . 
- Met de regel worden de gegevens op een bepaalde dimensie sleutel met waarde gefilterd `2` `C1` . Andere velden zijn een lege teken reeks, waarmee wordt aangegeven dat de invoer stroom niet moet worden gefilterd op die gebeurtenis velden. U kunt extra CPU-regels instellen voor het filteren van andere overeenkomende velden als dat nodig is.
- Niet alle kolommen moeten worden opgenomen in de uitvoer waarschuwings gebeurtenis. In dit geval `includedDim` is het sleutel nummer `2` ingeschakeld `TRUE` om aan te geven dat veld nummer 2 van gebeurtenis gegevens in de stroom wordt opgenomen in de in aanmerking komende uitvoer gebeurtenissen. De andere velden zijn niet opgenomen in de uitvoer van de waarschuwing, maar de lijst met velden kan worden aangepast.


```json
{
    "ruleId": 1234, 
    "deviceId" : "978648", 
    "metricName": "CPU", 
    "alertName": "hot node AVG CPU over 90",
    "operator" : "AVGGREATEROREQUAL",
    "value": 90, 
    "includeDim": {
        "0": "FALSE", 
        "1": "FALSE", 
        "2": "TRUE", 
        "3": "FALSE", 
        "4": "FALSE"
    },
    "filter": {
        "0": "", 
        "1": "",
        "2": "C1", 
        "3": "", 
        "4": ""
    }    
}
```

## <a name="example-streaming-query"></a>Voor beeld van streaming-query
In dit voor beeld Stream Analytics query worden toegevoegd aan de referentie gegevens van de **regel** uit het bovenstaande voor beeld naar een invoer stroom met de naam **metrische** gegevens.

```sql
WITH transformedInput AS
(
    SELECT
        dim0 = CASE rules.includeDim.[0] WHEN 'TRUE' THEN metrics.custom.dimensions.[0].value ELSE NULL END,
        dim1 = CASE rules.includeDim.[1] WHEN 'TRUE' THEN metrics.custom.dimensions.[1].value ELSE NULL END,
        dim2 = CASE rules.includeDim.[2] WHEN 'TRUE' THEN metrics.custom.dimensions.[2].value ELSE NULL END,
        dim3 = CASE rules.includeDim.[3] WHEN 'TRUE' THEN metrics.custom.dimensions.[3].value ELSE NULL END,
        dim4 = CASE rules.includeDim.[4] WHEN 'TRUE' THEN metrics.custom.dimensions.[4].value ELSE NULL END,
        metric = metrics.metric.value,
        metricName = metrics.metric.name,
        deviceId = rules.deviceId, 
        ruleId = rules.ruleId, 
        alertName = rules.alertName,
        ruleOperator = rules.operator, 
        ruleValue = rules.value
    FROM 
        metrics
        timestamp by eventTime
    JOIN 
        rules
        ON metrics.deviceId = rules.deviceId AND metrics.metric.name = rules.metricName
    WHERE
        (rules.filter.[0] = '' OR metrics.custom.filters.[0].value = rules.filter.[0]) AND 
        (rules.filter.[1] = '' OR metrics.custom.filters.[1].value = rules.filter.[1]) AND
        (rules.filter.[2] = '' OR metrics.custom.filters.[2].value = rules.filter.[2]) AND
        (rules.filter.[3] = '' OR metrics.custom.filters.[3].value = rules.filter.[3]) AND
        (rules.filter.[4] = '' OR metrics.custom.filters.[4].value = rules.filter.[4])
)

SELECT
    System.Timestamp as time, 
    transformedInput.deviceId as deviceId,
    transformedInput.ruleId as ruleId,
    transformedInput.metricName as metric,
    transformedInput.alertName as alert,
    AVG(metric) as avg,
    MIN(metric) as min, 
    MAX(metric) as max, 
    dim0, dim1, dim2, dim3, dim4
FROM
    transformedInput
GROUP BY
    transformedInput.deviceId,
    transformedInput.ruleId,
    transformedInput.metricName,
    transformedInput.alertName,
    dim0, dim1, dim2, dim3, dim4,
    ruleOperator, 
    ruleValue, 
    TumblingWindow(second, 60)
HAVING
    (
        (ruleOperator = 'AVGGREATEROREQUAL' AND avg(metric) >= ruleValue) OR
        (ruleOperator = 'AVGEQUALORLESS' AND avg(metric) <= ruleValue) 
    )
```

## <a name="example-streaming-input-event-data"></a>Voor beeld van gegevens stromen invoer gebeurtenissen
In dit voor beeld wordt gebruikgemaakt van de **metrische** invoer gegevens die worden gebruikt in de bovenstaande streaming-query. 

- Drie voorbeeld gebeurtenissen worden weer gegeven binnen de time span van 1 minuut, waarde `T14:50` . 
- Alle drie hebben dezelfde `deviceId` waarde `978648` .
- De waarden voor de CPU-waarde verschillen per gebeurtenis, `98` , `95` `80` respectievelijk. Alleen de eerste twee voorbeeld gebeurtenissen overschrijden de CPU-waarschuwings regel die in de regel is vastgelegd.
- Het veld includeDim in de waarschuwings regel is sleutel nummer 2. Het bijbehorende veld sleutel 2 in de voorbeeld gebeurtenissen heet `NodeName` . De drie voorbeeld gebeurtenissen hebben `N024` respectievelijk waarden `N024` `N014` . In de uitvoer ziet u alleen het knoop punt, alleen de `N024` gegevens die overeenkomen met de waarschuwings criteria voor hoge CPU. `N014` voldoet niet aan de hoge CPU-drempel.
- De waarschuwings regel is geconfigureerd met een `filter` enkel op sleutel nummer 2, die overeenkomt met het `cluster` veld in de voorbeeld gebeurtenissen. De drie voorbeeld gebeurtenissen hebben allemaal `C1` een waarde die overeenkomt met de filter criteria.

```json
{
    "eventTime": "2018-04-30T14:50:23.1324132Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N1"
            },
            "1": {
                "name": "Cluster",
                "value": "C1"
            },
            "2": {
                "name": "NodeName",
                "value": "N024"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N1"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 98,
        "count": 1.0,
        "min": 98,
        "max": 98,
        "stdDev": 0.0
    }
}
{
    "eventTime": "2018-04-30T14:50:24.1324138Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N2"
            },
            "1": {
                "name": "Cluster",
                "value": "C1"
            },
            "2": {
                "name": "NodeName",
                "value": "N024"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N2"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 95,
        "count": 1,
        "min": 95,
        "max": 95,
        "stdDev": 0
    }
}
{
    "eventTime": "2018-04-30T14:50:37.1324130Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N3"
            },
            "1": {
                "name": "Cluster",
                "value": "C1 "
            },
            "2": {
                "name": "NodeName",
                "value": "N014"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N3"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 80,
        "count": 1,
        "min": 80,
        "max": 80,
        "stdDev": 0
    }
}
```

## <a name="example-output"></a>Voorbeelduitvoer
In dit voor beeld wordt een enkele waarschuwings gebeurtenis geproduceerd op basis van de CPU-drempel regel die is gedefinieerd in de referentie gegevens. De gebeurtenis output bevat de naam van de waarschuwing en de geaggregeerde (gemiddelde, min, Max) van de overwogen velden. De gegevens van de uitvoer gebeurtenis bevatten de waarde voor veld sleutel nummer 2 `NodeName` `N024` als gevolg van de regel configuratie. (De JSON is gewijzigd om regel einden weer te geven voor de Lees baarheid.)

```JSON
{"time":"2018-05-01T02:03:00.0000000Z","deviceid":"978648","ruleid":1234,"metric":"CPU",
"alert":"hot node AVG CPU over 90","avg":96.5,"min":95.0,"max":98.0,
"dim0":null,"dim1":null,"dim2":"N024","dim3":null,"dim4":null}
```

---
title: Door de gebruiker gedefinieerde Java script-aggregaties in Azure Stream Analytics
description: In dit artikel wordt beschreven hoe u geavanceerde query mechanismen kunt uitvoeren met door de gebruiker gedefinieerde Java script-aggregaties in Azure Stream Analytics.
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2017
ms.custom: devx-track-js
ms.openlocfilehash: e9695a268d1c6ec43a737afd36536e4ec2a41a41
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98012475"
---
# <a name="azure-stream-analytics-javascript-user-defined-aggregates"></a>Door de gebruiker gedefinieerde Java script-functies Azure Stream Analytics
 
Azure Stream Analytics ondersteunt door de gebruiker gedefinieerde aggregaties (UDA) die zijn geschreven in Java script, kunt u complexe stateful bedrijfs logica implementeren. Binnen UDA hebt u volledige controle over de status gegevens structuur, status accumulatie, status decumulatie en cumulatieve resultaat berekening. In het artikel worden de twee verschillende java script UDA-interfaces geïntroduceerd, stappen voor het maken van een UDA en het gebruik van UDA met op Windows gebaseerde bewerkingen in Stream Analytics query.

## <a name="javascript-user-defined-aggregates"></a>Door de gebruiker gedefinieerde Java script-samen stellingen

Een door de gebruiker gedefinieerde statistische functie wordt bovenop een tijd venster specificatie gebruikt om een aggregatie over de gebeurtenissen in dat venster te genereren en een enkele resultaat waarde te produceren. Er zijn twee typen UDA-interfaces die Stream Analytics ondersteunen: vandaag, AccumulateOnly en AccumulateDeaccumulate. Beide typen UDA kunnen worden gebruikt door Tumblingvenstertriggers, verspringen, schuiven en het sessie venster. AccumulateDeaccumulate UDA werkt beter dan AccumulateOnly UDA wanneer het wordt gebruikt in combi natie met het venster voor verspringen, schuiven en sessies. U kiest een van de twee typen op basis van het algoritme dat u gebruikt.

### <a name="accumulateonly-aggregates"></a>AccumulateOnly-aggregaties

AccumulateOnly-aggregaties kunnen alleen nieuwe gebeurtenissen in de staat van de status verzamelen. het algoritme staat geen deaccumulatie van waarden toe. Kies dit aggregatie type wanneer het niet mogelijk is om een gebeurtenis informatie van de status waarde uit te voeren. Hieronder volgt de Java script-sjabloon voor AccumulatOnly-aggregaties:

```JavaScript
// Sample UDA which state can only be accumulated.
function main() {
    this.init = function () {
        this.state = 0;
    }

    this.accumulate = function (value, timestamp) {
        this.state += value;
    }

    this.computeResult = function () {
        return this.state;
    }
}
```

### <a name="accumulatedeaccumulate-aggregates"></a>AccumulateDeaccumulate-aggregaties

Met AccumulateDeaccumulate aggregaties wordt de ontdubbeling van een vorige verzamelde waarde uit de status mogelijk, zoals het verwijderen van een sleutel-waardepaar uit een lijst met gebeurtenis waarden, of het aftrekken van een waarde uit een status van Sum aggregate. Hieronder volgt de Java script-sjabloon voor AccumulateDeaccumulate-aggregaties:

```JavaScript
// Sample UDA which state can be accumulated and deaccumulated.
function main() {
    this.init = function () {
        this.state = 0;
    }

    this.accumulate = function (value, timestamp) {
        this.state += value;
    }

    this.deaccumulate = function (value, timestamp) {
        this.state -= value;
    }

    this.deaccumulateState = function (otherState){
        this.state -= otherState.state;
    }

    this.computeResult = function () {
        return this.state;
    }
}
```

## <a name="uda---javascript-function-declaration"></a>UDA-java script-functie declaratie

Elke Java script-UDA wordt gedefinieerd door een functie object declaratie. Hier volgen de belangrijkste elementen in een UDA-definitie.

### <a name="function-alias"></a>Functiealias

De functie alias is de UDA-id. Gebruik in Stream Analytics query altijd UDA-alias in combi natie met een ' UDA '. 'com.microsoft.intune.mam'.

### <a name="function-type"></a>Functie type

Voor UDA moet het functie type **Java script-UDA** zijn.

### <a name="output-type"></a>Uitvoertype

Een specifiek type dat Stream Analytics taak wordt ondersteund of ' any ' als u het type wilt afhandelen in uw query.

### <a name="function-name"></a>Functienaam

De naam van dit functie object. De functie naam moet overeenkomen met de UDA-alias.

### <a name="method---init"></a>Methode-init ()

De methode init () initialiseert de status van de aggregatie. Deze methode wordt aangeroepen wanneer het venster wordt gestart.

### <a name="method--accumulate"></a>Methode – cumulatief ()

De methode accumule () berekent de UDA-status op basis van de vorige status en de huidige gebeurtenis waarden. Deze methode wordt aangeroepen wanneer een gebeurtenis een tijd venster binnengaat (TUMBLINGWINDOW, HOPPINGWINDOW, SLIDINGWINDOW of SESSIONWINDOW).

### <a name="method--deaccumulate"></a>Methode – deaccumulatie ()

De methode decumulatie () berekent de status opnieuw op basis van de vorige status en de huidige gebeurtenis waarden. Deze methode wordt aangeroepen wanneer een gebeurtenis een SLIDINGWINDOW of SESSIONWINDOW verlaat.

### <a name="method--deaccumulatestate"></a>Methode – deaccumulateState ()

De methode deaccumulateState () berekent de status opnieuw op basis van de vorige status en de status van een hop. Deze methode wordt aangeroepen wanneer een set gebeurtenissen een HOPPINGWINDOW verlaat.

### <a name="method--computeresult"></a>Methode – computeResult ()

De methode computeResult () retourneert een aggregatie resultaat op basis van de huidige status. Deze methode wordt aan het einde van een tijd venster aangeroepen (TUMBLINGWINDOW, HOPPINGWINDOW, SLIDINGWINDOW of SESSIONWINDOW).

## <a name="javascript-uda-supported-input-and-output-data-types"></a>Java script UDA ondersteunde invoer-en uitvoer gegevens typen
Raadpleeg voor Java script UDA-gegevens typen sectie **Stream Analytics en Java script-type conversie** van [Java script-udf's integreren](stream-analytics-javascript-user-defined-functions.md).

## <a name="adding-a-javascript-uda-from-the-azure-portal"></a>Een Java script-UDA toevoegen vanuit de Azure Portal

Hieronder vindt u een overzicht van het proces voor het maken van een UDA vanuit de portal. Het voor beeld dat hier wordt gebruikt, is het berekenen van tijd gewogen gemiddelden.

Nu gaan we een Java script-UDA maken onder een bestaande ASA-taak door de volgende stappen uit te voeren.

1. Meld u aan bij Azure Portal en zoek de bestaande Stream Analytics taak.
1. Klik vervolgens op de koppeling functies onder **taak topologie**.
1. Klik op het pictogram **toevoegen** om een nieuwe functie toe te voegen.
1. Selecteer in de nieuwe functie weergave **Java script UDA** als het functie type. vervolgens ziet u een standaard UDA-sjabloon in de editor.
1. Vul "TWA" in als de UDA-alias en wijzig de functie-implementatie als volgt:

    ```JavaScript
    // Sample UDA which calculate Time-Weighted Average of incoming values.
    function main() {
        this.init = function () {
            this.totalValue = 0.0;
            this.totalWeight = 0.0;
        }

        this.accumulate = function (value, timestamp) {
            this.totalValue += value.level * value.weight;
            this.totalWeight += value.weight;

        }

        // Uncomment below for AccumulateDeaccumulate implementation
        /*
        this.deaccumulate = function (value, timestamp) {
            this.totalValue -= value.level * value.weight;
            this.totalWeight -= value.weight;
        }

        this.deaccumulateState = function (otherState){
            this.state -= otherState.state;
            this.totalValue -= otherState.totalValue;
            this.totalWeight -= otherState.totalWeight;
        }
        */

        this.computeResult = function () {
            if(this.totalValue == 0) {
                result = 0;
            }
            else {
                result = this.totalValue/this.totalWeight;
            }
            return result;
        }
    }
    ```

1. Wanneer u op de knop ' opslaan ' klikt, wordt uw UDA weer gegeven in de lijst met functies.

1. Klik op de nieuwe functie "TWA", u kunt de functie definitie controleren.

## <a name="calling-javascript-uda-in-asa-query"></a>Java script-UDA aanroepen in ASA-query

In Azure Portal en open uw taak, bewerkt u de query en roept u de functie TWA () aan met het voor voegsel Uda. Bijvoorbeeld:

```SQL
WITH value AS
(
    SELECT
    NoiseLevelDB as level,
    DurationSecond as weight
FROM
    [YourInputAlias] TIMESTAMP BY EntryTime
)
SELECT
    System.Timestamp as ts,
    uda.TWA(value) as NoseDoseTWA
FROM value
GROUP BY TumblingWindow(minute, 5)
```

## <a name="testing-query-with-uda"></a>Query's testen met UDA

Maak een lokaal JSON-bestand met onderstaande inhoud, upload het bestand naar Stream Analytics-taak en test hierboven de query.

```JSON
[
  {"EntryTime": "2017-06-10T05:01:00-07:00", "NoiseLevelDB": 80, "DurationSecond": 22.0},
  {"EntryTime": "2017-06-10T05:02:00-07:00", "NoiseLevelDB": 81, "DurationSecond": 37.8},
  {"EntryTime": "2017-06-10T05:02:00-07:00", "NoiseLevelDB": 85, "DurationSecond": 26.3},
  {"EntryTime": "2017-06-10T05:03:00-07:00", "NoiseLevelDB": 95, "DurationSecond": 13.7},
  {"EntryTime": "2017-06-10T05:03:00-07:00", "NoiseLevelDB": 88, "DurationSecond": 10.3},
  {"EntryTime": "2017-06-10T05:05:00-07:00", "NoiseLevelDB": 103, "DurationSecond": 5.5},
  {"EntryTime": "2017-06-10T05:06:00-07:00", "NoiseLevelDB": 99, "DurationSecond": 23.0},
  {"EntryTime": "2017-06-10T05:07:00-07:00", "NoiseLevelDB": 108, "DurationSecond": 1.76},
  {"EntryTime": "2017-06-10T05:07:00-07:00", "NoiseLevelDB": 79, "DurationSecond": 17.9},
  {"EntryTime": "2017-06-10T05:08:00-07:00", "NoiseLevelDB": 83, "DurationSecond": 27.1},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 91, "DurationSecond": 17.1},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 115, "DurationSecond": 7.9},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 80, "DurationSecond": 28.3},
  {"EntryTime": "2017-06-10T05:10:00-07:00", "NoiseLevelDB": 55, "DurationSecond": 18.2},
  {"EntryTime": "2017-06-10T05:10:00-07:00", "NoiseLevelDB": 93, "DurationSecond": 25.8},
  {"EntryTime": "2017-06-10T05:11:00-07:00", "NoiseLevelDB": 83, "DurationSecond": 11.4},
  {"EntryTime": "2017-06-10T05:12:00-07:00", "NoiseLevelDB": 89, "DurationSecond": 7.9},
  {"EntryTime": "2017-06-10T05:15:00-07:00", "NoiseLevelDB": 112, "DurationSecond": 3.7},
  {"EntryTime": "2017-06-10T05:15:00-07:00", "NoiseLevelDB": 93, "DurationSecond": 9.7},
  {"EntryTime": "2017-06-10T05:18:00-07:00", "NoiseLevelDB": 96, "DurationSecond": 3.7},
  {"EntryTime": "2017-06-10T05:20:00-07:00", "NoiseLevelDB": 108, "DurationSecond": 0.99},
  {"EntryTime": "2017-06-10T05:20:00-07:00", "NoiseLevelDB": 113, "DurationSecond": 25.1},
  {"EntryTime": "2017-06-10T05:22:00-07:00", "NoiseLevelDB": 110, "DurationSecond": 5.3}
]
```

## <a name="get-help"></a>Hulp vragen

Voor meer informatie kunt u onze [micro soft Q&een vraag pagina voor Azure stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Volgende stappen

* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslag informatie voor de query taal Azure Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference)
* [Naslag informatie over Azure Stream Analytics beheer REST API](/rest/api/streamanalytics/)
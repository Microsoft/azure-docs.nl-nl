---
title: Hoogwaardige handel met Azure Stream Analytics
description: Lineaire-regressiemodeltrainingen en scores uitvoeren als onderdeel van een Azure Stream Analytics-taak.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/16/2021
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: f632c916c3de61b94532e96be23da511ad5863ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104593030"
---
# <a name="high-frequency-trading-simulation-with-stream-analytics"></a>Simulatie van high-frequency trading met Stream Analytics
Met een combinatie van SQL-taal en UDF's (door de gebruiker gedefinieerde functies) en UDA's (door de gebruiker gedefinieerde aggregaties) van JavaScript in Azure Stream Analytics kunnen gebruikers geavanceerde analyses uitvoeren. Geavanceerde analyses omvatten mogelijk onder andere onlinetraining en -scoring voor Machine Learning, evenals simulatie van het stateful-proces. In dit artikel wordt beschreven hoe u lineaire regressie kunt uitvoeren in een Azure Stream Analytics-taak met continue training en scoring in een high-frequency trading-scenario.

## <a name="high-frequency-trading"></a>High-frequency trading
De logische stroom van high-frequency trading gaat over:
1. Koersen in realtime ophalen bij een effectenbeurs.
2. Een voorspellend model rond de koersen bouwen, zodat we de prijsstijgingen en -dalingen kunnen voorspellen.
3. Koop- of verkooporders plaatsen om geld te verdienen met de succesvolle voorspelling van de prijsstijgingen en -dalingen. 

Op basis hiervan hebben we het volgende nodig:
* Een koersfeed in realtime.
* Een voorspellend model voor de koersen in realtime.
* Een tradingsimulatie die de winst of het verlies toont, gemaakt op basis van het tradingalgoritme.

### <a name="real-time-quote-feed"></a>Koersfeed in realtime
IEX biedt gratis [realtime-koersen voor vraag en aanbod](https://iextrading.com/developer/docs/#websockets) met behulp van socket.io. Er kan een eenvoudig consoleprogramma worden geschreven voor het ontvangen van koersen in realtime en als gegevensbron worden gepusht naar Azure Event Hub. De volgende code is het geraamte van het programma. In de code is foutafhandeling weggelaten om het beknopt te houden. U moet ook NuGet-pakketten van SocketIoClientDotNet en WindowsAzure.ServiceBus opnemen in uw project.

```csharp
using Quobject.SocketIoClientDotNet.Client;
using Microsoft.ServiceBus.Messaging;
var symbols = "msft,fb,amzn,goog";
var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
var socket = IO.Socket("https://ws-api.iextrading.com/1.0/tops");
socket.On(Socket.EVENT_MESSAGE, (message) =>
{
    eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes((string)message)));
});
socket.On(Socket.EVENT_CONNECT, () =>
{
    socket.Emit("subscribe", symbols);
});
```

Hier volgt een aantal gegenereerde voorbeeldgebeurtenissen:

```json
{"symbol":"MSFT","marketPercent":0.03246,"bidSize":100,"bidPrice":74.8,"askSize":300,"askPrice":74.83,volume":70572,"lastSalePrice":74.825,"lastSaleSize":100,"lastSaleTime":1506953355123,lastUpdated":1506953357170,"sector":"softwareservices","securityType":"commonstock"}
{"symbol":"GOOG","marketPercent":0.04825,"bidSize":114,"bidPrice":870,"askSize":0,"askPrice":0,volume":11240,"lastSalePrice":959.47,"lastSaleSize":60,"lastSaleTime":1506953317571,lastUpdated":1506953357633,"sector":"softwareservices","securityType":"commonstock"}
{"symbol":"MSFT","marketPercent":0.03244,"bidSize":100,"bidPrice":74.8,"askSize":100,"askPrice":74.83,volume":70572,"lastSalePrice":74.825,"lastSaleSize":100,"lastSaleTime":1506953355123,lastUpdated":1506953359118,"sector":"softwareservices","securityType":"commonstock"}
{"symbol":"FB","marketPercent":0.01211,"bidSize":100,"bidPrice":169.9,"askSize":100,"askPrice":170.67,volume":39042,"lastSalePrice":170.67,"lastSaleSize":100,"lastSaleTime":1506953351912,lastUpdated":1506953359641,"sector":"softwareservices","securityType":"commonstock"}
{"symbol":"GOOG","marketPercent":0.04795,"bidSize":100,"bidPrice":959.19,"askSize":0,"askPrice":0,volume":11240,"lastSalePrice":959.47,"lastSaleSize":60,"lastSaleTime":1506953317571,lastUpdated":1506953360949,"sector":"softwareservices","securityType":"commonstock"}
{"symbol":"FB","marketPercent":0.0121,"bidSize":100,"bidPrice":169.9,"askSize":100,"askPrice":170.7,volume":39042,"lastSalePrice":170.67,"lastSaleSize":100,"lastSaleTime":1506953351912,lastUpdated":1506953362205,"sector":"softwareservices","securityType":"commonstock"}
{"symbol":"GOOG","marketPercent":0.04795,"bidSize":114,"bidPrice":870,"askSize":0,"askPrice":0,volume":11240,"lastSalePrice":959.47,"lastSaleSize":60,"lastSaleTime":1506953317571,lastUpdated":1506953362629,"sector":"softwareservices","securityType":"commonstock"}
```

>[!NOTE]
>De tijdstempel van de gebeurtenis is **lastUpdated**, in tijdvak.

### <a name="predictive-model-for-high-frequency-trading"></a>Voorspellend model voor high-frequency trading
Ter demonstratie gebruiken we een lineair model dat door Darryl Shen in [zijn verhandeling](https://docplayer.net/23038840-Order-imbalance-based-strategy-in-high-frequency-trading.html) wordt beschreven.

VOI (Volume Order Imbalance) is een functie van de huidige prijs en het huidige volume voor vraag/aanbod, en de prijs en het volume voor vraag/aanbod sinds de laatste tick. In de verhandeling wordt de correlatie tussen VOI en toekomstige prijsstijgingen en -dalingen geïdentificeerd. Er wordt een lineair model gebouwd tussen de afgelopen 5 VOI-waarden en de prijswijziging in de volgende 10 ticks. Het model wordt getraind door lineaire regressie te gebruiken voor de gegevens van de vorige dag. 

Het getrainde model wordt vervolgens gebruikt om in realtime koersen te voorspellen op de huidige handelsdag. Als er een prijswijziging wordt voorspeld die groot genoeg is, wordt er een aandelentransactie uitgevoerd. Afhankelijk van de instelling voor de drempelwaarde, kunnen voor een enkel aandeel tijdens een handelsdag duizenden transacties worden verwacht.

![Definitie van onevenwichtige volume volgorde](./media/stream-analytics-high-frequency-trading/volume-order-imbalance-formula.png)

Laten we nu eens de trainings- en voorspellingsbewerkingen uitdrukken die tijdens een Azure Stream Analytics-taak plaatshebben.

Eerst wordt de invoer opgeschoond. Tijdvak wordt geconverteerd naar datum en tijd via **DATEADD**. **TRY_CAST** wordt gebruikt om gegevens af te dwingen zonder dat de query mislukt. Het is altijd een goed idee om invoervelden naar de verwachte uitvoervelden te casten, zodat zich geen onverwacht gedrag voordoet als de velden worden gemanipuleerd of met elkaar worden vergeleken.

```SQL
WITH
typeconvertedquotes AS (
    /* convert all input fields to proper types */
    SELECT
        System.Timestamp AS lastUpdated,
        symbol,
        DATEADD(millisecond, CAST(lastSaleTime as bigint), '1970-01-01T00:00:00Z') AS lastSaleTime,
        TRY_CAST(bidSize as bigint) AS bidSize,
        TRY_CAST(bidPrice as float) AS bidPrice,
        TRY_CAST(askSize as bigint) AS askSize,
        TRY_CAST(askPrice as float) AS askPrice,
        TRY_CAST(volume as bigint) AS volume,
        TRY_CAST(lastSaleSize as bigint) AS lastSaleSize,
        TRY_CAST(lastSalePrice as float) AS lastSalePrice
    FROM quotes TIMESTAMP BY DATEADD(millisecond, CAST(lastUpdated as bigint), '1970-01-01T00:00:00Z')
),
timefilteredquotes AS (
    /* filter between 7am and 1pm PST, 14:00 to 20:00 UTC */
    /* clean up invalid data points */
    SELECT * FROM typeconvertedquotes
    WHERE DATEPART(hour, lastUpdated) >= 14 AND DATEPART(hour, lastUpdated) < 20 AND bidSize > 0 AND askSize > 0 AND bidPrice > 0 AND askPrice > 0
),
```

Vervolgens wordt de functie **LAG** gebruikt om waarden van de laatste tick te verkrijgen. Eén uur van de waarde **LIMIT DURATION** wordt willekeurig gekozen. Door de frequentie waarmee koersen worden gegeven, kunt u er gerust van uitgaan dat u de vorige tick vindt door één uur terug te gaan.  

```SQL
shiftedquotes AS (
    /* get previous bid/ask price and size in order to calculate VOI */
    SELECT
        symbol,
        (bidPrice + askPrice)/2 AS midPrice,
        bidPrice,
        bidSize,
        askPrice,
        askSize,
        LAG(bidPrice) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS bidPricePrev,
        LAG(bidSize) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS bidSizePrev,
        LAG(askPrice) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS askPricePrev,
        LAG(askSize) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS askSizePrev
    FROM timefilteredquotes
),
```

Dan kan de VOI-waarde worden berekend. Voor alle zekerheid worden de null-waarden eruit gefilterd als de vorige tick niet bestaat.

```SQL
currentPriceAndVOI AS (
    /* calculate VOI */
    SELECT
        symbol,
        midPrice,
        (CASE WHEN (bidPrice < bidPricePrev) THEN 0
            ELSE (CASE WHEN (bidPrice = bidPricePrev) THEN (bidSize - bidSizePrev) ELSE bidSize END)
         END) -
        (CASE WHEN (askPrice < askPricePrev) THEN askSize
            ELSE (CASE WHEN (askPrice = askPricePrev) THEN (askSize - askSizePrev) ELSE 0 END)
         END) AS VOI
    FROM shiftedquotes
    WHERE
        bidPrice IS NOT NULL AND
        bidSize IS NOT NULL AND
        askPrice IS NOT NULL AND
        askSize IS NOT NULL AND
        bidPricePrev IS NOT NULL AND
        bidSizePrev IS NOT NULL AND
        askPricePrev IS NOT NULL AND
        askSizePrev IS NOT NULL
),
```

We gaan nu **LAG** weer gebruiken om een reeks met 2 opeenvolgende VOI waarden te maken, gevolgd door 10 opeenvolgende gemiddelde prijswaarden.

```SQL
shiftedPriceAndShiftedVOI AS (
    /* get 10 future prices and 2 previous VOIs */
    SELECT
        symbol,
        midPrice AS midPrice10,
        LAG(midPrice, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice9,
        LAG(midPrice, 2) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice8,
        LAG(midPrice, 3) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice7,
        LAG(midPrice, 4) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice6,
        LAG(midPrice, 5) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice5,
        LAG(midPrice, 6) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice4,
        LAG(midPrice, 7) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice3,
        LAG(midPrice, 8) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice2,
        LAG(midPrice, 9) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice1,
        LAG(midPrice, 10) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice,
        LAG(VOI, 10) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS VOI1,
        LAG(VOI, 11) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS VOI2
    FROM currentPriceAndVOI
),
```

We zetten de gegevens vervolgens om in invoer voor een lineair model met twee variabelen. Opnieuw worden de gebeurtenissen eruit gefilterd waarvan we niet alle gegevens hebben.

```SQL
modelInput AS (
    /* create feature vector, x being VOI, y being delta price */
    SELECT
        symbol,
        (midPrice1 + midPrice2 + midPrice3 + midPrice4 + midPrice5 + midPrice6 + midPrice7 + midPrice8 + midPrice9 + midPrice10)/10.0 - midPrice AS y,
        VOI1 AS x1,
        VOI2 AS x2
    FROM shiftedPriceAndShiftedVOI
    WHERE
        midPrice1 IS NOT NULL AND
        midPrice2 IS NOT NULL AND
        midPrice3 IS NOT NULL AND
        midPrice4 IS NOT NULL AND
        midPrice5 IS NOT NULL AND
        midPrice6 IS NOT NULL AND
        midPrice7 IS NOT NULL AND
        midPrice8 IS NOT NULL AND
        midPrice9 IS NOT NULL AND
        midPrice10 IS NOT NULL AND
        midPrice IS NOT NULL AND
        VOI1 IS NOT NULL AND
        VOI2 IS NOT NULL
),
```

Omdat Azure Stream Analytics niet beschikt over een ingebouwde lineaire-regressiefunctie, worden de gegevens die met behulp van **SUM** en **AVG** zijn verzameld, gebruikt voor het berekenen van de coëfficiënten voor het lineaire model.

![Mathematische formule voor lineaire regressie](./media/stream-analytics-high-frequency-trading/linear-regression-formula.png)

```SQL
modelagg AS (
    /* get aggregates for linear regression calculation,
     http://faculty.cas.usf.edu/mbrannick/regression/Reg2IV.html */
    SELECT
        symbol,
        SUM(x1 * x1) AS x1x1,
        SUM(x2 * x2) AS x2x2,
        SUM(x1 * y) AS x1y,
        SUM(x2 * y) AS x2y,
        SUM(x1 * x2) AS x1x2,
        AVG(y) AS avgy,
        AVG(x1) AS avgx1,
        AVG(x2) AS avgx2
    FROM modelInput
    GROUP BY symbol, TumblingWindow(hour, 24, -4)
),
modelparambs AS (
    /* calculate b1 and b2 for the linear model */
    SELECT
        symbol,
        (x2x2 * x1y - x1x2 * x2y)/(x1x1 * x2x2 - x1x2 * x1x2) AS b1,
        (x1x1 * x2y - x1x2 * x1y)/(x1x1 * x2x2 - x1x2 * x1x2) AS b2,
        avgy,
        avgx1,
        avgx2
    FROM modelagg
),
model AS (
    /* calculate a for the linear model */
    SELECT
        symbol,
        avgy - b1 * avgx1 - b2 * avgx2 AS a,
        b1,
        b2
    FROM modelparambs
),
```

Als we het model van de vorige dag willen gebruiken voor het verzamelen van scores voor de huidige gebeurtenis, moeten we de koersen samenvoegen met het model. Maar in plaats van **JOIN** gebruiken we **UNION** voor de modelgebeurtenissen en koersgebeurtenissen. Vervolgens gebruiken we **LAG** om de gebeurtenissen te koppelen met het model van de vorige dag, zodat er precies één overeenkomst wordt opgehaald. Vanwege het weekend moeten we drie dagen terugkijken. Als we een eenvoudige **JOIN** hebben gebruikt, worden er voor elke koersgebeurtenis drie modellen opgehaald.

```SQL
shiftedVOI AS (
    /* get two consecutive VOIs */
    SELECT
        symbol,
        midPrice,
        VOI AS VOI1,        
        LAG(VOI, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS VOI2
    FROM currentPriceAndVOI
),
VOIAndModel AS (
    /* combine VOIs and models */
    SELECT
        'voi' AS type,
        symbol,
        midPrice,
        VOI1,
        VOI2,
        0.0 AS a,
        0.0 AS b1,
        0.0 AS b2
    FROM shiftedVOI
    UNION
    SELECT
        'model' AS type,
        symbol,
        0.0 AS midPrice,
        0 AS VOI1,
        0 AS VOI2,
        a,
        b1,
        b2
    FROM model
),
VOIANDModelJoined AS (
    /* match VOIs with the latest model within 3 days (72 hours, to take the weekend into account) */
    SELECT
        symbol,
        midPrice,
        VOI1 as x1,
        VOI2 as x2,
        LAG(a, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 72) WHEN type = 'model') AS a,
        LAG(b1, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 72) WHEN type = 'model') AS b1,
        LAG(b2, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 72) WHEN type = 'model') AS b2
    FROM VOIAndModel
    WHERE type = 'voi'
),
```

Nu kunnen we voorspellingen maken en koop-/verkoopsignalen genereren op basis van het model. We gebruiken hierbij een drempelwaarde van 0,02. Een handelswaarde van 10 is kopen. Een handelswaarde van -10 is verkopen.

``` SQL
prediction AS (
    /* make prediction if there is a model */
    SELECT
        symbol,
        midPrice,
        a + b1 * x1 + b2 * x2 AS efpc
    FROM VOIANDModelJoined
    WHERE
        a IS NOT NULL AND
        b1 IS NOT NULL AND
        b2 IS NOT NULL AND
        x1 IS NOT NULL AND
        x2 IS NOT NULL
),
tradeSignal AS (
    /* generate buy/sell signals */
    SELECT
        DateAdd(hour, -7, System.Timestamp) AS time,
        symbol,     
        midPrice,
        efpc,
        CASE WHEN (efpc > 0.02) THEN 10 ELSE (CASE WHEN (efpc < -0.02) THEN -10 ELSE 0 END) END AS trade,
        DATETIMEFROMPARTS(DATEPART(year, System.Timestamp), DATEPART(month, System.Timestamp), DATEPART(day, System.Timestamp), 0, 0, 0, 0) as date
    FROM prediction
),
```

### <a name="trading-simulation"></a>Simulatie van trading
Nu we over de tradingsignalen beschikken, gaan we testen hoe effectief de tradingstrategie is zonder echt iets verhandelen. 

We doen deze test met behulp van een UDA, met een hoppingvenster, waarbij elke minuut hopping plaatsvindt. Het aanvullende groeperen op datum en de HAVING-component staan dit venster alleen toe voor accounts voor gebeurtenissen die deel uitmaken van dezelfde dag. Als u wilt dat een hoppingvenster in twee richtingen werkt, wordt de groep met **GROUP BY**-datum gescheiden in een groep voor de vorige en een groep voor de huidige dag. Met de **HAVING**-component worden de vensters eruit gefilterd die op de huidige dag eindigen, maar doet dat bij groepen op de vorige dag.

```SQL
simulation AS
(
    /* perform trade simulation for the past 7 hours to cover an entire trading day, and generate output every minute */
    SELECT
        DateAdd(hour, -7, System.Timestamp) AS time,
        symbol,
        date,
        uda.TradeSimulation(tradeSignal) AS s
    FROM tradeSignal
    GROUP BY HoppingWindow(minute, 420, 1), symbol, date
    Having DateDiff(day, date, time) < 1 AND DATEPART(hour, time) < 13
)
```

De JavaScript-UDA initialiseert alle accumulators in de `init`-functie, berekent de statusovergang met elke gebeurtenis die aan het venster wordt toegevoegd, en retourneert de simulatieresultaten aan het einde van het venster. Het algemene handelsproces verloopt als volgt:

- Aandelen kopen wanneer er een koopsignaal wordt ontvangen en er geen aandelen zijn vastgezet.
- Aandelen verkopen wanneer er een verkoopsignaal wordt ontvangen en er aandelen zijn vastgezet.
- Short-gaan als er geen aandelen zijn vastgezet. 

Als zich een short-positie voordoet en er wordt een koopsignaal ontvangen, kopen we met het oog op dekking. In deze simulatie hebben we 10 aandelen van een aandeel. De transactiekosten zijn $ 8.

```javascript
function main() {
    var TRADE_COST = 8.0;
    var SHARES = 10;
    this.init = function () {
        this.own = false;
        this.pos = 0;
        this.pnl = 0.0;
        this.tradeCosts = 0.0;
        this.buyPrice = 0.0;
        this.sellPrice = 0.0;
        this.buySize = 0;
        this.sellSize = 0;
        this.buyTotal = 0.0;
        this.sellTotal = 0.0;
    }
    this.accumulate = function (tradeSignal, timestamp) {
        if(!this.own && tradeSignal.trade == 10) {
          // Buy to open
          this.own = true;
          this.pos = 1;
          this.buyPrice = tradeSignal.midprice;
          this.tradeCosts += TRADE_COST;
          this.buySize += SHARES;
          this.buyTotal += SHARES * tradeSignal.midprice;
        } else if(!this.own && tradeSignal.trade == -10) {
          // Sell to open
          this.own = true;
          this.pos = -1
          this.sellPrice = tradeSignal.midprice;
          this.tradeCosts += TRADE_COST;
          this.sellSize += SHARES;
          this.sellTotal += SHARES * tradeSignal.midprice;
        } else if(this.own && this.pos == 1 && tradeSignal.trade == -10) {
          // Sell to close
          this.own = false;
          this.pos = 0;
          this.sellPrice = tradeSignal.midprice;
          this.tradeCosts += TRADE_COST;
          this.pnl += (this.sellPrice - this.buyPrice)*SHARES - 2*TRADE_COST;
          this.sellSize += SHARES;
          this.sellTotal += SHARES * tradeSignal.midprice;
          // Sell to open
          this.own = true;
          this.pos = -1;
          this.sellPrice = tradeSignal.midprice;
          this.tradeCosts += TRADE_COST;
          this.sellSize += SHARES;        
          this.sellTotal += SHARES * tradeSignal.midprice;
        } else if(this.own && this.pos == -1 && tradeSignal.trade == 10) {
          // Buy to close
          this.own = false;
          this.pos = 0;
          this.buyPrice = tradeSignal.midprice;
          this.tradeCosts += TRADE_COST;
          this.pnl += (this.sellPrice - this.buyPrice)*SHARES - 2*TRADE_COST;
          this.buySize += SHARES;
          this.buyTotal += SHARES * tradeSignal.midprice;
          // Buy to open
          this.own = true;
          this.pos = 1;
          this.buyPrice = tradeSignal.midprice;
          this.tradeCosts += TRADE_COST;
          this.buySize += SHARES;         
          this.buyTotal += SHARES * tradeSignal.midprice;
        }
    }
    this.computeResult = function () {
        var result = {
            "pnl": this.pnl,
            "buySize": this.buySize,
            "sellSize": this.sellSize,
            "buyTotal": this.buyTotal,
            "sellTotal": this.sellTotal,
            "tradeCost": this.tradeCost
            };
        return result;
    }
}
```

En tot slot zenden we uitvoer naar het Power BI-dashboard ter visualisatie.

```SQL
SELECT * INTO tradeSignalDashboard FROM tradeSignal /* output tradeSignal to PBI */
SELECT
    symbol,
    time,
    date,
    TRY_CAST(s.pnl as float) AS pnl,
    TRY_CAST(s.buySize as bigint) AS buySize,
    TRY_CAST(s.sellSize as bigint) AS sellSize,
    TRY_CAST(s.buyTotal as float) AS buyTotal,
    TRY_CAST(s.sellTotal as float) AS sellTotal
    INTO pnlDashboard
FROM simulation /* output trade simulation to PBI */
```

![Visuele elementen Power BI grafiek](./media/stream-analytics-high-frequency-trading/trades-power-bi-chart.png)

![Visual Power BI grafiek van PNL](./media/stream-analytics-high-frequency-trading/pnl-power-bi-chart.png)


## <a name="summary"></a>Samenvatting
We kunnen in Azure Stream Analytics een realistisch model voor high-frequency trading implementeren met een niet al te complexe query. Doordat een ingebouwde lineaire-regressiefunctie ontbreekt, moeten we het model vereenvoudigen door in plaats van vijf invoervariabelen twee invoervariabelen te gebruiken. Voor een vastberaden gebruiker kunnen algoritmen met meer dimensies en een grotere complexiteit echter mogelijk ook als JavaScript-UDA worden geïmplementeerd. 

Met uitzondering van de JavaScript-UDA kan het grootste deel van de query worden getest en in Visual Studio aan foutopsporing worden onderworpen via het [Azure Stream Analytics-hulpprogramma voor Visual Studio](stream-analytics-tools-for-visual-studio-install.md). Nadat de eerste query was geschreven, had de auteur minder dan 30 minuten nodig om met Visual Studio de query te testen en fouten in de query op te sporen. 

Momenteel kunnen er met Visual Studio geen fouten in de UDA worden opgespoord. We werken eraan om dit mogelijk te maken, en willen dan tevens de mogelijkheid toevoegen om stapsgewijs door JavaScript-code heen te lopen. Houd er bovendien rekening mee dat de namen van de velden die de UDA bereiken, uit kleine letters bestaan. Dit werd niet als kenmerkend gedrag gezien tijdens het testen van query's. Maar met compatibiliteitsniveau 1.1 van Azure Stream Analytics blijft het hoofdlettergebruik van veldnamen behouden, zodat het gedrag natuurlijker is.

Ik hoop dat dit artikel dient als inspiratie voor alle gebruikers van Azure Stream Analytics, die onze service onafgebroken kunnen gebruiken om geavanceerde analyses in nagenoeg realtime uit te voeren. Stuur ons uw feedback, zodat we het implementeren van query's voor scenario's met geavanceerde analyses kunnen vereenvoudigen.

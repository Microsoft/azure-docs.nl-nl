---
title: Tijds intervallen en toerekeningen van ontbrekende waarden-Azure SQL Edge
description: Meer informatie over het invullen van tijds hiaten en het toezeggen van ontbrekende waarden in Azure SQL Edge
keywords: SQL-Edge, tijds Erie
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: c444732a497d595235ac337d7f5c71fb84f17cca
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185586"
---
# <a name="filling-time-gaps-and-imputing-missing-values"></a>De tijds intervallen voor het invullen en de ontbrekende waarden worden toegerekend 

Bij het verwerken van Time Series-gegevens is het vaak mogelijk dat er voor de time series-gegevens waarden voor de kenmerken ontbreken. Het is ook mogelijk dat, vanwege de aard van de gegevens of vanwege onderbrekingen in het verzamelen van gegevens, er sprake is van tijds *hiaten* in de gegevensset.

Als er bijvoorbeeld statistieken voor energie gebruik voor een Smart Device worden verzameld, zijn er hiaten in de gebruiks statistieken wanneer het apparaat niet operationeel is. In het geval van een scenario voor het verzamelen van telemetrie-gegevens is het mogelijk dat de verschillende Sens oren zijn geconfigureerd voor het verzenden van gegevens met verschillende frequenties, wat resulteert in ontbrekende waarden voor de Sens oren. Als er bijvoorbeeld twee Sens oren, spanning en druk, zijn geconfigureerd op respectievelijk 100 Hz en een frequentie van 10 Hz, stuurt de spannings sensor gegevens elke honderdste van een seconde, terwijl de druk sensor alleen op elke tiende van een seconde gegevens gaat verzenden.

In de volgende tabel wordt een gegevensset van de machine-telemetrie beschreven, die is verzameld met een interval van één seconde. 

```
timestamp               VoltageReading  PressureReading
----------------------- --------------- ----------------
2020-09-07 06:14:41.000 164.990400      97.223600
2020-09-07 06:14:42.000 162.241300      93.992800
2020-09-07 06:14:43.000 163.271200      NULL
2020-09-07 06:14:44.000 161.368100      93.403700
2020-09-07 06:14:45.000 NULL            NULL
2020-09-07 06:14:46.000 NULL            98.364800
2020-09-07 06:14:49.000 NULL            94.098300
2020-09-07 06:14:51.000 157.695700      103.359100
2020-09-07 06:14:52.000 157.019200      NULL
2020-09-07 06:14:54.000 NULL            95.352000
2020-09-07 06:14:56.000 159.183500      100.748200

```

Er zijn twee belang rijke kenmerken van de voor gaande gegevensset. 

- De gegevensset bevat geen gegevens punten die betrekking hebben op verschillende tijds tempels,,, en `2020-09-07 06:14:47.000` `2020-09-07 06:14:48.000` `2020-09-07 06:14:50.000` `2020-09-07 06:14:53.000` `2020-09-07 06:14:55.000` . Deze tijds tempels zijn *hiaten* in de gegevensset.  
- Er ontbreken waarden die worden weer gegeven als `null` voor de spannings-en druk metingen. 

## <a name="gap-filling"></a>Tussen ruimte opvullen 

Het opvullen van hiaten is een techniek waarmee u aaneengesloten, geordende set Time Stamps kunt maken om de analyse van tijdreeks gegevens te vereenvoudigen. In Azure SQL Edge is de eenvoudigste manier om hiaten in de gegevensset voor time series te vullen, het definiëren van een tijdelijke tabel met de gewenste distributie tijd en vervolgens een `LEFT OUTER JOIN` of een bewerking uit te voeren `RIGHT OUTER JOIN` in de tabel gegevensset. 

De `MachineTelemetry` volgende query kan worden gebruikt voor het genereren van aaneengesloten, geordende set Time Stamps voor analyse, waarbij de gegevens hierboven worden weer gegeven. 

> [!NOTE]
> Met de onderstaande query worden de ontbrekende rijen gegenereerd, met de tijds tempel waarden en `null` waarden voor de kenmerken. 

```sql
Create Table #SeriesGenerate(dt datetime Primary key Clustered)
GO

Declare @startdate datetime = '2020-09-07 06:14:41.000', @endtime datetime = '2020-09-07 06:14:56.000'
While (@startdate <= @endtime)
BEGIN
Insert into #SeriesGenerate values (@startdate)
set @startdate = DATEADD(SECOND, 1, @startdate)
END

Select a.dt as timestamp, b.VoltageReading, b.PressureReading 
From 
#SeriesGenerate a LEFT OUTER JOIN MachineTelemetry b 
    on a.dt = b.[timestamp]
```
De bovenstaande query produceert de volgende uitvoer met alle tijds tempels van *één seconde* in het opgegeven bereik.

Dit is de resultatenset

```

timestamp               VoltageReading    PressureReading
----------------------- ----------------- ----------------
2020-09-07 06:14:41.000 164.990400        97.223600
2020-09-07 06:14:42.000 162.241300        93.992800
2020-09-07 06:14:43.000 163.271200        NULL
2020-09-07 06:14:44.000 161.368100        93.403700
2020-09-07 06:14:45.000 NULL              NULL
2020-09-07 06:14:46.000 NULL              98.364800
2020-09-07 06:14:47.000 NULL              NULL
2020-09-07 06:14:48.000 NULL              NULL
2020-09-07 06:14:49.000 NULL              94.098300
2020-09-07 06:14:50.000 NULL              NULL
2020-09-07 06:14:51.000 157.695700        103.359100
2020-09-07 06:14:52.000 157.019200        NULL
2020-09-07 06:14:53.000 NULL              NULL
2020-09-07 06:14:54.000 NULL              95.352000
2020-09-07 06:14:55.000 NULL              NULL
2020-09-07 06:14:56.000 159.183500        100.748200
```

## <a name="imputing-missing-values"></a>Ontbrekende waarden invoeren

De voor gaande query heeft de ontbrekende tijds tempels gegenereerd voor gegevens analyse, maar heeft geen van de ontbrekende waarden vervangen (weer gegeven als null) voor `voltage` en `pressure` leesingen. In Azure SQL Edge is een nieuwe syntaxis toegevoegd aan de T-SQL `LAST_VALUE()` en `FIRST_VALUE()` functions, die mechanismen bieden voor het overtreden van ontbrekende waarden, op basis van de voor gaande of volgende waarden in de gegevensset. 

De nieuwe syntaxis voegt `IGNORE NULLS` de `RESPECT NULLS` componenten en toe aan de `LAST_VALUE()` `FIRST_VALUE()` functies en. Met de volgende query in de `MachineTelemetry` gegevensset worden de ontbrekende waarden berekend met behulp van de functie last_value, waarbij ontbrekende waarden worden vervangen door de laatst waargenomen waarde in de gegevensset.

```sql
Select 
    timestamp,
    VoltageReading As OriginalVoltageValues,
    LAST_VALUE(VoltageReading) IGNORE NULLS OVER (ORDER BY timestamp) As ImputedUsingLastValue, 
    PressureReading As OriginalPressureValues,
    LAST_VALUE(PressureReading) IGNORE NULLS OVER (ORDER BY timestamp) As ImputedUsingLastValue
From 
MachineTelemetry 
order by timestamp 
```
Dit is de resultatenset

```

timestamp               OrigVoltageVals  ImputedVoltage OrigPressureVals  ImputedPressure
----------------------- ---------------- -------------- ----------------- ----------------
2020-09-07 06:14:41.000 164.990400       164.990400     97.223600         97.223600
2020-09-07 06:14:42.000 162.241300       162.241300     93.992800         93.992800
2020-09-07 06:14:43.000 163.271200       163.271200     NULL              93.992800
2020-09-07 06:14:44.000 161.368100       161.368100     93.403700         93.403700
2020-09-07 06:14:45.000 NULL             161.368100     NULL              93.403700
2020-09-07 06:14:46.000 NULL             161.368100     98.364800         98.364800
2020-09-07 06:14:49.000 NULL             161.368100     94.098300         94.098300
2020-09-07 06:14:51.000 157.695700       157.695700     103.359100        103.359100
2020-09-07 06:14:52.000 157.019200       157.019200     NULL              103.359100
2020-09-07 06:14:54.000 NULL             157.019200     95.352000         95.352000
2020-09-07 06:14:56.000 159.183500       159.183500     100.748200        100.748200

```

Met de volgende query worden de ontbrekende waarden toegerekend met behulp van de- `LAST_VALUE()` en- `FIRST_VALUE` functie. Voor wordt in de uitvoer kolom `ImputedVoltage` de ontbrekende waarden vervangen door de laatst waargenomen waarde, terwijl voor de uitvoer kolom `ImputedPressure` de ontbrekende waarden worden vervangen door de volgende waargenomen waarde in de gegevensset. 

```sql
Select 
    dt as timestamp, 
    VoltageReading As OrigVoltageVals,
    LAST_VALUE(VoltageReading) IGNORE NULLS OVER (ORDER BY dt) As ImputedVoltage, 
    PressureReading As OrigPressureVals,
    First_VALUE(PressureReading) IGNORE NULLS OVER (ORDER BY dt ROWS 
                    BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING) As ImputedPressure
From 
(Select a.dt, b.VoltageReading,b.PressureReading  from 
    #SeriesGenerate a 
        LEFT OUTER JOIN 
    MachineTelemetry b 
        on a.dt = b.[timestamp]) A
order by timestamp
```
Dit is de resultatenset

```

timestamp               OrigVoltageVals  ImputedVoltage  OrigPressureVals  ImputedPressure
----------------------- ---------------- --------------- ----------------- ---------------
2020-09-07 06:14:41.000 164.990400       164.990400      97.223600         97.223600
2020-09-07 06:14:42.000 162.241300       162.241300      93.992800         93.992800
2020-09-07 06:14:43.000 163.271200       163.271200      NULL              93.403700
2020-09-07 06:14:44.000 161.368100       161.368100      93.403700         93.403700
2020-09-07 06:14:45.000 NULL             161.368100      NULL              98.364800
2020-09-07 06:14:46.000 NULL             161.368100      98.364800         98.364800
2020-09-07 06:14:47.000 NULL             161.368100      NULL              94.098300
2020-09-07 06:14:48.000 NULL             161.368100      NULL              94.098300
2020-09-07 06:14:49.000 NULL             161.368100      94.098300         94.098300
2020-09-07 06:14:50.000 NULL             161.368100      NULL              103.359100
2020-09-07 06:14:51.000 157.695700       157.695700      103.359100        103.359100
2020-09-07 06:14:52.000 157.019200       157.019200      NULL              95.352000
2020-09-07 06:14:53.000 NULL             157.019200      NULL              95.352000
2020-09-07 06:14:54.000 NULL             157.019200      95.352000         95.352000
2020-09-07 06:14:55.000 NULL             157.019200      NULL              100.748200
2020-09-07 06:14:56.000 159.183500       159.183500      100.748200        100.748200
```

> [!NOTE]
> De bovenstaande query gebruikt de `FIRST_VALUE()` functie om ontbrekende waarden te vervangen door de volgende waargenomen waarde. U kunt hetzelfde resultaat bereiken met behulp van de `LAST_VALUE()` functie met een- `ORDER BY <ordering_column> DESC` component.

## <a name="next-steps"></a>Volgende stappen 

- [FIRST_VALUE (Transact-SQL)](/sql/t-sql/functions/first-value-transact-sql?toc=%2fazure%2fazure-sql-edge%2ftoc.json)
- [LAST_VALUE (Transact-SQL)](/sql/t-sql/functions/last-value-transact-sql?toc=%2fazure%2fazure-sql-edge%2ftoc.json)
- [DATE_BUCKET (Transact-SQL)](date-bucket-tsql.md)
- [Statistische functies (Transact-SQL)](/sql/t-sql/functions/aggregate-functions-transact-sql)
---
title: Door de gebruiker gedefinieerde functies in Azure Stream Analytics
description: Dit artikel bevat een overzicht van door de gebruiker gedefinieerde functies in Azure Stream Analytics.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: c671d3989fa46fa7546ba042b9132e19d80265a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020499"
---
# <a name="user-defined-functions-in-azure-stream-analytics"></a>Door de gebruiker gedefinieerde functies in Azure Stream Analytics

Met de SQL-achtige query taal in Azure Stream Analytics kunt u eenvoudig real-time analyse logica implementeren op streaminggegevens. Stream Analytics biedt extra flexibiliteit via aangepaste functies die worden aangeroepen in uw query. Het volgende code voorbeeld is een UDF `sampleFunction` -naam die één para meter accepteert, elke invoer record die de taak ontvangt, en het resultaat wordt naar de uitvoer geschreven als `sampleResult` .

```sql
SELECT 
    UDF.sampleFunction(InputStream) AS sampleResult 
INTO 
    output 
FROM 
    InputStream 
```

## <a name="types-of-functions"></a>Typen functies

Azure Stream Analytics ondersteunt de volgende vier functie typen: 

* Door de gebruiker gedefinieerde JavaScript-functies 
* Door de gebruiker gedefinieerde Java script-samen stellingen 
* Door de gebruiker gedefinieerde C#-functies (met Visual Studio) 
* Azure Machine Learning 

U kunt deze functies gebruiken voor scenario's zoals realtime scores met behulp van machine learning modellen, teken reeks bewerkingen, complexe wiskundige berekeningen, gegevens coderen en decoderen. 

## <a name="limitations"></a>Beperkingen

Door de gebruiker gedefinieerde functies zijn stateless en de geretourneerde waarde kan alleen een scalaire waarde zijn. U kunt geen externe REST-eind punten aanroepen vanuit deze door de gebruiker gedefinieerde functies, omdat dit waarschijnlijk invloed heeft op de prestaties van uw taak. 

Azure Stream Analytics houdt geen registratie bij van alle functies en heeft resultaat opgeleverd. Ter waarborging van de Herhaal baarheid: als u uw taak opnieuw uitvoert vanuit oudere tijds tempel, worden dezelfde resultaten opnieuw weer gegeven. u kunt geen functies zoals `Date.GetData()` of gebruiken `Math.random()` , omdat deze functies niet hetzelfde resultaat voor elke aanroep retour neren.  

## <a name="resource-logs"></a>Resourcelogboeken

Runtime fouten worden als onherstelbaar beschouwd en worden opgehaald via activiteiten-en bron Logboeken. U wordt aangeraden alle uitzonde ringen en fouten te verwerken en een geldig resultaat te retour neren naar uw query. Hiermee wordt voor komen dat uw taak naar een [mislukte status](job-states.md)gaat.  

## <a name="exception-handling"></a>Afhandeling van uitzonderingen

Elke uitzonde ring tijdens het verwerken van gegevens wordt beschouwd als een onherstelbare fout bij het gebruiken van gegevens in Azure Stream Analytics. Door de gebruiker gedefinieerde functies hebben een grotere kans om uitzonde ringen te genereren en zorgen ervoor dat de verwerking stopt. Als u dit probleem wilt voor komen, gebruikt u een *try-catch-* blok in Java script of C# om uitzonde ringen te ondervangen tijdens het uitvoeren van code. Uitzonde ringen die zijn gevangen, kunnen worden geregistreerd en verwerkt zonder dat er een systeem fout optreedt. U wordt geadviseerd om uw aangepaste code altijd in een *try-catch-* blok te laten teruglopen om te voor komen dat er onverwachte uitzonde ringen op de verwerkings engine optreden.

## <a name="next-steps"></a>Volgende stappen

* [Door gebruiker gedefinieerde JavaScript-functies in Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
* [Door de gebruiker gedefinieerde Java script-functies Azure Stream Analytics](stream-analytics-javascript-user-defined-aggregates.md)
* [Door de gebruiker gedefinieerde .NET Standard-functies ontwikkelen voor Azure Stream Analytics-taken](stream-analytics-edge-csharp-udf-methods.md)
* [Azure Stream Analytics integreren met Azure Machine Learning](machine-learning-udf.md)

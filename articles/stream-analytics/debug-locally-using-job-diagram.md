---
title: Fouten opsporen Azure Stream Analytics query's lokaal met behulp van taak diagram in Visual Studio
description: In dit artikel wordt beschreven hoe u in Azure Stream Analytics-Hulpprogram Ma's voor Visual Studio fouten opspoort met behulp van het taak diagram.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 01/23/2020
ms.openlocfilehash: d0e94fda1fb21be1a01516f4cecf657426ae867e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019445"
---
# <a name="debug-azure-stream-analytics-queries-locally-using-job-diagram-in-visual-studio"></a>Fouten opsporen Azure Stream Analytics query's lokaal met behulp van taak diagram in Visual Studio

Taken waarvoor geen resultaten of onverwachte resultaten optreden, zijn veelvoorkomende scenario's voor het oplossen van problemen met het streamen van query's. U kunt het taak diagram gebruiken tijdens het lokaal testen van uw query in Visual Studio om de tussenliggende resultatenset en metrische gegevens voor elke stap te controleren. Taak diagrammen kunnen u helpen bij het snel isoleren van de bron van een probleem bij het oplossen van problemen.

## <a name="debug-a-query-using-job-diagram"></a>Fouten opsporen in een query met behulp van taak diagram

Een Azure Stream Analytics script wordt gebruikt om invoer gegevens naar uitvoer gegevens te transformeren. In het taak diagram wordt weer gegeven hoe gegevens stromen van invoer bronnen (Event hub, IoT Hub, enzovoort) via meerdere query stappen en tot slot naar uitvoer-Sinks. Elke query stap wordt toegewezen aan een tijdelijke resultatenset die is gedefinieerd in het script met behulp van een- `WITH` instructie. U kunt de gegevens weer geven, evenals de metrieken van elke query stap in elke tussenliggende resultatenset om de bron van een probleem te vinden.

> [!NOTE]
> In dit taak diagram worden alleen de gegevens en statistieken voor lokale tests in één knoop punt weer gegeven. Het mag niet worden gebruikt voor het afstemmen van de prestaties en het oplossen van problemen.

### <a name="start-local-testing"></a>Lokale tests starten

Gebruik deze [Quick](stream-analytics-quick-create-vs.md) start om te leren hoe u met Visual Studio een stream Analytics taak maakt of [een bestaande taak naar een lokaal project exporteert](stream-analytics-vs-tools.md#export-jobs-to-a-project). Als u de query wilt testen met lokale invoer gegevens, volgt u deze [instructies](stream-analytics-live-data-local-testing.md). Als u met Live-invoer wilt testen, gaat u naar de volgende stap.

> [!NOTE]
> Als u een taak exporteert naar een lokaal project en wilt testen op een live invoer stroom, moet u de referenties voor alle invoer opnieuw opgeven.  

Kies de invoer-en uitvoer bron van de script editor en selecteer **lokaal uitvoeren**. Het taak diagram wordt aan de rechter kant weer gegeven.

### <a name="view-the-intermediate-result-set"></a>De tussenliggende resultatenset weer geven  

1. Selecteer de query stap om naar het script te navigeren. U wordt automatisch omgeleid naar het bijbehorende script in de editor aan de linkerkant.

   ![Taak diagram navigatie script](./media/debug-locally-using-job-diagram/navigate-script.png)

2. Selecteer de query stap en selecteer **voor beeld** in het dialoog venster omhoog. De resultatenset wordt weer gegeven op een tabblad in het onderste resultaat venster.

   ![Resultaat van voor beeld van taak diagram](./media/debug-locally-using-job-diagram/preview-result.png)

### <a name="view-step-metrics"></a>Metrische stap waarden weer geven

In deze sectie bekijkt u de metrische gegevens die beschikbaar zijn voor elk deel van het diagram.

#### <a name="input-sources-live-stream"></a>Invoer bronnen (Live Stream)

![Taak diagram Live-invoer bronnen](./media/debug-locally-using-job-diagram/live-input.png)

|Metrisch|Beschrijving|
|-|-|
|**TaxiRide**| De naam van de invoer.|
|**Event Hub** | Invoer bron type.|
|**Gebeurtenissen**|Het aantal gelezen gebeurtenissen.|
|**Reserve gebeurtenis bronnen**|Hoeveel meer berichten moeten worden gelezen voor Event Hubs en IoT Hub invoer.|
|**Gebeurtenissen in bytes**|Het aantal gelezen bytes.|
| **Gedegradeerde gebeurtenissen**|Het aantal gebeurtenissen waarvoor een probleem is opgetreden, anders dan bij deserialisatie.|
|**Vroege gebeurtenissen**| Het aantal gebeurtenissen met een tijds tempel van de toepassing voor de bovengrens.|
|**Late gebeurtenissen**| Het aantal gebeurtenissen met een tijds tempel van een toepassing na de bovengrens.|
|**Gebeurtenisbronnen**| Het aantal gelezen gegevens eenheden. Bijvoorbeeld het aantal blobs.|

#### <a name="input-sources-local-input"></a>Invoer bronnen (lokale invoer)

![Taak diagram lokale invoer bronnen](./media/debug-locally-using-job-diagram/local-input.png)

|Metrisch|Beschrijving|
|-|-|
|**TaxiRide**| De naam van de invoer.|
|**Aantal rijen**| Het aantal rijen dat is gegenereerd op basis van de stap.|
|**Gegevensgrootte**| De grootte van de gegevens die uit deze stap zijn gegenereerd.|
|**Lokale invoer**| Gebruik lokale gegevens als invoer.|

#### <a name="query-steps"></a>Querystappen

![Query stap taak diagram](./media/debug-locally-using-job-diagram/query-step.png)

|Metrisch|Beschrijving|
|-|-|
|**Trip data**|De naam van de tijdelijke resultatenset.|
|**Aantal rijen**| Het aantal rijen dat is gegenereerd op basis van de stap.|
|**Gegevensgrootte**| De grootte van de gegevens die uit deze stap zijn gegenereerd.|
  
#### <a name="output-sinks-live-output"></a>Uitvoer-Sinks (live uitvoer)

![Taak diagram waarin de lokale uitvoer-sinks worden weer gegeven.](./media/debug-locally-using-job-diagram/live-output.png)

|Metrisch|Beschrijving|
|-|-|
|**regionaggEH**|De naam van de uitvoer.|
|**Gebeurtenissen**|Het aantal gebeurtenissen uitvoer naar Sinks.|

#### <a name="output-sinks-local-output"></a>Uitvoer-Sinks (lokale uitvoer)

![Taak diagram lokale uitvoer-sinks](./media/debug-locally-using-job-diagram/local-output.png)

|Metrisch|Beschrijving|
|-|-|
|**regionaggEH**|De naam van de uitvoer.|
|**Lokale uitvoer**| Uitvoer van resultaten naar een lokaal bestand.|
|**Aantal rijen**| Het aantal rijen uitvoer naar het lokale bestand.|
|**Gegevensgrootte**| De grootte van de gegevens uitvoer naar het lokale bestand.|

### <a name="close-job-diagram"></a>Taak diagram sluiten

Als u het taak diagram niet meer nodig hebt, selecteert u **sluiten** in de rechter bovenhoek. Nadat het diagram venster is gesloten, moet u de lokale tests opnieuw starten om het weer te geven.

### <a name="view-job-level-metrics-and-stop-running"></a>Metrische gegevens van taak niveau weer geven en stoppen met uitvoeren

Andere metrieken op taak niveau worden weer gegeven in de pop up-console. Druk op **CTRL + C** in de-console als u de taak wilt stoppen.

![Taak diagram stoppen taak](./media/debug-locally-using-job-diagram/stop-job.png)

## <a name="limitations"></a>Beperkingen

* Power BI-en Azure Data Lake Storage Gen1-uitvoer-sinks worden niet ondersteund vanwege beperkingen van het verificatie model.

* Alleen opties voor Cloud invoer hebben ondersteuning voor [tijd beleid](./stream-analytics-time-handling.md) , terwijl lokale invoer opties niet.

## <a name="next-steps"></a>Volgende stappen

* [Snelstartgids: een Stream Analytics-taak maken met behulp van Visual Studio](stream-analytics-quick-create-vs.md)
* [Visual Studio gebruiken om Azure Stream Analytics-taken weer te geven](stream-analytics-vs-tools.md)
* [Live-gegevens lokaal testen met Azure Stream Analytics-hulpprogram ma's voor Visual Studio (preview)](stream-analytics-live-data-local-testing.md)
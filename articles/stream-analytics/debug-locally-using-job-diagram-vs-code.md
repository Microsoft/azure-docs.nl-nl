---
title: Fouten opsporen Azure Stream Analytics query's lokaal met behulp van taak diagram in Visual Studio code
description: In dit artikel wordt beschreven hoe u fouten opspoort van query's met behulp van taak diagram in de Azure Stream Analytics extensie voor Visual Studio code.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 25ad696ad345fbf672f6bf26eb3f35a13fb03ea5
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019496"
---
# <a name="debug-azure-stream-analytics-queries-locally-using-job-diagram-in-visual-studio-code"></a>Fouten opsporen Azure Stream Analytics query's lokaal met behulp van taak diagram in Visual Studio code

Streaming-taken waarvoor geen resultaten of onverwachte resultaten optreden, moeten vaak problemen oplossen. De Visual Studio code Extension voor Azure Stream Analytics integreert taak diagrammen, metrische gegevens, Diagnostische logboeken en tussenliggende resultaten om snel de bron van een probleem te isoleren. U kunt het taak diagram gebruiken tijdens het lokaal testen van de query om de tussenliggende resultatenset en metrische gegevens voor elke stap te bekijken.

## <a name="debug-a-query-using-job-diagram"></a>Fouten opsporen in een query met behulp van taak diagram

Een Azure Stream Analytics script wordt gebruikt om invoer gegevens naar uitvoer gegevens te transformeren. In het taak diagram wordt weer gegeven hoe gegevens stromen van invoer bronnen, zoals Event hub of IoT Hub, via meerdere query stappen naar uitvoer-Sinks. Elke query stap wordt toegewezen aan een tijdelijke resultatenset die is gedefinieerd in het script met behulp van een- `WITH` instructie. U kunt de gegevens weer geven, evenals de metrieken van elke query stap in elke tussenliggende resultatenset om de bron van een probleem te vinden.

> [!NOTE]
> In dit taak diagram worden alleen de gegevens en statistieken voor lokale tests in één knoop punt weer gegeven. Het mag niet worden gebruikt voor het afstemmen van de prestaties en het oplossen van problemen.

### <a name="start-local-testing"></a>Lokale tests starten

Gebruik deze [Quick](quick-create-visual-studio-code.md) start om te leren hoe u met Visual Studio Code een stream Analytics taak maakt of [een bestaande taak naar een lokaal project exporteert](visual-studio-code-explore-jobs.md). Referenties voor invoer en uitvoer worden automatisch ingevuld voor geëxporteerde taken.

Als u de query wilt testen met lokale invoer gegevens, volgt u deze [instructies](visual-studio-code-local-run.md). Als u met Live-invoer wilt testen, gaat u naar de volgende stap om [uw invoer te configureren](stream-analytics-add-inputs.md) . 

Open het *\. asaql* -script bestand en selecteer **lokaal uitvoeren**. Selecteer vervolgens **lokale invoer gebruiken** of **Live-invoer gebruiken**. Het taak diagram wordt weer gegeven aan de rechter kant van het venster.

### <a name="view-the-output-and-intermediate-result-set"></a>De resultatenset van de uitvoer en de tussenliggende verzameling weer geven  

1. Alle taak uitvoer wordt weer gegeven in het resultaten venster aan de rechter kant van het venster Visual Studio-code.

   > [!div class="mx-imgBorder"]
   > ![Resultaten van taak uitvoer](./media/debug-locally-using-job-diagram-vs-code/job-output-results.png)

2. Selecteer de query stap om naar het script te navigeren. U wordt automatisch omgeleid naar het bijbehorende script in de editor aan de linkerkant. Het tussenliggende resultaat wordt weer gegeven in het resultaten venster aan de rechter kant van het venster Visual Studio-code.

   > [!div class="mx-imgBorder"]
   > ![Resultaat van voor beeld van taak diagram](./media/debug-locally-using-job-diagram-vs-code/preview-result.png)

### <a name="view-metrics"></a>Metrische gegevens bekijken

In deze sectie bekijkt u de metrische gegevens die beschikbaar zijn voor elk deel van het diagram.

1. Selecteer het tabblad **metrieken** naast het tabblad **resultaat** aan de rechter kant van het venster Visual Studio-code.

2. Selecteer **taak** in de vervolg keuzelijst. U kunt een lege ruimte in een grafiek knooppunt selecteren om te navigeren naar metrische gegevens op taak niveau. Deze weer gave bevat alle metrische gegevens die elke tien seconden worden bijgewerkt wanneer de taak wordt uitgevoerd. U kunt de metrische gegevens aan de rechter kant selecteren of de selectie ervan opheffen om ze in de grafieken weer te geven.

   > [!div class="mx-imgBorder"]
   > ![Metrische gegevens van taak diagram](./media/debug-locally-using-job-diagram-vs-code/job-metrics.png)

3. Selecteer de naam van de invoer gegevens bron in de vervolg keuzelijst om invoer metrieken te bekijken. De invoer bron in de onderstaande scherm afbeelding heet *aanhalings tekens*. Zie voor meer informatie over invoer metrieken [begrijpen stream Analytics taak bewaking en query's bewaken](stream-analytics-monitoring.md).

   > [!div class="mx-imgBorder"]
   > ![Invoer gegevens van het taak diagram](./media/debug-locally-using-job-diagram-vs-code/input-metrics.png)

4. Selecteer een query stap in het taak diagram of selecteer de naam van de stap in de vervolg keuzelijst om meet waarden voor het stap niveau weer te geven. Watermerk vertraging is de enige beschik bare stap metriek.

   > [!div class="mx-imgBorder"]
   > ![Metrische stap waarden](./media/debug-locally-using-job-diagram-vs-code/step-metrics.png)

5. Selecteer een uitvoer in het diagram of in de vervolg keuzelijst om metrische gegevens over uitvoer te bekijken. Zie voor meer informatie over metrische gegevens van uitvoer [begrijpen stream Analytics taak bewaking en het controleren van query's](stream-analytics-monitoring.md). Actieve sinks voor uitvoer worden niet ondersteund.

   > [!div class="mx-imgBorder"]
   > ![Metrische uitvoer gegevens](./media/debug-locally-using-job-diagram-vs-code/output-metrics.png)

### <a name="view-diagnostic-logs"></a>Diagnostische logboeken weergeven

Diagnostische logboeken op taak niveau bevatten diagnostische gegevens voor invoer gegevens bronnen en uitvoer-Sinks. Wanneer u een invoer knooppunt of uitvoer knooppunt selecteert, worden alleen de bijbehorende logboeken weer gegeven. Er worden geen logboeken weer gegeven als u een query stap selecteert. U kunt alle logboeken vinden op taak niveau en u kunt de logboeken filteren op ernst en tijd.

   > [!div class="mx-imgBorder"]
   > ![Diagnostische logboeken](./media/debug-locally-using-job-diagram-vs-code/diagnostic-logs.png)

   Selecteer een logboek vermelding om het hele bericht weer te geven.

   > [!div class="mx-imgBorder"]
   > ![Bericht over Diagnostische logboeken](./media/debug-locally-using-job-diagram-vs-code/diagnostic-logs-message.png)


## <a name="other-job-diagram-features"></a>Andere functies van het taak diagram

U kunt op de werk balk naar behoefte **stoppen** of **pauzeren** . Zodra de taak is onderbroken, kunt u deze uit de laatste uitvoer hervatten.

> [!div class="mx-imgBorder"]
> ![Taak stoppen of onderbreken](./media/debug-locally-using-job-diagram-vs-code/stop-pause-job.png)

Selecteer **taak samenvatting** in de rechter bovenhoek van het taak diagram om de eigenschappen en configuraties voor uw lokale taak te bekijken.

> [!div class="mx-imgBorder"]
> ![Overzicht van lokale taken](./media/debug-locally-using-job-diagram-vs-code/job-summary.png)

## <a name="limitations"></a>Beperkingen

* Dynamische uitvoer-sinks worden niet ondersteund in lokale uitvoering.

* Taak lokaal uitvoeren met Java script-functie wordt alleen ondersteund op het Windows-besturings systeem.

* Aangepaste C#-code en Azure Machine Learning-functies worden niet ondersteund. 

* Alleen opties voor Cloud invoer hebben ondersteuning voor [tijd beleid](./stream-analytics-time-handling.md) , terwijl lokale invoer opties niet.

## <a name="next-steps"></a>Volgende stappen

* [Snelstartgids: een Stream Analytics-taak maken met Visual Studio code](quick-create-visual-studio-code.md)
* [Azure Stream Analytics verkennen met Visual Studio code](visual-studio-code-explore-jobs.md)
* [Stream Analytics-query's lokaal testen met voorbeeldgegevens testen met behulp van Visual Studio Code](visual-studio-code-local-run.md)
* [Azure Stream Analytics taken lokaal met live input testen met Visual Studio code](visual-studio-code-local-run-live-input.md)
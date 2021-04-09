---
title: Live-gegevens testen met Azure Stream Analytics voor Visual Studio
description: Meer informatie over hoe u uw Azure Stream Analytics-taak lokaal kunt testen met live streaming-gegevens.
author: ajetasin
ms.author: ajetasi
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 99e9ce85bb1e59b5016e836ff16d03cb3326ec8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020363"
---
# <a name="test-live-data-locally-using-azure-stream-analytics-tools-for-visual-studio-preview"></a>Live-gegevens lokaal testen met Azure Stream Analytics-hulpprogram ma's voor Visual Studio (preview)

Met Azure Stream Analytics-hulpprogram ma's voor Visual Studio kunt u taken lokaal vanuit de IDE testen met live gebeurtenis stromen van Azure Event hub, IoT Hub en Blob Storage. Lokale tests van live data kunnen de [prestaties en schaal baarheid](stream-analytics-streaming-unit-consumption.md) niet vervangen die u in de cloud kunt uitvoeren, maar u kunt tijdens functionele tests tijd besparen door niet telkens wanneer u uw stream Analytics-taak wilt testen naar de Cloud. Deze functie is beschikbaar als preview-versie en mag niet worden gebruikt voor werk belastingen voor de productie.

## <a name="testing-options"></a>Test opties

De volgende lokale test opties worden ondersteund:

|**Invoer**  |**Uitvoer**  |**Taak type**  |
|---------|---------|---------|
|Lokale statische gegevens   |  Lokale statische gegevens   |   Cloud/Edge |
|Live invoer gegevens   |  Lokale statische gegevens   |   Cloud |
|Live invoer gegevens   |  Live uitvoer gegevens   |   Cloud |

## <a name="local-testing-with-live-data"></a>Lokale tests met Live-gegevens

1. Nadat u een [Azure stream Analytics Cloud project in Visual Studio](stream-analytics-quick-create-vs.md)hebt gemaakt, opent u **script. asaql**. De lokale test maakt standaard gebruik van lokale invoer en lokale uitvoer.

   ![Lokale invoer en lokale uitvoer van Visual Studio Azure Stream Analytics](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-local-input-output.png)

2. Als u Live-gegevens wilt testen, kiest **u Cloud invoer gebruiken** in de vervolg keuzelijst.

   ![Azure Stream Analytics Visual Studio Live-Cloud invoer](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input.png)

3. De **begin tijd** instellen om te definiëren wanneer de taak wordt gestart met het verwerken van invoer gegevens. De taak moet mogelijk invoer gegevens van tevoren lezen om nauw keurige resultaten te garanderen. De standaard instelling is ingesteld op 30 minuten vóór de huidige tijd.

   ![Start tijd van Visual Studio Live-gegevens Azure Stream Analytics](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-start-time.png)

4. Klik op **lokaal uitvoeren**. Er wordt een console venster weer gegeven met de uitvoerings voortgang en taak gegevens. Als u het proces wilt stoppen, kunt u dit hand matig doen. 

   ![Azure Stream Analytics Visual Studio live data process-venster](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-process-window.png)

   De uitvoer resultaten worden om de drie seconden vernieuwd met de eerste 500 uitvoer rijen in het venster voor de lokale uitvoering van resultaten en de uitvoer bestanden worden geplaatst in de map **ASALocalRun** van het project. U kunt de uitvoer bestanden ook openen door te klikken op de knop **map openen** in het venster lokale uitvoer resultaten.

   ![Map voor open bare resultaten van de Azure Stream Analytics Visual Studio Live-gegevens](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-open-results-folder.png)

5. Als u de resultaten naar de Cloud uitvoer-sinks wilt uitvoeren, kiest u in de tweede vervolg keuzelijst **uitvoer naar de Cloud** . Power BI en Azure Data Lake Storage worden geen uitvoer filters ondersteund.

   ![Azure Stream Analytics Visual Studio Live-gegevens uitvoer naar de Cloud](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-output.png)
 
## <a name="limitations"></a>Beperkingen

* Power BI en Azure Data Lake Storage worden geen uitvoer filters ondersteund vanwege beperkingen van het verificatie model.

* Alleen opties voor Cloud invoer hebben ondersteuning voor [tijd beleid](./stream-analytics-time-handling.md) , terwijl lokale invoer opties niet.

## <a name="next-steps"></a>Volgende stappen

* [Een Stream Analytics-taak maken met behulp van de Azure Stream Analytics-tools voor Visual Studio](stream-analytics-quick-create-vs.md)
* [Stream Analytics query's lokaal testen met Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Visual Studio gebruiken om Azure Stream Analytics-taken weer te geven](stream-analytics-vs-tools.md)
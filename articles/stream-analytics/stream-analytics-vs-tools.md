---
title: Azure Stream Analytics taken weer geven in Visual Studio
description: Meer informatie over het weer geven, starten en stoppen, testen van verbindingen, het controleren van resultaten en het exporteren van uw Azure Stream Analytics taken met Visual Studio.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 07/10/2018
ms.openlocfilehash: 4b568fa8d9e2fb8bd00acb27b0dc3f7c45c09c57
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019122"
---
# <a name="use-visual-studio-to-view-azure-stream-analytics-jobs"></a>Visual Studio gebruiken om Azure Stream Analytics-taken weer te geven

Met Azure Stream Analytics-hulpprogram ma's voor Visual Studio kunnen ontwikkel aars gemakkelijk hun Stream Analytics taken rechtstreeks vanuit de IDE beheren. Met Azure Stream Analytics-hulpprogram ma's kunt u het volgende doen:
- [Nieuwe taken maken](stream-analytics-quick-create-vs.md)
- Taken starten, stoppen en [bewaken](stream-analytics-monitor-jobs-use-vs.md)
- Taak resultaten controleren
- Bestaande taken naar een project exporteren
- Invoer- en uitvoerverbindingen testen
- [Query's lokaal uitvoeren](stream-analytics-vs-tools-local-run.md)

Meer informatie over het [installeren van Azure stream Analytics-hulpprogram ma's voor Visual Studio](stream-analytics-tools-for-visual-studio-install.md).

## <a name="explore-the-job-view"></a>De taak weergave verkennen

U kunt de taak weergave gebruiken om te communiceren met Azure Stream Analytics taken van Visual Studio.

### <a name="open-the-job-view"></a>De taak weergave openen

1. Selecteer in **Server Explorer** **Stream Analytics taken** en selecteer vervolgens **vernieuwen**. Uw taak moet worden weer gegeven onder **Stream Analytics taken**.

    ![Stream Analytics server Verkenner-lijst](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-list-jobs-01.png)

2. Vouw uw taak knooppunt uit en dubbel klik op het **taak weergave** knooppunt om een taak weergave te openen.
    
   ![Uitgevouwen taak knooppunt](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-job-view-01.png)

### <a name="start-and-stop-jobs"></a>Taken starten en stoppen

Azure Stream Analytics taken kunnen volledig worden beheerd vanuit de taak weergave in Visual Studio. Gebruik de besturings elementen om een taak te starten, te stoppen of te verwijderen.
    
   ![Stream Analytics taak besturings elementen](./media/stream-analytics-vs-tools/azure-stream-analytics-job-view-controls.png)

## <a name="check-job-results"></a>Taak resultaten controleren

Stream Analytics-hulpprogram ma's voor Visual Studio ondersteunen momenteel uitvoer voorbeelden voor Azure Data Lake Storage en Blob-opslag. Als u resultaat wilt weer geven, dubbelklikt u op het knoop punt uitvoer van het taak diagram in de **taak weergave** en voert u de juiste referenties in.

   ![Taak BLOB-uitvoer Stream Analytics](./media/stream-analytics-vs-tools/stream-analytics-blob-preview.png)

## <a name="export-jobs-to-a-project"></a>Taken exporteren naar een project

Er zijn twee manieren waarop u een bestaande taak kunt exporteren naar een project.

1. Klik in **Server Explorer** onder het knoop punt stream Analytics taken met de rechter muisknop op het knoop punt taak. Selecteer **exporteren naar nieuw stream Analytics project**.
    
   ![Taak exporteren naar project](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-01.png)
    
    Het gegenereerde project wordt weer gegeven in **Solution Explorer**.
    
   ![Solution Explorer](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-02.png)

2. Selecteer in de taak weergave **project genereren**.
    
   ![Project genereren vanuit de taak weergave](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-03.png)

## <a name="test-connections"></a>Verbindingen testen

Invoer-en uitvoer verbindingen kunnen worden getest vanuit de **taak weergave** door een optie te selecteren in de vervolg keuzelijst **Test verbinding** .

   ![Vervolg keuzelijst voor verbinding testen](./media/stream-analytics-vs-tools/stream-analytics-test-connection-dropdown.png)

De resultaten van de **Test verbinding** worden weer gegeven in het **uitvoer** venster.

   ![Verbindings resultaten testen](./media/stream-analytics-vs-tools/stream-analytics-test-connection-results.png)

## <a name="next-steps"></a>Volgende stappen

* [Azure Stream Analytics taken bewaken en beheren met Visual Studio](stream-analytics-monitor-jobs-use-vs.md)
* [Snelstartgids: een Stream Analytics-taak maken met behulp van Visual Studio](stream-analytics-quick-create-vs.md)
* [Continue integratie en ontwikkeling met Stream Analytics-hulpprogramma’s](stream-analytics-tools-for-visual-studio-cicd.md)

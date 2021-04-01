---
title: Een Azure Stream Analytics taak starten
description: In dit artikel wordt beschreven hoe u een Stream Analytics taak start vanuit Azure Portal, Power shell en Visual Studio.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 04/03/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 79149d8e9862ece24b4b2da4c2ca4afcceb23d63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98016249"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>Een Azure Stream Analytics taak starten

U kunt uw Azure Stream Analytics-taak starten met behulp van de Azure Portal, Visual Studio en Power shell. Wanneer u een taak start, selecteert u een tijd voor de taak om te beginnen met het maken van de uitvoer. Azure Portal, Visual Studio en Power Shell hebben verschillende methoden voor het instellen van de begin tijd. Deze methoden worden hieronder beschreven.

## <a name="start-options"></a>Start opties
De drie volgende opties zijn beschikbaar om een taak te starten. Houd er rekening mee dat alle hieronder genoemde tijden zijn opgegeven in [time stamp by](/stream-analytics-query/timestamp-by-azure-stream-analytics). Als TIJDS tempel door niet is opgegeven, wordt aankomst tijd gebruikt.
* **Nu**: maakt het begin punt van de uitvoer gebeurtenis stroom hetzelfde als wanneer de taak wordt gestart. Als er een tijdelijke operator wordt gebruikt (bijvoorbeeld een tijd venster, een vertraging of een samen voeging), wordt Azure Stream Analytics automatisch teruggestuurd naar de gegevens in de invoer bron. Als u bijvoorbeeld een taak nu start en als uw query een Tumblingvenstertriggers-venster van vijf minuten gebruikt, zoekt Azure Stream Analytics gegevens van vijf minuten geleden in de invoer.
De eerste mogelijke uitvoer gebeurtenis zou een tijds tempel hebben die gelijk is aan of groter is dan de huidige tijd en ASA garandeert dat alle invoer gebeurtenissen die logisch kunnen bijdragen aan de uitvoer, zijn verwerkt. Er worden bijvoorbeeld geen gedeeltelijke venster aggregaties gegenereerd. Het is altijd de volledige geaggregeerde waarde.

* **Aangepast**: u kunt het begin punt van de uitvoer kiezen. Net als bij de Azure Stream Analytics optie **nu** worden de gegevens vóór deze tijd automatisch gelezen als een tijdelijke operator wordt gebruikt 

* **Wanneer de laatste keer is gestopt**. Deze optie is beschikbaar wanneer de taak eerder is gestart, maar niet hand matig is gestopt of mislukt. Wanneer u deze optie kiest, gebruikt Azure Stream Analytics de laatste uitvoer tijd om de taak opnieuw te starten, zodat er geen gegevens verloren gaan. Net als bij vorige opties Azure Stream Analytics de gegevens vóór deze tijd automatisch lezen als er een tijdelijke operator wordt gebruikt. Omdat verschillende invoer partities een andere tijd kunnen hebben, wordt de eerste stop tijd van alle partities gebruikt, omdat er mogelijk dubbele waarden worden weer gegeven in de uitvoer. Meer informatie over eenmalige verwerking is beschikbaar op de pagina [gebeurtenis bezorgings garanties](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics).


## <a name="azure-portal"></a>Azure Portal

Navigeer naar uw taak in de Azure Portal en selecteer **starten** op de pagina overzicht. Selecteer een **begin tijd voor de taak uitvoer** en selecteer vervolgens **starten**.

Kies een van de opties voor de **Start tijd van de taak uitvoer**. De opties zijn *nu*, *aangepast* en, als de taak eerder werd uitgevoerd, wanneer het de  *laatste keer is gestopt*. Zie hierboven voor meer informatie over deze opties.

## <a name="visual-studio"></a>Visual Studio

Selecteer in de taak weergave de groene pijl knop om de taak te starten. Stel de **Start modus voor taak uitvoer** in en selecteer **starten**. De taak status wordt gewijzigd in **wordt uitgevoerd**.

Er zijn drie opties voor de **Start modus voor taak uitvoer**: *JobStartTime*, *CustomTime* en *LastOutputEventTime*. Als deze eigenschap ontbreekt, is de standaard waarde *JobStartTime*. Zie hierboven voor meer informatie over deze opties.


## <a name="powershell"></a>PowerShell

Gebruik de volgende cmdlet om uw taak te starten met behulp van Power shell:

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

Er zijn drie opties voor **output start mode**: *JobStartTime*, *CustomTime* en *LastOutputEventTime*. Als deze eigenschap ontbreekt, is de standaard waarde *JobStartTime*. Zie hierboven voor meer informatie over deze opties.

Voor meer informatie over de `Start-AzStreamAnalyitcsJob` cmdlet bekijkt u de [referentie start-AzStreamAnalyticsJob](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob).

## <a name="next-steps"></a>Volgende stappen

* [Snelstart: Een Stream Analytics-taak maken via Azure Portal](stream-analytics-quick-create-portal.md)
* [Quickstart: Een Stream Analytics-taak maken met behulp van Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Snelstart: Een Stream Analytics-taak maken met behulp van de Azure Stream Analytics-tools voor Visual Studio](stream-analytics-quick-create-vs.md)
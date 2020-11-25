---
title: Uw Azure Stream Analytics-taak opschonen
description: In dit artikel ziet u verschillende methoden voor het verwijderen van uw Azure Stream Analytics taken.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: 2652b97c9392d3016bbc52209d4b2bda81c31706
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022078"
---
# <a name="stop-or-delete-your-azure-stream-analytics-job"></a>Uw Azure Stream Analytics-taak stoppen of verwijderen

Azure Stream Analytics-taken kunnen eenvoudig worden gestopt of verwijderd via de Azure Portal, Azure PowerShell, Azure SDK voor .net of REST API. Een Stream Analytics taak kan niet worden hersteld als deze eenmaal is verwijderd.

>[!NOTE] 
>Wanneer u de Stream Analytics taak stopt, blijven de gegevens alleen aanwezig in de invoer-en uitvoer opslag, zoals Event Hubs of Azure SQL Database. Als u de gegevens van Azure wilt verwijderen, moet u het verwijderings proces voor de invoer-en uitvoer resources van uw Stream Analytics-taak volgen.

## <a name="stop-a-job-in-azure-portal"></a>Een taak stoppen in Azure Portal

Wanneer u een taak stopt, wordt de inrichting van de resources ongedaan gemaakt en stopt het verwerken van gebeurtenissen. Kosten die betrekking hebben op deze taak worden ook gestopt. Al uw configuratie wordt echter opgeslagen en u kunt de taak later opnieuw starten 

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). 

2. Zoek uw actieve Stream Analytics-taak en selecteer deze.

3. Selecteer op de pagina Stream Analytics-taak **stoppen** om de taak te stoppen. 

   ![Azure Stream Analytics taak stoppen](./media/stream-analytics-clean-up-your-job/stop-stream-analytics-job.png)


## <a name="delete-a-job-in-azure-portal"></a>Een taak in Azure Portal verwijderen

>[!WARNING] 
>Een Stream Analytics taak kan niet worden hersteld als deze eenmaal is verwijderd.

1. Meld u aan bij de Azure-portal. 

2. Zoek uw bestaande Stream Analytics-taak en selecteer deze.

3. Selecteer op de pagina Stream Analytics-taak de optie **verwijderen** om de taak te verwijderen. 

   ![Azure Stream Analytics taak verwijderen](./media/stream-analytics-clean-up-your-job/delete-stream-analytics-job.png)


## <a name="stop-or-delete-a-job-using-powershell"></a>Een taak stoppen of verwijderen met Power shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Als u een taak wilt stoppen met behulp van Power shell, gebruikt u de cmdlet [Stop-AzStreamAnalyticsJob](/powershell/module/az.streamanalytics/stop-azstreamanalyticsjob) . Als u een taak wilt verwijderen met behulp van Power shell, gebruikt u de cmdlet [Remove-AzStreamAnalyticsJob](/powershell/module/az.streamanalytics/Remove-azStreamAnalyticsJob) .

## <a name="stop-or-delete-a-job-using-azure-sdk-for-net"></a>Een taak stoppen of verwijderen met behulp van Azure SDK voor .NET

Als u een taak wilt stoppen met behulp van Azure SDK voor .NET, gebruikt u de methode [StreamingJobsOperationsExtensions. BeginStop](/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop) . Een taak verwijderen met behulp van de Azure SDK voor .NET-, [StreamingJobsOperationsExtensions. BeginDelete](/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete) -methode.

## <a name="stop-or-delete-a-job-using-rest-api"></a>Een taak stoppen of verwijderen met REST API

Als u een taak wilt stoppen met behulp van REST API, raadpleegt u de methode [Stop](/rest/api/streamanalytics/2016-03-01/streamingjobs/stop) . Als u een taak wilt verwijderen met behulp van REST API, raadpleegt u de methode [Delete](/rest/api/streamanalytics/2016-03-01/streamingjobs/delete) .
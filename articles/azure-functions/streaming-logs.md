---
title: Uitvoeringslogboeken streamen in Azure Functions
description: 115-145 tekens, inclusief spaties. Dit uittreksel wordt bij de zoekresultaten weergegeven.
ms.date: 9/1/2020
ms.topic: how-to
ms.custom: contperf-fy21q2, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 4afb1068acda69c9dd65a423d887abea80c695cd
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830894"
---
# <a name="enable-streaming-execution-logs-in-azure-functions"></a>Uitvoeringslogboeken voor streaming inschakelen in Azure Functions

Tijdens het ontwikkelen van een toepassing wilt u vaak in bijna realtime zien wat er naar de logboeken wordt geschreven wanneer deze in Azure worden uitgevoerd.

Er zijn twee manieren om een stroom logboekbestanden te bekijken die worden gegenereerd door uw functie-uitvoeringen.

* **Ingebouwde logboekstreaming:** met App Service platform kunt u een stroom van uw toepassingslogboekbestanden weergeven. Dit komt overeen met de uitvoer die [](functions-develop-local.md) wordt weergegeven wanneer u fouten in uw functies opspoort tijdens lokale ontwikkeling en wanneer u het **tabblad Testen** in de portal gebruikt. Alle op logboek gebaseerde informatie wordt weergegeven. Zie Stream-logboeken [voor meer informatie.](../app-service/troubleshoot-diagnostic-logs.md#stream-logs) Deze streamingmethode ondersteunt slechts één exemplaar en kan niet worden gebruikt met een app die wordt uitgevoerd op Linux in een verbruiksplan.

* **Live Metrics Stream:** wanneer uw functie-app is verbonden met [Application Insights,](configure-monitoring.md#enable-application-insights-integration)kunt u logboekgegevens en andere metrische gegevens bijna in realtime in de Azure Portal weergeven met [behulp van Live Metrics Stream](../azure-monitor/app/live-stream.md). Gebruik deze methode bij het bewaken van functies die worden uitgevoerd op meerdere exemplaren of op Linux in een verbruiksplan. Deze methode maakt gebruik [van voorbeeldgegevens](configure-monitoring.md#configure-sampling).

Logboekstreams kunnen zowel in de portal als in de meeste lokale ontwikkelomgevingen worden bekeken. 

## <a name="portal"></a>Portal

U kunt beide typen logboekstreams weergeven in de portal.

### <a name="built-in-log-streaming"></a>Ingebouwde logboekstreaming

Als u streaminglogboeken in de portal wilt weergeven, selecteert u het **tabblad Platformfuncties** in uw functie-app. Kies vervolgens onder **Bewaking de** optie **Logboekstreaming.**

![Streaminglogboeken inschakelen in de portal](./media/functions-monitoring/enable-streaming-logs-portal.png)

Hierdoor wordt uw app verbonden met de logboekstreamingservice en worden de toepassingslogboeken weergegeven in het venster. U kunt schakelen tussen **toepassingslogboeken en** **webserverlogboeken.**  

![Streaminglogboeken weergeven in de portal](./media/functions-monitoring/streaming-logs-window.png)

### <a name="live-metrics-stream"></a>Live Metrics Stream

Als u de Live Metrics Stream voor uw app wilt weergeven, selecteert u **het tabblad Overzicht** van uw functie-app. Wanneer u Application Insights hebt, ziet u een koppeling **Application Insights** **geconfigureerde functies.** Met deze koppeling gaat u naar Application Insights pagina voor uw app.

Selecteer Application Insights in **Live Metrics Stream**. [Voorbeelden van logboekgegevens worden](configure-monitoring.md#configure-sampling) weergegeven onder **Sample Telemetry**.

![De Live Metrics Stream weergeven in de portal](./media/functions-monitoring/live-metrics-stream.png) 

## <a name="visual-studio-code"></a>Visual Studio Code

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

## <a name="core-tools"></a>Kernhulpprogramma's

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

## <a name="azure-cli"></a>Azure CLI

U kunt streaminglogboeken inschakelen met behulp van [de Azure CLI](/cli/azure/install-azure-cli). Gebruik de volgende opdrachten om u aan te melden, uw abonnement te kiezen en logboekbestanden te streamen:

```azurecli
az login
az account list
az account set --subscription <subscriptionNameOrId>
az webapp log tail --resource-group <RESOURCE_GROUP_NAME> --name <FUNCTION_APP_NAME>
```

## <a name="azure-powershell"></a>Azure PowerShell

U kunt streaminglogboeken inschakelen met behulp [van Azure PowerShell](/powershell/azure/). Gebruik voor PowerShell de [opdracht Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) om logboekregistratie in te stellen voor de functie-app, zoals wordt weergegeven in het volgende fragment: 

:::code language="powershell" source="~/powershell_scripts/app-service/monitor-with-logs/monitor-with-logs.ps1" range="19-20":::

Zie het volledige [codevoorbeeld voor meer informatie.](../app-service/scripts/powershell-monitor.md#sample-script) 

## <a name="next-steps"></a>Volgende stappen

+ [Azure Functions controleren](functions-monitoring.md)
+ [Analyseer Azure Functions telemetrie in Application Insights](analyze-telemetry-data.md)

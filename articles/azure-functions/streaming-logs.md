---
title: Uitvoerings logboeken streamen in Azure Functions
description: 115-145 tekens, inclusief spaties. Dit uittreksel wordt bij de zoekresultaten weergegeven.
ms.date: 9/1/2020
ms.topic: how-to
ms.custom: contperfq2, devx-track-azurecli
ms.openlocfilehash: 68b9d567fe0f2959c809a25c3669b9529cf093b8
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94832876"
---
# <a name="enable-streaming-execution-logs-in-azure-functions"></a>Streaming-uitvoerings logboeken inschakelen in Azure Functions

Tijdens het ontwikkelen van een toepassing wilt u vaak zien wat er in de buurt van real-time naar de logboeken wordt geschreven wanneer u in azure uitvoert.

Er zijn twee manieren om een stroom weer te geven van de logboek bestanden die worden gegenereerd door uw functie-uitvoeringen.

* **Ingebouwde logboek streaming**: met het app service-platform kunt u een stroom weer geven van de logboek bestanden van uw toepassing. Dit komt overeen met de uitvoer die wordt weer gegeven wanneer u tijdens de [lokale ontwikkeling](functions-develop-local.md) fouten opspoort in uw functies en wanneer u het tabblad **testen** in de portal gebruikt. Alle gegevens op basis van het logboek worden weer gegeven. Zie [Stream-logboeken](../app-service/troubleshoot-diagnostic-logs.md#stream-logs)voor meer informatie. Deze streaming-methode ondersteunt slechts één exemplaar en kan niet worden gebruikt met een app die wordt uitgevoerd op Linux in een verbruiks abonnement.

* **Live Metrics stream**: als uw functie-app is [verbonden met Application Insights](configure-monitoring.md#enable-application-insights-integration), kunt u in bijna realtime logboek gegevens en andere gegevens weer geven in de Azure Portal met behulp van [Live Metrics stream](../azure-monitor/app/live-stream.md). Gebruik deze methode wanneer u functies bewaken die worden uitgevoerd op meerdere exemplaren of op Linux in een verbruiks abonnement. Met deze methode worden [voorbeeld gegevens](configure-monitoring.md#configure-sampling)gebruikt.

Logboek stromen kunnen zowel in de portal als in de meeste lokale ontwikkel omgevingen worden weer gegeven. 

## <a name="portal"></a>Portal

U kunt beide typen logboek stromen weer geven in de portal.

### <a name="built-in-log-streaming"></a>Ingebouwde logboekstreaming

Als u streaming-logboeken wilt weer geven in de portal, selecteert u het tabblad **platform functies** in uw functie-app. Klik vervolgens onder **bewaking** op **logboek streaming**.

![Streaming-logboeken inschakelen in de portal](./media/functions-monitoring/enable-streaming-logs-portal.png)

Hiermee wordt uw app verbonden met de service logboek streaming en worden toepassings Logboeken in het venster weer gegeven. U kunt scha kelen tussen **toepassings logboeken** en **webserver logboeken**.  

![Streaming-logboeken weer geven in de portal](./media/functions-monitoring/streaming-logs-window.png)

### <a name="live-metrics-stream"></a>Live Metrics Stream

Als u de Live Metrics Stream voor uw app wilt weer geven, selecteert u het tabblad **overzicht** van de functie-app. Wanneer u Application Insights hebt ingeschakeld, ziet u een **Application Insights** koppeling onder **geconfigureerde onderdelen**. Met deze koppeling gaat u naar de Application Insights-pagina voor uw app.

Selecteer **Live Metrics stream** in Application Insights. [Voorbeeld logboek vermeldingen](configure-monitoring.md#configure-sampling) worden weer gegeven onder voor **beeld-telemetrie**.

![Live Metrics Stream weer geven in de portal](./media/functions-monitoring/live-metrics-stream.png) 

## <a name="visual-studio-code"></a>Visual Studio Code

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

## <a name="core-tools"></a>Kern Hulpprogramma's

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

## <a name="azure-cli"></a>Azure CLI

U kunt streaming-logboeken inschakelen met behulp van de [Azure cli](/cli/azure/install-azure-cli). Gebruik de volgende opdrachten om u aan te melden, uw abonnement te kiezen en logboek bestanden te streamen:

```azurecli
az login
az account list
az account set --subscription <subscriptionNameOrId>
az webapp log tail --resource-group <RESOURCE_GROUP_NAME> --name <FUNCTION_APP_NAME>
```

## <a name="azure-powershell"></a>Azure PowerShell

U kunt streaming-logboeken inschakelen met behulp van [Azure PowerShell](/powershell/azure/). Gebruik voor Power shell de opdracht [set-AzWebApp](/powershell/module/az.websites/set-azwebapp) om logboek registratie in te scha kelen voor de functie-app, zoals wordt weer gegeven in het volgende code fragment: 

:::code language="powershell" source="~/powershell_scripts/app-service/monitor-with-logs/monitor-with-logs.ps1" range="19-20":::

Zie het [volledige code voorbeeld](../app-service/scripts/powershell-monitor.md#sample-script)voor meer informatie. 

## <a name="next-steps"></a>Volgende stappen

+ [Azure Functions controleren](functions-monitoring.md)
+ [Azure Functions telemetrie in Application Insights analyseren](analyze-telemetry-data.md)

---
title: Live Azure-Cloud Services profiel met Application Insights | Microsoft Docs
description: Schakel Application Insights Profiler in voor Azure Cloud Services.
ms.topic: conceptual
ms.custom: devx-track-dotnet
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 75927534bf62fa4733bb552906ffa7e3a6e76286
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98935621"
---
# <a name="profile-live-azure-cloud-services-with-application-insights"></a>Live Azure-Cloud Services profiel met Application Insights

U kunt ook Application Insights Profiler op deze services implementeren:
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric-toepassingen](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler is geïnstalleerd met de Azure Diagnostics extensie. U hoeft alleen Azure Diagnostics te configureren om Profiler te installeren en profielen naar uw Application Insights-bron te verzenden.

## <a name="enable-profiler-for-azure-cloud-services"></a>Profiler inschakelen voor Azure Cloud Services
1. Controleer of u [.NET Framework 4.6.1](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) of nieuwer gebruikt. Als u OS Family 4 gebruikt, moet u .NET Framework 4.6.1 of nieuwer installeren met een [opstart taak](../../cloud-services/cloud-services-dotnet-install-dotnet.md). BESTURINGSSYSTEEM familie 5 bevat standaard een compatibele versie van .NET Framework. 

1. [Application INSIGHTS SDK toevoegen aan Azure Cloud Services](./cloudservices.md?toc=%2fazure%2fazure-monitor%2ftoc.json).

    **De fout in de Profiler die in de WAD voor Cloud Services wordt geleverd, is opgelost.** De nieuwste versie van WAD (1.12.2.0) voor Cloud Services werkt met alle recente versies van de app Insights-SDK. Met Cloud service-hosts wordt WAD automatisch bijgewerkt, maar dit is niet direct. U kunt een upgrade forceren door uw service opnieuw te implementeren of het knoop punt opnieuw op te starten.

1. Aanvragen bijhouden met Application Insights:

    * Voor ASP.NET-webrollen kunt Application Insights de aanvragen automatisch volgen.

    * Voor werk rollen [voegt u code toe om aanvragen bij te houden](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json).

1. Configureer de Azure Diagnostics-extensie om Profiler in te scha kelen:

    a. Zoek het bestand [Azure Diagnostics](../platform/diagnostics-extension-overview.md) *Diagnostics. wadcfgx* voor uw toepassingsrol, zoals hier wordt weer gegeven:  

      ![Locatie van het configuratie bestand voor diagnostische gegevens](./media/profiler-cloudservice/cloudservice-solutionexplorer.png)  

      Als u het bestand niet kunt vinden, raadpleegt u [Diagnostische gegevens instellen voor Azure Cloud Services en virtual machines](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines).

    b. Voeg de volgende `SinksConfig` sectie toe als onderliggend element van `WadCfg` :  

      ```xml
      <WadCfg>
        <DiagnosticMonitorConfiguration>...</DiagnosticMonitorConfiguration>
        <SinksConfig>
          <Sink name="MyApplicationInsightsProfiler">
            <!-- Replace with your own Application Insights instrumentation key. -->
            <ApplicationInsightsProfiler>00000000-0000-0000-0000-000000000000</ApplicationInsightsProfiler>
          </Sink>
        </SinksConfig>
      </WadCfg>
      ```

    > [!NOTE]
    > Als het bestand *Diagnostics. wadcfgx* ook een andere Sink van het type ApplicationInsights bevat, moeten alle drie de volgende instrumentatie sleutels overeenkomen:  
    > * De sleutel die wordt gebruikt door uw toepassing. 
    > * De sleutel die wordt gebruikt door de ApplicationInsights-sink. 
    > * De sleutel die wordt gebruikt door de ApplicationInsightsProfiler-sink. 
    >
    > U vindt de waarde van de werkelijke instrumentatie sleutel die wordt gebruikt door de `ApplicationInsights` sink in de     *ServiceConfiguration. \* . cscfg* -bestanden. 
    > Na de Visual Studio 15,5 Azure SDK-versie moeten alleen de instrumentatie sleutels die worden gebruikt door de toepassing en de ApplicationInsightsProfiler-sink, overeenkomen met elkaar.

1. Implementeer uw service met de nieuwe diagnostische configuratie en Application Insights Profiler is geconfigureerd om te worden uitgevoerd op uw service.
 
## <a name="next-steps"></a>Volgende stappen

* Verkeer genereren voor uw toepassing (bijvoorbeeld een [beschikbaarheids test](monitor-web-app-availability.md)starten). Wacht vervolgens tien tot vijf tien minuten voordat traceringen worden verzonden naar het Application Insights-exemplaar.
* Zie [Profiler traceringen](profiler-overview.md?toc=/azure/azure-monitor/toc.json) in de Azure Portal.
* Zie problemen [oplossen met Profiler voor informatie over](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)het oplossen van problemen met profilering.


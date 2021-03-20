---
title: Web-apps op een virtuele Azure-machine profiel-Application Insights Profiler
description: Webtoepassingen op een virtuele Azure-machine profiel maken met behulp van Application Insights Profiler.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 11/08/2019
ms.reviewer: mbullwin
ms.openlocfilehash: f514dd7b54ac091535aeab43a8a7d2a645b50a09
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87315815"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-a-virtual-machine-scale-set-by-using-application-insights-profiler"></a>Profiel Web-apps die worden uitgevoerd op een virtuele machine van Azure of een schaalset voor virtuele machines met behulp van Application Insights Profiler

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

U kunt ook Azure-toepassing Insights Profiler implementeren op deze services:
* [Azure App Service](./profiler.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](?toc=%2fazure%2fazure-monitor%2ftoc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-a-virtual-machine-scale-set"></a>Profiler implementeren op een virtuele machine of een schaalset voor virtuele machines
In dit artikel wordt beschreven hoe u Application Insights Profiler op uw virtuele machine met Azure virtual machine (VM) of virtuele-machinehost van Azure kunt ophalen. Profiler wordt geïnstalleerd met de Azure Diagnostics-extensie voor Vm's. Configureer de uitbrei ding voor het uitvoeren van Profiler en bouw de Application Insights SDK in uw toepassing.

1. Voeg de Application Insights SDK toe aan uw [ASP.NET-toepassing](./asp-net.md).

   Als u profielen voor uw aanvragen wilt weer geven, moet u de aanvraag-telemetrie naar Application Insights verzenden.

1. Installeer Azure Diagnostics-extensie op uw VM. Voor de volledige voor beelden van Resource Manager-sjablonen raadpleegt u:  
   * [Virtuele machine](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
   * [Schaalset voor virtuele machines](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
    
     Het sleutel gedeelte is het ApplicationInsightsProfilerSink in de WadCfg. Voeg een andere Sink toe aan deze sectie om Azure Diagnostics Profiler in te scha kelen om gegevens naar uw iKey te verzenden.
    
     ```json
     "SinksConfig": {
       "Sink": [
         {
           "name": "ApplicationInsightsSink",
           "ApplicationInsights": "85f73556-b1ba-46de-9534-606e08c6120f"
         },
         {
           "name": "MyApplicationInsightsProfilerSink",
           "ApplicationInsightsProfiler": "85f73556-b1ba-46de-9534-606e08c6120f"
         }
       ]
     },
     ```

1. Implementeer de gewijzigde definitie van de implementatie van de omgeving.  

   Het Toep assen van de wijzigingen omvat doorgaans een volledige sjabloon implementatie of een op Cloud service gebaseerde publicatie via Power shell-cmdlets of Visual Studio.  

   De volgende Power shell-opdrachten vormen een alternatieve methode voor bestaande virtuele machines die alleen de Azure Diagnostics-extensie aanraken. Voeg de eerder genoemde ProfilerSink toe aan de configuratie die wordt geretourneerd door de Get-AzVMDiagnosticsExtension opdracht. Geef vervolgens de bijgewerkte configuratie door aan de opdracht Set-AzVMDiagnosticsExtension.

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. Als de gewenste toepassing via [IIS](https://www.microsoft.com/web/downloads/platform.aspx)wordt uitgevoerd, schakelt u de `IIS Http Tracing` Windows-functie in.

   a. Stel externe toegang tot de omgeving in en gebruik vervolgens het venster [Windows-onderdelen toevoegen](/iis/configuration/system.webserver/tracing/) . Of voer de volgende opdracht uit in Power shell (als Administrator):  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. Als externe toegang tot stand is gebracht, kunt u de [Azure cli](/cli/azure/get-started-with-azure-cli) gebruiken om de volgende opdracht uit te voeren:  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```

1. Implementeer uw toepassing.

## <a name="set-profiler-sink-using-azure-resource-explorer"></a>Profilerings Sink instellen met behulp van Azure Resource Explorer
Er is nog geen manier om de Application Insights Profiler sink in te stellen vanuit de portal. In plaats van Power shell zoals hierboven beschreven te gebruiken, kunt u Azure Resource Explorer gebruiken om de sink in te stellen. Houd er rekening mee dat als u de virtuele machine opnieuw implementeert, de Sink verloren gaat. U moet de configuratie bijwerken die u gebruikt bij het implementeren van de virtuele machine om deze instelling te behouden.

1. Controleer of de Windows Azure Diagnostics-extensie is geïnstalleerd door de uitbrei dingen die voor uw virtuele machine zijn geïnstalleerd weer te geven.  

    ![Controleren of de WAD-extensie is geïnstalleerd][wadextension]

2. Zoek de VM-extensie voor diagnostische gegevens voor uw VM. Ga naar [https://resources.azure.com](https://resources.azure.com). Vouw uw resource groep, micro soft. Compute informatie, de naam van de virtuele machine en de extensies uit.  

    ![Ga naar WAD config in Azure Resource Explorer][azureresourceexplorer]

3. Voeg de Application Insights Profiler-Sink toe aan het knoop punt SinksConfig onder WadCfg. Als u nog geen SinksConfig-sectie hebt, moet u er mogelijk een toevoegen. Zorg ervoor dat u de juiste Application Insights iKey in uw instellingen opgeeft. U moet in de rechter bovenhoek overschakelen op lezen/schrijven in de modus Verkenner en op de knop ' bewerken ' drukken.

    ![Application Insights Profiler Sink toevoegen][resourceexplorersinksconfig]

4. Wanneer u klaar bent met het bewerken van de configuratie, drukt u op put. Als de put is gelukt, wordt er een groen vinkje weer gegeven in het midden van het scherm.

    ![Put-aanvraag verzenden om wijzigingen toe te passen][resourceexplorerput]






## <a name="can-profiler-run-on-on-premises-servers"></a>Kan Profiler worden uitgevoerd op on-premises servers?
Er is geen abonnement om Application Insights Profiler te ondersteunen voor on-premises servers.

## <a name="next-steps"></a>Volgende stappen

- Verkeer genereren voor uw toepassing (bijvoorbeeld een [beschikbaarheids test](monitor-web-app-availability.md)starten). Wacht vervolgens tien tot vijf tien minuten voordat traceringen worden verzonden naar het Application Insights-exemplaar.
- Zie [Profiler traceringen](profiler-overview.md?toc=/azure/azure-monitor/toc.json) in de Azure Portal.
- Zie [problemen oplossen met Profiler](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)voor hulp bij het oplossen van problemen met profileringen.

[azureresourceexplorer]: ./media/profiler-vm/azure-resource-explorer.png
[resourceexplorerput]: ./media/profiler-vm/resource-explorer-put.png
[resourceexplorersinksconfig]: ./media/profiler-vm/resource-explorer-sinks-config.png
[wadextension]: ./media/profiler-vm/wad-extension.png


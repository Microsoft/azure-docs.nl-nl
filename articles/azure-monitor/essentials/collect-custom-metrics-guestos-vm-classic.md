---
title: Gegevens van klassieke Windows-VM'S naar Azure Monitor Data Base voor metrische gegevens verzenden
description: De metrische gegevens van het gast besturingssysteem verzenden naar het Azure Monitor van een virtuele Windows-machine (klassiek)
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.openlocfilehash: dcb2c0ceb3092bb6905ac1eb9fc58093da935f2f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102048978"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metrics-database-for-a-windows-virtual-machine-classic"></a>Metrische gegevens van het gast besturingssysteem verzenden naar de data base van de Azure Monitor metrics voor een virtuele Windows-machine (klassiek)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Met de [uitbrei ding voor de Azure monitor diagnostische gegevens](../agents/diagnostics-extension-overview.md) (bekend als ' wad ' of ' diagnostiek ') kunt u metrische gegevens en logboeken verzamelen van het gast besturingssysteem (gast besturingssysteem) dat wordt uitgevoerd als onderdeel van een virtuele machine, Cloud service of service Fabric cluster. De uitbrei ding kan telemetrie verzenden naar een [groot aantal verschillende locaties.](../data-platform.md?toc=%2fazure%2fazure-monitor%2ftoc.json)

In dit artikel wordt het proces beschreven voor het verzenden van de prestatie gegevens voor het gast besturingssysteem voor een Windows-virtuele machine (klassiek) naar de data base van de Azure Monitor metriek. Te beginnen met diagnostische gegevens van versie 1,11, kunt u metrische gegevens rechtstreeks naar de opslag voor metrische gegevens van Azure Monitor schrijven, waar de metrische gegevens van het standaard platform al zijn verzameld. 

Door ze op deze locatie op te slaan, hebt u toegang tot dezelfde acties als voor platform metrische gegevens. Acties omvatten bijna realtime waarschuwingen, grafieken, route ring, toegang vanaf een REST API en meer. In het verleden schreef de diagnostische uitbrei ding naar Azure Storage, maar niet naar de Azure Monitor gegevens opslag. 

Het proces dat wordt beschreven in dit artikel, werkt alleen op klassieke virtuele machines waarop het Windows-besturings systeem wordt uitgevoerd.

## <a name="prerequisites"></a>Vereisten

- U moet een [service beheerder of mede beheerder](../../cost-management-billing/manage/add-change-subscription-administrator.md) zijn voor uw Azure-abonnement. 

- Uw abonnement moet zijn geregistreerd bij [micro soft. Insights](../../azure-resource-manager/management/resource-providers-and-types.md). 

- U moet [Azure PowerShell](/powershell/azure) of [Azure Cloud shell](../../cloud-shell/overview.md) hebben geïnstalleerd.

- De VM-resource moet zich in een regio bevinden [die aangepaste metrische gegevens ondersteunt](./metrics-custom-overview.md#supported-regions).

## <a name="create-a-classic-virtual-machine-and-storage-account"></a>Een klassieke virtuele machine en een opslag account maken

1. Maak een klassieke virtuele machine met behulp van de Azure Portal.
   ![Klassieke virtuele machine maken](./media/collect-custom-metrics-guestos-vm-classic/create-classic-vm.png)

1. Wanneer u deze virtuele machine maakt, kiest u de optie voor het maken van een nieuw klassiek opslag account. We gebruiken dit opslag account in latere stappen.

1. Ga in het Azure Portal naar de Blade resource voor **opslag accounts** . Selecteer **sleutels** en noteer de naam van het opslag account en de sleutel van het opslag account. U hebt deze informatie nodig in latere stappen.
   ![Toegangssleutels voor opslag](./media/collect-custom-metrics-guestos-vm-classic/storage-access-keys.png)

## <a name="create-a-service-principal"></a>Een service-principal maken

Maak een Service-Principal in uw Azure Active Directory-Tenant met behulp van de instructies in [Create a Service Principal](../../active-directory/develop/howto-create-service-principal-portal.md). Let op het volgende tijdens dit proces: 
- Maak een nieuw client geheim voor deze app.
- Sla de sleutel en de client-ID op voor gebruik in latere stappen.

Geef deze app ' bewaking metrische gegevens Uitgever ' door aan de resource waarvoor u metrische gegevens wilt verzenden. U kunt een resource groep of een volledig abonnement gebruiken.  

> [!NOTE]
> De diagnostische uitbrei ding maakt gebruik van de service-principal voor het verifiëren van Azure Monitor en het verzenden van metrische gegevens voor uw klassieke VM.

## <a name="author-diagnostics-extension-configuration"></a>Configuratie van de extensie voor diagnostische gegevens van auteur

1. Bereid het configuratie bestand voor de diagnostische extensie voor. Dit bestand bepaalt welke logboeken en prestatie meter items de diagnostische uitbrei ding moet worden verzameld voor uw klassieke VM. Hier volgt een voorbeeld:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="applicationInsights.errors">
            <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" />
            <Directories scheduledTransferPeriod="PT1M">
                <IISLogs containerName="wad-iis-logfiles" />
                <FailedRequestLogs containerName="wad-failedrequestlogs" />
            </Directories>
            <PerformanceCounters scheduledTransferPeriod="PT1M">
                <PerformanceCounterConfiguration counterSpecifier="\Processor(*)\% Processor Time" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(*)\Disk Read Bytes/sec" sampleRate="PT15S" />
            </PerformanceCounters>
            <WindowsEventLog scheduledTransferPeriod="PT1M">
                <DataSource name="Application!*[System[(Level=1 or Level=2 or Level=3)]]" />
                <DataSource name="Windows Azure!*[System[(Level=1 or Level=2 or Level=3 or Level=4)]]" />
            </WindowsEventLog>
            <CrashDumps>
                <CrashDumpConfiguration processName="WaIISHost.exe" />
                <CrashDumpConfiguration processName="WaWorkerHost.exe" />
                <CrashDumpConfiguration processName="w3wp.exe" />
            </CrashDumps>
            <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Error" />
            <Metrics resourceId="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicCompute/virtualMachines/MyClassicVM">
                <MetricAggregation scheduledTransferPeriod="PT1M" />
                <MetricAggregation scheduledTransferPeriod="PT1H" />
            </Metrics>
        </DiagnosticMonitorConfiguration>
        <SinksConfig>
        </SinksConfig>
        </WadCfg>
        <StorageAccount />
    </PublicConfig>
    <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <StorageAccount name="" endpoint="" />
    </PrivateConfig>
    <IsEnabled>true</IsEnabled>
    </DiagnosticsConfiguration>
    ```
1. Geef in de sectie ' SinksConfig ' van het diagnostische bestand als volgt een nieuwe Azure Monitor-Sink op:

    ```xml
    <SinksConfig>
        <Sink name="AzMonSink">
            <AzureMonitor>
                <ResourceId>Provide the resource ID of your classic VM </ResourceId>
                <Region>The region your VM is deployed in</Region>
            </AzureMonitor>
        </Sink>
    </SinksConfig>
    ```

1. In het gedeelte van het configuratie bestand waarin de lijst met prestatie meter items die moeten worden verzameld, worden de prestatie meter items gerouteerd naar de Azure Monitor sink "AzMonSink".

    ```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
    ```

1. Definieer in de persoonlijke configuratie het Azure Monitor-account. Voeg vervolgens de gegevens van de Service-Principal toe om gegevens te verzenden.

    ```xml
    <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <StorageAccount name="" endpoint="" />
        <AzureMonitorAccount>
            <ServicePrincipalMeta>
                <PrincipalId>clientId for your service principal</PrincipalId>
                <Secret>client secret of your service principal</Secret>
            </ServicePrincipalMeta>
        </AzureMonitorAccount>
    </PrivateConfig>
    ```

1. Sla dit bestand lokaal op.

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>De diagnostische uitbrei ding implementeren voor uw Cloud service

1. Start Power shell en meld u aan.

    ```powershell
    Login-AzAccount
    ```

1. Begin met het instellen van de context voor uw klassieke virtuele machine.

    ```powershell
    $VM = Get-AzureVM -ServiceName <VM’s Service_Name> -Name <VM Name>
    ```

1. Stel de context in van het klassieke opslag account dat is gemaakt met de virtuele machine.

    ```powershell
    $StorageContext = New-AzStorageContext -StorageAccountName <name of your storage account from earlier steps> -storageaccountkey "<storage account key from earlier steps>"
    ```

1.  Stel het pad naar de diagnostische bestanden in op een variabele met behulp van de volgende opdracht:

    ```powershell
    $diagconfig = “<path of the diagnostics configuration file with the Azure Monitor sink configured>”
    ```

1.  Bereid de update voor uw klassieke VM voor met het diagnostische bestand waarvan de Azure Monitor-sink is geconfigureerd.

    ```powershell
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $diagconfig -VM $VM -StorageContext $Storage_Context
    ```

1.  Implementeer de update op uw virtuele machine door de volgende opdracht uit te voeren:

    ```powershell
    Update-AzureVM -ServiceName "ClassicVMWAD7216" -Name "ClassicVMWAD" -VM $VM_Update.VM
    ```

> [!NOTE]
> Het is nog verplicht een opslag account op te geven als onderdeel van de installatie van de diagnostische uitbrei ding. Alle logboeken of prestatie meter items die zijn opgegeven in het configuratie bestand voor diagnostische gegevens worden naar het opgegeven opslag account geschreven.

## <a name="plot-the-metrics-in-the-azure-portal"></a>De metrische gegevens in het Azure Portal uitzetten

1.  Ga naar Azure Portal. 

1.  Selecteer in het menu links de optie **monitor.**

1.  Selecteer **metrische gegevens** op de Blade **monitor** .

    ![Gegevens navigeren](./media/collect-custom-metrics-guestos-vm-classic/navigate-metrics.png)

1. Selecteer in de vervolg keuzelijst resources uw klassieke VM.

1. Selecteer in de vervolg keuzelijst naam ruimten de optie **Azure. VM. Windows. gast**.

1. Selecteer in de vervolg keuzelijst metrische gegevens **tussen geheugen\toegewezen bytes die in gebruik** zijn.
   ![Metrische gegevens tekenen](./media/collect-custom-metrics-guestos-vm-classic/plot-metrics.png)


## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [aangepaste metrische gegevens](./metrics-custom-overview.md).
---
title: Gegevens van klassieke Cloud Services naar Azure Monitor Data Base voor metrische gegevens verzenden
description: Hierin wordt het proces beschreven voor het verzenden van de prestatie gegevens voor het gast besturingssysteem voor Azure Classic Cloud Services naar de Azure Monitor metrische opslag.
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.openlocfilehash: 0ef9d8118a8ff1d9fdd69566dd60033f5847f810
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102048956"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-classic-cloud-services"></a>Metrische gegevens van het gast besturingssysteem verzenden naar de klassieke Azure Monitor voor metrische gegevens Cloud Services 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Met de Azure Monitor [Diagnostics-extensie](../agents/diagnostics-extension-overview.md)kunt u metrische gegevens en logboeken verzamelen van het gast besturingssysteem (gast besturingssysteem) dat wordt uitgevoerd als onderdeel van een virtuele machine, Cloud service of service Fabric cluster. De uitbrei ding kan telemetrie verzenden naar een [groot aantal verschillende locaties.](../data-platform.md?toc=%2fazure%2fazure-monitor%2ftoc.json)

In dit artikel wordt het proces beschreven voor het verzenden van gegevens over prestaties van gast besturingssystemen voor klassieke Azure-Cloud Services naar de Azure Monitor metrische opslag. Te beginnen met diagnostische gegevens van versie 1,11, kunt u metrische gegevens rechtstreeks naar de opslag voor metrische gegevens van Azure Monitor schrijven, waar de metrische gegevens van het standaard platform al zijn verzameld. 

Door ze op deze locatie op te slaan, hebt u toegang tot dezelfde acties die u kunt uitvoeren voor de metrische gegevens van het platform. Acties omvatten bijna realtime waarschuwingen, grafieken, route ring, toegang vanaf een REST API en meer.  In het verleden schreef de diagnostische uitbrei ding naar Azure Storage, maar niet naar de Azure Monitor gegevens opslag.  

Het proces dat wordt beschreven in dit artikel, werkt alleen voor prestatie meter items in azure Cloud Services. Deze functie werkt niet voor andere aangepaste metrische gegevens. 

## <a name="prerequisites"></a>Vereisten

- U moet een [service beheerder of mede beheerder](../../cost-management-billing/manage/add-change-subscription-administrator.md) zijn voor uw Azure-abonnement. 

- Uw abonnement moet zijn geregistreerd bij [micro soft. Insights](../../azure-resource-manager/management/resource-providers-and-types.md). 

- U moet [Azure PowerShell](/powershell/azure) of [Azure Cloud shell](../../cloud-shell/overview.md) hebben geïnstalleerd.

- Uw Cloud service moet zich in een [regio bevinden die aangepaste metrische gegevens ondersteunt](./metrics-custom-overview.md#supported-regions).

## <a name="provision-a-cloud-service-and-storage-account"></a>Een Cloud service-en opslag account inrichten 

1. Maak en implementeer een klassieke Cloud service. Een voor beeld van een klassieke Cloud Services toepassing en implementatie vindt u in aan de [slag met Azure Cloud Services en ASP.net](../../cloud-services/cloud-services-dotnet-get-started.md). 

2. U kunt een bestaand opslag account gebruiken of een nieuw opslag account implementeren. Het is het beste als het opslag account zich in dezelfde regio bevindt als de klassieke Cloud service die u hebt gemaakt. Ga in het Azure Portal naar de Blade resource voor **opslag accounts** en selecteer vervolgens **sleutels**. Noteer de naam van het opslag account en de sleutel van het opslag account. U hebt deze informatie in een latere stap nodig.

   ![Opslagaccountsleutels](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/storage-keys.png)

## <a name="create-a-service-principal"></a>Een service-principal maken 

Maak een Service-Principal in uw Azure Active Directory-Tenant met behulp van de instructies in de [Portal gebruiken om een Azure Active Directory toepassing en Service-Principal te maken die toegang heeft tot resources](../../active-directory/develop/howto-create-service-principal-portal.md). Let op het volgende tijdens dit proces: 

- U kunt een wille keurige URL voor de aanmeldings-URL plaatsen.  
- Maak een nieuw client geheim voor deze app.  
- Sla de sleutel en de client-ID op voor gebruik in latere stappen.  

Geef de app die u hebt gemaakt in de vorige stap *bewaking van metrische* machtigingen voor de uitgever aan de resource waarvoor u metrische gegevens wilt verzenden. Als u van plan bent om de app te gebruiken voor het verzenden van aangepaste metrische gegevens voor een groot aantal resources, kunt u deze machtigingen verlenen aan de resource groep of het abonnements niveau.  

> [!NOTE]
> De diagnostische uitbrei ding maakt gebruik van de service-principal voor het verifiëren van Azure Monitor en het verzenden van metrische gegevens voor uw Cloud service.

## <a name="author-diagnostics-extension-configuration"></a>Configuratie van de extensie voor diagnostische gegevens van auteur 

Bereid het configuratie bestand voor de diagnostische extensie voor. Dit bestand bepaalt welke logboeken en prestatie meter items de diagnostische uitbrei ding moet worden verzameld voor uw Cloud service. Hier volgt een voor beeld van een configuratie bestand voor diagnostische gegevens:  

```XML
<?xml version="1.0" encoding="utf-8"?> 
<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
    <WadCfg> 
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096"> 
        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" /> 
        <Directories scheduledTransferPeriod="PT1M"> 
          <IISLogs containerName="wad-iis-logfiles" /> 
          <FailedRequestLogs containerName="wad-failedrequestlogs" /> 
        </Directories> 
        <PerformanceCounters scheduledTransferPeriod="PT1M"> 
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Page Faults/sec" sampleRate="PT15S" /> 
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

Geef in de sectie ' SinksConfig ' van het diagnostische bestand een nieuwe Azure Monitor-Sink op: 

```XML
  <SinksConfig> 
    <Sink name="AzMonSink"> 
    <AzureMonitor> 
      <ResourceId>-Provide ClassicCloudService’s Resource ID-</ResourceId> 
      <Region>-Azure Region your Cloud Service is deployed in-</Region> 
    </AzureMonitor> 
    </Sink> 
  </SinksConfig> 
```

Azure Monitor Voeg in de sectie van uw configuratie bestand een lijst toe met de prestatie meter items die u wilt verzamelen. Dit item zorgt ervoor dat alle prestatie meter items die u hebt opgegeven, worden doorgestuurd naar Azure Monitor als meet waarden. U kunt prestatie meter items toevoegen of verwijderen op basis van uw behoeften. 

```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
```

Voeg ten slotte in de persoonlijke configuratie een sectie voor het *Azure monitor-account* toe. Voer de client-ID en het geheim van de Service-Principal in die u eerder hebt gemaakt. 

```XML
<PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <StorageAccount name="" endpoint="" /> 
    <AzureMonitorAccount> 
      <ServicePrincipalMeta> 
        <PrincipalId>clientId from step 3</PrincipalId> 
        <Secret>client secret from step 3</Secret> 
      </ServicePrincipalMeta> 
    </AzureMonitorAccount> 
</PrivateConfig> 
```

Sla dit diagnostische bestand lokaal op.  

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>De diagnostische uitbrei ding implementeren voor uw Cloud service 

Start Power shell en meld u aan bij Azure. 

```powershell
Login-AzAccount 
```

Gebruik de volgende opdrachten om de gegevens op te slaan van het opslag account dat u eerder hebt gemaakt. 

```powershell
$storage_account = <name of your storage account from step 3> 
$storage_keys = <storage account key from step 3> 
```

U kunt ook het pad naar de diagnostische bestanden instellen op een variabele met behulp van de volgende opdracht:

```powershell
$diagconfig = “<path of the Diagnostics configuration file with the Azure Monitor sink configured>” 
```

Implementeer de diagnostische extensie voor uw Cloud service met het diagnostische bestand met de Azure Monitor sink die is geconfigureerd met de volgende opdracht:  

```powershell
Set-AzureServiceDiagnosticsExtension -ServiceName <classicCloudServiceName> -StorageAccountName $storage_account -StorageAccountKey $storage_keys -DiagnosticsConfigurationPath $diagconfig 
```

> [!NOTE] 
> Het is nog steeds verplicht een opslag account op te geven als onderdeel van de installatie van de diagnostische uitbrei ding. Alle logboeken of prestatie meter items die zijn opgegeven in het configuratie bestand voor diagnostische gegevens worden naar het opgegeven opslag account geschreven.  

## <a name="plot-metrics-in-the-azure-portal"></a>Metrische gegevens in het Azure Portal tekenen 

1. Ga naar Azure Portal. 

   ![Scherm afbeelding toont de Azure Portal met monitor en vervolgens de geselecteerde metrische gegevens.](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/navigate-metrics.png)

2. Selecteer in het menu links de optie **monitor.**

3. Op de Blade **monitor** selecteert u het tabblad **voor beeld van metrische gegevens** .

4. Selecteer in de vervolg keuzelijst resources uw klassieke Cloud service.

5. Selecteer in de vervolg keuzelijst naam ruimten de optie **Azure. VM. Windows. gast**. 

6. Selecteer in de vervolg keuzelijst metrische gegevens **tussen geheugen\toegewezen bytes die in gebruik** zijn. 

U gebruikt de functies voor het filteren en splitsen van dimensies om het totale geheugen te bekijken dat wordt gebruikt door een specifieke rol of rolinstantie. 

 ![In de scherm afbeelding worden metrische gegevens weer gegeven.](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/metrics-graph.png)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [aangepaste metrische gegevens](./metrics-custom-overview.md).
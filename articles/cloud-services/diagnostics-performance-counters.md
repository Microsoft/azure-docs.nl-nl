---
title: Verzamelen van prestatie meter items in azure Cloud Services (klassiek) | Microsoft Docs
description: Meer informatie over het detecteren, gebruiken en maken van prestatie meter items in Cloud Services met Azure Diagnostics en Application Insights.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 16b54e8a59eb42c6e2351d37ec0a29d775161493
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98739833"
---
# <a name="collect-performance-counters-for-your-azure-cloud-service-classic"></a>Prestatie meter items verzamelen voor uw Azure-Cloud service (klassiek)

> [!IMPORTANT]
> [Azure Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md) is een nieuw implementatie model op basis van Azure Resource Manager voor het Azure Cloud Services-product.Met deze wijziging worden Azure-Cloud Services die worden uitgevoerd op het Azure Service Manager gebaseerde implementatie model, de naam van Cloud Services (klassiek) gewijzigd en moeten alle nieuwe implementaties [Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md)gebruiken.

Prestatie meter items bieden u een manier om te volgen hoe goed uw toepassing en de host worden uitgevoerd. Windows Server biedt veel verschillende prestatie meter items die betrekking hebben op hardware, toepassingen, het besturings systeem en meer. Door prestatie meter items te verzamelen en te verzenden naar Azure, kunt u deze informatie analyseren om betere beslissingen te nemen. 

## <a name="discover-available-counters"></a>Beschik bare items detecteren

Een prestatie meter item bestaat uit twee delen: een set naam (ook wel een categorie genoemd) en een of meer tellers. U kunt Power shell gebruiken om een lijst met beschik bare prestatie meter items op te halen:

```powershell
Get-Counter -ListSet * | Select-Object CounterSetName, Paths | Sort-Object CounterSetName

CounterSetName                                  Paths
--------------                                  -----
.NET CLR Data                                   {\.NET CLR Data(*)\SqlClient...
.NET CLR Exceptions                             {\.NET CLR Exceptions(*)\# o...
.NET CLR Interop                                {\.NET CLR Interop(*)\# of C...
.NET CLR Jit                                    {\.NET CLR Jit(*)\# of Metho...
.NET Data Provider for Oracle                   {\.NET Data Provider for Ora...
.NET Data Provider for SqlServer                {\.NET Data Provider for Sql...
.NET Memory Cache 4.0                           {\.NET Memory Cache 4.0(*)\C...
AppV Client Streamed Data Percentage            {\AppV Client Streamed Data ...
ASP.NET                                         {\ASP.NET\Application Restar...
ASP.NET Apps v4.0.30319                         {\ASP.NET Apps v4.0.30319(*)...
ASP.NET State Service                           {\ASP.NET State Service\Stat...
ASP.NET v2.0.50727                              {\ASP.NET v2.0.50727\Applica...
ASP.NET v4.0.30319                              {\ASP.NET v4.0.30319\Applica...
Authorization Manager Applications              {\Authorization Manager Appl...

#... results cut to save space ...
```

De `CounterSetName` eigenschap vertegenwoordigt een set (of categorie) en is een goede indicatie van de relatie met de prestatie meter items. De `Paths` eigenschap vertegenwoordigt een verzameling tellers voor een set. U kunt ook de `Description` eigenschap ophalen voor meer informatie over de set tellers.

Als u alle tellers voor een set wilt ophalen, gebruikt u de `CounterSetName` waarde en vouwt u de `Paths` verzameling uit. Elk pad is een prestatie meter item waarmee u kunt zoeken. Als u bijvoorbeeld de beschik bare tellers wilt ophalen die gerelateerd zijn aan de `Processor` set, vouwt u de `Paths` verzameling uit:

```powershell
Get-Counter -ListSet * | Where-Object CounterSetName -eq "Processor" | Select -ExpandProperty Paths

\Processor(*)\% Processor Time
\Processor(*)\% User Time
\Processor(*)\% Privileged Time
\Processor(*)\Interrupts/sec
\Processor(*)\% DPC Time
\Processor(*)\% Interrupt Time
\Processor(*)\DPCs Queued/sec
\Processor(*)\DPC Rate
\Processor(*)\% Idle Time
\Processor(*)\% C1 Time
\Processor(*)\% C2 Time
\Processor(*)\% C3 Time
\Processor(*)\C1 Transitions/sec
\Processor(*)\C2 Transitions/sec
\Processor(*)\C3 Transitions/sec
```

Deze afzonderlijke item paden kunnen worden toegevoegd aan het diagnostische Framework dat door uw Cloud service wordt gebruikt. Zie [een itempad opgeven](/windows/win32/perfctrs/specifying-a-counter-path)voor meer informatie over de constructie van een pad voor prestatie meter items.

## <a name="collect-a-performance-counter"></a>Een prestatie meter item verzamelen

Een prestatie meter item kan worden toegevoegd aan uw Cloud service voor zowel Azure Diagnostics als Application Insights.

### <a name="application-insights"></a>Application Insights

Met Azure-toepassing Insights voor Cloud Services kunt u opgeven welke prestatie meter items u wilt verzamelen. Nadat u [Application Insights aan uw project hebt toegevoegd](../azure-monitor/app/cloudservices.md#sdk), wordt er een configuratie bestand met de naam **ApplicationInsights.config** toegevoegd aan uw Visual Studio-project. Dit configuratie bestand definieert welk type informatie Application Insights worden verzameld en verzonden naar Azure.

Open het **ApplicationInsights.config** -bestand en zoek het element **ApplicationInsights**  >  **TelemetryModules** . Elk `<Add>` onderliggend element definieert een type telemetrie dat moet worden verzameld, samen met de configuratie. Het module type telemetrie van het prestatie meter item is `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector` . Als dit element al is gedefinieerd, moet u het niet een tweede keer toevoegen. Elk prestatie meter item dat moet worden verzameld, wordt gedefinieerd onder een knoop punt met de naam `<Counters>` . Hier volgt een voor beeld van het verzamelen van prestatie meter items voor stations:

```xml
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">

  <TelemetryModules>

    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <Add PerformanceCounter="\LogicalDisk(C:)\Disk Write Bytes/sec" ReportAs="Disk write (C:)" />
        <Add PerformanceCounter="\LogicalDisk(C:)\Disk Read Bytes/sec" ReportAs="Disk read (C:)" />
      </Counters>
    </Add>

  </TelemetryModules>

<!-- ... cut to save space ... -->
```

Elk prestatie meter item wordt weer gegeven als een- `<Add>` element onder `<Counters>` . Het `PerformanceCounter` kenmerk bepaalt welk prestatie meter item moet worden verzameld. Het `ReportAs` kenmerk is de titel die moet worden weer gegeven in de Azure portal voor het prestatie meter item. Elk prestatie meter item dat u verzamelt, wordt in een categorie met de naam **Custom** in de portal geplaatst. In tegens telling tot Azure Diagnostics, kunt u het interval voor het verzamelen van deze prestatie meter items niet instellen en naar Azure worden verzonden. Met Application Insights worden prestatie meter items verzameld en verzonden per minuut. 

Application Insights verzamelt automatisch de volgende prestatie meter items:

* \Process(?? APP_WIN32_PROC?) \% Processor tijd
* \Memory\Available Bytes
* \.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec
* \Process(??APP_WIN32_PROC??)\Private Bytes
* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
* \Processor(_Total)\% Processor Time

Zie [systeem prestatie meter items in Application Insights](../azure-monitor/app/performance-counters.md) en [Application Insights voor Azure Cloud Services](../azure-monitor/app/cloudservices.md#performance-counters)voor meer informatie.

### <a name="azure-diagnostics"></a>Azure Diagnostics

> [!IMPORTANT]
> Hoewel al deze gegevens worden geaggregeerd naar het opslag account, biedt de portal **geen** systeem eigen manier om de gegevens in een grafiek te plaatsen. Het is sterk aan te raden om een andere diagnostische service, zoals Application Insights, te integreren in uw toepassing.

Met de Azure Diagnostics extensie voor Cloud Services kunt u opgeven welke prestatie meter items u wilt verzamelen. Zie [overzicht van Cloud Service-bewaking](cloud-services-how-to-monitor.md#setup-diagnostics-extension)om Azure Diagnostics in te stellen.

De prestatie meter items die u wilt verzamelen, worden gedefinieerd in het bestand **Diagnostics. wadcfgx** . Open dit bestand (is gedefinieerd per rol) in Visual Studio en zoek het **DiagnosticsConfiguration**  >  **PublicConfig**  >  **WadCfg**  >  **DiagnosticMonitorConfiguration**  >  **Performance Counters** -element. Een nieuw **PerformanceCounterConfiguration** -element als onderliggend item toevoegen. Dit element heeft twee kenmerken: `counterSpecifier` en `sampleRate` . `counterSpecifier`Met het kenmerk wordt gedefinieerd welk prestatie meter item van het systeem (beschreven in de vorige sectie) moet worden verzameld. De `sampleRate` waarde geeft aan hoe vaak die waarde wordt gecontroleerd. Als geheel worden alle prestatie meter items overgebracht naar Azure volgens de `PerformanceCounters` kenmerk waarde van het bovenliggende element `scheduledTransferPeriod` .

`PerformanceCounters`Zie het [Azure Diagnostics schema](../azure-monitor/platform/diagnostics-extension-schema-windows.md#performancecounters-element)voor meer informatie over het schema-element.

De door het kenmerk gedefinieerde periode `sampleRate` maakt gebruik van het gegevens type XML-duur om aan te geven hoe vaak het prestatie meter item wordt gecontroleerd. In het onderstaande voor beeld is de rente ingesteld op `PT3M` , wat betekent `[P]eriod[T]ime[3][M]inutes` : om de drie minuten.

Voor meer informatie over hoe de `sampleRate` en `scheduledTransferPeriod` zijn gedefinieerd, raadpleegt u de sectie **duur gegevens type** in de hand leiding voor datum [-en tijd typen van w3 XML](https://www.w3schools.com/XML/schema_dtypes_date.asp) .

```xml
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig>
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096">

        <!-- ... cut to save space ... -->

        <PerformanceCounters scheduledTransferPeriod="PT1M">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />

          <!-- This is a new perf counter which will track the C: disk read activity in bytes per second, every minute -->
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(C:)\Disk Read Bytes/sec" sampleRate="PT1M" />

        </PerformanceCounters>
      </DiagnosticMonitorConfiguration>
    </WadCfg>
    
    <!-- ... cut to save space ... -->

  </PublicConfig>
</DiagnosticsConfiguration>
```

## <a name="create-a-new-perf-counter"></a>Een nieuw prestatie meter item maken

U kunt een nieuw prestatie meter item maken en gebruiken door uw code. De code waarmee een nieuw prestatie meter item wordt gemaakt, moet met verhoogde bevoegdheden worden uitgevoerd, anders mislukt dit. De `OnStart` opstart code van de Cloud service kan het prestatie meter item maken, waardoor u de rol in een verhoogde context moet uitvoeren. U kunt ook een opstart taak maken die verhoogde bevoegdheden uitvoert en het prestatie meter item maakt. Zie [opstart taken voor een Cloud service configureren en uitvoeren](cloud-services-startup-tasks.md)voor meer informatie over opstart taken.

Als u uw rol wilt configureren om een verhoogde bevoegdheid uit te voeren, voegt `<Runtime>` u een element toe aan het [. csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) -bestand.

```xml
<ServiceDefinition name="CloudServiceLoadTesting" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Large">

    <!-- ... cut to save space ... -->

    <Runtime executionContext="elevated">
      
    </Runtime>

    <!-- ... cut to save space ... -->

  </WorkerRole>
</ServiceDefinition>
```

U kunt een nieuw prestatie meter item maken en registreren met een paar regels code. Gebruik de `System.Diagnostics.PerformanceCounterCategory.Create` methode-overload waarmee zowel de categorie als de teller worden gemaakt. Met de volgende code wordt eerst gecontroleerd of de categorie bestaat en als deze ontbreekt, worden zowel de categorie als de teller gemaakt.

```csharp
using System.Diagnostics;
using Microsoft.WindowsAzure;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRoleWithSBQueue1
{
    public class WorkerRole : RoleEntryPoint
    {
        // Perf counter variable representing times service was used.
        private PerformanceCounter counterServiceUsed;

        public override bool OnStart()
        {
            // ... Other startup code here ...

            // Define the category and counter names.
            string perfCounterCatName = "MyService";
            string perfCounterName = "Times Used";

            // Create the counter if needed. Our counter category only has a single counter.
            // Both the category and counter are created in the same method call.
            if (!PerformanceCounterCategory.Exists(perfCounterCatName))
            {
                PerformanceCounterCategory.Create(perfCounterCatName, "Collects information about the cloud service.", 
                                                  PerformanceCounterCategoryType.SingleInstance, 
                                                  perfCounterName, "How many times the cloud service was used.");
            }

            // Get reference to our counter
            counterServiceUsed = new PerformanceCounter(perfCounterCatName, perfCounterName);
            counterServiceUsed.ReadOnly = false;
            
            return base.OnStart();
        }

        // ... cut class code to save space
    }
}
```

Wanneer u de teller wilt gebruiken, moet u de `Increment` or-methode aanroepen `IncrementBy` .

```csharp
// Increase the counter by 1
counterServiceUsed.Increment();
```

Nu uw toepassing gebruikmaakt van uw aangepaste teller, moet u Azure Diagnostics of Application Insights configureren om de teller bij te houden.


### <a name="application-insights"></a>Application Insights

Zoals eerder is vermeld, worden de prestatie meter items voor Application Insights gedefinieerd in het **ApplicationInsights.config** bestand. Open **ApplicationInsights.config** en zoek het element **ApplicationInsights**  >  **TelemetryModules**  >  **add**  >  **Counters** . Maak een `<Add>` onderliggend element en stel het `PerformanceCounter` kenmerk in op de categorie en de naam van het prestatie meter item dat u in uw code hebt gemaakt. Stel het `ReportAs` kenmerk in op een beschrijvende naam die u wilt weer geven in de portal.

```xml
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">

  <TelemetryModules>

    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <!-- ... cut other perf counters to save space ... -->

        <!-- This new perf counter matches the [category name]\[counter name] defined in your code -->
        <Add PerformanceCounter="\MyService\Times Used" ReportAs="Service used counter" />
      </Counters>
    </Add>

  </TelemetryModules>

<!-- ... cut to save space ... -->
```

### <a name="azure-diagnostics"></a>Azure Diagnostics

Zoals eerder is vermeld, worden de prestatie meter items die u wilt verzamelen gedefinieerd in het bestand **Diagnostics. wadcfgx** . Open dit bestand (is gedefinieerd per rol) in Visual Studio en zoek het **DiagnosticsConfiguration**  >  **PublicConfig**  >  **WadCfg**  >  **DiagnosticMonitorConfiguration**  >  **Performance Counters** -element. Een nieuw **PerformanceCounterConfiguration** -element als onderliggend item toevoegen. Stel het `counterSpecifier` kenmerk in op de categorie en de naam van het prestatie meter item dat u in uw code hebt gemaakt. 

```xml
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig>
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096">

        <!-- ... cut to save space ... -->

        <PerformanceCounters scheduledTransferPeriod="PT1M">
          <!-- ... cut other perf counters to save space ... -->
          
          <!-- This new perf counter matches the [category name]\[counter name] defined in your code -->
          <PerformanceCounterConfiguration counterSpecifier="\MyService\Times Used" sampleRate="PT1M" />

        </PerformanceCounters>
      </DiagnosticMonitorConfiguration>
    </WadCfg>
    
    <!-- ... cut to save space ... -->

  </PublicConfig>
</DiagnosticsConfiguration>
```

## <a name="more-information"></a>Meer informatie

- [Application Insights voor Azure Cloud Services](../azure-monitor/app/cloudservices.md#performance-counters)
- [Systeem prestatie meter items in Application Insights](../azure-monitor/app/performance-counters.md)
- [Een itempad opgeven](/windows/win32/perfctrs/specifying-a-counter-path)
- [Azure Diagnostics schema: prestatie meter items](../azure-monitor/platform/diagnostics-extension-schema-windows.md#performancecounters-element)
---
title: Problemen oplossen met Azure-toepassing Insights-agent en bekende problemen | Microsoft Docs
description: De bekende problemen met Application Insights agent en voor beelden van probleem oplossing. Bewaak de prestaties van de website zonder de website opnieuw te implementeren. Werkt met ASP.NET-Web-apps die on-premises worden gehost, in Vm's of op Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 2641218fa9ddef65c45f2f1a9c9ce807cef35048
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105642733"
---
# <a name="troubleshooting-application-insights-agent-formerly-named-status-monitor-v2"></a>Problemen met Application Insights agent oplossen (voorheen Status Monitor v2 genoemd)

Wanneer u controle inschakelt, kunt u problemen ondervinden die het verzamelen van gegevens verhinderen.
In dit artikel worden alle bekende problemen beschreven en vindt u voor beelden van problemen oplossen.

## <a name="known-issues"></a>Bekende problemen

### <a name="conflicting-dlls-in-an-apps-bin-directory"></a>Conflicterende dll-bestanden in de bin-map van een app

Als een of meer van deze DLL-bestanden aanwezig zijn in de bin-map, kan de bewaking mislukken:

- Microsoft.ApplicationInsights.dll
- Microsoft.AspNet.TelemetryCorrelation.dll
- System.Diagnostics.DiagnosticSource.dll

Sommige van deze DLL-bestanden zijn opgenomen in de Visual Studio-sjablonen voor standaard-apps, zelfs als deze niet worden gebruikt door uw app.
U kunt hulpprogram ma's voor probleem oplossing gebruiken om het gedrag van Symptomatic te bekijken:

- PerfView
    ```
    ThreadID="7,500" 
    ProcessorNumber="0" 
    msg="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ExtVer="2.8.13.5972" 
    SubscriptionId="" 
    AppName="" 
    FormattedMessage="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ```

- IISReset-en app-belasting (zonder telemetrie). Onderzoeken met Sysinternals (Handle.exe en ListDLLs.exe):
    ```
    .\handle64.exe -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
    E54: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

    .\Listdlls64.exe w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"
    0x0000000009be0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
    0x0000000009b90000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
    0x0000000004d20000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
    ```

### <a name="powershell-versions"></a>Power shell-versies
Dit product is geschreven en getest met Power shell v 5.1.
Deze module is niet compatibel met Power shell-versie 6 of 7.
We raden u aan Power shell v 5.1 naast nieuwere versies te gebruiken. Zie [using Power shell 7 side by side with Power shell 5,1](/powershell/scripting/install/migrating-from-windows-powershell-51-to-powershell-7#using-powershell-7-side-by-side-with-windows-powershell-51)(Engelstalig) voor meer informatie.

### <a name="conflict-with-iis-shared-configuration"></a>Conflict met gedeelde IIS-configuratie

Als u een cluster van webservers hebt, kunt u gebruikmaken van een [gedeelde configuratie](/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
De HTTP module kan niet worden ingevoegd in deze gedeelde configuratie.
Voer de opdracht inschakelen op elke webserver uit om de DLL te installeren in de GAC van elke server.

Nadat u de opdracht inschakelen hebt uitgevoerd, voert u de volgende stappen uit:
1. Ga naar de gedeelde configuratiemap en zoek het applicationHost.config bestand.
2. Voeg deze regel toe aan het gedeelte modules van uw configuratie:
    ```
    <modules>
        <!-- Registered global managed http module handler. The 'Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll' must be installed in the GAC before this config is applied. -->
        <add name="ManagedHttpModuleHelper" type="Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" preCondition="managedHandler,runtimeVersionv4.0" />
    </modules>
    ```

### <a name="iis-nested-applications"></a>Geneste IIS-toepassingen

Geneste toepassingen in IIS in versie 1,0 worden niet geinstrumenteerd.

### <a name="advanced-sdk-configuration-isnt-available"></a>Geavanceerde SDK-configuratie is niet beschikbaar.

De SDK-configuratie wordt niet weer gegeven aan de eind gebruiker in versie 1,0.

    
    
## <a name="troubleshooting"></a>Problemen oplossen
    
### <a name="troubleshooting-powershell"></a>Problemen met Power shell oplossen

#### <a name="determine-which-modules-are-available"></a>Bepalen welke modules beschikbaar zijn
U kunt de `Get-Module -ListAvailable` opdracht gebruiken om te bepalen welke modules zijn geïnstalleerd.

#### <a name="import-a-module-into-the-current-session"></a>Een module in de huidige sessie importeren
Als een module niet is geladen in een Power shell-sessie, kunt u deze hand matig laden met behulp van de `Import-Module <path to psd1>` opdracht.


### <a name="troubleshooting-the-application-insights-agent-module"></a>Problemen met de module Application Insights agent oplossen

#### <a name="list-the-commands-available-in-the-application-insights-agent-module"></a>De beschik bare opdrachten in de module Application Insights agent weer geven
Voer de opdracht uit `Get-Command -Module Az.ApplicationMonitor` om de beschik bare opdrachten op te halen:

```
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Disable-ApplicationInsightsMonitoring              0.4.0      Az.ApplicationMonitor
Cmdlet          Disable-InstrumentationEngine                      0.4.0      Az.ApplicationMonitor
Cmdlet          Enable-ApplicationInsightsMonitoring               0.4.0      Az.ApplicationMonitor
Cmdlet          Enable-InstrumentationEngine                       0.4.0      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringConfig            0.4.0      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringStatus            0.4.0      Az.ApplicationMonitor
Cmdlet          Set-ApplicationInsightsMonitoringConfig            0.4.0      Az.ApplicationMonitor
Cmdlet          Start-ApplicationInsightsMonitoringTrace           0.4.0      Az.ApplicationMonitor
```

#### <a name="determine-the-current-version-of-the-application-insights-agent-module"></a>De huidige versie van de Application Insights Agent-module bepalen
Voer de `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` opdracht uit om de volgende informatie over de module weer te geven:
   - Versie van Power shell-module
   - Application Insights SDK-versie
   - Bestands paden van de Power shell-module
    
Raadpleeg de [API-naslag informatie](status-monitor-v2-api-reference.md) voor een gedetailleerde beschrijving van het gebruik van deze cmdlet.


### <a name="troubleshooting-running-processes"></a>Problemen met actieve processen oplossen

U kunt de processen op de geinstrumenteerde computer controleren om te bepalen of alle Dll's zijn geladen.
Als de bewaking werkt, moeten ten minste 12 Dll's worden geladen.

Gebruik de `Get-ApplicationInsightsMonitoringStatus -InspectProcess` opdracht om de dll's te controleren.

Raadpleeg de [API-naslag informatie](status-monitor-v2-api-reference.md) voor een gedetailleerde beschrijving van het gebruik van deze cmdlet.


### <a name="collect-etw-logs-by-using-perfview"></a>ETW-logboeken verzamelen met behulp van PerfView

#### <a name="setup"></a>Instellen

1. Down load PerfView.exe en PerfView64.exe van [github](https://github.com/Microsoft/perfview/releases).
2. Start PerfView64.exe.
3. Vouw **Geavanceerde opties** uit.
4. Schakel deze selectie vakjes uit:
    - **Telefoon**
    - **Samenvoegen**
    - **.NET-symbool verzameling**
5. Deze **extra providers** instellen: `61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,252e28f4-43f9-5771-197a-e8c7e750a984`


#### <a name="collecting-logs"></a>Logboeken verzamelen

1. Voer in een opdracht console met beheerders bevoegdheden de `iisreset /stop` opdracht uit om IIS en alle web-apps uit te scha kelen.
2. Selecteer in PerfView de optie **verzameling starten**.
3. Voer in een opdracht console met beheerders bevoegdheden de `iisreset /start` opdracht uit om IIS te starten.
4. Probeer naar uw app te bladeren.
5. Nadat de app is geladen, keert u terug naar PerfView en selecteert u **verzameling stoppen**.

### <a name="how-to-capture-full-sql-command-text"></a>Volledige SQL-opdracht tekst vastleggen

Als u de volledige SQL-opdracht tekst wilt vastleggen, moet u het applicationinsights.config bestand wijzigen met het volgende:

```xml
<Add Type="Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.AI.DependencyCollector">,
<EnableSqlCommandTextInstrumentation>true</EnableSqlCommandTextInstrumentation>
</Add>
```

## <a name="next-steps"></a>Volgende stappen

- Raadpleeg de [API-verwijzing](status-monitor-v2-overview.md#powershell-api-reference) voor meer informatie over de para meters die u mogelijk hebt gemist.
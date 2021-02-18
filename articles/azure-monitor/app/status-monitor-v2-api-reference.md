---
title: Azure-toepassing Insights-API-naslag informatie over .net agent
description: Application Insights agent API-verwijzing. Bewaak de prestaties van de website zonder de website opnieuw te implementeren. Werkt met ASP.NET-Web-apps die on-premises worden gehost, in Vm's of op Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 2278b9d70e888fa546dc64da4743b2bf5b6c45e8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100587529"
---
# <a name="azure-monitor-application-insights-agent-api-reference"></a>API-naslag informatie over Azure Monitor Application Insights agent

In dit artikel wordt een cmdlet beschreven die lid is van de [Power shell-module AZ. ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!NOTE] 
> - U hebt een instrumentatie sleutel nodig om aan de slag te gaan. Zie [een resource maken](create-new-resource.md#copy-the-instrumentation-key)voor meer informatie.
> - Voor deze cmdlet moet u onze licentie en privacyverklaring bekijken en accepteren.

> [!IMPORTANT] 
> Voor deze cmdlet is een Power shell-sessie met beheerders machtigingen en een verhoogd uitvoerings beleid vereist. Zie [Power shell uitvoeren als Administrator met een verhoogd uitvoerings beleid](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy)voor meer informatie.
> - Voor deze cmdlet moet u onze licentie en privacyverklaring bekijken en accepteren.
> - De instrumentatie-engine voegt extra overhead toe en is standaard uitgeschakeld.


## <a name="enable-instrumentationengine"></a>Enable-InstrumentationEngine

Hiermee schakelt u de instrumentatie-engine in door een aantal register sleutels in te stellen.
Start IIS opnieuw op om de wijzigingen van kracht te laten worden.

De instrumentatie-engine kan gegevens aanvullen die worden verzameld door de .NET-Sdk's.
Er worden gebeurtenissen en berichten verzameld waarmee de uitvoering van een beheerd proces wordt beschreven. Deze gebeurtenissen en berichten bevatten afhankelijkheids resultaat codes, HTTP-woorden en [SQL-opdracht tekst](asp-net-dependencies.md#advanced-sql-tracking-to-get-full-sql-query).

Schakel de instrumentatie-engine in als:
- U hebt bewaking al ingeschakeld met de cmdlet Enable, maar de instrumentatie-engine is niet ingeschakeld.
- U hebt uw app hand matig geinstrumenteerd met de .NET-Sdk's en wilt extra telemetrie verzamelen.

### <a name="examples"></a>Voorbeelden

```powershell
PS C:\> Enable-InstrumentationEngine
```

### <a name="parameters"></a>Parameters

#### <a name="-acceptlicense"></a>-AcceptLicense
**Optioneel.** Gebruik deze schakel optie om de licentie en privacyverklaring in headless installaties te accepteren.

#### <a name="-verbose"></a>-Verbose
**Algemene para meter.** Gebruik deze optie om gedetailleerde logboeken uit te voeren.

### <a name="output"></a>Uitvoer


##### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>Voorbeeld uitvoer van het inschakelen van de instrumentatie-engine

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```

## <a name="enable-applicationinsightsmonitoring"></a>Enable-ApplicationInsightsMonitoring

Hiermee wordt de bewaking van de IIS-apps op een doel computer gecodeerd.

Met deze cmdlet wordt de IIS-applicationHost.config gewijzigd en worden enkele register sleutels ingesteld.
Er wordt ook een applicationinsights.ikey.config-bestand gemaakt waarin de instrumentatie sleutel wordt gedefinieerd die door elke app wordt gebruikt.
IIS laadt de RedfieldModule bij het opstarten, waardoor de SDK van Application Insights in toepassingen wordt ingevoegd wanneer de toepassingen worden gestart.
Start IIS opnieuw op om de wijzigingen van kracht te laten worden.

Nadat u bewaking hebt ingeschakeld, raden we u aan [Live metrische gegevens](live-stream.md) te gebruiken om snel te controleren of uw app een telemetrie verzendt.

### <a name="examples"></a>Voorbeelden

#### <a name="example-with-a-single-instrumentation-key"></a>Voor beeld met één instrumentatie sleutel
In dit voor beeld worden alle apps op de huidige computer toegewezen aan één instrumentatie sleutel.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

#### <a name="example-with-an-instrumentation-key-map"></a>Voor beeld met een instrumentatie sleutel toewijzing
In dit voorbeeld:
- `MachineFilter` komt overeen met de huidige computer met behulp van het `'.*'` Joker teken.
- `AppFilter='WebAppExclude'` biedt een `null` instrumentatie sleutel. De opgegeven app wordt niet geinstrumenteerd.
- `AppFilter='WebAppOne'` Hiermee wijst u de opgegeven app een unieke instrumentatie sleutel toe.
- `AppFilter='WebAppTwo'` Hiermee wijst u de opgegeven app een unieke instrumentatie sleutel toe.
- Ten slotte `AppFilter` gebruikt het `'.*'` Joker teken ook om alle web-apps te zoeken die niet overeenkomen met de eerdere regels en een standaard instrumentatie sleutel toe te wijzen.
- Spaties worden toegevoegd voor de Lees baarheid.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})

```


### <a name="parameters"></a>Parameters

#### <a name="-instrumentationkey"></a>-InstrumentationKey
**Vereist.** Gebruik deze para meter om één instrumentatie sleutel op te geven voor gebruik door alle apps op de doel computer.

#### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Vereist.** Gebruik deze para meter om meerdere instrumentatie sleutels en een toewijzing van de instrumentatie sleutels voor elke app op te geven.
U kunt één installatie script maken voor verschillende computers door in te stellen `MachineFilter` .

> [!IMPORTANT]
> Apps komen overeen met regels in de volg orde waarin de regels worden opgegeven. Daarom moet u eerst de meest specifieke regels opgeven en de meest algemene regels als laatste.

##### <a name="schema"></a>Schema
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **MachineFilter** is een vereiste C#-regex van de naam van de computer of virtuele machine.
    - '. * ' komt overeen met alles
    - ComputerName komt alleen overeen met computers met de exacte naam opgegeven.
- **AppFilter** is een vereiste C#-regex van de naam van de IIS-site. U kunt een lijst met sites op uw server ophalen door de opdracht [Get-iissite](/powershell/module/iisadministration/get-iissite)uit te voeren.
    - '. * ' komt overeen met alles
    - ' Site naam ' komt alleen overeen met de IIS-site met de exacte naam die is opgegeven.
- **InstrumentationKey** is vereist om de bewaking van apps die overeenkomen met de voor gaande twee filters in te scha kelen.
    - Laat deze waarde leeg als u regels wilt definiëren om bewaking uit te sluiten.


#### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**Optioneel.** Gebruik deze schakel optie om de instrumentatie-engine in te scha kelen voor het verzamelen van gebeurtenissen en berichten over wat er gebeurt tijdens de uitvoering van een beheerd proces. Deze gebeurtenissen en berichten bevatten afhankelijkheids resultaat codes, HTTP-woorden en SQL-opdracht tekst.

De instrumentatie-engine voegt overhead toe en is standaard uitgeschakeld.

#### <a name="-acceptlicense"></a>-AcceptLicense
**Optioneel.** Gebruik deze schakel optie om de licentie en privacyverklaring in headless installaties te accepteren.

#### <a name="-ignoresharedconfig"></a>-IgnoreSharedConfig
Wanneer u een cluster van webservers hebt, kunt u gebruikmaken van een [gedeelde configuratie](/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
De HTTP module kan niet worden ingevoegd in deze gedeelde configuratie.
Dit script werkt niet met het bericht dat er extra installatie stappen zijn vereist.
Gebruik deze schakel optie om deze controle te negeren en door te gaan met de installatie van vereisten. Zie [bekende conflict-with-IIS-Shared-Configuration](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration) (Engelstalig) voor meer informatie

#### <a name="-verbose"></a>-Verbose
**Algemene para meter.** Gebruik deze schakel optie om gedetailleerde logboeken weer te geven.

#### <a name="-whatif"></a>-WhatIf 
**Algemene para meter.** Gebruik deze schakel optie om de invoer parameters te testen en te valideren zonder dat de bewaking werkelijk wordt ingeschakeld.

### <a name="output"></a>Uitvoer

#### <a name="example-output-from-a-successful-enablement"></a>Voorbeeld uitvoer van een geslaagde activering

```powershell
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z'
in :1,237
No element in the source document matches '/configuration/location[@path='']/system.webServer/modules/add[@name='ManagedHttpModuleHelper']'
Not executing RemoveAll (transform line 1, 546)
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'disable'
GAC Module will not be removed, since this operation might cause IIS instabilities
Configuring IIS Environment for codeless attach...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring IIS Environment for instrumentation engine...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring registry for instrumentation engine...
Successfully disabled Application Insights Status Monitor
Installing GAC module 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.0\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll'
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
Found GAC module Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z_1'
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'enable'
Configuring IIS Environment for codeless attach...
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
Updating app pool permissions...
Successfully enabled Application Insights Status Monitor
```

## <a name="disable-instrumentationengine"></a>Disable-InstrumentationEngine

Hiermee schakelt u de instrumentatie-engine uit door enkele register sleutels te verwijderen.
Start IIS opnieuw op om de wijzigingen van kracht te laten worden.

### <a name="examples"></a>Voorbeelden

```powershell
PS C:\> Disable-InstrumentationEngine
```

### <a name="parameters"></a>Parameters 

#### <a name="-verbose"></a>-Verbose
**Algemene para meter.** Gebruik deze optie om gedetailleerde logboeken uit te voeren.

### <a name="output"></a>Uitvoer


##### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>Voorbeeld uitvoer van het uitschakelen van de instrumentatie-engine

```powershell
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```

## <a name="disable-applicationinsightsmonitoring"></a>Disable-ApplicationInsightsMonitoring

Hiermee schakelt u de bewaking op de doel computer uit.
Met deze cmdlet worden wijzigingen in de IIS-applicationHost.config verwijderd en worden register sleutels verwijderd.

### <a name="examples"></a>Voorbeelden

```powershell
PS C:\> Disable-ApplicationInsightsMonitoring
```

### <a name="parameters"></a>Parameters 

#### <a name="-verbose"></a>-Verbose
**Algemene para meter.** Gebruik deze schakel optie om gedetailleerde logboeken weer te geven.

### <a name="output"></a>Uitvoer


##### <a name="example-output-from-successfully-disabling-monitoring"></a>Voorbeeld uitvoer van het uitschakelen van de bewaking

```powershell
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-00z'
in :1,237
No element in the source document matches '/configuration/location[@path='']/system.webServer/modules/add[@name='ManagedHttpModuleHelper']'
Not executing RemoveAll (transform line 1, 546)
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'disable'
GAC Module will not be removed, since this operation might cause IIS instabilities
Configuring IIS Environment for codeless attach...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring IIS Environment for instrumentation engine...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring registry for instrumentation engine...
Successfully disabled Application Insights Status Monitor
```


## <a name="get-applicationinsightsmonitoringconfig"></a>Get-ApplicationInsightsMonitoringConfig

Hiermee wordt het configuratie bestand opgehaald en worden de waarden afgedrukt op de-console.

### <a name="examples"></a>Voorbeelden

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

### <a name="parameters"></a>Parameters

Geen para meters vereist.

### <a name="output"></a>Uitvoer


##### <a name="example-output-from-reading-the-config-file"></a>Voor beeld van uitvoer van het lezen van het configuratie bestand

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="get-applicationinsightsmonitoringstatus"></a>Get-ApplicationInsightsMonitoringStatus

Deze cmdlet biedt informatie over het oplossen van problemen met Status Monitor.
Gebruik deze cmdlet om de bewakings status, versie van de Power shell-module te onderzoeken en het actieve proces te controleren.
Met deze cmdlet worden versie-informatie en informatie over de vereiste sleutel bestanden voor bewaking gerapporteerd.

### <a name="examples"></a>Voorbeelden

#### <a name="example-application-status"></a>Voor beeld: toepassings status

Voer de opdracht uit `Get-ApplicationInsightsMonitoringStatus` om de bewakings status van websites weer te geven.

```powershell

PS C:\Windows\system32> Get-ApplicationInsightsMonitoringStatus

IIS Websites:

SiteName               : Default Web Site
ApplicationPoolName    : DefaultAppPool
SiteId                 : 1
SiteState              : Stopped

SiteName               : DemoWebApp111
ApplicationPoolName    : DemoWebApp111
SiteId                 : 2
SiteState              : Started
ProcessId              : not found

SiteName               : DemoWebApp222
ApplicationPoolName    : DemoWebApp222
SiteId                 : 3
SiteState              : Started
ProcessId              : 2024
Instrumented           : true
InstrumentationKey     : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx123

SiteName               : DemoWebApp333
ApplicationPoolName    : DemoWebApp333
SiteId                 : 4
SiteState              : Started
ProcessId              : 5184
AppAlreadyInstrumented : true
```

In dit voor beeld;
- **Machine-id** is een anonieme id die wordt gebruikt om uw server uniek te identificeren. Als u een ondersteunings aanvraag maakt, hebt u deze ID nodig om logboeken voor uw server te vinden.
- De **standaard website** is gestopt in IIS
- **DemoWebApp111** is gestart in IIS, maar heeft geen aanvragen ontvangen. Dit rapport geeft aan dat er geen proces kan worden uitgevoerd (ProcessId: niet gevonden).
- **DemoWebApp222** wordt uitgevoerd en wordt bewaakt (instrumented: True). Op basis van de gebruikers configuratie is de instrumentatie sleutel XXXXXXXX-XXXX-XXXX-XXXX-xxxxxxxxx123 overeenkomt met deze site.
- **DemoWebApp333** is hand matig geinstrumenteerd met behulp van de Application Insights SDK. De SDK is Status Monitor gedetecteerd en de site wordt niet bewaakt.


#### <a name="example-powershell-module-information"></a>Voor beeld: Power shell-module gegevens

Voer de opdracht uit `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` om informatie weer te geven over de huidige module:

```powershell

PS C:\> Get-ApplicationInsightsMonitoringStatus -PowerShellModule

PowerShell Module version:
0.4.0-alpha

Application Insights SDK version:
2.9.0.3872

Executing PowerShell Module Assembly:
Microsoft.ApplicationInsights.Redfield.Configurator.PowerShell, Version=2.8.14.11432, Culture=neutral, PublicKeyToken=31bf3856ad364e35

PowerShell Module Directory:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\PowerShell

Runtime Paths:
ParentDirectory (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content

ConfigurationPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config

ManagedHttpModuleHelperPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll

RedfieldIISModulePath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

InstrumentationEngine86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\MicrosoftInstrumentationEngine_x86.dll

InstrumentationEngine64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll

InstrumentationEngineExtensionHost86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\Microsoft.ApplicationInsights.ExtensionsHost_x86.dll

InstrumentationEngineExtensionHost64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll

InstrumentationEngineExtensionConfig86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\Microsoft.InstrumentationEngine.Extensions.config

InstrumentationEngineExtensionConfig64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.InstrumentationEngine.Extensions.config

ApplicationInsightsSdkPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.dll
```

#### <a name="example-runtime-status"></a>Voor beeld: runtime status

U kunt het proces op de geinstrumenteerde computer controleren om te controleren of alle Dll's zijn geladen. Als de bewaking werkt, moeten ten minste 12 Dll's worden geladen.

Voer de opdracht `Get-ApplicationInsightsMonitoringStatus -InspectProcess` uit:


```
PS C:\> Get-ApplicationInsightsMonitoringStatus -InspectProcess

iisreset.exe /status
Status for IIS Admin Service ( IISADMIN ) : Running
Status for Windows Process Activation Service ( WAS ) : Running
Status for Net.Msmq Listener Adapter ( NetMsmqActivator ) : Running
Status for Net.Pipe Listener Adapter ( NetPipeActivator ) : Running
Status for Net.Tcp Listener Adapter ( NetTcpActivator ) : Running
Status for World Wide Web Publishing Service ( W3SVC ) : Running

handle64.exe -accepteula -p w3wp
BF0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.ServerTelemetryChannel.dll
C58: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.AzureAppServices.dll
C68: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.DependencyCollector.dll
C78: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.WindowsServer.dll
C98: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.Web.dll
CBC: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.PerfCounterCollector.dll
DB0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.Agent.Intercept.dll
B98: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll
BB4: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.Contracts.dll
BCC: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.Redfield.Lightup.dll
BE0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.dll

listdlls64.exe -accepteula w3wp
0x0000000019ac0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
0x00000000198b0000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
0x000000000c460000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
0x000000000ad60000  0x108000  C:\Windows\TEMP\2.4.0.0.Microsoft.ApplicationInsights.Extensions.Intercept_x64.dll
```

### <a name="parameters"></a>Parameters

#### <a name="no-parameters"></a>(Geen para meters)

Met deze cmdlet wordt standaard de bewakings status van webtoepassingen gerapporteerd.
Gebruik deze optie om te controleren of uw toepassing is geinstrumenteerd.
U kunt ook controleren welke instrumentatie sleutel is afgestemd op uw site.


#### <a name="-powershellmodule"></a>-PowerShellModule
**Optioneel**. Gebruik deze schakel optie om de versie nummers en paden te rapporteren van DLL-bestanden die vereist zijn voor bewaking.
Gebruik deze optie als u de versie van een DLL-bestand, inclusief de Application Insights SDK, moet identificeren.

#### <a name="-inspectprocess"></a>-InspectProcess

**Optioneel**. Gebruik deze schakel optie om te rapporteren of IIS wordt uitgevoerd.
Ook worden er externe hulpprogram ma's gedownload om te bepalen of de benodigde DLL-bestanden in de IIS-runtime worden geladen.


Als dit proces om welke reden dan ook mislukt, kunt u deze opdrachten hand matig uitvoeren:
- iisreset.exe/status
- [handle64.exe](/sysinternals/downloads/handle) -p W3wp | findstr/I "InstrumentationEngine AI. ApplicationInsights
- [listdlls64.exe](/sysinternals/downloads/listdlls) W3wp | findstr/I "InstrumentationEngine AI ApplicationInsights"


#### <a name="-force"></a>-Force

**Optioneel**. Wordt alleen gebruikt met InspectProcess. Gebruik deze schakel optie om de prompt van de gebruiker over te slaan die wordt weer gegeven voordat extra hulpprogram ma's worden gedownload.


## <a name="set-applicationinsightsmonitoringconfig"></a>Set-ApplicationInsightsMonitoringConfig

Hiermee stelt u het configuratie bestand in zonder volledige herinstallatie.
Start IIS opnieuw op om de wijzigingen van kracht te laten worden.

> [!IMPORTANT] 
> Voor deze cmdlet is een Power shell-sessie met beheerders machtigingen vereist.


### <a name="examples"></a>Voorbeelden

#### <a name="example-with-a-single-instrumentation-key"></a>Voor beeld met één instrumentatie sleutel
In dit voor beeld wordt aan alle apps op de huidige computer één instrumentatie sleutel toegewezen.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

#### <a name="example-with-an-instrumentation-key-map"></a>Voor beeld met een instrumentatie sleutel toewijzing
In dit voorbeeld:
- `MachineFilter` komt overeen met de huidige computer met behulp van het `'.*'` Joker teken.
- `AppFilter='WebAppExclude'` biedt een `null` instrumentatie sleutel. De opgegeven app wordt niet geinstrumenteerd.
- `AppFilter='WebAppOne'` Hiermee wijst u de opgegeven app een unieke instrumentatie sleutel toe.
- `AppFilter='WebAppTwo'` Hiermee wijst u de opgegeven app een unieke instrumentatie sleutel toe.
- Ten slotte `AppFilter` gebruikt het `'.*'` Joker teken ook om alle web-apps te zoeken die niet overeenkomen met de eerdere regels en een standaard instrumentatie sleutel toe te wijzen.
- Spaties worden toegevoegd voor de Lees baarheid.

```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap `
       @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
          @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
          @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
          @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})
```

### <a name="parameters"></a>Parameters

#### <a name="-instrumentationkey"></a>-InstrumentationKey
**Vereist.** Gebruik deze para meter om één instrumentatie sleutel op te geven voor gebruik door alle apps op de doel computer.

#### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Vereist.** Gebruik deze para meter om meerdere instrumentatie sleutels en een toewijzing van de instrumentatie sleutels voor elke app op te geven.
U kunt één installatie script maken voor verschillende computers door in te stellen `MachineFilter` .

> [!IMPORTANT]
> Apps komen overeen met regels in de volg orde waarin de regels worden opgegeven. Daarom moet u eerst de meest specifieke regels opgeven en de meest algemene regels als laatste.

##### <a name="schema"></a>Schema
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** is een vereiste C#-regex van de naam van de computer of virtuele machine.
    - '. * ' komt overeen met alles
    - ComputerName komt alleen overeen met computers met de opgegeven naam.
- **AppFilter** is een vereiste C#-regex van de naam van de computer of virtuele machine.
    - '. * ' komt overeen met alles
    - ApplicationName komt alleen overeen met IIS-apps met de opgegeven naam.
- **InstrumentationKey** is vereist om de bewaking van de apps in te scha kelen die overeenkomen met de voor gaande twee filters.
    - Laat deze waarde leeg als u regels wilt definiëren om bewaking uit te sluiten.


#### <a name="-verbose"></a>-Verbose
**Algemene para meter.** Gebruik deze schakel optie om gedetailleerde logboeken weer te geven.


### <a name="output"></a>Uitvoer

Standaard geen uitvoer.

##### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>Voor beeld van uitgebreide uitvoer van het instellen van het configuratie bestand via-InstrumentationKey

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

##### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>Voor beeld van uitgebreide uitvoer van het instellen van het configuratie bestand via-InstrumentationKeyMap

```
VERBOSE: Operation: InstallWithIkeyMap
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

## <a name="start-applicationinsightsmonitoringtrace"></a>Start-ApplicationInsightsMonitoringTrace

Verzamelt [etw-gebeurtenissen](/windows/desktop/etw/event-tracing-portal) vanuit de runtime van code koppelen. Deze cmdlet is een alternatief voor het uitvoeren van [PerfView](https://github.com/microsoft/perfview).

Verzamelde gebeurtenissen worden in realtime afgedrukt op de console en opgeslagen in een ETL-bestand. Het ETL-bestand van de uitvoer kan worden geopend door [PerfView](https://github.com/microsoft/perfview) voor verder onderzoek.

Deze cmdlet wordt uitgevoerd totdat de time-outperiode (standaard 5 minuten) of hand matig () is bereikt `Ctrl + C` .

### <a name="examples"></a>Voorbeelden

#### <a name="how-to-collect-events"></a>Gebeurtenissen verzamelen

Normaal gesp roken wordt u gevraagd om gebeurtenissen te verzamelen om te onderzoeken waarom uw toepassing niet wordt geinstrumenteerd.

Tijdens de uitvoering van het programma code koppelen worden ETW-gebeurtenissen meegeteld wanneer IIS wordt gestart en wanneer de toepassing wordt gestart.

Deze gebeurtenissen verzamelen:
1. Voer in een cmd-console met beheerders bevoegdheden uit `iisreset /stop` om IIS en alle web-apps uit te scha kelen.
2. Deze cmdlet uitvoeren
3. Voer in een cmd-console met beheerders bevoegdheden uitvoeren uit `iisreset /start` om IIS te starten.
4. Probeer naar uw app te bladeren.
5. Nadat de app is geladen, kunt u deze hand matig stoppen ( `Ctrl + C` ) of wachten op de time-out.

#### <a name="what-events-to-collect"></a>Welke gebeurtenissen moeten worden verzameld

Er zijn drie opties voor het verzamelen van gebeurtenissen:
1. Gebruik de schakel optie `-CollectSdkEvents` voor het verzamelen van gebeurtenissen die afkomstig zijn van de Application INSIGHTS SDK.
2. Gebruik de schakel optie `-CollectRedfieldEvents` voor het verzamelen van gebeurtenissen die worden gegenereerd door status monitor en de Redfield-runtime. Deze logboeken zijn handig bij het vaststellen van IIS en het opstarten van toepassingen.
3. Gebruik beide Schakel opties om beide gebeurtenis typen te verzamelen.
4. Standaard als er geen schakelaar is opgegeven, worden beide gebeurtenis typen verzameld.


### <a name="parameters"></a>Parameters

#### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**Optioneel.** Gebruik deze para meter om in te stellen hoe lang dit script gebeurtenissen moet verzamelen. Standaard is dit 5 minuten.

#### <a name="-logdirectory"></a>-LogDirectory
**Optioneel.** Gebruik deze schakel optie om de map uitvoermap van het ETL-bestand in te stellen. Dit bestand wordt standaard gemaakt in de map Power shell-modules. Het volledige pad wordt weer gegeven tijdens het uitvoeren van het script.


#### <a name="-collectsdkevents"></a>-CollectSdkEvents
**Optioneel.** Gebruik deze schakel optie om Application Insights SDK-gebeurtenissen te verzamelen.

#### <a name="-collectredfieldevents"></a>-CollectRedfieldEvents
**Optioneel.** Gebruik deze schakel optie om gebeurtenissen van Status Monitor en de Redfield-runtime te verzamelen.

#### <a name="-verbose"></a>-Verbose
**Algemene para meter.** Gebruik deze optie om gedetailleerde logboeken uit te voeren.



### <a name="output"></a>Uitvoer


#### <a name="example-of-application-startup-logs"></a>Voor beeld van opstart logboeken van toepassingen
```powershell
PS C:\Windows\system32> Start-ApplicationInsightsMonitoringTrace -ColectRedfieldEvents
Starting...
Log File: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\logs\20190627_144217_ApplicationInsights_ETW_Trace.etl
Tracing enabled, waiting for events.
Tracing will timeout in 5 minutes. Press CTRL+C to cancel.

2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftAppInsights_ManagedHttpModulePath='C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll', MicrosoftAppInsights_ManagedHttpModuleType='Microsoft.ApplicationInsights.RedfieldIISModule.RedfieldIISModule'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftDiagnosticServices_ManagedHttpModulePath2='', MicrosoftDiagnosticServices_ManagedHttpModuleType2=''
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Environment variable 'MicrosoftDiagnosticServices_ManagedHttpModulePath2' or 'MicrosoftDiagnosticServices_ManagedHttpModuleType2' is null, skipping managed dll loading
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace MulticastHttpModule.constructor, success, 70 ms
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Current assembly 'Microsoft.ApplicationInsights.RedfieldIISModule, Version=2.8.18.27202, Culture=neutral, PublicKeyToken=f23a46de0be5d6f3' location 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Matched filter '.*'~'STATUSMONITORTE', '.*'~'DemoWithSql'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Lightup assembly calculated path: 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.Redfield.Lightup.dll'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-FrameworkLightup Trace Loaded applicationInsights.config from assembly's resource Microsoft.ApplicationInsights.Redfield.Lightup, Version=2.8.18.27202, Culture=neutral, PublicKeyToken=f23a46de0be5d6f3/Microsoft.ApplicationInsights.Redfield.Lightup.ApplicationInsights-recommended.config
2:42:34 PM EVENT: Microsoft-ApplicationInsights-FrameworkLightup Trace Successfully attached ApplicationInsights SDK
2:42:34 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.LoadLightupAssemblyAndGetLightupHttpModuleClass, success, 2687 ms
2:42:34 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.CreateAndInitializeApplicationInsightsHttpModules(lightupHttpModuleClass), success
2:42:34 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace ManagedHttpModuleHelper, multicastHttpModule.Init() success, 3288 ms
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftAppInsights_ManagedHttpModulePath='C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll', MicrosoftAppInsights_ManagedHttpModuleType='Microsoft.ApplicationInsights.RedfieldIISModule.RedfieldIISModule'
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftDiagnosticServices_ManagedHttpModulePath2='', MicrosoftDiagnosticServices_ManagedHttpModuleType2=''
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Environment variable 'MicrosoftDiagnosticServices_ManagedHttpModulePath2' or 'MicrosoftDiagnosticServices_ManagedHttpModuleType2' is null, skipping managed dll loading
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace MulticastHttpModule.constructor, success, 0 ms
2:42:35 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.CreateAndInitializeApplicationInsightsHttpModules(lightupHttpModuleClass), success
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace ManagedHttpModuleHelper, multicastHttpModule.Init() success, 0 ms
Timeout Reached. Stopping...
```

## <a name="next-steps"></a>Volgende stappen

  Uw telemetrie weergeven:
 - [Bekijk metrische gegevens](../essentials/metrics-charts.md) om de prestaties en het gebruik te bewaken.
- [Zoek gebeurtenissen en logboeken](./diagnostic-search.md) om problemen op te sporen.
- Gebruik [analyses](../logs/log-query-overview.md) voor meer geavanceerde query's.
- [Dash boards maken](./overview-dashboard.md).
 
 Meer telemetrie toevoegen:
 - [Maak webtests](monitor-web-app-availability.md) om ervoor te zorgen dat uw site actief blijft.
- [Voeg de telemetrie van de webclient](./javascript.md) toe om uitzonde ringen van webpagina code te bekijken en tracerings aanroepen in te scha kelen.
- [Voeg de Application INSIGHTS SDK toe aan uw code](./asp-net.md) zodat u tracerings-en logboek aanroepen kunt invoegen.
 
 Meer doen met Application Insights agent:
 - Gebruik onze hand leiding om Application Insights-agent op te [lossen](status-monitor-v2-troubleshoot.md) .


---
title: Azure-toepassing Insights-agent-aan de slag | Microsoft Docs
description: Een Snelstartgids voor Application Insights agent. Bewaak de prestaties van de website zonder de website opnieuw te implementeren. Werkt met ASP.NET-Web-apps die on-premises worden gehost, in Vm's of op Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 01/22/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 21184e1623fd47e8367d4c5dfbc2c85debe93124
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100587385"
---
# <a name="get-started-with-azure-monitor-application-insights-agent-for-on-premises-servers"></a>Aan de slag met Azure Monitor Application Insights-agent voor on-premises servers

Dit artikel bevat de Quick Start-opdrachten die naar verwachting werken voor de meeste omgevingen.
De instructies zijn afhankelijk van de PowerShell Gallery voor het distribueren van updates.
Deze opdrachten ondersteunen de Power shell- `-Proxy` para meter.

Raadpleeg de [gedetailleerde instructies](status-monitor-v2-detailed-instructions.md)voor een uitleg van deze opdrachten, instructies voor het aanpassen en informatie over het oplossen van problemen.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="download-and-install-via-powershell-gallery"></a>Downloaden en installeren via PowerShell Gallery

### <a name="install-prerequisites"></a>Vereiste onderdelen installeren

> [!NOTE]
> Vanaf 2020 april heeft PowerShell Gallery TLS 1,1 en 1,0 afgeschaft.
>
> Zie [POWERSHELL Gallery TLS-ondersteuning](https://devblogs.microsoft.com/powershell/powershell-gallery-tls-support)voor additionnal vereisten die u mogelijk nodig hebt.
>

Voer Power shell uit als beheerder.
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
Install-Module -Name PowerShellGet -Force
``` 
Sluit Power shell.

### <a name="install-application-insights-agent"></a>Application Insights-agent installeren
Voer Power shell uit als beheerder.
```powershell   
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-Module -Name Az.ApplicationMonitor -AllowPrerelease -AcceptLicense
``` 

### <a name="enable-monitoring"></a>Bewaking inschakelen
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Enable-ApplicationInsightsMonitoring -ConnectionString xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
    
        
## <a name="download-and-install-manually-offline-option"></a>Hand matig downloaden en installeren (offline optie)
### <a name="download-the-module"></a>De module downloaden
Down load de nieuwste versie van de module hand matig van [PowerShell Gallery](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).

### <a name="unzip-and-install-application-insights-agent"></a>Application Insights agent uitpakken en installeren
```powershell
$pathToNupkg = "C:\Users\t\Desktop\Az.ApplicationMonitor.0.3.0-alpha.nupkg"
$pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
$pathToNupkg | rename-item -newname $pathToZip
$pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\Az.ApplicationMonitor"
Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
```
### <a name="enable-monitoring"></a>Bewaking inschakelen
```powershell
Enable-ApplicationInsightsMonitoring -ConnectionString xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```



## <a name="next-steps"></a>Volgende stappen

 Uw telemetrie weergeven:

- [Bekijk metrische gegevens](../essentials/metrics-charts.md) om de prestaties en het gebruik te bewaken.
- [Zoek gebeurtenissen en logboeken](./diagnostic-search.md) om problemen op te sporen.
- [Gebruik analyses](../logs/log-query-overview.md) voor meer geavanceerde query's.
- [Dash boards maken](./overview-dashboard.md).

 Meer telemetrie toevoegen:

- [Maak webtests](monitor-web-app-availability.md) om ervoor te zorgen dat uw site actief blijft.
- [Voeg de telemetrie van de webclient](./javascript.md) toe om uitzonde ringen van webpagina code te bekijken en tracerings aanroepen in te scha kelen.
- [Voeg de Application INSIGHTS SDK toe aan uw code](./asp-net.md) zodat u tracerings-en logboek aanroepen kunt invoegen.

Meer doen met Application Insights agent:

- Raadpleeg de [gedetailleerde instructies](status-monitor-v2-detailed-instructions.md) voor een uitleg van de opdrachten die hier worden weer gegeven.
- Gebruik onze hand leiding om Application Insights-agent op te [lossen](status-monitor-v2-troubleshoot.md) .


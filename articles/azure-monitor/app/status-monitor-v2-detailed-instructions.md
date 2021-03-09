---
title: Gedetailleerde instructies voor Azure-toepassing Insights-agent | Microsoft Docs
description: Gedetailleerde instructies voor het aan de slag gaan met Application Insights-agent. Bewaak de prestaties van de website zonder de website opnieuw te implementeren. Werkt met ASP.NET-Web-apps die on-premises worden gehost, in Vm's of op Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 29922f088a51e4876e5e2ec8fe87c3bbce4482f3
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102521676"
---
# <a name="application-insights-agent-formerly-named-status-monitor-v2-detailed-instructions"></a>Application Insights-agent (voorheen met de naam Status Monitor v2): gedetailleerde instructies

In dit artikel wordt beschreven hoe u de PowerShell Gallery uitschakelt en de module ApplicationMonitor downloadt.
Inbegrepen zijn de meest voorkomende para meters die u nodig hebt om aan de slag te gaan.
We hebben ook hand matige Download instructies gegeven voor het geval u geen toegang hebt tot internet.

## <a name="get-an-instrumentation-key"></a>Een instrumentatie sleutel ophalen

U hebt een instrumentatie sleutel nodig om aan de slag te gaan. Zie [een Application Insights resource maken](create-new-resource.md#copy-the-instrumentation-key)voor meer informatie.

## <a name="run-powershell-as-admin-with-an-elevated-execution-policy"></a>Power shell uitvoeren als beheerder met een verhoogd uitvoerings beleid

### <a name="run-as-admin"></a>Uitvoeren als-beheerder

Power Shell heeft machtigingen op beheerders niveau nodig om wijzigingen aan te brengen op uw computer.
### <a name="execution-policy"></a>Uitvoerings beleid
- Beschrijving: standaard is het uitvoeren van Power shell-scripts uitgeschakeld. We raden u aan om RemoteSigned-scripts alleen toe te staan voor de huidige scope.
- Naslag [informatie: over uitvoerings beleid](/powershell/module/microsoft.powershell.core/about/about_execution_policies) en [Set-ExecutionPolicy](/powershell/module/microsoft.powershell.security/set-executionpolicy).
- Opdracht: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process` .
- Optionele para meter:
    - `-Force`. De bevestigings prompt wordt omzeild.

**Voorbeeld fouten**

```output
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.

Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>Vereisten voor Power shell

Controleer uw exemplaar van Power shell door de opdracht uit te voeren `$PSVersionTable` .
Met deze opdracht wordt de volgende uitvoer gegenereerd:

```output
Name                           Value
----                           -----
PSVersion                      5.1.17763.316
PSEdition                      Desktop
PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0...}
BuildVersion                   10.0.17763.316
CLRVersion                     4.0.30319.42000
WSManStackVersion              3.0
PSRemotingProtocolVersion      2.3
SerializationVersion           1.1.0.1
```

Deze instructies zijn geschreven en getest op een computer met Windows 10 en de hierboven weer gegeven versies.

## <a name="prerequisites-for-powershell-gallery"></a>Vereisten voor PowerShell Gallery

Met deze stappen wordt de server voor bereid om modules te downloaden van PowerShell Gallery.

> [!NOTE] 
> PowerShell Gallery wordt ondersteund op Windows 10, Windows Server 2016 en Power shell 6 +.
> Zie [Installing PowerShellGet](/powershell/scripting/gallery/installing-psget)(Engelstalig) voor meer informatie over eerdere versies.


1. Voer Power shell uit als beheerder met een verhoogd uitvoerings beleid.
2. Installeer de NuGet-pakket provider.
    - Beschrijving: u hebt deze provider nodig om te communiceren met op NuGet gebaseerde opslag plaatsen zoals PowerShell Gallery.
    - Referentie: [install-package provider](/powershell/module/packagemanagement/install-packageprovider).
    - Opdracht: `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201` .
    - Optionele para meters:
        - `-Proxy`. Hiermee geeft u een proxy server voor de aanvraag op.
        - `-Force`. De bevestigings prompt wordt omzeild.
    
    U ontvangt dit bericht als NuGet niet is ingesteld:

    ```output
    NuGet provider is required to continue
    PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. 
    The NuGet provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
    'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
    'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
    the NuGet provider now?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```    

3. Configureer PowerShell Gallery als een vertrouwde opslag plaats.
    - Beschrijving: standaard is PowerShell Gallery een niet-vertrouwde opslag plaats.
    - Verwijzing: [set-PSRepository](/powershell/module/powershellget/set-psrepository).
    - Opdracht: `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted` .
    - Optionele para meter:
        - `-Proxy`. Hiermee geeft u een proxy server voor de aanvraag op.

    U ontvangt dit bericht als PowerShell Gallery niet wordt vertrouwd:

    ```output
    Untrusted repository
    You are installing the modules from an untrusted repository. 
    If you trust this repository, change its InstallationPolicy value 
    by running the Set-PSRepository cmdlet. Are you sure you want to 
    install the modules from 'PSGallery'?
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):
    ```

    U kunt deze wijziging bevestigen en alle PSRepositories controleren door de opdracht uit te voeren `Get-PSRepository` .

4. Installeer de nieuwste versie van PowerShellGet.
    - Beschrijving: deze module bevat het hulp programma waarmee u andere modules van PowerShell Gallery kunt ophalen. Versie 1.0.0.1 wordt geleverd met Windows 10 en Windows Server. Versie 1.6.0 of hoger is vereist. Voer de opdracht uit om te bepalen welke versie is geïnstalleerd `Get-Command -Module PowerShellGet` .
    - Naslag informatie: [PowerShellGet installeren](/powershell/scripting/gallery/installing-psget).
    - Opdracht: `Install-Module -Name PowerShellGet` .
    - Optionele para meters:
        - `-Proxy`. Hiermee geeft u een proxy server voor de aanvraag op.
        - `-Force`. Hiermee wordt de waarschuwing ' reeds geïnstalleerd ' omzeild en wordt de nieuwste versie geïnstalleerd.

    Deze fout wordt weer gegeven als u niet de nieuwste versie van PowerShellGet gebruikt:

    ```output
    Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
    At line:1 char:20
    Install-Module abc -AllowPrerelease
                   ~~~~~~~~~~~~~~~~
    CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
    FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    ```

5. Start Power shell opnieuw. U kunt de nieuwe versie niet laden in de huidige sessie. Met nieuwe Power shell-sessies wordt de meest recente versie van PowerShellGet geladen.

## <a name="download-and-install-the-module-via-powershell-gallery"></a>Down load en installeer de module via PowerShell Gallery

Met deze stappen wordt de module AZ. ApplicationMonitor gedownload van PowerShell Gallery.

1. Zorg ervoor dat aan alle vereisten voor PowerShell Gallery worden voldaan.
2. Voer Power shell uit als beheerder met een verhoogd uitvoerings beleid.
3. Installeer de module AZ. ApplicationMonitor.
    - Verwijzing: [install-module](/powershell/module/powershellget/install-module).
    - Opdracht: `Install-Module -Name Az.ApplicationMonitor` .
    - Optionele para meters:
        - `-Proxy`. Hiermee geeft u een proxy server voor de aanvraag op.
        - `-AllowPrerelease`. Hiermee kunnen alpha-en bèta versies worden geïnstalleerd.
        - `-AcceptLicense`. De prompt ' licentie accepteren ' wordt overgeslagen
        - `-Force`. De waarschuwing "niet-vertrouwde opslag plaats" wordt omzeild.

## <a name="download-and-install-the-module-manually-offline-option"></a>De module hand matig downloaden en installeren (offline optie)

Als u om een of andere reden geen verbinding kunt maken met de Power shell-module, kunt u de module AZ. ApplicationMonitor hand matig downloaden en installeren.

### <a name="manually-download-the-latest-nupkg-file"></a>Het meest recente nupkg-bestand hand matig downloaden

1. Ga naar https://www.powershellgallery.com/packages/Az.ApplicationMonitor.
2. Selecteer de meest recente versie van het bestand in de tabel **versie geschiedenis** .
3. Selecteer onder **installatie opties** de optie **hand matig downloaden**.

### <a name="option-1-install-into-a-powershell-modules-directory"></a>Optie 1: installeren in een Power shell-modules directory
Installeer de hand matig gedownloade Power shell-module in een Power shell-Directory, zodat deze kan worden gedetecteerd door Power shell-sessies.
Zie [een Power shell-module installeren](/powershell/scripting/developer/module/installing-a-powershell-module)voor meer informatie.


#### <a name="unzip-nupkg-as-a-zip-file-by-using-expand-archive-v1010"></a>Nupkg uitpakken als een zip-bestand met behulp van Expand-Archive (v 1.0.1.0)

- Beschrijving: de basis versie van micro soft. Power shell. Archive (v 1.0.1.0) kan nupkg-bestanden niet uitpakken. Wijzig de naam van het bestand met de extensie. zip.
- Naslag informatie: [Expand-Archive](/powershell/module/microsoft.powershell.archive/expand-archive).
- Cmd

    ```console
    $pathToNupkg = "C:\az.applicationmonitor.0.3.0-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-by-using-expand-archive-v1100"></a>Nupkg uitpakken met behulp van Expand-Archive (v 1.1.0.0)

- Beschrijving: gebruik een huidige versie van Expand-Archive om nupkg-bestanden uit te pakken zonder de extensie te wijzigen.
- Naslag informatie: [Expand-Archive](/powershell/module/microsoft.powershell.archive/expand-archive) en [micro soft. Power shell. Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0).
- Cmd

    ```console
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-nupkg-manually"></a>Optie 2: unzip en nupkg hand matig decomprimeren
Installeer de hand matig gedownloade Power shell-module in een Power shell-Directory, zodat deze kan worden gedetecteerd door Power shell-sessies.
Zie [een Power shell-module installeren](/powershell/scripting/developer/module/installing-a-powershell-module)voor meer informatie.

Als u de module in een andere Directory installeert, importeert u de module hand matig met behulp van [import-module](/powershell/module/microsoft.powershell.core/import-module).

> [!IMPORTANT] 
> Dll's worden geïnstalleerd via relatieve paden.
> Sla de inhoud van het pakket op in de gewenste runtime Directory en controleer of de toegangs machtigingen lezen maar niet schrijven toestaan.

1. Wijzig de extensie in. zip en pak de inhoud van het pakket uit in de gewenste installatie directory.
2. Zoek het bestandspad van Az.ApplicationMonitor.psd1.
3. Voer Power shell uit als beheerder met een verhoogd uitvoerings beleid.
4. Laad de module met behulp van de `Import-Module Az.ApplicationMonitor.psd1` opdracht.
    

## <a name="route-traffic-through-a-proxy"></a>Verkeer via een proxy routeren

Wanneer u een computer op uw particuliere intranet bewaakt, moet u HTTP-verkeer via een proxy routeren.

De Power shell-opdrachten voor het downloaden en installeren van AZ. ApplicationMonitor van de PowerShell Gallery een `-Proxy` para meter ondersteunen.
Raadpleeg de voor gaande instructies wanneer u de installatie scripts schrijft.

De telemetrie van uw app naar micro soft moet worden verzonden met de Application Insights SDK. U kunt het beste proxy-instellingen voor uw app configureren in uw web.config-bestand. Zie [Application Insights FAQ: proxy passthrough (Engelstalig](../faq.md#proxy-passthrough)) voor meer informatie.


## <a name="enable-monitoring"></a>Bewaking inschakelen

Gebruik de `Enable-ApplicationInsightsMonitoring` opdracht om bewaking in te scha kelen.

Zie de [API-naslag informatie](./status-monitor-v2-api-reference.md#enable-applicationinsightsmonitoring) voor een gedetailleerde beschrijving van het gebruik van deze cmdlet.



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

- Gebruik onze hand leiding om Application Insights-agent op te [lossen](status-monitor-v2-troubleshoot.md) .


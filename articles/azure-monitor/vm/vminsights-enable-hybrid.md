---
title: Azure Monitor inschakelen voor een hybride omgeving
description: In dit artikel wordt beschreven hoe u VM Insights inschakelt voor een hybride cloud omgeving die een of meer virtuele machines bevat.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: c3c8495b7355ee5d9ee8c28b4e0097a0080964d4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102046634"
---
# <a name="enable-vm-insights-for-a-hybrid-virtual-machine"></a>VM Insights inschakelen voor een hybride virtuele machine
In dit artikel wordt beschreven hoe u VM Insights inschakelt voor een virtuele machine buiten Azure, met inbegrip van on-premises en andere Cloud omgevingen.

> [!IMPORTANT]
> De aanbevolen methode voor het inschakelen van hybride Vm's is het eerst inschakelen van [Azure Arc voor servers](../../azure-arc/servers/overview.md) , zodat de vm's kunnen worden ingeschakeld voor VM-inzichten met behulp van processen die vergelijkbaar zijn met Azure vm's. In dit artikel wordt beschreven hoe u hybride Vm's kunt voorbereiden als u ervoor kiest om Azure Arc niet te gebruiken.

## <a name="prerequisites"></a>Vereisten

- [Een log Analytics-werk ruimte maken en configureren](./vminsights-configure-workspace.md).
- Zie [ondersteunde besturings systemen](./vminsights-enable-overview.md#supported-operating-systems) om ervoor te zorgen dat het besturings systeem van de virtuele machine of virtuele-machine schaalset die u inschakelt, wordt ondersteund. 


## <a name="overview"></a>Overzicht
Voor virtuele machines buiten Azure zijn dezelfde Log Analytics agent en een afhankelijkheids agent vereist die worden gebruikt voor Azure-Vm's. Omdat u geen VM-extensies kunt gebruiken om de agents te installeren, moet u ze hand matig installeren in het gast besturingssysteem of moeten ze via een andere methode worden geïnstalleerd. 

Zie [Windows-computers verbinden met Azure monitor](../agents/agent-windows.md) of [Linux-computers verbinden met Azure monitor](../agents/agent-linux.md) voor meer informatie over het implementeren van de log Analytics agent. In dit artikel vindt u meer informatie over de afhankelijkheids agent. 

## <a name="firewall-requirements"></a>Firewallvereisten
De firewall vereisten voor de Log Analytics-agent vindt u in [log Analytics Overzicht](../agents/log-analytics-agent.md#network-requirements)van de agent. De VM Insights-toewijzings afhankelijke agent verzendt geen gegevens zelf en vereist geen wijzigingen in firewalls of poorten. De kaart gegevens worden altijd door de Log Analytics agent verzonden naar de Azure Monitor-service, hetzij rechtstreeks of via de [Operations Management Suite-gateway](../../azure-monitor/agents/gateway.md) als uw IT-beveiligings beleid niet toestaat dat computers in het netwerk verbinding maken met internet.


## <a name="dependency-agent"></a>Agent voor afhankelijkheden

>[!NOTE]
>De volgende informatie die in deze sectie wordt beschreven, is ook van toepassing op de [servicetoewijzing oplossing](./service-map.md).  

U kunt de afhankelijkheids agent downloaden van de volgende locaties:

| File | Besturingssysteem | Versie | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.10.7.12710 | CA29CC328F991D7301FD0360F4F56DF78275545BB8CDA853679899CA885E96F0  |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.10.7.12710 | 98380DBEB2E2A5848F2202BC22422C68B20B62090C1BFC1DECAB37ED5451ED8C |


## <a name="install-the-dependency-agent-on-windows"></a>De afhankelijkheids agent installeren in Windows

U kunt de afhankelijkheids agent hand matig installeren op Windows-computers door uit te voeren `InstallDependencyAgent-Windows.exe` . Als u dit uitvoer bare bestand zonder opties uitvoert, wordt een installatie wizard gestart die u kunt volgen om de agent interactief te installeren. U hebt *beheerders* bevoegdheden nodig voor het gast besturingssysteem om de agent te installeren of te verwijderen.

De volgende tabel geeft een overzicht van de para meters die worden ondersteund door de installatie voor de agent vanaf de opdracht regel.

| Parameter | Beschrijving |
|:--|:--|
| /? | Retourneert een lijst met de opdracht regel opties. |
| / S | Voert een stille installatie zonder tussen komst van de gebruiker uit. |

`/?`Voer bijvoorbeeld **InstallDependencyAgent-Windows.exe/?** in om het installatie programma uit te voeren met de para meter.

Bestanden voor de Windows-afhankelijkheids agent worden standaard in *C:\Program Files\Microsoft dependency agent* geïnstalleerd. Als de afhankelijkheids agent niet kan worden gestart nadat de installatie is voltooid, raadpleegt u de logboeken voor gedetailleerde informatie over de fout. De logboekmap is *%ProgramFiles%\Microsoft dependency Agent\logs*.

### <a name="powershell-script"></a>PowerShell-script
Gebruik het volgende Power shell-voorbeeld script om de agent te downloaden en te installeren:

```powershell
Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S
```


## <a name="install-the-dependency-agent-on-linux"></a>De afhankelijkheids agent installeren in Linux

De afhankelijkheids agent is geïnstalleerd op Linux-servers vanuit *InstallDependencyAgent-Linux64. bin*, een shell script met een zelfuitpakkend binair bestand. U kunt het bestand uitvoeren met `sh` of de machtiging voor uitvoeren toevoegen aan het bestand zelf.

>[!NOTE]
> Toegang tot de hoofdmap is vereist om de agent te installeren of configureren.
>

| Parameter | Beschrijving |
|:--|:--|
| -Help | Een lijst met de opdrachtregelopties ophalen. |
| -s | Een installatie op de achtergrond uitvoeren zonder gebruikersvragen. |
| --Controleer | Controleer de machtigingen en het besturings systeem, maar installeer de agent niet. |

`-help`Voer bijvoorbeeld **InstallDependencyAgent-Linux64. bin-Help** in om het installatie programma uit te voeren met de para meter. Installeer de Linux-afhankelijkheids agent als root door de opdracht uit te voeren `sh InstallDependencyAgent-Linux64.bin` .

Als de afhankelijkheids agent niet kan worden gestart, raadpleegt u de logboeken voor gedetailleerde informatie over de fout. Op Linux-agents is de logboekmap */var/opt/Microsoft/dependency-agent/log*.

Bestanden voor de afhankelijkheids agent worden in de volgende directory's geplaatst:

| Bestanden | Locatie |
|:--|:--|
| Kernbestanden | /opt/microsoft/dependency-agent |
| Logboekbestanden | /var/opt/microsoft/dependency-agent/log |
| Configuratiebestanden | /etc/opt/microsoft/dependency-agent/config |
| Uitvoerbare bestanden van de service | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Binaire opslagbestanden | /var/opt/microsoft/dependency-agent/storage |

### <a name="shell-script"></a>Shellscript 
Gebruik het volgende voor beeld-shell script om de agent te downloaden en te installeren:

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
sudo sh InstallDependencyAgent-Linux64.bin -s
```

## <a name="desired-state-configuration"></a>Desired State Configuration

Als u de afhankelijkheids agent wilt implementeren met behulp van desired state Configuration (DSC), kunt u de xPSDesiredStateConfiguration-module gebruiken met de volgende voorbeeld code:

```powershell
configuration VMInsights {

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    $DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"

    Node localhost
    {
        # Download and install the Dependency agent
        xRemoteFile DAPackage
        {
            Uri = "https://aka.ms/dependencyagentwindows"
            DestinationPath = $DAPackageLocalPath
        }

        xPackage DA
        {
            Ensure="Present"
            Name = "Dependency Agent"
            Path = $DAPackageLocalPath
            Arguments = '/S'
            ProductId = ""
            InstalledCheckRegKey = "HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"
            InstalledCheckRegValueName = "DisplayName"
            InstalledCheckRegValueData = "Dependency Agent"
            DependsOn = "[xRemoteFile]DAPackage"
        }
    }
}
```



## <a name="troubleshooting"></a>Problemen oplossen

### <a name="vm-doesnt-appear-on-the-map"></a>De virtuele machine wordt niet weer gegeven op de kaart

Als de installatie van de afhankelijkheids agent is voltooid, maar u de computer niet ziet op de kaart, kunt u de oorzaak van het probleem vaststellen door de volgende stappen uit te voeren.

1. Is de afhankelijkheidsagent correct geïnstalleerd? U kunt dit valideren door te controleren of de service is geïnstalleerd en actief is.

    **Windows**: zoek naar de service met de naam ' micro soft dependency agent '.

    **Linux**: zoek naar het actieve proces ' micro soft-dependency-agent '.

2. Bevindt u zich op de [prijs categorie gratis van log Analytics](../insights/solutions.md)? Het gratis abonnement staat Maxi maal vijf unieke computers toe. Eventuele volgende computers worden niet weer gegeven op de kaart, zelfs als de voor gaande vijf geen gegevens meer verzenden.

3. Stuurt de computer logboek-en prestatie gegevens naar Azure Monitor logboeken? Voer de volgende query uit voor uw computer:

    ```Kusto
    Usage | where Computer == "computer-name" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

    Zijn er een of meer resultaten geretourneerd? Zijn de gegevens recent? Als dit het geval is, wordt uw Log Analytics-agent correct uitgevoerd en communiceert deze met de service. Als dat niet het geval is, controleert u de agent op uw server: [log Analytics-agent voor Windows-probleem oplossing](../agents/agent-windows-troubleshoot.md) of [log Analytics agent voor Linux-probleem oplossing](../agents/agent-linux-troubleshoot.md).

#### <a name="computer-appears-on-the-map-but-has-no-processes"></a>De computer wordt weer gegeven op de kaart, maar heeft geen processen.

Als uw server op de kaart wordt weer gegeven, maar er geen proces-of verbindings gegevens zijn, geeft dit aan dat de afhankelijkheids agent is geïnstalleerd en wordt uitgevoerd, maar het kernel-stuur programma is niet geladen.

Controleer het bestand C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log (Windows) of het bestand /var/opt/microsoft/dependency-agent/log/service.log (Linux). De laatste regels van het bestand zouden moeten aangeven waarom de kernel niet is geladen. Het is bijvoorbeeld mogelijk dat de kernel niet wordt ondersteund op Linux als u uw kernel hebt bijgewerkt.


## <a name="next-steps"></a>Volgende stappen

Nu de bewaking voor uw virtuele machines is ingeschakeld, is deze informatie beschikbaar voor analyse met VM Insights.

- Als u gedetecteerde toepassings afhankelijkheden wilt weer geven, raadpleegt u [VM Insights-toewijzing weer geven](vminsights-maps.md).

- Zie [Azure-VM-prestaties weer geven](vminsights-performance.md)om knel punten en het algehele gebruik van de VM-prestaties te identificeren.
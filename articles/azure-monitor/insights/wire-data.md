---
title: Wire data-oplossing in Azure Monitor | Microsoft Docs
description: Wiregegevens zijn gegevens van het netwerk en de prestaties van computers met Log Analytics agents. Netwerkgegevens worden gecombineerd met uw logboekgegevens om te helpen bij het correleren van gegevens.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/29/2020
ms.openlocfilehash: 5981a5f136d613ffcedda86797d807d2eecfab0d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101713623"
---
# <a name="wire-data-20-preview-solution-in-azure-monitor"></a>Wire Data 2.0 (preview)-oplossing in Azure Monitor

![Symbool Wire Data](media/wire-data/wire-data2-symbol.png)

Wiregegevens zijn geconsolideerde netwerk-en prestatie gegevens die zijn verzameld van met Windows verbonden en Linux verbonden computers met de Log Analytics-agent, met inbegrip van de gegevens die worden bewaakt door Operations Manager in uw omgeving. Netwerkgegevens worden gecombineerd met uw andere logboekgegevens om te helpen bij het correleren van gegevens.

Naast de Log Analytics-agent maakt de Wire data-oplossing gebruik van micro soft-afhankelijkheids agenten die u installeert op computers in uw IT-infra structuur. Agents voor afhankelijkheden controleren netwerkgegevens die worden verzonden naar en van uw computers voor netwerkniveaus 2-3 in het [OSI-model](https://en.wikipedia.org/wiki/OSI_model), met inbegrip van de verschillende gebruikte protocollen en poorten. Gegevens worden vervolgens verzonden naar Azure Monitor met behulp van agents.  

>[!NOTE]
>De oplossing voor Wiregegevens is vervangen door de [servicetoewijzing oplossing](../vm/service-map.md).  Beide gebruiken de Log Analytics agent en de afhankelijkheids agent voor het verzamelen van netwerk verbindings gegevens in Azure Monitor. 
> 
>Bestaande klanten die gebruikmaken van de Wire data-oplossing, kunnen deze blijven gebruiken. Er worden richt lijnen gepubliceerd voor een migratie tijdlijn voor het verplaatsen naar Servicetoewijzing.
>
>Nieuwe klanten moeten de [servicetoewijzing-oplossing](../vm/service-map.md) of [VM Insights](../vm/vminsights-overview.md)installeren.  De Servicetoewijzing gegevensset is vergelijkbaar met Wired data.  VM Insights bevat de Servicetoewijzing gegevensset met aanvullende prestatie gegevens en-functies voor analyse. 


Azure Monitor registreert standaard gegevens voor CPU-, geheugen-, schijf-en netwerk prestatie gegevens van tellers die zijn ingebouwd in Windows en Linux, evenals andere prestatie meter items die u kunt opgeven. Het verzamelen van netwerk- en andere gegevens wordt voor elke agent in realtime uitgevoerd, met inbegrip van subnetten en protocollen op toepassingsniveau die door de computer worden gebruikt.  Wire Data kijkt naar netwerkgegevens op toepassingsniveau, niet naar die op de TCP-transportlaag.  De oplossing kijkt niet naar afzonderlijke ACK's en SYN's.  Zodra de handshake is voltooid, wordt dit als een live-verbinding beschouwd en wordt deze gemarkeerd als verbonden. Die verbinding blijft actief zolang beide zijden het erover eens zijn dat de socket geopend is en gegevens heen en weer kunnen worden gestuurd.  Zodra de verbinding is gesloten, wordt deze als losgekoppeld gemarkeerd.  Daarom wordt alleen de bandbreedte van voltooide pakketten meegeteld en wordt niet gemeld of pakketten opnieuw of niet zijn verzonden.

Als u [sFlow](http://www.sflow.org/) of andere software hebt gebruikt met het [NetFlow-protocol van Cisco](https://www.cisco.com/c/en/us/products/collateral/ios-nx-os-software/ios-netflow/prod_white_paper0900aecd80406232.html), zullen de statistieken en gegevens die u in gegevens van Wire Data ziet, u bekend voorkomen.

Enkele van de typen ingebouwde query's voor zoeken in logboeken:

- Agents die gegevens van Wire Data leveren
- IP-adressen van agents die gegevens van Wire Data leveren
- Uitgaande communicatie per IP-adres
- Aantal bytes verzonden per toepassingsprotocol
- Aantal bytes verzonden per toepassingsservice
- Bytes ontvangen door verschillende protocollen
- Totaal aantal bytes verzonden en ontvangen per IP-versie
- Gemiddelde latentie voor verbindingen die betrouwbaar zijn gemeten
- Computerprocessen waarmee netwerkverkeer is gestart of ontvangen
- Hoeveelheid netwerkverkeer voor een proces

Wanneer u zoekt met behulp van Wire Data, kunt u gegevens filteren en groeperen zodat u informatie kunt bekijken over de belangrijkste agents en protocollen. U kunt ook zien wanneer bepaalde computers (IP-adressen/MAC-adressen) met elkaar hebben gecommuniceerd, hoelang dat duurde en hoeveel gegevens er zijn verzonden. In wezen bekijkt u metagegevens over het netwerkverkeer, wat op zoeken is gebaseerd.

Maar aangezien u metagegevens bekijkt, is dat niet per se nuttig voor een diepgaande probleemoplossing. Bedradings gegevens in Azure Monitor zijn geen volledige opname van netwerk gegevens.  De oplossing is niet bedoeld voor het oplossen van problemen op een diep pakketniveau. Het voor deel van het gebruik van de agent, vergeleken met andere verzamelings methoden, is dat u geen apparaten hoeft te installeren, uw netwerk switches opnieuw wilt configureren of gecompliceerde configuraties moet uitvoeren. Gegevens van Wire Data zijn gewoon gebaseerd op agents: u installeert de agent op een computer en de agent zal dan het eigen netwerkverkeer controleren. Een ander voordeel is wanneer u werkbelastingen in cloudproviders of een serviceprovider of Microsoft Azure wilt hosten, waarbij de gebruiker niet de eigenaar van de infrastructuurlaag is.

## <a name="connected-sources"></a>Verbonden bronnen

Wire Data haalt zijn gegevens uit de Microsoft-agent voor afhankelijkheden. De Dependency Agent is afhankelijk van de Log Analytics-agent voor de verbindingen met Azure Monitor. Dit betekent dat de Log Analytics agent moet zijn geïnstalleerd en geconfigureerd op een server met de afhankelijkheids agent. De volgende tabel beschrijft de verbonden bronnen die worden ondersteund door Wire Data.

| **Verbonden bron** | **Ondersteund** | **Beschrijving** |
| --- | --- | --- |
| Windows-agents | Ja | Wire Data analyseert en verzamelt gegevens van Windows-agentcomputers. <br><br> Naast de Log Analytics- [agent voor Windows](../agents/agent-windows.md)vereist Windows-agents de micro soft-afhankelijkheids agent. Zie de [ondersteunde besturingssystemen](../vm/vminsights-enable-overview.md#supported-operating-systems) voor een volledige lijst met versies van besturingssystemen. |
| Linux-agents | Ja | Wire Data analyseert en verzamelt gegevens van Linux-agentcomputers.<br><br> Naast de Log Analytics- [agent voor Linux is voor](../vm/quick-collect-linux-computer.md)Linux-agents de micro soft-afhankelijkheids agent vereist. Zie de [ondersteunde besturingssystemen](../vm/vminsights-enable-overview.md#supported-operating-systems) voor een volledige lijst met versies van besturingssystemen. |
| Beheergroep System Center Operations Manager | Ja | Wire Data analyseert en verzamelt gegevens van Windows- en Linux-agents in een verbonden [System Center Operations Manager-beheergroep](../agents/om-agents.md). <br><br> Er is een directe verbinding van de System Center Operations Manager agent computer naar Azure Monitor vereist. |
| Azure Storage-account | Nee | Omdat Wire Data gegevens van agentcomputers verzamelt, zijn er geen gegevens te verzamelen van Azure Storage. |

In Windows wordt micro soft Monitoring Agent (MMA) zowel door System Center Operations Manager als Azure Monitor gebruikt voor het verzamelen en verzenden van gegevens. Afhankelijk van de context heet de agent de System Center Operations Manager agent, Log Analytics agent, MMA of direct agent. System Center Operations Manager en Azure Monitor bieden enigszins verschillende versies van de MMA. Deze versies kunnen elk rapport System Center Operations Manager, Azure Monitor of aan beide.

Op Linux verzamelt en verzendt de Log Analytics-agent voor Linux gegevens naar Azure Monitor. U kunt bedradings gegevens gebruiken op servers met agents die rechtstreeks zijn verbonden met Azure Monitor, of op servers die verbinding maken met Azure Monitor via System Center Operations Manager-beheer groepen.

De afhankelijkheids agent verzendt geen gegevens zelf en vereist geen wijzigingen in firewalls of poorten. De gegevens in Wiregegevens worden altijd door de Log Analytics agent verzonden naar Azure Monitor, hetzij rechtstreeks, hetzij via de Log Analytics gateway.

![diagram van agent](./media/wire-data/agents.png)

Als u een System Center Operations Manager gebruiker bent met een beheer groep die is verbonden met Azure Monitor:

- Er is geen aanvullende configuratie vereist wanneer uw System Center Operations Manager-agents toegang hebben tot internet om verbinding te maken met Azure Monitor.
- U moet de Log Analytics-gateway configureren om te werken met System Center Operations Manager wanneer uw System Center Operations Manager agents geen toegang krijgen tot Azure Monitor via internet.

Als uw Windows-of Linux-computers niet rechtstreeks verbinding kunnen maken met de service, moet u de Log Analytics agent configureren om verbinding te maken met Azure Monitor via de Log Analytics gateway. U kunt de Log Analytics-gateway downloaden van het [micro soft Download centrum](https://www.microsoft.com/download/details.aspx?id=52666).

## <a name="prerequisites"></a>Vereisten

- Hiervoor is de oplossing [Insight and Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite-pricing) vereist.
- Als u de vorige versie van Wire Data gebruikt, moet u deze eerst verwijderen. Alle gegevens die zijn vastgelegd via de oorspronkelijke versie van Wire Data zijn echter nog steeds beschikbaar in Wire Data 2.0 en in zoeken in logboeken.
- Er zijn beheerders bevoegdheden vereist om de afhankelijkheids agent te installeren of verwijderen.
- De afhankelijkheids agent moet worden geïnstalleerd op een computer met een 64-bits besturings systeem.

### <a name="operating-systems"></a>Besturingssystemen

De volgende secties bevatten een lijst met de ondersteunde besturings systemen voor de afhankelijkheids agent. Wire Data biedt geen ondersteuning voor 32-bits architecturen van besturingssystemen.

#### <a name="windows-server"></a>Windows Server

- Windows Server 2019
- Windows Server 2016 1803
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

#### <a name="windows-desktop"></a>Windows-bureaublad

- Windows 10 1803
- Windows 10
- Windows 8.1
- Windows 8
- Windows 7

#### <a name="supported-linux-operating-systems"></a>Ondersteunde Linux-besturingssystemen
De volgende secties bevatten een lijst met de ondersteunde besturings systemen voor de afhankelijkheids agent op Linux.  

- Alleen standaard- en SMP Linux kernelversies worden ondersteund.
- Niet-standaard kernelversies, zoals PAE en Xen, worden voor geen enkele Linux-distributie ondersteund. Bijvoorbeeld, een systeem met de release reeks ' 2.6.16.21-0,8-xen ' wordt niet ondersteund.
- Aangepaste kernels, met inbegrip van hercompilaties van standaardkernels, worden niet ondersteund.

##### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Besturingssysteemversie | Kernelversie |
|:--|:--|
| 7.4 | 3.10.0-693 |
| 7,5 | 3.10.0-862 |
| 7.6 | 3.10.0-957 |

##### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Besturingssysteemversie | Kernelversie |
|:--|:--|
| 6.9 | 2.6.32-696 |
| 6.10 | 2.6.32-754 |

##### <a name="centosplus"></a>CentOSPlus
| Besturingssysteemversie | Kernelversie |
|:--|:--|
| 6.9 | 2.6.32-696.18.7<br>2.6.32-696.30.1 |
| 6.10 | 2.6.32-696.30.1<br>2.6.32-754.3.5 |

##### <a name="ubuntu-server"></a>Ubuntu Server

| Besturingssysteemversie | Kernelversie |
|:--|:--|
| Ubuntu 18.04 | kernel 4,15.\*<br>4,18 * |
| Ubuntu 16.04.3 | kernel 4,15. * |
| 16,04 | 4,4.\*<br>4,8.\*<br>4,10.\*<br>4,11.\*<br>4,13.\* |
| 14,04 | 3,13.\*<br>4,4.\* |

##### <a name="suse-linux-11-enterprise-server"></a>SUSE Linux 11 Enter prise server

| Besturingssysteemversie | Kernelversie
|:--|:--|
| 11 SP4 | 3,0. * |

##### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enter prise server

| Besturingssysteemversie | Kernelversie
|:--|:--|
| 12 SP2 | 4,4. * |
| 12 SP3 | 4,4. * |

### <a name="dependency-agent-downloads"></a>Down loads van afhankelijkheids agent

| File | Besturingssysteem | Versie | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.7.4 | A111B92AB6CF28EB68B696C60FE51F980BFDFF78C36A900575E17083972989E0 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.7.4 | AB58F3DB8B1C3DEE7512690E5A65F1DFC41B43831543B5C040FCCE8390F2282C |



## <a name="configuration"></a>Configuratie

Voer de volgende stappen uit om Wire Data te configureren voor uw werkruimten.

1. Schakel de Analyse van activiteitenlogboek-oplossing in via de [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WireData2OMS?tab=Overview) of via het proces dat wordt beschreven in [bewakings oplossingen toevoegen van de Oplossingengalerie](../insights/solutions.md).
2. Installeer de afhankelijkheids agent op elke computer waarop u gegevens wilt ophalen. De afhankelijkheids agent kan verbindingen naar directe neighbors bewaken, zodat u op elke computer mogelijk geen agent nodig hebt.

> [!NOTE]
> U kunt de vorige versie van Wire Data niet toevoegen aan nieuwe werkruimten. Als u de oorspronkelijke versie van Wire Data hebt ingeschakeld, kunt u die blijven gebruiken. Als u echter Wire Data 2.0 wilt gebruiken, moet u eerst de oorspronkelijke versie verwijderen.
> 
 
### <a name="install-the-dependency-agent-on-windows"></a>De afhankelijkheids agent installeren in Windows

Er zijn beheerdersbevoegdheden vereist om de agent te installeren of verwijderen.

De afhankelijkheids agent wordt geïnstalleerd op computers met Windows via InstallDependencyAgent-Windows.exe. Als u dit uitvoerbare bestand zonder opties uitvoert, wordt er een wizard gestart die u kunt volgen om interactief te installeren.

Gebruik de volgende stappen om de afhankelijkheids agent te installeren op elke computer waarop Windows wordt uitgevoerd:

1. Installeer de Log Analytics agent volgens de stappen in [gegevens verzamelen van Windows-computers die in uw omgeving worden gehost](../agents/agent-windows.md).
2. Down load de Windows-afhankelijkheids agent met behulp van de koppeling in de vorige sectie en voer deze uit met behulp van de volgende opdracht: `InstallDependencyAgent-Windows.exe`
3. Volg de wizard om de agent te installeren.
4. Als de afhankelijkheids agent niet kan worden gestart, raadpleegt u de logboeken voor gedetailleerde informatie over de fout. Voor Windows-agents is dit de logboekmap: %Programfiles%\Microsoft Dependency Agent\logs.

#### <a name="windows-command-line"></a>Windows-opdrachtregel

Gebruik opties uit de volgende tabel om de agent te installeren vanaf een opdrachtregel. Als u een lijst met de installatie vlaggen wilt zien, voert u het installatie programma uit met behulp van de/? als volgt markeren.

InstallDependencyAgent-Windows.exe /?

| **Vlag** | **Beschrijving** |
| --- | --- |
| <code>/?</code> | Een lijst met de opdrachtregelopties ophalen. |
| <code>/S</code> | Een installatie op de achtergrond uitvoeren zonder gebruikersvragen. |

Bestanden voor de Windows-afhankelijkheids agent worden standaard in C:\Program Files\Microsoft dependency agent geplaatst.

### <a name="install-the-dependency-agent-on-linux"></a>De afhankelijkheids agent installeren in Linux

Toegang tot de hoofdmap is vereist om de agent te installeren of configureren.

De afhankelijkheids agent is geïnstalleerd op Linux-computers via InstallDependencyAgent-Linux64. bin, een shell script met een zelfuitpakkend binair bestand. U kunt het bestand uitvoeren met behulp van _sh_ of door aan het bestand zelf schrijfrechten toe te kennen.

Gebruik de volgende stappen om de afhankelijkheids agent te installeren op elke Linux-computer:

1. Installeer de Log Analytics agent volgens de stappen in [gegevens verzamelen van Linux-computers die in uw omgeving worden gehost](../vm/quick-collect-linux-computer.md#obtain-workspace-id-and-key).
2. Down load de Linux-afhankelijkheids agent met de koppeling in de vorige sectie en installeer deze met de volgende opdracht: sh InstallDependencyAgent-Linux64. bin
3. Als de afhankelijkheids agent niet kan worden gestart, raadpleegt u de logboeken voor gedetailleerde informatie over de fout. Voor Linux-agents is dit de logboekmap: /var/opt/microsoft/dependency-agent/log.

Als u een overzicht van de installatievlaggen wilt zien, voert u als volgt het installatieprogramma uit met behulp van de `-help`-vlag.

```
InstallDependencyAgent-Linux64.bin -help
```

| **Vlag** | **Beschrijving** |
| --- | --- |
| <code>-help</code> | Een lijst met de opdrachtregelopties ophalen. |
| <code>-s</code> | Een installatie op de achtergrond uitvoeren zonder gebruikersvragen. |
| <code>--check</code> | Controleer machtigingen en het besturingssysteem, maar installeer niet de agent. |

Bestanden voor de afhankelijkheids agent worden in de volgende directory's geplaatst:

| **Bestanden** | **Locatie** |
| --- | --- |
| Kernbestanden | /opt/microsoft/dependency-agent |
| Logboekbestanden | /var/opt/microsoft/dependency-agent/log |
| Configuratiebestanden | /etc/opt/microsoft/dependency-agent/config |
| Uitvoerbare bestanden van de service | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br><br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Binaire opslagbestanden | /var/opt/microsoft/dependency-agent/storage |

### <a name="installation-script-examples"></a>Voorbeelden van installatiescript

Om de afhankelijkheids agent eenvoudig op meerdere servers tegelijk te implementeren, is het handig om een script te gebruiken. U kunt de volgende script voorbeelden gebruiken om de afhankelijkheids agent te downloaden en te installeren op Windows of Linux.

#### <a name="powershell-script-for-windows"></a>PowerShell-script voor Windows

```powershell

Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S

```

#### <a name="shell-script-for-linux"></a>Shell-script voor Linux

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
```

```
sh InstallDependencyAgent-Linux64.bin -s
```

### <a name="desired-state-configuration"></a>Desired State Configuration

Als u de afhankelijkheids agent wilt implementeren via desired state Configuration, kunt u de xPSDesiredStateConfiguration-module en een stukje code gebruiken, zoals in het volgende:

```powershell
Import-DscResource -ModuleName xPSDesiredStateConfiguration

$DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"



Node $NodeName

{

    # Download and install the Dependency agent

    xRemoteFile DAPackage

    {

        Uri = "https://aka.ms/dependencyagentwindows"

        DestinationPath = $DAPackageLocalPath

        DependsOn = "[Package]OI"

    }

    xPackage DA

    {

        Ensure = "Present"

        Name = "Dependency Agent"

        Path = $DAPackageLocalPath

        Arguments = '/S'

        ProductId = ""

        InstalledCheckRegKey = "HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"

        InstalledCheckRegValueName = "DisplayName"

        InstalledCheckRegValueData = "Dependency Agent"

    }

}

```

### <a name="uninstall-the-dependency-agent"></a>De afhankelijkheids agent verwijderen

Gebruik de volgende secties om u te helpen de afhankelijkheids agent te verwijderen.

#### <a name="uninstall-the-dependency-agent-on-windows"></a>De afhankelijkheids agent in Windows verwijderen

Een beheerder kan de afhankelijkheids agent voor Windows verwijderen via het configuratie scherm.

Een beheerder kan ook%Programfiles%\Microsoft dependency Agent\Uninstall.exe uitvoeren om de afhankelijkheids agent te verwijderen.

#### <a name="uninstall-the-dependency-agent-on-linux"></a>De afhankelijkheids agent in Linux verwijderen

Als u de afhankelijkheids agent van Linux volledig wilt verwijderen, moet u de agent zelf en de connector verwijderen, die automatisch wordt geïnstalleerd met de agent. U kunt beide verwijderen met behulp van de volgende opdracht:

```
rpm -e dependency-agent dependency-agent-connector
```

## <a name="management-packs"></a>Management packs

Wanneer Wire Data wordt geactiveerd in een Log Analytics-werkruimte, wordt een management pack van 300 kB verzonden naar alle Windows-servers in die werkruimte. Als u System Center Operations Manager-agents in een [verbonden beheergroep](../agents/om-agents.md) gebruikt, wordt het management pack Afhankelijkheidsmonitor geïmplementeerd vanuit System Center Operations Manager. Als de agents rechtstreeks zijn verbonden, Azure Monitor levert de management pack.

De naam van het management pack is Microsoft.IntelligencePacks.ApplicationDependencyMonitor. Het management pack wordt geschreven naar: %Microsoft Monitoring Agent\Agent\Health Service State\Management Packs. De gegevensbron waarvan het management pack gebruikmaakt is: %Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources&lt;AutoGeneratedID&gt;\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="using-the-solution"></a>De oplossing gebruiken

Gebruik de volgende informatie om de oplossing te installeren en configureren.

- De oplossing Wire Data verkrijgt gegevens van computers met Windows Server 2012 R2, Windows 8.1 en latere besturingssystemen.
- Microsoft .NET Framework 4.0 of hoger is vereist op computers waarvan u draadgegevens wilt ophalen.
- Voeg de oplossing voor Wire data toe aan uw Log Analytics-werk ruimte met behulp van het proces dat wordt beschreven in [bewakings oplossingen toevoegen van de Oplossingengalerie](../insights/solutions.md). Er is geen verdere configuratie nodig.
- Als u draadgegevens voor een specifieke oplossing wilt weergeven, moet de oplossing al zijn toegevoegd aan uw werkruimte.

Nadat agents zijn geïnstalleerd en u de oplossing installeert, wordt de tegel Wire Data 2.0 weergegeven in de werkruimte.

![Tegel Wire Data](./media/wire-data/wire-data-tile.png)

## <a name="using-the-wire-data-20-solution"></a>De oplossing Wire Data 2.0 gebruiken

Op de pagina **Overzicht** voor uw Log Analytics-werkruimte in de Azure Portal klikt u op de tegel **Wire Data 2.0** om het dashboard van Wire Data te openen. Het dashboard bevat de blades in de volgende tabel. Elke blade bevat maximaal tien items die overeenkomen met de criteria voor het opgegeven bereik en de duur van die blade. U kunt zoeken in logboeken waarmee alle records worden geretourneerd door onderaan in de blade te klikken op **Alles bekijken** of door te klikken op de koptekst van de blade.

| **Blade** | **Beschrijving** |
| --- | --- |
| Agents waarmee het netwerkverkeer wordt vastgelegd | Toont het aantal agents dat netwerkverkeer vastlegt en geeft de eerste 10 computers weer die het meeste verkeer vastleggen. Klik op het nummer om zoeken in logboeken uit te voeren voor <code>WireData \| summarize sum(TotalBytes) by Computer \| take 500000</code>. Klik op een computer in de lijst om zoeken in logboeken uit te voeren waarmee het totale aantal vastgelegde bytes wordt geretourneerd. |
| Lokale subnetten | Toont het aantal lokale subnetten dat door agents is gedetecteerd.  Klik op het nummer om zoeken in logboeken uit te voeren voor <code>WireData \| summarize sum(TotalBytes) by LocalSubnet</code>, waarmee alle subnetten worden weergegeven met het aantal bytes dat via elk daarvan is verzonden. Klik op een subnet in de lijst om zoeken in logboeken uit te voeren waarmee het totale aantal via het subnet verzonden bytes wordt geretourneerd. |
| Protocollen op toepassingsniveau | Toont het aantal protocollen op toepassingsniveau in gebruik, zoals gedetecteerd door agents. Klik op het nummer om zoeken in logboeken uit te voeren voor <code>WireData \| summarize sum(TotalBytes) by ApplicationProtocol</code>. Klik op een protocol om zoeken in logboeken uit te voeren waarmee het totale aantal met het protocol verzonden bytes wordt geretourneerd. |

![Wire Data-dashboard](./media/wire-data/wire-data-dash.png)

U kunt de blade **Agents waarmee het netwerkverkeer wordt vastgelegd** gebruiken om te bepalen hoeveel netwerkbandbreedte wordt verbruikt door computers. Met deze blade kunt u gemakkelijk de _drukst bezette_ computer in uw omgeving terugvinden. Deze computers kunnen overbelast zijn, zich abnormaal gedragen of meer netwerkbronnen dan normaal gebruiken.

![Scherm afbeelding van de agents die de Blade netwerk verkeer vastleggen in het Wire Data 2.0 dash board met de netwerk bandbreedte die door elke computer wordt gebruikt.](./media/wire-data/log-search-example01.png)

Op soortgelijke wijze kunt u de blade **Lokale subnetten** gebruiken om te bepalen hoeveel netwerkverkeer via uw subnetten plaatsvindt. Gebruikers definiëren vaak subnetten rondom essentiële gebieden voor hun toepassingen. Deze blade geeft een beeld van die gebieden.

![Scherm opname van de Blade lokale subnetten in het Wire Data 2.0 dash board met de netwerk bandbreedte die wordt gebruikt door een LocalSubnet.](./media/wire-data/log-search-example02.png)

De blade **Protocollen op toepassingsniveau** is handig omdat u hiermee kunt vaststellen welke protocollen er worden gebruikt. U verwacht bijvoorbeeld dat SSH niet in uw netwerkomgeving wordt gebruikt. Aan de hand van de informatie in de blade kunt u dan snel vaststellen of die verwachting al dan niet is uitgekomen.

![Scherm opname van de Blade protocollen op toepassings niveau in het Wire Data 2.0 dash board met de netwerk bandbreedte die wordt gebruikt door elk protocol.](./media/wire-data/log-search-example03.png)

Het is ook handig om te weten als protocolverkeer gedurende een bepaalde periode toe- of afneemt. Als bijvoorbeeld de hoeveelheid gegevens die wordt verzonden door een toepassing toeneemt, is dat misschien iets waarmee u rekening wilt houden.

## <a name="input-data"></a>Invoergegevens

Wire Data verzamelt metagegevens over netwerkverkeer met behulp van de agents die u hebt ingeschakeld. Elke agent verzendt ongeveer elke 15 seconden gegevens.

## <a name="output-data"></a>Uitvoergegevens

Een record met het type _WireData_ is gemaakt voor elk type invoergegevens. WireData-records hebben eigenschappen die worden weergegeven in de volgende tabel:

| Eigenschap | Beschrijving |
|---|---|
| Computer | De naam van de computer waar gegevens zijn verzameld |
| TimeGenerated | Tijd van de record |
| LocalIP | IP-adres van de lokale computer |
| SessionState | Verbonden of verbroken |
| ReceivedBytes | Hoeveelheid ontvangen bytes |
| ProtocolName | Naam van het gebruikte netwerkprotocol |
| IPVersion | IP-versie |
| Richting | Binnenkomend of uitgaand |
| MaliciousIP | IP-adres van een bekende schadelijke bron |
| Severity | Ernst van mogelijke malware |
| RemoteIPCountry | Land/regio van het externe IP-adres |
| ManagementGroupName | Naam van de Operations Manager-beheergroep |
| SourceSystem | Bron waar gegevens zijn verzameld |
| SessionStartTime | Starttijd van de sessie |
| SessionEndTime | Eindtijd van de sessie |
| LocalSubnet | Subnet waar gegevens zijn verzameld |
| LocalPortNumber | Nummer van de lokale poort |
| RemoteIP | Extern IP-adres gebruikt door de externe computer |
| RemotePortNumber | Poortnummer gebruikt door het externe IP-adres |
| SessionID | Een unieke waarde die de communicatiesessie tussen twee IP-adressen aangeeft |
| SentBytes | Aantal verzonden bytes |
| TotalBytes | Totaal aantal bytes dat tijdens de sessie is verzonden |
| ApplicationProtocol | Type van het gebruikte netwerkprotocol   |
| ProcessID | Windows-proces-id |
| ProcessName | Pad en bestandsnaam van het proces |
| RemoteIPLongitude | Waarde IP-lengtegraad |
| RemoteIPLatitude | Waarde IP-breedtegraad |

## <a name="next-steps"></a>Volgende stappen

- [Zoeken in een logboek](../logs/log-query-overview.md) om gedetailleerde zoekrecords van Wire Data te bekijken.


---
title: Overzicht van Azure Automation Wijzigingen bijhouden en inventaris
description: In dit artikel wordt de functie Wijzigingen bijhouden en inventaris beschreven, waarmee u de wijzigingen in de software en micro soft-Services in uw omgeving kunt identificeren.
services: automation
ms.subservice: change-inventory-management
ms.date: 01/22/2021
ms.topic: conceptual
ms.openlocfilehash: ed29def305bfa33a0a947a331775de89275e5f7f
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220863"
---
# <a name="change-tracking-and-inventory-overview"></a>Overzicht Wijzigingen bijhouden en Inventaris

In dit artikel wordt beschreven hoe u Wijzigingen bijhouden en inventariseert in Azure Automation. Deze functie houdt wijzigingen bij van virtuele machines die worden gehost in azure, on-premises en andere Cloud omgevingen om u te helpen bij het lokaliseren van operationele en omgevings problemen met software die wordt beheerd door Distribution Package Manager. Items die worden bijgehouden door Wijzigingen bijhouden en inventaris omvatten:

- Windows-software
- Linux-software (pakketten)
- Windows-en Linux-bestanden
- Windows-register sleutels
- Micro soft-Services
- Linux-daemons

> [!NOTE]
> Zie de [wijzigings geschiedenis](../../governance/resource-graph/how-to/get-resource-changes.md)van de Azure-resource grafiek voor het bijhouden van wijzigingen in de eigenschappen van Azure Resource Manager.

Wijzigingen bijhouden en inventarisatie maakt gebruik van [Azure Security Center File Integrity Monitoring (FIM)](../../security-center/security-center-file-integrity-monitoring.md) om besturings systeem-en toepassings bestanden en Windows-REGI ster te onderzoeken. Hoewel FIM worden bewaakt door de entiteiten, worden Wijzigingen bijhouden en inventaris systeem eigen bijgehouden:

- Software wijzigingen
- Micro soft-Services
- Linux-daemons

Het inschakelen van alle functies in Wijzigingen bijhouden en de inventarisatie kan extra kosten in beslag nemen. Bekijk de [prijzen voor Automation](https://azure.microsoft.com/pricing/details/automation/) en [Azure monitor prijzen](https://azure.microsoft.com/pricing/details/monitor/)voordat u doorgaat.

Wijzigingen bijhouden-en inventarisatie gegevens worden door gegeven naar Azure Monitor-logboeken en deze verzamelde gegevens worden opgeslagen in een Log Analytics-werk ruimte. De functie File Integrity Monitoring (FIM) is alleen beschikbaar als **Azure Defender voor servers** is ingeschakeld. Zie Azure Security Center [prijzen](../../security-center/security-center-pricing.md) voor meer informatie. FIM uploadt gegevens naar dezelfde Log Analytics werkruimte als de werk ruimte die is gemaakt om gegevens op te slaan vanuit Wijzigingen bijhouden en de inventaris. U wordt aangeraden uw gekoppelde Log Analytics-werk ruimte te bewaken om uw exacte gebruik bij te houden. Zie [verbruik en kosten beheren](../../azure-monitor/logs/manage-cost-storage.md)voor meer informatie over het analyseren van het gegevens gebruik van Azure monitor Logboeken.

Computers die zijn verbonden met de Log Analytics-werk ruimte, gebruiken de [log Analytics agent](../../azure-monitor/agents/log-analytics-agent.md) voor het verzamelen van gegevens over wijzigingen in geïnstalleerde software, micro soft-Services, Windows-REGI ster en-bestanden en Linux-daemons op bewaakte servers. Wanneer de gegevens beschikbaar zijn, verzendt de agent deze naar Azure Monitor logboeken voor verwerking. Azure Monitor logboeken past logica toe op de ontvangen gegevens, registreert deze en maakt deze beschikbaar voor analyse.

> [!NOTE]
> Voor Wijzigingen bijhouden en inventarisatie moet u een Log Analytics werkruimte koppelen aan uw Automation-account. Zie [Azure Workspace-toewijzingen](../how-to/region-mappings.md)voor een definitieve lijst met ondersteunde regio's. De regio toewijzingen hebben geen invloed op de mogelijkheid om Vm's te beheren in een andere regio dan uw Automation-account.

Als service provider hebt u mogelijk meerdere tenants voor klanten in [Azure Lighthouse](../../lighthouse/overview.md). Met Azure Lighthouse kunt u in één keer bewerkingen uitvoeren op verschillende Azure Active Directory (Azure AD)-tenants, waardoor beheer taken, zoals Wijzigingen bijhouden en inventaris, efficiënter zijn voor de tenants die u wilt. Met Wijzigingen bijhouden en inventaris kunnen computers in meerdere abonnementen worden beheerd in dezelfde Tenant of via tenants met behulp van [Azure delegated resource management](../../lighthouse/concepts/azure-delegated-resource-management.md).

## <a name="current-limitations"></a>Huidige beperkingen

Wijzigingen bijhouden en inventarisatie biedt geen ondersteuning voor of heeft de volgende beperkingen:

- Recursie voor het bijhouden van Windows-REGI ster
- Netwerk bestands systemen
- Verschillende installatie methoden
- ***exe** -bestanden die zijn opgeslagen in Windows
- De kolom en waarden voor de **maximale bestands grootte** worden niet gebruikt in de huidige implementatie.
- Als u probeert meer dan 2500 bestanden te verzamelen in een verzamelings cyclus van 30 minuten, kunnen de prestaties van Wijzigingen bijhouden en de inventaris worden verslechterd.
- Als het netwerk verkeer hoog is, kan het tot zes uur duren voordat wijzigings records worden weer gegeven.
- Als u een configuratie wijzigt terwijl een machine of server is afgesloten, kunnen er wijzigingen worden aangebracht die horen bij de vorige configuratie.
- Het verzamelen van hotfix-updates op Windows Server 2016 core RS3-machines.
- Linux-daemons kunnen een gewijzigde status weer geven, zelfs als er geen wijziging heeft plaatsgevonden. Dit probleem doet zich voor vanwege de manier waarop de `SvcRunLevels` gegevens in de Azure monitor tabel [ConfigurationChange](/azure/azure-monitor/reference/tables/configurationchange) zijn geschreven.

## <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen

Wijzigingen bijhouden en inventarisatie wordt ondersteund op alle besturings systemen die voldoen aan de vereisten van Log Analytics agent. Zie [ondersteunde besturings systemen](../../azure-monitor/agents/agents-overview.md#supported-operating-systems) voor een lijst met versies van het Windows-en Linux-besturings systeem die momenteel worden ondersteund door de log Analytics-agent.

Zie [TLS 1,2 Enforcement voor Azure Automation](../automation-managing-data.md#tls-12-enforcement-for-azure-automation)voor meer informatie over de client vereisten voor TLS 1,2.

### <a name="python-requirement"></a>Python-vereiste

Wijzigingen bijhouden en inventaris bieden alleen ondersteuning voor Python2. Als uw computer gebruikmaakt van een distributie zonder python 2 standaard, dan moet u deze installeren. Met de volgende voorbeeld opdrachten wordt python 2 op verschillende distributies geïnstalleerd.

- Red Hat, CentOS, Oracle: `yum install -y python2`
- Ubuntu, Debian: `apt-get install -y python2`
- SuSE `zypper install -y python2`

Het uitvoer bare bestand van python2 moet zijn alias voor *python*.

## <a name="network-requirements"></a>Netwerkvereisten

Controleer [Azure Automation netwerk configuratie](../automation-network-configuration.md#update-management-and-change-tracking-and-inventory) voor gedetailleerde informatie over de poorten, url's en andere netwerk gegevens die nodig zijn voor wijzigingen bijhouden en inventaris.

## <a name="enable-change-tracking-and-inventory"></a>Wijzigingen bijhouden en Inventaris inschakelen

U kunt Wijzigingen bijhouden en inventaris op de volgende manieren inschakelen:

- Van uw [Automation-account](enable-from-automation-account.md) voor een of meer Azure-en niet-Azure-machines.

- Hand matig voor niet-Azure-machines, waaronder computers of servers die zijn geregistreerd bij [servers met Azure Arc](../../azure-arc/servers/overview.md). Voor hybride computers raden wij u aan om de Log Analytics-agent voor Windows te installeren door eerst uw computer te verbinden met [servers met Azure-Arc](../../azure-arc/servers/overview.md)en vervolgens Azure Policy te gebruiken om de implementatie van [log Analytics agent toe te wijzen aan het ingebouwde beleid voor *Linux* of *Windows* Azure Arc-machines](../../governance/policy/samples/built-in-policies.md#monitoring) . Als u van plan bent om ook de machines met Azure Monitor voor VM's te bewaken, moet u in plaats daarvan het Azure Monitor voor VM's-initiatief [inschakelen](../../governance/policy/samples/built-in-initiatives.md#monitoring) .

- Voor één Azure-VM vanaf de [pagina virtuele machine](enable-from-vm.md) in het Azure Portal. Dit scenario is beschikbaar voor Linux-en Windows-Vm's.

- Voor [meerdere virtuele Azure-machines](enable-from-portal.md) door ze te selecteren op de pagina virtual machines in de Azure Portal.

## <a name="tracking-file-changes"></a>Bestands wijzigingen bijhouden

Voor het bijhouden van wijzigingen in bestanden op Windows en Linux maakt Wijzigingen bijhouden en inventarisatie gebruik van MD5-hashes van de bestanden. De functie maakt gebruik van de hashes om te detecteren of er wijzigingen zijn aangebracht sinds de laatste inventarisatie.

## <a name="tracking-file-content-changes"></a>Wijzigingen in bestands inhoud bijhouden

Met Wijzigingen bijhouden en inventaris kunt u de inhoud van een Windows-of Linux-bestand weer geven. Voor elke wijziging in een bestand bevat Wijzigingen bijhouden en inventaris de inhoud van het bestand in een [Azure Storage-account](../../storage/common/storage-account-create.md). Wanneer u een bestand volgt, kunt u de inhoud ervan weer geven vóór of na een wijziging. De bestands inhoud kan in line of naast elkaar worden weer gegeven.

![Wijzigingen in een bestand weer geven](./media/overview/view-file-changes.png)

## <a name="tracking-of-registry-keys"></a>Register sleutels bijhouden

Met Wijzigingen bijhouden en inventaris kunt u wijzigingen in Windows-register sleutels controleren. Met bewaking kunt u uitbreid bare punten herkennen waarbij code en malware van derden kunnen worden geactiveerd. De volgende tabel geeft een lijst van vooraf geconfigureerde register sleutels (maar niet ingeschakeld). Als u deze sleutels wilt bijhouden, moet u deze inschakelen.

> [!div class="mx-tdBreakAll"]
> |Registersleutel | Doel |
> | --- | --- |
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup` | Bewaakt de scripts die worden uitgevoerd bij het opstarten.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown` | Bewaakt de scripts die worden uitgevoerd bij het afsluiten.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run` | Bewaakt sleutels die worden geladen voordat de gebruiker zich aanmeldt bij het Windows-account. De sleutel wordt gebruikt voor 32-bits toepassingen die worden uitgevoerd op 64-bits computers.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components` | Hiermee worden wijzigingen in toepassings instellingen gecontroleerd.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Bewaakt context menu-handlers die rechtstreeks in Windows Verkenner zijn aangesloten en die gewoonlijk in-process worden uitgevoerd met **explorer.exe**.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers` | Hiermee worden Kopieer Hook-handlers gecontroleerd die rechtstreeks in Windows Verkenner zijn aangesloten en normaal gesp roken met **explorer.exe** worden uitgevoerd.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Monitor voor de registratie van het pictogram-overlay-handler.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Monitors voor de registratie van pictogram-overlaysoftware-handler voor 32-bits toepassingen die worden uitgevoerd op 64-bits computers.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Monitors voor nieuwe browserhelperobjecten voor Internet Explorer. Wordt gebruikt om toegang te krijgen tot de Document Object Model (DOM) van de huidige pagina en om de navigatie te beheren.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Monitors voor nieuwe browserhelperobjecten voor Internet Explorer. Wordt gebruikt voor toegang tot de Document Object Model (DOM) van de huidige pagina en voor het beheren van de navigatie voor 32-bits toepassingen die worden uitgevoerd op 64-bits computers.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Internet Explorer\Extensions` | Controleert op nieuwe Internet Explorer-uitbrei dingen, zoals menu's voor aangepaste gereedschappen en aangepaste werkbalk knoppen.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions` | Controleert op nieuwe Internet Explorer-uitbrei dingen, zoals aangepaste hulpprogramma menu's en aangepaste werkbalk knoppen voor 32-bits toepassingen die worden uitgevoerd op 64-bits computers.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32` | Bewaakt 32-bits Stuur Programma's die zijn gekoppeld aan wavemapper, wave1 en wave2, Msacm. imaadpcm,. msadpcm,. msgsm610 en vidc. Vergelijkbaar met de sectie [drivers] in het **system.ini** -bestand.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32` | Bewaakt 32-bits Stuur Programma's die zijn gekoppeld aan wavemapper, wave1 en wave2, Msacm. imaadpcm,. msadpcm,. msgsm610 en vidc voor 32-bits-toepassingen die worden uitgevoerd op 64-bits computers. Vergelijkbaar met de sectie [drivers] in het **system.ini** -bestand.
> |`HKEY\LOCAL\MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls` | Hiermee wordt de lijst met bekende of veelgebruikte systeem-Dll's gecontroleerd. Door te controleren wordt voor komen dat gebruikers zwakke machtigingen voor toepassings mappen kunnen exploiteren door de Trojaanse paarden-versies van systeem-Dll's te verwijderen.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify` | Bewaakt de lijst met pakketten die gebeurtenis meldingen kunnen ontvangen van **winlogon.exe**, het model voor ondersteuning van interactieve aanmelding voor Windows.

## <a name="recursion-support"></a>Ondersteuning voor recursie

Wijzigingen bijhouden en inventarisatie ondersteunt recursie, waarmee u Joker tekens kunt opgeven om het bijhouden van meerdere mappen te vereenvoudigen. Recursie biedt ook omgevings variabelen waarmee u bestanden kunt volgen in omgevingen met meerdere of dynamische stations. De volgende lijst bevat algemene informatie die u moet kennen bij het configureren van recursie:

- Joker tekens zijn vereist voor het bijhouden van meerdere bestanden.

- U kunt alleen joker tekens gebruiken in het laatste segment van een bestandspad, bijvoorbeeld **c:\folder \\ File** _ of _ */etc/*. conf * *.

- Als een omgevings variabele een ongeldig pad heeft, wordt de validatie uitgevoerd, maar het pad mislukt tijdens de uitvoering.

- Vermijd algemene padnamen bij het instellen van het pad, omdat dit type instelling ertoe kan leiden dat er te veel mappen worden gepasseerd.

## <a name="change-tracking-and-inventory-data-collection"></a>Gegevens verzameling Wijzigingen bijhouden en inventaris

In de volgende tabel wordt de frequentie van gegevens verzameling weer gegeven voor de typen wijzigingen die worden ondersteund door Wijzigingen bijhouden en inventaris. Voor elk type wordt de gegevens momentopname van de huidige status ook ten minste elke 24 uur vernieuwd.

| **Type wijzigen** | **Frequentie** |
| --- | --- |
| Windows-REGI ster | 50 minuten |
| Windows-bestand | 30 minuten |
| Linux-bestand | 15 minuten |
| Micro soft-Services | 10 seconden tot 30 minuten</br> Standaard: 30 minuten |
| Linux-daemons | 5 minuten |
| Windows-software | 30 minuten |
| Linux-software | 5 minuten |

De volgende tabel bevat de limieten voor bijgehouden items per computer voor Wijzigingen bijhouden en inventaris.

| **Resource** | **Limiet** |
|---|---|---|
|File|500|
|Register|250|
|Windows-software (exclusief hotfixes) |250|
|Linux-pakketten|1250|
|Services|250|
|Daemons|250|

Het gemiddelde Log Analytics gegevens gebruik voor een machine met Wijzigingen bijhouden en inventaris is ongeveer 40 MB per maand, afhankelijk van uw omgeving. Met de functie voor gebruik en geschatte kosten van de Log Analytics-werk ruimte kunt u de gegevens weer geven die zijn opgenomen door Wijzigingen bijhouden en inventaris in een gebruiks diagram. Gebruik deze gegevens weergave om uw gegevens gebruik te evalueren en te bepalen hoe deze van invloed is op uw factuur. Bekijk [inzicht in uw gebruik en geschatte kosten](../../azure-monitor/logs/manage-cost-storage.md#understand-your-usage-and-estimate-costs).

### <a name="microsoft-service-data"></a>Micro soft-service gegevens

De standaard frequentie voor het verzamelen van micro soft-Services is 30 minuten. U kunt de frequentie configureren met behulp van een schuif regelaar op het tabblad **micro soft-Services** onder **Instellingen bewerken**.

![Micro soft Services-schuif regelaar](./media/overview/windowservices.png)

Om de prestaties te optimaliseren, houdt de Log Analytics agent alleen wijzigingen bij. Het instellen van een hoge drempel waarde kan wijzigingen missen als de service naar de oorspronkelijke staat terugkeert. Door de frequentie in te stellen op een lagere waarde, kunt u wijzigingen ondervangen die anders kunnen worden gemist.

> [!NOTE]
> Hoewel de agent wijzigingen kan bijhouden in een interval van 10 seconden, duurt het enkele minuten voordat de gegevens in de Azure Portal worden weer gegeven. Wijzigingen die zich voordoen tijdens de tijd die in de portal worden weer gegeven, worden nog steeds bijgehouden en geregistreerd.

## <a name="support-for-alerts-on-configuration-state"></a>Ondersteuning voor waarschuwingen over de configuratie status

Een belang rijke mogelijkheid van Wijzigingen bijhouden en inventarisatie wordt gewaarschuwd voor wijzigingen in de configuratie status van uw hybride omgeving. Er zijn veel nuttige acties beschikbaar om te activeren als reactie op waarschuwingen. Bijvoorbeeld acties voor Azure functions, Automation-runbooks, webhooks en dergelijke. Een waarschuwing over wijzigingen in het **c:\Windows\System32\drivers\etc\hosts** -bestand voor een machine is een goede toepassing van waarschuwingen voor wijzigingen bijhouden-en inventaris gegevens. Er zijn veel meer scenario's voor waarschuwingen, zoals de query scenario's die in de volgende tabel zijn gedefinieerd.

|Query  |Beschrijving  |
|---------|---------|
|ConfigurationChange <br>&#124; waarbij ConfigChangeType = = "files" en FileSystemPath bevat "c: \\ Windows \\ System32 \\ drivers \\ "|Dit is handig voor het bijhouden van wijzigingen in essentiële bestanden van het systeem.|
|ConfigurationChange <br>&#124; waarbij FieldsChanged ' FileContentChecksum ' en FileSystemPath = = "c: \\ Windows \\ System32 \\ drivers etc- \\ \\ hosts ' bevat.|Dit is handig voor het bijhouden van wijzigingen in sleutel configuratie bestanden.|
|ConfigurationChange <br>&#124; waarbij ConfigChangeType = = "WindowsServices" en SvcName bevat "W3SVC" en SvcState = = "gestopt"|Dit is handig voor het bijhouden van wijzigingen in essentiële services van het systeem.|
|ConfigurationChange <br>&#124; waarbij ConfigChangeType = = "daemons" en SvcName bevat "SSH" en SvcState! = "running"|Dit is handig voor het bijhouden van wijzigingen in essentiële services van het systeem.|
|ConfigurationChange <br>&#124; waarbij ConfigChangeType = = "software" en ChangeCategory = = "toegevoegd"|Dit is handig voor omgevingen waarvoor een vergrendelde software configuratie nodig is.|
|ConfigurationData <br>&#124; waarbij de softwarenaam "bewakings agent" en CurrentVersion! = "8.0.11081.0" bevat|Nuttig als u wilt zien op welke computers een verouderde of niet-compatibele software versie is geïnstalleerd. Deze query rapporteert de laatst gerapporteerde configuratie status, maar brengt geen wijzigingen aan.|
|ConfigurationChange <br>&#124; waarbij RegistryKey = = @ "HKEY_LOCAL_MACHINE \\ software \\ micro soft \\ Windows \\ CurrentVersion \\ QualityCompat"| Dit is handig voor het bijhouden van wijzigingen in essentiële antivirus sleutels.|
|ConfigurationChange <br>&#124; waarbij RegistryKey @ "HKEY_LOCAL_MACHINE \\ System \\ CurrentControlSet \\ Services \\ SharedAccess \\ para meters \\ FirewallPolicy" bevat| Dit is handig voor het bijhouden van wijzigingen in Firewall-instellingen.|

## <a name="next-steps"></a>Volgende stappen

- Zie [Wijzigingen bijhouden en inventaris inschakelen vanuit een Automation-account](enable-from-automation-account.md)om in te scha kelen op basis van een Automation-account.

- Als u de Azure Portal wilt inschakelen, raadpleegt u [Wijzigingen bijhouden en inventaris inschakelen in de Azure Portal](enable-from-portal.md).

- Zie [Wijzigingen bijhouden en inventaris inschakelen vanuit een runbook](enable-from-runbook.md)om een runbook in te scha kelen.

- Zie [Wijzigingen bijhouden en inventaris inschakelen op een](enable-from-vm.md)virtuele machine van Azure voor meer informatie over het inschakelen van een virtuele Azure-machine.

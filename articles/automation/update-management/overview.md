---
title: Overzicht van Azure Automation Updatebeheer
description: Dit artikel bevat een overzicht van de functie Updatebeheer die updates implementeert voor uw Windows-en Linux-computers.
services: automation
ms.subservice: update-management
ms.date: 11/23/2020
ms.topic: conceptual
ms.openlocfilehash: eadceb17d01b40deab656bdd6223cb8f0844f54f
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95528241"
---
# <a name="update-management-overview"></a>Overzicht van updatebeheer

U kunt Updatebeheer in Azure Automation gebruiken om updates van besturings systemen te beheren voor uw virtuele Windows-en Linux-machines in azure, in on-premises omgevingen en in andere Cloud omgevingen. U kunt snel de status van beschik bare updates op alle agent computers beoordelen en het proces voor het installeren van vereiste updates voor servers beheren.

> [!NOTE]
> U kunt een computer die is geconfigureerd met Updatebeheer niet gebruiken om aangepaste scripts uit Azure Automation uit te voeren. Op deze computer kan alleen het door micro soft ondertekende update script worden uitgevoerd.

> [!NOTE]
> Op dit moment wordt het rechtstreeks inschakelen van Updatebeheer vanaf een server met Azure Arc niet ondersteund. Raadpleeg [Updatebeheer inschakelen vanuit uw Automation-account](../../automation/update-management/enable-from-automation-account.md) om te zien wat de vereisten zijn en hoe u updatebeheer voor uw server inschakelt.

Als u de beschik bare *essentiële* en *beveiligings* patches automatisch op uw Azure-VM wilt downloaden en installeren, controleert u [automatische VM-gast patches](../../virtual-machines/windows/automatic-vm-guest-patching.md) voor Windows-vm's.

Voordat u Updatebeheer implementeert en uw machines voor beheer inschakelt, moet u de informatie in de volgende secties begrijpen.  

## <a name="about-update-management"></a>Over Updatebeheer

Computers die worden beheerd door Updatebeheer, zijn afhankelijk van het volgende om een evaluatie uit te voeren en updates te implementeren:

* [Log Analytics-agent](../../azure-monitor/platform/log-analytics-agent.md) voor Windows of Linux
* PowerShell Desired State Configuration (DSC) voor Linux
* Automation-Hybrid Runbook Worker (automatisch geïnstalleerd wanneer u Updatebeheer op de computer inschakelt)
* Microsoft Update of [Windows Server Update Services](/windows-server/administration/windows-server-update-services/get-started/windows-server-update-services-wsus) (WSUS) voor Windows-computers
* Een privé-of open bare update opslagplaats voor Linux-machines

In het volgende diagram ziet u hoe Updatebeheer beveiligings updates evalueert en toepast op alle verbonden Windows Server-en Linux-servers in een werk ruimte:

![Updatebeheer werk stroom](./media/overview/update-mgmt-updateworkflow.png)

Updatebeheer kan worden gebruikt om systeem eigen te implementeren op computers in meerdere abonnementen in dezelfde Tenant.

Nadat een pakket is vrijgegeven, duurt het 2 tot 3 uur voordat de patch wordt weer gegeven voor Linux-machines voor evaluatie. Voor Windows-computers duurt het 12 tot 15 uur voordat de patch wordt weer gegeven voor evaluatie nadat deze is uitgebracht. Wanneer een computer een scan voor de compatibiliteit van updates voltooit, stuurt de agent de gegevens bulksgewijs door naar Azure Monitor Logboeken. Op een Windows-computer wordt de compatibiliteits scan standaard elke 12 uur uitgevoerd. Voor een Linux-computer wordt standaard elk uur de compatibiliteits scan uitgevoerd. Als de Log Analytics-agent opnieuw is opgestart, wordt een nalevings scan binnen vijf tien minuten gestart.

Naast het scan schema wordt de controle op update vereisten binnen 15 minuten gestart nadat de Log Analytics agent opnieuw is opgestart, vóór de installatie van de update en na de installatie van de update.

Updatebeheer rapporteert u hoe up-to-date de machine is gebaseerd op de bron die u hebt geconfigureerd om te synchroniseren met. Als de Windows-computer is geconfigureerd om te rapporteren aan [Windows Server Update Services](/windows-server/administration/windows-server-update-services/get-started/windows-server-update-services-wsus) (WSUS), afhankelijk van de laatste synchronisatie van wsus met Microsoft Update, kunnen de resultaten afwijken van wat Microsoft Update laat zien. Dit gedrag is hetzelfde voor Linux-machines die zijn geconfigureerd om te rapporteren aan een lokale opslag plaats in plaats van naar een open bare opslag plaats.

> [!NOTE]
> Updatebeheer moet bepaalde Url's en poorten zijn ingeschakeld om de service goed te kunnen melden. Zie [netwerk configuratie](../automation-hybrid-runbook-worker.md#network-planning)voor meer informatie over deze vereisten.

U kunt software-updates implementeren en installeren op computers die de updates nodig hebben door een geplande implementatie te maken. Updates die zijn geclassificeerd als optioneel, worden niet opgenomen in het implementatie bereik voor Windows-machines. Alleen vereiste updates zijn opgenomen in het implementatie bereik.

De geplande implementatie definieert welke doel computers de toepasselijke updates ontvangen. Dit doet u door bepaalde computers expliciet op te geven of door een [computer groep](../../azure-monitor/platform/computer-groups.md) te selecteren die is gebaseerd op logboek zoekopdrachten van een specifieke set machines (of een [Azure-query](query-logs.md) waarmee dynamisch virtuele Azure-machines worden geselecteerd op basis van opgegeven criteria). Deze groepen wijken af van de [Scope configuratie](../../azure-monitor/insights/solution-targeting.md), die wordt gebruikt voor het beheren van het doel van machines die de configuratie ontvangen om updatebeheer in te scha kelen. Zo wordt voor komen dat ze update vereisten kunnen uitvoeren en rapporteren en goedgekeurde vereiste updates installeren.

Bij het definiëren van een implementatie moet u ook een schema opgeven dat u wilt goed keuren en een periode instellen waarin updates kunnen worden geïnstalleerd. Deze periode wordt het onderhouds venster genoemd. Een periode van 20 minuten van het onderhouds venster is gereserveerd voor opnieuw opstarten, ervan uitgaande dat er een is vereist en u de juiste opstart optie hebt geselecteerd. Als de patch langer duurt dan verwacht en er minder dan 20 minuten in het onderhouds venster wordt weer gegeven, wordt de computer niet opnieuw opgestart.

Updates worden geïnstalleerd door runbooks in Azure Automation. U kunt deze runbooks niet weer geven en er is geen configuratie vereist. Wanneer een update-implementatie wordt gemaakt, wordt er een planning gemaakt waarmee een Master update-runbook wordt gestart op het opgegeven tijdstip voor de inbegrepen computers. Het hoofd-runbook start een onderliggend runbook op elke agent om de vereiste updates te installeren.

Op de datum en tijd die zijn opgegeven in de update-implementatie, wordt de implementatie parallel uitgevoerd op de doel computers. Voorafgaand aan de installatie wordt een scan uitgevoerd om te controleren of de updates nog steeds vereist zijn. Voor WSUS-client computers, als de updates niet zijn goedgekeurd in WSUS, mislukt de update-implementatie.

Het is niet mogelijk om een computer te registreren voor Updatebeheer in meer dan één Log Analytics-werk ruimte (ook wel multihoming genoemd).

## <a name="clients"></a>Clients

### <a name="supported-client-types"></a>Ondersteunde client typen

De volgende tabel geeft een lijst van de ondersteunde besturings systemen voor update-evaluaties en patches. Voor patching is een Hybrid Runbook Worker vereist, dat automatisch wordt geïnstalleerd wanneer u de virtuele machine of server voor beheer inschakelt door Updatebeheer. Zie een [Windows-Hybrid Runbook worker implementeren](../automation-windows-hrw-install.md) en een [Linux-Hybrid Runbook worker implementeren](../automation-linux-hrw-install.md)voor meer informatie over Hybrid Runbook worker systeem vereisten.

> [!NOTE]
> Update-evaluatie van Linux-machines wordt alleen ondersteund in bepaalde regio's, zoals vermeld in het Automation-account en de tabel Log Analytics werkruimte [toewijzingen](../how-to/region-mappings.md#supported-mappings).

|Besturingssysteem  |Opmerkingen  |
|---------|---------|
|Windows Server 2019 (Data Center/Data Center core/Standard)<br><br>Windows Server 2016 (Data Center/Data Center core/Standard)<br><br>Windows Server 2012 R2 (Data Center/Standard)<br><br>Windows Server 2012 ||
|Windows Server 2008 R2 (RTM en SP1 Standard)| Updatebeheer ondersteunt evaluaties en patches voor dit besturings systeem. De [Hybrid Runbook worker](../automation-windows-hrw-install.md) wordt ondersteund voor Windows Server 2008 R2. |
|CentOS 6 en 7 (x64)      | Linux-agents moeten toegang hebben tot een update opslagplaats. Voor op classificaties gebaseerde patches moeten `yum` beveiligings gegevens worden geretourneerd die CentOS niet hebben in de RTM-releases. Zie [Update classificaties in Linux](view-update-assessments.md#linux)voor meer informatie over op CentOS gebaseerde patches op basis van classificatie.          |
|Red Hat Enter prise 6 en 7 (x64)     | Linux-agents moeten toegang hebben tot een update opslagplaats.        |
|SUSE Linux Enterprise Server 12 (x64)     | Linux-agents moeten toegang hebben tot een update opslagplaats.        |
|Ubuntu 14,04 LTS, 16,04 LTS en 18,04 (x64)      |Linux-agents moeten toegang hebben tot een update opslagplaats.         |

> [!NOTE]
> Virtuele-machine schaal sets van Azure kunnen worden beheerd via Updatebeheer. Updatebeheer werkt op de instanties zelf en niet op basis van de installatie kopie. U moet de updates op een incrementele manier plannen, zodat niet alle VM-exemplaren tegelijk worden bijgewerkt. U kunt knoop punten voor virtuele-machine schaal sets toevoegen door de stappen onder [een niet-Azure-computer toevoegen aan wijzigingen bijhouden en inventarisatie te](../automation-tutorial-installed-software.md#add-a-non-azure-machine-to-change-tracking-and-inventory)volgen.

### <a name="unsupported-client-types"></a>Niet-ondersteunde client typen

De volgende tabel bevat een lijst met niet-ondersteunde besturings systemen:

|Besturingssysteem  |Opmerkingen  |
|---------|---------|
|Windows-client     | Client besturingssystemen (zoals Windows 7 en Windows 10) worden niet ondersteund.<br> Voor Azure Windows virtueel bureau blad (WVD), de aanbevolen methode<br> voor het beheren van updates is [micro soft Endpoint Configuration Manager](../../virtual-desktop/configure-automatic-updates.md) voor patch beheer voor Windows 10-client computers. |
|Windows Server 2016 Nano Server     | Niet ondersteund.       |
|Azure Kubernetes-service knooppunten | Niet ondersteund. Gebruik het patch proces dat wordt beschreven in [beveiligings-en kernel-updates Toep assen op Linux-knoop punten in azure Kubernetes service (AKS)](../../aks/node-updates-kured.md)|

### <a name="client-requirements"></a>Clientvereisten

De volgende informatie beschrijft specifieke client vereisten voor het besturings systeem. Zie [netwerk planning](#ports)voor meer informatie. Zie [TLS 1,2 Enforcement voor Azure Automation](../automation-managing-data.md#tls-12-enforcement-for-azure-automation)voor meer informatie over de client vereisten voor TLS 1,2.

#### <a name="windows"></a>Windows

Windows-agents moeten worden geconfigureerd om te communiceren met een WSUS-server of moeten toegang hebben tot Microsoft Update. Voor hybride computers raden wij u aan om de Log Analytics-agent voor Windows te installeren door eerst uw computer te verbinden met [servers met Azure-Arc](../../azure-arc/servers/overview.md)en vervolgens Azure Policy te gebruiken om de implementatie van [log Analytics agent toe te wijzen aan](../../governance/policy/samples/built-in-policies.md#monitoring) het ingebouwde beleid van Windows Azure Arc-machines. Als u van plan bent om de machines met Azure Monitor voor VM's te bewaken, moet u in plaats daarvan het Azure Monitor voor VM's-initiatief [inschakelen](../../governance/policy/samples/built-in-initiatives.md#monitoring) .

U kunt Updatebeheer gebruiken met micro soft endpoint Configuration Manager. Zie [updatebeheer integreren met Windows-eind punt Configuration Manager](mecmintegration.md)voor meer informatie over integratie scenario's. De [log Analytics-agent voor Windows](../../azure-monitor/platform/agent-windows.md) is vereist voor Windows-servers die worden beheerd door sites in uw Configuration Manager omgeving. 

Standaard worden Windows-Vm's die zijn geïmplementeerd vanuit Azure Marketplace ingesteld voor het ontvangen van automatische updates van Windows Update service. Dit gedrag verandert niet wanneer u Windows-Vm's toevoegt aan uw werk ruimte. Als u updates niet actief beheert door gebruik te maken van Updatebeheer, is het standaard gedrag van toepassing (om updates automatisch toe te passen).

> [!NOTE]
> U kunt groepsbeleid wijzigen zodat het opnieuw opstarten van de machine alleen door de gebruiker kan worden uitgevoerd, niet door het systeem. Beheerde computers kunnen vastraken als Updatebeheer geen rechten heeft om de computer opnieuw op te starten zonder hand matige interactie van de gebruiker. Zie [Groepsbeleid instellingen voor automatische updates configureren](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates)voor meer informatie.

#### <a name="linux"></a>Linux

Voor Linux moet de computer toegang hebben tot een update opslagplaats, ofwel persoonlijk of openbaar. TLS 1,1 of TLS 1,2 is vereist voor de interactie met Updatebeheer. Updatebeheer biedt geen ondersteuning voor een Log Analytics agent voor Linux die is geconfigureerd om te rapporteren aan meer dan één Log Analytics-werk ruimte. Op de computer moet ook python 2. x zijn geïnstalleerd.

> [!NOTE]
> Update-evaluatie van Linux-machines wordt alleen ondersteund in bepaalde regio's. Zie het Automation-account en de tabel met Log Analytics werkruimte [toewijzingen](../how-to/region-mappings.md#supported-mappings).

Voor hybride computers raden we u aan om de Log Analytics-agent voor Linux te installeren door eerst uw computer te verbinden met [servers met Azure-Arc](../../azure-arc/servers/overview.md)en vervolgens Azure Policy te gebruiken om de implementatie van [log Analytics agent toe te wijzen aan het ingebouwde beleid voor Linux Azure Arc-machines](../../governance/policy/samples/built-in-policies.md#monitoring) . Als u van plan bent om de machines met Azure Monitor voor VM's te bewaken, moet u in plaats daarvan het Azure Monitor voor VM's-initiatief [inschakelen](../../governance/policy/samples/built-in-initiatives.md#monitoring) .

Vm's die zijn gemaakt op basis van de installatie kopieën op Red Hat Enterprise Linux aanvraag (RHEL) die beschikbaar zijn in azure Marketplace, worden geregistreerd voor toegang tot de [Red Hat Update infrastructure (RHUI)](../../virtual-machines/workloads/redhat/redhat-rhui.md) die is geïmplementeerd in Azure. Elke andere Linux-distributie moet worden bijgewerkt vanuit de online bestands opslagplaats van de distributie met behulp van methoden die worden ondersteund door de distributie.

## <a name="permissions"></a>Machtigingen

U hebt specifieke machtigingen nodig om update-implementaties te maken en te beheren. Zie op [rollen gebaseerde toegang – updatebeheer](../automation-role-based-access-control.md#update-management-permissions)voor meer informatie over deze machtigingen.

## <a name="update-management-components"></a>Updatebeheer onderdelen

Updatebeheer maakt gebruik van de resources die in deze sectie worden beschreven. Deze resources worden automatisch toegevoegd aan uw Automation-account wanneer u Updatebeheer inschakelt.

### <a name="hybrid-runbook-worker-groups"></a>Hybrid Runbook Worker groepen

Nadat u Updatebeheer hebt ingeschakeld, wordt elke Windows-computer die rechtstreeks is verbonden met uw Log Analytics-werk ruimte automatisch geconfigureerd als een Hybrid Runbook Worker ter ondersteuning van de runbooks die Updatebeheer ondersteunen.

Elke Windows-computer die wordt beheerd door Updatebeheer wordt weer gegeven in het deel venster Hybrid worker Groups als een Hybrid worker-groep voor het Automation-account. De groepen gebruiken de `Hostname FQDN_GUID` naamgevings Conventie. U kunt deze groepen niet richten op runbooks in uw account. Als u probeert, mislukt de poging. Deze groepen zijn bedoeld om alleen Updatebeheer te ondersteunen. Zie [Hybrid Runbook Workers weer geven](../automation-hybrid-runbook-worker.md#view-system-hybrid-runbook-workers)voor meer informatie over het weer geven van de lijst met Windows-machines die als Hybrid Runbook worker zijn geconfigureerd.

U kunt de Windows-computer toevoegen aan een Hybrid Runbook Worker groep in uw Automation-account ter ondersteuning van Automation-runbooks als u hetzelfde account gebruikt voor Updatebeheer en het lidmaatschap van de Hybrid Runbook Worker-groep. Deze functionaliteit is toegevoegd aan versie 7.2.12024.0 van de Hybrid Runbook Worker.

### <a name="management-packs"></a>Management packs

Als uw Operations Manager-beheer groep is [verbonden met een log Analytics-werk ruimte](../../azure-monitor/platform/om-agents.md), worden de volgende Management Packs in Operations Manager geïnstalleerd. Deze Management Packs zijn ook geïnstalleerd voor Updatebeheer op rechtstreeks verbonden Windows-computers. U hoeft deze management packs niet te configureren of te beheren.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Implementatie MP bijwerken

> [!NOTE]
> Als u een Operations Manager 1807-of 2019-beheer groep hebt verbonden met een Log Analytics-werk ruimte met agents die zijn geconfigureerd in de beheer groep om logboek gegevens te verzamelen, moet u de para meter overschrijven `IsAutoRegistrationEnabled` en instellen op waar in de **Microsoft.IntelligencePacks.AzureAutomation.HybridAgent.Init** -regel.

Zie [Connect Operations Manager to Azure monitor logs](../../azure-monitor/platform/om-agents.md)(Engelstalig) voor meer informatie over updates voor Management Packs.

> [!NOTE]
> Updatebeheer om computers met de Log Analytics agent volledig te beheren, moet u bijwerken naar de Log Analytics agent voor Windows of de Log Analytics-agent voor Linux. Zie [een Operations Manager-agent bijwerken](/system-center/scom/deploy-upgrade-agents)voor meer informatie over het bijwerken van de agent. In omgevingen waarin Operations Manager wordt gebruikt, moet u System Center Operations Manager 2012 R2 UR 14 of hoger uitvoeren.

## <a name="data-collection"></a>Gegevensverzameling

### <a name="supported-sources"></a>Ondersteunde bronnen

De volgende tabel beschrijft de verbonden bronnen die Updatebeheer ondersteunt:

| Verbonden bron | Ondersteund | Beschrijving |
| --- | --- | --- |
| Windows-agents |Ja |Updatebeheer verzamelt informatie over systeem updates van Windows-agents en start de installatie van de vereiste updates. |
| Linux-agents |Ja |Updatebeheer verzamelt informatie over systeem updates van Linux-agents en start de installatie van vereiste updates op ondersteunde distributies. |
| Beheergroep Operations Manager |Ja |Updatebeheer verzamelt informatie over systeem updates van agents in een verbonden beheer groep.<br/><br/>Een directe verbinding van de Operations Manager agent naar Azure Monitor-Logboeken is niet vereist. Gegevens worden doorgestuurd van de beheer groep naar de Log Analytics-werk ruimte. |

### <a name="collection-frequency"></a>Verzamelingsfrequentie

Updatebeheer scant beheerde machines op gegevens aan de hand van de volgende regels. Het kan tussen 30 minuten en 6 uur duren voordat het dash board bijgewerkte gegevens van beheerde computers weergeeft.

* Elke Windows-Updatebeheer voor machines voert een scan twee keer per dag uit voor elke computer.

* Elke Linux-machine-Updatebeheer voert een scan elk uur uit.

Het gemiddelde gegevens gebruik door Azure Monitor logboeken voor een machine met behulp van Updatebeheer is ongeveer 25 MB per maand. Deze waarde is alleen een benadering en is onderhevig aan wijzigingen, afhankelijk van uw omgeving. U wordt aangeraden uw omgeving te bewaken om uw exacte gebruik bij te houden. Zie [verbruik en kosten beheren](../../azure-monitor/platform/manage-cost-storage.md)voor meer informatie over het analyseren van het gegevens gebruik van Azure monitor Logboeken.

## <a name="network-planning"></a><a name="ports"></a>Netwerkplanning

De volgende adressen zijn specifiek vereist voor Updatebeheer. Communicatie met deze adressen vindt plaats via poort 443.

|Openbare Azure-peering  |Azure Government  |
|---------|---------|
|`*.ods.opinsights.azure.com`    | `*.ods.opinsights.azure.us`        |
|`*.oms.opinsights.azure.com`     | `*.oms.opinsights.azure.us`        |
|`*.blob.core.windows.net` | `*.blob.core.usgovcloudapi.net`|
|`*.azure-automation.net` | `*.azure-automation.us`|

Wanneer u beveiligings regels voor een netwerk groep maakt of Azure Firewall configureert om verkeer toe te staan voor de Automation-Service en de Log Analytics-werk ruimte, gebruikt u de [service label](../../virtual-network/service-tags-overview.md#available-service-tags) **GuestAndHybridManagement** en **AzureMonitor**. Dit vereenvoudigt het voortdurend beheer van uw netwerk beveiligings regels. Als u verbinding wilt maken met de Automation-Service van uw Azure-Vm's veilig en privé, raadpleegt u [Azure private link gebruiken](../how-to/private-link-security.md). Zie [Download bare json-bestanden](../../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)voor informatie over het verkrijgen van de huidige servicetag en bereik gegevens die u wilt opnemen als onderdeel van uw on-premises firewall configuraties.

Voor Windows-computers moet u ook verkeer toestaan voor eind punten die vereist zijn voor Windows Update. U kunt een bijgewerkte lijst met vereiste eind punten vinden in [kwesties met betrekking tot http/proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy). Als u een lokale [Windows Update server](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment)hebt, moet u ook verkeer toestaan naar de server die is opgegeven in uw [WSUS-sleutel](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry).

Zie IP-adressen voor de vereiste eind punten voor [de RHUI content delivery servers](../../virtual-machines/workloads/redhat/redhat-rhui.md#the-ips-for-the-rhui-content-delivery-servers) voor Red Hat Linux-machines. Raadpleeg de documentatie van uw provider voor andere Linux-distributies.

Zie [updatebeheer adressen voor Hybrid Runbook worker](../automation-hybrid-runbook-worker.md#update-management-addresses-for-hybrid-runbook-worker)voor meer informatie over de poorten die zijn vereist voor de Hybrid Runbook Worker.

Als uw IT-beveiligings beleid niet toestaat dat computers in het netwerk verbinding maken met internet, kunt u een [log Analytics gateway](../../azure-monitor/platform/gateway.md) instellen en vervolgens de computer zo configureren dat deze via de gateway verbinding maakt met Azure Automation en Azure monitor.

## <a name="update-classifications"></a>Updateclassificaties

In de volgende tabel worden de classificaties gedefinieerd die Updatebeheer ondersteunt voor Windows-updates.

|Classificatie  |Beschrijving  |
|---------|---------|
|Essentiële updates     | Een update voor een specifiek probleem dat betrekking heeft op een kritieke bug die niet aan beveiliging voldoet.        |
|Beveiligingsupdates     | Een update voor een productspecifiek, beveiligings probleem.        |
|Updatepakketten     | Een cumulatieve set met hotfixes die samen zijn verpakt voor een eenvoudige implementatie.        |
|Functiepakketten     | Nieuwe product functies die worden gedistribueerd buiten een product release.        |
|Servicepacks     | Een cumulatieve set met hotfixes die op een toepassing worden toegepast.        |
|Definitie-updates     | Een update van virus-of andere definitie bestanden.        |
|Hulpprogramma's     | Een hulp programma of functie waarmee u een of meer taken kunt volt ooien.        |
|Updates     | Een update voor een toepassing of bestand dat momenteel is geïnstalleerd.        |

In de volgende tabel worden de ondersteunde classificaties voor Linux-updates gedefinieerd.

|Classificatie  |Beschrijving  |
|---------|---------|
|Essentiële en beveiligingsupdates     | Updates voor een specifiek probleem of een productspecifiek beveiligings probleem.         |
|Andere Updates     | Alle andere updates die niet kritiek zijn of die geen beveiligings updates zijn.        |

>[!NOTE]
>Update classificatie voor Linux-machines is alleen beschikbaar als deze wordt gebruikt in de ondersteunde open bare Cloud regio's van Azure. Wanneer u Updatebeheer gebruikt in de volgende nationale Cloud regio's:
>* Azure US Government
>* 21Vianet in China
>
> Er is geen classificatie van Linux-updates en deze worden gerapporteerd in de categorie **andere updates** . Updatebeheer gebruikt gegevens die worden gepubliceerd door de ondersteunde distributies, met name de uitgebrachte [ovaal](https://oval.mitre.org/) (open beveiligings-en evaluatie taal). Omdat Internet toegang is beperkt van deze nationale Clouds, heeft Updatebeheer geen toegang tot deze bestanden en kan deze niet worden gebruikt.

Voor Linux kan Updatebeheer een onderscheid maken tussen essentiële updates en beveiligings updates in de Cloud, terwijl evaluatie gegevens worden weer gegeven vanwege gegevens verrijking in de Cloud. Voor patching is Updatebeheer afhankelijk van de classificatie gegevens die op de computer beschikbaar zijn. In tegens telling tot andere distributies is CentOS deze informatie niet beschikbaar in de RTM-versie. Als er CentOS machines zijn geconfigureerd voor het retour neren van beveiligings gegevens voor de volgende opdracht, kunt Updatebeheer patch op basis van classificaties.

```bash
sudo yum -q --security check-update
```

Er is momenteel geen ondersteunde methode voor het inschakelen van systeem eigen classificatie-gegevens beschikbaarheid op CentOS. Op dit moment wordt beperkte ondersteuning geboden aan klanten die deze functie mogelijk zelf hebben ingeschakeld.

Als u updates wilt classificeren voor Red Hat Enter prise versie 6, moet u de yum-beveiligings-invoeg toepassing installeren. Op Red Hat Enterprise Linux 7 maakt de invoeg toepassing al deel uit van yum. u hoeft niets te installeren. Zie het volgende Red Hat [Knowledge-artikel](https://access.redhat.com/solutions/10021)voor meer informatie.

## <a name="integrate-update-management-with-configuration-manager"></a>Updatebeheer integreren met Configuration Manager

Klanten die hebben geïnvesteerd in micro soft endpoint Configuration Manager voor het beheren van Pc's, servers en mobiele apparaten, zijn ook afhankelijk van de kracht en de loop tijd van Configuration Manager om software-updates te beheren. Zie [updatebeheer integreren met Windows-eind punt Configuration Manager](mecmintegration.md)voor meer informatie over het integreren van Updatebeheer met Configuration Manager.

## <a name="third-party-updates-on-windows"></a>Updates van derden in Windows

Updatebeheer is afhankelijk van de lokaal geconfigureerde update opslagplaats om ondersteunde Windows-systemen bij te werken, ofwel WSUS of Windows Update. Met hulpprogram ma's zoals [System Center updates Publisher](/configmgr/sum/tools/updates-publisher) kunt u aangepaste updates importeren en publiceren met WSUS. Met dit scenario kunnen Updatebeheer machines bijwerken die gebruikmaken van Configuration Manager als de update opslagplaats met software van derden. Zie [install updates Publisher](/configmgr/sum/tools/install-updates-publisher)(Engelstalig) voor meer informatie over het configureren van updates Publisher.

## <a name="enable-update-management"></a>Updatebeheer inschakelen

Op de volgende manieren kunt u Updatebeheer inschakelen en computers selecteren die moeten worden beheerd:

- Een Azure [Resource Manager-sjabloon](enable-from-template.md) gebruiken om updatebeheer te implementeren op een nieuw of bestaand Automation-account en Azure monitor log Analytics-werk ruimte in uw abonnement. Hiermee wordt niet het bereik geconfigureerd van machines die moeten worden beheerd. dit wordt uitgevoerd als een afzonderlijke stap na het gebruik van de sjabloon.

- Van uw [Automation-account](enable-from-automation-account.md) voor een of meer Azure-en niet-Azure-machines, waaronder servers met Arc-functionaliteit.

- Met de [runbook](enable-from-runbook.md) **-methode Enable-AutomationSolution** .

- Voor een [geselecteerde Azure VM](enable-from-vm.md) op de pagina **virtuele machines** in de Azure Portal. Dit scenario is beschikbaar voor Linux-en Windows-Vm's.

- Voor [meerdere virtuele Azure-machines](enable-from-portal.md) door ze te selecteren op de pagina **virtual machines** in de Azure Portal.

> [!NOTE]
> Updatebeheer moet een Log Analytics-werk ruimte koppelen aan uw Automation-account. Zie [Azure Workspace-toewijzingen](../how-to/region-mappings.md)voor een definitieve lijst met ondersteunde regio's. De regio toewijzingen hebben geen invloed op de mogelijkheid om Vm's te beheren in een andere regio dan uw Automation-account.

## <a name="next-steps"></a>Volgende stappen

* Zie [updates voor uw virtuele machines beheren](manage-updates-for-vm.md)voor meer informatie over het werken met updatebeheer.

* Raadpleeg Veelgestelde vragen over Updatebeheer in de [Azure Automation Veelgestelde vragen](../automation-faq.md).

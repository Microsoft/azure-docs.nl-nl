---
title: VMware-evaluatie ondersteuning in Azure Migrate
description: Meer informatie over ondersteuning voor VMware VM-evaluatie met Azure Migrate server-evaluatie.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/10/2020
ms.openlocfilehash: cea13d0037a8b8074a78177794c553668d3cd31b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100590594"
---
# <a name="support-matrix-for-vmware-assessment"></a>Ondersteunings matrix voor VMware-evaluatie 

In dit artikel vindt u een overzicht van de vereisten voor en ondersteuning wanneer u virtuele VMware-machines detecteert en evalueert voor migratie naar Azure, met behulp van het [Azure migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) tool. Als u virtuele VMware-machines wilt beoordelen, maakt u een Azure Migrate project en voegt u het hulp programma voor Server evaluatie toe aan het project. Wanneer het hulp programma is toegevoegd, implementeert u het Azure Migrate apparaat. Het apparaat detecteert voortdurend on-premises machines en verzendt meta gegevens en prestatie gegevens van de machine naar Azure. Nadat de detectie is voltooid, verzamelt u gedetecteerde computers in groepen en voert u een evaluatie uit voor een groep. 

Als u virtuele VMware-machines wilt migreren naar Azure, raadpleegt u de [ondersteunings matrix voor migratie](migrate-support-matrix-vmware-migration.md).



## <a name="limitations"></a>Beperkingen

**Vereiste** | **Details**
--- | ---
**Project limieten** | U kunt meerdere projecten maken in een Azure-abonnement.<br/><br/> U kunt Maxi maal 35.000 VMware-Vm's in één [project](migrate-support-matrix.md#azure-migrate-projects)detecteren en beoordelen. Een project kan ook fysieke servers en virtuele Hyper-V-machines bevatten, tot aan de evaluatie limieten voor elke.
**Discovery** (Detectie) | Het Azure Migrate-apparaat kan Maxi maal 10.000 VMware-Vm's detecteren op een vCenter Server.
**Evaluatie** | U kunt Maxi maal 35.000 computers in één groep toevoegen.<br/><br/> U kunt Maxi maal 35.000 Vm's in één evaluatie evalueren.

Meer [informatie](concepts-assessment-calculation.md) over evaluaties.


## <a name="vmware-requirements"></a>VMware-vereisten

**VMware** | **Details**
--- | ---
**vCenter Server** | Machines die u wilt detecteren en beoordelen, moeten worden beheerd door vCenter Server versie 5,5, 6,0, 6,5, 6,7 of 7,0.<br/><br/> De detectie van VMware-Vm's door ESXi in het apparaat op te geven, wordt momenteel niet ondersteund.
**Machtigingen** | Server Assessment heeft een vCenter Server alleen-lezen account nodig voor detectie en evaluatie.<br/><br/> Als u toepassings detectie of afhankelijkheids visualisatie wilt uitvoeren, heeft het account bevoegdheden nodig die zijn ingeschakeld voor **virtual machines**-  >  **gast bewerkingen**.

## <a name="vm-requirements"></a>VM-vereisten
**VMware** | **Details**
--- | ---
**VMware-VM's** | Alle besturings systemen kunnen worden geëvalueerd voor migratie. 
**Storage** | Schijven die zijn gekoppeld aan SCSI-, IDE-en SATA-controllers, worden ondersteund.


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate-apparaatvereisten

Azure Migrate gebruikt het [Azure migrate-apparaat](migrate-appliance.md) voor detectie en evaluatie. U kunt het apparaat als een virtuele VMware-machine implementeren met behulp van een eicellen-sjabloon, geïmporteerd in vCenter Server of een [Power shell-script](deploy-appliance-script.md)gebruiken.

- Meer informatie over de [vereisten voor apparaten](migrate-appliance.md#appliance---vmware) voor VMware.
- In Azure Government moet u het apparaat implementeren [met behulp van het script](deploy-appliance-script-government.md).
- Bekijk de Url's die het apparaat nodig heeft voor [open bare](migrate-appliance.md#public-cloud-urls) en [overheids](migrate-appliance.md#government-cloud-urls) Clouds.


## <a name="port-access-requirements"></a>Poort toegangs vereisten

**Apparaat** | **Verbinding**
--- | ---
**Apparaat** | Binnenkomende verbindingen op TCP-poort 3389 om extern bureau blad-verbindingen met het apparaat toe te staan.<br/><br/> Binnenkomende verbindingen op poort 44368 voor externe toegang tot de app voor het beheren van apparaten met behulp van de URL: ```https://<appliance-ip-or-name>:44368``` <br/><br/>Uitgaande verbindingen op poort 443 (HTTPS) voor het verzenden van meta gegevens voor detectie en prestaties naar Azure Migrate.
**vCenter-Server** | Binnenkomende verbindingen op TCP-poort 443 zodat het apparaat configuratie-en prestatie-meta gegevens voor evaluaties kan verzamelen. <br/><br/> Het apparaat maakt standaard verbinding met vCenter op poort 443. Als de vCenter-Server op een andere poort luistert, kunt u de poort wijzigen bij het instellen van detectie.
**ESXi-hosts** | Als u [app-detectie](how-to-discover-applications.md)wilt uitvoeren of een [afhankelijkheids analyse zonder agent](concepts-dependency-visualization.md#agentless-analysis)wilt uitvoeren, maakt het apparaat verbinding met ESXI-hosts op TCP-poort 443, om toepassingen te detecteren, en om te worden uitgevoerd op virtuele machines met agentloze afhankelijkheden.

## <a name="application-discovery-requirements"></a>Vereisten voor toepassings detectie

Naast het detecteren van computers kunnen server analyses apps, rollen en functies vinden die op machines worden uitgevoerd. Als u uw app-inventaris detecteert, kunt u een migratie traject identificeren en plannen dat is afgestemd op uw on-premises workloads. 

**Ondersteuning** | **Details**
--- | ---
**Ondersteunde computers** | Momenteel alleen ondersteund voor virtuele VMware-machines. U kunt op elk Azure Migrate apparaat apps die zijn geïnstalleerd op Maxi maal 10000 VMware-Vm's, detecteren.
**Besturingssystemen** | Ondersteuning voor Vm's waarop alle Windows-en Linux-versies worden uitgevoerd.
**VM-vereisten** | VMware-hulpprogram ma's moeten zijn geïnstalleerd en worden uitgevoerd op virtuele machines waarop u apps wilt detecteren. <br/><br/> De versie van de VMware-hulpprogram ma's moet later zijn dan 10.2.0.<br/><br/> Op Vm's moet Power shell-versie 2,0 of hoger zijn geïnstalleerd.
**Discovery** (Detectie) | Informatie over apps die op een virtuele machine zijn geïnstalleerd, wordt verzameld van de vCenter Server, met behulp van VMware-Hulpprogram Ma's die op de virtuele machine zijn geïnstalleerd. Het apparaat verzamelt de app-gegevens van de vCenter Server, met behulp van vSphere-Api's. App-detectie is zonder agent. Er is niets geïnstalleerd op Vm's en het apparaat maakt geen rechtstreekse verbinding met Vm's. WMI/SSH moet zijn ingeschakeld en beschikbaar zijn op Vm's.
**vCenter** | Het vCenter Server alleen-lezen account dat wordt gebruikt voor de evaluatie, heeft bevoegdheden nodig die zijn ingeschakeld voor **virtual machines**  >  **gast bewerkingen** om te kunnen communiceren met de virtuele machine voor toepassings detectie.
**VM-toegang** | App-detectie heeft een lokaal gebruikers account op de virtuele machine nodig voor toepassings detectie.<br/><br/> Azure Migrate biedt momenteel ondersteuning voor het gebruik van één referentie voor alle Windows-servers en één referentie voor alle Linux-servers.<br/><br/> U maakt een gast gebruikers account voor Windows-Vm's en een standaard/normaal gebruikers account (niet-sudo toegang) voor alle virtuele Linux-machines.
**Poort toegang** | Het Azure Migrate apparaat moet verbinding kunnen maken met TCP-poort 443 op ESXi-hosts waarop Vm's worden uitgevoerd waarop u apps wilt detecteren. De vCenter Server retourneert een ESXI om het bestand met de app-gegevens te downloaden.



## <a name="dependency-analysis-requirements-agentless"></a>Vereisten voor afhankelijkheids analyse (zonder agent)

[Afhankelijkheids analyse](concepts-dependency-visualization.md) helpt u bij het identificeren van afhankelijkheden tussen on-premises machines die u wilt beoordelen en migreren naar Azure. De tabel bevat een overzicht van de vereisten voor het instellen van een afhankelijkheids analyse zonder agent. 

**Ondersteuning** | **Details**
--- | --- 
**Ondersteunde computers** | Momenteel alleen ondersteund voor virtuele VMware-machines.
**Virtuele Windows-machines** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 (64-bits).<br/>Micro soft Windows Server 2008 (32-bits). 
**Virtuele Linux-machines** | Red Hat Enterprise Linux 7, 6, 5<br/> Ubuntu Linux 14,04, 16,04<br/> Debian 7, 8<br/> Oracle Linux 6, 7<br/> CentOS 5, 6, 7.<br/> SUSE Linux Enterprise Server 11 en hoger.
**VM-vereisten** | VMware-Hulpprogram Ma's (later dan 10.2.0) moeten zijn geïnstalleerd en worden uitgevoerd op de virtuele machines die u wilt analyseren.<br/><br/> Op Vm's moet Power shell-versie 2,0 of hoger zijn geïnstalleerd.
**Detectie methode** |  Afhankelijkheids informatie tussen Vm's wordt verzameld uit de vCenter Server, met behulp van VMware-hulpprogram ma's die op de virtuele machine zijn geïnstalleerd. Het apparaat verzamelt de informatie van de vCenter Server met behulp van vSphere-Api's. Detectie is zonder agent. Er is niets geïnstalleerd op de virtuele machine en het apparaat maakt geen rechtstreekse verbinding met Vm's. WMI/SSH moet zijn ingeschakeld en beschikbaar zijn op Vm's.
**vCenter-account** | Het alleen-lezen account dat door Azure Migrate wordt gebruikt voor beoordeling vereist bevoegdheden die zijn ingeschakeld voor **Virtual Machines >-gast bewerkingen**.
**Machtigingen voor Windows-VM'S** |  Een account (lokale beheerder of domein) met lokale beheerders machtigingen op Vm's.
**Linux-account** | Hoofd gebruikers account of een account met deze machtigingen voor/bin/netstat-en/bin/ls-bestanden: CAP_DAC_READ_SEARCH en CAP_SYS_PTRACE.<br/><br/> Stel deze mogelijkheden in met behulp van de volgende opdrachten: <br/><br/> sudo setcap CAP_DAC_READ_SEARCH, CAP_SYS_PTRACE = EP/bin/ls<br/><br/> sudo setcap CAP_DAC_READ_SEARCH, CAP_SYS_PTRACE = EP/bin/netstat
**Poort toegang** | Het Azure Migrate apparaat moet verbinding kunnen maken met TCP-poort 443 op ESXI-hosts waarop de virtuele machines worden uitgevoerd waarvan u de afhankelijkheden wilt detecteren. De vCenter Server retourneert een ESXI om het bestand met de afhankelijkheids gegevens te downloaden.


## <a name="dependency-analysis-requirements-agent-based"></a>Vereisten voor afhankelijkheids analyse (op agent gebaseerd)

[Afhankelijkheids analyse](concepts-dependency-visualization.md) helpt u bij het identificeren van afhankelijkheden tussen on-premises machines die u wilt beoordelen en migreren naar Azure. De tabel bevat een overzicht van de vereisten voor het instellen van afhankelijkheids analyse op basis van een agent. 

**Vereiste** | **Details** 
--- | --- 
**Vóór implementatie** | Er moet een Azure Migrate project aanwezig zijn, met het Azure Migrate: Server assessment tool is toegevoegd aan het project.<br/><br/>  U kunt een afhankelijkheids visualisatie implementeren nadat u een Azure Migrate apparaat hebt ingesteld om uw on-premises computers te detecteren<br/><br/> [Meer informatie over](create-manage-projects.md) het maken van een project voor de eerste keer.<br/> [Meer informatie over het](how-to-assess.md) toevoegen van een evaluatie programma aan een bestaand project.<br/> Meer informatie over het instellen van het Azure Migrate-apparaat voor de evaluatie van [Hyper-V](how-to-set-up-appliance-hyper-v.md)-, [VMware](how-to-set-up-appliance-vmware.md)-en fysieke servers.
**Ondersteunde computers** | Ondersteund voor alle machines.
**Azure Government** | Visualisatie van afhankelijkheid is niet beschikbaar in Azure Government.
**Log Analytics** | Azure Migrate gebruikt de [servicetoewijzing](../azure-monitor/vm/service-map.md) oplossing in [Azure monitor logboeken](../azure-monitor/logs/log-query-overview.md) voor de visualisatie van afhankelijkheden.<br/><br/> U koppelt een nieuwe of bestaande Log Analytics-werk ruimte aan een Azure Migrate-project. De werk ruimte voor een Azure Migrate project kan niet worden gewijzigd nadat deze is toegevoegd. <br/><br/> De werk ruimte moet zich in hetzelfde abonnement bevinden als het Azure Migrate-project.<br/><br/> De werk ruimte moet zich bevinden in de regio's VS-Oost, Zuidoost-Azië of Europa-west. Werk ruimten in andere regio's kunnen niet worden gekoppeld aan een project.<br/><br/> De werk ruimte moet zich in een regio bevinden waarin [servicetoewijzing wordt ondersteund](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).<br/><br/> In Log Analytics wordt de werk ruimte die is gekoppeld aan Azure Migrate gelabeld met de sleutel van het migratie project en de project naam.
**Vereiste agents** | Installeer de volgende agents op elke computer die u wilt analyseren:<br/><br/> [Micro soft Monitoring Agent (MMA)](../azure-monitor/agents/agent-windows.md).<br/> De [afhankelijkheids agent](../azure-monitor/agents/agents-overview.md#dependency-agent).<br/><br/> Als on-premises computers niet zijn verbonden met internet, moet u Log Analytics gateway op deze machines downloaden en installeren.<br/><br/> Meer informatie over het installeren van de [dependency agent](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) en [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Log Analytics werk ruimte** | De werk ruimte moet zich in hetzelfde abonnement bevinden als het Azure Migrate-project.<br/><br/> Azure Migrate ondersteunt werk ruimten die zich in het VS-Oost, Zuidoost-Azië en Europa-west regio's bevinden.<br/><br/>  De werk ruimte moet zich in een regio bevinden waarin [servicetoewijzing wordt ondersteund](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).<br/><br/> De werk ruimte voor een Azure Migrate project kan niet worden gewijzigd nadat deze is toegevoegd.
**Kosten** | De Servicetoewijzing oplossing heeft geen kosten in rekening gebracht voor de eerste 180 dagen (vanaf de dag dat u de werk ruimte Log Analytics koppelt aan het Azure Migrate project)/<br/><br/> Na 180 dagen gelden de standaardkosten voor Log Analytics.<br/><br/> Bij het gebruik van een andere oplossing dan Servicetoewijzing in de gekoppelde Log Analytics werk ruimte worden [standaard kosten](https://azure.microsoft.com/pricing/details/log-analytics/) in rekening gebracht voor log Analytics.<br/><br/> Wanneer het Azure Migrate project wordt verwijderd, wordt de werk ruimte samen niet verwijderd. Na het verwijderen van het project is Servicetoewijzing gebruik niet gratis en worden voor elk knoop punt de kosten in rekening gebracht volgens de betaalde laag van Log Analytics werk ruimte/<br/><br/>Als u projecten hebt die u hebt gemaakt vóór Azure Migrate algemene Beschik baarheid (GA 28 februari 2018), hebt u mogelijk extra Servicetoewijzing kosten in rekening gebracht. Om ervoor te zorgen dat u na 180 dagen alleen betaalt, is het raadzaam om een nieuw project te maken, omdat bestaande werk ruimten voor GA nog steeds toerekenbaar zijn.
**Beheer** | Wanneer u agents registreert bij de werk ruimte, gebruikt u de ID en de sleutel van het Azure Migrate project.<br/><br/> U kunt de Log Analytics-werk ruimte gebruiken buiten Azure Migrate.<br/><br/> Als u het gekoppelde Azure Migrate project verwijdert, wordt de werk ruimte niet automatisch verwijderd. [Verwijder deze hand matig](../azure-monitor/logs/manage-access.md).<br/><br/> Verwijder de werk ruimte die is gemaakt door Azure Migrate, tenzij u het Azure Migrate project verwijdert. Als u dat wel doet, werkt de visualisatie functionaliteit voor afhankelijkheden niet zoals verwacht.
**Internetconnectiviteit** | Als computers niet zijn verbonden met internet, moet u de Log Analytics-gateway hierop installeren.
**Azure Government** | Afhankelijkheids analyse op basis van een agent wordt niet ondersteund.


## <a name="next-steps"></a>Volgende stappen

- [Bekijk](best-practices-assessment.md) de aanbevolen procedures voor het maken van evaluaties.
- [VMware](./tutorial-discover-vmware.md) -evaluatie voorbereiden.
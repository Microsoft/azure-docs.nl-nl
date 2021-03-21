---
title: VMware-evaluatie ondersteuning in Azure Migrate
description: Meer informatie over ondersteuning voor VMware VM-evaluatie met Azure Migrate server-evaluatie
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/10/2020
ms.openlocfilehash: 7e0bc21fde2c030de7a836d82384c09c78d993ad
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102047822"
---
# <a name="support-matrix-for-vmware-assessment"></a>Ondersteunings matrix voor VMware-evaluatie 

In dit artikel vindt u een overzicht van de vereisten voor en ondersteuning bij het detecteren en beoordelen van servers die in VMware-omgeving worden uitgevoerd voor migratie naar Azure, met behulp van het [Azure migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) tool. Als u servers wilt beoordelen, maakt u een Azure Migrate project waarmee u het hulp programma voor Server evaluatie toevoegt aan het project. Wanneer het hulp programma is toegevoegd, implementeert u het Azure Migrate apparaat. Het apparaat detecteert voortdurend on-premises servers en verzendt meta gegevens over de configuratie en prestaties naar Azure. Nadat de detectie is voltooid, verzamelt u gedetecteerde servers in groepen en voert u een evaluatie uit voor een groep.

Als u VMware-servers naar Azure wilt migreren, raadpleegt u de [ondersteunings matrix voor migratie](migrate-support-matrix-vmware-migration.md).



## <a name="limitations"></a>Beperkingen

**Vereiste** | **Details**
--- | ---
**Project limieten** | U kunt meerdere projecten maken in een Azure-abonnement.<br/><br/> U kunt in één [project](migrate-support-matrix.md#azure-migrate-projects)maxi maal 50.000 servers in de VMware-omgeving detecteren en beoordelen. Een project kan ook fysieke servers en servers uit een Hyper-V-omgeving omvatten, tot aan de evaluatie limieten.
**Discovery** (Detectie) | Het Azure Migrate-apparaat kan Maxi maal 10.000 servers op een vCenter Server detecteren.
**Evaluatie** | U kunt Maxi maal 35.000 servers in één groep toevoegen.<br/><br/> U kunt Maxi maal 35.000 servers in één evaluatie evalueren.

Meer [informatie](concepts-assessment-calculation.md) over evaluaties.


## <a name="vmware-requirements"></a>VMware-vereisten

**VMware** | **Details**
--- | ---
**vCenter Server** | Servers die u wilt detecteren en beoordelen, moeten worden beheerd door vCenter Server versie 5,5, 6,0, 6,5, 6,7 of 7,0.<br/><br/> De detectie van servers door ESXi-hostgegevens in het apparaat te bieden, wordt momenteel niet ondersteund.
**Machtigingen** | Server Assessment heeft een vCenter Server alleen-lezen account nodig voor detectie en evaluatie.<br/><br/> Als u de installatie van geïnstalleerde toepassingen en afhankelijkheids analyse zonder agent wilt uitvoeren, moet aan het account bevoegdheden zijn ingeschakeld voor **virtual machines**-  >  **gast bewerkingen**.

## <a name="server-requirements"></a>Serververeisten
**VMware** | **Details**
--- | ---
**Ondersteund besturings systeem** | Alle Windows-en Linux-besturings systemen kunnen worden geëvalueerd voor migratie.
**Storage** | Schijven die zijn gekoppeld aan SCSI-, IDE-en SATA-controllers, worden ondersteund.


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate-apparaatvereisten

Azure Migrate gebruikt het [Azure migrate-apparaat](migrate-appliance.md) voor detectie en evaluatie. U kunt het apparaat als een server in uw VMware-omgeving implementeren met behulp van een eicellen-sjabloon, geïmporteerd in vCenter Server of een [Power shell-script](deploy-appliance-script.md)gebruiken.

- Meer informatie over de [vereisten voor apparaten](migrate-appliance.md#appliance---vmware) voor VMware.
- In Azure Government moet u het apparaat implementeren [met behulp van het script](deploy-appliance-script-government.md).
- Bekijk de Url's die het apparaat nodig heeft voor [open bare](migrate-appliance.md#public-cloud-urls) en [overheids](migrate-appliance.md#government-cloud-urls) Clouds.


## <a name="port-access-requirements"></a>Poort toegangs vereisten

**Apparaat** | **Verbinding**
--- | ---
**Apparaat** | Binnenkomende verbindingen op TCP-poort 3389 om extern bureau blad-verbindingen met het apparaat toe te staan.<br/><br/> Binnenkomende verbindingen op poort 44368 voor externe toegang tot de app voor het beheren van apparaten met behulp van de URL: ```https://<appliance-ip-or-name>:44368``` <br/><br/>Uitgaande verbindingen op poort 443 (HTTPS) voor het verzenden van meta gegevens voor detectie en prestaties naar Azure Migrate.
**vCenter-Server** | Binnenkomende verbindingen op TCP-poort 443 zodat het apparaat configuratie-en prestatie-meta gegevens voor evaluaties kan verzamelen. <br/><br/> Het apparaat maakt standaard verbinding met vCenter op poort 443. Als de vCenter-Server op een andere poort luistert, kunt u de poort wijzigen bij het instellen van detectie.
**ESXi-hosts** | Als u de [detectie van geïnstalleerde toepassingen](how-to-discover-applications.md)of de [afhankelijkheids analyse zonder agent](concepts-dependency-visualization.md#agentless-analysis)wilt uitvoeren, maakt het apparaat verbinding met ESXI-hosts op TCP-poort 443 om toepassingen en afhankelijkheden op de servers te detecteren.

## <a name="application-discovery-requirements"></a>Vereisten voor toepassings detectie

Naast het detecteren van servers kunnen server analyses toepassingen, rollen en functies vinden die op servers worden uitgevoerd. Met toepassings detectie kunt u een migratie traject identificeren en plannen dat is afgestemd op uw on-premises workloads.

**Ondersteuning** | **Details**
--- | ---
**Ondersteunde servers** | Momenteel alleen ondersteund voor servers in uw VMware-omgeving. U kunt toepassings detectie uitvoeren op Maxi maal 10000 servers, van elk Azure Migrate apparaat.
**Besturingssystemen** | Servers met alle Windows-en Linux-versies worden ondersteund.
**VM-vereisten** | Voor de detectie van geïnstalleerde toepassingen moeten VMware-hulpprogram ma's zijn geïnstalleerd en worden uitgevoerd op servers. <br/><br/> De versie van de VMware-hulpprogram ma's moet later zijn dan 10.2.0.<br/><br/> Op Windows-servers moet Power shell-versie 2,0 of hoger zijn geïnstalleerd.
**Discovery** (Detectie) | Toepassings detectie op servers wordt uitgevoerd vanuit de vCenter Server, met behulp van VMware-Hulpprogram Ma's die op de servers zijn geïnstalleerd. Het apparaat verzamelt de informatie over de geïnstalleerde toepassingen van de vCenter Server, met behulp van vSphere-Api's. Toepassings detectie is zonder agent. Er is geen agent geïnstalleerd op de server en het apparaat maakt geen rechtstreekse verbinding met de servers. WMI en SSH moeten respectievelijk worden ingeschakeld en beschikbaar zijn op Windows-en Linux-servers.
**gebruikers account vCenter Server** | Het vCenter Server alleen-lezen account dat wordt gebruikt voor de evaluatie, heeft bevoegdheden nodig die zijn ingeschakeld voor **virtual machines**  >  **gast bewerkingen** om te kunnen communiceren met de servers voor toepassings detectie.
**Server toegang** | U kunt meerdere domein-en niet-domein referenties (Windows/Linux) toevoegen aan het configuratie beheer van het apparaat voor toepassings detectie.<br/><br/> U hebt een gast gebruikers account voor Windows-servers en een reguliere/normale gebruikers account (niet-sudo toegang) nodig voor alle Linux-servers.
**Poort toegang** | Het Azure Migrate apparaat moet verbinding kunnen maken met TCP-poort 443 op ESXi-hosts waarop servers worden uitgevoerd waarop u toepassings detectie wilt uitvoeren. De vCenter Server retourneert een ESXi om het bestand te downloaden met de details van de geïnstalleerde toepassingen.

## <a name="requirements-for-discovery-of-sql-server-instances-and-databases"></a>Vereisten voor de detectie van SQL Server instanties en data bases

> [!Note]
> Detectie en evaluatie van SQL Server instanties en data bases die worden uitgevoerd in uw VMware-omgeving is nu beschikbaar als preview-versie. Als u deze functie wilt proberen, gebruikt u [**deze koppeling**](https://aka.ms/AzureMigrate/SQL) om een project te maken in de regio **Australië - oost**. Als u al een project in Australië-oost hebt en u deze functie wilt proberen, zorgt u ervoor dat u aan deze [**vereisten**](how-to-discover-sql-existing-project.md) voldoet in de portal.

[Toepassings detectie](how-to-discover-applications.md) identificeert de SQL Server exemplaren. Met deze informatie wordt het apparaat via de Windows-verificatie of SQL Server verificatie referenties die op het apparaat zijn verstrekt, verbonden met de betreffende SQL Server exemplaren. Zodra het apparaat is verbonden, worden configuratie-en prestatie gegevens van SQL Server instanties en data bases verzameld. De SQL Server configuratie gegevens worden elke 24 uur bijgewerkt en de prestatie gegevens worden elke 30 seconden vastgelegd.

**Ondersteuning** | **Details**
--- | ---
**Ondersteunde servers** | Momenteel alleen ondersteund voor SQL-servers in uw VMware-omgeving. U kunt Maxi maal 300 SQL Server exemplaren of 6000 SQL-data bases detecteren, afhankelijk van wat lager is.
**Windows-servers** | Windows Server 2008 en hoger worden ondersteund.
**Linux-servers** | Momenteel niet ondersteund.
**Verificatie mechanisme** | Zowel Windows-als SQL Server-verificatie worden ondersteund. U kunt de referenties van beide verificatie typen opgeven op de configuratie beheer van het apparaat.
**Toegang tot SQL Server** | Voor Azure Migrate is een Windows-gebruikers account vereist dat lid is van de serverrol sysadmin.
**SQL Server-versies** | SQL Server 2008 en hoger worden ondersteund.
**SQL Server-edities** | Enter prise-, Standard-, Developer-en Express-edities worden ondersteund.
**Ondersteunde SQL-configuratie** | Momenteel wordt alleen de detectie van zelfstandige SQL Server instanties en de bijbehorende data bases ondersteund.<br/> Identificatie van het failovercluster en AlwaysOn-beschikbaarheids groepen wordt niet ondersteund.
**Ondersteunde SQL-services** | Alleen SQL Server data base-engine wordt ondersteund. <br/> Detectie van SQL Server Reporting Services (SSRS), SQL Server Integration Services (SSIS) en SQL Server Analysis Services (SSAS) wordt niet ondersteund.

> [!Note]
> Azure Migrate versleutelt de communicatie tussen Azure Migrate apparaat en bron SQL Server instanties (waarbij de eigenschap versleutelings verbinding is ingesteld op TRUE). Deze verbindingen worden versleuteld met [**TrustServerCertificate**](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.trustservercertificate) (ingesteld op True). de transportlaag gebruikt SSL om het kanaal te versleutelen en de certificaat keten te omzeilen om de vertrouwens relatie te valideren. De toestel server moet zijn ingesteld om [**de basis instantie van het certificaat te vertrouwen**](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).<br/>
Als er geen certificaat is ingericht op de server wanneer het wordt gestart, SQL Server genereert een zelfondertekend certificaat dat wordt gebruikt om aanmeldings pakketten te versleutelen. [**Meer informatie**](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).

## <a name="dependency-analysis-requirements-agentless"></a>Vereisten voor afhankelijkheids analyse (zonder agent)

[Afhankelijkheids analyse](concepts-dependency-visualization.md) helpt u bij het identificeren van afhankelijkheden tussen on-premises servers die u wilt beoordelen en migreren naar Azure. De tabel bevat een overzicht van de vereisten voor het instellen van een afhankelijkheids analyse zonder agent.

**Ondersteuning** | **Details**
--- | --- 
**Ondersteunde servers** | Momenteel alleen ondersteund voor servers in uw VMware-omgeving.
**Windows-servers** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 (64-bits).<br/>Micro soft Windows Server 2008 (32-bits).
**Linux-servers** | Red Hat Enterprise Linux 7, 6, 5<br/> Ubuntu Linux 14,04, 16,04<br/> Debian 7, 8<br/> Oracle Linux 6, 7<br/> CentOS 5, 6, 7.<br/> SUSE Linux Enterprise Server 11 en hoger.
**Serververeisten** | VMware-Hulpprogram Ma's (later dan 10.2.0) moeten zijn geïnstalleerd en worden uitgevoerd op servers die u wilt analyseren.<br/><br/> Op servers moet Power shell-versie 2,0 of hoger zijn geïnstalleerd.
**Detectie methode** |  Afhankelijkheids informatie tussen servers wordt verzameld van de vCenter Server met behulp van VMware-hulpprogram ma's die op de virtuele machine zijn geïnstalleerd. Het apparaat verzamelt de informatie van de vCenter Server met behulp van vSphere-Api's. Er is geen agent geïnstalleerd op de server en het apparaat maakt geen rechtstreekse verbinding met servers. WMI en SSH moeten respectievelijk worden ingeschakeld en beschikbaar zijn op Windows-en Linux-servers.
**vCenter-account** | Het alleen-lezen account dat door Azure Migrate wordt gebruikt voor beoordeling vereist bevoegdheden die zijn ingeschakeld voor **Virtual Machines >-gast bewerkingen**.
**Windows Server-machtigingen** |  Een gebruikers account (lokaal of domein) met beheerders machtigingen op servers.
**Linux-account** | Hoofd gebruikers account of een account met deze machtigingen voor/bin/netstat-en/bin/ls-bestanden: CAP_DAC_READ_SEARCH en CAP_SYS_PTRACE.<br/><br/> Stel deze mogelijkheden in met behulp van de volgende opdrachten: <br/><br/> sudo setcap CAP_DAC_READ_SEARCH, CAP_SYS_PTRACE = EP/bin/ls<br/><br/> sudo setcap CAP_DAC_READ_SEARCH, CAP_SYS_PTRACE = EP/bin/netstat
**Poort toegang** | Het Azure Migrate apparaat moet verbinding kunnen maken met TCP-poort 443 op ESXi-hosts waarop de servers worden uitgevoerd waarvan u de afhankelijkheden wilt detecteren. De vCenter Server retourneert een ESXi om het bestand met de afhankelijkheids gegevens te downloaden.


## <a name="dependency-analysis-requirements-agent-based"></a>Vereisten voor afhankelijkheids analyse (op agent gebaseerd)

[Afhankelijkheids analyse](concepts-dependency-visualization.md) helpt u bij het identificeren van afhankelijkheden tussen on-premises servers die u wilt beoordelen en migreren naar Azure. De tabel bevat een overzicht van de vereisten voor het instellen van afhankelijkheids analyse op basis van een agent.

**Vereiste** | **Details** 
--- | --- 
**Vóór implementatie** | Er moet een Azure Migrate project aanwezig zijn, met het Azure Migrate: Server assessment tool is toegevoegd aan het project.<br/><br/>  U kunt de visualisatie van de afhankelijkheid implementeren nadat u een Azure Migrate apparaat hebt ingesteld om uw on-premises servers te detecteren.<br/><br/> [Meer informatie over](create-manage-projects.md) het maken van een project voor de eerste keer.<br/> [Meer informatie over het](how-to-assess.md) toevoegen van een hulp programma voor detectie en evaluatie aan een bestaand project.<br/> Meer informatie over het instellen van het Azure Migrate-apparaat voor de evaluatie van [Hyper-V](how-to-set-up-appliance-hyper-v.md)-, [VMware](how-to-set-up-appliance-vmware.md)-en fysieke servers.
**Ondersteunde servers** | Wordt ondersteund voor alle servers in uw on-premises omgeving.
**Log Analytics** | Azure Migrate gebruikt de [servicetoewijzing](../azure-monitor/insights/service-map.md) oplossing in [Azure monitor logboeken](../azure-monitor/log-query/log-query-overview.md) voor de visualisatie van afhankelijkheden.<br/><br/> U koppelt een nieuwe of bestaande Log Analytics-werk ruimte aan een Azure Migrate-project. De werk ruimte voor een project kan niet worden gewijzigd nadat deze is toegevoegd. <br/><br/> De werk ruimte moet zich in hetzelfde abonnement bevinden als het project.<br/><br/> De werk ruimte moet zich bevinden in de regio's VS-Oost, Zuidoost-Azië of Europa-west. Werk ruimten in andere regio's kunnen niet worden gekoppeld aan een project.<br/><br/> De werk ruimte moet zich in een regio bevinden waarin [servicetoewijzing wordt ondersteund](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions).<br/><br/> In Log Analytics wordt de werk ruimte die is gekoppeld aan Azure Migrate gelabeld met de sleutel van het migratie project en de project naam.
**Vereiste agents** | Installeer de volgende agents op elke server die u wilt analyseren:<br/><br/> [Micro soft Monitoring Agent (MMA)](../azure-monitor/platform/agent-windows.md).<br/> De [afhankelijkheids agent](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> Als on-premises servers niet zijn verbonden met internet, moet u de Log Analytics-gateway hierop downloaden en installeren.<br/><br/> Meer informatie over het installeren van de [dependency agent](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) en [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Log Analytics werk ruimte** | De werk ruimte moet zich in hetzelfde abonnement bevinden als het Azure Migrate-project.<br/><br/> Azure Migrate ondersteunt werk ruimten die zich in het VS-Oost, Zuidoost-Azië en Europa-west regio's bevinden.<br/><br/>  De werk ruimte moet zich in een regio bevinden waarin [servicetoewijzing wordt ondersteund](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions).<br/><br/> De werk ruimte voor een Azure Migrate project kan niet worden gewijzigd nadat deze is toegevoegd.
**Kosten** | De Servicetoewijzing oplossing heeft geen kosten in rekening gebracht voor de eerste 180 dagen (vanaf de dag dat u de werk ruimte Log Analytics koppelt aan het Azure Migrate project)/<br/><br/> Na 180 dagen gelden de standaardkosten voor Log Analytics.<br/><br/> Bij het gebruik van een andere oplossing dan Servicetoewijzing in de gekoppelde Log Analytics werk ruimte worden [standaard kosten](https://azure.microsoft.com/pricing/details/log-analytics/) in rekening gebracht voor log Analytics.<br/><br/> Wanneer het Azure Migrate project wordt verwijderd, wordt de werk ruimte samen niet verwijderd. Na het verwijderen van het project is Servicetoewijzing gebruik niet gratis en worden voor elk knoop punt de kosten in rekening gebracht volgens de betaalde laag van Log Analytics werk ruimte/<br/><br/>Als u projecten hebt die u hebt gemaakt vóór Azure Migrate algemene Beschik baarheid (GA 28 februari 2018), hebt u mogelijk extra Servicetoewijzing kosten in rekening gebracht. Om ervoor te zorgen dat u na 180 dagen alleen betaalt, is het raadzaam om een nieuw project te maken, omdat bestaande werk ruimten voor GA nog steeds toerekenbaar zijn.
**Beheer** | Wanneer u agents registreert bij de werk ruimte, gebruikt u de ID en de sleutel van het Azure Migrate project.<br/><br/> U kunt de Log Analytics-werk ruimte gebruiken buiten Azure Migrate.<br/><br/> Als u het gekoppelde Azure Migrate project verwijdert, wordt de werk ruimte niet automatisch verwijderd. [Verwijder deze hand matig](../azure-monitor/platform/manage-access.md).<br/><br/> Verwijder de werk ruimte die is gemaakt door Azure Migrate, tenzij u het Azure Migrate project verwijdert. Als u dat wel doet, werkt de visualisatie functionaliteit voor afhankelijkheden niet zoals verwacht.
**Internetconnectiviteit** | Als servers niet zijn verbonden met internet, moet u de Log Analytics-gateway hierop installeren.
**Azure Government** | Afhankelijkheids analyse op basis van een agent wordt niet ondersteund.


## <a name="next-steps"></a>Volgende stappen

- [Bekijk](best-practices-assessment.md) de aanbevolen procedures voor het maken van evaluaties.
- [VMware](./tutorial-discover-vmware.md) -evaluatie voorbereiden.
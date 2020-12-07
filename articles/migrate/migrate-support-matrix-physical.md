---
title: Ondersteuning voor de beoordeling van fysieke servers in Azure Migrate
description: Meer informatie over ondersteuning voor fysieke server beoordeling met Azure Migrate server-evaluatie
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/03/2020
ms.openlocfilehash: cfbbc1d218f590241fab804e389acd689c009dac
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754008"
---
# <a name="support-matrix-for-physical-server-assessment"></a>Ondersteunings matrix voor fysieke server evaluatie 

In dit artikel vindt u een overzicht van de vereisten voor en ondersteuning bij het beoordelen van fysieke servers voor migratie naar Azure, met behulp van het [Azure migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) tool. Als u fysieke servers naar Azure wilt migreren, raadpleegt u de [ondersteunings matrix voor migratie](migrate-support-matrix-physical-migration.md).


Voor het beoordelen van fysieke servers maakt u een Azure Migrate project en voegt u het hulp programma voor Server evaluatie toe aan het project. Wanneer het hulp programma is toegevoegd, implementeert u het [Azure migrate apparaat](migrate-appliance.md). Het apparaat detecteert voortdurend on-premises machines en verzendt meta gegevens en prestatie gegevens van de machine naar Azure. Nadat de detectie is voltooid, verzamelt u gedetecteerde computers in groepen en voert u een evaluatie uit voor een groep.


## <a name="limitations"></a>Beperkingen

**Ondersteuning** | **Details**
--- | ---
**Beoordelings limieten** | U kunt Maxi maal 35.000 fysieke servers in één [Azure migrate project](migrate-support-matrix.md#azure-migrate-projects)detecteren en beoordelen.
**Project limieten** | U kunt meerdere projecten maken in een Azure-abonnement. Naast fysieke servers kan een project VMware-Vm's en virtuele Hyper-V-machines bevatten, tot aan de evaluatie limieten voor elke.
**Discovery** (Detectie) | Het Azure Migrate-apparaat kan Maxi maal 1000 fysieke servers detecteren.
**Evaluatie** | U kunt Maxi maal 35.000 computers in één groep toevoegen.<br/><br/> U kunt Maxi maal 35.000 computers in één evaluatie evalueren.

Meer [informatie](concepts-assessment-calculation.md) over evaluaties.

## <a name="physical-server-requirements"></a>Vereisten voor fysieke servers

**Fysieke server implementatie:** De fysieke server kan zelfstandig of in een cluster worden geïmplementeerd.

**Besturings systeem:** Alle Windows-en Linux-besturings systemen kunnen worden geëvalueerd voor migratie.

**Machtigingen:**
- Gebruik voor Windows-servers een domeinaccount voor computer die lid zijn van een domein, en een lokaal account voor computers die geen lid zijn van een domein. Het gebruikersaccount moet worden toegevoegd aan deze groepen: Gebruikers van extern beheer, prestatiemetergebruikers en gebruikers van prestatielogboeken.
- Voor Linux-servers hebt u een hoofdaccount nodig op de Linux-servers die u wilt detecteren. U kunt ook een zich niet in de hoofdmap bevindend account met de vereiste mogelijkheden instellen met behulp van de volgende opdrachten:

**Opdracht** | **Doel**
--- | --- |
setcap CAP_DAC_READ_SEARCH+eip /usr/sbin/fdisk <br></br> setcap CAP_DAC_READ_SEARCH+eip /sbin/fdisk _(als /usr/sbin/fdisk niet aanwezig is)_ | Gegevens over de schijfconfiguratie verzamelen
setcap "cap_dac_override,cap_dac_read_search,cap_fowner,cap_fsetid,cap_setuid,<br>cap_setpcap,cap_net_bind_service,cap_net_admin,cap_sys_chroot,cap_sys_admin,<br>cap_sys_resource,cap_audit_control,cap_setfcap=+eip" /sbin/lvm | Gegevens van de schijfprestaties verzamelen
setcap CAP_DAC_READ_SEARCH+eip /usr/sbin/dmidecode | Het BIOS-serienummer verzamelen
chmod a+r /sys/class/dmi/id/product_uuid | BIOS-GUID verzamelen



## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate-apparaatvereisten

Azure Migrate gebruikt het [Azure migrate-apparaat](migrate-appliance.md) voor detectie en evaluatie. Het apparaat voor fysieke servers kan worden uitgevoerd op een virtuele machine of op een fysieke computer. 

- Meer informatie over de [vereisten voor apparaten](migrate-appliance.md#appliance---physical) voor fysieke servers.
- Meer informatie over Url's die het apparaat nodig heeft voor toegang tot [open bare](migrate-appliance.md#public-cloud-urls) en [overheids](migrate-appliance.md#government-cloud-urls) Clouds.
- U stelt het apparaat in met behulp van een [Power shell-script](how-to-set-up-appliance-physical.md) dat u van de Azure Portal downloadt.
Implementeer het apparaat in Azure Government [met dit script](deploy-appliance-script-government.md).

## <a name="port-access"></a>Poort toegang

De volgende tabel bevat een overzicht van de poort vereisten voor evaluatie.

**Apparaat** | **Verbinding**
--- | ---
**Apparaat** | Binnenkomende verbindingen op TCP-poort 3389, om extern bureau blad-verbindingen met het apparaat toe te staan.<br/><br/> Binnenkomende verbindingen op poort 44368, om op afstand toegang te krijgen tot de app voor het beheren van het apparaat met behulp van de URL: ``` https://<appliance-ip-or-name>:44368 ```<br/><br/> Uitgaande verbindingen op poort 443 (HTTPS) voor het verzenden van meta gegevens voor detectie en prestaties naar Azure Migrate.
**Fysieke servers** | **Windows:** Binnenkomende verbinding op WinRM-poort 5985 (HTTP) voor het ophalen van meta gegevens van de configuratie en prestaties van Windows-servers. <br/><br/> **Linux:**  Binnenkomende verbindingen op poort 22 (TCP), voor het ophalen van meta gegevens van de configuratie en prestaties van Linux-servers. |

## <a name="agent-based-dependency-analysis-requirements"></a>Vereisten voor afhankelijkheids analyse op basis van een agent

[Afhankelijkheids analyse](concepts-dependency-visualization.md) helpt u bij het identificeren van afhankelijkheden tussen on-premises machines die u wilt beoordelen en migreren naar Azure. De tabel bevat een overzicht van de vereisten voor het instellen van afhankelijkheids analyse op basis van een agent. Momenteel wordt alleen op agents gebaseerde afhankelijkheids analyse ondersteund voor fysieke servers.

**Vereiste** | **Details** 
--- | --- 
**Vóór implementatie** | Er moet een Azure Migrate project aanwezig zijn met het hulp programma voor Server evaluatie dat is toegevoegd aan het project.<br/><br/>  U kunt een afhankelijkheids visualisatie implementeren nadat u een Azure Migrate apparaat hebt ingesteld om uw on-premises computers te detecteren<br/><br/> [Meer informatie over](create-manage-projects.md) het maken van een project voor de eerste keer.<br/> [Meer informatie over het](how-to-assess.md) toevoegen van een evaluatie programma aan een bestaand project.<br/> Meer informatie over het instellen van het Azure Migrate-apparaat voor de evaluatie van [Hyper-V](how-to-set-up-appliance-hyper-v.md)-, [VMware](how-to-set-up-appliance-vmware.md)-en fysieke servers.
**Azure Government** | Visualisatie van afhankelijkheid is niet beschikbaar in Azure Government.
**Log Analytics** | Azure Migrate gebruikt de [servicetoewijzing](../azure-monitor/insights/service-map.md) oplossing in [Azure monitor logboeken](../azure-monitor/log-query/log-query-overview.md) voor de visualisatie van afhankelijkheden.<br/><br/> U koppelt een nieuwe of bestaande Log Analytics-werk ruimte aan een Azure Migrate-project. De werk ruimte voor een Azure Migrate project kan niet worden gewijzigd nadat deze is toegevoegd. <br/><br/> De werk ruimte moet zich in hetzelfde abonnement bevinden als het Azure Migrate-project.<br/><br/> De werk ruimte moet zich bevinden in de regio's VS-Oost, Zuidoost-Azië of Europa-west. Werk ruimten in andere regio's kunnen niet worden gekoppeld aan een project.<br/><br/> De werk ruimte moet zich in een regio bevinden waarin [servicetoewijzing wordt ondersteund](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions).<br/><br/> In Log Analytics wordt de werk ruimte die is gekoppeld aan Azure Migrate gelabeld met de sleutel van het migratie project en de project naam.
**Vereiste agents** | Installeer de volgende agents op elke computer die u wilt analyseren:<br/><br/> [Micro soft Monitoring Agent (MMA)](../azure-monitor/platform/agent-windows.md).<br/> De [afhankelijkheids agent](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> Als on-premises computers niet zijn verbonden met internet, moet u Log Analytics gateway op deze machines downloaden en installeren.<br/><br/> Meer informatie over het installeren van de [dependency agent](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) en [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Log Analytics werk ruimte** | De werk ruimte moet zich in hetzelfde abonnement bevinden als het Azure Migrate-project.<br/><br/> Azure Migrate ondersteunt werk ruimten die zich in het VS-Oost, Zuidoost-Azië en Europa-west regio's bevinden.<br/><br/>  De werk ruimte moet zich in een regio bevinden waarin [servicetoewijzing wordt ondersteund](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions).<br/><br/> De werk ruimte voor een Azure Migrate project kan niet worden gewijzigd nadat deze is toegevoegd.
**Kosten** | De Servicetoewijzing oplossing heeft geen kosten in rekening gebracht voor de eerste 180 dagen (vanaf de dag dat u de werk ruimte Log Analytics koppelt aan het Azure Migrate project)/<br/><br/> Na 180 dagen gelden de standaardkosten voor Log Analytics.<br/><br/> Bij het gebruik van een andere oplossing dan Servicetoewijzing in de gekoppelde Log Analytics werk ruimte worden [standaard kosten](https://azure.microsoft.com/pricing/details/log-analytics/) in rekening gebracht voor log Analytics.<br/><br/> Wanneer het Azure Migrate project wordt verwijderd, wordt de werk ruimte samen niet verwijderd. Na het verwijderen van het project is Servicetoewijzing gebruik niet gratis en worden voor elk knoop punt de kosten in rekening gebracht volgens de betaalde laag van Log Analytics werk ruimte/<br/><br/>Als u projecten hebt die u hebt gemaakt vóór Azure Migrate algemene Beschik baarheid (GA 28 februari 2018), hebt u mogelijk extra Servicetoewijzing kosten in rekening gebracht. Om ervoor te zorgen dat u na 180 dagen alleen betaalt, is het raadzaam om een nieuw project te maken, omdat bestaande werk ruimten voor GA nog steeds toerekenbaar zijn.
**Beheer** | Wanneer u agents registreert bij de werk ruimte, gebruikt u de ID en de sleutel van het Azure Migrate project.<br/><br/> U kunt de Log Analytics-werk ruimte gebruiken buiten Azure Migrate.<br/><br/> Als u het gekoppelde Azure Migrate project verwijdert, wordt de werk ruimte niet automatisch verwijderd. [Verwijder deze hand matig](../azure-monitor/platform/manage-access.md).<br/><br/> Verwijder de werk ruimte die is gemaakt door Azure Migrate, tenzij u het Azure Migrate project verwijdert. Als u dat wel doet, werkt de visualisatie functionaliteit voor afhankelijkheden niet zoals verwacht.
**Internetconnectiviteit** | Als computers niet zijn verbonden met internet, moet u de Log Analytics-gateway hierop installeren.
**Azure Government** | Afhankelijkheids analyse op basis van een agent wordt niet ondersteund.

## <a name="next-steps"></a>Volgende stappen

De [evaluatie van de fysieke server wordt voor bereid](./tutorial-discover-physical.md).
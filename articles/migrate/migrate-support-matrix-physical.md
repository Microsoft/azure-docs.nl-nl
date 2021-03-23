---
title: Ondersteuning voor de beoordeling van fysieke servers in Azure Migrate
description: Meer informatie over ondersteuning voor fysieke server beoordeling met Azure Migrate detectie en evaluatie
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 18176c5a79eda080c72b387781e6c7c9b0c66673
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104773195"
---
# <a name="support-matrix-for-physical-server-discovery-and-assessment"></a>Ondersteunings matrix voor detectie en evaluatie van fysieke servers 

In dit artikel vindt u een overzicht van de vereisten voor en ondersteuning bij het beoordelen van fysieke servers voor migratie naar Azure, met behulp van de Azure Migrate: hulp programma voor [detectie en evaluatie](migrate-services-overview.md#azure-migrate-server-assessment-tool) . Als u fysieke servers naar Azure wilt migreren, raadpleegt u de [ondersteunings matrix voor migratie](migrate-support-matrix-physical-migration.md).

Als u fysieke servers wilt beoordelen, maakt u een project en voegt u het hulp programma Azure Migrate: detectie en evaluatie toe aan het project. Wanneer het hulp programma is toegevoegd, implementeert u het [Azure migrate apparaat](migrate-appliance.md). Het apparaat detecteert voortdurend on-premises servers en verzendt de meta gegevens en prestatie gegevens van de server naar Azure. Nadat de detectie is voltooid, verzamelt u gedetecteerde servers in groepen en voert u een evaluatie uit voor een groep.

## <a name="limitations"></a>Beperkingen

**Ondersteuning** | **Details**
--- | ---
**Beoordelings limieten** | U kunt Maxi maal 35.000 fysieke servers in één [project](migrate-support-matrix.md#azure-migrate-projects)detecteren en beoordelen.
**Project limieten** | U kunt meerdere projecten maken in een Azure-abonnement. Naast fysieke servers kan een project servers op VMware en Hyper-V bevatten, tot aan de evaluatie limieten voor elke.
**Discovery** (Detectie) | Het Azure Migrate-apparaat kan Maxi maal 1000 fysieke servers detecteren.
**Evaluatie** | U kunt Maxi maal 35.000 servers in één groep toevoegen.<br/><br/> U kunt Maxi maal 35.000 servers in één evaluatie evalueren.

Meer [informatie](concepts-assessment-calculation.md) over evaluaties.

## <a name="physical-server-requirements"></a>Vereisten voor fysieke servers

**Fysieke server implementatie:** De fysieke server kan zelfstandig of in een cluster worden geïmplementeerd.

**Besturings systeem:** Alle Windows-en Linux-besturings systemen kunnen worden geëvalueerd voor migratie.

**Machtigingen:**

- Voor Windows-servers gebruikt u een domein account voor servers die lid zijn van een domein en een lokaal account voor servers die geen lid zijn van een domein. Het gebruikersaccount moet worden toegevoegd aan deze groepen: Gebruikers van extern beheer, prestatiemetergebruikers en gebruikers van prestatielogboeken.
- Voor Linux-servers hebt u een hoofdaccount nodig op de Linux-servers die u wilt detecteren. U kunt ook een zich niet in de hoofdmap bevindend account met de vereiste mogelijkheden instellen met behulp van de volgende opdrachten:

**Opdracht** | **Doel**
--- | --- |
setcap CAP_DAC_READ_SEARCH+eip /usr/sbin/fdisk <br></br> setcap CAP_DAC_READ_SEARCH+eip /sbin/fdisk _(als /usr/sbin/fdisk niet aanwezig is)_ | Gegevens over de schijfconfiguratie verzamelen
setcap "cap_dac_override,cap_dac_read_search,cap_fowner,cap_fsetid,cap_setuid,<br>cap_setpcap,cap_net_bind_service,cap_net_admin,cap_sys_chroot,cap_sys_admin,<br>cap_sys_resource,cap_audit_control,cap_setfcap=+eip" /sbin/lvm | Gegevens van de schijfprestaties verzamelen
setcap CAP_DAC_READ_SEARCH+eip /usr/sbin/dmidecode | Het BIOS-serienummer verzamelen
chmod a+r /sys/class/dmi/id/product_uuid | BIOS-GUID verzamelen

## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate-apparaatvereisten

Azure Migrate gebruikt het [Azure migrate-apparaat](migrate-appliance.md) voor detectie en evaluatie. Het apparaat voor fysieke servers kan worden uitgevoerd op een virtuele machine of op een fysieke server.

- Meer informatie over de [vereisten voor apparaten](migrate-appliance.md#appliance---physical) voor fysieke servers.
- Meer informatie over Url's die het apparaat nodig heeft voor toegang tot [open bare](migrate-appliance.md#public-cloud-urls) en [overheids](migrate-appliance.md#government-cloud-urls) Clouds.
- U stelt het apparaat in met behulp van een [Power shell-script](how-to-set-up-appliance-physical.md) dat u van de Azure Portal downloadt.
Implementeer het apparaat in Azure Government [met dit script](deploy-appliance-script-government.md).

## <a name="port-access"></a>Poort toegang

De volgende tabel bevat een overzicht van de poort vereisten voor evaluatie.

**Apparaat** | **Verbinding**
--- | ---
**Apparaat** | Binnenkomende verbindingen op TCP-poort 3389, om extern bureau blad-verbindingen met het apparaat toe te staan.<br/><br/> Binnenkomende verbindingen op poort 44368, om op afstand toegang te krijgen tot de app voor het beheren van het apparaat met behulp van de URL: ``` https://<appliance-ip-or-name>:44368 ```<br/><br/> Uitgaande verbindingen op poort 443 (HTTPS) voor het verzenden van meta gegevens voor detectie en prestaties naar Azure Migrate.
**Fysieke servers** | **Windows:** Binnenkomende verbinding op WinRM-poort 5985 (HTTP) of 5986 (HTTPS) voor het ophalen van meta gegevens van de configuratie en prestaties van Windows-servers. <br/><br/> **Linux:**  Binnenkomende verbindingen op poort 22 (TCP), voor het ophalen van meta gegevens van de configuratie en prestaties van Linux-servers. |

## <a name="agent-based-dependency-analysis-requirements"></a>Vereisten voor afhankelijkheids analyse op basis van een agent

[Afhankelijkheids analyse](concepts-dependency-visualization.md) helpt u bij het identificeren van afhankelijkheden tussen on-premises servers die u wilt beoordelen en migreren naar Azure. De tabel bevat een overzicht van de vereisten voor het instellen van afhankelijkheids analyse op basis van een agent. Momenteel wordt alleen op agents gebaseerde afhankelijkheids analyse ondersteund voor fysieke servers.

**Vereiste** | **Details**
--- | ---
**Vóór implementatie** | Er moet een project aanwezig zijn, met de Azure Migrate: hulp programma voor detectie en evaluatie toegevoegd aan het project.<br/><br/>  U kunt de visualisatie van de afhankelijkheid implementeren nadat u een Azure Migrate apparaat hebt ingesteld om uw on-premises servers te detecteren<br/><br/> [Meer informatie over](create-manage-projects.md) het maken van een project voor de eerste keer.<br/> [Meer informatie over het](how-to-assess.md) toevoegen van een evaluatie programma aan een bestaand project.<br/> Meer informatie over het instellen van het Azure Migrate-apparaat voor de evaluatie van [Hyper-V](how-to-set-up-appliance-hyper-v.md)-, [VMware](how-to-set-up-appliance-vmware.md)-en fysieke servers.
**Azure Government** | Visualisatie van afhankelijkheid is niet beschikbaar in Azure Government.
**Log Analytics** | Azure Migrate gebruikt de [servicetoewijzing](../azure-monitor/vm/service-map.md) oplossing in [Azure monitor logboeken](../azure-monitor/logs/log-query-overview.md) voor de visualisatie van afhankelijkheden.<br/><br/> U koppelt een nieuwe of bestaande Log Analytics-werk ruimte aan een project. De werk ruimte voor een project kan niet worden gewijzigd nadat deze is toegevoegd. <br/><br/> De werk ruimte moet zich in hetzelfde abonnement bevinden als het project.<br/><br/> De werk ruimte moet zich bevinden in de regio's VS-Oost, Zuidoost-Azië of Europa-west. Werk ruimten in andere regio's kunnen niet worden gekoppeld aan een project.<br/><br/> De werk ruimte moet zich in een regio bevinden waarin [servicetoewijzing wordt ondersteund](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).<br/><br/> In Log Analytics wordt de werk ruimte die is gekoppeld aan Azure Migrate gelabeld met de sleutel van het migratie project en de project naam.
**Vereiste agents** | Installeer de volgende agents op elke server die u wilt analyseren:<br/><br/> [Micro soft Monitoring Agent (MMA)](../azure-monitor/agents/agent-windows.md).<br/> De [afhankelijkheids agent](../azure-monitor/agents/agents-overview.md#dependency-agent).<br/><br/> Als on-premises servers niet zijn verbonden met internet, moet u de Log Analytics-gateway hierop downloaden en installeren.<br/><br/> Meer informatie over het installeren van de [dependency agent](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) en [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Log Analytics werk ruimte** | De werk ruimte moet zich in hetzelfde abonnement bevinden als een project.<br/><br/> Azure Migrate ondersteunt werk ruimten die zich in het VS-Oost, Zuidoost-Azië en Europa-west regio's bevinden.<br/><br/>  De werk ruimte moet zich in een regio bevinden waarin [servicetoewijzing wordt ondersteund](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).<br/><br/> De werk ruimte voor een project kan niet worden gewijzigd nadat deze is toegevoegd.
**Kosten** | De Servicetoewijzing oplossing heeft geen kosten in rekening gebracht voor de eerste 180 dagen (vanaf de dag dat u de werk ruimte van de Log Analytics aan het project koppelt)/<br/><br/> Na 180 dagen gelden de standaardkosten voor Log Analytics.<br/><br/> Bij het gebruik van een andere oplossing dan Servicetoewijzing in de gekoppelde Log Analytics werk ruimte worden [standaard kosten](https://azure.microsoft.com/pricing/details/log-analytics/) in rekening gebracht voor log Analytics.<br/><br/> Wanneer het project wordt verwijderd, wordt de werk ruimte samen niet verwijderd. Na het verwijderen van het project is Servicetoewijzing gebruik niet gratis en worden voor elk knoop punt de kosten in rekening gebracht volgens de betaalde laag van Log Analytics werk ruimte/<br/><br/>Als u projecten hebt die u hebt gemaakt vóór Azure Migrate algemene Beschik baarheid (GA 28 februari 2018), hebt u mogelijk extra Servicetoewijzing kosten in rekening gebracht. Om ervoor te zorgen dat u na 180 dagen alleen betaalt, is het raadzaam om een nieuw project te maken, omdat bestaande werk ruimten voor GA nog steeds toerekenbaar zijn.
**Beheer** | Wanneer u agents registreert bij de werk ruimte, gebruikt u de ID en de sleutel van het project.<br/><br/> U kunt de Log Analytics-werk ruimte gebruiken buiten Azure Migrate.<br/><br/> Als u het gekoppelde project verwijdert, wordt de werk ruimte niet automatisch verwijderd. [Verwijder deze hand matig](../azure-monitor/logs/manage-access.md).<br/><br/> Verwijder de werk ruimte die is gemaakt door Azure Migrate, tenzij u het project verwijdert. Als u dat wel doet, werkt de visualisatie functionaliteit voor afhankelijkheden niet zoals verwacht.
**Internetconnectiviteit** | Als servers niet zijn verbonden met internet, moet u de Log Analytics-gateway hierop installeren.
**Azure Government** | Afhankelijkheids analyse op basis van een agent wordt niet ondersteund.

## <a name="next-steps"></a>Volgende stappen

De [evaluatie van de fysieke server wordt voor bereid](./tutorial-discover-physical.md).
---
title: Ondersteuning voor Hyper-V-evaluatie in Azure Migrate
description: Meer informatie over ondersteuning voor Hyper-V-evaluatie met Azure Migrate detectie en evaluatie
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 8531d0b2252e6ddff75509046b5a4576b99d339f
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104870786"
---
# <a name="support-matrix-for-hyper-v-assessment"></a>Ondersteunings matrix voor Hyper-V-evaluatie

In dit artikel vindt u een overzicht van de vereisten voor en ondersteuning bij het detecteren en evalueren van on-premises servers die worden uitgevoerd in een Hyper-V-omgeving voor migratie naar Azure, met behulp van de Azure Migrate: hulp programma voor [detectie en evaluatie](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) . Als u servers wilt migreren die worden uitgevoerd op Hyper-V naar Azure, raadpleegt u de [ondersteunings matrix voor migratie](migrate-support-matrix-hyper-v-migration.md).

Voor het instellen van detectie en evaluatie van servers die worden uitgevoerd op Hyper-V, maakt u een project en voegt u de Azure Migrate: hulp programma voor detectie en evaluatie toe aan het project. Wanneer het hulp programma is toegevoegd, implementeert u het [Azure migrate apparaat](migrate-appliance.md). Het apparaat detecteert voortdurend on-premises servers en verzendt meta gegevens en prestaties van de server naar Azure. Nadat de detectie is voltooid, verzamelt u gedetecteerde servers in groepen en voert u een evaluatie uit voor een groep.

## <a name="limitations"></a>Beperkingen

**Ondersteuning** | **Details**
--- | ---
**Beoordelings limieten** | U kunt Maxi maal 35.000 servers in één [project](migrate-support-matrix.md#project)detecteren en beoordelen.
**Project limieten** | U kunt meerdere projecten maken in een Azure-abonnement. Naast servers op Hyper-V kan een project servers op VMware-en fysieke servers bevatten, tot de evaluatie limieten voor elk.
**Discovery** (Detectie) | Het Azure Migrate-apparaat kan Maxi maal 5000 servers detecteren die worden uitgevoerd op Hyper-V.<br/><br/> Het apparaat kan verbinding maken met Maxi maal 300 Hyper-V-hosts.
**Evaluatie** | U kunt Maxi maal 35.000 servers in één groep toevoegen.<br/><br/> U kunt Maxi maal 35.000 servers in één evaluatie voor een groep evalueren.

Meer [informatie](concepts-assessment-calculation.md) over evaluaties.

## <a name="hyper-v-host-requirements"></a>Vereisten voor de Hyper-V-host

| **Ondersteuning**                | **Details**
| :-------------------       | :------------------- |
| **Hyper-V-host**       | De Hyper-V-host kan zelfstandig zijn of in een cluster worden geïmplementeerd.<br/><br/> Op de Hyper-V-host kan Windows Server 2019, Windows Server 2016 of Windows Server 2012 R2 worden uitgevoerd. Server Core-installaties van deze besturings systemen worden ook ondersteund. <br/>U kunt geen servers beoordelen die zich bevinden op Hyper-V-hosts met Windows Server 2012.
| **Machtigingen**           | U hebt beheerders machtigingen nodig op de Hyper-V-host. <br/> Als u geen beheerders machtigingen wilt toewijzen, maakt u een lokaal of domein gebruikers account en voegt u het gebruikers account toe aan deze groepen: gebruikers van extern beheer, Hyper-V-Administrators en prestatie meter gebruikers. |
| **Externe communicatie van powershell**   | [Externe toegang via Power shell](/powershell/module/microsoft.powershell.core/enable-psremoting) moet zijn ingeschakeld op elke Hyper-V-host. |
| **Hyper-V Replica**       | Als u Hyper-V replica gebruikt (of als u meerdere servers met dezelfde server-id's hebt) en u de oorspronkelijke en gerepliceerde servers met Azure Migrate ontdekt, is de door Azure Migrate gegenereerde evaluatie mogelijk niet nauw keurig. |

## <a name="server-requirements"></a>Serververeisten

| **Ondersteuning**                  | **Details**
| :----------------------------- | :------------------- |
| **Besturingssysteem** | Alle besturings systemen kunnen worden geëvalueerd voor migratie.  |
| **Integratieservices**       | [Hyper-V-integratie Services](/virtualization/hyper-v-on-windows/reference/integration-services) moeten worden uitgevoerd op servers die u evalueert, om gegevens van het besturings systeem vast te leggen. |
| **Storage** | Lokale schijf, DAS, JBOD, opslag ruimten, CSV, SMB. Deze opslag voor Hyper-V-hosts waarop VHD/VHDX wordt opgeslagen, wordt ondersteund. <br/> Virtuele IDE-en SCSI-controllers worden ondersteund|

## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate-apparaatvereisten

Azure Migrate gebruikt het [Azure migrate-apparaat](migrate-appliance.md) voor detectie en evaluatie. U kunt het apparaat implementeren met behulp van een gecomprimeerde Hyper-V VHD die u via de portal downloadt, of met behulp van een [Power shell-script](deploy-appliance-script.md).

- Meer informatie over de [vereisten voor apparaten](migrate-appliance.md#appliance---hyper-v) voor Hyper-V.
- Meer informatie over Url's die het apparaat nodig heeft voor toegang tot [open bare](migrate-appliance.md#public-cloud-urls) en [overheids](migrate-appliance.md#government-cloud-urls) Clouds.
- In Azure Government moet u het apparaat implementeren [met behulp van het script](deploy-appliance-script-government.md).

## <a name="port-access"></a>Poort toegang

De volgende tabel bevat een overzicht van de poort vereisten voor evaluatie.

**Apparaat** | **Verbinding**
--- | ---
**Apparaat** | Binnenkomende verbindingen op TCP-poort 3389 om extern bureau blad-verbindingen met het apparaat toe te staan.<br/><br/> Binnenkomende verbindingen op poort 44368 voor externe toegang tot de app voor het beheren van apparaten met behulp van de URL: ``` https://<appliance-ip-or-name>:44368 ```<br/><br/> Uitgaande verbindingen op poort 443 (HTTPS) voor het verzenden van meta gegevens voor detectie en prestaties naar Azure Migrate.
**Hyper-V-host/cluster** | Binnenkomende verbinding op WinRM-poort 5985 (HTTP) of 5986 (HTTPS) voor het ophalen van meta gegevens en prestatie gegevens voor servers op Hyper-V, met behulp van een Common Information Model (CIM)-sessie.

## <a name="agent-based-dependency-analysis-requirements"></a>Vereisten voor afhankelijkheids analyse op basis van een agent

[Afhankelijkheids analyse](concepts-dependency-visualization.md) helpt u bij het identificeren van afhankelijkheden tussen on-premises servers die u wilt beoordelen en migreren naar Azure. De tabel bevat een overzicht van de vereisten voor het instellen van afhankelijkheids analyse op basis van een agent. Hyper-V ondersteunt momenteel alleen visualisatie van afhankelijkheden op basis van een agent.

**Vereiste** | **Details**
--- | --- 
**Vóór implementatie** | Er moet een project aanwezig zijn, met de Azure Migrate: hulp programma voor detectie en evaluatie toegevoegd aan het project.<br/><br/>  U kunt de visualisatie van de afhankelijkheid implementeren nadat u een Azure Migrate apparaat hebt ingesteld om uw on-premises servers te detecteren.<br/><br/> [Meer informatie over](create-manage-projects.md) het maken van een project voor de eerste keer.<br/> [Meer informatie over het](how-to-assess.md) toevoegen van Azure migrate: hulp programma voor detectie en evaluatie aan een bestaand project.<br/> Meer informatie over het instellen van het apparaat voor detectie en evaluatie van [servers op Hyper-V](how-to-set-up-appliance-hyper-v.md).
**Azure Government** | Visualisatie van afhankelijkheid is niet beschikbaar in Azure Government.
**Log Analytics** | Azure Migrate gebruikt de [servicetoewijzing](../azure-monitor/vm/service-map.md) oplossing in [Azure monitor logboeken](../azure-monitor/logs/log-query-overview.md) voor de visualisatie van afhankelijkheden.<br/><br/> U koppelt een nieuwe of bestaande Log Analytics-werk ruimte aan een project. De werk ruimte voor een project kan niet worden gewijzigd nadat deze is toegevoegd. <br/><br/> De werk ruimte moet zich in hetzelfde abonnement bevinden als het project.<br/><br/> De werk ruimte moet zich bevinden in de regio's VS-Oost, Zuidoost-Azië of Europa-west. Werk ruimten in andere regio's kunnen niet worden gekoppeld aan een project.<br/><br/> De werk ruimte moet zich in een regio bevinden waarin [servicetoewijzing wordt ondersteund](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).<br/><br/> In Log Analytics wordt de werk ruimte die is gekoppeld aan Azure Migrate gelabeld met de sleutel van het migratie project en de project naam.
**Vereiste agents** | Installeer de volgende agents op elke server die u wilt analyseren:<br/><br/> [Micro soft Monitoring Agent (MMA)](../azure-monitor/agents/agent-windows.md).<br/> De [afhankelijkheids agent](../azure-monitor/agents/agents-overview.md#dependency-agent).<br/><br/> Als on-premises servers niet zijn verbonden met internet, moet u de Log Analytics-gateway hierop downloaden en installeren.<br/><br/> Meer informatie over het installeren van de [dependency agent](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) en [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Log Analytics werk ruimte** | De werk ruimte moet zich in hetzelfde abonnement bevinden als het project.<br/><br/> Azure Migrate ondersteunt werk ruimten die zich in het VS-Oost, Zuidoost-Azië en Europa-west regio's bevinden.<br/><br/>  De werk ruimte moet zich in een regio bevinden waarin [servicetoewijzing wordt ondersteund](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).<br/><br/> De werk ruimte voor een project kan niet worden gewijzigd nadat deze is toegevoegd.
**Kosten** | De Servicetoewijzing oplossing heeft geen kosten in rekening gebracht voor de eerste 180 dagen (vanaf de dag dat u de werk ruimte van de Log Analytics aan het project koppelt)/<br/><br/> Na 180 dagen gelden de standaardkosten voor Log Analytics.<br/><br/> Bij het gebruik van een andere oplossing dan Servicetoewijzing in de gekoppelde Log Analytics werk ruimte worden [standaard kosten](https://azure.microsoft.com/pricing/details/log-analytics/) in rekening gebracht voor log Analytics.<br/><br/> Wanneer het project wordt verwijderd, wordt de werk ruimte samen niet verwijderd. Na het verwijderen van het project is Servicetoewijzing gebruik niet gratis en worden voor elk knoop punt de kosten in rekening gebracht volgens de betaalde laag van Log Analytics werk ruimte/<br/><br/>Als u projecten hebt die u hebt gemaakt vóór Azure Migrate algemene Beschik baarheid (GA 28 februari 2018), hebt u mogelijk extra Servicetoewijzing kosten in rekening gebracht. Om ervoor te zorgen dat u na 180 dagen alleen betaalt, is het raadzaam om een nieuw project te maken, omdat bestaande werk ruimten voor GA nog steeds toerekenbaar zijn.
**Beheer** | Wanneer u agents registreert bij de werk ruimte, gebruikt u de ID en de sleutel van het project.<br/><br/> U kunt de Log Analytics-werk ruimte gebruiken buiten Azure Migrate.<br/><br/> Als u het gekoppelde project verwijdert, wordt de werk ruimte niet automatisch verwijderd. [Verwijder deze hand matig](../azure-monitor/logs/manage-access.md).<br/><br/> Verwijder de werk ruimte die is gemaakt door Azure Migrate, tenzij u het project verwijdert. Als u dat wel doet, werkt de visualisatie functionaliteit voor afhankelijkheden niet zoals verwacht.
**Internetconnectiviteit** | Als servers niet zijn verbonden met internet, moet u de Log Analytics-gateway hierop installeren.
**Azure Government** | Afhankelijkheids analyse op basis van een agent wordt niet ondersteund.

## <a name="next-steps"></a>Volgende stappen

[Voor bereiding voor de evaluatie van servers die worden uitgevoerd op Hyper-V](./tutorial-discover-hyper-v.md).
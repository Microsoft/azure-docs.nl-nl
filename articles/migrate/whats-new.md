---
title: Nieuwe functies in Azure Migrate
description: Meer informatie over nieuwe en recente updates in de Azure Migrate-service.
ms.topic: overview
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.date: 04/19/2020
ms.custom: mvc
ms.openlocfilehash: cca4612d3b22296209b4adfc6be97cbe95477aa3
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786665"
---
# <a name="whats-new-in-azure-migrate"></a>Nieuwe functies in Azure Migrate

[Azure Migrate](migrate-services-overview.md) helpt u bij het detecteren, evalueren en migreren van on-premises servers, apps en gegevens naar de Microsoft Azure-cloud. In dit artikel vindt u een overzicht van nieuwe releases en functies in Azure Migrate.

## <a name="update-march-2021"></a>Update (maart 2021)
- Ondersteuning voor het bieden van meerdere Server referenties op Azure Migrate apparaat om geïnstalleerde toepassingen (software-inventarisatie), afhankelijkheids analyse zonder agent en detectie van SQL Server instanties en data bases in uw VMware-omgeving te detecteren. [Meer informatie](tutorial-discover-vmware.md#provide-server-credentials)
- Detectie en evaluatie van SQL Server instanties en data bases die worden uitgevoerd in uw VMware-omgeving is nu beschikbaar als preview-versie. [Meer informatie](concepts-azure-sql-assessment-calculation.md) Raadpleeg de zelf studies voor [detectie](tutorial-discover-vmware.md) en [beoordelingen](tutorial-assess-sql.md) om aan de slag te gaan.
- VMware-migratie zonder agent ondersteunt nu gelijktijdige replicatie van 500 Vm's per vCenter.
- In Azure Migrate wordt nu automatisch de Azure VM-agent op de VMware-VM's geïnstalleerd tijdens de migratie naar Azure, met behulp van de methode voor VMware-migratie zonder agent.

## <a name="update-january-2021"></a>Update (januari 2021)
-  Azure Migrate: met het hulp programma voor server migratie kunt u nu virtuele VMware-machines, fysieke servers en virtuele machines migreren van andere Clouds naar Azure virtual machines met schijven die zijn versleuteld met versleuteling aan de server zijde met door de klant beheerde sleutels (CMK).

## <a name="update-december-2020"></a>Update (december 2020)
- In Azure Migrate wordt nu automatisch de Azure VM-agent op de VMware-VM's geïnstalleerd tijdens de migratie naar Azure, met behulp van de methode voor VMware-migratie zonder agent.
- Migratie van VMware-VM's naar virtuele Azure-machines met schijven die zijn versleuteld met behulp van versleuteling aan de serverzijde (SSE) met door de klant beheerde sleutels (CMK), met behulp van Azure Migrate-servermigratie (replicatie zonder agent), is nu beschikbaar in de Azure-portal.

## <a name="update-september-2020"></a>Update (september 2020)
- Servers migreren naar Beschikbaarheidszones wordt nu ondersteund.
- Op UEFI gebaseerde VM's en fysieke servers migreren naar Azure-VM's van generatie 2 wordt nu ondersteund. Met deze release voert het hulpprogramma Azure Migrate: Server Migration de conversie van generatie 2 VM naar generatie 1 VM niet uit tijdens de migratie.
- Er is een nieuw Power BI-evaluatiedashboard voor Azure Migrate beschikbaar om u te helpen bij het vergelijken van de kosten bij verschillende evaluatie-instellingen. Het dashboard bevat een PowerShell-hulpprogramma dat automatisch de evaluaties maakt die worden ingevoegd in het Power BI-dashboard. [Meer informatie.](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/assessment-utility)
- Afhankelijkheidsanalyse (zonder agent) kan nu gelijktijdig worden uitgevoerd op 1000 VM's.
- Afhankelijkheidsanalyse (zonder agent) kan nu worden ingeschakeld of uitgeschakeld op schaal met behulp van PowerShell-scripts. [Meer informatie.](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale)
- Visualiseer netwerkverbindingen in Power BI met behulp van de gegevens die worden verzameld met behulp van afhankelijkheidsanalyse (zonder agent) [meer informatie.](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale)
- Migratie van virtuele VMware-machines met een grootte van maximaal 32 TB voor de gegevensschijf wordt nu ondersteund met behulp van de Azure Migrate: agentloze VMware-migratiemethode voor servermigratie.

## <a name="update-august-2020"></a>Update (augustus 2020)

- Verbeterde onboardingervaring waarbij een Azure Migrate-projectsleutel wordt gegenereerd op basis van de portal en wordt gebruikt om de apparaatregistratie te voltooien.
- Optie om ofwel OVA/VHD-bestanden of de installatiescripts te downloaden uit de portal om respectievelijk de VMware- en Hyper-V-apparaten in te stellen.
- Vernieuwd apparaatconfiguratiebeheer met uitgebreide gebruikerservaring.
- Ondersteuning voor meerdere referenties voor detectie van virtuele Hyper-V-machines.

## <a name="update-july-2020"></a>Update (juli 2020)

- VMware-migratie zonder agent ondersteunt nu gelijktijdige replicatie van 300 VM's per vCenter

## <a name="update-june-2020"></a>Update (juni 2020)

- Evaluaties voor het migreren van on-premises VMware VM's naar [Azure VMware Solution (AVS)](./concepts-azure-vmware-solution-assessment-calculation.md) wordt nu ondersteund. [Meer informatie](how-to-create-azure-vmware-solution-assessment.md)
- Ondersteuning voor meerdere referenties op een apparaat voor detectie van fysieke servers.
- Ondersteuning voor het toestaan van Azure-aanmelding vanaf een apparaat voor tenants, waarbij een tenantbeperking is geconfigureerd.


## <a name="update-april-2020"></a>Update (april 2020)

Azure Migrate ondersteunt implementaties in Azure Government. 

- U kunt VMware-VM's, Hyper-V-VM's en fysieke servers detecteren en evalueren.
- U kunt VMware-VM's, Hyper-V-VM's en fysieke servers migreren naar Azure.
- Voor VMware-migratie kunt u gebruikmaken van migratie met of zonder agent. [Meer informatie](server-migrate-overview.md).
- [Beoordeel](migrate-support-matrix.md#supported-geographies-azure-government) ondersteunde locaties en regio's voor Azure Government.
- [Op agent gebaseerde afhankelijkheidsanalyse](concepts-dependency-visualization.md#agent-based-analysis) wordt niet ondersteund in Azure Government.
- Functies in preview worden ondersteund in Azure Government, te weten de [afhankelijkheidsanalyse zonder agent](concepts-dependency-visualization.md#agentless-analysis) en [toepassingsdetectie](how-to-discover-applications.md).


## <a name="update-march-2020"></a>Update (maart 2020)

Er is nu installatie op basis van een script beschikbaar voor het instellen van het [Azure Migrate-apparaat](migrate-appliance.md):

- De installatie op basis van een script is een alternatief voor de OVA-installatie (VMware)/VHD (Hyper-V) van het apparaat.
- Deze biedt een PowerShell-installatiescript dat kan worden gebruikt om het apparaat in te stellen voor VMware/Hyper-V op een bestaande computer met Windows Server 2016.

## <a name="update-november-2019"></a>Update (november 2019)

Er zijn een aantal nieuwe functies toegevoegd aan Azure Migrate:

- **Evaluatie van fysieke servers**. Evaluatie van on-premises fysieke servers wordt nu ondersteund, naast de fysieke servermigratie die al wordt ondersteund.
- **Evaluatie op basis van import**. Evaluatie van computers die gebruikmaken van metagegevens en prestatiegegevens die worden geleverd in een CSV-bestand, wordt nu ondersteund.
- **Toepassingsdetectie**: Azure Migrate ondersteunt nu de detectie op toepassingsniveau van apps, rollen en functies met behulp van het Azure Migrate-apparaat. Dit wordt momenteel alleen ondersteund voor VMware-VM's en is beperkt tot detectie (evaluatie wordt momenteel niet ondersteund). [Meer informatie](how-to-discover-applications.md)
- **Visualisatie van afhankelijkheden zonder agent**: U hoeft niet meer expliciet agents te installeren voor visualisatie van afhankelijkheden. Zowel zonder agent als met agent wordt nu ondersteund.
- **Virtueel bureaublad**: Gebruik ISV-hulpprogramma's om on-premises VDI (Virtual Desktop Infrastructure) te evalueren en te migreren naar Windows Virtual Desktop in Azure.
- **Web-app**: De Azure App Service Migration Assistant, die wordt gebruikt voor het evalueren en migreren van web-apps, is nu geïntegreerd in Azure Migrate.

Er zijn nieuwe hulpprogramma's voor evaluatie en migratie toegevoegd aan Azure Migrate:

- **RackWare**: biedt Cloud migratie.
- **Movere**: biedt evaluatie.

[Meer informatie](migrate-services-overview.md) over het gebruik van hulpprogramma's en ISV-aanbiedingen voor evaluatie en migratie in Azure Migrate.

## <a name="azure-migrate-current-version"></a>Huidige versie van Azure Migrate

De huidige versie van Azure Migrate (uitgebracht in juli 2019) biedt een aantal nieuwe functies:

- **Geïntegreerd migratieplatform**: Azure Migrate biedt nu één portal om de beleving van uw migratie naar Azure te centraliseren, te beheren en bij te houden, met een verbeterde implementatiestroom en portalervaring.
- **Hulpprogramma's voor evaluatie en migratie**: Azure Migrate biedt systeemeigen hulpprogramma's en is te integreren met andere Azure-services, evenals met hulpprogramma's van onafhankelijke softwareleveranciers (ISV's). [Meer informatie](migrate-services-overview.md#isv-integration) over ISV-integratie.
- **Azure Migrate-evaluatie**: Met het hulpprogramma Azure Migrate Server Assessment kunt u VMware-VM's en Hyper-V-VM's evalueren voor migratie naar Azure. U kunt ook evalueren voor migratie met behulp van andere Azure-services en met ISV-hulpprogramma's.
- **Azure Migrate-migratie**: Met behulp van het hulpprogramma Azure Migrate Server Migration kunt u on-premises VMware-VM's en Hyper-V-VM's migreren naar Azure, evenals fysieke servers, andere gevirtualiseerde servers en VM's in een privécloud/openbare cloud. Daarnaast kunt u migreren naar Azure met behulp van ISV-hulpprogramma's.
- **Azure Migrate-apparaat**: Azure Migrate implementeert een licht apparaat voor detectie en evaluatie van on-premises VMware-VM's en Hyper-V-VM's.
    - Dit apparaat wordt gebruikt door Azure Migrate Server Assessment en Azure Migrate Server Migration voor migratie zonder agent.
    - Het apparaat detecteert voortdurend metagegevens en prestatiegegevens van de server ten behoeve van evaluatie en migratie.  
- **Migratie van VMware-VM's**:  Azure Migrate Server Migration biedt een aantal methoden voor het migreren van on-premises VMware-VM's naar Azure.  Een migratie zonder agent met behulp van het Azure Migrate-apparaat en een migratie op basis van een agent die gebruikmaakt van een replicatieapparaat en een agent implementeert op elke VM die u wilt migreren. [Meer informatie](server-migrate-overview.md)
 - **Database-evaluatie en -migratie**: Vanuit Azure Migrate kunt u on-premises databases evalueren voor migratie naar Azure met behulp van de Azure Database Migration Assistant. U kunt databases migreren met behulp van de Azure Database Migration Service.
- **Migratie van web-apps**: U kunt web-apps evalueren met behulp van een openbare eindpunt-URL met de Azure App Service. Voor de migratie van interne NET-apps kunt u de App Service Migration Assistant downloaden en uitvoeren.
- **Data Box**: Importeer grote hoeveelheden offlinegegevens in Azure met behulp van Azure Data Box in Azure Migrate.

## <a name="azure-migrate-previous-version"></a>Vorige versie van Azure Migrate

Als u de vorige versie van Azure Migrate gebruikt (alleen evaluatie van on-premises VMware-VM's werd ondersteund), kunt u nu beter de huidige versie gaan gebruiken. In de vorige versie kunt u geen nieuwe Azure Migrate-projecten meer maken of nieuwe detecties uitvoeren. U houdt toegang tot bestaande projecten. Ga hiertoe in Azure Portal naar **Alle services** en zoek naar **Azure Migrate**. De Azure Migrate-meldingen bevatten een koppeling voor toegang tot oude Azure Migrate-projecten.



## <a name="next-steps"></a>Volgende stappen

- Meer informatie over prijzen voor Azure Migrate vindt u [hier](https://azure.microsoft.com/pricing/details/azure-migrate/).
- [Bekijk de veelgestelde vragen](resources-faq.md) over Azure Migrate.
- Probeer onze zelfstudies om [VMware-VM's](./tutorial-assess-vmware-azure-vm.md) en [Hyper-V-VM's](tutorial-assess-hyper-v.md) te evalueren.
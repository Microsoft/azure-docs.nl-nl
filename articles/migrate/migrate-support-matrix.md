---
title: Ondersteunings matrix Azure Migrate
description: Hierin wordt een overzicht gegeven van de ondersteunings instellingen en beperkingen voor de Azure Migrate-service.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: af0b8a4d3dfbce32e412f5294fb19ade61fd7661
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105628166"
---
# <a name="azure-migrate-support-matrix"></a>Ondersteunings matrix Azure Migrate

U kunt de [Azure migrate-service](./migrate-services-overview.md) gebruiken om servers te beoordelen en te migreren naar de Microsoft Azure Cloud. In dit artikel vindt u een overzicht van algemene ondersteunings instellingen en beperkingen voor Azure Migrate scenario's en implementaties.

## <a name="supported-assessmentmigration-scenarios"></a>Ondersteunde scenario's voor evaluatie/migratie

De tabel bevat een overzicht van de ondersteunde detectie-, evaluatie-en migratie scenario's.

**Implementatie** | **Details** 
--- | --- 
**Discovery** (Detectie) | U kunt de meta gegevens van de server en dynamische prestaties ontdekken.
**App-detectie** | U kunt apps, rollen en functies ontdekken die worden uitgevoerd op virtuele VMware-machines. Deze functie is momenteel beperkt tot detectie. De evaluatie bevindt zich momenteel op het niveau van de server. We bieden nog geen evaluaties op basis van apps, rollen of functies. 
**Evaluatie** | Evalueer on-premises workloads en gegevens die worden uitgevoerd op virtuele VMware-machines, virtuele Hyper-V-machines en fysieke servers. Evalueer het gebruik van Azure Migrate: Server Assessment, Microsoft Data Migration Assistant (DMA), evenals andere hulpprogram ma's en ISV-aanbiedingen.
**Migratie** | Migreer werk belastingen en gegevens die worden uitgevoerd op fysieke servers, virtuele VMware-machines, virtuele Hyper-V-machines, fysieke servers en virtuele machines in de Cloud naar Azure. Migratie met behulp van Azure Migrate: Server assessment en Azure Database Migration Service (DMS) en ook andere hulpprogram ma's en ISV-aanbiedingen.

> [!NOTE]
> Op dit moment kunnen ISV-hulpprogram ma's geen gegevens verzenden naar Azure Migrate in Azure Government. U kunt geïntegreerde micro soft-hulpprogram ma's gebruiken of partner hulpprogramma's onafhankelijk van.

## <a name="supported-tools"></a>Ondersteunde hulpprogramma 's


Specifieke ondersteuning van het hulp programma wordt in de tabel samenvatten.

**Hulpprogramma** | **Evalueren** | **Migreren** 
--- | --- | ---
Azure Migrate: Server Assessment | Evalueer [virtuele VMware-machines](./tutorial-discover-vmware.md), [virtuele Hyper-V-machines](./tutorial-discover-hyper-v.md)en [fysieke servers](./tutorial-discover-physical.md). |  Niet beschikbaar (N.v.t.)
Azure Migrate: Server Migration | N.v.t. | Migreer [VMware-vm's](tutorial-migrate-vmware.md), [virtuele Hyper-V-machines](tutorial-migrate-hyper-v.md)en [fysieke servers](tutorial-migrate-physical-virtual-machines.md).
[Carbonite](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure) | N.v.t. | Migreer VMware-Vm's, Hyper-V-Vm's, fysieke servers en andere Cloud werkbelastingen. 
[Cloudamize](https://www.cloudamize.com/platform#tab-0)| Evalueer virtuele VMware-machines, virtuele Hyper-V-machines, fysieke servers en andere Cloud werkbelastingen. | N.v.t.
[Corent Technology](https://go.microsoft.com/fwlink/?linkid=2084928) | Evalueer VMware-Vm's, Hyper-V-Vm's, fysieke server zand andere Cloud werkbelastingen. |  Migreer VMware-Vm's, virtuele Hyper-V-machines, fysieke servers, open bare Cloud werkbelastingen.
[Apparaat 42](https://go.microsoft.com/fwlink/?linkid=2097158) | Evalueer virtuele VMware-machines, Hyper-V-Vm's, fysieke servers en andere Cloud werkbelastingen.| N.v.t.
[DMA](/sql/dma/dma-overview) | Beoordeel SQL Server data bases. | N.v.t.
[DMS](../dms/dms-overview.md) | N.v.t. | Migrate SQL Server, Oracle, MySQL, PostgreSQL, MongoDB. 
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Virtual Desktop Infrastructure (VDI) evalueren | N.v.t.
[Movere](https://www.movere.io/) | Evalueer virtuele VMware-machines, Hyper-V-Vm's, xen-Vm's, fysieke servers, werk stations (inclusief VDI) en andere Cloud werkbelastingen. | N.v.t.
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | N.v.t. | Virtuele VMWare-machines, virtuele Hyper-V-machines, xen-Vm's, KVM-Vm's, fysieke servers en andere Cloud werkbelastingen migreren 
[Turbonomic](https://go.microsoft.com/fwlink/?linkid=2094295)  | Evalueer virtuele VMware-machines, Hyper-V-Vm's, fysieke servers en andere Cloud werkbelastingen. | N.v.t.
[UnifyCloud](https://go.microsoft.com/fwlink/?linkid=2097195) | Evalueer virtuele VMware-machines, Hyper-V-Vm's, fysieke servers en andere Cloud werkbelastingen en SQL Server data bases. | N.v.t.
[Webapp Migration Assistant](https://appmigration.microsoft.com/) | Web-apps evalueren | Web-apps migreren.
[Zerto](https://go.microsoft.com/fwlink/?linkid=2157322) | N.v.t. |  Migreer VMware-Vm's, Hyper-V-Vm's, fysieke servers en andere Cloud werkbelastingen.


## <a name="project"></a>Project

**Ondersteuning** | **Details**
--- | ---
Abonnement | Kan meerdere projecten binnen een abonnement hebben.
Azure-machtigingen | De gebruiker heeft de machtigingen Inzender of eigenaar nodig in het abonnement om een project te maken.
Virtuele VMware-machines  | Evalueer Maxi maal 35.000 VMware-Vm's in één project.
Virtuele Hyper-V-machines    | Evalueer Maxi maal 35.000 Hyper-V-Vm's in één project.

Een project kan zowel virtuele VMware-machines als virtuele Hyper-V-machines bevatten, tot aan de evaluatie limieten.

## <a name="azure-permissions"></a>Azure-machtigingen

Azure Migrate werkt alleen met Azure als u deze machtigingen hebt voordat u begint met het beoordelen en migreren van servers.

**Taak** | **Machtigingen** | **Details**
--- | --- | ---
Een project maken | Uw Azure-account heeft machtigingen nodig om een project te maken. | Ingesteld voor [VMware](./tutorial-discover-vmware.md#prepare-an-azure-user-account)-, [Hyper-V-](./tutorial-discover-hyper-v.md#prepare-an-azure-user-account)of [fysieke servers](./tutorial-discover-physical.md#prepare-an-azure-user-account).
Het Azure Migrate apparaat registreren| Azure Migrate maakt gebruik van een licht gewicht [Azure migrate apparaat](migrate-appliance.md) om servers te beoordelen met Azure migrate: Server evaluatie en voor het uitvoeren van [agentloze migratie](server-migrate-overview.md) van virtuele VMware-machines met Azure migrate: Server migratie. Met dit apparaat worden servers gedetecteerd en worden meta gegevens en prestatie gegevens naar Azure Migrate verzonden.<br/><br/> Tijdens de registratie worden providers (micro soft. OffAzure, micro soft. migrate en micro soft. de sleutel kluis) geregistreerd bij het abonnement dat is geselecteerd in het apparaat, zodat het abonnement samenwerkt met de resource provider. Als u zich wilt registreren, moet u de toegang tot mede werkers of eigenaar hebben voor het abonnement.<br/><br/> **VMware**: tijdens het voorbereiden van Azure migrate worden twee Azure Active Directory-apps (Azure AD) gemaakt. De eerste app communiceert tussen de apparaat agents en de Azure Migrate service. De app heeft geen machtigingen om Azure resource management-aanroepen te maken of Azure RBAC-toegang voor resources te hebben. De tweede app heeft alleen toegang tot een Azure Key Vault die is gemaakt in het gebruikers abonnement voor VMware-migratie zonder agent. Bij migratie zonder agent maakt Azure Migrate een Key Vault voor het beheren van toegangs sleutels voor het replicatie-opslag account in uw abonnement. Het heeft Azure RBAC-toegang op de Azure Key Vault (in de Tenant van de klant) wanneer detectie vanaf het apparaat wordt gestart.<br/><br/> **Hyper-V**-tijdens onboarding. Azure Migrate maakt een Azure AD-app. De app communiceert tussen de apparaat agents en de Azure Migrate service. De app heeft geen machtigingen om Azure resource management-aanroepen te maken of Azure RBAC-toegang voor resources te hebben. | Ingesteld voor [VMware](./tutorial-discover-vmware.md#prepare-an-azure-user-account)-, [Hyper-V-](./tutorial-discover-hyper-v.md#prepare-an-azure-user-account)of [fysieke servers](./tutorial-discover-physical.md#prepare-an-azure-user-account).
Een sleutel kluis maken voor VMware-agentloze migratie | Als u virtuele VMware-machines wilt migreren met agentloze Azure Migrate: Server migratie, Azure Migrate maakt u een Key Vault voor het beheren van toegangs sleutels voor het replicatie-opslag account in uw abonnement. Als u de kluis wilt maken, stelt u machtigingen (eigenaar of Inzender en beheerder van gebruikers toegang) in voor de resource groep waar het project zich bevindt. | [Stel](./tutorial-discover-vmware.md#prepare-an-azure-user-account) machtigingen in.

## <a name="supported-geographies-public-cloud"></a>Ondersteunde geographs (open bare Cloud)

U kunt een project maken in een aantal geographs in de open bare Cloud.

- Hoewel u alleen projecten in deze geografi kunt maken, kunt u de servers voor andere doel locaties evalueren of migreren.
- De Geografie van het project wordt alleen gebruikt om de gedetecteerde meta gegevens op te slaan.
- Wanneer u een project maakt, selecteert u een geografie. Het project en gerelateerde resources worden gemaakt in een van de regio's in de geografie. De regio wordt toegewezen door de Azure Migrate service.

**Geografie** | **Opslag locatie van meta gegevens**
--- | ---
Azië en Stille Oceaan | Azië Azië-oost of Zuidoost
Australië | Australië-oost of Australië-zuidoost
Brazilië | Brazilië - zuid
Canada | Canada-centraal of Canada-oost
Europa | Europa - noord of Europa - west
Frankrijk | Frankrijk - centraal
India | Centraal-India of India-zuid
Japan |  Japan-Oost of Japan-West
Korea | Korea-centraal of Korea-zuid
Zwitserland | Zwitserland - noord
Verenigd Koninkrijk | UK-zuid of UK-west
Verenigde Staten | VS-midden, VS-West 2

> [!NOTE]
> Voor geografie is Zwitserland-west alleen beschikbaar voor REST API gebruikers en moet er een goedgekeurd abonnement zijn.

## <a name="supported-geographies-azure-government"></a>Ondersteunde geographs (Azure Government)

**Taak** | **Geografie** | **Details**
--- | --- | ---
Project maken | Verenigde Staten | Meta gegevens worden opgeslagen in US Gov-Arizona, US Gov-Virginia
Doel evaluatie | Verenigde Staten | Doel regio's: US Gov-Arizona, US Gov-Virginia, US Gov-Texas
Doel replicatie | Verenigde Staten | Doel regio's: US DoD-centraal, US DoD-oost, US Gov-Arizona, US Gov-Iowa, US Gov-Texas, US Gov-Virginia


## <a name="vmware-assessment-and-migration"></a>VMware-evaluatie en-migratie

[Bekijk](migrate-support-matrix-vmware.md) de Azure migrate: Server evaluatie en Azure migrate: Server migratie ondersteunings matrix voor VMware-vm's.

## <a name="hyper-v-assessment-and-migration"></a>Evaluatie en migratie van Hyper-V

[Bekijk](migrate-support-matrix-hyper-v.md) de Azure migrate: Server evaluatie en Azure migrate: Server migratie ondersteunings matrix voor Hyper-V-vm's.



## <a name="azure-migrate-versions"></a>Versies van Azure Migrate

Er zijn twee versies van de Azure Migrate-service:

- **Huidige versie**: met deze versie kunt u nieuwe projecten maken, on-premises evalueren en analyses en migraties organiseren. [Meer informatie](whats-new.md).
- **Vorige versie**: voor klant die de vorige versie van Azure migrate gebruikt (alleen de evaluatie van on-premises virtuele VMware-machines wordt ondersteund), moet u nu de huidige versie gebruiken. In de vorige versie kunt u geen nieuwe projecten maken of nieuwe detecties uitvoeren.

## <a name="next-steps"></a>Volgende stappen

- [Evalueer virtuele VMware-machines](./tutorial-assess-vmware-azure-vm.md) voor migratie.
- [Evalueer virtuele Hyper-V-machines](tutorial-assess-hyper-v.md) voor migratie.

---
title: Over Azure Site Recovery configuratie/proces/Master doel servers
description: Dit artikel bevat een overzicht van de configuratie, het proces en de hoofddoel servers die worden gebruikt bij het instellen van herstel na nood gevallen van on-premises virtuele VMware-machines naar Azure met Azure Site Recovery
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: cd5ded18d1a8f1f5fd96212d37725bb5db13002f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "80062094"
---
# <a name="about-site-recovery-components-configuration-process-master-target"></a>Over Site Recovery-onderdelen (configuratie, proces, hoofd doel)

In dit artikel worden de configuratie, het proces en de hoofddoel servers beschreven die worden gebruikt door de [site Recovery](site-recovery-overview.md) -service voor het repliceren van virtuele VMware-machines en fysieke servers naar Azure.

## <a name="configuration-server"></a>Configuratieserver

Voor herstel na nood gevallen van on-premises virtuele VMware-machines en fysieke servers implementeert u een on-premises Site Recovery configuratie server.

**Instelling** | **Details** | **Koppelingen**
--- | --- | ---
**Onderdelen**  | Op de computer met de configuratie server worden alle on-premises Site Recovery onderdelen uitgevoerd, waaronder de configuratie server, de proces server en de hoofddoel server.<br/><br/> Wanneer u de configuratie server instelt, worden alle onderdelen automatisch geïnstalleerd. | [Lees](vmware-azure-common-questions.md#configuration-server) de veelgestelde vragen over de configuratie server.
**Role** | De configuratieserver coördineert de communicatie tussen on-premises en Azure, en beheert de gegevensreplicatie. | Meer informatie over de architectuur voor de nood herstel van [VMware](vmware-azure-architecture.md) en [fysieke servers](physical-azure-architecture.md) naar Azure.
**VMware-vereisten** | Voor herstel na nood gevallen van on-premises virtuele VMware-machines moet u de configuratie server installeren en uitvoeren als een on-premises, Maxi maal beschik bare VMware VM. | [Meer informatie over](vmware-azure-deploy-configuration-server.md#prerequisites) de vereisten.
**VMware-implementatie** | We raden u aan de configuratie server te implementeren met behulp van een gedownloade eicellen-sjabloon. Deze methode biedt een eenvoudige manier om een configuratie server in te stellen die voldoet aan alle vereisten en voor waarden.<br/><br/> Als u om een of andere reden geen virtuele VMware-machine kunt implementeren met behulp van een sjabloon van de eicellen, stelt u de configuratie Server-computers hand matig in, zoals hieronder wordt beschreven voor nood herstel van een fysieke machine. | [Implementeren](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template) met een eicellen-sjabloon.
**Vereisten voor fysieke servers** | Voor herstel na nood gevallen op fysieke servers op locatie, implementeert u de configuratie server hand matig. | [Meer informatie over](physical-azure-set-up-source.md#prerequisites) de vereisten.
**Fysieke server implementatie** | Als deze niet kan worden geïnstalleerd als een virtuele VMware-machine, kunt u deze installeren op een fysieke server. | [Implementeer](physical-azure-set-up-source.md#set-up-the-source-environment) de configuratie server hand matig.

## <a name="process-server"></a>Proces server

Een proces server verwerkt replicatie gegevens tijdens failover en failback, en installeert de Mobility-service voor on-premises virtuele VMware-machines en fysieke servers.

**Instelling** | **Details** | **Koppelingen**
--- | --- | ---
**Implementatie**  | Wanneer de configuratie server wordt geïmplementeerd, wordt standaard de installatie van de proces server geïnstalleerd. <br/><br/> Er is een on-premises proces server nodig voor herstel na nood gevallen en replicatie van on-premises virtuele VMware-machines en fysieke servers. | [Meer informatie](vmware-azure-architecture.md#architectural-components).
**Rol (on-premises**) | Ontvangt replicatie gegevens van machines die zijn ingeschakeld voor replicatie. <br/><br/> Optimaliseert replicatie gegevens met caching, compressie en versleuteling, en verzendt deze naar Azure Storage. <br/><br/> Voert een push-installatie van de Site Recovery Mobility-service uit op on-premises VMware-Vm's en fysieke servers die u wilt repliceren. <br/><br/> Voert automatische detectie van on-premises machines uit. | [Meer informatie](vmware-azure-enable-replication.md).
**Rol (failback vanuit Azure)** | Na een failover vanaf uw on-premises site stelt u een proces server in Azure in als een Azure-VM om de failback naar uw on-premises locatie af te handelen.<br/><br/> De proces server in Azure is tijdelijk. De Azure-VM kan worden verwijderd nadat de failback is voltooid. | [Meer informatie](vmware-azure-set-up-process-server-azure.md).
**Schalen** | Voor grotere implementaties kunt u on-premises extra, scale-out proces servers instellen. Extra servers hebben een uitbrei ding van de capaciteit, door het verwerken van grotere aantallen replicerende machines en grotere volumes van replicatie verkeer.<br/><br/> U kunt computers verplaatsen tussen twee proces servers, om het replicatie verkeer te verdelen. | [Meer informatie](vmware-azure-set-up-process-server-scale.md).

## <a name="master-target-server"></a>Hoofddoelserver

Op de hoofddoelserver worden de replicatiegegevens tijdens de failback vanuit Azure afgehandeld.

- Standaard is de hoofddoel server geïnstalleerd op de configuratie server.
- Voor grote implementaties kunt u een extra, afzonderlijke Master doel server toevoegen voor failback.

## <a name="next-steps"></a>Volgende stappen

- Bekijk de [architectuur](vmware-azure-architecture.md) voor nood herstel van virtuele VMware-machines en fysieke servers.
- Bekijk de [vereisten en voor waarden](vmware-physical-azure-support-matrix.md) voor herstel na nood gevallen van virtuele VMware-machines en fysieke servers naar Azure.

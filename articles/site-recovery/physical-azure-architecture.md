---
title: Architectuur van herstel na nood geval voor fysieke server in Azure Site Recovery
description: Dit artikel bevat een overzicht van de onderdelen en architectuur die worden gebruikt tijdens nood herstel van on-premises fysieke servers naar Azure met de Azure Site Recovery-service.
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 966636e269043d81912b552711635f34b412f22f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97654724"
---
# <a name="physical-server-to-azure-disaster-recovery-architecture"></a>Architectuur van herstel na noodgevallen van fysieke server naar Azure

In dit artikel worden de architectuur en processen beschreven die worden gebruikt bij replicatie, failover en herstel van fysieke Windows-en Linux-servers tussen een on-premises site en Azure, met behulp van de [Azure site Recovery](site-recovery-overview.md) -service.

## <a name="architectural-components"></a>Architectuuronderdelen

De volgende tabel en afbeelding bevat een weer gave op hoog niveau van de onderdelen die worden gebruikt voor de replicatie van de fysieke server naar Azure.

| **Onderdeel** | **Vereiste** | **Details** |
| --- | --- | --- |
| **Azure** | Een Azure-abonnement en een Azure-netwerk. | Gerepliceerde gegevens van on-premises fysieke machines worden opgeslagen in azure Managed disks. Virtuele Azure-machines worden gemaakt met de gerepliceerde gegevens wanneer u een failover van on-premises naar Azure uitvoert. De Azure-VM's maken verbinding met het virtuele Azure-netwerk wanneer ze worden gemaakt. |
| **Proces server** | Standaard geïnstalleerd in combi natie met de configuratie server. | Fungeert als replicatiegateway. Dit onderdeel ontvangt replicatiegegevens, optimaliseert de gegevens met caching, compressie en codering, en verzendt ze naar de Azure-opslag.<br/><br/> De proces server installeert ook de Mobility-service op servers die u wilt repliceren.<br/><br/> Naarmate uw implementatie groeit, kunt u extra, afzonderlijke proces servers toevoegen om grotere volumes van replicatie verkeer af te handelen. |
| **Hoofddoelserver** | Standaard geïnstalleerd in combi natie met de configuratie server. | Hiermee worden replicatie gegevens verwerkt tijdens een failback van Azure.<br/><br/> Voor grote implementaties kunt u een extra, afzonderlijke Master doel server toevoegen voor failback. |
| **Gerepliceerde servers** | De Mobility-service wordt geïnstalleerd op elke server die u repliceert. | U wordt aangeraden automatische installatie vanaf de proces server toe te staan. Of u kunt de service hand matig installeren of een geautomatiseerde implementatie methode, zoals Configuration Manager, gebruiken. |

**Fysieke naar Azure-architectuur**

![Onderdelen](./media/physical-azure-architecture/arch-enhanced.png)

## <a name="set-up-outbound-network-connectivity"></a>Uitgaande netwerk verbinding instellen

Als u wilt dat Site Recovery werkt zoals verwacht, moet u de uitgaande netwerk verbinding wijzigen zodat uw omgeving kan repliceren.

> [!NOTE]
> Site Recovery biedt geen ondersteuning voor het gebruiken van een verificatieproxy om netwerkconnectiviteit te beheren.

### <a name="outbound-connectivity-for-urls"></a>Uitgaande connectiviteit voor URL's

Als u een URL-firewallproxy gebruikt om de uitgaande connectiviteit te beheren, staat u toegang tot deze URL’s toe:

| **Naam**                  | **Commercieel**                               | **Overheid**                                 | **Beschrijving** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Storage                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net` | Hiermee kunnen gegevens van de VM naar het cache-opslagaccount in de bronregio worden geschreven. |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Verzorgt autorisatie en authenticatie voor de URL’s van Site Recovery. |
| Replicatie               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | Maakt het de VM mogelijk te communiceren met de Site Recovery-service. |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Maakt het de VM mogelijk bewakings- en diagnosegegevens van Site Recovery te schrijven. |


## <a name="replication-process"></a>Replicatieproces

1. U stelt de implementatie in, met inbegrip van on-premises en Azure-onderdelen. In de Recovery Services kluis geeft u de replicatie bron en het doel op, stelt u de configuratie server in, maakt u een replicatie beleid en schakelt u replicatie in.
1. Computers repliceren met het replicatie beleid en een eerste kopie van de server gegevens wordt gerepliceerd naar Azure Storage.
1. Wanneer de initiële replicatie is voltooid, begint de replicatie van Delta wijzigingen naar Azure. Bijgehouden wijzigingen voor een machine worden opgeslagen in een bestand met de extensie _. HRL_ .
   - Computers communiceren met de configuratie server op HTTPS-poort 443 inkomend, voor replicatie beheer.
   - Computers verzenden replicatie gegevens naar de proces server op HTTPS-poort 9443 inkomend (kan worden gewijzigd).
   - De configuratie Server organiseert replicatie beheer met Azure via HTTPS-poort 443 uitgaand.
   - De proces server ontvangt gegevens van de bron machines, optimaliseert en versleutelt deze en verzendt deze naar Azure Storage via HTTPS-poort 443 uitgaand.
   - Als u multi-VM-consistentie inschakelt, communiceren machines in de replicatiegroep met elkaar via poort 20004. Meerdere VM’s worden gebruikt als u meerdere machines groepeert in de replicatiegroepen die crashconsistent en app-consistent herstelpunten delen bij failover. Deze groepen zijn handig als computers dezelfde werk belasting uitvoeren en moeten consistent zijn.
1. Verkeer wordt via internet gerepliceerd naar de openbare eindpunten van Azure Storage. U kunt ook [openbare peering](../expressroute/about-public-peering.md) van Azure ExpressRoute gebruiken.

   > [!NOTE]
   > Replicatie wordt niet ondersteund voor een site-naar-site-VPN vanaf een on-premises site of persoonlijke Azure ExpressRoute- [peering](concepts-expressroute-with-site-recovery.md#on-premises-to-azure-replication-with-expressroute).

**Fysiek naar Azure-replicatie proces**

![Replicatieproces](./media/physical-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Failover- en failbackproces

Nadat de replicatie is ingesteld, kunt u een nood herstel analyse uitvoeren (testfailover) om te controleren of alles werkt zoals verwacht. Daarna kunt u een failover uitvoeren en een failback uitvoeren als dat nodig is. Houd rekening met de volgende items:

- Geplande failover wordt niet ondersteund.
- Failback naar een on-premises VMware VM is nood zakelijk. U hebt een on-premises VMware-infra structuur nodig, zelfs wanneer u on-premises fysieke servers naar Azure repliceert.
- U kunt een failover uitvoeren voor een enkele machine of herstel plannen maken om meerdere computers samen te werken.
- Wanneer u een failover uitvoert, worden Azure-Vm's gemaakt op basis van gerepliceerde gegevens in azure Storage.
- Nadat de eerste failover is geactiveerd, voert u deze door om toegang te krijgen tot de workload vanuit de Azure-VM.
- Als uw primaire on-premises site weer beschikbaar is, kunt u een failback uitvoeren.
- Stel een infra structuur voor failback in, met inbegrip van:
  - **Tijdelijke proces server in azure**: als u een failback van Azure wilt uitvoeren, stelt u een Azure vm in om te fungeren als een proces server voor het afhandelen van replicatie vanuit Azure. U kunt deze virtuele machine verwijderen nadat de failback is voltooid.
  - **VPN-verbinding**: als u een failback wilt uitvoeren, hebt u een VPN-verbinding (of Azure ExpressRoute) nodig van het Azure-netwerk naar de on-premises site.
  - **Afzonderlijke hoofddoel server**: standaard wordt het failback uitgevoerd door de hoofddoel server die is geïnstalleerd met de configuratie server op de on-premises VMware VM. Als u grote hoeveel heden verkeer wilt herstellen, moet u een afzonderlijke on-premises hoofddoel server instellen.
  - **Failbackbeleid**: als u wilt repliceren naar de on-premises site, hebt u een failbackbeleid nodig. Het beleid is automatisch gemaakt bij het maken van uw replicatie beleid van on-premises naar Azure.
  - **VMware-infra structuur**: als u een failback wilt uitvoeren, hebt u een VMware-infra structuur nodig. U kunt geen failback uitvoeren naar een fysieke server.
- Nadat de onderdelen zijn geplaatst, vindt failback plaats in drie fasen:
  - **Fase 1**: Beveilig de Azure vm's opnieuw, zodat ze worden gerepliceerd van Azure naar de on-premises virtuele VMware-machines.
  - **Fase 2**: een failover uitvoeren naar de on-premises site.
  - **Fase 3**: nadat de workloads zijn hersteld, schakelt u de replicatie opnieuw in.

**VMware-failback van Azure**

![Failback](./media/physical-azure-architecture/enhanced-failback.png)

## <a name="next-steps"></a>Volgende stappen

Voor het instellen van herstel na nood gevallen voor fysieke servers naar Azure, raadpleegt u de [hand leiding](physical-azure-disaster-recovery.md).

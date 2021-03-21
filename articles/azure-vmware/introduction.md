---
title: Inleiding
description: Meer informatie over de functies en voordelen van Azure VMware Solution voor het implementeren en beheren van VMware-workloads in Azure.
ms.topic: overview
ms.date: 11/11/2020
ms.openlocfilehash: 255d3599385c60d3b13f4769796ced41a1177311
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100579290"
---
# <a name="what-is-azure-vmware-solution"></a>Wat is Azure VMware Solution?

Azure VMware Solution voorziet u van privéclouds die vSphere-clusters bevatten. Deze clusters zijn gebouwd op basis van een toegewezen bare-metal Azure-infrastructuur. Bij de initiële implementatie zijn er minimaal drie hosts. Extra hosts kunnen één voor één worden toegevoegd, tot een maximum van 16 hosts per cluster.  Alle ingerichte privéclouds beschikken over vCenter Server, vSAN, vSphere en NSX-T. U kunt workloads migreren vanuit uw on-premises omgevingen, nieuwe virtuele machines (VM's) implementeren en Azure-services gebruiken vanuit uw privéclouds.

Azure VMware Solution is een VMware-gevalideerde oplossing met voortdurende validatie van en testen op verbeteringen en upgrades. Microsoft beheert en onderhoudt de infrastructuur en software voor privéclouds. Zo kunt u zich richten op het ontwikkelen en uitvoeren van werkbelastingen in uw privéclouds. 

In het diagram wordt de verdeling weergegeven tussen privéclouds en VNets in Azure, Azure-services en on-premises omgevingen. Netwerktoegang van privéclouds naar Azure-services of VNets biedt SLA-gestuurde integratie van Azure-service-eindpunten. ExpressRoute Global Reach verbindt uw on-premises omgeving verbindt met uw Azure VMware Solution-privécloud. 

![Afbeelding van nabijheid van Azure VMware Solution-privécloud ten opzichte van Azure en on-premises](./media/adjacency-overview-drawing-final.png)

## <a name="hosts-clusters-and-private-clouds"></a>Hosts, clusters en privéclouds

Azure VMware Solution-privéclouds en -clusters zijn opgebouwd uit een bare-metal, hypergeconvergeerde Azure-infrastructuurhost. De high-end-hosts hebben 576 GB RAM en dubbele Intel 18-core, 2,3GHz-processors. De HE-hosts hebben twee vSAN-schijfgroepen met een raw vSAN-capaciteitslaag van 15,36 TB (SSD) en een vSAN-cachelaag van 3,2 TB (NVMe).

Nieuwe privéclouds worden geïmplementeerd via Azure Portal of Azure CLI.

## <a name="networking"></a>Netwerken

[!INCLUDE [avs-networking-description](includes/azure-vmware-solution-networking-description.md)]

Zie voor meer informatie [Concepten voor netwerken](concepts-networking.md).

## <a name="access-and-security"></a>Toegang en beveiliging

Azure VMware Solution-privéclouds maken gebruik van op vSphere-rollen gebaseerd toegangsbeheer voor verbeterde beveiliging. U kunt vSphere SSO LDAP-mogelijkheden integreren met Azure Active Directory. Zie voor meer informatie [Concepten voor toegang en identiteiten](concepts-identity.md).  

De encryptie 'vSAN data-at-rest' is standaard ingeschakeld en wordt gebruikt om vSAN-datastorebeveiliging te bieden. Zie voor meer informatie [Concepten voor opslag](concepts-storage.md).

## <a name="host-and-software-lifecycle-maintenance"></a>Onderhoud van de levenscyclus van host en software

Regelmatige upgrades van de Azure VMware Solution-privécloud en VMware-software zorgen ervoor dat de nieuwste beveiligings-, stabiliteits- en functiesets worden uitgevoerd in uw privéclouds. Zie voor meer informatie [Updates en upgrades voor privéclouds](concepts-upgrades.md).

## <a name="monitoring-your-private-cloud"></a>De privécloud bewaken

Zodra de Azure VMware Solution is geïmplementeerd in uw abonnement, worden [Azure Monitor-logboeken](../azure-monitor/overview.md) automatisch gegenereerd. 

In uw privécloud kunt u:
- Logboeken op al uw VM's verzamelen.
- [De MMA-agent downloaden en installeren](../azure-monitor/agents/log-analytics-agent.md#installation-options) op Linux- en Windows-VM's.
- De [Azure Diagnostics-extensie](../azure-monitor/agents/diagnostics-extension-overview.md) inschakelen.
- [Nieuwe query's maken en uitvoeren](../azure-monitor/logs/data-platform-logs.md#log-queries).
- Dezelfde query's uitvoeren die doorgaans worden uitgevoerd op uw VM's.

Bewaken dat de patronen in de Azure VMware Solution vergelijkbaar zijn met Azure VM's binnen het IaaS-platform. Zie voor meer informatie en uitleg [VM's van Azure bewaken met Azure Monitor](../azure-monitor/vm/monitor-vm-azure.md).

## <a name="next-steps"></a>Volgende stappen

De volgende stap is het leren van de belangrijkste [privécloud- en clusterconcepten](concepts-private-clouds-clusters.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-private-clouds-clusters]: ./concepts-private-clouds-clusters.md

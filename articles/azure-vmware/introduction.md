---
title: Inleiding
description: Meer informatie over de functies en voordelen van Azure VMware Solution voor het implementeren en beheren van VMware-workloads in Azure.
ms.topic: overview
ms.date: 05/04/2020
ms.openlocfilehash: 1875f535a5ca3e346362409c143da82cb8c07a12
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94488584"
---
# <a name="what-is-azure-vmware-solution"></a>Wat is Azure VMware Solution?

Met Azure VMware Solution beschikt u over privéclouds in Azure. De privécloud bevat vSphere-clusters, gebouwd op basis van een toegewezen bare-metal Azure-infrastructuur. U kunt privécloudclusters schalen van 3 naar 16 hosts, met de mogelijkheid om meerdere clusters in één privécloud te hebben. Alle privéclouds zijn ingericht met vCenter Server, vSAN, vSphere en NSX-T. U kunt workloads migreren uit uw on-premises omgevingen, nieuwe virtuele machines maken of implementeren en Azure-services gebruiken vanuit uw privéclouds.

Azure VMware Solution is een VMware-gevalideerde oplossing met voortdurende validatie van en testen op verbeteringen en upgrades. De infrastructuur en software van de privécloud worden beheerd en onderhouden door Microsoft, zodat u zich kunt richten op het ontwikkelen en uitvoeren van workloads in uw privéclouds.

In het volgende diagram wordt de verdeling weergegeven tussen privéclouds en VNets in Azure, Azure-services en on-premises omgevingen. Netwerktoegang van privéclouds naar Azure-services of VNets biedt SLA-gestuurde integratie van Azure-service-eindpunten. Privécloud-toegang vanuit on-premises omgevingen maakt gebruik van ExpressRoute Global Reach voor een privé- en beveiligde verbinding.

![Afbeelding van nabijheid van Azure VMware Solution-privécloud ten opzichte van Azure en on-premises](./media/adjacency-overview-drawing-final.png)

## <a name="hosts-clusters-and-private-clouds"></a>Hosts, clusters en privéclouds

Azure VMware Solution-privéclouds en -clusters zijn opgebouwd uit een bare-metal, hypergeconvergeerde Azure-infrastructuurhost. De high-end-hosts hebben 576 GB RAM en dubbele Intel 18-core, 2,3GHz-processors. De HE hosts hebben twee vSAN-schijfgroepen met een totale raw vSAN-capaciteitslaag van 15,36 TB (SSD) en een cachelaag van 3,2 TB (NVMe).

Nieuwe privéclouds worden geïmplementeerd via Azure Portal of Azure CLI.

## <a name="networking"></a>Netwerken

[!INCLUDE [avs-networking-description](includes/azure-vmware-solution-networking-description.md)]

Voor meer informatie over netwerken en interconnectiviteit raadpleegt u het artikel [Networking-concepten](concepts-networking.md).

## <a name="access-and-security"></a>Toegang en beveiliging

Voor verbeterde beveiliging maken Azure VMware Solution-privéclouds gebruik van op vSphere-rollen gebaseerd toegangsbeheer. vSphere SSO LDAP-mogelijkheden kunnen worden geïntegreerd met Azure Active Directory. Zie het artikel [Toegang en identiteitsconcepten](concepts-identity.md) voor meer informatie over identiteit en bevoegdheden.

vSAN data-at-rest encryptie is standaard ingeschakeld en wordt gebruikt om vSAN-datastorebeveiliging te bieden. Dit wordt gedetailleerd beschreven in het artikel [Opslagconcepten](concepts-storage.md).

## <a name="host-and-software-lifecycle-maintenance"></a>Onderhoud van de levenscyclus van host en software

Regelmatige upgrades van de Azure VMware Solution-privécloud en VMware-software zorgen ervoor dat de nieuwste beveiligings-, stabiliteits- en functiesets worden uitgevoerd in uw privéclouds. Meer informatie over platformonderhoud en -upgrades vindt u in het artikel [Upgradeconcepten](concepts-upgrades.md).

## <a name="monitoring-your-private-cloud"></a>De privécloud bewaken

Zodra de Azure VMware Solution is geïmplementeerd in uw abonnement, worden [Azure Monitor-logboeken](../azure-monitor/overview.md) automatisch gegenereerd. Daarnaast kunt u logboeken verzamelen op elke virtuele machine in uw privécloud. U kunt [de MMA-agent downloaden en installeren](../azure-monitor/platform/log-analytics-agent.md#installation-options) op virtuele machines met Linux en Windows die worden uitgevoerd in uw Azure VMware Solution-privéclouds. U kunt ook de [Azure Diagnostics-extensie](../azure-monitor/platform/diagnostics-extension-overview.md) inschakelen. U kunt zelfs dezelfde query's uitvoeren die normaal gesproken op uw virtuele machines worden uitgevoerd. Zie [Overzicht Azure Monitor-logboeken](../azure-monitor/platform/data-platform-logs.md) voor meer informatie over het maken van query's. Het bewaken van patronen in de Azure VMware Solution is vergelijkbaar met Azure Virtual Machines binnen het IaaS-platform. Zie [Virtuele machines van Azure bewaken met Azure Monitor](../azure-monitor/insights/monitor-vm-azure.md) voor meer informatie en uitleg.

## <a name="next-steps"></a>Volgende stappen

De volgende stap is het leren van de belangrijkste [privécloud- en clusterconcepten](concepts-private-clouds-clusters.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-private-clouds-clusters]: ./concepts-private-clouds-clusters.md

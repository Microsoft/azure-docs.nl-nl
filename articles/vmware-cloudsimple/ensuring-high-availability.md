---
title: Hoge Beschik baarheid van toepassingen garanderen bij uitvoering in VMware op Azure
description: Beschrijft CloudSimple-functies voor hoge Beschik baarheid om veelvoorkomende scenario's voor toepassings fouten te verhelpen voor toepassingen die worden uitgevoerd in een CloudSimple-Privécloud
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8bb1f8bb2aaeab88e5a9ea19534c8983af8c1626
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97895747"
---
# <a name="ensure-application-high-availability-when-running-in-vmware-on-azure"></a>Hoge Beschik baarheid van toepassingen garanderen bij uitvoering in VMware op Azure

De CloudSimple-oplossing biedt hoge Beschik baarheid voor uw toepassingen die in VMware worden uitgevoerd in de Azure-omgeving. De volgende tabel geeft een overzicht van fout scenario's en de bijbehorende functies voor hoge Beschik baarheid.

| Fout scenario | Toepassing beveiligd? | Functie platform HA | VMware HA-functie | Functie Azure HA |
------------ | ------------- | ------------ | ------------ | ------------- |
| Schijf fout | JA | Snelle vervanging van een uitgevallen knoop punt | [Over het vSAN-standaard beleid voor opslag](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-C228168F-6807-4C2A-9D74-E584CAF49A2A.html) |
| Fout met ventilator | JA | Redundante ventilatoren, snelle vervanging van een mislukt knoop punt |  |  |
| NIC-fout | JA | Redundante NIC, snelle vervanging van een mislukt knoop punt
| Stroom storing host | JA | Redundante voeding |  |  |
| ESXi-host-fout | JA | snelle vervanging van een uitgevallen knoop punt | [VMware vSphere hoge Beschik baarheid](https://www.vmware.com/products/vsphere/high-availability.html) |  |  |
| VM-fout | JA | [Load balancers](load-balancers.md)  | [VMware vSphere hoge Beschik baarheid](https://www.vmware.com/products/vsphere/high-availability.html) | Azure Load Balancer voor stateless VMware-Vm's |
| Fout in knooppunt switch poort | JA | Redundante NIC |  |  |
| Knooppunt switch is mislukt | JA | Redundante Blade Switches |  |  |
| Rack fout | JA | Plaatsingsgroepen |  |  |
| Netwerk verbinding met on-premises DC | JA  | Redundante netwerk services |  | Redundante-circuits |
| Netwerk verbinding met Azure | JA | |  | Redundante-circuits |
| Fout in Data Center | JA |  |  | Beschikbaarheidszones |
| Regionale fout | JA  |  |  | Azure-regio's |

Azure VMware-oplossing per CloudSimple biedt de volgende functies voor hoge Beschik baarheid.

## <a name="fast-replacement-of-failed-node"></a>Snelle vervanging van een uitgevallen knoop punt

De CloudSimple-besturings vlak software bewaakt voortdurend de status van VMware-clusters en detecteert wanneer een ESXi-knoop punt mislukt. Vervolgens wordt er automatisch een nieuwe ESXi-host aan het betrokken VMware-cluster toegevoegd vanuit de groep van de gemakkelijk beschik bare knoop punten en neemt het knoop punt uit het cluster niet uit. Deze functionaliteit zorgt ervoor dat de reserve capaciteit in het VMware-cluster snel wordt hersteld, zodat de tolerantie van het cluster die wordt verschaft door vSAN en VMware HA, wordt hersteld.

## <a name="placement-groups"></a>Plaatsingsgroepen

Een gebruiker die een Privécloud maakt, kan een Azure-regio en een plaatsings groep in de geselecteerde regio selecteren. Een plaatsings groep is een verzameling knoop punten verdeeld over meerdere racks, maar binnen hetzelfde spin-netwerk segment. Knoop punten binnen dezelfde plaatsings groep kunnen elkaar bereiken met Maxi maal twee extra switch-hops. Een plaatsings groep is altijd binnen één Azure-beschikbaarheids zone en omvat meerdere racks. Het CloudSimple-besturings vlak distribueert knoop punten van een Privécloud in meerdere racks op basis van de beste inspanningen. Knoop punten in verschillende plaatsings groepen worden gegarandeerd in verschillende racks geplaatst.

## <a name="availability-zones"></a>Beschikbaarheidszones

Beschikbaarheids zones zijn een aanbieding met hoge Beschik baarheid die uw toepassingen en gegevens beveiligt tegen Data Center-fouten. Beschikbaarheids zones zijn speciale fysieke locaties binnen een Azure-regio. Elke zone bestaat uit een of meer datacenters die zijn voorzien van een onafhankelijke stroomvoorziening, koeling en netwerken. Elke regio heeft één beschikbaarheids zone. Zie [Wat zijn Beschikbaarheidszones in azure?](../availability-zones/az-overview.md)voor meer informatie.

## <a name="redundant-azure-expressroute-circuits"></a>Redundante Azure ExpressRoute-circuits

Data Center-connectiviteit met Azure vNet met behulp van ExpressRoute heeft redundante circuits om een netwerk connectiviteit met hoge Beschik baarheid te bieden.

## <a name="redundant-networking-services"></a>Redundante netwerk services

Alle CloudSimple-netwerk services voor de Privécloud (inclusief VLAN, firewall, open bare IP-adressen, Internet en VPN) zijn zo ontworpen dat ze Maxi maal beschikbaar zijn en de service-SLA kunnen ondersteunen.

## <a name="azure-layer-7-load-balancer-for-stateless-vmware-vms"></a>Azure Layer 7 Load Balancer voor stateless VMware-Vm's

Gebruikers kunnen een Azure Layer 7-Load Balancer voor de staatloze Vm's die worden uitgevoerd in de VMware-omgeving, plaatsen om hoge Beschik baarheid voor de weblaag te krijgen.

## <a name="azure-regions"></a>Azure-regio's

Een Azure-regio is een set data centers die zijn geïmplementeerd binnen een latentie definitie en verbonden zijn via een toegewezen regionaal netwerk met lage latentie. Zie [Azure-regio's](https://azure.microsoft.com/global-infrastructure/regions)voor meer informatie.

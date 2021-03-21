---
title: Aanbevolen procedures voor Azure Service Fabric-netwerken
description: Regels en ontwerp overwegingen voor het beheer van netwerk verbindingen met behulp van Azure Service Fabric.
author: chrpap
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: chrpap
ms.openlocfilehash: caba864e77822ccab649f694df7e63e0ee5d6e51
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101732561"
---
# <a name="networking"></a>Netwerken

Wanneer u Azure Service Fabric-clusters maakt en beheert, biedt u netwerk connectiviteit voor uw knoop punten en toepassingen. De netwerk bronnen omvatten IP-adresbereiken, virtuele netwerken, load balancers en netwerk beveiligings groepen. In dit artikel vindt u aanbevolen procedures voor deze resources.

Bekijk Azure [service Fabric-netwerk patronen](./service-fabric-patterns-networking.md) voor meer informatie over het maken van clusters die gebruikmaken van de volgende functies: bestaand virtueel netwerk of subnet, statisch openbaar IP-adres, alleen intern Load Balancer of interne en externe Load Balancer.

## <a name="infrastructure-networking"></a>Infrastructuur netwerken
Maximaliseer de prestaties van uw virtuele machine met versneld netwerken door de eigenschap *enableAcceleratedNetworking* in uw Resource Manager-sjabloon te declareren, het volgende code fragment is een Networkinterfaceconfigurations schaalset voor virtuele-machine schaal sets waarmee versnelde netwerken kunnen worden ingeschakeld:

```json
"networkInterfaceConfigurations": [
  {
    "name": "[concat(variables('nicName'), '-0')]",
    "properties": {
      "enableAcceleratedNetworking": true,
      "ipConfigurations": [
        {
        <snip>
        }
      ],
      "primary": true
    }
  }
]
```
Service Fabric cluster kan worden ingericht op [Linux met versneld netwerken](../virtual-network/create-vm-accelerated-networking-cli.md)en [Windows met versneld netwerken](../virtual-network/create-vm-accelerated-networking-powershell.md).

Versnelde netwerken worden ondersteund voor virtuele machines uit de Azure-machine serie: D/DSv2, D/DSv3, E/ESv3, F/FS, FSv2 en MS/MMS. Versneld netwerken zijn getest met behulp van de Standard_DS8_v3 SKU op 01/23/2019 voor een Service Fabric Windows-cluster en gebruiken Standard_DS12_v2 op 01/29/2019 voor een Service Fabric Linux-cluster. Houd er rekening mee dat versneld netwerken ten minste 4 Vcpu's nodig hebben. 

Als u versneld netwerken op een bestaand Service Fabric cluster wilt inschakelen, moet u eerst [een service Fabric cluster uitschalen door een schaalset voor virtuele machines toe te voegen](./virtual-machine-scale-set-scale-node-type-scale-out.md), zodat u het volgende kunt doen:
1. Een NodeType inrichten met versneld netwerken ingeschakeld
2. Migreer uw services en hun status naar het ingerichte NodeType met versneld netwerken ingeschakeld

Er is een uitbrei ding van de infra structuur vereist om versnelde netwerken in te scha kelen op een bestaand cluster, omdat het inschakelen van versneld netwerken op locatie downtime zou kunnen veroorzaken, omdat alle virtuele machines in een beschikbaarheidsset moeten worden gestopt en de toewijzing ongedaan wordt gemaakt [voordat er versnelde netwerken op een bestaande NIC worden ingeschakeld](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

## <a name="cluster-networking"></a>Clusternetwerken

* Service Fabric clusters kunnen worden geïmplementeerd in een bestaand virtueel netwerk door de stappen te volgen die worden beschreven in [service Fabric-netwerk patronen](./service-fabric-patterns-networking.md).

* Netwerk beveiligings groepen (Nsg's) worden aanbevolen voor knooppunt typen om inkomend en uitgaand verkeer te beperken tot hun cluster. Zorg ervoor dat de benodigde poorten zijn geopend in de NSG. 

* Het primaire knooppunt type, dat de Service Fabric systeem services bevat, hoeft niet te worden weer gegeven via de externe load balancer en kan worden weer gegeven door een [interne Load Balancer](./service-fabric-patterns-networking.md#internal-only-load-balancer)

* Gebruik een [statisch openbaar IP-adres](./service-fabric-patterns-networking.md#static-public-ip-address-1) voor uw cluster.

## <a name="network-security-rules"></a>Netwerk beveiligings regels

De basis regels hier zijn het minimum voor een beveiligings vergrendeling van een door Azure beheerd Service Fabric cluster. Het openen van de volgende poorten of het goed keuren van de IP/URL verhindert de juiste werking van het cluster en wordt mogelijk niet ondersteund. Als deze regel is ingesteld, is deze strikt vereist voor het gebruik van [automatische upgrades van besturings systeem installatie kopieën](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md), anders moeten extra poorten worden geopend.

### <a name="inbound"></a>Inkomend 
|Prioriteit   |Name               |Poort        |Protocol  |Bron             |Doel       |Bewerking   
|---        |---                |---         |---       |---                |---               |---
|3900       |Azure              |19080       |TCP       |Internet           |VirtualNetwork    |Toestaan
|3910       |Client             |19000       |TCP       |Internet           |VirtualNetwork    |Toestaan
|3920       |Cluster            |1025-1027   |TCP       |VirtualNetwork     |VirtualNetwork    |Toestaan
|3930       |Kortstondige          |49152-65534 |TCP       |VirtualNetwork     |VirtualNetwork    |Toestaan
|3940       |Toepassing        |20000-30000 |TCP       |VirtualNetwork     |VirtualNetwork    |Toestaan
|3950       |SMB                |445         |TCP       |VirtualNetwork     |VirtualNetwork    |Toestaan
|3960       |RDP                |3389-3488   |TCP       |Internet           |VirtualNetwork    |Weigeren
|3970       |SSH                |22          |TCP       |Internet           |VirtualNetwork    |Weigeren
|3980       |Aangepast eind punt    |80          |TCP       |Internet           |VirtualNetwork    |Toestaan
|4100       |Blok keren binnenkomend      |443         |Alle       |Alle                |Alle               |Toestaan

Meer informatie over de regels voor binnenkomende beveiliging:

* **Azure**. Deze poort wordt gebruikt door Service Fabric Explorer om door uw cluster te bladeren en te beheren, en wordt ook gebruikt door de resource provider van Service Fabric om informatie over uw cluster op te vragen om weer te geven in de Azure Beheerportal. Als deze poort niet toegankelijk is vanuit de Service Fabric resource provider, ziet u een bericht zoals ' knoop punten niet gevonden ' of ' UpgradeServiceNotReachable ' in de Azure Portal en uw knoop punt en toepassings lijst worden leeg weer gegeven. Dit betekent dat als u de zicht baarheid van uw cluster in azure Beheerportal wilt zien, uw load balancer een openbaar IP-adres moet weer geven en dat uw NSG binnenkomend 19080-verkeer moet toestaan.  

* - **Client**. Het eind punt van de client verbinding voor Api's zoals REST/Power shell/CLI. 

* **Cluster**. Gebruikt voor communicatie tussen knoop punten; mag nooit worden geblokkeerd.

* **Tijdelijke**. Service Fabric gebruikt een deel van deze poorten als toepassings poorten en het resterende onderdeel is beschikbaar voor het besturings systeem. Dit bereik wordt ook toegewezen aan het bestaande bereik in het besturings systeem, dus voor alle doel einden kunt u de bereiken in het voor beeld gebruiken. Zorg ervoor dat het verschil tussen de begin-en eind poort ten minste 255 is. U kunt conflicten ondertreden als dit verschil te laag is, omdat dit bereik wordt gedeeld met het besturings systeem. Als u het geconfigureerde dynamische poort bereik wilt zien, voert u *netsh int IPv4 dynamische poort TCP weer geven* uit. Deze poorten zijn niet nodig voor Linux-clusters.

* **Toepassing**. Het bereik van de toepassings poort moet groot genoeg zijn om de eindpunt vereiste van uw toepassingen te kunnen voorzien. Dit bereik moet exclusief zijn van het dynamische poort bereik op de computer, dat wil zeggen, het ephemeralPorts-bereik dat is ingesteld in de configuratie. Service Fabric maakt gebruik van deze poorten wanneer er nieuwe poorten zijn vereist en zorgt ervoor dat de firewall voor deze poorten op de knoop punten wordt geopend.

* **SMB**. Het SMB-protocol wordt gebruikt door de installatie kopie opslag-service voor twee scenario's. Deze poort is nodig om de pakketten te downloaden van de installatie kopie opslag door de knoop punten en om deze te repliceren tussen de replica's. 

* **RDP**. Optioneel, als RDP is vereist van Internet of VirtualNetwork voor JumpBox-scenario's. 

* **SSH**. Optioneel, als SSH vereist is op internet of VirtualNetwork voor JumpBox-scenario's.

* **Aangepast eind punt**. Een voor beeld van uw toepassing om een Internet toegankelijk eind punt in te scha kelen.

### <a name="outbound"></a>Uitgaand

|Prioriteit   |Name               |Poort        |Protocol  |Bron             |Doel       |Bewerking   
|---        |---                |---         |---       |---                |---               |---
|3900       |Netwerk            |Alle         |TCP       |VirtualNetwork     |VirtualNetwork    |Toestaan
|3910       |Resourceprovider  |443         |TCP       |VirtualNetwork     |ServiceFabric     |Toestaan
|3920       |Upgrade uitvoeren            |443         |TCP       |VirtualNetwork     |Internet          |Toestaan
|3950       |Blok keren van uitgaand     |Alle         |Alle       |Alle                |Alle               |Weigeren

Meer informatie over de regels voor uitgaande beveiliging:

* **Netwerk**. Communicatie kanaal voor subnetten en andere virtuele netwerken.

* **Resource provider**. Verbinding door de UpgradeService om alle ARM-implementaties uit te voeren door de resource provider van Service Fabric.

* **Upgrade**. De upgrade-service gebruikt het adres download.microsoft.com om de bits op te halen, maar dit is nodig voor installatie, herstel van installatie kopieën en runtime-upgrades. De service werkt met dynamische IP-adressen. In het scenario van een ' alleen interne ' load balancer moet er een extra externe load balancer aan de sjabloon worden toegevoegd met een regel voor uitgaand verkeer voor poort 443. Deze poort kan optioneel worden geblokkeerd na een geslaagde installatie, maar in dit geval moet het upgrade pakket worden gedistribueerd naar de knoop punten of kan de poort gedurende korte tijd worden geopend, waarna een hand matige upgrade nodig is.

Gebruik Azure Firewall met [NSG flow log](../network-watcher/network-watcher-nsg-flow-logging-overview.md) en [Traffic Analytics](../network-watcher/traffic-analytics.md) om problemen met de vergren deling van de beveiliging op te sporen. De ARM-sjabloon [service fabric met NSG](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) is een goed voor beeld om te starten. 


## <a name="application-networking"></a>Toepassings netwerken

* Als u Windows-container werkbelastingen wilt uitvoeren, gebruikt u de [open-netwerk modus](./service-fabric-networking-modes.md#set-up-open-networking-mode) om service-to-service-communicatie eenvoudiger te maken.

* Gebruik een omgekeerde proxy zoals [Traefik](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/) of de [service Fabric reverse proxy](./service-fabric-reverseproxy.md) om algemene toepassings poorten weer te geven, zoals 80 of 443.

* Voor Windows-containers die worden gehost op gapped machines die geen basis lagen kunnen halen uit Azure-Cloud opslag, kunt u het gedrag van de afwijkende laag overschrijven met behulp van de vlag [--Allow-niet-distribueer bare artefacten](/virtualization/windowscontainers/about/faq#how-do-i-make-my-container-images-available-on-air-gapped-machines) in de docker-daemon.

## <a name="next-steps"></a>Volgende stappen

* Een cluster maken op Vm's of computers met Windows Server: [service Fabric cluster maken voor Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Een cluster maken op Vm's of computers met Linux: [een Linux-cluster maken](service-fabric-cluster-creation-via-portal.md)
* Meer informatie over [ondersteuningsopties voor Service Fabric](service-fabric-support.md)

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png

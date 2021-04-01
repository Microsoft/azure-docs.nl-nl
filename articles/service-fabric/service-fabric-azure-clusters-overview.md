---
title: Clusters maken op Windows Server en Linux
description: Service Fabric-clusters worden uitgevoerd op Windows Server en Linux. U kunt Service Fabric-toepassingen implementeren en hosten overal waar u Windows Server of Linux kunt uitvoeren.
services: service-fabric
documentationcenter: .net
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: bbfdc0a30aa673e8602ec9233fde4236c99ef5aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97882208"
---
# <a name="overview-of-service-fabric-clusters-on-azure"></a>Overzicht van Service Fabric clusters op Azure
Een Service Fabric-cluster is een met het netwerk verbonden reeks virtuele of fysieke machines waarop uw microservices worden geïmplementeerd en beheerd. Een computer of virtuele machine die deel uitmaakt van een cluster, wordt een cluster knooppunt genoemd. Clusters kunnen worden geschaald naar duizenden knoop punten. Als u nieuwe knoop punten aan het cluster toevoegt, worden in Service Fabric de service partitie replica's en instanties over het verhoogde aantal knoop punten gebalanceerd. De algehele prestaties van toepassingen verbeteren en conflicten voor toegang tot het geheugen neemt af. Als de knoop punten in het cluster niet efficiënt worden gebruikt, kunt u het aantal knoop punten in het cluster verlagen. Service Fabric opnieuw, worden de partitie replica's en instanties over het aantal knoop punten verkleind om beter gebruik te maken van de hardware op elk knoop punt.

Een knooppunt type definieert de grootte, het aantal en de eigenschappen van een set knoop punten (virtuele machines) in het cluster. Elk knooppunttype kan dan onafhankelijk omhoog of omlaag worden geschaald, verschillende open poorten bevatten en diverse capaciteitsstatistieken hebben. Knooppunttypen worden gebruikt voor het definiëren van rollen voor een set clusterknooppunten, zoals 'front-end' of 'back-end'. Uw cluster kan meer dan één knooppunttype hebben, maar voor productieclusters moet het primaire knooppunttype ten minste vijf VM's bevatten (of ten minste drie VM's voor testclusters). [Service Fabric-systeemservices](service-fabric-technical-overview.md#system-services) worden op de knooppunten van het primaire knooppunttype geplaatst. 

## <a name="cluster-components-and-resources"></a>Cluster onderdelen en bronnen
Een Service Fabric cluster op Azure is een Azure-resource die gebruikmaakt van en samenwerkt met andere Azure-resources:
* Vm's en virtuele netwerk kaarten
* virtuele-machineschaalsets
* virtuele netwerken
* load balancers
* opslag accounts
* open bare IP-adressen

![Service Fabric cluster][Image]

### <a name="virtual-machine"></a>Virtuele machine
Een [virtuele machine](../virtual-machines/index.yml) die deel uitmaakt van een cluster, wordt een knoop punt genoemd, maar een cluster knooppunt is een service Fabric runtime proces. Aan elk knooppunt wordt een knooppuntnaam toegewezen (een tekenreeks). Knoop punten hebben kenmerken, zoals [plaatsings eigenschappen](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints). Elke machine of VM heeft een service die automatisch wordt gestart, *FabricHost.exe*, die wordt gestart op het moment dat de computer wordt opgestart en twee uitvoer bare bestanden, *Fabric.exe* en *FabricGateway.exe* worden gestart, waarmee het knoop punt wordt gemaakt. Een productie-implementatie is één knoop punt per fysieke of virtuele machine. Voor het testen van scenario's kunt u meerdere knoop punten op één computer of virtuele machine hosten door meerdere exemplaren van *Fabric.exe* en *FabricGateway.exe* uit te voeren.

Elke VM is gekoppeld aan een virtuele netwerk interface kaart (NIC) en aan elke NIC wordt een privé-IP-adres toegewezen.  Een virtuele machine wordt toegewezen aan een virtueel netwerk en een lokale Balancer via de NIC.

Alle Vm's in een cluster worden in een virtueel netwerk geplaatst.  Alle knoop punten in dezelfde knooppunt type/schaalset worden op hetzelfde subnet in het virtuele netwerk geplaatst.  Deze knoop punten hebben alleen particuliere IP-adressen en zijn niet direct gericht buiten het virtuele netwerk.  Clients hebben toegang tot services op de knoop punten via de Azure-load balancer.

### <a name="scale-setnode-type"></a>Schaalset/knooppunt type
Wanneer u een cluster maakt, definieert u een of meer knooppunt typen.  De knoop punten, of Vm's, in een knooppunt type hebben dezelfde grootte en kenmerken, zoals het aantal Cpu's, het geheugen, het aantal schijven en de schijf-I/O.  U kunt bijvoorbeeld één type knoop punt hebben voor kleine, front-end Vm's met poorten die op internet zijn geopend, terwijl een ander type knoop punt voor grote, back-end Vm's is die gegevens verwerken. In azure-clusters wordt elk knooppunt type toegewezen aan een [schaalset voor virtuele machines](../virtual-machine-scale-sets/index.yml).

U kunt schaal sets gebruiken voor het implementeren en beheren van een verzameling virtuele machines als een set. Elk knooppunt type dat u in een Azure Service Fabric-cluster definieert, stelt een afzonderlijke schaalset in. De Service Fabric runtime wordt naar elke virtuele machine in de schaalset getrapt met behulp van Azure VM-extensies. U kunt elk knooppunt type omhoog of omlaag schalen, de SKU van het besturings systeem wijzigen dat op elk cluster knooppunt wordt uitgevoerd, verschillende sets poorten openen en verschillende capaciteits metrieken gebruiken. Een schaalset heeft vijf [upgrade domeinen](service-fabric-cluster-resource-manager-cluster-description.md#upgrade-domains) en vijf [fout domeinen](service-fabric-cluster-resource-manager-cluster-description.md#fault-domains) en kan Maxi maal 100 vm's hebben.  U maakt clusters van meer dan 100 knoop punten door meerdere schaal sets/knooppunt typen te maken.

> [!IMPORTANT]
> Het kiezen van het aantal knooppunt typen voor uw cluster en de eigenschappen van elk type knoop punt (grootte, primair, Internet Facing, aantal Vm's, enzovoort) is een belang rijke taak.  Lees voor meer informatie overwegingen voor het [plannen van cluster capaciteit](service-fabric-cluster-capacity.md).

Lees [service Fabric knooppunt typen en schaal sets voor virtuele machines](service-fabric-cluster-nodetypes.md)voor meer informatie.

### <a name="azure-load-balancer"></a>Azure Load Balancer
VM-exemplaren worden toegevoegd achter een [Azure-Load Balancer](../load-balancer/load-balancer-overview.md), die is gekoppeld aan een [openbaar IP-adres](../virtual-network/public-ip-addresses.md) en een DNS-label.  Bij het inrichten van een cluster met *&lt; clustername &gt;*, de DNS-naam, *&lt; clustername &gt; . &lt; Location &gt; . cloudapp.Azure.com* is het DNS-label dat is gekoppeld aan de Load Balancer vóór de schaalset.

Vm's in een cluster hebben alleen [privé-IP-adressen](../virtual-network/private-ip-addresses.md).  Verkeer van beheer en service verkeer wordt gerouteerd via de open bare load balancer.  Netwerk verkeer wordt doorgestuurd naar deze machines via NAT-regels (clients maken verbinding met specifieke knoop punten/instanties) of taakverdelings regels (verkeer gaat naar Vm's round robin).  Een load balancer heeft een openbaar IP-adres dat is gekoppeld aan een DNS-naam in de indeling: *&lt; clustername &gt; . &lt; Location &gt; . cloudapp.Azure.com*.  Een openbaar IP-adres is een andere Azure-resource in de resource groep.  Als u meerdere knooppunt typen in een cluster definieert, wordt er een load balancer gemaakt voor elk type knoop punt/schaalset. U kunt ook één load balancer instellen voor meerdere knooppunt typen.  Het primaire knooppunt type heeft de DNS-label *&lt; cluster naam &gt; . &lt; Location &gt; . cloudapp.Azure.com*, andere knooppunt typen hebben het DNS-label *&lt; cluster naam &gt; - &lt; NodeType &gt; . &lt; Location &gt; . cloudapp.Azure.com*.

### <a name="storage-accounts"></a>Opslagaccounts
Elk type cluster knooppunt wordt ondersteund door een [Azure-opslag account](../storage/common/storage-introduction.md) en beheerde schijven.

## <a name="cluster-security"></a>Clusterbeveiliging
Een Service Fabric cluster is een bron waarvan u eigenaar bent.  Het is uw verantwoordelijkheid om uw clusters te beveiligen om te voor komen dat onbevoegde gebruikers verbinding kunnen maken. Een beveiligd cluster is vooral belang rijk wanneer u werk belastingen voor productie op het cluster uitvoert. 

### <a name="node-to-node-security"></a>Beveiliging van knoop punt naar knoop punt
Beveiliging van knoop punt naar knoop punt beveiligt de communicatie tussen de Vm's of computers in een cluster. Dit beveiligings scenario zorgt ervoor dat alleen computers die gemachtigd zijn om deel te nemen aan het cluster, kunnen deel nemen aan het hosten van toepassingen en services in het cluster. Service Fabric maakt gebruik van X. 509-certificaten voor het beveiligen van een cluster en het bieden van beveiligings functies van toepassingen.  Een cluster certificaat is vereist voor het beveiligen van het cluster verkeer en het bieden van cluster-en server authenticatie.  Zelfondertekende certificaten kunnen worden gebruikt voor test clusters, maar een certificaat van een vertrouwde certificerings instantie moet worden gebruikt voor het beveiligen van productie clusters.

Lees voor meer informatie [knoop punt-naar-knooppunt beveiliging](service-fabric-cluster-security.md#node-to-node-security)

### <a name="client-to-node-security"></a>Beveiliging van client naar knoop punt
Bij client-naar-knoop punt-beveiliging worden clients geverifieerd en wordt de communicatie tussen een client en afzonderlijke knoop punten in het cluster beveiligd. Met dit type beveiliging kunt u ervoor zorgen dat alleen geautoriseerde gebruikers toegang hebben tot het cluster en de toepassingen die op het cluster worden geïmplementeerd. Clients zijn uniek geïdentificeerd via de beveiligings referenties van het X. 509-certificaat. Een wille keurig aantal optionele client certificaten kan worden gebruikt om beheerders of gebruikers clients te verifiëren met het cluster.

Naast client certificaten kan Azure Active Directory ook worden geconfigureerd voor het verifiëren van clients met het cluster.

Lees voor meer informatie [client-naar-knoop punt beveiliging](service-fabric-cluster-security.md#client-to-node-security)

### <a name="role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer
Met Azure op rollen gebaseerd toegangs beheer (Azure RBAC) kunt u nauw keurige toegangs controles toewijzen aan Azure-resources.  U kunt verschillende toegangs regels toewijzen aan abonnementen, resource groepen en resources.  Azure RBAC-regels worden overgenomen in de resource hiërarchie, tenzij deze op een lager niveau worden overschreven.  U kunt elke gebruiker of elk gebruikers groep op uw AAD toewijzen met Azure RBAC-regels zodat aangewezen gebruikers en groepen uw cluster kunnen wijzigen.  Lees voor meer informatie het [overzicht van Azure RBAC](../role-based-access-control/overview.md).

Service Fabric biedt ook ondersteuning voor toegangs beheer om de toegang tot bepaalde cluster bewerkingen voor verschillende groepen gebruikers te beperken. Dit helpt het cluster beter te beveiligen. Twee typen toegangs beheer worden ondersteund voor clients die verbinding maken met een cluster: beheerdersrol en gebruikersrol.  

Lees [service Fabric op rollen gebaseerd toegangs beheer](service-fabric-cluster-security.md#service-fabric-role-based-access-control)voor meer informatie.

### <a name="network-security-groups"></a>Netwerkbeveiligingsgroepen 
Netwerk beveiligings groepen (Nsg's) bepalen binnenkomend en uitgaand verkeer van een subnet, VM of specifieke NIC.  Wanneer meerdere Vm's in hetzelfde virtuele netwerk worden geplaatst, kunnen ze standaard met elkaar communiceren via een wille keurige poort.  Als u de communicatie tussen de computers wilt beperken, kunt u Nsg's definiëren om het netwerk te segmenteren of Vm's van elkaar te isoleren.  Als u meerdere knooppunt typen in een cluster hebt, kunt u Nsg's Toep assen op subnetten om te voor komen dat computers die deel uitmaken van verschillende knooppunt typen, met elkaar communiceren.  

Lees voor meer informatie over [beveiligings groepen](../virtual-network/network-security-groups-overview.md)

## <a name="scaling"></a>Schalen

Toepassings vereisten veranderen in de loop van de tijd. Mogelijk moet u cluster bronnen verg Roten om te voldoen aan de toegenomen werk belasting van de toepassing of het netwerk verkeer of cluster bronnen verlagen wanneer de vraag wordt neergezet. Nadat u een Service Fabric cluster hebt gemaakt, kunt u het cluster horizon taal schalen (Wijzig het aantal knoop punten) of verticaal (Wijzig de resources van de knoop punten). U kunt de schaal van het cluster op elk gewenst moment aanpassen, zelfs als er workloads op het cluster worden uitgevoerd. Tijdens het schalen van het cluster worden uw toepassingen ook automatisch geschaald.

Lees voor meer informatie [Azure-clusters schalen](service-fabric-cluster-scaling.md).

## <a name="upgrading"></a>Upgrade uitvoeren
Een Azure Service Fabric-cluster is een resource waarvan u de eigenaar bent, maar wordt gedeeltelijk beheerd door micro soft. Micro soft is verantwoordelijk voor het patchen van het onderliggende besturings systeem en het uitvoeren van Service Fabric runtime-upgrades voor uw cluster. U kunt uw cluster zo instellen dat automatische runtime-upgrades worden ontvangen, wanneer micro soft een nieuwe versie uitgeeft of een door u gewenste ondersteunde runtime versie selecteert. Naast runtime-upgrades kunt u ook de cluster configuratie, zoals certificaten of toepassings poorten, bijwerken.

Lees [upgrades voor clusters](service-fabric-cluster-upgrade.md)voor meer informatie.

## <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen
U kunt clusters maken op virtuele machines waarop deze besturings systemen worden uitgevoerd:

| Besturingssysteem | Eerste ondersteunde Service Fabric versie | Laatst ondersteunde Service Fabric versie |
| --- | --- | --- | 
| Windows Server 2019 | 6.4.654.9590 | N.v.t. |
| Windows Server 2016 | Alle versies | N.v.t. |
| Windows Server-20H2 | 7.2.445.9590 | N.v.t. |
| Windows Server 1809 | 6.4.654.9590 | 7.2.445.9590 |
| Windows Server 1803 | 6.4 | 7.2.445.9590 |
| Windows Server 1709 | 6.0 | 7.2.445.9590 |
| Windows Server 2012 | Alle versies | N.v.t. | 
| Linux Ubuntu 16,04 | 6.0 | N.v.t. |
| Linux Ubuntu 18,04 | 7.1 | N.v.t. |

Zie [ondersteunde cluster versies in azure](./service-fabric-versions.md#supported-operating-systems) voor meer informatie.

> [!NOTE]
> Als u besluit Service Fabric te implementeren op Windows Server 1709, moet u er rekening mee houden dat (1) het geen langlopende onderhouds vertakking is, dus u moet mogelijk versies in de toekomst verplaatsen en (2) als u containers implementeert, zijn de containers die zijn gebouwd op Windows Server 2016 niet in Windows Server 1709 en vice versa (u moet ze opnieuw bouwen om deze te implementeren
>


## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [beveiligen](service-fabric-cluster-security.md), [schalen](service-fabric-cluster-scaling.md)en [upgraden](service-fabric-cluster-upgrade.md) van Azure-clusters.

Meer informatie over [service Fabric ondersteunings opties](service-fabric-support.md).

[Image]: media/service-fabric-azure-clusters-overview/Cluster.PNG
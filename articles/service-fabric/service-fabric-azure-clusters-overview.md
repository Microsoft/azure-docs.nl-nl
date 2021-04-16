---
title: Clusters maken op Windows Server en Linux
description: Service Fabric worden uitgevoerd op Windows Server en Linux. U kunt toepassingen implementeren en hosten Service Fabric waar u Windows Server of Linux kunt uitvoeren.
services: service-fabric
documentationcenter: .net
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 4aed4ab38db9f8d8b95647b6662245c93778afed
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520153"
---
# <a name="overview-of-service-fabric-clusters-on-azure"></a>Overzicht van Service Fabric clusters in Azure
Een Service Fabric-cluster is een met het netwerk verbonden reeks virtuele of fysieke machines waarop uw microservices worden geïmplementeerd en beheerd. Een machine of VM die deel uitmaakt van een cluster wordt een clusterknooppunt genoemd. Clusters kunnen worden geschaald naar duizenden knooppunten. Als u nieuwe knooppunten aan het cluster toevoegt, Service Fabric replica's en exemplaren van de servicepartitie opnieuw verdeeld over het toegenomen aantal knooppunten. De algehele prestaties van toepassingen verbeteren en de toegang tot het geheugen neemt af. Als de knooppunten in het cluster niet efficiënt worden gebruikt, kunt u het aantal knooppunten in het cluster verlagen. Service Fabric maakt de partitiereplica's en exemplaren opnieuw verdeeld over het afgenomen aantal knooppunten om beter gebruik te maken van de hardware op elk knooppunt.

Een knooppunttype definieert de grootte, het aantal en de eigenschappen voor een set knooppunten (virtuele machines) in het cluster. Elk knooppunttype kan dan onafhankelijk omhoog of omlaag worden geschaald, verschillende open poorten bevatten en diverse capaciteitsstatistieken hebben. Knooppunttypen worden gebruikt voor het definiëren van rollen voor een set clusterknooppunten, zoals 'front-end' of 'back-end'. Uw cluster kan meer dan één knooppunttype hebben, maar voor productieclusters moet het primaire knooppunttype ten minste vijf VM's bevatten (of ten minste drie VM's voor testclusters). [Service Fabric-systeemservices](service-fabric-technical-overview.md#system-services) worden op de knooppunten van het primaire knooppunttype geplaatst. 

## <a name="cluster-components-and-resources"></a>Clusteronderdelen en -resources
Een Service Fabric cluster in Azure is een Azure-resource die gebruikmaakt van en communiceert met andere Azure-resources:
* VM's en virtuele netwerkkaarten
* virtuele-machineschaalsets
* virtuele netwerken
* load balancers
* opslagaccounts
* openbare IP-adressen

![Service Fabric Cluster][Image]

### <a name="virtual-machine"></a>Virtuele machine
Een [virtuele machine](../virtual-machines/index.yml) die deel uitmaakt van een cluster wordt een knooppunt genoemd. Technisch gezien is een clusterknooppunt echter een Service Fabric runtimeproces. Aan elk knooppunt wordt een knooppuntnaam toegewezen (een tekenreeks). Knooppunten hebben kenmerken, zoals [plaatsingseigenschappen.](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints) Elke machine of VM heeft een service voor automatisch *starten,FabricHost.exe,* die wordt uitgevoerd tijdens het opstarten en vervolgens twee uitvoerbare bestanden start, *Fabric.exe* en *FabricGateway.exe,* die het knooppunt maken. Een productie-implementatie is één knooppunt per fysieke of virtuele machine. Voor testscenario's kunt u meerdere knooppunten hosten op één computer *of* VM door meerdere exemplaren vanFabric.exeen *FabricGateway.exe.*

Elke VM is gekoppeld aan een virtuele netwerkinterfacekaart (NIC) en aan elke NIC wordt een privé-IP-adres toegewezen.  Een virtuele machine wordt via de NIC toegewezen aan een virtueel netwerk en een lokale balancer.

Alle VM's in een cluster worden in een virtueel netwerk geplaatst.  Alle knooppunten in hetzelfde knooppunttype/dezelfde schaalset worden in hetzelfde subnet in het virtuele netwerk geplaatst.  Deze knooppunten hebben alleen privé-IP-adressen en zijn niet rechtstreeks adresseerbaar buiten het virtuele netwerk.  Clients hebben toegang tot services op de knooppunten via de Azure load balancer.

### <a name="scale-setnode-type"></a>Schaalset/knooppunttype
Wanneer u een cluster maakt, definieert u een of meer knooppunttypen.  De knooppunten, of VM's, in een knooppunttype hebben dezelfde grootte en kenmerken, zoals het aantal CPU's, het geheugen, het aantal schijven en de schijf-I/O.  Eén knooppunttype kan bijvoorbeeld zijn voor kleine front-end-VM's met poorten die zijn geopend voor internet, terwijl een ander knooppunttype kan zijn voor grote back-end-VM's die gegevens verwerken. In Azure-clusters wordt elk knooppunttype aan een [virtuele-machineschaalset.](../virtual-machine-scale-sets/index.yml)

U kunt schaalsets gebruiken om een verzameling virtuele machines als set te implementeren en beheren. Elk knooppunttype dat u definieert in een Azure Service Fabric stelt een afzonderlijke schaalset in. De Service Fabric runtime wordt op elke virtuele machine in de schaalset gebootstrapt met behulp van Azure VM-extensies. U kunt elk knooppunttype onafhankelijk omhoog of omlaag schalen, de SKU van het besturingssysteem wijzigen die op elk clusterknooppunt wordt uitgevoerd, verschillende sets poorten open hebben en verschillende metrische capaciteitsgegevens gebruiken. Een schaalset heeft vijf [upgradedomeinen](service-fabric-cluster-resource-manager-cluster-description.md#upgrade-domains) en vijf [foutdomeinen](service-fabric-cluster-resource-manager-cluster-description.md#fault-domains) en kan maximaal 100 VM's hebben.  U maakt clusters van meer dan 100 knooppunten door meerdere schaalsets/knooppunttypen te maken.

> [!IMPORTANT]
> Het kiezen van het aantal knooppunttypen voor uw cluster en de eigenschappen van elk knooppunttype (grootte, primair, internet gericht, aantal VM's, enzovoort) is een belangrijke taak.  Lees Overwegingen voor [clustercapaciteitsplanning voor meer informatie.](service-fabric-cluster-capacity.md)

Lees voor meer informatie Service Fabric [knooppunttypen en virtuele-machineschaalsets.](service-fabric-cluster-nodetypes.md)

### <a name="azure-load-balancer"></a>Azure Load Balancer
VM-exemplaren worden gekoppeld achter een [Azure load balancer](../load-balancer/load-balancer-overview.md), die is gekoppeld aan een [openbaar IP-adres](../virtual-network/public-ip-addresses.md) en DNS-label.  Wanneer u een cluster inrichten met *&lt; clusternaam &gt;*, de DNS-naam, *&lt; clusternaam &gt; . &lt; location &gt; .cloudapp.azure.com* is het DNS-label dat is gekoppeld aan de load balancer voor de schaalset.

VM's in een cluster hebben alleen [privé-IP-adressen.](../virtual-network/private-ip-addresses.md)  Beheerverkeer en serviceverkeer worden gerouteerd via de openbare load balancer.  Netwerkverkeer wordt naar deze machines gerouteerd via NAT-regels (clients maken verbinding met specifieke knooppunten/exemplaren) of taakverdelingsregels (verkeer gaat naar VM's round robin).  Een load balancer heeft een bijbehorend openbaar IP-adres met een DNS-naam in de indeling: *&lt; clusternaam &gt; . &lt; location &gt; .cloudapp.azure.com*.  Een openbaar IP-adres is een andere Azure-resource in de resourcegroep.  Als u meerdere knooppunttypen in een cluster definieert, wordt load balancer voor elk knooppunttype/elke schaalset gemaakt. U kunt ook één knooppunt instellen load balancer meerdere knooppunttypen.  Het primaire knooppunttype heeft de clusternaam van het *&lt; DNS-label &gt; . &lt; location &gt; .cloudapp.azure.com*, andere knooppunttypen hebben het knooppunttype clusternaam van het *&lt; &gt; - &lt; DNS-label &gt; . &lt; location &gt; .cloudapp.azure.com*.

### <a name="storage-accounts"></a>Opslagaccounts
Elk clusterknooppunttype wordt ondersteund door een [Azure-opslagaccount](../storage/common/storage-introduction.md) en beheerde schijven.

## <a name="cluster-security"></a>Clusterbeveiliging
Een Service Fabric cluster is een resource van u.  Het is uw verantwoordelijkheid om uw clusters te beveiligen om te voorkomen dat onbevoegde gebruikers verbinding met hen maken. Een beveiligd cluster is vooral belangrijk wanneer u productieworkloads op het cluster gebruikt. 

### <a name="node-to-node-security"></a>Beveiliging van knooppunt naar knooppunt
Beveiliging van knooppunt naar knooppunt beveiligt de communicatie tussen de VM's of computers in een cluster. Dit beveiligingsscenario zorgt ervoor dat alleen computers die zijn gemachtigd om lid te worden van het cluster, kunnen deelnemen aan het hosten van toepassingen en services in het cluster. Service Fabric X.509-certificaten gebruikt om een cluster te beveiligen en toepassingsbeveiligingsfuncties te bieden.  Een clustercertificaat is vereist om clusterverkeer te beveiligen en cluster- en serververificatie te bieden.  Zelf ondertekende certificaten kunnen worden gebruikt voor testclusters, maar een certificaat van een vertrouwde certificeringsinstantie moet worden gebruikt om productieclusters te beveiligen.

Lees Beveiliging van [knooppunt naar knooppunt voor meer informatie](service-fabric-cluster-security.md#node-to-node-security)

### <a name="client-to-node-security"></a>Beveiliging van client naar knooppunt
Client-naar-knooppuntbeveiliging verifieert clients en helpt de communicatie tussen een client en afzonderlijke knooppunten in het cluster te beveiligen. Dit type beveiliging zorgt ervoor dat alleen geautoriseerde gebruikers toegang hebben tot het cluster en de toepassingen die op het cluster zijn geïmplementeerd. Clients worden uniek geïdentificeerd via hun X.509-certificaatbeveiligingsreferenties. Elk aantal optionele clientcertificaten kan worden gebruikt voor het verifiëren van beheerders- of gebruikersclients met het cluster.

Naast clientcertificaten kunnen Azure Active Directory ook worden geconfigureerd voor het verifiëren van clients met het cluster.

Lees [Client-naar-knooppuntbeveiliging voor meer informatie](service-fabric-cluster-security.md#client-to-node-security)

### <a name="role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer
Met op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) kunt u fijngranenteerde toegangsbeheer toewijzen aan Azure-resources.  U kunt verschillende toegangsregels toewijzen aan abonnementen, resourcegroepen en resources.  Azure RBAC-regels worden overgenomen in de resourcehiërarchie, tenzij ze op een lager niveau worden overgenomen.  U kunt elke gebruiker of gebruikersgroep op uw AAD toewijzen met Azure RBAC-regels, zodat aangewezen gebruikers en groepen uw cluster kunnen wijzigen.  Lees het Overzicht van [Azure RBAC voor meer informatie.](../role-based-access-control/overview.md)

Service Fabric biedt ook ondersteuning voor toegangsbeheer om de toegang tot bepaalde clusterbewerkingen voor verschillende groepen gebruikers te beperken. Dit helpt het cluster veiliger te maken. Er worden twee typen toegangsbeheer ondersteund voor clients die verbinding maken met een cluster: beheerdersrol en gebruikersrol.  

Lees voor meer informatie Service Fabric [op rollen gebaseerd toegangsbeheer.](service-fabric-cluster-security.md#service-fabric-role-based-access-control)

### <a name="network-security-groups"></a>Netwerkbeveiligingsgroepen 
Netwerkbeveiligingsgroepen (NSG's) bepalen het inkomende en uitgaande verkeer van een subnet, VM of specifieke NIC.  Wanneer meerdere VM's in hetzelfde virtuele netwerk worden ingesteld, kunnen ze standaard via elke poort met elkaar communiceren.  Als u de communicatie tussen de machines wilt beperken, kunt u NSG's definiëren om het netwerk te segmenteren of VM's van elkaar te isoleren.  Als u meerdere knooppunttypen in een cluster hebt, kunt u NSG's toepassen op subnetten om te voorkomen dat computers van verschillende knooppunttypen met elkaar communiceren.  

Lees over [beveiligingsgroepen](../virtual-network/network-security-groups-overview.md) voor meer informatie

## <a name="scaling"></a>Schalen

Toepassingseisen veranderen na een periode. Mogelijk moet u het aantal clusterbronnen verhogen om te voldoen aan de toegenomen werkbelasting van de toepassing of het netwerkverkeer, of om clusterbronnen te verlagen wanneer de vraag afneemt. Nadat u een Service Fabric cluster hebt aanmaken, kunt u het cluster horizontaal schalen (het aantal knooppunten wijzigen) of verticaal (de resources van de knooppunten wijzigen). U kunt de schaal van het cluster op elk gewenst moment aanpassen, zelfs als er workloads op het cluster worden uitgevoerd. Tijdens het schalen van het cluster worden uw toepassingen ook automatisch geschaald.

Lees [Azure-clusters schalen voor meer informatie.](service-fabric-cluster-scaling.md)

## <a name="upgrading"></a>Upgrade uitvoeren
Een Azure Service Fabric is een resource die u bezit, maar deels wordt beheerd door Microsoft. Microsoft is verantwoordelijk voor het patchen van het onderliggende besturingssysteem en het uitvoeren Service Fabric runtime-upgrades op uw cluster. U kunt instellen dat uw cluster automatische runtime-upgrades ontvangt wanneer Microsoft een nieuwe versie uitkomt of een ondersteunde runtimeversie selecteren die u wilt. Naast runtime-upgrades kunt u ook de clusterconfiguratie bijwerken, zoals certificaten of toepassingspoorten.

Lees Clusters upgraden [voor meer informatie.](service-fabric-cluster-upgrade.md)

## <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen
Zie [Ondersteunde versies in Azure voor](./service-fabric-versions.md) meer informatie


## <a name="next-steps"></a>Volgende stappen
Lees meer over het [beveiligen,](service-fabric-cluster-security.md) [schalen en](service-fabric-cluster-scaling.md) [upgraden van Azure-clusters.](service-fabric-cluster-upgrade.md)

Meer informatie [over Service Fabric ondersteuningsopties](service-fabric-support.md).

[Image]: media/service-fabric-azure-clusters-overview/Cluster.PNG
---
title: Een SQL Server AlwaysOn-beschikbaarheids groep configureren in verschillende regio's
description: In dit artikel wordt uitgelegd hoe u een SQL Server always on-beschikbaarheids groep configureert op virtuele machines van Azure met een replica in een andere regio.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 388c464e-a16e-4c9d-a0d5-bb7cf5974689
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: 60bb5ac652a80b5ae52c91f91fa0c80440e9cc82
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359078"
---
# <a name="configure-a-sql-server-always-on-availability-group-across-different-azure-regions"></a>Een SQL Server AlwaysOn-beschikbaarheids groep configureren in verschillende Azure-regio's

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In dit artikel wordt uitgelegd hoe u een replica van een SQL Server always on-beschikbaarheids groep configureert op virtuele machines van Azure op een externe Azure-locatie. Gebruik deze configuratie ter ondersteuning van herstel na nood gevallen.

Dit artikel is van toepassing op Azure Virtual Machines in de Resource Manager-modus.

In de volgende afbeelding ziet u een algemene implementatie van een beschikbaarheids groep op virtuele machines van Azure:

   ![Diagram waarin de Azure-load balancer en de Beschikbaarheidsset met een ' Windows Server-failovercluster ' en ' AlwaysOn-beschikbaarheids groep ' worden weer gegeven.](./media/availability-group-manually-configure-multiple-regions/00-availability-group-basic.png)

In deze implementatie bevinden alle virtuele machines zich in één Azure-regio. De replica's van de beschikbaarheids groep kunnen synchroon door voeren met automatische failover op SQL-1 en SQL-2. Als u deze architectuur wilt maken, raadpleegt u de [sjabloon of zelf studie voor de beschikbaarheids groep](availability-group-overview.md).

Deze architectuur is kwetsbaar voor uitval tijd als de Azure-regio ontoegankelijk wordt. U kunt dit beveiligings probleem oplossen door een replica toe te voegen aan een andere Azure-regio. In het volgende diagram ziet u hoe de nieuwe architectuur eruitziet:

   ![Beschikbaarheids groep DR](./media/availability-group-manually-configure-multiple-regions/00-availability-group-basic-dr.png)

In het voor gaande diagram ziet u een nieuwe virtuele machine met de naam SQL-3. SQL-3 bevindt zich in een andere Azure-regio. SQL-3 wordt toegevoegd aan het Windows Server-failovercluster. SQL-3 kan fungeren als host voor een replica van een beschikbaarheids groep. Ten slotte ziet u dat de Azure-regio voor SQL-3 een nieuwe Azure-load balancer heeft.

>[!NOTE]
> Een Azure-beschikbaarheidsset is vereist wanneer meer dan één virtuele machine zich in dezelfde regio bevindt. Als slechts één virtuele machine zich in de regio bevindt, is de beschikbaarheidsset niet vereist. U kunt alleen een virtuele machine in een beschikbaarheidsset plaatsen tijdens de aanmaak tijd. Als de virtuele machine zich al in een beschikbaarheidsset bevindt, kunt u later een virtuele machine voor een extra replica toevoegen.

In deze architectuur wordt de replica in de externe regio doorgaans geconfigureerd met de beschikbaarheids modus voor asynchrone door Voer en de hand matige failover-modus.

Wanneer replica's van beschikbaarheids groepen zich op virtuele Azure-machines in verschillende Azure-regio's bevinden, is voor elke regio het volgende vereist:

* Een virtuele netwerk gateway
* Een virtuele netwerk gateway verbinding

In het volgende diagram ziet u hoe de netwerken communiceren tussen data centers.

   ![Diagram waarin de twee virtuele netwerken in verschillende Azure-regio's worden weer gegeven die communiceren met behulp van V P N gateways.](./media/availability-group-manually-configure-multiple-regions/01-vpngateway-example.png)

>[!IMPORTANT]
>Met deze architectuur worden uitgaande gegevens kosten in rekening gebracht voor gegevens die tussen Azure-regio's worden gerepliceerd. Zie [prijzen van band breedte](https://azure.microsoft.com/pricing/details/bandwidth/).  

## <a name="create-remote-replica"></a>Externe replica maken

Voer de volgende stappen uit om een replica te maken in een extern Data Center:

1. [Maak een virtueel netwerk in de nieuwe regio](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network).

1. [Configureer een vnet-naar-VNet-verbinding met behulp van de Azure Portal](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).

   >[!NOTE]
   >In sommige gevallen moet u Power shell gebruiken om de VNet-naar-VNet-verbinding te maken. Als u bijvoorbeeld verschillende Azure-accounts gebruikt, kunt u de verbinding niet configureren in de portal. In dit geval raadpleegt u [een vnet-naar-vnet-verbinding configureren met behulp van de Azure Portal](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

1. [Maak een domein controller in de nieuwe regio](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100).

   Deze domein controller biedt verificatie als de domein controller in de primaire site niet beschikbaar is.

1. [Maak een SQL Server virtuele machine in de nieuwe regio](create-sql-vm-portal.md).

1. [Maak een Azure-Load Balancer in het netwerk in de nieuwe regio](availability-group-manually-configure-tutorial.md#configure-internal-load-balancer).

   Deze load balancer moet:

   - Zich in hetzelfde netwerk en subnet bevinden als de nieuwe virtuele machine.
   - Een statisch IP-adres hebben voor de beschikbaarheids groep-listener.
   - Neem een back-end-groep op die bestaat uit alleen de virtuele machines in dezelfde regio als de load balancer.
   - Gebruik een TCP-poort test die specifiek is voor het IP-adres.
   - Een taakverdelings regel hebben die specifiek is voor de SQL Server in dezelfde regio.  
   - Een Standard Load Balancer als de virtuele machines in de back-endadresgroep geen deel uitmaken van een enkele beschikbaarheidsset of een virtuele-machine schaalset. Raadpleeg [Azure Load Balancer standaard overzicht](../../../load-balancer/load-balancer-overview.md)voor meer informatie.

1. [Voeg de functie Failover Clustering toe aan de nieuwe SQL Server](availability-group-manually-configure-prerequisites-tutorial.md#add-failover-clustering-features-to-both-sql-server-vms).

1. [Voeg de nieuwe SQL Server toe aan het domein](availability-group-manually-configure-prerequisites-tutorial.md#joinDomain).

1. [Stel het nieuwe SQL Server-service account in om een domein account te gebruiken](availability-group-manually-configure-prerequisites-tutorial.md#setServiceAccount).

1. [Voeg de nieuwe SQL Server toe aan het Windows Server-failovercluster](availability-group-manually-configure-tutorial.md#addNode).

1. Voeg een IP-adres bron toe aan het cluster.

   U kunt de IP-adres bron maken in Failoverclusterbeheer. Selecteer de naam van het cluster, klik met de rechter muisknop op de cluster naam onder **cluster kern resources** en selecteer **Eigenschappen**: 

   ![Scherm opname waarin de ' Failoverclusterbeheer ' wordt weer gegeven met een cluster naam, ' server naam ' en ' Eigenschappen ' geselecteerd.](./media/availability-group-manually-configure-multiple-regions/cluster-name-properties.png)

   Selecteer in het dialoog venster **Eigenschappen** de optie **toevoegen** onder **IP-adres** en voeg vervolgens het IP-adres van de cluster naam toe uit de regio voor het externe netwerk. Selecteer **OK** in het dialoog venster **IP-adres** en selecteer vervolgens **OK** in het dialoog venster **cluster eigenschappen** om het nieuwe IP-adres op te slaan. 

   ![Cluster-IP toevoegen](./media/availability-group-manually-configure-multiple-regions/add-cluster-ip-address.png)


1. Voeg het IP-adres toe als afhankelijkheid voor de naam van het basis cluster.

   Open de cluster eigenschappen en klik op het tabblad **afhankelijkheden** . Een OR-afhankelijkheid configureren voor de twee IP-adressen: 

   ![Cluster eigenschappen](./media/availability-group-manually-configure-multiple-regions/cluster-ip-dependencies.png)

1. Voeg een IP-adres bron toe aan de rol van de beschikbaarheids groep in het cluster. 

   Klik met de rechter muisknop op de rol van de beschikbaarheids groep in Failoverclusterbeheer, kies **resource toevoegen**, **meer resources** en **IP-adres** selecteren.

   ![IP-adres maken](./media/availability-group-manually-configure-multiple-regions/20-add-ip-resource.png)

   Configureer dit IP-adres als volgt:

   - Gebruik het netwerk vanuit het externe Data Center.
   - Wijs het IP-adres toe vanuit de nieuwe Azure-load balancer. 

1. Voeg de IP-adres bron toe als afhankelijkheid voor het cluster van de listener-client toegangs punt (netwerk naam).

   De volgende scherm afbeelding toont een correct geconfigureerde IP-adres cluster Bron:

   ![Beschikbaarheidsgroep](./media/availability-group-manually-configure-multiple-regions/50-configure-dependency-multiple-ip.png)

   >[!IMPORTANT]
   >De cluster resource groep bevat zowel IP-adressen. Beide IP-adressen zijn afhankelijkheden voor het client toegangs punt van de listener. Gebruik de operator **or** in de cluster afhankelijkheids configuratie.

1. [Stel de cluster parameters in Power shell in](availability-group-manually-configure-tutorial.md#setparam).

   Voer het Power shell-script uit met de netwerk naam van het cluster, het IP-adres en de test poort die u hebt geconfigureerd op het load balancer in de nieuwe regio.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # The cluster name for the network in the new region (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name).
   $IPResourceName = "<IPResourceName>" # The cluster name for the new IP Address resource.
   $ILBIP = "<n.n.n.n>" # The IP Address of the Internal Load Balancer (ILB) in the new region. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ProbePort = <nnnnn> # The probe port you set on the ILB.

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

1. Schakel op de nieuwe SQL Server in SQL Server Configuration Manager AlwaysOn- [beschikbaarheids groepen](/sql/database-engine/availability-groups/windows/enable-and-disable-always-on-availability-groups-sql-server)in.

1. [Open firewall poorten op de nieuwe SQL Server](availability-group-manually-configure-prerequisites-tutorial.md#endpoint-firewall).

   De poort nummers die u moet openen, zijn afhankelijk van uw omgeving. Open poorten voor het mirroring-eind punt en Azure load balancer Health probe.


1. [Voeg op de nieuwe SQL Server een replica toe aan de beschikbaarheids groep](/sql/database-engine/availability-groups/windows/use-the-add-replica-to-availability-group-wizard-sql-server-management-studio).

   Voor een replica in een externe Azure-regio stelt u deze in voor asynchrone replicatie met hand matige failover.  

## <a name="set-connection-for-multiple-subnets"></a>Verbinding instellen voor meerdere subnetten

De replica in het externe Data Center maakt deel uit van de beschikbaarheids groep, maar bevindt zich in een ander subnet. Als deze replica de primaire replica wordt, kunnen er time-outs voor toepassings verbindingen optreden. Dit gedrag is hetzelfde als een on-premises beschikbaarheids groep in een implementatie met meerdere subnetten. Als u verbindingen van client toepassingen wilt toestaan, werkt u de client verbinding bij of configureert u de cache voor naam omzetting in het cluster netwerk naam bron.

Werk bij voor keur de client verbindings reeksen bij om in te stellen `MultiSubnetFailover=Yes` . Zie [verbinding maken met MultiSubnetFailover](/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#Anchor_0).

Als u de verbindings reeksen niet kunt wijzigen, kunt u de cache voor naam omzetting configureren. Zie [time-outfout en u kunt geen verbinding maken met een SQL Server 2012 AlwaysOn-beschikbaarheids groep-listener in een omgeving met meerdere subnetten](https://support.microsoft.com/help/2792139/time-out-error-and-you-cannot-connect-to-a-sql-server-2012-alwayson-av).

## <a name="fail-over-to-remote-region"></a>Failover naar externe regio

Als u de verbinding van de listener met de externe regio wilt testen, kunt u een failover uitvoeren voor de replicatie naar de externe regio. Hoewel de replica asynchroon is, is failover kwetsbaar voor mogelijk gegevens verlies. Als u een failover wilt uitvoeren zonder gegevens verlies, wijzigt u de beschikbaarheids modus in synchroon en stelt u de failover-modus in op automatisch. Voer de volgende stappen uit:

1. In **objectverkenner** maakt u verbinding met het exemplaar van SQL Server dat als host fungeert voor de primaire replica.
1. Klik onder **AlwaysOn-beschikbaarheidsgroepen**, **beschikbaarheids groepen** met de rechter muisknop op uw beschikbaarheids groep en selecteer **Eigenschappen**.
1. Stel op de pagina **Algemeen** onder **beschikbaarheids replica's** de secundaire replica in op de Dr-site om de beschikbaarheids modus voor **synchrone door Voer** en de modus **automatische** failover te gebruiken.
1. Als u een secundaire replica op dezelfde site hebt als uw primaire replica voor hoge Beschik baarheid, stelt u deze replica in op **asynchroon door voeren** en **hand matig**.
1. Selecteer OK.
1. Klik in **objectverkenner** met de rechter muisknop op de beschikbaarheids groep en selecteer **dash board weer geven**.
1. Controleer op het dash board of de replica op de DR-site is gesynchroniseerd.
1. Klik in **objectverkenner** met de rechter muisknop op de beschikbaarheids groep en selecteer **failover...**. SQL Server beheer Studios opent een wizard voor het uitvoeren van een failover van SQL Server.  
1. Selecteer **volgende** en selecteer de SQL Server instantie in de Dr-site. Selecteer **volgende** opnieuw.
1. Maak verbinding met het SQL Server-exemplaar op de DR-site en selecteer **volgende**.
1. Controleer op de pagina **samen vatting** de instellingen en selecteer **volt ooien**.

Nadat de verbinding is getest, verplaatst u de primaire replica terug naar uw primaire data centrum en stelt u de beschikbaarheids modus weer in op de normale instellingen van het besturings systeem. De volgende tabel bevat de normale operationele instellingen voor de architectuur die in dit document wordt beschreven:

| Locatie | Server exemplaar | Rol | Beschikbaarheids modus | Failover-modus
| ----- | ----- | ----- | ----- | -----
| Primair data centrum | SQL-1 | Primair | Synchroon | Automatisch
| Primair data centrum | SQL-2 | Secundair | Synchroon | Automatisch
| Secundair of extern Data Center | SQL-3 | Secundair | Asynchroon | Handmatig


### <a name="more-information-about-planned-and-forced-manual-failover"></a>Meer informatie over geplande en geforceerde hand matige failover

Zie de volgende onderwerpen voor meer informatie:

- [Een geplande hand matige failover van een beschikbaarheids groep uitvoeren (SQL Server)](/sql/database-engine/availability-groups/windows/perform-a-planned-manual-failover-of-an-availability-group-sql-server)
- [Een geforceerde hand matige failover van een beschikbaarheids groep uitvoeren (SQL Server)](/sql/database-engine/availability-groups/windows/perform-a-forced-manual-failover-of-an-availability-group-sql-server)

## <a name="next-steps"></a>Volgende stappen

* [AlwaysOn-beschikbaarheidsgroepen](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)
* [Azure Virtual Machines](../../../virtual-machines/windows/index.yml)
* [Load balancers van Azure](availability-group-manually-configure-tutorial.md#configure-internal-load-balancer)
* [Azure-beschikbaarheids sets](../../../virtual-machines/manage-availability.md)
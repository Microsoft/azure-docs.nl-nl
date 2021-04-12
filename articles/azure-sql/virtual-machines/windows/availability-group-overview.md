---
title: Overzicht van SQL Server AlwaysOn-beschikbaarheidsgroepen
description: In dit artikel vindt u algemene informatie over SQL Server AlwaysOn-beschikbaarheidsgroepen in Azure Virtual Machines.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: overview
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/07/2020
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: 8bbd56499c9b62248662fc5e8df0d5b3e1b672d4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102504164"
---
# <a name="always-on-availability-group-on-sql-server-on-azure-vms"></a>AlwaysOn-beschikbaarheidsgroep op SQL Server op Azure-VM's
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In dit artikel vindt u algemene informatie over AlwaysOn-beschikbaarheidsgroepen voor SQL Server in Azure Virtual Machines (VM's). 

## <a name="overview"></a>Overzicht

AlwaysOn-beschikbaarheidsgroepen in Azure Virtual Machines zijn vergelijkbaar met [on-premises AlwaysOn-beschikbaarheidsgroepen](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). Omdat de virtuele machines echter in Azure worden gehost, zijn er ook enkele aanvullende overwegingen, zoals VM-redundantie en routeringsverkeer in het Azure-netwerk. 

In het volgende diagram ziet u een beschikbaarheidsgroep voor SQL Server op Azure-VM's:

![Beschikbaarheidsgroep](./media/availability-group-overview/00-EndstateSampleNoELB.png)


## <a name="vm-redundancy"></a>VM-redundantie 

Om redundantie en de maximale beschikbaarheid te vergroten, moeten SQL Server-VM's zich in dezelfde [beschikbaarheidsset](../../../virtual-machines/availability-set-overview.md) bevinden of in verschillende [beschikbaarheidszones](../../../availability-zones/az-overview.md).

Het plaatsen van een set VM's in dezelfde beschikbaarheidsset, beschermt deze tegen storingen in een datacentrum door apparatuuruitval (VM's binnen een beschikbaarheidsset delen geen resources) of updates (VM's binnen een beschikbaarheidsset worden niet op hetzelfde moment bijgewerkt). Beschikbaarheidszones beschermen tegen storingen in een heel datacentrum, waar elke zone een set datacentra binnen een regio vertegenwoordigt.  Door ervoor te zorgen dat resources in verschillende beschikbaarheidszones worden geplaatst, kan geen storing al uw VM's offline halen.

Wanneer u Azure-VM's maakt, met u kiezen tussen het configureren van beschikbaarheidssets en beschikbaarheidszones.  Een Azure-VM kan niet deel nemen aan beide.


## <a name="connectivity"></a>Connectiviteit 

Bij traditionele on-premises implementaties kunnen clients verbinding maken met de listener voor de beschikbaarheidsgroep met behulp van de naam van het virtuele netwerk (VNN). De listener stuurt verkeer naar de juiste SQL Server-replica in de beschikbaarheidsgroep. Er is echter een extra vereiste voor het routeren van verkeer op het Azure-netwerk. 

Bij SQL Server op virtuele Azure-machines configureert u een [load balancer](availability-group-vnn-azure-load-balancer-configure.md) om verkeer door te sturen naar de listener van uw beschikbaarheidsgroep. Als u zich op SQL Server 2019 CU8 of hoger bevindt, kunt u een [DNN-listener (gedistribueerde netwerknaam) configureren](availability-group-distributed-network-name-dnn-listener-configure.md) om de traditionele listener van de VNN-beschikbaarheidsgroep te vervangen. 

Raadpleeg [HADR-verbindingen routeren naar SQL Server in Azure-VM's](hadr-cluster-best-practices.md#connectivity) voor meer informatie over opties voor clusterconnectiviteit. 

### <a name="vnn-listener"></a>VNN-listener 

Gebruik een [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) om verkeer te routeren van de client naar de traditionele VNN-listener (virtuele netwerknaam) van de beschikbaarheidsgroep op het Azure-netwerk. 

De load balancer bevat de IP-adressen voor de VNN-listener. Als u meer dan één beschikbaarheidsgroep hebt, is voor elke groep een VNN-listener vereist. Eén load balancer kan meerdere listeners ondersteunen.

Raadpleeg [een load balancer configureren](availability-group-vnn-azure-load-balancer-configure.md) om aan de slag te gaan. 

### <a name="dnn-listener"></a>DNN-listener

SQL Server 2019 CU8 introduceert ondersteuning voor de DNN-listener (gedistribueerde netwerknaam). De DNN-listener vervangt de traditionele listener van de beschikbaarheidsgroep, waarbij er geen Azure Load Balancer meer nodig is om verkeer te routeren in het Azure-netwerk. 

De DNN-listener is de aanbevolen HADR-connectiviteitsoplossing in Azure, omdat deze de implementatie vereenvoudigt, het onderhoud en de kosten vermindert en de failovertijd in het geval van een storing verkort. 

Gebruik de DNN-listener voor het vervangen van een bestaande VNN-listener, of gebruik deze in combinatie met een bestaande VNN-listener, zodat uw beschikbaarheidsgroep twee afzonderlijke verbindingspunten heeft: één met de naam (en poort, indien niet-standaard) van de VNN-listener en één met de naam en poort van de DNN-listener. Dit kan handig zijn voor klanten die de latentie van een load balancer-failover willen voorkomen, maar wel gebruik willen maken van SQL Server-functies die afhankelijk zijn van de VNN-listener, zoals gedistribueerde beschikbaarheidsgroepen, Service Broker of FileStream. Zie [interoperabiliteit van DNN-listener- en SQL Server-functies](availability-group-dnn-interoperability.md) voor meer informatie

Raadpleeg [een DNN-listener configureren](availability-group-distributed-network-name-dnn-listener-configure.md) om aan de slag te gaan.


## <a name="deployment"></a>Implementatie 

Er zijn meerdere opties voor het implementeren van een beschikbaarheidsgroep voor SQL Server op virtuele Azure-machines, waarbij sommige opties geautomatiseerder zijn dan andere. 

De volgende tabel bevat een vergelijking van de beschikbare opties:

| | Azure Portal | Azure CLI / PowerShell | Quickstartsjablonen | Handmatig |
|---------|---------|---------|---------|---------|
|**SQL Server-versie** |2016 + |2016 +|2016 +|2012 +|
|**SQL Server-editie** |Enterprise |Enterprise |Enterprise |Enterprise, Standard|
|**Windows Server-versie**| 2016 + | 2016 + | 2016 + | Alles|
|**Het cluster wordt voor u gemaakt**|Ja|Ja | Ja |Nee|
|**De beschikbaarheidsgroep wordt voor u gemaakt** |Ja |Nee|Nee|Nee|
|**De listener en load balancer worden onafhankelijk van elkaar gemaakt** |Nee|Nee|Nee|Ja|
|**Kan met deze methode een DNN-listener worden gemaakt?**|Nee|Nee|Nee|Ja|
|**Configuratie van WSFC-quorum**|Cloudwitness|Cloudwitness|Cloudwitness|Alles|
|**Herstel na noodgeval met meerdere regio's** |Nee|Nee|Nee|Ja|
|**Ondersteuning voor meerdere subnetten** |Ja|Ja|Ja|Ja|
|**Ondersteuning voor een bestaande AD**|Ja|Ja|Ja|Ja|
|**Herstel na noodgeval met multizone in dezelfde regio**|Ja|Ja|Ja|Ja|
|**Gedistribueerde beschikbaarheidsgroep zonder AD**|Nee|Nee|Nee|Ja|
|**Gedistribueerde beschikbaarheidsgroep zonder cluster** |Nee|Nee|Nee|Ja|

Raadpleeg [Azure Portal](availability-group-azure-portal-configure.md), [Azure CLI/PowerShell](./availability-group-az-commandline-configure.md), [Quickstart-sjablonen](availability-group-quickstart-template-configure.md) en [Handleiding](availability-group-manually-configure-prerequisites-tutorial.md) voor meer informatie.

## <a name="considerations"></a>Overwegingen 

Bij een failover-gastcluster voor een Azure IaaS-VM adviseren we één NIC per server (clusterknooppunt) en één subnet. Een Azure-netwerk maakt gebruikt van fysieke redundantie, waardoor extra NIC's en subnetten overbodig zijn voor een gastcluster voor een Azure IaaS-VM. Hoewel het clustervalidatierapport een waarschuwing zal bevatten dat de knooppunten alleen bereikbaar zijn in één netwerk, kan deze waarschuwing zonder problemen worden genegeerd in het geval van failover-gastclusters voor een Azure IaaS-VM. 

## <a name="next-steps"></a>Volgende stappen

Bekijk de [best practices voor HADR](hadr-cluster-best-practices.md) en ga vervolgens aan de slag met het implementeren van uw beschikbaarheidsgroep met behulp van de [Azure Portal](availability-group-azure-portal-configure.md), [Azure CLI/PowerShell](./availability-group-az-commandline-configure.md), [quickstart-sjablonen](availability-group-quickstart-template-configure.md) of [handmatig](availability-group-manually-configure-prerequisites-tutorial.md).

U kunt ook een [clusterloze beschikbaarheidsgroep](availability-group-clusterless-workgroup-configure.md) implementeren of een beschikbaarheidsgroep in [meerdere regio's](availability-group-manually-configure-multiple-regions.md) implementeren.

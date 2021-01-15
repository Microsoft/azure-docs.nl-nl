---
title: 'Netwerk beveiligings groep: hoe deze werkt'
titlesuffix: Azure Virtual Network
description: Meer informatie over hoe netwerk beveiligings groepen u helpen bij het filteren van netwerk verkeer tussen Azure-resources.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/24/2020
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: 5cf0345ccffe95286b95607c6c7322752df6342b
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98223275"
---
# <a name="how-network-security-groups-filter-network-traffic"></a>Hoe netwerk beveiligings groepen netwerk verkeer filteren
<a name="network-security-groups"></a>

U kunt een Azure-netwerk beveiligings groep gebruiken voor het filteren van netwerk verkeer naar en van Azure-resources in een virtueel Azure-netwerk. Een netwerk beveiligings groep bevat [beveiligings regels](./network-security-groups-overview.md#security-rules) voor het toestaan of weigeren van binnenkomend netwerk verkeer naar of uitgaand netwerk verkeer van verschillende typen Azure-resources. Voor elke regel kunt u de bron en het doel, de poort en het protocol opgeven.

U kunt resources van diverse Azure-services in een virtueel Azure-netwerk implementeren. Zie [Services die kunnen worden geïmplementeerd in een virtueel netwerk](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) voor de volledige lijst. U kunt nul of één netwerkbeveiligingsgroep aan elk [subnet](virtual-network-manage-subnet.md#change-subnet-settings) van een virtueel netwerk en [netwerkinterface](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group) in een virtuele machine koppelen. Dezelfde netwerkbeveiligingsgroep kan aan zoveel subnetten en netwerkinterfaces worden gekoppeld als u wilt.

In de volgende afbeelding ziet u verschillende scenario's voor het implementeren van netwerkbeveiligingsgroepen om netwerkverkeer naar en van internet via TCP-poort 80 toe te staan:

![Verwerken van NSG's](./media/network-security-group-how-it-works/network-security-group-interaction.png)

Raadpleeg de afbeelding hierboven in combinatie met de volgende tekst als u wilt weten hoe binnenkomende en uitgaande regels voor netwerkbeveiligingsgroepen worden verwerkt:

## <a name="inbound-traffic"></a>Binnenkomend verkeer

Voor binnenkomend verkeer worden eerst de regels verwerkt in een netwerkbeveiligingsgroep die aan een subnet is gekoppeld, indien aanwezig. Vervolgens worden de regels verwerkt die zijn gekoppeld aan een netwerkinterface, indien aanwezig.

- **VM1**: de beveiligingsregels in *NSG1* worden verwerkt, omdat deze is gekoppeld aan *Subnet1* en *VM1* zich in *Subnet1* bevindt. Tenzij u een regel hebt gemaakt waardoor poort 80 binnenkomend verkeer toestaat, wordt het verkeer geweigerd door de standaardbeveiligingsregel [DenyAllInbound](./network-security-groups-overview.md#denyallinbound) en nooit geëvalueerd door *NSG2*, omdat *NSG2* is gekoppeld aan de netwerkinterface. Als *NSG1* een beveiligingsregel bevat die poort 80 toestaat, wordt het verkeer door *NSG2* verwerkt. Als u poort 80 voor de virtuele machine wilt toestaan, moet zowel *NSG1* als *NSG2* een regel bevatten die verkeer vanaf internet via poort 80 toestaat.
- **VM2**: de regels in *NSG1* worden verwerkt omdat *VM2* zich ook in *Subnet1* bevindt. Omdat *VM2* geen netwerkinterface bevat die aan de bijbehorende netwerkinterface is gekoppeld, wordt hier alle verkeer ontvangen dat is toegestaan via *NSG1* of wordt alle verkeer geweigerd dat is geweigerd door *NSG1*. Verkeer wordt voor alle resources in hetzelfde subnet toegestaan of geweigerd als een netwerkbeveiligingsgroep aan een subnet is gekoppeld.
- **VM3**: omdat er geen netwerkbeveiligingsgroep is gekoppeld aan *Subnet2*, wordt verkeer naar het subnet toegestaan en verwerkt door *NSG2*, omdat *NSG2* is gekoppeld aan de netwerkinterface die met *VM3* is verbonden.
- **VM4**: verkeer naar *VM4* is toegestaan omdat er geen netwerkbeveiligingsgroep is gekoppeld aan *Subnet3* of de netwerkinterface in de virtuele machine. Alle netwerkverkeer wordt toegestaan via een subnet en netwerkinterface als er geen netwerkbeveiligingsgroep aan is gekoppeld.

## <a name="outbound-traffic"></a>Uitgaand verkeer

Voor uitgaand verkeer worden eerst de regels verwerkt in een netwerkbeveiligingsgroep die aan een netwerkinterface is gekoppeld, indien aanwezig. Vervolgens worden de regels in een netwerkbeveiligingsgroep verwerkt die zijn gekoppeld aan het subnet, indien aanwezig.

- **VM1**: de beveiligingsregels in *NSG2* worden verwerkt. Tenzij u een beveiligingsregel maakt die uitgaand verkeer naar internet via poort 80 weigert, wordt in zowel *NSG1* als *NSG2* verkeer toegestaan door de standaardbeveiligingsregel [AllowInternetOutbound](./network-security-groups-overview.md#allowinternetoutbound). Als *NSG2* een beveiligingsregel bevat die poort 80 weigert, wordt het verkeer geweigerd en nooit door *NSG1* geëvalueerd. Als u binnenkomend verkeer van de virtuele machine via poort 80 wilt weigeren, moet een van de netwerkbeveiligingsgroepen, of beide, een regel bevatten die uitgaand verkeer naar internet via poort 80 weigert.
- **VM2**: al het verkeer wordt via de netwerkinterface naar het subnet verzonden, omdat aan de netwerkinterface die met *VM2* is verbonden, geen netwerkbeveiligingsgroep is gekoppeld. De regels in *NSG1* worden verwerkt.
- **VM3**: als *NSG2* een beveiligingsregel bevat die poort 80 weigert, wordt het verkeer geweigerd. Als *NSG2* een beveiligingsregel bevat die poort 80 toestaat, wordt via poort 80 uitgaand verkeer naar internet toegestaan, omdat er geen netwerkbeveiligingsgroep aan *Subnet2* is gekoppeld.
- **VM4**: al het netwerkverkeer naar *VM4* is toegestaan omdat er geen netwerkbeveiligingsgroep is gekoppeld aan de netwerkinterface die met de virtuele machine is verbonden, of aan *Subnet3*.


## <a name="intra-subnet-traffic"></a>Intra-Subnet verkeer

Het is belang rijk te weten dat beveiligings regels in een NSG die aan een subnet zijn gekoppeld, de connectiviteit van de virtuele machine van de VM kunnen beïnvloeden. Als er bijvoorbeeld een regel wordt toegevoegd aan *NSG1* die al het binnenkomende en uitgaande verkeer weigert, kunnen *VM1* en *VM2* niet meer met elkaar communiceren. Een andere regel moet specifiek worden toegevoegd om dit toe te staan. 

U kunt eenvoudig de cumulatieve regels bekijken die op een netwerkinterface worden toegepast door de [effectieve beveiligingsregels](virtual-network-network-interface.md#view-effective-security-rules) voor een netwerkinterface te bekijken. U kunt ook de functie [IP-stroom controleren](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) in Azure Network Watcher gebruiken om te bepalen of de communicatie van of naar een netwerkinterface is toegestaan. Met IP-stroom controle wordt aangegeven of een communicatie wordt toegestaan of geweigerd en welke netwerk beveiligings regel het verkeer toestaat of weigert.

> [!NOTE]
> Netwerk beveiligings groepen zijn gekoppeld aan subnetten of virtuele machines en Cloud Services die zijn geïmplementeerd in het klassieke implementatie model, en op subnetten of netwerk interfaces in het Resource Manager-implementatie model. Zie [Over Azure-implementatiemodellen](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) voor meer informatie over Azure-implementatiemodellen.

> [!TIP]
> Tenzij u een specifieke reden hebt voor, wordt u aangeraden een netwerk beveiligings groep te koppelen aan een subnet of een netwerk interface, maar niet beide. Aangezien regels in een netwerkbeveiligingsgroep die aan een subnet is gekoppeld, kunnen conflicteren met regels in een netwerkbeveiligingsgroep die aan een netwerkinterface is gekoppeld, kunnen er onverwachte communicatieproblemen optreden waarvoor een probleemoplossing dient te worden uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

* Zie [Integratie van virtuele netwerken voor Azure-services](virtual-network-for-azure-services.md) voor meer informatie over de Azure-resources die in een virtueel netwerk kunnen worden geïmplementeerd en waaraan netwerkbeveiligingsgroepen kunnen worden gekoppeld.
* Als u nog nooit een netwerkbeveiligingsgroep hebt gemaakt, kunt u een snelle [zelfstudie](tutorial-filter-network-traffic.md) uitvoeren om enige ervaring met het maken van een netwerkbeveiligingsgroep op te doen.
* Zie [Een netwerkbeveiligingsgroep maken, wijzigen of verwijderen](manage-network-security-group.md) als u bekend bent met het netwerkbeveiligingsgroepen en u ze wilt beheren. 
* Zie [Een probleem met een netwerkverkeersfilter in een virtuele machine vaststellen](diagnose-network-traffic-filter-problem.md) als u communicatieproblemen hebt en problemen met netwerkbeveiligingsgroepen wilt oplossen. 
* Meer informatie over het inschakelen van [stroom logboeken voor netwerk beveiligings groepen](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) voor het analyseren van netwerk verkeer naar en van resources die een gekoppelde netwerk beveiligings groep hebben.
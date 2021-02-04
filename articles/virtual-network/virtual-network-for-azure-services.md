---
title: Virtueel netwerk voor Azure-services
titlesuffix: Azure Virtual Network
description: Meer informatie over het implementeren van speciale Azure-Services in een virtueel netwerk en meer informatie over de mogelijkheden die deze implementaties bieden.
services: virtual-network
documentationcenter: na
author: mohnader
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/06/2020
ms.author: kumud
ms.openlocfilehash: 2ae318f8372175b6e5988c8c07351e80cf3dcbce
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537861"
---
# <a name="deploy-dedicated-azure-services-into-virtual-networks"></a>Speciale Azure-Services implementeren in virtuele netwerken

Wanneer u toegewezen Azure-Services in een [virtueel netwerk](virtual-networks-overview.md)implementeert, kunt u met de service bronnen privé communiceren via privé-IP-adressen.

![Services die zijn geïmplementeerd in een virtueel netwerk](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Het implementeren van services binnen een virtueel netwerk biedt de volgende mogelijkheden:

- Resources binnen het virtuele netwerk kunnen met elkaar communiceren via privé-IP-adressen. Zo kunt u gegevens rechtstreeks overbrengen tussen HDInsight en SQL Server die worden uitgevoerd op een virtuele machine, in het virtuele netwerk.
- On-premises resources hebben toegang tot bronnen in een virtueel netwerk met behulp van privé-IP-adressen via een [site-to-site VPN (VPN gateway)](../vpn-gateway/design.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) of [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Virtuele netwerken kunnen worden gekoppeld om resources in de virtuele netwerken met [elkaar te laten](virtual-network-peering-overview.md) communiceren met behulp van privé-IP-adressen.
- Service-exemplaren in een virtueel netwerk worden doorgaans volledig beheerd door de Azure-service. Dit omvat het controleren van de status van de resources en schalen met laden.
- Service-exemplaren worden geïmplementeerd in een subnet in een virtueel netwerk. Binnenkomende en uitgaande netwerk toegang voor het subnet moet worden geopend via [netwerk beveiligings groepen](./network-security-groups-overview.md#network-security-groups), volgens de richt lijnen van de service.
- Bepaalde services leggen ook beperkingen op het subnet waarin ze worden geïmplementeerd, waardoor de toepassing van beleids regels wordt beperkt, de virtuele machines en service bronnen binnen hetzelfde subnet worden gedistribueerd of gecombineerd. Controleer bij elke service op de specifieke beperkingen, aangezien deze na verloop van tijd kunnen veranderen. Voor beelden van dergelijke services zijn Azure NetApp Files, toegewezen HSM, Azure Container Instances App Service. 
- Services kunnen eventueel een [gedelegeerd subnet](virtual-network-manage-subnet.md#add-a-subnet) vereisen als een expliciete id die een subnet kan hosten voor een bepaalde service. Door het delegeren van Services krijgen expliciete machtigingen voor het maken van servicespecifieke bronnen in het gedelegeerde subnet.
- Bekijk een voor beeld van een REST API antwoord op een [virtueel netwerk met een overgedragen subnet](/rest/api/virtualnetwork/virtualnetworks/get#get-virtual-network-with-a-delegated-subnet). Een uitgebreide lijst met services die gebruikmaken van het gedelegeerde subnet model kan worden verkregen via de [beschik bare delegaties](/rest/api/virtualnetwork/availabledelegations/list) -API.

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Services die kunnen worden geïmplementeerd in een virtueel netwerk

|Categorie|Service| Toegewezen<sup>1</sup> subnet
|-|-|-|
| Compute | Virtuele machines: [Linux](/previous-versions/azure/virtual-machines/linux/infrastructure-example?toc=%2fazure%2fvirtual-network%2ftoc.json) of [Windows](/previous-versions/azure/virtual-machines/windows/infrastructure-example?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Schaal sets voor virtuele machines](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Cloud service](/previous-versions/azure/reference/jj156091(v=azure.100)): alleen virtueel netwerk (klassiek)<br/> [Azure Batch](../batch/nodes-and-pools.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)| Nee <br/> Nee <br/> Nee <br/> Geen<sup>2</sup>
| Netwerk | [Application Gateway-WAF](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Firewall](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)  <br/> [Azure Bastion](../bastion/bastion-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Virtuele netwerk apparaten](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn)| Ja <br/> Ja <br/> Ja <br/> Ja <br/> Nee
|Gegevens|[RedisCache](../azure-cache-for-redis/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure SQL Managed Instance](../azure-sql/managed-instance/connectivity-architecture-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)| Ja <br/> Ja <br/> 
|Analyse | [Azure HDInsight](../hdinsight/hdinsight-plan-virtual-network-deployment.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks?toc=%2fazure%2fvirtual-network%2ftoc.json) |Geen<sup>2</sup> <br/> Geen<sup>2</sup> <br/> 
| Identiteit | [Azure Active Directory Domain Services](../active-directory-domain-services/tutorial-create-instance.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |No <br/>
| Containers | [Azure Kubernetes Service (AKS)](../aks/concepts-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure container instance (ACI)](https://www.aka.ms/acivnet)<br/>[Azure container service-engine](https://github.com/Azure/acs-engine) met Azure Virtual Network cni [-invoeg toepassing](https://github.com/Azure/acs-engine/tree/master/examples/vnet)<br/>[Azure Functions](../azure-functions/functions-networking-options.md#virtual-network-integration) |Geen<sup>2</sup><br/> Ja <br/><br/> Nee <br/> Ja
| Web | [API Management](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Web-apps](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[App Service-omgeving](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Ja <br/> Ja <br/> Ja <br/> Ja
| Zone | [Azure Dedicated HSM](../dedicated-hsm/index.yml?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Ja <br/> Ja <br/>
| Azure Spring Cloud | [Implementeren in een virtueel Azure-netwerk (VNet-injectie)](../spring-cloud/spring-cloud-tutorial-deploy-in-azure-virtual-network.md)<br/>| Yes <br/>
| | |

<sup>1</sup> ' dedicated ' impliceert dat alleen servicespecifieke resources kunnen worden geïmplementeerd in dit subnet en kan niet worden gecombineerd met de virtuele machine van de klant-VMSSs <br/> 
<sup>2</sup> het wordt aanbevolen als een best practice om deze services in een toegewezen subnet te hebben, maar dit is geen verplichte vereiste die door de service wordt opgelegd.
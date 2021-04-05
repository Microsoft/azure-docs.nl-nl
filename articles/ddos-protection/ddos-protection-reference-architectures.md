---
title: Azure DDoS Protection referentie architecturen
description: Meer informatie over Azure DDoS Protection-referentie architecturen.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: e5472620fe9b07d152a5325b0654044cb1505fd7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94992434"
---
# <a name="ddos-protection-reference-architectures"></a>DDoS Protection referentie architecturen

DDoS Protection Standard is ontworpen [voor services die zijn geïmplementeerd in een virtueel netwerk](../virtual-network/virtual-network-for-azure-services.md). Voor andere services is de standaard DDoS Protection Basic-service van toepassing. De volgende referentie architecturen zijn gerangschikt op scenario's, waarbij architectuur patronen samen worden gegroepeerd.

## <a name="virtual-machine-windowslinux-workloads"></a>Werk belastingen voor virtuele machines (Windows/Linux)

### <a name="application-running-on-load-balanced-vms"></a>Toepassing die wordt uitgevoerd op virtuele machines met taak verdeling

Deze referentie architectuur bevat een reeks beproefde procedures voor het uitvoeren van meerdere Windows-Vm's in een schaalset achter een load balancer, om de beschik baarheid en schaal baarheid te verbeteren. Deze architectuur kan worden gebruikt voor een staatloze werk belasting, zoals een webserver.

![Diagram van de referentie architectuur voor een toepassing die wordt uitgevoerd op virtuele machines met taak verdeling](./media/ddos-best-practices/image-9.png)

In deze architectuur wordt een werkbelasting verdeeld over meerdere VM-exemplaren. Er is één openbaar IP-adres en Internet verkeer wordt via een load balancer naar de virtuele machines gedistribueerd. DDoS Protection standaard is ingeschakeld in het virtuele netwerk van het Azure (Internet) load balancer waaraan het open bare IP-adres is gekoppeld.

De load balancer distribueert binnenkomende Internet aanvragen naar de VM-exemplaren. Met virtuele-machine schaal sets kan het aantal Vm's hand matig worden geschaald of uitgekozen, of automatisch op basis van vooraf gedefinieerde regels. Dit is belang rijk als de resource zich onder DDoS-aanval bevindt. Zie [dit artikel](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm)voor meer informatie over deze referentie architectuur.

### <a name="application-running-on-windows-n-tier"></a>Toepassing die wordt uitgevoerd op Windows N-tier

Er zijn veel manieren om een architectuur met meerdere lagen te implementeren. In het volgende diagram ziet u een typische webtoepassing met drie lagen. Deze architectuur bouwt voort op het artikel [virtuele machines met gelijke taak verdeling uitvoeren voor schaal baarheid en beschik baarheid](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm). De web- en bedrijfslagen gebruiken VM's met taakverdeling.

![Diagram van de referentie architectuur voor een toepassing die wordt uitgevoerd op Windows N-tier](./media/ddos-best-practices/image-10.png)

In deze architectuur is DDoS Protection standaard ingeschakeld op het virtuele netwerk. Alle open bare Ip's in het virtuele netwerk krijgen DDoS-beveiliging voor laag 3 en 4. Voor de beveiliging van laag 7 implementeert u Application Gateway in de WAF-SKU. Zie [dit artikel](/azure/architecture/reference-architectures/virtual-machines-windows/n-tier)voor meer informatie over deze referentie architectuur.

> [!NOTE]
> Scenario's waarin één virtuele machine wordt uitgevoerd achter een openbaar IP-adres, worden niet ondersteund.

### <a name="paas-web-application"></a>PaaS-webtoepassing

Deze referentie architectuur laat zien hoe een Azure App Service-toepassing wordt uitgevoerd in één regio. Deze architectuur bevat een reeks beproefde procedures voor een webtoepassing die gebruikmaakt van [Azure app service](https://azure.microsoft.com/documentation/services/app-service/) en [Azure SQL database](https://azure.microsoft.com/documentation/services/sql-database/).
Er is een stand-by-regio ingesteld voor failover-scenario's.

![Diagram van de referentie architectuur voor een PaaS-webtoepassing](./media/ddos-best-practices/image-11.png)

Azure Traffic Manager stuurt binnenkomende aanvragen naar Application Gateway in een van de regio's. Tijdens normale bewerkingen worden aanvragen gerouteerd naar Application Gateway in de actieve regio. Als deze regio niet beschikbaar is, wordt Traffic Manager failover naar Application Gateway in de stand-by-regio.

Al het verkeer van Internet dat bestemd is voor de webtoepassing, wordt via Traffic Manager doorgestuurd naar het [Application Gateway open bare IP-adres](../application-gateway/application-gateway-web-app-overview.md) . In dit scenario is de app service (Web-app) zelf niet rechtstreeks gericht en wordt beveiligd door Application Gateway. 

We raden u aan de Application Gateway WAF-SKU (modus voor komen) te configureren om te helpen beschermen tegen laag 7-aanvallen (HTTP/HTTPS/websockets). Daarnaast worden Web-Apps zodanig geconfigureerd dat [alleen verkeer van het Application Gateway](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/) IP-adres wordt geaccepteerd.

Zie [dit artikel](/azure/architecture/reference-architectures/app-service-web-app/multi-region)voor meer informatie over deze referentie architectuur.

## <a name="mitigation-for-non-web-paas-services"></a>Risico beperking voor niet-PaaS Services

### <a name="hdinsight-on-azure"></a>HDInsight op Azure

Deze referentie architectuur toont het configureren van DDoS Protection Standard voor een [Azure HDInsight-cluster](../hdinsight/index.yml). Zorg ervoor dat het HDInsight-cluster is gekoppeld aan een virtueel netwerk en dat DDoS Protection is ingeschakeld op het virtuele netwerk.

![De deel Vensters HDInsight en geavanceerde instellingen met virtuele netwerk instellingen](./media/ddos-best-practices/image-12.png)

![Selectie voor het inschakelen van DDoS Protection](./media/ddos-best-practices/image-13.png)

In deze architectuur wordt verkeer dat is bestemd voor het HDInsight-cluster via internet doorgestuurd naar het open bare IP-adres dat is gekoppeld aan de HDInsight-gateway load balancer. De gateway load balancer verzendt het verkeer vervolgens rechtstreeks naar de hoofd knooppunten of de worker-knoop punten. Omdat DDoS Protection standaard is ingeschakeld in het virtuele HDInsight-netwerk, krijgen alle open bare IP-adressen in het virtuele netwerk DDoS-beveiliging voor laag 3 en 4. Deze referentie architectuur kan worden gecombineerd met de referentie architecturen N-tier en meerdere regio's.

Meer informatie over deze referentie architectuur kunt u vinden in de documentatie [Azure HDInsight uitbreiden met azure Virtual Network](../hdinsight/hdinsight-plan-virtual-network-deployment.md?toc=%2fazure%2fvirtual-network%2ftoc.json) .


> [!NOTE]
> Azure App Service Environment voor PowerApps-of API-beheer in een virtueel netwerk met een openbaar IP-adres worden niet systeem eigen ondersteund.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [maken van een DDoS-beschermings plan](manage-ddos-protection.md).
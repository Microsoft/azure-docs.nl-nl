---
title: Netwerk verkeer in azure HDInsight beheren
description: Leer technieken voor het beheren van binnenkomend en uitgaand verkeer naar Azure HDInsight-clusters.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/02/2020
ms.openlocfilehash: 0cbda0b533a64e627bfeef9589ab95c4163ae73e
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943366"
---
# <a name="control-network-traffic-in-azure-hdinsight"></a>Netwerk verkeer in azure HDInsight beheren

Netwerk verkeer in een virtuele Azure-netwerken kan worden beheerd met behulp van de volgende methoden:

* Met **netwerk beveiligings groepen** (NSG) kunt u inkomend en uitgaand verkeer filteren op het netwerk. Zie het document [netwerk verkeer filteren met netwerk beveiligings groepen](../virtual-network/network-security-groups-overview.md) voor meer informatie.

* **Virtuele netwerk apparaten** (NVA) kunnen alleen worden gebruikt met uitgaand verkeer. Nva's repliceert de functionaliteit van apparaten, zoals firewalls en routers. Zie het document [netwerk apparaten](https://azure.microsoft.com/solutions/network-appliances) voor meer informatie.

Als beheerde service vereist HDInsight onbeperkte toegang tot de HDInsight-status-en beheer Services voor binnenkomend en uitgaand verkeer van het VNET. Wanneer u Nsg's gebruikt, moet u ervoor zorgen dat deze services nog steeds kunnen communiceren met het HDInsight-cluster.

![Diagram van HDInsight-entiteiten die zijn gemaakt in een aangepast Azure-VNET](./media/control-network-traffic/hdinsight-vnet-diagram.png)

## <a name="hdinsight-with-network-security-groups"></a>HDInsight met netwerk beveiligings groepen

Als u van plan bent om **netwerk beveiligings groepen** te gebruiken om netwerk verkeer te beheren, moet u de volgende acties uitvoeren voordat u HDInsight installeert:

1. Bepaal de Azure-regio die u wilt gebruiken voor HDInsight.

2. Bepaal welke service Tags vereist zijn voor HDInsight voor uw regio. Er zijn meerdere manieren om deze service tags te verkrijgen:
    1. Raadpleeg de lijst met gepubliceerde service tags in de [service tags voor de netwerk beveiligings groep (NSG) voor Azure HDInsight](hdinsight-service-tags.md). 
    2. Als uw regio niet aanwezig is in de lijst, gebruikt u de [service Tags detectie-API](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) om een servicetag voor uw regio te vinden.
    3. Als u de API niet kunt gebruiken, downloadt u het [JSON-bestand van de service label](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) en zoekt u naar de gewenste regio.


3. Maak of wijzig de netwerk beveiligings groepen voor het subnet waarop u HDInsight wilt installeren.

    * __Netwerk beveiligings groepen__: Allow __Inkomend__ verkeer op poort __443__ van de IP-adressen. Dit zorgt ervoor dat HDInsight-beheer Services het cluster kunnen bereiken van buiten het virtuele netwerk. Voor __Kafka rest-proxy__ clusters kunt u ook __Inkomend__ verkeer op poort __9400__ toestaan. Dit zorgt ervoor dat de Kafka REST-proxy server bereikbaar is.

Zie [overzicht van netwerk beveiligings groepen](../virtual-network/network-security-groups-overview.md)voor meer informatie over netwerk beveiligings groepen.

## <a name="controlling-outbound-traffic-from-hdinsight-clusters"></a>Uitgaand verkeer van HDInsight-clusters beheren

Zie voor meer informatie over het beheren van uitgaand verkeer van HDInsight-clusters [uitgaand netwerk verkeer configureren beperking voor Azure HDInsight-clusters](hdinsight-restrict-outbound-traffic.md).

### <a name="forced-tunneling-to-on-premises"></a>Geforceerde tunneling naar on-premises

Geforceerde tunneling is een door de gebruiker gedefinieerde routerings configuratie waarbij al het verkeer van een subnet wordt afgedwongen op een specifiek netwerk of locatie, zoals uw on-premises netwerk of firewall. Geforceerde tunneling van alle gegevens overdracht naar on-premises wordt _niet_ aanbevolen als gevolg van grote hoeveel heden gegevens overdracht en mogelijke gevolgen voor de prestaties.

Klanten die geforceerde tunneling willen instellen, moeten gebruikmaken van [aangepaste meta Stores](./hdinsight-use-external-metadata-stores.md) en de juiste connectiviteit van het subnet van het cluster of een on-premises netwerk instellen op deze aangepaste meta Stores.

Zie de [beperking uitgaand netwerk verkeer configureren voor Azure HDInsight-clusters](hdinsight-restrict-outbound-traffic.md)om een voor beeld te zien van de UDR-instelling met Azure firewall.

## <a name="required-ports"></a>Vereiste poorten

Als u van plan bent een **firewall** te gebruiken en u toegang te krijgen tot het cluster van buiten op bepaalde poorten, moet u mogelijk verkeer toestaan op de poorten die nodig zijn voor uw scenario. Standaard is geen speciaal filter van poorten nodig, zolang het Azure Management-verkeer dat in de vorige sectie wordt uitgelegd, het cluster op poort 443 mag bereiken.

Voor een lijst met poorten voor specifieke services, zie de [poorten die worden gebruikt door Apache Hadoop Services in HDInsight](hdinsight-hadoop-port-settings-for-services.md) -document.

Zie het scenario document van het [virtuele apparaat](../virtual-network/virtual-network-scenario-udr-gw-nva.md) voor meer informatie over Firewall regels voor virtuele apparaten.

## <a name="next-steps"></a>Volgende stappen

* Zie [virtuele netwerken voor Azure HDInsight-clusters maken](hdinsight-create-virtual-network.md)voor code voorbeelden en voor beelden van het maken van virtuele Azure-netwerken.
* Zie [Hdinsight verbinden met een on-premises netwerk](./connect-on-premises-network.md)voor een end-to-end-voor beeld van het configureren van hdinsight om verbinding te maken met een on-premises netwerk.
* Zie [overzicht van azure Virtual Network](../virtual-network/virtual-networks-overview.md)voor meer informatie over virtuele Azure-netwerken.
* Zie [netwerk beveiligings groepen](../virtual-network/network-security-groups-overview.md)voor meer informatie over netwerk beveiligings groepen.
* Zie door de gebruiker [gedefinieerde routes en door sturen via IP](../virtual-network/virtual-networks-udr-overview.md)voor meer informatie over door de gebruiker gedefinieerde routes.
* Zie [plan VNETs voor HDInsight](./hdinsight-plan-virtual-network-deployment.md)voor meer informatie over virtuele netwerken.
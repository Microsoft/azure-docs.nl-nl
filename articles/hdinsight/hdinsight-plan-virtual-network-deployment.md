---
title: Een virtueel netwerk plannen voor Azure HDInsight
description: Meer informatie over het plannen van een Azure Virtual Network-implementatie om HDInsight te verbinden met andere cloud resources of resources in uw Data Center.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 01/12/2021
ms.openlocfilehash: 429f0b2ffe486279a1c429fc7a153e6528642f54
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98131658"
---
# <a name="plan-a-virtual-network-for-azure-hdinsight"></a>Een virtueel netwerk plannen voor Azure HDInsight

Dit artikel bevat achtergrond informatie over het gebruik van [Azure Virtual Networks](../virtual-network/virtual-networks-overview.md) (VNets) met Azure HDInsight. Ook worden ontwerp-en implementatie beslissingen beschreven die moeten worden uitgevoerd voordat u een virtueel netwerk kunt implementeren voor uw HDInsight-cluster. Zodra de plannings fase is voltooid, kunt u door gaan met het [maken van virtuele netwerken voor Azure HDInsight-clusters](hdinsight-create-virtual-network.md). Zie [IP-adressen voor hdinsight-beheer](hdinsight-management-ip-addresses.md)voor meer informatie over de IP-adressen voor hdinsight-beheer die nodig zijn om netwerk beveiligings groepen (nsg's) en door de gebruiker gedefinieerde routes correct te configureren.

Als u een Azure-Virtual Network gebruikt, worden de volgende scenario's ingeschakeld:

* Rechtstreeks verbinding maken met HDInsight vanuit een on-premises netwerk.
* Verbinding maken tussen HDInsight en gegevens archieven in een virtueel Azure-netwerk.
* Rechtstreeks toegang tot Apache Hadoop services die niet openbaar via internet beschikbaar zijn. Bijvoorbeeld Apache Kafka Api's of de Apache HBase Java API.

> [!IMPORTANT]
> Als u een HDInsight-cluster in een VNET maakt, worden er verschillende netwerk bronnen gemaakt, zoals Nic's en load balancers. Verwijder deze netwerk bronnen **niet** , omdat deze nodig zijn om het cluster goed te laten functioneren met het VNET.
>
> Na 28 februari 2019 worden de netwerk bronnen (zoals Nic's, LBs, enzovoort) voor nieuwe HDInsight-clusters die zijn gemaakt in een VNET ingericht in dezelfde resource groep voor HDInsight-clusters. Voorheen werden deze resources ingericht in de VNET-resource groep. De huidige actieve clusters en de clusters die zijn gemaakt zonder VNET, worden niet gewijzigd.

## <a name="planning"></a>Planning

Hieronder vindt u de vragen die u moet beantwoorden bij het plannen van HDInsight in een virtueel netwerk:

* Moet u HDInsight installeren in een bestaand virtueel netwerk? Of maakt u een nieuw netwerk?

    Als u een bestaand virtueel netwerk gebruikt, moet u de netwerk configuratie mogelijk wijzigen voordat u HDInsight kunt installeren. Zie de sectie [HDInsight toevoegen aan een bestaand virtueel netwerk](#existingvnet) voor meer informatie.

* Wilt u het virtuele netwerk met HDInsight koppelen aan een ander virtueel netwerk of uw on-premises netwerk?

    Als u eenvoudig met resources over netwerken wilt werken, moet u mogelijk een aangepaste DNS-server maken en door sturen via DNS configureren. Zie de sectie [meerdere netwerken verbinden](#multinet) voor meer informatie.

* Wilt u het binnenkomende of uitgaande verkeer beperken of omleiden naar HDInsight?

    HDInsight moet onbeperkte communicatie met specifieke IP-adressen in het Azure-Data Center hebben. Er zijn ook verschillende poorten die moeten worden toegestaan via firewalls voor client communicatie. Zie [netwerk verkeer beheren](./control-network-traffic.md)voor meer informatie.

## <a name="add-hdinsight-to-an-existing-virtual-network"></a><a id="existingvnet"></a>HDInsight toevoegen aan een bestaand virtueel netwerk

Volg de stappen in deze sectie om te ontdekken hoe u een nieuwe HDInsight kunt toevoegen aan een bestaand Azure-Virtual Network.

> [!NOTE]  
> - U kunt een bestaand HDInsight-cluster niet toevoegen aan een virtueel netwerk.
> - Het VNET en het cluster dat wordt gemaakt, moeten zich in hetzelfde abonnement benemen.

1. Gebruikt u een klassiek of Resource Manager-implementatie model voor het virtuele netwerk?

    Voor HDInsight 3,4 en hoger is een virtueel netwerk van Resource Manager vereist. Eerdere versies van HDInsight vereist een klassiek virtueel netwerk.

    Als uw bestaande netwerk een klassiek virtueel netwerk is, moet u een virtueel netwerk van Resource Manager maken en vervolgens de twee verbinden. [Klassieke VNets verbinden met nieuwe VNets](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

    Zodra het lid is van het Resource Manager-netwerk, kan HDInsight communiceren met resources in het klassieke netwerk.

2. Gebruikt u netwerk beveiligings groepen, door de gebruiker gedefinieerde routes of Virtual Network apparaten om het verkeer naar of van het virtuele netwerk te beperken?

    Als beheerde service vereist HDInsight onbeperkte toegang tot meerdere IP-adressen in het Azure-Data Center. Werk alle bestaande netwerk beveiligings groepen of door de gebruiker gedefinieerde routes bij om communicatie met deze IP-adressen toe te staan.

    HDInsight host meerdere services, die gebruikmaken van verschillende poorten. Verkeer naar deze poorten blok keren. Zie de sectie Beveiliging voor een lijst met poorten die via virtuele-toestel firewalls kunnen worden toegestaan.

    Gebruik de volgende Azure PowerShell of Azure CLI-opdrachten om uw bestaande beveiligings configuratie te vinden:

    * Netwerkbeveiligingsgroepen

        Vervang door `RESOURCEGROUP` de naam van de resource groep die het virtuele netwerk bevat en voer de volgende opdracht in:

        ```powershell
        Get-AzNetworkSecurityGroup -ResourceGroupName  "RESOURCEGROUP"
        ```

        ```azurecli
        az network nsg list --resource-group RESOURCEGROUP
        ```

        Zie het document [problemen met netwerk beveiligings groepen oplossen](../virtual-network/diagnose-network-traffic-filter-problem.md) voor meer informatie.

        > [!IMPORTANT]  
        > De regels voor de netwerk beveiligings groep worden op volg orde toegepast op basis van regel prioriteit. De eerste regel die overeenkomt met het verkeers patroon wordt toegepast en er worden geen andere regels voor dat verkeer toegepast. Bestel regels van de meest strikte tot minst strikte. Zie het document [netwerk verkeer filteren met netwerk beveiligings groepen](../virtual-network/network-security-groups-overview.md) voor meer informatie.

    * Door de gebruiker gedefinieerde routes

        Vervang door `RESOURCEGROUP` de naam van de resource groep die het virtuele netwerk bevat en voer de volgende opdracht in:

        ```powershell
        Get-AzRouteTable -ResourceGroupName "RESOURCEGROUP"
        ```

        ```azurecli
        az network route-table list --resource-group RESOURCEGROUP
        ```

        Zie het document [problemen met routes oplossen](../virtual-network/diagnose-network-routing-problem.md) voor meer informatie.

3. Maak een HDInsight-cluster en selecteer de Azure-Virtual Network tijdens de configuratie. Volg de stappen in de volgende documenten om inzicht te krijgen in het proces voor het maken van het cluster:

    * [HDInsight maken met Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md)
    * [HDInsight maken met Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
    * [HDInsight maken met klassieke Azure-CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
    * [HDInsight maken met behulp van een Azure Resource Manager sjabloon](hdinsight-hadoop-create-linux-clusters-arm-templates.md)

   > [!IMPORTANT]  
   > Het toevoegen van HDInsight aan een virtueel netwerk is een optionele configuratie stap. Zorg ervoor dat u het virtuele netwerk selecteert bij het configureren van het cluster.

## <a name="connecting-multiple-networks"></a><a id="multinet"></a>Verbinding maken met meerdere netwerken

De grootste uitdaging met een configuratie met meerdere netwerken is naam omzetting tussen de netwerken.

Azure biedt naam omzetting voor Azure-Services die zijn geïnstalleerd in een virtueel netwerk. Met deze ingebouwde naam omzetting kan HDInsight verbinding maken met de volgende bronnen met behulp van een Fully Qualified Domain Name (FQDN):

* Alle bronnen die beschikbaar zijn op internet. Bijvoorbeeld, microsoft.com, windowsupdate.com.

* Een resource die zich in dezelfde Azure-Virtual Network bevindt, met behulp van de __interne DNS-naam__ van de resource. Als u bijvoorbeeld de standaard naam omzetting gebruikt, zijn de volgende voor beelden van interne DNS-namen die zijn toegewezen aan HDInsight worker-knoop punten:

  * wn0-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net
  * wn2-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net

    Deze knoop punten kunnen rechtstreeks communiceren met elkaar en andere knoop punten in HDInsight met behulp van interne DNS-namen.

Met de standaard naam omzetting kan HDInsight de namen van resources __niet__ omzetten in netwerken die zijn gekoppeld aan het virtuele netwerk. Het is bijvoorbeeld gebruikelijk om uw on-premises netwerk samen te voegen met het virtuele netwerk. Met alleen de standaard naam omzetting heeft HDInsight geen toegang tot bronnen in het on-premises netwerk op naam. Het tegenovergestelde is ook waar, resources in uw on-premises netwerk hebben geen toegang tot resources in het virtuele netwerk op naam.

> [!WARNING]  
> U moet de aangepaste DNS-server maken en het virtuele netwerk zo configureren dat deze wordt gebruikt voordat het HDInsight-cluster wordt gemaakt.

Voor het inschakelen van naam omzetting tussen het virtuele netwerk en bronnen in samengevoegde netwerken moet u de volgende acties uitvoeren:

1. Maak een aangepaste DNS-server in de Azure-Virtual Network waar u HDInsight wilt installeren.

2. Configureer het virtuele netwerk voor gebruik van de aangepaste DNS-server.

3. Zoek het aan Azure toegewezen DNS-achtervoegsel voor het virtuele netwerk. Deze waarde is vergelijkbaar met `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` . Zie voor meer informatie over het vinden van het DNS-achtervoegsel het gedeelte [voor beeld: aangepaste DNS](hdinsight-create-virtual-network.md#example-dns) .

4. Het door sturen van de DNS-servers configureren. De configuratie is afhankelijk van het type extern netwerk.

   * Als het externe netwerk een on-premises netwerk is, configureert u DNS als volgt:

     * __Aangepaste DNS__ (in het virtuele netwerk):

         * Hiermee worden aanvragen voor het DNS-achtervoegsel van het virtuele netwerk doorgestuurd naar de recursieve resolver van Azure (168.63.129.16). Azure verwerkt aanvragen voor bronnen in het virtuele netwerk

         * Alle andere aanvragen door sturen naar de on-premises DNS-server. De on-premises DNS verwerkt alle andere aanvragen voor naam omzetting, zelfs aanvragen voor Internet bronnen zoals Microsoft.com.

     * __On-premises DNS__: aanvragen door sturen voor het DNS-achtervoegsel van het virtuele netwerk naar de aangepaste DNS-server. De aangepaste DNS-server wordt vervolgens doorgestuurd naar de recursieve resolver van Azure.

       Deze configuratie routes aanvragen voor volledig gekwalificeerde domein namen die het DNS-achtervoegsel van het virtuele netwerk bevatten naar de aangepaste DNS-server. Alle andere aanvragen (zelfs voor open bare Internet adressen) worden verwerkt door de on-premises DNS-server.

   * Als het externe netwerk een andere Azure Virtual Network is, configureert u DNS als volgt:

     * __Aangepaste DNS__ (in elk virtueel netwerk):

         * Aanvragen voor het DNS-achtervoegsel van de virtuele netwerken worden doorgestuurd naar de aangepaste DNS-servers. De DNS in elk virtueel netwerk is verantwoordelijk voor het oplossen van bronnen binnen het netwerk.

         * Alle andere aanvragen door sturen naar de recursieve resolver van Azure. De recursieve resolver is verantwoordelijk voor het oplossen van lokale en Internet bronnen.

       De DNS-server voor elk netwerk stuurt aanvragen door naar de andere, op basis van DNS-achtervoegsel. Andere aanvragen worden opgelost met de recursieve resolver van Azure.

     Zie voor een voor beeld van elke configuratie de sectie [voor beeld: aangepaste DNS](hdinsight-create-virtual-network.md#example-dns) .

Zie voor meer informatie de [naam omzetting voor vm's en rollen instanties](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) document.

## <a name="directly-connect-to-apache-hadoop-services"></a>Rechtstreeks verbinding maken met Apache Hadoop Services

U kunt verbinding maken met het cluster op `https://CLUSTERNAME.azurehdinsight.net` . Dit adres maakt gebruik van een openbaar IP, die mogelijk niet bereikbaar is als u Nsg's hebt gebruikt om het inkomende verkeer van Internet te beperken. Wanneer u het cluster in een VNet implementeert, kunt u dit ook openen via het persoonlijke eind punt `https://CLUSTERNAME-int.azurehdinsight.net` . Dit eind punt wordt omgezet in een persoonlijk IP-adres in het VNet voor toegang tot het cluster.

Als u verbinding wilt maken met Apache Ambari en andere webpagina's via het virtuele netwerk, gebruikt u de volgende stappen:

1. Gebruik een van de volgende methoden om de interne volledig gekwalificeerde domein namen (FQDN) van de HDInsight-cluster knooppunten te detecteren:

    Vervang door `RESOURCEGROUP` de naam van de resource groep die het virtuele netwerk bevat en voer de volgende opdracht in:

    ```powershell
    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP" | where-object {$_.Name -like "*node*"}

    $nodes = @()
    foreach($nic in $clusterNICs) {
        $node = new-object System.Object
        $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
        $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
        $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
        $nodes += $node
    }
    $nodes | sort-object Type
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

    Zoek in de lijst met knoop punten de FQDN voor de hoofd knooppunten en gebruik de FQDN-namen om verbinding te maken met Ambari en andere webservices. U kunt bijvoorbeeld gebruiken `http://<headnode-fqdn>:8080` om toegang te krijgen tot Ambari.

    > [!IMPORTANT]  
    > Sommige services die op de hoofd knooppunten worden gehost, zijn op één knoop punt tegelijk actief. Als u probeert toegang te krijgen tot een service op één hoofd knooppunt en er wordt een 404-fout geretourneerd, gaat u naar het andere hoofd knooppunt.

2. Voor het bepalen van het knoop punt en de poort waarop een service beschikbaar is, raadpleegt u de [poorten die worden gebruikt door Hadoop-Services in HDInsight](./hdinsight-hadoop-port-settings-for-services.md) -document.

## <a name="load-balancing"></a>Taakverdeling

Wanneer u een HDInsight-cluster maakt, wordt er ook een load balancer gemaakt. Het type van deze load balancer bevindt zich op het niveau van de [basis-SKU](../load-balancer/skus.md), met bepaalde beperkingen. Een van deze beperkingen is dat als u twee virtuele netwerken in verschillende regio's hebt, u geen verbinding kunt maken met Basic load balancers. Zie de [Veelgestelde vragen over virtuele netwerken: beperkingen voor wereld wijde vnet-peering](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* Zie [virtuele netwerken voor Azure HDInsight-clusters maken](hdinsight-create-virtual-network.md)voor code voorbeelden en voor beelden van het maken van virtuele Azure-netwerken.
* Zie [Hdinsight verbinden met een on-premises netwerk](./connect-on-premises-network.md)voor een end-to-end-voor beeld van het configureren van hdinsight om verbinding te maken met een on-premises netwerk.
* Zie [overzicht van azure Virtual Network](../virtual-network/virtual-networks-overview.md)voor meer informatie over virtuele Azure-netwerken.
* Zie [netwerk beveiligings groepen](../virtual-network/network-security-groups-overview.md)voor meer informatie over netwerk beveiligings groepen.
* Zie door de gebruiker [gedefinieerde routes en door sturen via IP](../virtual-network/virtual-networks-udr-overview.md)voor meer informatie over door de gebruiker gedefinieerde routes.
* Zie [netwerk verkeer beheren](./control-network-traffic.md)voor meer informatie over het beheren van verkeer met inbegrip van Firewall integratie.

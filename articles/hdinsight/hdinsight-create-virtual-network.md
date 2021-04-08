---
title: Virtuele netwerken voor Azure HDInsight-clusters maken
description: Meer informatie over het maken van een Azure-Virtual Network om HDInsight te verbinden met andere cloud resources of resources in uw Data Center.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 04/16/2020
ms.openlocfilehash: 43d57eac94cabb5c648183911e0c0bf72889946d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946074"
---
# <a name="create-virtual-networks-for-azure-hdinsight-clusters"></a>Virtuele netwerken voor Azure HDInsight-clusters maken

In dit artikel vindt u voor beelden en code voorbeelden voor het maken en configureren van [Azure Virtual Networks](../virtual-network/virtual-networks-overview.md). Voor gebruik met Azure HDInsight-clusters. Gedetailleerde voor beelden van het maken van netwerk beveiligings groepen (Nsg's) en het configureren van DNS worden weer gegeven.

Zie [een virtueel netwerk voor Azure Hdinsight plannen](hdinsight-plan-virtual-network-deployment.md)voor achtergrond informatie over het gebruik van virtuele netwerken met Azure hdinsight.

## <a name="prerequisites-for-code-samples-and-examples"></a>Vereisten voor code voorbeelden en voor beelden

Voordat u een van de code voorbeelden in dit artikel uitvoert, hebt u een goed idee van TCP/IP-netwerken. Als u niet bekend bent met TCP/IP-netwerken, raadpleegt u iemand voordat u wijzigingen aanbrengt in productie netwerken.

Andere vereisten voor de voor beelden in dit artikel zijn onder andere de volgende items:

* Als u Power shell gebruikt, moet u de [AZ-module](/powershell/azure/)installeren.
* Als u Azure CLI wilt gebruiken en nog niet hebt geïnstalleerd, raadpleegt u [de Azure cli installeren](/cli/azure/install-azure-cli).

> [!IMPORTANT]  
> Als u op zoek bent naar stapsgewijze instructies voor het verbinden van HDInsight met uw on-premises netwerk met behulp van een Azure Virtual Network, raadpleegt u de [verbinding Hdinsight koppelen aan uw on-premises netwerk](connect-on-premises-network.md) document.

## <a name="example-network-security-groups-with-hdinsight"></a><a id="hdinsight-nsg"></a>Voor beeld: netwerk beveiligings groepen met HDInsight

In de voor beelden in deze sectie wordt uitgelegd hoe u regels voor netwerk beveiligings groepen maakt. Met de regels kan HDInsight communiceren met de Azure-beheer Services. Voordat u de voor beelden gebruikt, past u de IP-adressen aan zodat deze overeenkomen met de Azure-regio die u gebruikt. U kunt deze informatie vinden in het [IP-adres van HDInsight-beheer](hdinsight-management-ip-addresses.md).

### <a name="azure-resource-management-template"></a>Azure resource management-sjabloon

Met de volgende resource management-sjabloon wordt een virtueel netwerk gemaakt waarmee inkomend verkeer kan worden beperkt, maar verkeer van de IP-adressen die worden vereist door HDInsight. Met deze sjabloon wordt ook een HDInsight-cluster in het virtuele netwerk gemaakt.

* [Een beveiligd Azure-Virtual Network en een HDInsight Hadoop-cluster implementeren](https://azure.microsoft.com/resources/templates/101-hdinsight-secure-vnet/)

### <a name="azure-powershell"></a>Azure PowerShell

Gebruik het volgende Power shell-script om een virtueel netwerk te maken waarmee inkomend verkeer wordt beperkt en verkeer van de IP-adressen voor de Europa-noord regio wordt toegestaan.

> [!IMPORTANT]  
> Wijzig de IP-adressen voor `hdirule1` en `hdirule2` in dit voor beeld zodat deze overeenkomen met de Azure-regio die u gebruikt. U vindt deze informatie over [HDInsight-beheer-IP-adressen](hdinsight-management-ip-addresses.md).

```azurepowershell
$vnetName = "Replace with your virtual network name"
$resourceGroupName = "Replace with the resource group the virtual network is in"
$subnetName = "Replace with the name of the subnet that you plan to use for HDInsight"

# Get the Virtual Network object
$vnet = Get-AzVirtualNetwork `
    -Name $vnetName `
    -ResourceGroupName $resourceGroupName

# Get the region the Virtual network is in.
$location = $vnet.Location

# Get the subnet object
$subnet = $vnet.Subnets | Where-Object Name -eq $subnetName

# Create a Network Security Group.
# And add exemptions for the HDInsight health and management services.
$nsg = New-AzNetworkSecurityGroup `
    -Name "hdisecure" `
    -ResourceGroupName $resourceGroupName `
    -Location $location `
    | Add-AzNetworkSecurityRuleConfig `
        -name "hdirule1" `
        -Description "HDI health and management address 52.164.210.96" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "52.164.210.96" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 300 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule2" `
        -Description "HDI health and management 13.74.153.132" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "13.74.153.132" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 301 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule3" `
        -Description "HDI health and management 168.61.49.99" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.49.99" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 302 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule4" `
        -Description "HDI health and management 23.99.5.239" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "23.99.5.239" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 303 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule5" `
        -Description "HDI health and management 168.61.48.131" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.48.131" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 304 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule6" `
        -Description "HDI health and management 138.91.141.162" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "138.91.141.162" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 305 `
        -Direction Inbound `

# Set the changes to the security group
Set-AzNetworkSecurityGroup -NetworkSecurityGroup $nsg

# Apply the NSG to the subnet
Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name $subnetName `
    -AddressPrefix $subnet.AddressPrefix `
    -NetworkSecurityGroup $nsg
$vnet | Set-AzVirtualNetwork
```

In dit voor beeld ziet u hoe u regels kunt toevoegen om binnenkomend verkeer op de vereiste IP-adressen toe te staan. Het bevat geen regel voor het beperken van de inkomende toegang vanuit andere bronnen. De volgende code laat zien hoe u SSH-toegang via internet kunt inschakelen:

```azurepowershell
Get-AzNetworkSecurityGroup -Name hdisecure -ResourceGroupName RESOURCEGROUP |
Add-AzNetworkSecurityRuleConfig -Name "SSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 306 -Direction Inbound
```

### <a name="azure-cli"></a>Azure CLI

Gebruik de volgende stappen om een virtueel netwerk te maken dat inkomend verkeer beperkt, maar dat verkeer van de IP-adressen die worden vereist door HDInsight.

1. Gebruik de volgende opdracht om een nieuwe netwerk beveiligings groep te maken met de naam `hdisecure` . Vervang door `RESOURCEGROUP` de resource groep die de Azure-Virtual Network bevat. Vervang door `LOCATION` de locatie (regio) waarin de groep is gemaakt.

    ```azurecli
    az network nsg create -g RESOURCEGROUP -n hdisecure -l LOCATION
    ```

    Zodra de groep is gemaakt, ontvangt u informatie over de nieuwe groep.

2. Gebruik de volgende regel om regels toe te voegen aan de nieuwe netwerk beveiligings groep waarmee binnenkomende communicatie op poort 443 van de Azure HDInsight Health and Management-service is toegestaan. Vervang door `RESOURCEGROUP` de naam van de resource groep die de Azure-Virtual Network bevat.

    > [!IMPORTANT]  
    > Wijzig de IP-adressen voor `hdirule1` en `hdirule2` in dit voor beeld zodat deze overeenkomen met de Azure-regio die u gebruikt. U kunt deze informatie vinden in het [IP-adres van HDInsight-beheer](hdinsight-management-ip-addresses.md).

    ```azurecli
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "52.164.210.96" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "13.74.153.132" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule3 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule4 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule6 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 305 --direction "Inbound"
    ```

3. Als u de unieke id voor deze netwerk beveiligings groep wilt ophalen, gebruikt u de volgende opdracht:

    ```azurecli
    az network nsg show -g RESOURCEGROUP -n hdisecure --query "id"
    ```

    Met deze opdracht wordt een waarde geretourneerd die vergelijkbaar is met de volgende tekst:

    ```output
    "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

4. Gebruik de volgende opdracht om de netwerk beveiligings groep toe te passen op een subnet. Vervang de `GUID` waarden en door `RESOURCEGROUP` het resultaat van de vorige stap. Vervang `VNETNAME` en door `SUBNETNAME` de naam van het virtuele netwerk en het subnet dat u wilt maken.

    ```azurecli
    az network vnet subnet update -g RESOURCEGROUP --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

    Wanneer deze opdracht is voltooid, kunt u HDInsight installeren in de Virtual Network.

Deze stappen openen alleen toegang tot de HDInsight-status-en beheer service in de Azure-Cloud. Andere toegang tot het HDInsight-cluster van buiten het Virtual Network wordt geblokkeerd. Als u toegang van buiten het virtuele netwerk wilt inschakelen, moet u extra regels voor de netwerk beveiligings groep toevoegen.

De volgende code laat zien hoe u SSH-toegang via internet kunt inschakelen:

```azurecli
az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n ssh --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 306 --direction "Inbound"
```

## <a name="example-dns-configuration"></a><a id="example-dns"></a> Voor beeld: DNS-configuratie

### <a name="name-resolution-between-a-virtual-network-and-a-connected-on-premises-network"></a>Naam omzetting tussen een virtueel netwerk en een verbonden on-premises netwerk

In dit voor beeld worden de volgende veronderstellingen gemaakt:

* U hebt een Azure-Virtual Network die is verbonden met een on-premises netwerk via een VPN-gateway.

* De aangepaste DNS-server in het virtuele netwerk wordt Linux of UNIX als besturings systeem uitgevoerd.

* [BIND](https://www.isc.org/downloads/bind/) is geïnstalleerd op de aangepaste DNS-server.

Op de aangepaste DNS-server in het virtuele netwerk:

1. Gebruik Azure PowerShell of Azure CLI om het DNS-achtervoegsel van het virtuele netwerk te vinden:

    Vervang door `RESOURCEGROUP` de naam van de resource groep die het virtuele netwerk bevat en voer de volgende opdracht in:

    ```azurepowershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

1. Op de aangepaste DNS-server voor het virtuele netwerk gebruikt u de volgende tekst als de inhoud van het `/etc/bind/named.conf.local` bestand:

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {168.63.129.16;}; # Azure recursive resolver
    };
    ```

    Vervang de `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` waarde door het DNS-achtervoegsel van het virtuele netwerk.

    Deze configuratie routeert alle DNS-aanvragen voor het DNS-achtervoegsel van het virtuele netwerk naar de recursieve resolver van Azure.

1. Op de aangepaste DNS-server voor het virtuele netwerk gebruikt u de volgende tekst als de inhoud van het `/etc/bind/named.conf.options` bestand:

    ```
    // Clients to accept requests from
    // TODO: Add the IP range of the joined network to this list
    acl goodclients {
        10.0.0.0/16; # IP address range of the virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            # All other requests are sent to the following
            forwarders {
                192.168.0.1; # Replace with the IP address of your on-premises DNS server
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```
    
    * Vervang de `10.0.0.0/16` waarde door het IP-adres bereik van het virtuele netwerk. Met deze vermelding kunnen de naam omzetting aanvragen adressen binnen dit bereik.

    * Voeg het IP-adres bereik van het on-premises netwerk toe aan de `acl goodclients { ... }` sectie.  vermeldingen kunnen aanvragen voor naam omzetting van resources in het on-premises netwerk.
    
    * Vervang de waarde `192.168.0.1` door het IP-adres van uw on-premises DNS-server. Met deze vermelding worden alle andere DNS-aanvragen gerouteerd naar de on-premises DNS-server.

1. Als u de configuratie wilt gebruiken, start u BIND opnieuw. Bijvoorbeeld `sudo service bind9 restart`.

1. Een voorwaardelijke forwarder toevoegen aan de on-premises DNS-server. Configureer de voorwaardelijke doorstuur server voor het verzenden van aanvragen voor het DNS-achtervoegsel uit stap 1 naar de aangepaste DNS-servers.

    > [!NOTE]  
    > Raadpleeg de documentatie voor uw DNS-software voor meer informatie over het toevoegen van een voorwaardelijke doorstuur server.

Nadat u deze stappen hebt voltooid, kunt u verbinding maken met bronnen in beide netwerken met behulp van FQDN-namen (Fully Qualified Domain names). U kunt nu HDInsight installeren in het virtuele netwerk.

### <a name="name-resolution-between-two-connected-virtual-networks"></a>Naam omzetting tussen twee verbonden virtuele netwerken

In dit voor beeld worden de volgende veronderstellingen gemaakt:

* U hebt twee virtuele netwerken van Azure die zijn verbonden via een VPN-gateway of peering.

* Op de aangepaste DNS-server in beide netwerken wordt Linux of UNIX als besturings systeem uitgevoerd.

* [BIND](https://www.isc.org/downloads/bind/) is geïnstalleerd op de aangepaste DNS-servers.

1. Gebruik Azure PowerShell of Azure CLI om het DNS-achtervoegsel van beide virtuele netwerken te vinden:

    Vervang door `RESOURCEGROUP` de naam van de resource groep die het virtuele netwerk bevat en voer de volgende opdracht in:

    ```azurepowershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. Gebruik de volgende tekst als de inhoud van het `/etc/bind/named.config.local` bestand op de aangepaste DNS-server. Breng deze wijziging aan op de aangepaste DNS-server in beide virtuele netwerken.

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The IP address of the DNS server in the other virtual network
    };
    ```

    Vervang de `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` waarde door het DNS-achtervoegsel van het __andere__ virtuele netwerk. Met deze vermelding worden aanvragen gerouteerd voor het DNS-achtervoegsel van het externe netwerk naar de aangepaste DNS in dat netwerk.

3. Gebruik op de aangepaste DNS-servers in beide virtuele netwerken de volgende tekst als de inhoud van het `/etc/bind/named.conf.options` bestand:

    ```
    // Clients to accept requests from
    acl goodclients {
        10.1.0.0/16; # The IP address range of one virtual network
        10.0.0.0/16; # The IP address range of the other virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            forwarders {
            168.63.129.16;   # Azure recursive resolver
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```

   Vervang de `10.0.0.0/16` waarden en door `10.1.0.0/16` de IP-adresbereiken van uw virtuele netwerken. Met deze vermelding kunnen resources in elk netwerk aanvragen van de DNS-servers maken.

    Aanvragen die niet voor de DNS-achtervoegsels van de virtuele netwerken (bijvoorbeeld microsoft.com) zijn, worden verwerkt door de recursieve resolver van Azure.

4. Als u de configuratie wilt gebruiken, start u BIND opnieuw. Bijvoorbeeld `sudo service bind9 restart` op beide DNS-servers.

Nadat u deze stappen hebt voltooid, kunt u verbinding maken met resources in het virtuele netwerk met behulp van Fully Qualified Domain names (FQDN). U kunt nu HDInsight installeren in het virtuele netwerk.

## <a name="next-steps"></a>Volgende stappen

* Zie [Hdinsight verbinden met een on-premises netwerk](./connect-on-premises-network.md)voor een volledig voor beeld van het configureren van hdinsight om verbinding te maken met een on-premises netwerk.
* Zie voor het configureren van Apache HBase-clusters in azure Virtual Networks, [Apache HBase-clusters maken op HDInsight in azure Virtual Network](hbase/apache-hbase-provision-vnet.md).
* Zie voor het configureren van Apache HBase geo-replicatie de [configuratie van Apache HBase-cluster replicatie in azure Virtual Networks](hbase/apache-hbase-replication.md).
* Zie [overzicht van azure Virtual Network](../virtual-network/virtual-networks-overview.md)voor meer informatie over virtuele Azure-netwerken.

* Zie [netwerk beveiligings groepen](../virtual-network/network-security-groups-overview.md)voor meer informatie over netwerk beveiligings groepen.

* Zie door de gebruiker [gedefinieerde routes en door sturen via IP](../virtual-network/virtual-networks-udr-overview.md)voor meer informatie over door de gebruiker gedefinieerde routes.

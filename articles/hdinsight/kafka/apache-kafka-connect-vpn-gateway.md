---
title: Verbinding maken met Kafka met behulp van virtuele netwerken-Azure HDInsight
description: Meer informatie over hoe u rechtstreeks verbinding maakt met Kafka op HDInsight via een Azure-Virtual Network. Informatie over het maken van verbinding met Kafka van ontwikkel-clients met een VPN-gateway of van clients in uw on-premises netwerk met behulp van een VPN-gateway apparaat.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-python
ms.date: 03/04/2020
ms.openlocfilehash: eb97e209bc7bb9bc1a15ecadc88593e4c0573b5c
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104870616"
---
# <a name="connect-to-apache-kafka-on-hdinsight-through-an-azure-virtual-network"></a>Verbinding maken met Apache Kafka in HDInsight via een virtueel Azure-netwerk

Meer informatie over hoe u rechtstreeks verbinding maakt met Apache Kafka op HDInsight via een Azure Virtual Network. Dit document bevat informatie over het maken van verbinding met Kafka met behulp van de volgende configuraties:

* Van bronnen in een on-premises netwerk. Deze verbinding wordt tot stand gebracht met behulp van een VPN-apparaat (software of hardware) op het lokale netwerk.
* Vanuit een ontwikkel omgeving met een VPN-software-client.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="architecture-and-planning"></a>Architectuur en planning

HDInsight staat geen directe verbinding met Kafka toe via het open bare Internet. In plaats daarvan moeten Kafka-clients (producenten en consumenten) een van de volgende verbindings methoden gebruiken:

* Voer de-client uit in hetzelfde virtuele netwerk als Kafka in HDInsight. Deze configuratie wordt gebruikt in het document [beginnen met Apache Kafka in HDInsight](apache-kafka-get-started.md) . De client wordt rechtstreeks uitgevoerd op de HDInsight-cluster knooppunten of op een andere virtuele machine in hetzelfde netwerk.

* Verbind een particulier netwerk, zoals uw on-premises netwerk, met het virtuele netwerk. Met deze configuratie kunnen clients in uw on-premises netwerk rechtstreeks met Kafka werken. Voer de volgende taken uit om deze configuratie in te scha kelen:

  1. Maak een virtueel netwerk.
  2. Maak een VPN-gateway die gebruikmaakt van een site-naar-site-configuratie. De configuratie die in dit document wordt gebruikt, maakt verbinding met een VPN-gateway apparaat in uw on-premises netwerk.
  3. Maak een DNS-server in het virtuele netwerk.
  4. Configureer door sturen tussen de DNS-server in elk netwerk.
  5. Maak een Kafka in HDInsight-cluster in het virtuele netwerk.

     Zie de sectie [verbinding maken met Apache Kafka van een on-premises netwerk](#on-premises) voor meer informatie.

* Verbind afzonderlijke machines met het virtuele netwerk met behulp van een VPN-gateway en VPN-client. Voer de volgende taken uit om deze configuratie in te scha kelen:

  1. Maak een virtueel netwerk.
  2. Maak een VPN-gateway die gebruikmaakt van een punt-naar-site-configuratie. Deze configuratie kan worden gebruikt met Windows-en MacOS-clients.
  3. Maak een Kafka in HDInsight-cluster in het virtuele netwerk.
  4. Kafka configureren voor het adverteren van IP-adressen. Met deze configuratie kan de client verbinding maken met behulp van IP-adressen van de broker in plaats van domein namen.
  5. Down load en gebruik de VPN-client in het ontwikkel systeem.

     Zie de sectie [verbinding maken met Apache Kafka met een VPN-client](#vpnclient) voor meer informatie.

     > [!WARNING]  
     > Deze configuratie wordt alleen aanbevolen voor ontwikkelings doeleinden vanwege de volgende beperkingen:
     >
     > * Elke client moet verbinding maken met behulp van een VPN-software-client.
     > * De VPN-client geeft geen aanvragen voor naam omzetting door aan het virtuele netwerk, dus u moet IP-adres Sering gebruiken om met Kafka te communiceren. Voor IP-communicatie is aanvullende configuratie vereist op het Kafka-cluster.

Zie [een virtueel netwerk voor Azure HDInsight-clusters plannen](../hdinsight-plan-virtual-network-deployment.md)voor meer informatie over het gebruik van HDInsight in een virtueel netwerk.

## <a name="connect-to-apache-kafka-from-an-on-premises-network"></a><a id="on-premises"></a> Verbinding maken met Apache Kafka vanuit een on-premises netwerk

Als u een Kafka-cluster wilt maken dat communiceert met uw on-premises netwerk, volgt u de stappen in het [Connect HDInsight to your on-premises Network](./../connect-on-premises-network.md) document.

> [!IMPORTANT]  
> Wanneer u het HDInsight-cluster maakt, selecteert u het cluster type __Kafka__ .

Met deze stappen maakt u de volgende configuratie:

* Azure Virtual Network
* Site-naar-site VPN-gateway
* Azure Storage-account (gebruikt door HDInsight)
* Kafka op HDInsight

Als u wilt controleren of een Kafka-client vanuit on-premises verbinding kan maken met het cluster, gebruikt u de stappen in het gedeelte [voor beeld: python-client](#python-client) .

## <a name="connect-to-apache-kafka-with-a-vpn-client"></a><a id="vpnclient"></a> Verbinding maken met Apache Kafka met een VPN-client

Gebruik de stappen in deze sectie om de volgende configuratie te maken:

* Azure Virtual Network
* Punt-naar-site-VPN-gateway
* Azure Storage-account (gebruikt door HDInsight)
* Kafka op HDInsight

1. Volg de stappen in het document [werken met zelfondertekende certificaten voor punt-naar-site-verbindingen](../../vpn-gateway/vpn-gateway-certificates-point-to-site.md) . In dit document worden de certificaten gemaakt die nodig zijn voor de gateway.

2. Open een Power shell-prompt en gebruik de volgende code om u aan te melden bij uw Azure-abonnement:

    ```powershell
    Connect-AzAccount
    # If you have multiple subscriptions, uncomment to set the subscription
    #Select-AzSubscription -SubscriptionName "name of your subscription"
    ```

3. Gebruik de volgende code om variabelen te maken die configuratie-informatie bevatten:

    ```powershell
    # Prompt for generic information
    $resourceGroupName = Read-Host "What is the resource group name?"
    $baseName = Read-Host "What is the base name? It is used to create names for resources, such as 'net-basename' and 'kafka-basename':"
    $location = Read-Host "What Azure Region do you want to create the resources in?"
    $rootCert = Read-Host "What is the file path to the root certificate? It is used to secure the VPN gateway."

    # Prompt for HDInsight credentials
    $adminCreds = Get-Credential -Message "Enter the HTTPS user name and password for the HDInsight cluster" -UserName "admin"
    $sshCreds = Get-Credential -Message "Enter the SSH user name and password for the HDInsight cluster" -UserName "sshuser"

    # Names for Azure resources
    $networkName = "net-$baseName"
    $clusterName = "kafka-$baseName"
    $storageName = "store$baseName" # Can't use dashes in storage names
    $defaultContainerName = $clusterName
    $defaultSubnetName = "default"
    $gatewaySubnetName = "GatewaySubnet"
    $gatewayPublicIpName = "GatewayIp"
    $gatewayIpConfigName = "GatewayConfig"
    $vpnRootCertName = "rootcert"
    $vpnName = "VPNGateway"

    # Network settings
    $networkAddressPrefix = "10.0.0.0/16"
    $defaultSubnetPrefix = "10.0.0.0/24"
    $gatewaySubnetPrefix = "10.0.1.0/24"
    $vpnClientAddressPool = "172.16.201.0/24"

    # HDInsight settings
    $HdiWorkerNodes = 4
    $hdiVersion = "3.6"
    $hdiType = "Kafka"
    ```

4. Gebruik de volgende code om de Azure-resource groep en het virtuele netwerk te maken:

    ```powershell
    # Create the resource group that contains everything
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create the subnet configuration
    $defaultSubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -AddressPrefix $defaultSubnetPrefix
    $gatewaySubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -AddressPrefix $gatewaySubnetPrefix

    # Create the subnet
    New-AzVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AddressPrefix $networkAddressPrefix `
        -Subnet $defaultSubnetConfig, $gatewaySubnetConfig

    # Get the network & subnet that were created
    $network = Get-AzVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName
    $gatewaySubnet = Get-AzVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -VirtualNetwork $network
    $defaultSubnet = Get-AzVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -VirtualNetwork $network

    # Set a dynamic public IP address for the gateway subnet
    $gatewayPublicIp = New-AzPublicIpAddress -Name $gatewayPublicIpName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AllocationMethod Dynamic
    $gatewayIpConfig = New-AzVirtualNetworkGatewayIpConfig -Name $gatewayIpConfigName `
        -Subnet $gatewaySubnet `
        -PublicIpAddress $gatewayPublicIp

    # Get the certificate info
    # Get the full path in case a relative path was passed
    $rootCertFile = Get-ChildItem $rootCert
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($rootCertFile)
    $certBase64 = [System.Convert]::ToBase64String($cert.RawData)
    $p2sRootCert = New-AzVpnClientRootCertificate -Name $vpnRootCertName `
        -PublicCertData $certBase64

    # Create the VPN gateway
    New-AzVirtualNetworkGateway -Name $vpnName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -IpConfigurations $gatewayIpConfig `
        -GatewayType Vpn `
        -VpnType RouteBased `
        -EnableBgp $false `
        -GatewaySku Standard `
        -VpnClientAddressPool $vpnClientAddressPool `
        -VpnClientRootCertificates $p2sRootCert
    ```

    > [!WARNING]  
    > Het kan enkele minuten duren voordat dit proces is voltooid.

5. Gebruik de volgende code om het Azure Storage-account en de BLOB-container te maken:

    ```powershell
    # Create the storage account
    New-AzStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageName `
        -SkuName Standard_GRS `
        -Location $location `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly 1

    # Get the storage account keys and create a context
    $defaultStorageKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName `
        -Name $storageName)[0].Value
    $storageContext = New-AzStorageContext -StorageAccountName $storageName `
        -StorageAccountKey $defaultStorageKey

    # Create the default storage container
    New-AzStorageContainer -Name $defaultContainerName `
        -Context $storageContext
    ```

6. Gebruik de volgende code om het HDInsight-cluster te maken:

    ```powershell
    # Create the HDInsight cluster
    New-AzHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $hdiWorkerNodes `
        -ClusterType $hdiType `
        -OSType Linux `
        -Version $hdiVersion `
        -HttpCredential $adminCreds `
        -SshCredential $sshCreds `
        -DefaultStorageAccountName "$storageName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageKey `
        -DefaultStorageContainer $defaultContainerName `
        -DisksPerWorkerNode 2 `
        -VirtualNetworkId $network.Id `
        -SubnetName $defaultSubnet.Id
    ```

   > [!WARNING]  
   > Dit proces duurt circa 15 minuten.

### <a name="configure-kafka-for-ip-advertising"></a>Kafka voor IP-reclame configureren

Standaard retourneert Apache Zookeeper de domein naam van de Kafka-Brokers naar clients. Deze configuratie werkt niet met de VPN-client, omdat deze geen naam omzetting kan gebruiken voor entiteiten in het virtuele netwerk. Gebruik voor deze configuratie de volgende stappen om Kafka te configureren voor het adverteren van IP-adressen in plaats van domein namen:

1. Ga in een webbrowser naar `https://CLUSTERNAME.azurehdinsight.net`. Vervang door `CLUSTERNAME` de naam van de Kafka in HDInsight-cluster.

    Wanneer u hierom wordt gevraagd, gebruikt u de HTTPS-gebruikers naam en het wacht woord voor het cluster. De Ambari-webgebruikersinterface voor het cluster wordt weer gegeven.

2. Als u informatie over Kafka wilt weer geven, selecteert u __Kafka__ in de lijst aan de linkerkant.

    :::image type="content" source="./media/apache-kafka-connect-vpn-gateway/select-kafka-service.png" alt-text="Service lijst met Kafka gemarkeerd" border="true":::

3. Als u de configuratie van Kafka wilt weer geven, selecteert u __configuraties__ in het bovenste midden.

    :::image type="content" source="./media/apache-kafka-connect-vpn-gateway/select-kafka-config1.png" alt-text="Configuratie van Apache Ambari Services" border="true":::

4. Als u de __Kafka-env-__ configuratie wilt vinden, voert u `kafka-env` in het veld __filter__ in de rechter bovenhoek in.

    :::image type="content" source="./media/apache-kafka-connect-vpn-gateway/search-for-kafka-env.png" alt-text="Kafka-configuratie voor Kafka-env" border="true":::

5. Als u Kafka wilt configureren voor het adverteren van IP-adressen, voegt u de volgende tekst toe onder aan het veld __Kafka-env-Temp late__ :

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Als u de interface wilt configureren waarop Kafka luistert, voert u `listeners` in het veld __filter__ in de rechter bovenhoek in.

7. Als u Kafka wilt configureren om te Luis teren op alle netwerk interfaces, wijzigt u de waarde in het veld __listeners__ in `PLAINTEXT://0.0.0.0:9092` .

8. Gebruik de knop __Opslaan__ om de configuratie wijzigingen op te slaan. Voer een tekst bericht in waarin de wijzigingen worden beschreven. Selecteer __OK__ zodra de wijzigingen zijn opgeslagen.

    :::image type="content" source="./media/apache-kafka-connect-vpn-gateway/save-configuration-button.png" alt-text="Apache Ambari-configuratie opslaan" border="true":::

9. Als u fouten wilt voor komen bij het opnieuw starten van Kafka, gebruikt u de knop __service acties__ en selecteert u __onderhouds modus inschakelen__. Selecteer OK om deze bewerking te volt ooien.

    :::image type="content" source="./media/apache-kafka-connect-vpn-gateway/turn-on-maintenance-mode.png" alt-text="Service acties, met ingeschakeld onderhoud inschakelen" border="true":::

10. Als u Kafka opnieuw wilt starten, gebruikt u de knop __opnieuw opstarten__ en selecteert u __alle betrokkenen opnieuw opstarten__. Bevestig het opnieuw opstarten en gebruik vervolgens de knop __OK__ nadat de bewerking is voltooid.

    :::image type="content" source="./media/apache-kafka-connect-vpn-gateway/restart-required-button.png" alt-text="Knop opnieuw opstarten met alle betrokken gemarkeerd voor opnieuw opstarten" border="true":::

11. Als u de onderhouds modus wilt uitschakelen, gebruikt u de knop __service acties__ en selecteert u __onderhouds modus uitschakelen__. Selecteer **OK** om deze bewerking te volt ooien.

### <a name="connect-to-the-vpn-gateway"></a>Verbinding maken met de VPN-gateway

Als u verbinding wilt maken met de VPN-gateway, gebruikt u de sectie __verbinding maken met Azure__ van het document [een punt-naar-site-verbinding configureren](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md#connect) .

## <a name="example-python-client"></a><a id="python-client"></a> Voor beeld: python-client

Gebruik de volgende stappen om een python-producent en-consument te maken en uit te voeren om de connectiviteit met Kafka te valideren:

1. Gebruik een van de volgende methoden om de Fully Qualified Domain Name (FQDN) en IP-adressen van de knoop punten in het Kafka-cluster op te halen:

    ```powershell
    $resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

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
    az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

    In dit script wordt ervan uitgegaan dat `$resourceGroupName` de naam van de Azure-resource groep met het virtuele netwerk is.

    Sla de geretourneerde informatie op voor gebruik in de volgende stappen.

2. Gebruik het volgende om de [Kafka-python-](https://kafka-python.readthedocs.io/) client te installeren:

    ```bash
    pip install kafka-python
    ```

3. Als u gegevens wilt verzenden naar Kafka, gebruikt u de volgende python-code:

   ```python
   from kafka import KafkaProducer
   # Replace the `ip_address` entries with the IP address of your worker nodes
   # NOTE: you don't need the full list of worker nodes, just one or two.
   producer = KafkaProducer(bootstrap_servers=['kafka_broker_1','kafka_broker_2'])
   for _ in range(50):
      producer.send('testtopic', b'test message')
   ```

    Vervang de `'kafka_broker'` vermeldingen door de adressen uit stap 1 in deze sectie:

   * Als u een __Software-VPN-client__ gebruikt, vervangt u de `kafka_broker` vermeldingen door het IP-adres van uw worker-knoop punten.

   * Als u __naam omzetting hebt ingeschakeld via een aangepaste DNS-server__, vervangt u de `kafka_broker` vermeldingen door de FQDN van de worker-knoop punten.

     > [!NOTE]
     > Met deze code wordt de teken reeks `test message` naar het onderwerp verzonden `testtopic` . De standaard configuratie van Kafka in HDInsight is het onderwerp te maken als deze niet bestaat.

4. Gebruik de volgende python-code om de berichten op te halen uit Kafka:

   ```python
   from kafka import KafkaConsumer
   # Replace the `ip_address` entries with the IP address of your worker nodes
   # Again, you only need one or two, not the full list.
   # Note: auto_offset_reset='earliest' resets the starting offset to the beginning
   #       of the topic
   consumer = KafkaConsumer(bootstrap_servers=['kafka_broker_1','kafka_broker_2'],auto_offset_reset='earliest')
   consumer.subscribe(['testtopic'])
   for msg in consumer:
     print (msg)
   ```

    Vervang de `'kafka_broker'` vermeldingen door de adressen uit stap 1 in deze sectie:

    * Als u een __Software-VPN-client__ gebruikt, vervangt u de `kafka_broker` vermeldingen door het IP-adres van uw worker-knoop punten.

    * Als u __naam omzetting hebt ingeschakeld via een aangepaste DNS-server__, vervangt u de `kafka_broker` vermeldingen door de FQDN van de worker-knoop punten.

## <a name="next-steps"></a>Volgende stappen

Zie het document [een implementatie van een virtueel netwerk plannen voor Azure hdinsight-clusters](../hdinsight-plan-virtual-network-deployment.md) voor meer informatie over het gebruik van HDInsight met een virtueel netwerk.

Zie de volgende documenten voor meer informatie over het maken van een Azure-Virtual Network met een punt-naar-site-VPN-gateway:

* [Een punt-naar-site-verbinding configureren met behulp van de Azure Portal](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)

* [Een punt-naar-site-verbinding configureren met behulp van Azure PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

Raadpleeg de volgende documenten voor meer informatie over het werken met Apache Kafka op HDInsight:

* [Aan de slag met Apache Kafka op HDInsight](apache-kafka-get-started.md)
* [Spiegeling gebruiken met Apache Kafka op HDInsight](apache-kafka-mirroring.md)

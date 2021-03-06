---
title: Een open bare load balancer maken met IPv6-Azure CLI
titleSuffix: Azure Load Balancer
description: Met dit leer traject kunt u aan de slag met het maken van een open bare load balancer met IPv6 met behulp van Azure CLI.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: IPv6, Azure load balancer, dual stack, openbaar IP, systeem eigen IPv6, mobiel, IOT
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18, devx-track-azurecli
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/25/2018
ms.author: allensu
ms.openlocfilehash: 714fb92e8757fed6e11a09528b60cda3e945175e
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102218890"
---
# <a name="create-a-public-load-balancer-with-ipv6-using-azure-cli"></a>Een open bare load balancer met IPv6 maken met behulp van Azure CLI

>[!NOTE] 
>In dit artikel wordt een inleidende IPv6-functie beschreven waarmee Basic load balancers zowel IPv4-als IPv6-connectiviteit kunnen bieden. Uitgebreide IPv6-connectiviteit is nu beschikbaar met [IPv6 voor Azure VNETs](../virtual-network/ipv6-overview.md) , die IPv6-connectiviteit integreert met uw virtuele netwerken en die belang rijke functies bevat zoals regels voor IPv6-netwerk beveiligings groepen, door IPv6 door de gebruiker gedefinieerde route ring, IPv6-basis en standaard taak verdeling en meer.  IPv6 voor Azure VNETs is de aanbevolen standaard voor IPv6-toepassingen in Azure. Zie [IPv6 voor Azure VNET Power shell-implementatie](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) 

Azure Load Balancer is een Layer-4 (TCP, UDP) load balancer. Load balancers bieden hoge Beschik baarheid door inkomend verkeer te verdelen over in orde zijnde service-exemplaren in Cloud Services of virtuele machines in een load balancerset. Load balancers kunnen deze services ook presen teren op meerdere poorten of meerdere IP-adressen of beide.

## <a name="example-deployment-scenario"></a>Voor beeld van implementatie scenario

In het volgende diagram ziet u de oplossing voor taak verdeling die wordt geïmplementeerd met behulp van de voorbeeld sjabloon die in dit artikel wordt beschreven.

![Load balancer-scenario](./media/load-balancer-ipv6-internet-cli/lb-ipv6-scenario-cli.png)

In dit scenario maakt u de volgende Azure-resources:

* Twee virtuele machines (Vm's)
* Een virtuele netwerk interface voor elke virtuele machine waaraan IPv4-en IPv6-adressen zijn toegewezen
* Een openbaar load balancer met een IPv4-en een openbaar IP-adres
* Een beschikbaarheidsset die de twee virtuele machines bevat
* Twee taakverdelings regels om de open bare Vip's toe te wijzen aan de privé-eind punten

## <a name="deploy-the-solution-by-using-azure-cli"></a>De oplossing implementeren met behulp van Azure CLI

De volgende stappen laten zien hoe u een open bare load balancer maakt met behulp van Azure CLI. Met CLI kunt u elk object afzonderlijk maken en configureren en ze vervolgens samen voegen om een resource te maken.

Als u een load balancer wilt implementeren, moet u de volgende objecten maken en configureren:

* **Front-end-IP-configuratie**: bevat open bare IP-adressen voor binnenkomend netwerk verkeer.
* **Back-end-adres groep**: bevat netwerk interfaces (nic's) voor de virtuele machines om netwerk verkeer van de Load Balancer te ontvangen.
* Taakverdelings **regels**: bevat regels die een open bare poort op de Load Balancer toewijzen aan een poort in de back-end-adres groep.
* **Binnenkomende NAT-regels**: bevat Network Address Translation (NAT) regels die een open bare poort op de Load Balancer toewijzen aan een poort voor een specifieke virtuele machine in de back-end-adres groep.
* **Tests**: bevat status tests die worden gebruikt om de beschik baarheid van exemplaren van virtuele machines in de back-end-adres groep te controleren.

## <a name="set-up-azure-cli"></a>Azure CLI instellen

In dit voor beeld voert u de Azure CLI-hulpprogram ma's uit in een Power shell-opdracht venster. Als u de Lees baarheid en hergebruik wilt verbeteren, gebruikt u de script mogelijkheden van Power shell en niet de Azure PowerShell-cmdlets.

1. [Installeer en configureer de Azure cli](/cli/azure/install-azure-cli) door de stappen in het gekoppelde artikel te volgen en u aan te melden bij uw Azure-account.

2. Power shell-variabelen instellen voor gebruik met de Azure CLI-opdrachten:

    ```powershell
    $subscriptionid = "########-####-####-####-############"  # enter subscription id
    $location = "southcentralus"
    $rgName = "pscontosorg1southctrlus09152016"
    $vnetName = "contosoIPv4Vnet"
    $vnetPrefix = "10.0.0.0/16"
    $subnet1Name = "clicontosoIPv4Subnet1"
    $subnet1Prefix = "10.0.0.0/24"
    $subnet2Name = "clicontosoIPv4Subnet2"
    $subnet2Prefix = "10.0.1.0/24"
    $dnsLabel = "contoso09152016"
    $lbName = "myIPv4IPv6Lb"
    ```

## <a name="create-a-resource-group-a-load-balancer-a-virtual-network-and-subnets"></a>Een resource groep, een load balancer, een virtueel netwerk en subnetten maken

1. Een resourcegroep maken:

    ```azurecli
    az group create --name $rgName --location $location
    ```

2. Maak een load balancer:

    ```azurecli
    $lb = az network lb create --resource-group $rgname --location $location --name $lbName
    ```

3. Een virtueel netwerk maken:

    ```azurecli
    $vnet = az network vnet create  --resource-group $rgname --name $vnetName --location $location --address-prefixes $vnetPrefix
    ```

4. Maak in dit virtuele netwerk twee subnetten:

    ```azurecli
    $subnet1 = az network vnet subnet create --resource-group $rgname --name $subnet1Name --address-prefix $subnet1Prefix --vnet-name $vnetName
    $subnet2 = az network vnet subnet create --resource-group $rgname --name $subnet2Name --address-prefix $subnet2Prefix --vnet-name $vnetName
    ```

## <a name="create-public-ip-addresses-for-the-front-end-pool"></a>Open bare IP-adressen voor de front-end-pool maken

1. De Power shell-variabelen instellen:

    ```powershell
    $publicIpv4Name = "myIPv4Vip"
    $publicIpv6Name = "myIPv6Vip"
    ```

2. Een openbaar IP-adres maken voor de front-end-IP-groep:

    ```azurecli
    $publicipV4 = az network public-ip create --resource-group $rgname --name $publicIpv4Name --location $location --version IPv4 --allocation-method Dynamic --dns-name $dnsLabel
    $publicipV6 = az network public-ip create --resource-group $rgname --name $publicIpv6Name --location $location --version IPv6 --allocation-method Dynamic --dns-name $dnsLabel
    ```

    > [!IMPORTANT]
    > De load balancer gebruikt het domein label van het open bare IP-adres als Fully Qualified Domain Name (FQDN). Dit is een wijziging ten opzichte van de klassieke implementatie, waarbij de naam van de Cloud service wordt gebruikt als load balancer FQDN.
    >
    > In dit voor beeld is de FQDN *contoso09152016.southcentralus.cloudapp.Azure.com*.

## <a name="create-front-end-and-back-end-pools"></a>Front-end-en back-end-Pools maken

In deze sectie maakt u de volgende IP-adres groepen:
* De front-end-IP-adres groep die het binnenkomende netwerk verkeer op de load balancer ontvangt.
* De back-end-IP-pool waar de front-end-pool het netwerk verkeer met taak verdeling verzendt.

1. De Power shell-variabelen instellen:

    ```powershell
    $frontendV4Name = "FrontendVipIPv4"
    $frontendV6Name = "FrontendVipIPv6"
    $backendAddressPoolV4Name = "BackendPoolIPv4"
    $backendAddressPoolV6Name = "BackendPoolIPv6"
    ```

2. Maak een front-end-IP-adres groep en koppel deze aan het open bare IP-adres dat u in de vorige stap hebt gemaakt en de load balancer.

    ```azurecli
    $frontendV4 = az network lb frontend-ip create --resource-group $rgname --name $frontendV4Name --public-ip-address $publicIpv4Name --lb-name $lbName
    $frontendV6 = az network lb frontend-ip create --resource-group $rgname --name $frontendV6Name --public-ip-address $publicIpv6Name --lb-name $lbName
    $backendAddressPoolV4 = az network lb address-pool create --resource-group $rgname --name $backendAddressPoolV4Name --lb-name $lbName
    $backendAddressPoolV6 = az network lb address-pool create --resource-group $rgname --name $backendAddressPoolV6Name --lb-name $lbName
    ```

## <a name="create-the-probe-nat-rules-and-load-balancer-rules"></a>De test, NAT-regels en load balancer regels maken

In dit voorbeeld worden de volgende items gemaakt:

* Een test regel om te controleren of er verbinding is met TCP-poort 80.
* Een NAT-regel om al het binnenkomende verkeer op poort 3389 te vertalen naar poort 3389 voor RDP.\*
* Een NAT-regel om al het binnenkomende verkeer op poort 3391 te vertalen naar poort 3389 voor Remote Desktop Protocol (RDP).\*
* Een load balancer regel waarmee al het binnenkomende verkeer op poort 80 wordt gebalanceerd naar poort 80 op de adressen in de back-end-pool.

\* NAT-regels zijn gekoppeld aan een specifiek exemplaar van een virtuele machine achter de load balancer. Het netwerk verkeer dat binnenkomt op poort 3389 wordt verzonden naar de specifieke virtuele machine en poort die is gekoppeld aan de NAT-regel. U moet een protocol (UDP of TCP) voor een NAT-regel opgeven. U kunt beide protocollen niet toewijzen aan dezelfde poort.

1. De Power shell-variabelen instellen:

    ```powershell
    $probeV4V6Name = "ProbeForIPv4AndIPv6"
    $natRule1V4Name = "NatRule-For-Rdp-VM1"
    $natRule2V4Name = "NatRule-For-Rdp-VM2"
    $lbRule1V4Name = "LBRuleForIPv4-Port80"
    $lbRule1V6Name = "LBRuleForIPv6-Port80"
    ```

2. Maak de test.

    In het volgende voor beeld wordt een TCP-test gemaakt waarmee de verbinding met de TCP-poort 80 van de back-end elke 15 seconden wordt gecontroleerd. Na twee opeenvolgende fouten, markeert de back-end-resource als niet-beschikbaar.

    ```azurecli
    $probeV4V6 = az network lb probe create --resource-group $rgname --name $probeV4V6Name --protocol tcp --port 80 --interval 15 --threshold 2 --lb-name $lbName
    ```

3. Maak binnenkomende NAT-regels waarmee RDP-verbindingen met de back-end-bronnen worden toegestaan:

    ```azurecli
    $inboundNatRuleRdp1 = az network lb inbound-nat-rule create --resource-group $rgname --name $natRule1V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3389 --backend-port 3389 --lb-name $lbName
    $inboundNatRuleRdp2 = az network lb inbound-nat-rule create --resource-group $rgname --name $natRule2V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3391 --backend-port 3389 --lb-name $lbName
    ```

4. Maak load balancer regels die verkeer naar verschillende back-end-poorten verzenden, afhankelijk van de front-end die de aanvraag heeft ontvangen.

    ```azurecli
    $lbruleIPv4 = az network lb rule create --resource-group $rgname --name $lbRule1V4Name --frontend-ip-name $frontendV4Name --backend-pool-name $backendAddressPoolV4Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 80 --lb-name $lbName
    $lbruleIPv6 = az network lb rule create --resource-group $rgname --name $lbRule1V6Name --frontend-ip-name $frontendV6Name --backend-pool-name $backendAddressPoolV6Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 8080 --lb-name $lbName
    ```

5. Controleer uw instellingen:

    ```azurecli
    az network lb show --resource-group $rgName --name $lbName
    ```

    Verwachte uitvoer:

    ```output
    info:    Executing command network lb show
    info:    Looking up the load balancer "myIPv4IPv6Lb"
    data:    Id                              : /subscriptions/########-####-####-####-############/resourceGroups/pscontosorg1southctrlus09152016/providers/Microsoft.Network/loadBalancers/myIPv4IPv6Lb
    data:    Name                            : myIPv4IPv6Lb
    data:    Type                            : Microsoft.Network/loadBalancers
    data:    Location                        : southcentralus
    data:    Provisioning state              : Succeeded
    data:
    data:    Frontend IP configurations:
    data:    Name             Provisioning state  Private IP allocation  Private IP   Subnet  Public IP
    data:    ---------------  ------------------  ---------------------  -----------  ------  ---------
    data:    FrontendVipIPv4  Succeeded           Dynamic                                     myIPv4Vip
    data:    FrontendVipIPv6  Succeeded           Dynamic                                     myIPv6Vip
    data:
    data:    Probes:
    data:    Name                 Provisioning state  Protocol  Port  Path  Interval  Count
    data:    -------------------  ------------------  --------  ----  ----  --------  -----
    data:    ProbeForIPv4AndIPv6  Succeeded           Tcp       80          15        2
    data:
    data:    Backend Address Pools:
    data:    Name             Provisioning state
    data:    ---------------  ------------------
    data:    BackendPoolIPv4  Succeeded
    data:    BackendPoolIPv6  Succeeded
    data:
    data:    Load Balancing Rules:
    data:    Name                  Provisioning state  Load distribution  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
    data:    --------------------  ------------------  -----------------  --------  -------------  ------------  ------------------  -----------------------
    data:    LBRuleForIPv4-Port80  Succeeded           Default            Tcp       80             80            false               4
    data:    LBRuleForIPv6-Port80  Succeeded           Default            Tcp       80             8080          false               4
    data:
    data:    Inbound NAT Rules:
    data:    Name                 Provisioning state  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
    data:    -------------------  ------------------  --------  -------------  ------------  ------------------  -----------------------
    data:    NatRule-For-Rdp-VM1  Succeeded           Tcp       3389           3389          false               4
    data:    NatRule-For-Rdp-VM2  Succeeded           Tcp       3391           3389          false               4
    info:    network lb show
    ```

## <a name="create-nics"></a>NIC's maken

Maak Nic's en koppel deze aan NAT-regels, load balancer regels en tests.

1. De Power shell-variabelen instellen:

    ```powershell
    $nic1Name = "myIPv4IPv6Nic1"
    $nic2Name = "myIPv4IPv6Nic2"
    $subnet1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet1Name"
    $subnet2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet2Name"
    $backendAddressPoolV4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV4Name"
    $backendAddressPoolV6Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV6Name"
    $natRule1V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule1V4Name"
    $natRule2V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule2V4Name"
    ```

2. Maak een NIC voor elke back-end en voeg een IPv6-configuratie toe:

    ```azurecli
    $nic1 = az network nic create --name $nic1Name --resource-group $rgname --location $location --private-ip-address-version "IPv4" --subnet $subnet1Id --lb-address-pools $backendAddressPoolV4Id --lb-inbound-nat-rules $natRule1V4Id
    $nic1IPv6 = az network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-address-version "IPv6" --lb-address-pools $backendAddressPoolV6Id --nic-name $nic1Name

    $nic2 = az network nic create --name $nic2Name --resource-group $rgname --location $location --private-ip-address-version "IPv4" --subnet $subnet2Id --lb-address-pools $backendAddressPoolV4Id --lb-inbound-nat-rules $natRule2V4Id
    $nic2IPv6 = az network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-address-version "IPv6" --lb-address-pools $backendAddressPoolV6Id --nic-name $nic2Name
    ```

## <a name="create-the-back-end-vm-resources-and-attach-each-nic"></a>De back-end-VM-resources maken en elke NIC koppelen

Als u Vm's wilt maken, moet u een opslag account hebben. Voor taak verdeling moeten de virtuele machines lid zijn van een beschikbaarheidsset. Zie [een Azure-VM maken met behulp van Power shell](../virtual-machines/windows/quick-create-powershell.md?toc=%2fazure%2fload-balancer%2ftoc.json)voor meer informatie over het maken van vm's.

1. De Power shell-variabelen instellen:

    ```powershell
    $availabilitySetName = "myIPv4IPv6AvailabilitySet"
    $vm1Name = "myIPv4IPv6VM1"
    $vm2Name = "myIPv4IPv6VM2"
    $nic1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic1Name"
    $nic2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic2Name"
    $imageurn = "MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest"
    $vmUserName = "vmUser"
    $mySecurePassword = "PlainTextPassword*1"
    ```

    > [!WARNING]
    > In dit voor beeld worden de gebruikers naam en het wacht woord voor de virtuele machines in een lees bare tekst gebruikt. Zorg ervoor dat u deze referenties in een lees bare tekst gebruikt. Zie de cmdlet voor een veiligere methode voor het afhandelen van referenties in Power shell [`Get-Credential`](/powershell/module/microsoft.powershell.security/get-credential) .

2. De beschikbaarheidsset maken:

    ```azurecli
    $availabilitySet = az vm availability-set create --name $availabilitySetName --resource-group $rgName --location $location
    ```

3. Maak de virtuele machines met de bijbehorende Nic's:

    ```azurecli
    az vm create --resource-group $rgname --name $vm1Name --image $imageurn --admin-username $vmUserName --admin-password $mySecurePassword --nics $nic1Id --location $location --availability-set $availabilitySetName --size "Standard_A1" 

    az vm create --resource-group $rgname --name $vm2Name --image $imageurn --admin-username $vmUserName --admin-password $mySecurePassword --nics $nic2Id --location $location --availability-set $availabilitySetName --size "Standard_A1" 
    ```
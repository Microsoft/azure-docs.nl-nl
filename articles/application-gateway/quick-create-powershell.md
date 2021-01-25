---
title: 'Quickstart: Webverkeer doorsturen met PowerShell'
titleSuffix: Azure Application Gateway
description: In deze quickstart leest u hoe u met Azure PowerShell een Azure App Gateway maakt die webverkeer doorstuurt naar virtuele machines in een back-endpool.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 01/19/2021
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 8073d1e18b08a6deb0175f8eaf18de382e93e299
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98601858"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway-using-azure-powershell"></a>Quickstart: Webverkeer doorsturen met Azure Application Gateway met behulp van Azure PowerShell

In deze quickstart gebruikt u Azure PowerShell om een toepassingsgateway te maken. Vervolgens test u de toepassing om te controleren of alles correct werkt. 

De toepassingsgateway stuurt webverkeer van toepassingen naar specifieke resources in een back-endpool. U wijst listeners toe aan poorten, maakt regels en voegt resources toe aan een back-endpool. Dit artikel gebruikt een eenvoudige configuratie met een openbaar IP-adres voor de front-end, een eenvoudige listener om een enkele site op de toepassingsgateway te hosten, een eenvoudige regel voor doorsturen van aanvragen en twee virtuele machines in de back-endpool.

U kunt deze quickstart ook uitvoeren met [Azure CLI](quick-create-cli.md) of [Azure Portal](quick-create-portal.md).

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Azure PowerShell versie 1.0.0 of hoger](/powershell/azure/install-az-ps) (als u Azure PowerShell lokaal uitvoert).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-azure"></a>Verbinding maken met Azure

Voer `Connect-AzAccount` uit om verbinding te maken met Azure.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

In Azure kunt u verwante resources toewijzen aan een resourcegroep. U kunt een bestaande resourcegroep gebruiken of een nieuwe maken.

Gebruikt de `New-AzResourceGroup`-cmdlet om een nieuwe resourcegroep te maken: 

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```
## <a name="create-network-resources"></a>Netwerkbronnen maken

Er is een virtueel netwerk nodig voor communicatie tussen de resources die u maakt.  Het subnet van de toepassingsgateway kan alleen bestaan uit toepassingsgateways. Andere resources zijn niet toegestaan.  U kunt een nieuw subnet maken voor Application Gateway of een bestaand subnet gebruiken. In dit voorbeeld maakt u twee subnetten: één voor de toepassingsgateway en één voor de back-endservers. Afhankelijk van uw toepassing kunt u het IP-adres voor de front-end van de toepassingsgateway als openbaar of privé configureren. In dit voorbeeld kiest u voor een openbaar IP-adres voor de front-end.

1. Maak de subnetconfiguraties met behulp van `New-AzVirtualNetworkSubnetConfig`.
2. Maak het virtuele netwerk met de subnetconfiguraties met behulp van `New-AzVirtualNetwork`. 
3. Maak het openbare IP-adres met behulp van `New-AzPublicIpAddress`. 
> [!NOTE]
> [Het beleid voor endpoints van virtuele netwerken](../virtual-network/virtual-network-service-endpoint-policies-overview.md) wordt momenteel niet ondersteund in een Application Gateway-subnet.

```azurepowershell-interactive
$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.1.0/24
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.2.0/24
New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $agSubnetConfig, $backendSubnetConfig
New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Static `
  -Sku Standard
```
## <a name="create-an-application-gateway"></a>Een toepassingsgateway maken

### <a name="create-the-ip-configurations-and-frontend-port"></a>IP-configuraties en front-endpoort maken

1. Gebruik `New-AzApplicationGatewayIPConfiguration` om de configuratie te maken waarmee het subnet aan de toepassingsgateway wordt gekoppeld. 
2. Gebruik `New-AzApplicationGatewayFrontendIPConfig` om de configuratie te maken waarmee het openbare IP-adres dat u eerder hebt gemaakt voor de toepassingsgateway. 
3. Gebruik `New-AzApplicationGatewayFrontendPort` om poort 80 toe te wijzen voor toegang tot de toepassingsgateway.

```azurepowershell-interactive
$vnet   = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name myAGSubnet
$pip    = Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress 
$gipconfig = New-AzApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet
$fipconfig = New-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip
$frontendport = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80
```

### <a name="create-the-backend-pool"></a>Back-endpool maken

1. Gebruik `New-AzApplicationGatewayBackendAddressPool` om de back-endpool voor de toepassingsgateway te maken. De back-endpool is momenteel leeg. Wanneer u de netwerkinterfaces voor de back-endserver maakt in de volgende sectie, voegt u deze toe aan de back-endpool.
2. Configureer de instellingen voor de back-endpool met `New-AzApplicationGatewayBackendHttpSetting`.

```azurepowershell-interactive
$backendPool = New-AzApplicationGatewayBackendAddressPool `
  -Name myAGBackendPool
$poolSettings = New-AzApplicationGatewayBackendHttpSetting `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 30
```

### <a name="create-the-listener-and-add-a-rule"></a>Listener maken en regel toevoegen

Als u de toepassingsgateway wilt inschakelen om het verkeer op de juiste manier naar de back-endpool te routeren, is in Azure een listener vereist. In Azure is ook een regel vereist, zodat de listener weet welke back-endpool moet worden gebruikt voor binnenkomend verkeer. 

1. Maak een listener met behulp van `New-AzApplicationGatewayHttpListener` met de front-endconfiguratie en de front-endpoort die u eerder hebt gemaakt. 
2. Gebruik `New-AzApplicationGatewayRequestRoutingRule` om een regel met de naam *rule1* te maken. 

```azurepowershell-interactive
$defaultlistener = New-AzApplicationGatewayHttpListener `
  -Name myAGListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport
$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $backendPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>De toepassingsgateway maken

Nu u de benodigde ondersteunende resources hebt gemaakt, kunt u de toepassingsgateway maken:

1. Gebruik `New-AzApplicationGatewaySku` om parameters voor de toepassingsgateway op te geven.
2. Gebruik `New-AzApplicationGateway` om de toepassingsgateway te maken.

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_v2 `
  -Tier Standard_v2 `
  -Capacity 2
New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $backendPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultlistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku
```

### <a name="backend-servers"></a>Back-endservers

Nadat u de toepassingsgateway hebt gemaakt, maakt u de virtuele machines voor de back-end die de websites zullen hosten. Een back-end kan bestaan uit netwerkinterfaces, virtuele-machineschaalsets, een openbaar IP-adres, een interne IP-adres, FQDN's (Fully Qualified Domain Name) en multitenant back-ends zoals Azure App Service. 

In dit voorbeeld maakt u twee virtuele machines die worden gebruikt als back-endservers voor de toepassingsgateway. U installeert ook IIS op de virtuele machines om te controleren of de toepassingsgateway in Azure is gemaakt.

#### <a name="create-two-virtual-machines"></a>Twee virtuele machines maken

1. Haal de recent gemaakte configuratie voor de toepassingsgatewayback-endpool op met `Get-AzApplicationGatewayBackendAddressPool`.
2. Maak een nieuwe netwerkinterface met `New-AzNetworkInterface`.
3. Maak een VM-configuratie met `New-AzVMConfig`.
4. Maak de virtuele machine met `New-AzVM`.

Wanneer u het volgende codevoorbeeld uitvoert om de virtuele machines te maken, wordt u gevraagd om referenties. Voer een gebruikersnaam en een wachtwoord in:
    
```azurepowershell-interactive
$appgw = Get-AzApplicationGateway -ResourceGroupName myResourceGroupAG -Name myAppGateway
$backendPool = Get-AzApplicationGatewayBackendAddressPool -Name myAGBackendPool -ApplicationGateway $appgw
$vnet   = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name myBackendSubnet
$cred = Get-Credential
for ($i=1; $i -le 2; $i++)
{
  $nic = New-AzNetworkInterface `
    -Name myNic$i `
    -ResourceGroupName myResourceGroupAG `
    -Location EastUS `
    -Subnet $subnet `
    -ApplicationGatewayBackendAddressPool $backendpool
  $vm = New-AzVMConfig `
    -VMName myVM$i `
    -VMSize Standard_DS2_v2
  Set-AzVMOperatingSystem `
    -VM $vm `
    -Windows `
    -ComputerName myVM$i `
    -Credential $cred
  Set-AzVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
  Add-AzVMNetworkInterface `
    -VM $vm `
    -Id $nic.Id
  Set-AzVMBootDiagnostic `
    -VM $vm `
    -Disable
  New-AzVM -ResourceGroupName myResourceGroupAG -Location EastUS -VM $vm
  Set-AzVMExtension `
    -ResourceGroupName myResourceGroupAG `
    -ExtensionName IIS `
    -VMName myVM$i `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
    -Location EastUS
}
```

## <a name="test-the-application-gateway"></a>De toepassingsgateway testen

Het is niet nodig IIS te installeren om de toepassingsgateway te maken, maar u hebt het in deze snelstartgids geïnstalleerd om te controleren of het maken van de toepassingsgateway in Azure is geslaagd.

Gebruik IIS om de toepassingsgateway te testen:

1. Voer `Get-AzPublicIPAddress` uit om het openbare IP-adres van de toepassingsgateway op te halen. 
2. Kopieer het openbare IP-adres en plak het in de adresbalk van de browser. Als u de browser vernieuwt, ziet u de naam van de virtuele machine. Een geldig antwoord verifieert dat de toepassingsgateway is gemaakt en verbinding kan maken met de back-end.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Toepassingsgateway testen](./media/quick-create-powershell/application-gateway-iistest.png)


## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de bij de toepassingsgateway gemaakte resources niet meer nodig hebt, verwijdert u de resourcegroep. Wanneer u de resourcegroep verwijdert, verwijdert u ook de toepassingsgateway en alle resources die hieraan zijn gerelateerd. 

Als u de resourcegroep wilt verwijderen, roept u de cmdlet `Remove-AzResourceGroup` aan:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Webverkeer met een toepassingsgateway beheren met behulp van Azure PowerShell](./tutorial-manage-web-traffic-powershell.md)


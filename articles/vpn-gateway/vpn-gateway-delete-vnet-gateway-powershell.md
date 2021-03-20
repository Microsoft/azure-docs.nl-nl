---
title: 'Azure VPN Gateway: een gateway verwijderen: Power shell'
description: Verwijder een virtuele netwerk gateway met behulp van Power shell in het Resource Manager-implementatie model.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.date: 09/03/2020
ms.author: cherylmc
ms.topic: how-to
ms.openlocfilehash: 48e0998719ea19c0b360b50d8c9171d2b448ad19
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89440759"
---
# <a name="delete-a-virtual-network-gateway-using-powershell"></a>Een virtuele netwerk gateway verwijderen met Power shell
> [!div class="op_single_selector"]
> * [Azure-portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (klassiek)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

Er zijn een aantal verschillende benaderingen die u kunt nemen wanneer u een virtuele netwerk gateway wilt verwijderen voor een configuratie van een VPN-gateway.

- Als u alles wilt verwijderen en opnieuw wilt beginnen, zoals in het geval van een test omgeving, kunt u de resource groep verwijderen. Wanneer u een resource groep verwijdert, worden alle resources in de groep verwijderd. Deze methode wordt alleen aanbevolen als u geen van de resources in de resource groep wilt blijven gebruiken. Het is niet mogelijk om slechts een paar resources te verwijderen met behulp van deze methode.

- Als u een aantal resources in uw resource groep wilt blijven gebruiken, wordt het verwijderen van een virtuele netwerk gateway iets gecompliceerder. Voordat u de gateway van het virtuele netwerk kunt verwijderen, moet u eerst alle resources verwijderen die afhankelijk zijn van de gateway. De stappen die u volgt, zijn afhankelijk van het type verbindingen dat u hebt gemaakt en de afhankelijke bronnen voor elke verbinding.

## <a name="before-beginning"></a>Voordat u begint



### <a name="1-download-the-latest-azure-resource-manager-powershell-cmdlets"></a>1. down load de nieuwste Azure Resource Manager Power shell-cmdlets.

Down load en installeer de nieuwste versie van de Azure Resource Manager Power shell-cmdlets. Zie [Azure PowerShell installeren en configureren](/powershell/azure/)voor meer informatie over het downloaden en installeren van Power shell-cmdlets.

### <a name="2-connect-to-your-azure-account"></a>2. Maak verbinding met uw Azure-account.

Open de PowerShell-console en maak verbinding met uw account. Gebruik het volgende voorbeeld als hulp bij het maken van de verbinding:

```powershell
Connect-AzAccount
```

Controleer de abonnementen voor het account.

```powershell
Get-AzSubscription
```

Als u meer dan één abonnement hebt, geeft u het abonnement op dat u wilt gebruiken.

```powershell
Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="delete-a-site-to-site-vpn-gateway"></a><a name="S2S"></a>Een site-naar-site-VPN-gateway verwijderen

Als u een virtuele netwerk gateway voor een S2S-configuratie wilt verwijderen, moet u eerst elke resource verwijderen die betrekking heeft op de gateway van het virtuele netwerk. Resources moeten in een bepaalde volg orde worden verwijderd als gevolg van afhankelijkheden. Wanneer u met de onderstaande voor beelden werkt, moeten sommige waarden worden opgegeven, terwijl andere waarden een uitvoer resultaat zijn. We gebruiken de volgende specifieke waarden in de voor beelden voor demonstratie doeleinden:

VNet-naam: VNet1<br>
Naam van resource groep: RG1<br>
Naam van de gateway van het virtuele netwerk: GW1<br>

De volgende stappen zijn van toepassing op het Resource Manager-implementatie model.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Haal de gateway van het virtuele netwerk op die u wilt verwijderen.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Controleer of de gateway van het virtuele netwerk verbindingen heeft.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
$Conns=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```

### <a name="3-delete-all-connections"></a>3. alle verbindingen verwijderen.

U wordt mogelijk gevraagd om de verwijdering van elk van de verbindingen te bevestigen.

```powershell
$Conns | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="4-delete-the-virtual-network-gateway"></a>4. Verwijder de gateway van het virtuele netwerk.

U wordt mogelijk gevraagd om het verwijderen van de gateway te bevestigen. Als u naast uw S2S-configuratie een P2S-configuratie hebt voor dit VNet, wordt met het verwijderen van de gateway van het virtuele netwerk automatisch alle P2S-clients verbroken zonder dat er een waarschuwing wordt gegeven.


```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

Uw virtuele netwerk gateway is op dit moment verwijderd. U kunt de volgende stappen gebruiken om alle resources te verwijderen die niet meer worden gebruikt.

### <a name="5-delete-the-local-network-gateways"></a>5 de lokale netwerk gateways verwijderen.

Haal de lijst van de bijbehorende lokale netwerk gateways op.

```powershell
$LNG=Get-AzLocalNetworkGateway -ResourceGroupName "RG1" | where-object {$_.Id -In $Conns.LocalNetworkGateway2.Id}
```

Verwijder de lokale netwerk gateways. U wordt mogelijk gevraagd om te bevestigen dat elk van de lokale netwerk gateways zijn verwijderd.

```powershell
$LNG | ForEach-Object {Remove-AzLocalNetworkGateway -Name $_.Name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="6-delete-the-public-ip-address-resources"></a>6. Verwijder de resources van het open bare IP-adres.

De IP-configuraties van de virtuele netwerk gateway ophalen.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

De lijst met open bare IP-adres bronnen ophalen die worden gebruikt voor deze virtuele netwerk gateway. Als de gateway van het virtuele netwerk actief was, worden er twee open bare IP-adressen weer geven.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Verwijder de open bare IP-resources.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "RG1"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. Verwijder het gateway-subnet en stel de configuratie in.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete-a-vnet-to-vnet-vpn-gateway"></a><a name="v2v"></a>Een VNet-naar-VNet VPN-gateway verwijderen

Als u een virtuele netwerk gateway voor een V2V-configuratie wilt verwijderen, moet u eerst elke resource verwijderen die betrekking heeft op de gateway van het virtuele netwerk. Resources moeten in een bepaalde volg orde worden verwijderd als gevolg van afhankelijkheden. Wanneer u met de onderstaande voor beelden werkt, moeten sommige waarden worden opgegeven, terwijl andere waarden een uitvoer resultaat zijn. We gebruiken de volgende specifieke waarden in de voor beelden voor demonstratie doeleinden:

VNet-naam: VNet1<br>
Naam van resource groep: RG1<br>
Naam van de gateway van het virtuele netwerk: GW1<br>

De volgende stappen zijn van toepassing op het Resource Manager-implementatie model.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Haal de gateway van het virtuele netwerk op die u wilt verwijderen.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Controleer of de gateway van het virtuele netwerk verbindingen heeft.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
Er zijn mogelijk andere verbindingen met de gateway van het virtuele netwerk die deel uitmaken van een andere resource groep. Controleer op extra verbindingen in elke extra resource groep. In dit voor beeld controleren we op verbindingen van RG2. Voer dit uit voor elke resource groep die u hebt die een verbinding met de virtuele netwerk gateway kan hebben.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG2" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
```

### <a name="3-get-the-list-of-connections-in-both-directions"></a>3. Haal de lijst met verbindingen op in beide richtingen.

Omdat dit een VNet-naar-VNet-configuratie is, hebt u de lijst met verbindingen in beide richtingen nodig.

```powershell
$ConnsL=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
In dit voor beeld controleren we op verbindingen van RG2. Voer dit uit voor elke resource groep die u hebt die een verbinding met de virtuele netwerk gateway kan hebben.

```powershell
 $ConnsR=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "<NameOfResourceGroup2>" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
 ```

### <a name="4-delete-all-connections"></a>4. alle verbindingen verwijderen.

U wordt mogelijk gevraagd om de verwijdering van elk van de verbindingen te bevestigen.

```powershell
$ConnsL | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
$ConnsR | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="5-delete-the-virtual-network-gateway"></a>5. Verwijder de gateway van het virtuele netwerk.

U wordt mogelijk gevraagd om te bevestigen dat de gateway van het virtuele netwerk is verwijderd. Als u naast uw V2V-configuratie P2S-configuraties hebt voor uw VNets, wordt met het verwijderen van de gateways van het virtuele netwerk automatisch alle P2S-clients zonder waarschuwing verbroken.

```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

Uw virtuele netwerk gateway is op dit moment verwijderd. U kunt de volgende stappen gebruiken om alle resources te verwijderen die niet meer worden gebruikt.

### <a name="6-delete-the-public-ip-address-resources"></a>6. de open bare IP-adres bronnen verwijderen

De IP-configuraties van de virtuele netwerk gateway ophalen.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

De lijst met open bare IP-adres bronnen ophalen die worden gebruikt voor deze virtuele netwerk gateway. Als de gateway van het virtuele netwerk actief was, worden er twee open bare IP-adressen weer geven.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Verwijder de open bare IP-resources. U wordt mogelijk gevraagd om het verwijderen van het open bare IP-adres te bevestigen.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. Verwijder het gateway-subnet en stel de configuratie in.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete-a-point-to-site-vpn-gateway"></a><a name="deletep2s"></a>Een punt-naar-site-VPN-gateway verwijderen

Als u een virtuele netwerk gateway voor een P2S-configuratie wilt verwijderen, moet u eerst elke resource verwijderen die betrekking heeft op de gateway van het virtuele netwerk. Resources moeten in een bepaalde volg orde worden verwijderd als gevolg van afhankelijkheden. Wanneer u met de onderstaande voor beelden werkt, moeten sommige waarden worden opgegeven, terwijl andere waarden een uitvoer resultaat zijn. We gebruiken de volgende specifieke waarden in de voor beelden voor demonstratie doeleinden:

VNet-naam: VNet1<br>
Naam van resource groep: RG1<br>
Naam van de gateway van het virtuele netwerk: GW1<br>

De volgende stappen zijn van toepassing op het Resource Manager-implementatie model.


>[!NOTE]
> Wanneer u de VPN-gateway verwijdert, worden alle verbonden clients zonder waarschuwing losgekoppeld van het VNet.
>
>

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Haal de gateway van het virtuele netwerk op die u wilt verwijderen.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-delete-the-virtual-network-gateway"></a>2. Verwijder de gateway van het virtuele netwerk.

U wordt mogelijk gevraagd om te bevestigen dat de gateway van het virtuele netwerk is verwijderd.

```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

Uw virtuele netwerk gateway is op dit moment verwijderd. U kunt de volgende stappen gebruiken om alle resources te verwijderen die niet meer worden gebruikt.

### <a name="3-delete-the-public-ip-address-resources"></a>3. de open bare IP-adres bronnen verwijderen

De IP-configuraties van de virtuele netwerk gateway ophalen.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

De lijst met open bare IP-adressen die worden gebruikt voor deze virtuele netwerk gateway ophalen. Als de gateway van het virtuele netwerk actief was, worden er twee open bare IP-adressen weer geven.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

De open bare Ip's verwijderen. U wordt mogelijk gevraagd om het verwijderen van het open bare IP-adres te bevestigen.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="4-delete-the-gateway-subnet-and-set-the-configuration"></a>4. Verwijder het gateway-subnet en stel de configuratie in.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete-a-vpn-gateway-by-deleting-the-resource-group"></a><a name="delete"></a>Een VPN-gateway verwijderen door de resource groep te verwijderen

Als u zich geen zorgen maakt over het bewaren van uw resources in de resource groep en u alleen opnieuw wilt beginnen, kunt u een hele resource groep verwijderen. Dit is een snelle manier om alles te verwijderen. De volgende stappen zijn alleen van toepassing op het Resource Manager-implementatie model.

### <a name="1-get-a-list-of-all-the-resource-groups-in-your-subscription"></a>1. Haal een lijst op met alle resource groepen in uw abonnement.

```powershell
Get-AzResourceGroup
```

### <a name="2-locate-the-resource-group-that-you-want-to-delete"></a>2. Zoek de resource groep die u wilt verwijderen.

Zoek de resource groep die u wilt verwijderen en Bekijk de lijst met resources in die resource groep. In het voor beeld is de naam van de resource groep RG1. Wijzig het voor beeld om een lijst met alle resources op te halen.

```powershell
Find-AzResource -ResourceGroupNameContains RG1
```

### <a name="3-verify-the-resources-in-the-list"></a>3. Controleer de resources in de lijst.

Wanneer de lijst wordt geretourneerd, controleert u of u alle resources in de resource groep en de resource groep zelf wilt verwijderen. Als u een aantal resources in de resource groep wilt blijven gebruiken, gebruikt u de stappen in de vorige secties van dit artikel om uw gateway te verwijderen.

### <a name="4-delete-the-resource-group-and-resources"></a>4. Verwijder de resource groep en de resources.

Als u de resource groep en alle resources in de resource groep wilt verwijderen, wijzigt u het voor beeld en voert u uit.

```powershell
Remove-AzResourceGroup -Name RG1
```

### <a name="5-check-the-status"></a>5. Controleer de status.

Het duurt enige tijd voor Azure om alle resources te verwijderen. U kunt de status van de resource groep controleren met behulp van deze cmdlet.

```powershell
Get-AzResourceGroup -ResourceGroupName RG1
```

Het resultaat dat wordt geretourneerd, geeft ' geslaagd ' weer.

```
ResourceGroupName : RG1
Location          : eastus
ProvisioningState : Succeeded
```

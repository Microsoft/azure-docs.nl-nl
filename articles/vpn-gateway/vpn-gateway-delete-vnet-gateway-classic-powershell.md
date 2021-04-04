---
title: 'Een virtuele netwerk gateway verwijderen: klassiek Azure'
description: Verwijder een virtuele netwerk gateway met behulp van Power shell in het klassieke implementatie model.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/08/2020
ms.author: cherylmc
ms.openlocfilehash: 39585a68c5cddc50cd04e82caca71209270f7b68
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91874115"
---
# <a name="delete-a-virtual-network-gateway-using-powershell-classic"></a>Een virtuele netwerk gateway verwijderen met behulp van Power shell (klassiek)

> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [Klassiek - PowerShell](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>

Dit artikel helpt u bij het verwijderen van een VPN-gateway in het klassieke implementatie model met behulp van Power shell. Nadat de virtuele netwerk gateway is verwijderd, wijzigt u het netwerk configuratie bestand om elementen te verwijderen die u niet meer gebruikt.

## <a name="step-1-connect-to-azure"></a><a name="connect"></a>Stap 1: verbinding maken met Azure

### <a name="1-install-the-latest-powershell-cmdlets"></a>1. Installeer de meest recente Power shell-cmdlets.

[!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

### <a name="2-connect-to-your-azure-account"></a>2. Maak verbinding met uw Azure-account.

Open de PowerShell-console met verhoogde rechten en maak verbinding met uw account. Gebruik het volgende voorbeeld als hulp bij het maken van de verbinding:

1. Open de Power shell-console met verhoogde bevoegdheden.
2. Maak verbinding met uw account. Gebruik het volgende voorbeeld als hulp bij het maken van de verbinding:

   ```powershell
   Add-AzureAccount
   ```

## <a name="step-2-export-and-view-the-network-configuration-file"></a><a name="export"></a>Stap 2: het netwerk configuratie bestand exporteren en weer geven

Maak een map op de computer en exporteer vervolgens het netwerkconfiguratiebestand naar de map. U gebruikt dit bestand om de huidige configuratie gegevens weer te geven en om de netwerk configuratie te wijzigen.

In dit voorbeeld wordt het netwerkconfiguratiebestand geëxporteerd naar C:\AzureNet.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

Open het bestand met een tekst editor en Bekijk de naam voor uw klassieke VNet. Wanneer u een VNet maakt in de Azure Portal, wordt de volledige naam die door Azure wordt gebruikt, niet weer gegeven in de portal. Zo kan een VNet dat in het Azure Portal met de naam ' ClassicVNet1 ' wordt genoemd, een veel langere naam hebben in het netwerk configuratie bestand. De naam kan er ongeveer als volgt uitzien: ' Group ClassicRG1 ClassicVNet1 '. Namen van virtuele netwerken worden vermeld als **' VirtualNetworkSite name = '**. Gebruik de namen in het netwerk configuratie bestand bij het uitvoeren van uw Power shell-cmdlets.

## <a name="step-3-delete-the-virtual-network-gateway"></a><a name="delete"></a>Stap 3: de gateway van het virtuele netwerk verwijderen

Wanneer u een virtuele netwerk gateway verwijdert, worden alle verbindingen met het VNet via de gateway verbroken. Als er P2S-clients zijn verbonden met het VNet, wordt de verbinding zonder waarschuwing verbroken.

In dit voor beeld wordt de gateway van het virtuele netwerk verwijderd. Zorg ervoor dat u de volledige naam van het virtuele netwerk van het netwerk configuratie bestand gebruikt.

```powershell
Remove-AzureVNetGateway -VNetName "Group ClassicRG1 ClassicVNet1"
```

Als de waarde is geslaagd, wordt het volgende weer gegeven:

```
Status : Successful
```

## <a name="step-4-modify-the-network-configuration-file"></a><a name="modify"></a>Stap 4: het netwerk configuratie bestand wijzigen

Wanneer u een virtuele netwerk gateway verwijdert, wordt het netwerk configuratie bestand niet door de cmdlet gewijzigd. U moet het bestand wijzigen om de elementen te verwijderen die niet meer worden gebruikt. De volgende secties helpen u bij het wijzigen van het netwerk configuratie bestand dat u hebt gedownload.

### <a name="local-network-site-references"></a><a name="lnsref"></a>Verwijzingen naar lokale netwerk sites

Als u site referentie gegevens wilt verwijderen, moet u configuratie wijzigingen aanbrengen in **ConnectionsToLocalNetwork/LocalNetworkSiteRef**. Als u een lokale site verwijzing verwijdert, wordt Azure geactiveerd om een tunnel te verwijderen. Afhankelijk van de configuratie die u hebt gemaakt, is er mogelijk geen **LocalNetworkSiteRef** vermeld.

```
<Gateway>
   <ConnectionsToLocalNetwork>
     <LocalNetworkSiteRef name="D1BFC9CB_Site2">
       <Connection type="IPsec" />
     </LocalNetworkSiteRef>
   </ConnectionsToLocalNetwork>
 </Gateway>
```

Voorbeeld:

```
<Gateway>
   <ConnectionsToLocalNetwork>
   </ConnectionsToLocalNetwork>
 </Gateway>
```

### <a name="local-network-sites"></a><a name="lns"></a>Lokale netwerk sites

Verwijder alle lokale sites die u niet meer gebruikt. Afhankelijk van de configuratie die u hebt gemaakt, is het mogelijk dat er geen **LocalNetworkSite** wordt weer gegeven.

```
<LocalNetworkSites>
  <LocalNetworkSite name="Site1">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>5.4.3.2</VPNGatewayAddress>
  </LocalNetworkSite>
  <LocalNetworkSite name="Site3">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>57.179.18.164</VPNGatewayAddress>
  </LocalNetworkSite>
 </LocalNetworkSites>
```

In dit voor beeld zijn alleen Site3 verwijderd.

```
<LocalNetworkSites>
  <LocalNetworkSite name="Site1">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>5.4.3.2</VPNGatewayAddress>
  </LocalNetworkSite>
 </LocalNetworkSites>
```

### <a name="client-addresspool"></a><a name="clientaddresss"></a>Client adres groep client

Als u een P2S-verbinding hebt met uw VNet, hebt u een **VPNClientAddressPool**. Verwijder de client adres groepen die overeenkomen met de gateway van het virtuele netwerk die u hebt verwijderd.

```
<Gateway>
    <VPNClientAddressPool>
      <AddressPrefix>10.1.0.0/24</AddressPrefix>
    </VPNClientAddressPool>
  <ConnectionsToLocalNetwork />
 </Gateway>
```

Voorbeeld:

```
<Gateway>
  <ConnectionsToLocalNetwork />
 </Gateway>
```

### <a name="gatewaysubnet"></a><a name="gwsub"></a>GatewaySubnet

Verwijder de **GatewaySubnet** die overeenkomt met het VNet.

```
<Subnets>
   <Subnet name="FrontEnd">
     <AddressPrefix>10.11.0.0/24</AddressPrefix>
   </Subnet>
   <Subnet name="GatewaySubnet">
     <AddressPrefix>10.11.1.0/29</AddressPrefix>
   </Subnet>
 </Subnets>
```

Voorbeeld:

```
<Subnets>
   <Subnet name="FrontEnd">
     <AddressPrefix>10.11.0.0/24</AddressPrefix>
   </Subnet>
 </Subnets>
```

## <a name="step-5-upload-the-network-configuration-file"></a><a name="upload"></a>Stap 5: het netwerk configuratie bestand uploaden

Sla uw wijzigingen op en upload het netwerk configuratie bestand naar Azure. Zorg ervoor dat u het bestandspad zo nodig wijzigt voor uw omgeving.

```powershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

Als de bewerking is gelukt, ziet de retour waarde er ongeveer uit als in dit voor beeld:

```
OperationDescription        OperationId                      OperationStatus                                                
--------------------        -----------                      ---------------                                           
Set-AzureVNetConfig         e0ee6e66-9167-cfa7-a746-7casb9   Succeeded
```

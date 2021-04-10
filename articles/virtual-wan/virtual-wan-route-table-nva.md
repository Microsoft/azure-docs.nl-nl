---
title: 'Virtueel WAN: route tabel voor virtuele hub maken naar NVA: Azure PowerShell'
description: Route tabel virtuele WAN-hub om verkeer naar een virtueel netwerk apparaat te sturen.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 6a6e701377956e39696567eff9a6a0abca927b88
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106055073"
---
# <a name="create-a-virtual-hub-route-table-to-steer-traffic-to-a-network-virtual-appliance"></a>Een route tabel voor de virtuele hub maken om verkeer naar een virtueel netwerk apparaat te sturen

Dit artikel laat u zien hoe u verkeer van een virtuele hub naar een virtueel netwerk apparaat kunt sturen. 

![Virtual WAN-diagram](./media/virtual-wan-route-table-nva/vwanroute.png)

In dit artikel leert u het volgende:

* Een WAN maken
* Een hub maken
* Virtuele hub-netwerk verbindingen maken
* Een hub-route maken
* Een routetabel maken
* De route tabel Toep assen

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Controleer of u aan de volgende criteria hebt voldaan:

* U hebt een virtueel netwerk apparaat (NVA). Dit is een software van derden van uw keuze die doorgaans wordt ingericht vanuit Azure Marketplace in een virtueel netwerk.
* Er is een privé-IP-adres toegewezen aan de NVA-netwerk interface. 
* De NVA kan niet worden geïmplementeerd in de virtuele hub. Het moet worden geïmplementeerd in een afzonderlijk VNet. Voor dit artikel wordt het NVA VNet aangeduid als ' DMZ VNet '.
* Aan het DMZ VNet kunnen een of meer virtuele netwerken zijn verbonden. In dit artikel wordt dit VNet aangeduid als ' indirect spoke VNet '. Deze VNets kunnen worden verbonden met het DMZ VNet met behulp van VNet-peering.
* Controleer of u 2 VNets al hebt gemaakt. Deze worden gebruikt als spoke VNets. Voor dit artikel zijn de VNet-spoke-adres ruimten 10.0.2.0/24 en 10.0.3.0/24. Zie [een virtueel netwerk maken met Power shell](../virtual-network/quick-create-powershell.md)als u meer informatie nodig hebt over het maken van een VNet.
* Zorg ervoor dat er geen virtuele netwerk gateways aanwezig zijn in een VNets.

## <a name="1-sign-in"></a><a name="signin"></a>1. aanmelden

Zorg ervoor dat u de meest recente versie van de Power shell-cmdlets voor Resource Manager installeert. Zie [How to install and configure Azure PowerShell](/powershell/azure/install-az-ps) (Azure PowerShell installeren en configureren) voor meer informatie over het installeren van de PowerShell-cmdlets. Dit is belangrijk omdat eerdere versies van de cmdlets niet de huidige waarden bevatten die u nodig hebt voor deze oefening.

1. Open de Power shell-console met verhoogde bevoegdheden en meld u aan bij uw Azure-account. Met deze cmdlet wordt u gevraagd om de aanmeldings referenties op te vragen. Nadat u zich hebt aangemeld, worden de instellingen van uw account gedownload zodat ze beschikbaar zijn voor Azure PowerShell.

   ```powershell
   Connect-AzAccount
   ```
2. Haal een lijst met uw Azure-abonnementen op.

   ```powershell
   Get-AzSubscription
   ```
3. Geef het abonnement op dat u wilt gebruiken.

   ```powershell
   Select-AzSubscription -SubscriptionName "Name of subscription"
   ```

## <a name="2-create-resources"></a><a name="rg"></a>2. resources maken

1. Een resourcegroep maken.

   ```powershell
   New-AzResourceGroup -Location "West US" -Name "testRG"
   ```
2. Maak een virtueel WAN.

   ```powershell
   $virtualWan = New-AzVirtualWan -ResourceGroupName "testRG" -Name "myVirtualWAN" -Location "West US"
   ```
3. Maak een virtuele hub.

   ```powershell
   New-AzVirtualHub -VirtualWan $virtualWan -ResourceGroupName "testRG" -Name "westushub" -AddressPrefix "10.0.1.0/24" -Location "West US"
   ```

## <a name="3-create-connections"></a><a name="connections"></a>3. verbindingen maken

Maak hub virtuele netwerk verbindingen van het indirecte spoke VNet en het DMZ VNet naar de virtuele hub.

  ```powershell
  $remoteVirtualNetwork1= Get-AzVirtualNetwork -Name "indirectspoke1" -ResourceGroupName "testRG"
  $remoteVirtualNetwork2= Get-AzVirtualNetwork -Name "indirectspoke2" -ResourceGroupName "testRG"
  $remoteVirtualNetwork3= Get-AzVirtualNetwork -Name "dmzvnet" -ResourceGroupName "testRG"

  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection1" -RemoteVirtualNetwork $remoteVirtualNetwork1
  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection2" -RemoteVirtualNetwork $remoteVirtualNetwork2
  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection3" -RemoteVirtualNetwork $remoteVirtualNetwork3
  ```

## <a name="4-create-a-virtual-hub-route"></a><a name="route"></a>4. Maak een virtuele hub-route

Voor dit artikel zijn de indirecte spoke VNet-adres ruimten 10.0.2.0/24 en 10.0.3.0/24, en het privé IP-adres van DMZ NVA Network Interface is 10.0.4.5.

```powershell
$route1 = New-AzVirtualHubRoute -AddressPrefix @("10.0.2.0/24", "10.0.3.0/24") -NextHopIpAddress "10.0.4.5"
```

## <a name="5-create-a-virtual-hub-route-table"></a><a name="applyroute"></a>5. een route tabel voor de virtuele hub maken

Maak een route tabel voor de virtuele hub en pas de gemaakte route daar toe.
 
```powershell
$routeTable = New-AzVirtualHubRouteTable -Route @($route1)
```

## <a name="6-commit-the-changes"></a><a name="commit"></a>6. de wijzigingen door voeren

Voer de wijzigingen door in de virtuele hub.

```powershell
Update-AzVirtualHub -ResourceGroupName "testRG" -Name "westushub" -RouteTable $routeTable
```

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Virtual WAN de pagina [Overzicht van Virtual WAN](virtual-wan-about.md).

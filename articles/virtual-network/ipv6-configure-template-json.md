---
title: Een IPv6-toepassing met dubbele stack implementeren met de Basic-Load Balancer in het virtuele netwerk van Azure-Resource Manager-sjabloon
titlesuffix: Azure Virtual Network
description: In dit artikel wordt beschreven hoe u een dual stack-toepassing met IPv6 kunt implementeren in een virtueel Azure-netwerk met Azure Resource Manager VM-sjablonen.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 548b416ed0f21df83dafdb1c2d7015c625c36e4c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95244290"
---
# <a name="deploy-an-ipv6-dual-stack-application-with-basic-load-balancer-in-azure---template"></a>Een IPv6-toepassing met dubbele stack implementeren met Basic-Load Balancer in azure-sjabloon

Dit artikel bevat een lijst met IPv6-configuratie taken met het gedeelte van de Azure Resource Manager VM-sjabloon dat van toepassing is op. Gebruik de sjabloon die in dit artikel wordt beschreven voor het implementeren van een dual stack (IPv4 + IPv6)-toepassing met een basis Load Balancer met een dual stack virtueel netwerk met IPv4-en IPv6-subnetten, een basis Load Balancer met dubbele (IPv4 + IPv6) front-end configuraties, Vm's met Nic's met een dubbele IP-configuratie, netwerk beveiligings groep en open bare Ip's.

Als u een toepassing met dubbele stack (IPV4 + IPv6) wilt implementeren met behulp van Standard Load Balancer, raadpleegt u [een IPv6-toepassing met dubbele stack implementeren met Standard Load Balancer-sjabloon](ipv6-configure-standard-load-balancer-template-json.md).

## <a name="required-configurations"></a>Vereiste configuraties

Zoek naar de sjabloon secties in de sjabloon om te zien waar ze zich moeten voordoen.

### <a name="ipv6-addressspace-for-the-virtual-network"></a>IPv6-addressSpace voor het virtuele netwerk

Sjabloon sectie die moet worden toegevoegd:

```JSON
        "addressSpace": {
          "addressPrefixes": [
            "[variables('vnetv4AddressRange')]",
            "[variables('vnetv6AddressRange')]"    
```

### <a name="ipv6-subnet-within-the-ipv6-virtual-network-addressspace"></a>IPv6-subnet binnen het virtuele IPv6-netwerk addressSpace

Sjabloon sectie die moet worden toegevoegd:
```JSON
          {
            "name": "V6Subnet",
            "properties": {
              "addressPrefix": "[variables('subnetv6AddressRange')]"
            }

```

### <a name="ipv6-configuration-for-the-nic"></a>IPv6-configuratie voor de NIC

Sjabloon sectie die moet worden toegevoegd:
```JSON
          {
            "name": "ipconfig-v6",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
          "privateIPAddressVersion":"IPv6",
              "subnet": {
                "id": "[variables('v6-subnet-id')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(resourceId('Microsoft.Network/loadBalancers','loadBalancer'),'/backendAddressPools/LBBAP-v6')]"
                }
```

### <a name="ipv6-network-security-group-nsg-rules"></a>NSG-regels (IPv6-netwerk beveiligings groep)

```JSON
          {
            "name": "default-allow-rdp",
            "properties": {
              "description": "Allow RDP",
              "protocol": "Tcp",
              "sourcePortRange": "33819-33829",
              "destinationPortRange": "5000-6000",
              "sourceAddressPrefix": "fd00:db8:deca:deed::/64",
              "destinationAddressPrefix": "fd00:db8:deca:deed::/64",
              "access": "Allow",
              "priority": 1003,
              "direction": "Inbound"
            }
```

## <a name="conditional-configuration"></a>Voorwaardelijke configuratie

Als u een virtueel netwerk apparaat gebruikt, voegt u IPv6-routes toe aan de route tabel. Anders is deze configuratie optioneel.

```JSON
    {
      "type": "Microsoft.Network/routeTables",
      "name": "v6route",
      "apiVersion": "[variables('ApiVersion')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "routes": [
          {
            "name": "v6route",
            "properties": {
              "addressPrefix": "fd00:db8:deca:deed::/64",
              "nextHopType": "VirtualAppliance",
              "nextHopIpAddress": "fd00:db8:ace:f00d::1"
            }
```

## <a name="optional-configuration"></a>Optionele configuratie

### <a name="ipv6-internet-access-for-the-virtual-network"></a>IPv6 Internet toegang voor het virtuele netwerk

```JSON
{
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-public-ip-addresses"></a>Open bare IPv6-IP-adressen

```JSON
    {
      "apiVersion": "[variables('ApiVersion')]",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "lbpublicip-v6",
      "location": "[resourceGroup().location]",
      "properties": {
        "publicIPAllocationMethod": "Dynamic",
        "publicIPAddressVersion": "IPv6"
      }
```

### <a name="ipv6-front-end-for-load-balancer"></a>IPv6-front-end voor Load Balancer

```JSON
          {
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-back-end-address-pool-for-load-balancer"></a>Adres groep voor IPv6-back-end voor Load Balancer

```JSON
              "backendAddressPool": {
                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', 'loadBalancer'), '/backendAddressPools/LBBAP-v6')]"
              },
              "protocol": "Tcp",
              "frontendPort": 8080,
              "backendPort": 8080
            },
            "name": "lbrule-v6"
```

### <a name="ipv6-load-balancer-rules-to-associate-incoming-and-outgoing-ports"></a>IPv6-load balancer regels om binnenkomende en uitgaande poorten te koppelen

```JSON
          {
            "name": "ipconfig-v6",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
          "privateIPAddressVersion":"IPv6",
              "subnet": {
                "id": "[variables('v6-subnet-id')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(resourceId('Microsoft.Network/loadBalancers','loadBalancer'),'/backendAddressPools/LBBAP-v6')]"
                }
```

## <a name="sample-vm-template-json"></a>Voor beeld-VM-sjabloon JSON
Als u een IPv6-toepassing met dubbele stack wilt implementeren met de Basic-Load Balancer in een virtueel Azure-netwerk met behulp van Azure Resource Manager sjabloon, kunt u [hier](https://azure.microsoft.com/resources/templates/ipv6-in-vnet/)voorbeeld sjabloon bekijken.

## <a name="next-steps"></a>Volgende stappen

U kunt details vinden over de prijzen voor [open bare IP-adressen](https://azure.microsoft.com/pricing/details/ip-addresses/), [netwerk bandbreedte](https://azure.microsoft.com/pricing/details/bandwidth/)of [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).

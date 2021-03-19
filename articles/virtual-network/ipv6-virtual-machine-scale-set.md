---
title: Schaal sets voor virtuele machines implementeren met IPv6 in azure
titlesuffix: Azure Virtual Network
description: In dit artikel wordt beschreven hoe u schaal sets voor virtuele machines met IPv6 kunt implementeren in een virtueel Azure-netwerk.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: f969d7edc22c9c36481ca42449193af5f8c7b0d9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84709994"
---
# <a name="deploy-virtual-machine-scale-sets-with-ipv6-in-azure"></a>Schaal sets voor virtuele machines implementeren met IPv6 in azure

In dit artikel wordt beschreven hoe u een virtuele-machine Schaalset met dubbele stack (IPv4 + IPv6) implementeert met een externe load balancer met dubbele stack in een virtueel Azure-netwerk. Het proces voor het maken van een schaalset voor virtuele machines die met IPv6 kan worden uitgevoerd, is bijna identiek aan het proces voor het maken van afzonderlijke Vm's die [hier](ipv6-configure-standard-load-balancer-template-json.md)worden beschreven. U begint met de stappen die vergelijkbaar zijn met die van afzonderlijke Vm's:
1.    Open bare IPv4-en IPv6-Ip's maken.
2.    Maak een dual stack-load balancer.  
3.    Maak regels voor netwerk beveiligings groepen (NSG).  

De enige stap die verschilt van afzonderlijke Vm's is het maken van de netwerk interface configuratie (NIC) die gebruikmaakt van de virtuele-machine Scale set resource: networkProfile/Networkinterfaceconfigurations schaalset. De JSON-structuur is vergelijkbaar met die van het object micro soft. Network/networkInterfaces dat wordt gebruikt voor afzonderlijke Vm's, waarbij het instellen van de NIC en de IPv4 IpConfiguration als primaire interface wordt gebruikt, zoals wordt weer gegeven **in het volgende**  voor beeld:

```json
          "networkProfile": {
            "networkInterfaceConfigurations": [
              {
                "name": "[variables('nicName')]",
                "properties": {
                  "primary": true,
          "networkSecurityGroup": {
            "id": "[resourceId('Microsoft.Network/networkSecurityGroups','VmssNsg')]"
          },                  
                  "ipConfigurations": [
                    {
                      "name": "[variables('ipConfigName')]",
                      "properties": {
                        "primary": true,
                        "subnet": {
                          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', 'MyvirtualNetwork','Mysubnet')]"
                        },
                        "privateIPAddressVersion":"IPv4",                       
                        "publicipaddressconfiguration": {
                          "name": "pub1",
                          "properties": {
                            "idleTimeoutInMinutes": 15
                          }
                        },
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/backendAddressPools', 'loadBalancer', 'bePool'))]"
                          }
                        ],
                        "loadBalancerInboundNatPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/inboundNatPools', 'loadBalancer', 'natPool')]"
                          }
                        ]
                      }
                    },
                    {
                      "name": "[variables('ipConfigNameV6')]",
                      "properties": {
                        "subnet": {
                          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets','MyvirtualNetwork','Mysubnet')]"
                        },
                        "privateIPAddressVersion":"IPv6",
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/backendAddressPools', 'loadBalancer','bePoolv6')]"
                          }
                        ],                        
                      }
                    }
                  ]
                }
              }
            ]
          }

```


## <a name="sample-virtual-machine-scale-set-template-json"></a>Voor beeld van een JSON-sjabloon voor virtuele-machine schaal sets

Voor het implementeren van een virtuele-machine Schaalset met dubbele stack (IPv4 + IPv6) met dubbele stack externe Load Balancer en de [voorbeeld sjabloon virtuele](https://azure.microsoft.com/resources/templates/ipv6-in-vnet-vmss/)netwerk weergave.
## <a name="next-steps"></a>Volgende stappen

Zie [Wat is IPv6 voor azure Virtual Network?](ipv6-overview.md)voor meer informatie over IPv6-ondersteuning in azure Virtual Networks.

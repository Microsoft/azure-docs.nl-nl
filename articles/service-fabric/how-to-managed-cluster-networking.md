---
title: Netwerk instellingen configureren voor door Service Fabric beheerde clusters (preview-versie)
description: Meer informatie over het configureren van uw Service Fabric Managed cluster voor NSG-regels, toegang tot de RDP-poort, regels voor taak verdeling en meer.
ms.topic: how-to
ms.date: 03/02/2021
ms.openlocfilehash: e17251523c0720665c4c6f5b7811304eebc9923e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101745914"
---
# <a name="configure-network-settings-for-service-fabric-managed-clusters-preview"></a>Netwerk instellingen configureren voor door Service Fabric beheerde clusters (preview-versie)

Service Fabric beheerde clusters worden gemaakt met een standaard netwerk configuratie. Deze configuratie bestaat uit verplichte regels voor essentiële cluster functionaliteit en enkele optionele regels die zijn bedoeld om de configuratie van klanten gemakkelijker te maken.

Naast de standaard netwerk configuratie kunt u de netwerk regels aanpassen om te voldoen aan de behoeften van uw scenario.

## <a name="nsg-rules-guidance"></a>Richt lijnen voor NSG-regels

Houd rekening met deze overwegingen bij het maken van nieuwe NSG-regels voor uw beheerde cluster.

* Service Fabric beheerde clusters Reserveer het NSG-regel prioriteits bereik van 0 tot 999 voor essentiële functionaliteit. U kunt geen aangepaste NSG-regels maken met een prioriteits waarde van minder dan 1000.
* Service Fabric beheerde clusters hebben het prioriteits bereik 3001 tot 4000 voor het maken van optionele NSG-regels. Deze regels worden automatisch toegevoegd om zo snel en eenvoudig configuraties te maken. U kunt deze regels overschrijven door aangepaste NSG-regels toe te voegen aan het prioriteits bereik 1000 tot 3000.
* Aangepaste NSG-regels moeten een prioriteit hebben binnen het bereik van 1000 tot 3000.

## <a name="apply-nsg-rules"></a>NSG regels Toep assen

Met klassieke (niet-beheerde) Service Fabric clusters moet u een afzonderlijke resource van *micro soft. Network/networkSecurityGroups* declareren en beheren om [regels voor netwerk BEVEILIGINGS groepen (NSG) toe te passen op uw cluster](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-nsg-cluster-65-node-3-nodetype). Met Service Fabric beheerde clusters kunt u NSG-regels rechtstreeks toewijzen in de cluster resource van uw implementatie sjabloon.

Gebruik de eigenschap [networkSecurityRules](/azure/templates/microsoft.servicefabric/managedclusters#managedclusterproperties-object) van uw resource van *micro soft. ServiceFabric/managedclusters* (versie `2021-01-01-preview` of hoger) om NSG regels toe te wijzen. Bijvoorbeeld:

```json
            "apiVersion": "2021-01-01-preview",
            "type": "Microsoft.ServiceFabric/managedclusters",
            ...
            "properties": {
                ...
                "networkSecurityRules" : [
                    {
                        "name": "AllowCustomers",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "Internet",
                        "destinationAddressPrefix": "*",
                        "destinationPortRange": "33000-33499",
                        "access": "Allow",
                        "priority": 2001,
                        "direction": "Inbound" 
                    },
                    {
                        "name": "AllowARM",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "AzureResourceManager",
                        "destinationAddressPrefix": "*",
                        "destinationPortRange": "33500-33699",
                        "access": "Allow",
                        "priority": 2002,
                        "direction": "Inbound" 
                    },
                    {
                        "name": "DenyCustomers",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "Internet",
                        "destinationAddressPrefix": "*",
                        "destinationPortRange": "33700-33799",
                        "access": "Deny",
                        "priority": 2003,
                        "direction": "Outbound"
                    },
                    {
                        "name": "DenyRDP",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "*",
                        "destinationAddressPrefix": "VirtualNetwork",
                        "destinationPortRange": "3389",
                        "access": "Deny",
                        "priority": 2004,
                        "direction": "Inbound",
                        "description": "Override for optional SFMC_AllowRdpPort rule. This is required in tests to avoid Sev2 incident for security policy violation."
                    }
                ],
                "fabricSettings": [
                ...
                ]
            }
```

## <a name="rdp-ports"></a>RDP-poorten

Service Fabric beheerde clusters maken standaard geen toegang tot de RDP-poorten. U kunt RDP-poorten naar Internet openen door de volgende eigenschap in te stellen op een Service Fabric beheerde cluster bron.

```json
"allowRDPAccess": true 
```

Wanneer de eigenschap Rdptoegangtoestaan is ingesteld op True, wordt de volgende NSG-regel toegevoegd aan uw cluster implementatie.  

```json
{
    "name": "SFMC_AllowRdpPort", 
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "Optional rule to open RDP ports.",
        "protocol": "tcp",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 3002,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRange": "3389"
    }
}
```

## <a name="clientconnection-and-httpgatewayconnection-ports"></a>ClientConnection-en HttpGatewayConnection-poorten

### <a name="nsg-rule-sfmc_allowservicefabricgatewaytosfrp"></a>NSG-regel: SFMC_AllowServiceFabricGatewayToSFRP

Er wordt een standaard NSG-regel toegevoegd zodat de Service Fabric resource provider toegang kan krijgen tot de clientConnectionPort en httpGatewayConnectionPort van het cluster. Deze regel staat toegang tot de poorten toe via de servicetag ' ServiceFabric '.

>[!NOTE]
>Deze regel is altijd toegevoegd en kan niet worden overschreven.

```json
{ 
    "name": "SFMC_AllowServiceFabricGatewayToSFRP", 
    "type": "Microsoft.Network/networkSecurityGroups/securityRules", 
    "properties": { 
        "description": "This is required rule to allow SFRP to connect to the cluster. This rule cannot be overridden.", 
        "protocol": "TCP", 
        "sourcePortRange": "*", 
        "sourceAddressPrefix": "ServiceFabric", 
        "destinationAddressPrefix": "VirtualNetwork", 
        "access": "Allow", 
        "priority": 500, 
        "direction": "Inbound", 
        "sourcePortRanges": [], 
        "destinationPortRanges": [ 
            "19000", 
            "19080" 
        ] 
    } 
}
```

### <a name="nsg-rule-sfmc_allowservicefabricgatewayports"></a>NSG-regel: SFMC_AllowServiceFabricGatewayPorts

Dit is een optionele NSG-regel om toegang tot de clientConnectionPort en httpGatewayPort van Internet toe te staan. Met deze regel kunnen klanten toegang krijgen tot SFX, verbinding maken met het cluster met behulp van Power shell en gebruikmaken van Service Fabric cluster-API-eind punten van buiten de. 

>[!NOTE]
>Deze regel wordt niet toegevoegd als er een aangepaste regel is met dezelfde toegangs-, richtings-en protocol waarden voor dezelfde poort. U kunt deze regel overschrijven met aangepaste NSG-regels. 

```json
{ 
    "name": "SFMC_AllowServiceFabricGatewayPorts", 
    "type": "Microsoft.Network/networkSecurityGroups/securityRules", 
    "properties": { 
        "description": "Optional rule to open SF cluster gateway ports. To override add a custom NSG rule for gateway ports in priority range 1000-3000.", 
        "protocol": "tcp", 
        "sourcePortRange": "*", 
        "sourceAddressPrefix": "*", 
        "destinationAddressPrefix": "VirtualNetwork", 
        "access": "Allow", 
        "priority": 3001, 
        "direction": "Inbound", 
        "sourcePortRanges": [], 
        "destinationPortRanges": [ 
            "19000", 
            "19080" 
        ] 
    } 
}
```

## <a name="load-balancer-ports"></a>Load Balancer-poorten

Service Fabric beheerde clusters Maak een NSG-regel in het standaard prioriteits bereik voor alle load balancer (LB)-poorten die zijn geconfigureerd in de sectie ' loadBalancingRules ' onder *ManagedCluster* -eigenschappen. Deze regel opent LB-poorten voor inkomend verkeer van het internet.  

>[!NOTE]
>Deze regel wordt toegevoegd aan het optionele prioriteits bereik en kan worden overschreven door aangepaste NSG-regels toe te voegen.

```json
{
    "name": "SFMC_AllowLoadBalancedPorts",
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "Optional rule to open LB ports",
        "protocol": "*", 
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 3003,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRanges": [
        "80", "8080", "4343"
        ]
    }
}
```

## <a name="load-balancer-probes"></a>Load Balancer-tests

Service Fabric beheerde clusters maken automatisch load balancer tests voor infrastructuur gateway-poorten en alle poorten die zijn geconfigureerd in de sectie ' loadBalancingRules ' van eigenschappen van beheerde clusters.

```json
{ 
  "value": [ 
    { 
        "name": "FabricTcpGateway", 
        "properties": { 
            "provisioningState": "Succeeded", 
            "protocol": "Tcp", 
            "port": 19000, 
            "intervalInSeconds": 5, 
            "numberOfProbes": 2, 
            "loadBalancingRules": [ 
                { 
                    "id": "<>"
                } 
            ] 
        }, 
        "type": "Microsoft.Network/loadBalancers/probes" 
    }, 
    { 
        "name": "FabricHttpGateway", 
        "properties": { 
            "provisioningState": "Succeeded", 
            "protocol": "Tcp", 
            "port": 19080, 
            "intervalInSeconds": 5, 
            "numberOfProbes": 2, 
            "loadBalancingRules": [ 
                { 
                    "id": "<>" 
                } 
            ]
        },
        "type": "Microsoft.Network/loadBalancers/probes" 
    },
    {
        "name": "probe1_tcp_8080", 
        "properties": { 
            "provisioningState": "Succeeded", 
            "protocol": "Tcp", 
            "port": 8080, 
            "intervalInSeconds": 5, 
            "numberOfProbes": 2, 
            "loadBalancingRules": [ 
            { 
                "id": "<>" 
            } 
        ] 
      }, 
      "type": "Microsoft.Network/loadBalancers/probes" 
    } 
  ] 
} 
```

## <a name="next-steps"></a>Volgende stappen

[Configuratie opties voor beheerde Cluster Service Fabric](how-to-managed-cluster-configuration.md)

[Overzicht van Service Fabric beheerde clusters](overview-managed-cluster.md)

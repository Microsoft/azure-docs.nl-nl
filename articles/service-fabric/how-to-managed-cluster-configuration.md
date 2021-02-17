---
title: Uw beheerde Service Fabric-cluster configureren (preview-versie)
description: Meer informatie over het configureren van uw Service Fabric Managed cluster voor automatische upgrades van besturings systemen, NSG-regels en meer.
ms.topic: how-to
ms.date: 02/15/2021
ms.openlocfilehash: 90ba5057f06cf8841e278b8d921d812286459c49
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100642401"
---
# <a name="service-fabric-managed-cluster-preview-configuration-options"></a>Configuratie opties voor Service Fabric Managed cluster (preview)

Naast het selecteren van de [service Fabric Managed cluster SKU](overview-managed-cluster.md#service-fabric-managed-cluster-skus) bij het maken van uw cluster, zijn er een aantal andere manieren om dit te configureren. In de huidige preview kunt u het volgende doen:

* Een [extensie voor virtuele-machine schaal sets](how-to-managed-cluster-vmss-extension.md) toevoegen aan een knooppunt type
* [Automatische upgrades van besturings systemen](how-to-managed-cluster-configuration.md#enable-automatic-os-image-upgrades) voor uw knoop punten inschakelen
* Schakel de [versleuteling van besturings systemen en gegevens schijven](how-to-enable-managed-cluster-disk-encryption.md) in op uw knoop punten
* Over [netwerk configuraties](how-to-managed-cluster-configuration.md#networking-configurations)
* [Beheerde identiteit](how-to-managed-identity-managed-cluster-virtual-machine-scale-sets.md) configureren voor de knooppunt typen

## <a name="networking-configurations"></a>Netwerk configuraties

Service Fabric beheerde clusters worden gemaakt met een standaard netwerk configuratie. Deze configuratie bestaat uit verplichte regels voor essentiële cluster functionaliteit en enkele optionele regels die zijn bedoeld om de configuratie van klanten gemakkelijker te maken.

Naast de standaard netwerk configuratie kunt u de netwerk regels aanpassen om te voldoen aan de behoeften van uw scenario. 

### <a name="nsg-rules-guidance"></a>Richt lijnen voor NSG-regels

* Service Fabric beheerde clusters Reserveer het NSG-regel prioriteits bereik van 0 tot 999 voor essentiële functionaliteit. U kunt geen aangepaste NSG-regels maken met een prioriteits waarde van minder dan 1000. 
* Service Fabric beheerde clusters hebben het prioriteits bereik 3001 tot 4000 voor het maken van optionele NSG-regels. Deze regels worden automatisch toegevoegd om zo snel en eenvoudig configuraties te maken. U kunt deze regels overschrijven door aangepaste NSG-regels toe te voegen aan het prioriteits bereik 1000 tot 3000. 
* Aangepaste NSG-regels moeten een prioriteit hebben binnen het bereik van 1000 tot 3000. 


### <a name="apply-nsg-rules"></a>NSG regels Toep assen

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

### <a name="rdp-ports"></a>RDP-poorten 

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

### <a name="clientconnection-and-httpgatewayconnection-ports"></a>ClientConnection-en HttpGatewayConnection-poorten 

**NSG-regel: SFMC_AllowServiceFabricGatewayToSFRP** Er wordt een standaard NSG-regel toegevoegd zodat de Service Fabric resource provider toegang kan krijgen tot de clientConnectionPort en httpGatewayConnectionPort van het cluster. Deze regel staat toegang tot de poorten toe via de servicetag ' ServiceFabric '.

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

**NSG-regel: SFMC_AllowServiceFabricGatewayPorts** Dit is een optionele NSG-regel om toegang tot de clientConnectionPort en httpGatewayPort van Internet toe te staan. Met deze regel kunnen klanten toegang krijgen tot SFX, verbinding maken met het cluster met behulp van Power shell en gebruikmaken van Service Fabric cluster-API-eind punten van buiten de. 

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

### <a name="load-balancer-ports"></a>Load Balancer-poorten 

Service Fabric beheerde clusters Maak een NSG-regel in het standaard prioriteits bereik voor alle LB-poorten die zijn geconfigureerd in de sectie ' loadBalancingRules ' onder ManagedCluster-eigenschappen. Deze regel open LB-poorten voor binnenkomend verkeer van Internet.  

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

### <a name="load-balancer-probes"></a>Load Balancer-tests

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

## <a name="enable-automatic-os-image-upgrades"></a>Automatische upgrade van besturings systeem-installatie kopieën inschakelen

U kunt ervoor kiezen om automatische installatie kopieën van besturings systemen in te scha kelen naar de virtuele machines waarop uw beheerde cluster knooppunten worden uitgevoerd. Hoewel de virtuele-machine Scale set resources namens u worden beheerd met Service Fabric beheerde clusters, is het uw keuze om automatische installatie kopieën van besturings systemen in te scha kelen voor uw cluster knooppunten. Net als bij [klassieke service Fabric](service-fabric-best-practices-infrastructure-as-code.md#azure-virtual-machine-operating-system-automatic-upgrade-configuration) clusters worden beheerde cluster knooppunten niet standaard bijgewerkt, om onbedoelde onderbrekingen voor uw cluster te voor komen.

Automatische upgrades van besturings systemen inschakelen:

* Gebruik de `2021-01-01-preview` (of latere) versie van *micro soft. ServiceFabric/Managedclusters* en *micro soft. ServiceFabric/managedclusters/nodetypes* resources
* Stel de eigenschap van het cluster `enableAutoOSUpgrade` in op *True*
* Stel de eigenschap resource van het cluster nodeTypes `vmImageVersion` in op de *nieuwste*

Bijvoorbeeld:

```json
    {
      "apiVersion": "2021-01-01-preview",
      "type": "Microsoft.ServiceFabric/managedclusters",
      ...
      "properties": {
        ...
        "enableAutoOSUpgrade": true
      },
    },
    {
      "apiVersion": "2021-01-01-preview",
      "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
       ...
      "properties": {
        ...
        "vmImageVersion": "latest",
        ...
      }
    }
}

```

Wanneer deze functie is ingeschakeld, worden er in het beheerde Cluster installatie kopieën van besturings systemen in het beheer van Service Fabric gestart. Als er een nieuwe versie van het besturings systeem beschikbaar is, worden de cluster knooppunt typen (virtuele-machine schaal sets) een voor een bijgewerkt. Service Fabric runtime-upgrades worden alleen uitgevoerd nadat er is bevestigd dat er geen installatie kopieën van het besturings systeem voor het cluster knooppunt worden bijgewerkt.

Als een upgrade mislukt, wordt Service Fabric na 24 uur opnieuw geprobeerd, gedurende een maximum van drie nieuwe pogingen. Net als bij klassiek (onbeheerd) Service Fabric upgrades, kunnen beschadigde apps of knoop punten de upgrade van de installatie kopie van het besturings systeem blok keren.

Zie [automatische besturingssysteem installatie kopieën met virtuele-machine schaal sets van Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)voor meer informatie over het bijwerken van installatie kopieën.

## <a name="next-steps"></a>Volgende stappen

[Koppeling naar voorbeeld sjabloon]

[Overzicht van beheerd cluster]

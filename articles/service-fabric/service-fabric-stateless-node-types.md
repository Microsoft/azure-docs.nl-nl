---
title: Alleen stateless knooppunt typen implementeren in een Service Fabric cluster
description: Meer informatie over het maken en implementeren van stateless knooppunt typen in het Azure service Fabric-cluster.
author: peterpogorski
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: pepogors
ms.openlocfilehash: 266c04a049cab574576f781c397aee566efe5372
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516621"
---
# <a name="deploy-an-azure-service-fabric-cluster-with-stateless-only-node-types-preview"></a>Een Azure Service Fabric-cluster implementeren met stateless knooppunt typen (preview-versie)
Service Fabric knooppunt typen worden geleverd met inherente veronderstelling dat op een bepaald moment stateful Services kunnen worden geplaatst op de knoop punten. Stateless knooppunt typen versoepelen deze veronderstelling voor een knooppunt type, waardoor het knooppunt type ook andere functies kan gebruiken, zoals het snel uitbreiden van bewerkingen, ondersteuning voor automatische upgrades van besturings systemen op Bronze duurzaamheid en uitschalen naar meer dan 100 knoop punten in één virtuele-machine schaalset.

* Primaire knooppunt typen kunnen niet stateless worden geconfigureerd
* Stateless knooppunt typen worden alleen ondersteund met Bronze-duurzaamheids niveaus
* Stateless knooppunt typen worden alleen ondersteund voor Service Fabric runtime versie 7.1.409 of hoger.


Er zijn voorbeeld sjablonen beschikbaar: [service Fabric sjabloon voor stateless knooppunt typen](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="enabling-stateless-node-types-in-service-fabric-cluster"></a>Stateless knooppunt typen in Service Fabric cluster inschakelen
Als u een of meer knooppunt typen als stateless wilt instellen in een cluster bron, stelt u de eigenschap **isStateless** in op ' True '. Als u een Service Fabric cluster met stateless knooppunt typen implementeert, moet u Mini maal één primair knooppunt type hebben in de cluster bron.

* De Service Fabric cluster resource apiVersion moet 2020-12-01-preview of hoger zijn.

```json
{
    "nodeTypes": [
    {
        "name": "[parameters('vmNodeType0Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt0applicationEndPort')]",
            "startPort": "[parameters('nt0applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt0fabricTcpGatewayPort')]",
        "durabilityLevel": "Bronze",
        "ephemeralPorts": {
            "endPort": "[parameters('nt0ephemeralEndPort')]",
            "startPort": "[parameters('nt0ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt0fabricHttpGatewayPort')]",
        "isPrimary": true,
        "isStateles": false,
        "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType1Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt1applicationEndPort')]",
            "startPort": "[parameters('nt1applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt1fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt1ephemeralEndPort')]",
            "startPort": "[parameters('nt1ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt1fabricHttpGatewayPort')]",
        "isPrimary": false,
        "isStateless": true,
        "vmInstanceCount": "[parameters('nt1InstanceCount')]"
    }    
    ],
}
```

## <a name="configuring-virtual-machine-scale-set-for-stateless-node-types"></a>Schaalset voor virtuele machines configureren voor stateless knooppunt typen
Als u stateless knooppunt typen wilt inschakelen, moet u de onderliggende resource van de virtuele-machine schaalset op de volgende manier configureren:

* De waarde van de eigenschap  **singlePlacementGroup** , die moet worden ingesteld op waar/onwaar, afhankelijk van de vereiste om naar meer dan 100 vm's te schalen.
* De **upgrade mode ingesteld** van de schaalset die moet worden ingesteld op Rolling.
* Voor de modus voor rolling upgrades is een configuratie van een toepassings status of status controles vereist. Configureer de status test met de standaard configuratie voor stateless knooppunt typen, zoals hieronder wordt voorgesteld. Zodra toepassingen zijn geïmplementeerd op het NodeType, kunnen de poorten voor status controle/status worden gewijzigd om de status van de toepassing te bewaken.

```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "properties": {
        "overprovision": "[variables('overProvision')]",
        "upgradePolicy": {
          "mode": "Rolling",
          "automaticOSUpgradePolicy": {
            "enableAutomaticOSUpgrade": true
          }
        }
    }
    "virtualMachineProfile": {
    "extensionProfile": {
    "extensions": [
    {
    "name": "[concat(parameters('vmNodeType1Name'),'_ServiceFabricNode')]",
    "properties": {
        "type": "ServiceFabricNode",
        "autoUpgradeMinorVersion": false,
        "publisher": "Microsoft.Azure.ServiceFabric",
        "settings": {
            "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
            "nodeTypeRef": "[parameters('vmNodeType1Name')]",
            "dataPath": "D:\\\\SvcFab",
            "durabilityLevel": "Silver",
            "certificate": {
                "thumbprint": "[parameters('certificateThumbprint')]",
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
            "systemLogUploadSettings": {
                "Enabled": true
            },
        },
        "typeHandlerVersion": "1.0"
    }
    },
    {
        "type": "extensions",
        "name": "HealthExtension",
        "properties": {
            "publisher": "Microsoft.ManagedServices",
            "type": "ApplicationHealthWindows",
            "autoUpgradeMinorVersion": true,
            "typeHandlerVersion": "1.0",
            "settings": {
            "protocol": "tcp",
            "port": "19000"
            }
            }
        },
    ]
}
```

## <a name="networking-requirements"></a>Netwerkvereisten
### <a name="public-ip-and-load-balancer-resource"></a>Openbaar IP-adres en Load Balancer bron
Als u wilt schalen naar meer dan 100 Vm's in een resource met een schaalset voor virtuele machines, moet de load balancer en IP-resource waarnaar wordt verwezen door die virtuele-machine schaalset, beide gebruikmaken van een *standaard* -SKU. Als u een load balancer of IP-bron maakt zonder de SKU-eigenschap, wordt een basis-SKU gemaakt die geen ondersteuning biedt voor schalen naar meer dan 100 Vm's. Een standaard-SKU load balancer blokkeert standaard al het verkeer van de buiten kant. Als u buiten verkeer wilt toestaan, moet er een NSG in het subnet worden geïmplementeerd.

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat('LB','-', parameters('clusterName')]",
    "location": "[parameters('computeLocation')]",
    "sku": {
        "name": "Standard"
    }
}
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB','-', parameters('clusterName')]", 
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Network/networkSecurityGroups/', concat('nsg', parameters('subnet0Name')))]"
    ],
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "subnets": [
        {
            "name": "[parameters('subnet0Name')]",
            "properties": {
                "addressPrefix": "[parameters('subnet0Prefix')]",
                "networkSecurityGroup": {
                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', concat('nsg', parameters('subnet0Name')))]"
              }
            }
          }
        ]
    },
    "sku": {
        "name": "Standard"
    }
}
```

>[!NOTE]
> Het is niet mogelijk om een in-place wijziging van de SKU in het open bare IP-adres en load balancer resources uit te voeren. Zie de sectie migratie in dit artikel als u migreert van bestaande resources die een basis-SKU hebben.

### <a name="virtual-machine-scale-set-nat-rules"></a>NAT-regels voor schaal sets voor virtuele machines
De load balancer binnenkomende NAT-regels moeten overeenkomen met de NAT-groepen van de virtuele-machine schaalset. Elke schaalset voor virtuele machines moet een unieke binnenkomende NAT-groep hebben.

```json
{
"inboundNatPools": [
    {
        "name": "LoadBalancerBEAddressNatPool0",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "50999",
            "frontendPortRangeStart": "50000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool1",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "51999",
            "frontendPortRangeStart": "51000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool2",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "52999",
            "frontendPortRangeStart": "52000",
            "protocol": "tcp"
        }
    }
    ]
}
```

### <a name="standard-sku-load-balancer-outbound-rules"></a>Standaard SKU Load Balancer uitgaande regels
Standard Load Balancer en standaard open bare IP introduceren nieuwe mogelijkheden en verschillende gedragingen voor uitgaande connectiviteit in vergelijking met de basis-Sku's. Als u een uitgaande verbinding wilt gebruiken bij het werken met standaard-Sku's, moet u deze expliciet definiëren met een openbaar IP-adres of standaard open bare Load Balancer. Zie voor meer informatie [uitgaande verbindingen](../load-balancer/load-balancer-outbound-connections.md) en [Azure Standard Load Balancer](../load-balancer/load-balancer-overview.md).

>[!NOTE]
> De standaard sjabloon verwijst naar een NSG waarmee al het uitgaande verkeer standaard wordt toegestaan. Binnenkomend verkeer is beperkt tot de poorten die vereist zijn voor Service Fabric-beheer bewerkingen. De NSG-regels kunnen worden aangepast om te voldoen aan uw vereisten.

>[!NOTE]
> Een Service Fabric cluster dat gebruikmaakt van een standaard SKU SLB moet ervoor zorgen dat elk knooppunt type een regel voor uitgaand verkeer op poort 443 toestaat. Dit is nodig voor het volt ooien van de installatie van het cluster en een implementatie zonder een dergelijke regel zal mislukken.



### <a name="migrate-to-using-stateless-node-types-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>Migreer naar met stateless knooppunt typen vanuit een cluster met behulp van een basis SKU Load Balancer en een basis-SKU-IP
Voor alle migratie scenario's moet een nieuw stateless knoop punt type worden toegevoegd. Bestaande knooppunt typen kunnen niet worden gemigreerd als alleen stateless.

Als u een cluster wilt migreren dat gebruikmaakt van een Load Balancer en IP met een basis-SKU, moet u eerst een volledig nieuwe Load Balancer en IP-bron maken met behulp van de standaard-SKU. Het is niet mogelijk om deze resources in-place bij te werken.

Naar de nieuwe LB en IP moet worden verwezen in de nieuwe stateless knooppunt typen die u wilt gebruiken. In het bovenstaande voor beeld wordt een nieuwe resource voor virtuele-machine schaal sets toegevoegd om te worden gebruikt voor stateless knooppunt typen. Deze schaal sets voor virtuele machines verwijzen naar de nieuw gemaakte LB en IP en worden gemarkeerd als stateless knooppunt typen in de Service Fabric cluster bron.

U moet de nieuwe resources toevoegen aan uw bestaande resource manager-sjabloon om te beginnen. Deze resources omvatten:
* Een open bare IP-resource met een standaard-SKU.
* Een Load Balancer resource met standaard-SKU.
* Een NSG waarnaar wordt verwezen door het subnet waarin u de schaal sets voor virtuele machines implementeert.

Zodra de implementatie van de resources is voltooid, kunt u beginnen met het uitschakelen van de knoop punten in het knooppunt type dat u uit het oorspronkelijke cluster wilt verwijderen.


## <a name="next-steps"></a>Volgende stappen 
* [Reliable Services](service-fabric-reliable-services-introduction.md)
* [Knooppunttypen en virtuele-machineschaalsets](service-fabric-cluster-nodetypes.md)


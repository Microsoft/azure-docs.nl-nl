---
title: Staatloze knooppunttypen implementeren in een Service Fabric cluster
description: Meer informatie over het maken en implementeren van staatloze knooppunttypen in Azure Service Fabric cluster.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/16/2021
ms.author: pepogors
ms.openlocfilehash: 68c617b6e9345910bfd913e61e227a8e6c401bbc
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576037"
---
# <a name="deploy-an-azure-service-fabric-cluster-with-stateless-only-node-types"></a>Een Azure Service Fabric implementeren met staatloze knooppunttypen
Service Fabric knooppunttypen worden inherent aangenomen dat op een bepaald moment stateful services op de knooppunten kunnen worden geplaatst. Staatloze knooppunttypen vereendigen deze veronderstelling voor een knooppunttype, waardoor het knooppunttype andere functies kan gebruiken, zoals snellere uitschaalbewerkingen, ondersteuning voor automatische upgrades van besturingssystemen met duurzaamheid en uitschalen naar meer dan 100 knooppunten in één virtuele-machineschaalset.

* Primaire knooppunttypen kunnen niet worden geconfigureerd om staatloos te zijn
* Staatloze knooppunttypen worden alleen ondersteund met brons duurzaamheidsniveaus
* Staatloze knooppunttypen worden alleen ondersteund Service Fabric Runtime versie 7.1.409 of hoger.


Er zijn voorbeeldsjablonen beschikbaar: Service Fabric sjabloon voor [staatloze knooppunttypen](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="enabling-stateless-node-types-in-service-fabric-cluster"></a>Staatloze knooppunttypen inschakelen in Service Fabric cluster
Als u een of meer knooppunttypen wilt instellen als staatloos in een clusterresource, stelt u de eigenschap **isStateless** in op **true.** Wanneer u een Service Fabric cluster met staatloze knooppunttypen implementeert, moet u er niet aan denken dat er ten laatste één primair knooppunttype in de clusterresource is.

* De Service Fabric clusterresource-apiVersion moet '2020-12-01-preview' of hoger zijn.

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
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt0ephemeralEndPort')]",
            "startPort": "[parameters('nt0ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt0fabricHttpGatewayPort')]",
        "isPrimary": true,
        "isStateless": false, // Primary Node Types cannot be stateless
        "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType1Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt1applicationEndPort')]",
            "startPort": "[parameters('nt1applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt1fabricTcpGatewayPort')]",
        "durabilityLevel": "Bronze",
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

## <a name="configuring-virtual-machine-scale-set-for-stateless-node-types"></a>Een virtuele-machineschaalset configureren voor staatloze knooppunttypen
Als u staatloze knooppunttypen wilt inschakelen, moet u de onderliggende virtuele-machineschaalsetresource op de volgende manier configureren:

* De eigenschap  **value singlePlacementGroup,** die moet worden ingesteld op **false** als u wilt schalen naar meer dan 100 VM's.
* De upgradeMode van **de** schaalset moet worden ingesteld op **Rolling.**
* Voor de rolling upgrademodus moeten toepassings health-extensies of statustests zijn geconfigureerd. Configureer de statustest met de standaardconfiguratie voor staatloze knooppunttypen, zoals hieronder wordt voorgesteld. Zodra toepassingen zijn geïmplementeerd op het knooppunttype, kunnen de poorten voor de statustest/statusextensie worden gewijzigd om de status van de toepassing te controleren.

>[!NOTE]
> Tijdens het gebruik van Automatisch schalen met staatloze knooppunttypen wordt de knooppunttoestand na omlaag schalen niet automatisch opgeschoond. Als u de NodeState van knooppunten wilt opschonen tijdens automatisch schalen, wordt u aangeraden Service Fabric [helper voor](https://github.com/Azure/service-fabric-autoscale-helper) automatisch schalen te gebruiken.

```json
{
    "apiVersion": "2019-03-01",
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
        },
        "platformFaultDomainCount": 5
    },
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
            "durabilityLevel": "Bronze",
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

## <a name="configuring-stateless-node-types-with-multiple-availability-zones"></a>Staatloze knooppunttypen configureren met meerdere Beschikbaarheidszones
Als u staatloos knooppunttype wilt configureren voor meerdere beschikbaarheidszones, volgt u de documentatie [hier,](https://docs.microsoft.com/azure/service-fabric/service-fabric-cross-availability-zones#preview-enable-multiple-availability-zones-in-single-virtual-machine-scale-set)samen met de volgende wijzigingen:

* Stel **singlePlacementGroup** in:  **false**  als meerdere plaatsingsgroepen moeten worden ingeschakeld.
* Stel  **upgradeMode** in: **Rolling en**   voeg Application Health Extension/Health Probes toe zoals hierboven wordt vermeld.
* **PlatformFaultDomainCount instellen:** **5 voor** virtuele-machineschaalset.

>[!NOTE]
> Ongeacht de VMSSZonalUpgradeMode die in het cluster is geconfigureerd, vinden updates voor virtuele-machineschaalsets altijd opeenvolgend één beschikbaarheidszone tegelijk plaats voor het staatloze knooppunttype dat meerdere zones omvat, omdat deze de rolling upgrade-modus gebruikt.

Bekijk ter referentie de sjabloon [voor](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/15-VM-2-NodeTypes-Windows-Stateless-CrossAZ-Secure) het configureren van staatloze knooppunttypen met meerdere Beschikbaarheidszones

## <a name="networking-requirements"></a>Netwerkvereisten
### <a name="public-ip-and-load-balancer-resource"></a>Openbare IP en Load Balancer resource
Als u het schalen naar meer dan 100 VM's op een virtuele-machineschaalsetresource wilt inschakelen,  moeten de load balancer- en IP-resource waarnaar wordt verwezen door die virtuele-machineschaalset beide gebruikmaken van een Standard-SKU. Als u load balancer resource of IP-adres maakt zonder de SKU-eigenschap, wordt er een Basic-SKU gemaakt die geen ondersteuning biedt voor schalen naar meer dan 100 VM's. Een Standaard-SKU load balancer blokkeert standaard al het verkeer van buitenaf; om verkeer van buitenaf toe te staan, moet er een NSG worden geïmplementeerd in het subnet.

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
> Het is niet mogelijk om een in-place wijziging van de SKU uit te brengen op het openbare IP-adres en load balancer resources. 

### <a name="virtual-machine-scale-set-nat-rules"></a>NAT-regels voor virtuele-machineschaalsets
De load balancer nat-regels moeten overeenkomen met de NAT-pools uit de virtuele-machineschaalset. Elke virtuele-machineschaalset moet een unieke binnenkomende NAT-pool hebben.

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

### <a name="standard-sku-load-balancer-outbound-rules"></a>Standaard-SKU Load Balancer uitgaande regels
Standard Load Balancer en standaard openbare IP introduceren nieuwe mogelijkheden en verschillende gedragingen voor uitgaande connectiviteit in vergelijking met het gebruik van Basis-SKU's. Als u uitgaande connectiviteit wilt wanneer u met Standard-SKU's werkt, moet u deze expliciet definiëren met openbare STANDAARD-IP-adressen of Load Balancer. Zie Uitgaande verbindingen en [Azure](../load-balancer/load-balancer-outbound-connections.md) Standard Load Balancer [voor meer Standard Load Balancer.](../load-balancer/load-balancer-overview.md)

>[!NOTE]
> De standaardsjabloon verwijst naar een NSG die standaard al het uitgaande verkeer toestaat. Inkomende verkeer is beperkt tot de poorten die vereist zijn voor Service Fabric beheerbewerkingen. De NSG-regels kunnen worden gewijzigd om aan uw vereisten te voldoen.

>[!NOTE]
> Elk Service Fabric cluster dat gebruik maakt van een Standard SKU SLB moet ervoor zorgen dat elk knooppunttype een regel heeft die uitgaand verkeer op poort 443 toestaat. Dit is nodig om de installatie van het cluster te voltooien en elke implementatie zonder een dergelijke regel mislukt.



## <a name="migrate-to-using-stateless-node-types-in-a-cluster"></a>Migreren naar met behulp van staatloze knooppunttypen in een cluster
Voor alle migratiescenario's moet een nieuw staatloos knooppunttype worden toegevoegd. Bestaande knooppunttype kan niet worden gemigreerd om stateless te zijn.

Als u een cluster wilt migreren dat gebruik maakte van een Load Balancer en IP met een basis-SKU, moet u eerst een volledig nieuwe Load Balancer- en IP-resource maken met behulp van de standaard-SKU. Het is niet mogelijk om deze resources in-place bij te werken.

Er moet naar de nieuwe LB en IP worden verwezen in de nieuwe staatloze knooppunttypen die u wilt gebruiken. In het bovenstaande voorbeeld wordt een nieuwe virtuele-machineschaalset toegevoegd om te worden gebruikt voor staatloze knooppunttypen. Deze virtuele-machineschaalsets verwijzen naar de zojuist gemaakte LB en IP en zijn gemarkeerd als staatloze knooppunttypen in de Service Fabric clusterresource.

Om te beginnen moet u de nieuwe resources toevoegen aan uw bestaande Resource Manager sjabloon. Deze resources omvatten:
* Een openbare IP-resource met standard-SKU.
* Een Load Balancer resource met standard-SKU.
* Een NSG waarnaar wordt verwezen door het subnet waarin u uw virtuele-machineschaalsets implementeert.

Zodra de resources zijn geïmplementeerd, kunt u beginnen met het uitschakelen van de knooppunten in het knooppunttype dat u uit het oorspronkelijke cluster wilt verwijderen.

## <a name="next-steps"></a>Volgende stappen 
* [Reliable Services](service-fabric-reliable-services-introduction.md)
* [Knooppunttypen en virtuele-machineschaalsets](service-fabric-cluster-nodetypes.md)


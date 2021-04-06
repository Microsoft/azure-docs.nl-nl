---
title: Een Service Fabric-cluster schalen in Azure
description: In deze zelfstudie leert u hoe u een Service Fabric-cluster in Azure in- en uitschaalt en hoe u resterende resources opschoont.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: cdc7ba8d6c83ae72ffb8f1afae3954b3a46dc6ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98787991"
---
# <a name="tutorial-scale-a-service-fabric-cluster-in-azure"></a>Zelfstudie: Een Service Fabric-cluster schalen in Azure

Deze zelfstudie is deel drie van een serie. Hier ziet u hoe u een bestaand cluster kunt in- en uitschalen. Aan het einde van deze zelfstudie weet u hoe u een cluster kunt schalen en eventuele resterende resources kunt opschonen.  Lees [Service Fabric-clusters schalen](service-fabric-cluster-scaling.md) voor meer informatie over het aanpassen van de schaal van een cluster dat in Azure wordt uitgevoerd.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Knooppunten toevoegen en verwijderen (uitschalen en inschalen)
> * Knooppunttypen toevoegen en verwijderen (uitschalen en inschalen)
> * Het aantal knooppuntresources verhogen (opschalen)

In deze zelfstudiereeks leert u het volgende:
> [!div class="checklist"]
> * Een beveiligd [Windows-cluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) maken in Azure met behulp van een sjabloon
> * [Een cluster bewaken](service-fabric-tutorial-monitor-cluster.md)
> * Een cluster in- of uitschalen
> * [De runtime van een cluster upgraden](service-fabric-tutorial-upgrade-cluster.md)
> * [Een cluster verwijderen](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Voor u met deze zelfstudie begint:

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Installeer [Azure PowerShell](/powershell/azure/install-az-ps) of [Azure CLI](/cli/azure/install-azure-cli).
* Een beveiligd [Windows-cluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) maken in Azure

## <a name="important-considerations-and-guidelines"></a>Belangrijke overwegingen en richtlijnen

Toepassingsworkloads veranderen in de loop van de tijd. Hebt u voor uw bestaande services meer (of minder) resources nodig?  [Voeg knooppunten toe of verwijder deze](#add-nodes-to-or-remove-nodes-from-a-node-type) van een knooppunttype om het aantal clusterresources uit te breiden of te beperken.

Moet u meer dan 100 knooppunten aan uw cluster toevoegen?  Eén Service Fabric-knooppunttype/-schaalset mag niet meer dan 100 knooppunten/virtuele machines bevatten.  Als u de schaal van een cluster met meer dan 100 knooppunten wilt uitbreiden, [voegt u aanvullende knooppunttypen toe](#add-nodes-to-or-remove-nodes-from-a-node-type).

Biedt uw toepassing meerdere services en moeten hiervan een of meer service openbaar zijn of op internet gericht?  Typische toepassingen bevatten een front-endgatewayservice waarop invoer van een client wordt ontvangen en een of meer back-endservices die met de front-endservices communiceren. In dit geval kunt u het beste [ten minste twee knooppunttypen](#add-nodes-to-or-remove-nodes-from-a-node-type) aan het cluster toevoegen.  

Hebt u voor uw services verschillende infrastructuren nodig, zoals meer RAM of hogere CPU-cycli? Uw toepassing bevat bijvoorbeeld een front-endservice en een back-endservice. De front-endservice kan op kleinere virtuele machines (VM-grootten zoals D2) worden uitgevoerd, die over poorten beschikken die openstaan voor internet. De back-endservice vergt echter veel van het rekenvermogen en moet worden uitgevoerd op grotere virtuele machines (met VM-grootten zoals D4, D6, D15) die niet op internet zijn gericht. In dit geval wordt u aangeraden [twee of meer knooppunttypen](#add-nodes-to-or-remove-nodes-from-a-node-type) aan uw cluster toe te voegen. Hierdoor kan elk knooppunttype verschillende eigenschappen hebben, zoals internetconnectiviteit of VM-grootte. Het aantal virtuele machines kan ook afzonderlijk worden geschaald.

Wanneer u de schaal van een Azure-cluster aanpast, moet u rekening houden met de volgende richtlijnen:

* Eén Service Fabric-knooppunttype/-schaalset mag niet meer dan 100 knooppunten/virtuele machines bevatten.  Als u de schaal van een cluster met meer dan 100 knooppunten wilt uitbreiden, voegt u aanvullende knooppunttypen toe.
* Primaire knooppunttypen waarop productieworkloads worden uitgevoerd, moeten Gold of Silver als [duurzaamheidsniveau][durability] hebben en altijd over vijf of meer knooppunten beschikken.
* Niet-primaire knooppunttypen waarop stateful productieworkloads worden uitgevoerd, moeten altijd over vijf of meer knooppunten beschikken.
* Niet-primaire knooppunttypen waarop stateless productieworkloads worden uitgevoerd, moeten altijd over twee of meer knooppunten beschikken.
* Elk knooppunttype van met Gold of Silver als [duurzaamheidsniveau][durability] moet altijd over vijf of meer knooppunten beschikken.
* Als u een primair knooppunttype inschaalt (knooppunten verwijdert), mag u nooit het aantal exemplaren kleiner maken dan wat voor het [betrouwbaarheidsniveau][reliability] is vereist.

Lees [de richtlijnen voor clustercapaciteit](service-fabric-cluster-capacity.md) voor meer informatie.

## <a name="export-the-template-for-the-resource-group"></a>De sjabloon voor de resourcegroep exporteren

Nadat u een beveiligd [Windows-cluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) hebt gemaakt en uw resourcegroep hebt ingesteld, exporteert u de Resource Manager-sjabloon voor de resourcegroep. Door de sjabloon te exporteren, kunt toekomstige implementaties van het cluster en de bijbehorende resources automatiseren omdat de sjabloon de volledige infrastructuur bevat.  Lees [Azure Resource Manager-resourcegroepen beheren met behulp van Azure Portal](../azure-resource-manager/management/manage-resource-groups-portal.md) voor meer informatie over het exporteren van sjablonen.

1. Ga in [Azure Portal](https://portal.azure.com) naar de resourcegroep met het cluster (**sfclustertutorialgroup**, als u deze zelfstudie volgt). 

2. Selecteer in het linkerdeelvenster **Implementaties**, of selecteer de koppeling onder **Implementaties**. 

3. Selecteer de meest recente geslaagde implementatie uit de lijst.

4. In het linkerdeelvenster selecteert u **Sjabloon** en vervolgens **Downloaden** om de sjabloon te exporteren als een ZIP-bestand.  Sla de sjabloon en parameters op naar uw lokale computer.

## <a name="add-nodes-to-or-remove-nodes-from-a-node-type"></a>Knooppunten toevoegen aan of verwijderen van een knooppunttype

Door in- en uitschalen, of horizontale schaalaanpassing, wordt het aantal knooppunten in het cluster gewijzigd. Wanneer u in- of uitschaalt, voegt u meer instanties van de virtuele machine toe aan de schaalset. Deze instanties worden de knooppunten die door Service Fabric worden gebruikt. Service Fabric weet wanneer er meer exemplaren aan de schaalset zijn toegevoegd (door uitschaling) en reageert daar automatisch op. U kunt de schaal van het cluster op elk gewenst moment aanpassen, zelfs als er workloads op het cluster worden uitgevoerd.

### <a name="update-the-template"></a>De sjabloon bijwerken

[Exporteer een sjabloon en parameterbestand](#export-the-template-for-the-resource-group) van de resourcegroep voor de meest recente implementatie.  Open het bestand *parameters.json*.  Als u het cluster hebt geïmplementeerd met behulp van de [voorbeeldsjabloon][template] in deze zelfstudie, zijn er drie knooppunttypen in het cluster en drie parameters waarmee het aantal knooppunten voor elk knooppunttype wordt ingesteld: *nt0InstanceCount*, *nt1InstanceCount* en *nt2InstanceCount*.  Met de parameter *nt1InstanceCount* wordt bijvoorbeeld het aantal exemplaren voor het tweede knooppunttype ingesteld en wordt ook het aantal virtuele machines in de gekoppelde virtuele-machineschaalset ingesteld.

Dus door de waarde van *nt1InstanceCount* bij te werken, wijzigt u het aantal knooppunten in het tweede knooppunttype.  Vergeet niet dat u een knooppunttype niet naar meer dan 100 knooppunten kunt uitschalen.  Niet-primaire knooppunttypen waarop stateful productieworkloads worden uitgevoerd, moeten altijd over vijf of meer knooppunten beschikken. Niet-primaire knooppunttypen waarop stateless productieworkloads worden uitgevoerd, moeten altijd over twee of meer knooppunten beschikken.

Wanneer u een knooppunttype met het [duurzaamheidsniveau][durability] Bronze inschaalt (hieruit knooppunten verwijdert), moet u [de status van die knooppunten handmatig verwijderen](service-fabric-cluster-scale-in-out.md#manually-remove-vms-from-a-node-typevirtual-machine-scale-set).  Voor de duurzaamheidscategorieën Silver en Gold worden deze stappen automatisch door het platform uitgevoerd.

### <a name="deploy-the-updated-template"></a>De bijgewerkte sjabloon implementeren
Sla eventuele wijzigingen op in de bestanden *template.json* en *parameters.json*.  Voer de volgende opdracht uit om de bijgewerkte sjabloon te implementeren:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ChangingInstanceCount"
```
Of voer de volgende Azure CLI-opdracht uit:
```azurecli
az deployment group create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="add-a-node-type-to-the-cluster"></a>Een knooppunttype aan het cluster toevoegen

Elk knooppunttype dat is gedefinieerd in een Service Fabric-cluster dat in Azure wordt uitgevoerd, wordt ingesteld als een [afzonderlijke virtuele-machineschaalset](service-fabric-cluster-nodetypes.md). Elk knooppunttype kan vervolgens afzonderlijk worden beheerd. U kunt elk knooppunttype onafhankelijk omhoog of omlaag schalen, verschillende sets poorten openzetten en verschillende capaciteitsmeetwaarden gebruiken. U kunt ook de besturingssysteem-SKU die op elk clusterknooppunt wordt uitgevoerd, onafhankelijk wijzigen, maar houd er rekening mee dat u niet Windows en Linux tegelijk in het voorbeeldcluster kunt uitvoeren. Eén knooppunttype/schaalset mag niet meer dan 100 knooppunten bevatten.  U kunt de schaal van een cluster horizontaal aanpassen naar meer dan 100 knooppunten door aanvullende knooppunttypen/schaalsets toe te voegen. U kunt de schaal van het cluster op elk gewenst moment aanpassen, zelfs als er workloads op het cluster worden uitgevoerd.

### <a name="update-the-template"></a>De sjabloon bijwerken

[Exporteer een sjabloon en parameterbestand](#export-the-template-for-the-resource-group) van de resourcegroep voor de meest recente implementatie.  Open het bestand *parameters.json*.  Als u het cluster hebt geïmplementeerd met behulp van de [voorbeeldsjabloon][template] in deze zelfstudie, zijn er drie knooppunttypen in het cluster.  In deze sectie gaat u een vierde knooppunttype toevoegen door een Resource Manager-sjabloon bij te werken en te implementeren. 

Naast het nieuwe knooppunttype voegt u ook de gekoppelde virtuele-machineschaalset (die in een afzonderlijk subnet van het virtuele netwerk wordt uitgevoerd) en netwerkbeveiligingsgroep toe.  U kunt ervoor kiezen om nieuwe of bestaande openbare IP-adressen en Azure-taakverdelingsresources voor de nieuwe schaalset toe te voegen.  Het nieuwe knooppunttype heeft Silver als [duurzaamheidsniveau][durability] en de grootte Standard_D2_V2.

Voeg in het bestand *template.json* de volgende nieuwe parameters toe:
```json
"nt3InstanceCount": {
    "defaultValue": 5,
    "type": "Int",
    "metadata": {
        "description": "Instance count for node type"
    }
},
"vmNodeType3Size": {
    "defaultValue": "Standard_D2_V2",
    "type": "String"
},
```

Voeg in het bestand *template.json* de volgende nieuwe variabelen toe:
```json
"lbID3": "[resourceId('Microsoft.Network/loadBalancers',concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name')))]",
"lbIPConfig3": "[concat(variables('lbID3'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
"lbPoolID3": "[concat(variables('lbID3'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
"lbProbeID3": "[concat(variables('lbID3'),'/probes/FabricGatewayProbe')]",
"lbHttpProbeID3": "[concat(variables('lbID3'),'/probes/FabricHttpGatewayProbe')]",
"lbNatPoolID3": "[concat(variables('lbID3'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]",
"vmNodeType3Name": "[toLower(concat('NT4', variables('vmName')))]",
"vmStorageAccountName3": "[toLower(concat(uniqueString(resourceGroup().id), '1', '3' ))]",
"nt3applicationStartPort": "20000",
"nt3applicationEndPort": "30000",
"nt3ephemeralStartPort": "49152",
"nt3ephemeralEndPort": "65534",
"nt3fabricTcpGatewayPort": "19000",
"nt3fabricHttpGatewayPort": "19080",
"nt3reverseProxyEndpointPort": "19081",
"subnet3Name": "Subnet-3",
"subnet3Prefix": "10.0.3.0/24",
"subnet3Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet3Name'))]",
```

Voeg in het bestand *template.json* een nieuwe subnet toe aan de virtuele-netwerkresource:
```json
{
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[variables('virtualNetworkName')]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[variables('addressPrefix')]"
            ]
        },
        "subnets": [
            ...
            {
                "name": "[variables('subnet3Name')]",
                "properties": {
                    "addressPrefix": "[variables('subnet3Prefix')]",
                    "networkSecurityGroup": {
                        "id": "[resourceId('Microsoft.Network/networkSecurityGroups', concat('nsg', variables('subnet3Name')))]"
                    }
                }
            }
        ]
    },
    "dependsOn": [
        ...
        "[concat('Microsoft.Network/networkSecurityGroups/', concat('nsg', variables('subnet3Name')))]"
    ]
},
```

Voeg in het bestand *template.json* nieuwe openbare IP-adressen en taakverdelingresources toe:
```json
{
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat(variables('lbIPName'),'-',variables('vmNodeType3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "dnsSettings": {
            "domainNameLabel": "[concat(variables('dnsName'),'-','nt4')]"
        },
        "publicIPAllocationMethod": "Dynamic"
    }
},
        {
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "frontendIPConfigurations": [
            {
                "name": "LoadBalancerIPConfig",
                "properties": {
                    "publicIPAddress": {
                        "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('lbIPName'),'-',variables('vmNodeType3Name')))]"
                    }
                }
            }
        ],
        "backendAddressPools": [
            {
                "name": "LoadBalancerBEAddressPool",
                "properties": {}
            }
        ],
        "loadBalancingRules": [
            {
                "name": "LBRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[variables('nt3fabricTcpGatewayPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[variables('nt3fabricTcpGatewayPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[variables('lbProbeID3')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "LBHttpRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[variables('nt3fabricHttpGatewayPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[variables('nt3fabricHttpGatewayPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[variables('lbHttpProbeID3')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortLBRule1",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[parameters('loadBalancedAppPort1')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[parameters('loadBalancedAppPort1')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID3'),'/probes/AppPortProbe1')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortLBRule2",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[parameters('loadBalancedAppPort2')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[parameters('loadBalancedAppPort2')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID3'),'/probes/AppPortProbe2')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            {
                "name": "FabricGatewayProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[variables('nt3fabricTcpGatewayPort')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "FabricHttpGatewayProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[variables('nt3fabricHttpGatewayPort')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortProbe1",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[parameters('loadBalancedAppPort1')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortProbe2",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[parameters('loadBalancedAppPort2')]",
                    "protocol": "tcp"
                }
            }
        ],
        "inboundNatPools": [
            {
                "name": "LoadBalancerBEAddressNatPool",
                "properties": {
                    "backendPort": "3389",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPortRangeEnd": "4500",
                    "frontendPortRangeStart": "3389",
                    "protocol": "tcp"
                }
            }
        ]
    },
    "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/',concat(variables('lbIPName'),'-',variables('vmNodeType3Name')))]"
    ]
},
```

Voeg in het bestand *template.json* nieuwe netwerkbeveiligingsgroepen en virtuele-machineschaalsetresources toe.  Met de eigenschap NodeTypeRef bij de Service Fabric-extensie-eigenschappen van de virtuele-machineschaalset wijst u het opgegeven knooppunttype toe aan de schaalset.

```json
{
    "type": "Microsoft.Network/networkSecurityGroups",
    "name": "[concat('nsg', variables('subnet3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[resourceGroup().location]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "securityRules": [
            {
                "name": "allowSvcFabSMB",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "445",
                    "direction": "Inbound",
                    "priority": 3950,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow SMB traffic within the net, used by fabric to move packages around"
                }
            },
            {
                "name": "allowSvcFabCluser",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "1025-1027",
                    "direction": "Inbound",
                    "priority": 3920,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow ports within vnet that are used by the fabric to talk between nodes"
                }
            },
            {
                "name": "allowSvcFabEphemeral",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[concat(variables('nt3ephemeralStartPort'), '-', variables('nt3ephemeralEndPort'))]",
                    "direction": "Inbound",
                    "priority": 3930,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow fabric ephemeral ports within the vnet"
                }
            },
            {
                "name": "allowSvcFabPortal",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3fabricHttpGatewayPort')]",
                    "direction": "Inbound",
                    "priority": 3900,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used to access the fabric cluster web portal"
                }
            },
            {
                "name": "allowSvcFabClient",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3fabricTcpGatewayPort')]",
                    "direction": "Inbound",
                    "priority": 3910,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used by the fabric client (includes powershell)"
                }
            },
            {
                "name": "allowSvcFabApplication",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[concat(variables('nt3applicationStartPort'), '-', variables('nt3applicationEndPort'))]",
                    "direction": "Inbound",
                    "priority": 3940,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow fabric application ports within the vnet"
                }
            },
            {
                "name": "blockAll",
                "properties": {
                    "access": "Deny",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "*",
                    "direction": "Inbound",
                    "priority": 4095,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "block all traffic except what we've explicitly allowed"
                }
            },
            {
                "name": "allowVNetRDP",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "3389",
                    "direction": "Inbound",
                    "priority": 3960,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow RDP within the net"
                }
            },
            {
                "name": "allowSvcFabReverseProxy",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3reverseProxyEndpointPort')]",
                    "direction": "Inbound",
                    "priority": 3980,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used to access the fabric cluster using reverse proxy"
                }
            },
            {
                "name": "allowAppPort1",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[parameters('loadBalancedAppPort1')]",
                    "direction": "Inbound",
                    "priority": 2001,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow public application port 1"
                }
            },
            {
                "name": "allowAppPort2",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[parameters('loadBalancedAppPort2')]",
                    "direction": "Inbound",
                    "priority": 2002,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow public application port 2"
                }
            }
        ]
    }
},
{
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "sku": {
        "name": "[parameters('vmNodeType3Size')]",
        "capacity": "[parameters('nt3InstanceCount')]",
        "tier": "Standard"
    },
    "name": "[variables('vmNodeType3Name')]",
    "apiVersion": "2018-10-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "overprovision": "[variables('overProvision')]",
        "upgradePolicy": {
            "mode": "Automatic"
        },
        "virtualMachineProfile": {
            "extensionProfile": {
                "extensions": [
                    {
                        "name": "[concat(variables('vmNodeType3Name'),'OMS')]",
                        "properties": {
                            "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                            "type": "MicrosoftMonitoringAgent",
                            "typeHandlerVersion": "1.0",
                            "autoUpgradeMinorVersion": true,
                            "settings": {
                                "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
                            },
                            "protectedSettings": {
                                "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
                            }
                        }
                    },
                    {
                        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType3Name')]",
                        "properties": {
                            "type": "ServiceFabricNode",
                            "autoUpgradeMinorVersion": true,
                            "protectedSettings": {
                                "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                                "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
                            },
                            "publisher": "Microsoft.Azure.ServiceFabric",
                            "settings": {
                                "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                                "nodeTypeRef": "[variables('vmNodeType3Name')]",
                                "dataPath": "D:\\SvcFab",
                                "durabilityLevel": "Silver",
                                "enableParallelJobs": true,
                                "nicPrefixOverride": "[variables('subnet3Prefix')]",
                                "certificate": {
                                    "thumbprint": "[parameters('certificateThumbprint')]",
                                    "x509StoreName": "[parameters('certificateStoreValue')]"
                                }
                            },
                            "typeHandlerVersion": "1.0"
                        }
                    },
                    {
                        "name": "[concat('VMDiagnosticsVmExt','_vmNodeType3Name')]",
                        "properties": {
                            "type": "IaaSDiagnostics",
                            "autoUpgradeMinorVersion": true,
                            "protectedSettings": {
                                "storageAccountName": "[variables('applicationDiagnosticsStorageAccountName')]",
                                "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                                "storageAccountEndPoint": "https://core.windows.net/"
                            },
                            "publisher": "Microsoft.Azure.Diagnostics",
                            "settings": {
                                "WadCfg": {
                                    "DiagnosticMonitorConfiguration": {
                                        "overallQuotaInMB": "50000",
                                        "EtwProviders": {
                                            "EtwEventSourceProviderConfiguration": [
                                                {
                                                    "provider": "Microsoft-ServiceFabric-Actors",
                                                    "scheduledTransferKeywordFilter": "1",
                                                    "scheduledTransferPeriod": "PT5M",
                                                    "DefaultEvents": {
                                                        "eventDestination": "ServiceFabricReliableActorEventTable"
                                                    }
                                                },
                                                {
                                                    "provider": "Microsoft-ServiceFabric-Services",
                                                    "scheduledTransferPeriod": "PT5M",
                                                    "DefaultEvents": {
                                                        "eventDestination": "ServiceFabricReliableServiceEventTable"
                                                    }
                                                }
                                            ],
                                            "EtwManifestProviderConfiguration": [
                                                {
                                                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                                                    "scheduledTransferLogLevelFilter": "Information",
                                                    "scheduledTransferKeywordFilter": "4611686018427387904",
                                                    "scheduledTransferPeriod": "PT5M",
                                                    "DefaultEvents": {
                                                        "eventDestination": "ServiceFabricSystemEventTable"
                                                    }
                                                }
                                            ]
                                        }
                                    }
                                },
                                "StorageAccount": "[variables('applicationDiagnosticsStorageAccountName')]"
                            },
                            "typeHandlerVersion": "1.5"
                        }
                    },
                    {
                        "name": "[concat('VMIaaSAntimalware','_vmNodeType3Name')]",
                        "properties": {
                            "publisher": "Microsoft.Azure.Security",
                            "type": "IaaSAntimalware",
                            "typeHandlerVersion": "1.5",
                            "settings": {
                                "AntimalwareEnabled": "true",
                                "Exclusions": {
                                    "Paths": "D:\\SvcFab;D:\\SvcFab\\Log;C:\\Program Files\\Microsoft Service Fabric",
                                    "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
                                },
                                "RealtimeProtectionEnabled": "true",
                                "ScheduledScanSettings": {
                                    "isEnabled": "true",
                                    "scanType": "Quick",
                                    "day": "7",
                                    "time": "120"
                                }
                            },
                            "protectedSettings": null
                        }
                    }
                ]
            },
            "networkProfile": {
                "networkInterfaceConfigurations": [
                    {
                        "name": "[concat(variables('nicName'), '-2')]",
                        "properties": {
                            "ipConfigurations": [
                                {
                                    "name": "[concat(variables('nicName'),'-',2)]",
                                    "properties": {
                                        "loadBalancerBackendAddressPools": [
                                            {
                                                "id": "[variables('lbPoolID3')]"
                                            }
                                        ],
                                        "loadBalancerInboundNatPools": [
                                            {
                                                "id": "[variables('lbNatPoolID3')]"
                                            }
                                        ],
                                        "subnet": {
                                            "id": "[variables('subnet3Ref')]"
                                        }
                                    }
                                }
                            ],
                            "primary": true
                        }
                    }
                ]
            },
            "osProfile": {
                "adminPassword": "[parameters('adminPassword')]",
                "adminUsername": "[parameters('adminUsername')]",
                "computernamePrefix": "[variables('vmNodeType3Name')]",
                "secrets": [
                    {
                        "sourceVault": {
                            "id": "[parameters('sourceVaultValue')]"
                        },
                        "vaultCertificates": [
                            {
                                "certificateStore": "[parameters('certificateStoreValue')]",
                                "certificateUrl": "[parameters('certificateUrlValue')]"
                            }
                        ]
                    }
                ]
            },
            "storageProfile": {
                "imageReference": {
                    "publisher": "[parameters('vmImagePublisher')]",
                    "offer": "[parameters('vmImageOffer')]",
                    "sku": "[parameters('vmImageSku')]",
                    "version": "[parameters('vmImageVersion')]"
                },
                "osDisk": {
                    "caching": "ReadOnly",
                    "createOption": "FromImage",
                    "managedDisk": {
                        "storageAccountType": "[parameters('storageAccountType')]"
                    }
                }
            }
        }
    },
    "dependsOn": [
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
        "[concat('Microsoft.Network/loadBalancers/', concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name')))]",
        "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]",
        "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
    ]
},
```

Werk in het bestand *template.json* de clusterresource bij en voeg een nieuw knooppunttype toe:
```json
{
    "type": "Microsoft.ServiceFabric/clusters",
    "name": "[parameters('clusterName')]",
    "apiVersion": "2018-02-01",
    "location": "[parameters('clusterLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "nodeTypes": [
            ...
            {
                "name": "[variables('vmNodeType3Name')]",
                "applicationPorts": {
                    "endPort": "[variables('nt3applicationEndPort')]",
                    "startPort": "[variables('nt3applicationStartPort')]"
                },
                "clientConnectionEndpointPort": "[variables('nt3fabricTcpGatewayPort')]",
                "durabilityLevel": "Silver",
                "ephemeralPorts": {
                    "endPort": "[variables('nt3ephemeralEndPort')]",
                    "startPort": "[variables('nt3ephemeralStartPort')]"
                },
                "httpGatewayEndpointPort": "[variables('nt3fabricHttpGatewayPort')]",
                "isPrimary": false,
                "reverseProxyEndpointPort": "[variables('nt3reverseProxyEndpointPort')]",
                "vmInstanceCount": "[parameters('nt3InstanceCount')]"
            }
        ],    
    }
}                
```

Voeg in het bestand *parameters.json* de volgende nieuwe parameters en waarden toe:
```json
"nt3InstanceCount": {
    "Value": 5    
},
"vmNodeType3Size": {
    "Value": "Standard_D2_V2"
},
```

### <a name="deploy-the-updated-template"></a>De bijgewerkte sjabloon implementeren
Sla eventuele wijzigingen op in de bestanden *template.json* en *parameters.json*.  Voer de volgende opdracht uit om de bijgewerkte sjabloon te implementeren:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "AddingNodeType"
```
Of voer de volgende Azure CLI-opdracht uit:
```azurecli
az deployment group create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="remove-a-node-type-from-the-cluster"></a>Een knooppunttype uit het cluster verwijderen
Nadat u een Service Fabric-cluster hebt gemaakt, kunt u de schaal van een cluster horizontaal aanpassen door een knooppunttype (virtuele-machineschaalset) en alle bijbehorende knooppunten te verwijderen. U kunt de schaal van het cluster op elk gewenst moment aanpassen, zelfs als er workloads op het cluster worden uitgevoerd. Tijdens het schalen van het cluster worden uw toepassingen ook automatisch geschaald.

> [!WARNING]
> Het wordt afgeraden om Remove-AzServiceFabricNodeType op frequente basis te gebruiken om een knooppunttype uit een productiecluster te verwijderen. Het is een gevaarlijke opdracht omdat hiermee de virtuele-machineschaalsetresource achter het knooppunttype wordt verwijderd. 

Voer de cmdlet [Remove-AzServiceFabricNodeType](/powershell/module/az.servicefabric/remove-azservicefabricnodetype) uit om het knooppunttype te verwijderen.  Het knooppunttype moet Silver of Gold als [duurzaamheidsniveau][durability] hebben. Met de cmdlet wordt de schaalset verwijderd die aan het knooppunttype is gekoppeld; dit kan enige tijd duren.  Voer vervolgens de cmdlet [Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate) uit op alle knooppunten om te verwijderen. Hiermee verwijdert u de knooppuntstatus en worden de knooppunten uit het cluster verwijderd. Als er services bestaan op de knooppunten, worden de services vervolgens eerst naar een ander knooppunt verplaatst. Als de clusterbeheerder geen knooppunt voor de replica/service kan vinden, wordt de bewerking vervolgens uitgesteld/geblokkeerd.

```powershell
$groupname = "sfclustertutorialgroup"
$nodetype = "nt4vm"
$clustername = "mysfcluster123"

Remove-AzServiceFabricNodeType -Name $clustername  -NodeType $nodetype -ResourceGroupName $groupname

Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.eastus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <thumbprint> `
          -FindType FindByThumbprint -FindValue <thumbprint> `
          -StoreLocation CurrentUser -StoreName My

$nodes = Get-ServiceFabricNode | Where-Object {$_.NodeType -eq $nodetype} | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending

Foreach($node in $nodes)
{
    Remove-ServiceFabricNodeState -NodeName $node.NodeName -TimeoutSec 300 -Force 
}
```

## <a name="increase-node-resources"></a>Het aantal knooppuntresources verhogen 
Nadat u een Service Fabric-cluster hebt gemaakt, kunt u de schaal van een clusterknooppunttype verticaal aanpassen (de resources van de knooppunten wijzigen) of het besturingssysteem van de virtuele machines van het knooppunttype upgraden door het oorspronkelijke knooppunttype te vervangen door een nieuw knooppunttype (met een bijgewerkte VM-SKU of installatiekopie van het besturingssysteem). Zie [Een Azure Service Fabric-knooppunttype opschalen](service-fabric-scale-up-primary-node-type.md) voor meer informatie.

> [!IMPORTANT]
> Probeer een VM-SKU of installatiekopie van het besturingssysteem nooit ter plekke zelf te wijzigen. Dit is een gevaarlijke bewerking die niet wordt ondersteund.

Als dat niet mogelijk is, kunt u een nieuw cluster maken en [de toepassingsstatus herstellen](service-fabric-reliable-services-backup-restore.md) (indien van toepassing) op basis van uw oude cluster. U hoeft geen systeemservicestatussen te herstellen; deze worden opnieuw gemaakt wanneer u uw toepassingen op uw nieuwe cluster implementeert. Als u alleen stateless toepassingen op uw cluster uitvoerde, hoeft u alleen uw toepassingen naar het nieuwe cluster te implementeren. U hoeft niets te herstellen.

### <a name="update-the-template"></a>De sjabloon bijwerken

[Exporteer een sjabloon en parameterbestand](#export-the-template-for-the-resource-group) van de resourcegroep voor de meest recente implementatie.  Open het bestand *parameters.json*.  Als u het cluster hebt geïmplementeerd met behulp van de [voorbeeldsjabloon][template] in deze zelfstudie, zijn er drie knooppunttypen in het cluster.  

De grootte van de virtuele machines in het tweede knooppunttype wordt ingesteld in de parameter *vmNodeType1Size*.  Wijzig de parameterwaarde *vmNodeType1Size* van Standard_D2_V2 in [Standard_D3_V2](../virtual-machines/dv2-dsv2-series.md). Hiermee verdubbelt u het aantal resources van elk VM-exemplaar.

De VM-SKU voor alle drie knooppunttypen wordt ingesteld in de parameter *vmImageSku*.  Nogmaals: wees voorzichtig met het wijzigen van de VM-SKU van een knooppunttype. Dit wordt niet aanbevolen voor het primaire knooppunttype.

### <a name="deploy-the-updated-template"></a>De bijgewerkte sjabloon implementeren
Sla eventuele wijzigingen op in de bestanden *template.json* en *parameters.json*.  Voer de volgende opdracht uit om de bijgewerkte sjabloon te implementeren:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ScaleUpNodeType"
```
Of voer de volgende Azure CLI-opdracht uit:
```azurecli
az deployment group create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Knooppunten toevoegen en verwijderen (uitschalen en inschalen)
> * Knooppunttypen toevoegen en verwijderen (uitschalen en inschalen)
> * Het aantal knooppuntresources verhogen (opschalen)

Daarna gaat u verder met de volgende zelfstudie, waarin u leert hoe u een upgrade uitvoert van de runtime van een cluster.
> [!div class="nextstepaction"]
> [De runtime van een cluster upgraden](service-fabric-tutorial-upgrade-cluster.md)

[durability]: service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster
[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json

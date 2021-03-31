---
title: Netwerk patronen voor Azure Service Fabric
description: Beschrijft veelvoorkomende netwerk patronen voor Service Fabric en het maken van een cluster met behulp van Azure-netwerk functies.
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: 20bd5e931307725016c3e2ad69dae91214b2caab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87421464"
---
# <a name="service-fabric-networking-patterns"></a>Service Fabric netwerk patronen
U kunt uw Azure Service Fabric-cluster integreren met andere Azure-netwerk functies. In dit artikel wordt uitgelegd hoe u clusters maakt die gebruikmaken van de volgende functies:

- [Bestaand virtueel netwerk of subnet](#existingvnet)
- [Statisch openbaar IP-adres](#staticpublicip)
- [Alleen intern load balancer](#internallb)
- [Interne en externe load balancer](#internalexternallb)

Service Fabric wordt uitgevoerd in een standaard schaalset voor virtuele machines. Alle functionaliteiten die u kunt gebruiken in een schaalset voor virtuele machines, kunt u gebruiken met een Service Fabric cluster. De netwerk secties van de Azure Resource Manager sjablonen voor schaal sets voor virtuele machines en Service Fabric zijn identiek. Nadat u een bestaand virtueel netwerk hebt geïmplementeerd, kunt u eenvoudig andere netwerk functies, zoals Azure ExpressRoute, Azure VPN Gateway, een netwerk beveiligings groep en virtuele netwerk-peering, opnemen.

### <a name="allowing-the-service-fabric-resource-provider-to-query-your-cluster"></a>De Service Fabric resource provider toestaan om uw cluster op te vragen

Service Fabric is uniek van andere netwerk functies in één aspect. De [Azure Portal](https://portal.azure.com) gebruikt intern de service Fabric resource provider om een cluster aan te roepen om informatie over knoop punten en toepassingen op te halen. De resource provider Service Fabric vereist openbaar toegankelijke inkomende toegang tot de HTTP-gateway poort (standaard poort 19080) op het beheer eindpunt. [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) gebruikt het beheer eindpunt om uw cluster te beheren. De Service Fabric resource provider gebruikt deze poort ook om informatie over uw cluster op te vragen, om weer te geven in de Azure Portal. 

Als poort 19080 niet toegankelijk is vanuit de Service Fabric resource provider, wordt in de portal een bericht weer gegeven als *knoop punten die niet zijn gevonden* . de lijst met knoop punten en toepassingen wordt leeg weer gegeven. Als u uw cluster in de Azure Portal wilt zien, moet uw load balancer een openbaar IP-adres beschikbaar stellen en moet uw netwerk beveiligings groep binnenkomend poort 19080 verkeer toestaan. Als uw installatie niet aan deze vereisten voldoet, wordt de status van uw cluster in de Azure Portal niet weer gegeven.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="templates"></a>Sjablonen

Alle Service Fabric sjablonen bevinden zich in [github](https://github.com/Azure/service-fabric-scripts-and-templates/tree/master/templates/networking). U moet de sjablonen net zo kunnen implementeren met behulp van de volgende Power shell-opdrachten. Als u de bestaande Azure Virtual Network-sjabloon of het statische open bare IP-sjabloon implementeert, lees dan eerst de sectie [eerste installatie](#initialsetup) in dit artikel.

<a id="initialsetup"></a>
## <a name="initial-setup"></a>Eerste configuratie

### <a name="existing-virtual-network"></a>Bestaand virtueel netwerk

In het volgende voor beeld beginnen we met een bestaand virtueel netwerk met de naam ExistingRG-vnet in de resource groep **ExistingRG** . Het subnet heeft de naam standaard. Deze standaard resources worden gemaakt wanneer u de Azure Portal gebruikt voor het maken van een standaard virtuele machine (VM). U kunt het virtuele netwerk en het subnet maken zonder de virtuele machine te maken, maar het belangrijkste doel van het toevoegen van een cluster aan een bestaand virtueel netwerk is het bieden van een netwerk verbinding met andere Vm's. Het maken van de VM geeft een goed voor beeld van hoe een bestaand virtueel netwerk normaal gesp roken wordt gebruikt. Als uw Service Fabric cluster alleen een interne load balancer gebruikt, zonder een openbaar IP-adres, kunt u de virtuele machine en de open bare IP gebruiken als een beveiligde *Jump Box*.

### <a name="static-public-ip-address"></a>Statisch openbaar IP-adres

Een statisch openbaar IP-adres is doorgaans een toegewezen resource die onafhankelijk van de virtuele machine wordt beheerd of de Vm's waaraan deze is toegewezen. Het is ingericht in een specifieke netwerk resource groep (in plaats van in de Service Fabric cluster resource groep zelf). Maak een statisch openbaar IP-adres met de naam staticIP1 in dezelfde ExistingRG-resource groep, hetzij in het Azure Portal of met behulp van Power shell:

```powershell
PS C:\Users\user> New-AzPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG -Location westus -AllocationMethod Static -DomainNameLabel sfnetworking

Name                     : staticIP1
ResourceGroupName        : ExistingRG
Location                 : westus
Id                       : /subscriptions/1237f4d2-3dce-1236-ad95-123f764e7123/resourceGroups/ExistingRG/providers/Microsoft.Network/publicIPAddresses/staticIP1
Etag                     : W/"fc8b0c77-1f84-455d-9930-0404ebba1b64"
ResourceGuid             : 77c26c06-c0ae-496c-9231-b1a114e08824
ProvisioningState        : Succeeded
Tags                     :
PublicIpAllocationMethod : Static
IpAddress                : 40.83.182.110
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : null
DnsSettings              : {
                             "DomainNameLabel": "sfnetworking",
                             "Fqdn": "sfnetworking.westus.cloudapp.azure.com"
                           }
```

### <a name="service-fabric-template"></a>Service Fabric sjabloon

In de voor beelden in dit artikel gebruiken we de Service Fabric-template.jsop. U kunt de standaard portal wizard gebruiken om de sjabloon uit de portal te downloaden voordat u een cluster maakt. U kunt ook een van de [voorbeeld sjablonen](https://github.com/Azure-Samples/service-fabric-cluster-templates)gebruiken, zoals het [beveiligde service Fabric cluster met vijf knoop punten](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure).

<a id="existingvnet"></a>
## <a name="existing-virtual-network-or-subnet"></a>Bestaand virtueel netwerk of subnet

1. Wijzig de para meter subnet in de naam van het bestaande subnet en Voeg twee nieuwe para meters toe om te verwijzen naar het bestaande virtuele netwerk:

    ```json
        "subnet0Name": {
                "type": "string",
                "defaultValue": "default"
            },
            "existingVNetRGName": {
                "type": "string",
                "defaultValue": "ExistingRG"
            },

            "existingVNetName": {
                "type": "string",
                "defaultValue": "ExistingRG-vnet"
            },
            /*
            "subnet0Name": {
                "type": "string",
                "defaultValue": "Subnet-0"
            },
            "subnet0Prefix": {
                "type": "string",
                "defaultValue": "10.0.0.0/24"
            },*/
    ```

   U kunt ook een commentaar toevoegen aan de para meter met de naam ' virtualNetworkName ', zodat u niet wordt gevraagd om de naam van het virtuele netwerk twee keer op te geven op de Blade cluster implementatie in het Azure Portal.

2. `nicPrefixOverride`Het kenmerk Comment out van `Microsoft.Compute/virtualMachineScaleSets` , omdat u een bestaand subnet gebruikt en u deze variabele hebt uitgeschakeld in stap 1.

    ```json
            /*"nicPrefixOverride": "[parameters('subnet0Prefix')]",*/
    ```

3. Wijzig de `vnetID` variabele zodat deze naar het bestaande virtuele netwerk wijst:

    ```json
            /*old "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",*/
            "vnetID": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingVNetRGName'), '/providers/Microsoft.Network/virtualNetworks/', parameters('existingVNetName'))]",
    ```

4. Verwijder `Microsoft.Network/virtualNetworks` uit uw resources, dus maakt Azure geen nieuw virtueel netwerk:

    ```json
    /*{
    "apiVersion": "[variables('vNetApiVersion')]",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('virtualNetworkName')]",
    "location": "[parameters('computeLocation')]",
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
                    "addressPrefix": "[parameters('subnet0Prefix')]"
                }
            }
        ]
    },
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    }
    },*/
    ```

5. Maak een opmerking van het virtuele netwerk van het `dependsOn` `Microsoft.Compute/virtualMachineScaleSets` -kenmerk, zodat u niet afhankelijk bent van het maken van een nieuw virtueel netwerk:

    ```json
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Computer/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType0Name')]",
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        /*"[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
        */
        "[Concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray0')[0])]",

    ```

6. De sjabloon implementeren:

    ```powershell
    New-AzResourceGroup -Name sfnetworkingexistingvnet -Location westus
    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingexistingvnet -TemplateFile C:\SFSamples\Final\template\_existingvnet.json
    ```

    Na de implementatie moet het virtuele netwerk de nieuwe Vm's voor schaal sets bevatten. In het knooppunt type voor de schaalset voor virtuele machines moet het bestaande virtuele netwerk en subnet worden weer gegeven. U kunt Remote Desktop Protocol (RDP) ook gebruiken om toegang te krijgen tot de virtuele machine die al in het virtuele netwerk stond en om de nieuwe virtuele machines met schaal sets te pingen:

    ```
    C:>\Users\users>ping 10.0.0.5 -n 1
    C:>\Users\users>ping NOde1000000 -n 1
    ```

Zie een ander voor beeld [dat niet specifiek is voor service Fabric](https://github.com/gbowerman/azure-myriad/tree/main/existing-vnet).


<a id="staticpublicip"></a>
## <a name="static-public-ip-address"></a>Statisch openbaar IP-adres

1. Voeg para meters toe voor de naam van de bestaande vaste IP-resource groep, naam en Fully Qualified Domain Name (FQDN):

    ```json
    "existingStaticIPResourceGroup": {
                "type": "string"
            },
            "existingStaticIPName": {
                "type": "string"
            },
            "existingStaticIPDnsFQDN": {
                "type": "string"
    }
    ```

2. Verwijder de `dnsName` para meter. (Het statische IP-adres heeft er al een.)

    ```json
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

3. Voeg een variabele toe om te verwijzen naar het bestaande vaste IP-adres:

    ```json
    "existingStaticIP": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingStaticIPResourceGroup'), '/providers/Microsoft.Network/publicIPAddresses/', parameters('existingStaticIPName'))]",
    ```

4. Verwijder `Microsoft.Network/publicIPAddresses` uit uw resources, waardoor Azure geen nieuw IP-adres maakt:

    ```json
    /*
    {
        "apiVersion": "[variables('publicIPApiVersion')]",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[concat(parameters('lbIPName'),)'-', '0')]",
        "location": "[parameters('computeLocation')]",
        "properties": {
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsName')]"
            },
            "publicIPAllocationMethod": "Dynamic"        
        },
        "tags": {
            "resourceType": "Service Fabric",
            "clusterName": "[parameters('clusterName')]"
        }
    }, */
    ```

5. Maak een opmerking van het IP-adres van het `dependsOn` kenmerk van `Microsoft.Network/loadBalancers` , zodat u niet afhankelijk bent van het maken van een nieuw IP-adres:

    ```json
    "apiVersion": "[variables('lbIPApiVersion')]",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB', '-', parameters('clusterName'), '-', parameters('vmNodeType0Name'))]",
    "location": "[parameters('computeLocation')]",
    /*
    "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', concat(parameters('lbIPName'), '-', '0'))]"
    ], */
    "properties": {
    ```

6. Wijzig in de `Microsoft.Network/loadBalancers` resource het `publicIPAddress` element van `frontendIPConfigurations` om te verwijzen naar het bestaande vaste IP-adres in plaats van een nieuw gemaakt:

    ```json
                "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                "publicIPAddress": {
                                    /*"id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"*/
                                    "id": "[variables('existingStaticIP')]"
                                }
                            }
                        }
                    ],
    ```

7. Wijzig in de `Microsoft.ServiceFabric/clusters` resource `managementEndpoint` naar de DNS FQDN van het statische IP-adres. Als u een beveiligd cluster gebruikt, zorg er dan voor dat u *http://* wijzigt in *https://*. (Houd er rekening mee dat deze stap alleen van toepassing is op Service Fabric clusters. Sla deze stap over als u een schaalset voor virtuele machines gebruikt.)

    ```json
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',parameters('existingStaticIPDnsFQDN'),':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

8. De sjabloon implementeren:

    ```powershell
    New-AzResourceGroup -Name sfnetworkingstaticip -Location westus

    $staticip = Get-AzPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG

    $staticip

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingstaticip -TemplateFile C:\SFSamples\Final\template\_staticip.json -existingStaticIPResourceGroup $staticip.ResourceGroupName -existingStaticIPName $staticip.Name -existingStaticIPDnsFQDN $staticip.DnsSettings.Fqdn
    ```

Na de implementatie kunt u zien dat uw load balancer is gebonden aan het open bare statische IP-adres van de andere resource groep. Het eind punt van de Service Fabric client verbinding en [service Fabric Explorer](service-fabric-visualizing-your-cluster.md) eind punt verwijzen naar de DNS-FQDN van het statische IP-adres.

<a id="internallb"></a>
## <a name="internal-only-load-balancer"></a>Alleen intern load balancer

In dit scenario worden de externe load balancer in de standaard sjabloon vervangen door een load balancer met alleen interne Service Fabric. Zie [eerder in dit artikel](#allowing-the-service-fabric-resource-provider-to-query-your-cluster) voor implicaties voor de Azure Portal en voor de resource Provider service Fabric.

1. Verwijder de `dnsName` para meter. (Dit is niet nodig.)

    ```json
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

2. Als u een statische toewijzings methode gebruikt, kunt u desgewenst een para meter voor een statisch IP-adres toevoegen. Als u een dynamische toewijzings methode gebruikt, hoeft u deze stap niet uit te voeren.

    ```json
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

3. Verwijder `Microsoft.Network/publicIPAddresses` uit uw resources, waardoor Azure geen nieuw IP-adres maakt:

    ```json
    /*
    {
        "apiVersion": "[variables('publicIPApiVersion')]",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[concat(parameters('lbIPName'),)'-', '0')]",
        "location": "[parameters('computeLocation')]",
        "properties": {
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsName')]"
            },
            "publicIPAllocationMethod": "Dynamic"        
        },
        "tags": {
            "resourceType": "Service Fabric",
            "clusterName": "[parameters('clusterName')]"
        }
    }, */
    ```

4. Verwijder het kenmerk IP-adres `dependsOn` van `Microsoft.Network/loadBalancers` , zodat u niet afhankelijk bent van het maken van een nieuw IP-adres. Voeg het kenmerk virtueel netwerk toe `dependsOn` , omdat de Load Balancer nu afhankelijk is van het subnet van het virtuele netwerk:

    ```json
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'))]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /*"[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"*/
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
    ```

5. Wijzig de instelling van de load balancer `frontendIPConfigurations` van met behulp van een `publicIPAddress` , met behulp van een subnet en `privateIPAddress` . `privateIPAddress` maakt gebruik van een vooraf gedefinieerd statisch intern IP-adres. Als u een dynamisch IP-adres wilt gebruiken, verwijdert u het `privateIPAddress` element en wijzigt `privateIPAllocationMethod` u vervolgens in **dynamisch**.

    ```json
                "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                /*
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"
                                } */
                                "subnet" :{
                                    "id": "[variables('subnet0Ref')]"
                                },
                                "privateIPAddress": "[parameters('internalLBAddress')]",
                                "privateIPAllocationMethod": "Static"
                            }
                        }
                    ],
    ```

6. Wijzig in de `Microsoft.ServiceFabric/clusters` resource `managementEndpoint` om naar het interne Load Balancer adres te verwijzen. Als u een beveiligd cluster gebruikt, zorg er dan voor dat u *http://* wijzigt in *https://*. (Houd er rekening mee dat deze stap alleen van toepassing is op Service Fabric clusters. Sla deze stap over als u een schaalset voor virtuele machines gebruikt.)

    ```json
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',reference(variables('lbID0')).frontEndIPConfigurations[0].properties.privateIPAddress,':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

7. De sjabloon implementeren:

    ```powershell
    New-AzResourceGroup -Name sfnetworkinginternallb -Location westus

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternallb -TemplateFile C:\SFSamples\Final\template\_internalonlyLB.json
    ```

Na de implementatie maakt uw load balancer gebruik van het privé 10.0.0.250 IP-adres. Als u een andere machine in hetzelfde virtuele netwerk hebt, kunt u naar het interne [service Fabric Explorer](service-fabric-visualizing-your-cluster.md) -eind punt gaan. Houd er rekening mee dat deze verbinding maakt met een van de knoop punten achter de load balancer.

<a id="internalexternallb"></a>
## <a name="internal-and-external-load-balancer"></a>Interne en externe load balancer

In dit scenario begint u met het bestaande type met één knoop punt externe load balancer en voegt u een intern load balancer toe voor hetzelfde knooppunt type. Een back-end-poort die is gekoppeld aan een back-end-adres groep kan alleen worden toegewezen aan een enkele load balancer. Kies welke load balancer uw toepassings poorten heeft en welke load balancer uw beheer eindpunten heeft (poorten 19000 en 19080). Als u de beheer eindpunten op de interne load balancer plaatst, moet u er wel voor zorgen dat de beperkingen van de Service Fabric resource provider [eerder in het artikel](#allowing-the-service-fabric-resource-provider-to-query-your-cluster)worden besproken. In het voor beeld dat we gebruiken, blijven de beheer eindpunten op de externe load balancer. U voegt ook een poort 80-toepassings poort toe en plaatst deze op de interne load balancer.

In een cluster met twee knoop punten bevindt zich één knooppunt type op het externe load balancer. Het andere type knoop punt bevindt zich op de interne load balancer. Als u een cluster met twee knoop punten wilt gebruiken, schakelt u in de door de portal gemaakte sjabloon met twee knoop punten (die wordt geleverd met twee load balancers) het tweede load balancer naar een interne load balancer. Zie de sectie [met alleen interne Load Balancer](#internallb) voor meer informatie.

1. Voeg de para meter static interne load balancer IP-adres toe. (Zie eerdere secties in dit artikel voor opmerkingen met betrekking tot het gebruik van een dynamisch IP-adres.)

    ```json
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

2. Voeg een toepassings poort 80-para meter toe.

3. Als u interne versies van de bestaande netwerk variabelen wilt toevoegen, kopieert en plakt u deze en voegt u '-int ' toe aan de naam:

    ```json
    /* Add internal load balancer networking variables */
            "lbID0-Int": "[resourceId('Microsoft.Network/loadBalancers', concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal'))]",
            "lbIPConfig0-Int": "[concat(variables('lbID0-Int'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
            "lbPoolID0-Int": "[concat(variables('lbID0-Int'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
            "lbProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricGatewayProbe')]",
            "lbHttpProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricHttpGatewayProbe')]",
            "lbNatPoolID0-Int": "[concat(variables('lbID0-Int'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]",
            /* Internal load balancer networking variables end */
    ```

4. Als u begint met de door de portal gegenereerde sjabloon die gebruikmaakt van toepassings poort 80, voegt de standaard Portal sjabloon AppPort1 (poort 80) toe aan de externe load balancer. Verwijder in dit geval AppPort1 uit de externe load balancer `loadBalancingRules` en test, zodat u deze kunt toevoegen aan de interne Load Balancer:

    ```json
    "loadBalancingRules": [
        {
            "name": "LBHttpRule",
            "properties":{
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('nt0fabricHttpGatewayPort')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"            
                },
                "frontendPort": "[parameters('nt0fabricHttpGatewayPort')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[variables('lbHttpProbeID0')]"
                },
                "protocol": "tcp"
            }
        } /* Remove AppPort1 from the external load balancer.
        {
            "name": "AppPortLBRule1",
            "properties": {
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('loadBalancedAppPort1')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"            
                },
                "frontendPort": "[parameters('loadBalancedAppPort1')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[concate(variables('lbID0'), '/probes/AppPortProbe1')]"
                },
                "protocol": "tcp"
            }
        }*/

    ],
    "probes": [
        {
            "name": "FabricGatewayProbe",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('nt0fabricTcpGatewayPort')]",
                "protocol": "tcp"
            }
        },
        {
            "name": "FabricHttpGatewayProbe",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('nt0fabricHttpGatewayPort')]",
                "protocol": "tcp"
            }
        } /* Remove AppPort1 from the external load balancer.
        {
            "name": "AppPortProbe1",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('loadBalancedAppPort1')]",
                "protocol": "tcp"
            }
        } */

    ],
    "inboundNatPools": [
    ```

5. Voeg een tweede `Microsoft.Network/loadBalancers` resource toe. Het lijkt op het interne load balancer dat is gemaakt in de sectie [alleen intern Load Balancer](#internallb) , maar gebruikt de Load Balancer variabelen '-int ' en implementeert alleen de toepassings poort 80. Dit betekent ook dat `inboundNatPools` RDP-eind punten op de open bare Load Balancer worden bewaard. Als u RDP wilt inschakelen voor de interne load balancer, gaat u `inboundNatPools` van de externe Load Balancer naar deze interne Load Balancer:

    ```json
            /* Add a second load balancer, configured with a static privateIPAddress and the "-Int" load balancer variables. */
            {
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                /* Add "-Internal" to the name. */
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal')]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /* Remove public IP dependsOn, add vnet dependsOn
                    "[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"
                    */
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
                "properties": {
                    "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                /* Switch from Public to Private IP address
                                */
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"
                                }
                                */
                                "subnet" :{
                                    "id": "[variables('subnet0Ref')]"
                                },
                                "privateIPAddress": "[parameters('internalLBAddress')]",
                                "privateIPAllocationMethod": "Static"
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
                        /* Add the AppPort rule. Be sure to reference the "-Int" versions of backendAddressPool, frontendIPConfiguration, and the probe variables. */
                        {
                            "name": "AppPortLBRule1",
                            "properties": {
                                "backendAddressPool": {
                                    "id": "[variables('lbPoolID0-Int')]"
                                },
                                "backendPort": "[parameters('loadBalancedAppPort1')]",
                                "enableFloatingIP": "false",
                                "frontendIPConfiguration": {
                                    "id": "[variables('lbIPConfig0-Int')]"
                                },
                                "frontendPort": "[parameters('loadBalancedAppPort1')]",
                                "idleTimeoutInMinutes": "5",
                                "probe": {
                                    "id": "[concat(variables('lbID0-Int'),'/probes/AppPortProbe1')]"
                                },
                                "protocol": "tcp"
                            }
                        }
                    ],
                    "probes": [
                    /* Add the probe for the app port. */
                    {
                            "name": "AppPortProbe1",
                            "properties": {
                                "intervalInSeconds": 5,
                                "numberOfProbes": 2,
                                "port": "[parameters('loadBalancedAppPort1')]",
                                "protocol": "tcp"
                            }
                        }
                    ],
                    "inboundNatPools": [
                    ]
                },
                "tags": {
                    "resourceType": "Service Fabric",
                    "clusterName": "[parameters('clusterName')]"
                }
            },
    ```

6. `networkProfile`Voeg in voor de `Microsoft.Compute/virtualMachineScaleSets` resource de interne back-end-adres groep toe:

    ```json
    "loadBalancerBackendAddressPools": [
                                                        {
                                                            "id": "[variables('lbPoolID0')]"
                                                        },
                                                        {
                                                            /* Add internal BE pool */
                                                            "id": "[variables('lbPoolID0-Int')]"
                                                        }
    ],
    ```

7. De sjabloon implementeren:

    ```powershell
    New-AzResourceGroup -Name sfnetworkinginternalexternallb -Location westus

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternalexternallb -TemplateFile C:\SFSamples\Final\template\_internalexternalLB.json
    ```

Na de implementatie kunt u twee load balancers weer geven in de resource groep. Als u door de load balancers bladert, ziet u het open bare IP-adres en de beheer eindpunten (poorten 19000 en 19080) die zijn toegewezen aan het open bare IP-adres. U kunt ook het statische interne IP-adres en eind punt van de toepassing zien (poort 80) die aan de interne load balancer zijn toegewezen. Beide load balancers maken gebruik van dezelfde back-end-groep voor schaal sets voor virtuele machines.

## <a name="notes-for-production-workloads"></a>Opmerkingen voor werk belastingen voor productie

De bovenstaande GitHub-sjablonen zijn ontworpen voor gebruik met de standaard SKU voor Azure Standard Load Balancer (SLB), de basis-SKU. Deze SLB heeft geen SLA, dus voor productie werkbelastingen moet de standaard-SKU worden gebruikt. Zie het [overzicht van Azure Standard Load Balancer](../load-balancer/load-balancer-overview.md)voor meer informatie. Elk Service Fabric cluster dat gebruikmaakt van de standaard-SKU voor SLB moet ervoor zorgen dat elk knooppunt type een regel heeft die uitgaand verkeer op poort 443 toestaat. Dit is nodig voor het volt ooien van de installatie van het cluster en een implementatie zonder een dergelijke regel zal mislukken. In het bovenstaande voor beeld van een load balancer ' alleen intern ' moet er een extra externe load balancer aan de sjabloon worden toegevoegd met een regel voor uitgaand verkeer voor poort 443.

## <a name="next-steps"></a>Volgende stappen
[Een cluster maken](service-fabric-cluster-creation-via-arm.md)

Na de implementatie kunt u twee load balancers weer geven in de resource groep. Als u door de load balancers bladert, ziet u het open bare IP-adres en de beheer eindpunten (poorten 19000 en 19080) die zijn toegewezen aan het open bare IP-adres. U kunt ook het statische interne IP-adres en eind punt van de toepassing zien (poort 80) die aan de interne load balancer zijn toegewezen. Beide load balancers maken gebruik van dezelfde back-end-groep voor schaal sets voor virtuele machines.

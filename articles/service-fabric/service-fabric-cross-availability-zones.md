---
title: Een cluster implementeren in Beschikbaarheidszones
description: Meer informatie over het maken van een Azure Service Fabric-cluster in Beschikbaarheidszones.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/16/2021
ms.author: pepogors
ms.openlocfilehash: 9cc2a9d189e7a781dc6ba64a65af022150392485
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727758"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>Een Azure Service Fabric-cluster implementeren in Beschikbaarheidszones
Beschikbaarheidszones in Azure is een aanbieding voor hoge beschikbaarheid die uw toepassingen en gegevens beschermt tegen storingen in datacenters. Een beschikbaarheidszone is een unieke fysieke locatie die is uitgerust met onafhankelijke voeding, koeling en netwerken binnen een Azure-regio.

Service Fabric ondersteunt clusters die meerdere Beschikbaarheidszones door knooppunttypen te implementeren die zijn vastgemaakt aan specifieke zones. Dit zorgt voor hoge beschikbaarheid van uw toepassingen. Azure-beschikbaarheidszones zijn alleen beschikbaar in bepaalde regio's. Zie overzicht Azure-beschikbaarheidszones [voor meer informatie.](../availability-zones/az-overview.md)

Er zijn voorbeeldsjablonen beschikbaar: [Service Fabric beschikbaarheidszonesjabloon](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>Aanbevolen topologie voor het primaire knooppunttype van Azure Service Fabric-clusters die over meerdere Beschikbaarheidszones
Een Service Fabric cluster gedistribueerd over Beschikbaarheidszones zorgt voor hoge beschikbaarheid van de clustertoestand. Als u een Service Fabric meerdere zones wilt overspannen, moet u een primair knooppunttype maken in elke beschikbaarheidszone die wordt ondersteund door de regio. Hierdoor worden seed-knooppunten gelijkmatig verdeeld over elk van de primaire knooppunttypen.

Voor de aanbevolen topologie voor het primaire knooppunttype zijn de onderstaande resources vereist:

* Het betrouwbaarheidsniveau van het cluster is ingesteld op Moet.
* Drie knooppunttypen die zijn gemarkeerd als primair.
    * Elk knooppunttype moet worden ingesteld op een eigen virtuele-machineschaalset die zich in verschillende zones bevindt.
    * Elke virtuele-machineschaalset moet ten minste vijf knooppunten hebben (Duurzaamheid van silver).
* Eén openbare IP-resource met standard-SKU.
* Eén resource Load Balancer standard-SKU.
* Een NSG waarnaar wordt verwezen door het subnet waarin u uw virtuele-machineschaalsets implementeert.

>[!NOTE]
> De eigenschap voor een enkele plaatsingsgroep van de virtuele-machineschaalset moet worden ingesteld op true.

Diagram met het architectuurdiagram van Service Fabric Azure-beschikbaarheidszone met de architectuur van ![ Service Fabric Azure-beschikbaarheidszone.][sf-architecture]

Voorbeeld van een knooppuntlijst met FD-/UD-indelingen in een virtuele-machineschaalset die zones omspant

 ![Voorbeeld van een knooppuntlijst met FD-/UD-indelingen in een virtuele-machineschaalset die zones omspant.][sf-multi-az-nodes]

**Distributie van servicereplica's** over zones: wanneer een service wordt geïmplementeerd op de knooppunttypen die zones overspannen, worden de replica's geplaatst om ervoor te zorgen dat ze in afzonderlijke zones belandt. Dit wordt gegarandeerd omdat het foutdomein op de knooppunten in elk van deze knooppuntenTypes wordt geconfigureerd met de zone-informatie (dat wil zeggen FD = fd:/zone1/1 enzovoort). Bijvoorbeeld: voor 5 replica's of exemplaren van een service is de distributie 2-2-1 en probeert runtime een gelijke verdeling over AZ's te garanderen.

**User Service Replica Configuration:** Stateful user services deployed on the cross availability zone nodeTypes should be configured with this configuration: replica count with target = 9, min = 5. Deze configuratie helpt de service te werken, zelfs wanneer één zone uitgaat omdat er nog zes replica's actief zijn in de andere twee zones. Een toepassingsupgrade in een dergelijk scenario wordt ook uitgevoerd.

**Betrouwbaarheid van clusterNiveau:** hiermee definieert u het aantal seed-knooppunten in het cluster en tevens de replicagrootte van de systeemservices. Omdat de installatie van een zone voor meerdere beschikbaarheidszones een groter aantal knooppunten heeft, die zijn verdeeld over zones om zone-tolerantie mogelijk te maken, zorgt een hogere betrouwbaarheid ervoor dat het knooppunt meer seed-knooppunten en systeemservicereplica's aanwezig zijn en gelijkmatig worden verdeeld over zones, zodat in het geval van een zonefout het cluster en de systeemservices ongeimpacteerd blijven. 'ReliabilityLevel = Each' zorgt ervoor dat er 9 seed-knooppunten zijn verspreid over zones in het cluster met 3 vruchten in elke zone. Dit is daarom de aanbeveling voor het instellen van de beschikbaarheidszone voor meerdere zones.

**Scenario voor zone-down:** wanneer een zone uitgaat, worden alle knooppunten in die zone weergegeven als niet-omlaag. Servicereplica's op deze knooppunten zijn ook niet meer mogelijk. Omdat er replica's in de andere zones zijn, blijft de service responsief met primaire replica's waarvoor een failing over is naar de zones die functioneren. De services worden weergegeven met een waarschuwingstoestand omdat het aantal doelreplica's nog niet is bereikt en omdat het aantal VM's nog steeds meer dan de minimumgrootte van de doelreplica is. Vervolgens worden Service Fabric load balancer replica's in de werkzones die overeenkomen met het aantal geconfigureerde doelreplica's. Op dit moment worden de services in orde weergegeven. Wanneer de zone die niet was, weer een back-up maakt van de load balance, worden alle servicereplica's opnieuw gelijkmatig verdeeld over alle zones.

## <a name="networking-requirements"></a>Netwerkvereisten
### <a name="public-ip-and-load-balancer-resource"></a>Openbare IP en Load Balancer resource
Als u de zones-eigenschap van een virtuele-machineschaalsetresource wilt inschakelen, moeten de load balancer- en IP-resource waarnaar wordt verwezen door die virtuele-machineschaalset beide gebruikmaken van een Standard-SKU.  Als u een load balancer ip-resource maakt zonder de SKU-eigenschap, wordt er een Basis-SKU gemaakt die geen ondersteuning biedt voor Beschikbaarheidszones. Een standaard-SKU load balancer blokkeert standaard al het verkeer van buitenaf; om verkeer buiten toe te staan, moet er een NSG worden geïmplementeerd in het subnet.

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
```

```json
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
> Het is niet mogelijk om een in-place wijziging van de SKU uit te brengen op het openbare IP-adres en load balancer resources. Als u migreert van bestaande resources die een Basis-SKU hebben, bekijkt u de migratiesectie van dit artikel.

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
Standard Load Balancer en standaard openbare IP introduceren nieuwe mogelijkheden en verschillende gedragingen voor uitgaande connectiviteit in vergelijking met het gebruik van Basis-SKU's. Als u uitgaande connectiviteit wilt wanneer u met Standard-SKU's werkt, moet u deze expliciet definiëren met openbare STANDAARD-IP-adressen of openbare Standaard-Load Balancer. Zie Uitgaande verbindingen en [Azure](../load-balancer/load-balancer-outbound-connections.md) Standard Load Balancer [voor meer Standard Load Balancer.](../load-balancer/load-balancer-overview.md)

>[!NOTE]
> De standaardsjabloon verwijst naar een NSG die standaard al het uitgaande verkeer toestaat. Inkomende verkeer is beperkt tot de poorten die vereist zijn voor Service Fabric beheerbewerkingen. De NSG-regels kunnen worden gewijzigd om te voldoen aan uw vereisten.

>[!NOTE]
> Elk Service Fabric cluster dat gebruik maakt van een Standard SKU SLB moet ervoor zorgen dat elk knooppunttype een regel heeft die uitgaand verkeer op poort 443 toestaat. Dit is nodig om de clusterinstallatie te voltooien en elke implementatie zonder een dergelijke regel mislukt.


### <a name="enabling-zones-on-a-virtual-machine-scale-set"></a>Zones inschakelen op een virtuele-machineschaalset
Als u een zone wilt inschakelen, moet u op een virtuele-machineschaalset de volgende drie waarden opnemen in de resource van de virtuele-machineschaalset.

* De eerste waarde is de **eigenschap zones,** die aangeeft op welke beschikbaarheidszone de virtuele-machineschaalset wordt geïmplementeerd.
* De tweede waarde is de eigenschap singlePlacementGroup, die moet worden ingesteld op true.
* De derde waarde is de eigenschap faultDomainOverride in de Service Fabric virtuele-machineschaalsetextensie. De waarde voor deze eigenschap moet alleen de zone bevatten waarin deze virtuele-machineschaalset wordt geplaatst. Voorbeeld: "faultDomainOverride": "az1" Alle resources van de virtuele-machineschaalset moeten in dezelfde regio worden geplaatst omdat Azure Service Fabric-clusters geen ondersteuning voor andere regio's hebben.

```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "zones": ["1"],
    "properties": {
        "singlePlacementGroup": "true",
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
            "durabilityLevel": "Silver",
            "certificate": {
                "thumbprint": "[parameters('certificateThumbprint')]",
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
            "systemLogUploadSettings": {
                "Enabled": true
            },
            "faultDomainOverride": "az1"
        },
        "typeHandlerVersion": "1.0"
    }
}
```

### <a name="enabling-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>Meerdere primaire knooppunttypen inschakelen in de Service Fabric Cluster-resource
Als u een of meer knooppunttypen als primair wilt instellen in een clusterresource, stelt u de eigenschap isPrimary in op 'true'. Wanneer u een Service Fabric cluster in Beschikbaarheidszones implementeert, moet u drie knooppunttypen hebben in afzonderlijke zones.

```json
{
    "reliabilityLevel": "Platinum",
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
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt1InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType2Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt2applicationEndPort')]",
            "startPort": "[parameters('nt2applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt2fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt2ephemeralEndPort')]",
            "startPort": "[parameters('nt2ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt2fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt2InstanceCount')]"
    }
    ],
}
```

## <a name="migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>Migreren naar met behulp Beschikbaarheidszones van een cluster met behulp van een Basic SKU-Load Balancer en een IP-adres van een basic SKU
Als u een cluster wilt migreren dat gebruik maakte van een Load Balancer en IP met een basis-SKU, moet u eerst een volledig nieuwe Load Balancer- en IP-resource maken met behulp van de standaard-SKU. Het is niet mogelijk om deze resources in-place bij te werken.

Er moet naar de nieuwe LB- en IP-adressen worden verwezen in de nieuwe knooppunttypen voor de beschikbaarheidszone die u wilt gebruiken. In het bovenstaande voorbeeld zijn drie nieuwe resources voor virtuele-machineschaalsets toegevoegd in zones 1,2 en 3. Deze virtuele-machineschaalsets verwijzen naar de zojuist gemaakte LB en IP en zijn gemarkeerd als primaire knooppunttypen in de Service Fabric clusterresource.

Om te beginnen moet u de nieuwe resources toevoegen aan uw bestaande Resource Manager sjabloon. Deze resources omvatten:
* Een openbare IP-resource met standard-SKU.
* Een Load Balancer resource met standard-SKU.
* Een NSG waarnaar wordt verwezen door het subnet waarin u uw virtuele-machineschaalsets implementeert.
* Drie knooppunttypen die zijn gemarkeerd als primair.
    * Elk knooppunttype moet worden ingesteld op een eigen virtuele-machineschaalset die zich in verschillende zones bevindt.
    * Elke virtuele-machineschaalset moet ten minste vijf knooppunten hebben (Duurzaamheid van silver).

Een voorbeeld van deze resources vindt u in de [voorbeeldsjabloon](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure).

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

Zodra de implementatie van de resources is voltooid, kunt u beginnen met het uitschakelen van de knooppunten in het primaire knooppunttype van het oorspronkelijke cluster. Als de knooppunten zijn uitgeschakeld, migreren de systeemservices naar het nieuwe primaire knooppunttype dat in de bovenstaande stap is geïmplementeerd.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Write-Host "Connected to cluster"

$nodeNames = @("_nt0_0", "_nt0_1", "_nt0_2", "_nt0_3", "_nt0_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames) {
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

Zodra de knooppunten allemaal zijn uitgeschakeld, worden de systeemservices uitgevoerd op het primaire knooppunttype, dat is verdeeld over zones. Vervolgens kunt u de uitgeschakelde knooppunten uit het cluster verwijderen. Zodra de knooppunten zijn verwijderd, kunt u het oorspronkelijke IP-adres, de Load Balancer en de resources van de virtuele-machineschaalset verwijderen.

```powershell
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}

$scaleSetName="nt0"
Remove-AzureRmVmss -ResourceGroupName $groupname -VMScaleSetName $scaleSetName -Force

$lbname="LB-cluster-nt0"
$oldPublicIpName="LBIP-cluster-0"
$newPublicIpName="LBIP-cluster-1"

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force
```

Verwijder vervolgens de verwijzingen naar deze resources uit de Resource Manager die u hebt geïmplementeerd.

De laatste stap betreft het bijwerken van de DNS-naam en het openbare IP-adres.

```powershell
$oldprimaryPublicIP = Get-AzureRmPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force

$PublicIP = Get-AzureRmPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzureRmPublicIpAddress -PublicIpAddress $PublicIP

```

## <a name="preview-enable-multiple-availability-zones-in-single-virtual-machine-scale-set"></a>(Preview) Meerdere beschikbaarheidszones in één virtuele-machineschaalset inschakelen

De eerder genoemde oplossing maakt gebruik van één knooppunttype per AZ. Met de volgende oplossing kunnen gebruikers 3 AZ's implementeren in hetzelfde knooppunttype.

**Omdat deze functie momenteel in preview is, wordt deze momenteel niet ondersteund voor productiescenario's.**

De volledige voorbeeldsjabloon is [hier aanwezig.](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/15-VM-Windows-Multiple-AZ-Secure)

![Architectuur van Service Fabric Azure-beschikbaarheidszone][sf-multi-az-arch]

### <a name="configuring-zones-on-a-virtual-machine-scale-set"></a>Zones configureren op een virtuele-machineschaalset
Als u zones op een virtuele-machineschaalset wilt inschakelen, moet u de volgende drie waarden opnemen in de resource van de virtuele-machineschaalset.

* De eerste waarde is de **eigenschap zones,** waarmee de Beschikbaarheidszones aanwezig is in de virtuele-machineschaalset.
* De tweede waarde is de eigenschap singlePlacementGroup, die moet worden ingesteld op true. **De schaalset die is verdeeld over drie AZ's kan omhoog worden geschaald tot 300 VM's, zelfs met 'singlePlacementGroup = true'.**
* De derde waarde is zoneBalance, wat zorgt voor strikte zoneverdeling. Dit moet 'waar' zijn. Dit zorgt ervoor dat de VM-distributies over zones niet uit balans zijn, zodat wanneer een van de zones uitgaat, de andere twee zones voldoende VM's hebben om ervoor te zorgen dat het cluster ononderbroken blijft werken. Een cluster met een niet-verdeelde VM-distributie overleeft mogelijk geen zone-down scenario, omdat die zone mogelijk het merendeel van de VM's heeft. Een niet-gebalanceerde VM-distributie over zones leidt er ook toe dat problemen met serviceplaatsing & dat infrastructuurupdates vast komen te zitten.. Meer informatie [over zoneBalancing.](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md#zone-balancing)
* De overschrijvingen FaultDomain en UpgradeDomain zijn niet vereist om te worden geconfigureerd.

```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "zones": ["1", "2", "3"],
    "properties": {
        "singlePlacementGroup": "true",
        "zoneBalance": true
    }
}
```

>[!NOTE]
> * **Service Fabric clusters moeten tenast één primair knooppunttype hebben. DuurzaamheidLevel van primaire knooppunttypen moet Silver of hoger zijn.**
> * De AZ-overspannen virtuele-machineschaalset moet worden geconfigureerd met tenast 3 beschikbaarheidszones, ongeacht het duurzaamheidsniveau.
> * AZ overspant een virtuele-machineschaalset met silver-duurzaamheid (of hoger), moet tenast 15 VM's hebben.
> * AZ overspant een virtuele-machineschaalset met bronzen duurzaamheid en moet tenast 6 VM's hebben.

### <a name="enabling-the-support-for-multiple-zones-in-the-service-fabric-nodetype"></a>De ondersteuning voor meerdere zones inschakelen in Service Fabric nodeType
Het Service Fabric nodeType moet zijn ingeschakeld om meerdere beschikbaarheidszones te ondersteunen.

* De eerste waarde is **multipleAvailabilityZones,** die moet worden ingesteld op true voor het nodeType.
* De tweede waarde is **sfZonalUpgradeMode** en is optioneel. Deze eigenschap kan niet worden gewijzigd als er al een knooppunttype met meerdere AZ's aanwezig is in het cluster.
  De eigenschap bepaalt de logische groepering van VM's in upgradedomeinen.
  **Als de waarde is ingesteld op Parallel:** VM's onder het knooppunttype worden gegroepeerd in UD's, waarbij de zonegegevens in vijf UD's worden genegeerd. Dit leidt ertoe dat UD0 in alle zones tegelijkertijd wordt bijgewerkt. Deze implementatiemodus is sneller voor upgrades, maar wordt niet aanbevolen omdat deze in strijd is met de SDP-richtlijnen, die stellen dat de updates slechts één zone tegelijk moeten worden toegepast.
  **Als de waarde wordt weggelaten of ingesteld op 'Hiërarchisch':** VM's worden gegroepeerd om de zonale verdeling in maximaal 15 UD's weer te geven. Elk van de 3 zones heeft 5 UD's. Dit zorgt ervoor dat de updates zonegewijs worden bijgewerkt en pas naar de volgende zone worden verplaatst na het voltooien van 5 UD's binnen de eerste zone, langzaam over 15 UD's (3 zones, 5 UD's), wat veiliger is vanuit het oogpunt van het cluster en de gebruikerstoepassing.
  Deze eigenschap definieert alleen het upgradegedrag voor ServiceFabric-toepassings- en code-upgrades. De onderliggende upgrades voor virtuele-machineschaalsets zijn nog steeds parallel in alle AZ's.
  Deze eigenschap heeft geen invloed op de UD-distributie voor knooppunttypen waarvoor niet meerdere zones zijn ingeschakeld.
* De derde waarde is **vmssZonalUpgradeMode = Parallel**. Dit is een *verplichte* eigenschap die moet worden geconfigureerd in het cluster als een knooppunttype met meerdere AZ's wordt toegevoegd. Met deze eigenschap definieert u de upgrademodus voor de updates van de virtuele-machineschaalset die parallel worden uitgevoerd in alle AZ's tegelijk.
  Op dit moment kan deze eigenschap alleen worden ingesteld op parallel.
* De Service Fabric clusterresource-apiVersion moet '2020-12-01-preview' of hoger zijn.
* De versie van de clustercode moet 7.2.445 of hoger zijn.

```json
{
    "apiVersion": "2020-12-01-preview",
    "type": "Microsoft.ServiceFabric/clusters",
    "name": "[parameters('clusterName')]",
    "location": "[parameters('clusterLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
    ],
    "properties": {
        "reliabilityLevel": "Platinum",
        "SFZonalUpgradeMode": "Hierarchical",
        "VMSSZonalUpgradeMode": "Parallel",
        "nodeTypes": [
          {
                "name": "[parameters('vmNodeType0Name')]",
                "multipleAvailabilityZones": true,
          }
        ]
}
```

>[!NOTE]
> * Openbare IP en Load Balancer resources moeten gebruikmaken van de Standard-SKU, zoals eerder in het artikel is beschreven.
> * De eigenschap multipleAvailabilityZones op het knooppuntType kan alleen worden gedefinieerd op het moment dat nodeType wordt gemaakt en kan later niet meer worden gewijzigd. Bestaande nodeTypes kunnen daarom niet worden geconfigureerd met deze eigenschap.
> * Wanneer 'sfZonalUpgradeMode' wordt weggelaten of ingesteld op 'Hiërarchisch', zullen de cluster- en toepassingsimplementaties langzamer zijn omdat er meer upgradedomeinen in het cluster zijn. Het is belangrijk om de time-outs van het upgradebeleid correct aan te passen voor de duur van de upgrade voor 15 upgradedomeinen. Het upgradebeleid voor zowel de app als het cluster moet worden bijgewerkt om ervoor te zorgen dat de implementatie de time-outs voor de implementatie van Azure Resource Serbice van 12 uur niet overschrijdt. Dit betekent dat de implementatie niet langer dan 12 uur mag duren voor 15UD's, dat wil zeggen mag niet meer dan 40 minuten/UD duren.
> * Stel de **clusterbetrouwbaarheidLevel =Eigenschappen** in om ervoor te zorgen dat het cluster het één zone-omlaag scenario overleeft.

>[!NOTE]
> Voor best practice wordt aangeraden sfZonalUpgradeMode in te stellen op Hiërarchisch of weg te laten. De implementatie volgt de zonale distributie van VM's die invloed hebben op een kleinere hoeveelheid replica's en/of instanties, waardoor ze veiliger worden.
> Gebruik sfZonalUpgradeMode ingesteld op Parallel als de implementatiesnelheid een prioriteit is of als alleen staatloze workloads worden uitgevoerd op het knooppunttype met meerdere AZ's. Dit leidt ertoe dat de UD-walk parallel wordt uitgevoerd in alle AZ's.

### <a name="migration-to-the-node-type-with-multiple-availability-zones"></a>Migratie naar het knooppunttype met meerdere Beschikbaarheidszones
Voor alle migratiescenario's moet een nieuw knooppuntType worden toegevoegd, waarvoor meerdere beschikbaarheidszones worden ondersteund. Een bestaand knooppunttype kan niet worden gemigreerd om meerdere zones te ondersteunen.
In dit [artikel worden](./service-fabric-scale-up-primary-node-type.md) de gedetailleerde stappen beschreven voor het toevoegen van een nieuw knooppunttype en het toevoegen van de andere resources die vereist zijn voor het nieuwe knooppunttype, zoals de IP- en LB-resources. In hetzelfde artikel wordt nu ook beschreven hoe u het bestaande knooppunttype uit gebruik kunt stellen nadat het knooppuntType met meerdere beschikbaarheidszones is toegevoegd aan het cluster.

* Migratie van een knooppunttype dat gebruik maakt van lb- en IP-basisbronnen: dit wordt hier al beschreven voor de oplossing met één knooppunttype per AZ. [](#migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip) 
    Voor het nieuwe knooppunttype is het enige verschil dat er slechts 1 virtuele-machineschaalset en 1 knooppunttype is voor alle AZ's in plaats van 1 elk per AZ.
* Migratie van een knooppunttype dat gebruik maakt van de Standard SKU LB- en IP-resources met NSG: volg dezelfde procedure als hierboven beschreven, met uitzondering van dat er geen nieuwe LB-, IP- en NSG-resources hoeven toe te voegen en dat dezelfde resources opnieuw kunnen worden gebruikt in het nieuwe nodeType.


[sf-architecture]: ./media/service-fabric-cross-availability-zones/sf-cross-az-topology.png
[sf-multi-az-arch]: ./media/service-fabric-cross-availability-zones/sf-multi-az-topology.png
[sf-multi-az-nodes]: ./media/service-fabric-cross-availability-zones/sf-multi-az-nodes.png

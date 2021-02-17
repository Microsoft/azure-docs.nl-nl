---
title: Plannen en schalen van capaciteiten voor Azure Service Fabric
description: Aanbevolen procedures voor het plannen en schalen van Service Fabric clusters en toepassingen.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.custom: devx-track-csharp
ms.openlocfilehash: 32a9c26bb9e89cf4057cc753b02ad3c006d0bae6
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100595065"
---
# <a name="capacity-planning-and-scaling-for-azure-service-fabric"></a>Plannen en schalen van capaciteiten voor Azure Service Fabric

Voordat u Azure Service Fabric cluster of schaal Compute-resources maakt die uw cluster hosten, is het belang rijk om capaciteit te plannen. Zie [de service Fabric cluster capaciteit plannen](./service-fabric-cluster-capacity.md)voor meer informatie over het plannen van capaciteit. Zie [service Fabric schaalbaarheids overwegingen](/azure/architecture/reference-architectures/microservices/service-fabric#scalability-considerations)voor meer praktische richt lijnen voor schaal baarheid van clusters.

Naast het knooppunt type en de cluster kenmerken, moet u er rekening mee houden dat de schaal bewerkingen langer dan een uur duren voordat een productie omgeving is voltooid. Deze overweging is waar, ongeacht het aantal Vm's dat u toevoegt.

## <a name="autoscaling"></a>Automatisch schalen
U moet schaal bewerkingen uitvoeren via Azure Resource Manager sjablonen, omdat dit de best practice is om [bron configuraties als code](./service-fabric-best-practices-infrastructure-as-code.md)te behandelen. 

Door het gebruik van automatische schaling via schaal sets voor virtuele machines wordt uw versie van de Resource Manager-sjabloon voor de virtuele-machine schaal sets onnauwkeurig gedefinieerd. Met een onjuiste definitie wordt het risico verhoogd dat toekomstige implementaties leiden tot onbedoelde schaal bewerkingen. In het algemeen moet u automatisch schalen gebruiken als:

* Als u uw Resource Manager-sjablonen implementeert met de juiste capaciteit, wordt uw use-case niet ondersteund.
     
   Naast hand matig schalen kunt u een [continue integratie-en leverings pijplijn configureren in azure DevOps Services met behulp van Azure-implementatie projecten voor de resource groep](../azure-resource-manager/templates/add-template-to-azure-pipelines.md). Deze pijp lijn wordt meestal geactiveerd door een logische app die gebruikmaakt van prestatie gegevens van virtuele machines die worden opgevraagd uit het [Azure Monitor rest API](../azure-monitor/essentials/rest-api-walkthrough.md). De pijp lijn wordt in feite automatisch geschaald op basis van de gewenste metrische gegevens en tijdens het optimaliseren voor Resource Manager-sjablonen.
* U hoeft slechts één virtuele-machine schaalset-knoop punt tegelijk horizon taal te schalen.
   
   Als u met drie of meer knoop punten tegelijk wilt uitschalen, moet u een [service Fabric cluster uitschalen door een schaalset voor virtuele machines toe te voegen](virtual-machine-scale-set-scale-node-type-scale-out.md). Het is veilig om schaal sets voor virtuele machines horizon taal te schalen en te schalen, één knoop punt tegelijk.
* U beschikt over een betrouwbaardere betrouw baarheid of hoger voor uw Service Fabric-cluster, en Silver duurzaamheid of hoger op elke schaal waar u regels voor automatisch schalen configureert.
  
   De minimale capaciteit voor regels voor automatisch schalen moet gelijk zijn aan of groter zijn dan vijf virtuele machine-instanties. De waarde moet ook gelijk zijn aan of groter zijn dan uw minimum betrouwbaarheids niveau voor uw primaire knooppunt type.

> [!NOTE]
> Het Service Fabric stateful service Fabric:/System/InfastructureService/<NODE_TYPE_NAME> uitgevoerd op elk type knoop punt met een Silver-of hogere duurzaamheid. Het is de enige systeem service die wordt ondersteund om in azure te worden uitgevoerd op de knooppunt typen van uw clusters.

> [!IMPORTANT]
> Service Fabric automatisch schalen ondersteunt configuraties met schaal `Default` `NewestVM` sets [voor](../virtual-machine-scale-sets/virtual-machine-scale-sets-scale-in-policy.md)virtuele machines.

## <a name="vertical-scaling-considerations"></a>Overwegingen voor verticaal schalen

Voor het [verticaal schalen](./virtual-machine-scale-set-scale-node-type-scale-out.md) van een knooppunt type in azure service Fabric zijn een aantal stappen en overwegingen vereist. Bijvoorbeeld:

* Het cluster moet in orde zijn voordat het kan worden geschaald. Anders verstabielt u het cluster verder.
* De duur van het openstaande niveau zilver of hoger is vereist voor alle Service Fabric cluster knooppunt typen die stateful services hosten.

> [!NOTE]
> Uw primaire knooppunt type dat als host optreedt voor Service Fabric systeem services, moet het hoogste niveau van de duurzaamheid of hoger zijn. Nadat u Silver duurzaamheid hebt ingeschakeld, worden cluster bewerkingen zoals upgrades, het toevoegen of verwijderen van knoop punten langzamer, omdat het systeem optimaliseert voor gegevens beveiliging ten opzichte van de snelheid van de bewerkingen.

Verticaal schalen van een schaalset voor virtuele machines door eenvoudigweg de resource-SKU te wijzigen, is een destructieve bewerking, omdat de hosts hierdoor opnieuw worden geimageeerd, waardoor alle lokale permanente statussen worden verwijderd. In plaats daarvan moet u het cluster horizon taal schalen door een nieuwe schaalset toe te voegen met de gewenste SKU en vervolgens uw services naar de nieuwe schaalset te migreren om een veilige bewerking voor verticale schaling te volt ooien.

Uw cluster maakt gebruik van Service Fabric [knooppunt eigenschappen en plaatsings beperkingen](./service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints) om te bepalen waar u de services van uw toepassing wilt hosten. Bij het verticaal schalen van een primair knooppunt type implementeert u een tweede primaire knooppunt type en stelt u ( `"isPrimary": false` ) in op het oorspronkelijke primaire knooppunt type en gaat u verder met het uitschakelen van de knoop punten en het verwijderen van de bijbehorende schaalset en bijbehorende resources. Zie [het primaire knooppunt type van een service Fabric cluster omhoog schalen](service-fabric-scale-up-primary-node-type.md)voor meer informatie.

> [!NOTE]
> Valideer bewerkingen in test omgevingen altijd voordat u wijzigingen aanbrengt in de productie omgeving. Service Fabric cluster systeem services hebben standaard een plaatsings beperking naar het primaire knooppunt type doel.

Als de knooppunt eigenschappen en plaatsings beperkingen zijn gedeclareerd, voert u de volgende stappen uit voor één VM-exemplaar. Hierdoor kunnen de systeem services (en stateful Services) op de juiste wijze worden afgesloten op het VM-exemplaar dat u verwijdert, omdat er elders nieuwe replica's worden gemaakt.

1. Voer met behulp van Power shell uit `Disable-ServiceFabricNode` met opzet `RemoveNode` om het knoop punt dat u wilt verwijderen, uit te scha kelen. Verwijder het knooppunt type met het hoogste nummer. Als u bijvoorbeeld een cluster met zes knoop punten hebt, verwijdert u het exemplaar van de virtuele machine "MyNodeType_5".
2. Voer uit `Get-ServiceFabricNode` om ervoor te zorgen dat het knoop punt is overgeschakeld naar uitgeschakeld. Als dat niet het geval is, wacht u totdat het knoop punt is uitgeschakeld. Dit kan enkele uren duren voor elk knoop punt. Ga niet verder totdat het knoop punt is overgeschakeld op uitgeschakeld.
3. Verminder het aantal Vm's met één in dat knooppunt type. Het hoogste VM-exemplaar wordt nu verwijderd.
4. Herhaal stap 1 tot en met 3 zo nodig, maar Verklein het aantal instanties in de primaire knooppunt typen kleiner dan wat de betrouwbaarheids categorie warrantt. Zie [de service Fabric cluster capaciteit plannen](./service-fabric-cluster-capacity.md) voor een lijst met aanbevolen exemplaren.
5. Zodra alle Vm's zijn verdwenen (weer gegeven als ' omlaag '), wordt in de fabric:/System/InfrastructureService/[naam van knoop punt] een fout status weer gegeven. Vervolgens kunt u de cluster bron bijwerken om het knooppunt type te verwijderen. U kunt de implementatie van de ARM-sjabloon gebruiken of de cluster bron bewerken via [Azure Resource Manager](https://resources.azure.com). Hiermee wordt een cluster upgrade gestart waarmee de service Fabric:/System/InfrastructureService/[node type] met de fout status wordt verwijderd.
 6. Nadat u de VMScaleSet eventueel kunt verwijderen, ziet u de knoop punten nog steeds als ' down ' in de weer gave Service Fabric Explorer. De laatste stap is het opschonen van de `Remove-ServiceFabricNodeState` opdracht.

## <a name="horizontal-scaling"></a>Horizontale schaalaanpassing

U kunt horizon taal schalen ofwel [hand matig](./service-fabric-cluster-scale-in-out.md) of [programmatisch](./service-fabric-cluster-programmatic-scaling.md)uitvoeren.

> [!NOTE]
> Als u een knooppunt type met Silver of Gold duurzaamheid schaalt, is de schaal vertraging langzaam.

### <a name="scaling-out"></a>Uitschalen

Een Service Fabric cluster uitschalen door het aantal exemplaren voor een bepaalde schaalset voor virtuele machines te verhogen. U kunt het programma op een programmatische manier uitbreiden met de `AzureClient` id voor de gewenste schaalset om de capaciteit te verg Roten.

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

Als u hand matig wilt schalen, werkt u de capaciteit bij in de SKU-eigenschap van de gewenste resource voor de [schaalset van virtuele machines](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) .

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

### <a name="scaling-in"></a>Schalen in

Schalen in vereist meer aandacht dan uitschalen. Bijvoorbeeld:

* Service Fabric systeem services worden uitgevoerd in het primaire knooppunt type in uw cluster. Sluit het aantal exemplaren voor het type knoop punt nooit af of schaal deze zo dat u minder exemplaren hebt dan wat de betrouwbaarheids categorie garandeert. 
* Voor een stateful service hebt u een bepaald aantal knoop punten nodig die altijd beschikbaar zijn om de beschik baarheid te behouden en de status van uw service te behouden. U hebt mini maal een aantal knoop punten nodig die gelijk zijn aan het aantal doel replica sets van de partitie of service.

Voer de volgende stappen uit om de schaal hand matig in te schalen:

1. Voer met behulp van Power shell uit `Disable-ServiceFabricNode` met opzet `RemoveNode` om het knoop punt dat u wilt verwijderen, uit te scha kelen. Verwijder het knooppunt type met het hoogste nummer. Als u bijvoorbeeld een cluster met zes knoop punten hebt, verwijdert u het exemplaar van de virtuele machine "MyNodeType_5".
2. Voer uit `Get-ServiceFabricNode` om ervoor te zorgen dat het knoop punt is overgeschakeld naar uitgeschakeld. Als dat niet het geval is, wacht u totdat het knoop punt is uitgeschakeld. Dit kan enkele uren duren voor elk knoop punt. Ga niet verder totdat het knoop punt is overgeschakeld op uitgeschakeld.
3. Verminder het aantal Vm's met één in dat knooppunt type. Het hoogste VM-exemplaar wordt nu verwijderd.
4. Herhaal de stappen 1 tot en met 3 totdat u de gewenste capaciteit hebt ingericht. Schaal niet in het aantal instanties in de primaire knooppunt typen tot minder dan wat de betrouwbaarheids categorie garandeert. Zie [de service Fabric cluster capaciteit plannen](./service-fabric-cluster-capacity.md) voor een lijst met aanbevolen exemplaren.

Als u hand matig wilt schalen, werkt u de capaciteit bij in de SKU-eigenschap van de gewenste resource voor de [schaalset van virtuele machines](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) .

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

U moet het knoop punt voorbereiden op afsluiten om in een programma te schalen. Zoek het knoop punt dat moet worden verwijderd (het knoop punt met de hoogste instantie). Bijvoorbeeld:

```csharp
using (var client = new FabricClient())
{
    var mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync())
        .Where(n => n.NodeType.Equals(NodeTypeToScale, StringComparison.OrdinalIgnoreCase))
        .Where(n => n.NodeStatus == System.Fabric.Query.NodeStatus.Up)
        .OrderByDescending(n =>
        {
            var instanceIdIndex = n.NodeName.LastIndexOf("_");
            var instanceIdString = n.NodeName.Substring(instanceIdIndex + 1);
            return int.Parse(instanceIdString);
        })
        .FirstOrDefault();
```

Deactiveer en verwijder het knoop punt met behulp van dezelfde `FabricClient` instantie ( `client` in dit geval) en het knooppunt exemplaar ( `instanceIdString` in dit geval) dat u in de vorige code hebt gebruikt:

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shut down
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement virtual machine scale set capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply();
```

> [!NOTE]
> Wanneer u in een cluster schaalt, ziet u dat het verwijderde knoop punt/VM-exemplaar wordt weer gegeven in een slechte staat in Service Fabric Explorer. Zie [gedragingen die u mogelijk in service Fabric Explorer kunt](./service-fabric-cluster-scale-in-out.md#behaviors-you-may-observe-in-service-fabric-explorer)zien voor een uitleg van dit gedrag. U kunt:
> * Roep de [opdracht Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate) aan met de juiste knooppunt naam.
> * Implementeer het [service Fabric hulp programma voor automatisch schalen](https://github.com/Azure/service-fabric-autoscale-helper/) in uw cluster. Deze toepassing zorgt ervoor dat de geschaalde knoop punten uit Service Fabric Explorer worden gewist.

## <a name="reliability-levels"></a>Betrouwbaarheids niveaus

Het [betrouwbaarheids niveau](./service-fabric-cluster-capacity.md) is een eigenschap van uw service Fabric cluster bron. Het kan niet anders worden geconfigureerd voor afzonderlijke knooppunt typen. Het beheert de replicatie factor van de systeem services voor het cluster en is een instelling op het cluster bron niveau. 

Het betrouwbaarheids niveau bepaalt het minimum aantal knoop punten dat uw primaire knooppunt type moet hebben. De betrouwbaarheids categorie kan de volgende waarden hebben:

* Platinum: voert de systeem services uit met een aantal doel replica sets van zeven en negen Seed-knoop punten.
* Goud: voert de systeem services uit met het aantal zeven en zeven Seed-knoop punten van een doel replicaset.
* Silver: voert de systeem services uit met een aantal van vijf en vijf Seed-knoop punten in het doel replicaset.
* Brons: voert de systeem services uit met een aantal van drie en drie Seed-knoop punten in de doel replicaset.

Het mini maal aanbevolen betrouwbaarheids niveau is zilver.

Het betrouwbaarheids niveau is ingesteld in de sectie eigenschappen van de [resource micro soft. ServiceFabric/clusters](/azure/templates/microsoft.servicefabric/2018-02-01/clusters), zoals:

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>Duurzaamheids niveaus

> [!WARNING]
> Voor knooppunt typen met de duurzaamheid Bronze zijn _geen bevoegdheden_ nodig. Infrastructuur taken die van invloed zijn op uw staatloze workloads, worden niet gestopt of uitgesteld. Dit kan van invloed zijn op uw workloads. 
>
> Gebruik alleen Bronze duurzaamheid voor knooppunt typen die stateless werk belastingen uitvoeren. Voer zilver of hoger uit om de status consistentie te garanderen. Kies de juiste betrouw baarheid op basis van de richt lijnen in de [documentatie over capaciteits planning](./service-fabric-cluster-capacity.md).

Het duurzaamheids niveau moet worden ingesteld in twee resources. De ene is het extensie Profiel van de resource voor de [schaalset van de virtuele machine](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile):

```json
"extensionProfile": {
    "extensions":          {
        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
        "properties": {
            "settings": {
                "durabilityLevel": "Bronze"
            }
        }
    }
}
```

De andere resource bevindt zich onder `nodeTypes` in de [resource micro soft. ServiceFabric/clusters](/azure/templates/microsoft.servicefabric/2018-02-01/clusters): 

```json
"nodeTypes": [
    {
        "name": "[variables('vmNodeType0Name')]",
        "durabilityLevel": "Bronze"
    }
]
```

## <a name="next-steps"></a>Volgende stappen

* Een cluster maken op Vm's of computers met Windows Server: [service Fabric cluster maken voor Windows Server](service-fabric-cluster-creation-for-windows-server.md).
* Een cluster maken op Vm's of computers met Linux: [Maak een Linux-cluster](service-fabric-cluster-creation-via-portal.md).
* Meer informatie over [service Fabric ondersteunings opties](service-fabric-support.md).

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png

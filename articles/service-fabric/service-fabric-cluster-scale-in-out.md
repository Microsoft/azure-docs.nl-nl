---
title: Een Service Fabric cluster in-of uitschalen
description: Schaal een Service Fabric cluster in of uit om te voldoen aan de vraag door regels voor automatisch schalen in te stellen voor elk knooppunt type/virtuele-machine schaalset. Knoop punten toevoegen aan of verwijderen uit een Service Fabric-cluster
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 6ee04c73b75d6b335e450ff816c51f0a3089b918
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94409957"
---
# <a name="scale-a-cluster-in-or-out"></a>Een cluster in- of uitschalen

> [!WARNING]
> Lees deze sectie voordat u schaalt

Het schalen van reken resources voor de bron van de werk belasting van uw toepassing vereist een opzettelijke planning, duurt bijna altijd langer dan een uur om te volt ooien voor een productie omgeving. hiervoor moet u de werk belasting en de bedrijfs context begrijpen. Als u deze activiteit nog nooit eerder hebt uitgevoerd, is het raadzaam eerst te lezen en te weten Service Fabric overwegingen voor de planning van de [cluster capaciteit](service-fabric-cluster-capacity.md)voordat u verdergaat met de rest van dit document. Deze aanbeveling is om onbedoelde LiveSite-problemen te voor komen en het is ook raadzaam om de bewerkingen te testen die u wilt uitvoeren op een niet-productie omgeving. U kunt op elk gewenst moment [productie problemen melden of betaalde ondersteuning aanvragen voor Azure](service-fabric-support.md#report-production-issues-or-request-paid-support-for-azure). Voor technici die zijn toegewezen om deze bewerkingen uit te voeren die over de juiste context beschikken, wordt in dit artikel schaal bewerkingen beschreven, maar u moet bepalen welke bewerkingen geschikt zijn voor uw use-case. zoals welke resources moeten worden geschaald (CPU, opslag, geheugen), welke richting moet worden geschaald (verticaal of horizon taal) en welke bewerkingen moeten worden uitgevoerd (resource Sjabloonimlementatie, portal, Power shell/CLI).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules-or-manually"></a>Een Service Fabric cluster in-of uitschalen met behulp van regels voor automatisch schalen of hand matig
Virtuele-machine schaal sets vormen een Azure Compute-resource die u kunt gebruiken voor het implementeren en beheren van een verzameling virtuele machines als een set. Elk knooppunt type dat is gedefinieerd in een Service Fabric cluster, wordt ingesteld als een afzonderlijke schaalset voor virtuele machines. Elk knooppunt type kan vervolgens in of uit onafhankelijk van elkaar worden geschaald, verschillende sets poorten openen en verschillende capaciteits metrieken hebben. Meer informatie hierover vindt u in het [service Fabric knooppunt typen](service-fabric-cluster-nodetypes.md) document. Aangezien de Service Fabric knooppunt typen in uw cluster worden gemaakt van virtuele-machine schaal sets op de back-end, moet u regels voor automatisch schalen instellen voor elk type knoop punt/virtuele-machine schaalset.

> [!NOTE]
> Uw abonnement moet voldoende kern geheugens hebben om de nieuwe virtuele machines van dit cluster toe te voegen. Er is momenteel geen model validatie, waardoor er een implementatie tijd mislukt, als een van de quotum limieten wordt bereikt. Het is ook mogelijk dat een type knoop punt niet alleen meer dan 100 knoop punten per VMSS overschrijdt. Mogelijk moet u VMSS toevoegen om de beoogde schaal te verzorgen en automatisch schalen kan het VMSS niet Automagic toevoegen. Het toevoegen van een VMSS aan een live cluster is een uitdagende taak. Dit leidt er meestal toe dat gebruikers nieuwe clusters inrichten met de juiste knooppunt typen die tijdens de aanmaak zijn ingericht. [plan de cluster capaciteit](./service-fabric-cluster-capacity.md) dienovereenkomstig. 
> 
> 

## <a name="choose-the-node-typevirtual-machine-scale-set-to-scale"></a>Kies het type knoop punt/virtuele-machine schaalset die u wilt schalen
Op dit moment kunt u de regels voor het automatisch schalen van virtuele-machine schaal sets niet opgeven via de portal om een Service Fabric cluster te maken, dus laat het ons Azure PowerShell (1.0 +) gebruiken om de knooppunt typen weer te geven en voeg vervolgens regels voor automatisch schalen toe.

Voer de volgende cmdlets uit om de lijst met virtuele-machine schaal sets op te halen die het cluster vormen:

```powershell
Get-AzResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzVmss -ResourceGroupName <RGname> -VMScaleSetName <virtual machine scale set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevirtual-machine-scale-set"></a>Regels voor automatisch schalen instellen voor het knooppunt type/de virtuele-machine schaalset
Als uw cluster meerdere knooppunt typen heeft, herhaalt u dit voor elke knooppunt typen/virtuele-machine schaal sets die u wilt schalen (in of uit). Bedenk eerst hoeveel knooppunten u moet hebben, voordat u automatisch schalen instelt. Het minimum aantal knooppunten dat u nodig hebt voor het primaire knooppunttype, wordt bepaald door het betrouwbaarheidsniveau dat u hebt gekozen. Meer informatie over [betrouwbaarheids niveaus](service-fabric-cluster-capacity.md).

> [!NOTE]
> Als u de schaal van het primaire knooppunt type instelt op minder dan het minimale aantal, wordt het cluster Insta Biel of zelfs weer beschikbaar. Dit kan leiden tot verlies van gegevens voor uw toepassingen en voor de systeem services.
> 
> 

Momenteel wordt de functie voor automatisch schalen niet aangestuurd door de belasting die uw toepassingen mogelijk rapporteren aan Service Fabric. Op dit moment wordt de automatische schaal die u krijgt, uitsluitend aangestuurd door de prestatie meter items die worden verzonden door elk van de exemplaren van de virtuele-machine schaalset.  

Volg deze instructies voor [het instellen van automatisch schalen voor elke schaalset voor virtuele machines](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md).

> [!NOTE]
> In een schaal in een scenario, tenzij uw knooppunt type een [duurzaamheids niveau][durability] van goud of zilver heeft, moet u de [cmdlet Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate) aanroepen met de juiste knooppunt naam. Voor de duurzaamheid van bronzen is het niet raadzaam om in meer dan één knoop punt tegelijk te schalen.
> 

## <a name="manually-add-vms-to-a-node-typevirtual-machine-scale-set"></a>Vm's hand matig toevoegen aan een knooppunt type/virtuele-machine schaalset

Wanneer u uitschaalt, voegt u meer instanties van de virtuele machine toe aan de schaalset. Deze instanties worden de knooppunten die door Service Fabric worden gebruikt. Service Fabric weet wanneer er meer exemplaren aan de schaalset zijn toegevoegd (door uitschaling) en reageert daar automatisch op. 

> [!NOTE]
> Het toevoegen van Vm's kost tijd, dus u kunt niet verwachten dat de toevoegingen onmiddellijk worden uitgevoerd. Plan om capaciteit goed toe te voegen, zodat er meer dan tien minuten duren voordat de VM-capaciteit beschikbaar is voor de replica's/service-exemplaren die moeten worden geplaatst.
> 

### <a name="add-vms-using-a-template"></a>Vm's toevoegen met behulp van een sjabloon
Volg het voor beeld/de instructies in de Quick Start- [sjabloon galerie](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) om het aantal vm's in elk knooppunt type te wijzigen. 

### <a name="add-vms-using-powershell-or-cli-commands"></a>Vm's toevoegen met Power shell-of CLI-opdrachten
Met de volgende code verkrijgt u een schaalset op naam en verhoogt u de **capaciteit** van de schaalset met 1.

```powershell
$scaleset = Get-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity += 1

Update-AzVmss -ResourceGroupName $scaleset.ResourceGroupName -VMScaleSetName $scaleset.Name -VirtualMachineScaleSet $scaleset
```

Met deze code stelt u de capaciteit in op 6.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 6
```

## <a name="manually-remove-vms-from-a-node-typevirtual-machine-scale-set"></a>Vm's hand matig verwijderen uit een knooppunt type/virtuele-machine schaalset
Wanneer u schaalt in een knooppunt type, verwijdert u de VM-exemplaren uit de schaalset. Als het knooppunt type bronzen duurzaamheids niveau is, is Service Fabric niet op de hoogte van wat er is gebeurd en rapporten dat er geen knoop punt verloren is gegaan. Service Fabric rapporteert daarom een onjuiste status voor het cluster. Als u wilt voor komen dat de status is beschadigd, moet u het knoop punt expliciet uit het cluster verwijderen en de status van het knoop punt verwijderen.

De service Fabric-systeem services worden uitgevoerd in het primaire knooppunt type in uw cluster. Bij het schalen in het primaire knooppunt type, verkleint u nooit in het aantal exemplaren tot minder dan wat de [betrouwbaarheids categorie](service-fabric-cluster-capacity.md) garandeert. 
 
Voor een stateful service hebt u een bepaald aantal knoop punten nodig om altijd beschikbaar te zijn om de beschik baarheid te behouden en de status van uw service te behouden. U hebt mini maal het aantal knoop punten nodig dat gelijk is aan het aantal doel replica sets van de partitie/service.

### <a name="remove-the-service-fabric-node"></a>Het Service Fabric-knooppunt verwijderen

De stappen voor het hand matig verwijderen van de knooppunt status zijn alleen van toepassing op knooppunt typen met een *Bronze* -duurzaamheids categorie.  Voor *Silver* -en *Gold* -duurzaamheids lagen worden deze stappen automatisch uitgevoerd door het platform. Voor meer informatie over duurzaamheid raadpleegt u [Service Fabric cluster capacity planning][durability] (Capaciteitsplanning voor Service Fabric-clusters).

>[!NOTE]
> Behoud een minimum aantal van vijf knoop punten voor een schaalset voor virtuele machines met duurzaamheids niveau goud of zilver ingeschakeld. In uw cluster wordt de fout status ingevoerd als u deze drempel waarde inschaalt en u de verwijderde knoop punten hand matig moet opschonen.

Als u de knooppunten van het cluster gelijkmatig verdeeld wilt houden in upgrade- en foutdomeinen en op die manier gelijkmatig gebruik wilt inschakelen, moet het meest recent gemaakte knooppunt eerst worden verwijderd. Met andere woorden - de knooppunten moeten op basis van 'last in, first out' worden verwijderd. Het meest recent gemaakte knooppunt heeft de hoogste `virtual machine scale set InstanceId`-eigenschapswaarde. Met de onderstaande codevoorbeelden wordt het meest recent gemaakte knooppunt geretourneerd.

```powershell
Get-ServiceFabricNode | Sort-Object NodeInstanceId -Descending | Select-Object -First 1
```

```shell
sfctl node list --query "sort_by(items[*], &name)[-1]"
```

Het Service Fabric-cluster moet weten dat dit knooppunt zal worden verwijderd. Er zijn drie stappen die u moet uitvoeren:

1. Schakel het knooppunt uit zodat het geen replica meer is voor gegevens.  
PowerShell: `Disable-ServiceFabricNode`  
sfctl: `sfctl node disable`

2. Stop het knooppunt zodat de Service Fabric-runtime netjes wordt afgesloten en uw app een beëindigingsaanvraag ontvangt.  
PowerShell: `Start-ServiceFabricNodeTransition -Stop`  
sfctl: `sfctl node transition --node-transition-type Stop`

2. Verwijder het knooppunt uit het cluster.  
PowerShell: `Remove-ServiceFabricNodeState`  
sfctl: `sfctl node remove-state`

Nadat deze drie stappen op het knooppunt zijn toegepast, kan het knooppunt worden verwijderd uit de schaalset. Als u naast de [bronzen][durability] duurzaamheidslaag nog een andere duurzaamheidslaag gebruikt, worden deze stappen voor u uitgevoerd wanneer de schaalsetinstantie wordt verwijderd.

In het volgende codeblok wordt het laatst gemaakte knooppunt opgehaald en wordt het knooppunt uitgeschakeld, gestopt en uit het cluster verwijderd.

```powershell
#### After you've connected.....
# Get the node that was created last
$node = Get-ServiceFabricNode | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending | Select-Object -First 1

# Node details for the disable/stop process
$nodename = $node.NodeName
$nodeid = $node.NodeInstanceId

$loopTimeout = 10

# Run disable logic
Disable-ServiceFabricNode -NodeName $nodename -Intent RemoveNode -TimeoutSec 300 -Force

$state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus

while (($state -ne [System.Fabric.Query.NodeStatus]::Disabled) -and ($loopTimeout -ne 0))
{
    Start-Sleep 5
    $loopTimeout -= 1
    $state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus
    Write-Host "Checking state... $state found"
}

# Exit if the node was unable to be disabled
if ($state -ne [System.Fabric.Query.NodeStatus]::Disabled)
{
    Write-Error "Disable failed with state $state"
}
else
{
    # Stop node
    $stopid = New-Guid
    Start-ServiceFabricNodeTransition -Stop -OperationId $stopid -NodeName $nodename -NodeInstanceId $nodeid -StopDurationInSeconds 300

    $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
    $loopTimeout = 10

    # Watch the transaction
    while (($state -eq [System.Fabric.TestCommandProgressState]::Running) -and ($loopTimeout -ne 0))
    {
        Start-Sleep 5
        $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
        Write-Host "Checking state... $state found"
    }

    if ($state -ne [System.Fabric.TestCommandProgressState]::Completed)
    {
        Write-Error "Stop transaction failed with $state"
    }
    else
    {
        # Remove the node from the cluster
        Remove-ServiceFabricNodeState -NodeName $nodename -TimeoutSec 300 -Force
    }
}
```

In onderstaande **sfctl**-code wordt de volgende opdracht gebruikt om de waarde van **knooppuntnaam** op te halen van het laatst gemaakte knooppunt: `sfctl node list --query "sort_by(items[*], &name)[-1].name"`

```shell
# Inform the node that it is going to be removed
sfctl node disable --node-name _nt1vm_5 --deactivation-intent 4 -t 300

# Stop the node using a random guid as our operation id
sfctl node transition --node-instance-id 131541348482680775 --node-name _nt1vm_5 --node-transition-type Stop --operation-id c17bb4c5-9f6c-4eef-950f-3d03e1fef6fc --stop-duration-in-seconds 14400 -t 300

# Remove the node from the cluster
sfctl node remove-state --node-name _nt1vm_5
```

> [!TIP]
> Gebruik de volgende **sfctl**-query's om de status van elke stap te controleren
>
> **Status van deactiveren controleren**
> `sfctl node list --query "sort_by(items[*], &name)[-1].nodeDeactivationInfo"`
>
> **Status van stoppen controleren**
> `sfctl node list --query "sort_by(items[*], &name)[-1].isStopped"`
>

### <a name="scale-in-the-scale-set"></a>De schaalset inschalen

Nu het Service Fabric-knooppunt is verwijderd uit het cluster, kan de virtuele-machineschaalset worden ingeschaald. In onderstaand voorbeeld wordt de capaciteit van de schaalset verminderd met 1.

```powershell
$scaleset = Get-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity -= 1

Update-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm -VirtualMachineScaleSet $scaleset
```

Met deze code stelt u de capaciteit in op 5.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 5
```

## <a name="behaviors-you-may-observe-in-service-fabric-explorer"></a>Gedrag dat u in Service Fabric Explorer kunt zien
Wanneer u een cluster uitbreidt, weerspiegelt de Service Fabric Explorer het aantal knoop punten (exemplaren van de virtuele-machine schaalset) die deel uitmaken van het cluster.  Wanneer u echter een cluster in hebt geschaald, ziet u dat het verwijderde knoop punt/VM-exemplaar in een slechte staat wordt weer gegeven, tenzij u [Remove-ServiceFabricNodeState cmd](/powershell/module/servicefabric/remove-servicefabricnodestate) aanroept met de juiste knooppunt naam.   

Hier volgt een uitleg voor dit gedrag.

De knoop punten die in Service Fabric Explorer worden weer gegeven, zijn een reflectie van wat de Service Fabric systeem services (FM specifiek) kent over het aantal knoop punten dat het cluster bevat. Wanneer u de schaalset voor virtuele machines in schaalt in, wordt de VM verwijderd, maar wordt het knoop punt (dat is toegewezen aan de verwijderde VM), echter nog steeds weer gegeven. Dus Service Fabric Explorer blijft dat knoop punt weer geven (hoewel de status mogelijk fout of onbekend is).

Om ervoor te zorgen dat een knoop punt wordt verwijderd wanneer een virtuele machine wordt verwijderd, hebt u twee opties:

1. Kies een duurzaamheids niveau van goud of zilver voor de knooppunt typen in uw cluster, waarmee u de infra structuur kunt integreren. Hiermee worden de knoop punten vervolgens automatisch verwijderd uit de status van de systeem services (FM) wanneer u schaalt in.
Raadpleeg [hier de details over duurzaamheids niveaus](service-fabric-cluster-capacity.md)

> [!NOTE]
> Behoud een minimum aantal van vijf knoop punten voor een schaalset voor virtuele machines met duurzaamheids niveau goud of zilver ingeschakeld. In uw cluster wordt de fout status ingevoerd als u deze drempel waarde inschaalt en u de verwijderde knoop punten hand matig moet opschonen.

2. Zodra het VM-exemplaar is geschaald in, moet u de [cmdlet Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate)aanroepen.

> [!NOTE]
> Voor Service Fabric clusters moet een bepaald aantal knoop punten altijd actief zijn om de beschik baarheid te behouden en de status te behouden als ' quorum onderhouden '. Het is dus doorgaans veilig om alle computers in het cluster af te sluiten, tenzij u eerst een [volledige back-up hebt gemaakt van uw status](service-fabric-reliable-services-backup-restore.md).
> 
> 

## <a name="next-steps"></a>Volgende stappen
Lees het volgende voor meer informatie over het plannen van cluster capaciteit, het upgraden van een cluster en het partitioneren van services:

* [De cluster capaciteit plannen](service-fabric-cluster-capacity.md)
* [Cluster upgrades](service-fabric-cluster-upgrade.md)
* [Stateful Services partitioneren voor maximale schaal](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-in-out/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-in-out/ClusterResources.png

[durability]: service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster

---
title: Een knooppunt type verwijderen in azure Service Fabric | Microsoft Docs
description: Meer informatie over het verwijderen van een knooppunt type van een Service Fabric cluster dat in azure wordt uitgevoerd.
author: inputoutputcode
manager: sridmad
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: chrpap
ms.openlocfilehash: ede999bee9ce1a4a9dd10652a2c52a840d5b24be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88163574"
---
# <a name="how-to-remove-a-service-fabric-node-type"></a>Een Service Fabric knooppunt type verwijderen
In dit artikel wordt beschreven hoe u een Azure Service Fabric cluster kunt schalen door een bestaand knooppunt type uit een cluster te verwijderen. Een Service Fabric-cluster is een met het netwerk verbonden reeks virtuele of fysieke machines waarop uw microservices worden geïmplementeerd en beheerd. Een computer of virtuele machine die deel uitmaakt van een cluster, wordt een knoop punt genoemd. Virtuele-machine schaal sets vormen een Azure Compute-resource die u gebruikt om een verzameling virtuele machines als een set te implementeren en te beheren. Elk knooppunt type dat in een Azure-cluster is gedefinieerd, wordt [ingesteld als een afzonderlijke schaalset](service-fabric-cluster-nodetypes.md). Elk knooppunttype kan vervolgens afzonderlijk worden beheerd. Nadat u een Service Fabric cluster hebt gemaakt, kunt u horizon taal een cluster schalen door een knooppunt type (virtuele-machine schaalset) en alle knoop punten te verwijderen.  U kunt de schaal van het cluster op elk gewenst moment aanpassen, zelfs als er workloads op het cluster worden uitgevoerd.  Tijdens het schalen van het cluster worden uw toepassingen ook automatisch geschaald.

> [!WARNING]
> Het gebruik van deze methode voor het verwijderen van een knooppunt type uit een productie cluster wordt niet aanbevolen om regel matig te worden gebruikt. Het is een gevaarlijke opdracht omdat hiermee de virtuele-machineschaalsetresource achter het knooppunttype wordt verwijderd. 

## <a name="durability-characteristics"></a>Duurzaamheids kenmerken
De veiligheid heeft de prioriteit van de snelheid bij het gebruik van Remove-AzServiceFabricNodeType. Het knooppunt type moet zilver of goud- [duurzaamheids niveau](./service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)zijn. de reden hiervoor is:
- Bronzen geeft u geen garanties over het opslaan van status informatie.
- Met Silver en Gold duurzaamheid worden wijzigingen in de schaalset onderschept.
- Daarnaast biedt Gold u de controle over de updates van Azure onder de schaalset.

Met Service Fabric worden onderliggende wijzigingen en updates gecoördineerd zodat er geen gegevens verloren gaan. Wanneer u echter een knooppunt type met Bronze duurzaamheid verwijdert, kan de status informatie verloren gaan. Als u een primair knooppunt type verwijdert en de toepassing stateless is, is Bronze acceptabel. Wanneer u stateful workloads in productie uitvoert, moet de minimale configuratie zilver zijn. Voor productie scenario's geldt dat het primaire knooppunt type altijd zilver of goud moet zijn.

### <a name="more-about-bronze-durability"></a>Meer informatie over bronzen duurzaamheid

Bij het verwijderen van een type knoop punt dat bronzen, worden alle knoop punten in het knooppunt type onmiddellijk doorlopend. Service Fabric onderschept geen updates voor een schaalset van Bronze-knoop punten, waardoor alle Vm's onmiddellijk beschikbaar zijn. Als u iets op deze knoop punten hebt, gaan de gegevens verloren. Nu, zelfs als u stateless was, worden alle knoop punten in de Service Fabric deel nemen aan de ring, dus kan een hele groep verloren gaan, waardoor het cluster zelf kan worden overschreven.

## <a name="remove-a-node-type"></a>Een knooppunttype verwijderen

1. Let op deze vereisten voordat u het proces start.

    - Het cluster is in orde.
    - Er is nog voldoende capaciteit nadat het knooppunt type is verwijderd, bijvoorbeeld. het aantal knoop punten waarop het vereiste aantal replica's moet worden geplaatst.

2. Verplaats alle services met plaatsings beperkingen voor het type knoop punt van het knooppunt type.

    - Wijzig het toepassings-of service manifest om niet langer te verwijzen naar het knooppunt type.
    - Implementeer de wijziging.

    Controleer vervolgens of:
    - Alle hierboven gewijzigde services worden niet meer uitgevoerd op het knoop punt dat deel uitmaakt van het knooppunt type.
    - Alle services zijn in orde.

3. De markering van het knooppunt type als niet-primair verwijderen (overs laan voor niet-primaire knooppunt typen)

    - Zoek de Azure Resource Manager-sjabloon die wordt gebruikt voor de implementatie.
    - Zoek de sectie met betrekking tot het knooppunt type in het gedeelte Service Fabric.
    - Wijzig de eigenschap isPrimary in false. * * Verwijder de sectie met betrekking tot het knooppunt type niet in deze taak.
    - Implementeer de gewijzigde Azure Resource Manager sjabloon. * * Afhankelijk van de configuratie van het cluster kan deze stap enige tijd duren.
    
    Controleer vervolgens of:
    - Service Fabric sectie in de portal geeft aan dat het cluster gereed is.
    - Het cluster is in orde.
    - Geen van de knoop punten die tot het knooppunt type behoren, worden als seed-knoop punt gemarkeerd.

4. Schakel elk knoop punt in het knooppunt type uit.

    Maak verbinding met het cluster met behulp van Power shell en voer de volgende stap uit.
    
    ```powershell
    $nodeType = "" # specify the name of node type
    $nodes = Get-ServiceFabricNode
    
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Disable-ServiceFabricNode -Intent RemoveNode -NodeName $node.NodeName -Force
      }
    }
    ```

    - Voor de duurzaamheid van bronzen, wacht u totdat alle knoop punten de status uitgeschakeld krijgen
    - Voor de duurzaamheid van zilver en goud geldt dat sommige knoop punten in worden uitgeschakeld en dat de rest de status uitschakelen heeft. Controleer het tabblad Details van de knoop punten in het uitschakelen van de status, als deze allemaal vastzitten op het quorum voor infrastructuur service partities, dan is het veilig om door te gaan.

5. Stop de gegevens voor het knooppunt type.

    Maak verbinding met het cluster met behulp van Power shell en voer de volgende stap uit.
    
    ```powershell
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Start-ServiceFabricNodeTransition -Stop -OperationId (New-Guid) -NodeInstanceId $node.NodeInstanceId -NodeName $node.NodeName -StopDurationInSeconds 10000
      }
    }
    ```
    
    Wacht totdat alle knoop punten voor het knooppunt type zijn gemarkeerd.

6. Toewijzing van knoop punten in de oorspronkelijke Schaalset voor virtuele machines ongedaan maken
    
    Meld u aan bij het Azure-abonnement waarin de schaalset is geïmplementeerd en verwijder de virtuele-machine Schaalset. 

    ```powershell
    $scaleSetName="myscaleset"
    $scaleSetResourceType="Microsoft.Compute/virtualMachineScaleSets"
    
    Remove-AzResource -ResourceName $scaleSetName -ResourceType $scaleSetResourceType -ResourceGroupName $resourceGroupName -Force
    ```

    
7. Gegevens verwijderen voor het knooppunt type.

    Maak verbinding met het cluster met behulp van Power shell en voer de volgende stap uit.
    
    ```powershell
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Remove-ServiceFabricNodeState -NodeName $node.NodeName -Force
      }
    }
    ```

    Wacht totdat alle knoop punten uit het cluster zijn verwijderd. De knoop punten mogen niet worden weer gegeven in SFX.

8. Verwijder het knooppunt type uit de sectie Service Fabric.

    - Zoek de Azure Resource Manager-sjabloon die wordt gebruikt voor de implementatie.
    - Zoek de sectie met betrekking tot het knooppunt type in het gedeelte Service Fabric.
    - Verwijder de sectie die overeenkomt met het knooppunt type.
    - U kunt alleen voor Silver-en hogere duurzaamheids clusters de cluster bron in de sjabloon bijwerken en het status beleid configureren om de infra structuur te negeren:/de status van de systeem toepassing door toe te voegen `applicationDeltaHealthPolicies` onder cluster resource `properties` , zoals hieronder wordt vermeld. Het onderstaande beleid moet bestaande fouten negeren, maar geen nieuwe status fouten toestaan. 
 
 
     ```json
    "upgradeDescription":  
    { 
      "forceRestart": false, 
      "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807", 
      "healthCheckWaitDuration": "00:05:00", 
      "healthCheckStableDuration": "00:05:00", 
      "healthCheckRetryTimeout": "00:45:00", 
      "upgradeTimeout": "12:00:00", 
      "upgradeDomainTimeout": "02:00:00", 
      "healthPolicy": { 
        "maxPercentUnhealthyNodes": 100, 
        "maxPercentUnhealthyApplications": 100 
      }, 
      "deltaHealthPolicy":  
      { 
        "maxPercentDeltaUnhealthyNodes": 0, 
        "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0, 
        "maxPercentDeltaUnhealthyApplications": 0, 
        "applicationDeltaHealthPolicies":  
        { 
            "fabric:/System":  
            { 
                "defaultServiceTypeDeltaHealthPolicy":  
                { 
                        "maxPercentDeltaUnhealthyServices": 0 
                } 
            } 
        } 
      } 
    },
    ```

    - Implementeer de gewijzigde Azure Resource Manager sjabloon. * * Deze stap neemt enige tijd in beslag, meestal Maxi maal twee uur. Met deze upgrade worden instellingen gewijzigd in de InfrastructureService. Daarom is het opnieuw opstarten van het knoop punt vereist. In dit geval `forceRestart` wordt genegeerd. 
    De para meter `upgradeReplicaSetCheckTimeout` bepaalt de maximale tijds duur dat service Fabric wacht tot een partitie een veilige status heeft, als deze niet al een veilige status heeft. Zodra de controle van de veiligheid is geslaagd voor alle partities op een knoop punt, wordt Service Fabric door gegeven aan de upgrade op dat knoop punt.
    De waarde voor de para meter `upgradeTimeout` kan worden teruggebracht tot 6 uur, maar er moet een maximale beveiliging van 12 uur worden gebruikt.

    Controleer vervolgens of:
    - Service Fabric resource in de portal is nu weer gegeven.

9. Verwijder alle verwijzingen naar de resources met betrekking tot het knooppunt type uit de ARM-sjabloon.

    - Zoek de Azure Resource Manager-sjabloon die wordt gebruikt voor de implementatie.
    - Verwijder de virtuele-machine schaalset en andere resources die zijn gerelateerd aan het knooppunt type uit de sjabloon.
    - Implementeer de wijzigingen.

    Daarna kunt u het volgende doen:
    - Wacht tot de implementatie is voltooid.
    
10. Verwijder resources met betrekking tot het knooppunt type die niet meer worden gebruikt. Voor beeld Load Balancer en een openbaar IP-adres. 

    - Als u deze resources wilt verwijderen, kunt u dezelfde Power shell-opdracht gebruiken als wordt gebruikt in stap 6 om het specifieke resource type en de API-versie op te geven. 

> [!Note]
> Deze stap is optioneel als dezelfde Load Balancer en IP wordt hergebruikt tussen typen knoop punten.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over de [kenmerken](./service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)van de cluster duurzaamheid.
- Meer informatie over [knooppunt typen en virtual machine Scale sets](service-fabric-cluster-nodetypes.md).
- Meer informatie over het [schalen van service Fabric cluster](service-fabric-cluster-scaling.md).

---
title: Storingen in azure micro Services simuleren
description: In dit artikel vindt u informatie over de test acties in Microsoft Azure Service Fabric.
ms.topic: conceptual
ms.date: 06/07/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: c57f54096a9593f5ab25a5722d3f2d2b9878b511
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100595026"
---
# <a name="testability-actions"></a>Test acties
Voor het simuleren van een onbetrouwbare infra structuur biedt Azure Service Fabric u, de ontwikkelaar, de mogelijkheid om verschillende oorzaken van Real-World en status overgangen te simuleren. Deze worden weer gegeven als test taken. De acties zijn de Api's op laag niveau die een specifieke fout injectie, status overgang of validatie veroorzaken. Door deze acties te combi neren, kunt u uitgebreide test scenario's voor uw services schrijven.

Service Fabric biedt enkele algemene test scenario's die bestaan uit deze acties. We raden u ten zeerste aan gebruik te maken van deze ingebouwde scenario's, die zorgvuldig worden gekozen om algemene status overgangen en fout cases te testen. Acties kunnen echter worden gebruikt voor het maken van aangepaste test scenario's wanneer u een dekking wilt toevoegen voor scenario's die niet onder de ingebouwde scenario's vallen of die aangepast zijn aan uw toepassing.

C#-implementaties van de acties vindt u in de System.Fabric.dll-assembly. De System Fabric Power shell-module is te vinden in de Microsoft.ServiceFabric.Powershell.dll-assembly. Als onderdeel van de installatie van runtime wordt de Power shell-module ServiceFabric geïnstalleerd om gebruiks gemak te kunnen gebruiken.

## <a name="graceful-vs-ungraceful-fault-actions"></a>Gefeliciteerd versus probleem acties zonder uitstel
Test acties worden ingedeeld in twee grote buckets:

* Niet-verwerkte fouten: deze fouten simuleren fouten zoals het opnieuw opstarten van de computer en het proces loopt vast. In dergelijke gevallen van storingen wordt de uitvoerings context van het proces abrupt gestopt. Dit betekent dat het opruimen van de status niet kan worden uitgevoerd voordat de toepassing opnieuw wordt gestart.
* Probleemloze fouten: deze fouten simuleren gesimuleerde acties zoals replica verplaatsingen en worden geactiveerd door taak verdeling. In dergelijke gevallen ontvangt de service een melding van de afsluiting en kan deze de status opschonen voordat deze wordt afgesloten.

Voor een betere kwaliteits validatie voert u de service en de zakelijke werk belasting uit terwijl u verschillende gepaste en niet-respijt fouten veroorzaakt. Scenario's waarbij het service proces plotseling wordt afgesloten in het midden van een bepaalde werk stroom, worden uitgevallen. Hiermee wordt het herstelpad getest zodra de service replica is hersteld door Service Fabric. Dit helpt de consistentie van gegevens te testen en te controleren of de status van de service correct wordt bewaard na storingen. De andere set fouten (het probleemloze fouten) test of de service op de juiste wijze reageert op replica's die door Service Fabric worden verplaatst. Hiermee wordt de verwerking van de annulering in de methode RunAsync getest. De service moet controleren of het annulerings token is ingesteld, de status op de juiste wijze opslaat en de methode RunAsync afsluiten.

## <a name="testability-actions-list"></a>Lijst met test acties
| Bewerking | Beschrijving | Beheerde API | Power shell-cmdlet | Getoonde of niet-respijt fouten |
| --- | --- | --- | --- | --- |
| CleanTestState |Hiermee verwijdert u alle test status van het cluster in het geval van een slecht afsluiten van het test stuur programma. |CleanTestStateAsync |Remove-ServiceFabricTestState |Niet van toepassing |
| InvokeDataLoss |Veroorzaakt gegevens verlies in een service partitie. |InvokeDataLossAsync |Invoke-ServiceFabricPartitionDataLoss |Socket |
| InvokeQuorumLoss |Hiermee wordt een gegeven stateful service partitie in quorum verlies geplaatst. |InvokeQuorumLossAsync |Invoke-ServiceFabricQuorumLoss |Socket |
| MovePrimary |Hiermee wordt de opgegeven primaire replica van een stateful service naar het opgegeven cluster knooppunt verplaatst. |MovePrimaryAsync |Move-ServiceFabricPrimaryReplica |Socket |
| MoveSecondary |Hiermee verplaatst u de huidige secundaire replica van een stateful service naar een ander cluster knooppunt. |MoveSecondaryAsync |Move-ServiceFabricSecondaryReplica |Socket |
| RemoveReplica |Simuleert een replica fout door een replica uit een cluster te verwijderen. Hiermee wordt de replica gesloten en wordt deze overgezet naar de rol geen, waardoor alle statussen uit het cluster worden verwijderd. |RemoveReplicaAsync |Remove-ServiceFabricReplica |Socket |
| RestartDeployedCodePackage |Simuleert een fout in het proces van een code pakket door een code pakket dat is geïmplementeerd op een knoop punt in een cluster opnieuw te starten. Hiermee wordt het proces van het code pakket afgebroken, waardoor alle gebruikers Service replica's die in dat proces worden gehost, opnieuw worden gestart. |RestartDeployedCodePackageAsync |Restart-ServiceFabricDeployedCodePackage |Geforceerde |
| RestartNode |Simuleert een Service Fabric cluster knooppunt fout door een knoop punt opnieuw op te starten. |RestartNodeAsync |Restart-ServiceFabricNode |Geforceerde |
| RestartPartition |Simuleert een beschermings scenario voor het Data Center of het cluster voor beslag door enkele of alle replica's van een partitie opnieuw te starten. |RestartPartitionAsync |Restart-ServiceFabricPartition |Socket |
| RestartReplica |Simuleert een replica fout door een persistente replica in een cluster opnieuw op te starten, de replica te sluiten en deze vervolgens opnieuw te openen. |RestartReplicaAsync |Restart-ServiceFabricReplica |Socket |
| StartNode |Hiermee start u een knoop punt in een cluster dat al is gestopt. |StartNodeAsync |Start-ServiceFabricNode |Niet van toepassing |
| StopNode |Simuleert een storing in een knoop punt door een knoop punt in een cluster te stoppen. Het knoop punt blijft actief totdat StartNode wordt aangeroepen. |StopNodeAsync |Stop-ServiceFabricNode |Geforceerde |
| ValidateApplication |Valideert de beschik baarheid en de status van alle Service Fabric services binnen een toepassing, meestal nadat er een fout is opgetreden in het systeem. |ValidateApplicationAsync |Test-ServiceFabricApplication |Niet van toepassing |
| ValidateService |Valideert de beschik baarheid en de status van een Service Fabric service, meestal nadat er een storing in het systeem is opgetreden. |ValidateServiceAsync |Test-ServiceFabricService |Niet van toepassing |

## <a name="running-a-testability-action-using-powershell"></a>Een test actie uitvoeren met Power shell
Deze zelf studie laat zien hoe u een test baarheid kunt uitvoeren met behulp van Power shell. U leert hoe u een test actie kunt uitvoeren op een lokaal (een) cluster of in een Azure-cluster. Microsoft.Fabric.Powershell.dll--de module Service Fabric Power shell--wordt automatisch geïnstalleerd wanneer u de micro soft Service Fabric MSI installeert. De module wordt automatisch geladen wanneer u een Power shell-prompt opent.

Zelfstudie segmenten:

* [Een actie uitvoeren op een cluster met één doos](#run-an-action-against-a-one-box-cluster)
* [Een actie uitvoeren op een Azure-cluster](#run-an-action-against-an-azure-cluster)

### <a name="run-an-action-against-a-one-box-cluster"></a>Een actie uitvoeren op een cluster met één doos
Als u een test actie wilt uitvoeren op basis van een lokaal cluster, maakt u eerst verbinding met het cluster en opent u de Power shell-prompt in de beheerders modus. We kijken naar de actie **restart-ServiceFabricNode** .

```powershell
Restart-ServiceFabricNode -NodeName Node1 -CompletionMode DoNotVerify
```

Hier wordt de actie **opnieuw gestart-ServiceFabricNode** wordt uitgevoerd op een knoop punt met de naam ' Knooppunt1 '. De voltooiings modus geeft aan dat niet moet worden gecontroleerd of de bewerking voor het opnieuw starten van het knoop punt werkelijk is geslaagd. Als u de voltooiings modus als ' controleren ' opgeeft, wordt gecontroleerd of de actie voor opnieuw opstarten werkelijk is geslaagd. In plaats van het knoop punt rechtstreeks op naam op te geven, kunt u dit als volgt opgeven via een partitie sleutel en het soort replica:

```powershell
Restart-ServiceFabricNode -ReplicaKindPrimary  -PartitionKindNamed -PartitionKey Partition3 -CompletionMode Verify
```


```powershell
$connection = "localhost:19000"
$nodeName = "Node1"

Connect-ServiceFabricCluster $connection
Restart-ServiceFabricNode -NodeName $nodeName -CompletionMode DoNotVerify
```

**Restart-ServiceFabricNode** moet worden gebruikt om een service Fabric knoop punt in een cluster opnieuw op te starten. Hiermee wordt het Fabric.exe proces gestopt, waardoor alle systeem service-en gebruikers Service replica's die worden gehost op dat knoop punt opnieuw worden gestart. Als u deze API gebruikt om uw service te testen, kunt u fouten opsporen op basis van de herstel paden voor failover. Het helpt knooppunt fouten in het cluster te simuleren.

De volgende scherm afbeelding toont de test baarheids opdracht **restart-ServiceFabricNode** in actie.

![Scherm opname van het uitvoeren van de Restart-ServiceFabricNode-opdracht in Power shell.](media/service-fabric-testability-actions/Restart-ServiceFabricNode.png)

De uitvoer van de eerste **Get-ServiceFabricNode** (een cmdlet van de service Fabric Power shell-module) toont aan dat het lokale cluster vijf knoop punten heeft: node. 1 tot knoop punt. 5. Nadat de test actie (cmdlet) **restart-ServiceFabricNode** is uitgevoerd op het knoop punt met de naam node. 4, zien we dat de uptime van het knoop punt opnieuw is ingesteld.

### <a name="run-an-action-against-an-azure-cluster"></a>Een actie uitvoeren op een Azure-cluster
Het uitvoeren van een test actie (door gebruik te maken van Power shell) voor een Azure-cluster is vergelijkbaar met het uitvoeren van de actie op een lokaal cluster. Het enige verschil is dat voordat u de actie kunt uitvoeren, in plaats van verbinding te maken met het lokale cluster, eerst verbinding moet maken met het Azure-cluster.

## <a name="running-a-testability-action-using-c35"></a>Een test actie uitvoeren met behulp van C&#35;
Als u een test actie wilt uitvoeren met behulp van C#, moet u eerst verbinding maken met het cluster met behulp van FabricClient. Vraag vervolgens de para meters op die nodig zijn om de actie uit te voeren. Verschillende para meters kunnen worden gebruikt om dezelfde actie uit te voeren.
Als u de actie RestartServiceFabricNode wilt uitvoeren, kunt u deze een voor een gebruiken om deze uit te voeren met behulp van de knooppunt gegevens (knooppunt naam en knooppunt exemplaar-ID) in het cluster.

```csharp
RestartNodeAsync(nodeName, nodeInstanceId, completeMode, operationTimeout, CancellationToken.None)
```

Uitleg over para meter:

* **CompleteMode** geeft aan dat de modus niet moet controleren of de actie voor opnieuw opstarten werkelijk is geslaagd. Als u de voltooiings modus als ' controleren ' opgeeft, wordt gecontroleerd of de actie voor opnieuw opstarten werkelijk is geslaagd.  
* **OperationTimeout** stelt de hoeveelheid tijd in die de bewerking moet volt ooien voordat een TimeoutException-uitzonde ring wordt gegenereerd.
* Met **CancellationToken** kan een aanroep in behandeling worden geannuleerd.

In plaats van het knoop punt rechtstreeks op naam op te geven, kunt u dit opgeven via een partitie sleutel en het soort replica.

Zie PartitionSelector en ReplicaSelector voor meer informatie.

```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Fabric.Testability;
using System.Fabric;
using System.Threading;
using System.Numerics;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");
        string nodeName = "N0040";
        BigInteger nodeInstanceId = 130743013389060139;

        Console.WriteLine("Starting RestartNode test");
        try
        {
            //Restart the node by using ReplicaSelector
            RestartNodeAsync(clusterConnection, serviceName).Wait();

            //Another way to restart node is by using nodeName and nodeInstanceId
            RestartNodeAsync(clusterConnection, nodeName, nodeInstanceId).Wait();
        }
        catch (AggregateException exAgg)
        {
            Console.WriteLine("RestartNode did not complete: ");
            foreach (Exception ex in exAgg.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("RestartNode completed.");
        return 0;
    }

    static async Task RestartNodeAsync(string clusterConnection, Uri serviceName)
    {
        PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);
        ReplicaSelector primaryofReplicaSelector = ReplicaSelector.PrimaryOf(randomPartitionSelector);

        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.FaultManager.RestartNodeAsync(primaryofReplicaSelector, CompletionMode.Verify);
    }

    static async Task RestartNodeAsync(string clusterConnection, string nodeName, BigInteger nodeInstanceId)
    {
        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.FaultManager.RestartNodeAsync(nodeName, nodeInstanceId, CompletionMode.Verify);
    }
}
```

## <a name="partitionselector-and-replicaselector"></a>PartitionSelector en ReplicaSelector
### <a name="partitionselector"></a>PartitionSelector
PartitionSelector is een hulp programma dat in de test baarheid wordt weer gegeven en wordt gebruikt om een specifieke partitie te selecteren waarop de test baarheids acties moeten worden uitgevoerd. Het kan worden gebruikt om een specifieke partitie te selecteren als de partitie-ID vooraf bekend is. U kunt ook de partitie sleutel opgeven en met de bewerking wordt de partitie-ID intern omgezet. U kunt ook een wille keurige partitie selecteren.

Als u deze helper wilt gebruiken, maakt u het PartitionSelector-object en selecteert u de partitie met behulp van een van de Select-methoden. Geef vervolgens het object PartitionSelector door aan de API waarvoor het is vereist. Als er geen optie is geselecteerd, wordt standaard een wille keurige partitie gebruikt.

```csharp
Uri serviceName = new Uri("fabric:/samples/InMemoryToDoListApp/InMemoryToDoListService");
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
string partitionName = "Partition1";
Int64 partitionKeyUniformInt64 = 1;

// Select a random partition
PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

// Select a partition based on ID
PartitionSelector partitionSelectorById = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);

// Select a partition based on name
PartitionSelector namedPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionName);

// Select a partition based on partition key
PartitionSelector uniformIntPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionKeyUniformInt64);
```

### <a name="replicaselector"></a>ReplicaSelector
ReplicaSelector is een hulp programma dat in de test baarheid wordt weer gegeven en wordt gebruikt om te helpen bij het selecteren van een replica waarop de test baarheids acties worden uitgevoerd. Het kan worden gebruikt om een specifieke replica te selecteren als de replica-ID vooraf bekend is. Daarnaast hebt u de mogelijkheid om een primaire replica of een wille keurige secundaire te selecteren. ReplicaSelector is afgeleid van PartitionSelector, dus u moet zowel de replica als de partitie selecteren waarop u de test bewerking wilt uitvoeren.

Als u deze helper wilt gebruiken, maakt u een ReplicaSelector-object en stelt u in hoe u de replica en de partitie wilt selecteren. U kunt het vervolgens door geven aan de API waarvoor deze vereist is. Als er geen optie is geselecteerd, wordt standaard een wille keurige replica en wille keurige partitie gebruikt.

```csharp
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
PartitionSelector partitionSelector = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);
long replicaId = 130559876481875498;

// Select a random replica
ReplicaSelector randomReplicaSelector = ReplicaSelector.RandomOf(partitionSelector);

// Select the primary replica
ReplicaSelector primaryReplicaSelector = ReplicaSelector.PrimaryOf(partitionSelector);

// Select the replica by ID
ReplicaSelector replicaByIdSelector = ReplicaSelector.ReplicaIdOf(partitionSelector, replicaId);

// Select a random secondary replica
ReplicaSelector secondaryReplicaSelector = ReplicaSelector.RandomSecondaryOf(partitionSelector);
```

## <a name="next-steps"></a>Volgende stappen
* [Test scenario's](service-fabric-testability-scenarios.md)
* Uw service testen
  * [Fouten simuleren tijdens service werkbelastingen](service-fabric-testability-workload-tests.md)
  * [Problemen met Service-naar-service-communicatie](service-fabric-testability-scenarios-service-communication.md)


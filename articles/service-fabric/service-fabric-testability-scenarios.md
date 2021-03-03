---
title: Chaos en failover-tests maken voor Azure Service Fabric
description: Met behulp van de test scenario's voor de Service Fabric chaos test en failover kunt u fouten opdoen en de betrouw baarheid van uw services controleren.
ms.topic: conceptual
ms.date: 10/1/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: ef49d541cd00d289176ca61e055102faf6df7514
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101735553"
---
# <a name="testability-scenarios"></a>Test scenario's
Grote gedistribueerde systemen zoals Cloud infrastructuren zijn inherent onbetrouwbaar. Azure Service Fabric biedt ontwikkel aars de mogelijkheid om services te schrijven voor het uitvoeren van een onbetrouwbare infra structuur. Ontwikkel aars die een hoogwaardige service willen schrijven, moeten een dergelijke onbetrouwbare infra structuur kunnen maken om de stabiliteit van hun services te testen.

De fout analyse service biedt ontwikkel aars de mogelijkheid om fout acties te ondernemen om services te testen bij de aanwezigheid van fouten. Bij doel gesimuleerde fouten krijgt u echter slechts tot nu toe. Als u de tests verder wilt uitvoeren, kunt u de test scenario's in Service Fabric gebruiken: een chaos-test en een failover-test. Deze scenario's simuleren continue Interleaved fouten, zowel gesimuleerd als zonder enige tijd, gedurende het hele cluster gedurende een lange periode. Zodra een test is geconfigureerd met de frequentie en het soort fouten, kan deze worden gestart via C#-Api's of Power shell, om fouten te genereren in het cluster en uw service.

> [!WARNING]
> ChaosTestScenario wordt vervangen door een meer robuuste, op Services gebaseerde chaos. Raadpleeg het nieuwe [chaos](service-fabric-controlled-chaos.md) -artikel voor meer informatie.
> 
> 

## <a name="chaos-test"></a>Chaos-test
Het chaos-scenario genereert fouten in het volledige Service Fabric cluster. Het scenario comprimeert fouten doorgaans in maanden of jaren tot enkele uren. Door de combi natie van Interleaved fouten met het hoge fout aantal worden hoek cases gevonden die anderszins niet zijn gemist. Dit leidt tot een aanzienlijke verbetering van de code kwaliteit van de service.

### <a name="faults-simulated-in-the-chaos-test"></a>Gesimuleerde fouten in de chaos-test
* Een knoop punt opnieuw starten
* Een geïmplementeerd code pakket opnieuw starten
* Een replica verwijderen
* Een replica opnieuw opstarten
* Een primaire replica verplaatsen (optioneel)
* Een secundaire replica verplaatsen (optioneel)

De chaos-test voert meerdere herhalingen van fouten en cluster validaties uit voor de opgegeven periode. De tijd die nodig is om het cluster te stabiliseren en om de validatie te volt ooien, kan ook worden geconfigureerd. Het scenario mislukt als u één fout in de cluster validatie aanraakt.

Stel bijvoorbeeld dat een testset gedurende één uur wordt uitgevoerd met een maximum van drie gelijktijdige fouten. De test veroorzaakt drie fouten en valideert vervolgens de cluster status. Tijdens de test wordt de vorige stap herhaald totdat het cluster niet meer in orde is of één uur wordt weer gegeven. Als het cluster in een wille keurige herhaling wordt beschadigd, d.w.z. het niet binnen een geconfigureerde tijd wordt gestabiliseerd, mislukt de test met een uitzonde ring. Deze uitzonde ring geeft aan dat er iets mis is gegaan en verder moet worden onderzocht.

In het huidige formulier worden door de engine voor het genereren van fouten in de chaos-test alleen veilige fouten geinduceerd. Dit betekent dat als er geen externe fouten zijn, er geen quorum-of gegevens verlies optreedt.

### <a name="important-configuration-options"></a>Belang rijke configuratie opties
* **TimeToRun**: de totale tijd dat de test wordt uitgevoerd voordat het succes wordt voltooid. De test kan eerder worden voltooid in plaats van een validatie fout.
* **MaxClusterStabilizationTimeout**: de maximale hoeveelheid tijd die moet worden gewacht totdat het cluster in orde is voordat de test is mislukt. De controles worden uitgevoerd, ongeacht of de status van het cluster OK is, de service status is OK, de grootte van de doel replicaset wordt behaald voor de service partitie en er zijn geen inbuild-replica's.
* **MaxConcurrentFaults**: het maximum aantal gelijktijdige fouten dat is opgetreden in elke iteratie. Hoe hoger de waarde, hoe sterker de test, dus wat resulteert in complexere failovers en overgangs combinaties. De test garandeert dat bij afwezigheid van externe fouten geen quorum of gegevens verlies optreedt, ongeacht hoe hoog deze configuratie is.
* **EnableMoveReplicaFaults**: Hiermee worden de fouten die het verplaatsen van de primaire of secundaire replica's veroorzaken, in-of uitgeschakeld. Deze fouten zijn standaard uitgeschakeld.
* **WaitTimeBetweenIterations**: de hoeveelheid tijd die moet worden gewacht tussen iteraties, d.w.z. na een afronding van fouten en de bijbehorende validatie.

### <a name="how-to-run-the-chaos-test"></a>De chaos-test uitvoeren
C#-voorbeeld

```csharp
using System;
using System.Fabric;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";

        Console.WriteLine("Starting Chaos Test Scenario...");
        try
        {
            RunChaosTestScenarioAsync(clusterConnection).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Chaos Test Scenario did not complete: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Chaos Test Scenario completed.");
        return 0;
    }

    static async Task RunChaosTestScenarioAsync(string clusterConnection)
    {
        TimeSpan maxClusterStabilizationTimeout = TimeSpan.FromSeconds(180);
        uint maxConcurrentFaults = 3;
        bool enableMoveReplicaFaults = true;

        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);

        // The chaos test scenario should run at least 60 minutes or until it fails.
        TimeSpan timeToRun = TimeSpan.FromMinutes(60);
        ChaosTestScenarioParameters scenarioParameters = new ChaosTestScenarioParameters(
          maxClusterStabilizationTimeout,
          maxConcurrentFaults,
          enableMoveReplicaFaults,
          timeToRun);

        // Other related parameters:
        // Pause between two iterations for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenIterations = TimeSpan.FromSeconds(30);
        // Pause between concurrent actions for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenFaults = TimeSpan.FromSeconds(10);

        // Create the scenario class and execute it asynchronously.
        ChaosTestScenario chaosScenario = new ChaosTestScenario(fabricClient, scenarioParameters);

        try
        {
            await chaosScenario.ExecuteAsync(CancellationToken.None);
        }
        catch (AggregateException ae)
        {
            throw ae.InnerException;
        }
    }
}
```

PowerShell

De Service Fabric Power shell-module bevat twee manieren om een chaos-scenario te starten. `Invoke-ServiceFabricChaosTestScenario` is gebaseerd op de client en als de client computer halverwege de test wordt afgesloten, worden er geen verdere fouten geïntroduceerd. U kunt ook een reeks opdrachten gebruiken om te zorgen dat de test wordt uitgevoerd bij het afsluiten van de computer. `Start-ServiceFabricChaos` maakt gebruik van een stateful en betrouw bare systeem service met de naam FaultAnalysisService, zodat fouten blijven worden geïntroduceerd totdat de TimeToRun is ingesteld. `Stop-ServiceFabricChaos` kan worden gebruikt om het scenario hand matig te stoppen en er `Get-ServiceFabricChaosReport` wordt een rapport ontvangen. Zie voor meer informatie de [Azure service Fabric Power shell-referentie](/powershell/module/ServiceFabric/New-ServiceFabricService?view=azureservicefabricps) en het ontregelde [chaos in service Fabric-clusters](service-fabric-controlled-chaos.md).

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster $connection

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```


## <a name="failover-test"></a>Failover testen
Het test scenario voor de failover is een versie van het chaos-test scenario dat gericht is op een specifieke service partitie. Hiermee wordt het effect van een failover naar een specifieke service partitie getest, waarbij de andere services ongewijzigd blijven. Zodra deze is geconfigureerd met de informatie over de doel partitie en andere para meters, wordt deze uitgevoerd als een hulp programma aan de client zijde die C#-Api's of Power shell gebruikt om fouten te genereren voor een service partitie. Het scenario doorloopt een opeenvolging van gesimuleerde fouten en service validatie terwijl uw bedrijfs logica aan de zijde wordt uitgevoerd om een werk belasting te leveren. Een fout in de service validatie geeft aan dat er een probleem is dat verder moet worden onderzocht.

### <a name="faults-simulated-in-the-failover-test"></a>Gesimuleerde fouten in de failover-test
* Een geïmplementeerd code pakket waarop de partitie wordt gehost opnieuw opstarten
* Een primaire/secundaire replica of stateless exemplaar verwijderen
* Een primaire secundaire replica opnieuw opstarten (als er een persistente service is)
* Een primaire replica verplaatsen
* Een secundaire replica verplaatsen
* De partitie opnieuw opstarten

De failover-test veroorzaakt een gekozen fout en voert vervolgens validatie uit op de service om de stabiliteit te garanderen. De failover-test induceert slechts één fout per keer, in tegens telling tot meerdere fouten in de chaos-test. Als de service partitie niet stabiel is binnen de geconfigureerde time-out na elke fout, mislukt de test. De test induceert alleen veilige storingen. Dit betekent dat als er geen externe fouten optreden, er geen quorum-of gegevens verlies optreedt.

### <a name="important-configuration-options"></a>Belang rijke configuratie opties
* **PartitionSelector**: selector-object waarmee de partitie wordt opgegeven waarvoor een doel moet worden ingesteld.
* **TimeToRun**: de totale tijd dat de test wordt uitgevoerd voordat deze wordt voltooid.
* **MaxServiceStabilizationTimeout**: de maximale hoeveelheid tijd die moet worden gewacht totdat het cluster in orde is voordat de test is mislukt. De uitgevoerde controles zijn of de status van de service OK is, de grootte van de doel replicaset wordt bereikt voor alle partities en er bestaan geen inbouw replica's.
* **WaitTimeBetweenFaults**: de hoeveelheid tijd die moet worden gewacht tussen elke fout en validatie cyclus.

### <a name="how-to-run-the-failover-test"></a>De failover-test uitvoeren
**C#**

```csharp
using System;
using System.Fabric;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");

        Console.WriteLine("Starting Chaos Test Scenario...");
        try
        {
            RunFailoverTestScenarioAsync(clusterConnection, serviceName).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Chaos Test Scenario did not complete: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Chaos Test Scenario completed.");
        return 0;
    }

    static async Task RunFailoverTestScenarioAsync(string clusterConnection, Uri serviceName)
    {
        TimeSpan maxServiceStabilizationTimeout = TimeSpan.FromSeconds(180);
        PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);

        // The chaos test scenario should run at least 60 minutes or until it fails.
        TimeSpan timeToRun = TimeSpan.FromMinutes(60);
        FailoverTestScenarioParameters scenarioParameters = new FailoverTestScenarioParameters(
          randomPartitionSelector,
          timeToRun,
          maxServiceStabilizationTimeout);

        // Other related parameters:
        // Pause between two iterations for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenIterations = TimeSpan.FromSeconds(30);
        // Pause between concurrent actions for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenFaults = TimeSpan.FromSeconds(10);

        // Create the scenario class and execute it asynchronously.
        FailoverTestScenario failoverScenario = new FailoverTestScenario(fabricClient, scenarioParameters);

        try
        {
            await failoverScenario.ExecuteAsync(CancellationToken.None);
        }
        catch (AggregateException ae)
        {
            throw ae.InnerException;
        }
    }
}
```


**PowerShell**

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/SampleApp/SampleService"

Connect-ServiceFabricCluster $connection

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindSingleton
```

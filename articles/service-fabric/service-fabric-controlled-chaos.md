---
title: Chaos in Service Fabric clusters induceren
description: Fout injectie en cluster analyse service-Api's gebruiken voor het beheren van chaos in het cluster.
ms.topic: conceptual
ms.date: 02/05/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 72b8f7e9e4934b516f843ae8bc9bb7adc1c349ec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101720503"
---
# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>Beheerde chaos in Service Fabric clusters induceren
Grootschalige gedistribueerde systemen zoals Cloud infrastructuren zijn inherent onbetrouwbaar. Met Azure Service Fabric kunnen ontwikkel aars betrouw bare gedistribueerde services bovenop een onbetrouwbare infra structuur schrijven. Ontwikkel aars moeten de stabiliteit van hun services kunnen testen om robuuste gedistribueerde services boven op een onbetrouwbare infra structuur te schrijven, terwijl de onderliggende onbetrouwbare infra structuur in gecompliceerde status overgangen door problemen gaat.

De [fout injectie en de cluster analyse service](./service-fabric-testability-overview.md) (ook wel bekend als de fout analyse service) biedt ontwikkel aars de mogelijkheid fouten te ontzeggen om hun services te testen. Deze gesimuleerde fouten, zoals het [opnieuw starten van een partitie](/powershell/module/servicefabric/start-servicefabricpartitionrestart), kunnen helpen bij het uitoefenen van de meest voorkomende status overgangen. Doel gesimuleerde fouten worden echter bepaald door definitie en kunnen fouten veroorzaken die alleen worden weer gegeven in een moeilijk te voors pellen, lange en ingewikkelde volg orde van status overgangen. Voor een onzuivere test kunt u chaos gebruiken.

Chaos simuleert periodieke, Interleaved fouten (zowel gefeliciteerd als zonder enige uitstel) in het hele cluster gedurende een langere periode. Een correcte fout bestaat uit een set Service Fabric-API-aanroepen, bijvoorbeeld een fout bij het opnieuw starten van de replica, omdat dit een sluiten is, gevolgd door een open op een replica. Verwijder de replica, verplaats de primaire replica en verplaats de secundaire replica naar een andere probleemloze fout die wordt veroorzaakt door chaos. Niet-verwerkings fouten zijn proces afsluiten, zoals het opnieuw starten van het knoop punt en het opnieuw starten van code pakket. 

Wanneer u chaos hebt geconfigureerd met het aantal en het soort fouten, kunt u chaos starten via C#, Power shell of REST API om te beginnen met het genereren van fouten in het cluster en in uw services. U kunt chaos configureren om te worden uitgevoerd gedurende een opgegeven periode (bijvoorbeeld gedurende één uur), waarna chaos automatisch stopt, of u kunt StopChaos-API (C#, Power shell of REST) aanroepen om deze op elk gewenst moment te stoppen.

> [!NOTE]
> In het huidige formulier induceert chaos alleen veilige storingen, wat impliceert dat bij afwezigheid van externe fouten een quorum verlies of gegevens verlies optreedt.
>

Terwijl chaos wordt uitgevoerd, worden er verschillende gebeurtenissen gegenereerd waarmee de status van de uitvoering op het moment wordt vastgelegd. Een ExecutingFaultsEvent bevat bijvoorbeeld alle fouten die chaos heeft besloten om in die iteratie uit te voeren. Een ValidationFailedEvent bevat de details van een validatie fout (status-of stabiliteits problemen) die zijn gevonden tijdens de validatie van het cluster. U kunt de GetChaosReport-API (C#, Power shell of REST) aanroepen om het rapport van chaos-uitvoeringen weer te geven. Deze gebeurtenissen worden bewaard in een [betrouw bare woorden lijst](./service-fabric-reliable-services-reliable-collections.md), die een Afbrekings beleid heeft dat door twee configuraties wordt bepaald: **MaxStoredChaosEventCount** (standaard waarde is 25000) en **StoredActionCleanupIntervalInSeconds** (standaard waarde is 3600). Elke *StoredActionCleanupIntervalInSeconds* chaos-controle en alle, behalve de meest recente *MaxStoredChaosEventCount* -gebeurtenissen, worden verwijderd uit de betrouw bare woorden lijst.

## <a name="faults-induced-in-chaos"></a>Fouten die zijn opgetreden in chaos
Chaos genereert fouten in het hele Service Fabric cluster en comprimeert fouten die in maanden of jaren in een paar uur worden weer gegeven. De combi natie van Interleaved fouten met het hoge fout aantal detecteert hoek gevallen die anders kunnen worden gemist. Deze oefening van chaos leidt tot een aanzienlijke verbetering van de code kwaliteit van de service.

Chaos veroorzaakt fouten uit de volgende categorieën:

* Een knoop punt opnieuw starten
* Een geïmplementeerd code pakket opnieuw starten
* Een replica verwijderen
* Een replica opnieuw opstarten
* Een primaire replica verplaatsen (configureerbaar)
* Een secundaire replica verplaatsen (configureerbaar)

Chaos wordt uitgevoerd in meerdere herhalingen. Elke herhaling bestaat uit fouten en cluster validatie voor de opgegeven periode. U kunt de tijd configureren die wordt besteed aan het cluster om te stabiliseren en om validatie te volt ooien. Als er een fout wordt gevonden in de cluster validatie, genereert chaos een ValidationFailedEvent met de UTC-tijds tempel en de fout Details. Denk bijvoorbeeld aan een exemplaar van chaos dat is ingesteld om een uur te worden uitgevoerd met een maximum van drie gelijktijdige fouten. Chaos induceert drie fouten en valideert vervolgens de cluster status. De vorige stap wordt doorlopend totdat deze expliciet wordt gestopt via de StopChaosAsync-API of door gang van één uur. Als het cluster in een wille keurige herhaling wordt beschadigd (dat wil zeggen dat het niet stabiel is of niet in orde is binnen de door gegeven MaxClusterStabilizationTimeout), genereert chaos een ValidationFailedEvent. Deze gebeurtenis geeft aan dat er een fout is opgetreden en dat er een ander onderzoek nodig is.

U kunt de GetChaosReport-API (Power shell, C# of REST) gebruiken om te bepalen welke fouten chaos veroorzaakt. Met de API wordt het volgende segment van het chaos-rapport opgehaald op basis van het door gegeven vervolg token of het door gegeven tijds bereik. U kunt de ContinuationToken opgeven om het volgende segment van het chaos-rapport op te halen of u kunt het tijds bereik opgeven via StartTimeUtc en EndTimeUtc, maar u mag niet zowel het ContinuationToken als het tijds bereik opgeven in dezelfde aanroep. Wanneer er meer dan 100 chaos-gebeurtenissen zijn, wordt het chaos-rapport geretourneerd in segmenten waarin een segment niet meer dan 100 chaos gebeurtenissen bevat.

## <a name="important-configuration-options"></a>Belang rijke configuratie opties
* **TimeToRun**: de totale tijd dat chaos wordt uitgevoerd voordat het is voltooid. U kunt chaos stoppen voordat deze voor de TimeToRun-periode is uitgevoerd via de StopChaos-API.

* **MaxClusterStabilizationTimeout**: de maximale hoeveelheid tijd die moet worden gewacht totdat het cluster in orde is voordat een ValidationFailedEvent wordt geproduceerd. Deze wacht tijd is het verminderen van de belasting van het cluster tijdens het herstellen. De controles worden uitgevoerd:
  * Als de status van het cluster OK is
  * Als de service status OK is
  * Als de grootte van de doel replicaset is bereikt voor de service partitie
  * Er bestaan geen inbuild-replica's
* **MaxConcurrentFaults**: het maximum aantal gelijktijdige fouten dat in elke iteratie wordt veroorzaakt. Hoe hoger de waarde, hoe sterker het chaos en de failovers en de status overgangen die het cluster doorloopt, zijn ook ingewik kelder. 

> [!NOTE]
> Ongeacht hoe hoog een waarde *MaxConcurrentFaults* heeft, chaos garanties-bij afwezigheid van externe fouten-er is geen quorum verlies of gegevens verlies.
>

* **EnableMoveReplicaFaults**: Hiermee worden de fouten die ervoor zorgen dat de primaire of secundaire replica's worden verplaatst, in-of uitgeschakeld. Deze fouten zijn standaard ingeschakeld.
* **WaitTimeBetweenIterations**: de hoeveelheid tijd die moet worden gewacht tussen iteraties. Dat wil zeggen dat de hoeveelheid tijd chaos pauzeert na het uitvoeren van een afronding van fouten en de bijbehorende validatie van de status van het cluster heeft voltooid. Hoe hoger de waarde, hoe lager, het gemiddelde aantal mislukte fout injecties.
* **WaitTimeBetweenFaults**: de hoeveelheid tijd die moet worden gewacht tussen twee opeenvolgende fouten in één iteratie. Hoe hoger de waarde, hoe lager de gelijktijdigheid van (of de overlappende) fouten.
* **ClusterHealthPolicy**: het cluster status beleid wordt gebruikt voor het valideren van de status van het cluster in tussen chaos-iteraties. Als de status van het cluster beschadigd is of als er een onverwachte uitzonde ring optreedt tijdens de uitvoering van de fout, wacht chaos gedurende 30 minuten vóór de volgende status controle: het cluster wordt enige tijd op recuperate geleverd.
* **Context**: een verzameling van (teken reeks, teken reeks) type sleutel-waardeparen. De kaart kan worden gebruikt om informatie vast te leggen over de uitvoering van de chaos. Er kunnen niet meer dan 100 dergelijke paren bestaan en elke teken reeks (sleutel of waarde) mag Maxi maal 4095 tekens lang zijn. Deze kaart wordt ingesteld door de start van de chaos-uitvoering, zodat de context van de specifieke uitvoering optioneel kan worden opgeslagen.
* **ChaosTargetFilter**: dit filter kan worden gebruikt om chaos-fouten alleen te richten op bepaalde knooppunt typen of alleen op bepaalde toepassings exemplaren. Als ChaosTargetFilter niet wordt gebruikt, heeft chaos fouten met alle cluster entiteiten. Als ChaosTargetFilter wordt gebruikt, veroorzaakt chaos alleen de entiteiten die voldoen aan de ChaosTargetFilter-specificatie. NodeTypeInclusionList en ApplicationInclusionList geven alleen de semantiek van de vereniging toe. Met andere woorden, het is niet mogelijk om een snij punt op te geven voor NodeTypeInclusionList en ApplicationInclusionList. Het is bijvoorbeeld niet mogelijk om ' fout deze toepassing alleen op te geven wanneer deze zich op het knooppunt type bevindt '. Wanneer een entiteit is opgenomen in NodeTypeInclusionList of ApplicationInclusionList, kan die entiteit niet worden uitgesloten met ChaosTargetFilter. Zelfs als applicationx niet wordt weer gegeven in ApplicationInclusionList, kan in sommige chaos iteratie applicationx een fout optreden, omdat deze zich in een knoop punt van nodeTypeY bevindt dat is opgenomen in NodeTypeInclusionList. Als zowel NodeTypeInclusionList als ApplicationInclusionList null of leeg zijn, wordt er een ArgumentException gegenereerd.
    * **NodeTypeInclusionList**: een lijst met knooppunt typen die in chaos-fouten moeten worden meegenomen. Alle typen fouten (knoop punt opnieuw starten, code package opnieuw starten, replica verwijderen, replica opnieuw starten en secundaire verplaatsen) zijn ingeschakeld voor de knoop punten van deze knooppunt typen. Als een NodeType (bijvoorbeeld NodeTypeX) niet wordt weer gegeven in de NodeTypeInclusionList, worden er op knooppunt niveau fouten (zoals NodeRestart) nooit ingeschakeld voor de knoop punten van NodeTypeX, maar kan het code pakket en de replica fouten nog steeds worden ingeschakeld voor NodeTypeX als een toepassing in de ApplicationInclusionList zich op een knoop punt van NodeTypeX bevindt. Er kunnen Maxi maal 100 knooppunt type namen worden opgenomen in deze lijst, om dit aantal te verhogen, is een configuratie-upgrade vereist voor de configuratie van MaxNumberOfNodeTypesInChaosTargetFilter.
    * **ApplicationInclusionList**: een lijst met toepassings-uri's die in chaos-fouten moeten worden meegenomen. Alle replica's die deel uitmaken van services van deze toepassingen, zijn geschikter naar replica fouten (start replica, verwijder replica, Move Primary en Move secundair) door chaos. Chaos kan een code pakket alleen opnieuw starten als het code pakket alleen replica's van deze toepassingen host. Als een toepassing niet in deze lijst wordt weer gegeven, kan deze in sommige chaos-iteraties nog steeds worden gestopt als de toepassing wordt beëindigd op een knoop punt van een knooppunt type dat is opgenomen in NodeTypeInclusionList. Als applicationx echter is gekoppeld aan nodeTypeY via plaatsings beperkingen en applicationx niet aanwezig is in ApplicationInclusionList en nodeTypeY niet aanwezig is in NodeTypeInclusionList, wordt applicationx nooit gefoutd. Er kunnen Maxi maal 1000 toepassings namen in deze lijst worden opgenomen, om dit aantal te verhogen, is een configuratie-upgrade vereist voor de configuratie van MaxNumberOfApplicationsInChaosTargetFilter.

## <a name="how-to-run-chaos"></a>Chaos uitvoeren

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Fabric;

using System.Diagnostics;
using System.Fabric.Chaos.DataStructures;

class Program
{
    private class ChaosEventComparer : IEqualityComparer<ChaosEvent>
    {
        public bool Equals(ChaosEvent x, ChaosEvent y)
        {
            return x.TimeStampUtc.Equals(y.TimeStampUtc);
        }
        public int GetHashCode(ChaosEvent obj)
        {
            return obj.TimeStampUtc.GetHashCode();
        }
    }

    static void Main(string[] args)
    {
        var clusterConnectionString = "localhost:19000";
        using (var client = new FabricClient(clusterConnectionString))
        {
            var startTimeUtc = DateTime.UtcNow;

            // The maximum amount of time to wait for all cluster entities to become stable and healthy. 
            // Chaos executes in iterations and at the start of each iteration it validates the health of cluster
            // entities. 
            // During validation if a cluster entity is not stable and healthy within
            // MaxClusterStabilizationTimeoutInSeconds, Chaos generates a validation failed event.
            var maxClusterStabilizationTimeout = TimeSpan.FromSeconds(30.0);

            var timeToRun = TimeSpan.FromMinutes(60.0);

            // MaxConcurrentFaults is the maximum number of concurrent faults induced per iteration. 
            // Chaos executes in iterations and two consecutive iterations are separated by a validation phase.
            // The higher the concurrency, the more aggressive the injection of faults -- inducing more complex
            // series of states to uncover bugs.
            // The recommendation is to start with a value of 2 or 3 and to exercise caution while moving up.
            var maxConcurrentFaults = 3;

            // Describes a map, which is a collection of (string, string) type key-value pairs. The map can be
            // used to record information about the Chaos run. There cannot be more than 100 such pairs and
            // each string (key or value) can be at most 4095 characters long.
            // This map is set by the starter of the Chaos run to optionally store the context about the specific run.
            var startContext = new Dictionary<string, string>{{"ReasonForStart", "Testing"}};

            // Time-separation (in seconds) between two consecutive iterations of Chaos. The larger the value, the
            // lower the fault injection rate.
            var waitTimeBetweenIterations = TimeSpan.FromSeconds(10);

            // Wait time (in seconds) between consecutive faults within a single iteration.
            // The larger the value, the lower the overlapping between faults and the simpler the sequence of
            // state transitions that the cluster goes through. 
            // The recommendation is to start with a value between 1 and 5 and exercise caution while moving up.
            var waitTimeBetweenFaults = TimeSpan.Zero;

            // Passed-in cluster health policy is used to validate health of the cluster in between Chaos iterations. 
            var clusterHealthPolicy = new ClusterHealthPolicy
            {
                ConsiderWarningAsError = false,
                MaxPercentUnhealthyApplications = 100,
                MaxPercentUnhealthyNodes = 100
            };

            // All types of faults, restart node, restart code package, restart replica, move primary replica,
            // and move secondary replica will happen for nodes of type 'FrontEndType'
            var nodetypeInclusionList = new List<string> { "FrontEndType"};

            // In addition to the faults included by nodetypeInclusionList,
            // restart code package, restart replica, move primary replica, move secondary replica faults will
            // happen for 'fabric:/TestApp2' even if a replica or code package from 'fabric:/TestApp2' is residing
            // on a node which is not of type included in nodeypeInclusionList.
            var applicationInclusionList = new List<string> { "fabric:/TestApp2" };

            // List of cluster entities to target for Chaos faults.
            var chaosTargetFilter = new ChaosTargetFilter
            {
                NodeTypeInclusionList = nodetypeInclusionList,
                ApplicationInclusionList = applicationInclusionList
            };

            var parameters = new ChaosParameters(
                maxClusterStabilizationTimeout,
                maxConcurrentFaults,
                true, /* EnableMoveReplicaFault */
                timeToRun,
                startContext,
                waitTimeBetweenIterations,
                waitTimeBetweenFaults,
                clusterHealthPolicy) {ChaosTargetFilter = chaosTargetFilter};

            try
            {
                client.TestManager.StartChaosAsync(parameters).GetAwaiter().GetResult();
            }
            catch (FabricChaosAlreadyRunningException)
            {
                Console.WriteLine("An instance of Chaos is already running in the cluster.");
            }

            var filter = new ChaosReportFilter(startTimeUtc, DateTime.MaxValue);

            var eventSet = new HashSet<ChaosEvent>(new ChaosEventComparer());

            string continuationToken = null;

            while (true)
            {
                ChaosReport report;
                try
                {
                    report = string.IsNullOrEmpty(continuationToken)
                        ? client.TestManager.GetChaosReportAsync(filter).GetAwaiter().GetResult()
                        : client.TestManager.GetChaosReportAsync(continuationToken).GetAwaiter().GetResult();
                }
                catch (Exception e)
                {
                    if (e is FabricTransientException)
                    {
                        Console.WriteLine("A transient exception happened: '{0}'", e);
                    }
                    else if(e is TimeoutException)
                    {
                        Console.WriteLine("A timeout exception happened: '{0}'", e);
                    }
                    else
                    {
                        throw;
                    }

                    Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
                    continue;
                }

                continuationToken = report.ContinuationToken;

                foreach (var chaosEvent in report.History)
                {
                    if (eventSet.Add(chaosEvent))
                    {
                        Console.WriteLine(chaosEvent);
                    }
                }

                // When Chaos stops, a StoppedEvent is created.
                // If a StoppedEvent is found, exit the loop.
                var lastEvent = report.History.LastOrDefault();

                if (lastEvent is StoppedEvent)
                {
                    break;
                }

                Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
            }
        }
    }
}
```

```powershell
$clusterConnectionString = "localhost:19000"
$timeToRunMinute = 60

# The maximum amount of time to wait for all cluster entities to become stable and healthy.
# Chaos executes in iterations and at the start of each iteration it validates the health of cluster entities.
# During validation if a cluster entity is not stable and healthy within MaxClusterStabilizationTimeoutInSeconds,
# Chaos generates a validation failed event.
$maxClusterStabilizationTimeSecs = 30

# MaxConcurrentFaults is the maximum number of concurrent faults induced per iteration.
# Chaos executes in iterations and two consecutive iterations are separated by a validation phase.
# The higher the concurrency, the more aggressive the injection of faults -- inducing more complex series of
# states to uncover bugs.
# The recommendation is to start with a value of 2 or 3 and to exercise caution while moving up.
$maxConcurrentFaults = 3

# Time-separation (in seconds) between two consecutive iterations of Chaos. The larger the value, the lower the
# fault injection rate.
$waitTimeBetweenIterationsSec = 10

# Wait time (in seconds) between consecutive faults within a single iteration.
# The larger the value, the lower the overlapping between faults and the simpler the sequence of state
# transitions that the cluster goes through.
# The recommendation is to start with a value between 1 and 5 and exercise caution while moving up.
$waitTimeBetweenFaultsSec = 0

# Passed-in cluster health policy is used to validate health of the cluster in between Chaos iterations. 
$clusterHealthPolicy = new-object -TypeName System.Fabric.Health.ClusterHealthPolicy
$clusterHealthPolicy.MaxPercentUnhealthyNodes = 100
$clusterHealthPolicy.MaxPercentUnhealthyApplications = 100
$clusterHealthPolicy.ConsiderWarningAsError = $False

# Describes a map, which is a collection of (string, string) type key-value pairs. The map can be used to record
# information about the Chaos run.
# There cannot be more than 100 such pairs and each string (key or value) can be at most 4095 characters long.
# This map is set by the starter of the Chaos run to optionally store the context about the specific run.
$context = @{"ReasonForStart" = "Testing"}

#List of cluster entities to target for Chaos faults.
$chaosTargetFilter = new-object -TypeName System.Fabric.Chaos.DataStructures.ChaosTargetFilter
$chaosTargetFilter.NodeTypeInclusionList = new-object -TypeName "System.Collections.Generic.List[String]"

# All types of faults, restart node, restart code package, restart replica, move primary replica, and move
# secondary replica will happen for nodes of type 'FrontEndType'
$chaosTargetFilter.NodeTypeInclusionList.AddRange( [string[]]@("FrontEndType") )
$chaosTargetFilter.ApplicationInclusionList = new-object -TypeName "System.Collections.Generic.List[String]"

# In addition to the faults included by nodetypeInclusionList, 
# restart code package, restart replica, move primary replica, move secondary replica faults will happen for
# 'fabric:/TestApp2' even if a replica or code package from 'fabric:/TestApp2' is residing on a node which is
# not of type included in nodeypeInclusionList.
$chaosTargetFilter.ApplicationInclusionList.Add("fabric:/TestApp2")

Connect-ServiceFabricCluster $clusterConnectionString

$events = @{}
$now = [System.DateTime]::UtcNow

Start-ServiceFabricChaos -TimeToRunMinute $timeToRunMinute -MaxConcurrentFaults $maxConcurrentFaults -MaxClusterStabilizationTimeoutSec $maxClusterStabilizationTimeSecs -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ClusterHealthPolicy $clusterHealthPolicy -ChaosTargetFilter $chaosTargetFilter

while($true)
{
    $stopped = $false
    $report = Get-ServiceFabricChaosReport -StartTimeUtc $now -EndTimeUtc ([System.DateTime]::MaxValue)

    foreach ($e in $report.History) {

        if(-Not ($events.Contains($e.TimeStampUtc.Ticks)))
        {
            $events.Add($e.TimeStampUtc.Ticks, $e)
            if($e -is [System.Fabric.Chaos.DataStructures.ValidationFailedEvent])
            {
                Write-Host -BackgroundColor White -ForegroundColor Red $e
            }
            else
            {
                Write-Host $e
                # When Chaos stops, a StoppedEvent is created.
                # If a StoppedEvent is found, exit the loop.
                if($e -is [System.Fabric.Chaos.DataStructures.StoppedEvent])
                {
                    return
                }
            }
        }
    }

    Start-Sleep -Seconds 1
}
```

---
title: Cluster knooppunten starten en stoppen
description: Meer informatie over het gebruik van fout injectie om een Service Fabric-toepassing te testen door cluster knooppunten te starten en te stoppen.
author: LMWF
ms.topic: conceptual
ms.date: 6/12/2017
ms.author: lemai
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c31040ec13084f9e4b08bbc9a347e4ad44975bf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89021252"
---
# <a name="replacing-the-start-node-and-stop-node-apis-with-the-node-transition-api"></a>Het begin knooppunt vervangen en de knoop punt-Api's stoppen met de knooppunt overgangs-API

## <a name="what-do-the-stop-node-and-start-node-apis-do"></a>Wat gebeurt er met het knoop punt stoppen en de Api's voor het starten van knoop punten?

Met de stop knooppunt-API (beheerd: [StopNodeAsync ()][stopnode], Power shell: [Stop-ServiceFabricNode][stopnodeps]) wordt een service Fabric knoop punt gestopt.  Een Service Fabric knoop punt is proces, geen VM of machine: de VM of machine wordt nog steeds uitgevoerd.  Voor de rest van het document ' knoop punt ' betekent Service Fabric knoop punt.  Als een knoop punt wordt gestopt, wordt het omgezet in *een status waarbij* deze geen lid is van het cluster en kunnen er geen services worden gehost, waardoor er een *omlaag* knoop punt wordt gesimuleerd.  Dit is handig voor het invoegen van fouten in het systeem om uw toepassing te testen.  De start node-API (Managed: [StartNodeAsync ()][startnode], Power shell: [Start-ServiceFabricNode][startnodeps]]) keert de stop knooppunt-API om, waardoor het knoop punt weer normaal wordt.

## <a name="why-are-we-replacing-these"></a>Waarom worden deze vervangen?

Zoals eerder beschreven, is een *gestopt* service Fabric knoop punt een knoop punt waarvoor het doel is de stop knooppunt-API te gebruiken.  Een *omlaag* knoop punt is een knoop punt dat om een andere reden niet beschikbaar is (bijvoorbeeld als de virtuele machine is uitgeschakeld).  Met de knoop punt-API stoppen maakt het systeem geen informatie beschikbaar om onderscheid te *maken tussen gestopte* knoop punten en *dalende* knoop punten.

Bovendien zijn sommige fouten die door deze Api's worden geretourneerd, niet zo beschrijven als ze zouden kunnen zijn.  Als u bijvoorbeeld de stop knooppunt-API aanroept op een al *gestopt* knoop punt, wordt het fout bericht *InvalidAddress* geretourneerd.  Deze ervaring kan worden verbeterd.

De duur waarmee een knoop punt wordt gestopt voor is ' oneindig ' totdat de begin knooppunt-API wordt aangeroepen.  Dit kan problemen veroorzaken en is mogelijk gevoelig voor fouten.  We hebben bijvoorbeeld problemen gezien waarbij een gebruiker de knoop punt-API stoppen op een knoop punt heeft aangeroepen en deze vervolgens verg eten.  Later was het onduidelijk als het knoop punt is *ingedrukt* of *gestopt*.


## <a name="introducing-the-node-transition-apis"></a>Inleiding tot de knooppunt overgang Api's

Deze problemen zijn hierboven in een nieuwe set Api's opgelost.  De nieuwe knooppunt overgangs-API (beheerd: [StartNodeTransitionAsync ()][snt]) kan worden gebruikt om een service Fabric knoop punt over te zetten naar de status *stopped* , of om het over te zetten van een status *gestopt* naar een normale status.  Houd er rekening mee dat de ' Start ' in de naam van de API niet verwijst naar het starten van een knoop punt.  Het verwijst naar een asynchrone bewerking die door het systeem wordt uitgevoerd om het knoop punt over te zetten naar de status *gestopt* of gestart.

**Gebruik**

Als de knooppunt overgangs-API geen uitzonde ring genereert wanneer deze wordt aangeroepen, is de asynchrone bewerking door het systeem geaccepteerd en wordt deze uitgevoerd.  Een geslaagde aanroep houdt niet in dat de bewerking nog is voltooid.  Als u informatie wilt ophalen over de huidige status van de bewerking, roept u de voortgang van de knooppunt overgangs-API (beheerd: [GetNodeTransitionProgressAsync ()][gntp]) aan met de GUID die wordt gebruikt bij het aanroepen van de knooppunt overgangs-API voor deze bewerking.  De API voor voortgangs overgang van knoop punten retourneert een NodeTransitionProgress-object.  De eigenschap State van dit object geeft de huidige status van de bewerking aan.  Als de status wordt uitgevoerd, wordt de bewerking uitgevoerd.  Als de bewerking is voltooid, is deze zonder fouten voltooid.  Als er een fout optreedt, is er een probleem opgetreden bij het uitvoeren van de bewerking.  De eigenschap Exception van het resultaat wordt aangegeven wat het probleem is.  Zie https://docs.microsoft.com/dotnet/api/system.fabric.testcommandprogressstate voor meer informatie over de eigenschap State en de sectie ' voor beeld gebruik ' hieronder voor code voorbeelden.


**Onderscheid tussen een gestopt knoop punt en een omlaag knoop punt** Als een knoop punt wordt *gestopt* met de API voor knooppunt overgang, wordt met de uitvoer van een knooppunt query (beheerd: [GetNodeListAsync ()][nodequery], Power shell: [Get-ServiceFabricNode][nodequeryps]) aangegeven dat dit knoop punt een *IsStopped* eigenschaps waarde True heeft.  Houd er rekening mee dat dit verschilt van de waarde van de eigenschap *NodeStatus* , die wordt *uitgesteld*.  Als de eigenschap *NodeStatus* de waarde *down* heeft, maar *IsStopped* is ingesteld op False, is het knoop punt niet gestopt met de API van het knoop punt. Dit is om een andere reden niet meer *beschikbaar* .  Als de eigenschap *IsStopped* is ingesteld op True en de eigenschap *NodeStatus* is *uitgeschakeld*, is deze gestopt met de API voor knooppunt overgang.

Als u een *gestopt* knoop punt start met behulp van de knooppunt overgangs-API, wordt dit opnieuw gebruikt als een normaal lid van het cluster.  De uitvoer van de query-API van het knoop punt geeft *IsStopped* weer als onwaar en *NodeStatus* als iets dat niet uitvalt (bijvoorbeeld omhoog).


**Beperkte duur** Wanneer u de knooppunt overgangs-API gebruikt om een knoop punt te stoppen, geeft een van de vereiste para meters, *stopNodeDurationInSeconds*, de hoeveelheid tijd in seconden aan dat het knoop punt is *gestopt*.  Deze waarde moet binnen het toegestane bereik vallen, met een minimum van 600 en een maximum van 14400.  Nadat dit tijdstip is verlopen, wordt het knoop punt automatisch opnieuw opgestart.  Raadpleeg voorbeeld 1 hieronder voor een voor beeld van het gebruik.

> [!WARNING]
> Vermijd het combi neren van knoop punt overgang Api's en het stop knooppunt en start knoop punt-Api's.  De aanbeveling is alleen de knooppunt overgangs-API te gebruiken.  > als er al een knoop punt is gestopt met de knoop punt-API stoppen, moet dit eerst worden gestart met de knoop punt-API starten voordat u de > knooppunt overgang Api's gebruikt.

> [!WARNING]
> Meerdere knooppunt overgang-Api's kunnen niet parallel worden gemaakt op hetzelfde knoop punt.  In een dergelijke situatie wordt de knooppunt overgangs-API > een FabricException gegenereerd met de waarde van de eigenschap error code van NodeTransitionInProgress.  Zodra een knooppunt overgang op een specifiek knoop punt is > gestart, moet u wachten tot de bewerking een Terminal status heeft bereikt (voltooid, mislukt of ForceCancelled) voordat u een > nieuwe overgang op hetzelfde knoop punt start.  Overgangs aanroepen op parallelle knoop punten op verschillende knoop punt zijn toegestaan.


#### <a name="sample-usage"></a>Voorbeeldgebruik


Voor **Beeld 1** : in het volgende voor beeld wordt de knooppunt overgangs-API gebruikt om een knoop punt te stoppen.

```csharp
        // Helper function to get information about a node
        static Node GetNodeInfo(FabricClient fc, string node)
        {
            NodeList n = null;
            while (n == null)
            {
                n = fc.QueryManager.GetNodeListAsync(node).GetAwaiter().GetResult();
                Task.Delay(TimeSpan.FromSeconds(1)).GetAwaiter();
            };

            return n.FirstOrDefault();
        }

        static async Task WaitForStateAsync(FabricClient fc, Guid operationId, TestCommandProgressState targetState)
        {
            NodeTransitionProgress progress = null;

            do
            {
                bool exceptionObserved = false;
                try
                {
                    progress = await fc.TestManager.GetNodeTransitionProgressAsync(operationId, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                    exceptionObserved = true;
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                    exceptionObserved = true;
                }

                if (!exceptionObserved)
                {
                    Console.WriteLine("Current state of operation '{0}': {1}", operationId, progress.State);

                    if (progress.State == TestCommandProgressState.Faulted)
                    {
                        // Inspect the progress object's Result.Exception.HResult to get the error code.
                        Console.WriteLine("'{0}' failed with: {1}, HResult: {2}", operationId, progress.Result.Exception, progress.Result.Exception.HResult);

                        // ...additional logic as required
                    }

                    if (progress.State == targetState)
                    {
                        Console.WriteLine("Target state '{0}' has been reached", targetState);
                        break;
                    }
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }
            while (true);
        }

        static async Task StopNodeAsync(FabricClient fc, string nodeName, int durationInSeconds)
        {
            // Uses the GetNodeListAsync() API to get information about the target node
            Node n = GetNodeInfo(fc, nodeName);

            // Create a Guid
            Guid guid = Guid.NewGuid();

            // Create a NodeStopDescription object, which will be used as a parameter into StartNodeTransition
            NodeStopDescription description = new NodeStopDescription(guid, n.NodeName, n.NodeInstanceId, durationInSeconds);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStopDescription from above, which will stop the target node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    // This is retryable
                }
                catch (FabricTransientException fte)
                {
                    // This is retryable
                }

                // Backoff
                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until the desired state is reached.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Completed).ConfigureAwait(false);
        }
```

Voor **Beeld 2** : met het volgende voor beeld wordt een *gestopt* knoop punt gestart.  Er worden enkele hulp methoden van het eerste voor beeld gebruikt.

```csharp
        static async Task StartNodeAsync(FabricClient fc, string nodeName)
        {
            // Uses the GetNodeListAsync() API to get information about the target node
            Node n = GetNodeInfo(fc, nodeName);

            Guid guid = Guid.NewGuid();
            BigInteger nodeInstanceId = n.NodeInstanceId;

            // Create a NodeStartDescription object, which will be used as a parameter into StartNodeTransition
            NodeStartDescription description = new NodeStartDescription(guid, n.NodeName, nodeInstanceId);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStartDescription from above, which will start the target stopped node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);

            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until the desired state is reached.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Completed).ConfigureAwait(false);
        }
```

Voor **Beeld 3** : in het volgende voor beeld wordt het onjuiste gebruik weer gegeven.  Dit gebruik is onjuist omdat de *stopDurationInSeconds* die het biedt, groter is dan het toegestane bereik.  Omdat StartNodeTransitionAsync () mislukt met een onherstelbare fout, wordt de bewerking niet geaccepteerd en moet de voortgangs-API niet worden aangeroepen.  In dit voor beeld worden enkele hulp methoden van het eerste voor beeld gebruikt.

```csharp
        static async Task StopNodeWithOutOfRangeDurationAsync(FabricClient fc, string nodeName)
        {
            Node n = GetNodeInfo(fc, nodeName);

            Guid guid = Guid.NewGuid();

            // Use an out of range value for stopDurationInSeconds to demonstrate error
            NodeStopDescription description = new NodeStopDescription(guid, n.NodeName, n.NodeInstanceId, 99999);

            try
            {
                await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
            }

            catch (FabricException e)
            {
                Console.WriteLine("Caught {0}", e);
                Console.WriteLine("ErrorCode {0}", e.ErrorCode);
                // Output:
                // Caught System.Fabric.FabricException: System.Runtime.InteropServices.COMException (-2147017629)
                // StopDurationInSeconds is out of range ---> System.Runtime.InteropServices.COMException: Exception from HRESULT: 0x80071C63
                // << Parts of exception omitted>>
                //
                // ErrorCode InvalidDuration
            }
        }
```

Voor **beeld 4** : in het volgende voor beeld ziet u de fout gegevens die worden geretourneerd door de API voor voortgangs overgang van het knoop punt wanneer de bewerking die wordt geïnitieerd door de knooppunt overgangs-API, wordt geaccepteerd, maar later mislukt tijdens het uitvoeren van.  In het geval mislukt dit omdat de knooppunt overgangs-API probeert om een knoop punt te starten dat niet bestaat.  In dit voor beeld worden enkele hulp methoden van het eerste voor beeld gebruikt.

```csharp
        static async Task StartNodeWithNonexistentNodeAsync(FabricClient fc)
        {
            Guid guid = Guid.NewGuid();
            BigInteger nodeInstanceId = 12345;

            // Intentionally use a nonexistent node
            NodeStartDescription description = new NodeStartDescription(guid, "NonexistentNode", nodeInstanceId);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStartDescription from above, which will start the target stopped node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);

            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until the desired state is reached.  In this case, it will end up in the Faulted state since the node does not exist.
            // When StartNodeTransitionProgressAsync()'s returned progress object has a State if Faulted, inspect the progress object's Result.Exception.HResult to get the error code.
            // In this case, it will be NodeNotFound.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Faulted).ConfigureAwait(false);
        }
```

[stopnode]: /dotnet/api/system.fabric.fabricclient.faultmanagementclient
[stopnodeps]: /previous-versions/azure/mt125982(v=azure.100)
[startnode]: /dotnet/api/system.fabric.fabricclient.faultmanagementclient
[startnodeps]: /previous-versions/azure/mt163520(v=azure.100)
[nodequery]: /dotnet/api/system.fabric.fabricclient.queryclient
[nodequeryps]: /powershell/module/servicefabric/get-servicefabricnode
[snt]: /dotnet/api/system.fabric.fabricclient.testmanagementclient
[gntp]: /dotnet/api/system.fabric.fabricclient.testmanagementclient

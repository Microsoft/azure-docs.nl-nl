---
title: Service Fabric programmatisch schalen van Azure
description: Een Azure Service Fabric cluster in-of uitschalen in een programma, volgens aangepaste triggers
author: mjrousos
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: mikerou
ms.custom: devx-track-csharp
ms.openlocfilehash: 9f39bd874c1f5a1be42ca1c88e6ea2fe8df22f87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94648205"
---
# <a name="scale-a-service-fabric-cluster-programmatically"></a>Een Service Fabric cluster via een programma schalen 

Service Fabric clusters die worden uitgevoerd in azure, zijn gebouwd op schaal sets van virtuele machines.  [Cluster schaalt](./service-fabric-cluster-scale-in-out.md) een beschrijving van hoe service Fabric clusters hand matig of met regels voor automatisch schalen kunnen worden geschaald. In dit artikel wordt beschreven hoe u referenties beheert en een cluster in-of uitschaalt met behulp van de Fluent Azure Compute SDK, een meer geavanceerd scenario. Lees voor een overzicht de [programmatische methoden voor het coördineren van Azure-schaal bewerkingen](service-fabric-cluster-scaling.md#programmatic-scaling). 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="manage-credentials"></a>Referenties beheren
Een uitdaging voor het schrijven van een service voor het afhandelen van schalen is dat de service toegang moet hebben tot bronnen van virtuele-machine schaal sets zonder interactieve aanmelding. Toegang tot het Service Fabric cluster is eenvoudig als de schaal service een eigen Service Fabric toepassing wijzigt, maar er zijn referenties nodig om toegang te krijgen tot de schaalset. Als u zich wilt aanmelden, kunt u een [Service-Principal](/cli/azure/create-an-azure-service-principal-azure-cli) gebruiken die is gemaakt met de [Azure cli](https://github.com/azure/azure-cli).

U kunt een service-principal maken met de volgende stappen:

1. Meld u aan bij de Azure CLI ( `az login` ) als gebruiker met toegang tot de schaalset voor virtuele machines
2. De service-principal maken met `az ad sp create-for-rbac`
    1. Noteer de appId (client-ID elders genoemd), de naam, het wacht woord en de Tenant voor later gebruik.
    2. U hebt ook uw abonnements-ID nodig, die kan worden weer gegeven met `az account list`

De Fluent Compute-bibliotheek kan als volgt worden aangemeld met deze referenties (Houd er rekening mee dat de belangrijkste Fluent Azure-typen zoals `IAzure` zijn in het [micro soft. Azure. Management. Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Fluent/) -pakket):

```csharp
var credentials = new AzureCredentials(new ServicePrincipalLoginInformation {
                ClientId = AzureClientId,
                ClientSecret = 
                AzureClientKey }, AzureTenantId, AzureEnvironment.AzureGlobalCloud);
IAzure AzureClient = Azure.Authenticate(credentials).WithSubscription(AzureSubscriptionId);

if (AzureClient?.SubscriptionId == AzureSubscriptionId)
{
    ServiceEventSource.Current.ServiceMessage(Context, "Successfully logged into Azure");
}
else
{
    ServiceEventSource.Current.ServiceMessage(Context, "ERROR: Failed to login to Azure");
}
```

Na het aanmelden kan het aantal exemplaren van de schaalset worden opgevraagd via `AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId).Capacity` .

## <a name="scaling-out"></a>Uitschalen
Met behulp van de Fluent Azure Compute SDK kunnen instanties worden toegevoegd aan de schaalset voor virtuele machines met slechts enkele aanroepen:

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
``` 

U kunt ook de grootte van de schaalset voor virtuele machines beheren met Power shell-cmdlets. [`Get-AzVmss`](/powershell/module/az.compute/get-azvmss) kan het object voor de virtuele-machine schaalset ophalen. De huidige capaciteit is beschikbaar via de `.sku.capacity` eigenschap. Nadat u de capaciteit hebt gewijzigd in de gewenste waarde, kan de schaalset voor virtuele machines in Azure worden bijgewerkt met de [`Update-AzVmss`](/powershell/module/az.compute/update-azvmss) opdracht.

Net als bij het hand matig toevoegen van een knoop punt, moet u een instantie van een schaalset toevoegen die nodig is om een nieuw Service Fabric knoop punt te starten omdat de sjabloon voor de schaalset extensies bevat om automatisch nieuwe exemplaren toe te voegen aan het Service Fabric cluster. 

## <a name="scaling-in"></a>Schalen in

Schalen in is vergelijkbaar met uitschalen. De werkelijke wijzigingen in de schaalset van virtuele machines zijn nagenoeg hetzelfde. Maar zoals eerder is besproken, reinigt Service Fabric alleen verwijderde knoop punten automatisch met een duurzaamheid van goud of zilver. In het geval van de schaal van Bronze-duurzaamheid is het echter nodig om te communiceren met het Service Fabric-cluster om het knoop punt dat moet worden verwijderd af te sluiten en de status ervan te verwijderen.

Voor het voorbereiden van het knoop punt voor afsluiten moet u het knoop punt vinden dat moet worden verwijderd (de laatst toegevoegde instantie van de virtuele-machine schaalset) en deactiveren. Exemplaren van virtuele-machine schaal sets worden genummerd in de volg orde waarin ze worden toegevoegd, zodat er nieuwe knoop punten kunnen worden gevonden door het achtervoegsel van het nummer in de namen van de knoop punten te vergelijken (die overeenkomen met de onderliggende exemplaar namen van de virtuele-machine schaal sets). 

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

Nadat het knoop punt dat moet worden verwijderd, is gevonden, kan het worden gedeactiveerd en worden verwijderd met hetzelfde `FabricClient` exemplaar en het `IAzure` vorige exemplaar.

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shutdown
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement VMSS capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

Net als bij uitschalen kunt u Power shell-cmdlets voor het wijzigen van de capaciteit van de schaalset voor virtuele machines hier ook gebruiken als u de voor keur geeft aan een script benadering. Zodra het exemplaar van de virtuele machine is verwijderd, kan Service Fabric knooppunt status worden verwijderd.

```csharp
await client.ClusterManager.RemoveNodeStateAsync(mostRecentLiveNode.NodeName);
```

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende concepten en nuttige Api's om aan de slag te gaan met het implementeren van uw eigen logica voor automatisch schalen:

- [Hand matig schalen of met regels voor automatisch schalen](./service-fabric-cluster-scale-in-out.md)
- [Azure-beheer bibliotheken voor .net](https://github.com/Azure/azure-libraries-for-net) (geschikt voor interactie met de onderliggende virtuele-machine schaal sets van een service Fabric cluster)
- [System. Fabric. FabricClient](/dotnet/api/system.fabric.fabricclient) (handig voor interactie met een service Fabric cluster en de bijbehorende knoop punten)

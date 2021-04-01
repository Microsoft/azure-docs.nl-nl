---
title: WCF-communicatie stack Reliable Services
description: De ingebouwde WCF-communicatie stack in Service Fabric biedt WCF-communicatie van de client-service voor Reliable Services.
author: BharatNarasimman
ms.topic: conceptual
ms.date: 06/07/2017
ms.author: bharatn
ms.custom: devx-track-csharp
ms.openlocfilehash: c5b5d413eee2528e2d5c7d04d06f1607949beaae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89012701"
---
# <a name="wcf-based-communication-stack-for-reliable-services"></a>Op WCF gebaseerde communicatie stack voor Reliable Services
Met het Reliable Services Framework kunnen service ontwerpers de communicatie stack kiezen die ze willen gebruiken voor hun service. Ze kunnen de communicatie stack van hun keuze aansluiten via de **ICommunicationListener** die wordt geretourneerd door de [CreateServiceReplicaListeners-of CreateServiceInstanceListeners](service-fabric-reliable-services-communication.md) -methoden. Het Framework bevat een implementatie van de communicatie stack op basis van de Windows Communication Foundation (WCF) voor service auteurs die op WCF gebaseerde communicatie willen gebruiken.

## <a name="wcf-communication-listener"></a>WCF-communicatie-listener
De WCF-specifieke implementatie van **ICommunicationListener** wordt gegeven door de klasse **micro soft. ServiceFabric. Services. Communication. WCF. runtime. WcfCommunicationListener** .

Lest Stel dat we een service contract van het type hebben `ICalculator`

```csharp
[ServiceContract]
public interface ICalculator
{
    [OperationContract]
    Task<int> Add(int value1, int value2);
}
```

We kunnen op de volgende manier een WCF-communicatie-listener maken in de service.

```csharp

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[] { new ServiceReplicaListener((context) =>
        new WcfCommunicationListener<ICalculator>(
            wcfServiceObject:this,
            serviceContext:context,
            //
            // The name of the endpoint configured in the ServiceManifest under the Endpoints section
            // that identifies the endpoint that the WCF ServiceHost should listen on.
            //
            endpointResourceName: "WcfServiceEndpoint",

            //
            // Populate the binding information that you want the service to use.
            //
            listenerBinding: WcfUtility.CreateTcpListenerBinding()
        )
    )};
}

```

## <a name="writing-clients-for-the-wcf-communication-stack"></a>Clients schrijven voor de WCF-communicatie stack
Voor het schrijven van clients om met services te communiceren met behulp van WCF, biedt het Framework **WcfClientCommunicationFactory**. Dit is de WCF-specifieke implementatie van [ClientCommunicationFactoryBase](service-fabric-reliable-services-communication.md).

```csharp

public WcfCommunicationClientFactory(
    Binding clientBinding = null,
    IEnumerable<IExceptionHandler> exceptionHandlers = null,
    IServicePartitionResolver servicePartitionResolver = null,
    string traceId = null,
    object callback = null);
```

Het WCF-communicatie kanaal kan worden geopend vanuit de **WcfCommunicationClient** die door de **WcfCommunicationClientFactory** zijn gemaakt.

```csharp

public class WcfCommunicationClient : ServicePartitionClient<WcfCommunicationClient<ICalculator>>
   {
       public WcfCommunicationClient(ICommunicationClientFactory<WcfCommunicationClient<ICalculator>> communicationClientFactory, Uri serviceUri, ServicePartitionKey partitionKey = null, TargetReplicaSelector targetReplicaSelector = TargetReplicaSelector.Default, string listenerName = null, OperationRetrySettings retrySettings = null)
           : base(communicationClientFactory, serviceUri, partitionKey, targetReplicaSelector, listenerName, retrySettings)
       {
       }
   }

```

Client code kan de **WcfCommunicationClientFactory** gebruiken samen met de **WcfCommunicationClient** die **ServicePartitionClient** implementeert om het service-eind punt te bepalen en te communiceren met de service.

```csharp
// Create binding
Binding binding = WcfUtility.CreateTcpClientBinding();
// Create a partition resolver
IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();
// create a  WcfCommunicationClientFactory object.
var wcfClientFactory = new WcfCommunicationClientFactory<ICalculator>
    (clientBinding: binding, servicePartitionResolver: partitionResolver);

//
// Create a client for communicating with the ICalculator service that has been created with the
// Singleton partition scheme.
//
var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
                wcfClientFactory,
                ServiceUri,
                ServicePartitionKey.Singleton);

//
// Call the service to perform the operation.
//
var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
                client => client.Channel.Add(2, 3)).Result;

```
> [!NOTE]
> In de standaard ServicePartitionResolver wordt ervan uitgegaan dat de client in hetzelfde cluster als de service wordt uitgevoerd. Als dat niet het geval is, maakt u een ServicePartitionResolver-object en geeft u de eind punten voor de cluster verbinding door.
> 
> 

## <a name="next-steps"></a>Volgende stappen
* [Externe procedure aanroep met Reliable Services externe communicatie](service-fabric-reliable-services-communication-remoting.md)
* [Web-API met OWIN in Reliable Services](./service-fabric-reliable-services-communication-aspnetcore.md)
* [Communicatie beveiligen voor Reliable Services](service-fabric-reliable-services-secure-communication-wcf.md)

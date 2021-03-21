---
title: Externe toegang tot service met behulp van C# in Service Fabric
description: Met Service Fabric externe toegang kunnen clients en services communiceren met C#-Services met behulp van een externe procedure aanroep.
ms.topic: conceptual
ms.date: 09/20/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: a0486a27d76c978a65c4a3cfd81df52a12e4ea1d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791574"
---
# <a name="service-remoting-in-c-with-reliable-services"></a>Externe service in C# met Reliable Services

> [!div class="op_single_selector"]
> * [C# op Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java op Linux](service-fabric-reliable-services-communication-remoting-java.md)
>
>

Voor services die niet zijn gekoppeld aan een bepaald communicatie protocol of stack, zoals een web-API, Windows Communication Foundation of anderen, biedt het Reliable Services Framework een extern mechanisme om snel en eenvoudig externe procedure aanroepen voor services in te stellen. In dit artikel wordt beschreven hoe u externe procedure aanroepen instelt voor services die zijn geschreven met C#.

## <a name="set-up-remoting-on-a-service"></a>Externe toegang instellen voor een service

U kunt in twee eenvoudige stappen externe toegang instellen voor een service:

1. Maak een interface voor uw service die u wilt implementeren. Deze interface definieert de methoden die beschikbaar zijn voor een externe procedure aanroep voor uw service. De methoden moeten asynchrone methoden van het taak resultaat hebben. De interface moet `Microsoft.ServiceFabric.Services.Remoting.IService` worden geïmplementeerd om aan te geven dat de service een interface voor externe toegang heeft.
2. Gebruik een externe listener in uw service. Een externe listener is een `ICommunicationListener` implementatie die mogelijkheden biedt voor externe communicatie. De `Microsoft.ServiceFabric.Services.Remoting.Runtime` naam ruimte bevat de uitbreidings methode `CreateServiceRemotingInstanceListeners` voor stateless en stateful services die kunnen worden gebruikt om een externe listener te maken met behulp van het standaard externe transport protocol.

>[!NOTE]
>De `Remoting` naam ruimte is beschikbaar als een afzonderlijk NuGet-pakket met de naam `Microsoft.ServiceFabric.Services.Remoting` .

De volgende stateless service biedt bijvoorbeeld een enkele methode om Hallo wereld te verkrijgen via een externe procedure aanroep.

```csharp
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Remoting;
using Microsoft.ServiceFabric.Services.Remoting.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

public interface IMyService : IService
{
    Task<string> HelloWorldAsync();
}

class MyService : StatelessService, IMyService
{
    public MyService(StatelessServiceContext context)
        : base (context)
    {
    }

    public Task<string> HelloWorldAsync()
    {
        return Task.FromResult("Hello!");
    }

    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
     return this.CreateServiceRemotingInstanceListeners();
    }
}
```

> [!NOTE]
> De argumenten en retour typen in de service-interface kunnen eenvoudige, complexe of aangepaste typen zijn, maar ze moeten kunnen worden geserialiseerd met de .NET [DataContractSerializer](/dotnet/framework/wcf/feature-details/types-supported-by-the-data-contract-serializer).
>
>

## <a name="call-remote-service-methods"></a>Externe service methoden aanroepen

Het aanroepen van methoden voor een service met behulp van de externe stack wordt uitgevoerd door middel van een lokale proxy voor de service via de- `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` klasse. Met de- `ServiceProxy` methode wordt een lokale proxy gemaakt met behulp van de interface die door de service wordt geïmplementeerd. Met die proxy kunt u op afstand methoden op de interface aanroepen.

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

Het externe Framework geeft uitzonde ringen door de service door aan de client. Als gevolg hiervan wordt `ServiceProxy` de client verantwoordelijk voor het verwerken van de uitzonde ringen die door de service worden veroorzaakt.

## <a name="service-proxy-lifetime"></a>Levens duur van Service proxy

Het maken van een Service proxy is een licht gewicht bewerking, zodat u zoveel mogelijk kunt maken. Service proxy-exemplaren kunnen opnieuw worden gebruikt zolang ze nodig zijn. Als een externe procedure aanroep een uitzonde ring genereert, kunt u nog steeds hetzelfde proxy-exemplaar gebruiken. Elke service proxy bevat een communicatie client die wordt gebruikt voor het verzenden van berichten via de kabel. Tijdens het aanroepen van externe aanroepen worden interne controles uitgevoerd om te bepalen of de communicatie client geldig is. Op basis van de resultaten van die controles wordt de communicatie client, indien nodig, opnieuw gemaakt. Als er een uitzonde ring optreedt, hoeft u dus niet opnieuw te maken `ServiceProxy` .

### <a name="service-proxy-factory-lifetime"></a>Levens duur van Service proxy-Factory

[ServiceProxyFactory](/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) is een Factory waarmee proxy-instanties voor verschillende externe interfaces worden gemaakt. Als u de API gebruikt `ServiceProxyFactory.CreateServiceProxy` om een proxy te maken, maakt het Framework een singleton-Service proxy.
Het is handig om een hand matig te maken wanneer u [IServiceRemotingClientFactory](/dotnet/api/microsoft.servicefabric.services.remoting.v1.client.iserviceremotingclientfactory) -eigenschappen wilt overschrijven.
Het maken van de fabriek is een dure bewerking. Een Service proxy-Factory houdt een interne cache van de communicatie-client bij.
Een best practice is de Service proxy-Factory zo lang mogelijk in de cache op te slaan.

## <a name="remoting-exception-handling"></a>Afhandeling van externe uitzonde ringen

Alle externe uitzonde ringen die worden veroorzaakt door de API van de service, worden teruggestuurd naar de client als AggregateException. Externe uitzonde ringen moeten kunnen worden geserialiseerd door data contract. Als dat niet het geval is, genereert de proxy-API [ServiceException](/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) met de serialisatiefout.

De Service proxy verwerkt alle failover-uitzonde ringen voor de service partitie waarvoor deze is gemaakt. De eind punten worden opnieuw omgezet als er failover-uitzonde ringen zijn (geen tijdelijke uitzonde ringen) en de aanroep opnieuw probeert met het juiste eind punt. Het aantal nieuwe pogingen voor failover-uitzonde ringen is oneindig.
Als er tijdelijke uitzonde ringen optreden, probeert de proxy de aanroep opnieuw uit te voeren.

De standaard parameters voor opnieuw proberen worden opgegeven door [OperationRetrySettings](/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings).

Een gebruiker kan deze waarden configureren door het object OperationRetrySettings door te geven aan de ServiceProxyFactory-constructor.

## <a name="use-the-remoting-v2-stack"></a>De externe v2-stack gebruiken

Vanaf versie 2,8 van het NuGet Remoting-pakket hebt u de mogelijkheid om de externe v2-stack te gebruiken. De externe v2-stack werkt beter. Het biedt ook functies als aangepaste serialisatie en meer pluggable Api's.
De sjabloon code blijft de externe v1-stack gebruiken.
Remoting v2 is niet compatibel met V1 (de vorige externe stack). Volg de instructies in het artikel  [upgrade van v1 naar v2](#upgrade-from-remoting-v1-to-remoting-v2) om te voor komen dat er gevolgen zijn voor de beschik baarheid van de service.

De volgende benaderingen zijn beschikbaar om de v2-stack in te scha kelen.

### <a name="use-an-assembly-attribute-to-use-the-v2-stack"></a>Een assembly-kenmerk gebruiken om de v2-stack te gebruiken

Met deze stappen wijzigt u de sjabloon code om de v2-stack te gebruiken met behulp van een assembly-kenmerk.

1. Wijzig de eindpunt resource van `"ServiceEndpoint"` in `"ServiceEndpointV2"` in het service manifest.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
   </Resources>
   ```

2. Gebruik de `Microsoft.ServiceFabric.Services.Remoting.Runtime.CreateServiceRemotingInstanceListeners`  uitbreidings methode voor het maken van externe listeners (gelijk voor v1 en v2).

   ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
   ```

3. Markeer de assembly die de externe interfaces met een `FabricTransportServiceRemotingProvider` kenmerk bevat.

   ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
   ```

Er zijn geen code wijzigingen vereist in het client project.
Bouw de client-assembly met de interface-assembly om ervoor te zorgen dat het assembly kenmerk dat eerder wordt weer gegeven, wordt gebruikt.

### <a name="use-explicit-v2-classes-to-use-the-v2-stack"></a>Expliciete v2-klassen gebruiken om de v2-stack te gebruiken

Als alternatief voor het gebruik van een assembly-kenmerk kan de v2-stack ook worden ingeschakeld met behulp van expliciete v2-klassen.

Met deze stappen wijzigt u de sjabloon code om de v2-stack te gebruiken met behulp van expliciete v2-klassen.

1. Wijzig de eindpunt resource van `"ServiceEndpoint"` in `"ServiceEndpointV2"` in het service manifest.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
   </Resources>
   ```

2. Gebruik [FabricTransportServiceRemotingListener](/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotinglistener) uit de `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Runtime` naam ruimte.

   ```csharp
   protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[]
        {
            new ServiceInstanceListener((c) =>
            {
                return new FabricTransportServiceRemotingListener(c, this);

            })
        };
    }
   ```

3. Gebruik [FabricTransportServiceRemotingClientFactory](/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory) uit de `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Client` naam ruimte om clients te maken.

   ```csharp
   var proxyFactory = new ServiceProxyFactory((c) =>
          {
              return new FabricTransportServiceRemotingClientFactory();
          });
   ```

## <a name="upgrade-from-remoting-v1-to-remoting-v2"></a>Upgrade van externe v1 naar externe versie v2

Als u een upgrade wilt uitvoeren van v1 naar v2, zijn upgrades van twee stappen vereist. Volg de stappen in deze reeks.

1. Voer met dit kenmerk een upgrade uit van de V1-service naar de v2-service.
Met deze wijziging wordt gecontroleerd of de service naar de v1-en v2-listener luistert.

    a. Voeg een eindpunt resource met de naam ' ServiceEndpointV2 ' toe aan het service manifest.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2" />  
        </Endpoints>
      </Resources>
      ```

    b. Gebruik de volgende extensie methode voor het maken van een externe listener.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c. Voeg een assembly-kenmerk toe op externe interfaces voor het gebruik van de v1-en v2-listener en de v2-client.
    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2|RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2)]

      ```
2. Upgrade de V1-client naar een v2-client met behulp van het kenmerk v2-client.
Met deze stap zorgt u ervoor dat de-client gebruikmaakt van de v2-stack.
Het client project/de service hoeft niet te worden gewijzigd. Het bouwen van client projecten met een bijgewerkte interface-assembly is voldoende.

3. Deze stap is optioneel. Gebruik het kenmerk v2-listener en werk vervolgens de v2-service bij.
Met deze stap zorgt u ervoor dat de service alleen luistert op de v2-listener.

    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
    ```


## <a name="use-the-remoting-v2-interface-compatible-stack"></a>De externe v2-stack (interface compatibel) gebruiken

 De externe v2 (interface compatibel, bekend als V2_1) stack beschikt over alle functies van de v2-externe stack. De Interface stack is compatibel met de externe v1-stack, maar is niet achterwaarts compatibel met v2 en v1. Als u wilt upgraden van v1 naar V2_1 zonder dat dit van invloed is op de beschik baarheid van de service, volgt u de stappen in het artikel upgrade van v1 naar v2 (interface compatibel).


### <a name="use-an-assembly-attribute-to-use-the-remoting-v2-interface-compatible-stack"></a>Gebruik een assembly-kenmerk om de externe v2-stack (interface compatibel) te gebruiken

Volg deze stappen om een V2_1 stack te wijzigen.

1. Voeg een eindpunt resource met de naam ' ServiceEndpointV2_1 ' toe aan het service manifest.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
   </Resources>
   ```

2. Gebruik de externe uitbreidings methode voor het maken van een externe listener.

   ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
   ```

3. Voeg een [Assembly-kenmerk](/dotnet/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportserviceremotingproviderattribute) toe op externe interfaces.

   ```csharp
    [assembly:  FabricTransportServiceRemotingProvider(RemotingListenerVersion=  RemotingListenerVersion.V2_1, RemotingClientVersion= RemotingClientVersion.V2_1)]

   ```

Er zijn geen wijzigingen vereist in het client project.
Bouw de client-assembly met de interface-assembly om er zeker van te zijn dat het vorige assembly kenmerk wordt gebruikt.

### <a name="use-explicit-remoting-classes-to-create-a-listenerclient-factory-for-the-v2-interface-compatible-version"></a>Gebruik expliciete externe klassen om een listener/client-Factory te maken voor de v2-versie (interface compatibel)

Volg deze stappen:

1. Voeg een eindpunt resource met de naam ' ServiceEndpointV2_1 ' toe aan het service manifest.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
   </Resources>
   ```

2. Gebruik de [externe v2-listener](/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotinglistener). De standaard naam van de service-eindpunt resource die wordt gebruikt, is "ServiceEndpointV2_1". Deze moet worden gedefinieerd in het service manifest.

   ```csharp
   protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[]
        {
            new ServiceInstanceListener((c) =>
            {
                var settings = new FabricTransportRemotingListenerSettings();
                settings.UseWrappedMessage = true;
                return new FabricTransportServiceRemotingListener(c, this,settings);

            })
        };
    }
   ```

3. De v2- [client-Factory](/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory)gebruiken.
   ```csharp
   var proxyFactory = new ServiceProxyFactory((c) =>
          {
            var settings = new FabricTransportRemotingSettings();
            settings.UseWrappedMessage = true;
            return new FabricTransportServiceRemotingClientFactory(settings);
          });
   ```

## <a name="upgrade-from-remoting-v1-to-remoting-v2-interface-compatible"></a>Upgrade van remoting v1 naar Remoting v2 (interface compatibel)

Als u een upgrade wilt uitvoeren van v1 naar v2 (interface compatibel, bekend als V2_1), zijn upgrades in twee stappen vereist. Volg de stappen in deze reeks.

> [!NOTE]
> Bij de upgrade van v1 naar v2 moet u controleren of de `Remoting` naam ruimte is bijgewerkt voor het gebruik van v2. Voor beeld: ' micro soft. ServiceFabric. Services. Remoting. v2. FabricTransport. client '
>
>

1. Voer een upgrade uit van de V1-service naar V2_1-service met behulp van het volgende kenmerk.
Met deze wijziging wordt gecontroleerd of de service op de v1-en de V2_1-listener luistert.

    a. Voeg een eindpunt resource met de naam ' ServiceEndpointV2_1 ' toe aan het service manifest.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2_1" />  
        </Endpoints>
      </Resources>
      ```

    b. Gebruik de volgende extensie methode voor het maken van een externe listener.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c. Voeg een assembly-kenmerk toe op externe interfaces om de v1-, V2_1 listener-en V2_1-client te gebruiken.
    ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1 | RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2_1)]

      ```
2. Voer een upgrade uit van de V1-client naar de V2_1-client met behulp van het kenmerk V2_1 client.
Met deze stap zorgt u ervoor dat de client de V2_1 stack gebruikt.
Het client project/de service hoeft niet te worden gewijzigd. Het bouwen van client projecten met een bijgewerkte interface-assembly is voldoende.

3. Deze stap is optioneel. Verwijder de versie van de V1-listener uit het kenmerk en werk vervolgens de v2-service bij.
Met deze stap zorgt u ervoor dat de service alleen luistert op de v2-listener.

    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1, RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```
  
### <a name="use-custom-serialization-with-a-remoting-wrapped-message"></a>Aangepaste serialisatie met een extern verpakt bericht gebruiken

Voor een extern verpakte bericht maken we één verpakt object met alle para meters als een veld.
Volg deze stappen:

1. Implementeer de `IServiceRemotingMessageSerializationProvider` interface voor het leveren van implementatie voor aangepaste serialisatie.
    Dit code fragment laat zien hoe de implementatie eruitziet.

      ```csharp
      public class ServiceRemotingJsonSerializationProvider : IServiceRemotingMessageSerializationProvider
      {
        public IServiceRemotingMessageBodyFactory CreateMessageBodyFactory()
        {
          return new JsonMessageFactory();
        }

        public IServiceRemotingRequestMessageBodySerializer CreateRequestMessageSerializer(Type serviceInterfaceType, IEnumerable<Type> requestWrappedType, IEnumerable<Type> requestBodyTypes = null)
        {
          return new ServiceRemotingRequestJsonMessageBodySerializer();
        }

        public IServiceRemotingResponseMessageBodySerializer CreateResponseMessageSerializer(Type serviceInterfaceType, IEnumerable<Type> responseWrappedType, IEnumerable<Type> responseBodyTypes = null)
        {
          return new ServiceRemotingResponseJsonMessageBodySerializer();
        }
      }
      ```
      ```csharp
        class JsonMessageFactory : IServiceRemotingMessageBodyFactory
            {

              public IServiceRemotingRequestMessageBody CreateRequest(string interfaceName, string methodName, int numberOfParameters, object wrappedRequestObject)
              {
                return new JsonBody(wrappedRequestObject);
              }

              public IServiceRemotingResponseMessageBody CreateResponse(string interfaceName, string methodName, object wrappedRequestObject)
              {
                return new JsonBody(wrappedRequestObject);
              }
            }
      ```
      ```csharp
      class ServiceRemotingRequestJsonMessageBodySerializer : IServiceRemotingRequestMessageBodySerializer
        {
            private JsonSerializer serializer;

            public ServiceRemotingRequestJsonMessageBodySerializer()
            {
              serializer = JsonSerializer.Create(new JsonSerializerSettings()
              {
                TypeNameHandling = TypeNameHandling.All
                });
              }

              public IOutgoingMessageBody Serialize(IServiceRemotingRequestMessageBody serviceRemotingRequestMessageBody)
             {
               if (serviceRemotingRequestMessageBody == null)
               {
                 return null;
               }          
               using (var writeStream = new MemoryStream())
               {
                 using (var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream)))
                 {
                   serializer.Serialize(jsonWriter, serviceRemotingRequestMessageBody);
                   jsonWriter.Flush();
                   var bytes = writeStream.ToArray();
                   var segment = new ArraySegment<byte>(bytes);
                   var segments = new List<ArraySegment<byte>> { segment };
                   return new OutgoingMessageBody(segments);
                 }
               }
              }

              public IServiceRemotingRequestMessageBody Deserialize(IIncomingMessageBody messageBody)
             {
               using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))
               {
                 using (JsonReader reader = new JsonTextReader(sr))
                 {
                   var ob = serializer.Deserialize<JsonBody>(reader);
                   return ob;
                 }
               }
             }
            }
      ```
      ```csharp
      class ServiceRemotingResponseJsonMessageBodySerializer : IServiceRemotingResponseMessageBodySerializer
       {
         private JsonSerializer serializer;

        public ServiceRemotingResponseJsonMessageBodySerializer()
        {
          serializer = JsonSerializer.Create(new JsonSerializerSettings()
          {
              TypeNameHandling = TypeNameHandling.All
            });
          }

          public IOutgoingMessageBody Serialize(IServiceRemotingResponseMessageBody responseMessageBody)
          {
            if (responseMessageBody == null)
            {
              return null;
            }

            using (var writeStream = new MemoryStream())
            {
              using (var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream)))
              {
                serializer.Serialize(jsonWriter, responseMessageBody);
                jsonWriter.Flush();
                var bytes = writeStream.ToArray();
                var segment = new ArraySegment<byte>(bytes);
                var segments = new List<ArraySegment<byte>> { segment };
                return new OutgoingMessageBody(segments);
              }
            }
          }

          public IServiceRemotingResponseMessageBody Deserialize(IIncomingMessageBody messageBody)
          {

             using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))
             {
               using (var reader = new JsonTextReader(sr))
               {
                 var obj = serializer.Deserialize<JsonBody>(reader);
                 return obj;
               }
             }
           }
       }
    ```
    ```csharp
    class JsonBody : WrappedMessage, IServiceRemotingRequestMessageBody, IServiceRemotingResponseMessageBody
    {
          public JsonBody(object wrapped)
          {
            this.Value = wrapped;
          }

          public void SetParameter(int position, string parameName, object parameter)
          {  //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public object GetParameter(int position, string parameName, Type paramType)
          {
            //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public void Set(object response)
          { //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public object Get(Type paramType)
          {  //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }
    }
    ```

2. De standaard-serialization-provider vervangen door `JsonSerializationProvider` voor een externe listener.

   ```csharp
   protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
   {
       return new[]
       {
           new ServiceInstanceListener((c) =>
           {
               return new FabricTransportServiceRemotingListener(c, this,
                   new ServiceRemotingJsonSerializationProvider());
           })
       };
   }
   ```

3. De standaard-serialization-provider vervangen door `JsonSerializationProvider` voor een externe client-Factory.

    ```csharp
    var proxyFactory = new ServiceProxyFactory((c) =>
    {
        return new FabricTransportServiceRemotingClientFactory(
        serializationProvider: new ServiceRemotingJsonSerializationProvider());
      });
      ```

## <a name="next-steps"></a>Volgende stappen

* [Web-API met OWIN in Reliable Services](./service-fabric-reliable-services-communication-aspnetcore.md)
* [Communicatie Windows Communication Foundation met Reliable Services](service-fabric-reliable-services-communication-wcf.md)
* [Beveiligde communicatie voor Reliable Services](service-fabric-reliable-services-secure-communication.md)

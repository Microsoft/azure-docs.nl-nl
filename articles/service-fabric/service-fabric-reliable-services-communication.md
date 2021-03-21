---
title: Overzicht van Reliable Services communicatie
description: Overzicht van het Reliable Services communicatie model, inclusief het openen van listeners voor services, het oplossen van eind punten en het communiceren tussen services.
ms.topic: conceptual
ms.date: 11/01/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 3436d29446e963faea9bda47f5a5247b7de7d859
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97912611"
---
# <a name="how-to-use-the-reliable-services-communication-apis"></a>De Reliable Services Communication Api's gebruiken
Azure Service Fabric als platform is volledig neutraal over de communicatie tussen services. Alle protocollen en Stacks zijn acceptabel, van UDP tot HTTP. Het is aan de service ontwikkelaar om te kiezen hoe services moeten communiceren. Het Reliable Services Application Framework biedt ingebouwde communicatie stacks en Api's die u kunt gebruiken om uw aangepaste communicatie-onderdelen te bouwen.

## <a name="set-up-service-communication"></a>Service communicatie instellen
De Reliable Services-API maakt gebruik van een eenvoudige interface voor service communicatie. Als u een eind punt voor uw service wilt openen, implementeert u deze interface:

```csharp

public interface ICommunicationListener
{
    Task<string> OpenAsync(CancellationToken cancellationToken);

    Task CloseAsync(CancellationToken cancellationToken);

    void Abort();
}

```

```java
public interface CommunicationListener {
    CompletableFuture<String> openAsync(CancellationToken cancellationToken);

    CompletableFuture<?> closeAsync(CancellationToken cancellationToken);

    void abort();
}
```

U kunt vervolgens de implementatie van de communicatie-listener toevoegen door deze te retour neren in een op een service gebaseerde klassen methode negeren.

Voor stateless Services:

```csharp
public class MyStatelessService : StatelessService
{
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        ...
    }
    ...
}
```
```java
public class MyStatelessService extends StatelessService {

    @Override
    protected List<ServiceInstanceListener> createServiceInstanceListeners() {
        ...
    }
    ...
}
```

Voor stateful Services:

```java
    @Override
    protected List<ServiceReplicaListener> createServiceReplicaListeners() {
        ...
    }
    ...
```

```csharp
public class MyStatefulService : StatefulService
{
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        ...
    }
    ...
}
```

In beide gevallen retourneert u een verzameling listeners. Hiermee kan uw service op meerdere eind punten Luis teren, met behulp van verschillende protocollen, met behulp van meerdere listeners. U kunt bijvoorbeeld een HTTP-listener en een afzonderlijke WebSocket-listener hebben. Elke listener krijgt een naam en de resulterende verzameling van *naam: adres* paren worden weer gegeven als een JSON-object wanneer een client de luister adressen voor een service-exemplaar of een partitie aanvraagt.

In een stateless service retourneert de onderdrukking een verzameling ServiceInstanceListeners. Een `ServiceInstanceListener` bevat een functie voor het maken van een `ICommunicationListener(C#) / CommunicationListener(Java)` en geeft deze een naam. Voor stateful Services retourneert de onderdrukking een verzameling ServiceReplicaListeners. Dit wijkt enigszins af van de staatloze tegen Hangers, omdat er een `ServiceReplicaListener` optie is voor het `ICommunicationListener` openen van een op secundaire replica's. U kunt niet alleen meerdere communicatie-listeners in een service gebruiken, maar u kunt ook opgeven welke listeners aanvragen op secundaire replica's accepteren en welke listener alleen op primaire replica's luistert.

U kunt bijvoorbeeld een ServiceRemotingListener hebben dat RPC-aanroepen alleen op primaire replica's, en een tweede aangepaste listener die lees aanvragen voor secundaire replica's via HTTP gebruikt:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[]
    {
        new ServiceReplicaListener(context =>
            new MyCustomHttpListener(context),
            "HTTPReadonlyEndpoint",
            true),

        new ServiceReplicaListener(context =>
            this.CreateServiceRemotingListener(context),
            "rpcPrimaryEndpoint",
            false)
    };
}
```

> [!NOTE]
> Wanneer u meerdere listeners voor een service maakt, **moet** elke listener een unieke naam hebben.
>
>

Ten slotte beschrijft u de eind punten die vereist zijn voor de service in het [service manifest](service-fabric-application-and-service-manifests.md) , onder de sectie op eind punten.

```xml
<Resources>
    <Endpoints>
      <Endpoint Name="WebServiceEndpoint" Protocol="http" Port="80" />
      <Endpoint Name="OtherServiceEndpoint" Protocol="tcp" Port="8505" />
    <Endpoints>
</Resources>

```

De communicatie-listener kan toegang krijgen tot de eindpunt resources die hieraan zijn toegewezen vanuit de `CodePackageActivationContext` in `ServiceContext` . De listener kan vervolgens beginnen met Luis teren naar aanvragen wanneer deze wordt geopend.

```csharp
var codePackageActivationContext = serviceContext.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```
```java
CodePackageActivationContext codePackageActivationContext = serviceContext.getCodePackageActivationContext();
int port = codePackageActivationContext.getEndpoint("ServiceEndpoint").getPort();

```

> [!NOTE]
> Eindpunt resources zijn gebruikelijk voor het hele service pakket en ze worden toegewezen door Service Fabric wanneer het service pakket wordt geactiveerd. Meerdere service replica's die worden gehost in dezelfde ServiceHost, delen mogelijk dezelfde poort. Dit betekent dat de communicatie-listener het delen van poorten moet ondersteunen. De aanbevolen manier om dit te doen, is dat de communicatie-listener de partitie-ID en de ID van de replica/instantie gebruikt wanneer het Luister adres wordt gegenereerd.
>
>

### <a name="service-address-registration"></a>Registratie service adres
Een systeem service met de naam *Naming Service* wordt uitgevoerd op service Fabric-clusters. De Naming Service is een registratie service voor services en de bijbehorende adressen waarop elk exemplaar of de replica van de service wordt geluisterd. Wanneer de `OpenAsync(C#) / openAsync(Java)` methode van een `ICommunicationListener(C#) / CommunicationListener(Java)` voltooid is, wordt de geretourneerde waarde geregistreerd in de Naming Service. Deze geretourneerde waarde die wordt gepubliceerd in de Naming Service, is een teken reeks waarvan de waarde helemaal niets kan hebben. Deze teken reeks waarde is wat clients zien wanneer ze vragen om een adres voor de service van de Naming Service.

```csharp
public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    EndpointResourceDescription serviceEndpoint = serviceContext.CodePackageActivationContext.GetEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.Port;

    this.listeningAddress = string.Format(
                CultureInfo.InvariantCulture,
                "http://+:{0}/",
                port);

    this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

    this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));

    // the string returned here will be published in the Naming Service.
    return Task.FromResult(this.publishAddress);
}
```
```java
public CompletableFuture<String> openAsync(CancellationToken cancellationToken)
{
    EndpointResourceDescription serviceEndpoint = serviceContext.getCodePackageActivationContext.getEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.getPort();

    this.publishAddress = String.format("http://%s:%d/", FabricRuntime.getNodeContext().getIpAddressOrFQDN(), port);

    this.webApp = new WebApp(port);
    this.webApp.start();

    /* the string returned here will be published in the Naming Service.
     */
    return CompletableFuture.completedFuture(this.publishAddress);
}
```

Service Fabric biedt Api's waarmee clients en andere services dit adres vervolgens kunnen vragen op service naam. Dit is belang rijk omdat het service adres niet statisch is. Services worden in het cluster verplaatst voor resource verdeling en beschik baarheid. Dit is het mechanisme waarmee clients het Luister adres voor een service kunnen omzetten.

> [!NOTE]
> Zie voor een volledig overzicht van het schrijven van een communicatie-listener [service Fabric Web API-services met OWIN self-hosting](./service-fabric-reliable-services-communication-aspnetcore.md) voor C#, terwijl u voor Java uw eigen http-server implementatie kunt schrijven, zie EchoServer toepassing voor beeld in https://github.com/Azure-Samples/service-fabric-java-getting-started .
>
>

## <a name="communicating-with-a-service"></a>Communiceren met een service
De Reliable Services-API biedt de volgende bibliotheken om clients te schrijven die communiceren met Services.

### <a name="service-endpoint-resolution"></a>Service-eindpunt resolutie
De eerste stap bij het communiceren met een service is het omzetten van een eindpunt adres van de partitie of het exemplaar van de service waarmee u wilt praten. De `ServicePartitionResolver(C#) / FabricServicePartitionResolver(Java)` klasse Utility is een eenvoudige primitieve die clients helpt bij het bepalen van het eind punt van een service tijdens runtime. In Service Fabric terminologie wordt het proces van het bepalen van het eind punt van een service aangeduid als de *service-eindpunt resolutie*.

Als u verbinding wilt maken met services binnen een cluster, kunt u ServicePartitionResolver maken met behulp van de standaard instellingen. Dit is het aanbevolen gebruik voor de meeste situaties:

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();
```
```java
FabricServicePartitionResolver resolver = FabricServicePartitionResolver.getDefault();
```

Als u verbinding wilt maken met Services in een ander cluster, kunt u een ServicePartitionResolver maken met een set cluster gateway-eind punten. Houd er rekening mee dat gateway-eind punten alleen verschillende eind punten hebben om verbinding te maken met hetzelfde cluster. Bijvoorbeeld:

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```

U kunt ook `ServicePartitionResolver` een functie krijgen voor het maken van een `FabricClient` intern gebruik:

```csharp
public delegate FabricClient CreateFabricClientDelegate();
```
```java
public FabricServicePartitionResolver(CreateFabricClient createFabricClient) {
...
}

public interface CreateFabricClient {
    public FabricClient getFabricClient();
}
```

`FabricClient` is het object dat wordt gebruikt voor communicatie met het Service Fabric-cluster voor verschillende beheer bewerkingen op het cluster. Dit is handig als u meer controle wilt over de manier waarop een service partitie-resolver communiceert met uw cluster. `FabricClient` voert intern caching uit en is doorgaans kostbaar. het is dus belang rijk dat instanties zoveel mogelijk opnieuw worden gebruikt `FabricClient` .

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver(() => CreateMyFabricClient());
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver(() -> new CreateFabricClientImpl());
```

Vervolgens wordt er een methode voor het oplossen gebruikt om het adres van een service of een service partitie voor gepartitioneerde Services op te halen.

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();

ResolvedServicePartition partition =
    await resolver.ResolveAsync(new Uri("fabric:/MyApp/MyService"), new ServicePartitionKey(), cancellationToken);
```
```java
FabricServicePartitionResolver resolver = FabricServicePartitionResolver.getDefault();

CompletableFuture<ResolvedServicePartition> partition =
    resolver.resolveAsync(new URI("fabric:/MyApp/MyService"), new ServicePartitionKey());
```

Een service adres kan gemakkelijk worden omgezet met behulp van een ServicePartitionResolver, maar er is meer werk nodig om ervoor te zorgen dat het omgezette adres correct kan worden gebruikt. Uw client moet detecteren of de verbindings poging is mislukt vanwege een tijdelijke fout en opnieuw kan worden uitgevoerd (bijvoorbeeld als de service is verplaatst of tijdelijk niet beschikbaar is) of een permanente fout (bijvoorbeeld omdat de service is verwijderd of de aangevraagde bron niet meer bestaat). Service-exemplaren of replica's kunnen op elk gewenst moment van het knoop punt worden verplaatst naar een knoop punt om meerdere redenen. Het service adres dat via ServicePartitionResolver is opgelost, kan worden verouderd op het moment dat de client code verbinding probeert te maken. In dat geval moet de client het adres opnieuw omzetten. Als u de vorige opgeeft, `ResolvedServicePartition` geeft u aan dat de resolver het opnieuw moet proberen in plaats van eenvoudigweg een adres in de cache op te halen.

Normaal gesp roken hoeft de client code niet rechtstreeks met de ServicePartitionResolver te werken. Het wordt gemaakt en door gegeven aan communicatie-client fabrieken in de Reliable Services-API. De fabrieken gebruiken de resolver intern om een client object te genereren dat kan worden gebruikt om te communiceren met Services.

### <a name="communication-clients-and-factories"></a>Communicatie clients en-fabrieken
De communicatie-Factory-bibliotheek implementeert een typisch herhalings patroon voor fout afhandeling waarmee opnieuw verbinding wordt gemaakt met omgezette service-eind punten eenvoudiger. De fabrieks bibliotheek biedt het mechanisme voor opnieuw proberen terwijl u de fout afhandeling opgeeft.

`ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)` Hiermee definieert u de basis interface die wordt geïmplementeerd door een communicatie-client-Factory die clients produceert die kunnen communiceren met een Service Fabric-service. De implementatie van de CommunicationClientFactory is afhankelijk van de communicatie stack die wordt gebruikt door de Service Fabric-service waarbij de client wil communiceren. De Reliable Services-API biedt een `CommunicationClientFactoryBase<TCommunicationClient>` . Dit biedt een basis implementatie van de CommunicationClientFactory-interface en voert taken uit die gemeen schappelijk zijn voor alle communicatie stacks. (Deze taken omvatten het gebruik van een ServicePartitionResolver om het service-eind punt te bepalen). Clients implementeren gewoonlijk de abstracte CommunicationClientFactoryBase-klasse voor het verwerken van logica die specifiek is voor de communicatie stack.

De communicatie client ontvangt alleen een adres en gebruikt deze om verbinding te maken met een service. De client kan elk gewenst protocol gebruiken.

```csharp
public class MyCommunicationClient : ICommunicationClient
{
    public ResolvedServiceEndpoint Endpoint { get; set; }

    public string ListenerName { get; set; }

    public ResolvedServicePartition ResolvedServicePartition { get; set; }
}
```
```java
public class MyCommunicationClient implements CommunicationClient {

    private ResolvedServicePartition resolvedServicePartition;
    private String listenerName;
    private ResolvedServiceEndpoint endPoint;

    /*
     * Getters and Setters
     */
}
```

De client-Factory is primair verantwoordelijk voor het maken van communicatie clients. Voor clients die geen permanente verbinding onderhouden, zoals een HTTP-client, hoeft de fabriek alleen de client te maken en te retour neren. Andere protocollen die een permanente verbinding onderhouden, zoals sommige binaire protocollen, moeten ook worden gevalideerd ( `ValidateClient(string endpoint, MyCommunicationClient client)` ) door de fabriek om te bepalen of de verbinding opnieuw moet worden gemaakt.  

```csharp
public class MyCommunicationClientFactory : CommunicationClientFactoryBase<MyCommunicationClient>
{
    protected override void AbortClient(MyCommunicationClient client)
    {
    }

    protected override Task<MyCommunicationClient> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
    {
    }

    protected override bool ValidateClient(MyCommunicationClient clientChannel)
    {
    }

    protected override bool ValidateClient(string endpoint, MyCommunicationClient client)
    {
    }
}
```
```java
public class MyCommunicationClientFactory extends CommunicationClientFactoryBase<MyCommunicationClient> {

    @Override
    protected boolean validateClient(MyCommunicationClient clientChannel) {
    }

    @Override
    protected boolean validateClient(String endpoint, MyCommunicationClient client) {
    }

    @Override
    protected CompletableFuture<MyCommunicationClient> createClientAsync(String endpoint) {
    }

    @Override
    protected void abortClient(MyCommunicationClient client) {
    }
}
```

Ten slotte is een uitzonderings-handler verantwoordelijk voor het bepalen van welke actie moet worden ondernomen wanneer er een uitzonde ring optreedt. Uitzonde ringen worden gecategoriseerd in **herstel** bare en **niet-nieuwer**.

* **Niet-herstel** bare uitzonde ringen worden gewoon teruggeleid naar de aanroeper.
* **Herhaal** bare uitzonde ringen worden verder ingedeeld in **tijdelijke** en **niet-tijdelijke**.
  * **Tijdelijke** uitzonde ringen zijn die eenvoudigweg opnieuw kunnen worden uitgevoerd zonder het adres van het service-eind punt opnieuw op te lossen. Hieronder vallen tijdelijke netwerk problemen of service fout reacties, behalve die aangeven dat het adres van het service-eind punt niet bestaat.
  * **Niet-tijdelijke** uitzonde ringen zijn die vereisen dat het adres van het service-eind punt opnieuw wordt opgelost. Dit zijn onder andere uitzonde ringen die aangeven dat het service-eind punt niet kan worden bereikt. Dit geeft aan dat de service naar een ander knoop punt is verplaatst.

Het `TryHandleException` maakt een beslissing over een bepaalde uitzonde ring. Als het **niet weet** wat beslissingen nemen over een uitzonde ring, moet deze **Onwaar** retour neren. Als het beslist wat er moet worden **gedaan** , moet het het resultaat dienovereenkomstig instellen en **waar** retour neren.

```csharp
class MyExceptionHandler : IExceptionHandler
{
    public bool TryHandleException(ExceptionInformation exceptionInformation, OperationRetrySettings retrySettings, out ExceptionHandlingResult result)
    {
        // if exceptionInformation.Exception is known and is transient (can be retried without re-resolving)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, true, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;


        // if exceptionInformation.Exception is known and is not transient (indicates a new service endpoint address must be resolved)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, false, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;

        // if exceptionInformation.Exception is unknown (let the next IExceptionHandler attempt to handle it)
        result = null;
        return false;
    }
}
```
```java
public class MyExceptionHandler implements ExceptionHandler {

    @Override
    public ExceptionHandlingResult handleException(ExceptionInformation exceptionInformation, OperationRetrySettings retrySettings) {

        /* if exceptionInformation.getException() is known and is transient (can be retried without re-resolving)
         */
        result = new ExceptionHandlingRetryResult(exceptionInformation.getException(), true, retrySettings, retrySettings.getDefaultMaxRetryCount());
        return true;


        /* if exceptionInformation.getException() is known and is not transient (indicates a new service endpoint address must be resolved)
         */
        result = new ExceptionHandlingRetryResult(exceptionInformation.getException(), false, retrySettings, retrySettings.getDefaultMaxRetryCount());
        return true;

        /* if exceptionInformation.getException() is unknown (let the next ExceptionHandler attempt to handle it)
         */
        result = null;
        return false;

    }
}
```
### <a name="putting-it-all-together"></a>Alles samenvoegen
Met een `ICommunicationClient(C#) / CommunicationClient(Java)` , `ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)` , en `IExceptionHandler(C#) / ExceptionHandler(Java)` gebouwd rond een communicatie protocol, `ServicePartitionClient(C#) / FabricServicePartitionClient(Java)` wordt dit allemaal samengebracht en biedt de omzetting van de fout afhandeling en het service partitie adres rond deze onderdelen.

```csharp
private MyCommunicationClientFactory myCommunicationClientFactory;
private Uri myServiceUri;

var myServicePartitionClient = new ServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myPartitionKey);

var result = await myServicePartitionClient.InvokeWithRetryAsync(async (client) =>
   {
      // Communicate with the service using the client.
   },
   CancellationToken.None);

```
```java
private MyCommunicationClientFactory myCommunicationClientFactory;
private URI myServiceUri;

FabricServicePartitionClient myServicePartitionClient = new FabricServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myPartitionKey);

CompletableFuture<?> result = myServicePartitionClient.invokeWithRetryAsync(client -> {
      /* Communicate with the service using the client.
       */
   });

```

## <a name="next-steps"></a>Volgende stappen
* [ASP.NET Core met Reliable Services](service-fabric-reliable-services-communication-aspnetcore.md)
* [Externe procedure aanroepen met Reliable Services externe toegang](service-fabric-reliable-services-communication-remoting.md)
* [WCF-communicatie met behulp van Reliable Services](service-fabric-reliable-services-communication-wcf.md)

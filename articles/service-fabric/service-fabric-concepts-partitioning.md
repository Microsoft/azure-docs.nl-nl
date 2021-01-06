---
title: Service Fabric Services partitioneren
description: Meer informatie over het partitioneren Service Fabric stateless en stateful Services
ms.topic: conceptual
ms.date: 06/30/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 199ae9d9844149c1931da638633110f717fe0517
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97915892"
---
# <a name="partition-service-fabric-reliable-services"></a>Partitioneren van Service Fabric-betrouwbare services
Dit artikel bevat een inleiding tot de basis concepten van het partitioneren van Azure Service Fabric reliable Services. Als u partitioneert, kan de gegevens opslag op de lokale computers worden geschaald.

> [!TIP]
> Een [volledig voor beeld](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions) van de code in dit artikel is beschikbaar op github.

## <a name="partitioning"></a>Partitionering
Partitioneren is niet uniek voor Service Fabric. Eigenlijk is het een kern patroon van het bouwen van schaal bare Services. In een bredere zin kunnen we nadenken over partitioneren als een concept van het delen van de status (gegevens) en te berekenen in kleinere, toegankelijke eenheden om de schaal baarheid en prestaties te verbeteren. Een bekende vorm van partitionering is het [partitioneren van gegevens][wikipartition], ook wel bekend als sharding.

### <a name="partition-service-fabric-stateless-services"></a>Partities Service Fabric stateless Services
Voor stateless Services kunt u nadenken over een partitie die een logische eenheid is die een of meer exemplaren van een service bevat. In afbeelding 1 ziet u een stateless service met vijf instanties die over een cluster worden gedistribueerd met behulp van één partitie.

![Stateless service](./media/service-fabric-concepts-partitioning/statelessinstances.png)

Er zijn heel twee soorten stateless service oplossingen. De eerste is een service die de status van het externe apparaat persistent maakt, bijvoorbeeld in een data base in Azure SQL Database (zoals een website waar de sessie-informatie en-gegevens worden opgeslagen). De tweede is een alleen-reken kundige service (zoals een calculator of afbeeldings miniatuur) die geen permanente status beheert.

In beide gevallen is het partitioneren van een stateless service een zeer zeldzaam scenario: de schaal baarheid en beschik baarheid worden normaal gesp roken bereikt door meer instanties toe te voegen. De enige keer dat u meerdere partities wilt overwegen voor stateless service exemplaren, is wanneer u aan speciale routerings aanvragen moet voldoen.

Denk bijvoorbeeld aan een geval waarbij gebruikers met Id's in een bepaald bereik alleen moeten worden geleverd door een bepaald service-exemplaar. Een ander voor beeld van wanneer u een stateless service partitioneert, is wanneer u een echt gepartitioneerde back-end hebt (bijvoorbeeld een Shard-data base in SQL Database) en u wilt bepalen welk service-exemplaar moet schrijven naar de data base Shard, of andere voorbereidende werkzaamheden uitvoeren in de stateless service waarvoor dezelfde partitie-informatie is vereist als wordt gebruikt in de back-end. Deze soorten scenario's kunnen ook op verschillende manieren worden opgelost en hoeven niet noodzakelijkerwijs service partitioneren.

De rest van dit scenario is gericht op stateful Services.

### <a name="partition-service-fabric-stateful-services"></a>Service Fabric stateful Services partitioneren
Met Service Fabric kunt u eenvoudig schaal bare stateful services ontwikkelen door een eerste klasse te bieden voor het partitioneren van de status (gegevens). In het algemeen kunt u nadenken over een partitie van een stateful service als een schaal eenheid die zeer betrouwbaar is via [replica's](service-fabric-availability-services.md) die worden gedistribueerd en verdeeld over de knoop punten in een cluster.

Partitioneren in de context van Service Fabric stateful Services verwijst naar het proces van het bepalen of een bepaalde service partitie verantwoordelijk is voor een deel van de volledige status van de service. (Zoals eerder vermeld, is een partitie een set [replica's](service-fabric-availability-services.md)). Het is een goed idee over Service Fabric dat de partities op verschillende knoop punten worden geplaatst. Hierdoor kunnen ze worden uitgebreid naar de resource limiet van een knoop punt. Wanneer de gegevens moeten groeien, worden de partities verg root en Service Fabric worden partities op verschillende knoop punten opnieuw verdeeld. Zo zorgt u ervoor dat hardwarebronnen blijvend worden gebruikt.

Stel dat u begint met een cluster met vijf knoop punten en een service die is geconfigureerd met 10 partities en een doel van drie replica's. In dit geval moet Service Fabric de replica's in het hele cluster verdelen en distribueren, en kunt u met twee primaire [replica's](service-fabric-availability-services.md) per knoop punt eindigen.
Als u het cluster nu naar tien knoop punten wilt uitschalen, worden de primaire [replica's](service-fabric-availability-services.md) op alle tien knoop punten opnieuw gebalanceerd met Service Fabric. Als u terugschaalt naar 5 knoop punten, Service Fabric alle replica's op de vijf knoop punten opnieuw verdelen.  

In afbeelding 2 ziet u de distributie van 10 partities vóór en na het schalen van het cluster.

![Stateful service](./media/service-fabric-concepts-partitioning/partitions.png)

Als gevolg hiervan wordt het uitschalen bereikt omdat aanvragen van clients worden gedistribueerd op computers, de algehele prestaties van de toepassing worden verbeterd en de toegang tot de gegevens segmenten wordt beperkt.

## <a name="plan-for-partitioning"></a>Partitioneren plannen
Voordat u een service implementeert, moet u altijd rekening houden met de strategieën voor partitioneren die nodig zijn om uit te schalen. Er zijn verschillende manieren, maar alle focussen op wat de toepassing nodig heeft. Voor de context van dit artikel worden enkele van de belang rijke aspecten in overweging nemen.

Een goede benadering is om te denken over de structuur van de status die moet worden gepartitioneerd, zoals in de eerste stap.

Laten we een eenvoudig voor beeld maken. Als u een service voor een hele regio wilt bouwen, kunt u een partitie maken voor elke plaats in de regio. Vervolgens kunt u de stemmen voor elke persoon opslaan in de plaats in de partitie die overeenkomt met die plaats. Afbeelding 3 illustreert een verzameling personen en de plaats waarin ze zich bevinden.

![Eenvoudige partitie](./media/service-fabric-concepts-partitioning/cities.png)

Wanneer de populatie van steden sterk varieert, kan het voor komen dat u een aantal partities met veel gegevens (bijvoorbeeld Seattle) en andere partities met een zeer weinig status (bijvoorbeeld Kirkland) hebt. Wat is de impact van het hebben van partities met een oneven hoeveelheid status?

Als u het voor beeld opnieuw vindt, kunt u gemakkelijk zien dat de partitie met de stemmen voor Seattle meer verkeer krijgt dan het Kirkland. Service Fabric zorgt er standaard voor dat er precies hetzelfde aantal primaire en secundaire replica's is op elk knoop punt. Het kan voor komen dat u zich kunt voordoen met knoop punten die replica's bevatten die meer verkeer leveren en andere die minder verkeer leveren. U wilt liever warme en koude vlekken in een cluster vermijden.

Als u dit wilt voor komen, moet u twee dingen doen vanuit een partitie punt van de weer gave:

* Probeer de status te partitioneren zodat deze gelijkmatig over alle partities wordt verdeeld.
* Rapport belasting van elk van de replica's voor de service. (Raadpleeg dit artikel voor meer informatie over [metrische gegevens en belasting](service-fabric-cluster-resource-manager-metrics.md)). Service Fabric biedt de mogelijkheid om de belasting te rapporteren die wordt verbruikt door services, zoals de hoeveelheid geheugen of het aantal records. Op basis van de gerapporteerde metrische gegevens, Service Fabric detecteert dat sommige partities hogere belastingen bieden dan andere, en het cluster opnieuw verdelen door replica's naar meer geschikte knoop punten te verplaatsen, zodat het algehele knoop punt niet overbelast is.

Soms kunt u niet weten hoeveel gegevens er in een bepaalde partitie worden weer gegeven. Een algemene aanbeveling is daarom beide te doen door een strategie voor partitionering te nemen waarmee de gegevens gelijkmatig over de partities en seconde worden verdeeld, door de belasting van de rapportage.  De eerste methode voor komt situaties die in het stemmen-voor beeld worden beschreven, terwijl de tweede de tijdelijke verschillen in de toegang of belasting in de loop van de tijd vereenvoudigt.

Een ander aspect van het plannen van partities is het kiezen van het juiste aantal partities dat moet worden gestart.
Vanuit een Service Fabric perspectief is er niets dat voor komt dat u kunt beginnen met een groter aantal partities dan verwacht voor uw scenario.
Ervan uitgaande dat het maximum aantal partities een geldige benadering is.

In zeldzame gevallen is het mogelijk dat u uiteindelijk meer partities nodig hebt dan u eerst hebt gekozen. Wanneer u het aantal partities na het feit niet kunt wijzigen, moet u een aantal geavanceerde partitie benaderingen Toep assen, zoals het maken van een nieuw service-exemplaar van hetzelfde service type. U moet ook enkele logica van de client implementeren die de aanvragen naar het juiste service-exemplaar doorstuurt, op basis van kennis aan de client zijde die de client code moet onderhouden.

Een andere overweging voor het partitioneren van de planning is de beschik bare computer bronnen. Als de status moet worden geopend en opgeslagen, kunt u het volgende doen:

* Limieten voor netwerk bandbreedte
* Systeem geheugen limieten
* Limieten voor schijf opslag

Wat gebeurt er als u de resource beperkingen in een actief cluster uitvoert? Het antwoord is dat u het cluster eenvoudig kunt uitschalen om te voorzien in de nieuwe vereisten.

[De hand leiding voor capaciteits planning](service-fabric-capacity-planning.md) biedt richt lijnen voor het bepalen van het aantal knoop punten dat nodig is voor uw cluster.

## <a name="get-started-with-partitioning"></a>Aan de slag met partitioneren
In deze sectie wordt beschreven hoe u aan de slag gaat met het partitioneren van uw service.

Service Fabric biedt een keuze uit drie partitie schema's:

* Partitioneren met een bereik (ook wel bekend als UniformInt64Partition).
* Partitioneren met een naam. Toepassingen die gebruikmaken van dit model, bevatten meestal gegevens die kunnen worden gebuckd binnen een gebonden set. Enkele algemene voor beelden van gegevens velden die als benoemde partitie sleutels worden gebruikt, zijn regio's, post codes, klant groepen of andere zakelijke grenzen.
* Singleton-partitionering. Singleton-partities worden meestal gebruikt wanneer de service geen extra route ring nodig heeft. Stateless Services gebruiken bijvoorbeeld standaard dit partitie schema.

Het schema voor het partitioneren van namen en Singleton zijn speciale vormen van partities met een bereik. Standaard worden in de Visual Studio-sjablonen voor Service Fabric gepartitioneerde partities gebruikt, aangezien dit het meest voorkomende is en het handigst is. De rest van dit artikel is gericht op het partitioneren van het bereik.

### <a name="ranged-partitioning-scheme"></a>Schema voor partitioneren met bereik
Dit wordt gebruikt om een bereik met gehele getallen (aangeduid met een lage sleutel en hoge sleutel) en een aantal partities (n) op te geven. Er worden n partities gemaakt, die elk verantwoordelijk zijn voor een niet-overlappend subbereik van het totale partitie sleutel bereik. Een voor beeld: een partitie schema met een lage sleutel van 0, een hoge sleutel van 99 en een aantal van 4 zou vier partities maken, zoals hieronder wordt weer gegeven.

![Bereik partitioneren](./media/service-fabric-concepts-partitioning/range-partitioning.png)

Een veelvoorkomende aanpak is het maken van een hash op basis van een unieke sleutel in de gegevensset. Enkele algemene voor beelden van sleutels zijn een voertuig identificatienummer (chassis nummer), een werk nemer-ID of een unieke teken reeks. Als u deze unieke sleutel gebruikt, genereert u vervolgens een hash-code, modulus het sleutel bereik om als uw sleutel te gebruiken. U kunt de boven-en ondergrens van het toegestane sleutel bereik opgeven.

### <a name="select-a-hash-algorithm"></a>Hash-algoritme selecteren
Een belang rijk onderdeel van hashing is het selecteren van uw hash-algoritme. Een overweging is of het doel is om Vergelijk bare sleutels in de buurt te groeperen (gevoelige hashing van lokalen), of dat de activiteit breed moet worden verdeeld over alle partities (distributie-hashing).

De kenmerken van een goede distributie-hash-algoritme zijn dat ze gemakkelijk te berekenen zijn, het een aantal botsingen heeft en de sleutels gelijkmatig distribueert. Een goed voor beeld van een efficiënt hash-algoritme is het [FNV-1](https://en.wikipedia.org/wiki/Fowler%E2%80%93Noll%E2%80%93Vo_hash_function) hash-algoritme.

Een goede resource voor algemene hash-code algoritmen is de [Wikipedia-pagina op hash-functies](https://en.wikipedia.org/wiki/Hash_function).

## <a name="build-a-stateful-service-with-multiple-partitions"></a>Een stateful service met meerdere partities bouwen
Laten we uw eerste betrouw bare stateful service maken met meerdere partities. In dit voor beeld maakt u een zeer eenvoudige toepassing waarin u alle achternamen wilt opslaan die beginnen met dezelfde letter in dezelfde partitie.

Voordat u code schrijft, moet u nadenken over de partities en partitie sleutels. U hebt 26 partities nodig (één voor elke letter in het alfabet), maar wat zijn de lage en hoge sleutels?
Omdat we letterlijk één partitie per letter willen hebben, kunnen we 0 als de lage sleutel en 25 als hoge sleutel gebruiken, aangezien elke letter een eigen sleutel is.

> [!NOTE]
> Dit is een vereenvoudigd scenario, zoals in werkelijkheid de distributie zou ongelijk zijn. Achternamen die beginnen met de letters "S" of "M", komen vaker voor dan de naam die begint met "X" of "Y".
> 
> 

1. Open **Visual Studio**-  >  **bestand**  >  **Nieuw**  >  **project**.
2. Kies in het dialoog venster **Nieuw project** de service Fabric toepassing.
3. Roep het project "AlphabetPartitions" aan.
4. Kies in het dialoog venster **een service maken** de optie **stateful** service en roep het ' alfabet. processing ' aan.
5. Stel het aantal partities in. Open het Applicationmanifest.xml-bestand dat zich bevindt in de map Application Package root van het AlphabetPartitions-project en werk de para meter bij Processing_PartitionCount op 26 zoals hieronder wordt weer gegeven.
   
    ```xml
    <Parameter Name="Processing_PartitionCount" DefaultValue="26" />
    ```
   
    U moet ook de eigenschappen LowKey en HighKey van het element StatefulService in het ApplicationManifest.xml bijwerken, zoals hieronder wordt weer gegeven.
   
    ```xml
    <Service Name="Processing">
      <StatefulService ServiceTypeName="ProcessingType" TargetReplicaSetSize="[Processing_TargetReplicaSetSize]" MinReplicaSetSize="[Processing_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Processing_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    ```
6. Als de service toegankelijk is, opent u een eind punt op een poort door het eindpunt element van ServiceManifest.xml (in de map PackageRoot) toe te voegen voor de alfabet. processing-service, zoals hieronder wordt weer gegeven:
   
    ```xml
    <Endpoint Name="ProcessingServiceEndpoint" Port="8089" Protocol="http" Type="Internal" />
    ```
   
    De service is nu geconfigureerd om te Luis teren naar een intern eind punt met 26 partities.
7. Vervolgens moet u de `CreateServiceReplicaListeners()` methode van de verwerkings klasse overschrijven.
   
   > [!NOTE]
   > Voor dit voor beeld wordt ervan uitgegaan dat u een eenvoudige HttpCommunicationListener gebruikt. Zie voor meer informatie over betrouw bare service communicatie [het betrouw bare communicatie model voor services](service-fabric-reliable-services-communication.md).
   > 
   > 
8. Een aanbevolen patroon voor de URL waar een replica op luistert, is de volgende indeling: `{scheme}://{nodeIp}:{port}/{partitionid}/{replicaid}/{guid}` .
    Daarom wilt u uw communicatie-listener configureren om te Luis teren naar de juiste eind punten en met dit patroon.
   
    Meerdere replica's van deze service worden mogelijk gehost op dezelfde computer, dus dit adres moet uniek zijn voor de replica. Dit is de reden waarom partitie-ID + replica-ID zich in de URL bevindt. HttpListener kunnen Luis teren op meerdere adressen op dezelfde poort zolang het URL-voor voegsel uniek is.
   
    De extra GUID is daar voor een geavanceerde situatie waarbij secundaire replica's ook op alleen-lezen aanvragen Luis teren. Als dat het geval is, moet u ervoor zorgen dat er een nieuw uniek adres wordt gebruikt bij het overstappen van de primaire naar de secundaire client, zodat clients het adres opnieuw kunnen omzetten. + wordt gebruikt als het adres, zodat de replica luistert op alle beschik bare hosts (IP, FQDN, localhost, enzovoort) In de volgende code ziet u een voor beeld.
   
    ```csharp
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
         return new[] { new ServiceReplicaListener(context => this.CreateInternalListener(context))};
    }
    private ICommunicationListener CreateInternalListener(ServiceContext context)
    {
   
         EndpointResourceDescription internalEndpoint = context.CodePackageActivationContext.GetEndpoint("ProcessingServiceEndpoint");
         string uriPrefix = String.Format(
                "{0}://+:{1}/{2}/{3}-{4}/",
                internalEndpoint.Protocol,
                internalEndpoint.Port,
                context.PartitionId,
                context.ReplicaOrInstanceId,
                Guid.NewGuid());
   
         string nodeIP = FabricRuntime.GetNodeContext().IPAddressOrFQDN;
   
         string uriPublished = uriPrefix.Replace("+", nodeIP);
         return new HttpCommunicationListener(uriPrefix, uriPublished, this.ProcessInternalRequest);
    }
    ```
   
    Het is ook een goed idee dat de gepubliceerde URL enigszins afwijkt van het Luister-URL-voor voegsel.
    De luister-URL wordt gegeven aan HttpListener. De gepubliceerde URL is de URL die wordt gepubliceerd naar het Service Fabric Naming Service, dat wordt gebruikt voor service detectie. Clients vragen om dit adres via die detectie service. Het adres dat clients krijgen, moet het daad werkelijke IP of de FQDN van het knoop punt hebben om verbinding te kunnen maken. Daarom moet u + vervangen door het IP-adres of de FQDN van het knoop punt, zoals hierboven wordt weer gegeven.
9. De laatste stap bestaat uit het toevoegen van de verwerkings logica aan de service zoals hieronder wordt weer gegeven.
   
    ```csharp
    private async Task ProcessInternalRequest(HttpListenerContext context, CancellationToken cancelRequest)
    {
        string output = null;
        string user = context.Request.QueryString["lastname"].ToString();
   
        try
        {
            output = await this.AddUserAsync(user);
        }
        catch (Exception ex)
        {
            output = ex.Message;
        }
   
        using (HttpListenerResponse response = context.Response)
        {
            if (output != null)
            {
                byte[] outBytes = Encoding.UTF8.GetBytes(output);
                response.OutputStream.Write(outBytes, 0, outBytes.Length);
            }
        }
    }
    private async Task<string> AddUserAsync(string user)
    {
        IReliableDictionary<String, String> dictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<String, String>>("dictionary");
   
        using (ITransaction tx = this.StateManager.CreateTransaction())
        {
            bool addResult = await dictionary.TryAddAsync(tx, user.ToUpperInvariant(), user);
   
            await tx.CommitAsync();
   
            return String.Format(
                "User {0} {1}",
                user,
                addResult ? "successfully added" : "already exists");
        }
    }
    ```
   
    `ProcessInternalRequest` Hiermee worden de waarden van de query teken reeks parameter voor het aanroepen van de partitie en aanroepen `AddUserAsync` om de LastName toe te voegen aan de betrouw bare woorden lijst gelezen `dictionary` .
10. We gaan een stateless service toevoegen aan het project om te zien hoe u een bepaalde partitie kunt aanroepen.
    
    Deze service fungeert als een eenvoudige webinterface die de para meter LastName als een query teken reeks parameter accepteert, bepaalt de partitie sleutel en verzendt deze naar de alfabet. processing-service voor verwerking.
11. In het dialoog venster **een service maken** kiest u **stateless** service en roept u deze ' alfabet. Web ' aan, zoals hieronder wordt weer gegeven.
    
    ![Scherm afbeelding stateless service](./media/service-fabric-concepts-partitioning/createnewstateless.png).
12. Werk de eindpunt gegevens bij in de ServiceManifest.xml van de alfabet. WebApi-service om een poort te openen, zoals hieronder wordt weer gegeven.
    
    ```xml
    <Endpoint Name="WebApiServiceEndpoint" Protocol="http" Port="8081"/>
    ```
13. U moet een verzameling ServiceInstanceListeners in het web class ophalen. U kunt er ook voor kiezen om een eenvoudige HttpCommunicationListener te implementeren.
    
    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[] {new ServiceInstanceListener(context => this.CreateInputListener(context))};
    }
    private ICommunicationListener CreateInputListener(ServiceContext context)
    {
        // Service instance's URL is the node's IP & desired port
        EndpointResourceDescription inputEndpoint = context.CodePackageActivationContext.GetEndpoint("WebApiServiceEndpoint")
        string uriPrefix = String.Format("{0}://+:{1}/alphabetpartitions/", inputEndpoint.Protocol, inputEndpoint.Port);
        var uriPublished = uriPrefix.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);
        return new HttpCommunicationListener(uriPrefix, uriPublished, this.ProcessInputRequest);
    }
    ```
14. Nu moet u de verwerkings logica implementeren. De HttpCommunicationListener wordt aangeroepen `ProcessInputRequest` Wanneer een aanvraag binnenkomt. We gaan nu de onderstaande code toevoegen.
    
    ```csharp
    private async Task ProcessInputRequest(HttpListenerContext context, CancellationToken cancelRequest)
    {
        String output = null;
        try
        {
            string lastname = context.Request.QueryString["lastname"];
            char firstLetterOfLastName = lastname.First();
            ServicePartitionKey partitionKey = new ServicePartitionKey(Char.ToUpper(firstLetterOfLastName) - 'A');
    
            ResolvedServicePartition partition = await this.servicePartitionResolver.ResolveAsync(alphabetServiceUri, partitionKey, cancelRequest);
            ResolvedServiceEndpoint ep = partition.GetEndpoint();
    
            JObject addresses = JObject.Parse(ep.Address);
            string primaryReplicaAddress = (string)addresses["Endpoints"].First();
    
            UriBuilder primaryReplicaUriBuilder = new UriBuilder(primaryReplicaAddress);
            primaryReplicaUriBuilder.Query = "lastname=" + lastname;
    
            string result = await this.httpClient.GetStringAsync(primaryReplicaUriBuilder.Uri);
    
            output = String.Format(
                    "Result: {0}. <p>Partition key: '{1}' generated from the first letter '{2}' of input value '{3}'. <br>Processing service partition ID: {4}. <br>Processing service replica address: {5}",
                    result,
                    partitionKey,
                    firstLetterOfLastName,
                    lastname,
                    partition.Info.Id,
                    primaryReplicaAddress);
        }
        catch (Exception ex) { output = ex.Message; }
    
        using (var response = context.Response)
        {
            if (output != null)
            {
                output = output + "added to Partition: " + primaryReplicaAddress;
                byte[] outBytes = Encoding.UTF8.GetBytes(output);
                response.OutputStream.Write(outBytes, 0, outBytes.Length);
            }
        }
    }
    ```
    
    Laten we stap voor stap door lopen. De code leest de eerste letter van de query teken reeks parameter `lastname` in een teken. Vervolgens wordt de partitie sleutel voor deze letter bepaald door de hexadecimale waarde van `A` uit de hexadecimale waarde van de achternaam ' eerste letter ' af te trekken.
    
    ```csharp
    string lastname = context.Request.QueryString["lastname"];
    char firstLetterOfLastName = lastname.First();
    ServicePartitionKey partitionKey = new ServicePartitionKey(Char.ToUpper(firstLetterOfLastName) - 'A');
    ```
    
    Houd er rekening mee dat we voor dit voor beeld 26 partities met één partitie sleutel per partitie gebruiken.
    Vervolgens verkrijgen we de service partitie `partition` voor deze sleutel met behulp van de `ResolveAsync` methode voor het `servicePartitionResolver` object. `servicePartitionResolver` is gedefinieerd als
    
    ```csharp
    private readonly ServicePartitionResolver servicePartitionResolver = ServicePartitionResolver.GetDefault();
    ```
    
    De `ResolveAsync` -methode gebruikt de service-URI, de partitie sleutel en het annulerings token als para meters. De service-URI voor de verwerkings service is `fabric:/AlphabetPartitions/Processing` . Daarna krijgen we het eind punt van de partitie.
    
    ```csharp
    ResolvedServiceEndpoint ep = partition.GetEndpoint()
    ```
    
    Ten slotte maken we de eind punt-URL plus de query reeks en roepen we de verwerkings service aan.
    
    ```csharp
    JObject addresses = JObject.Parse(ep.Address);
    string primaryReplicaAddress = (string)addresses["Endpoints"].First();
    
    UriBuilder primaryReplicaUriBuilder = new UriBuilder(primaryReplicaAddress);
    primaryReplicaUriBuilder.Query = "lastname=" + lastname;
    
    string result = await this.httpClient.GetStringAsync(primaryReplicaUriBuilder.Uri);
    ```
    
    Zodra de verwerking is voltooid, wordt de uitvoer teruggeschreven.
15. De laatste stap is het testen van de service. Visual Studio gebruikt toepassings parameters voor lokale en Cloud implementatie. Als u de service met 26 partities lokaal wilt testen, moet u het `Local.xml` bestand bijwerken in de map Application parameters van het AlphabetPartitions-project, zoals hieronder wordt weer gegeven:
    
    ```xml
    <Parameters>
      <Parameter Name="Processing_PartitionCount" Value="26" />
      <Parameter Name="WebApi_InstanceCount" Value="1" />
    </Parameters>
    ```
16. Nadat u de implementatie hebt voltooid, kunt u de service en alle partities ervan controleren in de Service Fabric Explorer.
    
    ![Scherm afbeelding Service Fabric Explorer](./media/service-fabric-concepts-partitioning/sfxpartitions.png)
17. In een browser kunt u de partitionatie logica testen door in te voeren `http://localhost:8081/?lastname=somename` . U ziet dat elke achternaam die begint met dezelfde letter wordt opgeslagen in dezelfde partitie.
    
    ![Scherm afbeelding browser](./media/service-fabric-concepts-partitioning/samplerunning.png)

De volledige oplossing van de code die in dit artikel wordt gebruikt, is hier beschikbaar: https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions .

## <a name="next-steps"></a>Volgende stappen
Meer informatie over Service Fabric Services:

* [Verbinding maken en communiceren met Services in Service Fabric](service-fabric-connect-and-communicate-with-services.md)
* [Beschik baarheid van Service Fabric Services](service-fabric-availability-services.md)
* [Schaal baarheid van Service Fabric Services](service-fabric-concepts-scalability.md)

[wikipartition]: https://en.wikipedia.org/wiki/Partition_(database)

[1]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog-2.png

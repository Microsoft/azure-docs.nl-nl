---
title: Verbinding maken en communiceren met Services in azure Service Fabric
description: Meer informatie over het oplossen, verbinden en communiceren met Services in Service Fabric.
ms.topic: conceptual
ms.date: 11/01/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 11f525eba89dc963deee0ba9a86566361ef644de
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96576295"
---
# <a name="connect-and-communicate-with-services-in-service-fabric"></a>Verbinding maken en communiceren met Services in Service Fabric
In Service Fabric wordt een service ergens in een Service Fabric cluster uitgevoerd, meestal verdeeld over meerdere Vm's. Het kan worden verplaatst van de ene locatie naar de andere, hetzij door de eigenaar van de service, hetzij automatisch door Service Fabric. Services zijn niet statisch gebonden aan een bepaalde machine of een bepaald adres.

Een Service Fabric-toepassing bestaat over het algemeen uit een groot aantal verschillende services, waarbij elke service een gespecialiseerde taak uitvoert. Deze services kunnen met elkaar communiceren om een volledige functie te vormen, zoals het weer geven van verschillende onderdelen van een webtoepassing. Er zijn ook client toepassingen die verbinding maken met en communiceren met Services. In dit document wordt beschreven hoe u communicatie met en tussen uw services instelt in Service Fabric.

## <a name="bring-your-own-protocol"></a>Uw eigen protocol meenemen
Service Fabric helpt de levens cyclus van uw services te beheren, maar maakt geen beslissingen over wat uw services doen. Dit omvat communicatie. Wanneer uw service wordt geopend door Service Fabric, is dat de mogelijkheid van de service om een eind punt in te stellen voor inkomende aanvragen, met behulp van het gewenste protocol of elke communicatie stack. Uw service luistert op een normaal **IP: poort** adres met behulp van een adresserings schema, zoals een URI. Meerdere service-exemplaren of replica's kunnen een hostproces delen. in dat geval moeten ze verschillende poorten gebruiken of een mechanisme voor het delen van de poort gebruiken, zoals het http.sys kernelstuurprogramma in Windows. In beide gevallen moet elk service-exemplaar of elke replica in een host proces uniek zijn.

![Service-eind punten][1]

## <a name="service-discovery-and-resolution"></a>Service detectie en-oplossing
In een gedistribueerd systeem kunnen services van de ene machine naar de andere worden verplaatst. Dit kan verschillende oorzaken hebben, waaronder resource verdeling, upgrades, failovers of uitschalen. Dit betekent dat service-eindpunt adressen veranderen wanneer de service wordt verplaatst naar knoop punten met verschillende IP-adressen, en kan worden geopend op verschillende poorten als de service een dynamisch geselecteerde poort gebruikt.

![Distributie van services][7]

Service Fabric biedt een detectie-en oplossings service die de Naming Service wordt genoemd. De Naming Service onderhoudt een tabel die benoemde service-exemplaren toewijst aan de eindpunt adressen waarop ze Luis teren. Alle benoemde service-exemplaren in Service Fabric hebben unieke namen die als Uri's worden weer gegeven, bijvoorbeeld `"fabric:/MyApplication/MyService"` . De naam van de service wordt niet gewijzigd gedurende de levens duur van de service. Dit zijn alleen de eindpunt adressen die kunnen veranderen wanneer services worden verplaatst. Dit is vergelijkbaar met websites die constante Url's hebben, maar waarbij het IP-adres kan veranderen. En net als bij DNS op het web, waarmee website-Url's worden omgezet naar IP-adressen, heeft Service Fabric een registratie-instantie waarmee service namen worden toegewezen aan hun eindpunt adres.

![Diagram dat laat zien dat Service Fabric een registratie-instantie heeft waarmee service namen worden toegewezen aan hun eindpunt adres.][2]

Als u services wilt omzetten en er verbinding mee wilt maken, moet u de volgende stappen uitvoeren in een lus:

* **Oplossen**: Haal het eind punt op dat een service heeft gepubliceerd vanuit het Naming Service.
* **Connect**: Maak verbinding met de service via het protocol dat wordt gebruikt voor dat eind punt.
* **Opnieuw proberen**: een verbindings poging kan om een aantal redenen mislukken, bijvoorbeeld als de service is verplaatst sinds de laatste keer dat het eindpunt adres is opgelost. In dat geval moeten de voor gaande stappen voor oplossen en verbinden opnieuw worden geprobeerd, en wordt deze cyclus herhaald totdat de verbinding is geslaagd.

## <a name="connecting-to-other-services"></a>Verbinding maken met andere services
Services die met elkaar in een cluster verbinding maken, hebben doorgaans rechtstreeks toegang tot de eind punten van andere services, omdat de knoop punten in een cluster zich op hetzelfde lokale netwerk bevinden. Het is eenvoudiger om verbinding te maken tussen services, Service Fabric extra services biedt die gebruikmaken van de Naming Service. Een DNS-service en een reverse proxy service.


### <a name="dns-service"></a>DNS-service
Omdat veel services, met name in container Services, een bestaande URL-naam kunnen hebben, is het mogelijk om deze op te lossen met behulp van het standaard-DNS-protocol (in plaats van het Naming Service-Protocol). Dit is met name in de scenario's ' lift and Shift '. Dit is precies wat de DNS-service doet. Hiermee kunt u DNS-namen toewijzen aan een service naam en daarom IP-adres van het eind punt omzetten. 

Zoals in het volgende diagram wordt weer gegeven, wijst de DNS-service, die wordt uitgevoerd in het Service Fabric cluster, DNS-namen toe aan service namen die vervolgens worden omgezet door de Naming Service om de eindpunt adressen te retour neren waarmee verbinding moet worden gemaakt. De DNS-naam voor de service wordt verschaft op het moment dat deze wordt gemaakt. 

![Diagram dat laat zien hoe de DNS-service, wanneer dit wordt uitgevoerd in het Service Fabric cluster, DNS-namen toewijst aan service namen die vervolgens worden omgezet door de Naming Service om de eindpunt adressen te retour neren waarmee verbinding moet worden gemaakt.][9]

Zie de [DNS-service in Azure service Fabric-](service-fabric-dnsservice.md) artikel voor meer informatie over het gebruik van de DNS-service.

### <a name="reverse-proxy-service"></a>Reverse proxy service
De services voor omgekeerde proxy adressen in het cluster die HTTP-eind punten, inclusief HTTPS, weer gegeven. De omgekeerde proxy vereenvoudigt het aanroepen van andere services en de bijbehorende methoden door een specifieke URI-indeling te hebben en de stappen voor het oplossen, verbinden en opnieuw proberen te verwerken die vereist zijn voor de ene service om met een andere te communiceren met behulp van de Naming Service. Met andere woorden, de Naming Service van u wordt verborgen wanneer u andere services aanroept door dit net zo eenvoudig te maken als het aanroepen van een URL.

![Diagram dat laat zien hoe de services voor omgekeerde proxy's in het cluster de HTTP-eind punten, inclusief HTTPS, beschikbaar stelt.][10]

Zie voor meer informatie over het gebruik van de reverse proxy-service [reverse proxy in Azure service Fabric](service-fabric-reverseproxy.md) -artikel.

## <a name="connections-from-external-clients"></a>Verbindingen van externe clients
Services die met elkaar in een cluster verbinding maken, hebben doorgaans rechtstreeks toegang tot de eind punten van andere services, omdat de knoop punten in een cluster zich op hetzelfde lokale netwerk bevinden. In sommige omgevingen kan een cluster echter achter een load balancer dat het binnenkomend verkeer via een beperkt aantal poorten routeert. In dergelijke gevallen kunnen services nog steeds communiceren met elkaar en adressen omzetten met behulp van de Naming Service, maar er moeten extra stappen worden ondernomen om externe clients toe te staan verbinding te maken met Services.

## <a name="service-fabric-in-azure"></a>Service Fabric in azure
Een Service Fabric cluster in azure wordt achter een Azure Load Balancer geplaatst. Alle externe verkeer naar het cluster moet worden door gegeven via de load balancer. De load balancer stuurt automatisch verkeer dat binnenkomt op een bepaalde poort naar een wille keurig *knoop punt* waarop dezelfde poort is geopend. De Azure Load Balancer weet alleen over poorten die op de *knoop punten* zijn geopend. het is niet bekend over poorten die door afzonderlijke *Services* worden geopend.

![Azure Load Balancer-en Service Fabric-topologie][3]

Als u bijvoorbeeld extern verkeer op poort **80** wilt accepteren, moet u de volgende zaken configureren:

1. Schrijf een service die luistert op poort 80. Configureer poort 80 in de ServiceManifest.xml van de service en open een listener in de service, bijvoorbeeld een zelf-hostende webserver.

    ```xml
    <Resources>
        <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="80" />
        </Endpoints>
    </Resources>
    ```
    ```csharp
        class HttpCommunicationListener : ICommunicationListener
        {
            ...

            public Task<string> OpenAsync(CancellationToken cancellationToken)
            {
                EndpointResourceDescription endpoint =
                    serviceContext.CodePackageActivationContext.GetEndpoint("WebEndpoint");

                string uriPrefix = $"{endpoint.Protocol}://+:{endpoint.Port}/myapp/";

                this.httpListener = new HttpListener();
                this.httpListener.Prefixes.Add(uriPrefix);
                this.httpListener.Start();

                string publishUri = uriPrefix.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);
                return Task.FromResult(publishUri);
            }

            ...
        }

        class WebService : StatelessService
        {
            ...

            protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
            {
                return new[] { new ServiceInstanceListener(context => new HttpCommunicationListener(context))};
            }

            ...
        }
    ```
    ```java
        class HttpCommunicationlistener implements CommunicationListener {
            ...

            @Override
            public CompletableFuture<String> openAsync(CancellationToken arg0) {
                EndpointResourceDescription endpoint =
                    this.serviceContext.getCodePackageActivationContext().getEndpoint("WebEndpoint");
                try {
                    HttpServer server = com.sun.net.httpserver.HttpServer.create(new InetSocketAddress(endpoint.getPort()), 0);
                    server.start();

                    String publishUri = String.format("http://%s:%d/",
                        this.serviceContext.getNodeContext().getIpAddressOrFQDN(), endpoint.getPort());
                    return CompletableFuture.completedFuture(publishUri);
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }

            ...
        }

        class WebService extends StatelessService {
            ...

            @Override
            protected List<ServiceInstanceListener> createServiceInstanceListeners() {
                <ServiceInstanceListener> listeners = new ArrayList<ServiceInstanceListener>();
                listeners.add(new ServiceInstanceListener((context) -> new HttpCommunicationlistener(context)));
                return listeners;       
            }

            ...
        }
    ```
2. Maak een Service Fabric cluster in Azure en geef poort **80** op als aangepaste eindpunt poort voor het knooppunt type dat als host moet fungeren voor de service. Als u meer dan één knooppunt type hebt, kunt u een *plaatsings beperking* instellen op de service om ervoor te zorgen dat deze alleen wordt uitgevoerd op het knooppunt type waarvoor de aangepaste eindpunt poort is geopend.

    ![Een poort openen in een knooppunt type][4]
3. Zodra het cluster is gemaakt, configureert u de Azure Load Balancer in de resource groep van het cluster voor het door sturen van verkeer op poort 80. Bij het maken van een cluster via de Azure Portal, wordt dit automatisch ingesteld voor elke aangepaste eindpunt poort die is geconfigureerd.

    ![Scherm opname van het veld backend-poort onder regels voor taak verdeling.][5]
4. De Azure Load Balancer gebruikt een test om te bepalen of het verkeer naar een bepaald knoop punt moet worden verzonden. De test controleert periodiek een eind punt op elk knoop punt om te bepalen of het knoop punt reageert. Als de test geen reactie heeft ontvangen na een geconfigureerd aantal keren, stopt de load balancer het verzenden van verkeer naar dat knoop punt. Bij het maken van een cluster via de Azure Portal, wordt er automatisch een test ingesteld voor elke aangepaste eindpunt poort die is geconfigureerd.

    ![Verkeer door sturen in de Azure Load Balancer][8]

Het is belang rijk te weten dat de Azure Load Balancer en de test alleen weten over de *knoop punten*, niet de *Services* die worden uitgevoerd op de knoop punten. De Azure Load Balancer stuurt altijd verkeer naar knoop punten die op de test reageren. Daarom moet er rekening mee worden gehouden om ervoor te zorgen dat de services beschikbaar zijn op de knoop punten die op de test kunnen reageren.

## <a name="reliable-services-built-in-communication-api-options"></a>Reliable Services: ingebouwde communicatie-API-opties
Het Reliable Services Framework wordt geleverd met verschillende vooraf ontwikkelde communicatie opties. De beslissing over welke het voor u het meest geschikt is, is afhankelijk van de keuze van het programmeer model, het communicatie raamwerk en de programmeer taal waarin uw services zijn geschreven.

* **Geen specifiek Protocol:**  Als u geen specifieke communicatie raamwerk hebt, maar u al iets aan de slag wilt gaan, kunt u de ideale optie voor externe procedures gebruiken, waardoor het Reliable Services en Reliable Actors sterk getypte aanroepen op [afstand worden uitgevoerd](service-fabric-reliable-services-communication-remoting.md). Dit is de eenvoudigste en snelste manier om aan de slag te gaan met Service communicatie. Externe communicatie van service verzorgt de omzetting van service adressen, verbindingen, opnieuw proberen en fout afhandeling. Dit is beschikbaar voor zowel C#-als Java-toepassingen.
* **Http**: voor neutraal-communicatie biedt http een industrie standaard keuze met hulpprogram MA'S en HTTP-servers die beschikbaar zijn in veel verschillende talen, die allemaal door service Fabric worden ondersteund. Services kunnen gebruikmaken van elke beschik bare HTTP-stack, waaronder [ASP.net Web API](./service-fabric-reliable-services-communication-aspnetcore.md) voor C#-toepassingen. Clients die zijn geschreven in C#, kunnen gebruikmaken `ICommunicationClient` `ServicePartitionClient` van de klassen en, terwijl u voor Java de `CommunicationClient` klassen and gebruikt `FabricServicePartitionClient` , [voor service omzetting, http-verbindingen en lussen](service-fabric-reliable-services-communication.md).
* **WCF**: als u bestaande code hebt die gebruikmaakt van WCF als uw communicatie raamwerk, kunt u de `WcfCommunicationListener` voor de server zijde en `WcfCommunicationClient` en `ServicePartitionClient` klassen voor de client gebruiken. Dit is echter alleen beschikbaar voor C#-toepassingen op op Windows gebaseerde clusters. Zie dit artikel over [op WCF gebaseerde implementatie van de communicatie stack](service-fabric-reliable-services-communication-wcf.md)voor meer informatie.

## <a name="using-custom-protocols-and-other-communication-frameworks"></a>Aangepaste protocollen en andere communicatie raamwerken gebruiken
Services kunnen gebruikmaken van elk protocol of Framework voor communicatie, of het nu een aangepast binair protocol via TCP-sockets is, of het streamen van gebeurtenissen via [azure Event hubs](https://azure.microsoft.com/services/event-hubs/) of [Azure IOT hub](https://azure.microsoft.com/services/iot-hub/). Service Fabric biedt communicatie-Api's waarmee u uw communicatie stack kunt aansluiten, terwijl al het werk dat u wilt detecteren en verbinding maakt, van u wordt afgeleid. Raadpleeg dit artikel over het [betrouw bare communicatie model voor services](service-fabric-reliable-services-communication.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over de concepten en Api's die beschikbaar zijn in het [communicatie model van reliable Services](service-fabric-reliable-services-communication.md). Ga vervolgens snel aan de slag met [service Remoting](service-fabric-reliable-services-communication-remoting.md) of Ga dieper in voor meer informatie over het schrijven van een communicatie-listener met behulp [van Web API met OWIN Self-Host](./service-fabric-reliable-services-communication-aspnetcore.md).

[1]: ./media/service-fabric-connect-and-communicate-with-services/serviceendpoints.png
[2]: ./media/service-fabric-connect-and-communicate-with-services/namingservice.png
[3]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancertopology.png
[4]: ./media/service-fabric-connect-and-communicate-with-services/nodeport.png
[5]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerport.png
[7]: ./media/service-fabric-connect-and-communicate-with-services/distributedservices.png
[8]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerprobe.png
[9]: ./media/service-fabric-connect-and-communicate-with-services/dns.png
[10]: ./media/service-fabric-reverseproxy/internal-communication.png

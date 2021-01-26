---
title: Azure Service Fabric reverse-proxy
description: Gebruik Service Fabric omgekeerde proxy voor communicatie met micro services van binnen en buiten het cluster.
author: BharatNarasimman
ms.topic: conceptual
ms.date: 11/03/2017
ms.author: bharatn
ms.custom: devx-track-csharp
ms.openlocfilehash: 012a49762596adee39988614ed0c1020cd8bc104
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791101"
---
# <a name="reverse-proxy-in-azure-service-fabric"></a>Reverse proxy in azure Service Fabric
Met omgekeerde proxy die in azure Service Fabric is ingebouwd, kunnen micro services die worden uitgevoerd in een Service Fabric cluster worden gedetecteerd en gecommuniceerd met andere services met http-eind punten.

## <a name="microservices-communication-model"></a>Communicatie model micro Services
Micro Services in Service Fabric worden uitgevoerd op een subset van knoop punten in het cluster en kunnen om verschillende redenen worden gemigreerd tussen de knoop punten. Als gevolg hiervan kunnen de eind punten voor micro services dynamisch worden gewijzigd. Micro service moet de volgende stappen uitvoeren om te ontdekken en te communiceren met andere services in het cluster:

1. Los de service locatie op via de naamgevings service.
2. Maak verbinding met de service.
3. De voor gaande stappen in een lus afronden waarmee service omzetting wordt geïmplementeerd en beleid voor opnieuw proberen wordt toegepast op verbindings fouten

Zie [verbinding maken en communiceren met Services](service-fabric-connect-and-communicate-with-services.md)voor meer informatie.

### <a name="communicating-by-using-the-reverse-proxy"></a>Communiceren met behulp van de omgekeerde proxy
Omgekeerde proxy is een service die op elk knoop punt wordt uitgevoerd en die de eindpunt resolutie, automatische nieuwe pogingen en andere verbindings fouten afhandelt namens client services. Omgekeerde proxy kan worden geconfigureerd voor het Toep assen van verschillende beleids regels wanneer deze aanvragen van client services verwerken. Door gebruik te maken van een reverse-proxy kan de client service alle HTTP-communicatie bibliotheken aan de client zijde gebruiken en is er geen speciale oplossing vereist en wordt de logica opnieuw geprobeerd in de service. 

Met omgekeerde proxy worden een of meer eind punten op het lokale knoop punt voor client services gebruikt voor het verzenden van aanvragen naar andere services.

![Interne communicatie][1]

> [!NOTE]
> **Ondersteunde platforms**
>
> Reverse proxy in Service Fabric ondersteunt momenteel de volgende platformen
> * *Windows-cluster*: Windows 8 en hoger of windows server 2012 en hoger
> * *Linux-cluster*: reverse proxy is momenteel niet beschikbaar voor Linux-clusters
>

## <a name="reaching-microservices-from-outside-the-cluster"></a>Micro services van buiten het cluster bereiken
Het standaard externe communicatie model voor micro Services is een opt-in-model waarbij elke service niet rechtstreeks vanuit externe clients kan worden geopend. [Azure Load Balancer](../load-balancer/load-balancer-overview.md), een netwerk grens tussen micro Services en externe clients, voert Network Address Translation en stuurt externe aanvragen door naar interne IP: poort-eind punten. Als u het eind punt van een micro service rechtstreeks toegankelijk wilt maken voor externe clients, moet u eerst Load Balancer configureren om verkeer door te sturen naar elke poort die door de service in het cluster wordt gebruikt. Daarnaast hebben de meeste micro Services, met name micro Services, niet live op alle knoop punten van het cluster. De micro Services kunnen tussen knoop punten in de failover worden verplaatst. In dergelijke gevallen kan Load Balancer niet in staat zijn om de locatie van het doel knooppunt te bepalen van de replica's waarnaar het verkeer moet worden doorgestuurd.

### <a name="reaching-microservices-via-the-reverse-proxy-from-outside-the-cluster"></a>Micro Services bereiken via de omgekeerde proxy van buiten het cluster
In plaats van de poort van een afzonderlijke service in Load Balancer te configureren, kunt u alleen de poort van de omgekeerde proxy in Load Balancer configureren. Met deze configuratie kunnen clients buiten het cluster services binnen het cluster bereiken met behulp van de omgekeerde proxy zonder extra configuratie.

![Externe communicatie][0]

> [!WARNING]
> Wanneer u de poort van de reverse proxy in Load Balancer configureert, zijn alle micro Services in het cluster die een HTTP-eind punt beschikbaar maken, adresseerbaar van buiten het cluster. Dit betekent dat de micro services die intern bedoeld zijn, kunnen worden gedetecteerd door een bepaalde kwaadwillende gebruiker. Dit biedt mogelijk ernstige beveiligings problemen die kunnen worden misbruikt. bijvoorbeeld:
>
> * Een kwaadwillende gebruiker kan een DOS-aanval (Denial of service) starten door herhaaldelijk een interne service aan te roepen die geen voldoende harde kwets baarheid heeft.
> * Een kwaadwillende gebruiker kan misvormde pakketten leveren aan een interne service, wat leidt tot onbedoeld gedrag.
> * Een service die als intern is bedoeld, kan persoonlijke of gevoelige informatie retour neren die niet bedoeld is om te worden blootgesteld aan services buiten het cluster, waardoor deze gevoelige informatie beschikbaar wordt gesteld aan een kwaadwillende gebruiker. 
>
> Zorg ervoor dat u de mogelijke beveiligings implicaties voor uw cluster en de apps die erop worden uitgevoerd, volledig begrijpt en verhelpt voordat u de omgekeerde proxy poort openbaar maakt. 
>


## <a name="uri-format-for-addressing-services-by-using-the-reverse-proxy"></a>URI-indeling voor het adresseren van services met behulp van de omgekeerde proxy
De omgekeerde proxy gebruikt een specifieke URI-indeling (Uniform Resource Identifier) om de service partitie te identificeren waarnaar de binnenkomende aanvraag moet worden doorgestuurd:

```
http(s)://<Cluster FQDN | internal IP>:Port/<ServiceInstanceName>/<Suffix path>?PartitionKey=<key>&PartitionKind=<partitionkind>&ListenerName=<listenerName>&TargetReplicaSelector=<targetReplicaSelector>&Timeout=<timeout_in_seconds>
```

* **http (s):** De omgekeerde proxy kan worden geconfigureerd voor het accepteren van HTTP-of HTTPS-verkeer. Voor het door sturen van HTTPS, raadpleegt u [verbinding maken met een beveiligde service met de reverse proxy](service-fabric-reverseproxy-configure-secure-communication.md) nadat u de installatie van de proxy ongedaan hebt gemaakt om te Luis teren op https
* **Cluster Fully Qualified Domain Name (FQDN) | intern IP-adres:** Voor externe clients kunt u de omgekeerde proxy configureren zodat deze bereikbaar is via het cluster domein, zoals mycluster.eastus.cloudapp.azure.com. Standaard wordt de omgekeerde proxy uitgevoerd op elk knoop punt. Voor intern verkeer kan de omgekeerde proxy worden bereikt op localhost of op een IP-adres van een intern knoop punt, zoals 10.0.0.1.
* **Poort:** Dit is de poort, zoals 19081, die is opgegeven voor de omgekeerde proxy.
* **ServiceInstanceName:** Dit is de volledig gekwalificeerde naam van het geïmplementeerde service-exemplaar dat u probeert te bereiken zonder ' Fabric:/' niveaus. Als u bijvoorbeeld de *Fabric:/Mijntoep/myservice/* service wilt bereiken, gebruikt u *MyApp/myservice*.

    De naam van het service-exemplaar is hoofdletter gevoelig. Het gebruik van een andere behuizing voor de naam van het service-exemplaar in de URL zorgt ervoor dat de aanvragen mislukken met 404 (niet gevonden).
* **Pad naar achtervoegsel:** Dit is het werkelijke URL-pad, zoals *myapi/values/add/3*, voor de service waarmee u verbinding wilt maken.
* **PartitionKey:** Voor een gepartitioneerde service is dit de berekende partitie sleutel van de partitie die u wilt bereiken. Houd er rekening mee dat dit *niet* de GUID van de partitie-id is. Deze para meter is niet vereist voor services die gebruikmaken van het Singleton-partitie schema.
* **PartitionKind:** Dit is het schema voor service partities. Dit kan ' Int64Range ' of ' name ' zijn. Deze para meter is niet vereist voor services die gebruikmaken van het Singleton-partitie schema.
* **ListenerName** De eind punten van de service hebben de vorm {"eind punten": {"Listener1": "Endpoint1", "Listener2": "Endpoint2"...}}. Wanneer de service meerdere eind punten beschikbaar stelt, wordt het eind punt geïdentificeerd waarnaar de client aanvraag moet worden doorgestuurd. Dit kan worden wegge laten als de service slechts één listener heeft.
* **TargetReplicaSelector** Hiermee wordt aangegeven hoe de doel replica of-instantie moet worden geselecteerd.
  * Wanneer de doel service stateful is, kan de TargetReplicaSelector een van de volgende zijn: ' PrimaryReplica ', ' RandomSecondaryReplica ' of ' RandomReplica '. Als deze para meter niet wordt opgegeven, is de standaard waarde ' PrimaryReplica '.
  * Wanneer de doel service stateless is, pickt reverse proxy een wille keurig exemplaar van de service partitie om de aanvraag door te sturen naar.
* **Time-out:**  Hiermee geeft u de time-out op voor de HTTP-aanvraag die is gemaakt door de omgekeerde proxy naar de service namens de client aanvraag. De standaard waarde is 120 seconden. Dit is een optionele para meter.

### <a name="example-usage"></a>Gebruiksvoorbeelden
Als voor beeld gaat u naar de service *Fabric:/MyApp/MyService* waarmee een HTTP-listener wordt geopend op de volgende URL:

```
http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/
```

Hieronder vindt u de resources voor de service:

* `/index.html`
* `/api/users/<userId>`

Als de service het Singleton-partitie schema gebruikt, zijn de *PartitionKey* -en *PartitionKind* -query teken reeks parameters niet vereist en de service kan worden bereikt door de gateway te gebruiken als:

* Extern `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService`
* Interne `http://localhost:19081/MyApp/MyService`

Als de service gebruikmaakt van het uniforme Int64-partitie schema, moeten de *PartitionKey* -en *PartitionKind* -query teken reeks parameters worden gebruikt om een partitie van de service te bereiken:

* Extern `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`
* Interne `http://localhost:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`

Als u de resources die door de service worden weer gegeven, wilt bereiken, plaatst u het bronpad na de service naam in de URL:

* Extern `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService/index.html?PartitionKey=3&PartitionKind=Int64Range`
* Interne `http://localhost:19081/MyApp/MyService/api/users/6?PartitionKey=3&PartitionKind=Int64Range`

De gateway stuurt deze aanvragen vervolgens door naar de URL van de service:

* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/index.html`
* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/api/users/6`

## <a name="special-handling-for-port-sharing-services"></a>Speciale verwerking voor services voor het delen van poorten
De Service Fabric reverse-proxy probeert een service adres opnieuw op te lossen en voer de aanvraag opnieuw uit wanneer een service niet kan worden bereikt. Over het algemeen geldt dat als een service niet kan worden bereikt, het service-exemplaar of de replica is verplaatst naar een ander knoop punt als onderdeel van de normale levens cyclus. Als dit gebeurt, kan de omgekeerde proxy een netwerk verbindings fout ontvangen die aangeeft dat een eind punt niet meer is geopend op het oorspronkelijk opgeloste adres.

Replica's of service-exemplaren kunnen echter een hostproces delen en kunnen ook een poort delen als deze wordt gehost door een webserver op basis van http.sys, waaronder:

* [System .net. HttpListener](/dotnet/api/system.net.httplistener)
* [Weblistener ASP.NET Core](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
* [Katana](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OwinSelfHost/)

In dit geval is het waarschijnlijk dat de webserver beschikbaar is in het hostproces en reageert op aanvragen, maar het omgezette service-exemplaar of de replica is niet meer beschikbaar op de host. In dit geval ontvangt de gateway een HTTP 404-antwoord van de webserver. Een HTTP 404-antwoord kan dus twee verschillende betekenissen hebben:

- Case #1: het service adres is juist, maar de resource die de aangevraagde gebruiker niet bestaat.
- Case #2: het service adres is onjuist en de resource die de gebruiker heeft aangevraagd, kan bestaan op een ander knoop punt.

De eerste aanvraag is een normale HTTP 404, die als een gebruikers fout wordt beschouwd. In het tweede geval heeft de gebruiker echter een resource aangevraagd die wel bestaat. De omgekeerde proxy kan deze niet vinden omdat de service zelf is verplaatst. De omgekeerde proxy moet het adres opnieuw omzetten en de aanvraag opnieuw proberen.

De omgekeerde proxy vereist daarom een manier om onderscheid te maken tussen deze twee gevallen. Om dat onderscheid te maken, is een hint van de server vereist.

* De omgekeerde proxy veronderstelt standaard #2 en probeert de aanvraag opnieuw te verhelpen.
* De service moet de volgende HTTP-antwoord header retour neren om aan te geven dat de reverse proxy is #1:

  `X-ServiceFabric : ResourceNotFound`

Deze HTTP-antwoord header duidt op een normale HTTP 404-situatie waarin de aangevraagde bron niet bestaat en probeert de omgekeerde proxy het service adres niet opnieuw op te lossen.

## <a name="special-handling-for-services-running-in-containers"></a>Speciale verwerking voor services die in containers worden uitgevoerd

Voor services die in containers worden uitgevoerd, kunt u de omgevings variabele gebruiken `Fabric_NodeIPOrFQDN` om de [omgekeerde proxy-URL](#uri-format-for-addressing-services-by-using-the-reverse-proxy) te maken, zoals in de volgende code:

```csharp
    var fqdn = Environment.GetEnvironmentVariable("Fabric_NodeIPOrFQDN");
    var serviceUrl = $"http://{fqdn}:19081/DockerSFApp/UserApiContainer";
```
Voor het lokale cluster `Fabric_NodeIPOrFQDN` is standaard ingesteld op localhost. Start het lokale cluster met de `-UseMachineName` para meter om ervoor te zorgen dat containers een omgekeerde proxy kunnen bereiken die op het knoop punt wordt uitgevoerd. Zie [uw ontwikkelaars omgeving configureren voor het opsporen van fouten in containers](service-fabric-how-to-debug-windows-containers.md#configure-your-developer-environment-to-debug-containers)voor meer informatie.

Voor Service Fabric services die worden uitgevoerd binnen docker-samenstel bare containers is een speciale docker-Compose. yml- *poorten sectie* http: of https: configuratie vereist. Zie voor meer informatie [docker opstellen implementatie ondersteuning in Azure service Fabric](service-fabric-docker-compose.md).

## <a name="next-steps"></a>Volgende stappen
* [Stel een omgekeerde proxy in op een cluster en configureer](service-fabric-reverseproxy-setup.md)deze.
* [Door sturen naar beveiligde HTTP-service instellen met de omgekeerde proxy](service-fabric-reverseproxy-configure-secure-communication.md)
* [Omgekeerde-proxygebeurtenissen onderzoeken](service-fabric-reverse-proxy-diagnostics.md)
* Bekijk een voor beeld van HTTP-communicatie tussen services in een [voorbeeld project op github](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [Externe procedure aanroepen met Reliable Services externe toegang](service-fabric-reliable-services-communication-remoting.md)
* [Web-API die gebruikmaakt van OWIN in Reliable Services](./service-fabric-reliable-services-communication-aspnetcore.md)
* [WCF-communicatie met behulp van Reliable Services](service-fabric-reliable-services-communication-wcf.md)

[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png

---
title: Externe service met behulp van java in azure Service Fabric
description: Met Service Fabric externe toegang kunnen clients en services communiceren met Java-Services met behulp van een externe procedure aanroep.
author: PavanKunapareddyMSFT
ms.topic: conceptual
ms.date: 06/30/2017
ms.custom: devx-track-java
ms.author: pakunapa
ms.openlocfilehash: d53d20510db70d81aab796efab48de40c880bb3a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87316121"
---
# <a name="service-remoting-in-java-with-reliable-services"></a>Externe service in Java met Reliable Services
> [!div class="op_single_selector"]
> * [C# op Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java op Linux](service-fabric-reliable-services-communication-remoting-java.md)
>
>

Voor services die niet zijn gekoppeld aan een bepaald communicatie protocol of stack, zoals WebAPI, Windows Communication Foundation (WCF) of anderen, biedt het Reliable Services Framework een extern mechanisme om snel en eenvoudig externe procedure aanroepen voor services in te stellen.  In dit artikel wordt beschreven hoe u externe procedure aanroepen instelt voor services die zijn geschreven met Java.

## <a name="set-up-remoting-on-a-service"></a>Externe toegang instellen voor een service
Het instellen van externe toegang voor een service geschiedt in twee eenvoudige stappen:

1. Maak een interface voor uw service die u wilt implementeren. Deze interface definieert de methoden die beschikbaar zijn voor een externe procedure aanroep voor uw service. De methoden moeten asynchrone methoden van het taak resultaat hebben. De interface moet `microsoft.serviceFabric.services.remoting.Service` worden geïmplementeerd om aan te geven dat de service een interface voor externe toegang heeft.
2. Gebruik een externe listener in uw service. Dit is een `CommunicationListener` implementatie die mogelijkheden biedt voor externe communicatie. `FabricTransportServiceRemotingListener` kan worden gebruikt om een externe listener te maken met behulp van het standaard externe transport protocol.

De volgende stateless service biedt bijvoorbeeld een enkele methode om Hallo wereld te verkrijgen via een externe procedure aanroep.

```java
import java.util.ArrayList;
import java.util.concurrent.CompletableFuture;
import java.util.List;
import microsoft.servicefabric.services.communication.runtime.ServiceInstanceListener;
import microsoft.servicefabric.services.remoting.Service;
import microsoft.servicefabric.services.runtime.StatelessService;

public interface MyService extends Service {
    CompletableFuture<String> helloWorldAsync();
}

class MyServiceImpl extends StatelessService implements MyService {
    public MyServiceImpl(StatelessServiceContext context) {
       super(context);
    }

    public CompletableFuture<String> helloWorldAsync() {
        return CompletableFuture.completedFuture("Hello!");
    }

    @Override
    protected List<ServiceInstanceListener> createServiceInstanceListeners() {
        ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
        listeners.add(new ServiceInstanceListener((context) -> {
            return new FabricTransportServiceRemotingListener(context,this);
        }));
        return listeners;
    }
}
```

> [!NOTE]
> De argumenten en de retour typen in de service-interface kunnen eenvoudige, complexe of aangepaste typen zijn, maar ze moeten serialiseerbaar zijn.
>
>

## <a name="call-remote-service-methods"></a>Externe service methoden aanroepen
Het aanroepen van methoden voor een service met behulp van de externe stack wordt uitgevoerd door middel van een lokale proxy voor de service via de- `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` klasse. Met de- `ServiceProxyBase` methode wordt een lokale proxy gemaakt met behulp van de interface die door de service wordt geïmplementeerd. Met die proxy kunt u eenvoudig methoden op de interface aanroepen op afstand.

```java

MyService helloWorldClient = ServiceProxyBase.create(MyService.class, new URI("fabric:/MyApplication/MyHelloWorldService"));

CompletableFuture<String> message = helloWorldClient.helloWorldAsync();

```

Het externe Framework geeft uitzonde ringen door die zijn gegenereerd bij de service naar de client. De logica voor het verwerken van uitzonde ringen op de client met behulp van kunt u dus `ServiceProxyBase` direct afhandelen die de service genereert.

## <a name="service-proxy-lifetime"></a>Levens duur van Service proxy
Het maken van een ServiceProxy is een licht gewicht bewerking, zodat u zoveel mogelijk kunt maken. Service proxy-exemplaren kunnen opnieuw worden gebruikt zolang ze nodig zijn. Als een externe procedure aanroep een uitzonde ring genereert, kunt u nog steeds hetzelfde proxy-exemplaar gebruiken. Elk ServiceProxy bevat een communicatie-client die wordt gebruikt voor het verzenden van berichten via de kabel. Tijdens het aanroepen van externe aanroepen worden interne controles uitgevoerd om te bepalen of de communicatie client geldig is. Op basis van de resultaten van die controles wordt de communicatie client indien nodig opnieuw gemaakt. Als er een uitzonde ring optreedt, hoeft u daarom niet opnieuw te maken `ServiceProxy` .

### <a name="serviceproxyfactory-lifetime"></a>Levens duur ServiceProxyFactory
[FabricServiceProxyFactory](/java/api/microsoft.servicefabric.services.remoting.client.fabricserviceproxyfactory) is een Factory waarmee proxy voor verschillende externe interfaces wordt gemaakt. Als u API gebruikt `ServiceProxyBase.create` voor het maken van een proxy, maakt Framework een `FabricServiceProxyFactory` .
Het is handig om een hand matig te maken wanneer u [ServiceRemotingClientFactory](/java/api/microsoft.servicefabric.services.remoting.client.serviceremotingclientfactory) -eigenschappen wilt overschrijven.
Factory is een dure bewerking. `FabricServiceProxyFactory` onderhoudt cache van communicatie-clients.
De aanbevolen procedure is om zo lang mogelijk in de cache te plaatsen `FabricServiceProxyFactory` .

## <a name="remoting-exception-handling"></a>Afhandeling van externe uitzonde ringen
Alle externe uitzonde ringen die worden veroorzaakt door Service-API, worden teruggestuurd naar de client, hetzij RuntimeException of FabricException.

ServiceProxy verwerkt alle failover-uitzonde ringen voor de service partitie waarvoor deze is gemaakt. De eind punten worden opnieuw omgezet als er failover-uitzonde ringen zijn (geen tijdelijke uitzonde ringen) en de aanroep opnieuw probeert met het juiste eind punt. Aantal nieuwe pogingen voor failover-uitzonde ring is oneindig.
In het geval van TransientExceptions wordt de aanroep alleen opnieuw geprobeerd.

De standaard parameters voor nieuwe pogingen zijn geleverde door [OperationRetrySettings](/java/api/microsoft.servicefabric.services.communication.client.operationretrysettings).
U kunt deze waarden configureren door OperationRetrySettings object door te geven aan ServiceProxyFactory constructor.

## <a name="next-steps"></a>Volgende stappen
* [Communicatie beveiligen voor Reliable Services](service-fabric-reliable-services-secure-communication-java.md)

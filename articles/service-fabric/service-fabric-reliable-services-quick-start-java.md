---
title: Uw eerste betrouw bare service maken in Java
description: Inleiding tot het maken van een Microsoft Azure Service Fabric-toepassing met stateless en stateful Services in Java.
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-javai
ms.openlocfilehash: f67957d711958febdb01dfad0b3c44a92cb0bcfa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91535233"
---
# <a name="get-started-with-reliable-services-in-java"></a>Aan de slag met Reliable Services in Java
> [!div class="op_single_selector"]
> * [C# op Windows](service-fabric-reliable-services-quick-start.md)
> * [Java op Linux](service-fabric-reliable-services-quick-start-java.md)
>
>

In dit artikel worden de basis beginselen van Azure Service Fabric Reliable Services beschreven en wordt uitgelegd hoe u een eenvoudige, betrouw bare service toepassing maakt en implementeert die in Java is geschreven. 

## <a name="installation-and-setup"></a>Installatie en configuratie
Voordat u begint, moet u controleren of de Service Fabric-ontwikkel omgeving is ingesteld op uw machine.
Als u deze wilt instellen, gaat u naar aan de [slag met Mac](service-fabric-get-started-mac.md) of aan de slag met [Linux](service-fabric-get-started-linux.md).

## <a name="basic-concepts"></a>Basisbegrippen
Om aan de slag te gaan met Reliable Services hoeft u slechts enkele basis concepten te begrijpen:

* **Service type**: dit is de service-implementatie. Het wordt gedefinieerd door de klasse die u schrijft `StatelessService` , en alle andere code of afhankelijkheden die erin worden gebruikt, samen met een naam en versie nummer.
* **Benoemd service-exemplaar**: als u uw service wilt uitvoeren, maakt u benoemde instanties van uw service type, net zoals u object instanties van een klassetype maakt. Service-exemplaren bevinden zich in feite object instanties van uw service klasse die u schrijft.
* **Servicehost: de** benoemde service-exemplaren die u maakt, moeten binnen een host worden uitgevoerd. De servicehost is slechts een proces waarbij exemplaren van uw service kunnen worden uitgevoerd.
* **Service registratie**: registratie brengt alles samen. Het Service type moet worden geregistreerd bij de Service Fabric runtime in een servicehost om Service Fabric toe te staan dat er exemplaren van worden gemaakt om te worden uitgevoerd.  

## <a name="create-a-stateless-service"></a>Een stateless service maken
Maak eerst een Service Fabric-toepassing. De Service Fabric SDK voor Linux bevat een Yeoman-generator voor het maken van de steiger voor een Service Fabric toepassing met een stateless service. Begin met het uitvoeren van de volgende Yeoman-opdracht:

```bash
$ yo azuresfjava
```

Volg de instructies voor het maken van een **betrouw bare stateless service**. Voor deze zelf studie moet u de toepassing "HelloWorldApplication" en de service "HelloWorld" noemen. Het resultaat bevat mappen voor de `HelloWorldApplication` en `HelloWorld` .

```bash
HelloWorldApplication/
├── build.gradle
├── HelloWorld
│   ├── build.gradle
│   └── src
│       └── statelessservice
│           ├── HelloWorldServiceHost.java
│           └── HelloWorldService.java
├── HelloWorldApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   └── _readme.txt
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── install.sh
├── settings.gradle
└── uninstall.sh
```
### <a name="service-registration"></a>Service registratie
Service typen moeten zijn geregistreerd bij de Service Fabric-runtime. Het Service type wordt gedefinieerd in de `ServiceManifest.xml` en uw service klasse die wordt geïmplementeerd `StatelessService` . Service registratie wordt uitgevoerd in het hoofd toegangs punt van het proces. In dit voor beeld is het hoofd toegangs punt van het proces `HelloWorldServiceHost.java` :

```java
public static void main(String[] args) throws Exception {
    try {
        ServiceRuntime.registerStatelessServiceAsync("HelloWorldType", (context) -> new HelloWorldService(), Duration.ofSeconds(10));
        logger.log(Level.INFO, "Registered stateless service type HelloWorldType.");
        Thread.sleep(Long.MAX_VALUE);
    }
    catch (Exception ex) {
        logger.log(Level.SEVERE, "Exception in registration:", ex);
        throw ex;
    }
}
```

## <a name="implement-the-service"></a>De service implementeren

Open **HelloWorldApplication/HelloWorld/src/statelessservice/HelloWorldService. java**. Deze klasse definieert het Service type en kan elke wille keurige code uitvoeren. De service-API biedt twee toegangs punten voor uw code:

* Een open-geëindigd methode voor het ingangs punt, `runAsync()` die wordt aangeroepen, waar u werk belastingen kunt uitvoeren, inclusief langlopende Compute-workloads.

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    ...
}
```

* Een communicatie-ingangs punt waar u uw communicatie stack kunt aansluiten. Hier kunt u beginnen met het ontvangen van aanvragen van gebruikers en andere services.

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {
    ...
}
```

Deze zelf studie is gericht op de methode van het `runAsync()` toegangs punt. Hier kunt u direct beginnen met het uitvoeren van uw code.

### <a name="runasync"></a>RunAsync
Het platform roept deze methode aan wanneer een exemplaar van een service wordt geplaatst en gereed is om te worden uitgevoerd. Voor een stateless service betekent dat wanneer het service-exemplaar wordt geopend. Er wordt een annulerings token gegeven om te coördineren wanneer uw service-exemplaar moet worden gesloten. In Service Fabric kan deze open/close-cyclus van een service-exemplaar veel keren voor komen gedurende de levens duur van de service als geheel. Dit kan verschillende oorzaken hebben, waaronder:

* Het systeem verplaatst uw service-exemplaren voor resource verdeling.
* Er treden fouten op in uw code.
* Er wordt een upgrade uitgevoerd voor de toepassing of het systeem.
* De onderliggende hardware ondervindt een storing.

Deze indeling wordt beheerd door Service Fabric om ervoor te zorgen dat uw service Maxi maal beschikbaar is en op de juiste wijze is gebalanceerd.

`runAsync()` moet synchroon niet worden geblokkeerd. Uw implementatie van runAsync moet een CompletableFuture retour neren zodat de runtime kan door gaan. Als uw werk belasting een langlopende taak moet implementeren die in de CompletableFuture moet worden uitgevoerd.

#### <a name="cancellation"></a>Opzegging
Het annuleren van uw werk belasting is een gezamenlijke inspanning die wordt georganiseerd door het door gegeven annulerings token. Het systeem wacht tot de taak is voltooid (door voltooiing, annulering of fout) voordat deze wordt verplaatst. Het is belang rijk om het annulerings token te voldoen, werk te volt ooien en `runAsync()` zo snel mogelijk af te sluiten wanneer de systeem aanvragen worden geannuleerd. In het volgende voor beeld ziet u hoe u een annulerings gebeurtenis kunt afhandelen:

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {

    // TODO: Replace the following sample code with your own logic
    // or remove this runAsync override if it's not needed in your service.

    return CompletableFuture.runAsync(() -> {
        long iterations = 0;
        while(true)
        {
        cancellationToken.throwIfCancellationRequested();
        logger.log(Level.INFO, "Working-{0}", ++iterations);

        try {
            Thread.sleep(1000);
        } catch (InterruptedException ex){}
        }
    });
}
```

In dit stateless service voor beeld wordt het aantal opgeslagen in een lokale variabele. Maar omdat dit een stateless service is, bestaat de opgeslagen waarde alleen voor de huidige levens duur van het service-exemplaar. Wanneer de service wordt verplaatst of opnieuw wordt gestart, gaat de waarde verloren.

## <a name="create-a-stateful-service"></a>Een stateful service maken
Service Fabric introduceert een nieuwe soort service die stateful is. Een stateful service kan de status op betrouw bare wijze in de service zelf onderhouden, naast de code die deze gebruikt. De status wordt Maxi maal beschikbaar gemaakt door Service Fabric zonder dat de status in een externe opslag moet worden bewaard.

Als u een item waarde wilt converteren van stateless naar Maxi maal beschikbaar en permanent, zelfs wanneer de service wordt verplaatst of opnieuw wordt opgestart, hebt u een stateful service nodig.

In dezelfde map als de HelloWorld-toepassing kunt u een nieuwe service toevoegen door de opdracht uit te voeren `yo azuresfjava:AddService` . Kies de ' betrouw bare stateful service ' voor uw Framework en noem de service ' HelloWorldStateful '. 

Uw toepassing moet nu twee services hebben: de stateless service HelloWorld en de stateful service-HelloWorldStateful.

Een stateful service heeft dezelfde toegangs punten als een stateless service. Het belangrijkste verschil is de beschik baarheid van een State-provider die de status betrouwbaar kan opslaan. Service Fabric wordt geleverd met de implementatie van een status provider, die reliable verzamelingen wordt genoemd, waarmee u gerepliceerde gegevens structuren kunt maken met behulp van betrouw bare status Manager. Een stateful betrouw bare service maakt standaard gebruik van deze State-provider.

Open HelloWorldStateful. java in **HelloWorldStateful-> src**, dat de volgende RunAsync-methode bevat:

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    Transaction tx = stateManager.createTransaction();
    return this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap").thenCompose((map) -> {
        return map.computeAsync(tx, "counter", (k, v) -> {
            if (v == null)
                return 1L;
            else
                return ++v;
            }, Duration.ofSeconds(4), cancellationToken)
                .thenCompose((r) -> tx.commitAsync())
                .whenComplete((r, e) -> {
            try {
                tx.close();
            } catch (Exception e) {
                logger.log(Level.SEVERE, e.getMessage());
            }
        });
    });
}
```

### <a name="runasync"></a>RunAsync
`RunAsync()` werkt op dezelfde manier als stateful en stateless Services. In een stateful service voert het platform echter namens u extra werkzaamheden uit voordat het wordt uitgevoerd `RunAsync()` . Dit werk kan erop kunnen toezien dat de betrouw bare status Manager en de betrouw bare verzamelingen klaar zijn voor gebruik.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Betrouw bare verzamelingen en de betrouw bare status Manager
```java
ReliableHashMap<String,Long> map = this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap")
```

[ReliableHashMap](/java/api/microsoft.servicefabric.data.collections.reliablehashmap) is een woorden lijst-implementatie die u kunt gebruiken om de status van de service op een betrouw bare manier op te slaan. Met Service Fabric en betrouw bare HashMaps kunt u gegevens rechtstreeks in uw service opslaan zonder dat hiervoor een externe permanente opslag nodig is. Betrouw bare HashMaps maken uw gegevens Maxi maal beschikbaar. Service Fabric dit te bereiken door meerdere *replica's* van uw service voor u te maken en te beheren. Het biedt ook een API die de complexiteit van het beheer van deze replica's en hun status overgangen opstelt.

In betrouw bare verzamelingen kan elk Java-type worden opgeslagen, inclusief uw aangepaste typen, met een aantal voor behoud:

* Service Fabric maakt uw status Maxi maal beschikbaar door de status van de knoop punten te *repliceren* en betrouw bare HashMap worden uw gegevens opgeslagen op de lokale schijf van elke replica. Dit betekent dat alles dat is opgeslagen in betrouw bare HashMaps *serialiseerbaar* moet zijn. 
* Objecten worden gerepliceerd voor hoge Beschik baarheid wanneer u trans acties doorvoert op betrouw bare HashMaps. Objecten die zijn opgeslagen in betrouw bare HashMaps, worden in het lokale geheugen in uw service bewaard. Dit betekent dat u een lokale verwijzing naar het object hebt.
  
   Het is belang rijk dat u geen lokale exemplaren van deze objecten mutate zonder een update-bewerking uit te voeren op de betrouw bare verzameling in een trans actie. Dit komt doordat wijzigingen in lokale exemplaren van objecten niet automatisch worden gerepliceerd. U moet het object opnieuw invoegen in de woorden lijst of een van de *Update* methoden gebruiken in de woorden lijst.

De betrouw bare status beheerder beheert betrouw bare HashMaps voor u. U kunt de betrouw bare status Manager voor een betrouw bare verzameling vragen op naam op elk gewenst moment en op elke plaats in uw service. De betrouw bare status beheerder zorgt ervoor dat u een referentie back-up krijgt. Het is niet raadzaam om verwijzingen naar betrouw bare verzamelings instanties in variabelen of eigenschappen van klassen leden op te slaan. Er moet speciale aandacht worden besteed om ervoor te zorgen dat de verwijzing op elk moment in de levens cyclus van de service wordt ingesteld op een exemplaar. De betrouw bare status manager behandelt dit werk voor u en is geoptimaliseerd voor herhaalde bezoeken.


### <a name="transactional-and-asynchronous-operations"></a>Transactionele en asynchrone bewerkingen
```java
return map.computeAsync(tx, "counter", (k, v) -> {
    if (v == null)
        return 1L;
    else
        return ++v;
    }, Duration.ofSeconds(4), cancellationToken)
        .thenCompose((r) -> tx.commitAsync())
        .whenComplete((r, e) -> {
    try {
        tx.close();
    } catch (Exception e) {
        logger.log(Level.SEVERE, e.getMessage());
    }
});
```

Bewerkingen op betrouw bare HashMaps zijn asynchroon. Dit is omdat schrijf bewerkingen met betrouw bare verzamelingen I/O-bewerkingen uitvoeren om gegevens te repliceren en te persistent maken op schijf.

Betrouw bare HashMap-bewerkingen zijn *Transactioneel*, zodat u de status consistent kunt blijven over meerdere betrouw bare HashMaps en bewerkingen. U kunt bijvoorbeeld een werk item van één betrouw bare woorden lijst ophalen, een bewerking uitvoeren en het resultaat opslaan in een andere betrouw bare HashMap, allemaal binnen één trans actie. Dit wordt behandeld als een Atomic-bewerking en garandeert dat de gehele bewerking slaagt of dat de hele bewerking wordt teruggedraaid. Als er een fout optreedt nadat u het item in de wachtrij hebt geplaatst, maar voordat u het resultaat opslaat, wordt de hele trans actie teruggedraaid en blijft het item in de wachtrij voor verwerking.


## <a name="build-the-application"></a>De toepassing bouwen

De Yeoman-steiger bevat een gradle-script om de toepassing en bash-scripts te bouwen om de toepassing te implementeren en te verwijderen. Als u de toepassing wilt uitvoeren, moet u eerst de toepassing bouwen met gradle:

```bash
$ gradle
```

Dit produceert een Service Fabric toepassings pakket dat kan worden geïmplementeerd met behulp van Service Fabric CLI.

## <a name="deploy-the-application"></a>De toepassing implementeren

Nadat de toepassing is gemaakt, kunt u deze implementeren in het lokale cluster.

1. Maak verbinding met het lokale cluster van Service Fabric.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Voer het installatiescript uit dat is opgegeven in de sjabloon om het toepassingspakket te kopiëren naar de installatiekopieopslag van het cluster, het toepassingstype te registreren en een exemplaar van de toepassing te maken.

    ```bash
    ./install.sh
    ```

De implementatie van de gemaakte toepassing werkt hetzelfde als van andere Service Fabric-toepassingen. Zie de documentatie over het [beheren van een Service Fabric-toepassing met de Service Fabric-CLI](service-fabric-application-lifecycle-sfctl.md) voor gedetailleerde instructies.

Parameters voor deze opdrachten vindt u in de gegenereerde manifesten binnen het toepassingspakket.

Nadat de toepassing is geïmplementeerd, opent u een browser en gaat u naar [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) op `http://localhost:19080/Explorer`. Vouw vervolgens het knooppunt **Toepassingen** uit. U ziet dat er nu een vermelding is voor uw toepassingstype en nog een voor het eerste exemplaar van dat type.

> [!IMPORTANT]
> Als u de toepassing wilt implementeren in een beveiligd Linux-cluster in azure, moet u een certificaat configureren om uw toepassing te valideren met de Service Fabric runtime. Hierdoor kunnen uw Reliable Services-services communiceren met de onderliggende Service Fabric runtime-Api's. Zie [een reliable Services-app configureren voor het uitvoeren van Linux-clusters](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters)voor meer informatie.  
>

## <a name="next-steps"></a>Volgende stappen

* [Aan de slag met de Service Fabric-CLI](service-fabric-cli.md)

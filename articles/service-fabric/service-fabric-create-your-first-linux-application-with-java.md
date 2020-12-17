---
title: Een Java-toepassing met Azure Service Fabric reliable actors maken in Linux
description: Lees hoe u een betrouwbare Service Fabric Java-actortoepassing in vijf minuten kunt maken en implementeren.
ms.topic: conceptual
ms.date: 06/18/2018
ms.custom: devx-track-java
ms.openlocfilehash: 7d87b72437f86d7dc1ca4e3cf9f3d67609691c70
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97655948"
---
# <a name="create-your-first-java-service-fabric-reliable-actors-application-on-linux"></a>Uw eerste betrouwbare Service Fabric Java-actortoepassing maken in Linux
> [!div class="op_single_selector"]
> * [Java - Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# - Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

Met deze Quick Start kunt u in een paar minuten uw eerste Azure Service Fabric Java-toepassing maken in een Linux-ontwikkelomgeving.  Wanneer u klaar bent, hebt u een eenvoudige Java-toepassing voor één service die wordt uitgevoerd op het lokale ontwikkelcluster.  

## <a name="prerequisites"></a>Vereisten
Voordat u aan de slag gaat, installeert u de Service Fabric SDK, de CLI van Service Fabric en Yeoman en stelt u een ontwikkelingscluster in uw [Linux-ontwikkelomgeving](service-fabric-get-started-linux.md) in. Als u Mac OS X gebruikt, kunt u [een ontwikkelomgeving instellen op Mac met behulp van Docker](service-fabric-get-started-mac.md).

Installeer ook de [Service Fabric-CLI](service-fabric-cli.md).

### <a name="install-and-set-up-the-generators-for-java"></a>Generatoren voor Java installeren en instellen
Service Fabric biedt hulpprogramma's waarmee u vanuit de terminal een Service Fabric Java-toepassing kunt maken met behulp van de Yeoman-sjabloongenerator.  Als Yeoman niet al is geïnstalleerd, raadpleegt u [Service Fabric getting started with Linux](service-fabric-get-started-linux.md#set-up-yeoman-generators-for-containers-and-guest-executables) (Service Fabric: aan de slag met Linux) voor instructies over het installeren van Yeoman. Voer de volgende opdracht uit om de Service Fabric Yeoman-sjabloongenerator voor Java te installeren.

  ```bash
  npm install -g generator-azuresfjava
  ```

## <a name="basic-concepts"></a>Basisconcepten
Om aan de slag te gaan met Reliable Actors hoeft u slechts enkele basisbegrippen te kennen:

* **Actor-service**. Reliable Actors zijn verpakt in Reliable Services die in de Service Fabric-infrastructuur kunnen worden geïmplementeerd. Actor-exemplaren worden geactiveerd in een benoemd service-exemplaar.
* **Actor-registratie**. Net als Reliable Services moet een Reliable Actor-service worden geregistreerd bij de Service Fabric-runtime. Daarnaast moet het actortype worden geregistreerd bij de Actor-runtime.
* **Actor-interface**. De Actor-interface wordt gebruikt voor het definiëren van een sterk getypeerde openbare interface van een actor. In de terminologie van Reliable Actor-modellen definieert de actorinterface de typen berichten die de actor kan begrijpen en verwerken. De actorinterface wordt door andere actors en clienttoepassingen gebruikt om (asynchroon) berichten naar de actor te 'verzenden'. Reliable Actors kunnen meerdere interfaces implementeren.
* **Klasse ActorProxy**. De klasse ActorProxy wordt door clienttoepassingen gebruikt voor het aanroepen van de methoden die toegankelijk zijn via de actorinterface. De klasse ActorProxy heeft twee belangrijke functies:

  * Naamomzetting: de klasse kan de actor in het cluster vinden (het knooppunt van het cluster waar de actor wordt gehost).
  * Foutafhandeling: de klasse kan methodes opnieuw proberen aan te roepen en de locatie van een actor opnieuw omzetten, bijvoorbeeld na een storing waardoor de actor is verplaatst naar een ander knooppunt in het cluster.

De volgende regels met betrekking tot actorinterfaces zijn het noemen waard:

* Actor-interfacemethoden kunnen niet worden overbelast.
* Actor-interfacemethoden mogen geen out-, ref- of optionele parameters hebben.
* Algemene interfaces worden niet ondersteund.

## <a name="create-the-application"></a>De toepassing maken
Een Service Fabric-toepassing bevat een of meer services, elk met een specifieke functie met betrekking tot het leveren van de functionaliteit van de toepassing. De generator die u in de laatste sectie hebt geïnstalleerd, zorgt ervoor dat u gemakkelijk uw eerste service kunt maken en er later meer kunt toevoegen.  U kunt ook Service Fabric-Java-toepassingen maken, ontwikkelen en implementeren met behulp van een invoegtoepassing voor Eclipse. Zie [Uw eerste Java-toepassing maken en implementeren met behulp van Eclipse](service-fabric-get-started-eclipse.md). In deze Quick Start gebruikt u Yeoman om een toepassing met één service te maken waarmee een tellerwaarde wordt opgeslagen en opgehaald.

1. Typ in een terminal ``yo azuresfjava``.
2. Geef uw toepassing een naam.
3. Kies het type van uw eerste service en geef de service een naam. In deze zelfstudie kiest u voor een Reliable Actor Service. Zie [Service Fabric programming model overview](service-fabric-choose-framework.md) (Overzicht van het Service Fabric-programmeermodel) voor meer informatie over de andere typen services.
   ![Service Fabric Yeoman-generator voor Java][sf-yeoman]

Als u de toepassing 'HelloWorldActorApplication' noemt en de actor 'HelloWorldActor', wordt de volgende constructie gemaakt:

```bash
HelloWorldActorApplication/
├── build.gradle
├── HelloWorldActor
│   ├── build.gradle
│   ├── settings.gradle
│   └── src
│       └── reliableactor
│           ├── HelloWorldActorHost.java
│           └── HelloWorldActorImpl.java
├── HelloWorldActorApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldActorPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   ├── _readme.txt
│       │   └── Settings.xml
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── HelloWorldActorInterface
│   ├── build.gradle
│   └── src
│       └── reliableactor
│           └── HelloWorldActor.java
├── HelloWorldActorTestClient
│   ├── build.gradle
│   ├── settings.gradle
│   ├── src
│   │   └── reliableactor
│   │       └── test
│   │           └── HelloWorldActorTestClient.java
│   └── testclient.sh
├── install.sh
├── settings.gradle
└── uninstall.sh
```
## <a name="reliable-actors-basic-building-blocks"></a>Basisbouwstenen van Reliable Actors
De eerder beschreven basisbegrippen kunnen worden vertaald naar de basisbouwstenen van een Reliable Actor-service.

### <a name="actor-interface"></a>Actorinterface
Deze bevat de interfacedefinitie voor de actor. Deze interface definieert het actorcontract dat wordt gedeeld met de actorimplementatie en de clients die de actor aanroepen. Doorgaans is het dus zinvol om deze te definiëren op een locatie die is gescheiden van de implementatie van de actor en die kan worden gedeeld met meerdere andere services of clienttoepassingen.

`HelloWorldActorInterface/src/reliableactor/HelloWorldActor.java`:

```java
public interface HelloWorldActor extends Actor {
    @Readonly   
    CompletableFuture<Integer> getCountAsync();

    CompletableFuture<?> setCountAsync(int count);
}
```

### <a name="actor-service"></a>Actorservice
Deze bevat uw actorimplementatie en actorregistratiecode. De actorklasse implementeert de actorinterface. Dit is waar de actor zijn werk doet.

`HelloWorldActor/src/reliableactor/HelloWorldActorImpl`:

```java
@ActorServiceAttribute(name = "HelloWorldActorService")
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class HelloWorldActorImpl extends FabricActor implements HelloWorldActor {
    private Logger logger = Logger.getLogger(this.getClass().getName());

    public HelloWorldActorImpl(FabricActorService actorService, ActorId actorId){
        super(actorService, actorId);
    }

    @Override
    protected CompletableFuture<?> onActivateAsync() {
        logger.log(Level.INFO, "onActivateAsync");

        return this.stateManager().tryAddStateAsync("count", 0);
    }

    @Override
    public CompletableFuture<Integer> getCountAsync() {
        logger.log(Level.INFO, "Getting current count value");
        return this.stateManager().getStateAsync("count");
    }

    @Override
    public CompletableFuture<?> setCountAsync(int count) {
        logger.log(Level.INFO, "Setting current count value {0}", count);
        return this.stateManager().addOrUpdateStateAsync("count", count, (key, value) -> count > value ? count : value);
    }
}
```

### <a name="actor-registration"></a>Actorregistratie
De actorservice moet met een servicetype worden geregistreerd in de Service Fabric-runtime. De Actor-service kan uw actorexemplaren alleen uitvoeren als ook het actortype is geregistreerd bij de Actor-service. De `ActorRuntime`-registratiemethode doet dit voor actors.

`HelloWorldActor/src/reliableactor/HelloWorldActorHost`:

```java
public class HelloWorldActorHost {

private static final Logger logger = Logger.getLogger(HelloWorldActorHost.class.getName());

public static void main(String[] args) throws Exception {

        try {

            ActorRuntime.registerActorAsync(HelloWorldActorImpl.class, (context, actorType) -> new FabricActorService(context, actorType, (a,b)-> new HelloWorldActorImpl(a,b)), Duration.ofSeconds(10));
            Thread.sleep(Long.MAX_VALUE);
        } catch (Exception e) {
            logger.log(Level.SEVERE, "Exception occurred", e);
            throw e;
        }
    }
}
```

## <a name="build-the-application"></a>De toepassing bouwen
De Service Fabric Yeoman-sjablonen bevatten een bouwscript voor [Gradle](https://gradle.org/), dat u kunt gebruiken om de toepassing via de terminal te maken.
Java-afhankelijkheden in Service Fabric worden opgehaald uit Maven. U kunt alleen Service Fabric-Java-toepassingen bouwen en hieraan werken als JDK en Gradle zijn geïnstalleerd. Als ze niet al zijn geïnstalleerd, raadpleegt u [Service Fabric getting started with Linux](service-fabric-get-started-linux.md#set-up-java-development) (Service Fabric: aan de slag met Linux) voor instructies over het installeren van JDK en Gradle.

Ga als volgt te werk om de toepassing te maken en in te pakken:

  ```bash
  cd HelloWorldActorApplication
  gradle
  ```

## <a name="deploy-the-application"></a>De toepassing implementeren
Nadat de toepassing is gemaakt, kunt u deze implementeren in het lokale cluster.

1. Maak verbinding met de lokale Service Fabric-cluster (het cluster moet [zijn ingesteld en worden uitgevoerd](service-fabric-get-started-linux.md#set-up-a-local-cluster)).

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Voer het installatiescript uit dat is opgegeven in de sjabloon om het toepassingspakket te kopiëren naar de installatiekopieopslag van het cluster, het toepassingstype te registreren en een exemplaar van de toepassing te maken.

    ```bash
    ./install.sh
    ```

De implementatie van de gemaakte toepassing werkt hetzelfde als van andere Service Fabric-toepassingen. Zie de documentatie over het [beheren van een Service Fabric-toepassing met de Service Fabric-CLI](service-fabric-application-lifecycle-sfctl.md) voor gedetailleerde instructies.

Parameters voor deze opdrachten vindt u in de gegenereerde manifesten binnen het toepassingspakket.

Nadat de toepassing is geïmplementeerd, opent u een browser en gaat u naar [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) op `http://localhost:19080/Explorer`.
Vouw vervolgens het knooppunt **Toepassingen** uit. U ziet dat er nu een vermelding is voor uw toepassingstype en nog een voor het eerste exemplaar van dat type.

> [!IMPORTANT]
> Als u de toepassing wilt implementeren in een beveiligd Linux-cluster in azure, moet u een certificaat configureren om uw toepassing te valideren met de Service Fabric runtime. Hierdoor kunnen uw Reliable Actors-services communiceren met de onderliggende Service Fabric runtime-Api's. Zie [een reliable Services-app configureren voor het uitvoeren van Linux-clusters](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters)voor meer informatie.  
>

## <a name="start-the-test-client-and-perform-a-failover"></a>De testclient starten en een failover uitvoeren
Actoren doen niets uit zichzelf, ze hebben een andere service of client nodig die hen berichten stuurt. De actorsjabloon bevat een eenvoudig testscript dat u kunt gebruiken om te communiceren met de actorservice.

> [!Note]
> De test-client maakt gebruik van de actor proxy-klasse om te communiceren met actors, die moeten worden uitgevoerd binnen hetzelfde cluster als de actor-service, of dezelfde IP-adres ruimte delen.  U kunt de test-client uitvoeren op dezelfde computer als het lokale ontwikkel cluster.  Als u wilt communiceren met actors in een extern cluster, moet u echter een gateway implementeren op het cluster dat externe communicatie met de actors verwerkt.

1. Voer het script uit met behulp van het controleprogramma om de uitvoer van de actorservice te bekijken.  Via het testscript wordt de methode `setCountAsync()` op de actor aangeroepen om een teller te verhogen, wordt de methode `getCountAsync()` op de actor aangeroepen om de nieuwe tellerwaarde op te halen en wordt deze waarde weergegeven op de console.

   In het geval van MAC OS X moet u de HelloWorldTestClient-map naar een bepaalde locatie in de container kopiëren door de volgende extra opdrachten uit te voeren.    

    ```bash
     docker cp HelloWorldTestClient [first-four-digits-of-container-ID]:/home
     docker exec -it [first-four-digits-of-container-ID] /bin/bash
     cd /home
     ```

    ```bash
    cd HelloWorldActorTestClient
    watch -n 1 ./testclient.sh
    ```

2. Zoek in Service Fabric Explorer het knooppunt op dat als host fungeert voor de primaire replica voor de actorservice. In de onderstaande schermafbeelding is dit knooppunt 3. Via de primaire servicereplica worden lees- en schrijfbewerkingen verwerkt.  Wijzigingen in de status van de service worden vervolgens gerepliceerd naar de secundaire replica's die worden uitgevoerd op de knoop punten 0 en 1 in de onderstaande scherm afbeelding.

    ![Zoeken naar de primaire replica in Service Fabric Explorer][sfx-primary]

3. Klik in **Knooppunten** op het knooppunt dat u hebt gevonden in de vorige stap en selecteer **Deactiveren (opnieuw starten)** in het menu Acties. Met deze actie wordt het knooppunt opnieuw gestart waarop de primaire servicereplica wordt uitgevoerd en wordt een failover afgedwongen op een van de secundaire replica's die worden uitgevoerd op een ander knooppunt.  Deze secundaire replica wordt een niveau verhoogd naar primair, er wordt nog een secundaire replica gemaakt op een ander knooppunt en de primaire replica begint met het uitvoeren van lees-/ schrijfbewerkingen. Terwijl het knooppunt opnieuw wordt gestart, ziet u in de uitvoer van de testclient dat de teller blijft toenemen ondanks de failover.

## <a name="remove-the-application"></a>De toepassing verwijderen
Gebruik het uninstall-script dat is opgegeven in de sjabloon om het toepassingsexemplaar te verwijderen, de registratie van het toepassingspakket op te heffen en het toepassingspakket te verwijderen uit de installatiekopieopslag van het cluster.

```bash
./uninstall.sh
```

In Service Fabric Explorer ziet u dat de toepassing en het toepassingstype niet meer worden weergegeven in het knooppunt **Toepassingen**.

## <a name="service-fabric-java-libraries-on-maven"></a>Service Fabric-Java-bibliotheken op Maven
Service Fabric Java-bibliotheken worden gehost in Maven. U kunt de afhankelijkheden toevoegen in de ``pom.xml`` of ``build.gradle`` van uw projecten om voortaan de Service Fabric-Java-bibliotheken op **mavenCentral** te gebruiken. 

### <a name="actors"></a>Actoren

Ondersteuning voor betrouwbare actoren in Service Fabric voor uw toepassing.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-actors</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-actors:1.0.0'
  }
  ```

### <a name="services"></a>Services

Ondersteuning van Reliable Services in Service Fabric voor uw toepassing.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-services</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-services:1.0.0'
  }
  ```

### <a name="others"></a>Andere
#### <a name="transport"></a>Transport

Ondersteuning van transportlaag voor Service Fabric Java-toepassing. U hoeft deze afhankelijkheid niet expliciet toe te voegen aan uw Reliable Actor- of Service-toepassingen, tenzij u programmeert op de transportlaag.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-transport</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-transport:1.0.0'
  }
  ```

#### <a name="fabric-support"></a>Fabric-ondersteuning

Ondersteuning op systeemniveau voor Service Fabric, dat communiceert met native Service Fabric op het moment van uitvoering. U hoeft deze afhankelijkheid niet expliciet toe te voegen aan uw Reliable Actor- of Service-toepassingen. Dit wordt automatisch opgehaald van Maven wanneer u de andere bovenstaande afhankelijkheden opneemt.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf:1.0.0'
  }
  ```

## <a name="next-steps"></a>Volgende stappen

* [Uw eerste Service Fabric Java-toepassing maken op Linux met Eclipse](service-fabric-get-started-eclipse.md)
* [Meer informatie over Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Werken met Service Fabric-clusters via de Service Fabric-CLI](service-fabric-cli.md)
* Meer informatie over [ondersteuningsopties voor Service Fabric](service-fabric-support.md)
* [Aan de slag met de Service Fabric-CLI](service-fabric-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png

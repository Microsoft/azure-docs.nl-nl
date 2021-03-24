---
title: SCP.NET-programmeer gids voor Storm in azure HDInsight
description: Meer informatie over het gebruik van SCP.NET om te maken. Op NET gebaseerde Storm-topologieën voor gebruik met storm in azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 01/13/2020
ms.openlocfilehash: c993b3f70f609fb79c51ba9be08fa3d5dc7e8317
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864105"
---
# <a name="scp-programming-guide-for-apache-storm-in-azure-hdinsight"></a>SCP-programmeer gids voor Apache Storm in azure HDInsight

SCP is een platform voor het bouwen van real-time, betrouw bare, consistente en hoogwaardige toepassingen voor gegevens verwerking. Het is gebaseerd op [Apache Storm](https://storm.incubator.apache.org/), een gegevensstroom verwerkings systeem dat is ontworpen door open-source software community's. Nathan Marz gemaakt Storm. Het is gepubliceerd als open source door Twitter. Storm maakt gebruik van [Apache ZooKeeper](https://zookeeper.apache.org/). Dit is een ander Apache-project dat zeer betrouw bare gedistribueerde coördinatie en status beheer mogelijk maakt.

Het SCP-project heeft niet alleen stormen over Windows, maar ook door project toegevoegde extensies en aanpassing voor de Windows-omgeving. De uitbrei dingen bevatten de .NET-ontwikkelaars ervaring en .NET-bibliotheken. De aanpassing omvat implementatie op basis van Windows.

Met de uitbrei dingen en aanpassingen hoeft u de open-source software projecten niet te splitsen. U kunt afgeleide omgevingen gebruiken die bovenop Storm zijn gebouwd.

## <a name="processing-model"></a>Verwerkings model

De gegevens in SCP zijn gemodelleerd als continue streams van Tuples. Normaal gesp roken worden de Tuples:

1. Stroom naar een wachtrij.
1. Worden opgenomen en getransformeerd op bedrijfs logica die wordt gehost in een storm-topologie.
1. Hun uitvoer pipet als Tuples naar een ander SCP-systeem of worden doorgevoerd in archieven zoals gedistribueerde bestands systemen en data bases zoals SQL Server.

:::image type="content" source="./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png" alt-text="Een diagram van een wachtrij verwerkings gegevens, die op zijn beurt een gegevens archief feeds" border="false":::

In Storm wordt in een toepassings topologie een reken grafiek gedefinieerd. Elk knoop punt in een topologie bevat verwerkings logica. Koppelingen tussen knoop punten geven de gegevens stroom aan.

Knoop punten die invoer gegevens in de topologie injecteren, worden _spouts_ genoemd. U kunt ze gebruiken om de gegevens op te geven. De invoer gegevens kunnen afkomstig zijn van een bron, zoals bestands logboeken, een transactionele data base of een systeem prestatie meter item.

Knoop punten met zowel invoer-als uitvoer gegevens stromen worden ' _bouten_' genoemd. Hiermee worden de daad werkelijke gegevens filters, selecties en aggregatie uitgevoerd.

SCP ondersteunt beste inspanningen, mini maal één keer en precies eenmaal per gegevens verwerking.

In een gedistribueerde stream-verwerkings toepassing kunnen fouten optreden tijdens het verwerken van gegevens. Dergelijke fouten omvatten een netwerk storing, een apparaatfout of een fout in de code. Ten minste één keer wordt gegarandeerd dat alle gegevens ten minste één keer worden verwerkt door dezelfde gegevens automatisch opnieuw af te spelen wanneer er een fout optreedt.

Ten minste eenmalige verwerking is eenvoudig en betrouwbaar, en het is geschikt voor veel toepassingen. Maar wanneer een toepassing exact moet worden geteld, is de verwerking ten minste één keer ontoereikend omdat dezelfde gegevens in de toepassings topologie opnieuw kunnen worden afgespeeld. In dat geval moet het resultaat exact eenmaal worden verwerkt, zelfs wanneer de gegevens meerdere keren worden afgespeeld en verwerkt.

Met SCP kunnen .NET-ontwikkel aars realtime gegevens verwerkings toepassingen maken met behulp van een Java Virtual Machine (JVM) met storm. Een JVM-en .NET-communicatie via lokale TCP-sockets. Elke Spout/Schicht is een .NET/Java-proces paar, waarbij de gebruikers logica wordt uitgevoerd in een .NET-proces als een invoeg toepassing.

Voer de volgende stappen uit om een gegevens verwerkings toepassing boven op SCP te bouwen:

1. Ontwerp en implementeer spouts om gegevens uit wacht rijen op te halen.
1. Ontwerp en implementeer schichten die de invoer gegevens verwerken en sla deze op in externe winkels, zoals een Data Base.
1. Ontwerp de topologie en verzend deze en voer deze uit.

De topologie definieert hoek punten en de gegevens die tussen deze twee stromen worden gestroomd. SCP gebruikt een topologie specificatie en implementeert deze in een storm-cluster, waarbij elk hoek punt op één logisch knoop punt wordt uitgevoerd. De Storm-taak planner zorgt voor failover en schalen.

In dit artikel worden enkele eenvoudige voor beelden gebruikt om te leren hoe u toepassingen voor gegevens verwerking bouwt met SCP.

## <a name="scp-plug-in-interface"></a>Interface van SCP-invoeg toepassing

SCP-invoeg toepassingen zijn zelfstandige toepassingen. Ze kunnen tijdens de ontwikkeling binnen Visual Studio worden uitgevoerd en na de productie-implementatie worden aangesloten op de Storm-pijp lijn.

Het schrijven van een SCP-invoeg toepassing is hetzelfde als het schrijven van een andere Windows-console toepassing. Het SCP.NET-platform declareert enkele interfaces voor spout/schicht. Uw invoeg toepassings code implementeert deze interfaces. Het belangrijkste doel van dit ontwerp is om u te concentreren op uw bedrijfs logica terwijl de SCP.NET-platform andere dingen kunnen verwerken.

Uw invoeg toepassings code implementeert een van de volgende interfaces. Welke interface is afhankelijk van of de topologie transactioneel of niet-transactioneel is en of het onderdeel een Spout of een Schicht is.

* **ISCPSpout**
* **ISCPBolt**
* **ISCPTxSpout**
* **ISCPBatchBolt**

### <a name="iscpplugin"></a>ISCPPlugin

**ISCPPlugin** is de gemeen schappelijke interface voor veel invoeg toepassingen. Het is momenteel een dummy-interface.

```csharp
public interface ISCPPlugin
{
}
```

### <a name="iscpspout"></a>ISCPSpout

**ISCPSpout** is de interface voor een niet-transactionele Spout.

```csharp
public interface ISCPSpout : ISCPPlugin
{
    void NextTuple(Dictionary<string, Object> parms);
    void Ack(long seqId, Dictionary<string, Object> parms); 
    void Fail(long seqId, Dictionary<string, Object> parms);
}
```

Wanneer **NextTuple** wordt aangeroepen, kan uw C#-code een of meer Tuples verzenden. Als er niets is om te verzenden, moet deze methode worden geretourneerd zonder dat er iets hoeft te worden verzonden.

De **NextTuple**-, **ACK**-en **Fail** -methoden worden allemaal in een kleine lus in één thread van een C#-proces genoemd. Als er geen Tuples zijn om te verzenden, moet u in de slaap stand **NextTuple** gedurende een korte periode, zoals 10 milliseconden. Deze slaap stand helpt de CPU-Beschik baarheid te voor komen.

De methoden **ACK** en **Fail** worden alleen aangeroepen wanneer een specificatie bestand het bevestigings mechanisme mogelijk maakt. De para meter *beveiligings onthouden* identificeert de tuple die is bevestigd of mislukt. Als bevestiging is ingeschakeld in een niet-transactionele topologie, moet de volgende **Verzend** functie worden gebruikt in een Spout:

```csharp
public abstract void Emit(string streamId, List<object> values, long seqId);
```

Als een niet-transactionele topologie geen bevestiging ondersteunt, kunnen **ACK** en **Fail** worden gelaten als lege functies.

De invoer parameter *parameters* in deze functies geeft een lege woorden lijst op en is gereserveerd voor toekomstig gebruik.

### <a name="iscpbolt"></a>ISCPBolt

**ISCPBolt** is de interface voor een niet-transactionele schicht.

```csharp
public interface ISCPBolt : ISCPPlugin
{
void Execute(SCPTuple tuple);
}
```

Wanneer een nieuwe tuple beschikbaar is, wordt de functie **Execute** aangeroepen om deze te verwerken.

### <a name="iscptxspout"></a>ISCPTxSpout

**ISCPTxSpout** is de interface voor een transactionele Spout.

```csharp
public interface ISCPTxSpout : ISCPPlugin
{
    void NextTx(out long seqId, Dictionary<string, Object> parms);  
    void Ack(long seqId, Dictionary<string, Object> parms);         
    void Fail(long seqId, Dictionary<string, Object> parms);        
}
```

Net als de niet-transactionele tegen Hangers, **NextTx**, **ACK** en **failed** worden alle in een kleine lus in één thread van een C#-proces genoemd. Als er geen Tuples zijn om te verzenden, moet u in de slaap stand **NextTx** gedurende een korte periode, zoals 10 milliseconden. Deze slaap stand helpt de CPU-Beschik baarheid te voor komen.

Wanneer **NextTx** wordt aangeroepen om een nieuwe trans actie te starten, identificeert de *beveiligings onthouden* -uitvoer parameter de trans actie. De trans actie wordt ook gebruikt in **ACK** en **mislukt**. Uw **NextTx** -methode kan gegevens verzenden naar de Java-zijde. De gegevens worden opgeslagen in ZooKeeper om replay te ondersteunen. Omdat ZooKeeper beperkte capaciteit heeft, moet uw code alleen meta gegevens en geen bulksgewijze gegevens in een transactionele Spout verzenden.

Omdat Storm automatisch een mislukte trans actie opnieuw afspeelt, **mislukt de fout** gewoonlijk. Maar als SCP de meta gegevens kan controleren die worden verzonden door een transactionele Spout, kan het **mislukken** worden aangeroepen wanneer de meta gegevens ongeldig zijn.

De invoer parameter *parameters* in deze functies geeft een lege woorden lijst op en is gereserveerd voor toekomstig gebruik.

### <a name="iscpbatchbolt"></a>ISCPBatchBolt

**ISCPBatchBolt** is de interface voor een transactionele flits.

```csharp
public interface ISCPBatchBolt : ISCPPlugin
{
    void Execute(SCPTuple tuple);
    void FinishBatch(Dictionary<string, Object> parms);  
}
```

De methode **Execute** wordt aangeroepen wanneer een nieuwe tuple bij de flits arriveert. De methode **FinishBatch** wordt aangeroepen wanneer deze trans actie wordt beëindigd. De invoer parameter *parameters* is gereserveerd voor toekomstig gebruik.

**StormTxAttempt** is een belang rijke klasse voor een transactionele topologie. Het heeft twee leden: **TxId** en **AttemptId**. Het **TxId** -lid identificeert een specifieke trans actie. Een trans actie kan meerdere keren worden geprobeerd als deze mislukt en wordt opnieuw afgespeeld.

SCP.NET maakt een nieuw **ISCPBatchBolt** -object voor de verwerking van elk **StormTxAttempt** -object, net zoals bij storm in Java. Het doel van dit ontwerp is het ondersteunen van parallelle transactie verwerking. Nadat een transactie poging is voltooid, wordt het bijbehorende **ISCPBatchBolt** -object vernietigd en wordt de garbage collector verzameld.

## <a name="object-model"></a>Objectmodel

SCP.NET biedt ook een eenvoudige set van belang rijke objecten waarmee ontwikkel aars kunnen Program meren. De objecten zijn **context**, **State Store** en **SCPRuntime**. Deze worden beschreven in deze sectie.

### <a name="context"></a>Context

Het **context** -object biedt een actieve omgeving voor een toepassing. Elk **ISCPPlugin** -exemplaar van **ISCPSpout**, **ISCPBolt**, **ISCPTxSpout** of **ISCPBatchBolt** heeft een bijbehorend **context** exemplaar. De functionaliteit van de **context** is onderverdeeld in de volgende twee delen:

* Het statische deel, dat beschikbaar is in het hele C#-proces
* Het dynamische deel, dat alleen beschikbaar is voor het specifieke **context** exemplaar

### <a name="static-part"></a>Statisch deel

```csharp
public static ILogger Logger = null;
public static SCPPluginType pluginType;
public static Config Config { get; set; }
public static TopologyContext TopologyContext { get; set; }  
```

Het **logboek** object wordt verschaft voor logboek registratie doeleinden.

Het **pluginType** -object geeft het type invoeg toepassing van het C#-proces aan. Als het proces wordt uitgevoerd in de lokale test modus zonder Java, wordt het type invoeg toepassing **SCP_NET_LOCAL**.

```csharp
public enum SCPPluginType 
{
    SCP_NET_LOCAL = 0,
    SCP_NET_SPOUT = 1,
    SCP_NET_BOLT = 2,
    SCP_NET_TX_SPOUT = 3,
    SCP_NET_BATCH_BOLT = 4  
    }
```

De eigenschap **config** haalt configuratie parameters van de Java-zijde, waarmee deze worden door gegeven wanneer een C#-invoeg toepassing wordt geïnitialiseerd. De **configuratie** parameters zijn onderverdeeld in twee delen: **stormConf** en **pluginConf**.

```csharp
public Dictionary<string, Object> stormConf { get; set; }  
public Dictionary<string, Object> pluginConf { get; set; }  
```

Het deel **stormConf** is para meters die zijn gedefinieerd door storm en het onderdeel **pluginConf** is para meters die zijn gedefinieerd door SCP. Hier volgt een voorbeeld:

```csharp
public class Constants
{
    … …

    // constant string for pluginConf
    public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";  

    // constant string for stormConf
    public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";
    public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";
}
```

Het **TopologyContext** -type haalt de topologie context op. Het is het handigst voor meerdere parallelle onderdelen. Hier volgt een voorbeeld:

```csharp
//demo how to get TopologyContext info
if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
{
    Context.Logger.Info("TopologyContext info:");
    TopologyContext topologyContext = Context.TopologyContext;
    Context.Logger.Info("taskId: {0}", topologyContext.GetThisTaskId());
    taskIndex = topologyContext.GetThisTaskIndex();
    Context.Logger.Info("taskIndex: {0}", taskIndex);
    string componentId = topologyContext.GetThisComponentId();
    Context.Logger.Info("componentId: {0}", componentId);
    List<int> componentTasks = topologyContext.GetComponentTasks(componentId);  
    Context.Logger.Info("taskNum: {0}", componentTasks.Count);
}
```

### <a name="dynamic-part"></a>Dynamisch deel

De volgende interfaces zijn relevant voor een bepaald **context** exemplaar, dat wordt gemaakt door het SCP.NET-platform en wordt door gegeven aan uw code:

```csharp
// Declare the Output and Input Stream Schemas

public void DeclareComponentSchema(ComponentStreamSchema schema);

// Emit tuple to default stream.
public abstract void Emit(List<object> values);

// Emit tuple to the specific stream.
public abstract void Emit(string streamId, List<object> values);  
```

Voor een niet-transactionele Spout die bevestigingen ondersteunt, wordt de volgende methode gegeven:

```csharp
// for nontransactional spout that supports ack
public abstract void Emit(string streamId, List<object> values, long seqId);  
```

Een niet-transactionele Schicht die bevestiging ondersteunt, moet expliciet een **ACK** aanroepen of **mislukken** met de ontvangen tuple. Wanneer een nieuwe tuple wordt verzonden, moet de schicht ook de ankers van de tupel opgeven. De volgende methoden zijn aanwezig:

```csharp
public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values);
public abstract void Ack(SCPTuple tuple);
public abstract void Fail(SCPTuple tuple);
```

### <a name="statestore"></a>State Store

Het **State Store** -object biedt meta gegevens Services, monotone reeksen en wachtende coördinatie. U kunt op **State Store** op een hoger niveau gebaseerde samen vattingen van gelijktijdigheid bouwen. Deze abstracties zijn onder meer gedistribueerde vergren delingen, gedistribueerde wacht rijen, barrières en transactie Services.

SCP-toepassingen kunnen het object **status** gebruiken om gegevens in [Apache ZooKeeper](https://zookeeper.apache.org/)te serialiseren. Deze mogelijkheid is vooral nuttig voor een transactionele topologie. Als een transactionele Spout niet meer reageert en opnieuw wordt gestart, kan de **status** de benodigde gegevens ophalen uit ZooKeeper en de pijp lijn opnieuw starten.

Het object **State Store** heeft de volgende principal-methoden:

```csharp
/// <summary>
/// Static method to retrieve a state store of the given path and connStr 
/// </summary>
/// <param name="storePath">StateStore path</param>
/// <param name="connStr">StateStore address</param>
/// <returns>Instance of StateStore</returns>
public static StateStore Get(string storePath, string connStr);

/// <summary>
/// Create a new state object in this state store instance
/// </summary>
/// <returns>State from StateStore</returns>
public State Create();

/// <summary>
/// Retrieve all states that were previously uncommitted, excluding all exited states
/// </summary>
/// <returns>Uncommitted states</returns>
public IEnumerable<State> GetUnCommitted();

/// <summary>
/// Get all the states in the StateStore
/// </summary>
/// <returns>All the states</returns>
public IEnumerable<State> States();

/// <summary>
/// Get state or registry object
/// </summary>
/// <param name="info">Registry name (registry only)</param>
/// <typeparam name="T">Type, registry or state</typeparam>
/// <returns>Return registry or state</returns>
public T Get<T>(string info = null);

/// <summary>
/// List all the committed states
/// </summary>
/// <returns>Registries containing the committed state </returns>
public IEnumerable<Registry> Committed();

/// <summary>
/// List all the exited states in the StateStore
/// </summary>
/// <returns>Registries containing the exited states</returns>
public IEnumerable<Registry> Aborted();

/// <summary>
/// Retrieve an existing state object from this state store instance 
/// </summary>
/// <returns>State from StateStore</returns>
/// <typeparam name="T">stateId, id of the State</typeparam>
public State GetState(long stateId)
```

Het object **State** heeft deze principal-methoden:

```csharp
/// <summary>
/// Set the status of the state object to commit
/// </summary>
public void Commit(bool simpleMode = true);

/// <summary>
/// Set the status of the state object to exit
/// </summary>
public void Abort();

/// <summary>
/// Put an attribute value under the given key
/// </summary>
/// <param name="key">Key</param>
/// <param name="attribute">State attribute</param>
    public void PutAttribute<T>(string key, T attribute);

/// <summary>
/// Get the attribute value associated with the given key
/// </summary>
/// <param name="key">Key</param>
/// <returns>State attribute</returns>
    public T GetAttribute<T>(string key);
```

Wanneer **simpleMode** is ingesteld op **True**, wordt met de **commit** -methode de overeenkomstige ZNode in ZooKeeper verwijderd. Anders verwijdert de methode de huidige ZNode en voegt een nieuw knoop punt toe aan het doorgevoerde \_ pad.

### <a name="scpruntime"></a>SCPRuntime

De klasse **SCPRuntime** biedt de volgende twee methoden:

```csharp
public static void Initialize();

public static void LaunchPlugin(newSCPPlugin createDelegate);  
```

De **initialisatie** methode initialiseert de SCP runtime-omgeving. In deze methode verbindt het C#-proces met de Java-zijde om configuratie parameters en topologie context op te halen.

De methode **LaunchPlugin** start de lus voor het verwerken van berichten. In deze lus ontvangt de C#-invoeg toepassing berichten van de Java-zijde. Deze berichten bevatten Tuples en besturings signalen. De invoeg toepassing verwerkt vervolgens de berichten, mogelijk door de interface methode van uw code aan te roepen.

De invoer parameter voor **LaunchPlugin** is een gemachtigde. De methode kan een object retour neren dat de **ISCPSpout**-, **ISCPBolt**-, **ISCPTxSpout**-of **ISCPBatchBolt** -interface implementeert.

```csharp
public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary<string, Object> parms);
```

Voor **ISCPBatchBolt** kunt u een **StormTxAttempt** -object ophalen uit de para meter *parameters* en gebruiken om te beoordelen of de poging een opnieuw gespelde poging is. De controle op een replay-poging wordt vaak uitgevoerd op de door voering. In het voor beeld HelloWorldTx verderop in dit artikel wordt deze controle gedemonstreerd.

SCP-invoeg toepassingen kunnen meestal in twee modi worden uitgevoerd: lokale test modus en normale modus.

#### <a name="local-test-mode"></a>Lokale test modus

In deze modus worden de SCP-invoeg toepassingen in uw C#-code binnen de ontwikkelings fase uitgevoerd in Visual Studio. U kunt de **ILocalContext** -interface gebruiken in deze modus. De interface biedt methoden om de verzonden Tuples te serialiseren naar lokale bestanden en deze terug te lezen in het RAM-geheugen.

```csharp
public interface ILocalContext
{
    List<SCPTuple> RecvFromMsgQueue();
    void WriteMsgQueueToFile(string filepath, bool append = false);  
    void ReadFromFileToMsgQueue(string filepath);
}
```

#### <a name="regular-mode"></a>Normale modus

In deze modus voert het Storm Java-proces de SCP-invoeg toepassingen uit. Hier volgt een voor beeld:

```csharp
namespace Scp.App.HelloWorld
{
public class Generator : ISCPSpout
{
    … …
    public static Generator Get(Context ctx, Dictionary<string, Object> parms)
    {
    return new Generator(ctx);
    }
}

class HelloWorld
{
    static void Main(string[] args)
    {
    /* Setting the environment variable here can change the log file name */
    System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "HelloWorld");

    SCPRuntime.Initialize();
    SCPRuntime.LaunchPlugin(new newSCPPlugin(Generator.Get));
    }
}
}
```

## <a name="topology-specification-language"></a>Taal van topologie specificatie

De SCP-topologie specificatie is een domein-specifieke taal (DSL) voor het beschrijven en configureren van SCP-topologieën. Het is gebaseerd op [de CLOJURE DSL van Storm](https://storm.incubator.apache.org/documentation/Clojure-DSL.html) en wordt uitgebreid door SCP.

U kunt topologie specificaties rechtstreeks bij een storm-cluster indienen voor uitvoering via de opdracht **runSpec** .

SCP.NET heeft de volgende functies toegevoegd voor het definiëren van transactionele topologieën:

| Nieuwe functie | Parameters | Beschrijving |
| --- | --- | --- |
| **TX-topolopy** |*topologie-naam*<br />*Spout-kaart*<br />*bout-kaart* |Hiermee wordt een transactionele topologie gedefinieerd met de naam van de topologie, de spouts-definitie en de definitie toewijzing van de schicht. |
| **SCP-TX-Spout** |*exec-naam*<br />*argumenten*<br />*bedragvelden* |Hiermee wordt een transactionele Spout gedefinieerd. De functie voert de toepassing uit die is opgegeven door *exec-name* en gebruikt *argumenten*.<br /><br />Met de para meter *Fields* geeft u de uitvoer velden voor de Spout op. |
| **SCP-TX-batch-Schicht** |*exec-naam*<br />*argumenten*<br />*bedragvelden* |Hiermee wordt een transactionele batch-Schicht gedefinieerd. De functie voert de toepassing uit die is opgegeven door *exec-name* en gebruikt *argumenten.*<br /><br />De para meter *Fields* geeft de uitvoer velden voor de bout op. |
| **SCP-TX-door Voer-flits** |*exec-naam*<br />*argumenten*<br />*bedragvelden* |Hiermee wordt een transactionele doorvoer flits gedefinieerd. De functie voert de toepassing uit die is opgegeven door *exec-name* en gebruikt *argumenten*.<br /><br />De para meter *Fields* geeft de uitvoer velden voor de bout op. |
| **nontx-topologie** |*topologie-naam*<br />*Spout-kaart*<br />*bout-kaart* |Hiermee wordt een niet-transactionele topologie gedefinieerd met de naam van de topologie, de spouts en de definitie toewijzing. |
| **SCP-Spout** |*exec-naam*<br />*argumenten*<br />*bedragvelden*<br />*instellen* |Hiermee wordt een niet-transactionele Spout gedefinieerd. De functie voert de toepassing uit die is opgegeven door *exec-name* en gebruikt *argumenten*.<br /><br />Met de para meter *Fields* geeft u de uitvoer velden voor de Spout op.<br /><br />De *para meter parameters* is optioneel. Gebruik het om para meters als "niet-transactioneel. ack. enabled" op te geven. |
| **SCP-flits** |*exec-naam*<br />*argumenten*<br />*bedragvelden*<br />*instellen* |Hiermee wordt een niet-transactionele Schicht gedefinieerd. De functie voert de toepassing uit die is opgegeven door *exec-name* en gebruikt *argumenten*.<br /><br />Met de para meter *Fields* worden de uitvoer velden voor de schicht opgegeven<br /><br />De *para meter parameters* is optioneel. Gebruik het om para meters als "niet-transactioneel. ack. enabled" op te geven. |

SCP.NET definieert de volgende sleutel woorden:

| Zoek | Beschrijving |
| --- | --- |
| **: naam** |De naam van de topologie |
| **: topologie** |De topologie met behulp van de functies in de vorige tabel en ingebouwde functies |
| **:p** |De parallellisme Hint voor elke Spout of Schicht |
| **: config** |Hiermee wordt aangegeven of para meters moeten worden geconfigureerd of de bestaande moeten worden bijgewerkt |
| **: schema** |Het schema van de stroom |

SCP.NET definieert ook deze vaak gebruikte para meters:

| Parameter | Beschrijving |
| --- | --- |
| ' plugin.name ' |De naam van het. exe-bestand van de C#-invoeg toepassing |
| "plugin. args" |De argumenten voor de invoeg toepassing |
| "uitvoer. schema" |Het uitvoer schema |
| "niet-transactioneel. ack. enabled" |Of bevestiging is ingeschakeld voor een niet-transactionele topologie |

De **runSpec** -opdracht wordt samen met de bits geïmplementeerd. Dit is het gebruik van de opdracht:

```csharp
.\bin\runSpec.cmd
usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target
```

De para meter *resource-dir* is optioneel. Geef het op wanneer u een C#-toepassing wilt aansluiten. De opgegeven map bevat de toepassing, afhankelijkheden en configuraties.

De para meter *classpath* is ook optioneel. Hiermee wordt het Java classpath opgegeven als het specificatie bestand een Java-Spout of-flits bevat.

## <a name="miscellaneous-features"></a>Diverse functies

### <a name="input-and-output-schema-declarations"></a>Invoer-en uitvoer schema declaraties

Uw C#-processen kunnen Tuples verzenden. Hiertoe worden door het platform Tuples naar **byte []** -objecten geserialiseerd en worden de objecten naar de Java-zijde overgebracht. Storm brengt deze Tuples vervolgens over naar de doelen.

In downstream-onderdelen worden door C# processen weer gegeven van de Java-zijde en kunnen ze worden geconverteerd naar de oorspronkelijke typen van het platform. Al deze bewerkingen worden verborgen door het platform.

Voor de ondersteuning van serialisatie en deserialisatie moet uw code het schema van de invoer en uitvoer declareren. Het schema wordt gedefinieerd als een woorden lijst. De stream-ID is de woordenlijst sleutel. De sleutel waarde is de typen kolommen. Een onderdeel kan meerdere streams declareren.

```csharp
public class ComponentStreamSchema
{
    public Dictionary<string, List<Type>> InputStreamSchema { get; set; }
    public Dictionary<string, List<Type>> OutputStreamSchema { get; set; }
    public ComponentStreamSchema(Dictionary<string, List<Type>> input, Dictionary<string, List<Type>> output)
    {
        InputStreamSchema = input;
        OutputStreamSchema = output;
    }
}
```

De volgende functie wordt toegevoegd aan een **context** object:

```csharp
public void DeclareComponentSchema(ComponentStreamSchema schema)
```

Ontwikkel aars moeten ervoor zorgen dat de verzonden Tuples het schema volgen dat is gedefinieerd voor een stroom. Anders wordt er een runtime-uitzonde ring gegenereerd.

### <a name="multistream-support"></a>Ondersteuning voor multistream

Met SCP kan uw code op hetzelfde moment meerdere streams verzenden of ontvangen. In het **context** object wordt deze ondersteuning weer gegeven als de optionele stream id-para meter van de **Verzend** methode.

Er zijn twee methoden in het **context** object SCP.net toegevoegd. Ze verzenden een of meer Tuples naar specifieke streams. De para meter *streamId* is een teken reeks. De waarde moet in zowel C#-code als de topologie definitie worden opgegeven.

```csharp
/* Emit tuple to the specific stream. */
public abstract void Emit(string streamId, List<object> values);

/* for nontransactional spout only */
public abstract void Emit(string streamId, List<object> values, long seqId);
```

Wanneer een niet-bestaande stroom wordt verzonden, worden runtime-uitzonde ringen veroorzaakt.

### <a name="fields-grouping"></a>Groepering van velden

De ingebouwde veld groepering in Storm werkt niet goed in SCP.NET. Op de Java-proxy zijde is het gegevens type van alle velden in feite **byte []**. De groepering van velden maakt gebruik van de hash-code van het **byte []** -object om de groepering uit te voeren. De hash-code is het adres van dit object in het RAM-geheugen. Daarom is de groepering onjuist voor multi byte-objecten die dezelfde inhoud delen, maar niet hetzelfde adres.

SCP.NET voegt een aangepaste groeperings methode toe en maakt gebruik van de inhoud van het object **byte []** om de groepering uit te voeren. In een specificatie bestand ziet de syntaxis eruit als in dit voor beeld:

```csharp
(bolt-spec
    {
        "spout_test" (scp-field-group :non-tx [0,1])
    }
    …
)
```

In het voor gaande specificatie bestand:

* `scp-field-group` Hiermee geeft u op dat de groepering een aangepaste veld groepering is die door SCP wordt geïmplementeerd.
* `:tx` of `:non-tx` geeft aan of de topologie transactioneel is. U hebt deze informatie nodig omdat de begin index verschilt van transactionele en niet-transactionele topologieën.
* `[0,1]` Hiermee geeft u een hash-set veld-Id's op die beginnen met nul.

### <a name="hybrid-topology"></a>Hybride topologie

Systeem eigen Storm code wordt geschreven in Java. SCP.NET heeft Storm verbeterd zodat u C#-code kunt schrijven om uw bedrijfs logica te verwerken. Maar SCP.NET biedt ook ondersteuning voor hybride topologieën die niet alleen C# spouts/schichten bevatten, maar ook Java spouts/schichts.

### <a name="specify-java-spoutbolt-in-a-specification-file"></a>Java Spout/Schicht in een specificatie bestand opgeven

U kunt **SCP-Spout** en **SCP-Schicht** in een specificatie bestand gebruiken om Java-spouts en-bouten op te geven. Hier volgt een voorbeeld:

```csharp
(spout-spec 
  (microsoft.scp.example.HybridTopology.Generator.)
  :p 1)
```

Dit `microsoft.scp.example.HybridTopology.Generator` is de naam van de Java Spout-klasse.

### <a name="specify-the-java-classpath-in-a-runspec-command"></a>Geef het Java-klassenpad op in een runSpec-opdracht

Als u een topologie wilt indienen die Java-spouts of-grendels bevat, moet u deze eerst compileren om JAR-bestanden te maken. Geef vervolgens het Java-klassenpad met de JAR-bestanden op wanneer u een topologie verzendt. Hier volgt een voorbeeld:

```csharp
bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*
```

Hier `examples\HybridTopology\java\target\` ziet u de map met het bestand Java Spout/SCHICHT jar.

### <a name="serialization-and-deserialization-between-java-and-c"></a>Serialisatie en deserialisatie van Java en C #

Een SCP-onderdeel bevat de Java-zijde en de C#-zijde. Voor interactie met systeem eigen Java spouts/-flitsen moet serialisatie en deserialisatie plaatsvinden tussen de Java-kant en de C#, zoals wordt geïllustreerd in de volgende grafiek:

:::image type="content" source="./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png" alt-text="Diagram van Java-onderdeel dat wordt verzonden naar een SCP-onderdeel, dat vervolgens naar een ander Java-onderdeel verzendt" border="false":::

#### <a name="serialization-in-the-java-side-and-deserialization-in-the-c-side"></a>Serialisatie in de Java-zijde en deserialisatie in de C#-zijde

Geef eerst de standaard implementatie voor serialisatie op in de Java-zijde en deserialisatie in de C#-zijde.

Geef de serialisatie methode van de Java-zijde op in een specificatie bestand.

```csharp
(scp-bolt
    {
        "plugin.name" "HybridTopology.exe"
        "plugin.args" ["displayer"]
        "output.schema" {}
        "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
    })
```

Specificeer de methode voor het deserialiseren van de C#-Side in uw C#-code.

```csharp
Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
inputSchema.Add("default", new List<Type>() { typeof(Person) });
this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());
```  

Als het gegevens type niet te complex is, moeten deze standaard implementatie de meeste gevallen verwerken. Hier volgen enkele gevallen waarin u uw eigen implementatie kunt aansluiten:

* Het gegevens type is te complex voor de standaard implementatie.
* De prestaties van uw standaard implementatie voldoen niet aan uw vereisten.

De serialisatie-interface in de Java-zijde wordt gedefinieerd als:

```csharp
public interface ICustomizedInteropJavaSerializer {
    public void prepare(String[] args);
    public List<ByteBuffer> serialize(List<Object> objectList);
}
```

De deserialisatie-interface in de C#-zijde is als volgt gedefinieerd:

```csharp
public interface ICustomizedInteropCSharpDeserializer
{
    List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
}
```

#### <a name="serialization-in-the-c-side-and-deserialization-in-the-java-side"></a>Serialisatie in de C#-en deserialisatie in de Java-zijde

Geef de serialisatie methode van de C#-zijde op in uw C#-code.

```csharp
this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 
```

Geef de deserialisatie methode van de Java-zijde op in een specificatie bestand.

```csharp
(scp-spout
   {
     "plugin.name" "HybridTopology.exe"
     "plugin.args" ["generator"]
     "output.schema" {"default" ["person"]}
     "customized.java.deserializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" "microsoft.scp.example.HybridTopology.Person"]
   }
)
```

Hier `"microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer"` is de naam van de deserializer en de `"microsoft.scp.example.HybridTopology.Person"` doel klasse waarin de gegevens worden gedeserialiseerd.

U kunt ook uw eigen implementatie van een C#-serialisatiefunctie en een Java-deserializer aansluiten.

Deze code is de interface voor de C#-serialisatiefunctie:

```csharp
public interface ICustomizedInteropCSharpSerializer
{
    List<byte[]> Serialize(List<object> dataList);
}
```

Deze code is de interface voor de Java-deserializer:

```csharp
public interface ICustomizedInteropJavaDeserializer {
    public void prepare(String[] targetClassNames);
    public List<Object> Deserialize(List<ByteBuffer> dataList);
}
```

## <a name="scp-host-mode"></a>SCP-host-modus

In deze modus kunt u uw code als een DLL-bestand compileren en SCPHost.exe gebruiken zoals wordt verstrekt door SCP om een topologie in te dienen. Een specificatie bestand ziet er als volgt uit:

```csharp
(scp-spout
  {
    "plugin.name" "SCPHost.exe"
    "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
    "output.schema" {"default" ["sentence"]}
  })
```

Hier `"plugin.name"` wordt opgegeven als `"SCPHost.exe"` , die wordt opgegeven door de SCP-SDK. SCPHost.exe accepteert drie para meters in de volgende volg orde:

1. De DLL-naam, die `"HelloWorld.dll"` in dit voor beeld is.
1. De naam van de klasse, `"Scp.App.HelloWorld.Generator"` in dit voor beeld.
1. De naam van een open bare statische methode die kan worden aangeroepen om een exemplaar van **ISCPPlugin** op te halen.

Compileer de code in de host-modus als een DLL-bestand voor het aanroepen van het SCP-platform. Omdat het platform vervolgens volledig beheer over de gehele verwerkings logica kan krijgen, raden we u aan om de topologie in de SCP-host-modus in te dienen. Dit vereenvoudigt de ontwikkelings ervaring. Daarnaast beschikt u over meer flexibiliteit en betere achterwaartse compatibiliteit voor latere releases.

## <a name="scp-programming-examples"></a>Voor beelden van SCP-programmering

### <a name="helloworld"></a>HelloWorld

In het volgende eenvoudige HelloWorld-voor beeld ziet u een smaak van SCP.NET. Er wordt gebruikgemaakt van een niet-transactionele topologie met een Spout met de naam **Generator** en twee bouten met de naam **splitter** en **Counter**. De **Generator** Spout wille keurig zinnen genereren en deze zinnen naar **splitter** verzenden. De **Splits** Schicht splitst de zinnen in woorden en verzendt deze woorden naar de **Counter** -bout. De **Counter** -bout gebruikt een woorden lijst om het exemplaar van elk woord vast te leggen.

Dit voor beeld heeft twee specificatie bestanden: HelloWorld. SPEC en HelloWorld \_ EnableAck. spec. Met de C#-code kunt u nagaan of er een bevestiging is ingeschakeld door het `pluginConf` object van de Java-zijde op te halen.

```csharp
/* demo how to get pluginConf info */
if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
{
    enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
}
Context.Logger.Info("enableAck: {0}", enableAck);
```

Als er een bevestiging is ingeschakeld in de Spout, slaat een woorden lijst de Tuples op die nog niet zijn bevestigd. Als `Fail` is aangeroepen, wordt de mislukte tuple opnieuw afgespeeld.

```csharp
public void Fail(long seqId, Dictionary<string, Object> parms)
{
    Context.Logger.Info("Fail, seqId: {0}", seqId);
    if (cachedTuples.ContainsKey(seqId))
    {
        /* get the cached tuple */
        string sentence = cachedTuples[seqId];

        /* replay the failed tuple */
        Context.Logger.Info("Re-Emit: {0}, seqId: {1}", sentence, seqId);
        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), seqId);
    }
    else
    {
        Context.Logger.Warn("Fail(), can't find cached tuple for seqId {0}!", seqId);
    }
}
```

### <a name="helloworldtx"></a>HelloWorldTx

In het volgende HelloWorldTx-voor beeld ziet u hoe u transactionele topologie implementeert. Het voor beeld heeft één Spout genaamd **Generator**, een batch-Schicht met de naam **gedeeltelijke Count** en een commit-Schicht met de naam **Count-Sum**. Het voor beeld heeft ook drie bestaande tekst bestanden: DataSource0.txt, DataSource1.txt en DataSource2.txt.

In elke trans actie Spout de **Generator** twee bestanden uit de bestaande drie bestanden wille keurig selecteren en worden de twee bestands namen verzonden naar de schicht van het **gedeeltelijke aantal** . De schicht van het **gedeeltelijke aantal** :

1. Hiermee haalt u een bestands naam op uit de ontvangen tuple.
1. Hiermee opent u het bijbehorende bestand.
1. Telt het aantal woorden in het bestand.
1. Het aantal woorden wordt verzonden naar het **aantal-Sum-** schicht.

Het **aantal-Sum-** schicht geeft een samen vatting van het totale aantal.

Voor het uitvoeren van de functie voor het **tellen van het aantal-Sum** moet worden beoordeeld of het een opnieuw geplayte trans actie is. In dit voor beeld heeft het de volgende statische lidvariabele:

```csharp
public static long lastCommittedTxId = -1; 
```

Wanneer er een **ISCPBatchBolt** -exemplaar wordt gemaakt, wordt de waarde van het `txAttempt` object opgehaald uit de invoer parameters.

```csharp
public static CountSum Get(Context ctx, Dictionary<string, Object> parms)
{
    /* for transactional topology, we can get txAttempt from the input parms */
    if (parms.ContainsKey(Constants.STORM_TX_ATTEMPT))
    {
        StormTxAttempt txAttempt = (StormTxAttempt)parms[Constants.STORM_TX_ATTEMPT];
        return new CountSum(ctx, txAttempt);
    }
    else
    {
        throw new Exception("null txAttempt");
    }
}
```

Wanneer `FinishBatch` wordt aangeroepen, `lastCommittedTxId` wordt bijgewerkt als het geen opnieuw gespeelde trans actie is.

```csharp
public void FinishBatch(Dictionary<string, Object> parms)
{
    /* judge whether it is a replayed transaction */
    bool replay = (this.txAttempt.TxId <= lastCommittedTxId);

    if (!replay)
    {
        /* If it is not replayed, update the totalCount and lastCommittedTxId value */
        totalCount = totalCount + this.count;
        lastCommittedTxId = this.txAttempt.TxId;
    }
    … …
}
```

### <a name="hybridtopology"></a>HybridTopology

Deze topologie bevat een Java-Spout en een C#-schicht. Hierbij wordt gebruikgemaakt van de standaard implementatie van serialisatie en deserialisatie van het SCP-platform. Zie het bestand HybridTopology. spec in de \\ HybridTopology-map voor beelden voor de details van het specificatie bestand. Zie ook SubmitTopology.bat voor het opgeven van het Java-klassenpad.

### <a name="scphostdemo"></a>SCPHostDemo

Dit voor beeld is in essentie hetzelfde als HelloWorld. Het enige verschil is dat uw code wordt gecompileerd als een DLL-bestand en de topologie wordt verzonden met behulp van SCPHost.exe. Zie de sectie SCP-host modus voor een gedetailleerde uitleg.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor voor beelden van Apache Storm topologieën die zijn gemaakt met SCP:

* [C#-topologieën ontwikkelen voor Apache Storm op HDInsight met behulp van Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [Gebeurtenissen verwerken vanuit Azure Event Hubs met Apache Storm in HDInsight](apache-storm-develop-csharp-event-hub-topology.md)
* [Voertuig sensor gegevens van Event Hubs verwerken met Apache Storm op HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [ETL van Azure Event Hubs uitpakken, transformeren en laden naar Apache HBase](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)

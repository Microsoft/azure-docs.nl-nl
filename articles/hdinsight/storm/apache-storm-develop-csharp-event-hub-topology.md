---
title: Gebeurtenissen verwerken van Event Hubs met Storm-Azure HDInsight
description: Meer informatie over het verwerken van gegevens van Azure Event Hubs met een C# Storm-topologie die is gemaakt in Visual Studio, met behulp van de HDInsight-hulpprogram ma's voor Visual Studio.
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/14/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: ee483872535c58bde521cf0a751058fdc2fecd40
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871449"
---
# <a name="process-events-from-azure-event-hubs-with-apache-storm-on-hdinsight-c"></a>Gebeurtenissen uit Azure Event Hubs verwerken met Apache Storm in HDInsight (C#)

Meer informatie over het werken met Azure Event Hubs van [Apache Storm](https://storm.apache.org/) op HDInsight. In dit document wordt een C# Storm-topologie gebruikt voor het lezen en schrijven van gegevens van Event Hubs

> [!NOTE]  
> Zie [gebeurtenissen verwerken vanuit Azure Event hubs met Apache Storm op HDInsight (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)voor een Java-versie van dit project.

## <a name="scpnet"></a>SCP.NET

De stappen in dit document gebruiken SCP.NET, een NuGet-pakket waarmee u eenvoudig C#-topologieën en-onderdelen kunt maken voor gebruik met Storm op HDInsight.

HDInsight 3,4 en hoger gebruiken mono om C#-topologieën uit te voeren. Het voor beeld dat in dit document wordt gebruikt, werkt met HDInsight 3,6. Als u van plan bent uw eigen .NET-oplossingen voor HDInsight te maken, controleert u het document met [zwartwit compatibiliteit](https://www.mono-project.com/docs/about-mono/compatibility/) voor mogelijke incompatibiliteit.

### <a name="cluster-versioning"></a>Cluster versie beheer

Het micro soft. SCP. net. SDK NuGet-pakket dat u voor uw project gebruikt, moet overeenkomen met de primaire versie van Storm geïnstalleerd op HDInsight. HDInsight-versies 3,5 en 3,6 gebruiken Storm 1. x, dus u moet SCP.NET versie 1.0. x. x met deze clusters gebruiken.

C#-topologieën moeten ook .NET 4,5-doel gericht zijn.

## <a name="how-to-work-with-event-hubs"></a>Werken met Event Hubs

Micro soft biedt een set Java-onderdelen die kunnen worden gebruikt om te communiceren met Event Hubs vanuit een storm-topologie. U vindt het bestand met het Java-archief (JAR) dat een met HDInsight 3,6 compatibele versie van deze onderdelen bevat op [https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar) .

> [!IMPORTANT]  
> Hoewel de onderdelen in Java zijn geschreven, kunt u ze eenvoudig gebruiken vanuit een C#-topologie.

De volgende onderdelen worden gebruikt in dit voor beeld:

* __EventHubSpout__: gegevens uit Event hubs worden gelezen.
* __EventHubBolt__: schrijft gegevens naar Event hubs.
* __EventHubSpoutConfig__: wordt gebruikt voor het configureren van EventHubSpout.
* __EventHubBoltConfig__: wordt gebruikt voor het configureren van EventHubBolt.

### <a name="example-spout-usage"></a>Voor beeld van Spout-gebruik

SCP.NET biedt methoden voor het toevoegen van een EventHubSpout aan uw topologie. Deze methoden maken het gemakkelijker om een Spout toe te voegen dan het gebruik van de algemene methoden voor het toevoegen van een Java-onderdeel. In het volgende voor beeld ziet u hoe u een Spout maakt met behulp van de __SetEventHubSpout__ -en **EventHubSpoutConfig** -methoden van SCP.net:

```csharp
 topologyBuilder.SetEventHubSpout(
    "EventHubSpout",
    new EventHubSpoutConfig(
        ConfigurationManager.AppSettings["EventHubSharedAccessKeyName"],
        ConfigurationManager.AppSettings["EventHubSharedAccessKey"],
        ConfigurationManager.AppSettings["EventHubNamespace"],
        ConfigurationManager.AppSettings["EventHubEntityPath"],
        eventHubPartitions),
    eventHubPartitions);
```

In het vorige voor beeld wordt een nieuw Spout-onderdeel met de naam __EventHubSpout__ gemaakt en geconfigureerd om te communiceren met een event hub. De parallellisme Hint voor het onderdeel wordt ingesteld op het aantal partities in de Event Hub. Met deze instelling kan Storm een exemplaar van het onderdeel maken voor elke partitie.

### <a name="example-bolt-usage"></a>Voor beeld van bout gebruik

Gebruik de methode **JavaComponmentConstructor** om een exemplaar van de schicht te maken. In het volgende voor beeld ziet u hoe u een nieuw exemplaar van de **EventHubBolt** maakt en configureert:

```csharp
// Java construcvtor for the Event Hub Bolt
JavaComponentConstructor constructor = JavaComponentConstructor.CreateFromClojureExpr(
    String.Format(@"(org.apache.storm.eventhubs.bolt.EventHubBolt. (org.apache.storm.eventhubs.bolt.EventHubBoltConfig. " +
        @"""{0}"" ""{1}"" ""{2}"" ""{3}"" ""{4}"" {5}))",
        ConfigurationManager.AppSettings["EventHubPolicyName"],
        ConfigurationManager.AppSettings["EventHubPolicyKey"],
        ConfigurationManager.AppSettings["EventHubNamespace"],
        "servicebus.windows.net",
        ConfigurationManager.AppSettings["EventHubName"],
        "true"));

// Set the bolt to subscribe to data from the spout
topologyBuilder.SetJavaBolt(
    "eventhubbolt",
    constructor,
    partitionCount)
        .shuffleGrouping("Spout");
```

> [!NOTE]  
> In dit voor beeld wordt een clojure-expressie gebruikt die als teken reeks is door gegeven, in plaats van **JavaComponentConstructor** te gebruiken om een **EventHubBoltConfig** te maken, omdat het Spout-voor beeld. Beide methoden werken. Gebruik de methode die het beste bij u past.

## <a name="download-the-completed-project"></a>Het voltooide project downloaden

U kunt een volledige versie downloaden van het project dat in dit artikel is gemaakt op basis van [github](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub). U moet echter nog steeds configuratie-instellingen opgeven door de stappen in dit artikel te volgen.

### <a name="prerequisites"></a>Vereisten

* Een Apache Storm-cluster in HDInsight. Zie [Apache Hadoop-clusters maken met behulp van de Azure-portal](../hdinsight-hadoop-create-linux-clusters-portal.md) en selecteer **Storm** voor **Clustertype**.

* Een [Azure-Event hub](../../event-hubs/event-hubs-create.md).

* De [Azure .NET SDK](https://azure.microsoft.com/downloads/).

* De [HDInsight-hulpprogram ma's voor Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

* Java JDK 1,8 of hoger in uw ontwikkel omgeving. JDK-down loads zijn beschikbaar via [Oracle](/azure/developer/java/fundamentals/java-jdk-long-term-support).

  * De omgevings variabele **JAVA_HOME** moet verwijzen naar de map die Java bevat.
  * De map **% JAVA_HOME%/bin** moet zich in het pad bevinden.

## <a name="download-the-event-hubs-components"></a>De Event Hubs-onderdelen downloaden

Down load het Event Hubs Spout-en-bout-onderdeel van [https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar) .

Maak een map `eventhubspout` met de naam en sla het bestand op in de map.

## <a name="configure-event-hubs"></a>Event Hubs configureren

Event Hubs is de gegevens bron voor dit voor beeld. Gebruik de informatie in de sectie ' een Event Hub maken ' van aan de [slag met Event hubs](../../event-hubs/event-hubs-create.md).

1. Nadat de Event Hub is gemaakt, bekijkt u de instellingen voor **EventHub** in de Azure Portal en selecteert u **beleid voor gedeelde toegang**. Selecteer **+ toevoegen** om het volgende beleid te maken:

   | Name | Machtigingen |
   | --- | --- |
   | Writer |Verzenden |
   | gelezen |Luisteren |

    :::image type="content" source="./media/apache-storm-develop-csharp-event-hub-topology/share-access-policies.png" alt-text="Scherm afbeelding van het venster toegangs beleid delen" border="true":::

2. Selecteer het beleid voor **lezer** en **Writer** . Kopieer de primaire-sleutel waarde voor beide beleids regels en sla deze op. deze waarden worden later gebruikt.

## <a name="configure-the-eventhubwriter"></a>De EventHubWriter configureren

1. Zie [aan de slag met hdinsight-hulpprogram ma's voor Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)als u de nieuwste versie van de HDInsight-hulpprogram Ma's voor Visual Studio nog niet hebt geïnstalleerd.

2. Down load de oplossing van [eventhub-Storm-hybride](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

3. Open **EventHubExample. SLN**. Open het **App.config** -bestand in het project **EventHubWriter** . Gebruik de informatie uit de Event Hub die u eerder hebt geconfigureerd om de waarde voor de volgende sleutels in te vullen:

   | Sleutel | Waarde |
   | --- | --- |
   | EventHubPolicyName |Writer (als u een andere naam voor het beleid met de machtiging *verzenden* hebt gebruikt, gebruikt u dit in plaats daarvan.) |
   | EventHubPolicyKey |De sleutel voor het Writer-beleid. |
   | EventHubNamespace |De naam ruimte die uw Event Hub bevat. |
   | EventHubName |De naam van uw Event Hub. |
   | EventHubPartitionCount |Het aantal partities in uw Event Hub. |

4. Sla het **App.config** bestand op en sluit het.

## <a name="configure-the-eventhubreader"></a>De EventHubReader configureren

1. Open het **EventHubReader** -project.

2. Open het **App.config** -bestand voor de **EventHubReader**. Gebruik de informatie uit de Event Hub die u eerder hebt geconfigureerd om de waarde voor de volgende sleutels in te vullen:

   | Sleutel | Waarde |
   | --- | --- |
   | EventHubPolicyName |lezer (als u een andere naam voor het beleid hebt gebruikt met de machtiging *Luis teren* , moet u deze gebruiken.) |
   | EventHubPolicyKey |De sleutel voor het Lees beleid. |
   | EventHubNamespace |De naam ruimte die uw Event Hub bevat. |
   | EventHubName |De naam van uw Event Hub. |
   | EventHubPartitionCount |Het aantal partities in uw Event Hub. |

3. Sla het **App.config** bestand op en sluit het.

## <a name="deploy-the-topologies"></a>De topologieën implementeren

1. Klik vanuit **Solution Explorer** met de rechter muisknop op het project **EventHubReader** en selecteer **verzenden naar Storm op HDInsight**.

    :::image type="content" source="./media/apache-storm-develop-csharp-event-hub-topology/submit-to-apache-storm.png" alt-text="Scherm opname van Solution Explorer, met verzenden naar Storm op HDInsight gemarkeerd" border="true":::

2. Selecteer in het dialoog venster **indienings topologie** het **Storm-cluster**. Vouw **extra configuraties** uit, selecteer **Java-bestands paden**, selecteer **...** en selecteer de map met het jar-bestand dat u eerder hebt gedownload. Klik ten slotte op **verzenden**.

    :::image type="content" source="./media/apache-storm-develop-csharp-event-hub-topology/submit-storm-topology.png" alt-text="Scherm afbeelding van het dialoog venster verzend topologie" border="true":::

3. Wanneer de topologie is verzonden, wordt de **Storm-topologie weergave** weer gegeven. Als u informatie over de topologie wilt weer geven, selecteert u de **EventHubReader** -topologie in het linkerdeel venster.

    :::image type="content" source="./media/apache-storm-develop-csharp-event-hub-topology/storm-topology-viewer.png" alt-text="Scherm afbeelding van de viewer voor Storm-topologieën" border="true":::

4. Klik vanuit **Solution Explorer** met de rechter muisknop op het project **EventHubWriter** en selecteer **verzenden naar Storm op HDInsight**.

5. Selecteer in het dialoog venster **indienings topologie** het **Storm-cluster**. Vouw **extra configuraties** uit, selecteer **Java-bestands paden**, selecteer **...** en selecteer de directory met het jar-bestand dat u eerder hebt gedownload. Klik ten slotte op **verzenden**.

6. Wanneer de topologie is verzonden, vernieuwt u de topologie lijst in de **Viewer voor Storm-topologieën** om te controleren of beide topologieën op het cluster worden uitgevoerd.

7. Selecteer in de weer gave **Storm-topologieën** de **EventHubReader** -topologie.

8. Als u het onderdeel overzicht voor de schicht wilt openen, dubbelklikt u op het onderdeel **LogBolt** in het diagram.

9. Selecteer in de sectie **uitvoerendeers** een van de koppelingen in de kolom **poort** . Hiermee wordt informatie weer gegeven die door het onderdeel is geregistreerd. De geregistreerde gegevens zijn vergelijkbaar met de volgende tekst:

    ```output
    2017-03-02 14:51:29.255 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,255 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1830978598,"deviceId":"8566ccbc-034d-45db-883d-d8a31f34068e"}
    2017-03-02 14:51:29.283 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,283 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1756413275,"deviceId":"647a5eff-823d-482f-a8b4-b95b35ae570b"}
    2017-03-02 14:51:29.313 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,312 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1108478910,"deviceId":"206a68fa-8264-4d61-9100-bfdb68ee8f0a"}
    ```

## <a name="stop-the-topologies"></a>De topologieën stoppen

Als u de topologieën wilt stoppen, selecteert u elke topologie in de **Storm-topologie Viewer** en klikt u vervolgens op **Kill**.

:::image type="content" source="./media/apache-storm-develop-csharp-event-hub-topology/kill-storm-topology1.png" alt-text="Scherm afbeelding van Storm-topologie viewer met de knop voor het afsluiten van de weer gave" border="true":::

## <a name="delete-your-cluster"></a>Uw cluster verwijderen

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u de Java Event Hubs Spout en de schicht van een C#-topologie kunt gebruiken om te werken met gegevens in azure Event Hubs. Zie het volgende voor meer informatie over het maken van C#-topologieën:

* [C#-topologieën ontwikkelen voor Apache Storm op HDInsight met behulp van Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [SCP-programmeer gids](apache-storm-scp-programming-guide.md)
* [Example Storm toplogies and components for Apache Storm on HDInsight](apache-storm-example-topology.md) (Voorbeelden van Storm-topologieën en -onderdelen in HDInsight)
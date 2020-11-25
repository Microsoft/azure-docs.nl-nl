---
title: Apache Storm-topologieën implementeren en beheren in Azure HDInsight
description: Meer informatie over het implementeren, bewaken en beheren van Apache Storm topologieën met behulp van het Storm-dash board op HDInsight op basis van Linux. Gebruik Hadoop-hulpprogram ma's voor Visual Studio.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: 9b190b5847c7412344e2bb09fd4000985816219b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995587"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-azure-hdinsight"></a>Apache Storm-topologieën implementeren en beheren in Azure HDInsight

In dit document leert u de basis beginselen van het beheren en bewaken van [Apache Storm](https://storm.apache.org/) topologieën die worden uitgevoerd op Storm op HDInsight-clusters.

## <a name="prerequisites"></a>Vereisten

* Een Apache Storm-cluster in HDInsight. Zie [Apache Hadoop-clusters maken met behulp van de Azure-portal](../hdinsight-hadoop-create-linux-clusters-portal.md) en selecteer **Storm** voor **Clustertype**.

* Beschrijving Vertrouwd met de Secure Shell (SSH) en Secure Copy (SCP). Zie voor meer informatie [Verbinding maken met HDInsight (Apache Hadoop) via SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Beschrijving Visual Studio, Azure SDK 2.5.1 of nieuwer en de Data Lake-Hulpprogram Ma's voor Visual Studio. Zie [Apache Hadoop & Visual Studio data Lake-Hulpprogram ma's](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)voor meer informatie.

## <a name="submit-a-topology-using-visual-studio"></a>Een topologie verzenden met Visual Studio

U kunt de Data Lake-Hulpprogram Ma's voor Visual Studio gebruiken om C#-of hybride topologieën naar uw Storm-cluster te verzenden. In de volgende stappen wordt een voorbeeld toepassing gebruikt. Zie [Apache Storm-topologieën met Visual Studio en C#](apache-storm-develop-csharp-visual-studio-topology.md)voor meer informatie over het maken van een topologie met behulp van de data Lake-hulpprogram ma's.

1. Als u de nieuwste versie van de Data Lake-hulpprogram ma's voor Visual Studio nog niet hebt geïnstalleerd, raadpleegt u [Data Lake-Hulpprogram ma's voor Visual Studio gebruiken](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    > [!NOTE]  
    > De Hulpprogram Ma's Azure Data Lake en Stream Analytics werden voorheen de HDInsight-Hulpprogram Ma's voor Visual Studio genoemd.
    >
    > Azure Data Lake-en Stream Analytics-Hulpprogram Ma's voor Visual Studio zijn opgenomen in de **Azure Development** -werk belasting voor visual studio 2019.

1. Start Visual Studio.

1. Selecteer in het **Startvenster** de optie **Een nieuw project maken**.

1. In het venster **een nieuw project maken** selecteert u het zoekvak en voert u in `Storm` . Kies vervolgens **Storm** -voor beeld in de lijst met resultaten en selecteer **volgende**.

1. Voer in het venster **uw nieuwe project configureren** een **project naam** in en ga naar of maak een **locatie** om het nieuwe project in op te slaan. Selecteer vervolgens **Maken**.

    ![Het nieuwe project venster configureren, Visual Studio](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-sample1.png)

1. Klik vanuit **Server Explorer** met de rechter muisknop op **Azure** en selecteer **verbinding maken met Microsoft Azure abonnement...** en voltooi het aanmeldings proces.

1. Klik vanuit **Solution Explorer** met de rechter muisknop op het project en kies **verzenden naar Storm op HDInsight**.

    > [!NOTE]  
    > Voer de aanmeldings referenties voor uw Azure-abonnement in als u hierom wordt gevraagd. Als u meer dan één abonnement hebt, meldt u zich aan bij de versie met uw Storm op HDInsight-cluster.

1. Kies in het dialoog venster **topologie indienen** in de vervolg keuzelijst **Storm-cluster** het Storm-cluster op HDInsight en selecteer vervolgens **verzenden**. U kunt controleren of de verzen ding is geslaagd door het deel venster **uitvoer** weer te geven.

## <a name="submit-a-topology-using-ssh-and-the-storm-command"></a>Een topologie verzenden met SSH en de Storm-opdracht

1. Gebruik de [ssh-opdracht](../hdinsight-hadoop-linux-use-ssh-unix.md) om verbinding te maken met uw cluster. Bewerk de onderstaande opdracht door CLUSTERNAME te vervangen door de naam van uw cluster. Voer vervolgens deze opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Gebruik vanuit uw SSH-sessie de volgende opdracht om de **WordCount** -voorbeeld topologie te starten:

    ```bash
    storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount
    ```

    Met deze opdracht wordt de WordCount-voorbeeldtopologie gestart op het cluster. Deze topologie genereert wille keurig zinnen en telt vervolgens het exemplaar van elk woord in de zinnen.

    > [!NOTE]  
    > Wanneer u een topologie indient naar het cluster, moet u eerst het jar-bestand met het cluster kopiëren voordat u de `storm` opdracht gebruikt. U kunt de opdracht gebruiken om het bestand naar het cluster te kopiëren `scp` . Voer bijvoorbeeld `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar` in.
    >
    > Het *WordCount* -voor beeld en andere Storm starter-voor beelden zijn al opgenomen in uw cluster op `/usr/hdp/current/storm-client/contrib/storm-starter/` .

## <a name="submit-a-topology-programmatically"></a>Een topologie via een programma verzenden

U kunt een topologie programmatisch implementeren met behulp van de Nimbus-service. [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology) biedt een voor beeld van een Java-toepassing die laat zien hoe u een topologie kunt implementeren en starten via de Nimbus-service.

## <a name="monitor-and-manage-a-topology-in-visual-studio"></a>Een topologie bewaken en beheren in Visual Studio

Wanneer u een topologie verzendt met behulp van Visual Studio, wordt het venster **Storm-topologieën weer geven** weer gegeven. Selecteer de topologie in de lijst om informatie over de actieve topologie weer te geven.

![Topologie bewaken, weergave venster Storm topologieën, Visual Studio](./media/apache-storm-deploy-monitor-topology-linux/visual-studio-monitor.png)

> [!NOTE]  
> U kunt Storm- **topologieën** ook bekijken vanuit **Server Explorer**. Vouw **Azure**  >  **HDInsight** uit, klik met de rechter muisknop op een storm op HDInsight-cluster en selecteer vervolgens **Storm-topologieën weer geven**.

Selecteer de vorm voor de spouts of bouten om informatie over deze onderdelen weer te geven. Er wordt een knop info met onderdeel informatie weer gegeven voor het geselecteerde item.

### <a name="deactivate-and-reactivate-a-topology"></a>Een topologie deactiveren en opnieuw activeren

Als een topologie wordt gedeactiveerd, wordt deze onderbroken totdat de topologie wordt afgebroken of opnieuw geactiveerd. Als u deze bewerkingen wilt uitvoeren, gebruikt u de knoppen **deactiveren** en **opnieuw activeren** in het gebied **acties** boven in het venster **Storm-topologieën weer geven** .

### <a name="rebalance-a-topology"></a>Een topologie opnieuw verdelen

Door een topologie te herverdelen kan het systeem de parallellisme van de topologie herzien. Als u bijvoorbeeld het formaat van het cluster hebt gewijzigd om meer notities toe te voegen, kunt u met herverdeling de nieuwe knoop punten weer geven met een topologie.

Als u een topologie wilt herverdelen, gebruikt u de knop opnieuw **verdelen** in het gebied **acties** van het venster **Storm-topologieën weer geven** .

> [!WARNING]  
> Als u een topologie opnieuw wilt verdelen, wordt de topologie gedeactiveerd, worden de werk nemers gelijkmatig verdeeld over het cluster en wordt de topologie vervolgens geretourneerd naar de status waarin deze zich bevond voordat de herverdeling plaatsvond. Als de topologie actief is, wordt deze weer actief. Als de topologie is gedeactiveerd, blijft deze gedeactiveerd.

### <a name="kill-a-running-topology"></a>Een actieve topologie beëindigen

Storm-topologieën blijven actief totdat ze zijn gestopt of het cluster is verwijderd. Als u een topologie wilt stoppen, gebruikt u de knop **Kill** in het gedeelte **Actions** .

## <a name="monitor-and-manage-a-topology-using-ssh-and-the-storm-command"></a>Een topologie bewaken en beheren met SSH en de Storm-opdracht

Met het `storm` hulp programma kunt u met het uitvoeren van topologieën werken vanaf de opdracht regel. Gebruiken `storm -h` voor een volledige lijst met opdrachten.

### <a name="list-topologies"></a>Topologieën weer geven

Gebruik de volgende opdracht om een lijst met alle actieve topologieën weer te geven:

```shell
storm list
```

Met deze opdracht wordt informatie geretourneerd die lijkt op de volgende tekst:

```shell
Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
-------------------------------------------------------------------
WordCount            ACTIVE     29         2            263
```

### <a name="deactivate-and-reactivate-a-topology"></a>Een topologie deactiveren en opnieuw activeren

Als een topologie wordt gedeactiveerd, wordt deze onderbroken totdat de topologie wordt afgebroken of opnieuw geactiveerd. Gebruik de volgende opdrachten om te deactiveren of opnieuw te activeren:

```shell
storm Deactivate TOPOLOGYNAME
```

```shell
storm Activate TOPOLOGYNAME
```

### <a name="kill-a-running-topology"></a>Een actieve topologie beëindigen

Storm-topologieën, zodra het is gestart, blijven actief totdat het wordt gestopt. Gebruik de volgende opdracht om een topologie te stoppen:

```shell
storm kill TOPOLOGYNAME
```

### <a name="rebalance-a-topology"></a>Een topologie opnieuw verdelen

Door een topologie te herverdelen kan het systeem de parallellisme van de topologie herzien. Als u bijvoorbeeld het formaat van het cluster hebt gewijzigd om meer notities toe te voegen, kunt u met herverdeling de nieuwe knoop punten weer geven met een topologie.

> [!WARNING]  
> Als u een topologie opnieuw wilt verdelen, wordt de topologie gedeactiveerd, worden de werk nemers gelijkmatig verdeeld over het cluster en wordt de topologie vervolgens geretourneerd naar de status waarin deze zich bevond voordat de herverdeling plaatsvond. Als de topologie actief is, wordt deze weer actief. Als de service is gedeactiveerd, blijft deze gedeactiveerd.

```shell
storm rebalance TOPOLOGYNAME
```

## <a name="monitor-and-manage-a-topology-using-the-storm-ui"></a>Een topologie controleren en beheren met behulp van de Storm-gebruikers interface

De Storm-UI biedt een webinterface voor het werken met actieve topologieën en is opgenomen in uw HDInsight-cluster. Als u de Storm-gebruikers interface wilt weer geven, gebruikt u een webbrowser om te openen `https://CLUSTERNAME.azurehdinsight.net/stormui` , waarbij *clustername* de naam van uw cluster is.

> [!NOTE]  
> Als u wordt gevraagd om een gebruikers naam en wacht woord op te geven, voert u de naam van de Cluster beheerder en het wacht woord in die u hebt gebruikt bij het maken van het cluster.

### <a name="storm-ui-main-page"></a>Hoofd pagina van Storm-gebruikers interface

De hoofd pagina van de Storm-gebruikers interface bevat de volgende informatie:

| Sectie | Beschrijving |
| --- | --- |
| Clusteroverzicht| Basis informatie over het Storm-cluster. |
| Nimbus-samen vatting | Een lijst met basis informatie over Nimbus. |
| Topologie samenvatting | Een lijst met actieve topologieën. Als u meer informatie over een specifieke topologie wilt weer geven, selecteert u de koppeling in de kolom **naam** . |
| Overzicht Super Visor | Informatie over de Storm-Super Visor. Als u de werk resources wilt zien die zijn gekoppeld aan een specifieke supervisor, selecteert u de koppeling in de kolom **host** of **id** . |
| Nimbus-configuratie | Nimbus-configuratie voor het cluster. |

De hoofd pagina van de Storm-gebruikers interface ziet er ongeveer als volgt uit:

![Hoofd pagina, Storm-gebruikers interface, Apache Storm topologieën, Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-main-page.png)

#### <a name="topology-summary"></a>Topologie samenvatting

Als u een koppeling selecteert in het gedeelte **topologie overzicht** , wordt de volgende informatie over de topologie weer gegeven:

| Sectie | Beschrijving |
| --- | --- |
| Topologie samenvatting | Basis informatie over de topologie. |
| Topologie acties| Beheer acties die u kunt uitvoeren voor de topologie. De beschik bare acties worden verderop in deze sectie beschreven. |
| Topology stats | Statistieken over de topologie. Als u het tijds bestek voor een item in deze sectie wilt instellen, selecteert u de bijbehorende koppeling in de kolom **venster** . |
| Spouts *(tijds periode)* | De spouts die wordt gebruikt door de topologie. Als u meer informatie over een specifieke Spout wilt weer geven, selecteert u de koppeling in de kolom **id** . |
| Bouten *(tijds bestek)* | De schichten die door de topologie worden gebruikt. Als u meer informatie over een specifieke Schicht wilt weer geven, selecteert u de koppeling in de kolom **id** . |
| Werknemers resources | Een lijst met werknemers resources. Als u meer informatie over een specifieke worker-resource wilt weer geven, selecteert u de koppeling in de kolom **host** . |
| Visualisatie van topologie | Een **visualisatie knop weer geven** waarmee een visualisatie van de topologie wordt weer gegeven. |
| Topology configuration | De configuratie van de geselecteerde topologie. |

De pagina overzicht van Storm-topologie ziet er ongeveer als volgt uit:

![Pagina topologie overzicht, Storm-gebruikers interface, Apache Storm, Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-topology-summary.png)

In het gedeelte **topologie acties** kunt u de volgende knoppen selecteren om een actie uit te voeren:

| Knop | Description |
| --- | --- |
| Activate | Hiermee wordt de verwerking van een gedeactiveerde topologie hervat. |
| Deactivate | Hiermee wordt een actieve topologie onderbroken. |
| Rebalance | Hiermee wordt de parallelle uitvoering van de topologie aangepast. U moet actieve topologieën opnieuw verdelen nadat u het aantal knoop punten in het cluster hebt gewijzigd. Met deze bewerking kan de topologie de parallellisme aanpassen om het extra of gereduceerde aantal knoop punten in het cluster te compenseren.<br/><br/>Zie <a href="https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Inzicht in de parallelle uitvoering van een Apache Storm-topologie</a> voor meer informatie.
| Kill | Hiermee wordt een Storm-topologie na de opgegeven time-out beëindigd. |
| Fouten opsporen | Hiermee wordt een foutopsporingssessie gestart voor de actieve topologie. |
| Fout opsporing stoppen | Hiermee wordt de foutopsporingssessie voor de actieve topologie beëindigd. |
| Logboek niveau wijzigen | Hiermee wijzigt u het logboek niveau voor fout opsporing. |

##### <a name="spout-and-bolt-summary"></a>Samen vatting van Spout en Schicht

Als u een Spout selecteert in de secties **Spouts** of **Bouts** , wordt de volgende informatie over het geselecteerde item weer gegeven:

| Sectie | Beschrijving |
| --- | --- |
| Onderdeel overzicht | Basis informatie over de Spout of bout. |
| Onderdeel acties | **Debug** -en **Stop** -knoppen voor fout opsporing. |
| Spout statistieken of flits statistieken | Statistieken over de Spout of bout. Als u het tijds bestek voor een item in deze sectie wilt instellen, selecteert u de bijbehorende koppeling in de kolom **venster** . |
| (Alleen voor de flits)<br/>Invoer statistieken *(tijds periode)* | Informatie over de invoer stromen die door de schicht worden gebruikt. |
| Uitvoer statistieken *(tijds periode)* | Informatie over de stromen die worden verzonden door de Spout of bout. |
| Profilering en fout opsporing | Besturings elementen voor het profileren en opsporen van fouten in de onderdelen op deze pagina. U kunt de waarde **status/time-out (minuten)** instellen en u kunt knoppen selecteren voor **JStack**, de **werk nemer** en de **heap** opnieuw opstarten. |
| Uitvoerendeers *(tijd frame)* | Informatie over de instanties van de Spout of bout. Als u een logboek van diagnostische gegevens wilt weer geven die voor deze instantie zijn geproduceerd, selecteert u de poort vermelding voor een specifieke **uitvoerder** . U kunt ook de worker-resources zien die zijn gekoppeld aan een specifieke uitvoerder door de bijbehorende koppeling in de kolom **host** te selecteren. |
| Fouten | Eventuele fout gegevens voor de Spout of bout. |

De overzichts pagina Storm-flits ziet er ongeveer als volgt uit:

![Pagina samen vatting van bout, Storm-gebruikers interface, Apache Storm, Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-bolt-summary.png)

## <a name="monitor-and-manage-the-topology-using-the-rest-api"></a>De topologie bewaken en beheren met de REST API

De Storm-gebruikers interface is gebaseerd op de REST API, zodat u vergelijk bare beheer-en bewakings taken kunt uitvoeren met behulp van de REST API. U kunt de REST API gebruiken om aangepaste hulp middelen te maken voor het beheren en bewaken van Storm-topologieën.

Zie [Apache Storm UI rest API](https://storm.apache.org/releases/current/STORM-UI-REST-API.html)voor meer informatie. De volgende informatie is specifiek voor het gebruik van de REST API met Apache Storm op HDInsight.

> [!IMPORTANT]  
> De Storm-REST API is niet openbaar beschikbaar via internet. Het moet worden geopend met behulp van een SSH-tunnel naar het hoofd knooppunt van het HDInsight-cluster. Zie [ssh-tunneling gebruiken om toegang te krijgen tot Azure HDInsight](../hdinsight-linux-ambari-ssh-tunnel.md)voor meer informatie over het maken en gebruiken van een SSH-tunnel.

### <a name="base-uri"></a>Basis-URI

De basis-URI voor de REST API op op Linux gebaseerde HDInsight-clusters is beschikbaar op URL `https://HEADNODEFQDN:8744/api/v1/` -adres, waar u *HEADNODEFQDN* vervangt door het hoofd knooppunt. De domein naam van het hoofd knooppunt wordt gegenereerd tijdens het maken van het cluster en is niet statisch.

U kunt de Fully Qualified Domain Name (FQDN) voor het hoofd knooppunt van het cluster op verschillende manieren vinden:

| FQDN-detectie methode | Description |
| --- | --- |
| SSH-sessie | Gebruik de opdracht `headnode -f` van een SSH-sessie naar het cluster. |
| Ambari-Web | Selecteer op de webpagina van het Ambari-cluster ( `https://CLUSTERNAME.azurehdinsight.net` ) **Services** boven aan de pagina en selecteer vervolgens **Storm**. Selecteer op het tabblad **samen vatting** de optie **Storm-gebruikers interface Server**. De FQDN van het knoop punt dat als host fungeert voor de Storm-gebruikers interface en REST API wordt boven aan de pagina weer gegeven. |
| Ambari REST API | Gebruik de opdracht `curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` om informatie op te halen over het knoop punt waarop de Storm-gebruikers interface en de rest API worden uitgevoerd. Vervang de twee exemplaren van *clustername* door de naam van het cluster. Wanneer u hierom wordt gevraagd, voert u het wacht woord voor het gebruikers account (beheer) in. In het antwoord bevat de vermelding ' host_name ' van de JSON-uitvoer de FQDN-naam van het knoop punt. |

### <a name="authentication"></a>Verificatie

Aanvragen voor de REST API moeten *basis verificatie* gebruiken, dus u moet de naam en het wacht woord van de beheerder voor het HDInsight-cluster gebruiken.

> [!NOTE]  
> Omdat basis verificatie wordt verzonden met behulp van Lees bare tekst, moet u *altijd* https gebruiken om de communicatie met het cluster te beveiligen.

### <a name="return-values"></a>Retourwaarden

Informatie die wordt geretourneerd door de REST API, kan alleen worden gebruikt vanuit het cluster. Zo is de Fully Qualified Domain Name (FQDN) die voor [Apache ZooKeeper](https://zookeeper.apache.org/) servers wordt geretourneerd, niet toegankelijk via internet.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [ontwikkelen van op Java gebaseerde topologieën met Apache Maven](apache-storm-develop-java-topology.md).

Zie voor een lijst met meer voorbeeld topologieën [Apache Storm topologieën in azure HDInsight](apache-storm-example-topology.md).

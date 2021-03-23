---
title: SSH-tunneling gebruiken om toegang te krijgen tot Azure HDInsight
description: Meer informatie over het gebruik van een SSH-tunnel om veilig webbronnen te doorzoeken die worden gehost op uw op Linux gebaseerde HDInsight-knoop punten.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 04/14/2020
ms.openlocfilehash: ef7e0450725b456a7fb2b1ab61c50d7edece52ce
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104867556"
---
# <a name="use-ssh-tunneling-to-access-apache-ambari-web-ui-jobhistory-namenode-apache-oozie-and-other-uis"></a>SSH-tunneling gebruiken om toegang te krijgen tot Apache Ambari Web UI, JobHistory, NameNode, Apache Oozie en andere UIs

HDInsight-clusters bieden toegang tot de Web-UI van Apache Ambari via internet. Sommige functies vereisen een SSH-tunnel. Bijvoorbeeld, de Web-UI van Apache Oozie kan niet via internet worden benaderd zonder SSH-tunnel.

## <a name="why-use-an-ssh-tunnel"></a>Waarom een SSH-tunnel gebruiken

Diverse menu's in Ambari werken alleen via een SSH-tunnel. Deze menu's zijn afhankelijk van websites en services die worden uitgevoerd op andere knooppunt typen, zoals worker-knoop punten.

Voor de volgende Web-UIs is een SSH-tunnel vereist:

* JobHistory
* NameNode
* Thread-stacks
* Oozie-webgebruikersinterface
* Gebruikers interface van HBase Master en Logboeken

Services die zijn geïnstalleerd met script acties die een webservice beschikbaar maken, hebben een SSH-tunnel nodig. Tint die is geïnstalleerd met een script actie vereist een SSH-tunnel om toegang te krijgen tot de Web-UI.

> [!IMPORTANT]  
> Als u rechtstreeks toegang hebt tot HDInsight via een virtueel netwerk, hoeft u geen SSH-tunnels te gebruiken. Voor een voor beeld van het rechtstreeks openen van HDInsight via een virtueel netwerk, zie het [Connect HDInsight to your on-premises Network](connect-on-premises-network.md) document.

## <a name="what-is-an-ssh-tunnel"></a>Wat is een SSH-tunnel?

Met het [tunnelen van Secure Shell (SSH)](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling) kunt u een poort op uw lokale machine verbinden met een hoofd knooppunt in HDInsight. Verkeer dat naar de lokale poort wordt verzonden, wordt doorgestuurd via een SSH-verbinding met het hoofd knooppunt. De aanvraag wordt opgelost alsof deze afkomstig is van het hoofd knooppunt. Het antwoord wordt vervolgens teruggestuurd door de tunnel naar uw werk station.

## <a name="prerequisites"></a>Vereisten

* Een SSH-client. Zie voor meer informatie [Verbinding maken met HDInsight (Apache Hadoop) via SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

* Een webbrowser die kan worden geconfigureerd voor het gebruik van een SOCKS5-proxy.

    > [!WARNING]  
    > De SOCKs-proxy ondersteuning die is ingebouwd in Windows Internet Settings ondersteunt geen SOCKS5 en werkt niet met de stappen in dit document. De volgende browsers zijn afhankelijk van de Windows-proxy-instellingen en werken momenteel niet met de stappen in dit document:
    >
    > * Microsoft Edge
    > * Microsoft Internet Explorer
    >
    > Google Chrome is ook afhankelijk van de Windows-proxy-instellingen. U kunt echter uitbrei dingen installeren die SOCKS5 ondersteunen. We raden [FoxyProxy Standard](https://chrome.google.com/webstore/detail/foxyproxy-standard/gcknhkkoolaabfmlnjonogaaifnjlfnp)aan.

## <a name="create-a-tunnel-using-the-ssh-command"></a><a name="usessh"></a>Een tunnel maken met behulp van de SSH-opdracht

Gebruik de volgende opdracht om een SSH-tunnel te maken met behulp van de `ssh` opdracht. Vervang door `sshuser` een ssh-gebruiker voor uw hdinsight-cluster en vervang door `CLUSTERNAME` de naam van uw hdinsight-cluster:

```cmd
ssh -C2qTnNf -D 9876 sshuser@CLUSTERNAME-ssh.azurehdinsight.net
```

Met deze opdracht maakt u een verbinding die verkeer naar lokale poort 9876 naar het cluster via SSH routeert. De opties zijn:

|Optie |Beschrijving |
|---|---|
|D 9876|De lokale poort die verkeer via de tunnel routeert.|
|C|Comprimeer alle gegevens, omdat webverkeer voornamelijk tekst is.|
|2|Forceer SSH om alleen Protocol versie 2 te proberen.|
|q|Stille modus.|
|T|Schakel pseudo-tty-toewijzing uit omdat u alleen een poort doorstuurt.|
|n|Lezen van STDIN voor komen, omdat u zojuist een poort doorstuurt.|
|N|Voer geen externe opdracht uit, omdat u zojuist een poort doorstuurt.|
|f|Op de achtergrond uitvoeren.|

Zodra de opdracht is voltooid, wordt verkeer dat is verzonden naar poort 9876 op de lokale computer doorgestuurd naar het hoofd knooppunt van het cluster.

## <a name="create-a-tunnel-using-putty"></a><a name="useputty"></a>Een tunnel maken met behulp van PuTTy

[Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty) is een grafische SSH-client voor Windows. Als u niet bekend bent met PuTTy, raadpleegt u de [documentatie van putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html). Gebruik de volgende stappen om een SSH-tunnel te maken met behulp van PuTTy:

### <a name="create-or-load-a-session"></a>Een sessie maken of laden

1. Open PuTTy en zorg ervoor dat **sessie** is geselecteerd in het menu links. Als u al een sessie hebt opgeslagen, selecteert u de sessie naam in de lijst **opgeslagen sessies** en selecteert u **laden**.

1. Als u nog geen opgeslagen sessie hebt, voert u de verbindings gegevens in:

    |Eigenschap |Waarde |
    |---|---|
    |Hostnaam (of IP-adres)|Het SSH-adres voor het HDInsight-cluster. Bijvoorbeeld **mijncluster-ssh.azurehdinsight.net**.|
    |Poort|22|
    |Verbindingstype|SSH|

1. Selecteer **Opslaan**

    :::image type="content" source="./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-create-putty-session.png" alt-text="Maken van een putty-sessie HDInsight":::

1. Vouw in het gedeelte **categorie** aan de linkerkant van het dialoog venster **verbinding** uit, vouw **SSH** uit en selecteer vervolgens **tunnels**.

1. Geef de volgende informatie op over de opties voor het **door sturen van SSH-poort beheren** :

    |Eigenschap |Waarde |
    |---|---|
    |Bronpoort|De poort op de client die u wilt door sturen. Bijvoorbeeld **9876**.|
    |Doel|Het SSH-adres voor het HDInsight-cluster. Bijvoorbeeld **mijncluster-ssh.azurehdinsight.net**.|
    |Dynamisch|Hiermee schakelt u dynamische SOCKs-proxy routering in.|

    :::image type="content" source="./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-putty-tunnel.png" alt-text="Opties voor de tunneling van PuTTy-configuratie":::

1. Selecteer **toevoegen** om de instellingen toe te voegen en selecteer vervolgens **openen** om een SSH-verbinding te openen.

1. Meld u aan bij de server wanneer u hierom wordt gevraagd.

## <a name="use-the-tunnel-from-your-browser"></a>De tunnel van uw browser gebruiken

> [!IMPORTANT]  
> In de stappen in deze sectie wordt de Mozilla FireFox-browser gebruikt, aangezien deze dezelfde proxy instellingen voor alle platforms biedt. Andere moderne browsers, zoals Google Chrome, hebben mogelijk een extensie zoals FoxyProxy nodig om met de tunnel te werken.

1. Configureer de browser voor het gebruik van **localhost** en de poort die u hebt gebruikt bij het maken van de tunnel als een **SOCKS V5** -proxy. De instellingen van de Firefox zien er als volgt uit. Als u een andere poort dan 9876 hebt gebruikt, wijzigt u de poort in het account dat u hebt gebruikt:

    :::image type="content" source="./media/hdinsight-linux-ambari-ssh-tunnel/firefox-proxy-settings.png" alt-text="proxy-instellingen van de Firefox-browser":::

   > [!NOTE]  
   > Als u **externe DNS** selecteert, worden er Domain Name System (DNS)-aanvragen via het HDInsight-cluster. Met deze instelling wordt DNS omgezet met behulp van het hoofd knooppunt van het cluster.

2. Controleer of de tunnel werkt door een site te bezoeken, zoals [https://www.whatismyip.com/](https://www.whatismyip.com/) . Het geretourneerde IP-adres moet één worden gebruikt door het Microsoft Azure Data Center.

## <a name="verify-with-ambari-web-ui"></a>Controleren met Ambari-webgebruikersinterface

Nadat het cluster is gemaakt, volgt u de volgende stappen om te controleren of u toegang hebt tot de Web-UIs op het Ambari-Web:

1. Ga in uw browser naar `http://headnodehost:8080`. Het `headnodehost` adres wordt via de tunnel naar het cluster verzonden en omgezet naar het hoofd knooppunt waarop Ambari wordt uitgevoerd. Wanneer u hierom wordt gevraagd, voert u de gebruikers naam van de beheerder (admin) en het wacht woord voor uw cluster in. Mogelijk wordt u een tweede keer gevraagd door de Ambari-webgebruikersinterface. Als dit het geval is, voert u de gegevens opnieuw in.

   > [!NOTE]  
   > Wanneer u het `http://headnodehost:8080` adres gebruikt om verbinding te maken met het cluster, maakt u verbinding via de tunnel. Communicatie wordt beveiligd met behulp van de SSH-tunnel in plaats van HTTPS. Als u via het Internet verbinding wilt maken met behulp van HTTPS, gebruikt u `https://clustername.azurehdinsight.net` , waarbij `clustername` de naam van het cluster is.

2. Selecteer in de Ambari-webgebruikersinterface de optie HDFS in de lijst aan de linkerkant van de pagina.

    :::image type="content" source="./media/hdinsight-linux-ambari-ssh-tunnel/hdfs-service-selected.png" alt-text="Apache Ambari hdfs-service geselecteerd":::

3. Wanneer de gegevens van de HDFS-service worden weer gegeven, selecteert u **snelle koppelingen**. Er wordt een lijst met de hoofd knooppunten van het cluster weer gegeven. Selecteer een van de hoofd knooppunten en selecteer vervolgens **NameNode-gebruikers interface**.

    :::image type="content" source="./media/hdinsight-linux-ambari-ssh-tunnel/namenode-drop-down-menu.png" alt-text="Afbeelding met het menu nemen uitgevouwen":::

    > [!NOTE]  
    > Wanneer u __snelle koppelingen__ selecteert, krijgt u mogelijk een wait-indicator. Dit probleem kan zich voordoen als u een trage Internet verbinding hebt. Wacht enkele minuten of twee om de gegevens van de server te ontvangen en probeer het opnieuw.
    >
    > Sommige vermeldingen in het menu **snelle koppelingen** kunnen worden afgekapt aan de rechter kant van het scherm. Als dit het geval is, vouwt u het menu uit met de muis en schuift u het scherm naar rechts om de rest van het menu weer te geven.

4. Er wordt een pagina weer gegeven die vergelijkbaar is met de volgende afbeelding:

    :::image type="content" source="./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-namenode-ui.png" alt-text="Afbeelding van de Hadoop NameNode-gebruikers interface":::

    > [!NOTE]  
    > Let op de URL voor deze pagina. Dit moet vergelijkbaar zijn met `http://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/cluster` . Deze URI gebruikt de interne Fully Qualified Domain Name (FQDN) van het knoop punt en is alleen toegankelijk als u een SSH-tunnel gebruikt.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u een SSH-tunnel maakt en gebruikt, raadpleegt u het volgende document voor andere manieren om Ambari te gebruiken:

* [HDInsight-clusters beheren met Apache Ambari](hdinsight-hadoop-manage-ambari.md)

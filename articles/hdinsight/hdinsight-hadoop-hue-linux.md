---
title: Tint met Hadoop op HDInsight Linux-gebaseerde clusters-Azure
description: Meer informatie over het installeren van tint op HDInsight-clusters en het gebruik van tunneling om de aanvragen naar tinten te sturen. Gebruik tint om door de opslag te bladeren en Hive of Pig uit te voeren.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 03/31/2020
ms.openlocfilehash: 97a8dd2476642e693b589b4046f612c5569b9c0b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104865125"
---
# <a name="install-and-use-hue-on-hdinsight-hadoop-clusters"></a>Tint op HDInsight Hadoop-clusters installeren en gebruiken

Meer informatie over het installeren van tint op HDInsight-clusters en het gebruik van tunneling om de aanvragen naar tinten te sturen.

## <a name="what-is-hue"></a>Wat is tint?

Tint is een set webtoepassingen die worden gebruikt voor interactie met een Apache Hadoop cluster. U kunt tint gebruiken om te bladeren door de opslag die is gekoppeld aan een Hadoop-cluster (WASB, in het geval van HDInsight-clusters), Hive-taken en Pig-scripts, enzovoort, uit te voeren. De volgende onderdelen zijn beschikbaar voor tint installaties op een HDInsight Hadoop-cluster.

* Beeswax-Hive-editor
* Apache-Pig
* Meta Store-beheer
* Apache Oozie
* FileBrowser (die praat met de WASB-standaard container)
* Taak browser

> [!WARNING]  
> Onderdelen die worden meegeleverd met het HDInsight-cluster, worden volledig ondersteund en Microsoft Ondersteuning helpt bij het isoleren en oplossen van problemen met betrekking tot deze onderdelen.
>
> Aangepaste onderdelen ontvangen commercieel redelijke ondersteuning om u te helpen het probleem verder op te lossen. Dit kan leiden tot het oplossen van het probleem of het vragen om beschik bare kanalen te benaderen voor de open source-technologieën waar diep gaande expertise voor die technologie wordt gevonden. Er zijn bijvoorbeeld veel community-sites die kunnen worden gebruikt, zoals: [micro soft Q&een vraag pagina voor HDInsight](/answers/topics/azure-hdinsight.html) [https://stackoverflow.com](https://stackoverflow.com) . Ook Apache-projecten hebben project sites op [https://apache.org](https://apache.org) , bijvoorbeeld: [Hadoop](https://hadoop.apache.org/).

## <a name="install-hue-using-script-actions"></a>Tint installeren met script acties

Gebruik de informatie in de onderstaande tabel voor uw script actie. Zie [HDInsight-clusters aanpassen met script acties](hdinsight-hadoop-customize-cluster-linux.md) voor specifieke instructies voor het gebruik van script acties.

> [!NOTE]  
> Voor het installeren van tint op HDInsight-clusters is de aanbevolen hoofd knooppunt-grootte ten minste A4 (8 kernen, 14 GB geheugen).

|Eigenschap |Waarde |
|---|---|
|Script type:|- Aangepast|
|Name|Kleur Toon installeren|
|Bash-script-URI|`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`|
|Knooppunt type (n):|Head|

## <a name="use-hue-with-hdinsight-clusters"></a>Tint gebruiken met HDInsight-clusters

U kunt slechts één gebruikers account hebben met kleur Toon op gewone clusters. Schakel [Enterprise Security Package](./domain-joined/hdinsight-security-overview.md) in op het cluster voor toegang voor meerdere gebruikers. SSH-Tunneling is de enige manier om toegang te krijgen tot de tint op het cluster zodra deze actief is. Door middel van tunneling via SSH kan het verkeer rechtstreeks naar de hoofd knooppunt van het cluster gaan waarop tint wordt uitgevoerd. Wanneer het inrichten van het cluster is voltooid, gebruikt u de volgende stappen om tint op een HDInsight-cluster te gebruiken.

> [!NOTE]  
> U wordt aangeraden om de volgende instructies te volgen met Firefox-webbrowser.

1. Gebruik de informatie in [ssh-tunneling gebruiken om toegang te krijgen tot Apache Ambari Web UI, Resource Manager, JobHistory, NameNode, Oozie en andere WEBgebruikersinterfaces](hdinsight-linux-ambari-ssh-tunnel.md) om een SSH-tunnel van uw client systeem naar het HDInsight-cluster te maken. Configureer vervolgens uw webbrowser om de SSH-tunnel te gebruiken als een proxy server.

1. Gebruik de [ssh-opdracht](./hdinsight-hadoop-linux-use-ssh-unix.md) om verbinding te maken met uw cluster. Bewerk de onderstaande opdracht door CLUSTERNAME te vervangen door de naam van uw cluster. Voer vervolgens deze opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Als de verbinding tot stand is gebracht, gebruikt u de volgende opdracht om de Fully Qualified Domain Name van de primaire hoofd knooppunt te verkrijgen:

    ```bash
    hostname -f
    ```

    Dit retourneert een naam die er ongeveer als volgt uitziet:

    ```output
    myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net
    ```

    Dit is de hostnaam van de primaire hoofd knooppunt waar de website tint zich bevindt.

1. Gebruik de browser om de tint portal te openen op `http://HOSTNAME:8888` . Vervang HOSTNAME door de naam die u in de vorige stap hebt verkregen.

   > [!NOTE]  
   > Wanneer u zich voor de eerste keer aanmeldt, wordt u gevraagd een account te maken om u aan te melden bij de tint Portal. De referenties die u hier opgeeft, zijn beperkt tot de portal en zijn niet gerelateerd aan de beheerders-of SSH-gebruikers referenties die u hebt opgegeven tijdens het inrichten van het cluster.

    :::image type="content" source="./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-login.png" alt-text="Aanmeldings venster van HDInsight-tint Portal":::

### <a name="run-a-hive-query"></a>Een Hive-query uitvoeren

1. Selecteer in de portal voor kleur Toon **query-editors** en selecteer vervolgens **Hive** om de Hive-editor te openen.

    :::image type="content" source="./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-use-hive.png" alt-text="Portal voor HDInsight-tinten gebruiken Hive-editor":::

2. Op het tabblad **assistent** , onder **Data Base**, ziet u **hivesampletable**. Dit is een voorbeeld tabel die wordt geleverd met alle Hadoop-clusters in HDInsight. Voer een voorbeeld query in het rechterdeel venster in en Bekijk de uitvoer op het tabblad **resultaten** in het onderstaande deel venster, zoals wordt weer gegeven in de scherm opname.

    :::image type="content" source="./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-hive-query.png" alt-text="Portal Hive-query voor HDInsight-tint":::

    U kunt ook het tabblad **grafiek** gebruiken om een visuele weer gave van het resultaat te bekijken.

### <a name="browse-the-cluster-storage"></a>Bladeren door de cluster opslag

1. Selecteer in de portal voor kleur Toon **bestands browser** in de rechter bovenhoek van de menu balk.
2. De bestands browser wordt standaard geopend in de **/User/myuser** -map. Selecteer de slash direct vóór de gebruikers lijst in het pad om naar de hoofdmap te gaan van de Azure storage-container die aan het cluster is gekoppeld.

    :::image type="content" source="./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-file-browser.png" alt-text="Portal bestands browser voor HDInsight-tinten":::

3. Klik met de rechter muisknop op een bestand of map om de beschik bare bewerkingen weer te geven. Gebruik de knop **uploaden** in de rechter bovenhoek om bestanden te uploaden naar de huidige map. Gebruik de knop **Nieuw** om nieuwe bestanden of mappen te maken.

> [!NOTE]  
> De bestands browser tinten kan alleen de inhoud weer geven van de standaard container die is gekoppeld aan het HDInsight-cluster. Alle extra opslag accounts/containers die u aan het cluster hebt gekoppeld, zijn niet toegankelijk via de bestands browser. De extra containers die zijn gekoppeld aan het cluster, zijn echter altijd toegankelijk voor de Hive-taken. Als u bijvoorbeeld de opdracht `dfs -ls wasbs://newcontainer@mystore.blob.core.windows.net` in de Hive-editor invoert, ziet u ook de inhoud van extra containers. In deze opdracht is **newcontainer** niet de standaard container die aan een cluster is gekoppeld.

## <a name="important-considerations"></a>Belangrijke overwegingen

1. Het script dat wordt gebruikt om de kleur Toon te installeren, installeert dit alleen op de primaire hoofd knooppunt van het cluster.

1. Tijdens de installatie worden meerdere Hadoop-Services (HDFS, GARENs, MR2, Oozie) opnieuw gestart voor het bijwerken van de configuratie. Nadat het script is voltooid, kan het enige tijd duren voordat andere Hadoop-services worden opgestart. Dit kan in eerste instantie van invloed zijn op de prestaties van de kleur. Zodra alle services zijn opgestart, is de kleur Toon volledig functioneel.

1. Met kleur Toon kunnen Apache TEZ-taken niet worden uitgevoerd. Dit is de huidige standaard waarde voor Hive. Als u MapReduce wilt gebruiken als de engine voor het uitvoeren van de Hive, werkt u het script bij voor het gebruik van de volgende opdracht in uw script:

   `set hive.execution.engine=mr;`

1. Met Linux-clusters kunt u een scenario hebben waarin uw services worden uitgevoerd op de primaire hoofd knooppunt terwijl de Resource Manager kan worden uitgevoerd op de secundaire server. Dit scenario kan leiden tot fouten (zie hieronder) als u de details van actieve taken op het cluster wilt weer geven met behulp van tint. U kunt de taak Details echter bekijken wanneer de taak is voltooid.

   :::image type="content" source="./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-error.png" alt-text="Voorbeeld bericht van de kleur tint Portal":::

   Dit is te wijten aan een bekend probleem. Als tijdelijke oplossing wijzigt u Ambari zodat de actieve Resource Manager ook wordt uitgevoerd op de primaire hoofd knooppunt.

1. Kleur Toon begrijpt WebHDFS terwijl HDInsight-clusters Azure Storage gebruiken `wasbs://` . Het aangepaste script dat wordt gebruikt met script actie installeert dus WebWasb, een WebHDFS-compatibele service voor het praten met WASB. Dit betekent dat, zelfs als de kleur Toon, HDFS op locatie (bijvoorbeeld wanneer u de muis over de **bestands browser** beweegt) wordt geïnterpreteerd als WASB.

## <a name="next-steps"></a>Volgende stappen

[Installeer R op HDInsight-clusters](./r-server/r-server-overview.md). Gebruik cluster aanpassing om R-op HDInsight Hadoop-clusters te installeren. R is een open source-taal en-omgeving voor statistische computing. Het biedt honderden ingebouwde statistische functies en een eigen programmeer taal waarmee aspecten van functionele en objectgeoriënteerd Program meren worden gecombineerd. Het biedt ook uitgebreide grafische mogelijkheden.
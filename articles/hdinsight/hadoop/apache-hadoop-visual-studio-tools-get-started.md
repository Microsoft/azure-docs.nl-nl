---
title: Apache Hadoop & Visual Studio Data Lake-Hulpprogram Ma's-Azure HDInsight
description: Meer informatie over het installeren en gebruiken van Data Lake-Hulpprogram Ma's voor Visual Studio. Gebruik hulp programma om verbinding te maken met Apache Hadoop clusters in azure HDInsight en vervolgens Hive-query's uit te voeren.
keywords: hadoop-hulpprogramma's, hive-query, visual studio, visual studio hadoop
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: how-to
ms.date: 04/14/2020
ms.openlocfilehash: c6969c3108fb1465a705d8e61b3a7bdf2083736a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104865652"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-apache-hive-queries"></a>Data Lake-Hulpprogram Ma's voor Visual Studio gebruiken om verbinding te maken met Azure HDInsight en Apache Hive query's uit te voeren

Meer informatie over het gebruik van Microsoft Azure Data Lake en Stream Analytics tools for Visual Studio (Data Lake-Hulpprogram Ma's). Gebruik het hulp programma om verbinding te maken met [Apache Hadoop clusters in azure HDInsight](apache-hadoop-introduction.md) en Hive-query's te verzenden.  

Zie [aan de slag met hdinsight](apache-hadoop-linux-tutorial-get-started.md)voor meer informatie over het gebruik van hdinsight.  

Zie [C#-topologieën ontwikkelen voor Apache Storm met behulp van de data Lake-hulpprogram ma's](../storm/apache-storm-develop-csharp-visual-studio-topology.md)voor meer informatie over het maken van verbinding met Apache Storm.

U kunt Data Lake Tools voor Visual Studio gebruiken voor toegang tot Azure Data Lake Analytics en HDInsight. Zie voor informatie over Data Lake Tools [Zelfstudie: U-SQL-scripts ontwikkelen met Data Lake Tools voor Visual Studio](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt volt ooien en Data Lake-Hulpprogram Ma's voor Visual Studio wilt gebruiken, hebt u de volgende items nodig:

* Een Azure HDInsight-cluster. Zie aan de [slag met Apache Hadoop in azure HDInsight](apache-hadoop-linux-tutorial-get-started.md)om een HDInsight-cluster te maken. Als u interactieve Apache Hive query's wilt uitvoeren, hebt u een [HDInsight Interactive query](../interactive-query/apache-interactive-query-get-started.md) -cluster nodig.  

* [Visual Studio](https://visualstudio.microsoft.com/downloads/). De [Visual Studio Community-editie](https://visualstudio.microsoft.com/vs/community/) is gratis. De instructies die hier worden weer gegeven, zijn voor [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).

## <a name="install-data-lake-tools-for-visual-studio"></a>Data Lake Tools voor Visual Studio installeren  

Volg de onderstaande instructies om Data Lake-Hulpprogram Ma's te installeren voor uw versie van Visual Studio:

* Voor Visual Studio 2017 of Visual Studio 2019:

    Zorg er tijdens de installatie van Visual Studio voor dat u de werk belasting voor **Azure Development** of de werk belasting voor **gegevens opslag en-verwerking** opneemt.  

    Voor bestaande Visual Studio-installaties gaat u naar de IDE-menu balk en selecteert u **hulpprogram** ma's  >  **en functies ophalen** om Visual Studio Installer te openen. Op het tabblad **workloads** selecteert u ten minste de werk belasting van **Azure Development** (onder **Web & Cloud**). Of selecteer de werk belasting voor **gegevens opslag en-verwerking** (onder **andere sluit**).

  :::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/vs-installation.png" alt-text="Werkbelasting selectie, Visual Studio Installer" border="true":::

* Voor Visual Studio 2015:

    [Down load data Lake-Hulpprogram ma's](https://www.microsoft.com/download/details.aspx?id=49504). Kies de versie van Data Lake Tools die overeenkomt met uw versie van Visual Studio.

## <a name="update-data-lake-tools-for-visual-studio"></a>Data Lake-Hulpprogram Ma's voor Visual Studio bijwerken  

Zorg er vervolgens voor dat u Data Lake-Hulpprogram Ma's bijwerkt naar de meest recente versie.

1. Open Visual Studio.

2. Selecteer **door gaan zonder code** in het **Start** venster.

3. Kies in de menu balk van Visual Studio IDE de optie **extensies** extensies  >  **beheren**.

4. Vouw in het dialoog venster **extensies beheren** het knoop punt **updates** uit.

5. Als de lijst met beschik bare updates **Azure data Lake en stream analytic-Hulpprogram ma's** bevat, selecteert u deze. Selecteer vervolgens de knop **bijwerken** . Nadat het dialoog venster **downloaden en installeren** wordt weer gegeven en verdwijnt, voegt Visual Studio de extensie **Azure data Lake en stream analytic-hulpprogram ma's** toe aan de update planning.

6. Sluit alle Visual Studio-Vensters. Het dialoog venster **VSIX installatie programma** wordt weer gegeven.

7. Selecteer de **licentie** om de licentie voorwaarden te lezen en selecteer vervolgens **sluiten** om terug te keren naar het dialoog venster **VSIX Installer** .

8. Selecteer **wijzigen**. De installatie van de extensie-update wordt gestart. Na enige tijd wordt het dialoog venster weer gegeven om aan te geven dat de wijzigingen zijn aangebracht. Selecteer **sluiten** en Start Visual Studio opnieuw om de installatie te volt ooien.

> [!NOTE]  
> U kunt alleen Data Lake Tools versie 2.3.0.0 of hoger gebruiken om verbinding te maken met Interactive Query-clusters en interactieve Hive-query's uit te voeren.

## <a name="connect-to-azure-subscriptions"></a>Verbinding maken met Azure-abonnementen

U kunt Data Lake-Hulpprogram Ma's voor Visual Studio gebruiken om verbinding te maken met uw HDInsight-clusters, enkele eenvoudige beheer bewerkingen uitvoeren en Hive-query's uit te voeren.

> [!NOTE]  
> Zie [Hive-query's schrijven en verzenden met Visual Studio](/archive/blogs/xiaoyong/how-to-write-and-submit-hive-queries-using-visual-studio)voor meer informatie over het maken van verbinding met een algemeen Hadoop-cluster.

### <a name="connect-to-an-azure-subscription"></a>Verbinding maken met een Azure-abonnement

Verbinding maken met uw Azure-abonnement:

1. Open Visual Studio.

2. Selecteer **door gaan zonder code** in het **Start** venster.

3. Kies Server Explorer **weer geven** in de IDE-menu balk  >  .

4. In **Server Explorer** klikt u met de rechter muisknop op **Azure**, selecteert **u verbinding maken met Microsoft Azure abonnement** en voltooit u het verificatie proces. Vouw in **Server Explorer** **Azure**  >  **HDInsight** uit om een lijst met bestaande HDInsight-clusters weer te geven.

5. Als u geen clusters hebt, kunt u er een maken met behulp van de Azure Portal, Azure PowerShell of de HDInsight SDK. Zie [clusters instellen in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)voor meer informatie.

   :::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-server-explorer.png" alt-text="HDInsight-cluster lijst, Server Explorer, Visual Studio" border="true":::

6. Een HDInsight-cluster uitbreiden. Het cluster bevat knoop punten voor **Hive-data bases**. Ook een standaard-opslag account, eventueel extra gekoppelde opslag accounts en **Hadoop-service logboek**. U kunt de entiteiten verder uitbreiden.

Nadat u verbinding hebt gemaakt met het Azure-abonnement, kunt u de volgende taken uitvoeren.

### <a name="connect-to-azure-from-visual-studio"></a>Verbinding maken met Azure vanuit Visual Studio

Verbinding maken met Azure Portal vanuit Visual Studio:

1. Vouw in **Server Explorer** **Azure**  >  **HDInsight** uit en selecteer uw cluster.

2. Klik met de rechter muisknop op een HDInsight-cluster en selecteer **cluster beheren in azure Portal**.

### <a name="offer-questions-and-feedback-from-visual-studio"></a>Vragen en feedback van Visual Studio bieden

Vragen stellen en of feedback geven vanuit Visual Studio:

1. Kies in Server Explorer **Azure**  >  **HDInsight**.

2. Klik met de rechter muisknop op **HDInsight** en selecteer **MSDN-forum** om vragen te stellen of **Geef feedback** om feedback te geven.

## <a name="link-to-or-edit-a-cluster"></a>Een koppeling maken naar of bewerken in een cluster

> [!NOTE]
> Momenteel is het enige type HDInsight-cluster waarmee u kunt koppelen, is een Hive-type.

Een HDInsight-cluster koppelen:

1. Klik met de rechter muisknop op **HDInsight** en selecteer vervolgens **koppelen van een hdinsight-cluster** om het dialoog venster **een hdinsight-cluster te koppelen** weer te geven.

2. Voer een **verbindings-URL** in het formulier in `https://CLUSTERNAME.azurehdinsight.net` . De **cluster naam** wordt automatisch ingevuld met het cluster naam gedeelte van uw URL wanneer u naar een ander veld gaat. Voer vervolgens een **gebruikers naam** en **wacht woord** in en selecteer **volgende**.

    :::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-dialog.png" alt-text="Een cluster koppelen, HDInsight, Visual Studio" border="true":::

3. Selecteer **Finish**. Als de cluster koppeling is geslaagd, wordt het cluster weer gegeven onder het **HDInsight** -knoop punt.

Als u een gekoppeld cluster wilt bijwerken, klikt u met de rechter muisknop op het cluster en selecteert u **bewerken**. U kunt vervolgens de cluster gegevens bijwerken.

:::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-update.png" alt-text="Een gekoppeld cluster, HDInsight, Visual Studio bewerken" border="true":::

## <a name="explore-linked-resources"></a>Gekoppelde resources verkennen

In Server Explorer worden het standaardaccount voor opslag en alle gekoppelde opslagaccounts weergegeven. Als u het standaardopslagaccount uitvouwt, kunt u de containers op het opslagaccount zien. Het standaardopslagaccount en de standaardcontainer worden gemarkeerd.

:::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-linked-resources.png" alt-text="Data Lake-Hulpprogram Ma's voor Visual Studio gekoppelde resources in Server Explorer" border="true":::

Klik met de rechter muisknop op een container en selecteer **container weer geven** om de inhoud van de container weer te geven. Nadat u een container hebt geopend, kunt u de knoppen op de werk balk gebruiken om de inhouds lijst te **vernieuwen** , **BLOB te uploaden**, **geselecteerde blobs te verwijderen**, blob te **openen** en te downloaden (**Opslaan als**) geselecteerde blobs.

:::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-blob-operations.png" alt-text="Container lijst en BLOB-bewerkingen, HDInsight-cluster, Visual Studio" border="true":::

## <a name="run-interactive-apache-hive-queries"></a>Interactieve Apache Hive query's uitvoeren

[Apache Hive](https://hive.apache.org) is een datawarehouse-infrastructuur die is gebouwd op Hadoop. Hive wordt gebruikt voor gegevenssamenvatting, query's en analyse. U kunt Data Lake Tools voor Visual Studio gebruiken om Hive-query's uit te voeren vanuit Visual Studio. Zie [Wat is Apache Hive en HiveQL in azure HDInsight?](hdinsight-use-hive.md)voor meer informatie over Hive.

[Interactieve query's in azure HDInsight](../interactive-query/apache-interactive-query-get-started.md) gebruiken [Hive op LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) in Apache hive 2,1. Interactieve query brengt interactiviteit op complexe, Data Warehouse-stijl query's op grote opgeslagen gegevens sets. Het uitvoeren van Hive-query's op interactieve Query's is veel sneller dan de traditionele Hive-batch taken. 

> [!NOTE]  
> U kunt alleen interactieve Hive-query's uitvoeren wanneer u verbinding maakt met een [HDInsight Interactive Query](../interactive-query/apache-interactive-query-get-started.md)-cluster.

U kunt ook Data Lake-Hulpprogram Ma's voor Visual Studio gebruiken om te zien wat er in een Hive-taak zit. Data Lake Tools voor Visual Studio verzamelt de Yarn-logboeken van bepaalde Hive-taken en maakt ze zichtbaar.

Kies in **Server Explorer** **Azure**  >  **HDInsight** en selecteer uw cluster.  Dit knoop punt is het begin punt in **Server Explorer** voor de volgende secties.

### <a name="view-hivesampletable"></a>hivesampletable bekijken

Alle HDInsight-clusters hebben een standaard voor beeld-Hive-tabel met de naam `hivesampletable` .  

Kies in het cluster de standaard hivesampletable voor de **Hive-data bases**  >    >  .

* Het schema weer geven `hivesampletable` :

    Vouw **hivesampletable** uit. De namen en gegevens typen van de `hivesampletable` kolommen worden weer gegeven.

* De gegevens weer geven `hivesampletable` :

    Klik met de rechter muisknop op **hivesampletable** en selecteer **Top 100-rijen weer geven**. De lijst met 100 resultaten wordt weer gegeven in de **Hive-tabel: hivesampletable** -venster. Deze actie is gelijk aan het uitvoeren van de volgende Hive-query met behulp van het Hive ODBC-stuur programma:

    `SELECT * FROM hivesampletable LIMIT 100`

    U kunt het aantal rijen aanpassen door het wijzigen **van de waarde van** een rij. u kunt kiezen uit de vervolg keuzelijst 50, 100, 200 of 1000 rijen.

### <a name="create-hive-tables"></a>Hive-tabellen maken

U een Hive-tabel maken door de GUI te gebruiken of door Hive-query's te gebruiken. Zie [Hive-Query's maken en uitvoeren](#create-and-run-hive-queries)voor meer informatie over het gebruik van Hive-query's.

1. Kies in uw cluster standaard **Hive-data bases**  >  .

2. Klik met de rechter muisknop op **standaard** en selecteer **tabel maken**.

3. Configureer de tabel.

4. Selecteer de knop **tabel maken** om de taak te verzenden, waarmee de nieuwe Hive-tabel wordt gemaakt.

    :::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-create-hive-table.png" alt-text="Tabel venster maken, Hive, HDInsight-cluster, Visual Studio" border="true":::

### <a name="create-and-run-hive-queries"></a>Hive-query's maken en uitvoeren

U hebt twee opties voor het maken en uitvoeren van Hive-query's:

* Ad-hocquery's maken
* Een Hive-toepassing maken

#### <a name="create-an-ad-hoc-query"></a>Een ad-hoc-query maken

Een ad-hoc query maken en uitvoeren:

1. Klik met de rechter muisknop op het cluster waar u de query wilt uitvoeren en selecteer **een Hive-query schrijven**.  

2. Voer een Hive-query in.

    De Hive-editor ondersteunt IntelliSense. Data Lake Tools voor Visual Studio biedt ondersteuning voor het laden van externe metagegevens wanneer u het Hive-script bewerkt. Als u bijvoorbeeld typt `SELECT * FROM` , geeft IntelliSense alle voorgestelde tabel namen weer. Wanneer een tabelnaam wordt opgegeven, geeft IntelliSense de kolomnamen weer. De hulpprogramma's ondersteunen de meeste DML-instructies, subquery's en ingebouwde UDF's van Hive.

    :::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-table-names.png" alt-text="IntelliSense-voor beeld 1, Hive ad-hoc query, HDInsight-cluster, Visual Studio" border="true":::

    :::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-column-names.png" alt-text="IntelliSense-voor beeld 2, Hive ad-hoc query, HDInsight-cluster, Visual Studio" border="true":::

    > [!NOTE]  
    > IntelliSense suggereert alleen de metagegevens van het cluster dat in de HDInsight-werkbalk is geselecteerd.

    Hier volgt een voor beeld van een query die u kunt gebruiken:

    ```sql
    SELECT devicemodel, COUNT(devicemodel) AS deviceCount
    FROM hivesampletable
    GROUP BY devicemodel
    ORDER BY devicemodel
    ```

3. De uitvoerings modus kiezen:

    * **Interactief**  

        Kies in de eerste vervolg keuzelijst de optie **interactief** en selecteer vervolgens **uitvoeren**.

        :::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-execute.png" alt-text="Interactieve modus, Hive ad-hoc query, HDInsight-cluster, Visual Studio" border="true":::  

    * **Batch**  

        Kies in de eerste vervolg keuzelijst **batch**, en selecteer vervolgens **verzenden**. Of selecteer het pictogram vervolg keuzelijst naast **verzenden** en kies **Geavanceerd**.

        :::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-batch.png" alt-text="Batch-modus, Hive ad-hoc query, HDInsight-cluster, Visual Studio" border="true":::

        Als u de optie Geavanceerde verzen ding selecteert, wordt het dialoog venster **script indienen** weer gegeven. Configureer de **taak naam**, **argumenten**, **aanvullende configuraties** en de **status Directory** voor het script.

        :::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-submit-jobs-advanced.png" alt-text="Het dialoog venster script indienen, Hive ad-hoc query, HDInsight-cluster, Visual Studio" border="true":::

      > [!NOTE]  
      > U kunt geen batches naar interactieve query clusters verzenden.  U moet de interactieve modus gebruiken.

#### <a name="create-a-hive-application"></a>Een Hive-toepassing maken

Een Hive-oplossing maken en uitvoeren:

1. Kies in de menu balk **bestand**  >  **Nieuw**  >  **project**.

2. Selecteer in het venster **een nieuw project maken** het **onderdeel** zoeken en type. Kies vervolgens **Hive-toepassing** en selecteer **volgende**.

3. Voer in het venster **uw nieuwe project configureren** een **project naam** in, selecteer of maak de project **locatie** en selecteer vervolgens **maken**.

    :::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-new-hive-project.png" alt-text="Nieuwe Hive-toepassing, het nieuwe project venster configureren, HDInsight Visual Studio" border="true":::

4. Dubbelklik in **Solution Explorer** op **Script.hql** om het script te openen.

### <a name="view-job-summary-and-output"></a>Taak samenvatting en-uitvoer weer geven

De samen vatting van de taak varieert enigszins tussen **batch** en **interactieve** modus.

:::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-summary.png" alt-text="Overzicht van Hive-taken Windows, batch en interactieve modus, Visual Studio" border="true":::

Gebruik het **vernieuwings** pictogram om de status bij te werken totdat de taak status is gewijzigd in **voltooid**.  

* Voor de taak Details van de **batch** modus selecteert u de koppelingen onderaan om de **taak query**, **taak uitvoer** of **taak logboek** te bekijken of om de **garens te bekijken**.

* Zie **de uitvoer-en** **HiveServer2 uitvoer** deel Vensters voor de taak Details van de **interactieve** modus.

    :::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-details.png" alt-text="Onderdeel interactieve taak uitvoer, HDInsight-cluster, Visual Studio" border="true":::

### <a name="view-job-graph"></a>Taak grafiek weer geven

Taak grafieken worden momenteel alleen weer gegeven voor Hive-taken die gebruikmaken van TEZ als uitvoerings engine.  Zie [Wat is Apache Hive en HiveQL in azure HDInsight?](hdinsight-use-hive.md)voor meer informatie over het inschakelen van TEZ.  Zie ook [Apache TEZ gebruiken in plaats van toewijzings reductie](../hdinsight-hadoop-optimize-hive-query.md#use-apache-tez-instead-of-map-reduce).  

Dubbel klik op de hoek punten van de taak grafiek om alle Opera tors in het hoek punt weer te geven. U kunt ook een bepaalde operator aanwijzen voor meer informatie over de operator.

Zelfs als TEZ is opgegeven als de uitvoerings engine, wordt de taak grafiek mogelijk niet weer gegeven als er geen TEZ-toepassing wordt gestart.  Deze situatie kan zich voordoen omdat de taak geen DML-instructies bevat. Of omdat de DML-instructies kunnen retour neren zonder een TEZ-toepassing te starten. `SELECT * FROM table1`De TEZ-toepassing kan bijvoorbeeld niet worden gestart.

:::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-fast-path-hive-execution.png" alt-text="Apache Hive-taak grafiek, Visual Studio" border="true":::

### <a name="view-task-execution-detail"></a>Details van taak uitvoering weer geven

Vanuit de taak grafiek kunt u details van **taak uitvoering** selecteren om gestructureerde en gevisualiseerde informatie voor Hive-taken op te halen. U kunt ook meer taak Details ophalen. Als er prestatieproblemen optreden, kunt u de weergave gebruiken om meer informatie over het probleem op te vragen. U kunt bijvoorbeeld gegevens ophalen over de werking van elke taak en gedetailleerde informatie over elke taak (gegevens lezen/schrijven, planning/begin-en eind tijd en meer). Gebruik de informatie om taakconfiguraties of systeemarchitectuur bij te stellen op basis van de gevisualiseerde informatie.

:::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-task-execution-view.png" alt-text="Venster taak uitvoering weer geven, Data Lake Visual Studio Tools" border="true":::

### <a name="view-hive-jobs"></a>Hive-taken weergeven

U kunt taakquery's, taakuitvoer, logboeken van taken en Yarn-logboeken voor Hive-taken weergeven.

In de meest recente versie van de hulpprogram ma's kunt u zien wat er in uw Hive-taken zit door halen garen-logboeken te verzamelen en te registreren. Een Yarn-logboek kan u helpen bij het onderzoeken van prestatieproblemen. Zie [Access Apache HADOOP garens van toepassings logboeken](../hdinsight-hadoop-access-yarn-app-logs-linux.md)voor meer informatie over hoe HDInsight garens verzamelt.

Hive-taken weergeven:

1. Klik met de rechter muisknop op een HDInsight-cluster en selecteer **taken weer geven**.

    :::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-view-hive-jobs.png" alt-text="Taken weer geven, Apache Hive, HDInsight-cluster, Visual Studio" border="true":::

    Er wordt een lijst weergegeven met de Hive-taken die op het cluster zijn uitgevoerd.  

2. Selecteer een taak. Selecteer in het venster **samen vatting van Hive-taak** een van de volgende koppelingen:
    - **Taakquery**
    - **Taak uitvoer**
    - **Takenlogboek**  
    - **Logboek voor garen**

## <a name="run-apache-pig-scripts"></a>Apache Pig-scripts uitvoeren

1. Kies in de menu balk **bestand**  >  **Nieuw**  >  **project**.

2. Schakel in het **Start** venster het zoekvak in en voer **Pig** in. Selecteer vervolgens **varkens toepassing** en selecteer **volgende**.

3. Voer in het venster **uw nieuwe project configureren** een **project naam** in en selecteer of maak een **locatie** voor het project. Selecteer vervolgens **Maken**.

4. Dubbel klik in het deel venster IDE- **Solution Explorer** op **script. Pig** om het script te openen.

## <a name="feedback-and-known-issues"></a>Feedback en bekende problemen

* Een probleem waardoor resultaten beginnend met null-waarden niet worden weergegeven, is opgelost. Neem contact op met het ondersteuningsteam als u vastloopt op dit probleem.

* Het HQL-script dat door Visual Studio wordt gemaakt, is versleuteld, afhankelijk van de instelling van de lokale regio van de gebruiker. Het script wordt niet correct uitgevoerd als u het script als binair bestand naar een cluster uploadt.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u hoe geleerd u het Data Lake Tools-pakket voor Visual Studio kunt gebruiken om verbinding te maken met HDInsight-clusters vanuit Visual Studio. U hebt ook geleerd hoe u een Hive-query uitvoert. 

* [Apache Hive-query's uitvoeren met de Data Lake-hulpprogramma's voor Visual Studio](apache-hadoop-use-hive-visual-studio.md)
* [Wat is Apache Hive en HiveQL in azure HDInsight?](hdinsight-use-hive.md)
* [Een Apache Hadoop-cluster maken - sjabloon](apache-hadoop-linux-tutorial-get-started.md)
* [Apache Hadoop-taken in HDInsight verzenden](submit-apache-hadoop-jobs-programmatically.md)
* [Twitter-gegevens analyseren met Apache Hive en Apache Hadoop op HDInsight](../hdinsight-analyze-twitter-data-linux.md)
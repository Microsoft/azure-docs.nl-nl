---
title: Kernels voor Jupyter Notebook in Spark-clusters in azure HDInsight
description: Meer informatie over de PySpark-, PySpark3-en Spark-kernels voor Jupyter Notebook beschikbaar zijn met Spark-clusters in azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: ef2bc5e00779200e5447c8829a437824657a2227
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104865975"
---
# <a name="kernels-for-jupyter-notebook-on-apache-spark-clusters-in-azure-hdinsight"></a>Kernels voor het Jupyter Notebook op Apache Spark clusters in azure HDInsight

HDInsight Spark-clusters bieden kernels die u kunt gebruiken met de Jupyter Notebook op [Apache Spark](./apache-spark-overview.md) voor het testen van uw toepassingen. Een kernel is een programma dat uw code uitvoert en interpreteert. De drie kernels zijn:

- **PySpark** -voor toepassingen die zijn geschreven in Python2.
- **PySpark3** -voor toepassingen die zijn geschreven in Python3.
- **Spark** -voor toepassingen die zijn geschreven in scala.

In dit artikel leert u hoe u deze kernels en de voor delen van het gebruik ervan kunt gebruiken.

## <a name="prerequisites"></a>Vereisten

Een Apache Spark cluster in HDInsight. Zie [Apache Spark-clusters maken in Azure HDInsight](apache-spark-jupyter-spark-sql.md) voor instructies.

## <a name="create-a-jupyter-notebook-on-spark-hdinsight"></a>Een Jupyter Notebook maken in Spark HDInsight

1. Selecteer in de [Azure Portal](https://portal.azure.com/)uw Spark-cluster.  Zie de [lijst en clusters weer geven](../hdinsight-administer-use-portal-linux.md#showClusters) voor de instructies. De **overzichts** weergave wordt geopend.

2. Selecteer in de weer gave **overzicht** in het vak **cluster dashboards** de optie **Jupyter notebook**. Voer de beheerdersreferenties voor het cluster in als u daarom wordt gevraagd.

    :::image type="content" source="./media/apache-spark-jupyter-notebook-kernels/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png " alt-text="Jupyter Notebook op Apache Spark" border="true":::
  
   > [!NOTE]  
   > U kunt ook de Jupyter Notebook in Spark-cluster bereiken door de volgende URL in uw browser te openen. Vervang **CLUSTERNAME** door de naam van uw cluster.
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

3. Selecteer **Nieuw** en selecteer vervolgens **Pyspark**, **PySpark3** of **Spark** om een notitie blok te maken. Gebruik de Spark-kernel voor scala-toepassingen, PySpark-kernel voor Python2-toepassingen en PySpark3 kernel voor Python3-toepassingen.

    :::image type="content" source="./media/apache-spark-jupyter-notebook-kernels/kernel-jupyter-notebook-on-spark.png " alt-text="Kernels voor Jupyter Notebook op Spark" border="true":::

4. Er wordt een notitie blok geopend met de kernel die u hebt geselecteerd.

## <a name="benefits-of-using-the-kernels"></a>Voor delen van het gebruik van de kernels

Hier volgen enkele voor delen van het gebruik van de nieuwe kernels met Jupyter Notebook op Spark HDInsight-clusters.

- **Vooraf ingestelde contexten**. Met  **PySpark**, **PySpark3** of **Spark** -kernels hoeft u de Spark-of Hive-contexten niet expliciet in te stellen voordat u met uw toepassingen begint te werken. Deze contexten zijn standaard beschikbaar. Het betreft deze contexten:

  - **sc** -voor Spark-context
  - **sqlContext** -voor Hive-context

    U hoeft dus **geen** instructies uit te voeren zoals de volgende om de contexten in te stellen:

    ```sql
    sc = SparkContext('yarn-client')
    sqlContext = HiveContext(sc)
    ```

    In plaats daarvan kunt u rechtstreeks de vooraf ingestelde contexten in uw toepassing gebruiken.

- **Cel magics**. De PySpark-kernel biedt enkele vooraf gedefinieerde ' magics '. Dit zijn speciale opdrachten waarmee u kunt aanroepen `%%` (bijvoorbeeld `%%MAGIC` `<args>` ). De Magic-opdracht moet het eerste woord in een code-cel zijn en meerdere regels inhoud toestaan. Het Magic-woord moet het eerste woord in de cel zijn. Het toevoegen van iets vóór het magische, zelfs opmerkingen, veroorzaakt een fout.     Zie [hier](https://ipython.readthedocs.org/en/stable/interactive/magics.html)voor meer informatie over magics.

    De volgende tabel geeft een lijst van de verschillende magics die beschikbaar zijn via de kernels.

   | Magische | Voorbeeld | Beschrijving |
   | --- | --- | --- |
   | help |`%%help` |Hiermee wordt een tabel met alle beschik bare magics gegenereerd met voor beeld en beschrijving |
   | Info |`%%info` |Hiermee worden sessie gegevens voor het huidige livy-eind punt uitgevoerd |
   | configureren |`%%configure -f`<br>`{"executorMemory": "1000M"`,<br>`"executorCores": 4`} |Hiermee configureert u de para meters voor het maken van een sessie. De Force Flag ( `-f` ) is verplicht als er al een sessie is gemaakt. deze zorgt ervoor dat de sessie wordt verwijderd en opnieuw wordt gemaakt. Bekijk [de hoofd tekst van de LIVY post/Sessions](https://github.com/cloudera/livy#request-body) voor een lijst met geldige para meters. Para meters moeten worden door gegeven als een JSON-teken reeks en moeten zich op de volgende regel na de Magic bevallen, zoals wordt weer gegeven in de kolom voor beeld. |
   | sql |`%%sql -o <variable name>`<br> `SHOW TABLES` |Voert een Hive-query uit op basis van de sqlContext. Als de `-o` para meter wordt door gegeven, wordt het resultaat van de query persistent gemaakt in de lokale python-context%% als een [Panda](https://pandas.pydata.org/) data frame. |
   | lokaal |`%%local`<br>`a=1` |Alle code in latere regels wordt lokaal uitgevoerd. De code moet geldige Python2-code zijn, ongeacht de kernel die u gebruikt. , Zelfs als u **PySpark3** of **Spark** -kernels hebt geselecteerd tijdens het maken van het notitie blok, `%%local` moet die cel alleen geldige Python2-code hebben als u het Magic in een cel gebruikt. |
   | logboeken |`%%logs` |Hiermee worden de logboeken voor de huidige livy-sessie uitgevoerd. |
   | delete |`%%delete -f -s <session number>` |Hiermee verwijdert u een specifieke sessie van het huidige livy-eind punt. U kunt de sessie die is gestart voor de kernel zelf niet verwijderen. |
   | opruimen |`%%cleanup -f` |Hiermee verwijdert u alle sessies voor het huidige livy-eind punt, inclusief de sessie van dit notitie blok. De Force Flag-f is verplicht. |

   > [!NOTE]  
   > Naast de magics die door de PySpark-kernel worden toegevoegd, kunt u ook gebruikmaken [van de ingebouwde IPython magics](https://ipython.org/ipython-doc/3/interactive/magics.html#cell-magics), waaronder `%%sh` . U kunt het `%%sh` Magic gebruiken om scripts uit te voeren en code blok keren op het cluster hoofd knooppunt.

- **Automatische visualisatie**. De Pyspark-kernel visualiseert automatisch de uitvoer van Hive-en SQL-query's. U kunt kiezen uit verschillende soorten visualisaties, waaronder tabel, cirkel, lijn, vlak, staaf.

## <a name="parameters-supported-with-the-sql-magic"></a>Para meters die worden ondersteund met%% SQL Magic

Het `%%sql` Magic ondersteunt verschillende para meters die u kunt gebruiken om het soort uitvoer te bepalen dat u ontvangt wanneer u query's uitvoert. De volgende tabel bevat de uitvoer.

| Parameter | Voorbeeld | Beschrijving |
| --- | --- | --- |
| -o |`-o <VARIABLE NAME>` |Gebruik deze para meter om het resultaat van de query op te slaan in de context%% Local python als een [Panda](https://pandas.pydata.org/) data frame. De naam van de variabele data frame is de naam van de variabele die u opgeeft. |
| -q |`-q` |Gebruik deze para meter om visualisaties voor de cel uit te scha kelen. Als u de inhoud van een cel niet wilt visualiseren en u deze gewoon wilt vastleggen als een data frame, gebruikt u `-q -o <VARIABLE>` . Als u visualisaties wilt uitschakelen zonder de resultaten vast te leggen (bijvoorbeeld voor het uitvoeren van een SQL-query, zoals een `CREATE TABLE` instructie), gebruikt u `-q` zonder een argument op te geven `-o` . |
| -m |`-m <METHOD>` |Waarbij de **methode** ofwel **nemen** of voor **beeld** is (de standaard instelling **is).** Als de methode is **`take`** ingesteld, worden de elementen van de kernel verzameld van de resultaat gegevensset die is opgegeven door MaxRows (verderop in deze tabel beschreven). Als de methode voor **beeld** is, wordt de kernel wille keurig voor beelden van elementen van de gegevensset op basis van de `-r` para meter in de volgende tabel beschreven. |
| -r |`-r <FRACTION>` |Hier is een **fractie** een getal met drijvende komma tussen 0,0 en 1,0. Als de voorbeeld methode voor de SQL-query is, wordt in `sample` de kernel wille keurig de opgegeven Fractie van de onderdelen van de resultatenset voor u weer gegeven. Als u bijvoorbeeld een SQL-query met de argumenten uitvoert `-m sample -r 0.01` , worden 1% van de resultaat rijen wille keurig voor beeld. |
| -n |`-n <MAXROWS>` |**MaxRows** is een geheel getal. De kernel beperkt het aantal uitvoer rijen tot **MaxRows**. Als **MaxRows** een negatief getal is, zoals **-1**, is het aantal rijen in de resultatenset niet beperkt. |

**Voorbeeld:**

```sql
%%sql -q -m sample -r 0.1 -n 500 -o query2
SELECT * FROM hivesampletable
```

De bovenstaande instructie voert de volgende acties uit:

- Hiermee worden alle records uit **hivesampletable** geselecteerd.
- Omdat we-q gebruiken, wordt de functie voor autovisualisatie uitgeschakeld.
- Omdat we een `-m sample -r 0.1 -n 500` wille keurig 10% van de rijen in de hivesampletable gebruiken, wordt de grootte van het resultaat ingesteld op 500 rijen.
- Ten slotte wordt `-o query2` de uitvoer ook opgeslagen in een data frame met de naam **query2**.

## <a name="considerations-while-using-the-new-kernels"></a>Overwegingen bij het gebruik van de nieuwe kernels

Welke kernel u gebruikt, waardoor de notitie blokken die worden uitgevoerd, verbruikt de cluster resources.  Met deze kernels, omdat de contexten vooraf worden ingesteld, wordt de context niet door de notitie blokken afgesloten. En daarom blijven de cluster bronnen in gebruik. Het is een goed idee om de optie **sluiten en stoppen** te gebruiken in het menu **bestand** van het notitie blok wanneer u klaar bent met het notitie blok. De sluiting beëindigt de context en verlaat vervolgens het notitie blok.

## <a name="where-are-the-notebooks-stored"></a>Waar worden de notitie blokken opgeslagen?

Als uw cluster Azure Storage als standaard-opslag account gebruikt, worden Jupyter-notebooks opgeslagen in het opslag account in de map **/HdiNotebooks** .  Notebooks, tekst bestanden en mappen die u maakt vanuit Jupyter zijn toegankelijk vanuit het opslag account.  Als u bijvoorbeeld Jupyter gebruikt voor het maken van een map **`myfolder`** en een notebook **MyFolder/mynotebook. ipynb**, hebt u toegang tot die notebook op `/HdiNotebooks/myfolder/mynotebook.ipynb` het opslag account.  Het omgekeerde is ook waar, dat wil zeggen, als u een notitie blok rechtstreeks naar uw opslag account uploadt op `/HdiNotebooks/mynotebook1.ipynb` , de laptop ook zichtbaar is vanuit Jupyter.  Notebooks blijven aanwezig in het opslag account, zelfs nadat het cluster is verwijderd.

> [!NOTE]  
> HDInsight-clusters met Azure Data Lake Storage als standaard opslag slaan geen notitie blokken op in gekoppelde opslag.

De manier waarop notitie blokken worden opgeslagen in het opslag account, is compatibel met [Apache HADOOP HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html). Als u een SSH-verbinding met het cluster hebt, kunt u de opdrachten voor bestands beheer gebruiken:

| Opdracht | Beschrijving |
|---------|-------------|
| `hdfs dfs -ls /HdiNotebooks` | # Lijst alles in de hoofdmap: alles in deze map is zichtbaar voor Jupyter vanaf de start pagina |
| `hdfs dfs –copyToLocal /HdiNotebooks` | # De inhoud van de map HdiNotebooks downloaden|
| `hdfs dfs –copyFromLocal example.ipynb /HdiNotebooks` | # Upload een notebook-voor beeld. ipynb naar de hoofdmap zodat deze zichtbaar is vanuit Jupyter |

Of het cluster Azure Storage of Azure Data Lake Storage als het standaard opslag account gebruikt, de notitie blokken worden ook opgeslagen op het cluster hoofd knooppunt op `/var/lib/jupyter` .

## <a name="supported-browser"></a>Ondersteunde browser

Jupyter-notebooks op Spark HDInsight-clusters worden alleen ondersteund op Google Chrome.

## <a name="suggestions"></a>Suggesties

De nieuwe kernels bevinden zich in de ontwikkelings fase en zullen gedurende een periode vervallen. De Api's kunnen dus worden gewijzigd als deze kernels rijp zijn. We waarderen de feedback die u hebt bij het gebruik van deze nieuwe kernels. De feedback is handig bij het vorm geven van de definitieve versie van deze kernels. U kunt uw opmerkingen/feedback in het gedeelte **feedback** onder aan dit artikel laten staan.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht: Apache Spark in Azure HDInsight](apache-spark-overview.md)
- [Apache Zeppelin-notebooks gebruiken met een Apache Spark-cluster in HDInsight](apache-spark-zeppelin-notebook.md)
- [Externe pakketten gebruiken met Jupyter-notebooks](apache-spark-jupyter-notebook-use-external-packages.md)
- [Jupyter op uw computer installeren en verbinding maken met een HDInsight Spark-cluster](apache-spark-jupyter-notebook-install-locally.md)

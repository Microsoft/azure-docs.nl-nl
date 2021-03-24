---
title: Spark-toepassings afhankelijkheden beheren in azure HDInsight
description: Dit artikel bevat een inleiding in het beheren van Spark-afhankelijkheden in een HDInsight Spark-cluster voor PySpark-en scala-toepassingen.
author: yanancai
ms.author: yanacai
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: c950903522d42b3c279cb89f3a6031043fd49bf3
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104868797"
---
# <a name="manage-spark-application-dependencies"></a>Spark-toepassingsafhankelijkheden beheren

In dit artikel vindt u informatie over het beheren van afhankelijkheden voor uw Spark-toepassingen die worden uitgevoerd op HDInsight. We beslaan zowel scala als PySpark op Spark-toepassing en cluster bereik.

Gebruik snelle koppelingen om naar de sectie te gaan op basis van uw gebruikers Case:
* [Spark-taak jar-afhankelijkheden instellen met behulp van Jupyter Notebook](#use-jupyter-notebook)
* [Spark-taak jar-afhankelijkheden instellen met behulp van Azure-toolkit voor IntelliJ](#use-azure-toolkit-for-intellij)
* [Jar-afhankelijkheden configureren voor Spark-cluster](#jar-libs-for-cluster)
* [Jar-afhankelijkheden veilig beheren](#safely-manage-jar-dependencies)
* [Een Spark-taak python-pakket instellen met behulp van Jupyter Notebook](#use-jupyter-notebook-1)
* [Python-pakketten veilig beheren voor Spark-cluster](#python-packages-for-cluster)

## <a name="jar-libs-for-one-spark-job"></a>Jar bibliotheken voor een Spark-taak
### <a name="use-jupyter-notebook"></a>Jupyter Notebook gebruiken
Wanneer een Spark-sessie wordt gestart in Jupyter Notebook in Spark kernel voor scala, kunt u pakketten configureren van:

* [Maven-opslag plaats](https://search.maven.org/)of door de Community bijgedragen pakketten bij [Spark-pakketten](https://spark-packages.org/).
* Jar-bestanden die zijn opgeslagen op de primaire opslag van uw cluster.

U gebruikt de `%%configure` Magic om het notitie blok te configureren voor het gebruik van een extern pakket. Zorg er in notitie blokken die gebruikmaken van externe pakketten voor dat u het Magic aanroept `%%configure` in de eerste cel van de code. Dit zorgt ervoor dat de kernel zo is geconfigureerd dat het pakket wordt gebruikt voordat de sessie wordt gestart.

>
>[!IMPORTANT]  
>Als u vergeet de kernel in de eerste cel te configureren, kunt u de gebruiken `%%configure` met de `-f` para meter, maar wordt de sessie opnieuw gestart en gaat alle voortgang verloren.

**Voor beeld voor pakketten uit maven-opslag plaats of Spark-pakketten**

Nadat u het pakket hebt gevonden vanuit de Maven-opslag plaats, moet u de waarden voor **GroupId**, **ArtifactId** en **Version** verzamelen. De drie waarden samen voegen, gescheiden door een dubbele punt (**:**).

   :::image type="content" source="./media/apache-spark-manage-dependencies/spark-package-schema.png " alt-text="Pakket schema samen voegen" border="true":::stallatiekopiepakket schema "Border =" True ":::

Zorg ervoor dat de waarden die u verzamelt, overeenkomen met het cluster. In dit geval gebruiken we het Spark Cosmos DB-connector pakket voor scala 2,11 en Spark 2,3 voor HDInsight 3,6 Spark-cluster. Als u het niet zeker weet, voert u `scala.util.Properties.versionString` in code-cel uit in Spark-kernel om de cluster scala-versie op te halen. Voer uit `sc.version` om de versie van het cluster Spark op te halen.

```
%%configure { "conf": {"spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.3.0_2.11:1.3.3" }}
```

**Voor beeld voor potten die zijn opgeslagen op de primaire opslag**

Gebruik het [URI-schema](../hdinsight-hadoop-linux-information.md#URI-and-scheme) voor JAR-bestanden op uw clusters primaire opslag. Dit is `wasb://` voor Azure Storage, `abfs://` voor Azure Data Lake Storage Gen2 of `adl://` voor Azure Data Lake Storage Gen1. Als beveiligde overdracht is ingeschakeld voor Azure Storage of Data Lake Storage Gen2, is de URI `wasbs://` of `abfss://` . Zie [beveiligde overdracht](../../storage/common/storage-require-secure-transfer.md).

Gebruik een door komma's gescheiden lijst met jar-paden voor meerdere jar-bestanden. Globs zijn toegestaan. De potten zijn opgenomen op de driver en de uitvoerder classpath.

```
%%configure { "conf": {"spark.jars": "wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/azure-cosmosdb-spark_2.3.0_2.11-1.3.3.jar" }}
```

Nadat u externe pakketten hebt geconfigureerd, kunt u in de cel code importeren uitvoeren om te controleren of de pakketten correct zijn geplaatst.

```scala
import com.microsoft.azure.cosmosdb.spark._
```

### <a name="use-azure-toolkit-for-intellij"></a>Azure-toolkit voor IntelliJ gebruiken
[Azure-Toolkit voor IntelliJ-invoeg](./apache-spark-intellij-tool-plugin.md) toepassing biedt UI-ervaring voor het indienen van Spark scala-toepassingen naar een HDInsight-cluster. Het biedt `Referenced Jars` en `Referenced Files` Eigenschappen voor het configureren van jar bibliotheken-paden bij het verzenden van de Spark-toepassing. Zie voor meer informatie over [het gebruik van Azure-Toolkit voor IntelliJ-invoeg toepassing voor HDInsight](./apache-spark-intellij-tool-plugin.md#run-a-spark-scala-application-on-an-hdinsight-spark-cluster).

:::image type="content" source="./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-02.png" alt-text="Het dialoogvenster voor de verzending van Spark" border="true":::

## <a name="jar-libs-for-cluster"></a>Jar bibliotheken voor cluster
In sommige gevallen wilt u mogelijk de jar-afhankelijkheden op cluster niveau configureren zodat elke toepassing standaard met dezelfde afhankelijkheden kan worden ingesteld. De aanpak is om uw jar-paden toe te voegen aan Spark-stuur programma en het pad naar de uitvoerder klasse.

1. Voer de onderstaande voorbeeld script acties uit om jar-bestanden van de primaire opslag `wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*` naar het lokale cluster bestands systeem te kopiëren `/usr/libs/sparklibs` . De stap is vereist omdat Linux wordt gebruikt `:` voor het scheiden van een klassen lijst, maar HDInsight ondersteunt alleen opslag paden met schema als `wasb://` . Het externe opslagpad werkt niet goed als u het rechtstreeks toevoegt aan het pad van de klasse.

    ```bash
    sudo mkdir -p /usr/libs/sparklibs
    sudo hadoop fs -copyToLocal wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*.* /usr/libs/sparklibs
    ```

2. Wijzig de configuratie van de Spark-service van Ambari om het pad naar de klasse bij te werken. Ga naar **Ambari > Spark >-configuratie > aangepaste Spark2-defaults**. Voeg als volgt een **eigenschap toe** . Gebruik `:` deze om paden te scheiden als u meer dan één pad wilt toevoegen. Globs zijn toegestaan.

    ```
    spark.driver.extraClassPath=/usr/libs/sparklibs/*
    spark.executor.extraClassPath=/usr/libs/sparklibs/*
    ```

   :::image type="content" source="./media/apache-spark-manage-dependencies/change-spark-default-config.png " alt-text="Wijzigen Spark standaard configuratie" border="true":::QL config "Border =" True ":::

3. Sla de gewijzigde configuraties op en start betrokken services opnieuw op.

   Betrokken :::image type="content" source="./media/apache-spark-manage-dependencies/restart-impacted-services.png " alt-text="Services worden opnieuw opgestart" border="true":::: Ted-Services "Border =" True ",::

U kunt de stappen automatiseren met [script acties](../hdinsight-hadoop-customize-cluster-linux.md). Script actie voor het [toevoegen van aangepaste bibliotheken met hive](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh) is een goede verwijzing. Wanneer u de configuratie van Spark-service wijzigt, moet u ervoor zorgen dat u Ambari-Api's gebruikt in plaats van de configuratie bestanden rechtstreeks te wijzigen. 

## <a name="safely-manage-jar-dependencies"></a>Jar-afhankelijkheden veilig beheren
HDInsight-cluster heeft ingebouwde jar-afhankelijkheden en de updates voor deze jar-versies worden van tijd tot tijd uitgevoerd. Als u wilt voor komen dat versie conflicten optreden tussen ingebouwde potten en de potten die u ter referentie brengt, kunt u overwegen om [uw toepassings afhankelijkheden te arceren](./safely-manage-jar-dependency.md).

## <a name="python-packages-for-one-spark-job"></a>Python-pakketten voor een Spark-taak
### <a name="use-jupyter-notebook"></a>Jupyter Notebook gebruiken
HDInsight Jupyter Notebook PySpark kernel biedt geen ondersteuning voor het rechtstreeks installeren van Python-pakketten van PyPi of Anaconda-pakket opslagplaats. Als u `.zip` , `.egg` of `.py` afhankelijkheden hebt en u deze wilt raadplegen voor een Spark-sessie, volgt u de onderstaande stappen:

1. Voer de onderstaande voorbeeld script acties uit om te kopiëren naar `.zip` `.egg` of `.py` bestanden van de primaire opslag `wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*` naar het lokale cluster bestands systeem `/usr/libs/pylibs` . De stap is vereist omdat Linux wordt gebruikt `:` voor het scheiden van een lijst met zoek paden, maar HDInsight ondersteunt alleen opslag paden met schema als `wasb://` . Het externe opslagpad werkt niet naar behoren wanneer u gebruikt `sys.path.insert` .

    ```bash
    sudo mkdir -p /usr/libs/pylibs
    sudo hadoop fs -copyToLocal wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*.* /usr/libs/pylibs
    ```

2. Voer in uw notebook de volgende code uit in een code-cel met PySpark kernel:

   ```python
   import sys
   sys.path.insert(0, "/usr/libs/pylibs/pypackage.zip")
   ```

3. Voer uit `import` om te controleren of uw pakketten zijn opgenomen.  

## <a name="python-packages-for-cluster"></a>Python-pakketten voor cluster
U kunt Python-pakketten van Anaconda op het cluster installeren met behulp van de Conda-opdracht via script acties. De geïnstalleerde pakketten bevinden zich op het cluster niveau en zijn van toepassing op alle toepassingen. 

HDInsight Spark-cluster heeft twee ingebouwde python-installaties, Anaconda python 2,7 en Anaconda python 3,5. Meer informatie over de standaard instellingen van python voor services en het veilig installeren van externe Python-pakketten zonder het cluster te verbreken, vindt u in meer details [voor het veilig beheren van python-afhankelijkheden voor uw cluster](./apache-spark-python-package-installation.md).

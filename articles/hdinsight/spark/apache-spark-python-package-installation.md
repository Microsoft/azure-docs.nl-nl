---
title: Script actie voor python-pakketten met Jupyter in azure HDInsight
description: Stapsgewijze instructies voor het gebruik van script actie voor het configureren van Jupyter-notebooks die beschikbaar zijn met HDInsight Spark-clusters voor het gebruik van externe Python-pakketten.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-python
ms.date: 04/29/2020
ms.openlocfilehash: 8fbbe137ece7aac2dd2196c5ebec435e118297ad
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98929823"
---
# <a name="safely-manage-python-environment-on-azure-hdinsight-using-script-action"></a>Een Python-omgeving veilig beheren in Azure HDInsight met scriptactie

HDInsight heeft twee ingebouwde python-installaties in het Spark-cluster, Anaconda python 2,7 en python 3,5. Klanten moeten mogelijk de python-omgeving aanpassen, zoals het installeren van externe Python-pakketten. Hier zien we de best practice van het veilig beheren van python-omgevingen voor Apache Spark clusters op HDInsight.

## <a name="prerequisites"></a>Vereisten

Een Apache Spark-cluster in HDInsight. Zie [Apache Spark-clusters maken in Azure HDInsight](apache-spark-jupyter-spark-sql.md) voor instructies. Als u nog geen Spark-cluster op HDInsight hebt, kunt u script acties uitvoeren tijdens het maken van het cluster. Raadpleeg de documentatie over [het gebruik van aangepaste script acties](../hdinsight-hadoop-customize-cluster-linux.md).

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Ondersteuning voor open-source software die wordt gebruikt in HDInsight-clusters

De Microsoft Azure HDInsight-service gebruikt een omgeving van open-source technologieën die zijn gevormd rond Apache Hadoop. Microsoft Azure biedt een algemeen ondersteunings niveau voor open-source technologieën. Zie de [website met veelgestelde vragen over Azure-ondersteuning](https://azure.microsoft.com/support/faq/)voor meer informatie. De HDInsight-service biedt een extra ondersteunings niveau voor ingebouwde componenten.

Er zijn twee soorten open source-onderdelen die beschikbaar zijn in de HDInsight-service:

|Onderdeel |Beschrijving |
|---|---|
|Ingebouwd|Deze onderdelen zijn vooraf geïnstalleerd op HDInsight-clusters en bieden kern functionaliteit van het cluster. Bijvoorbeeld Apache Hadoop GARENs Resource Manager, de Apache Hive query language (HiveQL) en de mahout-bibliotheek behoren tot deze categorie. Een volledige lijst met cluster onderdelen is beschikbaar in [Wat is er nieuw in de Apache Hadoop cluster versies van HDInsight](../hdinsight-component-versioning.md).|
|Aangepast|Als gebruiker van het cluster kan in uw workload elk onderdeel dat beschikbaar is in de community, worden geïnstalleerd of gebruikt, of door u gemaakt.|

> [!IMPORTANT]
> Onderdelen die worden meegeleverd met het HDInsight-cluster, worden volledig ondersteund. Microsoft Ondersteuning helpt bij het isoleren en oplossen van problemen met betrekking tot deze onderdelen.
>
> Aangepaste onderdelen ontvangen commercieel redelijke ondersteuning om u te helpen het probleem verder op te lossen. Micro soft ondersteuning kan het probleem mogelijk oplossen of u wordt gevraagd beschik bare kanalen te betrekken voor de open source-technologieën waar diep gaande expertise voor die technologie wordt gevonden. Er zijn bijvoorbeeld veel community-sites die kunnen worden gebruikt, zoals: [micro soft Q&een vraag pagina voor HDInsight](/answers/topics/azure-hdinsight.html) `https://stackoverflow.com` . Ook Apache-projecten hebben project sites op `https://apache.org` .

## <a name="understand-default-python-installation"></a>Informatie over de standaard installatie van python

HDInsight Spark-cluster wordt gemaakt met Anaconda-installatie. Er zijn twee python-installaties in het cluster, Anaconda python 2,7 en python 3,5. In de volgende tabel ziet u de standaard python-instellingen voor Spark, livy en Jupyter.

|Instelling |Python 2,7|Python 3,5|
|----|----|----|
|Pad|/usr/bin/anaconda/bin|/usr/bin/anaconda/envs/py35/bin|
|Spark-versie|Standaard ingesteld op 2,7|Kan de configuratie wijzigen in 3,5|
|Livy-versie|Standaard ingesteld op 2,7|Kan de configuratie wijzigen in 3,5|
|Jupyter|PySpark-kernel|PySpark3-kernel|

## <a name="safely-install-external-python-packages"></a>Externe Python-pakketten veilig installeren

HDInsight-cluster is afhankelijk van de ingebouwde python-omgeving, zowel python 2,7 als python 3,5. Het rechtstreeks installeren van aangepaste pakketten in deze ingebouwde omgevingen kan leiden tot onverwachte wijzigingen in de bibliotheek versie. En breek het cluster verder. Volg de onderstaande stappen om de aangepaste externe Python-pakketten voor uw Spark-toepassingen veilig te installeren.

1. Maak een virtuele python-omgeving met behulp van Conda. Een virtuele omgeving biedt een geïsoleerde ruimte voor uw projecten zonder dat anderen dat doen. Bij het maken van de virtuele python-omgeving kunt u een python-versie opgeven die u wilt gebruiken. U moet nog steeds een virtuele omgeving maken, zelfs als u python 2,7 en 3,5 wilt gebruiken. Dit is vereist om ervoor te zorgen dat de standaard omgeving van het cluster niet wordt verzorgd. Voer script acties uit op uw cluster voor alle knoop punten met het onderstaande script om een virtuele python-omgeving te maken.

    -   `--prefix` Hiermee geeft u een pad op waar de virtuele Conda-omgeving zich bevindt. Er zijn verschillende configuraties die verder moeten worden gewijzigd op basis van het pad dat hier wordt opgegeven. In dit voor beeld gebruiken we de py35new, omdat het cluster al een bestaande virtuele omgeving met de naam py35 bevat.
    -   `python=` Hiermee geeft u de python-versie op voor de virtuele omgeving. In dit voor beeld gebruiken we versie 3,5, dezelfde versie als het cluster dat in één is ingebouwd. U kunt ook andere python-versies gebruiken om de virtuele omgeving te maken.
    -   `anaconda` Hiermee geeft u de package_spec als Anaconda voor het installeren van Anaconda-pakketten in de virtuele omgeving.
    
    ```bash
    sudo /usr/bin/anaconda/bin/conda create --prefix /usr/bin/anaconda/envs/py35new python=3.5 anaconda --yes
    ```

2. Installeer indien nodig externe Python-pakketten in de gemaakte virtuele omgeving. Voer script acties uit op uw cluster voor alle knoop punten met het onderstaande script om externe Python-pakketten te installeren. U moet sudo-bevoegdheid hier hebben om bestanden te schrijven naar de map virtuele omgeving.

    Zoek in de [pakket index](https://pypi.python.org/pypi) naar de volledige lijst met pakketten die beschikbaar zijn. U kunt ook een lijst met beschik bare pakketten uit andere bronnen ophalen. U kunt bijvoorbeeld pakketten installeren die beschikbaar worden gesteld via [Conda-vervalsing](https://conda-forge.org/feedstocks/).

    Gebruik de onderstaande opdracht als u een bibliotheek met de meest recente versie wilt installeren:

    - Conda-kanaal gebruiken:

        -   `seaborn` is de naam van het pakket dat u wilt installeren.
        -   `-n py35new` Geef de naam op van de virtuele omgeving die zojuist wordt gemaakt. Zorg ervoor dat u de naam wijzigt die overeenkomt met het maken van uw virtuele omgeving.

        ```bash
        sudo /usr/bin/anaconda/bin/conda install seaborn -n py35new --yes
        ```

    - Of gebruik PyPi opslag plaats, Wijzig `seaborn` en `py35new` dienovereenkomstig:
        ```bash
        sudo /usr/bin/anaconda/envs/py35new/bin/pip install seaborn
        ```

    Gebruik de onderstaande opdracht als u een bibliotheek met een specifieke versie wilt installeren:

    - Conda-kanaal gebruiken:

        -   `numpy=1.16.1` is de naam en versie van het pakket dat u wilt installeren.
        -   `-n py35new` Geef de naam op van de virtuele omgeving die zojuist wordt gemaakt. Zorg ervoor dat u de naam wijzigt die overeenkomt met het maken van uw virtuele omgeving.

        ```bash
        sudo /usr/bin/anaconda/bin/conda install numpy=1.16.1 -n py35new --yes
        ```

    - Of gebruik PyPi opslag plaats, Wijzig `numpy==1.16.1` en `py35new` dienovereenkomstig:

        ```bash
        sudo /usr/bin/anaconda/envs/py35new/bin/pip install numpy==1.16.1
        ```

    Als u de naam van de virtuele omgeving niet weet, kunt u SSHen naar het hoofd knooppunt van het cluster en uitvoeren `/usr/bin/anaconda/bin/conda info -e` om alle virtuele omgevingen weer te geven.

3. Wijzig Spark-en livy-configuraties en ga naar de gemaakte virtuele omgeving.

    1. Open Ambari-gebruikers interface, ga naar Spark2-pagina, tabblad Configuratie.

        ![Spark-en livy-configuratie wijzigen via Ambari](./media/apache-spark-python-package-installation/ambari-spark-and-livy-config.png)

    2. Vouw Geavanceerd livy2-env uit en voeg onderaan de instructies toe. Als u de virtuele omgeving met een ander voor voegsel hebt geïnstalleerd, wijzigt u het pad dienovereenkomstig.

        ```bash
        export PYSPARK_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        export PYSPARK_DRIVER_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        ```

        ![Livy-configuratie wijzigen via Ambari](./media/apache-spark-python-package-installation/ambari-livy-config.png)

    3. Vouw Advanced spark2-env uit en vervang de bestaande instructie export PYSPARK_PYTHON onderaan. Als u de virtuele omgeving met een ander voor voegsel hebt geïnstalleerd, wijzigt u het pad dienovereenkomstig.

        ```bash
        export PYSPARK_PYTHON=${PYSPARK_PYTHON:-/usr/bin/anaconda/envs/py35new/bin/python}
        ```

        ![Spark-configuratie wijzigen via Ambari](./media/apache-spark-python-package-installation/ambari-spark-config.png)

    4. Sla de wijzigingen op en start de betrokken services opnieuw. Voor deze wijzigingen moet de Spark2-service opnieuw worden gestart. De Ambari-gebruikers interface vraagt een vereiste herinnering opnieuw op te starten. Klik op opnieuw opstarten om alle betrokken services opnieuw te starten.

        ![Services opnieuw starten](./media/apache-spark-python-package-installation/ambari-restart-services.png)

    5. Stel twee eigenschappen in op uw Spark-sessie om ervoor te zorgen dat de taak naar de bijgewerkte Spark-configuratie verwijst: `spark.yarn.appMasterEnv.PYSPARK_PYTHON` en `spark.yarn.appMasterEnv.PYSPARK_DRIVER_PYTHON` . 

        Gebruik de functie met behulp van de terminal of een notitie blok `spark.conf.set` .

        ```spark
        spark.conf.set("spark.yarn.appMasterEnv.PYSPARK_PYTHON", "/usr/bin/anaconda/envs/py35/bin/python")
        spark.conf.set("spark.yarn.appMasterEnv.PYSPARK_DRIVER_PYTHON", "/usr/bin/anaconda/envs/py35/bin/python")
        ```

        Als u livy gebruikt, voegt u de volgende eigenschappen toe aan de hoofd tekst van de aanvraag:

        ```
        “conf” : {
        “spark.yarn.appMasterEnv.PYSPARK_PYTHON”:”/usr/bin/anaconda/envs/py35/bin/python”,
        “spark.yarn.appMasterEnv.PYSPARK_DRIVER_PYTHON”:”/usr/bin/anaconda/envs/py35/bin/python”
        }
        ```

4. Als u de nieuwe virtuele omgeving die u hebt gemaakt, wilt gebruiken op Jupyter. Wijzig Jupyter-configuraties en start Jupyter opnieuw. Voer script acties uit op alle hoofd knooppunten met de instructie hieronder om Jupyter naar de nieuwe virtuele omgeving te wijzen. Zorg ervoor dat u het pad naar het voor voegsel dat u hebt opgegeven voor uw virtuele omgeving wijzigt. Nadat u deze script actie hebt uitgevoerd, start u de Jupyter-service opnieuw via de Ambari-gebruikers interface om deze wijziging beschikbaar te maken.

    ```bash
    sudo sed -i '/python3_executable_path/c\ \"python3_executable_path\" : \"/usr/bin/anaconda/envs/py35new/bin/python3\"' /home/spark/.sparkmagic/config.json
    ```

    U kunt de python-omgeving in Jupyter Notebook dubbel bevestigen door de volgende code uit te voeren:

    ![Python-versie controleren in Jupyter Notebook](./media/apache-spark-python-package-installation/check-python-version-in-jupyter.png)

## <a name="known-issue"></a>Bekend probleem

Er is een bekende fout voor Anaconda `4.7.11` -versie, `4.7.12` en `4.8.0` . Als u de script acties ziet, reageert niet meer op `"Collecting package metadata (repodata.json): ...working..."` en mislukt deze `"Python script has been killed due to timeout after waiting 3600 secs"` . U kunt [Dit script](https://gregorysfixes.blob.core.windows.net/public/fix-conda.sh) downloaden en uitvoeren als script acties op alle knoop punten om het probleem op te lossen.

Als u uw Anaconda-versie wilt controleren, kunt u SSHen naar het knoop punt cluster header en uitvoeren `/usr/bin/anaconda/bin/conda --v` .

## <a name="next-steps"></a>Volgende stappen

* [Overzicht: Apache Spark in Azure HDInsight](apache-spark-overview.md)
* [Externe pakketten met Jupyter-notebooks in Apache Spark](apache-spark-jupyter-notebook-use-external-packages.md)
* [Taken die worden uitgevoerd in een Apache Spark-cluster in HDInsight, traceren en er fouten in oplossen](apache-spark-job-debugging.md)

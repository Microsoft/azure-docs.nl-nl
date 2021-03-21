---
title: Azure HDInsight voor Visual Studio code
description: Meer informatie over het gebruik van de Spark-& Hive-Hulpprogram Ma's (Azure HDInsight) voor Visual Studio code. Gebruik de hulpprogram ma's om query's en scripts te maken en in te dienen.
ms.service: hdinsight
ms.topic: how-to
ms.date: 10/20/2020
ms.custom: devx-track-python
ms.openlocfilehash: 1209c49cbd988a02b1e3503994fdbdac3f26216a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946143"
---
# <a name="use-spark--hive-tools-for-visual-studio-code"></a>Gebruik de Spark-& Hive-Hulpprogram Ma's voor Visual Studio code

Meer informatie over het gebruik van Apache Spark & Hive Tools voor Visual Studio Code. Gebruik de hulpprogramma's om Apache Hive-batchtaken, interactieve Hive-query's en PySpark-scripts te maken en te verzenden voor Apache Spark. Eerst wordt beschreven hoe u Spark & Hive Tools installeert in Visual Studio Code. Vervolgens wordt uitgelegd hoe u taken kunt verzenden naar Spark & Hive Tools.  

Spark & Hive Tools kan worden geïnstalleerd op platforms die worden ondersteund door Visual Studio Code. Houd rekening met de volgende vereisten voor verschillende platforms.

## <a name="prerequisites"></a>Vereisten

De volgende items zijn vereist voor het voltooien van de stappen in dit artikel:

- Een Azure HDInsight-cluster. Zie aan de [slag met HDInsight](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)om een cluster te maken. Of gebruik een Spark-en Hive-cluster dat ondersteuning biedt voor een Apache livy-eind punt.
- [Visual Studio Code](https://code.visualstudio.com/).
- [Mono](https://www.mono-project.com/docs/getting-started/install/). Mono is alleen vereist voor Linux en macOS.
- [Een interactieve PySpark-omgeving voor Visual Studio Code](set-up-pyspark-interactive-environment.md).
- Een lokale map. In dit artikel wordt gebruikgemaakt van  **C:\HD\HDexample**.

## <a name="install-spark--hive-tools"></a>Spark & Hive Tools installeren

Wanneer u aan de vereisten voldoet, kunt u Spark & Hive Tools voor Visual Studio Code installeren door de volgende stappen uit te voeren:

1. Open Visual Studio Code.

2. Navigeer op de menu balk naar **Beeld** > **Uitbreidingen**.

3. Voer in het zoekvak **Spark & Hive** in.

4. Selecteer **Spark & Hive Tools** in de zoekresultaten en selecteer vervolgens **Installeren**:

   ![Spark & Hive voor Visual Studio Code Python installeren](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. Selecteer zo nodig **Opnieuw laden**.

## <a name="open-a-work-folder"></a>Een werkmap openen

Voer de volgende stappen uit om een werkmap te openen en een bestand te maken in Visual Studio Code:

1. Ga in de menu balk naar **bestand**  >  **openen map...**  >  **C:\HD\HDexample** en selecteer vervolgens de knop **map selecteren** . De map wordt weergegeven in de weergave **Explorer** aan de linkerkant.

2. Selecteer in de **Verkenner** -weer gave de map **HDexample** en selecteer vervolgens het pictogram **nieuw bestand** naast de werkmap:

   ![pictogram Nieuw bestand in Visual Studio Code](./media/hdinsight-for-vscode/visual-studio-code-new-file.png)

3. Geef het nieuwe bestand een naam met behulp van de `.hql` (Hive-query's) of de `.py` bestands extensie (Spark-script). In dit voor beeld wordt **HelloWorld. HQL** gebruikt.

## <a name="set-the-azure-environment"></a>De Azure-omgeving instellen

Voor een nationale Cloud gebruiker voert u de volgende stappen uit om eerst de Azure-omgeving in te stellen en vervolgens de **Azure: Sign in** -opdracht te gebruiken om u aan te melden bij Azure:

1. Navigeer naar   >  **instellingen voor bestands voorkeuren**  >  .
2. Zoek naar de volgende teken reeks: **Azure: Cloud**.
3. Selecteer de nationale Cloud in de lijst:

   ![Configuratie van standaard aanmeldings vermelding instellen](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-an-azure-account"></a>Verbinding maken met een Azure-account

Voordat u scripts kunt verzenden naar uw clusters vanuit Visual Studio code, kan de gebruiker zich aanmelden bij het Azure-abonnement of [een HDInsight-cluster koppelen](#link-a-cluster). Gebruik het Ambari gebruikers naam/wacht woord of de referenties die aan het domein zijn gekoppeld voor het ESP-cluster om verbinding te maken met uw HDInsight-cluster. Volg deze stappen om verbinding te maken met Azure:

1. Ga op de menubalk naar **Beeld** > **Opdrachtpalet...** en voer **Azure: Aanmelden** in:

   ![aanmelding bij Spark & Hive Tools voor Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. Volg de instructies om u aan te melden bij Azure. Nadat u verbinding hebt gemaakt, ziet u de naam van uw Azure-account op de statusbalk onder in het Visual Studio Code-venster.  

## <a name="link-a-cluster"></a>Een cluster koppelen

### <a name="link-azure-hdinsight"></a>Koppeling: Azure HDInsight

U kunt een normaal cluster koppelen met behulp van een door [Apache Ambari](https://ambari.apache.org/)beheerde gebruikers naam, of u kunt een beveiligd Hadoop-cluster van het ondernemings beveiligings pakket koppelen met behulp van een domein gebruikers naam (zoals: `user1@contoso.com` ).

1. Navigeer in de menu balk naar het **weer geven** van het  >  **opdracht palet...**, en voer **Spark/Hive: koppelen aan een cluster**.

   ![Opdracht palet koppeling cluster opdracht](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Selecteer het gekoppelde cluster type **Azure HDInsight**.

3. Voer de URL van het HDInsight-cluster in.

4. Voer uw Ambari-gebruikers naam in; de standaard instelling is **admin**.

5. Voer uw Ambari-wacht woord in.

6. Selecteer het cluster type.

7. Stel de weergave naam van het cluster in (optioneel).

8. Bekijk de **uitvoer** weergave voor verificatie.

   > [!NOTE]  
   > De gekoppelde gebruikers naam en het bijbehorende wacht woord worden gebruikt als het cluster beide zijn aangemeld bij het Azure-abonnement en een cluster heeft gekoppeld.  

### <a name="link-generic-livy-endpoint"></a>Koppeling: algemeen livy-eind punt

1. Navigeer in de menu balk naar het **weer geven** van het  >  **opdracht palet...**, en voer **Spark/Hive: koppelen aan een cluster**.

2. Selecteer het type van het **algemene livy-eind punt** voor het gekoppelde cluster.

3. Voer het algemene livy-eind punt in. Bijvoorbeeld: http \: //10.172.41.42:18080.

4. Selecteer autorisatie type **Basic** of **none**.  Als u **Basic** selecteert:  
   
   1. Voer uw Ambari-gebruikers naam in; de standaard instelling is **admin**.  

   2. Voer uw Ambari-wacht woord in.

5. Bekijk de **uitvoer** weergave voor verificatie.

## <a name="list-clusters"></a>Clusters weergeven

1. Navigeer in de menu balk naar opdracht palet **weer geven**  >  **...** en voer **Spark/Hive: List cluster** in.

2. Selecteer het gewenste abonnement.

3. Bekijk de **uitvoer** weergave. In deze weer gave worden uw gekoppelde cluster (of clusters) en alle clusters onder uw Azure-abonnement weer gegeven:

   ![Een standaard cluster configuratie instellen](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="set-the-default-cluster"></a>Het standaard cluster instellen

1. Open de **HDexample** -map die [eerder](#open-a-work-folder)werd beschreven, indien deze is gesloten.  

2. Selecteer het bestand **HelloWorld. HQL** dat u [eerder](#open-a-work-folder)hebt gemaakt. Dit wordt geopend in de scripteditor.

3. Klik met de rechter muisknop op de script editor en selecteer **Spark/Hive: standaard cluster instellen**.  

4. [Maak verbinding](#connect-to-an-azure-account) met uw Azure-account of koppel een cluster als u dit nog niet hebt gedaan.

5. Selecteer een cluster als het standaard cluster voor het huidige script bestand. Het configuratiebestand **.VSCode\settings.json** wordt automatisch bijgewerkt:

   ![Standaardclusterconfiguratie instellen](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="submit-interactive-hive-queries-and-hive-batch-scripts"></a>Interactieve Hive-query's en Hive batch scripts verzenden

Met Spark & Hive-Hulpprogram Ma's voor Visual Studio code kunt u interactieve Hive-query's en Hive-batch scripts verzenden naar uw clusters.

1. Open de **HDexample** -map die [eerder](#open-a-work-folder)werd beschreven, indien deze is gesloten.  

2. Selecteer het bestand **HelloWorld. HQL** dat u [eerder](#open-a-work-folder)hebt gemaakt. Dit wordt geopend in de scripteditor.

3. Kopieer en plak de volgende code in het Hive-bestand en sla het op:

   ```hiveql
   SELECT * FROM hivesampletable;
   ```

4. [Maak verbinding](#connect-to-an-azure-account) met uw Azure-account of koppel een cluster als u dit nog niet hebt gedaan.

5. Klik met de rechter muisknop op de script editor en selecteer **Hive: Interactive** om de query te verzenden, of gebruik de sneltoets CTRL + ALT + I.  Selecteer **Hive: batch** om het script in te dienen, of gebruik de sneltoets CTRL + ALT + H.  

6. Als u geen standaard cluster hebt opgegeven, selecteert u een cluster. Met de hulpprogram ma's kunt u ook een blok code verzenden in plaats van het hele script bestand met behulp van het context menu. Na enkele ogen blikken worden de query resultaten weer gegeven op een nieuw tabblad:

   ![Resultaat van interactieve Apache Hive query](./media/hdinsight-for-vscode/interactive-hive-result.png)

   - **Resultaten** paneel: u kunt het hele resultaat opslaan als een CSV-, JSON-of Excel-bestand naar een lokaal pad of door alleen meerdere regels te selecteren.

   - **Berichten** paneel: wanneer u een **regel** nummer selecteert, springt dit naar de eerste regel van het script dat wordt uitgevoerd.

## <a name="submit-interactive-pyspark-queries"></a>Interactieve PySpark-query's verzenden

Gebruikers kunnen PySpark interactief uitvoeren op de volgende manieren:

### <a name="using-the-pyspark-interactive-command-in-py-file"></a>De PySpark Interactive opdracht in PY File gebruiken
Voer de volgende stappen uit om de query's te verzenden met de interactieve opdracht van PySpark:

1. Open de **HDexample** -map die [eerder](#open-a-work-folder)werd beschreven, indien deze is gesloten.  

2. Maak een nieuw **HelloWorld.py**-bestand en volg de [eerdere](#open-a-work-folder) stappen.

3. Kopieer en plak de volgende code in het scriptbestand:

   ```python
   from operator import add
   lines = spark.read.text("/HdiSamples/HdiSamples/FoodInspectionData/README").rdd.map(lambda r: r[0])
   counters = lines.flatMap(lambda x: x.split(' ')) \
                .map(lambda x: (x, 1)) \
                .reduceByKey(add)

   coll = counters.collect()
   sortedCollection = sorted(coll, key = lambda r: r[1], reverse = True)

   for i in range(0, 5):
        print(sortedCollection[i])
   ```

4. De prompt voor het installeren van PySpark/Synapse Pyspark-kernel wordt rechtsonder in het venster weergegeven. U kunt klikken op de knop **Installeren** om door te gaan met de installaties van PySpark/Synapse Pyspark. U kunt ook op de knop **Overslaan** klikken om deze stap over te slaan.

   ![Scherm afbeelding toont een optie om de installatie van PySpark over te slaan.](./media/hdinsight-for-vscode/install-the-pyspark-kernel.png)

5. Als u het later opnieuw moet installeren, kunt u naar de instellingen van de **Bestands**  >  **voorkeur** navigeren  >  en vervolgens **HDInsight uitschakelen: Schakel overs Laan Pyspark-installatie** in de instellingen in. 
    
    ![Scherm afbeelding toont de optie om de installatie overs Laan Pyspark in te scha kelen.](./media/hdinsight-for-vscode/enable-skip-pyspark-installation.png)

6. Als de installatie is geslaagd in stap 4, wordt het bericht venster ' PySpark geïnstalleerd ' weer gegeven in de rechter benedenhoek van het venster. Klik op de knop **Opnieuw laden** om het venster opnieuw te laden.

   ![pyspark correct geïnstalleerd](./media/hdinsight-for-vscode/pyspark-kernel-installed-successfully.png)


7. Ga op de menubalk naar **Beeld** > **Opdrachtpalet...** of gebruik de sneltoets **Shift + Ctrl + P** en voer **Python: selecteer Interpreter om Jupyter-server te starten** in.

   ![selecteer interpreter om Jupyter-server te starten](./media/hdinsight-for-vscode/select-interpreter-to-start-jupyter-server.png)

8. Selecteer hieronder de Python-optie.

   ![kies de onderstaande optie](./media/hdinsight-for-vscode/choose-the-below-option.png)
    
9. Ga op de menubalk naar **Beeld** > **Opdrachtpalet...** of gebruik de sneltoets **Shift + Ctrl + P** en voer **Developer: Venster opnieuw laden** in.

    ![venster opnieuw laden](./media/hdinsight-for-vscode/reload-window.png)

10. [Maak verbinding](#connect-to-an-azure-account) met uw Azure-account of koppel een cluster als u dit nog niet hebt gedaan.

11. Selecteer alle code, klik met de rechter muisknop op de script editor en selecteer **Spark: PySpark Interactive/Synapse: PySpark Interactive** om de query te verzenden. 

    ![contextmenu van pyspark interactive](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

12. Selecteer het cluster als u geen standaard cluster hebt opgegeven. Na enkele ogen blikken worden de **python-interactieve** resultaten weer gegeven op een nieuw tabblad. Klik op PySpark om de kernel over te scha kelen naar **PySpark/Synapse PySpark** en de code wordt uitgevoerd. Als u wilt overschakelen naar Synapse Pyspark kernel, wordt het uitschakelen van automatische instellingen in Azure Portal aangemoedigd. Anders kan het lang duren voordat het cluster wordt geactiveerd en de Synapse-kernel wordt ingesteld voor het eerste gebruik. Als u met de hulpprogram ma's ook een blok code in plaats van het hele script bestand kunt verzenden met behulp van het snelmenu:

    ![interactief pyspark Interactive python-venster](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png)

13. Voer **%% info** in en druk op SHIFT + ENTER om de taak gegevens weer te geven (optioneel):

    ![pyspark interactieve weergave taak gegevens](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

Het hulp programma biedt ook ondersteuning voor de **Spark SQL** -query:

  ![resultaat van interactieve weer gave pyspark](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)


### <a name="perform-interactive-query-in-py-file-using-a--comment"></a>Interactieve query's uitvoeren in een PY-bestand met behulp van een #%%-opmerking

1. Voeg **#%%** vóór de py-code toe om de notitieblok ervaring op te halen.

   ![#%% toevoegen](./media/hdinsight-for-vscode/run-cell.png)

2. Klik op **Cel uitvoeren**. Na enkele ogen blikken worden de python-interactieve resultaten weer gegeven op een nieuw tabblad. Klik op PySpark om de kernel over te scha kelen naar PySpark/Synapse PySpark, klik vervolgens nogmaals op **cel uitvoeren** en de code wordt uitgevoerd.

   ![celresultaten uitvoeren](./media/hdinsight-for-vscode/run-cell-get-results.png)

## <a name="leverage-ipynb-support-from-python-extension"></a>IPYNB-ondersteuning van Python-extensie gebruiken

1. U kunt een Jupyter Notebook maken met het opdrachtpalet of door een nieuw IPYNB-bestand te maken in uw werkruimte. Zie [Werken met Jupyter Notebooks in Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support) voor meer informatie

2. Klik op de knop **Cel uitvoeren** en volg de aanwijzingen om **de standaard-Spark-pool in te stellen** (het is aan te raden om elke keer een standaardcluster/-pool in te stellen voordat u een notebook opent). U kunt nu het venster **Opnieuw laden**.

   ![de standaard-Spark-pool instellen en opnieuw laden](./media/hdinsight-for-vscode/set-the-default-spark-pool-and-reload.png)

3. Klik op PySpark om de kernel over te scha kelen naar **PySpark/Synapse PySpark** en klik na een tijdje op de **cel run**. Daarna wordt het resultaat weer gegeven.

   ![ipynb-resultaten uitvoeren](./media/hdinsight-for-vscode/run-ipynb-file-results.png)


> [!NOTE]
>
> [' MS-python >= 2020.5.78807-versie wordt niet ondersteund voor deze uitbrei ding '](#issues-changed) is opgelost. Werk de **MS-python** nu bij naar de **nieuwste versie** .

## <a name="submit-pyspark-batch-job"></a>Batch taak PySpark verzenden

1. Open de **HDexample** -map die u [eerder](#open-a-work-folder)hebt besproken als deze is gesloten.  

2. Maak een nieuw **BatchFile.py**-bestand en volg de [eerdere](#open-a-work-folder) stappen.

3. Kopieer en plak de volgende code in het scriptbestand:

   ```python
   from __future__ import print_function
   import sys
   from operator import add
   from pyspark.sql import SparkSession
   if __name__ == "__main__":
       spark = SparkSession\
           .builder\
           .appName("PythonWordCount")\
           .getOrCreate()
   
       lines = spark.read.text('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv').rdd.map(lambda r: r[0])
       counts = lines.flatMap(lambda x: x.split(' '))\
                  .map(lambda x: (x, 1))\
                   .reduceByKey(add)
       output = counts.collect()
       for (word, count) in output:
           print("%s: %i" % (word, count))
       spark.stop()
   ```

4. [Maak verbinding](#connect-to-an-azure-account) met uw Azure-account of koppel een cluster als u dit nog niet hebt gedaan.

5. Klik met de rechter muisknop op de script editor en selecteer **Spark: PySpark batch** of * * Synapse: PySpark batch * * *.

6. Selecteer een cluster/Spark-groep om uw PySpark-taak in te dienen voor:

   ![Uitvoer van de Python-taakresultaten verzenden](./media/hdinsight-for-vscode/submit-pythonjob-result.png)

Nadat u een python-taak hebt verzonden, worden de inzendings logboeken weer gegeven in het **uitvoer** venster van Visual Studio code. De url's van de Spark-gebruikers interface en de URL van de garen-interface worden ook weer gegeven. Als u de batch-taak naar een Apache Spark pool verzendt, worden ook de URL van de gebruikers interface voor Spark-geschiedenis en de URL van de Spark-taak toepassing weer gegeven. U kunt de URL openen in een webbrowser om de taakstatus bij te houden.

## <a name="integrate-with-hdinsight-identity-broker-hib"></a>Integreren met HDInsight Identity Broker (HIB)

### <a name="connect-to-your-hdinsight-esp-cluster-with-id-broker-hib"></a>Verbinding maken met uw HDInsight ESP-cluster met ID Broker (HIB)

U kunt de gebruikelijke stappen volgen om u aan te melden bij het Azure-abonnement om verbinding te maken met uw HDInsight ESP-cluster met ID Broker (HIB). Nadat u zich hebt aangemeld, ziet u de cluster lijst in azure Verkenner. Zie [verbinding maken met uw HDInsight-cluster](#connect-to-an-azure-account)voor meer instructies.

### <a name="run-a-hivepyspark-job-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>Een Hive/PySpark-taak uitvoeren op een HDInsight ESP-cluster met ID Broker (HIB)

Voor het uitvoeren van een Hive-taak kunt u de normale stappen volgen voor het verzenden van een taak naar het HDInsight ESP-cluster met ID Broker (HIB). Raadpleeg [interactieve Hive-query's en Hive batch scripts indienen](#submit-interactive-hive-queries-and-hive-batch-scripts) voor meer instructies.

Voor het uitvoeren van een interactieve PySpark-taak kunt u de normale stappen volgen voor het verzenden van een taak naar het HDInsight ESP-cluster met ID Broker (HIB). Raadpleeg [interactieve PySpark-Query's verzenden](#submit-interactive-pyspark-queries) voor meer instructies.

Voor het uitvoeren van een PySpark batch-taak kunt u de normale stappen volgen om de taak te verzenden naar het HDInsight ESP-cluster met ID Broker (HIB). Raadpleeg de [batch verwerking PySpark verzenden](#submit-pyspark-batch-job) voor meer instructies.

## <a name="apache-livy-configuration"></a>Apache livy-configuratie

De [Apache livy](https://livy.incubator.apache.org/) -configuratie wordt ondersteund. U kunt deze configureren in de **.VSCode\settings.jsvoor** het bestand in de map met de werk ruimte. Momenteel biedt livy-configuratie alleen ondersteuning voor python-script. Zie [LIVY README](https://github.com/cloudera/livy/blob/master/README.rst )(Engelstalig) voor meer informatie.

<a id="triggerlivyconf"></a>**Livy-configuratie activeren**

### <a name="method-1"></a>Methode 1  

1. Navigeer in de menu balk naar instellingen voor **Bestands**  >  **Voorkeuren**  >  .
2. Voer in het vak **Zoek instellingen** **HDInsight-taak inzending in: livy conf**.  
3. Selecteer **bewerken in settings.js** voor het relevante Zoek resultaat.

### <a name="method-2"></a>Methode 2

Een bestand verzenden en u ziet dat de `.vscode` map automatisch wordt toegevoegd aan de werkmap. U kunt de livy-configuratie bekijken door **.vscode\settings.jsaan** te selecteren.

- De project instellingen:

  ![Configuratie van HDInsight Apache livy](./media/hdinsight-for-vscode/hdi-apache-livy-config.png)

  >[!NOTE]
  >Stel de waarde en eenheid in voor de instellingen **driverMemory** en **executorMemory** . Bijvoorbeeld: 1g of 1024m.

- Ondersteunde livy-configuraties:

  **/Batches plaatsen**
  
  **Aanvraagbody**

  | naam | beschrijving | type |
  | --- | --- | --- |
  | file | Bestand met de toepassing die moet worden uitgevoerd | Pad (vereist) |
  | proxyUser | Gebruiker die moet worden geïmiteerd bij het uitvoeren van de taak | Tekenreeks |
  | className | Hoofd klasse java/Spark-toepassing | Tekenreeks |
  | argumenten | Opdracht regel argumenten voor de toepassing | Lijst met teken reeksen |
  | Pott | Potten die in deze sessie moeten worden gebruikt | Lijst met teken reeksen | 
  | pyFiles | Python-bestanden die moeten worden gebruikt in deze sessie | Lijst met teken reeksen |
  | bestanden | Bestanden die moeten worden gebruikt in deze sessie | Lijst met teken reeksen |
  | driverMemory | Hoeveelheid geheugen die moet worden gebruikt voor het stuur programma | Tekenreeks |
  | driverCores | Het aantal kernen dat moet worden gebruikt voor het stuur programma | Int |
  | executorMemory | Hoeveelheid geheugen die per bewerkings proces moet worden gebruikt | Tekenreeks |
  | executorCores | Aantal kernen dat voor elke uitvoerder moet worden gebruikt | Int |
  | numExecutors | Aantal actieve uitvoerendeers dat voor deze sessie wordt gestart | Int |
  | archieven | Archief archieven die moeten worden gebruikt in deze sessie | Lijst met teken reeksen |
  | wachtrij | De naam van de wachtrij met GARENs die moet worden ingediend| Tekenreeks |
  | naam | De naam van deze sessie | Tekenreeks |
  | belangen | Eigenschappen van Spark-configuratie | Toewijzing van sleutel = val |

  **Antwoord tekst** Het gemaakte batch-object.

  | naam | beschrijving | type |
  | --- | ---| --- |
  | Id | Sessie-id | Int |
  | appId | Toepassings-ID van deze sessie | Tekenreeks |
  | appInfo | Gedetailleerde toepassings informatie | Toewijzing van sleutel = val |
  | logboek | Logboek regels | Lijst met teken reeksen |
  | staat |Batch status | Tekenreeks |

  > [!NOTE]
  > De toegewezen livy-configuratie wordt weer gegeven in het deel venster uitvoer wanneer u het script verzendt.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Integreren met Azure HDInsight vanuit Explorer

U kunt de Hive-tabel in uw clusters rechtstreeks bekijken via de **Azure HDInsight** Explorer:

1. [Maak verbinding](#connect-to-an-azure-account) met uw Azure-account als u dit nog niet hebt gedaan.

2. Selecteer het pictogram van **Azure** in de kolom uiterst links.

3. Vouw in het linkerdeel venster **Azure: HDINSIGHT** uit. De beschik bare abonnementen en clusters worden weer gegeven.

4. Vouw het cluster uit om de meta gegevens database en het tabel schema van de Hive weer te geven.

5. Klik met de rechter muisknop op de Hive-tabel. Bijvoorbeeld: **hivesampletable**. Selecteer **voor beeld**.

   ![Component tabel van Spark & voor Visual Studio code-Preview](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png)

6. Het venster **preview-resultaten** wordt geopend:

   ![Het &-onderdeel Spark voor Visual Studio code-voorbeeld resultaten](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png)

- Paneel resultaten

   U kunt het hele resultaat opslaan als een CSV-, JSON-of Excel-bestand naar een lokaal pad, of u hoeft alleen maar meerdere regels te selecteren.

- BERICHTEN paneel

  1. Wanneer het aantal rijen in de tabel groter is dan 100, ziet u het volgende bericht: "de eerste 100 rijen worden weer gegeven voor de Hive-tabel."
  2. Wanneer het aantal rijen in de tabel kleiner is dan of gelijk is aan 100, ziet u het volgende bericht: "60 rijen worden weer gegeven voor Hive-tabel".
  3. Wanneer er geen inhoud in de tabel is, wordt het volgende bericht weer gegeven: " `0 rows are displayed for Hive table.` "

     >[!NOTE]
     >
     >Installeer xclip in Linux om Kopieer tabel gegevens in te scha kelen.
     >
     >![Spark-& Hive voor Visual Studio code in Linux](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png)

## <a name="additional-features"></a>Aanvullende functies

De Spark-& Hive voor Visual Studio code biedt ook ondersteuning voor de volgende functies:

- **Automatisch aanvullen IntelliSense**. Suggesties worden pop-ups gemaakt voor tref woorden, methoden, variabelen en andere programmeer elementen. Verschillende pictogrammen vertegenwoordigen verschillende typen objecten:

    ![Hulp middelen voor Spark &-Hive voor Visual Studio code IntelliSense-objecten](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)

- **Fout markering voor IntelliSense**. De taal service onderstreept het bewerken van fouten in het Hive-script.     
- **Syntaxis punten**. De taal service gebruikt verschillende kleuren om variabelen, tref woorden, gegevens typen, functies en andere programmeer elementen te onderscheiden:

    ![De belangrijkste aandachtspunten voor de Visual Studio-code voor het onderdeel Spark &](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="reader-only-role"></a>Alleen-lezen rol

Gebruikers aan wie de rol alleen lezer voor het cluster is toegewezen, kunnen geen taken verzenden naar het HDInsight-cluster, noch de Hive-Data Base weer geven. Neem contact op met de Cluster beheerder om uw rol bij te werken naar de [**HDInsight-cluster operator**](./hdinsight-migrate-granular-access-cluster-configurations.md#add-the-hdinsight-cluster-operator-role-assignment-to-a-user) in de [Azure Portal](https://portal.azure.com/). Als u geldige Ambari-referenties hebt, kunt u het cluster hand matig koppelen met behulp van de volgende richt lijnen.

### <a name="browse-the-hdinsight-cluster"></a>Bladeren in het HDInsight-cluster  

Wanneer u de Azure HDInsight Explorer selecteert om een HDInsight-cluster uit te breiden, wordt u gevraagd het cluster te koppelen als u de rol alleen-lezen hebt voor het cluster. Gebruik de volgende methode om een koppeling naar het cluster te maken met behulp van uw Ambari-referenties.

### <a name="submit-the-job-to-the-hdinsight-cluster"></a>De taak verzenden naar het HDInsight-cluster

Bij het verzenden van een taak naar een HDInsight-cluster wordt u gevraagd het cluster te koppelen als u de rol alleen-lezen voor het cluster gebruikt. Gebruik de volgende stappen om een koppeling naar het cluster te maken met behulp van Ambari-referenties.

### <a name="link-to-the-cluster"></a>Koppeling naar het cluster

1. Geef een geldige Ambari-gebruikers naam op.
2. Voer een geldig wacht woord in.

   ![De gebruikers naam van de & voor de Visual Studio-code van Spark-Hulpprogram Ma's](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png)

   ![Onderdeel Hulpprogramma's van Spark & voor Visual Studio code-wacht woord](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png)

   > [!NOTE]
   >
   >U kunt gebruiken `Spark / Hive: List Cluster` om het gekoppelde cluster te controleren:
   >
   >![Koppeling naar het onderdeel Hulpprogramma's van Spark & voor Visual Studio code Reader](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="browse-a-data-lake-storage-gen2-account"></a>Bladeren in een Data Lake Storage Gen2-account

Selecteer de Azure HDInsight Explorer om een Data Lake Storage Gen2 account uit te vouwen. U wordt gevraagd om de toegangs sleutel voor opslag in te voeren als uw Azure-account geen toegang heeft tot Gen2-opslag. Nadat de toegangs sleutel is gevalideerd, wordt het Data Lake Storage Gen2-account automatisch uitgevouwen.

### <a name="submit-jobs-to-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>Taken verzenden naar een HDInsight-cluster met Data Lake Storage Gen2

Een taak verzenden naar een HDInsight-cluster met behulp van Data Lake Storage Gen2. U wordt gevraagd om de toegangs sleutel voor opslag in te voeren als uw Azure-account geen schrijf toegang heeft tot Gen2-opslag. Nadat de toegangs sleutel is gevalideerd, wordt de taak verzonden.

![Spark-& Hive-Hulpprogram Ma's voor Visual Studio code AccessKey](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)

> [!NOTE]
>
> U kunt de toegangs sleutel voor het opslag account ophalen van de Azure Portal. Zie [Toegangssleutels voor een opslagaccount beheren](../storage/common/storage-account-keys-manage.md) voor meer informatie.

## <a name="unlink-cluster"></a>Cluster ontkoppelen

1. Ga in de menu balk naar het   >  **opdracht palet** weer geven en voer **Spark/Hive: ontkoppelen van een cluster**.  

2. Selecteer een cluster om te ontkoppelen.  

3. Raadpleeg de **uitvoer** weergave voor verificatie.  

## <a name="sign-out"></a>Afmelden  

Ga in de menu balk naar het   >  **opdracht palet** weer geven en voer **Azure in: Meld** u aan.

## <a name="issues-changed"></a>Problemen gewijzigd

Voor dit probleem is ' MS-python >= 2020.5.78807-versie wordt niet ondersteund voor deze uitbrei ding ' is opgelost, moet u de **MS-python** nu bijwerken naar de **nieuwste versie** .


## <a name="next-steps"></a>Volgende stappen

Zie voor een video die demonstreert met behulp van Spark-& Hive voor Visual Studio code [spark & Hive voor Visual Studio code](https://go.microsoft.com/fwlink/?linkid=858706).
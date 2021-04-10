---
title: "Zelfstudie: Spark- en Hive-hulpprogramma's voor VSCode (Spark-toepassing)"
description: 'Zelfstudie: gebruik de Spark & Hive Tools voor VSCode om Spark-toepassingen, die in Python worden geschreven, te ontwikkelen en te verzenden naar een serverloze Apache Spark-pool.'
services: synapse-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 09/03/2020
ms.openlocfilehash: e5d335ee14709ec330405419f5be8ac5fbd6ce75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943769"
---
# <a name="tutorial-create-an-apache-spark-applications-with-vscode-using-a-synapse-workspace"></a>Zelfstudie: Een Apache Spark-toepassing met VSCode maken met behulp van een Synapse-werkruimte

Meer informatie over het gebruik van Apache Spark & Hive Tools voor Visual Studio Code. Gebruik de hulpprogramma's om Apache Hive-batchtaken, interactieve Hive-query's en PySpark-scripts te maken en te verzenden voor Apache Spark. Eerst wordt beschreven hoe u Spark & Hive Tools installeert in Visual Studio Code. Vervolgens wordt uitgelegd hoe u taken kunt verzenden naar Spark & Hive Tools.

Spark & Hive Tools kan worden geïnstalleerd op platforms die worden ondersteund door Visual Studio Code. Houd rekening met de volgende vereisten voor verschillende platforms.

## <a name="prerequisites"></a>Vereisten

De volgende items zijn vereist voor het voltooien van de stappen in dit artikel:

- Een serverloze Apache Spark-pool. Zie [Apache Spark-pool maken met de Azure-portal](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md) om een serverloze Apache Spark-pool te maken.
- [Visual Studio Code](https://code.visualstudio.com/).
- [Mono](https://www.mono-project.com/docs/getting-started/install/). Mono is alleen vereist voor Linux en macOS.
- [Een interactieve PySpark-omgeving voor Visual Studio Code](../../hdinsight/set-up-pyspark-interactive-environment.md).
- Een lokale map. In dit artikel wordt **C:\HD\Synaseexample** gebruikt.

## <a name="install-spark--hive-tools"></a>Spark & Hive Tools installeren

Wanneer u aan de vereisten voldoet, kunt u Spark & Hive Tools voor Visual Studio Code installeren door de volgende stappen uit te voeren:

1. Open Visual Studio Code.

2. Navigeer op de menu balk naar **Beeld** > **Uitbreidingen**.

3. Voer in het zoekvak **Spark & Hive** in.

4. Selecteer **Spark & Hive Tools** in de zoekresultaten en selecteer vervolgens **Installeren**:

     ![Spark & Hive voor Visual Studio Code Python installeren](./media/vscode-tool-synapse/install-hdInsight-plugin.png)

5. Selecteer zo nodig **Opnieuw laden**.

## <a name="open-a-work-folder"></a>Een werkmap openen

Voer de volgende stappen uit om een werkmap te openen en een bestand te maken in Visual Studio Code:

1. Navigeer op de menubalk naar **Bestand** > **Map openen...**  > **C:\HD\Synaseexample** en selecteer de knop **Map selecteren**. De map wordt weergegeven in de weergave **Explorer** aan de linkerkant.

2. Selecteer in de weergave **Explorer** de map **Synaseexample** en selecteer het pictogram **Nieuw bestand** naast de werkmap:

     ![pictogram Nieuw bestand in Visual Studio Code](./media/vscode-tool-synapse/visual-studio-code-new-file.png)

3. Geef het nieuwe bestand een naam met de extensie `.py` (Spark-script). In dit voorbeeld wordt **HelloWorld.py** gebruikt.

## <a name="connect-to-your-spark-pools"></a>Verbinding maken met uw Spark-pools

Meld u aan bij uw Azure-abonnement om verbinding te maken met uw Spark-pools.

### <a name="sign-in-to-your-azure-subscription"></a>Aanmelden bij uw Azure-abonnement

Volg deze stappen om verbinding te maken met Azure:

1. Ga op de menubalk naar **Beeld** > **Opdrachtpalet...** en voer **Azure: Aanmelden** in:

     ![aanmelding bij Spark & Hive Tools voor Visual Studio Code](./media/vscode-tool-synapse/hdinsight-for-vscode-extension-login.png)

2. Volg de instructies om u aan te melden bij Azure. Nadat u verbinding hebt gemaakt, ziet u de naam van uw Azure-account op de statusbalk onder in het Visual Studio Code-venster.

## <a name="set-the-default-spark-pool"></a>De standaard-Spark-pool instellen

1. Open de map **Synaseexample** opnieuw die [eerder](#open-a-work-folder) is besproken, als deze is gesloten.  

2. Selecteer het bestand **HelloWorld.py** dat [eerder](#open-a-work-folder) is gemaakt. Dit wordt geopend in de scripteditor.

3. Klik met de rechtermuisknop op de scripteditor en selecteer **Synapse: Standaard-Spark-pool instellen**.  

4. [Maak verbinding](#connect-to-your-spark-pools) met uw Azure-account als u dit nog niet hebt gedaan.

5. Selecteer de standaard-Spark-pool voor het huidige scriptbestand. Het configuratiebestand **.VSCode\settings.json** wordt automatisch bijgewerkt:

     ![Standaardclusterconfiguratie instellen](./media/vscode-tool-synapse/set-default-cluster-configuration.png)

## <a name="submit-interactive-synapse-pyspark-queries-to-spark-pool"></a>Interactieve Synapse PySpark-query's verzenden naar Spark-pool

Gebruikers kunnen op de volgende manieren Synapse PySpark interactief uitvoeren voor de Spark-pool:

### <a name="using-the-synapse-pyspark-interactive-command-in-py-file"></a>De interactieve Synapse PySpark-opdracht gebruiken in het PY-bestand
Voer de volgende stappen uit om de query's te verzenden met de interactieve opdracht van PySpark:

1. Open de map **Synaseexample** opnieuw die [eerder](#open-a-work-folder) is besproken, als deze is gesloten.  

2. Maak een nieuw **HelloWorld.py**-bestand en volg de [eerdere](#open-a-work-folder) stappen.

3. Kopieer en plak de volgende code in het scriptbestand:

```python
import sys
from operator import add
from pyspark.sql import SparkSession, Row
 
spark = SparkSession\
 .builder\
 .appName("PythonWordCount")\
 .getOrCreate()
 
data = [Row(col1='pyspark and spark', col2=1), Row(col1='pyspark', col2=2), Row(col1='spark vs hadoop', col2=2), Row(col1='spark', col2=2), Row(col1='hadoop', col2=2)]
df = spark.createDataFrame(data)
lines = df.rdd.map(lambda r: r[0])
 
counters = lines.flatMap(lambda x: x.split(' ')) \
 .map(lambda x: (x, 1)) \
 .reduceByKey(add)
 
output = counters.collect()
sortedCollection = sorted(output, key = lambda r: r[1], reverse = True)
 
for (word, count) in sortedCollection:
 print("%s: %i" % (word, count))
```

4. De prompt voor het installeren van PySpark/Synapse Pyspark-kernel wordt rechtsonder in het venster weergegeven. U kunt klikken op de knop **Installeren** om door te gaan met de installaties van PySpark/Synapse Pyspark. U kunt ook op de knop **Overslaan** klikken om deze stap over te slaan.

     ![pyspark-kernel installeren](./media/vscode-tool-synapse/install-the-pyspark-kernel.png)

5. Als u deze later wilt installeren, gaat u naar **Bestand** > **Voorkeur** > **Instellingen** en schakelt u **Hdinsight: Pyspark-installatie overslaan inschakelen** uit in de instellingen. 
    
     ![pyspark-installatie overslaan inschakelen](./media/vscode-tool-synapse/enable-skip-pyspark-installation.png)

6. Als de installatie is voltooid in stap 4, wordt het berichtvenster 'PySpark/Synapse Pyspark correct geïnstalleerd' rechtsonder in het venster weergegeven. Klik op de knop **Opnieuw laden** om het venster opnieuw te laden.

     ![pyspark correct geïnstalleerd](./media/vscode-tool-synapse/pyspark-kernel-installed-successfully.png)

7. Ga op de menubalk naar **Beeld** > **Opdrachtpalet...** of gebruik de sneltoets **Shift + Ctrl + P** en voer **Python: selecteer Interpreter om Jupyter-server te starten** in.

     ![selecteer interpreter om Jupyter-server te starten](./media/vscode-tool-synapse/select-interpreter-to-start-jupyter-server.png)

8. Selecteer hieronder de Python-optie.

     ![kies de onderstaande optie](./media/vscode-tool-synapse/choose-the-below-option.png)
    
9. Ga op de menubalk naar **Beeld** > **Opdrachtpalet...** of gebruik de sneltoets **Shift + Ctrl + P** en voer **Developer: Venster opnieuw laden** in.

     ![venster opnieuw laden](./media/vscode-tool-synapse/reload-window.png)

10. [Maak verbinding](#connect-to-your-spark-pools) met uw Azure-account als u dit nog niet hebt gedaan.

11. Selecteer alle code, klik met de rechtermuisknop op de scripteditor en selecteer **Synapse: Pyspark Interactive** om de query te verzenden. 

     ![contextmenu van pyspark interactive](./media/vscode-tool-synapse/pyspark-interactive-right-click.png)

12. Selecteer de Spark-pool als u geen standaard-Spark-pool hebt opgegeven. Na enkele ogenblikken worden de resultaten van **Python Interactive** weergegeven op een nieuw tabblad. Klik op PySpark om de kernel om te zetten naar **Synapse PySpark** en verzend de geselecteerde code opnieuw. De code wordt nu correct uitgevoerd. Met de hulpprogramma’s kunt u ook een codeblok verzenden in plaats van het hele scriptbestand, met behulp van het contextmenu:

     ![interactief](./media/vscode-tool-synapse/pyspark-interactive-python-interactive-window.png)

### <a name="perform-interactive-query-in-py-file-using-a--comment"></a>Interactieve query's uitvoeren in een PY-bestand met behulp van een #%%-opmerking

1. Voeg **#%%** toe vóór de code om de notebook-ervaring op te halen.

     ![#%% toevoegen](./media/vscode-tool-synapse/run-cell.png)

2. Klik op **Cel uitvoeren**. Na enkele ogenblikken worden de resultaten van Python Interactive weergegeven op een nieuw tabblad. Klik op PySpark om de kernel om te zetten naar **Synapse PySpark** en klik opnieuw op **Cel uitvoeren**. De code wordt nu correct uitgevoerd. 

     ![celresultaten uitvoeren](./media/vscode-tool-synapse/run-cell-get-results.png)

## <a name="leverage-ipynb-support-from-python-extension"></a>IPYNB-ondersteuning van Python-extensie gebruiken

1. U kunt een Jupyter Notebook maken met het opdrachtpalet of door een nieuw IPYNB-bestand te maken in uw werkruimte. Zie [Werken met Jupyter Notebooks in Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support) voor meer informatie

2. Klik op de knop **Cel uitvoeren** en volg de aanwijzingen om **de standaard-Spark-pool in te stellen** (het is aan te raden om elke keer een standaardcluster/-pool in te stellen voordat u een notebook opent). U kunt nu het venster **Opnieuw laden**.

     ![de standaard-Spark-pool instellen en opnieuw laden](./media/vscode-tool-synapse/set-the-default-spark-pool-and-reload.png)

3. Klik op PySpark om de kernel om te zetten naar **Synapse Pyspark** en klik op **Cel uitvoeren**. Na enige tijd wordt het resultaat weergegeven.

     ![ipynb-resultaten uitvoeren](./media/vscode-tool-synapse/run-ipynb-file-results.png)


> [!NOTE]
>
>1. De versie Ms-python >=2020.5.78807 wordt niet ondersteund voor deze uitbreiding. Dit is een [bekend probleem](#known-issues).
>  
>2. Schakel over naar Synapse Pyspark-kernel. U wordt aangeraden de automatische instellingen uit te schakelen in de Azure-portal. Anders kan het lang duren voordat het cluster wordt geactiveerd en de Synapse-kernel wordt ingesteld voor het eerste gebruik. 
>
>    ![automatische instellingen](./media/vscode-tool-synapse/auto-settings.png)

## <a name="submit-pyspark-batch-job-to-spark-pool"></a>PySpark-batchtaak verzenden naar Spark-pool

1. Open de map **Synaseexample** opnieuw die [eerder](#open-a-work-folder) is besproken, als deze is gesloten.  

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

4. [Maak verbinding](#connect-to-your-spark-pools) met uw Azure-account als u dit nog niet hebt gedaan.

5. Klik met de rechtermuisknop op de scripteditor en selecteer **Synapse: PySpark-batch**.

6. Selecteer een Spark-pool waarnaar u uw PySpark-taak wilt verzenden:

     ![Uitvoer van de Python-taakresultaten verzenden](./media/vscode-tool-synapse/submit-pythonjob-result.png)

Nadat u een batchtaak naar de Spark-pool hebt verzonden, worden de verzendingslogboeken weergegeven in het venster **UITVOER** in Visual Studio Code. De URL van de **Spark-gebruikersinterface** en de URL van de **gebruikersinterface van de Spark-taaktoepassing** worden ook weergegeven. U kunt de URL openen in een webbrowser om de taakstatus bij te houden.

## <a name="access-and-manage-synapse-workspace"></a>Synapse-werkruimte openen en beheren

U kunt verschillende bewerkingen uitvoeren in Azure Explorer in Spark & Hive Tools voor VSCode. Vanuit de Azure Explorer.

![afbeelding van Azure](./media/vscode-tool-synapse/azure.png)

### <a name="launch-workspace"></a>Werkruimte starten

1. Ga in Azure Explorer naar **SYNAPSE**, vouw dit uit en geef de lijst Synapse-abonnementen weer.

     ![synapse explorer](./media/vscode-tool-synapse/synapse-explorer.png)

2. Klik op Abonnement van Synapse-werkruimte, vouw deze uit en geef de lijst met werkruimten weer.

3. Klik met de rechtermuisknop op een werkruimte en selecteer **Apache Spark-toepassingen weergeven**. De pagina Apache Spark-toepassing wordt nu geopend op de website van Synapse Studio.

     ![met de rechtermuisknop op een werkruimte klikken](./media/vscode-tool-synapse/right-click-on-workspace.png)

     ![synapse studio-toepassing](./media/vscode-tool-synapse/synapse-studio-application.png)

4. Vouw een werkruimte uit om **Standaardopslag** en **Spark-pools** weer te geven.

5. Klik met de rechtermuisknop op **Standaardopslag** om **Volledig pad kopiëren** en **Openen in Synapse Studio** weer te geven. 

     ![met de rechtermuisknop op standaardopslag klikken](./media/vscode-tool-synapse/right-click-on-default-storage.png)

     - Klik op **Volledig pad kopiëren**. De URL van het primaire ADLS Gen2-account wordt gekopieerd en u kunt deze plakken waar u wilt。

     - Klik op **Openen in Synapse Studio** om het primaire opslagaccount te openen in Synapse Studio.

     ![standaardopslag in synapse studio](./media/vscode-tool-synapse/default-storage-in-synapse-studio.png)

6. Vouw **Standaardopslag** uit om het primaire opslagaccount weer te geven.

7. Vouw **Spark-pools** uit om alle Spark-pools in de werkruimte weer te geven.

     ![opslaggroep uitvouwen](./media/vscode-tool-synapse/expand-storage-pool.png)


## <a name="known-issues"></a>Bekende problemen

### <a name="ms-python-2020578807-version-is-not-supported-on-this-extention"></a>De versie ms-python >=2020.5.78807 wordt niet ondersteund voor deze uitbreiding 

'Kan geen verbinding maken met Jupyter notebook.' is een bekend probleem voor Python-versie >=2020.5.78807. Gebruikers wordt aangeraden de versie **[2020.4.76186](https://github.com/microsoft/vscode-python/releases/download/2020.4.76186/ms-python-release.vsix)** van MS-python te gebruiken om dit probleem te voorkomen.

![bekende problemen](./media/vscode-tool-synapse/known-issue.png)


## <a name="next-steps"></a>Volgende stappen

- [Azure Synapse Analytics](../overview-what-is.md)
- [Een nieuwe Apache Spark-pool maken voor een Azure Synapse Analytics-werkruimte](../../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)

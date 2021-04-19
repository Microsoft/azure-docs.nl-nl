---
title: Synapse Studio notebooks
description: In dit artikel leert u hoe u een Azure Synapse Studio-notebooks maakt en ontwikkelt voor het voorbereiden en visualiseren van gegevens.
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 10/19/2020
ms.author: ruxu
ms.reviewer: ''
ms.custom: devx-track-python
ms.openlocfilehash: 4230ced172de52e5acf45e071fa2a49a332eb696
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719219"
---
# <a name="create-develop-and-maintain-synapse-studio-notebooks-in-azure-synapse-analytics"></a>Notebooks maken, ontwikkelen Synapse Studio onderhouden in Azure Synapse Analytics

Een Synapse Studio notebook is een webinterface voor het maken van bestanden die livecode, visualisaties en verhalende tekst bevatten. Notebooks zijn een goede plaats om ideeën te valideren en snelle experimenten te gebruiken om inzichten uit uw gegevens te verkrijgen. Notebooks worden ook veel gebruikt voor gegevensvoorbereiding, gegevensvisualisatie, machine learning en andere big data-scenario's.

Met een Azure Synapse Studio-notebook kunt u het volgende doen:

* Aan de slag met nul installatie-inspanningen.
* Gegevens beveiligen met ingebouwde beveiligingsfuncties voor ondernemingen.
* Analyseer gegevens in onbewerkte indelingen (CSV, txt, JSON, enzovoort), verwerkte bestandsindelingen (parquet, Delta Lake, ORC, enzovoort) en SQL-gegevensbestanden in tabelvorm voor Spark en SQL.
* Wees productief met verbeterde ontwerpmogelijkheden en ingebouwde gegevensvisualisatie.

In dit artikel wordt beschreven hoe u notebooks gebruikt in Azure Synapse Studio.

## <a name="preview-of-the-new-notebook-experience"></a>Preview van de nieuwe notebookervaring
Het Synapse-team heeft het nieuwe notebookonderdeel in Synapse Studio gebracht om consistente notebookervaring te bieden voor Microsoft-klanten en de detecteerbaarheid, productiviteit, delen en samenwerking te maximaliseren. De nieuwe notebook-ervaring is gereed voor preview. Schakel de **knop Preview-functies** in de notebookwerkbalk in. In de onderstaande tabel wordt de functievergelijking tussen bestaande notebooks (ook wel 'klassieke notebook' genoemd) met de nieuwe preview-versie beschreven.  

|Functie|Klassieke notebook|Preview-notebook|
|--|--|--|
|%run| Niet ondersteund | &#9745;|
|%history| Niet ondersteund |&#9745;
|%load| Niet ondersteund |&#9745;|
|%%html| Niet ondersteund |&#9745;|
|Slepen en neerzetten om een cel te verplaatsen| Niet ondersteund |&#9745;|
|Tekstcel opmaken met werkbalkknoppen|&#9745;| Niet beschikbaar |
|Celbewerking ongedaan maken| &#9745;| Niet beschikbaar |


## <a name="create-a-notebook"></a>Een notebook maken

Er zijn twee manieren om een notebook te maken. U kunt een nieuw notebook maken of een bestaand notebook importeren in Azure Synapse werkruimte van **de Objectverkenner.** Azure Synapse Studio-notebooks kunnen standaard IPYNB Jupyter Notebook bestanden herkennen.

![notebook voor importeren maken](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook-2.png)

## <a name="develop-notebooks"></a>Notebooks ontwikkelen

Notebooks bestaan uit cellen. Dit zijn afzonderlijke codeblokken of tekstblokken die onafhankelijk of als groep kunnen worden uitvoeren.

### <a name="add-a-cell"></a>Een cel toevoegen

Er zijn meerdere manieren om een nieuwe cel aan uw notebook toe te voegen.

# <a name="classical-notebook"></a>[Klassieke notebook](#tab/classical)

1. Vouw de knop linksboven **+ Cel** uit en selecteer **Codecel toevoegen** of **Tekstcel toevoegen.**

    ![add-cell-with-cell-button](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. Beweeg de muisaanwijzer over de spatie tussen twee cellen en selecteer **Code toevoegen** of **Tekst toevoegen.**

    ![add-cell-between-space](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. Gebruik [Sneltoetsen onder de opdrachtmodus](#shortcut-keys-under-command-mode). Druk **op A** om een cel boven de huidige cel in te voegen. Druk **op B** om een cel onder de huidige cel in te voegen.


# <a name="preview-notebook"></a>[Preview-notebook](#tab/preview)

1. Vouw de knop linksboven **+ Cel** uit en selecteer **codecel** of **Markdown-cel**.
    ![add-azure-notebook-cell-with-cell-button](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-add-cell-1.png)
2. Selecteer het plusteken aan het begin van een cel en selecteer **Codecel** of **Markdown-cel**.

    ![add-azure-notebook-cell-between-space](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-add-cell-2.png)

3. Gebruik [aznb-sneltoetsen onder de opdrachtmodus](#shortcut-keys-under-command-mode). Druk **op A** om een cel boven de huidige cel in te voegen. Druk **op B** om een cel onder de huidige cel in te voegen.

---

### <a name="set-a-primary-language"></a>Een primaire taal instellen

Azure Synapse Studio-notebooks ondersteunen vier Apache Spark talen:

* pySpark (Python)
* Spark (Scala)
* SparkSQL
* .NET voor Apache Spark (C#)

U kunt de primaire taal voor nieuwe toegevoegde cellen instellen in de vervolgkeuzelijst in de bovenste opdrachtbalk.

   ![default-synapse-language](./media/apache-spark-development-using-notebooks/synapse-default-language.png)

### <a name="use-multiple-languages"></a>Meerdere talen gebruiken

U kunt meerdere talen in één notebook gebruiken door de juiste magic-opdracht voor de taal aan het begin van een cel op te geven. De volgende tabel bevat de magic-opdrachten voor het schakelen tussen celtalen.

|Magic-opdracht |Taal | Description |  
|---|------|-----|
|%%pyspark| Python | Voer een **Python-query** uit op Spark-context.  |
|%%spark| Scala | Voer een **Scala-query** uit op Spark-context.  |  
|%%sql| SparkSQL | Voer een **SparkSQL-query** uit op Spark-context.  |
|%%csharp | .NET voor Spark C # | Voer een **.NET voor Spark C#-query** uit op Spark-context. |

De volgende afbeelding is een voorbeeld van hoe u een PySpark-query kunt schrijven met behulp van de **magic-opdracht %%pyspark** of een SparkSQL-query met de **magic-opdracht %%sql** in een **Spark(Scala)-notebook.** U ziet dat de primaire taal voor het notebook is ingesteld op pySpark.

   ![Magic-opdrachten voor Synapse Spark](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)

### <a name="use-temp-tables-to-reference-data-across-languages&quot;></a>Tijdelijke tabellen gebruiken om te verwijzen naar gegevens in verschillende talen

U kunt niet rechtstreeks verwijzen naar gegevens of variabelen in verschillende talen in een Synapse Studio notebook. In Spark kan naar een tijdelijke tabel in meerdere talen worden verwezen. Hier is een voorbeeld van het lezen van een DataFrame in en het gebruik van een `Scala` `PySpark` tijdelijke `SparkSQL` Spark-tabel als tijdelijke oplossing.

1. Lees in Cel 1 een DataFrame uit een SQL-poolconnector met behulp van Scala en maak een tijdelijke tabel.

   ```scala
   %%scala
   val scalaDataFrame = spark.read.sqlanalytics(&quot;mySQLPoolDatabase.dbo.mySQLPoolTable")
   scalaDataFrame.createOrReplaceTempView( "mydataframetable" )
   ```

2. In Cel 2 query's uitvoeren op de gegevens met behulp van Spark SQL.
   
   ```sql
   %%sql
   SELECT * FROM mydataframetable
   ```

3. Gebruik in Cel 3 de gegevens in PySpark.

   ```python
   %%pyspark
   myNewPythonDataFrame = spark.sql("SELECT * FROM mydataframetable")
   ```

### <a name="ide-style-intellisense"></a>IDE-stijl IntelliSense

Azure Synapse Studio-notebooks zijn geïntegreerd met de Editor van Monaco om IDE-stijl IntelliSense naar de celeditor te brengen. Syntaxismarkering, foutmarkering en automatische code-aanvullingen helpen u om sneller code te schrijven en problemen te identificeren.

De IntelliSense-functies zijn op verschillende volwassenheidsniveaus voor verschillende talen. Gebruik de volgende tabel om te zien wat er wordt ondersteund.

|Talen| Syntaxis markeren | Syntaxisfoutmarkering  | Voltooiing van syntaxiscode | Voltooiing van variabele code| Voltooiing van systeemfunctiecode| Voltooiing van gebruikersfunctiecode| Smart Indent | Code Folding|
|--|--|--|--|--|--|--|--|--|
|PySpark (Python)|Ja|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Spark (Scala)|Ja|Ja|Ja|Ja|-|-|-|Ja|
|SparkSQL|Ja|Ja|-|-|-|-|-|-|
|.NET voor Spark (C#)|Yes|-|-|-|-|-|-|-|

### <a name="format-text-cell-with-toolbar-buttons"></a>Tekstcel opmaken met werkbalkknoppen

# <a name="classical-notebook"></a>[Klassieke notebook](#tab/classical)

U kunt de opmaakknoppen in de werkbalk van de tekstcellen gebruiken om algemene Markdown-acties uit te voeren. Het omvat vetgedrukte tekst, het italiseren van tekst, het invoegen van codefragmenten, het invoegen van niet-geordende lijst, het invoegen van een geordende lijst en het invoegen van een afbeelding vanuit een URL.

  ![Werkbalk van Synapse-tekstcel](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

# <a name="preview-notebook"></a>[Preview-notebook](#tab/preview)

De werkbalk van de knop Opmaak is nog niet beschikbaar voor de preview-notebookervaring. 

---

### <a name="undo-cell-operations"></a>Celbewerkingen ongedaan maken

# <a name="classical-notebook"></a>[Klassieke notebook](#tab/classical)

Selecteer de **knop Ongedaan** maken of druk **op Ctrl+Z om** de meest recente celbewerking in te trekken. U kunt nu tot de laatste 20 historische celacties ongedaan maken. 

   ![Synapse-cellen ongedaan maken](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)
# <a name="preview-notebook"></a>[Preview-notebook](#tab/preview)

De bewerking Cel ongedaan maken is nog niet beschikbaar voor de preview-notebookervaring. 

---

### <a name="move-a-cell"></a>Een cel verplaatsen

# <a name="classical-notebook"></a>[Klassieke notebook](#tab/classical)

Selecteer het beletselmenu (...) voor toegang tot het menu extra celacties aan de rechterkant. Selecteer vervolgens **Cel omhoog verplaatsen of** Cel omlaag verplaatsen **om** de huidige cel te verplaatsen. 

U kunt ook [sneltoetsen gebruiken onder de opdrachtmodus](#shortcut-keys-under-command-mode). Druk **op Ctrl+Alt+"** om de huidige cel omhoog te verplaatsen. Druk **op Ctrl+Alt+ om** de huidige cel omlaag te verplaatsen.

   ![move-a-cell](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

# <a name="preview-notebook"></a>[Preview-notebook](#tab/preview)

Klik aan de linkerkant van een cel en sleep deze naar de gewenste positie. 
    ![Synapse-cellen verplaatsen](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-drag-drop-cell.gif)

---

### <a name="delete-a-cell"></a>Een cel verwijderen

# <a name="classical-notebook"></a>[Klassieke notebook](#tab/classical)

Als u een cel wilt verwijderen, selecteert u het beletselmenu (...) voor toegang tot het menu extra celacties helemaal rechts en selecteert u **vervolgens Cel verwijderen.** 

U kunt ook [sneltoetsen gebruiken onder de opdrachtmodus](#shortcut-keys-under-command-mode). Druk **op D,D** om de huidige cel te verwijderen.
  
   ![delete-a-cell](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

# <a name="preview-notebook"></a>[Preview-notebook](#tab/preview)

Als u een cel wilt verwijderen, selecteert u de knop Verwijderen aan de rechterkant van de cel. 

U kunt ook [sneltoetsen gebruiken onder de opdrachtmodus](#shortcut-keys-under-command-mode). Druk **op Shift+D om** de huidige cel te verwijderen. 

   ![azure-notebook-delete-a-cell](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-delete-cell.png)

---

### <a name="collapse-a-cell-input"></a>Een celinvoer samenvgevouwen

# <a name="classical-notebook"></a>[Klassieke notebook](#tab/classical)

Selecteer de pijlknop onder aan de huidige cel om deze samen te klappen. Selecteer de pijlknop terwijl de cel is samengevouwen om deze uit te vouwen.

   ![samenvgevouwen celinvoer](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

# <a name="preview-notebook"></a>[Preview-notebook](#tab/preview)

Selecteer het **beletselletsel (...)** meer opdrachten op de celwerkbalk en de **invoer om** de invoer van de huidige cel samen te klappen. Als u deze wilt uitbreiden, **selecteert u de invoer verborgen** terwijl de cel wordt samengevouwen.

   ![azure-notebook-collapse-cell-input](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-collapse-cell-input.gif)

---

### <a name="collapse-a-cell-output"></a>Uitvoer van een cel samenvgevouwen

# <a name="classical-notebook"></a>[Klassieke notebook](#tab/classical)

Selecteer **linksboven in** de uitvoer van de huidige cel de knop voor samenvgevouwen uitvoer om deze samen te klappen. Selecteer celuitvoer **tonen** terwijl de celuitvoer is samengevouwen om de uitvoer uit te vouwen.

   ![collapse-cell-output](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

# <a name="preview-notebook"></a>[Preview-notebook](#tab/preview)

Selecteer het **beletselletsel(...)** bij Meer opdrachten op de celwerkbalk en de uitvoer **om** de uitvoer van de huidige cel samen te klappen. Als u deze wilt uitbreiden, selecteert u dezelfde knop terwijl de uitvoer van de cel verborgen is.

   ![azure-notebook-collapse-cell-output](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-collapse-cell-output.gif)


---

## <a name="run-notebooks"></a>Notebooks gebruiken

U kunt de codecellen in uw notebook afzonderlijk of in één keer uitvoeren. De status en voortgang van elke cel worden weergegeven in het notebook.

### <a name="run-a-cell"></a>Een cel uitvoeren

Er zijn verschillende manieren om de code in een cel uit te voeren.

1. Beweeg de muisaanwijzer over de cel die u wilt uitvoeren en selecteer de knop **Cel** uitvoeren of druk **op Ctrl+Enter.**

   ![run-cell-1](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)
  
2. Gebruik [Sneltoetsen onder de opdrachtmodus](#shortcut-keys-under-command-mode). Druk **op Shift+Enter** om de huidige cel uit te voeren en selecteer de onderstaande cel. Druk **op Alt+Enter om** de huidige cel uit te voeren en een nieuwe cel hieronder in te voegen.

---

### <a name="run-all-cells"></a>Alle cellen uitvoeren
Selecteer de **knop Alles** uitvoeren om alle cellen in het huidige notebook opeenvolgend uit te voeren.

   ![run-all-cells](./media/apache-spark-development-using-notebooks/synapse-run-all.png)


### <a name="run-all-cells-above-or-below"></a>Alle cellen boven of onder uitvoeren

# <a name="classical-notebook"></a>[Klassieke notebook](#tab/classical)

Als u helemaal rechts toegang wilt krijgen tot het extra celactiesmenu, selecteert u het beletselletselmenu (**...**). Selecteer vervolgens **Cellen uitvoeren hierboven om** alle cellen boven de huidige opeenvolgend uit te voeren. Selecteer **Cellen uitvoeren hieronder** om alle cellen onder de huidige opeenvolgend uit te voeren.

   ![run-cells-above-or-below](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)

# <a name="preview-notebook"></a>[Preview-notebook](#tab/preview)

Vouw de vervolgkeuzelijst uit via **de** knop Alles uitvoeren en selecteer vervolgens Cellen **uitvoeren hierboven** om alle cellen boven de huidige opeenvolgend uit te voeren. Selecteer **Cellen uitvoeren hieronder** om alle cellen onder de huidige opeenvolgend uit te voeren.

   ![azure-notebook-run-cells-above-or-below](./media/apache-spark-development-using-notebooks/synapse-aznb-run-cells-above-or-below.png)

---

### <a name="cancel-all-running-cells"></a>Alle lopende cellen annuleren

# <a name="classical-notebook"></a>[Klassieke notebook](#tab/classical)
Selecteer de **knop Alles annuleren** om de cellen die in de wachtrij wachten, te annuleren. 
   ![cancel-all-cells](./media/apache-spark-development-using-notebooks/synapse-cancel-all.png) 

# <a name="preview-notebook"></a>[Preview-notebook](#tab/preview)

Selecteer de **knop Alles annuleren** om de cellen die in de wachtrij wachten, te annuleren. 
   ![azure-notebook-cancel-all-cells](./media/apache-spark-development-using-notebooks/synapse-aznb-cancel-all.png) 

---



### <a name="notebook-reference"></a>Notebookverwijzing

# <a name="classical-notebook"></a>[Klassieke notebook](#tab/classical)

Wordt niet ondersteund.

# <a name="preview-notebook"></a>[Preview-notebook](#tab/preview)

U kunt ```%run <notebook path>``` magic-opdracht gebruiken om te verwijzen naar een ander notebook binnen de context van het huidige notebook. Alle variabelen die in het referentienoteboek zijn gedefinieerd, zijn beschikbaar in het huidige notebook. ```%run``` Magic-opdracht ondersteunt geneste aanroepen, maar geen ondersteuning voor recursieve aanroepen. U ontvangt een uitzondering als de instructiediepte groter is dan vijf. ```%run``` De opdracht ondersteunt momenteel alleen het doorgeven van een notebookpad als parameter. 

Bijvoorbeeld: ``` %run /path/notebookA ```.

> [!NOTE]
> Notebookverwijzing wordt niet ondersteund in synapse-pijplijn.
>
>

---


### <a name="cell-status-indicator"></a>Celstatusindicator

De uitvoeringsstatus van een cel wordt stap voor stap weergegeven onder de cel om u te helpen de huidige voortgang te bekijken. Zodra de uitvoering van de cel is voltooid, wordt er een uitvoeringssamenvatting met de totale duur en eindtijd weergegeven en bewaard voor toekomstig gebruik.

![celstatus](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>Voortgangsindicator van Spark

Azure Synapse Studio-notebook is uitsluitend gebaseerd op Spark. Codecellen worden uitgevoerd op de serverloze Apache Spark groep op afstand. Er wordt een voortgangsindicator van de Spark-taak weergegeven met een realtime voortgangsbalk om inzicht te krijgen in de uitvoeringsstatus van de taak.
Het aantal taken per job of fase helpt u bij het identificeren van het parallelle niveau van uw Spark-job. U kunt ook dieper inzoomen op de Spark-gebruikersinterface van een specifieke taak (of fase) door de koppeling in de taaknaam (of fasenaam) te selecteren.


![spark-progress-indicator](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>Configuratie van Spark-sessie

U kunt de time-outduur, het aantal en de grootte van uitvoerders opgeven die aan de huidige Spark-sessie moeten worden opgegeven in **Sessie configureren.** Start de Spark-sessie opnieuw op om configuratiewijzigingen door te voeren. Alle notebookvariabelen in de cache worden gew cleared.

[![sessiebeheer](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-spark-session-management.png)](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-spark-session-management.png#lightbox)

#### <a name="spark-session-config-magic-command"></a>Magic-opdracht voor spark-sessie config
U kunt ook spark-sessie-instellingen opgeven via een magic-opdracht **%%configureer**. De Spark-sessie moet opnieuw worden gestart om het instellingeneffect te bereiken. U wordt aangeraden de **%%configure aan het** begin van uw notebook uit te voeren. Hier is een voorbeeld, raadpleeg voor https://github.com/cloudera/livy#request-body een volledige lijst met geldige parameters 

```
%%configure -f
{
    to config the session.
    "driverMemory":"2g",
    "driverCores":3,
    "executorMemory":"2g",
    "executorCores":2,
    "jars":["myjar1.jar","myjar.jar"],
    "conf":{
        "spark.driver.maxResultSize":"10g"
    }
}
```
> [!NOTE]
> Magic-opdracht voor spark-sessie config wordt niet ondersteund in Synapse-pijplijn.
>
>

## <a name="bring-data-to-a-notebook"></a>Gegevens naar een notebook brengen

U kunt gegevens laden uit Azure Blob Storage, Azure Data Lake Store Gen 2 en SQL-pool, zoals wordt weergegeven in de onderstaande codevoorbeelden.

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>Een CSV lezen uit Azure Data Lake Store Gen2 als een Spark DataFrame

```python
from pyspark.sql import SparkSession
from pyspark.sql.types import *
account_name = "Your account name"
container_name = "Your container name"
relative_path = "Your path"
adls_path = 'abfss://%s@%s.dfs.core.windows.net/%s' % (container_name, account_name, relative_path)

df1 = spark.read.option('header', 'true') \
                .option('delimiter', ',') \
                .csv(adls_path + '/Testfile.csv')

```

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>Een CSV van een Azure Blob Storage als een Spark DataFrame

```python

from pyspark.sql import SparkSession

# Azure storage access info
blob_account_name = 'Your account name' # replace with your blob name
blob_container_name = 'Your container name' # replace with your container name
blob_relative_path = 'Your path' # replace with your relative folder path
linked_service_name = 'Your linked service name' # replace with your linked service name

blob_sas_token = mssparkutils.credentials.getConnectionStringOrCreds(linked_service_name)

# Allow SPARK to access from Blob remotely

wasb_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)

spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
print('Remote blob path: ' + wasb_path)

df = spark.read.option("header", "true") \
            .option("delimiter","|") \
            .schema(schema) \
            .csv(wasbs_path)
```

### <a name="read-data-from-the-primary-storage-account"></a>Gegevens lezen uit het primaire opslagaccount

U hebt rechtstreeks toegang tot gegevens in het primaire opslagaccount. U hoeft de geheime sleutels niet op te geven. Klik Data Explorer met de rechtermuisknop op een bestand en selecteer Nieuw **notebook** om een nieuw notebook te zien met automatisch gegenereerde gegevensextractor.

![data-to-cell](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)

## <a name="save-notebooks"></a>Notebooks opslaan

U kunt één notebook of alle notebooks opslaan in uw werkruimte.

1. Als u wijzigingen wilt opslaan die u in één notebook hebt aangebracht, selecteert u **de knop Publiceren** op de notebook-opdrachtbalk.

   ![publish-notebook](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. Als u alle notebooks in uw werkruimte wilt opslaan, selecteert u **de knop Alles** publiceren op de opdrachtbalk van de werkruimte. 

   ![publish-all](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

In de notebookeigenschappen kunt u configureren of de celuitvoer moet worden op te nemen bij het opslaan.

   ![notebook-properties](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

## <a name="magic-commands"></a>Magic-opdrachten
U kunt bekende Jupyter Magic-opdrachten gebruiken in Azure Synapse Studio-notebooks. Bekijk de volgende lijst als de huidige beschikbare magic-opdrachten. Vertel ons [uw gebruiksgevallen op GitHub,](https://github.com/MicrosoftDocs/azure-docs/issues/new) zodat we meer magic-opdrachten kunnen blijven bouwen om aan uw behoeften te voldoen.

> [!NOTE]
> Alleen de volgende magic-opdrachten worden ondersteund in Synapse-pijplijn: %%pyspark, %%spark, %%csharp, %%sql. 
>
>

# <a name="classical-notebook"></a>[Klassieke notebook](#tab/classical)

Beschikbare line magics: [%lsmagic,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic) [%time,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time) [%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)

Beschikbare cel magics: [%%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%%capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture), [%%writefile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%%sql](#use-multiple-languages), [%%pyspark](#use-multiple-languages), [%%spark](#use-multiple-languages), [%%csharp](#use-multiple-languages),[%%configure](#spark-session-config-magic-command)



# <a name="preview-notebook"></a>[Preview-notebook](#tab/preview)

Beschikbare line magics: [%lsmagic,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic) [%time,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time) [%timeit,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit) [%history,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-history) [%run,](#notebook-reference) [%load](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-load)

Beschikbare cel magics: [%%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%%capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture), [%%writefile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%%sql](#use-multiple-languages), [%%pyspark](#use-multiple-languages), [%%spark](#use-multiple-languages), [%%csharp](#use-multiple-languages), [%%html](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-html), [%%configure](#spark-session-config-magic-command)

--- 

## <a name="integrate-a-notebook"></a>Een notebook integreren

### <a name="add-a-notebook-to-a-pipeline"></a>Een notebook toevoegen aan een pijplijn

Selecteer de **knop Toevoegen aan pijplijn** in de rechterbovenhoek om een notebook toe te voegen aan een bestaande pijplijn of een nieuwe pijplijn te maken.

![Notebook toevoegen aan pijplijn](./media/apache-spark-development-using-notebooks/add-to-pipeline.png)

### <a name="designate-a-parameters-cell"></a>Een parameterscel aanwijzen

# <a name="classical-notebook"></a>[Klassieke notebook](#tab/classical)

Als u een parameter wilt instellen voor uw notebook, selecteert u het beletselteken (...) voor toegang tot het extra menu voor celacties aan de rechterkant. Selecteer vervolgens **Parametercel in-/uitschakelen** om de cel aan te wijzen als de parametercel.

![toggle-parameter](./media/apache-spark-development-using-notebooks/toggle-parameter-cell.png)

# <a name="preview-notebook"></a>[Preview-notebook](#tab/preview)

Als u een parameter wilt instellen voor uw notebook, selecteert u het beletselteken (...) voor toegang tot de meer **opdrachten** op de celwerkbalk. Selecteer vervolgens **Parametercel in-/uitschakelen** om de cel aan te wijzen als de parametercel.

![azure-notebook-toggle-parameter](./media/apache-spark-development-using-notebooks/azure-notebook-toggle-parameter-cell.png)

---

Azure Data Factory zoekt naar de parameterscel en behandelt deze cel als standaardwaarden voor de parameters die tijdens de uitvoering worden doorgegeven. De uitvoeringsen engine voegt een nieuwe cel toe onder de parameterscel met invoerparameters om de standaardwaarden te overschrijven. Wanneer er geen parameterscel is aangewezen, wordt de geïnjecteerde cel boven aan het notebook ingevoegd.


### <a name="assign-parameters-values-from-a-pipeline"></a>Parameterswaarden toewijzen vanuit een pijplijn

Zodra u een notebook met parameters hebt gemaakt, kunt u deze uitvoeren vanuit een pijplijn met de Azure Synapse Notebook-activiteit. Nadat u de activiteit aan uw pijplijn-canvas hebt toevoegen, kunt u de parameters instellen onder de sectie Basisparameters op het **tabblad** Instellingen.  

![Een parameter toewijzen](./media/apache-spark-development-using-notebooks/assign-parameter.png)

Wanneer u parameterwaarden toewijst, kunt u de taal van de [pijplijnexpressie](../../data-factory/control-flow-expression-language-functions.md) of [systeemvariabelen gebruiken.](../../data-factory/control-flow-system-variables.md)



## <a name="shortcut-keys"></a>Sneltoetsen

Net als bij Jupyter Notebooks hebben Azure Synapse Studio-notebooks een modale gebruikersinterface. Het toetsenbord doet verschillende dingen, afhankelijk van de modus waarin de notebookcel zich in. Synapse Studio notebooks ondersteunen de volgende twee modi voor een bepaalde codecel: de opdrachtmodus en de bewerkingsmodus.

1. Een cel is in de opdrachtmodus wanneer er geen tekstcursor is waarin u wordt gevraagd om te typen. Wanneer een cel zich in de opdrachtmodus, kunt u het notebook als geheel bewerken, maar niet typen in afzonderlijke cellen. Voer de opdrachtmodus in door op of met de muis te drukken om buiten het editorgebied van een `ESC` cel te selecteren.

   ![opdrachtmodus](./media/apache-spark-development-using-notebooks/synapse-command-mode-2.png)

2. De bewerkingsmodus wordt aangegeven door een tekstcursor waarin u wordt gevraagd om in het editorgebied te typen. Wanneer een cel in de bewerkingsmodus is, kunt u in de cel typen. Voer de bewerkingsmodus in `Enter` door op of met de muis te drukken om te selecteren in het editorgebied van een cel.
   
   ![bewerkingsmodus](./media/apache-spark-development-using-notebooks/synapse-edit-mode-2.png)

### <a name="shortcut-keys-under-command-mode"></a>Sneltoetsen in de opdrachtmodus

# <a name="classical-notebook"></a>[Klassieke notebook](#tab/classical)

Met behulp van de volgende sneltoetsen kunt u eenvoudiger navigeren en code uitvoeren in Azure Synapse notebooks.

| Bewerking |Synapse Studio notebooksnelkoppelingen maken  |
|--|--|
|Voer de huidige cel uit en selecteer hieronder | Shift+Enter |
|Voer de huidige cel uit en voeg hieronder in | Alt+Enter |
|Selecteer de bovenstaande cel| Omhoog |
|Selecteer de onderstaande cel| Buiten gebruik |
|Cel hierboven invoegen| A |
|Voeg de onderstaande cel in| B |
|Geselecteerde cellen hierboven uitbreiden| Shift + omhoog |
|Geselecteerde cellen hieronder uitbreiden| Shift + down|
|Cel omhoog verplaatsen| Ctrl +Alt+· |
|Cel omlaag verplaatsen| Ctrl +Alt+↓ |
|Geselecteerde cellen verwijderen| D, D |
|Overschakelen naar de bewerkingsmodus| Enter |

# <a name="preview-notebook"></a>[Preview-notebook](#tab/preview)

| Bewerking |Synapse Studio notebooksnelkoppelingen maken  |
|--|--|
|Voer de huidige cel uit en selecteer hieronder | Shift + Enter |
|Voer de huidige cel uit en voeg hieronder in | Alt +Enter |
|Huidige cel uitvoeren| Ctrl+Enter |
|Selecteer de bovenstaande cel| Omhoog |
|Selecteer de onderstaande cel| Buiten gebruik |
|Vorige cel selecteren| K |
|Volgende cel selecteren| J |
|Cel hierboven invoegen| A |
|Cel hieronder invoegen| B |
|Geselecteerde cellen verwijderen| Shift+D |
|Overschakelen naar de bewerkingsmodus| Enter |

---

### <a name="shortcut-keys-under-edit-mode"></a>Sneltoetsen in de bewerkingsmodus


Met behulp van de volgende sneltoetsen kunt u eenvoudiger navigeren en code uitvoeren in Azure Synapse notebooks in de bewerkingsmodus.

| Bewerking |Synapse Studio notebooksnelkoppelingen maken  |
|--|--|
|Cursor omhoog verplaatsen | Omhoog |
|Cursor omlaag verplaatsen|Buiten gebruik|
|Ongedaan maken|Ctrl + Z|
|Opnieuw uitvoeren|Ctrl + Y|
|Opmerkingen/opmerkingen bij de opmerking|Ctrl + /|
|Woord vóór verwijderen|Ctrl + Backspace|
|Woord verwijderen na|Ctrl + Verwijderen|
|Naar cel starten gaan|Ctrl + Start|
|Naar cel beëindigen gaan |Ctrl + End|
|Eén woord links gaan|Ctrl + links|
|Ga één woord naar rechts|Ctrl + Rechts|
|Alles selecteren|Ctrl + A|
|Streepje| Ctrl +]|
|Dedent|Ctrl + [|
|Overschakelen naar de opdrachtmodus| Esc |

---

## <a name="next-steps"></a>Volgende stappen
- [Bekijk Synapse-voorbeeldnote notebooks](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks)
- [Quickstart: Een Apache Spark-pool maken in Azure Synapse Analytics met behulp van webhulpprogramma's](../quickstart-apache-spark-notebook.md)
- [Wat is Apache Spark in Azure Synapse Analytics?](apache-spark-overview.md)
- [.NET voor Apache Spark gebruiken met Azure Synapse Analytics](spark-dotnet.md)
- [Documentatie voor .NET voor Apache Spark](/dotnet/spark)
- [Azure Synapse Analytics](../index.yml)
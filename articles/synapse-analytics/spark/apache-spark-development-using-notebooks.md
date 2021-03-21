---
title: Synapse Studio-notebooks
description: In dit artikel leert u hoe u Azure Synapse Studio-notitie blokken maakt en ontwikkelt om gegevens voor te bereiden en visualisatie te maken.
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 10/19/2020
ms.author: ruxu
ms.reviewer: ''
ms.custom: devx-track-python
ms.openlocfilehash: d5ff3fb988a7e907308ccccc8d0900d45a0601c0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101671597"
---
# <a name="create-develop-and-maintain-synapse-studio-notebooks-in-azure-synapse-analytics"></a>Synapse Studio-notitie blokken maken, ontwikkelen en onderhouden in azure Synapse Analytics

Een Synapse Studio-notebook is een webinterface waarmee u bestanden kunt maken die live code, visualisaties en tekst verhalen bevatten. Notebooks zijn een goede plaats om ideeën te valideren en snelle experimenten te gebruiken om inzicht te krijgen in uw gegevens. Notebooks worden ook veel gebruikt in gegevens voorbereiding, gegevens visualisatie, machine learning en andere scenario's voor Big data.

Met een Azure Synapse Studio-notebook kunt u het volgende doen:

* Ga aan de slag met de installatie van nul.
* Beveilig gegevens met ingebouwde beveiligings functies van het bedrijf.
* Gegevens analyseren over onbewerkte indelingen (CSV, txt, JSON, enzovoort), verwerkte bestands indelingen (Parquet, Delta Lake, ORC, enzovoort) en SQL-gegevens bestanden in tabel vorm tegen Spark en SQL.
* Blijf productief met verbeterde ontwerp mogelijkheden en ingebouwde gegevens visualisatie.

In dit artikel wordt beschreven hoe u notitie blokken gebruikt in azure Synapse Studio.

## <a name="preview-of-the-new-notebook-experience"></a>Preview van de nieuwe ervaring voor het notitie blok
Het Synapse-team heeft het nieuwe notitieblok onderdeel in Synapse Studio gebracht om een consistente laptop ervaring te bieden voor micro soft-klanten en de detectie, productiviteit, delen en samen werking te maximaliseren. De nieuwe ervaring voor het notitie blok is gereed voor preview. Controleer de knop **Preview-functies** in de werk balk van notitie blok om deze in te scha kelen. In de volgende tabel wordt de functie vergelijking tussen een bestaand notitie blok en de nieuwe preview-versie vastgelegd.  

|Functie|Klassiek notebook|Voor beeld van notebook|
|--|--|--|
|% uitvoeren| Niet ondersteund | &#9745;|
|% History| Niet ondersteund |&#9745;
|% Load| Niet ondersteund |&#9745;|
|%% HTML| Niet ondersteund |&#9745;|
|Slepen en neerzetten om een cel te verplaatsen| Niet ondersteund |&#9745;|
|Uitvoer van permanente weer gave ()|&#9745;| Niet beschikbaar |
|Alles annuleren| &#9745;| Niet beschikbaar|
|Alle cellen hierboven uitvoeren|&#9745;| Niet beschikbaar |
|Alle onderliggende cellen uitvoeren|&#9745;| Niet beschikbaar |
|Tekst cel opmaken met werkbalk knoppen|&#9745;| Niet beschikbaar |
|Bewerking voor cel ongedaan maken| &#9745;| Niet beschikbaar |


## <a name="create-a-notebook"></a>Een notebook maken

Er zijn twee manieren om een notitie blok te maken. U kunt een nieuw notitie blok maken of een bestaand notitie blok importeren in een Azure Synapse-werk ruimte vanuit het **objectverkenner**. Azure Synapse Studio-notebooks kunnen standaard Jupyter Notebook IPYNB-bestanden herkennen.

![import notitieblok maken](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook-2.png)

## <a name="develop-notebooks"></a>Notebooks ontwikkelen

Notitie blokken bestaan uit cellen. Dit zijn afzonderlijke blokken code of tekst die onafhankelijk of als groep kunnen worden uitgevoerd.

### <a name="add-a-cell"></a>Een cel toevoegen

Er zijn meerdere manieren om een nieuwe cel aan uw notitie blok toe te voegen.

# <a name="classical-notebook"></a>[Klassiek notebook](#tab/classical)

1. Vouw de knop linksboven **+ cel** uit en selecteer **code-cel toevoegen** of **tekst toevoegen**.

    ![add-cel-with-Cell-knop](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. Beweeg de muis aanwijzer over de ruimte tussen twee cellen en selecteer **code toevoegen** of **tekst toevoegen**.

    ![add-cel: tussen ruimte](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. Gebruik sneltoetsen [onder de opdracht modus](#shortcut-keys-under-command-mode). Druk op **a** om een cel boven de huidige cel in te voegen. Druk op **B** om een cel onder de huidige cel in te voegen.


# <a name="preview-notebook"></a>[Voor beeld van notebook](#tab/preview)

1. Vouw de knop linksboven **+ cel** uit en selecteer cel met **code** of **prijs verlaging**.
    ![add-Azure-notebook-cel-with-Cell-knop](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-add-cell-1.png)
2. Selecteer het plus teken aan het begin van een cel en selecteer **code-cel** of **prijs verlaging**.

    ![add-Azure-notebook-cel-tussen ruimte](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-add-cell-2.png)

3. Gebruik de aznb-sneltoetsen [onder de opdracht modus](#shortcut-keys-under-command-mode). Druk op **a** om een cel boven de huidige cel in te voegen. Druk op **B** om een cel onder de huidige cel in te voegen.

---

### <a name="set-a-primary-language"></a>Een primaire taal instellen

Azure Synapse Studio-notebooks ondersteunen vier Apache Spark talen:

* pySpark (python)
* Spark (Scala)
* SparkSQL
* .NET voor Apache Spark (C#)

U kunt de primaire taal voor nieuwe toegevoegde cellen instellen in de vervolg keuzelijst in de bovenste opdracht balk.

   ![default-Synapse-taal](./media/apache-spark-development-using-notebooks/synapse-default-language.png)

### <a name="use-multiple-languages"></a>Meerdere talen gebruiken

U kunt meerdere talen gebruiken in één notebook door de juiste Magic-opdracht voor de taal aan het begin van een cel op te geven. De volgende tabel bevat de Magic-opdrachten om te scha kelen tussen talen in cellen.

|Opdracht Magic |Taal | Beschrijving |  
|---|------|-----|
|%% pyspark| Python | Voer een **python** -query uit op Spark-context.  |
|% Spark| Scala | Voer een **scala** -query uit op Spark-context.  |  
|%% SQL| SparkSQL | Voer een **SparkSQL** -query uit op Spark-context.  |
|%% csharp | .NET voor Spark C # | Voer een **.net for Spark C#** -query uit op Spark-context. |

De volgende afbeelding is een voor beeld van hoe u een PySpark-query kunt schrijven met behulp van de opdracht **%% PySpark** Magic of een SparkSQL-query met de **%% SQL** Magic-opdracht in een **Spark (scala)-** notebook. U ziet dat de primaire taal voor het notitie blok is ingesteld op pySpark.

   ![Synapse Spark Magic-opdrachten](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)

### <a name="use-temp-tables-to-reference-data-across-languages"></a>Tijdelijke tabellen gebruiken om te verwijzen naar gegevens tussen talen

U kunt niet rechtstreeks verwijzen naar gegevens of variabelen in verschillende talen in een Synapse Studio-notebook. In Spark kan naar een tijdelijke tabel worden verwezen tussen talen. Hier volgt een voor beeld van het lezen van een `Scala` Data frame in `PySpark` en `SparkSQL` het gebruik van een Spark-tijdelijke tabel als tijdelijke oplossing.

1. In cel 1 leest u een data frame van een SQL-groeps connector met behulp van scala en maakt u een tijdelijke tabel.

   ```scala
   %%scala
   val scalaDataFrame = spark.read.sqlanalytics("mySQLPoolDatabase.dbo.mySQLPoolTable")
   scalaDataFrame.createOrReplaceTempView( "mydataframetable" )
   ```

2. In cel 2 kunt u een query uitvoeren op de gegevens met behulp van Spark SQL.
   
   ```sql
   %%sql
   SELECT * FROM mydataframetable
   ```

3. Gebruik in cel 3 de gegevens in PySpark.

   ```python
   %%pyspark
   myNewPythonDataFrame = spark.sql("SELECT * FROM mydataframetable")
   ```

### <a name="ide-style-intellisense"></a>IDE-stijl IntelliSense

Azure Synapse Studio-notebooks zijn geïntegreerd met de Monaco-editor om een IDE-stijl IntelliSense te bieden in de cel-editor. Met syntaxis markering, fout markering en automatische code voltooiingen kunt u code sneller schrijven en problemen identificeren.

De IntelliSense-functies bevinden zich op verschillende niveaus van de verval datum voor verschillende talen. Gebruik de volgende tabel om te zien wat er wordt ondersteund.

|Talen| Syntaxis markering | Syntaxis fout markering  | Syntaxis code volt ooien | Voltooiing van variabele code| Voltooiing van systeem functie code| Voltooiing van gebruikers functie code| Smart Indent | Code vouwen|
|--|--|--|--|--|--|--|--|--|
|PySpark (Python)|Ja|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Spark (Scala)|Ja|Ja|Ja|Ja|-|-|-|Ja|
|SparkSQL|Ja|Ja|-|-|-|-|-|-|
|.NET voor Spark (C#)|Ja|-|-|-|-|-|-|-|

### <a name="format-text-cell-with-toolbar-buttons"></a>Tekst cel opmaken met werkbalk knoppen

# <a name="classical-notebook"></a>[Klassiek notebook](#tab/classical)

U kunt de opmaak knoppen op de werk balk tekst cellen gebruiken om algemene kortings acties uit te voeren. Het bevat vette tekst, italicizing tekst, het invoegen van code fragmenten, het invoegen van een niet-geordende lijst, het invoegen van een geordende lijst en het invoegen van de afbeelding van de URL.

  ![Werk balk voor Synapse tekst cellen](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

# <a name="preview-notebook"></a>[Voor beeld van notebook](#tab/preview)

De werk balk voor de indelings knop is nog niet beschikbaar voor de preview-versie van het notitie blok. 

---

### <a name="undo-cell-operations"></a>Bewerkingen in een cel ongedaan maken

# <a name="classical-notebook"></a>[Klassiek notebook](#tab/classical)

Selecteer de knop **ongedaan maken** of druk op **CTRL + Z** om de meest recente bewerking van de cel in te trekken. Nu kunt u tot de laatste 20 historische acties ongedaan maken. 

   ![Synapse ongedaan maken](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)
# <a name="preview-notebook"></a>[Voor beeld van notebook](#tab/preview)

Bewerking voor het ongedaan maken van de cel is nog niet beschikbaar voor de preview-versie van het notitie blok. 

---

### <a name="move-a-cell"></a>Een cel verplaatsen

# <a name="classical-notebook"></a>[Klassiek notebook](#tab/classical)

Selecteer de weglatings tekens (...) om het menu met aanvullende celwaarden helemaal rechts te openen. Selecteer vervolgens **cel verplaatsen** of **omlaag verplaatsen** om de huidige cel te verplaatsen. 

U kunt ook sneltoetsen gebruiken [onder de opdracht modus](#shortcut-keys-under-command-mode). Druk op **CTRL + ALT + ↑** om de huidige cel omhoog te verplaatsen. Druk op **CTRL + ALT + ↓** om de huidige cel omlaag te verplaatsen.

   ![Move-a-cel](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

# <a name="preview-notebook"></a>[Voor beeld van notebook](#tab/preview)

Klik aan de linkerkant van een cel en sleep deze naar de gewenste positie. 
    ![Synapse verplaatsen](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-drag-drop-cell.gif)

---

### <a name="delete-a-cell"></a>Een cel verwijderen

# <a name="classical-notebook"></a>[Klassiek notebook](#tab/classical)

Als u een cel wilt verwijderen, selecteert u de weglatings tekens (...) om het menu met extra celwaarden helemaal rechts te openen en selecteert u vervolgens **cel verwijderen**. 

U kunt ook sneltoetsen gebruiken [onder de opdracht modus](#shortcut-keys-under-command-mode). Druk op **d, d** om de huidige cel te verwijderen.
  
   ![Delete-a-cel](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

# <a name="preview-notebook"></a>[Voor beeld van notebook](#tab/preview)

Als u een cel wilt verwijderen, selecteert u de knop verwijderen aan de rechter kant van de cel. 

U kunt ook sneltoetsen gebruiken [onder de opdracht modus](#shortcut-keys-under-command-mode). Druk op **SHIFT + D** om de huidige cel te verwijderen. 

   ![Azure-notebook-Delete-a-cel](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-delete-cell.png)

---

### <a name="collapse-a-cell-input"></a>Een cel-invoer samen vouwen

# <a name="classical-notebook"></a>[Klassiek notebook](#tab/classical)

Selecteer de pijl knop aan de onderkant van de huidige cel om deze samen te vouwen. Als u deze wilt uitvouwen, selecteert u de pijl knop terwijl de cel wordt samengevouwen.

   ![samen vouwen-cel-invoer](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

# <a name="preview-notebook"></a>[Voor beeld van notebook](#tab/preview)

Selecteer de **meer opdrachten** weglatings tekens (...) op de werk balk van cel en **Voer de invoer** uit om de invoer van de huidige cel samen te vouwen. Als u deze wilt uitvouwen, selecteert u de **invoer verborgen** terwijl de cel wordt samengevouwen.

   ![Azure-notebook-samen vouwen-cel-invoer](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-collapse-cell-input.gif)

---

### <a name="collapse-a-cell-output"></a>Een cel-uitvoer samen vouwen

# <a name="classical-notebook"></a>[Klassiek notebook](#tab/classical)

Selecteer de knop **uitvoer samen vouwen** in de linkerbovenhoek van de huidige cel uitvoer om deze samen te vouwen. Als u deze wilt uitvouwen, selecteert u de uitvoer van de **cel weer geven** terwijl de uitvoer van de cel wordt samengevouwen.

   ![samen vouwen-cel-uitvoer](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

# <a name="preview-notebook"></a>[Voor beeld van notebook](#tab/preview)

Selecteer de **meer opdrachten** weglatings tekens (...) op de werk balk van cel en **uitvoer** om de uitvoer van de huidige cel samen te vouwen. Als u deze wilt uitvouwen, selecteert u dezelfde knop terwijl de uitvoer van de cel verborgen is.

   ![Azure-notebook-samen vouwen-cel-uitvoer](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-collapse-cell-output.gif)


---

## <a name="run-notebooks"></a>Notebooks gebruiken

U kunt de code cellen in uw notitie blok afzonderlijk of in één keer uitvoeren. De status en voortgang van elke cel worden weer gegeven in het notitie blok.

### <a name="run-a-cell"></a>Een cel uitvoeren

Er zijn verschillende manieren om de code in een cel uit te voeren.

1. Plaats de muis aanwijzer op de cel die u wilt uitvoeren en selecteer de knop **cel uitvoeren** of druk op **CTRL + ENTER**.

   ![Run-cel-1](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)
  
2. Gebruik sneltoetsen [onder de opdracht modus](#shortcut-keys-under-command-mode). Druk op **SHIFT + ENTER** om de huidige cel uit te voeren en selecteer de cel hieronder. Druk op **ALT + ENTER** om de huidige cel uit te voeren en voeg hieronder een nieuwe cel in.

---

### <a name="run-all-cells"></a>Alle cellen uitvoeren
Selecteer de knop **alles uitvoeren** om alle cellen in het huidige notitie blok in de juiste volg orde uit te voeren.

   ![run-alle-cellen](./media/apache-spark-development-using-notebooks/synapse-run-all.png)


# <a name="classical-notebook"></a>[Klassiek notebook](#tab/classical)

### <a name="run-all-cells-above-or-below"></a>Alle cellen boven of onder uitvoeren

Selecteer de weglatings tekens (**...**) om het menu met de extra celwaarden helemaal rechts te openen. Selecteer vervolgens **cellen hierboven uitvoeren** om alle cellen boven de huidige in de reeks uit te voeren. Selecteer **onderstaande cellen uitvoeren** om alle cellen onder de huidige in de reeks uit te voeren.

   ![Run-cellen-boven-of-onder](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)


### <a name="cancel-all-running-cells"></a>Alle actieve cellen annuleren
Selecteer de knop **Alles annuleren** om de actieve cellen of cellen in de wachtrij te annuleren. 
   ![annuleren-alle cellen](./media/apache-spark-development-using-notebooks/synapse-cancel-all.png) 

# <a name="preview-notebook"></a>[Voor beeld van notebook](#tab/preview)

Annuleren van alle actieve cellen is nog niet beschikbaar voor de preview-versie van het notitie blok. 

---



### <a name="reference-notebook"></a>Referentie notitieblok

# <a name="classical-notebook"></a>[Klassiek notebook](#tab/classical)

Wordt niet ondersteund.

# <a name="preview-notebook"></a>[Voor beeld van notebook](#tab/preview)

U kunt ```%run <notebook path>``` de opdracht Magic gebruiken om te verwijzen naar een andere notebook binnen de context van het huidige notitie blok. Alle variabelen die zijn gedefinieerd in de referentie notitieblok, zijn beschikbaar in het huidige notitie blok. ```%run``` de opdracht Magic ondersteunt geneste aanroepen, maar biedt geen ondersteuning voor recursieve aanroepen. Er wordt een uitzonde ring weer gegeven als de diepte van de verklaring groter is dan vijf. ```%run``` de opdracht ondersteunt momenteel alleen om een pad naar een notitie blok door te geven als para meter. 

Bijvoorbeeld: ``` %run /path/notebookA ```.

---


### <a name="cell-status-indicator"></a>Indicator status van cel

Er wordt een stap-voor-stap-uitvoerings status weer gegeven onder de cel om u te helpen de huidige voortgang te zien. Zodra de uitvoering van de cel is voltooid, wordt een samen vatting van de uitvoering met de totale duur en eind tijd weer gegeven en bewaard voor toekomstige referentie.

![cel-status](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>Voortgangs indicator Spark

Azure Synapse Studio notebook is louter Spark. Code cellen worden op afstand uitgevoerd op de serverloze Apache Spark groep. Er wordt een voortgangs indicator van Spark met een realtime voortgangs balk weer gegeven om u te helpen inzicht te krijgen in de status van de taak uitvoering.
Het aantal taken per taak of fase helpt u bij het identificeren van het parallelle niveau van uw Spark-taak. U kunt ook inzoomen op de Spark-gebruikers interface van een specifieke taak (of fase) via het selecteren van de koppeling voor de naam van de taak (of fase).


![vonk-voortgang-indicator](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>Configuratie van Spark-sessie

U kunt de duur van de time-out, het nummer en de grootte van de uitvoerder opgeven om aan de huidige Spark-sessie te geven in de **sessie configureren**. De Spark-sessie moet opnieuw worden gestart om de configuratie wijzigingen van kracht te laten worden. Alle notebook-variabelen in de cache zijn gewist.

[![sessie beheer](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-spark-session-management.png)](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-spark-session-management.png#lightbox)

#### <a name="spark-session-config-magic-command"></a>Opdracht Spark Session config Magic
U kunt ook Spark-sessie-instellingen opgeven via een Magic-opdracht **%% Configure**. De Spark-sessie moet opnieuw worden opgestart om de instellingen van kracht te laten worden. U wordt aangeraden de **configuratie%%** aan het begin van uw notitie blok uit te voeren. Hier volgt een https://github.com/cloudera/livy#request-body voor beeld van voor de volledige lijst met geldige para meters 

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


## <a name="bring-data-to-a-notebook"></a>Gegevens naar een notitie blok brengen

U kunt gegevens laden uit Azure Blob Storage, Azure Data Lake Store gen 2 en SQL-pool, zoals wordt weer gegeven in de onderstaande code voorbeelden.

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>Een CSV lezen van Azure Data Lake Store Gen2 als Spark data frame

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

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>Een CSV van Azure Blob Storage lezen als Spark-data frame

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

### <a name="read-data-from-the-primary-storage-account"></a>Gegevens uit het primaire opslag account lezen

U kunt rechtstreeks toegang krijgen tot gegevens in het primaire opslag account. Het is niet nodig om de geheime sleutels op te geven. Klik in Data Explorer met de rechter muisknop op een bestand en selecteer **Nieuw notitie blok** om een nieuw notitie blok met automatisch gegenereerde gegevens te bekijken.

![gegevens naar cel](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)

## <a name="save-notebooks"></a>Notitie blokken opslaan

U kunt één notitie blok of alle notitie blokken in uw werk ruimte opslaan.

1. Als u de wijzigingen die u hebt aangebracht in één notitie blok wilt opslaan, selecteert u de knop **publiceren** op de opdracht balk van het notitie blok.

   ![publiceren-notebook](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. Als u alle notitie blokken in uw werk ruimte wilt opslaan, selecteert u de knop **Alles publiceren** op de opdracht balk van de werk ruimte. 

   ![Alles publiceren](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

In de eigenschappen van het notitie blok kunt u configureren of de celinhoud moet worden meegenomen bij het opslaan.

   ![Notebook-eigenschappen](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

## <a name="magic-commands"></a>Magic-opdrachten
U kunt vertrouwde Jupyter Magic-opdrachten gebruiken in azure Synapse Studio-notebooks. Bekijk de volgende lijst als de huidige beschik bare Magic-opdrachten. Vertel ons [uw use cases op github](https://github.com/MicrosoftDocs/azure-docs/issues/new) zodat we meer Magic-opdrachten kunnen blijven bouwen om aan uw behoeften te voldoen.

# <a name="classical-notebook"></a>[Klassiek notebook](#tab/classical)

Beschik bare lijn-magices: [% lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic), [% time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [% timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)

Beschik bare cel magics: [%% time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%% timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%% Capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture), [%% Write File](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%% SQL](#use-multiple-languages), [%% pyspark](#use-multiple-languages), [%% Spark](#use-multiple-languages), [%% csharp](#use-multiple-languages),[%% Configure](#spark-session-config-magic-command)



# <a name="preview-notebook"></a>[Voor beeld van notebook](#tab/preview)

Beschik bare lijn-magices: [% lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic), [% time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [% timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [% history](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-history), [% Run](#reference-notebook), [% Load](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-load)

Beschik bare cel-magische: [%% time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%% timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%% Capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture), [%% Write File](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%% SQL](#use-multiple-languages), [%% pyspark](#use-multiple-languages), [%% Spark](#use-multiple-languages), [%% csharp](#use-multiple-languages), [%% HTML](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-html), [%% configureren](#spark-session-config-magic-command)

--- 

## <a name="integrate-a-notebook"></a>Een notitie blok integreren

### <a name="add-a-notebook-to-a-pipeline"></a>Een notitie blok toevoegen aan een pijp lijn

Selecteer de knop **toevoegen aan pijp lijn** in de rechter bovenhoek om een notitie blok toe te voegen aan een bestaande pijp lijn of een nieuwe pijp lijn te maken.

![Notitie blok toevoegen aan pijp lijn](./media/apache-spark-development-using-notebooks/add-to-pipeline.png)

### <a name="designate-a-parameters-cell"></a>Een cel met para meters aanwijzen

# <a name="classical-notebook"></a>[Klassiek notebook](#tab/classical)

Als u uw notitie blok wilt para meters, selecteert u de weglatings tekens (...) om het menu met extra celwaarden helemaal rechts te openen. Selecteer vervolgens de cel **Toggle para meter** om de cel aan te wijzen als de para meters-cel.

![Toggle-para meter](./media/apache-spark-development-using-notebooks/toggle-parameter-cell.png)

# <a name="preview-notebook"></a>[Voor beeld van notebook](#tab/preview)

Als u uw notitie blok wilt para meters, selecteert u de weglatings tekens (...) om toegang te krijgen tot de **meer opdrachten** op de werk balk van de cel. Selecteer vervolgens de cel **Toggle para meter** om de cel aan te wijzen als de para meters-cel.

![Azure-notebook-Toggle-para meter](./media/apache-spark-development-using-notebooks/azure-notebook-toggle-parameter-cell.png)

---

Azure Data Factory zoekt naar de para meters-cel en behandelt deze cel als standaard waarden voor de para meters die tijdens de uitvoerings tijd worden door gegeven. De uitvoerings engine voegt een nieuwe cel onder de cel para meters met invoer parameters toe om de standaard waarden te overschrijven. Als er geen para meters-cel is opgegeven, wordt de geïnjecteerde cel boven aan het notitie blok ingevoegd.


### <a name="assign-parameters-values-from-a-pipeline"></a>Parameter waarden toewijzen vanuit een pijp lijn

Zodra u een notitie blok met para meters hebt gemaakt, kunt u het uitvoeren vanuit een pijp lijn met de Azure Synapse-notebook activiteit. Nadat u de activiteit aan uw pijplijn doek hebt toegevoegd, kunt u de parameter waarden instellen in het gedeelte **basis parameters** op het tabblad **instellingen** . 

![Een para meter toewijzen](./media/apache-spark-development-using-notebooks/assign-parameter.png)

Wanneer u parameter waarden toewijst, kunt u de taal van de [pijplijn expressie](../../data-factory/control-flow-expression-language-functions.md) of [systeem variabelen](../../data-factory/control-flow-system-variables.md)gebruiken.



## <a name="shortcut-keys"></a>Sneltoetsen

Net als Jupyter-notebooks hebben Azure Synapse Studio-notebooks een modale gebruikers interface. Het toetsen bord heeft verschillende dingen, afhankelijk van de modus waarin de notebook-cel zich bevindt. Synapse Studio-notitie blokken ondersteunen de volgende twee modi voor een bepaalde code-cel: opdracht modus en bewerkings modus.

1. Een cel bevindt zich in de opdracht modus als er geen tekst cursor wordt gevraagd om te typen. Wanneer een cel zich in de opdracht modus bevindt, kunt u het notitie blok als geheel bewerken, maar niet typen in afzonderlijke cellen. Voer de opdracht modus in door te drukken `ESC` of door met de muis te klikken buiten het editor gebied van een cel.

   ![opdracht modus](./media/apache-spark-development-using-notebooks/synapse-command-mode-2.png)

2. De bewerkings modus wordt aangegeven door een tekst cursor waarin u wordt gevraagd in het gebied van de editor te typen. Wanneer een cel zich in de bewerkings modus bevindt, kunt u in de cel typen. Voer de bewerkings modus in door te drukken `Enter` of door met de muis te klikken op het editor gebied van een cel.
   
   ![bewerkingsmodus](./media/apache-spark-development-using-notebooks/synapse-edit-mode-2.png)

### <a name="shortcut-keys-under-command-mode"></a>Sneltoetsen onder de opdracht modus

# <a name="classical-notebook"></a>[Klassiek notebook](#tab/classical)

Met behulp van de volgende sneltoetsen kunt u eenvoudig navigeren en code uitvoeren in azure Synapse-notebooks.

| Bewerking |Synapse Studio-notebook-snelkoppelingen  |
|--|--|
|Voer de huidige cel uit en selecteer hieronder | SHIFT + ENTER |
|De huidige cel uitvoeren en onder invoegen | ALT + ENTER |
|Cel hierboven selecteren| Omhoog |
|Selecteer de cel onder| Buiten gebruik |
|Cel boven invoegen| A |
|Cel onder invoegen| B |
|Geselecteerde cellen hierboven uitbreiden| Shift + omhoog |
|Geselecteerde cellen hieronder uitbreiden| Shift + pijl-omlaag|
|Cel omhoog verplaatsen| CTRL + ALT + ↑ |
|Cel omlaag verplaatsen| CTRL + ALT + ↓ |
|Geselecteerde cellen verwijderen| D, D |
|Overschakelen naar de bewerkings modus| Enter |

# <a name="preview-notebook"></a>[Voor beeld van notebook](#tab/preview)

| Bewerking |Synapse Studio-notebook-snelkoppelingen  |
|--|--|
|Voer de huidige cel uit en selecteer hieronder | SHIFT + ENTER |
|De huidige cel uitvoeren en onder invoegen | ALT + ENTER |
|Huidige cel uitvoeren| Ctrl+Enter |
|Cel hierboven selecteren| Omhoog |
|Selecteer de cel onder| Buiten gebruik |
|Vorige cel selecteren| K |
|Selecteer volgende cel| J |
|Cel boven invoegen| A |
|Cel onder invoegen| B |
|Geselecteerde cellen verwijderen| SHIFT + D |
|Overschakelen naar de bewerkings modus| Enter |

---

### <a name="shortcut-keys-under-edit-mode"></a>Sneltoetsen in de bewerkings modus


Met de volgende sneltoetsen kunt u gemakkelijker code in azure Synapse-notebooks navigeren en uitvoeren in de bewerkings modus.

| Bewerking |Synapse Studio-notebook-snelkoppelingen  |
|--|--|
|Cursor omhoog verplaatsen | Omhoog |
|Cursor omlaag verplaatsen|Buiten gebruik|
|Ongedaan maken|CTRL + Z|
|Opnieuw uitvoeren|CTRL + Y|
|Opmerking/Opmerking opheffen|CTRL +/|
|Woord vóór verwijderen|CTRL + BACKSPACE|
|Woord verwijderen na|CTRL + DELETE|
|Naar begin van cel|Ctrl + Home|
|Naar einde van cel |Ctrl + End|
|Eén woord naar links gaan|Ctrl + pijl-links|
|Eén woord naar rechts gaan|CTRL + rechts|
|Alles selecteren|CTRL + A|
|Indent| CTRL +]|
|Inspringing verkleinen|CTRL + [|
|Overschakelen naar de opdracht modus| Esc |

---

## <a name="next-steps"></a>Volgende stappen
- [Voor beelden van Synapse-voorbeeld notitieblokken bekijken](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks)
- [Quickstart: Een Apache Spark-pool maken in Azure Synapse Analytics met behulp van webhulpprogramma's](../quickstart-apache-spark-notebook.md)
- [Wat is Apache Spark in Azure Synapse Analytics?](apache-spark-overview.md)
- [.NET voor Apache Spark gebruiken met Azure Synapse Analytics](spark-dotnet.md)
- [Documentatie voor .NET voor Apache Spark](/dotnet/spark)
- [Azure Synapse Analytics](../index.yml)
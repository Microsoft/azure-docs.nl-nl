---
title: 'Zelfstudie: Apache Spark-taakdefinitie maken in Synapse Studio'
description: Zelfstudie - gebruik de Azure Synapse Analytics om Spark-taakdefinities te maken en verzend deze naar een Apache Spark voor de Azure Synapse Analytics-pool.
author: Jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 10/16/2020
ms.openlocfilehash: 3f1e3fd360197310a89a67d43053649d904aeb18
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101677622"
---
# <a name="tutorial-create-apache-spark-job-definition-in-synapse-studio"></a>Zelfstudie: Apache Spark-taakdefinitie maken in Synapse Studio

In deze zelfstudie ziet u hoe u Azure Synapse Studio kunt gebruiken om Apache Spark-taakdefinities te maken en deze vervolgens naar een serverloze Apache Spark-pool te verzenden.

Deze zelfstudie bestaat uit de volgende taken:
> [!div class="checklist"]
>
> - Een Apache Spark-taakdefinitie maken voor PySpark (Python)
> - Een Apache Spark-taakdefinitie maken voor Spark (Scala)
> - Een Apache Spark-taakdefinitie maken voor .NET Spark (C#/F#)
> - Een Apache Spark-taakdefinitie verzenden als batchtaak
> - Een Apache Spark-taakdefinitie toevoegen aan de pijplijn

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u aan de volgende vereisten voldoet voordat u met deze zelfstudie begint:

* Een Azure Synapse Analytics-werkruimte. Zie [Een Azure Synapse Analytics-werkruimte maken](../../machine-learning/how-to-manage-workspace.md) voor instructies.
* Een serverloze Apache Spark-pool.
* Een ADLS Gen2-opslagaccount. U moet de **gegevenseigenaar van de opslag-blob** zijn van het ADLS Gen2-bestandssysteem waarmee u wilt werken. Als dat niet het geval is, moet u de machtiging handmatig toevoegen.
* Als u de standaardopslag van de werkruimte niet wilt gebruiken, koppelt u het vereiste ADLS Gen2-opslagaccount in Synapse Studio. 

## <a name="create-an-apache-spark-job-definition-for-pyspark-python"></a>Een Apache Spark-taakdefinitie maken voor PySpark (Python)

In deze sectie maakt u een Apache Spark-taakdefinitie voor PySpark (Python).

1. Open [Azure Synapse Studio](https://web.azuresynapse.net/).

2. U kunt naar [Voorbeeldbestanden voor het maken van Apache Spark-taakdefinities](https://github.com/Azure-Samples/Synapse/tree/master/Spark/Python) gaan om **voorbeeldbestanden voor python.zip** te downloaden. Pak het gecomprimeerde pakket vervolgens uit, inclusief de bestanden **wordcount.py** en **shakespeare.txt**. 

     ![voorbeeldbestanden](./media/apache-spark-job-definitions/sample-files.png)

3. Selecteer **Gegevens** -> **Gekoppeld** -> **Azure Data Lake Storage Gen2** en upload **wordcount.py** en **shakespeare.txt** naar uw ADLS Gen2-bestandssysteem. 

     ![python-bestand uploaden](./media/apache-spark-job-definitions/upload-python-file.png)

4. Selecteer de hub **Ontwikkelen**, selecteer het pictogram ‘+’ en selecteer **Spark-taakdefinitie** om een nieuwe Spark-taakdefinitie te maken. 

     ![nieuwe definitie maken voor python](./media/apache-spark-job-definitions/create-new-definition.png)

5. Selecteer **PySpark (Python)** in de vervolgkeuzelijst Taal in het hoofdvenster van de Apache Spark-taakdefinitie.

     ![Selecteer Python](./media/apache-spark-job-definitions/select-python.png)

6. Vul de gegevens in voor de Apache Spark-taakdefinitie. 

     |  Eigenschap   | Beschrijving   |  
     | ----- | ----- |  
     |Naam van taakdefinitie| Voer een naam in voor de Apache Spark-taakdefinitie. Deze naam kan op elk gewenst moment worden bijgewerkt totdat deze wordt gepubliceerd. <br> Voorbeeld: `job definition sample`|
     |Primair definitiebestand| Het primaire bestand dat wordt gebruikt voor de taak. Selecteer een PY-bestand uit uw opslag. U kunt **Bestand uploaden** selecteren om het bestand te uploaden naar een opslagaccount. <br> Voorbeeld: `abfss://…/path/to/wordcount.py`|
     |Opdrachtregelargumenten| Optionele argumenten voor de taak. <br> Voorbeeld: `abfss://…/path/to/shakespeare.txt` `abfss://…/path/to/result` <br> *Opmerking: Twee argumenten voor de voorbeeld taakdefinitie worden gescheiden door een spatie.*|
     |Verwijzingsbestanden| Aanvullende bestanden die worden gebruikt voor verwijzingen in het hoofddefinitiebestand. U kunt **Bestand uploaden** selecteren om het bestand te uploaden naar een opslagaccount. |
     |Spark-pool| De taak wordt verzonden naar de geselecteerde Apache Spark-pool.|
     |Spark-versie| De versie van Apache Spark waarin de Apache Spark-pool wordt uitgevoerd.|
     |Uitvoerders| Aantal uitvoerders dat moet worden opgegeven in de gespecificeerde Apache Spark-pool voor de taak.|
     |Grootte van uitvoerder| Aantal kernen en het geheugen die moet worden gebruikt voor de uitvoerders die in de gespecificeerde Apache Spark-pool voor de taak zijn opgegeven.|  
     |Grootte van stuurprogramma| Aantal kernen en het geheugen die moet worden gebruikt voor het stuurprogramma dat in de gespecificeerde Apache Spark-pool voor de taak is opgegeven.|

     ![De waarde van de Apache Spark-taakdefinitie voor Python instellen](./media/apache-spark-job-definitions/create-py-definition.png)

7. Selecteer **Publiceren** om de Apache Spark-taakdefinitie op te slaan.

     ![py-definitie publiceren](./media/apache-spark-job-definitions/publish-py-definition.png)

## <a name="create-an-apache-spark-job-definition-for-apache-sparkscala"></a>Een Apache Spark-taakdefinitie maken voor Apache Spark (Scala)

In deze sectie maakt u een Apache Spark-taakdefinitie voor Apache Spark (Scala).

 1. Open [Azure Synapse Studio](https://web.azuresynapse.net/).

 2. U kunt naar [Voorbeeldbestanden voor het maken van Apache Spark-taakdefinities](https://github.com/Azure-Samples/Synapse/tree/master/Spark/Scala) gaan om **voorbeeldbestanden voor scala.zip** te downloaden. Pak het gecomprimeerde pakket vervolgens uit, inclusief de bestanden **wordcount.jar** en **shakespeare.txt**. 
 
     ![voorbeeldbestanden voor scala](./media/apache-spark-job-definitions/sample-files-scala.png)

 3. Selecteer **Gegevens** -> **Gekoppeld** -> **Azure Data Lake Storage Gen2** en upload **wordcount.jar** en **shakespeare.txt** naar uw ADLS Gen2-bestandssysteem.
 
     ![scala-structuur voorbereiden](./media/apache-spark-job-definitions/prepare-scala-structure.png)

 4. Selecteer de hub **Ontwikkelen**, selecteer het pictogram ‘+’ en selecteer **Spark-taakdefinitie** om een nieuwe Spark-taakdefinitie te maken. (De voorbeeldafbeelding is hetzelfde als stap 4 van **Een Apache Spark-taakdefinitie (Python) maken voor PySpark**.)

 5. Selecteer **Spark(Scala)** in de vervolgkeuzelijst Taal in het hoofdvenster van de Apache Spark-taakdefinitie.

     ![scala selecteren](./media/apache-spark-job-definitions/select-scala.png)

 6. Vul de gegevens in voor de Apache Spark-taakdefinitie. U kunt de voorbeeldgegevens kopiëren.

     |  Eigenschap   | Beschrijving   |  
     | ----- | ----- |  
     |Naam van taakdefinitie| Voer een naam in voor de Apache Spark-taakdefinitie. Deze naam kan op elk gewenst moment worden bijgewerkt totdat deze wordt gepubliceerd. <br> Voorbeeld: `scala`|
     |Primair definitiebestand| Het primaire bestand dat wordt gebruikt voor de taak. Selecteer een JAR-bestand uit uw opslag. U kunt **Bestand uploaden** selecteren om het bestand te uploaden naar een opslagaccount. <br> Voorbeeld: `abfss://…/path/to/wordcount.jar`|
     |Hoofdklassenaam| De volledig gekwalificeerde id of de hoofdklasse die zich in het hoofddefinitiebestand bevindt. <br> Voorbeeld: `WordCount`|
     |Opdrachtregelargumenten| Optionele argumenten voor de taak. <br> Voorbeeld: `abfss://…/path/to/shakespeare.txt` `abfss://…/path/to/result` <br> *Opmerking: Twee argumenten voor de voorbeeld taakdefinitie worden gescheiden door een spatie.* |
     |Verwijzingsbestanden| Aanvullende bestanden die worden gebruikt voor verwijzingen in het hoofddefinitiebestand. U kunt **Bestand uploaden** selecteren om het bestand te uploaden naar een opslagaccount.|
     |Spark-pool| De taak wordt verzonden naar de geselecteerde Apache Spark-pool.|
     |Spark-versie| De versie van Apache Spark waarin de Apache Spark-pool wordt uitgevoerd.|
     |Uitvoerders| Aantal uitvoerders dat moet worden opgegeven in de gespecificeerde Apache Spark-pool voor de taak.|  
     |Grootte van uitvoerder| Aantal kernen en het geheugen die moet worden gebruikt voor de uitvoerders die in de gespecificeerde Apache Spark-pool voor de taak zijn opgegeven.|
     |Grootte van stuurprogramma| Aantal kernen en het geheugen die moet worden gebruikt voor het stuurprogramma dat in de gespecificeerde Apache Spark-pool voor de taak is opgegeven.|

     ![De waarde van de Apache Spark-taakdefinitie voor scala instellen](./media/apache-spark-job-definitions/create-scala-definition.png)

 7. Selecteer **Publiceren** om de Apache Spark-taakdefinitie op te slaan.

      ![scala-definitie publiceren](./media/apache-spark-job-definitions/publish-scala-definition.png)

## <a name="create-an-apache-spark-job-definition-for-net-sparkcf"></a>Een Apache Spark-taakdefinitie maken voor .NET Spark (C#/F#)

In deze sectie maakt u een Apache Spark-taakdefinitie voor .NET Spark(C#/F#).
 1. Open [Azure Synapse Studio](https://web.azuresynapse.net/).

 2. U kunt naar [Voorbeeldbestanden voor het maken van Apache Spark-taakdefinities](https://github.com/Azure-Samples/Synapse/tree/master/Spark/DotNET) gaan om **voorbeeldbestanden voor dotnet.zip** te downloaden. Pak het gecomprimeerde pakket vervolgens uit, inclusief de bestanden **wordcount.zip** en **shakespeare.txt**. 

     ![voorbeeld van dotnet](./media/apache-spark-job-definitions/sample-dotnet.png)

 3. Selecteer **Gegevens** -> **Gekoppeld** -> **Azure Data Lake Storage Gen2** en upload **wordcount.zip** en **shakespeare.txt** naar uw ADLS Gen2-bestandssysteem.
 
     ![dotnet-structuur voorbereiden](./media/apache-spark-job-definitions/prepare-dotnet-structure.png)

 4. Selecteer de hub **Ontwikkelen**, selecteer het pictogram ‘+’ en selecteer **Spark-taakdefinitie** om een nieuwe Spark-taakdefinitie te maken. (De voorbeeldafbeelding is hetzelfde als stap 4 van **Een Apache Spark-taakdefinitie (Python) maken voor PySpark**.)

 5. Selecteer **.NET Spark(C#/F#)** in de vervolgkeuzelijst Taal in het hoofdvenster van de Apache Spark-taakdefinitie.

     ![dotnet selecteren](./media/apache-spark-job-definitions/select-dotnet.png)

 6. Vul de gegevens in voor de Apache Spark-taakdefinitie. U kunt de voorbeeldgegevens kopiëren.
    
     |  Eigenschap   | Beschrijving   |  
     | ----- | ----- |  
     |Naam van taakdefinitie| Voer een naam in voor de Apache Spark-taakdefinitie. Deze naam kan op elk gewenst moment worden bijgewerkt totdat deze wordt gepubliceerd. <br> Voorbeeld: `dotnet`|
     |Primair definitiebestand| Het primaire bestand dat wordt gebruikt voor de taak. Selecteer een ZIP-bestand dat uw .NET voor Apache Spark-toepassing bevat (dat wil zeggen, het belangrijkste uitvoerbare bestand, DLL's met door de gebruiker gedefinieerde functies en andere vereiste bestanden) van uw opslag. U kunt **Bestand uploaden** selecteren om het bestand te uploaden naar een opslagaccount. <br> Voorbeeld: `abfss://…/path/to/wordcount.zip`|
     |Uitvoerbaar hoofdbestand| Het belangrijkste uitvoerbare bestand in het primaire ZIP-definitiebestand. <br> Voorbeeld: `WordCount`|
     |Opdrachtregelargumenten| Optionele argumenten voor de taak. <br> Voorbeeld: `abfss://…/path/to/shakespeare.txt` `abfss://…/path/to/result` <br> *Opmerking: Twee argumenten voor de voorbeeld taakdefinitie worden gescheiden door een spatie.* |
     |Verwijzingsbestanden| Aanvullende bestanden die nodig zijn voor de werkknooppunten voor het uitvoeren van de .NET voor Apache Spark-toepassing die niet is opgenomen in het bestand met de primaire definitie (dat wil zeggen, afhankelijke JAR's, aanvullende door de gebruiker gedefinieerde functie-DLL's en andere configuratiebestanden). U kunt **Bestand uploaden** selecteren om het bestand te uploaden naar een opslagaccount.|
     |Spark-pool| De taak wordt verzonden naar de geselecteerde Apache Spark-pool.|
     |Spark-versie| De versie van Apache Spark waarin de Apache Spark-pool wordt uitgevoerd.|
     |Uitvoerders| Aantal uitvoerders dat moet worden opgegeven in de gespecificeerde Apache Spark-pool voor de taak.|  
     |Grootte van uitvoerder| Aantal kernen en het geheugen die moet worden gebruikt voor de uitvoerders die in de gespecificeerde Apache Spark-pool voor de taak zijn opgegeven.|
     |Grootte van stuurprogramma| Aantal kernen en het geheugen die moet worden gebruikt voor het stuurprogramma dat in de gespecificeerde Apache Spark-pool voor de taak is opgegeven.|

     ![De waarde van de Apache Spark-taakdefinitie voor dotnet instellen](./media/apache-spark-job-definitions/create-dotnet-definition.png)

 7. Selecteer **Publiceren** om de Apache Spark-taakdefinitie op te slaan.

      ![dotnet-definitie publiceren](./media/apache-spark-job-definitions/publish-dotnet-definition.png)

## <a name="submit-an-apache-spark-job-definition-as-a-batch-job"></a>Een Apache Spark-taakdefinitie verzenden als batchtaak

Nadat u een Apache Spark-taakdefinitie hebt gemaakt, kunt u deze verzenden naar een Apache Spark-pool. Zorg ervoor dat u de **gegevenseigenaar van de opslag-blob** bent van het ADLS Gen2-bestandssysteem waarmee u wilt werken. Als dat niet het geval is, moet u de machtiging handmatig toevoegen.

### <a name="scenario-1-submit-apache-spark-job-definition"></a>Scenario 1: Apache Spark-taakdefinitie indienen
 1. Open een Apache Spark-taakdefinitievenster door deze te selecteren.

      ![Spark-taakdefinitie openen om te verzenden ](./media/apache-spark-job-definitions/open-spark-definition.png)

 2. Selecteer de knop **Verzenden** om uw project te verzenden naar de geselecteerde Apache Spark-pool. U kunt het tabblad **URL voor Spark-bewaking** selecteren om de LogQuery van de Apache Spark-toepassing weer te geven.

    ![Selecteer de knop verzenden om een Spark-taakdefinitie te verzenden](./media/apache-spark-job-definitions/submit-spark-definition.png)

    ![Het dialoogvenster voor de verzending van Spark](./media/apache-spark-job-definitions/submit-definition-result.png)

### <a name="scenario-2-view-apache-spark-job-running-progress"></a>Scenario 2: Voortgang van Apache Spark-taak weergeven

 1. Selecteer **Bewaken** en selecteer vervolgens de optie **Apache Spark-toepassingen**. U kunt de verzonden Apache Spark-toepassing vinden.

     ![Spark-toepassing weergeven](./media/apache-spark-job-definitions/view-spark-application.png)

 2. Selecteer vervolgens een Apache Spark-toepassing; het venster **SparkJobDefinition**-taak wordt weergegeven. U kunt de voortgang van de taakuitvoering van daaruit bekijken.
     
     ![Spark-toepassing LogQuery weergeven](./media/apache-spark-job-definitions/view-job-log-query.png)

### <a name="scenario-3-check-output-file"></a>Scenario 3: Uitvoerbestand controleren

 1. Selecteer **Gegevens** -> **Gekoppeld** -> **Azure Data Lake Storage Gen2** (hozhaobdbj), open de map **Resultaat** die u eerder hebt gemaakt, ga naar de map met resultaten en controleer of de uitvoer is gegenereerd.

     ![Uitvoerbestand weergeven](./media/apache-spark-job-definitions/view-output-file.png)

## <a name="add-an-apache-spark-job-definition-into-pipeline"></a>Een Apache Spark-taakdefinitie toevoegen aan de pijplijn

In deze sectie voegt u een Apache Spark-taakdefinitie toe aan de pijplijn.

 1. Open een bestaande Apache Spark-taakdefinitie.

 2. Selecteer het pictogram in de rechterbovenhoek van de Apache Spark-taakdefinitie, kies **Bestaande pijplijn** of **Nieuwe pijplijn**. U kunt de pijplijnpagina raadplegen voor meer informatie.

     ![toevoegen aan pipeline1](./media/apache-spark-job-definitions/add-to-pipeline01.png)

     ![toevoegen aan pipeline2](./media/apache-spark-job-definitions/add-to-pipeline02.png)

## <a name="next-steps"></a>Volgende stappen

Vervolgens kunt u de Azure Synapse Studio gebruiken om Power BI-gegevenssets te maken en Power BI-gegevens te beheren. Ga naar het artikel [Een Power BI-werkruimte koppelen aan een Synapse-werkruimte](../quickstart-power-bi.md) voor meer informatie. 


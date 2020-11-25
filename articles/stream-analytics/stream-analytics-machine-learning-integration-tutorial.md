---
title: Azure Stream Analytics integratie met Azure Machine Learning Studio (klassiek)
description: In dit artikel wordt beschreven hoe u snel een eenvoudige Azure Stream Analytics-taak kunt instellen die Azure Machine Learning Studio (klassiek) integreert met behulp van een door de gebruiker gedefinieerde functie.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 08/12/2020
ms.custom: seodec18
ms.openlocfilehash: 9b08b59090d9dd23405f8a0ba86ce608e3a64902
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011384"
---
# <a name="do-sentiment-analysis-with-azure-stream-analytics-and-azure-machine-learning-studio-classic"></a>Sentiment analyse met Azure Stream Analytics en Azure Machine Learning Studio (klassiek)

Dit artikel laat u zien hoe u een eenvoudige Azure Stream Analytics-taak kunt instellen die gebruikmaakt van Azure Machine Learning Studio (klassiek) voor sentiment analyse. U gebruikt een studio (klassiek) sentiment Analytics-model van de Cortana Intelligence Gallery voor het analyseren van streaming-tekst gegevens en het bepalen van de sentiment Score.

> [!TIP]
> Het is raadzaam om [Azure machine learning UDFs](machine-learning-udf.md) te gebruiken in plaats van Azure machine learning Studio (klassiek) UDF voor verbeterde prestaties en betrouw baarheid.

U kunt de informatie in dit artikel over de volgende scenario's gebruiken:

* Het analyseren van real-time sentiment voor het streamen van Twitter-gegevens.
* Het analyseren van records van klant chats met ondersteunings personeel.
* Opmerkingen over forums, blogs en Video's evalueren.
* Veel andere scenario's in realtime, voorspellende scores.

De streaming Analytics-taak die u maakt, past het sentiment Analytics-model toe als een door de gebruiker gedefinieerde functie (UDF) op de voorbeeld tekst gegevens uit de Blob-opslag. De uitvoer (het resultaat van de analyse van de sentiment) wordt geschreven naar dezelfde Blob-opslag in een ander CSV-bestand. 

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u het volgende hebt voordat u begint:

* Een actief Azure-abonnement.

* Een CSV-bestand met enkele Twitter-gegevens. U kunt een voorbeeld bestand downloaden van [github](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/sampleinput.csv)of u kunt uw eigen bestand maken. In een praktijk scenario haalt u de gegevens rechtstreeks op uit een Twitter-gegevens stroom.

## <a name="create-a-storage-container-and-upload-the-csv-input-file"></a>Een opslag container maken en het CSV-invoer bestand uploaden

In deze stap uploadt u een CSV-bestand naar uw opslag container.

1. Selecteer in de Azure Portal **een resource**-  >  **opslag**  >  **account** maken.

2. Vul de volgende gegevens in op het tabblad *basis principes* en behoud de standaard waarden voor de resterende velden:

   |Veld  |Waarde  |
   |---------|---------|
   |Abonnement|Kies uw abonnement.|
   |Resourcegroep|Kies uw resource groep.|
   |Naam van het opslagaccount|Voer een naam in voor het opslagaccount. De naam moet uniek zijn binnen Azure.|
   |Locatie|Kies een locatie. Alle resources moeten dezelfde locatie gebruiken.|
   |Soort account|BlobStorage|

   ![Details van opslag account opgeven](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account1.png)

3. Selecteer **Controleren + maken**. Selecteer vervolgens **maken** om uw opslag account te implementeren.

4. Wanneer de implementatie is voltooid, gaat u naar uw opslag account. Klik onder **Blob service** op **Containers**. Selecteer vervolgens **+ container** om een nieuwe container te maken.

   ![BLOB storage-container maken voor invoer](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account2.png)

5. Geef een naam op voor de container en controleer of het **niveau voor open bare toegang** is ingesteld op **privé**. Als u gereed bent, selecteert u **Maken**.

   ![Details van BLOB-container opgeven](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account3.png)

6. Navigeer naar de zojuist gemaakte container en selecteer **uploaden**. Upload het **sampleinput.csv** bestand dat u eerder hebt gedownload.

   ![De knop uploaden voor een container](./media/stream-analytics-machine-learning-integration-tutorial/create-sa-upload-button.png)

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Voeg het sentiment Analytics-model toe vanuit de Cortana Intelligence Gallery

Nu de voorbeeld gegevens zich in een BLOB bevindt, kunt u het sentiment-analyse model inschakelen in Cortana Intelligence Gallery.

1. Ga naar de pagina [Predictive sentiment Analytics model](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) in de Cortana Intelligence Gallery.  

2. Selecteer **openen in Studio (klassiek)**.  
   
   ![Open Studio (klassiek) Stream Analytics Azure Machine Learning Studio (klassiek)](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. Meld u aan om naar de werk ruimte te gaan. Selecteer een locatie.

4. Selecteer **uitvoeren** onder aan de pagina. Het proces wordt uitgevoerd. dit duurt ongeveer een minuut.

   ![Experiment uitvoeren in Studio (klassiek)](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-run-experiment.png)  

5. Nadat het proces is uitgevoerd, selecteert u **webservice implementeren** onder aan de pagina.

   ![Experiment in Studio (klassiek) implementeren als een webservice](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-deploy-web-service.png)  

6. Selecteer de knop **testen** om te controleren of het sentiment Analytics-model gereed is voor gebruik. Geef tekst invoer op zoals ' Ik hou van micro soft '.

   ![Test experiment in Studio (klassiek)](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test.png)  

   Als de test werkt, ziet u een resultaat zoals in het volgende voor beeld:

   ![Test resultaten in Studio (klassiek)](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test-results.png)  

7. Selecteer in de kolom **apps** de koppeling **Excel 2010 of een eerdere werkmap** om een Excel-werkmap te downloaden. De werkmap bevat de API-sleutel en de URL die u later nodig hebt om de Stream Analytics-taak in te stellen.

    ![Stream Analytics Azure Machine Learning Studio (klassiek), beknopt](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  

## <a name="create-a-stream-analytics-job-that-uses-the-studio-classic-model"></a>Een Stream Analytics-taak maken die gebruikmaakt van het model studio (klassiek)

U kunt nu een Stream Analytics-taak maken die de voorbeeld tweets leest uit het CSV-bestand in Blob Storage.

### <a name="create-the-job"></a>De taak maken

Ga naar de [Azure Portal](https://portal.azure.com) en maak een stream Analytics taak. Als u niet bekend bent met dit proces, raadpleegt u [een stream Analytics-taak maken met behulp van de Azure Portal](stream-analytics-quick-create-portal.md).

### <a name="configure-the-job-input"></a>De taak invoer configureren

De taak haalt de invoer op uit het CSV-bestand dat u eerder hebt geüpload naar de Blob-opslag.

1. Ga naar de Stream Analytics-taak. Selecteer de optie **invoer** onder **taak topologie**. Selecteer **Stream-invoer**  > **Blob-opslag** toevoegen.

2. Vul de **Blob Storage** gegevens in met de volgende waarden:

   |Veld  |Waarde  |
   |---------|---------|
   |Invoeralias|Geef uw invoer een naam. Onthoud deze alias voor wanneer u uw query schrijft.|
   |Abonnement|Selecteer uw abonnement.|
   |Storage-account|Selecteer het opslag account dat u in de vorige stap hebt gemaakt.|
   |Container|Selecteer de container die u in de vorige stap hebt gemaakt.|
   |Serialisatie-indeling voor gebeurtenissen|CSV|

3. Selecteer **Opslaan**.

### <a name="configure-the-job-output"></a>De taak uitvoer configureren

De taak verzendt resultaten naar dezelfde Blob-opslag waar de invoer wordt ingevoerd.

1. Ga naar de Stream Analytics-taak. Selecteer de optie **uitvoer** onder **taak topologie**. Selecteer **Add**  >  **Blob-opslag** toevoegen.

2. Vul het **Blob Storage** formulier in met de volgende waarden:

   |Veld  |Waarde  |
   |---------|---------|
   |Invoeralias|Geef uw invoer een naam. Onthoud deze alias voor wanneer u uw query schrijft.|
   |Abonnement|Selecteer uw abonnement.|
   |Storage-account|Selecteer het opslag account dat u in de vorige stap hebt gemaakt.|
   |Container|Selecteer de container die u in de vorige stap hebt gemaakt.|
   |Serialisatie-indeling voor gebeurtenissen|CSV|

3. Selecteer **Opslaan**.

### <a name="add-the-studio-classic-function"></a>De functie Studio (klassiek) toevoegen

Eerder hebt u een studio-model (klassiek) gepubliceerd naar een webservice. Als in dit scenario de stroom analyse taak wordt uitgevoerd, verzendt deze elke voorbeeld Tweet van de invoer naar de webservice voor sentiment analyse. De Studio-webservice (klassiek) retourneert een sentiment ( `positive` , `neutral` of `negative` ) en een kans dat de Tweet positief is.

In deze sectie definieert u een functie in de analyse taak voor streams. De functie kan worden aangeroepen om een Tweet te verzenden naar de webservice en de reactie terug te halen.

1. Zorg ervoor dat u de URL en API-sleutel van de webservice hebt die u eerder in de Excel-werkmap hebt gedownload.

2. Ga naar de Stream Analytics-taak. Selecteer vervolgens **functies**  >  **+**  >  **Azure ml Studio** toevoegen

3. Vul het **Azure machine learning functie** formulier in met de volgende waarden:

   |Veld  |Waarde  |
   |---------|---------|
   | Functiealias | Gebruik de naam `sentiment` en selecteer **Azure machine learning functie-instellingen hand matig opgeven**, waarmee u de URL en de sleutel kunt invoeren.      |
   | URL| Plak de URL van de webservice.|
   |Sleutel | Plak de API-sleutel. |

4. selecteer **Opslaan**.

### <a name="create-a-query-to-transform-the-data"></a>Een query maken om de gegevens te transformeren

Stream Analytics maakt gebruik van een declaratieve SQL-query om de invoer te controleren en te verwerken. In deze sectie maakt u een query die elke Tweet leest uit invoer en vervolgens de functie Studio (klassiek) aanroept om sentiment analyse uit te voeren. De query verzendt vervolgens het resultaat naar de uitvoer die u hebt gedefinieerd (Blob Storage).

1. Ga terug naar het overzicht van de Stream Analytics-taak.

2. Selecteer **Query** onder **Taaktopologie**.

3. Voer de volgende query in:

    ```SQL
    WITH sentiment AS (  
    SELECT text, sentiment1(text) as result 
    FROM <input>  
    )  

    SELECT text, result.[Score]  
    INTO <output>
    FROM sentiment  
    ```    

   Met de query wordt de functie aangeroepen die u eerder hebt gemaakt ( `sentiment` ) om sentiment analyse uit te voeren op elke tweet in de invoer.

4. Selecteer **Opslaan** om de query op te slaan.

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>De Stream Analytics-taak starten en uitvoer controleren

U kunt nu de Stream Analytics-taak starten.

### <a name="start-the-job"></a>Taak starten

1. Ga terug naar het overzicht van de Stream Analytics-taak.

2. Selecteer **beginnen** bovenaan de pagina.

3. Selecteer in **taak starten** de optie **aangepast** en selecteer vervolgens één dag voorafgaand aan het moment waarop u het CSV-bestand naar de Blob-opslag uploadt. Selecteer **Starten** als u klaar bent.  

### <a name="check-the-output"></a>De uitvoer controleren

1. Laat de taak enkele minuten uitvoeren totdat u de activiteit in het vak **controle** ziet.

2. Als u een hulp programma hebt dat u normaal gesp roken gebruikt om de inhoud van de Blob-opslag te controleren, gebruikt u dat hulp programma om de container te onderzoeken. U kunt ook de volgende stappen uitvoeren in de Azure Portal:

      1. Ga in het Azure Portal naar uw opslag account en zoek de container in het account. U ziet twee bestanden in de container: het bestand met het voor beeld-tweets en een CSV-bestand dat door de Stream Analytics-taak wordt gegenereerd.
      2. Klik met de rechter muisknop op het gegenereerde bestand en selecteer vervolgens **downloaden**.

3. Open het gegenereerde CSV-bestand. U ziet iets zoals in het volgende voor beeld:  

   ![Stream Analytics Azure Machine Learning Studio (klassiek), CSV-weer gave](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  

### <a name="view-metrics"></a>Metrische gegevens bekijken

U kunt ook studio-gerelateerde metrische functie gegevens weer geven. De volgende metrische functie gegevens worden weer gegeven in het vak **bewaking** van het taak overzicht:

* Met **functie aanvragen** wordt het aantal aanvragen aangegeven dat is verzonden naar een studio-webservice (klassiek).  
* **Functie gebeurtenissen** geeft het aantal gebeurtenissen in de aanvraag aan. Elke aanvraag van een studio-webservice (Classic) bevat standaard Maxi maal 1.000 gebeurtenissen.

## <a name="next-steps"></a>Volgende stappen

* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Naslaggids voor Azure Stream Analytics Query](/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API en Machine Learning Studio integreren (klassiek)](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)
* [REST API-naslaggids voor Azure Stream Analytics Management](/rest/api/streamanalytics/)
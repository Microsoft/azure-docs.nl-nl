---
title: Activiteiten in een pijplijn vertakken en koppelen met behulp van de Microsoft Azure-portal
description: Leer de stroom van gegevens in Azure Data Factory pijplijn te beheren met behulp van de Azure-portal.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/11/2018
ms.openlocfilehash: 9d8d9af4892001ff1f8f60a92f28aedad14c17cf
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100378601"
---
# <a name="branching-and-chaining-activities-in-an-azure-data-factory-pipeline-using-the-azure-portal"></a>Activiteiten in een Azure Data Factory-pijplijn vertakken en koppelen met behulp van de Azure-portal

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In deze zelfstudie maakt u een Data Factory-pijplijn die enkele van de stroombeheerfuncties demonstreert. Deze pijplijn voert een eenvoudige kopieerbewerking uit van een container in Azure Blob Storage naar een andere container in hetzelfde opslagaccount. Als de kopieerbewerking is geslaagd, worden de details over de geslaagde kopieerbewerking (zoals de hoeveelheid geschreven gegevens) via de pijplijn verzonden in een e-mail met een succesbericht. Als de kopieerbewerking is mislukt, worden de details over de mislukte kopieerbewerking (zoals de foutmelding) via de pijplijn verzonden in een e-mail met de foutmelding. In de zelfstudie ziet u hoe u parameters kunt doorgeven.

Een overzicht van het scenario: ![In het diagram wordt Azure Blob Storage weergegeven. Dit is het doel van een kopie, die bij voltooiing een e-mail met details of bij een fout een e-mail met foutgegevens verzendt.](media/tutorial-control-flow-portal/overview.png)

In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
> * Een data factory maken.
> * Een gekoppelde Azure Storage-service maken
> * Een Azure Blob-gegevensset maken
> * Een pijplijn maken met een kopieeractiviteit en een webactiviteit
> * Verzenden van uitvoer van activiteiten naar latere activiteiten
> * Parameters doorgeven en systeemvariabelen gebruiken
> * Een pijplijnuitvoering starten
> * De uitvoering van de pijplijn en van de activiteit controleren

In deze zelfstudie wordt Azure Portal gebruikt. U kunt andere methoden gebruiken voor interactie met Azure Data Factory. Kijk voor voorbeelden onder 'Quickstarts' (Snelstartgidsen) in de inhoudsopgave.

## <a name="prerequisites"></a>Vereisten

* **Azure-abonnement**. Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
* **Een Azure Storage-account**. U gebruikt de blob-opslag als **bron**-gegevensopslag. Als u geen Azure-opslagaccount hebt, raadpleegt u het artikel [Een opslagaccount maken](../storage/common/storage-account-create.md) om een account te maken.
* **Azure SQL-database**. U gebruikt de database als **sink**-gegevensopslag. Als u geen database in Azure SQL Database hebt, raadpleegt u het artikel [Een database in Azure SQL Database maken](../azure-sql/database/single-database-create-quickstart.md) om te stappen te zien om er een te maken.

### <a name="create-blob-table"></a>Blobtabel maken

1. Start Kladblok. Kopieer de volgende tekst en sla deze op schijf op in het bestand **input.txt**.

    ```
    John,Doe
    Jane,Doe
    ```
2. Gebruik hulpprogramma's zoals [Azure Storage Explorer](https://storageexplorer.com/) om de volgende stappen uit te voeren:
    1. Maak de container **adfv2branch**.
    2. Maak de **invoermap** in de container **adfv2branch**.
    3. Upload het bestand **input.txt** naar de container.

## <a name="create-email-workflow-endpoints"></a>Eindpunten voor de e-mailwerkstroom maken
Al u het verzenden van een e-mail wilt activeren, gebruikt u [Logic Apps](../logic-apps/logic-apps-overview.md) om de werkstroom te definiëren. Zie voor meer informatie over het maken van een Logic App-werkstroom [How to create a logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md) (Het maken van een logische app).

### <a name="success-email-workflow"></a>Werkstroom voor e-mail met succesbericht
Maak een Logic App-werkstroom met de naam `CopySuccessEmail`. Definieer de werkstroomtrigger als `When an HTTP request is received`, en voeg de actie `Office 365 Outlook – Send an email` toe.

![Werkstroom voor e-mail met succesbericht](media/tutorial-control-flow-portal/success-email-workflow.png)

Als aanvraagtrigger vult u het `Request Body JSON Schema` met de volgende JSON:

```json
{
    "properties": {
        "dataFactoryName": {
            "type": "string"
        },
        "message": {
            "type": "string"
        },
        "pipelineName": {
            "type": "string"
        },
        "receiver": {
            "type": "string"
        }
    },
    "type": "object"
}
```

De aanvraag in Logic App Designer moet er uitzien zoals de volgende afbeelding:

![Logic App Designer - aanvraag](media/tutorial-control-flow-portal/logic-app-designer-request.png)

Pas voor de actie **E-mail verzenden** de opmaak van de e-mail aan met behulp van de eigenschappen die in het JSON-schema voor de hoofdtekst van de aanvraag worden doorgegeven. Hier volgt een voorbeeld:

![Logic App Designer - actie e-mail verzenden](media/tutorial-control-flow-portal/send-email-action-2.png)

Sla de werkstroom op. Noteer de URL van de HTTP POST-aanvraag voor uw werkstroom voor de e-mail met het succesbericht:

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

### <a name="fail-email-workflow"></a>Werkstroom voor e-mail met foutbericht
Volg dezelfde stappen om nog een Logic Apps-werkstroom te maken van **CopyFailEmail**. In de aanvraagtrigger is het `Request Body JSON schema` hetzelfde. Wijzig de indeling van uw e-mailbericht net als het `Subject` om er een e-mail met de foutmelding van te maken. Hier volgt een voorbeeld:

![Logic App designer - werkstroom voor e-mailbericht met foutmelding](media/tutorial-control-flow-portal/fail-email-workflow-2.png)

Sla de werkstroom op. Noteer de URL van de HTTP POST-aanvraag voor uw werkstroom voor de e-mail met het foutbericht:

```
//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

Als het goed is, hebt u nu twee werkstroom-URL's:

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000

//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

1. Start de webbrowser **Microsoft Edge** of **Google Chrome**. Op dit moment wordt de Data Factory-gebruikersinterface alleen ondersteund in de webbrowsers Microsoft Edge en Google Chrome.
1. Selecteer in het linkermenu **Een resource maken** > **Gegevens en analyses** > **Data factory**:

   ![Selectie van Data Factory in het deelvenster Nieuw](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Voer op de pagina **Nieuwe data factory** **ADFTutorialDataFactory** in als **naam**.

     ![De pagina Nieuwe data factory](./media/tutorial-control-flow-portal/new-azure-data-factory.png)

   De naam van de Azure-gegevensfactory moet **wereldwijd uniek** zijn. Als u het volgende foutbericht krijgt, wijzigt u de naam van de gegevensfactory (bijvoorbeeld uwnaamADFTutorialDataFactory) en probeert u het opnieuw. Zie het artikel [Data factory - Naamgevingsregels](naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.

   *Data factory-naam 'ADFTutorialDataFactory' is niet beschikbaar.*

3. Selecteer het Azure-**abonnement** waarin u de gegevensfactory wilt maken.
4. Voer een van de volgende stappen uit voor de **Resourcegroep**:

      - Selecteer **Bestaande gebruiken** en selecteer een bestaande resourcegroep in de vervolgkeuzelijst.
      - Selecteer **Nieuwe maken** en voer de naam van een resourcegroep in.   
         
        Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/management/overview.md) voor meer informatie.  
4. Selecteer **V2** als de **versie**.
5. Selecteer de **locatie** voor de gegevensfactory. In de vervolgkeuzelijst worden alleen ondersteunde locaties weergegeven. De gegevensopslagexemplaren (Azure Storage, Azure SQL Database, enzovoort) en berekeningen (HDInsight, enzovoort) die worden gebruikt in Data Factory, kunnen zich in andere regio's bevinden.
6. Selecteer **Vastmaken aan dashboard**.     
7. Klik op **Create**.      
8. Op het dashboard ziet u de volgende tegel met de status: **Gegevensfactory implementeren**.

    ![tegel met de status 'gegevensfactory implementeren'](media/tutorial-control-flow-portal/deploying-data-factory.png)
9. Na het aanmaken ziet u de pagina **Data Factory** zoals weergegeven in de afbeelding.

   ![Startpagina van de gegevensfactory](./media/tutorial-control-flow-portal/data-factory-home-page.png)
10. Klik op de tegel **Author & Monitor** om de gebruikersinterface (UI) van Azure Data Factory te openen in een afzonderlijk tabblad.


## <a name="create-a-pipeline"></a>Een pijplijn maken
In deze stap maakt u een pijplijn met één kopieeractiviteit en twee webactiviteiten. U gebruikt de volgende functies om de pijplijn te maken:

- Parameters voor de pijplijn die wordt gebruikt voor gegevenssets.
- Webactiviteit waarmee Logic Apps-werkstromen worden aangeroepen voor het verzenden van e-mail met een succesbericht of foutmelding.
- Een activiteit verbinden met een andere activiteit (bij slagen en mislukken)
- De uitvoer van een activiteit gebruiken als invoer voor de volgende activiteit

1. Klik op de pagina **Aan de slag** van de Data Factory-UI op de tegel **Pijplijn maken**.  

   ![Pagina Aan de slag](./media/tutorial-control-flow-portal/get-started-page.png)
3. Ga in het venster Eigenschappen voor de pijplijn naar het tabblad **Parameters** en gebruik de knop **Nieuw** om de volgende drie parameters toe te voegen van het type Tekenreeks: sourceBlobContainer, sinkBlobContainer en ontvanger.

    - **sourceBlobContainer** - parameter in de pijplijn die wordt gebruikt voor de bron-blob-gegevensset.
    - **sinkBlobContainer** - parameter in de pijplijn die wordt gebruikt voor de sink-blob-gegevensset
    - **ontvanger** - deze parameter wordt gebruikt voor de twee webactiviteiten in de pijplijn waarmee een e-mail met een succesbericht of foutmelding wordt verzonden naar de ontvanger die met deze parameter wordt gespecificeerd.

   ![Menu Nieuwe pijplijn](./media/tutorial-control-flow-portal/pipeline-parameters.png)
4. Vouw in de **Activiteiten**-werkset de optie **Gegevensstroom** uit. Gebruik vervolgens slepen-en-neerzetten om de **kopieeractiviteit** te verplaatsen naar het ontwerpoppervlak voor pijplijnen.

   ![De kopieeractiviteit slepen en neerzetten](./media/tutorial-control-flow-portal/drag-drop-copy-activity.png)
5. Ga in het venster **Eigenschappen** voor de **kopieeractiviteit** onderaan naar het tabblad **Bron** en klik op **+ Nieuw**. In deze stap maakt u een brongegevensset voor de kopieeractiviteit.

   ![Schermopname die laat zien hoe u een brongegevensset maakt voor de kopieeractiviteit.](./media/tutorial-control-flow-portal/new-source-dataset-button.png)
6. Selecteer in het venster **Nieuwe gegevensset** de optie **Azure Blob Storage** en klik op **Voltooien**.

   ![Azure Blob Storage selecteren](./media/tutorial-control-flow-portal/select-azure-blob-storage.png)
7. U ziet een nieuw **tabblad** met de naam **AzureBlob1**. Wijzig de naam van de gegevensset in **SourceBlobDataset**.

   ![Algemene instellingen voor gegevenssets](./media/tutorial-control-flow-portal/dataset-general-page.png)
8. Ga in het venster **Eigenschappen** naar het tabblad **Verbinding** en klik op Nieuw voor de **Gekoppelde service**. In deze stap maakt u een gekoppelde service om uw Azure Storage-account te koppelen aan de gegevensfactory.

   ![Gegevenssetverbinding - nieuwe gekoppelde service](./media/tutorial-control-flow-portal/dataset-connection-new-button.png)
9. Voer in het venster **Nieuwe gekoppelde service** de volgende stappen uit:

    1. Voer **AzureStorageLinkedService** in als **Naam**.
    2. Selecteer uw Azure-opslagaccount als **Naam van opslagaccount**.
    3. Klik op **Opslaan**.

   ![Nieuwe gekoppelde Azure Storage-service](./media/tutorial-control-flow-portal/new-azure-storage-linked-service.png)
12. Voer `@pipeline().parameters.sourceBlobContainer` in als de map en `emp.txt` als de bestandsnaam. U gebruikt de pijplijnparameter sourceBlobContainer om het bestandspad voor de gegevensset in te stellen.

    ![Instellingen voor de brongegevensset](./media/tutorial-control-flow-portal/source-dataset-settings.png)

13. Ga naar het tabblad **Pijplijn** (of) klik op de pijplijn in de structuurweergave. Controleer of **SourceBlobDataset** is geselecteerd als **Brongegevensset**.
      
   ![Brongegevensset](./media/tutorial-control-flow-portal/pipeline-source-dataset-selected.png)

13. Ga in het venster Eigenschappen naar het tabblad **Sink** en klik op **+ Nieuw** voor **Sink-gegevensset**. In deze stap maakt u een sink-gegevensset voor de kopieeractiviteit. Dit doet u op dezelfde manier als waarop u de brongegevensset hebt gemaakt.

    ![Knop Nieuwe sink-gegevensset](./media/tutorial-control-flow-portal/new-sink-dataset-button.png)
14. Selecteer in het venster **Nieuwe gegevensset** de optie **Azure Blob Storage** en klik op **Voltooien**.
15. Voer op de pagina **Algemene instellingen** voor de gegevensset **SinkBlobDataset** in als **Naam**.
16. Ga naar het tabblad **Verbinding** en voer de volgende stappen uit:

    1. Selecteer **AzureStorageLinkedService** bij **Gekoppelde service**.
    2. Voer `@pipeline().parameters.sinkBlobContainer` als de map.
    1. Voer `@CONCAT(pipeline().RunId, '.txt')` in als bestandsnaam. Voor de expressie wordt de id van de huidige pijplijnuitvoering gebruikt als de bestandsnaam. Zie [Systeemvariabelen](control-flow-system-variables.md) en [Expressietaal](control-flow-expression-language-functions.md) voor de lijst met ondersteunde systeemvariabelen en expressies.

        ![Instellingen voor sink-gegevenssets](./media/tutorial-control-flow-portal/sink-dataset-settings.png)
17. Ga bovenaan naar het tabblad **Pijplijn**. Vouw **Algemeen** uit in de **Activiteiten**-werkset. Gebruik vervolgens slepen-en-neerzetten om een **webactiviteit** te verplaatsen naar het ontwerpoppervlak voor pijplijnen. Stel de naam van de activiteit in op **SendSuccessEmailActivity**. De Web Activity (webactiviteit) staat een aanroep toe naar elk REST-eindpunt. Zie voor meer informatie over de activiteit [Webactiviteit](control-flow-web-activity.md). Deze pijplijn gebruikt een webactiviteit voor het aanroepen van de Logic Apps-e-mailwerkstroom.

    ![De eerste webactiviteit slepen en neerzetten](./media/tutorial-control-flow-portal/success-web-activity-general.png)
18. Ga vanuit het tabblad **Algemeen** naar het tabblad **Instellingen** en voer de volgende stappen uit:
    1. Geef bij **URL** de URL voor de Logic Apps-werkstroom op waarmee de e-mail met het succesbericht wordt verzonden.  
    2. Selecteer **POST** als **Methode**.
    3. Klik op de koppeling **+ Koptekst toevoegen** in de sectie **Kopteksten**.
    4. Voeg een koptekst **Inhoudstype** toe en stel deze in op **application/json**.
    5. Geef de volgende JSON op als **Hoofdtekst**.

        ```json
        {
            "message": "@{activity('Copy1').output.dataWritten}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
        }
        ```
        De hoofdtekst van het bericht bevat de volgende eigenschappen:

       - Bericht: geeft de waarde van `@{activity('Copy1').output.dataWritten` door. Leest een eigenschap van de vorige kopieeractiviteit en geeft de waarde van dataWritten door. In het geval waarin het kopiëren mislukt, wordt de uitvoer van de fout doorgegeven in plaats van `@{activity('CopyBlobtoBlob').error.message`.
       - Naam data factory: geeft de waarde van `@{pipeline().DataFactory}` door. Dit is een systeemvariabele, zodat u toegang hebt tot de bijbehorende data factory-naam. Zie voor een lijst van systeemvariabelen het artikel [System Variables](control-flow-system-variables.md) (Systeemvariabelen).
       - Naam pijplijn: geeft de waarde van `@{pipeline().Pipeline}` door. Dit is ook een systeemvariabele, zodat u toegang hebt tot de bijbehorende pijplijnnaam.
       - Ontvanger: geeft de waarde van "\@pipeline().parameters.receiver") door. Toegang tot de pijplijnparameters.

         ![Instellingen voor de eerste webactiviteit](./media/tutorial-control-flow-portal/web-activity1-settings.png)         
19. Verbind de **kopieeractiviteit** met de **webactiviteit** door de groene knop naast de kopieeractiviteit te slepen en neer te zetten op de webactiviteit.

    ![Kopieeractiviteit verbinden met de eerste webactiviteit](./media/tutorial-control-flow-portal/connect-copy-web-activity1.png)
20. Gebruik slepen-en-neerzetten om nog een **Webactiviteit** uit de Activiteiten-werkset te verplaatsen naar het ontwerpoppervlak voor pijplijnen, en stel de **naam** in op **SendFailureEmailActivity**.

    ![Naam van de tweede webactiviteit](./media/tutorial-control-flow-portal/web-activity2-name.png)
21. Ga naar het tabblad **Instellingen** en voer de volgende stappen uit:

    1. Geef bij **URL** de URL voor de Logic Apps-werkstroom op waarmee de e-mail met de foutmelding wordt verzonden.  
    2. Selecteer **POST** als **Methode**.
    3. Klik op de koppeling **+ Koptekst toevoegen** in de sectie **Kopteksten**.
    4. Voeg een koptekst **Inhoudstype** toe en stel deze in op **application/json**.
    5. Geef de volgende JSON op als **Hoofdtekst**.

        ```json
        {
            "message": "@{activity('Copy1').error.message}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
        }
        ```

        ![Instellingen voor de tweede webactiviteit](./media/tutorial-control-flow-portal/web-activity2-settings.png)         
22. Selecteer **kopieeractiviteit** in de pijplijnontwerper en klik op de knop **+->** . Selecteer vervolgens **Fout**.  

    ![Schermopname die laat zien hoe u Fout selecteert in de kopieeractiviteit in de pijplijnontwerper.](./media/tutorial-control-flow-portal/select-copy-failure-link.png)
23. Sleep de **rode** knop naast de kopieeractiviteit naar de tweede webactiviteit **SendFailureEmailActivity**. U kunt de activiteiten verplaatsen, zodat de pijplijn er uitziet zoals in de volgende afbeelding:

    ![Volledige pijplijn met alle activiteiten](./media/tutorial-control-flow-portal/full-pipeline.png)
24. Klik in de werkbalk op de knop **Valideren** om de pijplijn te valideren. Sluit het venster met de **uitvoergegevens van de pijplijnvalidatie** door op de knop **>>** te klikken.

    ![Pijplijn valideren](./media/tutorial-control-flow-portal/validate-pipeline.png)
24. Als u entiteiten (gegevenssets, pijplijnen enzovoort) wilt publiceren in de Data Factory-service, selecteert u **Alles publiceren**. Wacht tot u het bericht **Gepubliceerd** ziet.

    ![Publiceren](./media/tutorial-control-flow-portal/publish-button.png)

## <a name="trigger-a-pipeline-run-that-succeeds"></a>Een pijplijnuitvoering activeren die slaagt
1. Klik in de werkbalk op **Activeren** en klik op **Nu activeren** om een pijplijnuitvoering te **activeren**.

    ![Een pijplijnuitvoering activeren](./media/tutorial-control-flow-portal/trigger-now-menu.png)
2. Voer in het venster **Pijplijnuitvoering** de volgende stappen uit:

    1. Voer **adftutorial/adfv2branch/input** in als de parameter **sourceBlobContainer**.
    2. Voer **adftutorial/adfv2branch/output** in als de parameter **sinkBlobContainer**.
    3. Voer een **e-mailadres** van de **ontvanger** in.
    4. Klik op **Voltooien**

        ![Parameters voor pijplijnuitvoeringen](./media/tutorial-control-flow-portal/pipeline-run-parameters.png)

## <a name="monitor-the-successful-pipeline-run"></a>De geslaagde pijplijnuitvoering controleren

1. Ga naar het tabblad **Controleren** aan de linkerkant om de pijplijnuitvoering te controleren. U ziet de pijplijnuitvoering die handmatig door u is geactiveerd. Gebruik de knop **Vernieuwen** om de lijst te vernieuwen.

    ![Geslaagde pijplijnuitvoering](./media/tutorial-control-flow-portal/monitor-success-pipeline-run.png)
2. Klik op de eerste koppeling in de kolom **Acties** om de **uitvoeringen van activiteit weer te geven** die zijn gekoppeld aan deze pijplijnuitvoering. Als u wilt terugkeren naar de vorige weergave, klikt u bovenaan op **Pijplijnen**. Gebruik de knop **Vernieuwen** om de lijst te vernieuwen.

    ![Schermopname die laat zien hoe u de lijst met uitvoering van activiteiten kunt bekijken.](./media/tutorial-control-flow-portal/activity-runs-success.png)

## <a name="trigger-a-pipeline-run-that-fails"></a>Een pijplijnuitvoering activeren die mislukt
1. Ga naar het tabblad **Bewerken** aan de linkerkant.
2. Klik in de werkbalk op **Activeren** en klik op **Nu activeren** om een pijplijnuitvoering te **activeren**.
3. Voer in het venster **Pijplijnuitvoering** de volgende stappen uit:

    1. Voer **adftutorial/dummy/input** in als de parameter **sourceBlobContainer**. Controleer of de dummymap niet bestaat in de container adftutorial.
    2. Voer **adftutorial/dummy/output** in als de parameter **sinkBlobContainer**.
    3. Voer een **e-mailadres** van de **ontvanger** in.
    4. Klik op **Voltooien**.

## <a name="monitor-the-failed-pipeline-run"></a>De mislukte pijplijnuitvoering controleren

1. Ga naar het tabblad **Controleren** aan de linkerkant om de pijplijnuitvoering te controleren. U ziet de pijplijnuitvoering die handmatig door u is geactiveerd. Gebruik de knop **Vernieuwen** om de lijst te vernieuwen.

    ![Mislukte pijplijnuitvoering](./media/tutorial-control-flow-portal/monitor-failure-pipeline-run.png)
2. Klik op de **Foutkoppeling** voor de pijplijnuitvoering voor de details over de fout.

    ![Pijplijnfout](./media/tutorial-control-flow-portal/pipeline-error-message.png)
2. Klik op de eerste koppeling in de kolom **Acties** om de **uitvoeringen van activiteit weer te geven** die zijn gekoppeld aan deze pijplijnuitvoering. Gebruik de knop **Vernieuwen** om de lijst te vernieuwen. U ziet dat de kopieeractiviteit in de pijplijn is mislukt. De e-mail met de foutmelding is via de webactiviteit verzonden naar de opgegeven server.

    ![Uitvoering van activiteiten](./media/tutorial-control-flow-portal/activity-runs-failure.png)
4. Klik op de **Foutkoppeling** in de kolom **Acties** voor de details over de fout.

    ![Fout in uitvoeringen van activiteit](./media/tutorial-control-flow-portal/activity-run-error.png)

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u de volgende stappen uitgevoerd:

> [!div class="checklist"]
> * Een data factory maken.
> * Een gekoppelde Azure Storage-service maken
> * Een Azure Blob-gegevensset maken
> * Een pijplijn met een kopieeractiviteit en een webactiviteit maken
> * Verzenden van uitvoer van activiteiten naar latere activiteiten
> * Parameters doorgeven en systeemvariabelen gebruiken
> * Een pijplijnuitvoering starten
> * De uitvoering van de pijplijn en van de activiteit controleren

U kunt nu doorgaan naar de sectie Concepten voor meer informatie over Azure Data Factory.
> [!div class="nextstepaction"]
>[Pijplijnen en activiteiten](concepts-pipelines-activities.md)

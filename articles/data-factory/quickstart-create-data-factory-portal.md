---
title: Een Azure data factory maken met de gebruikersinterface van Azure Data Factory
description: Maak een gegevensfactory met een pijplijn die gegevens kopieert van de ene locatie naar de andere in een Azure Blob-opslag.
author: linda33wj
ms.service: data-factory
ms.topic: quickstart
ms.date: 12/14/2020
ms.author: jingwang
ms.openlocfilehash: 8b33005b7f0d08e6da963457813b012199d89f5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104655030"
---
# <a name="quickstart-create-a-data-factory-by-using-the-azure-data-factory-ui"></a>Quickstart: Een gegevensfactory maken met de Azure Data Factory-UI 

> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Huidige versie](quickstart-create-data-factory-portal.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In deze quickstart wordt beschreven hoe u de Azure Data Factory-UI kunt gebruiken om een Azure-gegevensfactory te maken en te controleren. Met de pijplijn die u in deze data factory maakt, worden gegevens *gekopieerd* van één map naar een andere map in een Azure Blob-opslag. Als u gegevens wilt *Transformeren* met behulp van Azure Data Factory, raadpleegt u [gegevens stroom toewijzen](concepts-data-flow-overview.md).

> [!NOTE]
> Als u niet bekend bent met Azure Data Factory, raadpleegt u eerst de [Inleiding voor Azure Data Factory](introduction.md) voordat u deze quickstart uitvoert. 

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="video"></a>Video 
Bekijk deze video voor een beter begrip van de Data Factory-UI: 
>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Visually-build-pipelines-for-Azure-Data-Factory-v2/Player]

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

1. Start de webbrowser **Microsoft Edge** of **Google Chrome**. Op dit moment wordt de Data Factory-gebruikersinterface alleen ondersteund in de webbrowsers Microsoft Edge en Google Chrome.
1. Ga naar de [Azure Portal](https://portal.azure.com). 
1. Selecteer **Een resource maken** in het menu van de Azure-portal.
1. Selecteer **Integratie** en selecteer vervolgens **Data Factory**. 
   
   :::image type="content" source="./media/doc-common-process/new-azure-data-factory-menu.png" alt-text="Data Factory selectie in het nieuwe deel venster.":::

1. Selecteer op de pagina **Data factory maken** op het tabblad **Basisbeginselen** het Azure-**abonnement** waarin u de data factory wilt maken.
1. Voer een van de volgende stappen uit voor **Resourcegroep**:

    a. Selecteer een bestaande resourcegroep in de vervolgkeuzelijst.

    b. Selecteer **Nieuwe maken** en voer de naam van een nieuwe resourcegroep in.
    
    Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/management/overview.md) voor meer informatie. 

1. Selecteer bij **Regio** de locatie voor de data factory.

   De lijst bevat alleen locaties die worden ondersteund door Data Factory en waar uw Azure Data Factory-metagegevens worden opgeslagen. De bijbehorende gegevensarchieven (zoals Azure Storage en Azure SQL Database) en berekeningen (zoals Azure HDInsight) die Data Factory gebruikt, kunnen in andere regio's worden uitgevoerd.
 
1. Voer **ADFTutorialDataFactory** in bij **Naam**.
   De naam van de Azure-gegevensfactory moet *wereldwijd uniek* zijn. Als u het volgende foutbericht ziet, wijzigt u de naam van de data factory (bijvoorbeeld **&lt;uwnaam&gt;ADFTutorialDataFactory**) en probeert u het opnieuw. Zie het artikel [Data factory - Naamgevingsregels](naming-rules.md) voor naamgevingsregels voor Data Factory-artefacten.

    :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Nieuw data factory fout bericht voor dubbele naam.":::

1. Selecteer **V2** als **Versie**.

1. Selecteer **Volgende: Git-configuratie**, en selecteer vervolgens het selectievakje **Git later configureren**.

1. Selecteer **Controleren en maken**, en selecteer **Maken** nadat de validatie is voltooid. Nadat het maken is voltooid, selecteert u **Ga naar resource** om naar de pagina **Data Factory** te gaan. 

1. Selecteer de tegel **Maken en controleren** om de UI (gebruikersinterface) van Azure Data Factory te openen op een afzonderlijk browsertabblad.
   
    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Start pagina voor de Azure Data Factory, met de tegel Author & monitor.":::
   
   > [!NOTE]
   > Als u ziet dat de webbrowser vastloopt bij Autoriseren, schakelt u het selectievakje **Cookies van derden en sitegegevens blokkeren**  uit. U kunt het selectievakje ook ingeschakeld laten, een uitzondering maken voor **login.microsoftonline.com** en de app opnieuw openen.
   

## <a name="create-a-linked-service"></a>Een gekoppelde service maken
In deze procedure maakt u een gekoppelde service om uw Azure Storage-account te koppelen aan de data factory. De gekoppelde service beschikt over de verbindingsgegevens die de Data Factory-service tijdens runtime gebruikt om er een verbinding mee tot stand te brengen.

1. Open op de pagina van de Azure Data Factory-UI het tabblad [**Beheren**](./author-management-hub.md) in het linkerdeelvenster.

1. Selecteer op de pagina Gekoppelde services **+Nieuw** om een nieuwe gekoppelde service te maken.

   :::image type="content" source="./media/doc-common-process/new-linked-service.png" alt-text="Nieuwe gekoppelde service.":::
   
1. Selecteer op de pagina **Nieuwe gekoppelde service** de optie **Azure Blob-opslag** en selecteer vervolgens **Doorgaan**. 

1. Voer de volgende stappen uit op de pagina Nieuwe gekoppelde service (Azure Blob Storage): 

   a. Voer bij **Naam** **AzureStorageLinkedService** in.

   b. Selecteer bij **Naam van opslagaccount** uw Azure-opslagaccount.

   c. Selecteer **Verbinding testen** om te bevestigen dat de Data Factory-service verbinding mag maken met het opslagaccount. 

   d. Selecteer **Maken** om de gekoppelde service op te slaan. 

      :::image type="content" source="./media/quickstart-create-data-factory-portal/linked-service.png" alt-text="Gekoppelde service.":::


## <a name="create-datasets"></a>Gegevenssets maken
In deze procedure maakt u twee gegevenssets: **InputDataset** en **OutputDataset**. Deze gegevenssets zijn van het type **AzureBlob**. Ze verwijzen naar de gekoppelde Azure Storage-service die u in de vorige sectie hebt gemaakt. 

De invoergegevensset vertegenwoordigt de brongegevens in de invoermap. In de definitie van de invoergegevensset geeft u de blob-container (**adftutorial**) en de map (**invoer**) op, en het bestand (**emp.txt**) dat de brongegevens bevat. 

De uitvoergegevensset vertegenwoordigt de gegevens die worden gekopieerd naar de bestemming. In de definitie van de uitvoergegevensset geeft u de blob-container (**adftutorial**) en de map (**uitvoer**) op, en het bestand waarin de brongegevens zijn gekopieerd. Elke uitvoering van een pijplijn heeft een unieke id die eraan is gekoppeld. U hebt toegang tot deze id via de systeemvariabele **RunId**. De naam van het uitvoerbestand wordt dynamisch geëvalueerd op basis van de run-id van de pijplijn.   

In de instellingen voor de gekoppelde service hebt u het Azure Storage-account opgegeven dat de brongegevens bevat. In de instellingen voor de brongegevensset geeft u de precieze locatie van de brongegevens op (blob-container, map en bestand). In de instellingen voor de sink-gegevensset geeft u de locatie op waarnaar de gegevens worden gekopieerd (blob-container, map en bestand). 

1. Selecteer het tabblad **Maken** in het linkerdeelvenster.

1. Selecteer de knop **+** (plus) en vervolgens **Gegevensset**.

   :::image type="content" source="./media/quickstart-create-data-factory-portal/new-dataset-menu.png" alt-text="Menu voor het maken van een gegevensset.":::

1. Selecteer op de pagina **Nieuwe gegevensset** de optie **Azure Blob-opslag** en selecteer **Doorgaan**. 

1. Selecteer op de pagina **Indeling selecteren** het indelingstype van uw gegevens en selecteer **Doorgaan**. Selecteer in dit geval **Binair** wanneer u bestanden ongewijzigd kopieert zonder de inhoud te parseren.

   :::image type="content" source="./media/quickstart-create-data-factory-portal/select-format.png" alt-text="Indeling selecteren.":::   
   
1. Voer de volgende stappen uit op de pagina **Eigenschappen instellen**:

    a. Voer **InputDataset** in onder **Naam**. 

    b. Selecteer **AzureStorageLinkedService** bij **Linked service**.

    c. Selecteer de knop **Bladeren** bij **Bestandspad**.

    d. Ga in het venster **Een bestand of map kiezen** naar de map **invoer** in de container **adftutorial**, selecteer het bestand **emp.txt** en selecteer vervolgens **OK**.
    
    e. Selecteer **OK**.   

    :::image type="content" source="./media/quickstart-create-data-factory-portal/set-properties-for-inputdataset.png" alt-text="Eigenschappen instellen voor input DataSet.":::

1. Herhaal de stappen om de uitvoergegevensset te maken:  

    a. Selecteer de knop **+** (plus) en vervolgens **Gegevensset**.

    b. Selecteer op de pagina **Nieuwe gegevensset** de optie **Azure Blob-opslag** en selecteer **Doorgaan**.

    c. Selecteer op de pagina **Indeling selecteren** het indelingstype van uw gegevens en selecteer **Doorgaan**.

    d. Geef op de pagina **Eigenschappen instellen** de naam **OutputDataset** op. Selecteer **AzureStorageLinkedService** als gekoppelde service.

    e. Voer **adftutorial/output** in onder **Bestandspad**. Als de map **output** niet bestaat, wordt deze in runtime gemaakt door de kopieeractiviteit.

    f. Selecteer **OK**.   

    :::image type="content" source="./media/quickstart-create-data-factory-portal/set-properties-for-outputdataset.png" alt-text="Eigenschappen instellen voor output DataSet.":::    

## <a name="create-a-pipeline"></a>Een pijplijn maken 
In deze procedure maakt en valideert u een pijplijn via een kopieeractiviteit die gebruikmaakt van de invoer- en uitvoergegevenssets. Met de kopieeractiviteit worden gegevens uit het bestand dat is opgegeven bij de instellingen voor de invoergegevensset gekopieerd naar het bestand dat is opgegeven in de instellingen voor de uitvoergegevensset. Als in de invoergegevensset alleen een map is opgegeven (en geen bestandsnaam), worden met de kopieeractiviteit alle bestanden in de bronmap gekopieerd naar de bestemming. 

1. Selecteer de knop **+** (plus) en selecteer vervolgens **Pijplijn**. 

1. Geef bij **Eigenschappen** op het tabblad Algemeen **CopyPipeline** op als **Naam**. Vouw vervolgens het deelvenster samen door in de rechterbovenhoek op het pictogram Eigenschappen te klikken.

1. Open de werkset **Activiteiten** en vouw **Verplaatsen en transformeren** uit. Sleep de activiteit **Gegevens kopiëren** vanuit de werkset **Activiteiten** naar het ontwerpoppervlak voor pijplijnen. U kunt ook zoeken naar activiteiten in de werkset **Activiteiten**. Geef **CopyFromBlobToBlob** op bij **Naam**.

    :::image type="content" source="./media/quickstart-create-data-factory-portal/copy-activity.png" alt-text="Een activiteit voor het kopiëren van gegevens maken.":::   

1. Ga naar het tabblad **Bron** in de instellingen voor de kopieeractiviteit en selecteer **InputDataset** als de **brongegevensset**.

1. Ga naar het tabblad **Sink** in de instellingen voor de kopieeractiviteit en selecteer **OutputDataset** bij **Sink-gegevensset**.

1. Klik in de pijplijnwerkbalk boven het canvas op **Valideren** om de instellingen voor de pijplijn te valideren. Controleer of de pijplijn is gevalideerd. Als u de validatie-uitvoer wilt sluiten, selecteert u in de rechterbovenhoek de knop Valideren. 

   :::image type="content" source="./media/quickstart-create-data-factory-portal/pipeline-validate.png"   alt-text="Een pijp lijn valideren.":::

## <a name="debug-the-pipeline"></a>Fouten opsporen in de pijplijn
In deze stap spoort u fouten op in de pijplijn voordat u deze implementeert in Data Factory. 

1. Klik in de pijplijnwerkbalk boven het canvas op **Fouten opsporen** om een testuitvoering te activeren. 
    
1. Controleer of de status van de pijplijnuitvoering onder aan het tabblad **Uitvoer** van de pijplijninstellingen wordt weergegeven. 
 
    :::image type="content" source="./media/quickstart-create-data-factory-portal/pipeline-output.png" alt-text="Uitvoer van pijplijnuitvoering":::

1. Controleer of een uitvoerbestand wordt weergegeven in de **uitvoermap** van de container **adftutorial**. Als de uitvoermap niet bestaat, wordt deze automatisch gemaakt in de Data Factory-service. 

## <a name="trigger-the-pipeline-manually"></a>De pijplijn handmatig activeren
In deze procedure implementeert u entiteiten (gekoppelde services, gegevenssets, pijplijnen) in Azure Data Factory. Vervolgens activeert u handmatig een pijplijnuitvoering. 

1. Voordat u een pijplijn activeert, moet u eerst entiteiten publiceren in Data Factory. Als u wilt publiceren, selecteert u bovenaan de optie **Alles publiceren**. 

    :::image type="content" source="./media/quickstart-create-data-factory-portal/publish-all.png" alt-text="Alles publiceren.":::    

1. Als u de pijplijn handmatig wilt activeren, selecteert u de optie **Trigger toevoegen** op de pijplijnwerkbalk en selecteert u **Nu activeren**. Selecteer **OK** op de pagina **Pijplijnuitvoering**.

## <a name="monitor-the-pipeline"></a>De pijplijn bewaken

1. Ga naar het tabblad **Controleren** aan de linkerkant. Gebruik de knop **Vernieuwen** om de lijst te vernieuwen.

    :::image type="content" source="./media/quickstart-create-data-factory-portal/monitor-trigger-now-pipeline.png" alt-text="Tabblad voor het bewaken van pijplijnuitvoeringen"::: 
1. Selecteer de koppeling **CopyPipeline**. Op deze pagina ziet u de status van de uitvoering van de kopieeractiviteit. 

1. Selecteer de koppeling **Details** (afbeelding van een bril) om details van de kopieerbewerking weer te geven. Zie [Overzicht van kopieeractiviteit](copy-activity-overview.md) voor meer informatie over de eigenschappen. 

   :::image type="content" source="./media/quickstart-create-data-factory-portal/copy-operation-details.png" alt-text="Details van Kopieer bewerking.":::
1. Controleer of een nieuw bestand wordt weergegeven in de **uitvoermap**. 
1. Als u vanuit de weergave **Uitvoeringen van activiteit** wilt terugkeren naar de weergave **Pijplijnuitvoeringen**, selecteert u de koppeling **Alle pijplijnuitvoeringen**. 

## <a name="trigger-the-pipeline-on-a-schedule"></a>De pijplijn activeren volgens een schema
Deze procedure is optioneel in deze zelfstudie. U kunt een *Scheduler-trigger* maken om in te plannen dat de pijplijn periodiek wordt uitgevoerd (elk uur, dagelijks, enzovoort). In deze procedure maakt u een trigger die elke minuut wordt uitgevoerd en stopt op een door u bepaald tijdstip. 

1. Schakel over naar het tabblad **Auteur**. 

1. Ga naar uw pijplijn, selecteer **Trigger toevoegen** op de pijplijnwerkbalk en selecteer **Nieuw/bewerken**. 

1. Selecteer op de pagina **Triggers toevoegen** de optie **Trigger kiezen** en selecteer vervolgens **Nieuw**. 

1. Selecteer op de pagina **Nieuwe trigger** onder **Beëindigen**, de optie **Op datum** en geef een eindtijd op die een paar minuten later is dan de huidige tijd. Selecteer ten slotte **OK**. 

   Aan elke pijplijnuitvoering zijn kosten verbonden. Geef daarom een eindtijd op die slechts enkele minuten later is dan de begintijd. Zorg ervoor dat de eindtijd op dezelfde dag is. Zorg er echter wel voor dat er voldoende tijd is om de pijplijn uit te voeren tussen de publicatietijd en de eindtijd. De trigger gaat pas van kracht nadat u de oplossing hebt gepubliceerd in Data Factory, niet wanneer u de trigger opslaat in de UI. 

1. Schakel op de pagina **Nieuwe trigger** het selectievakje **Geactiveerd** in en selecteer **OK**. 

    :::image type="content" source="./media/quickstart-create-data-factory-portal/trigger-settings-next.png" alt-text="Nieuwe trigger instelling.":::   
1. Lees het waarschuwingsbericht en selecteer **OK**.

1. Selecteer **Alles publiceren** om wijzigingen te publiceren naar Data Factory. 

1. Ga naar het tabblad **Controleren** aan de linkerkant. Selecteer **Vernieuwen** om de lijst te vernieuwen. U ziet dat de pijplijn één keer per minuut wordt uitgevoerd vanaf het moment van publiceren tot de eindtijd. 

   Bekijk de waarden in de kolom **GEACTIVEERD DOOR**. De handmatige triggeruitvoering is afkomstig uit de stap (**Nu activeren**) die u eerder hebt uitgevoerd. 

1. Schakel over naar de weergave **Triggeruitvoeringen**. 

1. Controleer of er tot de opgegeven datum en tijd voor elke pijplijnuitvoering een uitvoerbestand is gemaakt in de **uitvoermap**. 

## <a name="next-steps"></a>Volgende stappen
Met de pijplijn in dit voorbeeld worden gegevens gekopieerd van de ene locatie naar een andere locatie in Azure Blob Storage. Doorloop de [zelfstudies](tutorial-copy-data-portal.md) voor meer informatie over het gebruiken van Data Factory in andere scenario's.

---
title: Een data factory-pijplijn maken met Azure Portal
description: Deze zelfstudie bevat stapsgewijze instructies voor het maken van een data factory met een pijplijn met behulp van Azure Portal. In dit voorbeeld gebruikt de pijplijn de kopieeractiviteit om gegevens vanuit Azure Blob Storage naar Azure SQL Database te kopiëren.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 11/11/2020
ms.author: jingwang
ms.openlocfilehash: ef9ac29735289d5c7a60ff0fca3b9e9f360f6e08
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005126"
---
# <a name="copy-data-from-azure-blob-storage-to-a-database-in-azure-sql-database-by-using-azure-data-factory"></a>Gegevens kopiëren van Azure Blob-opslag naar een database in Azure SQL Database met Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In deze zelfstudie maakt u een data factory met behulp van de Azure Data Factory-gebruikersinterface. Met de pijplijn in deze data factory worden gegevens gekopieerd van Azure Blob Storage naar een database in Azure SQL Database. Het configuratiepatroon in deze zelfstudie geldt voor het kopiëren van een gegevensarchief op basis van bestanden naar een relationeel gegevensarchief. Zie de tabel [Ondersteunde gegevensarchieven](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevensarchieven die worden ondersteund als bron en als sink.

> [!NOTE]
> - Zie [Inleiding tot Azure Data Factory](introduction.md) als u niet bekend bent met Azure Data Factory.

In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
> * Een data factory maken.
> * Een pijplijn met kopieeractiviteit maken.
> * De uitvoering van de pijplijn testen.
> * De pijplijn handmatig activeren.
> * De pijplijn activeren volgens een schema.
> * De uitvoering van de pijplijn en van de activiteit controleren.

## <a name="prerequisites"></a>Vereisten
* **Azure-abonnement**. Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.
* **Azure-opslagaccount**. U gebruikt de blobopslag als *bron*-gegevensopslag. Als u geen opslagaccount hebt, raadpleegt u het artikel [Een opslagaccount maken](../storage/common/storage-account-create.md) om een account te maken.
* **Azure SQL-database**. U gebruikt de database als *sink*-gegevensopslag. Als u geen database in Azure SQL Database hebt, raadpleegt u [Een database in Azure SQL Database maken](../azure-sql/database/single-database-create-quickstart.md) om er een te maken.

### <a name="create-a-blob-and-a-sql-table"></a>Een blob en een SQL-tabel maken

Voer nu de volgende stappen uit om uw blobopslag en SQL database voor te bereiden voor gebruik in deze zelfstudie.

#### <a name="create-a-source-blob"></a>Een bron-blob maken

1. Start Kladblok. Kopieer de volgende tekst en sla deze op uw schijf op in het bestand **emp.txt**:

    ```
    FirstName,LastName
    John,Doe
    Jane,Doe
    ```

1. Maak in de blobopslag een container met de naam **adftutorial**. Maak een map aan met de naam **invoer** in deze container. Vervolgens kunt u het bestand **emp.txt** uploaden naar de map **invoer**. Gebruik de Azure-portal of hulpprogramma's zoals [Azure Storage Explorer](https://storageexplorer.com/) voor deze taken.

#### <a name="create-a-sink-sql-table"></a>Een SQL-sink-tabel maken

1. Gebruik het volgende SQL-script om de tabel **dbo.emp** te maken in uw database:

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

1. Geef Azure-services toegang tot de SQL-server. Zorg ervoor dat **Toegang tot Azure-services toestaan** is **ingeschakeld** voor SQL Server, zodat Data Factory gegevens naar SQL Server kan schrijven. U kunt deze instelling controleren en inschakelen door naar Logische SQL-server > Overzicht > Serverfirewall instellen > Instellen te gaan en de optie **Toegang tot Azure-services toestaan** in te stellen op **AAN**.

## <a name="create-a-data-factory"></a>Een gegevensfactory maken
In deze stap maakt u een data factory en start u de Data Factory-gebruikersinterface om een pijplijn te maken in de data factory.

1. Open **Microsoft Edge** of **Google Chrome**. Op dit moment wordt de Data Factory-gebruikersinterface alleen ondersteund in de webbrowsers Microsoft Edge en Google Chrome.
2. Selecteer in het linkermenu **Een resource maken** > **Integratie** > **Data Factory**.
3. Voer op de pagina **Nieuwe data factory** **ADFTutorialDataFactory** in bij **Naam**.

   De naam van de Azure-gegevensfactory moet *wereldwijd uniek* zijn. Als u een foutbericht ontvangt dat betrekking heeft op de waarde die bij de naam is ingevuld, voert u een andere naam in voor de data factory. (Gebruik dan bijvoorbeeld uwnaamADFTutorialDataFactory). Zie [Data Factory - Naamgevingsregels](naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.

     ![Nieuwe data factory](./media/doc-common-process/name-not-available-error.png)
4. Selecteer het Azure-**abonnement** waarin u de data factory wilt maken.
5. Voer een van de volgende stappen uit voor **Resourcegroep**:

    a. Selecteer **Bestaande gebruiken** en selecteer een bestaande resourcegroep in de vervolgkeuzelijst.

    b. Selecteer **Nieuwe maken** en voer de naam van een resourcegroep in. 
         
    Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/management/overview.md) voor meer informatie. 
6. Selecteer **V2** onder **Versie**.
7. Selecteer onder **Locatie** een locatie voor de data factory. In de vervolgkeuzelijst worden alleen ondersteunde locaties weergegeven. De gegevensarchieven (bijvoorbeeld Azure Storage en SQL Database) en berekenservices (bijvoorbeeld Azure HDInsight) die door de data factory worden gebruikt, kunnen zich in andere regio's bevinden.
8. Selecteer **Maken**.
9. Als het maken is voltooid, ziet u de melding in het meldingencentrum. Selecteer **Naar resource gaan** om naar de pagina Data factory te gaan.
10. Selecteer de tegel **Maken en controleren** om de Data Factory-gebruikersinterface te openen op een afzonderlijk tabblad.


## <a name="create-a-pipeline"></a>Een pijplijn maken
In deze stap maakt u een pijplijn met kopieeractiviteit in de data factory. De kopieeractiviteit kopieert gegevens van de blobopslag naar SQL Database. In de [Quickstart](quickstart-create-data-factory-portal.md) hebt u een pijplijn gemaakt met de volgende stappen:

1. Maak een gekoppelde service.
1. Maak invoer- en uitvoergegevenssets.
1. Maak een pijplijn.

In deze zelfstudie begint u met het maken van de pijplijn. Vervolgens maakt u gekoppelde services en gegevenssets wanneer u deze nodig hebt om de pijplijn te configureren.

1. Selecteer op de pagina **Aan de slag** de optie **Pijplijn maken**.

   ![Pijplijn maken](./media/doc-common-process/get-started-page.png)

1. 1. Geef bij **Eigenschappen** op het tabblad Algemeen **CopyPipeline** op als **Naam**. Vouw vervolgens het deelvenster samen door in de rechterbovenhoek op het pictogram Eigenschappen te klikken.

1. Breid in de werkset **Activiteiten** de categorie **Verplaatsen en transformeren** uit. Sleep de activiteit **Gegevens kopiëren** uit de werkset en zet deze neer op het ontwerpoppervlak voor pijplijnen. Geef **CopyFromBlobToSql** op bij **Naam**.

    ![Kopieeractiviteit](./media/tutorial-copy-data-portal/drag-drop-copy-activity.png)

### <a name="configure-source"></a>Bron configureren

>[!TIP]
>In deze zelfstudie gebruikt u *Accountsleutel* als verificatietype voor uw brongegevensarchief, maar u kunt zo nodig ook andere ondersteunde verificatiemethoden kiezen: *SAS URI*,*Service-principal* en *Beheerde identiteit*. Raadpleeg de bijbehorende secties in [dit artikel](./connector-azure-blob-storage.md#linked-service-properties) voor meer informatie.
>Het ook raadzaam om een Azure Key Vault te gebruiken om geheimen voor gegevensarchieven veilig op te slaan. Raadpleeg [dit artikel](./store-credentials-in-key-vault.md) voor gedetailleerde illustraties.

1. Ga naar het tabblad **Bron**. Selecteer **+Nieuw** om een brongegevensset te maken.

1. Selecteer in het dialoogvenster **Nieuwe gegevensset** de optie **Azure Blob Storage** en selecteer vervolgens **Doorgaan**. De brongegevens bevinden zich in een blobopslag, daarom selecteert u **Azure Blob-opslag** voor de brongegevensset.

1. Selecteer in het dialoogvenster **Indeling selecteren** het indelingstype van uw gegevens en selecteer vervolgens **Doorgaan**.

1. Voer in het dialoogvenster **Eigenschappen instellen** als naam **SourceBlobDataset** in. Schakel het selectievakje voor **Eerste rij als header** in. Selecteer onder het tekstvak **Gekoppelde service** de optie **+ Nieuw**.

1. Voer in het dialoogvenster **Nieuwe gekoppelde service** als naam **AzureStorageLinkedService** in en selecteer uw opslagaccount in de lijst **Naam van opslagaccount**. Test de verbinding en selecteer **Maken** om de gekoppelde service te implementeren.

1. Nadat de gekoppelde service is gemaakt, wordt u teruggeleid naar de pagina **Eigenschappen instellen**. Selecteer naast **Bestandspad** de knop **Bladeren**.

1. Navigeer naar de map **adftutorial/input**, selecteer het bestand **emp.txt** en klik vervolgens op **OK**.

1. Selecteer **OK**. U wordt automatisch naar de pijplijnpagina geleid. Controleer op het tabblad **Bron** of **SourceBlobDataset** is geselecteerd. Selecteer **Gegevens vooraf bekijken** om een voorbeeld van de gegevens op deze pagina te bekijken.

    ![Brongegevensset](./media/tutorial-copy-data-portal/source-dataset-selected.png)

### <a name="configure-sink"></a>Sink configureren
>[!TIP]
>In deze zelfstudie gebruikt u *SQL-verificatie* als het verificatietype voor uw sinkgegevensarchief, maar u kunt zo nodig ook andere ondersteunde verificatiemethoden kiezen: *Service-principal* en *Beheerde identiteit*. Raadpleeg de bijbehorende secties in [dit artikel](./connector-azure-sql-database.md#linked-service-properties) voor meer informatie.
>Het ook raadzaam om een Azure Key Vault te gebruiken om geheimen voor gegevensarchieven veilig op te slaan. Raadpleeg [dit artikel](./store-credentials-in-key-vault.md) voor gedetailleerde illustraties.

1. Ga naar het tabblad **Sink** en selecteer **+Nieuw** om een sink-gegevensset te maken.

1. Voer in het dialoogvenster **Nieuwe gegevensset** 'SQL' in het zoekvak in om de connectoren te filteren, selecteer **Azure SQL Database** en selecteer vervolgens **Doorgaan**. In deze zelfstudie kopieert u gegevens naar een SQL database.

1. Voer in het dialoogvenster **Eigenschappen instellen** als naam **OutputSqlDataset** in. Selecteer **+ Nieuw** in de vervolgkeuzelijst **Gekoppelde service**. Een gegevensset moet worden gekoppeld aan een gekoppelde service. De gekoppelde service beschikt over de verbindingsreeks die door Data Factory wordt gebruikt om tijdens runtime een verbinding met SQL Database tot stand te brengen. De dataset geeft informatie over de container, map en het bestand (optioneel) met de brongegevens.

1. Voer in het dialoogvenster **Nieuwe gekoppelde service (Azure SQL Database)** de volgende stappen uit:

    a. Geef **AzureSqlDatabaseLinkedService** op als **Naam**.

    b. Selecteer bij **Servernaam** uw SQL Server-exemplaar.

    c. Selecteer uw database bij **Databasenaam**.

    d. Voer bij **Gebruikersnaam** de naam van de gebruiker in.

    e. Voer bij **Wachtwoord** het wachtwoord voor de gebruiker in.

    f. Als u de verbinding wilt testen, selecteert u **Verbinding testen**.

    g. Selecteer **Maken** om de gekoppelde service te implementeren.

    ![Nieuwe gekoppelde service opslaan](./media/tutorial-copy-data-portal/new-azure-sql-linked-service-window.png)

1. U wordt automatisch naar het dialoogvenster **Eigenschappen instellen** geleid. Selecteer bij **Tabel** **[dbo].[emp]** . Selecteer vervolgens **OK**.

1. Ga naar het tabblad met de pijplijn en controleer bij **Sink-gegevensset** of **OutputSqlDataset** is geselecteerd.

    ![Tabblad Pijplijn](./media/tutorial-copy-data-portal/pipeline-tab-2.png)       

U kunt optioneel het schema van de bron toewijzen aan het overeenkomstige schema van het doel door [Schematoewijzing in kopieeractiviteit](copy-activity-schema-and-type-mapping.md) te volgen.

## <a name="validate-the-pipeline"></a>De pijplijn valideren
Selecteer in de werkbalk **Valideren** om de pijplijn te valideren.

U ziet de JSON-code die is gekoppeld aan de pijplijn door te klikken op **Code** in de rechterbovenhoek.

## <a name="debug-and-publish-the-pipeline"></a>Fouten opsporen in de pijplijn en de pijplijn publiceren
U kunt fouten opsporen in een pijplijn voordat u artefacten (gekoppelde services, gegevenssets en pijplijn) publiceert naar Data Factory of uw eigen Azure Repos Git-opslagplaats.

1. Selecteer **Fouten opsporen** om fouten op te sporen in de pijplijn. De status van de pijplijnuitvoering wordt weergegeven op het tabblad **Uitvoer** onder in het venster.

1. Zodra de pijplijn kan worden uitgevoerd, selecteert u **Alles publiceren** in de bovenste werkbalk. Met deze actie publiceert u entiteiten (gegevenssets en pijplijnen) die u hebt gemaakt met Data Factory.

1. Wacht tot u het bericht **Gepubliceerd** ziet. Om meldingsberichten te zien, klikt u op **Meldingen weergeven** rechts bovenin (belknop).

## <a name="trigger-the-pipeline-manually"></a>De pijplijn handmatig activeren
In deze stap moet u handmatig de pijplijn activeren, die u in de vorige stap heeft gepubliceerd.

1. Selecteer op de werkbalk de optie **Activeren** en selecteer vervolgens **Nu activeren**. Klik op de pagina **Pijplijnuitvoering** op **OK**.  

1. Ga naar het tabblad **Controleren** aan de linkerkant. U ziet een pijplijn die wordt geactiveerd door een handmatige trigger. U kunt via koppelingen in de kolom **NAAM PIJPLIJN** details van activiteiten bekijken en de pijplijn opnieuw uitvoeren.

    [![Pijplijnuitvoeringen](./media/tutorial-copy-data-portal/monitor-pipeline-inline-and-expended.png)](./media/tutorial-copy-data-portal/monitor-pipeline-inline-and-expended.png#lightbox) controleren

1. Selecteer de koppeling **CopyPipeline** in de kolom **NAAM PIJPLIJN** om de uitvoering van activiteiten te zien die zijn gekoppeld aan de pijplijnuitvoering. Omdat er in dit voorbeeld slechts één activiteit is, ziet u slechts één vermelding in de lijst. Selecteer de koppeling **Details** (pictogram van een bril) in de kolom **NAAM ACTIVITEIT** om details van de kopieerbewerking te zien. Selecteer **Alle pijplijnuitvoeringen** bovenaan om terug te gaan naar de weergave Pijplijnuitvoeringen. Selecteer **Vernieuwen** om de weergave te vernieuwen.

    [![Uitvoering van activiteiten controleren](./media/tutorial-copy-data-portal/view-activity-runs-inline-and-expended.png)](./media/tutorial-copy-data-portal/view-activity-runs-inline-and-expended.png#lightbox)

1. Controleer of er twee extra rijen zijn toegevoegd aan de **emp**-tabel in de database.

## <a name="trigger-the-pipeline-on-a-schedule"></a>De pijplijn activeren volgens een schema
In dit schema maakt u een planningstrigger voor de pijplijn. De trigger voert de pijplijn uit volgens de opgegeven planning, bijvoorbeeld elk uur of dagelijks. Hier stelt u de trigger in om de pijplijn elke minuut uit te voeren tot en met de opgegeven einddatum/-tijd.

1. Ga naar het tabblad **Auteur** links boven op het tabblad Monitor.

1. Ga naar uw pijplijn, klik op **Activeren** in de werkbalk en selecteer **Nieuw/Bewerken**.

1. Selecteer in het dialoogvenster **Triggers toevoegen** de optie **+ Nieuw** voor het gebied **Trigger kiezen**.

1. Voer in het venster **Nieuwe trigger** de volgende stappen uit:

    a. Geef **RunEveryMinute** op bij **Naam**.

    b. Werk de **Startdatum** voor de trigger bij. Als de datum vóór de huidige datum/tijd ligt, wordt de trigger van kracht zodra de wijziging is gepubliceerd. 

    c. Selecteer de vervolgkeuzelijst onder **Tijdzone**.

    d. Stel **Terugkeerpatroon** in op **Elke minuut**.

    e. Schakel het selectievakje voor **Einddatum opgeven** in, en werk het gedeelte **Eindigen op** bij naar enkele minuten na de huidige datum/tijd. De trigger wordt pas geactiveerd nadat u de wijzigingen publiceert. Als u deze slechts enkele minuten uit elkaar instelt en u voor die tijd niet publiceert, ziet u geen triggeruitvoering.

    f. Selecteer voor de optie **Geactiveerd** **Ja**.

    g. Selecteer **OK**.

    > [!IMPORTANT]
    > Er zijn kosten verbonden aan elke pijplijnuitvoering, dus stel een geschikte einddatum in.

1. Lees de waarschuwing op de pagina **Trigger bewerken** en selecteer vervolgens **Opslaan**. De pijplijn in dit voorbeeld gebruikt geen parameters.

1. Klik op **Alles publiceren** om de wijziging te publiceren.

1. Ga naar het tabblad **Controleren** aan de linkerkant om de geactiveerde pijplijnuitvoeringen te bekijken.

    [![Geactiveerde pijplijnuitvoeringen](./media/tutorial-copy-data-portal/triggered-pipeline-runs-inline-and-expended.png)](./media/tutorial-copy-data-portal/triggered-pipeline-runs-inline-and-expended.png#lightbox)

1. Als u wilt overschakelen van de weergave **Pijplijnuitvoeringen** naar de weergave **Triggeruitvoeringen**, selecteert u **Triggeruitvoeringen** aan de linkerkant van het venster.

1. U ziet de triggeruitvoeringen in een lijst.

1. Controleer of er twee rijen per minuut (voor elke pijplijnuitvoering) tot de opgegeven eindtijd in de **emp**-tabel worden ingevoegd.

## <a name="next-steps"></a>Volgende stappen
Met de pijplijn in dit voorbeeld worden gegevens gekopieerd van de ene locatie naar een andere locatie in een blobopslag. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een data factory maken.
> * Een pijplijn met kopieeractiviteit maken.
> * De uitvoering van de pijplijn testen.
> * De pijplijn handmatig activeren.
> * De pijplijn activeren volgens een schema.
> * De uitvoering van de pijplijn en van de activiteit controleren.


Ga verder met de volgende zelfstudie als u wilt weten hoe u on-premises gegevens kopieert naar de cloud:

> [!div class="nextstepaction"]
>[On-premises gegevens kopiëren naar de cloud](tutorial-hybrid-copy-portal.md)
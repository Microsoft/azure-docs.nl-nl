---
title: Gegevens Data Management Gateway verplaatsen
description: Stel een gegevens gateway in om gegevens te verplaatsen tussen on-premises en de Cloud. Gebruik Data Management Gateway in Azure Data Factory om uw gegevens te verplaatsen.
author: nabhishek
ms.author: abnarain
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
robots: noindex
ms.openlocfilehash: 53fce1744ccbf4289b2415e926e084c90d708a13
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100380284"
---
# <a name="move-data-between-on-premises-sources-and-the-cloud-with-data-management-gateway"></a>Gegevens verplaatsen tussen on-premises bronnen en de Cloud met Data Management Gateway
> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [gegevens kopiëren tussen on-premises en de Cloud met behulp van Data Factory](../tutorial-hybrid-copy-powershell.md).

Dit artikel bevat een overzicht van gegevens integratie tussen on-premises gegevens archieven en gegevens archieven in de Cloud met behulp van Data Factory. Het is gebaseerd op het artikel [activiteiten voor gegevens verplaatsing](data-factory-data-movement-activities.md) en andere artikelen over Data Factory core-concepten: [gegevens sets](data-factory-create-datasets.md) en [pijp lijnen](data-factory-create-pipelines.md).

## <a name="data-management-gateway"></a>Gegevensbeheergateway
U moet Data Management Gateway op de on-premises computer installeren om gegevens naar/van een on-premises gegevens opslag te kunnen verplaatsen. De gateway kan worden geïnstalleerd op dezelfde computer als het gegevens archief of op een andere computer, mits de gateway verbinding kan maken met het gegevens archief.

> [!IMPORTANT]
> Zie [Data Management Gateway](data-factory-data-management-gateway.md) artikel voor meer informatie over Data Management Gateway.

In de volgende procedure ziet u hoe u een data factory maakt met een pijp lijn die gegevens verplaatst van een on-premises **SQL Server** -Data Base naar een Azure Blob-opslag. Als onderdeel van de procedure installeert en configureert u de gegevensbeheergateway op uw computer.

## <a name="walkthrough-copy-on-premises-data-to-cloud"></a>Walkthrough: on-premises gegevens kopiëren naar de Cloud
In dit scenario voert u de volgende stappen uit:

1. Een data factory maken.
2. Een Data Management gateway maken.
3. Gekoppelde services maken voor de bron-en Sink-gegevens opslag.
4. Gegevens sets maken om invoer-en uitvoer gegevens weer te geven.
5. Een pijplijn maakt met een kopieeractiviteit om de gegevens te verplaatsen.

## <a name="prerequisites-for-the-tutorial"></a>Vereisten voor de zelf studie
Voordat u met deze procedure begint, moet u beschikken over de volgende vereisten:

* **Azure-abonnement**.  Als u geen abonnement hebt, kunt u binnen een paar minuten een gratis proefaccount maken. Raadpleeg het artikel over de [gratis proef versie](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie.
* **Azure Storage-account**. In deze zelf studie gebruikt u de Blob-opslag als een **doel/Sink-** gegevens archief. Als u geen Azure Storage-account hebt, raadpleegt u het artikel [een opslag account maken](../../storage/common/storage-account-create.md) voor de stappen om er een te maken.
* **SQL Server**. In deze zelf studie gebruikt u een SQL Server-Data Base als een **brongegevens** opslag.

## <a name="create-data-factory"></a>Een gegevensfactory maken
In deze stap gebruikt u de Azure Portal om een Azure Data Factory-exemplaar te maken met de naam **ADFTutorialOnPremDF**.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Klik op **een resource maken**, klik op **Intelligence en analytische** gegevens en klik op **Data Factory**.

   ![Nieuw -> DataFactory](./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png)  
3. Voer op de pagina **nieuw Data Factory** **ADFTutorialOnPremDF** in als naam.

    ![Toevoegen aan start Board](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNewDataFactoryAddToStartboard.png)

   > [!IMPORTANT]
   > De naam van de Azure-gegevensfactory moet wereldwijd uniek zijn. Als het volgende fout bericht wordt weer gegeven: **naam van Data Factory "ADFTutorialOnPremDF" is niet beschikbaar**, wijzigt u de naam van de Data Factory (bijvoorbeeld yournameADFTutorialOnPremDF) en probeert u het opnieuw. Gebruik deze naam in plaats van ADFTutorialOnPremDF tijdens het uitvoeren van de resterende stappen in deze zelf studie.
   >
   > De naam van de data factory kan in de toekomst worden geregistreerd als een **DNS-** naam en kan daarom openbaar worden weer gegeven.
   >
   >
4. Selecteer het **Azure-abonnement** waarvoor u de gegevensfactory wilt maken.
5. Selecteer een bestaande **resourcegroep** of maak een resourcegroep. Voor de zelf studie maakt u een resource groep met de naam: **ADFTutorialResourceGroup**.
6. Klik op de pagina **nieuw Data Factory** op **maken** .

   > [!IMPORTANT]
   > Als u Data Factory-exemplaren wilt maken, moet u lid zijn van de rol [Inzender Data Factory](../../role-based-access-control/built-in-roles.md#data-factory-contributor) op abonnements-/resourcegroepsniveau.
   >
   >
7. Nadat het maken is voltooid, ziet u de **Data Factory** pagina, zoals wordt weer gegeven in de volgende afbeelding:

   ![Start pagina van Data Factory](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png)

## <a name="create-gateway"></a>Gateway maken
1. Klik op de pagina **Data Factory** op de tegel **ontwerpen en implementeren** om de **Editor** voor de Data Factory te starten.

    ![Tegel Ontwerpen en implementeren](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png)
2. Klik in de Data Factory editor op **... Meer** op de werk balk en klik vervolgens op **nieuwe gegevens gateway**. U kunt ook met de rechter muisknop op **gegevens gateways** in de structuur weergave klikken en op **nieuwe gegevens gateway** klikken.

   ![Nieuwe gegevens gateway op werk balk](./media/data-factory-move-data-between-onprem-and-cloud/NewDataGateway.png)
3. Voer op de pagina **maken** **adftutorialgateway** in als **naam** en klik op **OK**.     

    ![Gateway pagina maken](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)

    > [!NOTE]
    > In dit scenario maakt u de logische gateway met slechts één knoop punt (on-premises Windows-machine). U kunt een Data Management Gateway uitschalen door meerdere on-premises machines aan de gateway te koppelen. U kunt omhoog schalen door het aantal gegevens verplaatsings taken te verhogen dat gelijktijdig kan worden uitgevoerd op een knoop punt. Deze functie is ook beschikbaar voor een logische gateway met één knoop punt. Zie [gegevens beheer gateway schalen in azure Data Factory](data-factory-data-management-gateway-high-availability-scalability.md) artikel voor meer informatie.  
4. Klik op de pagina **configureren** op **rechtstreeks op deze computer installeren**. Met deze actie wordt het installatie pakket voor de gateway gedownload, wordt de gateway op de computer geïnstalleerd, geconfigureerd en geregistreerd.  

   > [!NOTE]
   > Gebruik Internet Explorer of een webbrowser die compatibel is met micro soft ClickOnce.
   >
   > Als u Chrome gebruikt, gaat u naar de [Chrome-webwinkel](https://chrome.google.com/webstore/), zoekt u met het sleutel woord ' ClickOnce ', kiest u een van de ClickOnce-extensies en installeert u het.
   >
   > Doe hetzelfde voor Firefox (invoeg toepassing installeren). Klik op de knop **menu openen** op de werk balk (**drie horizontale lijnen** in de rechter bovenhoek), klik op **invoeg toepassingen**, Zoek met het tref woord ' ClickOnce ', kies een van de ClickOnce-extensies en installeer deze.    
   >
   >

    ![Gateway-pagina configureren](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

    Op deze manier kunt u de gateway met één muis klik downloaden, installeren, configureren en registreren in één stap. U kunt zien dat de toepassing **micro soft Data Management Gateway Configuration Manager** op uw computer is geïnstalleerd. U kunt ook de uitvoer bare **ConfigManager.exe** vinden in de map: **C:\Program Files\Microsoft gegevensbeheer Gateway\2.0\Shared**.

    U kunt Gateway ook hand matig downloaden en installeren met behulp van de koppelingen op deze pagina en deze registreren met behulp van de sleutel die wordt weer gegeven in het tekstvak **nieuwe sleutel** .

    Zie [Data Management Gateway](data-factory-data-management-gateway.md) artikel voor meer informatie over de gateway.

   > [!NOTE]
   > U moet een beheerder zijn op de lokale computer om de Data Management Gateway te kunnen installeren en configureren. U kunt extra gebruikers toevoegen aan de lokale Windows-groep **Data Management Gateway gebruikers** . De leden van deze groep kunnen het Data Management Gateway Configuration Manager-hulp programma gebruiken om de gateway te configureren.
   >
   >
5. Wacht een paar minuten of wacht totdat het volgende meldings bericht wordt weer gegeven:

    ![Installatie van de gateway is voltooid](./media/data-factory-move-data-between-onprem-and-cloud/gateway-install-success.png)
6. Start **Data Management Gateway Configuration Manager** -toepassing op uw computer. Typ in het venster **zoeken** **Data Management Gateway** voor toegang tot dit hulp programma. U kunt ook de uitvoer bare **ConfigManager.exe** vinden in de map: **C:\Program Files\Microsoft gegevensbeheer Gateway\2.0\Shared**

    ![Gateway Configuration Manager](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png)
7. Bevestig dat u het `adftutorialgateway is connected to the cloud service` bericht ziet. Op de status balk worden de onderste schermen weer gegeven **die zijn verbonden met de Cloud service** , samen met een **groen vinkje**.

    Op het tabblad **Start** kunt u ook de volgende bewerkingen uitvoeren:

   * **Registreer** een gateway met een sleutel van de Azure Portal met behulp van de knop registreren.
   * **Stop** de Data Management gateway host-service die op de gateway computer wordt uitgevoerd.
   * **Plannen dat updates** op een specifiek tijdstip van de dag moeten worden geïnstalleerd.
   * Weer geven wanneer de gateway voor het **laatst is bijgewerkt**.
   * Geef de tijd op waarop een update voor de gateway kan worden geïnstalleerd.
8. Ga naar het tabblad **instellingen** . Het certificaat dat is opgegeven in de sectie **certificaat** wordt gebruikt voor het versleutelen/ontsleutelen van referenties voor het on-premises gegevens archief dat u opgeeft op de portal. Beschrijving Klik in plaats daarvan op **wijzigen** om uw eigen certificaat te gebruiken. De gateway gebruikt standaard het certificaat dat automatisch wordt gegenereerd door de Data Factory-service.

    ![Configuratie van Gateway certificaat](./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png)

    U kunt ook de volgende acties uitvoeren op het tabblad **instellingen** :

   * Het certificaat weer geven of exporteren dat door de gateway wordt gebruikt.
   * Wijzig het HTTPS-eind punt dat wordt gebruikt door de gateway.    
   * Stel een HTTP-proxy in die door de gateway moet worden gebruikt.     
9. Beschrijving Schakel over naar het tabblad **Diagnostische gegevens** en schakel de optie **uitgebreide logboek registratie inschakelen** in als u uitgebreide logboek registratie wilt inschakelen die u kunt gebruiken om problemen met de gateway op te lossen. De informatie over logboek registratie vindt u in **Logboeken** onder **Logboeken toepassingen en services**  ->  **Data Management Gateway** knoop punt.

    ![Tabblad Diagnostische gegevens](./media/data-factory-move-data-between-onprem-and-cloud/diagnostics-tab.png)

    U kunt ook de volgende acties uitvoeren op het tabblad **Diagnostische gegevens** :

   * Gebruik de sectie **verbinding testen** met een on-premises gegevens bron met behulp van de gateway.
   * Klik op **Logboeken weer geven** voor een overzicht van het Data Management Gateway logboek in een logboeken venster.
   * Klik op **Logboeken verzenden** om een zip-bestand te uploaden met Logboeken van de afgelopen zeven dagen naar micro soft om het oplossen van problemen met problemen te vergemakkelijken.
10. Selecteer op het tabblad **Diagnostische** gegevens in de sectie **verbinding testen** de optie **sqlserver** voor het type van het Data Store, voer de naam in van de database server, de naam van de data base, geef het verificatie type op, voer de gebruikers naam en het wacht woord in en klik op **testen** om te testen of de gateway verbinding kan maken met de data base.
11. Ga naar de webbrowser en klik in de **Azure Portal** op **OK** op de pagina **configureren** en vervolgens op de pagina **nieuwe gegevens gateway** .
12. U ziet **adftutorialgateway** onder **gegevens gateways** in de structuur weergave aan de linkerkant.  Als u erop klikt, wordt de bijbehorende JSON weer geven.

## <a name="create-linked-services"></a>Gekoppelde services maken
In deze stap maakt u twee gekoppelde services: **AzureStorageLinkedService** en **SqlServerLinkedService**. De **SqlServerLinkedService** koppelt een SQL Server-Data Base en de gekoppelde **AzureStorageLinkedService** -service koppelt een Azure blob Store aan de Data Factory. U maakt later in dit overzicht een pijp lijn waarmee gegevens worden gekopieerd van de SQL Server Data Base naar de Azure Blob Store.

#### <a name="add-a-linked-service-to-a-sql-server-database"></a>Een gekoppelde service toevoegen aan een SQL Server-Data Base
1. Klik in de **Data Factory editor** op **Nieuw gegevens archief** op de werk balk en selecteer **SQL Server**.

   ![Nieuwe gekoppelde SQL Server-service](./media/data-factory-move-data-between-onprem-and-cloud/NewSQLServer.png)
2. Voer de volgende stappen uit in de **JSON-editor** aan de rechter kant:

   1. Geef voor de **gatewaynaam** **adftutorialgateway** op.    
   2. Voer de volgende stappen uit in de **Connections Tring**:    

      1. Voer bij **servername** de naam in van de server die als host fungeert voor de SQL Server-Data Base.
      2. Voer voor **DATABASENAME** de naam van de data base in.
      3. Klik op de knop **versleutelen** op de werk balk. U ziet de toepassing voor het beheer van referenties.

         ![Toepassing voor referenties beheren](./media/data-factory-move-data-between-onprem-and-cloud/credentials-manager-application.png)
      4. Geef in het dialoog venster **Referenties instellen** het verificatie type, de gebruikers naam en het wacht woord op en klik op **OK**. Als de verbinding tot stand is gebracht, worden de versleutelde referenties in de JSON opgeslagen en wordt het dialoog venster gesloten.
      5. Sluit het lege browser tabblad waarmee het dialoog venster wordt geopend als het niet automatisch wordt gesloten en ga terug naar het tabblad met de Azure Portal.

         Op de gateway computer worden deze referenties **versleuteld** met behulp van een certificaat waarvan de Data Factory-service eigenaar is. Als u het certificaat wilt gebruiken dat is gekoppeld aan de Data Management Gateway in plaats daarvan, raadpleegt u referenties veilig instellen.    
   3. Klik op **implementeren** op de opdracht balk om de gekoppelde SQL Server-service te implementeren. De gekoppelde service wordt weer gegeven in de structuur weergave.

      ![SQL Server gekoppelde service in de structuur weergave](./media/data-factory-move-data-between-onprem-and-cloud/sql-linked-service-in-tree-view.png)    

#### <a name="add-a-linked-service-for-an-azure-storage-account"></a>Een gekoppelde service voor een Azure Storage-account toevoegen
1. Klik in de **Data Factory editor** op **Nieuw gegevens archief** op de opdracht balk en klik op **Azure Storage**.
2. Voer de naam van uw Azure Storage-account in voor de **account naam**.
3. Voer de sleutel voor uw Azure Storage-account in voor de **account sleutel**.
4. Klik op **implementeren** om de **AzureStorageLinkedService** te implementeren.

## <a name="create-datasets"></a>Gegevenssets maken
In deze stap maakt u invoer- en uitvoergegevenssets die invoer- en uitvoergegevenssets voor de kopieerbewerking vertegenwoordigen (on-premises SQL Server-database => Azure Blob-opslag). Voordat u de gegevenssets maakt, voert u de volgende stappen uit (gedetailleerde stappen vindt u na de lijst):

* Maak een tabel met de naam **emp** in de SQL Server-database die u als gekoppelde service hebt toegevoegd aan de gegevensfactory, en voer een aantal voorbeeldvermeldingen toe aan de tabel.
* Maak een blobcontainer met de naam **adftutorial** in het Azure Blob-opslagaccount dat u als gekoppelde service hebt toegevoegd aan de gegevensfactory.

### <a name="prepare-on-premises-sql-server-for-the-tutorial"></a>On-premises SQL Server voorbereiden voor de zelf studie
1. Gebruik in de data base die u hebt opgegeven voor de SQL Server gekoppelde service (**SqlServerLinkedService**) het volgende SQL-script om de **EMP** -tabel in de data base te maken.

    ```SQL   
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
        CONSTRAINT PK_emp PRIMARY KEY (ID)
    )
    GO
    ```
2. Voer een aantal voorbeelden in de tabel in:

    ```SQL
    INSERT INTO emp VALUES ('John', 'Doe')
    INSERT INTO emp VALUES ('Jane', 'Doe')
    ```

### <a name="create-input-dataset"></a>Invoergegevensset maken

1. Klik in de **Data Factory editor** op **...** Klik op **nieuwe gegevensset** op de opdracht balk en klik op **SQL Server tabel**.
2. Vervang de JSON in het rechterdeel venster door de volgende tekst:

    ```JSON   
    {        
        "name": "EmpOnPremSQLTable",
        "properties": {
            "type": "SqlServerTable",
            "linkedServiceName": "SqlServerLinkedService",
            "typeProperties": {
                "tableName": "emp"
            },
            "external": true,
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }     
    ```       
   Houd rekening met de volgende punten:

   * **type** wordt ingesteld op **SqlServerTable**.
   * **TableName** is ingesteld op **EMP**.
   * **linkedServiceName** is ingesteld op **SqlServerLinkedService** (u hebt deze gekoppelde service eerder in dit overzicht gemaakt.)
   * Voor een invoer gegevensset die niet door een andere pijp lijn in Azure Data Factory wordt gegenereerd, moet u **External** instellen op **True**. Hiermee wordt aangegeven dat de invoer gegevens buiten de Azure Data Factory-service worden geproduceerd. U kunt eventueel elk beleid voor externe gegevens opgeven met behulp van het **externalData** -element in de sectie **Policy** .    

   Zie [gegevens verplaatsen naar/van SQL Server](data-factory-sqlserver-connector.md) voor meer informatie over JSON-eigenschappen.
3. Klik op **implementeren** op de opdracht balk om de gegevensset te implementeren.  

### <a name="create-output-dataset"></a>Uitvoergegevensset maken

1. Klik in de **Data Factory editor** op **nieuwe gegevensset** op de opdracht balk en klik vervolgens op **Azure Blob-opslag**.
2. Vervang de JSON in het rechterdeel venster door de volgende tekst:

    ```JSON   
    {
        "name": "OutputBlobTable",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "adftutorial/outfromonpremdf",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
     }
    ```   
   Houd rekening met de volgende punten:

   * **type** wordt ingesteld op **AzureBlob**.
   * **linkedServiceName** wordt ingesteld op **AzureStorageLinkedService** (u hebt deze gekoppelde service gemaakt in stap 2).
   * **FolderPath** is ingesteld op **adftutorial/outfromonpremdf** waarbij outfromonpremdf de map is in de adftutorial-container. Maak de **adftutorial** -container als deze nog niet bestaat.
   * De **beschikbaarheid** wordt ingesteld op **elk uur** (de **frequentie** wordt ingesteld op **elk uur** en het **interval** wordt ingesteld op **1**).  De Data Factory-Service genereert elk uur een segment met uitvoer gegevens in de tabel **EMP** in de Azure SQL database.

   Als u geen **filename** opgeeft voor een **uitvoer tabel**, worden de gegenereerde bestanden in de **FolderPath** benoemd met de volgende indeling: `Data.<Guid>.txt` (bijvoorbeeld: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

   Als u **folderPath** en **fileName** dynamisch wilt instellen op basis van de **SliceStart**-tijd, gebruikt u de eigenschap partitionedBy. In het volgende voorbeeld worden voor folderPath Year, Month en Day gebruikt van de SliceStart-waarde (tijd waarop is begonnen met het verwerken van het segment). Voor fileName wordt gebruikgemaakt van Hour van de SliceStart-waarde. Als er bijvoorbeeld een segment wordt geproduceerd voor 2014-10-20T08:00:00, wordt folderName ingesteld op wikidatagateway/wikisampledataout/2014/10/20 en wordt fileName ingesteld op 08.csv.

    ```JSON
    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy":
    [

        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
    ],
    ```

    Zie [gegevens verplaatsen van/naar Azure Blob Storage](data-factory-azure-blob-connector.md) voor meer informatie over JSON-eigenschappen.
3. Klik op **implementeren** op de opdracht balk om de gegevensset te implementeren. Controleer of de gegevens sets in de structuur weergave worden weer gegeven.  

## <a name="create-pipeline"></a>Pijplijn maken
In deze stap maakt u een **pijp lijn** met een **Kopieer activiteit** die gebruikmaakt van **EmpOnPremSQLTable** als invoer en **OutputBlobTable** als uitvoer.

1. Klik in Data Factory editor op **... Meer** en klik op **nieuwe pijp lijn**.
2. Vervang de JSON in het rechterdeel venster door de volgende tekst:    

    ```JSON   
     {
         "name": "ADFTutorialPipelineOnPrem",
         "properties": {
         "description": "This pipeline has one Copy activity that copies data from an on premises SQL to Azure blob",
         "activities": [
           {
             "name": "CopyFromSQLtoBlob",
             "description": "Copy data from on premises SQL server to blob",
             "type": "Copy",
             "inputs": [
               {
                 "name": "EmpOnPremSQLTable"
               }
             ],
             "outputs": [
               {
                 "name": "OutputBlobTable"
               }
             ],
             "typeProperties": {
               "source": {
                 "type": "SqlSource",
                 "sqlReaderQuery": "select * from emp"
               },
               "sink": {
                 "type": "BlobSink"
               }
             },
             "Policy": {
               "concurrency": 1,
               "executionPriorityOrder": "NewestFirst",
               "style": "StartOfInterval",
               "retry": 0,
               "timeout": "01:00:00"
             }
           }
         ],
         "start": "2016-07-05T00:00:00Z",
         "end": "2016-07-06T00:00:00Z",
         "isPaused": false
       }
     }
    ```   
   > [!IMPORTANT]
   > Vervang de waarde van de eigenschap **start** door de huidige dag en de waarde **end** door de volgende dag.
   >
   >

   Houd rekening met de volgende punten:

   * In de sectie activiteiten is er alleen activiteit waarvan het **type** is ingesteld op **kopiëren**.
   * De **invoer** voor de activiteit is ingesteld op **EmpOnPremSQLTable** en de **uitvoer** voor de activiteit is ingesteld op **OutputBlobTable**.
   * In de sectie **typeProperties** is **SqlSource** opgegeven als **bron type** en **BlobSink** is opgegeven als het sink- **type**.
   * SQL-query `select * from emp` is opgegeven voor de eigenschap **SqlReaderQuery** van **SqlSource**.

   Zowel de begin- als einddatum en -tijd moeten de [ISO-indeling](https://en.wikipedia.org/wiki/ISO_8601) hebben. Bijvoorbeeld: 2014-10-14T16:32:41Z. De **eindtijd** is optioneel, maar we gebruiken hem in deze zelfstudie.

   Als u geen waarde opgeeft voor de eigenschap **end**, wordt automatisch **start + 48 uur** gebruikt. Als u de pijplijn voor onbepaalde tijd wilt uitvoeren, geeft u **9/9/9999** op als waarde voor de eigenschap **end**.

   U definieert de tijds duur waarin de gegevens segmenten worden verwerkt op basis van de **beschikbaarheids** eigenschappen die voor elke Azure Data Factory gegevensset zijn gedefinieerd.

   In het bovenstaande voorbeeld zijn er 24 gegevenssegmenten omdat er elk uur één gegevenssegment wordt gemaakt.        
3. Klik op **implementeren** op de opdracht balk om de gegevensset te implementeren (tabel is een rechthoekige gegevensset). Controleer of de pijp lijn wordt weer gegeven in de structuur weergave onder het knoop punt **pijp lijnen** .  
4. Klik nu op **X** twee keer om de pagina te sluiten om terug te gaan naar de **Data Factory** -pagina voor de **ADFTutorialOnPremDF**.

**Gefeliciteerd!** U hebt een Azure data factory, gekoppelde services, gegevens sets en een pijp lijn gemaakt en de pijp lijn gepland.

#### <a name="view-the-data-factory-in-a-diagram-view"></a>De gegevensfactory weergeven in een diagramweergave
1. Klik in de **Azure Portal** op **diagram** tegel op de start pagina van de **ADFTutorialOnPremDF** -Data Factory. :

    ![Diagram koppeling](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)
2. U ziet een diagram dat lijkt op de volgende afbeelding:

    ![Diagramweergave](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

    U kunt inzoomen, uitzoomen, zoomen naar 100%, in-en uitzoomen, pijp lijnen en gegevens sets automatisch plaatsen en afkomst informatie weer geven (upstream-en downstream-items van geselecteerde items markeren).  U kunt dubbel klikken op een object (invoer/uitvoer gegevensset of pijp lijn) om de eigenschappen ervan weer te geven.

## <a name="monitor-pipeline"></a>De pijplijn bewaken
In deze stap gebruikt u de Azure Portal om te controleren wat er gebeurt in een Azure data factory. U kunt ook PowerShell-cmdlets gebruiken om gegevenssets en pijplijnen te bewaken. Zie [pijp lijnen bewaken en beheren](data-factory-monitor-manage-pipelines.md)voor meer informatie over bewaking.

1. Dubbel klik in het diagram op **EmpOnPremSQLTable**.  

    ![EmpOnPremSQLTable segmenten](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)
2. U ziet dat alle gegevens segmenten in de status **gereed** zijn, omdat de pijplijn duur (begin tijd tot eind tijd) in het verleden ligt. Het is ook mogelijk dat u de gegevens in de SQL Server-Data Base hebt ingevoegd, en dat is de enige tijd. Controleer of er onderaan geen segmenten worden weer gegeven in de sectie **probleem segmenten** . Als u alle segmenten wilt bekijken, klikt u op **meer weer** geven onder aan de lijst met segmenten.
3. Klik nu op de pagina **gegevens sets** op **OutputBlobTable**.

    ![OputputBlobTable segmenten](./media/data-factory-move-data-between-onprem-and-cloud/OutputBlobTableSlicesBlade.png)
4. Klik op een gegevens segment uit de lijst en de pagina **gegevens segment** moet worden weer gegeven. U ziet de uitvoering van activiteiten voor het segment. Normaal gesp roken ziet u dat er slechts één activiteit wordt uitgevoerd.  

    ![Blade gegevens segment](./media/data-factory-move-data-between-onprem-and-cloud/DataSlice.png)

    Als het segment niet de status **Gereed** heeft, kunt u de upstreamsegmenten bekijken die niet de status Gereed hebben en die voorkomen dat de huidige status wordt uitgevoerd. U ziet deze segmenten in de lijst **Upstreamsegmenten die niet gereed zijn**.
5. Klik op de uitvoering van de **activiteit** in de lijst onderaan om **Details** van de uitvoering van de activiteit weer te geven.

   ![Pagina Details voor uitvoering van activiteit](./media/data-factory-move-data-between-onprem-and-cloud/ActivityRunDetailsBlade.png)

   U ziet informatie zoals door Voer, duur en de gateway die wordt gebruikt om de gegevens over te dragen.
6. Klik op **X** om alle pagina's te sluiten totdat u
7. Ga terug naar de start pagina voor de **ADFTutorialOnPremDF**.
8. Beschrijving Klik op **pijp lijnen**, klik op **ADFTutorialOnPremDF** en zoom in op invoer tabellen (**verbruikte**) of uitvoer gegevens sets (**geproduceerd**).
9. Gebruik hulpprogram ma's zoals [micro soft Storage Explorer](https://storageexplorer.com/) om te controleren of een blob/bestand voor elk uur wordt gemaakt.

   ![Azure Storage Explorer](./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png)

## <a name="next-steps"></a>Volgende stappen
* Zie [Data Management Gateway](data-factory-data-management-gateway.md) artikel voor meer informatie over de Data Management Gateway.
* Zie [gegevens kopiëren van Azure Blob naar Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor meer informatie over het gebruik van de Kopieer activiteit om gegevens van een brongegevens archief naar een Sink-gegevens archief te verplaatsen.

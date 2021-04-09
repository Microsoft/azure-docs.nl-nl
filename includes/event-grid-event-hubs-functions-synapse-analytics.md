---
title: bestand opnemen
description: bestand opnemen
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 12/07/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: d6b8b35b36830568d6ff8c46a381fec0cfc57e84
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96854157"
---
:::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/overview.png" alt-text="Overzicht van toepassing":::

In dit diagram ziet u de werkstroom van de oplossing die u in deze zelfstudie maakt: 

1. Gegevens die worden verzonden naar een Azure Event Hub worden opgenomen in een Azure Blob-opslag.
2. Wanneer het opnemen van gegevens voltooid is, wordt er een gebeurtenis gegenereerd en verzonden naar een Azure-gebeurtenisraster. 
3. Het gebeurtenisraster stuurt deze gebeurtenisgegevens door naar een Azure-functie-app.
4. De functie-app maakt gebruik van de blob-URL in de gebeurtenisgegevens om de blob op te halen uit de opslag. 
5. De functie-app migreert de blob-gegevens naar Azure Synapse Analytics. 

In dit artikel voert u de volgende stappen uit:

> [!div class="checklist"]
> - Voor de zelfstudie de vereiste infrastructuur implementeren
> - Code publiceren naar een Azure Functions-app
> - Een Event Grid-abonnement maken 
> - Voorbeeldgegevens streamen naar Event Hubs
> - Vastgelegde gegevens verifiëren in Azure Synapse Analytics

## <a name="prerequisites"></a>Vereisten
U hebt het volgende nodig om deze zelfstudie te voltooien:

- Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
- [Visual studio 2019](https://www.visualstudio.com/vs/) met werkbelastingen voor: .NET-desktopontwikkeling, Azure-ontwikkeling, ASP.NET- en webontwikkeling, Node.js-ontwikkeling en Python-ontwikkeling.
- Download het [voorbeeldproject EventHubsCaptureEventGridDemo](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) op uw computer.
    - WindTurbineDataGenerator: een eenvoudige uitgever waarmee voorbeeldgegevens van windturbines worden verzonden naar een event hub waarin gegevens kunnen worden vastgelegd
    - FunctionDWDumper: een Azure-functie die een Event Grid-melding ontvangt wanneer een Avro-bestand wordt vastgelegd in de Azure Storage Blob. Het URI-pad van de blob wordt ontvangen en de inhoud wordt gelezen, waarna deze gegevens naar Azure Synapse Analytics (toegewezen SQL-pool) worden gepusht.

## <a name="deploy-the-infrastructure"></a>De infrastructuur implementeren
In deze stap implementeert u de vereiste infrastructuur met behulp van een [Resource Manager-sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json). Wanneer u de sjabloon implementeert, worden de volgende resources gemaakt:

* Gebeurtenishub waarbij de functie Capture is ingeschakeld.
* Opslagaccount voor de opgenomen bestanden. 
* App Service-plan voor het hosten van de functie-app
* Functie-app voor het verwerken van de gebeurtenis
* SQL Server voor het hosten van het datawarehouse
* Azure Synapse Analytics (toegewezen SQL-pool) voor het opslaan van de gemigreerde gegevens

### <a name="use-azure-cli-to-deploy-the-infrastructure"></a>Met de Azure CLI de infrastructuur implementeren

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). 
2. Selecteer de knop **Cloud Shell** bovenaan.

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/azure-portal.png" alt-text="Azure-portal":::
3. U ziet onderin de browser dat Cloud Shell wordt geopend.

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/launch-cloud-shell.png" alt-text="Cloud Shell":::
4. Als in Cloud Shell een optie wordt weergegeven om te kiezen tussen **Bash** en **PowerShell**, kiest u **Bash**. 
5. Als u Cloud Shell voor het eerst gebruikt, maakt u een opslagaccount door **Create storage** (Opslag maken) te selecteren. Voor het opslaan van sommige bestanden in Azure Cloud Shell is een Azure-opslagaccount vereist. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/create-storage-cloud-shell.png" alt-text="Opslag voor Cloud Shell maken":::
6. Wacht totdat Cloud Shell is geïnitialiseerd. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/cloud-shell-initialized.png" alt-text="Cloud Shell geïnitialiseerd":::
1. Maak een Azure-resourcegroep door de volgende CLI-opdracht uit te voeren: 
    1. Kopieer en plak de volgende opdracht in het Cloud Shell-venster. Wijzig de naam en locatie van de resourcegroep als u dat wilt.

        ```azurecli
        az group create -l eastus -n rgDataMigration
        ```
    2. Druk op **ENTER**. 

        Hier volgt een voorbeeld:
    
        ```azurecli
        user@Azure:~$ az group create -l eastus -n rgDataMigration
        {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/rgDataMigration",
          "location": "eastus",
          "managedBy": null,
          "name": "rgDataMigration",
          "properties": {
            "provisioningState": "Succeeded"
          },
          "tags": null
        }
        ```
2. Implementeer alle resources die worden vermeld in de vorige sectie (gebeurtenishub, opslagaccount, functie-app, Azure Synapse Analytics) door de volgende CLI-opdracht uit te voeren: 
    1. Kopieer en plak de opdracht in het Cloud Shell-venster. U kunt de opdracht ook kopiëren en plakken in een editor naar keuze, waarden instellen en de opdracht vervolgens kopiëren naar Cloud Shell. 

        > [!IMPORTANT]
        > Geef waarden op voor de volgende entiteiten voordat u de opdracht uitvoert: 
        > - Naam van de resourcegroep die u eerder hebt gemaakt.
        > - Naam voor de naamruimte van de gebeurtenishub. 
        > - Naam voor de gebeurtenishub. U kunt de waarde laten zoals deze is (hubdatamigration).
        > - Naam voor de SQL-server.
        > - Naam en wachtwoord van de SQL-gebruiker. 
        > - Naam voor de database.
        > - Naam van het opslagaccount. 
        > - Naam voor de functie-app. 


        ```azurecli
        az deployment group create \
            --resource-group rgDataMigration \
            --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
            --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
        ```
    3.  Druk op **Enter** in het Cloud Shell-venster om de opdracht uit te voeren. Dit proces kan enige tijd duren omdat u een groot aantal bronnen maakt. Controleer in het resultatenvenster van de opdracht of er geen fouten zijn opgetreden. 
1. Sluit Cloud Shell met de knop **Cloud Shell** in de portal of met de knop **X** in de rechterbovenhoek van het Cloud Shell-venster. 

### <a name="verify-that-the-resources-are-created"></a>Controleer of de resources zijn gemaakt

1. Selecteer **Resourcegroepen** in het linkermenu van Azure Portal. 
2. Filter de lijst met resourcegroepen door de naam van uw resourcegroep in te voeren in het zoekvak. 
3. Selecteer uw resourcegroep in de lijst.

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/select-resource-group.png" alt-text="Uw resourcegroep selecteren":::
4. Controleer of u de volgende resources in de resourcegroep ziet:

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/resources-in-resource-group.png" alt-text="Resources in de resourcegroep" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/resources-in-resource-group.png":::

### <a name="create-a-table-in-azure-synapse-analytics"></a>Een tabel maken in Azure Synapse Analytics
Maak een tabel in uw datawarehouse door het script [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql) uit te voeren. Gebruik hiervoor Visual Studio of de query-editor in de portal. De volgende stappen laten zien hoe u de query-editor gebruikt: 

1. Selecteer uw **toegewezen SQL-pool** in de lijst met resources in de resourcegroep. 
2. Selecteer op de pagina **Toegewezen SQL-pool** in de sectie **Algemene taken** in het linkermenu **Query-editor (preview)** . 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/sql-data-warehouse-page.png" alt-text="Azure Synapse Analytics-pagina":::
2. Voer de naam van **gebruiker** en het **wachtwoord** voor de SQL-server in, en selecteer **OK**. Voer de volgende stappen uit als u een bericht ziet over het toestaan van toegang tot de SQL-server aan uw client:
    1. Selecteer de koppeling: **Serverfirewall instellen**. 
    2. Selecteer op de pagina **Firewall-instellingen** de optie **IP-adres van client toevoegen** op de werkbalk en selecteer vervolgens **Opslaan** op de werkbalk. 
    3. Selecteer **OK** in het succesbericht.
    4. Ga terug naar de pagina **Toegewezen SQL-pool** en selecteer in het linkermenu **Query-editor (preview)** . 
    5. Voer een **gebruiker** en **wachtwoord** in en selecteer **OK**. 
1. Kopieer en plak het volgende SQL-script in het queryvenster: 

    ```sql
    CREATE TABLE [dbo].[Fact_WindTurbineMetrics] (
        [DeviceId] nvarchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL, 
        [MeasureTime] datetime NULL, 
        [GeneratedPower] float NULL, 
        [WindSpeed] float NULL, 
        [TurbineSpeed] float NULL
    )
    WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    ```

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/run-sql-query.png" alt-text="SQL-query uitvoeren":::
5. Houd dit tabblad of venster geopend zodat u kunt controleren of de gegevens aan het einde van de zelfstudie zijn gemaakt. 

### <a name="update-the-function-runtime-version"></a>De runtime-versie van de functie bijwerken

1. Open een ander tabblad in de webbrowser en navigeer naar [Azure Portal](https://portal.azure.com).
1. Selecteer **Resourcegroepen** in het linkermenu van Azure Portal.
1. Selecteer de resourcegroep waarin de functie-app bestaat. 
1. Selecteer de **functie-app** in de lijst met resources in de resourcegroep.
1. Selecteer **Configuratie** onder **Instellingen** in het linkermenu. 
1. Schakel over naar het tabblad **Runtime-instellingen van de functie** in het rechter deelvenster. 
1. Werk de **runtime-versie** bij naar **~3**. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/function-runtime-version.png" alt-text="De runtime-versie van de functie bijwerken":::
6. Selecteer **Opslaan** op de werkbalk. 
1. Selecteer **Doorgaan** in het bevestigingsvenster **Wijzigingen opslaan**. 

## <a name="publish-the-azure-functions-app"></a>De Azure Functions-app publiceren

1. Start Visual Studio.
2. Open de oplossing **EventHubsCaptureEventGridDemo.sln**, die u eerder hebt gedownload van de [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo)-opslagplaats als onderdeel van de vereisten. U kunt deze vinden in de map `/samples/e2e/EventHubsCaptureEventGridDemo`. 
3. Klik in Solution Explorer met de rechtermuisknop op het project **FunctionEGDWDumper** en selecteer **Publiceren**.
4. Als u het volgende scherm ziet, selecteert u **Start**. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/start-publish-button.png" alt-text="De knop Start in de sectie Publiceren.":::
5. In het dialoogvenster **Publiceren** selecteert u **Azure** voor **Doel** en vervolgens **Volgende**. 
6. Selecteer achtereenvolgens **Azure-functie-app** en **Volgende**.
7. Selecteer uw Azure-abonnement op het tabblad **Functions-instantie**, vouw de resourcegroep uit en selecteer vervolgens uw functie-app en **Voltooien**. Als u dit nog niet hebt gedaan, moet u zich aanmelden bij uw Azure-account. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/publish-select-function-app.png" alt-text="Uw functie-app selecteren":::
8. Op de pagina **Publiceren** in de sectie **Service-afhankelijkheden** selecteert u **Configureren** bij **Opslag**. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/publish-storage-configure-link.png" alt-text="Selecteer koppeling configureren bij afhankelijkheid opslagservice":::
1. Voer de volgende stappen uit op de pagina **Afhankelijkheid configureren**: 
    1. Selecteer het **opslagaccount** dat u eerder hebt gemaakt, en selecteer vervolgens **Volgende**. 

        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/select-dependency-storage.png" alt-text="Opslagaccount selecteren":::
    10. Geef een **naam op voor de verbindingsreeks**, selecteer **Geen** bij de optie **Verbindingsreeks opslaan** en selecteer vervolgens **Volgende**. 
    
        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/dependency-storage-connection-string.png" alt-text="Naam verbindingsreeks opgeven":::      
    1. Wis de opties **C#-codebestand** en **Geheimenarchief** en selecteer vervolgens **Voltooien**.  
    
        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/dependency-storage-changes-summary.png" alt-text="Overzicht van wijzigingen controleren":::
1. Als Visual Studio het profiel heeft geconfigureerd, selecteert u **Publish**.

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/select-publish.png" alt-text="Selecteer Publish":::
2. Op het tabblad waar de pagina **Azure-functie** is geopend, selecteert u **Functies** in het menu aan de linkerkant. Controleer of de functie **EventGridTriggerMigrateData** wordt weergegeven in de lijst. Als dat niet het geval is, probeer dan opnieuw te publiceren vanuit Visual Studio en vernieuw vervolgens de pagina in de portal. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/confirm-function-creation.png" alt-text="Het maken van de functie bevestigen":::    

Nadat de functie is gepubliceerd, kunt u zich abonneren op de gebeurtenis.

## <a name="subscribe-to-the-event"></a>Abonneren op de gebeurtenis

1. Navigeer in een nieuw tabblad of een nieuw venster van een webbrowser naar [Azure Portal](https://portal.azure.com).
2. Selecteer **Resourcegroepen** in het linkermenu van Azure Portal. 
3. Filter de lijst met resourcegroepen door de naam van uw resourcegroep in te voeren in het zoekvak. 
4. Selecteer uw resourcegroep in de lijst.
1. Selecteer de **Event Hubs-naamruimte** in de lijst met resources.
1. Selecteer op de pagina **Event Hubs-naamruimte** de optie **Gebeurtenissen** in het linkermenu en selecteer vervolgens **+ Gebeurtenisabonnement** op de werkbalk. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/event-hub-add-subscription-link.png" alt-text="Koppeling voor gebeurtenisabonnement toevoegen op de pagina evenementen voor een Event Hubs-naamruimte":::
1. Voer op de pagina **Gebeurtenisabonnement maken** de volgende stappen uit:
    1. Voer een naam in voor het **gebeurtenisabonnement**. 
    1. Voer een naam in voor het **systeemonderwerp**. Een systeemonderwerp biedt een eindpunt voor het verzenden van gebeurtenissen door de afzender. Zie [Systeemonderwerpen](../articles/event-grid/system-topics.md) voor meer informatie
    1. Selecteer **Azure-functie** als het **eindpunttype**.
    1. Selecteer de koppeling voor het **eindpunt**.
    1. Voer de volgende stappen uit op de pagina **Azure-functie selecteren** als deze niet automatisch zijn gevuld.
        1. Selecteer het Azure-abonnement dat de Azure-functie bevat. 
        1. Selecteer de resourcegroep voor de functie. 
        1. Selecteer de functie-app.
        1. Selecteer de implementatiesite. 
        1. Selecteer de functie **EventGridTriggerMigrateData**. 
    1. Selecteer op de pagina **Azure-functie selecteren** de optie **Selectie bevestigen**.
    1. Selecteer vervolgens **Maken** als u terug bent op de pagina **Gebeurtenisabonnement maken**. 
    
        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/event-subscription-select-function.png" alt-text="Een gebeurtenisabonnement maken met behulp van de functie" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/event-subscription-select-function.png":::
1. Controleer of het gebeurtenisabonnement is gemaakt. Ga naar het tabblad **Gebeurtenisabonnementen** op de pagina **Gebeurtenissen** voor de Event Hubs-naamruimte. 
    
    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/confirm-event-subscription.png" alt-text="Gebeurtenisabonnement bevestigen" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/confirm-event-subscription.png":::
1. Selecteer het App Service-plan (niet de App Service) in de lijst met resources in de resourcegroep. 

## <a name="run-the-app-to-generate-data"></a>De app uitvoeren om gegevens te genereren
U bent nu klaar met het instellen van uw event hub, toegewezen SQL-pool (voorheen SQL Data Warehouse), Azure-functie-app en gebeurtenisabonnement. Voordat u een toepassing gaat uitvoeren die gegevens voor Event Hub genereert, moet u nog enkele waarden configureren.

1. Navigeer in Azure Portal naar uw resourcegroep zoals u eerder hebt gedaan. 
2. Selecteer de Event Hubs-naamruimte.
3. Op de pagina **Event Hubs-naamruimte** selecteert u **Beleid voor gedeelde toegang** in het menu links.
4. Selecteer **RootManageSharedAccessKey** in de lijst met beleidsregels. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/event-hub-namespace-shared-access-policies.png" alt-text="Pagina Gedeeld toegangsbeleid voor een Event Hubs-naamruimte":::    
1. Selecteer de knop Kopiëren naast het tekstvak **Verbindingsreeks - primaire sleutel**. 
1. Ga terug naar uw Visual Studio-oplossing. 
1. Klik met de rechtermuisknop op het project **WindTurbineDataGenerator** en selecteer **Instellen als opstartproject**. 
1. Open **program.cs** in het project WindTurbineDataGenerator.
1. Vervang `<EVENT HUBS NAMESPACE CONNECTION STRING>` door de verbindingsreeks die u uit de portal hebt gekopieerd. 
1. Vervang `<EVENT HUB NAME>` door de naam van de event hub. 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```
6. Bouw de oplossing. Voer de toepassing **WindTurbineGenerator.exe** uit. 
7. Na een paar minuten kunt u in het andere browsertabblad waar u het queryvenster hebt geopend, de tabel in uw datawarehouse doorzoeken op de gemigreerde gegevens.

    ```sql
    select * from [dbo].[Fact_WindTurbineMetrics]    
    ```

    ![Queryresultaten](media/event-grid-event-hubs-functions-synapse-analytics/query-results.png)

## <a name="monitor-the-solution"></a>De oplossing bewaken
Deze sectie helpt u bij het bewaken van de oplossing of het oplossen van problemen ermee. 

### <a name="view-captured-data-in-the-storage-account"></a>Vastgelegde gegevens in het opslagaccount weergeven
1. Navigeer naar de resourcegroep en selecteer het opslagaccount dat wordt gebruikt voor het vastleggen van gebeurtenisgegevens. 
1. Selecteer **Opslagverkenner (preview)** op de pagina **Opslagaccount**.
1. Vouw **BLOBCONTAINERS** uit en selecteer **windturbinecapture**. 
1. Open de map met dezelfde naam als uw **Event Hubs-naamruimte** in het rechterdeelvenster. 
1. Open de map met dezelfde naam als uw event hub (**hubdatamigration**). 
1. Als u de mappen doorzoekt, zult u zien dat de AVRO-bestanden er zich in bevinden. Hier volgt een voorbeeld:

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/storage-captured-file.png" alt-text="Vastgelegd bestand in de opslag" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/storage-captured-file.png":::
    

### <a name="verify-that-the-event-grid-trigger-invoked-the-function"></a>Controleren of de Event Grid-trigger de functie heeft aangeroepen
1. Ga naar de resourcegroep en selecteer de functie-app. 
1. Selecteer **Functies** in het menu links.
1. Selecteer de functie **EventGridTriggerMigrateData** in de lijst. 
1. Selecteer **Bewaken** op de pagina **Functie** in het linkermenu. 
1. Selecteer **Configureren** om Application Insights te configureren voor het vastleggen van aanroeplogboeken. 
1. Maak een nieuwe **Application Insights**-resource of gebruik een bestaande resource. 
1. Ga terug naar de pagina **Bewaken** voor de functie. 
1. Controleer of de clienttoepassing (**WindTurbineDataGenerator**) die de gebeurtenissen verzendt, nog steeds wordt uitgevoerd. Voer de app uit als dat niet het geval is. 
1. Wacht enkele minuten (5 minuten of langer) en selecteer de knop **Vernieuwen** om functieaanroepen weer te geven.    

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/function-invocations.png" alt-text="Functieaanroepen":::
1. Selecteer een aanroep om details ervan weer te geven.

    Event Grid distribueert gebeurtenisgegevens naar de abonnees. In het volgende voorbeeld ziet u hoe gebeurtenisgegevens worden gegenereerd wanneer het streamen van gegevens via een gebeurtenishub wordt vastgelegd in een blob. Merk op dat de eigenschap `fileUrl` in het object `data`-verwijst naar de blob in de opslag. De functie-app maakt gebruik van deze URL om het blob-bestand met de opgenomen gegevens op te halen.

    ```json
    {
        "topic": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourcegroups/rgDataMigration/providers/Microsoft.EventHub/namespaces/spehubns1207",
        "subject": "hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "id": "4538f1a5-02d8-4b40-9f20-36301ac976ba",
        "data": {
            "fileUrl": "https://spehubstorage1207.blob.core.windows.net/windturbinecapture/spehubns1207/hubdatamigration/0/2020/12/07/21/49/12.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "0",
            "sizeInBytes": 473444,
            "eventCount": 2800,
            "firstSequenceNumber": 55500,
            "lastSequenceNumber": 58299,
            "firstEnqueueTime": "2020-12-07T21:49:12.556Z",
            "lastEnqueueTime": "2020-12-07T21:50:11.534Z"
        },
        "dataVersion": "1",
        "metadataVersion": "1",
        "eventTime": "2020-12-07T21:50:12.7065524Z"
    }
    ```

### <a name="verify-that-the-data-is-stored-in-the-dedicated-sql-pool"></a>Controleren of de gegevens zijn opgeslagen in de toegewezen SQL-pool
Zoek op het browsertabblad waar u het queryvenster hebt geopend, in de tabel in uw toegewezen SQL-pool naar de gemigreerde gegevens.

![Queryresultaten](media/event-grid-event-hubs-functions-synapse-analytics/query-results.png)


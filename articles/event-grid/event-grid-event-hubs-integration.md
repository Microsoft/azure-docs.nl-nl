---
title: 'Zelfstudie: Event Hubs-gegevens naar datawarehouse verzenden - Event Grid'
description: 'Zelfstudie: In dit artikel wordt beschreven hoe u met Azure Event Grid en Event Hubs gegevens kunt migreren naar Azure Synapse Analytics. Er wordt een Azure-functie gebruikt om een |Capture-bestand op te halen.'
ms.topic: tutorial
ms.date: 07/07/2020
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 42a2f7fd557970328f6d88b08e296317cecd8c66
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462149"
---
# <a name="tutorial-stream-big-data-into-a-data-warehouse"></a>Zelfstudie: Big data streamen naar een datawarehouse
Azure [Event Grid](overview.md) is een intelligente service voor het routeren van gebeurtenissen waarmee u kunt reageren op meldingen (gebeurtenissen) van apps en services. Het kan bijvoorbeeld een Azure-functie activeren voor het verwerken van Event Hubs-gegevens die zijn opgenomen in een Azure Blob-opslag of Azure Data Lake Storage en de gegevens naar andere gegevensopslagplaatsen migreren. Dit [Event Hubs en Event Grid-voorbeeld](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) laat zien hoe u Event Hubs gebruikt met Event Grid voor het naadloos migreren van opgenomen gegevens van Event Hubs uit blob-opslag naar Azure Synapse Analytics.

![Overzicht van toepassing](media/event-grid-event-hubs-integration/overview.png)

In dit diagram ziet u de werkstroom van de oplossing die u in deze zelfstudie maakt: 

1. Gegevens die worden verzonden naar een Azure Event Hub worden opgenomen in een Azure Blob-opslag.
2. Wanneer het opnemen van gegevens voltooid is, wordt er een gebeurtenis gegenereerd en verzonden naar een Azure-gebeurtenisraster. 
3. Het gebeurtenisraster stuurt deze gebeurtenisgegevens door naar een Azure-functie-app.
4. De functie-app maakt gebruik van de blob-URL in de gebeurtenisgegevens om de blob op te halen uit de opslag. 
5. De functie-app migreert de blob-gegevens naar Azure Synapse Analytics. 

In dit artikel voert u de volgende stappen uit:

> [!div class="checklist"]
> * De infrastructuur implementeren met behulp van een Azure Resource Manager-sjabloon: een gebeurtenishub, een opslagaccount, een functie-app en een toegewezen SQL-pool.
> * Een tabel maken in de toegewezen SQL-pool.
> * Code toevoegen aan de functie-app.
> * Abonneren op de gebeurtenis. 
> * App uitvoeren die gegevens naar de gebeurtenishub verzendt.
> * Gemigreerde gegevens weergeven in het datawarehouse.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

U hebt het volgende nodig om deze zelfstudie te voltooien:

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
* [Visual studio 2019](https://www.visualstudio.com/vs/) met werkbelastingen voor: .NET-desktopontwikkeling, Azure-ontwikkeling, ASP.NET- en webontwikkeling, Node.js-ontwikkeling en Python-ontwikkeling.
* Download het [voorbeeldproject EventHubsCaptureEventGridDemo](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) op uw computer.

## <a name="deploy-the-infrastructure"></a>De infrastructuur implementeren
In deze stap implementeert u de vereiste infrastructuur met behulp van een [Resource Manager-sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json). Wanneer u de sjabloon implementeert, worden de volgende resources gemaakt:

* Gebeurtenishub waarbij de functie Capture is ingeschakeld.
* Opslagaccount voor de opgenomen bestanden. 
* App Service-plan voor het hosten van de functie-app
* Functie-app voor het verwerken van de gebeurtenis
* SQL Server voor het hosten van het datawarehouse
* Azure Synapse Analytics voor het opslaan van de gemigreerde gegevens

### <a name="launch-azure-cloud-shell-in-azure-portal"></a>Azure Cloud Shell openen in Azure Portal

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). 
2. Selecteer de knop **Cloud Shell** bovenaan.

    ![Azure Portal](media/event-grid-event-hubs-integration/azure-portal.png)
3. U ziet onderin de browser dat Cloud Shell wordt geopend.

    ![Cloud Shell](media/event-grid-event-hubs-integration/launch-cloud-shell.png) 
4. Als in Cloud Shell een optie wordt weergegeven om te kiezen tussen **Bash** en **PowerShell**, kiest u **Bash**. 
5. Als u Cloud Shell voor het eerst gebruikt, maakt u een opslagaccount door **Create storage** (Opslag maken) te selecteren. Voor het opslaan van sommige bestanden in Azure Cloud Shell is een Azure-opslagaccount vereist. 

    ![Schermopname van het dialoogvenster ‘U hebt geen opslag gekoppeld’ met de knop ‘Opslag maken’ geselecteerd.](media/event-grid-event-hubs-integration/create-storage-cloud-shell.png)
6. Wacht totdat Cloud Shell is geïnitialiseerd. 

    ![Opslag voor Cloud Shell maken](media/event-grid-event-hubs-integration/cloud-shell-initialized.png)


### <a name="use-azure-cli"></a>Azure CLI gebruiken

1. Maak een Azure-resourcegroep door de volgende CLI-opdracht uit te voeren: 
    1. Kopieer en plak de volgende opdracht in het Cloud Shell-venster. Wijzig de naam en locatie van de resourcegroep als u dat wilt.

        ```azurecli
        az group create -l eastus -n rgDataMigration
        ```
    2. Druk op **ENTER**. 

        Hier volgt een voorbeeld:
    
        ```azurecli
        user@Azure:~$ az group create -l eastus -n ehubegridgrp
        {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/ehubegridgrp",
          "location": "eastus",
          "managedBy": null,
          "name": "ehubegridgrp",
          "properties": {
            "provisioningState": "Succeeded"
          },
          "tags": null
        }
        ```
2. Implementeer alle resources die worden vermeld in de vorige sectie (gebeurtenishub, opslagaccount, functie-app, Azure Synapse Analytics) door de volgende CLI-opdracht uit te voeren: 
    1. Kopieer en plak de opdracht in het Cloud Shell-venster. U kunt de opdracht ook kopiëren en plakken in een editor naar keuze, waarden instellen en de opdracht vervolgens kopiëren naar Cloud Shell. 

        ```azurecli
        az group deployment create \
            --resource-group rgDataMigration \
            --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
            --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
        ```
    2. Geef waarden op voor de volgende entiteiten:
        1. Naam van de resourcegroep die u eerder hebt gemaakt.
        2. Naam voor de naamruimte van de gebeurtenishub. 
        3. Naam voor de gebeurtenishub. U kunt de waarde laten zoals deze is (hubdatamigration).
        4. Naam voor de SQL-server.
        5. Naam en wachtwoord van de SQL-gebruiker. 
        6. Naam voor Azure Synapse Analytics
        7. Naam van het opslagaccount. 
        8. Naam voor de functie-app. 
    3.  Druk op **Enter** in het Cloud Shell-venster om de opdracht uit te voeren. Dit proces kan enige tijd duren omdat u een groot aantal bronnen maakt. Controleer in het resultatenvenster van de opdracht of er geen fouten zijn opgetreden. 
    

### <a name="use-azure-powershell"></a>Azure PowerShell gebruiken

1. Activeer de PowerShell-modus in Azure Cloud Shell. Selecteer de pijl-omlaag in de linkerbovenhoek van Azure Cloud Shell en selecteer **PowerShell**.

    ![Overschakelen naar PowerShell](media/event-grid-event-hubs-integration/select-powershell-cloud-shell.png)
2. Maak een Azure-resourcegroep door de volgende opdracht uit te voeren: 
    1. Kopieer en plak de volgende opdracht in het Cloud Shell-venster.

        ```powershell
        New-AzResourceGroup -Name rgDataMigration -Location eastus
        ```
    2. Geef een naam op voor de **resourcegroep**.
    3. Druk op Enter. 
3. Implementeer alle resources die worden vermeld in de vorige sectie (gebeurtenishub, opslagaccount, functie-app, Azure Synapse Analytics) door de volgende opdracht uit te voeren:
    1. Kopieer en plak de opdracht in het Cloud Shell-venster. U kunt de opdracht ook kopiëren en plakken in een editor naar keuze, waarden instellen en de opdracht vervolgens kopiëren naar Cloud Shell. 

        ```powershell
        New-AzResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
        ```
    2. Geef waarden op voor de volgende entiteiten:
        1. Naam van de resourcegroep die u eerder hebt gemaakt.
        2. Naam voor de naamruimte van de gebeurtenishub. 
        3. Naam voor de gebeurtenishub. U kunt de waarde laten zoals deze is (hubdatamigration).
        4. Naam voor de SQL-server.
        5. Naam en wachtwoord van de SQL-gebruiker. 
        6. Naam voor Azure Synapse Analytics
        7. Naam van het opslagaccount. 
        8. Naam voor de functie-app. 
    3.  Druk op **Enter** in het Cloud Shell-venster om de opdracht uit te voeren. Dit proces kan enige tijd duren omdat u een groot aantal bronnen maakt. Controleer in het resultatenvenster van de opdracht of er geen fouten zijn opgetreden. 

### <a name="close-the-cloud-shell"></a>Sluit Cloud Shell 
Sluit Cloud Shell met de knop **Cloud Shell** in de portal of met de knop **X** in de rechterbovenhoek van het Cloud Shell-venster. 

### <a name="verify-that-the-resources-are-created"></a>Controleer of de resources zijn gemaakt

1. Selecteer **Resourcegroepen** in het linkermenu van Azure Portal. 
2. Filter de lijst met resourcegroepen door de naam van uw resourcegroep in te voeren in het zoekvak. 
3. Selecteer uw resourcegroep in de lijst.

    ![Uw resourcegroep selecteren](media/event-grid-event-hubs-integration/select-resource-group.png)
4. Controleer of u de volgende resources in de resourcegroep ziet:

    ![Resources in de resourcegroep](media/event-grid-event-hubs-integration/resources-in-resource-group.png)

### <a name="create-a-table-in-azure-synapse-analytics"></a>Een tabel maken in Azure Synapse Analytics
Maak een tabel in uw datawarehouse door het script [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql) uit te voeren. Gebruik hiervoor Visual Studio of de query-editor in de portal. De volgende stappen laten zien hoe u de query-editor gebruikt: 

1. Selecteer uw **toegewezen SQL-pool** in de lijst met resources in de resourcegroep. 
2. Selecteer in het linkermenu op de Azure Synapse Analytics-pagina **Query-editor (preview)** . 

    ![Azure Synapse Analytics-pagina](media/event-grid-event-hubs-integration/sql-data-warehouse-page.png)
2. Voer de naam van **gebruiker** en het **wachtwoord** voor de SQL-server in, en selecteer **OK**. U moet uw client-IP-adres mogelijk aan de firewall toevoegen om u aan te kunnen melden bij de SQL-server. 

    ![SQL-serververificatie](media/event-grid-event-hubs-integration/sql-server-authentication.png)
4. Kopieer en plak het volgende SQL-script in het queryvenster: 

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

    ![SQL-query uitvoeren](media/event-grid-event-hubs-integration/run-sql-query.png)
5. Houd dit tabblad of venster geopend zodat u kunt controleren of de gegevens aan het einde van de zelfstudie zijn gemaakt. 

### <a name="update-the-function-runtime-version"></a>De runtime-versie van de functie bijwerken

1. Selecteer **Resourcegroepen** in het linkermenu van Azure Portal.
2. Selecteer de resourcegroep waarin de functie-app bestaat. 
3. Selecteer de functie-app van het type **App Service** in de lijst met resources in de resourcegroep.
4. Selecteer **Configuratie** onder **Instellingen** in het linkermenu. 
5. Schakel over naar het tabblad **Runtime-instellingen van de functie** in het rechter deelvenster. 
5. Werk de **runtime-versie** bij naar **~3**. 

    ![De runtime-versie van de functie bijwerken](media/event-grid-event-hubs-integration/function-runtime-version.png)
    

## <a name="publish-the-azure-functions-app"></a>De Azure Functions-app publiceren

1. Start Visual Studio.
2. Open de oplossing **EventHubsCaptureEventGridDemo.sln**, die u eerder hebt gedownload van de [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo)-opslagplaats als onderdeel van de vereisten.
3. Klik in Solution Explorer met de rechtermuisknop op **FunctionEGDWDumper** en selecteer **Publish**.

   ![Functie-app publiceren](media/event-grid-event-hubs-integration/publish-function-app.png)
4. Als u het volgende scherm ziet, selecteert u **Start**. 

   ![Schermopname van Visual Studio met de knop ‘Starten’ in de sectie Publiceren.](media/event-grid-event-hubs-integration/start-publish-button.png) 
5. In het dialoogvenster **Publiceren** selecteert u **Azure** voor **Doel** en vervolgens **Volgende**. 

   ![De knop Start voor het publiceren](media/event-grid-event-hubs-integration/publish-select-azure.png)
6. Selecteer achtereenvolgens **Azure-functie-app** en **Volgende**. 

   ![Azure-functie-app selecteren - Windows](media/event-grid-event-hubs-integration/select-azure-function-windows.png)
7. Selecteer uw Azure-abonnement op het tabblad **Functions-instantie**, vouw de resourcegroep uit en selecteer vervolgens uw functie-app en **Voltooien**. Als u dit nog niet hebt gedaan, moet u zich aanmelden bij uw Azure-account. 

   ![Uw functie-app selecteren](media/event-grid-event-hubs-integration/publish-select-function-app.png)
8. In het gedeelte **Service-afhankelijkheden** selecteert u **Configureren**.
9. Selecteer op de pagina **Afhankelijkheid configureren** het opslagaccount dat u eerder hebt gemaakt en selecteer vervolgens **Volgende**. 
10. Behoud de instellingen voor de naam en waarde van de verbindingsreeks en selecteer **Volgende**.
11. Wis de optie **Secrets Store** en selecteer vervolgens **Voltooien**.  
8. Als Visual Studio het profiel heeft geconfigureerd, selecteert u **Publish**.

   ![Selecteer Publish](media/event-grid-event-hubs-integration/select-publish.png)

Nadat de functie is gepubliceerd, kunt u zich abonneren op de gebeurtenis.

## <a name="subscribe-to-the-event"></a>Abonneren op de gebeurtenis

1. Navigeer in een nieuw tabblad of een nieuw venster van een webbrowser naar [Azure Portal](https://portal.azure.com).
2. Selecteer **Resourcegroepen** in het linkermenu van Azure Portal. 
3. Filter de lijst met resourcegroepen door de naam van uw resourcegroep in te voeren in het zoekvak. 
4. Selecteer uw resourcegroep in de lijst.

    ![Uw resourcegroep selecteren](media/event-grid-event-hubs-integration/select-resource-group.png)
4. Selecteer het App Service-plan (niet de App Service) in de lijst met resources in de resourcegroep. 
5. Selecteer **Apps** in het menu links op de pagina App Service-plan, en selecteer de functie-app. 

    ![Selecteer uw functie-app](media/event-grid-event-hubs-integration/select-function-app-app-service-plan.png)
6. Vouw achtereenvolgens de functie-app en de functies uit en selecteer vervolgens uw functie. 
7. Selecteer **Event Grid-abonnement toevoegen** op de werkbalk. 

    ![Selecteer uw Azure-functie](media/event-grid-event-hubs-integration/select-function-add-button.png)
8. Ga als volgt te werk op de pagina **Event Grid-abonnement toevoegen**: 
    1. Voer op de pagina **Gebeurtenisabonnementdetails** een naam voor het abonnement in, bijvoorbeeld captureEventSub, en selecteer **Maken**. 
    2. Voer de volgende acties uit in de sectie **Onderwerpdetails**:
        1. Selecteer **Event Hubs-naamruimten** voor **Onderwerptypen**. 
        2. Selecteer uw Azure-abonnement.
        2. Selecteer de Azure-resourcegroep.
        3. Selecteer uw Event Hubs-naamruimte.
    3. In het gedeelte **GEBEURTENISTYPEN** moet u bevestigen dat het **Gemaakte opnamebestand** is geselecteerd voor **Filteren op gebeurtenistypen**. 
    4. In het gedeelte **EINDPUNTDETAILS** bevestigt u dat het **Eindpunttype** op **Azure Function** en het **Eindpunt** op de Azure-functie is ingesteld. 
    
        ![Event Grid-abonnement maken](media/event-grid-event-hubs-integration/create-event-subscription.png)

## <a name="run-the-app-to-generate-data"></a>De app uitvoeren om gegevens te genereren
U bent nu klaar met het instellen van uw gebeurtenishub, Azure Synapse Analytics, Azure-functie-app en gebeurtenisabonnement. Voordat u een toepassing gaat uitvoeren die gegevens voor Event Hub genereert, moet u nog enkele waarden configureren.

1. Navigeer in Azure Portal naar uw resourcegroep zoals u eerder hebt gedaan. 
2. Selecteer de Event Hubs-naamruimte.
3. Op de pagina **Event Hubs-naamruimte** selecteert u **Beleid voor gedeelde toegang** in het menu links.
4. Selecteer **RootManageSharedAccessKey** in de lijst met beleidsregels. 
5. Selecteer de knop Kopiëren naast het tekstvak **Verbindingsreeks - primaire sleutel**. 

    ![Verbindingsreeks voor de Event Hub-naamruimte](media/event-grid-event-hubs-integration/get-connection-string.png)
1. Ga terug naar uw Visual Studio-oplossing. 
2. Open **program.cs** in het project WindTurbineDataGenerator.
5. Vervang de twee constante waarden. Gebruik de gekopieerde waarde voor **EventHubConnectionString**. Gebruik **hubdatamigration** als de naam van de gebeurtenishub. Als u een andere naam voor de gebeurtenishub hebt gebruikt, geeft u die naam op. 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. Bouw de oplossing. Voer de toepassing **WindTurbineGenerator.exe** uit. 
7. Voer na een paar minuten een query voor de gemigreerde gegevens uit op de tabel in het datawarehouse.

    ![Queryresultaten](media/event-grid-event-hubs-integration/query-results.png)

### <a name="event-data-generated-by-the-event-hub"></a>Gebeurtenisgegevens die zijn gegenereerd door de gebeurtenishub
Event Grid distribueert gebeurtenisgegevens naar de abonnees. In het volgende voorbeeld ziet u hoe gebeurtenisgegevens worden gegenereerd wanneer het streamen van gegevens via een gebeurtenishub wordt vastgelegd in een blob. Merk op dat de eigenschap `fileUrl` in het object `data`-verwijst naar de blob in de opslag. De functie-app maakt gebruik van deze URL om het blob-bestand met de opgenomen gegevens op te halen.

```json
[
    {
        "topic": "/subscriptions/<guid>/resourcegroups/rgDataMigrationSample/providers/Microsoft.EventHub/namespaces/tfdatamigratens",
        "subject": "eventhubs/hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-08-31T19:12:46.0498024Z",
        "id": "14e87d03-6fbf-4bb2-9a21-92bd1281f247",
        "data": {
            "fileUrl": "https://tf0831datamigrate.blob.core.windows.net/windturbinecapture/tfdatamigratens/hubdatamigration/1/2017/08/31/19/11/45.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 249168,
            "eventCount": 1500,
            "firstSequenceNumber": 2400,
            "lastSequenceNumber": 3899,
            "firstEnqueueTime": "2017-08-31T19:12:14.674Z",
            "lastEnqueueTime": "2017-08-31T19:12:44.309Z"
        }
    }
]
```


## <a name="next-steps"></a>Volgende stappen

* Zie voor meer informatie over de verschillen in de Azure-berichtenservices [Kiezen tussen Azure-services die berichten bezorgen](compare-messaging-services.md).
* Zie [Een inleiding tot Event Grid](overview.md) voor een inleiding tot Event Grid.
* Zie [Event Hubs Capture inschakelen met behulp van Azure Portal](../event-hubs/event-hubs-capture-enable-through-portal.md) voor een inleiding tot Event Hubs Capture.
* Zie het Engelstalige artikel [An overview, how Event Hubs Capture integrates with Event Grid](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) voor meer informatie over het instellen en uitvoeren van het voorbeeld.

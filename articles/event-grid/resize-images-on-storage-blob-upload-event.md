---
title: 'Zelfstudie: Formaat van geüploade afbeeldingen automatisch wijzigen met Azure Event Grid'
description: 'Zelfstudie: U kunt instellen dat Azure Event Grid wordt geactiveerd als er blobs worden geüpload in Azure Storage. Dit is handig om afbeeldingsbestanden naar Azure Storage die worden geüpload naar Azure Storage te verzenden naar andere services, zoals Azure Functions, voor het aanpassen van het formaat en andere verbeteringen.'
ms.topic: tutorial
ms.date: 07/07/2020
ms.openlocfilehash: ca231fc65162fe38f4dcb8b8d5677ef42c7807bb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99550508"
---
# <a name="tutorial-automate-resizing-uploaded-images-using-event-grid"></a>Zelfstudie: Formaat van geüploade afbeeldingen automatisch wijzigen met Event Grid

[Azure Event Grid](overview.md) is een gebeurtenisservice voor de cloud. Met Event Grid kunt u abonnementen nemen op gebeurtenissen die worden gegenereerd door Azure-services of resources van derden.  

Deze zelfstudie is deel twee in een reeks zelfstudies over Azure Storage en is een vervolg op de [vorige zelfstudie over Storage][previous-tutorial] met als doel om zonder tussenkomst van een server automatisch miniaturen te genereren met behulp van Azure Event Grid en Azure Functions. Event Grid zorgt ervoor dat [Azure Functions](../azure-functions/functions-overview.md) kan reageren op gebeurtenissen van [Azure Blob-opslag](../storage/blobs/storage-blobs-introduction.md) om miniaturen van geüploade afbeeldingen te genereren. Een gebeurtenisabonnement wordt gekoppeld aan gebeurtenis waarmee Blob-opslag wordt gemaakt. Wanneer er een blob wordt toegevoegd aan een specifieke Blob-opslagcontainer, wordt er een functie-eindpunt aangeroepen. Aan de hand van gegevens die vanuit Event Grid worden doorgegeven aan de functiebinding, wordt er toegang verkregen tot de blob en wordt de miniatuurafbeelding gegenereerd.

Met behulp van de Azure CLI en Azure Portal kunt u de functionaliteit voor formaatwijziging toevoegen aan een bestaande app voor het uploaden van afbeeldingen.

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

![Schermopname van een gepubliceerde web-app in een browser voor de \.NET v12 SDK.](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

# <a name="nodejs-v10-sdk"></a>[Node.js V10 SDK](#tab/nodejsv10)

![Schermopname van een gepubliceerde web-app in een browser voor de \.NET v10 SDK.](./media/resize-images-on-storage-blob-upload-event/upload-app-nodejs-thumb.png)

---

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Azure Storage-account maken
> * Serverloze code implementeren met behulp van Azure Functions
> * Een gebeurtenisabonnement voor Blob-opslag maken in Event Grid

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Vereisten voor het voltooien van deze zelfstudie:

U moet de vorige zelfstudie over blobopslag hebben voltooid: [Afbeeldingsgegevens uploaden in de cloud met Azure Storage][previous-tutorial].

U hebt een [Azure-abonnement](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)nodig. Deze zelf studie werkt niet met het **gratis** abonnement. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze zelfstudie gebruikmaken van Azure CLI versie 2.0.14 of hoger. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

Als u Cloud Shell niet gebruikt, moet u eerst zich aanmelden met `az login`.

Als u de Event Grid-resourceprovider nog niet hebt geregistreerd in uw abonnement, doet u dat eerst.

```bash
az provider register --namespace Microsoft.EventGrid
```

```powershell
az provider register --namespace Microsoft.EventGrid
```

## <a name="create-an-azure-storage-account"></a>Een Azure Storage-account maken

Voor Azure Functions is een algemeen opslagaccount vereist. Naast het Blob-opslagaccount dat u in de vorige zelfstudie hebt gemaakt, moet u een afzonderlijk algemeen opslagaccount in de brongroep maken met behulp van de opdracht [az storage account create](/cli/azure/storage/account). Namen van opslagaccounts moeten tussen 3 en 24 tekens lang zijn en mogen alleen cijfers en kleine letters bevatten.

1. Stel een variabele in om daarin de naam op te nemen van de resourcegroep die u in de vorige zelfstudie hebt gemaakt.

    ```bash
    resourceGroupName="myResourceGroup"
    ```

    ```powershell
    $resourceGroupName="myResourceGroup"
    ```

1. Stel een variabele in die de locatie moet bevatten voor de resources die moeten worden gemaakt. 

    ```bash
    location="eastus"
    ```

    ```powershell
    $location="eastus"
    ```

1. Stel een variabele in voor de naam van het nieuwe opslagaccount dat voor Azure Functions vereist is.

    ```bash
    functionstorage="<name of the storage account to be used by the function>"
    ```

    ```powershell
    $functionstorage="<name of the storage account to be used by the function>"
    ```

1. Maak het opslagaccount voor de Azure-functie.

    ```bash
    az storage account create --name $functionstorage --location $location \
    --resource-group $resourceGroupName --sku Standard_LRS --kind StorageV2
    ```

    ```powershell
    az storage account create --name $functionstorage --location $location `
    --resource-group $resourceGroupName --sku Standard_LRS --kind StorageV2
    ```

## <a name="create-a-function-app"></a>Een functie-app maken  

U moet een functie-app hebben die als host fungeert voor de uitvoering van uw functie. De functie-app biedt een omgeving waarin uw functiecode zonder server kan worden uitgevoerd. Een functie-app maken met behulp van de opdracht [az functionapp create](/cli/azure/functionapp).

Geef de naam van uw eigen unieke functie-app op in de volgende opdracht. De naam van de functie-app wordt gebruikt als het standaard DNS-domein voor de functie-app. Om die reden moet de naam uniek zijn in alle apps in Azure.

1. Geef een naam op voor de functie-app die moet worden gemaakt.

    ```bash
    functionapp="<name of the function app>"
    ```

    ```powershell
    $functionapp="<name of the function app>"
    ```

1. Maak de Azure-functie.

    ```bash
    az functionapp create --name $functionapp --storage-account $functionstorage \
      --resource-group $resourceGroupName --consumption-plan-location $location \
      --functions-version 2
    ```

    ```powershell
    az functionapp create --name $functionapp --storage-account $functionstorage `
      --resource-group $resourceGroupName --consumption-plan-location $location `
      --functions-version 2
    ```

Nu moet u de functie-app configureren om verbinding te maken met het Blob-opslagaccount dat u in de [vorige zelfstudie][previous-tutorial] hebt gemaakt.

## <a name="configure-the-function-app"></a>De functie-app configureren

De functie vereist referenties voor het Blob-opslagaccount, die worden toegevoegd aan de toepassingsinstellingen van de functie-app met behulp van de opdracht [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings).

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

```bash
storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName \
  --name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName \
  --settings AzureWebJobsStorage=$storageConnectionString THUMBNAIL_CONTAINER_NAME=thumbnails \
  THUMBNAIL_WIDTH=100 FUNCTIONS_EXTENSION_VERSION=~2
```

```powershell
$storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName `
  --name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName `
  --settings AzureWebJobsStorage=$storageConnectionString THUMBNAIL_CONTAINER_NAME=thumbnails `
  THUMBNAIL_WIDTH=100 FUNCTIONS_EXTENSION_VERSION=~2
```

# <a name="nodejs-v10-sdk"></a>[Node.js V10 SDK](#tab/nodejsv10)

```bash
blobStorageAccountKey=$(az storage account keys list -g $resourceGroupName \
  -n $blobStorageAccount --query [0].value --output tsv)

storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName \
  --name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName \
  --settings FUNCTIONS_EXTENSION_VERSION=~2 BLOB_CONTAINER_NAME=thumbnails \
  AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount \
  AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey \
  AZURE_STORAGE_CONNECTION_STRING=$storageConnectionString
```

```powershell
$blobStorageAccountKey=$(az storage account keys list -g $resourceGroupName `
  -n $blobStorageAccount --query [0].value --output tsv)

$storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName `
  --name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName `
  --settings FUNCTIONS_EXTENSION_VERSION=~2 BLOB_CONTAINER_NAME=thumbnails `
  AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount `
  AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey `
  AZURE_STORAGE_CONNECTION_STRING=$storageConnectionString
```

---

De instelling `FUNCTIONS_EXTENSION_VERSION=~2` zorgt ervoor dat de functie-app wordt uitgevoerd met versie 2.x van de runtime van Azure Functions.

U kunt nu een codeproject van Functions implementeren naar deze functie-app.

## <a name="deploy-the-function-code"></a>De functiecode implementeren 

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

De C#-voorbeeldfunctie van resize is beschikbaar op [GitHub](https://github.com/Azure-Samples/function-image-upload-resize). Implementeer dit codeproject naar de functie-app met behulp van de opdracht [az functionapp deployment source config](/cli/azure/functionapp/deployment/source).

```bash
az functionapp deployment source config --name $functionapp --resource-group $resourceGroupName \
  --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/function-image-upload-resize
```

```powershell
az functionapp deployment source config --name $functionapp --resource-group $resourceGroupName `
  --branch master --manual-integration `
  --repo-url https://github.com/Azure-Samples/function-image-upload-resize
```

# <a name="nodejs-v10-sdk"></a>[Node.js V10 SDK](#tab/nodejsv10)

Het voorbeeld van de resize-functie van Node.js is beschikbaar op [GitHub](https://github.com/Azure-Samples/storage-blob-resize-function-node-v10). Implementeer dit codeproject van Functions naar de functie-app met behulp van de opdracht [az functionapp deployment source config](/cli/azure/functionapp/deployment/source).

```bash
az functionapp deployment source config --name $functionapp \
  --resource-group $resourceGroupName --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/storage-blob-resize-function-node-v10
```

```powershell
az functionapp deployment source config --name $functionapp `
  --resource-group $resourceGroupName --branch master --manual-integration `
  --repo-url https://github.com/Azure-Samples/storage-blob-resize-function-node-v10
```

---

De functie om afbeeldingen in grootte aan te passen wordt geactiveerd door HTTP-aanvragen die vanaf de Event Grid-service worden verzonden. U kunt Event Grid laten weten dat u deze meldingen bij de URL van uw functie wilt ontvangen door een gebeurtenisabonnement te maken. Voor deze zelfstudie abonneert u zich op gebeurtenissen die door een blob zijn gemaakt.

De gegevens die aan de functie worden doorgegeven in de Event Grid-melding bevatten de URL van de blob. Die URL is op zijn beurt doorgegeven aan de invoerbinding om de geüploade installatiekopie uit de Blob-opslag op te halen. De functie genereert een miniatuurafbeelding en de resulterende stroom wordt weggeschreven naar een afzonderlijke container in de Blob-opslag.

Dit project gebruikt `EventGridTrigger` als het type trigger. Het wordt aangeraden om de trigger van Event Grid te gebruiken in plaats van algemene HTTP-triggers. Functie-triggers van Event Grid worden namelijk automatisch gevalideerd. Bij gebruik van algemene HTTP-triggers moet u een [validatie-antwoord](security-authentication.md) implementeren.

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

Als u meer wilt leren over deze functie, bekijk dan de [bestanden function.json en run.csx](https://github.com/Azure-Samples/function-image-upload-resize/tree/master/ImageFunctions).

# <a name="nodejs-v10-sdk"></a>[Node.js V10 SDK](#tab/nodejsv10)

Zie voor meer informatie over deze functie de [bestanden function.json en index.js](https://github.com/Azure-Samples/storage-blob-resize-function-node-v10/tree/master/Thumbnail).

---

Het codeproject van Functions wordt rechtstreeks vanuit de openbare opslagplaats met voorbeelden geïmplementeerd. Zie [Doorlopende implementatie voor Azure Functions](../azure-functions/functions-continuous-deployment.md) voor meer informatie over implementatieopties voor Azure Functions.

## <a name="create-an-event-subscription"></a>Een gebeurtenisabonnement maken

Een gebeurtenisabonnement geeft aan welke door de provider gegenereerde gebeurtenissen u naar een bepaald eindpunt wilt versturen. In dit geval wordt het eindpunt bepaald door de functie. Gebruik de volgende stappen om in Azure Portal een gebeurtenisabonnement te maken die meldingen aan uw functie verzendt:

1. Zoek in de [Azure Portal](https://portal.azure.com) bovenaan de pagina naar `Function App`, selecteer het kies de functie-app die u zojuist hebt gemaakt. Selecteer **Functies** en kies de functie **Miniatuur**.

    :::image type="content" source="media/resize-images-on-storage-blob-upload-event/choose-thumbnail-function.png" alt-text="De functie Miniatuur in de portal kiezen":::

1.  Selecteer **Integratie**, kies vervolgens de **Event Grid-trigger** en vervolgens **Event Grid-abonnement maken**.

    :::image type="content" source="./media/resize-images-on-storage-blob-upload-event/add-event-subscription.png" alt-text="Navigeren naar Event Grid-abonnement toevoegen in de Azure Portal" :::

1. Gebruik de instellingen voor het gebeurtenisabonnement zoals die zijn opgegeven in de tabel onder de afbeelding.
    
    ![Een gebeurtenisabonnement maken voor de functie in Azure Portal](./media/resize-images-on-storage-blob-upload-event/event-subscription-create.png)

    | Instelling      | Voorgestelde waarde  | Beschrijving                                        |
    | ------------ | ---------------- | -------------------------------------------------- |
    | **Naam** | imageresizersub | De naam voor het nieuwe gebeurtenisabonnement. |
    | **Onderwerptype** | Opslagaccounts | Kies de provider voor de gebeurtenissen van het opslagaccount. |
    | **Abonnement** | Uw Azure-abonnement | Uw huidige Azure-abonnement is standaard geselecteerd. |
    | **Resourcegroep** | myResourceGroup | Selecteer **Bestaande gebruiken** en kies de resourcegroep die u in deze zelfstudie hebt gebruikt. |
    | **Resource** | Uw Blob-opslagaccount | Kies het Blob-opslagaccount dat u hebt gemaakt. |
    | **Naam van systeemonderwerp** | imagestoragesystopic | Geef een naam op voor het systeemonderwerp. Zie [Overzicht van systeemonderwerpen](system-topics.md) voor meer informatie over systeemonderwerpen. |    
    | **Gebeurtenistypen** | BlobCreated | Schakel alle typen uit behalve **BlobCreated**. Alleen gebeurtenistypen van `Microsoft.Storage.BlobCreated` worden doorgegeven aan de functie. |
    | **Eindpunttype** | automatisch gegenereerd | Vooraf gedefinieerd als **Azure-functie**. |
    | **Eindpunt** | automatisch gegenereerd | Naam van de functie. In dit geval is deze **Miniatuur**. |

1. Open het tabblad **Filters** en voer de volgende acties uit:
    1. Selecteer de optie **Filteren van onderwerpen inschakelen**.
    1. Voer voor **onderwerp begint met** de volgende waarde in: **/blobServices/default/containers/images/**.

        ![Een filter opgeven voor het gebeurtenisabonnement](./media/resize-images-on-storage-blob-upload-event/event-subscription-filter.png)

1. Selecteer **Maken** om het gebeurtenisabonnement toe te voegen. Er wordt een gebeurtenisabonnement gemaakt die de functie `Thumbnail` activeert op het moment dat er een blob wordt toegevoegd aan de container `images`. De functie past de afbeelding in grootte aan en voegt deze toe aan de container `thumbnails`.

De services in de back-end zijn nu geconfigureerd. Dit betekent dat u de functionaliteit voor het aanpassen van het formaat van afbeeldingen kunt gaan testen in de voorbeeld-web-app.

## <a name="test-the-sample-app"></a>De voorbeeld-app testen

U kunt de formaatwijziging door de web-app testen door naar de URL van de gepubliceerde app te gaan. De standaard-URL van de web-app is `https://<web_app>.azurewebsites.net`.

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

Klik ergens in het gebied **Foto's uploaden** om een bestand te selecteren en te uploaden. U kunt ook een foto naar dit gebied slepen.

Zodra de geüploade afbeelding is verdwenen, ziet u een kopie van de geüploade afbeelding in de carrousel **Gegenereerde miniaturen**. Het formaat van deze afbeelding werd gewijzigd door de functie, waarna de afbeelding werd toegevoegd aan de container *thumbnails* en gedownload door de webclient.

![Schermopname van de gepubliceerde web-app ImageResizer in een browser voor de \.NET v12 SDK.](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

# <a name="nodejs-v10-sdk"></a>[Node.js V10 SDK](#tab/nodejsv10)

Klik op **Bestand kiezen**, selecteer een bestand en klik op **Afbeelding uploaden**. Als het uploaden is voltooid, wordt in de browser een pagina weergegeven dat het uploaden is geslaagd. Klik op de koppeling om terug te keren naar de startpagina. Een kopie van de geüploade afbeelding wordt weergegeven in het gebied **Gegenereerde miniaturen**. (Als de afbeelding niet meteen wordt weergegeven, laadt u de pagina opnieuw.) Het formaat van deze afbeelding werd gewijzigd door de functie, waarna de afbeelding werd toegevoegd aan de container *thumbnails* en gedownload door de webclient.

![Gepubliceerde web-app in de browser](./media/resize-images-on-storage-blob-upload-event/upload-app-nodejs-thumb.png)

---

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Een algemene Azure Storage-account maken
> * Serverloze code implementeren met behulp van Azure Functions
> * Een gebeurtenisabonnement voor Blob-opslag maken in Event Grid

Ga naar deel drie van de reeks met zelfstudies over Azure Storage om te leren hoe u de toegang tot het opslagaccount beveiligt.

> [!div class="nextstepaction"]
> [Toegang tot gegevens van een toepassingen in de cloud beveiligen](../storage/blobs/storage-secure-access-application.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

+ Zie [Een inleiding tot Azure Event Grid](overview.md) voor meer informatie over Event Grid.
+ Zie [Een functie maken die kan worden geïntegreerd met Azure Logic Apps](../azure-functions/functions-twitter-email.md) als u nog een zelfstudie wilt volgen waarin Azure Functions wordt gebruikt.

[previous-tutorial]: ../storage/blobs/storage-upload-process-images.md

---
title: 'Quickstart: Azure Blob Storage voor JavaScript v10 in de browser'
description: Leer hoe u een exemplaar van BlobService gebruikt voor het uploaden, vermelden en verwijderen van blobs met behulp van JavaScript op een HTML-pagina.
services: storage
author: twooley
ms.custom: mvc, devx-track-js
ms.service: storage
ms.author: twooley
ms.date: 07/24/2020
ms.topic: quickstart
ms.subservice: blobs
ms.openlocfilehash: b3cca61fe272b61f2227e7546b37e4ea37cfd693
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280136"
---
<!-- Customer intent: As a web application developer I want to interface with Azure Blob storage entirely on the client so that I can build a SPA application that is able to upload and delete files on blob storage. -->

# <a name="quickstart-manage-blobs-with-javascript-v10-sdk-in-browser"></a>Quickstart: Blobs beheren met JavaScript v10 SDK in browser

In deze quickstart leert u hoe u blobs beheert met behulp van JavaScript-code die volledig in de browser wordt uitgevoerd. Blobs zijn objecten die grote hoeveelheden tekst of binaire gegevens kunnen bevatten, zoals afbeeldingen, documenten, streaming media en archiefgegevens. U gebruikt de vereiste beveiligingsmaatregelen om verzekerd te zijn van beveiligde toegang tot uw blobopslagaccount.

> [!NOTE]
> In deze snelstart wordt gebruikgemaakt van een oudere versie van de Azure Blob Storage-clientbibliotheek. Als u aan de slag wilt gaan met de vorige versie, raadpleegt u [Quickstart: Blobs beheren met JavaScript v12 SDK in een browser](quickstart-blobs-javascript-browser.md).

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Een Azure Storage-account. [Een opslagaccount maken](../common/storage-account-create.md).
- Een lokale webserver. Dit artikel gebruikt [Node.js](https://nodejs.org) om een basisserver te openen.
- [Visual Studio Code](https://code.visualstudio.com).
- Een VS Code-extensie voor foutopsporing in de browser, zoals [Foutopsporingsprogramma voor Chrome](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-chrome) of [Foutopsporing voor Microsoft Edge](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-edge).

## <a name="setting-up-storage-account-cors-rules"></a>CORS-regels voor het opslagaccount instellen

Voordat uw webtoepassing vanuit de client toegang kan krijgen tot een blob-opslag, moet het account worden geconfigureerd om [cross-origin-resource sharing](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) oftewel CORS in te schakelen.

Ga terug naar Azure Portal en selecteer uw opslagaccount. Als u een nieuwe CORS-regel wilt definiëren, navigeert u terug naar de sectie **Instellingen** en klikt u op de koppeling **CORS**. Klik vervolgens op de knop **Toevoegen** om het venster **CORS-regel toevoegen** te openen. Voor deze snelstart maakt u een open CORS-regel:

![CORS-instellingen in Azure Blob Storage-account](media/storage-quickstart-blobs-javascript-client-libraries-v10/azure-blob-storage-cors-settings.png)

In de volgende tabel worden alle CORS-instellingen beschreven en de waarden voor het definiëren van de regel uitgelegd.

|Instelling  |Waarde  | Beschrijving |
|---------|---------|---------|
| Toegestane oorsprongen | * | Accepteert een door komma's gescheiden lijst met domeinen die als acceptabele oorsprongen zijn ingesteld. Als de waarde wordt ingesteld op `*`, hebben alle domeinen toegang tot het opslagaccount. |
| Toegestane methoden     | delete, get, head, merge, post, options en put | Vermeldt de HTTP-bewerkingen die zijn toegestaan om voor het opslagaccount te worden uitgevoerd. Voor deze snelstart selecteert u alle beschikbare opties. |
| Toegestane headers | * | Definieert een lijst met aanvraagheaders (inclusief headers met een voorvoegsel) die door het opslagaccount zijn toegestaan. Als de waarde wordt ingesteld op `*`, hebben alle headers toegang. |
| Weergegeven headers | * | Vermeldt de door het account toegestane antwoordheaders. Als de waarde wordt ingesteld op `*`, kan het account elke header verzenden.  |
| Max. leeftijd (seconden) | 86400 | De maximale periode dat de browser de voorbereidende aanvraag OPTIONS in de cache opslaat. De waarde *86400* betekent dat de cache een hele dag in stand blijft. |

> [!IMPORTANT]
> Zorg ervoor dat instellingen die u in productie gebruikt, de minimale mate van toegang tot het opslagaccount weergeven zodat beveiligde toegang gehandhaafd blijft. De hier beschreven CORS-instellingen zijn geschikt voor een snelstart, omdat ze een minder streng beveiligingsbeleid definiëren. Deze instellingen worden echter niet aangeraden in reële situaties.

Vervolgens gebruikt u Azure Cloud Shell om een beveiligingstoken te maken.

[!INCLUDE [Open the Azure cloud shell](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-shared-access-signature"></a>Handtekening voor gedeelde toegang maken

Shared Access Signature (handtekening voor gedeelde toegang, SAS) wordt gebruikt door de code die wordt uitgevoerd in de browser om aanvragen bij de blob-opslag te verifiëren. Als u een SAS gebruikt, kan de client toegang tot de opslagresources autoriseren zonder de toegangssleutel of verbindingsreeks van het account. Zie [Handtekeningen voor gedeelde toegang gebruiken](../common/storage-sas-overview.md) voor meer informatie over SAS.

U kunt een SAS maken met behulp van de CLI via de Azure-cloudshell of met het Azure-portal of Azure Storage Explorer. In de volgende tabel worden de parameters beschreven waar u waarden voor moet opgeven om een SAS te maken met de CLI.

| Parameter      |Beschrijving  | Tijdelijke aanduiding |
|----------------|-------------|-------------|
| *expiry*       | De vervaldatum van het toegangstoken in de notatie JJJJ-MM-DD. Voer voor deze snelstart de datum van morgen in. | *FUTURE_DATE* |
| *account-name* | De naam van het opslagaccount. Gebruik de naam die in een vorige stap is bewaard. | *YOUR_STORAGE_ACCOUNT_NAME* |
| *account-key*  | De opslagaccountsleutel. Gebruik de sleutel die in een vorige stap is bewaard. | *YOUR_STORAGE_ACCOUNT_KEY* |

Gebruik de volgende CLI-opdracht, met de werkelijke waarden bij elke tijdelijke aanduiding, om een SAS te genereren die u in uw JavaScript-code kunt gebruiken.

```azurecli-interactive
az storage account generate-sas \
  --permissions racwdl \
  --resource-types sco \
  --services b \
  --expiry FUTURE_DATE \
  --account-name YOUR_STORAGE_ACCOUNT_NAME \
  --account-key YOUR_STORAGE_ACCOUNT_KEY
```

Mogelijk vindt u de reeks waarden na elke parameter wat cryptisch. Deze parameterwaarden zijn afkomstig van de eerste letter van hun respectieve machtiging. In de volgende tabel wordt uitgelegd waar de waarden vandaan komen:

| Parameter        | Waarde   | Beschrijving  |
|------------------|---------|---------|
| *permissions*    | racwdl  | Deze SAS staat de mogelijkheden *read*, *append*, *create*, *write*, *delete* en *list* toe. |
| *resource-types* | sco     | De resources die waar de SAS invloed op heeft, zijn *service*, *container* en *object*. |
| *services*       | b       | De service waar de SAS invloed op heeft is de *blob*-service. |

Wanneer de SAS is gegenereerd, kopieert u de geretourneerde waarde en slaat u deze ergens op voor gebruik bij een volgende stap. Als u uw SAS hebt gegenereerd met een andere methode dan de Azure CLI, dan moet u de initiële `?` verwijderen als deze aanwezig is. Dit teken is een scheidingsteken voor de URL dat al is opgenomen in de URL-sjabloon, verderop in dit onderwerp waar de SAS gebruikt wordt.

> [!IMPORTANT]
> Geef in productie SAS-tokens altijd door met behulp van TLS. SAS-tokens moeten op de server worden gegenereerd en naar de HTML-pagina verzonden om ze opnieuw door te geven aan Azure Blob Storage. U kunt eventueel een serverloze functie gebruiken om SAS-tokens te genereren. Azure Portal bevat functiesjablonen waarmee een SAS kan worden gemaakt met behulp van een JavaScript-functie.

## <a name="implement-the-html-page"></a>HTML-pagina implementeren

In dit onderdeel maakt u een eenvoudige webpagina en configureert u VS Code om de pagina te lanceren en fouten op te sporen. Voordat u deze kunt lanceren, moet u node.js gebruiken om een lokale webserver op te starten en de pagina aan te bieden wanneer uw browser deze opvraagt. Vervolgens voegt u JavaScript-code toe om verschillende Blob Storage-API's aan te roepen en de resultaten weer te geven op de pagina. U kunt de resultaten van deze aanroepen ook weergeven in het [Azure-portal](https://portal.azure.com), [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer) en de [Azure Storage-extensie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) voor VS Code.

### <a name="set-up-the-web-application"></a>Webtoepassing instellen

Maak eerst een nieuwe map met de naam *azure-blobs-javascript* en open deze in VS Code. Maak vervolgens een nieuw bestand in VS Code, voeg de volgende HTML toe en sla deze op als *index.html* in de map *azure-blobs-javascript*.

```html
<!DOCTYPE html>
<html>

<body>
    <button id="create-container-button">Create container</button>
    <button id="delete-container-button">Delete container</button>
    <button id="select-button">Select and upload files</button>
    <input type="file" id="file-input" multiple style="display: none;" />
    <button id="list-button">List files</button>
    <button id="delete-button">Delete selected files</button>
    <p><b>Status:</b></p>
    <p id="status" style="height:160px; width: 593px; overflow: scroll;" />
    <p><b>Files:</b></p>
    <select id="file-list" multiple style="height:222px; width: 593px; overflow: scroll;" />
</body>

<!-- You'll add code here later in this quickstart. -->

</html>
```

### <a name="configure-the-debugger"></a>Het foutopsporingsprogramma configureren

Om de foutopsporingsextensie in te stellen in VS Code, selecteert u **Foutopsporing &gt; Configuratie toevoegen...** en vervolgens **Chrome** of **Microsoft Edge** afhankelijk van de extensie die u eerder heeft geïnstalleerd in het onderdeel Vereisten. Deze actie maakt een *launch.json*-bestand en opent het in de editor.

Wijzig vervolgens het bestand *launch.json* zodat de `url`-waarde `/index.html` bevat, zoals hier wordt weergegeven:

```json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "type": "chrome",
            "request": "launch",
            "name": "Launch Chrome against localhost",
            "url": "http://localhost:8080/index.html",
            "webRoot": "${workspaceFolder}"
        }
    ]
}
```

Deze configuratie vertelt VS Code welke browser moet worden geopend en welke URL moet worden geladen.

### <a name="launch-the-web-server"></a>De webserver starten

Als u de lokale webserver met Node.js wilt starten, selecteert u **Weergeven > Terminal** om een consolevenster te openen in VS Code, en voert u de volgende opdracht in.

```console
npx http-server
```

Deze opdracht installeert het pakket voor de *http-server* en start de server, zodat de huidige map beschikbaar wordt via standaard-URL's, inclusief degene die u heeft opgegeven in de vorige stap.

### <a name="start-debugging"></a>Foutopsporing starten

Als u *index.html* in de browser wilt openen terwijl het VS Code-foutopsporingsprogramma is gekoppeld, selecteert u **Foutopsporing > Beginnen met foutopsporing** of drukt u op F5 in VS Code.

De weergegeven gebruikersinterface doet nog niets, maar in het volgende onderdeel voegt u JavaScript-code toe om elke weergegeven functie te implementeren. U kunt vervolgens onderbrekingspunten instellen en met het foutopsporingsprogramma communiceren wanneer het onderbroken is op uw code.

Wanneer u wijzigingen aanbrengt in *index.html*, moet u de pagina opnieuw laden om de wijzigingen in de browser weer te geven. In VS Code kunt u ook **Foutopsporing > Opnieuw beginnen met foutopsporing** selecteren of drukken op CTRL + SHIFT + F5.

### <a name="add-the-blob-storage-client-library"></a>Clientbibliotheek voor blob-opslag toevoegen

Als u aanroepen naar de Blob Storage-API wilt inschakelen, moet u eerst [De Azure Storage SDK voor JavaScript - Blob-clientbibliotheek downloaden](https://aka.ms/downloadazurestoragejsblob), de inhoud van het .zip-bestand uitpakken en het bestand *azure-storage-blob.js* in de map *azure-blobs-javascript* zetten.

Plak vervolgens de volgende HTML-code in *index. html* na de afsluitende tag `</body>` en vervang de tijdelijke aanduiding voor de opmerking.

```html
<script src="azure-storage-blob.js" charset="utf-8"></script>

<script>
// You'll add code here in the following sections.
</script>
```

Deze code voegt een verwijzing toe naar het scriptbestand en biedt een plaats voor uw eigen JavaScript-code. Voor deze quickstart gebruiken we het scriptbestand *azure-storage-blob.js*, zodat u het kunt openen in VS Code, de inhoud ervan kunt lezen en onderbrekingspunten instellen. In productie moet u het compactere bestand *azure-storage.blob.min.js* gebruiken dat ook is opgegeven in het .zip-bestand.

U vindt meer informatie over elke Blob Storage-functie in de [referentiedocumentatie](/javascript/api/%40azure/storage-blob/index). Sommige van de functies in de SDK zijn alleen beschikbaar in node.js, of alleen beschikbaar in de browser.

De code in *azure-storage-blob.js* exporteert een globale variabele met de naam `azblob`, die u in uw JavaScript-code gaat gebruiken om toegang te krijgen tot de Blob Storage-API's.

### <a name="add-the-initial-javascript-code"></a>De eerste JavaScript-code toevoegen

Plak vervolgens de volgende code in het `<script>`-element dat in het vorige codeblok wordt weergegeven en vervang de tijdelijke aanduiding voor de tijdelijke aanduiding.

```javascript
const createContainerButton = document.getElementById("create-container-button");
const deleteContainerButton = document.getElementById("delete-container-button");
const selectButton = document.getElementById("select-button");
const fileInput = document.getElementById("file-input");
const listButton = document.getElementById("list-button");
const deleteButton = document.getElementById("delete-button");
const status = document.getElementById("status");
const fileList = document.getElementById("file-list");

const reportStatus = message => {
    status.innerHTML += `${message}<br/>`;
    status.scrollTop = status.scrollHeight;
}
```

Deze code maakt velden voor elk HTML-element dat de volgende code zal gebruiken en implementeert een `reportStatus`-functie om de uitvoer weer te geven.

Voeg in de volgende secties elk nieuw blok van JavaScript-code toe na het vorige blok.

### <a name="add-your-storage-account-info"></a>Voeg de gegevens van uw opslagaccount toe

Voeg vervolgens code toe om toegang te krijgen tot uw opslagaccount, waarbij u de tijdelijke aanduidingen vervangt door de naam van uw account en de SAS die u in een vorige stap hebt gegenereerd.

```javascript
const accountName = "<Add your storage account name>";
const sasString = "<Add the SAS you generated earlier>";
const containerName = "testcontainer";
const containerURL = new azblob.ContainerURL(
    `https://${accountName}.blob.core.windows.net/${containerName}?${sasString}`,
    azblob.StorageURL.newPipeline(new azblob.AnonymousCredential));
```

Deze code gebruikt uw accountgegevens en SAS om een [ContainerURL](/javascript/api/@azure/storage-blob/ContainerURL)-instantie te maken. Dit is handig om een opslagcontainer te maken en te bewerken.

### <a name="create-and-delete-a-storage-container"></a>Een opslagcontainer maken en verwijderen

Voeg vervolgens code toe om de opslagcontainer te maken en te verwijderen wanneer u op de bijbehorende knop klikt.

```javascript
const createContainer = async () => {
    try {
        reportStatus(`Creating container "${containerName}"...`);
        await containerURL.create(azblob.Aborter.none);
        reportStatus(`Done.`);
    } catch (error) {
        reportStatus(error.body.message);
    }
};

const deleteContainer = async () => {
    try {
        reportStatus(`Deleting container "${containerName}"...`);
        await containerURL.delete(azblob.Aborter.none);
        reportStatus(`Done.`);
    } catch (error) {
        reportStatus(error.body.message);
    }
};

createContainerButton.addEventListener("click", createContainer);
deleteContainerButton.addEventListener("click", deleteContainer);
```

Deze code roept de ContainerURL-functies [maken](/javascript/api/@azure/storage-blob/containerclient#create-containercreateoptions-) en [verwijderen](/javascript/api/@azure/storage-blob/containerclient#delete-containerdeletemethodoptions-) aan zonder een [Aborter](/javascript/api/@azure/storage-blob/aborter)-instantie te gebruiken. Om deze quickstart eenvoudig te houden, gaat deze code ervan uit dat uw opslagaccount is aangemaakt en ingeschakeld. Gebruik in productiecode een Aborter-instantie om een time-outfunctie toe te voegen.

### <a name="list-blobs"></a>Blobs vermelden

Voeg vervolgens code toe om de inhoud van de opslagcontainer weer te geven wanneer u op de knop **Bestanden weergeven** klikt.

```javascript
const listFiles = async () => {
    fileList.size = 0;
    fileList.innerHTML = "";
    try {
        reportStatus("Retrieving file list...");
        let marker = undefined;
        do {
            const listBlobsResponse = await containerURL.listBlobFlatSegment(
                azblob.Aborter.none, marker);
            marker = listBlobsResponse.nextMarker;
            const items = listBlobsResponse.segment.blobItems;
            for (const blob of items) {
                fileList.size += 1;
                fileList.innerHTML += `<option>${blob.name}</option>`;
            }
        } while (marker);
        if (fileList.size > 0) {
            reportStatus("Done.");
        } else {
            reportStatus("The container does not contain any files.");
        }
    } catch (error) {
        reportStatus(error.body.message);
    }
};

listButton.addEventListener("click", listFiles);
```

Met deze code wordt de functie [ContainerURL.listBlobFlatSegment](/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-) aan te roepen in een lus om ervoor te zorgen dat alle segmenten worden opgehaald. Voor elk segment doorloopt het de lijst van blobitems die het bevat en werkt het de lijst **Bestanden** bij.

### <a name="upload-blobs"></a>Blobs uploaden

Voeg vervolgens code toe om bestanden naar de opslagcontainer te uploaden wanneer u klikt op de knop **Bestanden selecteren en uploaden**.

```javascript
const uploadFiles = async () => {
    try {
        reportStatus("Uploading files...");
        const promises = [];
        for (const file of fileInput.files) {
            const blockBlobURL = azblob.BlockBlobURL.fromContainerURL(containerURL, file.name);
            promises.push(azblob.uploadBrowserDataToBlockBlob(
                azblob.Aborter.none, file, blockBlobURL));
        }
        await Promise.all(promises);
        reportStatus("Done.");
        listFiles();
    } catch (error) {
        reportStatus(error.body.message);
    }
}

selectButton.addEventListener("click", () => fileInput.click());
fileInput.addEventListener("change", uploadFiles);
```

Deze code verbindt de knop **Bestanden selecteren en uploaden** naar het verborgen `file-input`-element. Met de gebeurtenisknop `click` wordt zo de gebeurtenis bestandsinvoer `click` geactiveerd en wordt de bestandenkiezer weergegeven. Nadat u bestanden hebt geselecteerd en het dialoogvenster hebt gesloten, treedt de gebeurtenis `input` op en wordt de functie `uploadFiles` aangeroepen. Deze functie roept de functie [uploadBrowserDataToBlockBlob](/javascript/api/@azure/storage-blob/blockblobclient#uploadbrowserdata-blob---arraybuffer---arraybufferview--blockblobparalleluploadoptions-) (enkel voor de browser) aan te roepen voor elk bestand dat u hebt geselecteerd. Elke aanroep retourneert een Promise die wordt toegevoegd aan een lijst, zodat ze allemaal tegelijk kunnen worden verwacht, waardoor de bestanden parallel worden geüpload.

### <a name="delete-blobs"></a>Blobs verwijderen

Voeg vervolgens code toe om bestanden te verwijderen uit de opslagcontainer wanneer u klikt op de knop **Geselecteerde bestanden verwijderen**.

```javascript
const deleteFiles = async () => {
    try {
        if (fileList.selectedOptions.length > 0) {
            reportStatus("Deleting files...");
            for (const option of fileList.selectedOptions) {
                const blobURL = azblob.BlobURL.fromContainerURL(containerURL, option.text);
                await blobURL.delete(azblob.Aborter.none);
            }
            reportStatus("Done.");
            listFiles();
        } else {
            reportStatus("No files selected.");
        }
    } catch (error) {
        reportStatus(error.body.message);
    }
};

deleteButton.addEventListener("click", deleteFiles);
```

Met deze code wordt de functie [BlobURL.delete](/javascript/api/@azure/storage-blob/BlobURL#delete-aborter--iblobdeleteoptions-) aangeroepen om elk bestand te verwijderen dat in de lijst is geselecteerd. Vervolgens wordt de eerder getoonde functie `listFiles` aangeroepen om de inhoud van de **Bestanden**-lijst te vernieuwen.

### <a name="run-and-test-the-web-application"></a>De webtoepassing uitvoeren en testen

Nu kunt u de pagina starten en experimenteren om te ontdekken hoe blob-opslag werkt. Als er fouten optreden (bijvoorbeeld wanneer u bestanden probeert weer te geven voordat u de container hebt gemaakt), wordt in het deelvenster **Status** het ontvangen foutbericht weergegeven. U kunt ook onderbrekingspunten instellen in de JavaScript-code om de waarden te controleren die worden geretourneerd door de opslag-API's.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources wilt opschonen die tijdens deze quickstart zijn gemaakt, gaat u naar de [Azure-portal](https://portal.azure.com) en verwijdert u de resourcegroep die u hebt gemaakt in de sectie Vereisten.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart heeft u een eenvoudige website gemaakt die toegang heeft tot blobopslag vanuit JavaScript in een browser. Ga verder met de volgende zelfstudie om te weten hoe u een website zelf kunt hosten in blobopslag:

> [!div class="nextstepaction"]
> [Een statische website hosten op Blob-opslag](./storage-blob-static-website-host.md)
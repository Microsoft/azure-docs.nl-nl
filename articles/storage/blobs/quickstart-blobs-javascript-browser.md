---
title: 'Quickstart: Azure Blob-opslagbibliotheek V12 - JavaScript in een browser'
description: In deze quickstart leert u hoe u de Azure Blob-opslagclientbibliotheek versie 12 voor JavaScript in een browser kunt gebruiken. U maakt een container en een object in Blob-opslag. Vervolgens leert u hoe u alle blobs in een container kunt weergeven. Tot slot leert u hoe u blobs verwijdert en een container verwijdert.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 998d49e91d38a1f2fdc2503165ee99635e153027
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96001895"
---
<!-- Customer intent: As a web application developer I want to interface with Azure Blob storage entirely on the client so that I can build a SPA application that is able to upload and delete files on blob storage. -->

# <a name="quickstart-manage-blobs-with-javascript-v12-sdk-in-a-browser"></a>Quickstart: Blobs beheren met JavaScript v12 SDK in een browser

Azure Blob-opslag is geoptimaliseerd voor het opslaan van grote hoeveelheden ongestructureerde gegevens. Blobs zijn objecten die tekst of binaire gegevens kunnen bevatten, zoals afbeeldingen, documenten, streaming media en archiefgegevens. In deze quickstart leert u hoe u blobs beheert met behulp van JavaScript in een browser. U kunt blobs uploaden en weergeven en u kunt containers maken en verwijderen.

Aanvullende bronnen:

* [API-referentiedocumentatie](/javascript/api/@azure/storage-blob)
* [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob)
* [Package (npm)](https://www.npmjs.com/package/@azure/storage-blob)
* [Voorbeelden](../common/storage-samples-javascript.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Vereisten

* [Een Azure-account met een actief abonnement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [Een Azure Storage-account](../common/storage-account-create.md)
* [Node.js](https://nodejs.org)
* [Microsoft Visual Studio Code](https://code.visualstudio.com)
* Een Visual Studio code-extensie voor foutopsporing in een browser, zoals:
    * [Foutopsporingsprogramma voor Microsoft Edge](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-edge)
    * [Foutopsporingsprogramma voor Chrome](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-chrome)
    * [Foutopsporingsprogramma voor Firefox](https://marketplace.visualstudio.com/items?itemName=firefox-devtools.vscode-firefox-debug)


[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="object-model"></a>Objectmodel

Er zijn drie typen resources voor blobopslag:

* Het opslagaccount
* Een container in het opslagaccount
* Een blob in de container

Het volgende diagram geeft de relatie tussen deze resources weer.

![Diagram van de blobopslagarchitectuur](./media/storage-blobs-introduction/blob1.png)

In deze quickstart gebruikt u de volgende JavaScript-klassen om te communiceren met deze resources:

* [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient): Met de klasse `BlobServiceClient` kunt u Azure Storage-resources en blob-containers bewerken.
* [ContainerClient](/javascript/api/@azure/storage-blob/containerclient): Met de klasse `ContainerClient` kunt u Azure Storage-containers en de bijbehorende blobs bewerken.
* [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient): Met de klasse `BlockBlobClient` kunt u Azure Storage-blobs bewerken.

## <a name="setting-up"></a>Instellen

In dit gedeelte wordt uitgelegd hoe u een project voorbereidt voor gebruik met de Azure Blob Storage-clientbibliotheek v12 voor JavaScript.

### <a name="create-a-cors-rule"></a>Een CORS-regel maken

Voordat uw webtoepassing vanuit de client toegang kan krijgen tot een blob-opslag, moet het account worden geconfigureerd om [cross-origin-resource sharing](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) oftewel CORS in te schakelen.

Selecteer in de Azure-portal uw opslagaccount. Als u een nieuwe CORS-regel wilt definiëren, gaat u terug naar de sectie **Instellingen** en klikt u op **CORS**. Voor deze snelstart maakt u een open CORS-regel:

![CORS-instellingen in Azure Blob Storage-account](media/quickstart-blobs-javascript-browser/azure-blob-storage-cors-settings.png)

In de volgende tabel worden alle CORS-instellingen beschreven en de waarden voor het definiëren van de regel uitgelegd.

|Instelling  |Waarde  | Beschrijving |
|---------|---------|---------|
| **TOEGESTANE OORSPRONGEN** | **\** _ | Accepteert een door komma's gescheiden lijst met domeinen die als acceptabele oorsprongen zijn ingesteld. Als de waarde wordt ingesteld op `_`, hebben alle domeinen toegang tot het opslagaccount. |
| **TOEGESTANE METHODEN** | **DELETE**, **GET**, **HEAD**, **MERGE**, **POST**, **OPTIONS** en **PUT** | Vermeldt de HTTP-bewerkingen die zijn toegestaan om voor het opslagaccount te worden uitgevoerd. Voor deze snelstart selecteert u alle beschikbare opties. |
| **TOEGESTANE HEADERS** | **\** _ | Definieert een lijst met aanvraagheaders (inclusief headers met een voorvoegsel) die door het opslagaccount zijn toegestaan. Als de waarde wordt ingesteld op `_`, hebben alle headers toegang. |
| **WEERGEGEVEN KOPTEKSTEN** | **\** _ | Vermeldt de door het account toegestane antwoordheaders. Als de waarde wordt ingesteld op `_`, kan het account elke header verzenden. |
| **MAXIMUMLEEFTIJD** | **86400** | De maximale periode dat de browser de voorbereidende aanvraag OPTIES in de cache opslaat. De waarde *86400* betekent dat de cache een hele dag in stand blijft. |

Nadat u de velden met de waarden uit deze tabel hebt ingevuld, klikt u op de knop **Opslaan**.

> [!IMPORTANT]
> Zorg ervoor dat instellingen die u in productie gebruikt, de minimale mate van toegang tot het opslagaccount weergeven zodat beveiligde toegang gehandhaafd blijft. De hier beschreven CORS-instellingen zijn geschikt voor een snelstart, omdat ze een minder streng beveiligingsbeleid definiëren. Deze instellingen worden echter niet aangeraden in reële situaties.

### <a name="create-a-shared-access-signature"></a>Handtekening voor gedeelde toegang maken

Shared Access Signature (handtekening voor gedeelde toegang, SAS) wordt gebruikt door de code die wordt uitgevoerd in de browser om aanvragen bij de Azure Blob-opslag te verifiëren. Als u een SAS gebruikt, kan de client toegang tot de opslagresources autoriseren zonder de toegangssleutel of verbindingsreeks van het account. Zie [Handtekeningen voor gedeelde toegang gebruiken](../common/storage-sas-overview.md) voor meer informatie over SAS.

Volg deze stappen om de Blob-service SAS-URL op te halen:

1. Selecteer in de Azure-portal uw opslagaccount.
2. Ga naar het gedeelte met de **Instellingen** en selecteer **Handtekening voor gedeelde toegang**.
3. Schuif omlaag en klik op de knop **SAS en verbindingsreeks** genereren.
4. Schuif verder naar beneden en zoek het veld **SAS-URL van Blob-service** veld
5. Klik op de knop **Kopiëren naar klembord** aan de rechterkant van het veld **SAS-URL van Blob-service**.
6. Sla de gekopieerde URL ergens op voor gebruik in een volgende stap.

### <a name="add-the-azure-blob-storage-client-library"></a>De clientbibliotheek voor Azure Blob-opslag toevoegen

Maak op uw lokale computer een nieuwe map met de naam *azure-blobs-js-browser* en open deze in Visual Studio Code.

Selecteer **Weergave > Terminal** om een consolevenster te openen in Visual Studio Code. Voer de volgende Node.js Packet Manager (NPM)-opdracht uit in het terminalvenster om een [package.json](https://docs.npmjs.com/files/package.json)-bestand te maken.

```console
npm init -y
```

De Azure SDK bestaat uit een groot aantal afzonderlijke pakketten. U kunt kiezen welke pakketten u nodig hebt op basis van de services die u wilt gebruiken. Voer de volgende `npm`-opdracht uit in het terminalvenster om het `@azure/storage-blob`-pakket te installeren.

```console
npm install --save @azure/storage-blob
```

#### <a name="bundle-the-azure-blob-storage-client-library"></a>De clientbibliotheek voor Azure Blob-opslag bundelen

Als u Azure SDK-bibliotheken op een website wilt gebruiken, zet u de code om zodat ze in de browser kunnen werken. U kunt dit doen met een hulpprogramma dat een bundelaar wordt genoemd. Bundelen neemt JavaScript-code die is geschreven met [Node.js-](https://nodejs.org)-conventies en converteert deze naar een indeling die door browsers wordt begrepen. In dit quickstart-artikel wordt gebruikgemaakt van de [Parcel](https://parceljs.org/)-bundelaar.

Installeer Parcel door de volgende `npm`-opdracht uit te voeren in het terminalvenster:

```console
npm install -g parcel-bundler
```

Open in Visual Studio Code het bestand *package.json* en voeg een `browserlist` toe tussen de vermeldingen `license` en `dependencies`. Deze `browserlist` streeft naar de nieuwste versie van drie populaire browsers. Het volledige bestand *package.json* moet er nu als volgt uitzien:

:::code language="json" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/package.json" highlight="12-16":::

Sla het *package.json*-bestand op.

### <a name="import-the-azure-blob-storage-client-library"></a>De clientbibliotheek voor Azure Blob-opslag importeren

Als u Azure SDK-bibliotheken wilt gebruiken in JavaScript, importeert u het `@azure/storage-blob`-pakket. Maak een nieuw bestand in Visual Studio Code met de volgende JavaScript-code.

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_ImportLibrary":::

Sla het bestand op als *index.js* in de map *azure-blobs-js-browser*.

### <a name="implement-the-html-page"></a>HTML-pagina implementeren

Maak een nieuw bestand in Visual Studio Code met de volgende HTML-code.

:::code language="html" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.html":::

Sla het bestand op als *index.html* in de map *azure-blobs-js-browser*.

## <a name="code-examples"></a>Codevoorbeelden

De voorbeeldcode laat zien hoe u de volgende taken kunt uitvoeren met de Azure Blob Storage-clientbibliotheek voor JavaScript:

* [Velden declareren voor UI-elementen](#declare-fields-for-ui-elements)
* [De gegevens van uw opslagaccount toevoegen](#add-your-storage-account-info)
* [Clientobjecten maken](#create-client-objects)
* [Een opslagcontainer maken en verwijderen](#create-and-delete-a-storage-container)
* [Blobs vermelden](#list-blobs)
* [Blobs uploaden](#upload-blobs)
* [Blobs verwijderen](#delete-blobs)

U voert de code uit nadat u alle fragmenten hebt toegevoegd aan het bestand *index.js*.

### <a name="declare-fields-for-ui-elements"></a>Velden declareren voor UI-elementen

Voeg de volgende code toe aan het einde van het bestand *index.js* toe.

:::code language="JavaScript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_DeclareVariables":::

Sla het bestand *index. js* op.

Deze code declareert velden voor elk HTML-element en implementeert een `reportStatus`-functie om de uitvoer weer te geven.

Voeg in de volgende secties elk nieuw blok van JavaScript-code toe na het vorige blok.

### <a name="add-your-storage-account-info"></a>Voeg de gegevens van uw opslagaccount toe

Voeg code toe om toegang te krijgen tot uw opslag account. Vervang de tijdelijke aanduiding door uw Blob service SAS-URL die u eerder hebt gegenereerd. Voeg de volgende code toe aan het einde van het bestand *index.js* toe.

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_StorageAcctInfo":::

Sla het bestand *index. js* op.

### <a name="create-client-objects"></a>Clientobjecten maken

Maak [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient) en [ContainerClient](/javascript/api/@azure/storage-blob/containerclient)-objecten voor interactie met de Azure Blob Storage-service. Voeg de volgende code toe aan het einde van het bestand *index.js* toe.

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_CreateClientObjects":::

Sla het bestand *index. js* op.

### <a name="create-and-delete-a-storage-container"></a>Een opslagcontainer maken en verwijderen

Maak en verwijder de opslagcontainer wanneer u op de bijbehorende knop op de webpagina klikt. Voeg de volgende code toe aan het einde van het bestand *index.js* toe.

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_CreateDeleteContainer":::

Sla het bestand *index. js* op.

### <a name="list-blobs"></a>Blobs vermelden

Geef de inhoud van de opslagcontainer weer wanneer u op de knop **Bestanden weergeven** klikt. Voeg de volgende code toe aan het einde van het bestand *index.js* toe.

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_ListBlobs":::

Sla het bestand *index. js* op.

Met deze code wordt de [ContainerClient.listBlobsFlat](/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-)-functie aangeroepen en wordt vervolgens een iterator gebruikt om de naam op te halen van elk geretourneerd [BlobItem](/javascript/api/@azure/storage-blob/blobitem). Voor elke `BlobItem` wordt de **Bestanden**-lijst met de eigenschapswaarde [naam](/javascript/api/@azure/storage-blob/blobitem#name) bijgewerkt.

### <a name="upload-blobs"></a>Blobs uploaden

Upload bestanden naar de opslagcontainer wanneer u klikt op de knop **Bestanden selecteren en uploaden**. Voeg de volgende code toe aan het einde van het bestand *index.js* toe.

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_UploadBlobs":::

Sla het bestand *index. js* op.

Deze code verbindt de knop **Bestanden selecteren en uploaden** naar het verborgen `file-input`-element. Met de gebeurtenisknop `click` wordt de gebeurtenis bestandsinvoer `click` geactiveerd en wordt de bestandenkiezer weergegeven. Nadat u bestanden hebt geselecteerd en het dialoogvenster hebt gesloten, treedt de gebeurtenis `input` op en wordt de functie `uploadFiles` aangeroepen. Deze functie maakt een [BlockBlobClient-](/javascript/api/@azure/storage-blob/blockblobclient)-object en roept vervolgens de functie voor alleen-browser [uploadBrowserData](/javascript/api/@azure/storage-blob/blockblobclient#uploadbrowserdata-blob---arraybuffer---arraybufferview--blockblobparalleluploadoptions-) aan voor elk bestand dat u hebt geselecteerd. Elke aanroep retourneert een `Promise`. Elke `Promise` wordt toegevoegd aan een lijst, zodat ze allemaal tegelijk kunnen worden verwacht, waardoor de bestanden parallel worden geüpload.

### <a name="delete-blobs"></a>Blobs verwijderen

Verwijder bestanden uit de opslagcontainer wanneer u klikt op de knop **Geselecteerde bestanden verwijderen**. Voeg de volgende code toe aan het einde van het bestand *index.js* toe.

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_DeleteBlobs":::

Sla het bestand *index. js* op.

Met deze code wordt de functie [ContainerClient.deleteBlob](/javascript/api/@azure/storage-blob/containerclient#deleteblob-string--blobdeleteoptions-) aangeroepen om elk bestand te verwijderen dat in de lijst is geselecteerd. Vervolgens wordt de eerder getoonde functie `listFiles` aangeroepen om de inhoud van de **Bestanden**-lijst te vernieuwen.

## <a name="run-the-code"></a>De code uitvoeren

Als u de code wilt uitvoeren in de Visual Studio Code-foutopsporing, configureert u het bestand *launch.json* voor uw browser.

### <a name="configure-the-debugger"></a>Het foutopsporingsprogramma configureren

De uitbreiding voor foutopsporing in Visual Studio Code instellen:

1. Selecteer **Uitvoeren > Configuratie toevoegen**
2. Selecteer **Microsoft Edge**, **Chrome** of **Firefox**, afhankelijk van de extensie die eerder u hebt geïnstalleerd in het gedeelte [Vereisten](#prerequisites).

Als u een nieuwe configuratie toevoegt, wordt er een *launch.json*-bestand gemaakt en geopend in de editor. Wijzig het bestand *launch.json* zodat de `url`-waarde `http://localhost:1234/index.html` is, zoals hier wordt weergegeven:

:::code language="json" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/.vscode/launch.json" highlight="11":::

Sla na het bijwerken het bestand *launch.json* op. Deze configuratie vertelt Visual Studio Code welke browser moet worden geopend en welke URL moet worden geladen.

### <a name="launch-the-web-server"></a>De webserver starten

Als u de webserver voor lokale ontwikkeling wilt starten, selecteert u **Weergeven > Terminal** om een consolevenster te openen in Visual Studio Code, en voert u de volgende opdracht in.

```console
parcel index.html
```

Parcel bundelt uw code en start een lokale ontwikkelserver voor uw pagina op `http://localhost:1234/index.html`. Wijzigingen die u aanbrengt in *index.js* worden automatisch op de ontwikkelingsserver gebouwd en weergegeven wanneer u het bestand opslaat.

Als u een bericht ontvangt met de melding **geconfigureerde poort 1234 kan niet worden gebruikt**, kunt u de poort wijzigen door de opdracht `parcel -p <port#> index.html` uit te voeren. In het bestand *launch.json* werkt u de poort in het URL-pad zodat deze overeenkomt.

### <a name="start-debugging"></a>Foutopsporing starten

Voer de pagina uit in het foutopsporingsprogramma en krijg een idee van de werking van blob-opslag. Als er fouten optreden, wordt het foutbericht weergegeven in het deelvenster **Status** op de webpagina.

Als u *index.html* in de browser wilt openen terwijl het Visual Studio Code-foutopsporingsprogramma is gekoppeld, selecteert u **Run > Start Debugging** of drukt u op F5 in Visual Studio Code.

### <a name="use-the-web-app"></a>De web-app gebruiken

In de [Azure-portal](https://portal.azure.com) kunt u de resultaten van de API-aanroepen controleren wanneer u de volgende stappen uitvoert.

#### <a name="step-1---create-a-container"></a>Stap 1 - Een container maken

1. Selecteer in de web-app **Container maken**. De status geeft aan dat een container is gemaakt.
2. Selecteer uw opslagaccount om in de Azure-portal te verifiëren. Klik onder **Blob service** op **Containers**. Controleer of de nieuwe container wordt weergegeven. (Mogelijk moet u **Vernieuwen** selecteren.)

#### <a name="step-2---upload-a-blob-to-the-container"></a>Stap 2 - Een blob uploaden naar de container

1. Maak een testbestand op uw lokale computer en sla dit op, bijvoorbeeld *test.txt*.
2. Klik in de web-app op **Bestanden selecteren en uploaden**.
3. Blader naar het testbestand en selecteer **Openen**. De status geeft aan dat het bestand is geüpload en dat de lijst met bestanden is opgehaald.
4. Selecteer in de Azure-portal de naam van de nieuwe container die u eerder hebt gemaakt. Controleer of het testbestand wordt weergegeven.

#### <a name="step-3---delete-the-blob"></a>Stap 3 - De blob verwijderen

1. Selecteer in de web-app onder **Bestanden** het testbestand.
2. Selecteer **Geselecteerde bestanden verwijderen**. De status geeft aan dat het bestand is verwijderd en dat de container geen bestanden bevat.
3. Selecteer **Vernieuwen** in de Azure-portal. Controleer of **Geen blobs gevonden** kunt zien.

#### <a name="step-4---delete-the-container"></a>Stap 4 - De container verwijderen

1. Selecteer in de web-app **Container verwijderen**. De status geeft aan dat een container is verwijderd.
2. Selecteer in Azure Portal de koppeling **\<account-name\> | Containers** linksboven in het portaldeelvenster.
3. Selecteer **Vernieuwen**. De nieuwe container verdwijnt.
4. Sluit de web-app.

### <a name="clean-up-resources"></a>Resources opschonen

Klik op de console **Terminal** in Visual Studio Code en druk op CTRL + C om de webserver te stoppen.

Als u de resources wilt opschonen die tijdens deze quickstart zijn gemaakt, gaat u naar de [Azure-portal](https://portal.azure.com) en verwijdert u de resourcegroep die u hebt gemaakt in de sectie [Vereisten](#prerequisites).

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u blobs kunt uploaden, downloaden, weergeven en verwijderen met behulp van JavaScript. U hebt ook geleerd hoe u een blob-opslagcontainer maakt en verwijdert.

Voor zelfstudies, voorbeelden, quickstarts en andere documentatie gaat u naar:

> [!div class="nextstepaction"]
> [Documentatie over Azure voor JavaScript](/azure/developer/javascript/)

* Zie de [Azure Blob-opslag-clientbibliotheek voor JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob)voor meer informatie.
* Als u voorbeeld-apps voor blob-opslag wilt zien, gaat u verder met [Voorbeelden van Azure Blob-opslag-clientbibliotheek v12 JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples).
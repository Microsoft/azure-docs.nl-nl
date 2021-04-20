---
title: Live streamen met Media Services v3 Node.js
titleSuffix: Azure Media Services
description: Meer informatie over live streamen met Node.js.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc, devx-track-nodejs
ms.date: 04/15/2021
ms.author: inhenkel
ms.openlocfilehash: 749d2fc845f036a2802c80c161b3fc8c171c2555
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730527"
---
# <a name="tutorial-stream-live-with-media-services-using-nodejs-and-typescript"></a>Zelfstudie: Live streamen met Media Services met Node.js en TypeScript

> [!NOTE]
> Hoewel in de zelfstudie gebruik wordt Node.js voorbeelden, zijn de algemene stappen hetzelfde voor [REST API,](/rest/api/media/liveevents) [CLI](/cli/azure/ams/live-event)of andere ondersteunde [SDK's.](media-services-apis-overview.md#sdks) 

In Azure Media Services zijn [livegebeurtenissen](/rest/api/media/liveevents) verantwoordelijk voor het verwerken inhoud voor live streamen. Een livegebeurtenis biedt een invoereindpunt (de URL voor opnemen) dat u vervolgens doorgeeft aan een live-encoder. De livegebeurtenis ontvangt live-invoerstromen van de live-encoder en maakt deze beschikbaar voor streaming via een of meer [streaming-eindpunten](/rest/api/media/streamingendpoints). Livegebeurtenissen bieden ook een preview-eindpunt (voorbeeld-URL) dat u kunt gebruiken om een voorbeeld van de stream te bekijken en deze te valideren voordat deze verder wordt verwerkt en geleverd. Deze zelfstudie laat zien hoe u Node.js een **pass-through-type** van een livegebeurtenis maakt en een livestream naar de gebeurtenis uitzendt met [OBS Studio](https://obsproject.com/download).

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Download de voorbeeldcode die in het onderwerp wordt beschreven.
> * Bekijk de code die live streamen configureert en uitvoert.
> * De gebeurtenis bekijken met [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) op [https://ampdemo.azureedge.net](https://ampdemo.azureedge.net).
> * Resources opschonen.


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Hieronder wordt aangegeven wat de vereisten zijn om de zelfstudie te voltooien:

- [Node.js](https://nodejs.org/en/download/) installeren
- [TypeScript installeren](https://www.typescriptlang.org/)
- [Een Azure Media Services-account maken](./create-account-howto.md).<br/>Vergeet niet de waarden die u hebt gebruikt voor de namen van de resourcegroep en het Media Services-account.
- Volg de stappen in [Access Azure Media Services API with the Azure CLI](./access-api-howto.md) (Toegang tot de Azure Media Services-API met de Azure CLI) en sla de referenties op. U moet deze gebruiken om toegang te krijgen tot de API en het bestand met omgevingsvariabelen te configureren.
- Doorloop de informatie [over configureren ](./configure-connect-nodejs-howto.md) en Node.jseerste om te begrijpen hoe u de client-SDK Node.js gebruiken
- Installeer Visual Studio Code of Visual Studio.
- [Stel uw Visual Studio Code-omgeving in ter](https://code.visualstudio.com/Docs/languages/typescript) ondersteuning van de TypeScript-taal.

## <a name="additional-settings-for-live-streaming-software"></a>Aanvullende instellingen voor livestreamingsoftware

- Een camera of een apparaat (zoals een laptop) die wordt gebruikt om een gebeurtenis uit te zenden.
- Zie aanbevolen [on-premises live-encoders](encode-recommended-on-premises-live-encoders.md)voor een on-premises software-encoder die uw camerastream codeert en deze naar de Media Services-service voor live streamen verzendt met behulp van het RTMP-protocol. De stroom moet de **RTMP**- of **Smooth Streaming**-indeling hebben.  
- Voor dit voorbeeld is het raadzaam om te beginnen met een software-encoder zoals de gratis [Open Broadcast Software OBS Studio](https://obsproject.com/download) om eenvoudig aan de slag te gaan.

In dit voorbeeld wordt ervan uitgenomen dat u OBS Studio gebruikt om RTMP uit te zenden naar het opname-eindpunt. Installeer eerst OBS Studio.
Gebruik de volgende coderingsinstellingen in OBS Studio:

- Encoder: NVIDIA NVENC (indien beschikbaar) of x264
- Snelheidscontrole: CBR
- Bitrate: 2500 Kbps (of iets redelijks voor uw laptop)
- Keyframe-interval: 2 s of 1 s voor lage latentie  
- Voorinstelling: Kwaliteit of prestaties met lage latentie (NVENC) of 'zeer snel' met x264
- Profiel: hoog
- GPU: 0 (automatisch)
- Maximum aantal B-frames: 2

> [!TIP]
> Zorg dat u [Live streamen met Media Services v3](stream-live-streaming-concept.md) hebt gelezen voordat u verder gaat.

## <a name="download-and-configure-the-sample"></a>Het voorbeeld downloaden en configureren

Kloon de volgende Git Hub-opslagplaats met het live streamen Node.js voorbeeld naar uw computer met behulp van de volgende opdracht:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
 ```

Het voorbeeld voor live streamen staat in de map [Live](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/Live).

Kopieer in de [map AMSv3Samples](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples) het bestand met de naam sample.env naar een nieuw bestand met de naam .env om de instellingen voor uw omgevingsvariabelen op te slaan die u hebt verzameld in het artikel Access Azure Media Services API with the Azure CLI (Toegang tot Azure Media Services API met de [Azure CLI).](./access-api-howto.md)
Zorg ervoor dat het bestand de punt (.) vóór .env bevat, zodat dit correct werkt met het codevoorbeeld.

Het [.env-bestand](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/main/AMSv3Samples/sample.env) bevat uw AAD-toepassingssleutel en -geheim, samen met accountnaam en abonnementsgegevens die nodig zijn om SDK-toegang tot uw Media Services verifiëren. Het bestand .gitignore is al geconfigureerd om te voorkomen dat dit bestand naar uw gevorkte opslagplaats wordt gepubliceerd. Sta niet toe dat deze referenties worden gelekt, omdat het belangrijke geheimen voor uw account zijn.

> [!IMPORTANT]
> In dit voorbeeld wordt een uniek achtervoegsel voor elke resource gebruikt. Als u de foutopsporing annuleert of de app beëindigt zonder deze helemaal door te lopen, blijven er meerdere livegebeurtenissen in uw account staan. <br/>Zorg dat u de actieve livegebeurtenissen stopt. Anders worden deze **in rekening gebracht**. Voer het programma helemaal tot aan de voltooiing uit om resources automatisch op te schonen. Als het programma vast loopt of u per ongeluk het debugger stopt en de uitvoering van het programma uitbreekt, controleert u de portal om te controleren of u geen livegebeurtenissen hebt overgelaten in de staten Actief of Stand-by die leiden tot ongewenste factureringskosten.

## <a name="examine-the-typescript-code-for-live-streaming"></a>De TypeScript-code voor live streamen bekijken

In deze sectie worden de functies onderzocht die zijn gedefinieerd in het [bestand index.ts](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/main/AMSv3Samples/Live/index.ts) van het *live-project.*

In het voorbeeld wordt een uniek achtervoegsel voor elke resource gemaakt, zodat er geen naamconflicten optreden als u het voorbeeld meerdere keren uitvoert zonder dat er wordt opgeschoond.

### <a name="start-using-media-services-sdk-for-nodejs-with-typescript"></a>Begin met Media Services SDK voor Node.js met TypeScript

Als u Media Services API's met Node.js wilt gaan gebruiken, moet u eerst de [@azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices) SDK-module toevoegen met behulp van npm Package Manager

```bash
npm install @azure/arm-mediaservices
```

In de package.jsis dit al voor u geconfigureerd, dus u hoeft alleen *npm install* uit te voeren om de modules en afhankelijkheden te laden.

1. Open een **opdrachtprompt** en blader naar de map van het voorbeeld.
1. Wijzig de map in de map AMSv3Samples.

    ```bash
    cd AMSv3Samples
    ```

1. Installeer de pakketten die worden gebruikt in het *packages.jsin het bestand.*

    ```bash
    npm install 
    ```

1. Start Visual Studio Code vanuit de *map AMSv3Samples.* (Dit is vereist om te starten vanuit de map waarin de *map .vscode* en *tsconfig.jsbestanden* zich bevinden.)

    ```bash
    cd ..
    code .
    ```

Open de map voor *Live* en open het *bestand index.ts* in de Visual Studio Code-editor.
Druk in het *bestand index.ts* op F5 om het debugger te starten.

### <a name="create-the-media-services-client"></a>De client Media Services maken

Het volgende codefragment laat zien hoe u de Media Services-client in Node.js.
U ziet dat we in deze code eerst de eigenschap **longRunningOperationRetryTimeout** van de AzureMediaServicesOptions instellen op 2 seconden om de tijd te verminderen die nodig is om de status van een langdurige bewerking op het Azure Resource Management-eindpunt te peilen.  Omdat de meeste bewerkingen op livegebeurtenissen asynchroon zullen zijn en enige tijd in beslag kunnen nemen, moet u dit polling-interval voor de SDK verminderen van de standaardwaarde van 30 seconden om de tijd te versnellen die nodig is om belangrijke bewerkingen te voltooien, zoals het maken van livegebeurtenissen, starten en stoppen. Dit zijn allemaal asynchrone aanroepen. Twee seconden is de aanbevolen waarde voor de meeste use-casescenario's.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>Een livegebeurtenis maken

In deze sectie wordt beschreven hoe u een livegebeurtenis van het type **pass-through** maakt (LiveEventEncodingType ingesteld op None). Zie Typen livegebeurtenissen voor meer informatie over de andere beschikbare [typen livegebeurtenissen.](live-event-outputs-concept.md#live-event-types) Naast pass-through kunt u een live transcoderende livegebeurtenis gebruiken voor 720P- of 1080P Adaptive Bitrate Cloud-codering.
 
U kunt een aantal zaken opgeven bij het maken van de livegebeurtenis:

* Het opnameprotocol voor de livegebeurtenis (momenteel worden de RTMP(S) en Smooth Streaming ondersteund).<br/>U kunt de protocoloptie niet wijzigen terwijl de livegebeurtenis of de daaraan gekoppelde live-uitvoer worden uitgevoerd. Als u verschillende protocollen nodig hebt, maakt u voor elk streaming-protocol een afzonderlijke livegebeurtenis.  
* IP-beperkingen voor de opname en voorbeeldweergave. U kunt de IP-adressen definiëren die zijn toegestaan om een video van deze livegebeurtenis op te nemen. Toegestane IP-adressen kunnen worden opgegeven als één IP-adres (bijvoorbeeld 10.0.0.1), een IP-adresbereik met een IP-adres en een CIDR-subnetmasker (bijvoorbeeld 10.0.0.1/22) of een IP-adresbereik met een IP-adres en een decimaal subnetmasker met punten (bijvoorbeeld , ' 10.0.0.1(255.255.252.0)').<br/>Als geen IP-adressen zijn opgegeven en er geen regeldefinitie bestaat, zijn er geen IP-adressen toegestaan. Als u IP-adres(sen) wilt toestaan, maakt u een regel en stelt u 0.0.0.0/0 in.<br/>De IP-adressen moeten een van de volgende indelingen hebben: IpV4-adres met 4 cijfers of een CIDR-adresbereik.
* Bij het maken van de gebeurtenis kunt u opgeven dat deze automatisch wordt gestart. <br/>Wanneer autostart is ingesteld op True, wordt de Live gebeurtenis gestart na het maken ervan. Dit betekent dat facturering begint zodra de livegebeurtenis begint. U moet expliciet Stop aanroepen in de resource van de livegebeurtenis om verdere facturering stop te zetten. Zie [Live Event states and billing](live-event-states-billing-concept.md) (Statussen en facturering voor livegebeurtenissen) voor meer informatie.
Er zijn ook stand-bymodi beschikbaar om de livegebeurtenis te starten in een status met lagere kosten 'toegewezen', waardoor de livegebeurtenis sneller kan worden verplaatst naar de status Actief. Dit is handig voor situaties zoals hot-pools die kanalen snel aan streamers moeten uitzenden.
* Een opname-URL is voorspellend en gemakkelijker te onderhouden in een live-encoder op basis van hardware, stelt u de eigenschap useStaticHostname in op true en gebruikt u een aangepaste unieke GUID in het 'accessToken'. Zie [URL's voor opnemen van livegebeurtenissen](live-event-outputs-concept.md#live-event-ingest-urls) voor gedetailleerde informatie.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateLiveEvent)]

### <a name="create-an-asset-to-record-and-archive-the-live-event"></a>Een asset maken om de livegebeurtenis vast te registreren en te archiveren

In dit codeblok maakt u een lege asset om als tape te gebruiken voor het opnemen van uw livegebeurtenisarchief.
Wanneer u deze concepten leert, kunt u het object Asset het beste zien als de tape die u in de oude tijd in een videotaperecorder zou invoegen. De 'live-uitvoer' is de taperecorder. De 'livegebeurtenis' is slechts het videosignaal dat achter in de machine binnenkomt.

Houd er rekening mee dat de asset, of 'tape', op elk moment kan worden gemaakt. Het is gewoon een lege asset die u aan het live-uitvoerobject, de taperecorder in deze analogie, overhandigt.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateAsset)]

### <a name="create-the-live-output"></a>De live-uitvoer maken

In deze sectie maken we een live-uitvoer die de assetnaam als invoer gebruikt om aan te geven waar de livegebeurtenis moet worden op registreren. Daarnaast stellen we het DVR-venster (time shifting) in dat in de opname moet worden gebruikt.
De voorbeeldcode laat zien hoe u een tijdsverschuifvenster van 1 uur in kunt stellen. Hierdoor kunnen clients overal in het laatste uur van de gebeurtenis afspelen.  Bovendien blijft alleen het laatste uur van de livegebeurtenis in het archief. U kunt dit zo nodig uitbreiden naar maximaal 25 uur.  Houd er ook rekening mee dat u de naamgeving van het uitvoermanifest kunt bepalen die de HLS- en DASH-manifesten in uw URL-paden gebruikt wanneer deze worden gepubliceerd.

De live-uitvoer, of 'taperecorder' in onze analogie, kan ook op elk moment worden gemaakt. Dit betekent dat u een live-uitvoer kunt maken voordat u de signaalstroom start, of later. Als u dingen wilt versnellen, is het vaak handig om deze te maken voordat u de signaalstroom start.

Live-uitvoer starten zodra ze zijn gemaakt en stoppen wanneer ze worden verwijderd.  Wanneer u de live-uitvoer verwijdert, verwijdert u de onderliggende asset of inhoud in de asset niet. U kunt het zien als het uitwerpen van de tape. De asset met de opname duurt zo lang als u wilt en wanneer deze wordt uitwerpt (wat betekent dat wanneer de live-uitvoer wordt verwijderd), is deze onmiddellijk beschikbaar voor weergave op aanvraag.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateLiveOutput)]


### <a name="get-ingest-urls"></a>URL’s voor opnemen ophalen

Wanneer de livegebeurtenis is gemaakt, kunt u URL's voor opnemen ophalen die u aan de live-encoder levert. De encoder gebruikt deze URL's om een livestream in te voeren met behulp van het RTMP-protocol

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#GetIngestURL)]

### <a name="get-the-preview-url"></a>De voorbeeld-URL ophalen

Gebruik het previewEndpoint als u een voorbeeld wilt bekijken en wilt controleren of de invoer van de encoder daadwerkelijk wordt ontvangen.

> [!IMPORTANT]
> Controleer voordat u verder gaat of video naar de voorbeeld-URL stroomt.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#GetPreviewURL)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>Livegebeurtenissen en live-uitvoer maken en beheren

Zodra de stream naar de livegebeurtenis stroomt, kunt u de streaminggebeurtenis starten door een streaming-locator te publiceren die uw client spelers kunnen gebruiken. Hierdoor wordt het beschikbaar voor kijkers via het streaming-eindpunt.

U maakt het signaal eerst door de livegebeurtenis te maken.  Het signaal stroomt pas wanneer u die livegebeurtenis start en uw encoder verbindt met de invoer.

Als u de taperecorder wilt stoppen, roept u verwijderen aan op de LiveOutput. Hiermee wordt de  inhoud van uw archief op de tape 'Asset' niet daadwerkelijk verwijderd, maar wordt alleen de taperecorder verwijderd en wordt de archivering gestopt. De asset wordt altijd bij de gearchiveerde video-inhoud bewaard totdat u verwijderen expliciet aanroept op de asset zelf. Zodra u de liveOutput verwijdert, is de opgenomen inhoud van de asset nog steeds beschikbaar om af te spelen via eventuele reeds gepubliceerde URL's van streaming-locator. Als u de mogelijkheid voor een klant om de gearchiveerde inhoud af te spelen wilt verwijderen, moet u eerst alle locators uit de asset verwijderen en ook de CDN-cache op het URL-pad leegmaken als u een CDN gebruikt voor levering. Anders wordt de inhoud live in de cache van het CDN voor de standaard time-to-live-instelling op het CDN (dit kan maximaal 72 uur duren).)

#### <a name="create-a-streaming-locator-to-publish-hls-and-dash-manifests"></a>Een streaming-locator maken om HLS- en DASH-manifesten te publiceren

> [!NOTE]
> Wanneer uw Media Services-account is gemaakt, wordt er een **standaard** streaming-eindpunt met de status **Gestopt** aan uw account toegevoegd. Als u inhoud wilt streamen en gebruik wilt maken van [dynamische pakketten](encode-dynamic-packaging-concept.md) en dynamische versleuteling, moet het streaming-eindpunt van waar u inhoud wilt streamen, de status **Wordt uitgevoerd** hebben.

Wanneer u de asset publiceert met behulp van een streaming-locator, blijft de livegebeurtenis (tot de lengte van het DVR-venster) weergegeven totdat de streaming-locator verloopt of wordt verwijderd, wat het eerst gebeurt. Op deze manier maakt u de virtuele tape-opname beschikbaar voor uw kijk publiek om live en on-demand te zien. Dezelfde URL kan worden gebruikt om de livegebeurtenis, het DVR-venster of de on-demand asset te bekijken wanneer de opname is voltooid (wanneer de live-uitvoer wordt verwijderd).)

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateStreamingLocator)]

#### <a name="build-the-paths-to-the-hls-and-dash-manifests"></a>De paden naar de HLS- en DASH-manifesten maken

De methode BuildManifestPaths in het voorbeeld laat zien hoe u de deterministisch de streamingpaden maakt die moeten worden gebruikt voor DASH- of HLS-levering aan verschillende clients en speler-frameworks.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#BuildManifestPaths)]

## <a name="watch-the-event"></a>De gebeurtenis bekijken

Als u de gebeurtenis wilt bekijken, kopieert u de streaming-URL die u hebt verkregen toen u de code uitvoerde, beschreven in Een streaming-locator maken. U kunt een speler van uw keuze gebruiken. [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) is beschikbaar om uw stream te testen op https://ampdemo.azureedge.net.

De livegebeurtenis wordt automatisch geconverteerd naar inhoud op aanvraag wanneer deze wordt gestopt. Zelfs na het stoppen en verwijderen van de gebeurtenis kunnen gebruikers de gearchiveerde inhoud als video op aanvraag streamen, zolang u de asset niet verwijdert. Een asset kan niet worden verwijderd als deze wordt gebruikt door een gebeurtenis. U moet eerst de gebeurtenis verwijderen.

### <a name="cleaning-up-resources-in-your-media-services-account"></a>Resources in uw Media Services-account opschonen

Als u de toepassing helemaal doorloop, worden alle resources die worden gebruikt in de functie cleanUpResources automatisch opschonen. Zorg ervoor dat de toepassing of het debugger helemaal tot aan de voltooiing wordt uitgevoerd of dat u resources kunt lekken en uiteindelijk livegebeurtenissen in uw account kunt uitvoeren. Controleer het Azure Portal om te controleren of alle resources in uw Media Services zijn opgeschoond.  

Raadpleeg in de voorbeeldcode de **cleanUpResources-methode** voor meer informatie.

> [!IMPORTANT]
> Zolang de livegebeurtenis loopt, worden er kosten in rekening gebracht. Als het project/programma vastloopt of om welke reden dan ook wordt afgesloten, is het mogelijk dat de livegebeurtenis in een factureringsstaat staat.

## <a name="ask-questions-give-feedback-get-updates"></a>Vragen stellen, feedback geven, updates ophalen

Ga naar het artikel van de [Azure Media Services-community](media-services-community.md) voor verschillende manieren om vragen te stellen, feedback te geven en updates voor Media Services op te halen.

## <a name="more-developer-documentation-for-nodejs-on-azure"></a>Meer documentatie voor ontwikkelaars voor Node.js in Azure

- [Azure voor JavaScript-& Node.js ontwikkelaars](/azure/developer/javascript/)
- [Media Services code in de @azure/azure-sdk-for-js Git Hub-repo](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [Documentatie voor Azure-pakketten Node.js ontwikkelaars](/javascript/api/overview/azure/)


## <a name="next-steps"></a>Volgende stappen

[Bestanden streamen](stream-files-tutorial-with-api.md)
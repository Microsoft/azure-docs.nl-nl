---
Titel: uploaden, coderen en streamen met Media Services V3: Azure Media Services beschrijving: zelf studie waarin wordt getoond hoe u een bestand uploadt, video versleutelt en inhoud streamt met Azure Media Services v3.
Services: Media Services documentationcenter: ' ' Auteur: IngridAtMicrosoft Manager: femila editor: ' '

MS. service: Media-Services MS. workload: MS. topic: zelf studie MS. Custom: MVC MS. date: 08/31/2020 MS. Author: inhenkel
---

# <a name="tutorial-upload-encode-and-stream-videos-with-media-services-v3"></a>Zelfstudie: Video's uploaden, coderen en streamen met Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

> [!NOTE]
> Hoewel in deze zelfstudie [.NET SDK](/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet)-voorbeelden worden gebruikt, zijn de algemene stappen hetzelfde voor [REST API-](/rest/api/media/liveevents), de [CLI-](/cli/azure/ams/live-event) of andere ondersteunde [SDK's](media-services-apis-overview.md#sdks).

Met Azure Media Services kunt u mediabestanden coderen in indelingen die kunnen worden afgespeeld met een groot aantal verschillende browsers en apparaten. Zo kunt u bijvoorbeeld inhoud streamen in de indelingen Apple HLS of MPEG DASH. Voordat u gaat streamen, moet u uw digitale mediabestand van hoge kwaliteit coderen. Zie [Het concept codering](encoding-concept.md) voor hulp bij het coderen. In deze zelfstudie wordt een lokaal videobestand geüpload en wordt het geüploade bestand gecodeerd. U kunt ook inhoud coderen die u toegankelijk maakt via een HTTPS-URL. Zie [Taakinvoer maken via een HTTP(s)-URL](job-input-from-http-how-to.md) voor meer informatie.

![Een video afspelen met Azure Media Player](./media/stream-files-tutorial-with-api/final-video.png)

In deze zelfstudie ontdekt u hoe u:

> [!div class="checklist"]
> * Download de voorbeeld-app, zoals beschreven in het onderwerp.
> * Onderzoek de code die het uploaden, coderen en streamen regelt.
> * Voer de app uit.
> * Test de streaming-URL.
> * Resources opschonen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

- Als u niet beschikt over Visual Studio, kunt u [Visual Studio Community 2019](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15) downloaden.
- [Een Azure Media Services-account maken](./create-account-howto.md).<br/>Vergeet niet de waarden die u hebt gebruikt voor de namen van de resourcegroep en het Media Services-account.
- Volg de stappen in [Access Azure Media Services API with the Azure CLI](./access-api-howto.md) (Toegang tot de Azure Media Services-API met de Azure CLI) en sla de referenties op. U hebt deze nodig voor toegang tot de API.

## <a name="download-and-set-up-the-sample"></a>Het voorbeeld downloaden en instellen

Gebruik de volgende opdracht om een GitHub-opslagplaats met het .NET-voorbeeld voor het streamen van video op uw computer te klonen:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

Het voorbeeld bevindt zich de map [UploadEncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/UploadEncodeAndStreamFiles).

Open [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/appsettings.json) in het project dat u hebt gedownload. Vervang de waarden door referenties die u hebt verkregen via [toegang tot API's](./access-api-howto.md).

## <a name="examine-the-code-that-uploads-encodes-and-streams"></a>De code controleren die wordt geüpload, gecodeerd en gestreamd

In dit gedeelte worden de functies bekeken die zijn gedefinieerd in het bestand [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs) van het project *UploadEncodeAndStreamFiles*.

In het voorbeeld worden de volgende acties uitgevoerd:

1. Er wordt een nieuwe **transformatie** gemaakt (eerst wordt gecontroleerd of de opgegeven transformatie bestaat).
2. Er wordt een **uitvoerasset** gemaakt die wordt gebruikt als uitvoer van de **coderingstaak**.
3. Er wordt een invoer **asset** gemaakt en het opgegeven lokale videobestand wordt erin geladen. De asset wordt gebruikt als de invoer voor de taak.
4. De coderingstaak wordt met behulp van de gemaakte invoer en uitvoer verzonden.
5. De status van de taak controleren.
6. Er wordt een **streaming-locator** gemaakt.
7. Er worden streaming-URL's samengesteld.

### <a name="start-using-media-services-apis-with-net-sdk"></a>Starten met het gebruik van Media Services API's met .NET SDK

Als u wilt starten met Media Services API's met .NET, moet u een **AzureMediaServicesClient**-object maken. Als u het object wilt maken, moet u referenties opgeven die de client nodig heeft om verbinding te maken met Azure met behulp van Microsoft Azure Active Directory. In de code die u aan het begin van het artikel hebt gekloond, wordt met de functie **GetCredentialsAsync** het object ServiceClientCredentials gemaakt op basis van de referenties die zijn opgegeven in het lokale configuratiebestand.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Een invoerasset maken en er een lokaal bestand in uploaden

Met de functie **CreateInputAsset** wordt een nieuwe [invoerasset](/rest/api/media/assets) gemaakt en het opgegeven lokale videobestand wordt hierin geladen. Deze **asset** wordt gebruikt als invoer voor uw coderingstaak. In Media Services v3 kan de invoer voor een **taak** een **asset** zijn ofof inhoud die u beschikbaar stelt voor uw Media Services-account via HTTPS-URL's. Zie [dit](job-input-from-http-how-to.md) artikel als u wilt weten hoe u een HTTPS-URL kunt coderen.

In Media Services v3 kunt u Azure Storage-API's gebruiken om bestanden te uploaden. Het volgende .NET-fragment laat zien hoe.

De volgende functie voert deze acties uit:

* Er wordt een **asset** gemaakt.
* Er wordt een beschrijfbare [SAS-URL](../../storage/common/storage-sas-overview.md) opgehaald voor de [container in opslag](../../storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-a-container) van de asset.

    Als u de functie [ListContainerSas](/rest/api/media/assets/listcontainersas) van de asset gebruikt om SAS-URL's op te halen, moet u er rekening mee houden dat met deze functie meerdere SAS-URL's worden geretourneerd, aangezien er voor elk opslagaccount twee opslagaccountsleutels zijn. Een opslagaccount heeft twee sleutels omdat dit naadloze rotatie van opslagaccountsleutels mogelijk maakt (bijvoorbeeld als u een van de sleutels wilt wijzigen terwijl u de andere gebruikt en vervolgens de nieuwe sleutel wilt gebruiken en de andere roteert). De eerste SAS-URL staat voor opslagsleutel 1 en een tweede voor opslagsleutel 2.
* Uploadt het bestand naar de container in opslag met behulp van de SAS-URL.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-a-job"></a>Een uitvoerasset maken voor het opslaan van het resultaat van een taak

In de [uitvoerasset](/rest/api/media/assets) wordt het resultaat van de coderingstaak opgeslagen. Het project definieert de functie **DownloadResults** die de resultaten van deze uitvoerasset naar de uitvoermap downloadt zodat u kunt zien wat u hebt gekregen.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>Een transformatie en een taak maken die het geüploade bestand codeert

Bij het coderen of verwerken van inhoud in Media Services is het gebruikelijk om de coderingsinstellingen als recept in te stellen. U dient vervolgens een **taak** in te dienen om het recept toe te passen op een video. Door voor elke nieuwe video nieuwe taken in te dienen, past u het recept toe op alle video's in de bibliotheek. Een recept in Media Services wordt een **transformatie** genoemd. Zie [Transformaties en taken](./transforms-jobs-concept.md) voor meer informatie. Het voorbeeld dat wordt beschreven in deze zelfstudie definieert een recept dat de video codeert om het te streamen naar tal van iOS- en Android-apparaten.

#### <a name="transform"></a>Transformeren

Bij het maken van een nieuw [transformatie](/rest/api/media/transforms)-exemplaar, moet u opgeven wat u als uitvoer wilt maken. De vereiste parameter is een **TransformOutput**-object, zoals weergegeven in de onderstaande code. Elke **transformatie-uitvoer** bevat een **voorinstelling**. **Voorinstelling** bevat de stapsgewijze instructies van de video- en/of audioverwerkingen die moeten worden gebruikt voor het genereren van de gewenste **TransformOutput**. Het voorbeeld dat in dit artikel wordt beschreven, maakt gebruik van een ingebouwde voorinstelling genaamd **AdaptiveStreaming** . De voorinstelling codeert de invoervideo in een automatisch gegenereerde bitrate-ladder (bitrate-resolutieparen) op basis van de invoerresolutie en bitsnelheid en produceert ISO MP4-bestanden met H.264-video en AAC-audio die overeenkomen met elk bitrate-resolutiepaar. Zie [een bitrate-ladder automatisch genereren](autogen-bitrate-ladder.md) voor meer informatie over deze voorinstelling.

U kunt een ingebouwde EncoderNamedPreset gebruiken of aangepaste voorinstellingen gebruiken. Zie [Coderingsvoorinstellingen aanpassen](customize-encoder-presets-how-to.md) voor meer informatie.

Bij het maken van een [transformatie](/rest/api/media/transforms) moet u controleren of er al een bestaat met de methode **Ophalen** zoals weergegeven in de volgende code. In Media Services-v3 retourneert de methode **Ophalen** van entiteiten **null** als de entiteit (een hoofdlettergevoelige controle van de naam).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>Taak

Zoals eerder vermeld, is het [transformatie](/rest/api/media/transforms)-object het recept en is de [taak](/rest/api/media/jobs) de werkelijke aanvraag bij Media Services om deze **transformatie** toe te passen op een bepaalde invoervideo of audio-inhoud. De **taak** bevat informatie zoals de locatie van de invoervideo en de locatie voor de uitvoer.

In dit voorbeeld is de invoervideo geüpload naar uw lokale machine. Zie [dit](job-input-from-http-how-to.md) artikel voor meer informatie over het coderen van een HTTPS-URL.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>Wacht tot de taak is voltooid

De taak neemt enige tijd in beslag en wanneer deze is voltooid, wordt u hiervan op de hoogte gesteld. In het onderstaande codevoorbeeld ziet u hoe de status van de [taak](/rest/api/media/jobs) kan worden opgevraagd in de service. Navragen is geen aanbevolen best practice voor productie-apps vanwege mogelijke latentie. Polling kan worden beperkt bij een te intensief gebruik op een account. Ontwikkelaars moeten in plaats daarvan Event Grid gebruiken.

Event Grid is ontworpen voor hoge beschikbaarheid, consistente prestaties en dynamisch schalen. Met Event Grid kunnen uw apps luisteren naar en reageren op gebeurtenissen uit vrijwel alle Azure-services, evenals aangepaste bronnen. Eenvoudige, op HTTP gebaseerde reactieve gebeurtenisafhandeling maakt het mogelijk om efficiënte oplossingen te bouwen met intelligente filtering en routering van gebeurtenissen.  Zie [Gebeurtenissen routeren naar een aangepast eindpunt](job-state-events-cli-how-to.md).

De **taak** doorloopt meestal de volgende statussen: **Gepland**, **In de wachtrij geplaatst**, **Verwerken**, **Voltooid** (de eindstatus). Als bij de taak een fout is opgetreden is, krijgt u de status **Fout**. Als de taak momenteel wordt geannuleerd, krijgt u de meldingen **Wordt geannuleerd** en **Geannuleerd** wanneer het annuleren is voltooid.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#WaitForJobToFinish)]

### <a name="job-error-codes"></a>Foutcodes in taak

Zie [Foutcodes](/rest/api/media/jobs/get#joberrorcode).

### <a name="get-a-streaming-locator"></a>Een streaming-locator ophalen

Nadat de codering is voltooid, bestaat de volgende stap eruit om de video in de uitvoerasset beschikbaar te maken voor weergave door clients. U kunt het in twee stappen beschikbaar maken: maak eerst een [streaming-locator](/rest/api/media/streaminglocators) en bouw vervolgens de streaming-URL's die de clients kunnen gebruiken.

Het proces van het maken van een **streaming-locator** wordt publiceren genoemd. Nadat u de API-aanroepen hebt uitgevoerd, is de **streaming-locator** standaard onmiddellijk geldig totdat deze wordt verwijderd, tenzij u de optionele start- en eindtijden configureert.

Bij het maken van een [StreamingLocator](/rest/api/media/streaminglocators) moet u de gewenste **StreamingPolicyName** opgeven. In dit voorbeeld gaat u in-the-clear (of niet-versleutelde) inhoud streamen, zodat het vooraf gedefinieerde, duidelijke streamingbeleid, **PredefinedStreamingPolicy.ClearStreamingOnly**, wordt gebruikt.

> [!IMPORTANT]
> Wanneer u een aangepast [streamingbeleid](/rest/api/media/streamingpolicies) gebruikt, moet u een beperkte set met dergelijke beleidsregels ontwerpen voor uw Media Service-account, en deze opnieuw gebruiken voor de StreamingLocators wanneer dezelfde versleutelingsopties en protocollen nodig zijn. Uw Media Service-account heeft een quotum voor het aantal streaming-beleidsvermeldingen. U hoeft geen nieuw streaming-beleid te maken voor elke streaming-locator.

Bij de volgende code wordt ervan uitgegaan dat u de functie met een unieke locatorName aanroept.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateStreamingLocator)]

Terwijl in het voorbeeld in dit onderwerp streaming wordt behandeld, kunt u dezelfde aanroep gebruiken om een streaming-locator te maken voor het leveren van video via progressief downloaden.

### <a name="get-streaming-urls"></a>Streaming-URL's ophalen

Nu de [streaming-locator](/rest/api/media/streaminglocators) is gemaakt, kunt u de streaming-URL's ophalen, zoals weergegeven in **GetStreamingURLs**. Als u een URL wilt samenstellen, moet u de hostnaam van het [streaming-eindpunt](/rest/api/media/streamingendpoints) en het pad van de **streaming-locator** samenvoegen. In dit voorbeeld wordt het *standaard***streaming-eindpunt** gebruikt. Wanneer u voor het eerst een Media Service-account maakt, wordt dit *standaard***streaming-eindpunt** gestopt. Daarom moet u **Start** aanroepen.

> [!NOTE]
> In deze methode hebt u de locatorName nodig die is gebruikt bij het maken van de **streaming-locator** voor de uitvoerasset.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#GetStreamingURLs)]

### <a name="clean-up-resources-in-your-media-services-account"></a>Resources in uw Media Services-account opschonen

Over het algemeen moet u alles opschonen, behalve objecten die u van plan bent opnieuw te gebruiken (meestal gebruikt u transformaties opnieuw en behoudt u StreamingLocators, enzovoort). Als u wilt dat uw account na het experiment wordt opgeschoond, moet u de resources verwijderen die u niet opnieuw wilt gebruiken. Met het volgende codevoorbeeld worden de taak, de gemaakte assets en het beleid voor inhoudssleutels verwijderd:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CleanUp)]

## <a name="run-the-sample-app"></a>De voorbeeld-app uitvoeren

1. Druk op Ctrl+F5 om de app *EncodeAndStreamFiles* uit te voeren.
2. Kopieer een van de streaming-URL's van de console.

In dit voorbeeld worden URL's weergegeven die kunnen worden gebruikt om de video met verschillende protocollen af te spelen:

![Voorbeelduitvoer met URL's voor streaming-video van Media Services](./media/stream-files-tutorial-with-api/output.png)

## <a name="test-the-streaming-url"></a>De streaming-URL testen

In dit artikel gebruiken we Azure Media Player om de stream te testen.

> [!NOTE]
> Als een speler wordt gehost op een https-site, moet u de URL bijwerken naar 'https'.

1. Open een browser en ga naar [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Plak in het vak **URL:** een van de streaming-URL's die u hebt verkregen tijdens het uitvoeren van de app.
3. Selecteer **Speler bijwerken**.

Azure Media Player kan worden gebruikt voor testdoeleinden, maar mag niet worden gebruikt in een productieomgeving.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources van de resourcegroep niet meer nodig hebt, met inbegrip van de Media Services en opslagaccounts die u hebt gemaakt voor deze zelfstudie, verwijdert u de resourcegroep die u eerder hebt gemaakt.

Voer de volgende CLI-opdracht uit:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Multithreading

De SDK's van Azure Media Services v3 zijn niet thread-safe. Als u een app met meerdere threads ontwikkelt, moet u per thread een nieuw AzureMediaServicesClient-object genereren en gebruiken.

## <a name="ask-questions-give-feedback-get-updates"></a>Vragen stellen, feedback geven, updates ophalen

Ga naar het artikel van de [Azure Media Services-community](media-services-community.md) voor verschillende manieren om vragen te stellen, feedback te geven en updates voor Media Services op te halen.

## <a name="next-steps"></a>Volgende stappen

Nu u weet hoe u uw video kunt uploaden, coderen en streamen, kunt u doorgaan naar het volgende artikel: 

> [!div class="nextstepaction"]
> [Video's analyseren](analyze-videos-tutorial-with-api.md)

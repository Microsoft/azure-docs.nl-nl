---
Titel: video versleutelen met AES-128: beschrijving van Azure Media Services: meer informatie over het versleutelen van video met AES 128-bits versleuteling en het gebruik van de key delivery service in Azure Media Services.
Services: Media Services documentationcenter: ' ' Auteur: IngridAtMicrosoft Manager: femila editor: ' '

MS. service: Media-Services MS. workload: medium ms.tgt_pltfrm: na MS. devlang: na MS. topic: zelf studie MS. date: 03/17/2021 MS. Author: inhenkel

---
# <a name="tutorial-encrypt-video-with-aes-128-and-use-the-key-delivery-service"></a>Zelfstudie: Video versleutelen met AES-128 en de sleutelleveringsservice gebruiken

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

> [!NOTE]
> Hoewel in de zelfstudie de [.NET SDK](/dotnet/api/microsoft.azure.management.media.models.liveevent)-voorbeelden worden gebruikt, zijn de algemene stappen hetzelfde voor de SDK’s van [REST API](/rest/api/media/liveevents) of [CLI](/cli/azure/ams/live-event), of voor andere ondersteunde [SDK's](media-services-apis-overview.md#sdks).

U kunt Media Services gebruiken om HLS (HTTP Live Streaming), MPEG-DASH, en Smooth Streaming te leveren, versleuteld met de AES met behulp van 128-bits versleutelingssleutels. Media Services biedt ook de sleutelleveringsservice die versleutelingssleutels levert aan gemachtigde gebruikers. Als u wilt dat Media Services uw video dynamisch versleutelt, koppelt u de versleutelingssleutel aan een streaminglocator, en configureert u ook het beleid voor de inhoudssleutel. Wanneer een stream wordt aangevraagd door een speler, maakt Media Services gebruik van de opgegeven sleutel om uw inhoud dynamisch te versleutelen met AES-128. Voor het ontsleutelen van de stream, wordt door de speler de sleutel van de sleutelleveringsservice aangevraagd. De service evalueert het door u opgegeven beleid voor de inhoudssleutel om te bepalen of de gebruiker is gemachtigd om de sleutel op te halen.

U kunt elke asset met meerdere versleutelingstypen versleutelen (AES-128, PlayReady, Widevine, FairPlay). Zie [Streamingprotocollen en versleutelingstypen](drm-content-protection-concept.md#streaming-protocols-and-encryption-types) voor nuttige combinaties. Zie ook [Beschermen met DRM](drm-protect-with-drm-tutorial.md).

De uitvoer uit het voorbeeld in dit artikel omvat een URL naar de Azure Media Player, de manifest-URL en het AES-token dat nodig is om de inhoud af te spelen. In het voorbeeld wordt de geldigheidsduur van het JWT-token (JSON Web Token) ingesteld op 1 uur. U kunt een browser openen en de resulterende URL erin plakken om de demopagina van Azure Media Player te starten waarin de URL en het token al zijn ingevuld in de volgende indeling: ```https://ampdemo.azureedge.net/?url= {dash Manifest URL} &aes=true&aestoken=Bearer%3D{ JWT Token here}```.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Het voorbeeld [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) downloaden dat wordt beschreven in het artikel.
> * Starten met het gebruik van Media Services API's met .NET SDK.
> * Een uitvoeractivum maken.
> * Een coderingstransformatie maken.
> * Een taak indienen.
> * Wachten tot de taak is voltooid.
> * Beleid voor inhoudssleutels maken
> * Beleid configureren om de beperking van het JWT-token te gebruiken.
> * Een streaminglocator maken.
> * De streaminglocator configureren om de video te versleutelen met AES (ClearKey).
> * Een testtoken ophalen.
> * Een streaming-URL samenstellen.
> * Resources opschonen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Hieronder wordt aangegeven wat de vereisten zijn om de zelfstudie te voltooien.

* Lees het artikel [Content protection overview](drm-content-protection-concept.md) (Overzicht inhoudsbeveiliging).
* Installeer Visual Studio Code of Visual Studio.
* [Een Azure Media Services-account maken](./account-create-how-to.md).
* Zorg dat u referenties hebt die nodig zijn om Media Services API's te kunnen gebruiken via [toegangs-API's](./access-api-howto.md).

## <a name="download-code"></a>Code downloaden

Kloon met de volgende opdracht een GitHub-opslagplaats met het volledige .NET-voorbeeld dat in dit artikel wordt beschreven, op de computer:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

Het voorbeeld 'Versleutelen met AES-128' bevindt zich in de map [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES).

> [!NOTE]
> Met het voorbeeld worden unieke resources gemaakt telkens wanneer u de app uitvoert. Normaal gesproken gebruikt u bestaande resources, zoals transformaties en beleidsregels opnieuw (mits de bestaande resource over de vereiste configuraties beschikt).

## <a name="start-using-media-services-apis-with-net-sdk"></a>Starten met het gebruik van Media Services API's met .NET SDK

Als u wilt starten met Media Services API's met .NET, moet u een **AzureMediaServicesClient**-object maken. Als u het object wilt maken, moet u referenties opgeven die de client nodig heeft om verbinding te maken met Azure met behulp van Microsoft Azure Active Directory. In de code die u aan het begin van het artikel hebt gekloond, wordt met de functie **GetCredentialsAsync** het object ServiceClientCredentials gemaakt op basis van de referenties die zijn opgegeven in het lokale configuratiebestand.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Een uitvoeractivum maken  

In de [uitvoerasset](/rest/api/media/assets) wordt het resultaat van de coderingstaak opgeslagen.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateOutputAsset)]

## <a name="get-or-create-an-encoding-transform"></a>Een coderingstransformatie verkrijgen of maken

Bij het maken van een nieuw [transformatie](/rest/api/media/transforms)-exemplaar, moet u opgeven wat u als uitvoer wilt maken. De vereiste parameter is een **TransformOutput**-object, zoals weergegeven in de onderstaande code. Elke **transformatie-uitvoer** bevat een **voorinstelling**. **Voorinstelling** bevat de stapsgewijze instructies van de video- en/of audioverwerkingen die moeten worden gebruikt voor het genereren van de gewenste **TransformOutput**. Het voorbeeld dat in dit artikel wordt beschreven, maakt gebruik van een ingebouwde voorinstelling genaamd **AdaptiveStreaming** . De voorinstelling codeert de invoervideo in een automatisch gegenereerde bitrate-ladder (bitrate-resolutieparen) op basis van de invoerresolutie en bitsnelheid, en produceert vervolgens ISO MP4-bestanden met H.264-video en AAC-audio die overeenkomen met elk bitrate-resolutiepaar.

Voordat u een nieuwe [transformatie](/rest/api/media/transforms) maakt, controleert u eerst of er al een bestaat. Dit doet u met de methode **Ophalen**, zoals weergegeven in de volgende code. In Media Services-v3 retourneert de methode **Ophalen** van entiteiten **null** als de entiteit (een hoofdlettergevoelige controle van de naam).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Taak indienen

Zoals eerder vermeld, is het [transformatie](/rest/api/media/transforms)-object het recept en is de [taak](/rest/api/media/jobs) de werkelijke aanvraag bij Media Services om deze **transformatie** toe te passen op een bepaalde invoervideo of audio-inhoud. De **taak** bevat informatie zoals de locatie van de invoervideo en de locatie voor de uitvoer.

In deze zelfstudie maakt u invoer voor de taak op basis van een bestand dat rechtstreeks vanuit een [HTTPs bron-URL](job-input-from-http-how-to.md) is opgenomen.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Wacht tot de taak is voltooid

Het duurt even voordat de taak is voltooid. Wanneer dit is gebeurd, wilt u daarover een melding ontvangen. In het onderstaande codevoorbeeld ziet u hoe de status van de [taak](/rest/api/media/jobs) kan worden opgevraagd in de service. Navragen is geen aanbevolen best practice voor productie-apps vanwege mogelijke latentie. Polling kan worden beperkt bij een te intensief gebruik op een account. Ontwikkelaars moeten in plaats daarvan Event Grid gebruiken. Zie [Gebeurtenissen routeren naar een aangepast webeindpunt](monitoring/job-state-events-cli-how-to.md) voor meer informatie.

De **taak** doorloopt meestal de volgende statussen: **Gepland**, **In de wachtrij geplaatst**, **Verwerken**, **Voltooid** (de eindstatus). Als bij de taak een fout is opgetreden, krijgt u de status **Fout**. Als de taak momenteel wordt geannuleerd, krijgt u de meldingen **Wordt geannuleerd** en **Geannuleerd** wanneer het annuleren is voltooid.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>Beleid voor inhoudssleutels maken

Een inhoudssleutel biedt veilige toegang tot uw assets. U moet **Beleid voor de inhoudssleutels** maken om te configureren hoe de inhoudssleutel wordt geleverd aan eindclients. De inhoudssleutel is gekoppeld aan de **streaminglocator**. Media Services biedt ook de sleutelleveringsservice die versleutelingssleutels levert aan gemachtigde gebruikers.

Wanneer een stream wordt aangevraagd door een speler, gebruikt Media Services de opgegeven sleutel om uw inhoud dynamisch te versleutelen (in dit geval met AES-versleuteling). Voor het ontsleutelen van de stream, wordt door de speler de sleutel van de sleutelleveringsservice aangevraagd. De service evalueert het door u opgegeven beleid voor de inhoudssleutel om te bepalen of de gebruiker is gemachtigd om de sleutel op te halen.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>Een streaming-locator maken

Nadat de codering is voltooid en het inhoudssleutelbeleid is ingesteld, bestaat de volgende stap eruit om de video in de uitvoerasset beschikbaar te maken voor weergave door clients. U maakt de video in twee stappen beschikbaar:

1. Een [streaminglocator](/rest/api/media/streaminglocators) maken.
2. De streaming-URL's bouwen die clients kunnen gebruiken.

Het proces voor het maken van de **streaminglocator** wordt publiceren genoemd. Standaard is de **streaminglocator** direct geldig nadat u de API-aanroepen hebt gemaakt. De streaminglocator blijft geldig totdat deze wordt verwijderd, tenzij u de optionele begin- en eindtijden configureert.

Bij het maken van een [streaminglocator](/rest/api/media/streaminglocators) moet u de gewenste **StreamingPolicyName** opgeven. In deze zelfstudie gebruiken we een van de PredefinedStreamingPolicies. Deze worden in Azure Media Services gebruikt om te bepalen hoe de inhoud voor streaming moet worden gepubliceerd. In dit voorbeeld wordt de AES Envelope-versleuteling toegepast. (Deze versleuteling staat ook bekend als ClearKey-versleuteling omdat de sleutel wordt geleverd aan de afspeelclient, via HTTPS en niet via een DRM-licentie).

> [!IMPORTANT]
> Wanneer u een aangepast [streamingbeleid](/rest/api/media/streamingpolicies) gebruikt, moet u een beperkte set met dergelijke beleidsregels ontwerpen voor uw Media Service-account, en deze opnieuw gebruiken voor de streaminglocators wanneer dezelfde versleutelingsopties en protocollen nodig zijn. Uw Media Service-account heeft een quotum voor het aantal StreamingPolicy-vermeldingen. U hoeft geen nieuw streamingbeleid te maken voor elke streaminglocator.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Een test-token ophalen

In deze zelfstudie geven we voor het inhoudssleutelbeleid op dat het een tokenbeperking heeft. Het beleid met de tokenbeperking moet vergezeld gaan van een token dat is uitgegeven door een beveiligingstokenservice (STS). Media Services biedt ondersteuning voor tokens in de [JWT](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_3)-indeling. Dit is wat wordt geconfigureerd in het voorbeeld.

De ContentKeyIdentifierClaim wordt gebruikt in het **Beleid voor de inhoudssleutels**, wat betekent dat het token dat aan de sleutelleveringsservice wordt gepresenteerd, de id van de inhoudssleutel moet bevatten. In het voorbeeld wordt geen inhoudssleutel opgegeven bij het maken van de streaminglocator. Het systeem maakt een willekeurige aan. Om de testtoken te kunnen maken, moet de ContentKeyId de claim ContentKeyIdentifierClaim kunnen invoegen.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetToken)]

## <a name="build-a-dash-streaming-url"></a>Een DASH-streaming-URL bouwen

Nu de [streaminglocator](/rest/api/media/streaminglocators) is gemaakt, kunt u de streaming-URL's ophalen. Als u de URL wilt samenstellen, moet u de hostnaam van het [StreamingEndpoint](/rest/api/media/streamingendpoints) en het pad van de **streaminglocator** samenvoegen. In dit voorbeeld wordt het *standaard***streaming-eindpunt** gebruikt. Wanneer u voor het eerst een Media Service-account maakt, wordt dit *standaard***streaming-eindpunt** gestopt. Daarom moet u **Start** aanroepen.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>Resources in uw Media Services-account opschonen

Over het algemeen moet u alles opschonen, behalve objecten die u van plan bent opnieuw te gebruiken. (Meestal gebruikt u transformaties, streaminglocators enzovoort opnieuw.) Als u wilt dat uw account na het experiment is opgeschoond, moet u de resources verwijderen die u niet van plan bent opnieuw te gebruiken. Met het volgende codevoorbeeld worden de taak, de gemaakte assets en het beleid voor inhoudssleutels verwijderd:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources van de resourcegroep niet meer nodig hebt, met inbegrip van de Media Services en opslagaccounts die u hebt gemaakt voor deze zelfstudie, verwijdert u de resourcegroep die u eerder hebt gemaakt.

Voer de volgende CLI-opdracht uit:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="ask-questions-give-feedback-get-updates"></a>Vragen stellen, feedback geven, updates ophalen

Ga naar het artikel van de [Azure Media Services-community](media-services-community.md) voor verschillende manieren om vragen te stellen, feedback te geven en updates voor Media Services op te halen.

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Widevine is een service van Google Inc. en is onderworpen aan de servicevoorwaarden en het privacybeleid van Google Inc.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Beschermen met DRM](drm-protect-with-drm-tutorial.md)

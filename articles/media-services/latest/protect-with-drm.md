---
Titel: Azure Media Services DRM-versleuteling en licentie bezorgings service: Azure Media Services beschrijving: informatie over het gebruik van DRM dynamische versleuteling en licentie leverings service om stromen te leveren die zijn versleuteld met de micro soft PlayReady-, Google Widevine-of Apple FairPlay-licenties.
Services: Media Services documentationcenter: ' ' Auteur: IngridAtMicrosoft Manager: femila editor: ' '

MS. service: Media-Services MS. workload: medium ms.tgt_pltfrm: na MS. devlang: na MS. topic: zelf studie MS. date: 08/31/2020 MS. Author: inhenkel MS. Custom: seodec18

---
# <a name="tutorial-use-drm-dynamic-encryption-and-license-delivery-service"></a>Zelfstudie: Het gebruik van de Digital Rights Management-service voor dynamische versleuteling en licentielevering

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

> [!NOTE]
> Hoewel in deze zelfstudie de [.NET SDK](/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet)-voorbeelden worden gebruikt, zijn de algemene stappen hetzelfde voor de [REST API](/rest/api/media/liveevents), de [CLI-](/cli/azure/ams/live-event?view=azure-cli-latest) of andere ondersteunde [SDK's](media-services-apis-overview.md#sdks).

U kunt Azure Media Services gebruiken voor het leveren van streams die zijn versleuteld met licenties van Microsoft PlayReady, Google Widevine of Apple FairPlay. Zie [Inhoudsbeveiliging met dynamische versleuteling](content-protection-overview.md) voor een gedetailleerde uitleg.

Media Services voorziet bovendien in een service voor het leveren van licenties van PlayReady, Widevine en FairPlay. Wanneer een gebruiker door DRM beveiligde inhoud aanvraagt, wordt met de speler-app een licentie aangevraagd bij de licentieservice van Media Services. Als de speler-app is geautoriseerd, ontvangt deze een licentie van de licentieservice van Media Services. Een licentie bevat de ontsleutelingssleutel die kan worden gebruikt door de clientspeler om de inhoud te ontsleutelen en te streamen.

Dit artikel is gebaseerd op het voorbeeld waarbij [DRM wordt gebruikt voor de versleuteling](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM).

In het voorbeeld in dit artikel wordt het volgende resultaat bereikt:

![AMS met DRM beveiligde video in Azure Media Player](./media/protect-with-drm/ams_player.png)

In deze zelfstudie ontdekt u hoe u:

> [!div class="checklist"]
> * Een coderingstransformatie kunt maken.
> * De ondertekeningssleutel instellen die wordt gebruik om uw token te verifiëren.
> * Vereisten kunt instellen voor het beleid voor inhoudssleutels.
> * Een StreamingLocator kunt maken met het opgegeven streamingbeleid.
> * Een URL maakt die wordt gebruikt om uw bestand af te spelen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Hieronder wordt aangegeven wat de vereisten zijn om de zelfstudie te voltooien:

* Lees het artikel [Content protection overview](content-protection-overview.md) (Overzicht inhoudsbeveiliging).
* Lees [Inhoudsbeveiligingssysteem met multi-DRM met toegangsbeheer ontwerpen](design-multi-drm-system-with-access-control.md).
* Installeer Visual Studio Code of Visual Studio.
* Maak een nieuw Media Services-account zoals beschreven in deze [snelstart](./create-account-howto.md).
* Zorg dat u referenties hebt die nodig zijn om Media Services-API's te kunnen gebruiken via [toegangs-API's](./access-api-howto.md)
* Stel de juiste waarden in in het configuratiebestand van de app (appsettings.json).

## <a name="download-code"></a>Code downloaden

Kloon met de volgende opdracht een GitHub-opslagplaats met het volledige .NET-voorbeeld dat in dit artikel wordt beschreven, op de computer:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
Het voorbeeld 'Versleutelen met DRM' bevindt zich in de map [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM).

> [!NOTE]
> Met het voorbeeld worden unieke resources gemaakt telkens wanneer u de app uitvoert. Normaal gesproken gebruikt u bestaande resources, zoals transformaties en beleidsregels opnieuw (mits de bestaande resource over de vereiste configuraties beschikt).

## <a name="start-using-media-services-apis-with-net-sdk"></a>Starten met het gebruik van Media Services API's met .NET SDK

Als u wilt starten met Media Services API's met .NET, moet u een **AzureMediaServicesClient**-object maken. Als u het object wilt maken, moet u referenties opgeven die de client nodig heeft om verbinding te maken met Azure met behulp van Microsoft Azure Active Directory. In de code die u aan het begin van het artikel hebt gekloond, wordt met de functie **GetCredentialsAsync** het object ServiceClientCredentials gemaakt op basis van de referenties die zijn opgegeven in het lokale configuratiebestand.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Een uitvoeractivum maken  

In de [uitvoerasset](assets-concept.md) wordt het resultaat van de coderingstaak opgeslagen.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateOutputAsset)]

## <a name="get-or-create-an-encoding-transform"></a>Een coderingstransformatie verkrijgen of maken

Bij het maken van een nieuw [transformatie](transforms-jobs-concept.md)-exemplaar, moet u opgeven wat u als uitvoer wilt maken. De vereiste parameter is een `transformOutput`-object, zoals weergegeven in de onderstaande code. Elke transformatie-uitvoer bevat een **voorinstelling**. Voorinstelling bevat de stapsgewijze instructies van de video- en/of audioverwerkingen die moeten worden gebruikt voor het genereren van de gewenste TransformOutput. Het voorbeeld dat in dit artikel wordt beschreven, maakt gebruik van een ingebouwde voorinstelling genaamd **AdaptiveStreaming** . De voorinstelling codeert de invoervideo in een automatisch gegenereerde bitrate-ladder (bitrate-resolutieparen) op basis van de invoerresolutie en bitsnelheid en produceert ISO MP4-bestanden met H.264-video en AAC-audio die overeenkomen met elk bitrate-resolutiepaar. 

Voordat u een nieuwe **transformatie** gaat maken, moet u eerst controleren of er al een bestaat. Dit doet u met de methode **Ophalen**, zoals weergegeven in de volgende code.  In Media Services-v3 retourneert de methode **Ophalen** van entiteiten **null** als de entiteit (een hoofdlettergevoelige controle van de naam).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Taak indienen

Zoals eerder vermeld, is het **transformatie**-object het recept en is de [taak](transforms-jobs-concept.md) de werkelijke aanvraag bij Media Services om deze **transformatie** toe te passen op een bepaalde invoervideo of audio-inhoud. De **taak** bevat informatie zoals de locatie van de invoervideo en de locatie voor de uitvoer.

In deze zelfstudie maakt u invoer voor de taak op basis van een bestand dat rechtstreeks vanuit een [HTTPs bron-URL](job-input-from-http-how-to.md) is opgenomen.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Wacht tot de taak is voltooid

Het duurt even voordat de taak is voltooid. Wanneer dit is gebeurd, wilt u daarover een melding ontvangen. In het onderstaande codevoorbeeld ziet u hoe de status van de **taak** kan worden opgevraagd in de service. Navragen is geen aanbevolen best practice voor productie-apps vanwege mogelijke latentie. Polling kan worden beperkt bij een te intensief gebruik op een account. Ontwikkelaars moeten in plaats daarvan Event Grid gebruiken. Zie [Gebeurtenissen routeren naar een aangepast eindpunt](job-state-events-cli-how-to.md).

De **taak** doorloopt meestal de volgende statussen: **Gepland**, **In de wachtrij geplaatst**, **Verwerken**, **Voltooid** (de eindstatus). Als bij de taak een fout is opgetreden, krijgt u de status **Fout**. Als de taak momenteel wordt geannuleerd, krijgt u de meldingen **Wordt geannuleerd** en **Geannuleerd** wanneer het annuleren is voltooid.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>Beleid voor inhoudssleutels maken

Een inhoudssleutel biedt veilige toegang tot uw assets. U moet een [beleid voor inhoudssleutels](content-key-policy-concept.md) maken wanneer u uw inhoud versleutelt met een DRM. Het beleid configureert hoe de inhoudssleutel wordt geleverd aan eindclients. De inhoudssleutel is gekoppeld aan een StreamingLocator. Media Services verzorgt ook de sleutelleveringsservice waarmee versleutelingssleutels en -licenties aan gemachtigde gebruikers worden geleverd.

U moet de vereisten (beperkingen) instellen voor **inhoudssleutelbeleid** waaraan moet worden voldaan voor het leveren van sleutels met de opgegeven configuratie. In dit voorbeeld worden de volgende configuraties en vereisten ingesteld:

* Configuratie

    De licenties van [PlayReady](playready-license-template-overview.md) en [Widevine](widevine-license-template-overview.md) zijn geconfigureerd om te kunnen worden geleverd door de Media Services-service voor het leveren van licenties. Hoewel met deze voorbeeld-app de licentie van [FairPlay](fairplay-license-overview.md) niet wordt geconfigureerd, bevat deze een methode waarmee u FairPlay kunt configureren. U kunt het configureren van FairPlay als een andere optie toevoegen.

* Beperking

    Door de app wordt voor het beleid een beperking voor tokens van het type JWT (JSON Web Token) ingesteld.

Wanneer een stream wordt aangevraagd door een speler, maakt Media Services gebruik van de opgegeven sleutel om uw inhoud dynamisch te versleutelen. Voor het ontsleutelen van de stream, wordt door de speler de sleutel van de sleutelleveringsservice aangevraagd. De service evalueert het door u opgegeven beleid voor de inhoudssleutel om te bepalen of de gebruiker is gemachtigd om de sleutel op te halen.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>Een streaming-locator maken

Nadat de codering is voltooid en het inhoudssleutelbeleid is ingesteld, bestaat de volgende stap eruit om de video in de uitvoerasset beschikbaar te maken voor weergave door clients. U maakt de video in twee stappen beschikbaar:

1. Een [streaminglocator](streaming-locators-concept.md) maken.
2. De streaming-URL's bouwen die clients kunnen gebruiken.

Het proces voor het maken van de **streaminglocator** wordt publiceren genoemd. Standaard is de **streaminglocator** direct geldig nadat u de API-aanroepen hebt gemaakt. De streaminglocator blijft geldig totdat deze wordt verwijderd, tenzij u de optionele begin- en eindtijden configureert.

Bij het maken van een **streaminglocator** moet u de gewenste `StreamingPolicyName` opgeven. In deze zelfstudie gebruiken we een vooraf gedefinieerd Streaming-beleid. Dit wordt door Azure Media Services gebruikt om te bepalen hoe de inhoud voor streaming moet worden gepubliceerd. In dit voorbeeld stellen we StreamingLocator.StreamingPolicyName in op het beleid Predefined_MultiDrmCencStreaming. Er worden dan PlayReady- en Widevine-versleutelingen toegepast, en de sleutel wordt aan de afspeelclient geleverd op basis van de geconfigureerde DRM-licenties. Als u wilt dat uw stream ook met CBCS (FairPlay) wordt versleuteld, gebruikt u Predefined_MultiDrmStreaming.

> [!IMPORTANT]
> Wanneer u een aangepast [streamingbeleid](streaming-policy-concept.md) gebruikt, moet u een beperkte set met dergelijke beleidsregels ontwerpen voor uw Media Service-account, en deze opnieuw gebruiken voor de StreamingLocators wanneer dezelfde versleutelingsopties en protocollen nodig zijn. Uw Media Service-account heeft een quotum voor het aantal StreamingPolicy-vermeldingen. U dient geen nieuw StreamingPolicy voor elke StreamingLocator te maken.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Een test-token ophalen

In deze zelfstudie geven we voor het inhoudssleutelbeleid op dat het een tokenbeperking heeft. Het beleid met de tokenbeperking moet vergezeld gaan van een token dat is uitgegeven door een beveiligingstokenservice (STS). Media Services biedt ondersteuning voor tokens in de [JWT-indelingen](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_3) en dat is wat we in het voorbeeld gaan configureren.

De ContentKeyIdentifierClaim wordt in de ContentKeyPolicy gebruikt, wat inhoudt dat de token die aan de sleutelleveringsservice wordt gepresenteerd, de identificatie van de ContentKey moet bevatten. In het voorbeeld wordt geen inhoudssleutel opgegeven bij het maken van de streaminglocator. Het systeem maakt een willekeurige aan. Om de testtoken te kunnen maken, moet de ContentKeyId de claim ContentKeyIdentifierClaim kunnen invoegen.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetToken)]

## <a name="build-a-streaming-url"></a>Een streaming-URL samenstellen

Nu de [StreamingLocator](/rest/api/media/streaminglocators) is gemaakt, kunt u de streaming-URL's ophalen. Als u de URL wilt samenstellen, moet u de hostnaam van het [StreamingEndpoint](/rest/api/media/streamingendpoints) en het pad van de **streaminglocator** samenvoegen. In dit voorbeeld wordt het *standaard***streaming-eindpunt** gebruikt. Wanneer u voor het eerst een Media Service-account maakt, wordt dit *standaard***streaming-eindpunt** gestopt. Daarom moet u **Start** aanroepen.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetMPEGStreamingUrl)]

Wanneer u de app uitvoert, wordt het volgende scherm weergegeven:

![Beschermen met drm](./media/protect-with-drm/playready_encrypted_url.png)

U kunt een browser openen en de resulterende URL erin plakken om de demopagina van Azure Media Player te starten waarin de URL en token al zijn ingevuld.

## <a name="clean-up-resources-in-your-media-services-account"></a>Resources in uw Media Services-account opschonen

Over het algemeen moet u alles opschonen, behalve objecten die u van plan bent opnieuw te gebruiken (meestal gebruikt u transformaties, StreamingLocators) enzovoorts opnieuw. Als u wilt dat uw account na het experiment is opgeschoond, moet u de resources verwijderen die u niet van plan bent opnieuw te gebruiken. Met het volgende codevoorbeeld worden de taak, de gemaakte assets en het beleid voor inhoudssleutels verwijderd:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources van de resourcegroep niet meer nodig hebt, met inbegrip van de Media Services en opslagaccounts die u hebt gemaakt voor deze zelfstudie, verwijdert u de resourcegroep die u eerder hebt gemaakt.

Voer de volgende CLI-opdracht uit:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Widevine is een service van Google Inc. en is onderworpen aan de servicevoorwaarden en het privacybeleid van Google Inc.

## <a name="ask-questions-give-feedback-get-updates"></a>Vragen stellen, feedback geven, updates ophalen

Ga naar het artikel van de [Azure Media Services-community](media-services-community.md) voor verschillende manieren om vragen te stellen, feedback te geven en updates voor Media Services op te halen.

## <a name="next-steps"></a>Volgende stappen

Uitchecken

> [!div class="nextstepaction"]
> [Beveiligen met AES-128](protect-with-aes128.md)

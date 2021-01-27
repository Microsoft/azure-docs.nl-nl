---
# <a name="mandatory-fields-see-more-on-akamsskyeyemeta"></a>Verplichte velden. Meer informatie over aka.ms/skyeye/meta.
Titel: Ontwikkelen met v3 Api's: Azure Media Services beschrijving: meer informatie over regels die van toepassing zijn op entiteiten en Api's bij het ontwikkelen met Media Services v3. Services: Media Services documentationcenter: ' ' Auteur: IngridAtMicrosoft Manager: femila editor: ' '

MS. service: Media-Services MS. workload: MS. topic: conceptuele MS. date: 10/23/2020 MS. Author: inhenkel MS. Custom: seodec18

---

# <a name="develop-with-media-services-v3-apis"></a>Ontwikkelen met Media Services v3-API’s

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Als een ontwikkelaar kunt u de [REST-API](/rest/api/media/) van Media Services gebruiken, of clientbibliotheken waarmee u kunt communiceren met de REST-API, om eenvoudig aangepaste mediawerkstromen te maken, beheren en onderhouden. De [Media Services v3](https://aka.ms/ams-v3-rest-sdk) API is gebaseerd op de OpenAPI-specificatie (voorheen bekend als Swagger).

In dit artikel worden regels beschreven die van toepassing zijn op entiteiten en Api's bij het ontwikkelen met Media Services v3.

## <a name="accessing-the-azure-media-services-api"></a>Toegang tot de API van Azure Media Services

Om te worden geautoriseerd voor toegang tot Media Services-resources en de API van Media Services, moet u eerst worden geverifieerd. Media Services biedt ondersteuning voor [verificatie op basis van Azure AD (Azure Active Directory)](../../active-directory/fundamentals/active-directory-whatis.md). Twee algemene verificatieopties zijn:
 
* **Verificatie van service-principal**: Wordt gebruikt om een service te verifiëren (bijvoorbeeld: web-apps, functie-apps, logische apps, API en microservices). Toepassingen die meestal gebruikmaken van deze verificatiemethode, zijn apps waarmee daemonservices, services uit de middelste laag, of geplande taken worden uitgevoerd. Voor web-apps moet er bijvoorbeeld altijd een mid-tier zijn die verbinding maakt met Media Services met een service-principal.
* **Gebruikersverificatie**: Wordt gebruikt om een persoon te verifiëren die de app gebruikt om te communiceren met Media Services-resources. De interactieve app moet de gebruiker eerst vragen om zijn/haar referenties. Een voorbeeld is een beheerconsole-app die door geautoriseerde gebruikers wordt gebruikt om coderingstaken of livestreams te bewaken.

Voor de API van Media Services is vereist dat de gebruiker of app die de REST API-aanvragen doet, toegang heeft tot het Media Services-account en beschikt over de rol **Inzender** of **Eigenaar**. Toegang tot de API met de rol **Lezer** is ook mogelijk, maar dan zijn alleen **Get**- of **List**-bewerkingen beschikbaar.Zie voor meer informatie [Azure op rollen gebaseerd toegangs beheer (Azure RBAC) voor Media Services accounts](rbac-overview.md).

In plaats van een service-principal te maken, kunt u overwegen beheerde identiteiten te gebruiken voor Azure-resources, om via Azure Resource Manager toegang te krijgen tot de API van Media Services. Zie [Wat zijn beheerde identiteiten voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md) voor meer informatie over beheerde identiteiten voor Azure-resources.

### <a name="azure-ad-service-principal"></a>Service-Principal van Azure AD

De Azure AD-app en service-principal moeten zich in dezelfde Tenant bezien. Nadat u de app hebt gemaakt, geeft u de rol app **Inzender** of **eigenaar** toegang tot het Media Services-account.

Zie [vereiste machtigingen](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)als u niet zeker weet of u gemachtigd bent om een Azure AD-app te maken.

In de volgende afbeelding vertegenwoordigen de cijfers de stroom van de aanvragen in chronologische volg orde:

![App-verificatie op middelste laag met AAD vanuit een web-API](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. Een middelste laag-app vraagt een Azure AD-toegangs token met de volgende para meters:  

   * Azure AD-Tenant eindpunt.
   * Media Services resource-URI.
   * Resource-URI voor REST-Media Services.
   * Azure AD-App-waarden: de client-ID en het client geheim.

   Als u alle benodigde waarden wilt ophalen, raadpleegt u de [Access Azure Media Services-API](./access-api-howto.md).

2. Het Azure AD-toegangs token wordt verzonden naar de middelste laag.
4. De middelste laag verzendt een aanvraag naar de Azure media-REST API met het Azure AD-token.
5. De middelste laag krijgt een back-up van de gegevens van Media Services.

### <a name="samples"></a>Voorbeelden

Bekijk de volgende voor beelden die laten zien hoe u verbinding maakt met de Azure AD-Service-Principal:

* [Verbinding maken met REST](media-rest-apis-with-postman.md)  
* [Verbinding maken met Java](configure-connect-java-howto.md)
* [Verbinding maken met .NET](configure-connect-dotnet-howto.md)
* [Verbinding maken met Node.js](configure-connect-nodejs-howto.md)
* [Verbinding maken met Python](configure-connect-python-howto.md)

## <a name="naming-conventions"></a>Naamconventies

Namen van Azure Media Services v3-resources (bijvoorbeeld activa, taken, transformaties) zijn onderhevig aan de naamgevingsbeperkingen van Azure Resource Manager. In overeenstemming met Azure Resource Manager zijn de resourcenamen altijd uniek. U kunt dus alle unieke id-strings (bijvoorbeeld GUID's) gebruiken voor uw resourcenamen.

Media Services resource namen mogen niet de volgende tekens bevatten: ' < ', ' > ', '% ', ' & ', ': ', ' &#92; ', '? ', '/', ' * ', ' + ', '. ', het enkele aanhalings teken of de Stuur codes. Alle andere tekens zijn toegestaan. De maximale lengte van een resourcenaam is 260 tekens.

Zie [naamgevings vereisten](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) en [naam conventies](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)voor meer informatie over de naamgeving van Azure Resource Manager.

### <a name="names-of-filesblobs-within-an-asset"></a>Namen van bestanden/blobs in een Asset

De namen van bestanden/blobs in een Asset moeten de [vereisten voor de BLOB-naam](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) en de [NTFS-naam](/windows/win32/fileio/naming-a-file)volgen. De reden voor deze vereisten is dat de bestanden kunnen worden gekopieerd van Blob Storage naar een lokale NTFS-schijf voor verwerking.

## <a name="long-running-operations"></a>Langlopende bewerkingen

De bewerkingen die zijn gemarkeerd met `x-ms-long-running-operation` in de Azure Media Services [Swagger-bestanden](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) zijn langlopende bewerkingen. 

Zie [asynchrone bewerkingen](../../azure-resource-manager/management/async-operations.md)voor meer informatie over het bijhouden van asynchrone Azure-bewerkingen.

Media Services heeft de volgende langlopende bewerkingen:

* [Live-gebeurtenissen maken](/rest/api/media/liveevents/create)
* [Live-gebeurtenissen bijwerken](/rest/api/media/liveevents/update)
* [Live-gebeurtenis verwijderen](/rest/api/media/liveevents/delete)
* [Live-gebeurtenis starten](/rest/api/media/liveevents/start)
* [LiveEvent stoppen](/rest/api/media/liveevents/stop)

  Gebruik de `removeOutputsOnStop` para meter om alle gekoppelde live-uitvoer te verwijderen bij het stoppen van de gebeurtenis.  
* [LiveEvent opnieuw instellen](/rest/api/media/liveevents/reset)
* [LiveOutput maken](/rest/api/media/liveevents/create)
* [LiveOutput verwijderen](/rest/api/media/liveevents/delete)
* [StreamingEndpoint maken](/rest/api/media/streamingendpoints/create)
* [StreamingEndpoint bijwerken](/rest/api/media/streamingendpoints/update)
* [StreamingEndpoint verwijderen](/rest/api/media/streamingendpoints/delete)
* [StreamingEndpoint starten](/rest/api/media/streamingendpoints/start)
* [StreamingEndpoint stoppen](/rest/api/media/streamingendpoints/stop)
* [StreamingEndpoint schalen](/rest/api/media/streamingendpoints/scale)

Wanneer een lange bewerking is ingediend, ontvangt u een ' 201 gemaakt ' en moet er worden gecontroleerd of de bewerking is voltooid met de geretourneerde bewerkings-ID.

In het artikel [Asynchrone Azure-bewerkingen volgen](../../azure-resource-manager/management/async-operations.md) wordt uitvoerig beschreven hoe de status van asynchrone Azure-bewerkingen wordt gevolgd aan de hand van waarden die zijn geretourneerd in het antwoord.

Er wordt slechts één langlopende bewerking ondersteund voor een bepaalde live gebeurtenis of een van de bijbehorende live-uitvoer bewerkingen. Eenmaal gestart, moet een langlopende bewerking worden voltooid voordat een volgende langlopende bewerking wordt gestart op dezelfde LiveEvent of aan de gekoppelde live uitvoer. Voor Live-gebeurtenissen met meerdere Live outputs moet u wachten op het volt ooien van een langlopende bewerking op één live uitvoer voordat een langlopende bewerking wordt geactiveerd op een andere live uitvoer.

## <a name="sdks"></a>SDK's

> [!NOTE]
> De Azure Media Services v3 Sdk's zijn niet gegarandeerd thread-safe. Wanneer u een app met meerdere threads ontwikkelt, moet u uw eigen thread synchronisatie logica toevoegen om de client te beveiligen of een nieuw AzureMediaServicesClient-object per thread te gebruiken. Wees ook voorzichtig met het oplossen van problemen met meerdere threads die worden geïntroduceerd door de optionele objecten die door uw code worden verstrekt aan de client (zoals een httpclient maakt-exemplaar in .NET).

|SDK|Naslaginformatie|
|---|---|
|[.NET SDK](https://aka.ms/ams-v3-dotnet-sdk)|[.NET-ref](/dotnet/api/overview/azure/mediaservices/management)|
|[Java SDK](https://aka.ms/ams-v3-java-sdk)|[Java-ref](/java/api/overview/azure/mediaservices/management)|
|[Python SDK](https://aka.ms/ams-v3-python-sdk)|[Python-ref](/python/api/overview/azure/mediaservices/management)|
|[Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk) |[Node.js-ref](/javascript/api/overview/azure/mediaservices/management)| 
|[Go-SDK](https://aka.ms/ams-v3-go-sdk) |[Go-ref](https://aka.ms/ams-v3-go-ref)|
|[Ruby SDK](https://aka.ms/ams-v3-ruby-sdk)||

### <a name="see-also"></a>Zie ook

- [EventGrid .NET SDK die media service-gebeurtenissen bevat](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Definities van Media Services gebeurtenissen](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)

## <a name="azure-media-services-explorer"></a>Azure Media Services Explorer

[Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) is een hulpprogramma beschikbaar voor Windows-klanten die kennis willen maken met Media Services. AMSE is een Winforms/C#-toepassing voor het uploaden, downloaden, coderen en streamen van VOD en live-inhoud met Media Services. Het AMSE-hulpprogramma is voor clients die Media Services willen testen zonder code te schrijven. De AMSE-code wordt geleverd als een bron voor klanten die willen ontwikkelen met Media Services.

AMSE is een Open-Source-project, en dus wordt er ondersteuning geboden door de community (problemen kunnen worden gemeld aan https://github.com/Azure/Azure-Media-Services-Explorer/issues). Op dit project is de [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/) (Microsoft Open Source-gedragscode) van toepassing. Zie de [Veelgestelde vragen over de gedrags code](https://opensource.microsoft.com/codeofconduct/faq/) voor meer informatie of neem contact op opencode@microsoft.com met andere vragen of opmerkingen.

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Filteren, ordenen, paginering van Media Services entiteiten

Zie [filteren, ordenen, pagineren van Azure Media Services entiteiten](entities-overview.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Vragen stellen, feedback geven, updates ophalen

Ga naar het artikel van de [Azure Media Services-community](media-services-community.md) voor verschillende manieren om vragen te stellen, feedback te geven en updates voor Media Services op te halen.

## <a name="see-also"></a>Zie ook

Als u alle benodigde waarden wilt ophalen, raadpleegt u de [Access Azure Media Services-API](./access-api-howto.md).

## <a name="next-steps"></a>Volgende stappen

* [Verbinding maken met Media Services met Java](configure-connect-java-howto.md)
* [Verbinding maken met Media Services met .NET](configure-connect-dotnet-howto.md)
* [Verbinding maken met Media Services met Node.js](configure-connect-nodejs-howto.md)
* [Verbinding maken met Media Services met python](configure-connect-python-howto.md)
---
Titel: Azure Media Services v3-overzicht: Azure Media Services beschrijving: een overzicht op hoog niveau van Azure Media Services V3 met koppelingen naar Quick starts, zelf studies en code voorbeelden.
Services: Media Services documentationcenter: na Auteur: IngridAtMicrosoft Manager: femila editor: ' ' Tags: ' ' tref woorden: Azure Media Services, stream, broadcast, Live, offline

MS. service: Media-Services MS. devlang: meerdere MS. topic: overzicht ms.tgt_pltfrm: meerdere MS. workload: medium MS. date: 3/10/2021 MS. Author: inhenkel MS. Custom: MVC
#<a name="customer-intent-as-a-developer-or-a-content-provider-i-want-to-encode-stream-on-demand-or-live-analyze-my-media-content-so-that-my-customers-can-view-the-content-on-a-wide-variety-of-browsers-and-devices-gain-valuable-insights-from-recorded-content"></a>Klant intentie: als ontwikkelaar of een inhouds provider, ik wil coderen, streamen (op aanvraag of Live), mijn media-inhoud analyseren zodat mijn klanten het volgende kunnen doen: Bekijk de inhoud van een groot aantal browsers en apparaten, krijg waardevolle inzichten van opgenomen inhoud.
---

# <a name="azure-media-services-v3-overview"></a>Overzicht van Azure Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services is een cloudgebaseerd platform waarmee u oplossingen bouwt voor het creëren van videostreaming van broadcast-kwaliteit, het verbeteren van toegankelijkheid en distributie, het analyseren van inhoud en nog veel meer. Ongeacht of u een ontwikkelaar van apps, een callcenter, een overheidsinstelling of een entertainmentbedrijf bent, is Media Services het perfecte hulpmiddel bij het maken van apps die media-ervaringen van uitzonderlijke kwaliteit willen aanbieden aan grote doelgroepen, op alle populaire mobiele apparaten en in de meestgebruikte browsers van dit moment.

De Media Services v3-SDK's zijn gebaseerd op [Media Services v3 OpenAPI-specificatie (Swagger)](https://aka.ms/ams-v3-rest-sdk).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="compliance-privacy-and-security"></a>Compliance, privacy en beveiliging

Het is heel belangrijk dat u zich realiseert dat u zich bij het gebruik van Azure Media Services moet houden aan alle toepasselijke wetgeving en dat het niet is toegestaan Media Services of een Azure-service te gebruiken op een manier die de rechten van anderen schendt of anderen schade berokkent.

Voordat u een video/afbeelding naar Media Services uploadt, moet u over alle juiste rechten beschikken voor het gebruik van de video/afbeelding, inclusief, indien vereist door de wet, alle vereiste toestemmingen van personen (indien van toepassing) in de video/afbeelding, voor het gebruik, de verwerking en de opslag van hun gegevens in Media Services en Azure. Sommige jurisdicties kunnen speciale wettelijke vereisten opleggen voor het verzamelen, online verwerken en opslaan van bepaalde typen gegevens, zoals biometrische gegevens. Voordat u Media Services en Azure gebruikt voor het verwerken en opslaan van gegevens die onder bijzondere wettelijke vereisten vallen, moet u ervoor zorgen dat u voldoet aan de wettelijke vereisten die voor u van toepassing kunnen zijn.

Meer informatie over compliance, privacy en beveiliging in Media Services vindt u op [Vertrouwenscentrum](https://www.microsoft.com/trust-center/?rtc=1) van Microsoft. Als u meer wilt weten over de privacyverplichtingen en procedures voor gegevensverwerking en -retentie die Microsoft hanteert ten aanzien van uw gegevens, inclusief het verwijderen van uw gegevens, kunt u de [Privacyverklaring](https://privacy.microsoft.com/PrivacyStatement) van Microsoft, de [Voorwaarden voor Online Diensten](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) ('OST') en het [Addendum met betrekking tot gegevensverwerking](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ('DPA') raadplegen. Door Media Services te gebruiken, gaat u akkoord met de voorwaarden van de OST, DPA en de Privacyverklaring.
 
## <a name="what-can-i-do-with-media-services"></a>Wat kan ik doen met Media Services?

Met Media Services kunt u diverse mediawerkstromen in de cloud bouwen. Hier volgen enkele voorbeelden van wat u met Media Services kunt doen:

* Video's in verschillende indelingen aanbieden, zodat deze kunnen worden afgespeeld in een groot aantal browsers en op diverse apparaten. Voor zowel on demand als live streaming naar verschillende clients (mobiele apparaten, tv, pc, enzovoort) geldt dat de video- en audio-inhoud op de juiste manier moet worden gecodeerd en verpakt. Zie [Snelstart: Bestanden coderen en streamen](stream-files-dotnet-quickstart.md) om te lezen hoe u dergelijke inhoud kunt leveren en streamen.
* Live sportevenementen streamen naar een groot online publiek, zoals voetbalwedstrijden, schaatswedstrijden en grote kampioenschappen.
* Openbare vergaderingen en evenementen uitzenden, zoals gemeenteraadsvergaderingen, debatten of verkiezingen.
* Opgenomen videobeelden of audio-inhoud analyseren. Zo kunnen organisaties bijvoorbeeld de klanttevredenheid verbeteren door spraak-naar-tekst te extraheren en zoekindexen en dashboards samen te stellen. Vervolgens kunnen ze informatie over veelvoorkomende klachten, bronnen van klachten en andere relevante gegevens verzamelen.
* Een videoservice op abonnementsbasis maken en met DRM beveiligde inhoud streamen wanneer een klant (bijvoorbeeld een filmstudio) de toegang tot en het gebruik van bedrijfseigen, auteursrechtelijk beschermd werk moet beperken.
* Offline inhoud aanbieden voor afspelen in vliegtuigen, treinen en auto's. Klanten willen bijvoorbeeld inhoud downloaden naar hun telefoon of tablet, omdat ze weten dat ze later geen toegang hebben tot het netwerk en dan toch deze inhoud kunnen afspelen.
* Een videoplatform voor e-learning implementeren met Azure Media Services en [API's van Azure Cognitive Services](../../index.yml?pivot=products&panel=ai) voor spraak-naar-tekst ondertiteling, vertalen naar meerdere talen, enzovoort.
* Gebruik Azure Media Services in combinatie met [Azure Cognitive Services API's](../../index.yml?pivot=products&panel=ai) om ondertitels en bijschriften toe te voegen aan video's om een breder publiek te bereiken (bijvoorbeeld mensen met een gehoorbeperking of personen die de video in een andere taal willen volgen).
* Schakel Azure CDN in om grote schaalbaarheid te bieden, zodat piekbelastingen beter kunnen worden verwerkt (bijvoorbeeld bij een productlancering).

## <a name="how-can-i-get-started-with-v3"></a>Hoe ga ik aan de slag met v3?

Informatie over coderen en verpakken van content, video-streaming op aanvraag, live uitzenden en het analyseren van uw video's met Media Services v3. Zelfstudies, API-verwijzingen en andere documentatie laten zien hoe u veilig on-demand of live video- of audiostromen levert aan miljoenen gebruikers.

> [!TIP]
> Controleer het volgende voordat u begint met ontwikkelen: [Fundamentele concepten](concepts-overview.md), waaronder belangrijke concepten, zoals inpakken, coderen en beveiligen, en [Ontwikkelen met Media Services v3 API's](media-services-apis-overview.md), waaronder informatie over toegang tot API's, naamconventies, enzovoort.

### <a name="sdks"></a>SDK's

Beginnen met ontwikkelen met [SDK's voor Azure Media Services v3-clients](media-services-apis-overview.md#sdks).

### <a name="quickstarts"></a>Snelstartgidsen  

Deze quickstarts bevatten basisinstructies voor nieuwe klanten die Media Services snel willen uitproberen.

* [Videobestanden streamen - .NET](stream-files-dotnet-quickstart.md)
* [Videobestanden streamen - CLI](stream-files-cli-quickstart.md)
* [Videobestanden streamen - Node.js](stream-files-nodejs-quickstart.md)

### <a name="tutorials"></a>Zelfstudies

In de zelfstudies vindt u op scenario's gebaseerde procedures voor een aantal veelgebruikte Media Services-taken.

* [Externe bestanden coderen en video streamen - REST](stream-files-tutorial-with-rest.md)
* [Geüploade bestanden coderen en video streamen - .NET](stream-files-tutorial-with-api.md)
* [Live streamen - .NET](stream-live-tutorial-with-api.md)
* [Uw video analyseren - .NET](analyze-videos-tutorial.md)
* [Dynamische AES-128-versleuteling - .NET](drm-playready-license-template-concept.md)

### <a name="samples"></a>Voorbeelden

Gebruik [deze voorbeeldbrowser](/samples/browse/?products=azure-media-services) om Azure Media Services-codevoorbeelden te bekijken.

### <a name="how-to-guides"></a>Instructiegidsen

Instructiegidsen bevatten codevoorbeelden die laten zien hoe u taken moet uitvoeren. In deze sectie vindt u veel voorbeelden. Dit zijn er een paar:

* [Een account maken - CLI](./account-create-how-to.md)
* [Toegang tot API's - CLI](./access-api-howto.md)
* [Coderen met HTTPS als taakinvoer - .NET](job-input-from-http-how-to.md)  
* [Gebeurtenissen bewaken - Portal](monitoring/monitor-events-portal-how-to.md)
* [Dynamisch versleutelen met multi-DRM - .NET](drm-protect-with-drm-tutorial.md) 
* [Coderen met een aangepaste transformatie - CLI](transform-custom-preset-cli-how-to.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Vragen stellen, feedback geven, updates ophalen

Ga naar het artikel van de [Azure Media Services-community](media-services-community.md) voor verschillende manieren om vragen te stellen, feedback te geven en updates voor Media Services op te halen.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over basisconcepten](concepts-overview.md)

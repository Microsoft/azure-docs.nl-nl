---
title: Wat is de Speech-service?
titleSuffix: Azure Cognitive Services
description: De spraakservice combineert spraak-naar-tekst, tekst-naar-spraak en spraakomzetting in één Azure-abonnement. U kunt spraak eenvoudig aan uw toepassingen, hulpprogramma's en apparaten toevoegen met de Speech SDK, Speech Devices SDK of REST API's.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 11/23/2020
ms.author: trbye
ms.openlocfilehash: 291ac7804109b098b556e38c57ca493ba74f1b14
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95528887"
---
# <a name="what-is-the-speech-service"></a>Wat is de Speech-service?

De spraakservice combineert spraak-naar-tekst, tekst-naar-spraak en spraakomzetting in één Azure-abonnement. U kunt spraak eenvoudig inschakelen voor uw toepassingen, hulpprogramma's en apparaten met de [Speech CLI](spx-overview.md), [Speech SDK](./speech-sdk.md), [Speech Devices SDK](./speech-devices-sdk-quickstart.md?pivots=platform-android), [Speech Studio](https://speech.microsoft.com/) of [REST API's](#reference-docs).

> [!IMPORTANT]
> De spraakservice heeft de Bing Speech API en Translator Speech vervangen. Zie de sectie _Migratie_ voor migratie-instructies.

De volgende onderdelen maken deel uit van de spraakservice. Gebruik de koppelingen in deze tabel voor meer informatie over veelvoorkomende gebruikssituaties voor elke functie of blader door de API-naslaginformatie.

| Service | Functie | Beschrijving | SDK | REST |
|---------|---------|-------------|-----|------|
| [Spraak-naar-tekst](speech-to-text.md) | Spraak-naar-tekst in realtime | Bij spraak-naar-tekst worden audiostromen of lokale bestanden in realtime naar tekst getranscribeerd of vertaald, zodat uw toepassingen, hulpprogramma's of apparaten deze kunnen gebruiken of weergeven. Gebruik spraak-naar-tekst met [Language Understanding (LUIS)](../luis/index.yml) om gebruikersintentie af te leiden uit getranscribeerde spraak en om te reageren op spraakopdrachten. | [Ja](./speech-sdk.md) | [Ja](#reference-docs) |
| | [Spraak-naar-tekst batchgewijs verwerken](batch-transcription.md) | Met Spraak-naar-tekst batchgewijs verwerken kunt u asynchrone spraak-naar-tekst-transcriptie doorvoeren van grote volumes spraakgegevens die zijn opgeslagen in Azure Blob Storage. Met Spraak-naar-tekst batchgewijs verwerken kunt u niet alleen spraak omzetten naar tekst, maar ook sprekersindexering en sentimentanalyses uitvoeren. | Nee | [Ja](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) |
| | [Gesprek via meerdere apparaten](multi-device-conversation.md) | Meerdere apparaten of clients in een gesprek verbinden om spraak- of tekstberichten te verzenden, met eenvoudige ondersteuning voor transcriptie en vertaling| Ja | Nee |
| | [Gesprekstranscriptie](./conversation-transcription.md) | Hiermee schakelt u realtime spraakherkenning, sprekeridentificatie en sprekersindexering in. Het is ideaal voor het transcriberen van persoonlijke vergaderingen met de mogelijkheid om de sprekers te onderscheiden. | Ja | Nee |
| | [Aangepaste spraakmodellen maken](#customize-your-speech-experience) | Als u spraak-naar-tekst gebruikt voor herkenning en transcriptie in een unieke omgeving, kunt u aangepaste akoestische, taal- en uitspraakmodellen maken en trainen om te kunnen omgaan met omgevingslawaai of branchespecifieke woordenlijsten. | Nee | [Ja](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) |
| [Tekst naar spraak](text-to-speech.md) | Tekst naar spraak | Bij tekst-naar-spraak wordt invoertekst omgezet in menselijke spraak die is samengesteld met behulp van [SSML (Speech Synthesis Markup Language)](speech-synthesis-markup.md). Kies uit standaardstemmen en neurale stemmen (zie [Taalondersteuning](language-support.md)). | [Ja](./speech-sdk.md) | [Ja](#reference-docs) |
| | [Aangepaste stemmen maken](#customize-your-speech-experience) | Maak aangepaste spraakstijlen die uniek zijn voor uw merk of product. | Nee | [Ja](#reference-docs) |
| [Speech Translation](speech-translation.md) | Spraakomzetting | Spraakomzetting maakt realtime omzetting van spraak in meerdere talen mogelijk voor uw toepassingen, hulpprogramma's en apparaten. Gebruik deze service voor het omzetten van spraak-naar-spraak en spraak-naar-tekst. | [Ja](./speech-sdk.md) | Nee |
| [Spraakassistenten](voice-assistants.md) | Spraakassistenten | Spraakassistenten die gebruikmaken van de spraakservice stellen ontwikkelaars in staat om natuurlijke, menselijke gespreksinterfaces te maken voor hun toepassingen en gebruikstoepassingen. De spraakassistentservice biedt een snelle, betrouwbare interactie tussen een apparaat en een assistentimplementatie die gebruikmaakt van het Direct Line Speech-kanaal van het Bot Framework of de geïntegreerde service voor het uitvoeren van aangepaste opdrachten (preview) voor het uitvoeren van taken. | [Ja](voice-assistants.md) | Nee |
| [Speaker Recognition](speaker-recognition-overview.md) | Sprekercontrole en -identificatie | De Speaker Recognition-service biedt algoritmen die sprekers controleren en identificeren aan de hand van hun unieke stemkenmerken. Speaker Recognition wordt gebruikt voor het beantwoorden van de vraag 'Wie spreekt er?'. | Ja | [Ja](/rest/api/speakerrecognition/) |


[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

## <a name="try-the-speech-service-for-free"></a>Speech Service gratis uitproberen

Voor de volgende stappen hebt u zowel een Microsoft-account als een Azure-account nodig. Als u geen Microsoft-account hebt, kunt u zich gratis registreren bij het [Microsoft-accountportal](https://account.microsoft.com/account). Selecteer **Aanmelden met Microsoft** en klik als u wordt gevraagd om u aan te melden op **Een Microsoft-account maken**. Volg de stappen om uw nieuwe Microsoft-account te maken en te verifiëren.

Zodra u een Microsoft-account hebt, gaat u naar de [Azure-aanmeldingspagina](https://azure.microsoft.com/free/ai/), selecteert u **Gratis starten** en maakt u een nieuw Azure-account met behulp van een Microsoft-account. Hier volgt een video van hoe u zich kunt [aanmelden voor een gratis Azure-account](https://www.youtube.com/watch?v=GWT2R1C_uUU).

> [!NOTE]
> Wanneer u zich aanmeldt voor een gratis Azure-account, krijgt u dit met $ 200 aan servicetegoed dat u kunt toepassen op een betaald abonnement op de spraakservice, dat maximaal 30 dagen geldig is. Als uw tegoed aan het einde van de 30 dagen bijna op of verlopen is, worden uw Azure-services uitgeschakeld. Als u gebruik wilt blijven maken van Azure-services, moet u uw account upgraden. Zie [Uw gratis Azure-account upgraden](https://docs.microsoft.com/azure/cost-management-billing/manage/upgrade-azure-subscription) voor meer informatie. 
>
> De spraakservice heeft twee servicecategorieën: gratis (f0) en abonnement (s0), die verschillende beperkingen en voordelen hebben. Als u de gratis prijscategorie van de spraakservice met een laag volume kiest, kunt u dit gratis abonnement ook houden nadat uw gratis proefperiode of servicetegoed is verlopen. Zie [Prijzen van Cognitive Services - spraakservice](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) voor meer informatie.

### <a name="create-the-azure-resource"></a>De Azure-resource maken

Ga als volgt te werk om een spraakserviceresource (prijscategorie gratis of betaald) toe te voegen aan uw Azure-account:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/) met behulp van uw Microsoft-account.

1. Selecteer **Een resource maken** in de linkerbovenhoek van de portal. Als u **Een resource maken** niet ziet, kunt u dit altijd vinden door in de linkerbovenhoek van het scherm het samengevouwen menu te selecteren.

1. Typ 'spraak' in het zoekvak in het venster **Nieuw** en druk op Enter.

1. Selecteer **spraak** in de zoekresultaten.

   ![zoekresultaten voor spraak](media/index/speech-search.png)

1. Selecteer **Maken** en voer vervolgens de volgende handelingen uit:

   - Geef een unieke naam op voor uw nieuwe resource. De naam helpt u om onderscheid te maken tussen meerdere abonnementen die aan dezelfde service zijn gekoppeld.
   - Kies het Azure-abonnement waaraan de nieuwe resource wordt gekoppeld om te bepalen hoe de kosten worden gefactureerd. Hier vindt u de inleiding voor [het maken van een Azure-abonnement](https://docs.microsoft.com/azure/cost-management-billing/manage/create-subscription#create-a-subscription-in-the-azure-portal) in Azure Portal.
   - Kies de [regio](regions.md) waarin de resource wordt gebruikt. Azure is een wereldwijd cloudplatform dat algemeen beschikbaar is in veel regio's over de hele wereld. Als u de beste prestaties wilt, selecteert u een regio die zich het dichtst bij u bevindt of waar uw toepassing wordt uitgevoerd. De beschikbaarheid van de spraakservice kan per regio verschillen. Zorg ervoor dat u uw resource in een ondersteunde regio maakt. Zie de [ondersteuning per regio voor spraakservices](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-to-text-text-to-speech-and-translation).
   - Kies gratis (F0) of betaald (S0) als prijscategorie. Selecteer **Volledige prijsgegevens weergeven** voor volledige informatie over prijzen en quota voor gebruik voor elke prijscategorie of zie de [prijzen voor spraakservices](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/). Zie de [limieten voor Azure Cognitive Services](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cognitive-services-limits) voor meer informatie over resourcelimieten.
   - Maak een nieuwe resourcegroep voor dit spraakabonnement of wijs het abonnement toe aan een bestaande resourcegroep. Met resourcegroepen kunt u de verschillende Azure-abonnementen ordenen.
   - Selecteer **Maken**. Hiermee gaat u naar het implementatieoverzicht en worden berichten over de voortgang van de implementatie weergegeven.  
<!--
> [!NOTE]
> You can create an unlimited number of standard-tier subscriptions in one or multiple regions. However, you can create only one free-tier subscription. Model deployments on the free tier that remain unused for 7 days will be decommissioned automatically.
-->
Het kan even duren voordat u uw nieuwe spraakresource is geïmplementeerd. 

### <a name="find-keys-and-region"></a>Sleutels en regio zoeken

Voer de volgende stappen uit om de sleutels en regio van een voltooide implementatie te vinden:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/) met behulp van uw Microsoft-account.

2. Selecteer **Alle resources** en selecteer de naam van uw Cognitive Services-resource.

3. Selecteer in het linker deelvenster onder **RESOURCEBEHEER** de optie **Sleutels en Eindpunt**.

Elk abonnement heeft twee sleutels. U kunt beide sleutels gebruiken in uw toepassing. Als u een sleutel naar de code-editor of een andere locatie wilt kopiëren/plakken, selecteert u de kopieerknop naast elke sleutel en schakelt u over naar Windows om de inhoud van het klembord op de gewenste locatie te plakken.

Kopieer bovendien de `LOCATION`-waarde, uw regio-ID (bijvoorbeeld `westus`, `westeurope`) voor SDK-aanroepen.

> [!IMPORTANT]
> Deze abonnementssleutels worden gebruikt om toegang te krijgen tot de API van Cognitive Services. Deel uw sleutels niet. Sla ze veilig op, bijvoorbeeld met behulp van Azure Key Vault. We raden u ook aan om deze sleutels regelmatig opnieuw te genereren. Er is slechts één sleutel nodig om een API-aanroep te maken. Wanneer u de eerste sleutel opnieuw genereert, kunt u de tweede sleutel gebruiken voor verdere toegang tot de service.

## <a name="complete-a-quickstart"></a>Een quickstart volgen

We bieden quickstarts in de populairste programmeertalen, die zijn ontworpen om u de basisontwerppatronen te leren en waarmee u in minder dan tien minuten code kunt uitvoeren. Zie de volgende lijst voor de quickstart voor elke functie.

* [Quickstart voor spraak-naar-tekst](get-started-speech-to-text.md)
* [Quickstart voor tekst-naar-spraak](get-started-text-to-speech.md)
* [Quickstart over spraakomzetting](./get-started-speech-translation.md)
* [Quickstart over intentieherkenning](quickstarts/intent-recognition.md)
* [Quickstart over sprekerherkenning](./get-started-speaker-recognition.md)

Nu u een begin hebt gemaakt met de spraakservice, kunt u onze zelfstudies proberen waarin u leert om verschillende scenario's op te lossen.

- [Zelfstudie: Intenties van gesproken inhoud herkennen met de Speech SDK en LUIS, C#](how-to-recognize-intents-from-speech-csharp.md)
- [Zelfstudie: Spraak inschakelen voor uw bot met de Speech SDK, C#](tutorial-voice-enable-your-bot-speech-sdk.md)
- [Zelfstudie: Een Flask-app maken om tekst te vertalen, sentiment te analyseren en vertaalde tekst in spraak om te zetten](../translator/tutorial-build-flask-app-translation-synthesis.md?bc=%252fazure%252fcognitive-services%252fspeech-service%252fbreadcrumb%252ftoc.json%252c%252fen-us%252fazure%252fbread%252ftoc.json&toc=%252fazure%252fcognitive-services%252fspeech-service%252ftoc.json%252c%252fen-us%252fazure%252fcognitive-services%252fspeech-service%252ftoc.json)

## <a name="get-sample-code"></a>Voorbeeldcode ophalen

Voorbeeldcode is beschikbaar op GitHub voor de spraakservice. Deze voorbeelden hebben betrekking op veelvoorkomende scenario's, zoals het lezen van audio van een bestand of stream, continue en eenmalige herkenning en het werken met aangepaste modellen. Gebruik de volgende koppelingen om SDK- en REST-voorbeelden te bekijken:

- [Voorbeelden van spraak-naar-tekst, tekst-naar-spraak en spraakomzetting (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Voorbeelden van batchtranscriptie (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Voorbeelden van tekst-naar-spraak (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)
- [Voorbeelden van spraakassistenten (SDK)](https://aka.ms/csspeech/samples)

## <a name="customize-your-speech-experience"></a>Uw spraaktoepassing aanpassen

De spraakservice kan goed worden gebruikt met ingebouwde modellen, maar mogelijk wilt u de toepassing verder aanpassen en afstemmen op uw product of omgeving. Aanpassingsopties variëren van het afstemmen van akoestische modellen tot unieke spraakstijlen voor uw merk.

Andere producten bieden spraakmodellen die zijn afgestemd op specifieke doeleinden als gezondheidszorg of verzekeringen, maar zijn ook voor iedereen beschikbaar. Aanpassing in Azure Speech wordt onderdeel van *uw unieke* concurrentievoordeel dat niet beschikbaar is voor een andere gebruiker of klant. Met andere woorden, uw modellen zijn privé en aangepast, uitsluitend afgestemd op uw gebruikstoepassing.

| Speech Service | Platform | Beschrijving |
| -------------- | -------- | ----------- |
| Spraak naar tekst | [Aangepaste spraak](https://aka.ms/customspeech) | Pas de modellen voor spraakherkenning aan uw behoeften en beschikbare gegevens aan. Neem belemmeringen voor spraakherkenning weg, zoals spreekstijl, vocabulaire en achtergrondgeluiden. |
| Tekst naar spraak | [Aangepaste stem](https://aka.ms/customvoice) | Bouw een herkenbare, eenduidige stem voor uw tekst-naar-spraak-apps met uw beschikbare gesproken gegevens. U kunt de stemuitvoer verder verfijnen door een aantal stemparameters aan te passen. |

## <a name="deploy-on-premises-using-docker-containers"></a>On-premises implementeren met behulp van Docker-containers

[Gebruik Speech Service-containers](speech-container-howto.md) om API-functies on-premises te implementeren. Deze Docker-containers stellen u in staat om de service dichter bij uw gegevens te brengen voor naleving, beveiliging en andere operationele redenen. Speech Service biedt de volgende containers:

* Standaard spraak-naar-tekst
* Aangepaste spraak-naar-tekst
* Standaard tekst-naar-spraak
* Neurale tekst-naar-spraak
* Aangepaste tekst-naar-spraak (preview)
* Taaldetectie voor spraak (preview)

## <a name="reference-docs"></a>Naslagdocumentatie

- [Speech-SDK](./speech-sdk.md)
- [Speech Devices SDK](speech-devices-sdk.md)
- [REST API: Spraak-naar-tekst](rest-speech-to-text.md)
- [REST API: Tekst-naar-spraak](rest-text-to-speech.md)
- [REST API: Batchtranscriptie en aanpassing](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aan de slag met spraak-naar-tekst](./get-started-speech-to-text.md)
> [Aan de slag met tekst-naar-spraak](get-started-text-to-speech.md)
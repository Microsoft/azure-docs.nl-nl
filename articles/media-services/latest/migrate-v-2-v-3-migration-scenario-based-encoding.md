---
title: Richt lijnen voor het coderen van migratie
description: In dit artikel vindt u informatie over het coderen van scenario's die u helpen bij het migreren van Azure Media Services v2 naar v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/17/2021
ms.author: inhenkel
ms.openlocfilehash: 915fdcb059d9e7bf9e1853040b90b82a0457652e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104598402"
---
# <a name="encoding-scenario-based-migration-guidance"></a>Richt lijnen voor migratie op basis van een code ring

![logo van de migratie handleiding](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![migratie stappen 2](./media/migration-guide/steps-4.svg)

In dit artikel vindt u informatie over het coderen van scenario's die u helpen bij het migreren van Azure Media Services v2 naar v3.

## <a name="prerequisites"></a>Vereisten

Voordat u de werk stroom voor de code ring wijzigt, kunt u het beste inzicht krijgen in de verschillen in de manier waarop opslag wordt beheerd.  In AMS V3 wordt de Azure Storage-API gebruikt voor het beheren van de opslag account (s) die zijn gekoppeld aan uw Media Services-account.

> [!NOTE]
> Taken en taken die zijn gemaakt in v2, worden niet weer gegeven in v3, omdat ze niet zijn gekoppeld aan een trans formatie. De aanbeveling is om over te scha kelen naar v3-trans formaties en-taken.

## <a name="encoding-workflow-comparison"></a>Vergelijking van werk stroom coderen

Bekijk de onderstaande stroom diagrammen voor een visuele vergelijking van de coderings werk stromen voor v2 en v3.

### <a name="v2-encoding-workflow"></a>V2-coderings werk stroom

Klik op de onderstaande afbeelding om een grotere versie te zien.

[![Werk stroom voor code ring voor v2 ](./media/migration-guide/V2-pretty.svg)](./media/migration-guide/V2-pretty.svg#lightbox)

1. Instellen
    1. Maak een activum of gebruik en bestaande activa. Als u een nieuwe Asset gebruikt, uploadt u inhoud naar die Asset. Als u een bestaand activum gebruikt, moet u bestanden coderen die al bestaan in de Asset.
    2. Haal de waarden van de volgende items op:
        - Media processor-ID of-object
        - Coderings teken reeks (naam) van het coderings programma dat u wilt gebruiken
        - Activum-ID van nieuwe activum of de Asset-ID van de bestaande Asset
    3. Voor bewaking maakt u een taak-of taak niveau meldings abonnement of een SDK-gebeurtenis-handler
2. Maak de taak die de taak of taken bevat. Elke taak moet de bovenstaande items bevatten en:
    - Een instructie waarvoor een uitvoer activum moet worden gemaakt.  Het uitvoer activum wordt gemaakt door het systeem.
    - Optionele naam voor het uitvoer activum
3. Verzend de taak.
4. De taak bewaken.

### <a name="v3-encoding-workflow"></a>V3 encoding-werk stroom

[![Werk stroom voor het coderen van v3](./media/migration-guide/V3-pretty.svg)](./media/migration-guide/V3-pretty.svg#lightbox)

1. Instellen
    1. Maak een activum of gebruik een bestaande Asset. Als u een nieuwe Asset gebruikt, uploadt u inhoud naar die Asset. Als u een bestaand activum gebruikt, moet u bestanden coderen die al bestaan in de Asset. *Upload meer inhoud naar die Asset.*
    1. Een uitvoeractivum maken.  Het uitvoer activum is de locatie waar de gecodeerde bestanden en de invoer-en uitvoer meta gegevens worden opgeslagen.
    1. Waarden ophalen voor de trans formatie:
        - Standaard encoder vooraf
        - Resource groep AMS
        - AMS-account naam
    1. Maak de trans formatie of gebruik een bestaande trans formatie.  Trans formaties kunnen opnieuw worden gebruikt. Het is niet nodig om elke keer dat u een taak wilt indienen een nieuwe trans formatie te maken.
1. Een taak maken
    1. Haal voor de taak de waarden op voor de volgende items:
        - Transformatie naam
        - De basis-URI voor de SAS-URL voor uw asset, het HTTPs-bronpad van uw bestands share of het lokale pad van de bestanden. De `JobInputAsset` kan ook een Asset name gebruiken als invoer.
        - Bestands naam (s)
        - Uitvoer activa (s)
        - Een resourcegroep
        - AMS-account naam  
1. Gebruik [Event grid](monitoring/monitor-events-portal-how-to.md) voor het bewaken van uw taak.
1. Verzend de taak.

## <a name="custom-presets-from-v2-to-v3-encoding"></a>Aangepaste voor instellingen van v2 tot v3-code ring

Als uw v2-code de standaard Encoder met een aangepaste voor instelling heet, moet u eerst een nieuwe trans formatie maken met de aangepaste standaard encoder vooraf voordat u een taak verzendt.

Aangepaste voor instellingen zijn nu JSON en zijn niet meer gebaseerd op XML. Maak de voor instelling in JSON opnieuw volgens het aangepaste vooraf ingestelde schema zoals gedefinieerd in de hand leiding voor het [transformeren van open API (Swagger)](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/examples/transforms-create.json) .

## <a name="input-and-output-metadata-files-from-an-encoding-job"></a>Meta gegevensbestanden voor invoer en uitvoer van een coderings taak

In v2 worden XML-invoer-en uitvoer-meta gegevensbestanden gegenereerd als resultaat van een coderings taak. In v3 wordt de meta gegevens indeling gewijzigd van XML naar JSON. Zie meta gegevens over [invoer](input-metadata-schema.md) en [uitvoer](output-metadata-schema.md)van meta gegevens voor meer informatie over meta gegevens.

## <a name="premium-encoder-to-v3-standard-encoder-or-partner-based-solutions"></a>Premium encoder naar v3 Standard Encoder of oplossingen op basis van een partner

De v2 API biedt geen ondersteuning meer voor Premium encoder. Als u eerder de op werk stroom gebaseerde Premium Encoder voor HEVC-code ring hebt gebruikt, moet u migreren naar de nieuwe v3- [standaard encoder](media-encoder-standard-formats.md) met HEVC-coderings ondersteuning.

Als u de geavanceerde werk stroom functies van de Premium encoder nodig hebt, raden we u aan om aan de slag te gaan met het gebruik van een oplossing voor een geavanceerde coderings partner van Azure, zoals [communicatie](https://imaginecommunications.com), [Telestream](https://www.telestream.net)of [Bitmovin](https://bitmovin.com).

## <a name="jobs-with-inputs-that-are-on-https-hosted-urls"></a>Taken met invoer op door HTTPS gehoste Url's

U kunt nu taken in v3 verzenden van bestanden die zijn opgeslagen in azure Storage, lokaal opgeslagen of externe webservers met behulp van de [http (S) taak invoer ondersteuning](job-input-from-http-how-to.md).

Als u eerder werk stromen hebt gebruikt voor het kopiëren van bestanden van Azure Blob-bestanden naar lege assets voordat u taken indient, kunt u uw werk stroom vereenvoudigen door een SAS-URL voor het bestand in Azure Blob Storage rechtstreeks in de taak aan te geven.

## <a name="indexer-v1-audio-transcription-to-the-new-audioanalyzer-basic-mode"></a>Indexeer functie v1-audio-Transcriptie naar de nieuwe AudioAnalyzer ' Basic-modus '

Voor klanten die de indexer v1-processor in de v2 API gebruiken, moet u een trans formatie maken die de nieuwe `AudioAnalyzer` in de [basis modus](how-to-create-basic-audio-transform.md) aanroept voordat een taak wordt verzonden.

## <a name="encoding-transforms-and-jobs-concepts-tutorials-and-how-to-guides"></a>De concepten van code ring, trans formaties en taken, zelf studies en hand leidingen

### <a name="concepts"></a>Concepten

- [Video en audio coderen met Media Services](encoding-concept.md)
- [Standaard indelingen en-codecs voor encoders](media-encoder-standard-formats.md)
- [Versleutelen met een automatisch gegenereerde bitrate-ladder](autogen-bitrate-ladder.md)
- [De vooraf ingestelde coderings voorinstelling gebruiken om de optimale bitrate waarde voor een bepaalde oplossing te vinden](content-aware-encoding.md)
- [Gereserveerde media-eenheden](concept-media-reserved-units.md)
- [Invoermetagegevens](input-metadata-schema.md)
- [Uitvoermetagegevens](output-metadata-schema.md)
- [Dynamische verpakking in Media Services V3: audiocodecs](dynamic-packaging-overview.md#audio-codecs-supported-by-dynamic-packaging)

### <a name="tutorials"></a>Zelfstudies

- [Zelfstudie: Extern bestand coderen op basis van URL en video streamen - .NET](stream-files-dotnet-quickstart.md)
- [Zelfstudie: Video's uploaden, coderen en streamen met Media Services v3](stream-files-tutorial-with-api.md)

### <a name="how-to-guides"></a>Handleidingen

- [Een taak invoer maken op basis van een HTTPS-URL](job-input-from-http-how-to.md)
- [Een taak invoer maken op basis van een lokaal bestand](job-input-from-local-file-how-to.md)
- [Een eenvoudige audio transformatie maken](how-to-create-basic-audio-transform.md)
- Met .NET
  - [Coderen met een aangepaste trans formatie-.NET](customize-encoder-presets-how-to.md)
  - [Een overlay met Media Encoder Standard maken](how-to-create-overlay.md)
  - [Miniaturen genereren met behulp van encoder Standard met .NET](media-services-generate-thumbnails-dotnet.md)
- Met Azure CLI
  - [Coderen met een aangepaste trans formatie-Azure CLI](custom-preset-cli-howto.md)
- Met REST
  - [Coderen met een aangepaste transform-REST](custom-preset-rest-howto.md)
  - [Miniaturen genereren met behulp van coderings standaard met REST](media-services-generate-thumbnails-rest.md)
- [Een video afspelen tijdens het coderen met Media Services-.NET](subclip-video-dotnet-howto.md)
- [Een video bijsnijden bij het coderen met Media Services-REST](subclip-video-rest-howto.md)

## <a name="samples"></a>Voorbeelden

U kunt ook [de v2-en V3-code vergelijken in de code voorbeelden](migrate-v-2-v-3-migration-samples.md).

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]

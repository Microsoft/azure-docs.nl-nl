---
title: Swagger-documentatie-Speech-Service
titleSuffix: Azure Cognitive Services
description: De Swagger-documentatie kan worden gebruikt om automatisch Sdk's te genereren voor een aantal programmeer talen. Alle bewerkingen in onze service worden ondersteund door Swagger
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: reference
ms.date: 02/16/2021
ms.author: alexeyo
ms.openlocfilehash: d4369b66bacbe8cff4fc9712ffcd0cb5a370c439
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100636265"
---
# <a name="swagger-documentation"></a>Documentatie voor Swagger

Speech Service biedt een Swagger-specificatie voor interactie met een aantal REST-Api's die worden gebruikt voor het importeren van gegevens, het maken van modellen, het testen van de model nauwkeurigheid, het maken van aangepaste eind punten, het in de wachtrij plaatsen van batch-transcripties en het beheren van abonnementen. De meeste bewerkingen die beschikbaar zijn via [het Custom speech gebied van de speech Studio](https://aka.ms/customspeech) , kunnen via een programma worden voltooid met behulp van deze api's.

> [!NOTE]
> Speech Service heeft verschillende REST-Api's voor [spraak naar tekst](rest-speech-to-text.md) en [tekst naar spraak](rest-text-to-speech.md).  
>
> Er worden echter alleen [spraak-naar-tekst rest API v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) en v 2.0 beschreven in de Swagger-specificatie. Zie de documenten waarnaar wordt verwezen in de vorige alinea voor de informatie over alle andere speech Services REST-Api's.

## <a name="generating-code-from-the-swagger-specification"></a>Code genereren op basis van de Swagger-specificatie

De [Swagger-specificatie](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) bevat opties waarmee u snel kunt testen op verschillende paden. Soms is het echter wenselijk om code te genereren voor alle paden, waarbij u één bibliotheek met aanroepen maakt waarmee u toekomstige oplossingen op kunt baseren. Laten we eens kijken naar het proces voor het genereren van een python-bibliotheek.

U moet Swagger instellen op de regio van uw spraak bron. U kunt de regio bevestigen in het gedeelte **overzicht** van de instellingen voor spraak bronnen in azure Portal. De volledige lijst met ondersteunde regio's is [hier](regions.md#speech-to-text)beschikbaar.

1. Ga in een browser naar de Swagger-specificatie voor uw [regio](regions.md#speech-to-text):  
       `https://<your-region>.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0`
1. Klik op die pagina op **API-definitie** en klik op **Swagger**. Kopieer de URL van de pagina die wordt weer gegeven.
1. Ga in een nieuwe browser naar [https://editor.swagger.io](https://editor.swagger.io)
1. Klik op **bestand**, klik op **URL importeren**, plak de URL en klik op **OK**.
1. Klik op **client genereren** en selecteer **python**. De client bibliotheek wordt in een bestand gedownload naar uw computer `.zip` .
1. Haal alles uit de down load. U kunt gebruiken `tar -xf` om alles op te halen.
1. Installeer de uitgepakte module in uw python-omgeving:  
      `pip install path/to/package/python-client`
1. Het geïnstalleerde pakket heet `swagger_client` . Controleer of de installatie heeft gewerkt:  
       `python -c "import swagger_client"`

U kunt de python-bibliotheek gebruiken die u hebt gegenereerd met de [voor beelden van de speech-service op github](https://aka.ms/csspeech/samples).

## <a name="reference-documents"></a>Referentie documenten

* [Swagger: spraak-naar-tekst REST API v 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)
* [REST API voor spraak-naar-tekst](rest-speech-to-text.md)
* [REST API voor tekst-naar-spraak](rest-text-to-speech.md)

## <a name="next-steps"></a>Volgende stappen

* [Speech Service-voor beelden op github](https://aka.ms/csspeech/samples).
* [Verkrijg gratis een spraakserviceabonnementssleutel](overview.md#try-the-speech-service-for-free)

---
title: Migreren naar v3-Translator
titleSuffix: Azure Cognitive Services
description: Dit artikel bevat de stappen waarmee u kunt migreren van v2 naar v3 van de Azure Cognitive Services Translator.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: baaf907d2fafe23805e6b49582ecc7592ee05e0b
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98028230"
---
# <a name="translator-v2-to-v3-migration"></a>Migratie van Translator v2 naar v3

> [!NOTE]
> V2 is op 30 april 2018 afgeschaft. Migreer uw toepassingen naar v3 om te kunnen profiteren van de nieuwe functionaliteit die alleen beschikbaar is in v3. V2 zal worden ingetrokken op 24 mei 2021. 

Het micro soft Translator-team heeft versie 3 (v3) van het conversie programma uitgebracht. Deze release bevat nieuwe functies, afgeschafte methoden en een nieuwe indeling voor het verzenden van en het ontvangen van gegevens van de micro soft Translator-service. Dit document bevat informatie over het wijzigen van toepassingen voor het gebruik van v3. 

Het einde van dit document bevat nuttige koppelingen voor meer informatie.

## <a name="summary-of-features"></a>Overzicht van functies

* Er is geen traceer-in v3 No-Trace van toepassing op alle prijs categorieën in de Azure Portal. Deze functie houdt in dat er geen tekst wordt verzonden naar de V3 API, die wordt opgeslagen door micro soft.
* JSON-XML wordt vervangen door JSON. Alle gegevens die naar de service worden verzonden en die zijn ontvangen van de service, zijn in JSON-indeling.
* Meerdere doel talen in één aanvraag: de Vertaal methode accepteert meerdere ' aan ' talen voor vertaling in één aanvraag. Een enkele aanvraag kan bijvoorbeeld ' van ' Engels en ' naar ' Duits, Spaans en Japans of een andere groep talen zijn.
* Tweetalige woorden lijst-er is een tweetalige woordenlijst methode toegevoegd aan de API. Deze methode bevat ' Lookup ' en ' voor beelden '.
* Trans-transcrib: er is een methode voor trans-isatie toegevoegd aan de API. Met deze methode worden woorden en zinnen in één script geconverteerd (bijvoorbeeld Arabisch) in een ander script (bijvoorbeeld Latijn).
* Talen: een nieuwe methode ' talen ' levert taal informatie, in JSON-indeling, voor gebruik met de methoden ' vertalen ', ' dictionary ' en ' '.
* Nieuw in Vertaal: er zijn nieuwe mogelijkheden toegevoegd aan de Vertaal methode om enkele van de functies in de v2-API als afzonderlijke methoden te ondersteunen. Een voor beeld is TranslateArray.
* Methode speak: tekst-naar-spraak-functionaliteit wordt niet meer ondersteund in micro soft Translator. Tekst-naar-spraak-functionaliteit is beschikbaar in [micro soft Speech Service](../speech-service/text-to-speech.md).

In de volgende lijst met v2-en V3-methoden worden de V3-methoden en Api's geïdentificeerd waarmee de functionaliteit van v2 wordt geleverd.

| V2 API-methode   | Compatibiliteit met v3-API |
|:----------- |:-------------|
| `Translate`     | [Vertalen](reference/v3-0-translate.md)          |
| `TranslateArray`      | [Vertalen](reference/v3-0-translate.md)        |
| `GetLanguageNames`      | [Talen](reference/v3-0-languages.md)         |
| `GetLanguagesForTranslate`     | [Talen](reference/v3-0-languages.md)       |
| `GetLanguagesForSpeak`      | [Micro soft Speech-Service](../speech-service/language-support.md#text-to-speech)         |
| `Speak`     | [Micro soft Speech-Service](../speech-service/text-to-speech.md)          |
| `Detect`     | [Detecteren](reference/v3-0-detect.md)         |
| `DetectArray`     | [Detecteren](reference/v3-0-detect.md)         |
| `AddTranslation`     | De functie wordt niet meer ondersteund       |
| `AddTranslationArray`    | De functie wordt niet meer ondersteund          |
| `BreakSentences`      | [BreakSentence](reference/v3-0-break-sentence.md)       |
| `GetTranslations`      | De functie wordt niet meer ondersteund         |
| `GetTranslationsArray`      | De functie wordt niet meer ondersteund         |

## <a name="move-to-json-format"></a>Naar JSON-indeling verplaatsen

Micro soft Translator Translation v2 geaccepteerde en geretourneerde gegevens in XML-indeling. In v3 worden alle gegevens die zijn verzonden en ontvangen met behulp van de API in JSON-indeling. XML wordt niet meer geaccepteerd of geretourneerd in v3.

Deze wijziging is van invloed op verschillende aspecten van een toepassing die is geschreven voor de v2 Text Translator-API. Voor beeld: de talen-API retourneert taal informatie voor tekst omzetting, vele en de twee woordenlijst methoden. U kunt alle taal informatie voor alle methoden in één gesprek aanvragen of ze afzonderlijk aanvragen.

Voor de methode language is geen verificatie vereist; door op de volgende koppeling te klikken, ziet u alle taal informatie voor v3 in JSON:

[https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation, woorden lijst, vele](https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration)

## <a name="authentication-key"></a>Verificatiesleutel

De verificatie sleutel die u voor v2 gebruikt, wordt geaccepteerd voor v3. U hoeft geen nieuw abonnement te krijgen. U kunt v2 en V3 in uw apps tijdens de Yearlong-migratie periode combi neren, waardoor het eenvoudiger wordt om nieuwe versies vrij te geven terwijl u nog steeds migreert van v2-XML naar v3-JSON.

## <a name="pricing-model"></a>Prijs model

Micro soft Translator V3 heeft geprijsd op dezelfde manier als v2 heeft geprijsd. per teken, inclusief spaties. De nieuwe functies in v3 maken enkele wijzigingen in welke tekens worden geteld voor facturering.

| V3-methode   | Tekens die worden geteld voor facturering |
|:----------- |:-------------|
| `Languages`     | Geen tekens verzonden, geen geteld, geen kosten.          |
| `Translate`     | Aantal is gebaseerd op het aantal tekens dat voor vertaling wordt verzonden en hoeveel talen de tekens worden omgezet in. 50 tekens verzonden en vijf gevraagde talen worden 50x5.           |
| `Transliterate`     | Het aantal tekens dat voor vele wordt verzonden, wordt geteld.         |
| `Dictionary lookup & example`     | Het aantal tekens dat is verzonden voor het opzoeken van de dictionary en voor beelden worden geteld.         |
| `BreakSentence`     | Geen kosten.       |
| `Detect`     | Geen kosten.      |

## <a name="v3-end-points"></a>V3-eind punten

Globaal

* api.cognitive.microsofttranslator.com

## <a name="v3-api-text-translations-methods"></a>Methoden voor tekst vertalingen van v3 API

[`Languages`](reference/v3-0-languages.md)

[`Translate`](reference/v3-0-translate.md)

[`Transliterate`](reference/v3-0-transliterate.md)

[`BreakSentence`](reference/v3-0-break-sentence.md)

[`Detect`](reference/v3-0-detect.md)

[`Dictionary/lookup`](reference/v3-0-dictionary-lookup.md)

[`Dictionary/example`](reference/v3-0-dictionary-examples.md)

## <a name="compatibility-and-customization"></a>Compatibiliteit en aanpassing

> [!NOTE]
> 
> De micro soft Translator-hub wordt ingetrokken op 17 mei 2019. [Belang rijke migratie-informatie en-datums weer geven](https://www.microsoft.com/translator/business/hub/).   

Micro soft Translator V3 maakt standaard gebruik van Neural machine vertalingen. Dit kan niet worden gebruikt in combi natie met de micro soft Translator-hub. De Translator-hub ondersteunt alleen verouderde vertalingen van de statistische machine. Aanpassing voor Neural-vertaling is nu beschikbaar via het aangepaste conversie programma. [Meer informatie over het aanpassen van Neural machine translation](custom-translator/overview.md)

Neural-vertaling met de V3-tekst-API biedt geen ondersteuning voor het gebruik van standaard categorieën (SMT, Speech, Tech, generalnn).

| Versie | Eindpunt | Naleving van AVG-processor | Translator hub gebruiken | Aangepaste Translator gebruiken (preview-versie) |
| :------ | :------- | :------------------------ | :----------------- | :------------------------------ |
|Translator versie 2|    api.microsofttranslator.com|    Nee    |Ja    |Nee|
|Translator versie 3|    api.cognitive.microsofttranslator.com|    Ja|    Nee|    Ja|

**Translator versie 3**
* Is algemeen beschikbaar en volledig ondersteund.
* Is AVG compatibel als een processor en voldoet aan alle ISO 20001 en 20018 en aan SOC 3-certificerings vereisten. 
* Met kunt u de Neural-netwerk Vertaal systemen aanroepen die u hebt aangepast met aangepaste Translator (preview), de nieuwe functie Translator NMT Customization. 
* Biedt geen toegang tot aangepaste Vertaal systemen die zijn gemaakt met behulp van de micro soft Translator-hub.

U gebruikt versie 3 van het conversie programma als u het api.cognitive.microsofttranslator.com-eind punt gebruikt.

**Translator versie 2**
* Voldoet niet aan alle vereisten voor ISO 20001, 20018 en SOC 3-certificering. 
* Biedt geen toestemming voor het aanroepen van de Neural-netwerk Vertaal systemen die u hebt aangepast met de functie voor het aanpassen van het conversie programma.
* Biedt toegang tot aangepaste Vertaal systemen die zijn gemaakt met behulp van de micro soft Translator-hub.
* U gebruikt versie 2 van het conversie programma als u het api.microsofttranslator.com-eind punt gebruikt.

Er wordt geen versie van de vertaler gemaakt om een record van uw vertalingen te maken. Uw vertalingen worden nooit met iedereen gedeeld. Meer informatie op de webpagina [Translator No-Trace](https://www.aka.ms/NoTrace) .

## <a name="links"></a>Koppelingen

* [Microsoft Privacy Policy](https://privacy.microsoft.com/privacystatement)
* [Juridische informatie Microsoft Azure](https://azure.microsoft.com/support/legal)
* [Voorwaarden voor Online Diensten](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Documentatie voor V 3.0 weer geven](reference/v3-0-reference.md)

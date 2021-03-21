---
title: Microsoft Translator-service
titlesuffix: Azure Cognitive Services
description: Integreer Translator in uw toepassingen, websites, hulpprogramma's en andere oplossingen om gebruikerservaringen in meerdere talen te bieden.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.topic: overview
ms.subservice: translator-text
ms.date: 03/15/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: translator, tekstvertaling, machine translation, vertaalservice
ms.openlocfilehash: ec76aa7554110b7440eb825f2d5e86ae2da6baa2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104657719"
---
# <a name="what-is-the-translator-service"></a>Wat is de Translator-service?

Translator is een cloudservice voor automatische vertaling en maakt deel uit van de [Azure Cognitive Services](../../index.yml?panel=ai&pivot=products)-familie van cognitieve API's voor het bouwen van intelligente apps. Translator is eenvoudig in uw toepassingen, websites, hulpprogramma's en oplossingen te integreren. Hiermee kunt u meertalige gebruikers ervaringen toevoegen in [90 talen en dialecten](./language-support.md) en kunnen worden gebruikt voor tekst omzetting met elk besturings systeem.

Deze documentatie bevat de volgende artikel typen:  

* [**Quick**](quickstart-translator.md) starts zijn aan de slag-instructies die u helpen bij het maken van aanvragen voor de service.  
* [**Hand leidingen**](translator-how-to-signup.md) bevatten instructies voor het gebruik van de service op meer specifieke of aangepaste manieren.  
* [**Concepten**](character-counts.md) geven uitgebreide uitleg over de service functionaliteit en-functies.  
* [**Zelf studies**](tutorial-wpf-translation-csharp.md) zijn meer gidsen die laten zien hoe u de service kunt gebruiken als onderdeel in bredere zakelijke oplossingen.  


## <a name="about-microsoft-translator"></a>Over Microsoft Translator

Translator voorziet in veel micro soft-producten en-services en wordt gebruikt door duizenden bedrijven wereld wijd in hun toepassingen en werk stromen.

Spraakomzetting, mogelijk gemaakt door Translator, is ook beschikbaar via [Azure Speech Service](../speech-service/index.yml). In spraakomzetting worden functionaliteit van de Translator Speech-API en de Custom Speech Service gecombineerd tot een geïntegreerde en volledig aan te passen service. 

## <a name="language-support"></a>Taalondersteuning

Translator biedt meertalige ondersteuning voor tekstvertalingen, transliteraties, taaldetectie en woordenlijsten. Zie [taalondersteuning](language-support.md) voor een volledige lijst of open de lijst programmatisch met de [REST API](./reference/v3-0-languages.md).  

## <a name="microsoft-translator-neural-machine-translation"></a>Neurale machinevertalingen van Microsoft Translator

Neurale machinevertalingen (NMT) is de nieuwe standaard voor AI-machinevertalingen van hoge kwaliteit. Dit vervangt de verouderde technologie voor statistische machinevertaling waarvan de kwaliteit niet meer is verbeterd sinds het midden van de jaren 10.

NMT biedt betere vertalingen dan SMT, niet alleen op basis van de kwaliteitsscores van onbewerkte vertalingen, maar ook omdat deze soepeler en menselijker klinken. De belangrijkste reden hiervoor is dat NMT gebruikmaakt van de volledige context van een zin bij het vertalen van woorden. Bij SMT werd alleen gekeken naar de directe context van een paar woorden voor en na elk woord.

NMT-modellen vormen de kern van de API en zijn niet zichtbaar voor eindgebruikers. Het enige merkbare verschil is de verbeterde vertaalkwaliteit, met name voor talen zoals Chinees, Japans en Arabisch.

Meer informatie over [de werking van NMT](https://www.microsoft.com/en-us/translator/mt.aspx#nnt).

## <a name="improve-translations-with-custom-translator"></a>Vertalingen verbeteren met Custom Translator

 [Aangepaste Translator](customization.md), een uitbrei ding van de Translator-service, kan worden gebruikt om het Neural-Vertaal systeem aan te passen en de vertaling te verbeteren voor uw specifieke terminologie en stijl.

Met Custom Translator kunt u Vertaal systemen maken voor het verwerken van de terminologie die wordt gebruikt in uw eigen bedrijf of branche. Uw aangepaste Vertaal systeem kan eenvoudig worden geïntegreerd met uw bestaande toepassingen, werk stromen, websites en apparaten, via de reguliere vertaler, met behulp van de para meter Category.

## <a name="next-steps"></a>Volgende stappen

- [Maak een Translator-service](./translator-how-to-signup.md) om uw toegangs sleutels en eind punt op te halen.
- Gebruik de [Quickstart](quickstart-translator.md) om snel de Translator-service aan te roepen.
- [API-naslag](./reference/v3-0-reference.md) bevat de technische documentatie voor de API's.
- [Prijsdetails](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)

---
title: Microsoft Translator-service
titlesuffix: Azure Cognitive Services
description: Integreer Translator in uw toepassingen, websites, hulpprogramma's en andere oplossingen om gebruikerservaringen in meerdere talen te bieden.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: overview
ms.date: 09/11/2020
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: translator, tekstvertaling, machine translation, vertaalservice
ms.openlocfilehash: 37f0d64879493abcf3e5b9448068b86b7a36dccb
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896694"
---
# <a name="what-is-the-translator-service"></a>Wat is de Translator-service?

Translator is een cloudservice voor automatische vertaling en maakt deel uit van de [Azure Cognitive Services](../../index.yml?panel=ai&pivot=products)-familie van cognitieve API's voor het bouwen van intelligente apps. Translator is eenvoudig in uw toepassingen, websites, hulpprogramma's en oplossingen te integreren. U kunt hiermee meertalige gebruikerservaringen in [meer dan zeventig talen](./language-support.md) toevoegen en u kunt het gebruiken op elk hardwareplatform onder elk willekeurig besturingssysteem voor vertalingen van tekst.

## <a name="about-microsoft-translator"></a>Over Microsoft Translator

Translator stuurt een aantal Microsoft-producten en -services aan en wordt door duizenden bedrijven over de hele wereld gebruikt in hun toepassingen en werkstromen, zodat hun inhoud beschikbaar wordt gemaakt voor een wereldwijd publiek.

Spraakomzetting, mogelijk gemaakt door Translator, is ook beschikbaar via [Azure Speech Service](../speech-service/index.yml). In spraakomzetting worden functionaliteit van de Translator Speech-API en de Custom Speech Service gecombineerd tot een geïntegreerde en volledig aan te passen service. 

## <a name="language-support"></a>Taalondersteuning

Translator biedt meertalige ondersteuning voor tekstvertalingen, transliteraties, taaldetectie en woordenlijsten. Zie [taalondersteuning](language-support.md) voor een volledige lijst of open de lijst programmatisch met de [REST API](./reference/v3-0-languages.md).  

## <a name="microsoft-translator-neural-machine-translation"></a>Neurale machinevertalingen van Microsoft Translator

Neurale machinevertalingen (NMT) is de nieuwe standaard voor AI-machinevertalingen van hoge kwaliteit. Dit vervangt de verouderde technologie voor statistische machinevertaling waarvan de kwaliteit niet meer is verbeterd sinds het midden van de jaren 10.

NMT biedt betere vertalingen dan SMT, niet alleen op basis van de kwaliteitsscores van onbewerkte vertalingen, maar ook omdat deze soepeler en menselijker klinken. De belangrijkste reden hiervoor is dat NMT gebruikmaakt van de volledige context van een zin bij het vertalen van woorden. Bij SMT werd alleen gekeken naar de directe context van een paar woorden voor en na elk woord.

NMT-modellen vormen de kern van de API en zijn niet zichtbaar voor eindgebruikers. Het enige merkbare verschil is de verbeterde vertaalkwaliteit, met name voor talen zoals Chinees, Japans en Arabisch.

Meer informatie over [de werking van NMT](https://www.microsoft.com/en-us/translator/mt.aspx#nnt).

## <a name="improve-translations-with-custom-translator"></a>Vertalingen verbeteren met Custom Translator

Custom Translator is een uitbreiding op de Translator-service en kan samen met Translator worden gebruikt om het neurale vertaalsysteem aan te passen en de vertalingen te verbeteren voor uw specifieke terminologie en stijl.

Met Custom Translator kunt u vertaalsystemen maken waarin de terminologie wordt verwerkt die wordt gebruikt in uw bedrijf of sector. U kunt uw aangepaste vertaalsysteem vervolgens eenvoudig integreren in uw bestaande toepassingen, werkstromen en websites voor meerdere typen apparaten via de gewone Translator, met behulp van de categorieparameter.

Meer informatie over [Custom Translator](customization.md).

## <a name="next-steps"></a>Volgende stappen

- [Registreer](./translator-how-to-signup.md) u voor een toegangssleutel.
- Gebruik de [Quickstart](quickstart-translator.md) om snel de Translator-service aan te roepen.
- [API-naslag](./reference/v3-0-reference.md) bevat de technische documentatie voor de API's.
- [Prijsdetails](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
---
title: Wat is Insluitende lezer?
titleSuffix: Azure Cognitive Services
description: Insluitende lezer is een hulpprogramma dat is ontworpen om gebruikers met leerproblemen te helpen of om nieuwe lezers en personen die een taal willen leren, taalbegrip bij te brengen.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: overview
ms.date: 01/4/2020
ms.author: metang
ms.custom: cog-serv-seo-aug-2020
keywords: lezers, personen die een taal willen leren, afbeeldingen weergeven, lezen verbeteren, inhoud lezen, vertalen
ms.openlocfilehash: 1911d3ce62434f1dc24966798e9ace259ae072bc
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801567"
---
# <a name="what-is-immersive-reader"></a>Wat is Insluitende lezer?

[Insluitende lezer](https://www.onenote.com/learningtools) is een exclusief ontworpen hulpprogramma dat bewezen technieken implementeert om begrijpend lezen te verbeteren voor beginnende lezers, personen die een taal willen leren en mensen met leerproblemen, zoals dyslexie. Met de clientbibliotheek van Insluitende lezer kunt u gebruikmaken van dezelfde technologie die wordt gebruikt in Microsoft Word en Microsoft OneNote om uw webtoepassingen te verbeteren. 

Deze documentatie bevat de volgende typen artikelen:  

* **[Quick](quickstarts/client-libraries.md)** starts zijn aan de slag-instructies die u helpen bij het maken van aanvragen voor de service.
* **[Hand leidingen](how-to-create-immersive-reader.md)** bevatten instructies voor het gebruik van de service op meer specifieke of aangepaste manieren.

## <a name="use-immersive-reader-to-improve-reading-accessibility"></a>Insluitende lezer gebruiken om de leestoegang te verbeteren 

Insluitende lezer is ontworpen om lezen eenvoudiger en toegankelijker te maken voor iedereen. Laten we eens kijken naar enkele van de belangrijkste functies van Insluitende lezer.

### <a name="isolate-content-for-improved-readability"></a>Inhoud isoleren voor verbeterde leesbaarheid

Insluitende lezer isoleert inhoud om de leesbaarheid te verbeteren. 

  ![Inhoud isoleren voor verbeterde leesbaarheid met Insluitende lezer](./media/immersive-reader.png)

### <a name="display-pictures-for-common-words"></a>Afbeeldingen voor veelgebruikte woorden weergeven

Voor veelgebruikte termen wordt in Insluitende lezer een afbeelding weergegeven.

  ![Afbeeldingenlijst bij Insluitende lezer](./media/picture-dictionary.png)

### <a name="highlight-parts-of-speech"></a>Delen van de spraak benadrukken

Insluitende lezer kan worden gebruikt om lezers bepaalde onderdelen van spraak en grammatica te laten begrijpen door werkwoorden, zelfstandige naamwoorden, voornaamwoorden en nog veel meer te benadrukken.

  ![Delen van spraak weergeven met Insluitende lezer](./media/parts-of-speech.png)

### <a name="read-content-aloud"></a>Inhoud hardop lezen

Spraaksynthese (of tekst-naar-spraak) is geïntegreerd in de service Insluitende lezer. Hiermee kunnen uw lezers tekst selecteren die hardop moet worden voorgelezen. 

  ![Tekst hardop voorlezen met Insluitende lezer](./media/read-aloud.png)

### <a name="translate-content-in-real-time"></a>Inhoud in realtime vertalen

Insluitende lezer kan tekst in vele talen in realtime vertalen. Dit is handig voor het verbeteren van het leesbegrip van lezers die een nieuwe taal leren.

  ![Tekst vertalen met Insluitende lezer](./media/translation.png)

### <a name="split-words-into-syllables"></a>Woorden in lettergrepen splitsen

Met Insluitende lezer kunt u woorden in lettergrepen splitsen om de leesbaarheid te verbeteren of nieuwe woorden duidelijker te laten klinken.

  ![Woorden in lettergrepen splitsen met Insluitende lezer](./media/syllabification.png)

## <a name="how-does-immersive-reader-work"></a>Hoe werkt Insluitende lezer?

Insluitende lezer is een zelfstandige webtoepassing. Wanneer u de clientbibliotheek van Insluitende lezer aanroept, wordt deze in een afzonderlijk `iframe` buiten de bestaande webtoepassing weergegeven. Wanneer uw webtoepassing de service Insluitende lezer aanroept, bepaalt u welke inhoud aan de lezer moet worden getoond. De clientbibliotheek van Insluitende lezer verwerkt het maken en opmaken van het `iframe` en de communicatie met de back-endservice van Insluitende lezer. De service Insluitende lezer verwerkt de inhoud voor delen van spraak, tekst-naar-spraak, vertaling en meer.

## <a name="get-started-with-immersive-reader"></a>Aan de slag met Insluitende lezer

De clientbibliotheek van Insluitende lezer is beschikbaar in C#, JavaScript, Java (Android), Kotlin (Android) en Swift (iOS). Ga aan de slag met:

* [Quickstart: Gebruik de clientbibliotheek voor de Insluitende lezer](quickstarts/client-libraries.md)

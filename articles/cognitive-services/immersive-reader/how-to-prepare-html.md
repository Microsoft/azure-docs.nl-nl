---
title: HTML-inhoud voorbereiden voor insluitende lezer
titleSuffix: Azure Cognitive Services
description: Meer informatie over het starten van de insluitende lezer met behulp van HTML, java script, Python, Android of iOS. Insluitende lezer gebruikt bewezen technieken om de Lees vaardigheid te verbeteren voor taal kennis, opkomende lezers en studenten met meer informatie.
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: erhopf
ms.openlocfilehash: 40aab9510eb90c405f92be49a3b4c0a3f756c872
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102620068"
---
# <a name="how-to-prepare-html-content-for-immersive-reader"></a>HTML-inhoud voorbereiden voor insluitende lezer

Dit artikel laat u zien hoe u uw HTML structureert en de inhoud ophaalt, zodat deze kan worden gebruikt door insluitende lezer.

## <a name="prepare-the-html-content"></a>De HTML-inhoud voorbereiden

Plaats de inhoud die u wilt weer geven in de insluitende lezer binnen een container element. Zorg ervoor dat het container element een unieke naam heeft `id` . De insluitende lezer biedt ondersteuning voor eenvoudige HTML-elementen. Zie de [referentie](reference.md#html-support) voor meer informatie.

```html
<div id='immersive-reader-content'>
    <b>Bold</b>
    <i>Italic</i>
    <u>Underline</u>
    <strike>Strikethrough</strike>
    <code>Code</code>
    <sup>Superscript</sup>
    <sub>Subscript</sub>
    <ul><li>Unordered lists</li></ul>
    <ol><li>Ordered lists</li></ol>
</div>
```

## <a name="get-the-html-content-in-javascript"></a>De HTML-inhoud in Java script ophalen

Gebruik het `id` element van de container om de HTML-inhoud in uw Java script-code op te halen.

```javascript
const htmlContent = document.getElementById('immersive-reader-content').innerHTML;
```

## <a name="launch-the-immersive-reader-with-your-html-content"></a>De insluitende lezer starten met uw HTML-inhoud

Wanneer u aanroept `ImmersiveReader.launchAsync` , stelt u de eigenschap van het segment `mimeType` in op `text/html` om rendering van HTML in te scha kelen.

```javascript
const data = {
    chunks: [{
        content: htmlContent,
        mimeType: 'text/html'
    }]
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, data, YOUR_OPTIONS);
```

## <a name="next-steps"></a>Volgende stappen

* Lees de [naslagdocumentatie voor de Immersive Reader-SDK](reference.md).
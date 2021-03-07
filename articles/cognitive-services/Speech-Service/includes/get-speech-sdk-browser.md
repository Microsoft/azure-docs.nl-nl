---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.custom: devx-track-js
ms.openlocfilehash: 252f2e6275b01522b83e846201d190b39d2bbf21
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102434450"
---
:::row:::
    :::column span="3":::
        De Speech SDK voor Java script is beschikbaar als een NPM-pakket, Zie <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">micro soft-cognitiveservices-Speech-SDK </a> en de bijbehorende Companion github repository <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">cognitieve-Services-Speech-SDK-js </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="JavaScript" src="https://docs.microsoft.com/media/logos/logo_js.svg"  width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Hoewel de Speech SDK voor Java script beschikbaar is als een NPM-pakket, kunnen webbrowsers en Node.js van de client dit gebruiken. Houd rekening met de verschillende architectuur implicaties van elke omgeving. Zo is het <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">Document object model (DOM) </a> niet beschikbaar voor server toepassingen, net zoals het <a href="https://nodejs.org/api/fs.html" target="_blank">Bestands systeem </a> niet beschikbaar is voor toepassingen op de client.

### <a name="nodejs-package-manager-npm"></a>Pakket beheer Node.js (NPM)

Voer de volgende opdracht uit om de Speech SDK voor Java script te installeren `npm install` .

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

### <a name="html-script-tag"></a>HTML-script code

U kunt ook rechtstreeks een `<script>` tag in het HTML- `<head>` element toevoegen, afhankelijk van de <a href="https://www.jsdelivr.com/package/npm/microsoft-cognitiveservices-speech-sdk" target="_blank"> **JSDelivr** NPM Syndicate</a>.

```html
<script src="https://cdn.jsdelivr.net/npm/microsoft-cognitiveservices-speech-sdk@latest/distrib/browser/microsoft.cognitiveservices.speech.sdk.bundle-min.js">
</script>
```

Zie de Quick start voor de <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser" target="_blank">webbrowser Speech SDK </a>voor meer informatie.

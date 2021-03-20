---
title: Wiskundige formules weer geven in de insluitende lezer
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt uitgelegd hoe u wiskundige berekeningen in de insluitende lezer kunt weer geven.
author: nitinme
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: nitinme
ms.custom: devx-track-js
ms.openlocfilehash: 75b748604e8faa502970ac71e3fec4fae2774c19
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91334511"
---
# <a name="how-to-display-math-in-the-immersive-reader"></a>Wiskunde weer geven in de insluitende lezer

De insluitende lezer kan wiskundige formules weer geven wanneer deze zijn opgenomen in de vorm van de wiskundige Markup Language ([MathML](https://developer.mozilla.org/docs/Web/MathML)).
Het MIME-type kan worden ingesteld via het [segment](../reference.md#chunk)van de insluitende lezer. Zie [ondersteunde MIME-typen](../reference.md#supported-mime-types) voor meer informatie.

## <a name="send-math-to-the-immersive-reader"></a>Wiskunde naar de insluitende lezer verzenden
Als u wiskunde wilt verzenden naar de insluitende lezer, geeft u een segment met MathML op en stelt u het MIME-type in op ```application/mathml+xml``` ;

Als uw inhoud bijvoorbeeld het volgende was:

```html
<div id='ir-content'>
    <math xmlns='http://www.w3.org/1998/Math/MathML'>
        <mfrac>
            <mrow>
                <msup>
                    <mi>x</mi>
                    <mn>2</mn>
                </msup>
                <mo>+</mo>
                <mn>3</mn>
                <mi>x</mi>
                <mo>+</mo>
                <mn>2</mn>
            </mrow>
            <mrow>
                <mi>x</mi>
                <mo>−</mo>
                <mn>3</mn>
            </mrow>
        </mfrac>
        <mo>=</mo>
        <mn>4</mn>
    </math>
</div>
```

Vervolgens kunt u uw inhoud weer geven met behulp van de volgende Java script.

```javascript
const data = {
    title: 'My Math',
    chunks: [{
        content: document.getElementById('ir-content').innerHTML.trim(),
        mimeType: 'application/mathml+xml'
    }]
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, data, YOUR_OPTIONS);
```

Wanneer u de insluitende lezer start, ziet u het volgende:

![Wiskunde in insluitende lezer](../media/how-tos/1-math.png)

## <a name="next-steps"></a>Volgende stappen

* De [SDK voor Insluitende lezer](https://github.com/microsoft/immersive-reader-sdk) en de [naslaginformatie voor de SDK voor Insluitende lezer](../reference.md) verkennen
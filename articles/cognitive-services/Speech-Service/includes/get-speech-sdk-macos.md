---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: e7714dcc3e14e3659ca1943507d55d20dfb28fa6
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99213988"
---
Bij het ontwikkelen voor macOS zijn er drie spraak-Sdk's beschikbaar.

- De SDK van de doel-C spraak is standaard beschikbaar als een CocoaPod-pakket
- De .NET Speech SDK kan worden gebruikt met **Xamarin. Mac** bij het implementeren van .net Standard 2,0
- De python Speech SDK is beschikbaar als een PyPI-module

> [!TIP]
> Zie voor meer informatie met behulp van de hand van de doel-C Speech SDK met SWIFT, <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">objectief-c importeren in SWIFT <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.

### <a name="system-requirements"></a>Systeemvereisten

- Een macOS-versie 10,13 of hoger

# <a name="xcode"></a>[Xcode](#tab/mac-xcode)

:::row:::
    :::column span="3":::
        Het macOS CocoaPod-pakket is beschikbaar voor downloaden en gebruiken met de <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1 (of hoger <span class="docon docon-navigate-external x-hidden-focus"></span> )</a> Integrated Development Environment (IDE). <a href="https://aka.ms/csspeech/macosbinary" target="_blank">Down load eerst de binaire CocoaPod <span class="docon docon-navigate-external x-hidden-focus"></span> </a>. Pak de pod in dezelfde map uit voor het beoogde gebruik, maak een *Podfile* en lijst de `pod` as a `target` .
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xcode" src="https://docs.microsoft.com/media/logos/logo_xcode.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

```
platform :ios, '9.3'
use_frameworks!

target 'MyApp' do
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.15.0'
end
```

# <a name="xamarinmac"></a>[Xamarin. Mac](#tab/mac-xamarin)

:::row:::
    :::column span="3":::
        Xamarin. Mac biedt de volledige macOS SDK voor .NET-ontwikkel aars om systeem eigen Mac-toepassingen te bouwen met C#. Zie <a href="https://docs.microsoft.com/xamarin/mac/" target="_blank">Xamarin. Mac <span class="docon docon-navigate-external x-hidden-focus"></span> </a>voor meer informatie.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xamarin" src="https://docs.microsoft.com/media/logos/logo_xamarin.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

# <a name="python"></a>[Python](#tab/mac-python)

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

---

#### <a name="additional-resources"></a>Aanvullende bronnen

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/macos" target="_blank">macOS Speech SDK Quick Start-C-bron code <span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/macos" target="_blank">Quick Start-Swift-bron code voor macOS Speech SDK <span class="docon docon-navigate-external x-hidden-focus"></span></a>
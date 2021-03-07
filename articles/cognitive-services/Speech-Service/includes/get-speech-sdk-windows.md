---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: fc4cc5063f72ff3f0db62cde79f7908add86166e
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102434489"
---
:::row:::
    :::column span="3":::
        De Speech SDK ondersteunt Windows 10 en Windows Server 2016 of hoger. Eerdere versies worden **niet** officieel ondersteund. Het is mogelijk om onderdelen van de Speech SDK te gebruiken met eerdere versies van Windows, maar dit wordt niet aanbevolen.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Windows" src="https://docs.microsoft.com/media/logos/logo_Windows.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

### <a name="system-requirements"></a>Systeemvereisten

Voor de Speech SDK in Windows is <a href="https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads" target="_blank">micro soft Visual C++ Redistributable voor Visual Studio 2019 </a> op het systeem vereist.

- <a href="https://aka.ms/vs/16/release/vc_redist.x86.exe" target="_blank">Installeren voor x86 </a>
- <a href="https://aka.ms/vs/16/release/vc_redist.x64.exe" target="_blank">Installeren voor x64 </a>
- <a href="https://aka.ms/vs/16/release/vc_redist.arm64.exe" target="_blank">Installeren voor ARMx64 </a>

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

Voor invoer van de microfoon moeten de Media Foundation-bibliotheken zijn geïnstalleerd. Deze bibliotheken maken deel uit van Windows 10 en Windows Server 2016. Het is mogelijk om de Speech SDK te gebruiken zonder deze bibliotheken, zolang een microfoon niet wordt gebruikt als het apparaat voor audio-invoer.

De vereiste spraak-SDK-bestanden kunnen worden geïmplementeerd in dezelfde map als uw toepassing. Op deze manier kan uw toepassing rechtstreeks toegang krijgen tot de bibliotheken. Zorg ervoor dat u de juiste versie (x86/x64) selecteert die overeenkomt met uw toepassing.

| Name                                            | Functie                                             |
|-------------------------------------------------|------------------------------------------------------|
| `Microsoft.CognitiveServices.Speech.core.dll`   | Core-SDK, vereist voor systeem eigen en beheerde implementatie |
| `Microsoft.CognitiveServices.Speech.csharp.dll` | Vereist voor beheerde implementatie                      |

> [!NOTE]
> Vanaf de release-1.3.0 het bestand `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (geleverd in eerdere releases) is niet meer nodig. De functionaliteit is nu geïntegreerd in de core-SDK.

> [!IMPORTANT]
> Voor het C#-project Windows Forms app (.NET Framework) moet u ervoor zorgen dat de bibliotheken zijn opgenomen in de implementatie-instellingen van uw project. U kunt dit controleren onder `Properties -> Publish Section` . Klik op de `Application Files` knop en zoek de bijbehorende bibliotheken in de schuif lijst omlaag. Zorg ervoor dat de waarde is ingesteld op `Included` . Visual Studio neemt het bestand op wanneer het project wordt gepubliceerd/geïmplementeerd.

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]

---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 03/30/2021
ms.author: parkerra
ms.openlocfilehash: 7faab3340483d99fa276de06f3fd7787457edb9e
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076690"
---
De volgende stap is het downloaden van de Azure spatiale-ankers pakketten voor Unity. 

> [!WARNING]
> ASA SDK 2.7.0 is de mini maal ondersteunde versie. Als eenheid 2020 wordt gebruikt, is ASA SDK 2.9.0 de mini maal ondersteunde versie.

Als u gebruik wilt gaan van ruimtelijke beslagen van Azure in eenheid, moet u het kern pakket downloaden, evenals een platformspecifieke pakket voor elk platform (Android/iOS/HoloLens) dat u wilt ondersteunen.

| Platform | Naam van het pakket                                    |
|----------|-------------------------------------------------|
| Android/iOS/HoloLens  | com. micro soft. Azure. ruimtelijke-ankers-sdk.core@ <version_number> |
| Android  | com. micro soft. Azure. ruimtelijke-ankers-sdk.android@ <version_number> |
| iOS      | com. micro soft. Azure. ruimtelijke-ankers-sdk.ios@ <version_number>     |
| HoloLens | com. micro soft. Azure. ruimtelijke-ankers-sdk.windows@ <version_number> |

# <a name="download-with-web-browser"></a>[Downloaden met webbrowser](#tab/unity-package-web-ui)

Zoek [hier](https://aka.ms/aoa/unity-sdk/package)het kern pakket van de Azure spatiale ankers voor unit. Selecteer de gewenste versie en down load het pakket met behulp van de knop **downloaden** . Herhaal deze stap om het pakket te downloaden voor elk platform dat u wilt ondersteunen.

# <a name="download-with-npm"></a>[Downloaden met NPM](#tab/unity-package-npm)

Voor deze stap moet <a href="https://www.npmjs.com/get-npm" target="_blank">NPM</a> zijn geïnstalleerd en beschikbaar zijn.

Voer de volgende opdracht uit, waarbij `<version_number>` u vervangt door de versie van de Azure spatiale ankers die u naar de huidige map wilt downloaden:

```bash
npm pack com.microsoft.azure.spatial-anchors-sdk.core@<version_number> --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/
```

> [!NOTE]
> Voer de volgende handelingen uit om de beschik bare versies van het Azure spatiale ankers-pakket te vermelden:
>
> ```bash
> npm view com.microsoft.azure.spatial-anchors-sdk.core --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/ versions
> ```

Het kern pakket voor de Azure spatiale ankers wordt gedownload naar de map waarin u de opdracht hebt uitgevoerd. Herhaal deze stap om het pakket te downloaden voor elk platform dat u wilt ondersteunen.

# <a name="install-with-mixed-reality-feature-tool-beta"></a>[Installeren met het hulp programma voor functie voor gemengde realiteit (bèta)](#tab/unity-package-mixed-reality-feature-tool)

Ga door naar de volgende stap. U gebruikt het <a a href="/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool" target="_blank">hulp programma voor de functie voor gemengde realiteit</a> in een latere stap.

---
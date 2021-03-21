---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 03/18/2021
ms.author: parkerra
ms.openlocfilehash: d91c0aeda2b7ae2f133d2099cbc9d238fd19d287
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104719867"
---
Als u de vereiste pakketten wilt downloaden, moet <a href="https://www.npmjs.com/get-npm" target="_blank">NPM</a> zijn geïnstalleerd.

Voer de volgende opdracht uit, waarbij `<version_number>` u vervangt door de versie van de Azure spatiale ankers die u naar de huidige map wilt downloaden:

```bash
npm pack com.microsoft.azure.spatial-anchors-sdk.core@<version_number> --registry https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM
```

> [!NOTE]
> Voer de volgende handelingen uit om de beschik bare versies van het Azure spatiale ankers-pakket te vermelden:
>
> ```bash
> npm view com.microsoft.azure.spatial-anchors-sdk.core --registry https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM versions
> ```

> [!WARNING]
> ASA SDK 2.7.0 is de mini maal ondersteunde versie. Als eenheid 2020 wordt gebruikt, is ASA SDK 2.9.0 de mini maal ondersteunde versie.

Het kern pakket voor de Azure spatiale ankers wordt gedownload naar de map waarin u de opdracht hebt uitgevoerd.

Herhaal deze stap om het pakket te downloaden voor elk platform (Android/iOS/HoloLens) dat u wilt ondersteunen in uw project.

| Platform | Naam van het pakket                                    |
|----------|-------------------------------------------------|
| Android  | com. micro soft. Azure. ruimtelijke-ankers-sdk.android@ <version_number> |
| iOS      | com. micro soft. Azure. ruimtelijke-ankers-sdk.ios@ <version_number>     |
| HoloLens | com. micro soft. Azure. ruimtelijke-ankers-sdk.windows@ <version_number> |
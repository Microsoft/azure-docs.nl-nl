---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 2/3/21
ms.author: parkerra
ms.openlocfilehash: f6c2780ccbb914228a9870cb3b5fe4b0e3d0b214
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99569567"
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

Het kern pakket voor de Azure spatiale ankers wordt gedownload naar de map waarin u de opdracht hebt uitgevoerd.

Herhaal deze stap om het pakket te downloaden voor elk platform (Android/iOS/HoloLens) dat u wilt ondersteunen in uw project.

| Platform | Naam van het pakket                                    |
|----------|-------------------------------------------------|
| Android  | com. micro soft. Azure. ruimtelijke-ankers-sdk.android@ <version_number> |
| iOS      | com. micro soft. Azure. ruimtelijke-ankers-sdk.ios@ <version_number>     |
| HoloLens | com. micro soft. Azure. ruimtelijke-ankers-sdk.windows@ <version_number> |
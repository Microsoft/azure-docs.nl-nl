---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 2/3/21
ms.author: parkerra
ms.openlocfilehash: c97067c66296e8980a36b21298a7b2061e0f9b4c
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550377"
---
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
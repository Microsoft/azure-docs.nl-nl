---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 02/18/2021
ms.author: crtreasu
ms.openlocfilehash: 4345810292896cf88de19baf419eee025ba5853f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102044634"
---
De volgende stap is het downloaden van het pakket met Azure object-ankers voor Unity.

# <a name="download-with-web-browser"></a>[Downloaden met webbrowser](#tab/unity-package-web-ui)

Zoek [hier](https://aka.ms/aoa/unity-sdk/package)het pakket met Azure object-ankers voor Unity. Selecteer de gewenste versie en down load het pakket met behulp van de knop **downloaden** .

# <a name="download-with-npm"></a>[Downloaden met NPM](#tab/unity-package-npm)

Voor deze stap moet <a href="https://www.npmjs.com/get-npm" target="_blank">NPM</a> zijn geïnstalleerd en beschikbaar zijn.

Voer de volgende opdracht uit, waarbij `<version_number>` u vervangt door de versie van de Azure object-ankers die u wilt downloaden:

```bash
npm pack com.microsoft.azure.object-anchors.runtime@<version_number> --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/
```

> [!NOTE]
> Voer de volgende handelingen uit om de beschik bare versies van het pakket met Azure object-ankers weer te geven:
>
> ```bash
> npm view com.microsoft.azure.object-anchors.runtime --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/ versions
> ```

Het pakket met Azure object-ankers wordt gedownload naar de map waarin u de opdracht hebt uitgevoerd.

# <a name="install-with-mixed-reality-feature-tool-beta"></a>[Installeren met het hulp programma voor functie voor gemengde realiteit (bèta)](#tab/unity-package-mixed-reality-feature-tool)

Ga door naar de volgende stap. U gebruikt het <a a href="https://aka.ms/MRFeatureToolDocs" target="_blank">hulp programma voor de functie voor gemengde realiteit</a> in een latere stap.

---

---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 03/02/2021
ms.author: crtreasu
ms.openlocfilehash: 8b7ab183dfb7b6721beae7835acd75bc7a05e72c
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102044624"
---
### <a name="upload-your-model"></a>Uw model uploaden

Als u nog geen object ankers model hebt, volgt u de instructies in [Create a model](/object-anchors/quickstarts/get-started-model-conversion.md) om er een te maken. Ga vervolgens hier terug.

Als uw HoloLens is verbonden met de Windows-portal voor apparaten, volgt u deze stappen om een model te uploaden dat de app kan gebruiken:

1. Ga in de Windows-Portal naar **System > bestanden verkenner > LocalAppData**. Daar ziet u uw toepassing in de lijst met apps.

    :::image type="content" source="./media/object-anchors-quickstarts-unity/portal-localappdata.png" alt-text="bestanden Verkenner":::

2. Open uw toepassing en klik op de `LocalState` map.

    :::image type="content" source="./media/object-anchors-quickstarts-unity/portal-localstate.png" alt-text="Open de map LocalState":::

3. Upload het model bestand naar de `LocalState` map.

    :::image type="content" source="./media/object-anchors-quickstarts/portal-upload-model.png" alt-text="het model uploaden in de portal":::

    Start de toepassing opnieuw vanuit HoloLens. U kunt nu objecten detecteren die overeenkomen met het model.

---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 03/02/2021
ms.author: crtreasu
ms.openlocfilehash: d8dfc3d4b7a8447250481b98c1adadc865a29da1
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102532601"
---
### <a name="upload-your-model"></a>Uw model uploaden

Als u nog geen object ankers model hebt, volgt u de instructies in [Create a model](/azure/object-anchors/quickstarts/get-started-model-conversion) om er een te maken. Ga vervolgens hier terug.

Als uw HoloLens is verbonden met de Windows-portal voor apparaten, volgt u deze stappen om een model te uploaden dat de app kan gebruiken:

1. Ga in de Windows-Portal naar **System > bestanden verkenner > LocalAppData**. Daar ziet u uw toepassing in de lijst met apps.

    :::image type="content" source="./media/object-anchors-quickstarts-unity/portal-localappdata.png" alt-text="bestanden Verkenner":::

2. Open uw toepassing en klik op de `LocalState` map.

    :::image type="content" source="./media/object-anchors-quickstarts-unity/portal-localstate.png" alt-text="Open de map LocalState":::

3. Upload het model bestand naar de `LocalState` map.

    :::image type="content" source="./media/object-anchors-quickstarts/portal-upload-model.png" alt-text="het model uploaden in de portal":::

    Start de toepassing opnieuw vanuit HoloLens. U kunt nu objecten detecteren die overeenkomen met het model.

---
title: Overzicht van Azure object-ankers
description: Meer informatie over hoe u met Azure object ankerpunten objecten in de fysieke wereld kunt detecteren.
author: craigktreasure
manager: vriveras
ms.author: crtreasu
ms.date: 03/02/2021
ms.topic: overview
ms.service: azure-object-anchors
ms.openlocfilehash: b4284411fbe09a0d0ce8412e6e5a5df464f35564
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103462861"
---
# <a name="azure-object-anchors-overview"></a>Overzicht van Azure object-ankers

Met Azure object-ankers kan een toepassing een object in de fysieke wereld detecteren met behulp van een 3D-model en de 6DoFe pose schatten. De 6DoF (6 graden van vrijheid) is gedefinieerd als een rotatie en omzetting tussen een 3D-model en de bijbehorende fysieke tegen hanger, het echte object.

Azure object-ankers bestaan uit een service voor model conversie en een runtime-client-SDK voor HoloLens. De service accepteert een 3D-object model en voert een model van Azure object-ankers uit. Het Azure object ankers model wordt samen met de runtime-SDK gebruikt om een HoloLens-toepassing in staat te stellen een object model te laden, te detecteren en een exemplaar (en) van dat model in de fysieke wereld te volgen.

:::image type="content" source="./media/aoa-overview.jpg" alt-text="Azure object-ankers in actie":::

## <a name="examples"></a>Voorbeelden

Enkele voor beelden van use cases die worden ingeschakeld door Azure object-ankers zijn:

- **Training**. Maak een trainings ervaring voor gemengde realiteit voor uw werk nemers, zonder de behoefte aan markeringen of besteed tijd hand matig te wijzigen. Als u uw trainings ervaring voor gemengde realiteit met automatische detectie en tracering wilt uitbreiden, neemt u uw model op in de Azure object ankers-service en gaat u één stap in de buurt van een ervaring met markeringen.

- **Taak richtlijnen**. Het door lopen van werk nemers door een reeks taken kan aanzienlijk worden vereenvoudigd wanneer u Mixed Reality gebruikt. Het bedekken van digitale instructies en aanbevolen procedures, boven op het fysieke object, zijn het een stuk machine op een fabriek of een koffie maker in de team keuken, waardoor de kans op het volt ooien van de taken aanzienlijk kan worden verkleind. Het activeren van deze ervaringen vereist meestal een vorm van een markering of hand matige uitlijning, maar met Azure object-ankers kunt u een ervaring maken die automatisch het object detecteert dat aan de taak is gekoppeld. Daarna kunt u de richt lijnen voor gemengde realiteit naadloos door lopen zonder markeringen of hand matige uitlijning.

- **Activa zoeken**. Als u al een 3D-model van een object in uw fysieke ruimte hebt, kunt u met Azure object-ankers instanties van dat object zoeken en volgen in uw fysieke omgeving.

## <a name="next-steps"></a>Volgende stappen

De volgende secties bevatten informatie over aan de slag met het gebruik en het bouwen van apps met Azure object-ankers.

> [!div class="nextstepaction"]
> [Model opname](quickstarts/get-started-model-conversion.md)

> [!div class="nextstepaction"]
> [De eenheid HoloLens](quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [De eenheid HoloLens met MRTK](quickstarts/get-started-unity-hololens-mrtk.md)

> [!div class="nextstepaction"]
> [HoloLens DirectX](quickstarts/get-started-hololens-directx.md)

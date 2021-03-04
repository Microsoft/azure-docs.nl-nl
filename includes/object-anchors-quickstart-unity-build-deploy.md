---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 04/03/2020
ms.author: crtreasu
ms.openlocfilehash: 43c8c578587c65b6e0317caf77ff2d0604cb4fa3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "102044571"
---
### <a name="build-and-deploy-the-app"></a>De app compileren en implementeren

Open het `.sln` bestand dat is gegenereerd door eenheid. Wijzig de configuratie van de build in het volgende.

:::image type="content" source="./media/object-anchors-quickstarts-unity/aoa-unity-vs-build-config.png" alt-text="configuratie maken":::

Vervolgens moet u het **IP-adres van de externe computer** configureren om de app te implementeren en fouten op te sporen.

Klik met de rechter muisknop op het app-project en selecteer **Eigenschappen**. Selecteer **configuratie-eigenschappen-> fout opsporing** op de pagina Eigenschappen. Wijzig de waarde van de **computer naam** naar het IP-adres van uw HoloLens-apparaat en klik op **Toep assen**.

:::image type="content" source="./media/object-anchors-quickstarts-unity/aoa-unity-vs-remote-debug.png" alt-text="fout opsporing op afstand":::

Sluit de eigenschappen pagina. Klik op **externe computer**. De app moet beginnen met het bouwen en implementeren op uw externe apparaat. Zorg ervoor dat uw apparaat actief is.

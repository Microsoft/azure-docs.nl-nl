---
title: 'Quickstart: Een HoloLens-app maken met Unity'
description: In deze Quick Start leert u hoe u een app voor het maken van een HoloLens-eenheid maakt met behulp van object ankers.
author: craigktreasure
manager: virivera
ms.author: crtreasu
ms.date: 03/02/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: 4f85a258042430d58690ef578db6d21a6c831d50
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102044644"
---
# <a name="quickstart-create-a-hololens-app-with-azure-object-anchors-in-unity"></a>Snelstartgids: een HoloLens-app maken met Azure object-ankers, in eenheid

In deze Quick Start maakt u een unit HoloLens-app die gebruikmaakt van [Azure object-ankers](../overview.md). Azure object-ankers is een beheerde Cloud service waarmee 3D-assets naar AI-modellen worden geconverteerd waarmee objecten met gemengde realiteiten voor de HoloLens kunnen worden ingeschakeld. Wanneer u klaar bent, hebt u een app HoloLens gemaakt met unit met een eenheid waarmee objecten in de fysieke wereld kunnen worden gedetecteerd.

U leert het volgende:

> [!div class="checklist"]
> * Build-instellingen voor Unity voorbereiden.
> * Het HoloLens Visual Studio-project exporteren.
> * Implementeer de app en voer deze uit op een HoloLens 2-apparaat.

[!INCLUDE [Unity quickstart prerequisites](../../../includes/object-anchors-quickstart-unity-prerequisites.md)]

[!INCLUDE [Unity device setup](../../../includes/object-anchors-quickstart-unity-device-setup.md)]

## <a name="open-the-sample-project"></a>Voorbeeldproject openen

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

[!INCLUDE [Download Unity Package](../../../includes/object-anchors-quickstart-unity-download-package.md)]

Open het project in Unity `quickstarts/apps/unity/basic` .

[!INCLUDE [Import Unity Package](../../../includes/object-anchors-quickstart-unity-import-package.md)]

[!INCLUDE [Unity build sample scene 1](../../../includes/object-anchors-quickstart-unity-build-sample-scene-1.md)]

[!INCLUDE [Unity build sample scene 2](../../../includes/object-anchors-quickstart-unity-build-sample-scene-2.md)]

[!INCLUDE [Unity build and deploy](../../../includes/object-anchors-quickstart-unity-build-deploy.md)]

### <a name="run-the-sample-app"></a>De voorbeeld-app uitvoeren

Schakel het apparaat in, selecteer **alle apps**, zoek de app en start deze. Na het begin scherm van de eenheid ziet u een bericht dat aangeeft dat de object waarnemer is geïnitialiseerd. U moet echter uw model toevoegen aan de app.

[!INCLUDE [Unity setup Windows Device Portal](../../../includes/object-anchors-quickstart-unity-setup-device-portal.md)]

[!INCLUDE [Unity upload your model](../../../includes/object-anchors-quickstart-unity-upload-model.md)]

De app zoekt naar objecten in het huidige veld van de weer gave en traceert deze vervolgens eenmaal gedetecteerd. Een exemplaar wordt verwijderd wanneer het 6 meters uit de locatie van de gebruiker bevindt. De tekst van de fout opsporing toont details over een exemplaar, zoals ID, bijgewerkte verhouding van tijds tempel en dekking van Opper vlakken.

[!INCLUDE [Unity troubleshooting](../../../includes/object-anchors-quickstart-unity-troubleshooting.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [De eenheid HoloLens met MRTK](get-started-unity-hololens-mrtk.md)

> [!div class="nextstepaction"]
> [Concepten: overzicht van de SDK](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [Veelgestelde vragen](../faq.md)

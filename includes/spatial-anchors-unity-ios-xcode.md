---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: d969b1639dbb39b920c985964abe1239f068d740
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185264"
---
Selecteer **Build**. Selecteer in het deelvenster dat wordt geopend een map waarnaar u het Xcode-project wilt exporteren.

   Als het exporteren is voltooid, wordt er een map weergegeven met het geëxporteerde Xcode-project.

   > [!NOTE]
   > Als er een venster wordt weergegeven waarin u wordt gevraagd of u wilt vervangen of toevoegen, raden wij aan **Toevoegen** te selecteren, omdat dat sneller gaat. Selecteer alleen **Vervangen** als u activa in uw scène wijzigt. Bijvoorbeeld als u bovenliggende/onderliggende relaties toevoegt, verwijdert of wijzigt, of als u eigenschappen toevoegt, verwijdert of wijzigt. Als u alleen wijzigingen in de broncode aanbrengt, moet **Toevoegen** voldoende zijn.

## <a name="open-the-xcode-project"></a>Xcode-project openen

U kunt uw `Unity-iPhone.xcodeproj`-project nu openen in Xcode. 

U kunt Xcode starten en het geëxporteerde project `Unity-iPhone.xcodeproj` openen of het project in Xcode starten door de volgende opdracht uit te voeren vanaf de locatie waar u het project hebt geëxporteerd:

 ```bash
open ./Unity-iPhone.xcodeproj
```

Selecteer het hoofdknooppunt **Unity-iPhone** om de projectinstellingen weer te geven en selecteer vervolgens het tabblad **General**.

Controleer of onder **Signing** de instelling **Automatically manage signing** is ingeschakeld. Als dat niet het geval is, schakelt u deze in en selecteert u **Enable Automatic** in het dialoogvenster dat wordt weergegeven om de opbouwinstellingen opnieuw in te stellen.

Controleer of onder **Deployment Info** de optie **Deployment Target** is ingesteld op **11.0**.

## <a name="deploy-the-app-to-your-ios-device"></a>De app implementeren op uw iOS-apparaat

Verbind het iOS-apparaat met de Mac en stel het **actieve schema** in voor uw iOS-apparaat.

   ![Schermopname van de knop Mijn iPhone voor het selecteren van het apparaat.](./media/spatial-anchors-unity/select-device.png)

Selecteer **Build and then run the current scheme** (Het huidige schema compileren en uitvoeren).

   ![Schermopname van de pijlknop 'Implementeren en uitvoeren'.](./media/spatial-anchors-unity/deploy-run.png)

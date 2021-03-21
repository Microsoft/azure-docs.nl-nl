---
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: erhopf
ms.openlocfilehash: a3345ff9a6cc1d5f8e2fbc78c2a76ba6f183aeb6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102620086"
---
## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services)
* Een insluitende lezer-resource die is geconfigureerd voor Azure Active Directory-verificatie. Volg [deze instructies](../../how-to-create-immersive-reader.md) om deze in te stellen.  U hebt enkele waarden nodig die u hier hebt gemaakt bij het configureren van de eigenschappen van de omgeving. Sla de uitvoer van uw sessie op in een tekstbestand voor later gebruik.
* [macOS](https://www.apple.com/macos).
* [Git](https://git-scm.com/).
* [SDK voor Insluitende lezer](https://github.com/microsoft/immersive-reader-sdk).
* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12).

## <a name="configure-authentication-credentials"></a>Verificatiereferenties configureren

1. Open Xcode en open **immersive-reader-sdk/js/samples/ios/quickstart-swift/quickstart-swift.xcodeproj**.
1. Selecteer in het bovenste menu **Product** > **Scheme** > **Edit Scheme**.
1. Selecteer in de weergave **Run** het tabblad **Arguments**.
1. Voeg in de sectie **Environment Variables** de volgende namen en waarden toe. Geef de waarden op die werden weergegeven bij het maken van de Insluitende lezer-resource.

    ```text
    TENANT_ID=<YOUR_TENANT_ID>
    CLIENT_ID=<YOUR_CLIENT_ID>
    CLIENT_SECRET<YOUR_CLIENT_SECRET>
    SUBDOMAIN=<YOUR_SUBDOMAIN>
    ```

Zorg ervoor dat u deze wijziging niet doorvoert in broncodebeheer, omdat er geheimen in staan die niet openbaar mogen worden gemaakt.

## <a name="start-the-immersive-reader-with-sample-content"></a>Insluitende lezer starten met voorbeeldinhoud

Selecteer in Xcode **Ctrl+R** om het project uit te voeren.

## <a name="next-steps"></a>Volgende stappen

* Verken de [SDK voor Insluitende lezer](https://github.com/microsoft/immersive-reader-sdk) en de [naslaginformatie voor de SDK voor Insluitende lezer](../../reference.md).
* Bekijk codevoorbeelden op [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/).

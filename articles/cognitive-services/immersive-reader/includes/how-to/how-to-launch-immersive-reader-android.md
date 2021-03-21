---
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: erhopf
ms.openlocfilehash: d5a483cb239893d04d2c2581fb378f411878f4ba
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102620074"
---
## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services)
* Een insluitende lezer-resource die is geconfigureerd voor Azure Active Directory-verificatie. Volg [deze instructies](../../how-to-create-immersive-reader.md) om deze in te stellen.  U hebt enkele waarden nodig die u hier hebt gemaakt bij het configureren van de eigenschappen van de omgeving. Sla de uitvoer van uw sessie op in een tekstbestand voor later gebruik.
* [Git](https://git-scm.com/).
* [SDK voor Insluitende lezer](https://github.com/microsoft/immersive-reader-sdk).
* [Android Studio](https://developer.android.com/studio).

## <a name="configure-authentication-credentials"></a>Verificatiereferenties configureren

1. Start Android Studio en open het project in de map **immersive-reader-sdk/js/samples/quickstart-java-android** (Java) of de map **immersive-reader-sdk/js/samples/quickstart-kotlin** (Kotlin).

1. Maak een bestand met de naam **env** in de map **/assets**. Voeg de volgende namen en waarden toe en geef waar nodig waarden op. Zorg ervoor dat u dit bestand niet doorvoert in broncodebeheer, omdat er geheimen in staan die niet openbaar mogen worden gemaakt.
    
    ```text
    TENANT_ID=<YOUR_TENANT_ID>
    CLIENT_ID=<YOUR_CLIENT_ID>
    CLIENT_SECRET=<YOUR_CLIENT_SECRET>
    SUBDOMAIN=<YOUR_SUBDOMAIN>
    ```

## <a name="start-the-immersive-reader-with-sample-content"></a>Insluitende lezer starten met voorbeeldinhoud

Kies een apparaat-emulator in AVD Manager en voer het project uit.

## <a name="next-steps"></a>Volgende stappen

* Verken de [SDK voor Insluitende lezer](https://github.com/microsoft/immersive-reader-sdk) en de [naslaginformatie voor de SDK voor Insluitende lezer](../../reference.md).
* Bekijk codevoorbeelden op [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/).
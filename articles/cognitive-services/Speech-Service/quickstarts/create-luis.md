---
title: 'Quickstart: een LUIS-sleutel maken'
titleSuffix: Azure Cognitive Services
description: In deze quickstart leert u hoe u een LUIS-toepassing maakt en een sleutel verkrijgt.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 06/25/2020
ms.author: trbye
ms.openlocfilehash: f547b353091cc81816f0974fced8d4dc836d22b2
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95023817"
---
# <a name="quickstart-getting-a-luis-endpoint-key"></a>Quickstart: een LUIS-eindpuntsleutel ophalen

## <a name="prerequisites"></a>Vereisten

Zorg dat u over de volgende items beschikt voordat u met deze zelfstudie begint:

* Een LUIS-account. U kunt een gratis account aanvragen via het [LUIS portal](https://www.luis.ai/home).

## <a name="luis-and-speech"></a>LUIS en spraakherkenning

LUIS kan worden geïntegreerd met de Speech-service voor het herkennen van intenties van spraak. U hebt geen abonnement op de Speech-service nodig, alleen op LUIS.

LUIS maakt gebruik van drie soorten sleutels:

|Type sleutel|Doel|
|--------|-------|
|Ontwerpen|Hiermee kunt u via programmacode LUIS-apps maken en wijzigen|
|Starter|Hiermee kunt u uw LUIS-toepassing testen met alleen tekst|
|Eindpunt |Hiermee kunt u toegang verlenen tot een bepaalde LUIS-app|

Voor deze zelfstudie hebt u het een sleutel van het type eindpunt nodig. In de zelfstudie wordt gebruikgemaakt van de voorbeeld-app Home Automation van LUIS. Deze app kunt u maken aan de hand van de instructies in de quickstart [ Een vooraf gemaakte Home Automation-app gebruiken](../../luis/luis-get-started-create-app.md). Als u de beschikking hebt over een eigen LUIS-app, kunt u die ook gebruiken.

Wanneer u een LUIS-app maakt, wordt er automatisch een starter-sleutel gegenereerd, zodat u de app kunt testen met tekstquery's. Deze sleutel zorgt er overigens niet voor dat de integratie van de Speech-service wordt ingeschakeld en werkt ook niet met deze zelfstudie. Maak een LUIS-resource in het Azure-dashboard en wijs deze toe aan de LUIS-app. U kunt de gratis versie van het abonnement gebruiken voor deze zelfstudie.

Als u de LUIS-resource in het Azure-dashboard hebt gemaakt, meldt u zich aan bij de [LUIS-portal](https://www.luis.ai/home), kiest u uw toepassing op de pagina **My Apps** en gaat u naar de pagina **Manage** van de app. Ten slotte selecteert u **Keys and Endpoints** in de zijbalk.

![Instellingen voor sleutels en eindpunten in de LUIS-portal](~/articles/cognitive-services/Speech-Service/media/sdk/luis-keys-endpoints-page.png)

Ga als volgt te werk op de pagina **Keys and Endpoints settings**:

1. Scrol omlaag naar het gedeelte **Resources and Keys** en selecteer **Assign resource**.
1. In het dialoogvenster **Assign a key to your app** brengt u de volgende wijzigingen aan:

   * Onder **Tenant** kiest u **Microsoft**.
   * Onder **Subscription Name** kiest u het Azure-abonnement met de LUIS-resource die u wilt gebruiken.
   * Kies onder **Key** de LUIS-resource die u voor de app wilt gebruiken.

   Na korte tijd wordt het nieuwe abonnement weergegeven in de tabel onderaan de pagina.

1. Selecteer het pictogram naast een sleutel om deze naar het klembord te kopiëren. (U kunt beide sleutels gebruiken.)

![Abonnementssleutels voor LUIS-app](~/articles/cognitive-services/Speech-Service/media/sdk/luis-keys-assigned.png)


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Intenties herkennen](~/articles/cognitive-services/Speech-Service/quickstarts/intent-recognition.md)
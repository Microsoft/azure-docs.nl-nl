---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: trbye
ms.openlocfilehash: 4725a1a9cf2cb74655a37ac27a0a86f10d7f4bb9
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98052772"
---
In deze quickstart gebruikt u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) en de Language Understanding-service (LUIS) om intenties te herkennen van audiogegevens die zijn vastgelegd vanuit een microfoon. In het bijzonder gebruikt u de Speech SDK om spraak vast te leggen en een vooraf ingebouwd domein van LUIS om intenties voor huisautomatisering te identificeren, zoals het in- en uitschakelen van een lampje. 

Na het voldoen aan verschillende voorwaarden, zijn er slechts enkele stappen nodig om de spraak en de intenties van de microfoon te identificeren:

> [!div class="checklist"]
>
> * Maak een `SpeechConfig`-object op basis van uw abonnementssleutel en -regio.
> * Maak een `IntentRecognizer`-object met het bovenstaande `SpeechConfig`-object.
> * Gebruik het `IntentRecognizer`-object om het herkenningsproces voor één uiting te starten.
> * Inspecteer de geretourneerde `IntentRecognitionResult`.


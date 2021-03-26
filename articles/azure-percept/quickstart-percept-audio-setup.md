---
title: Azure percept-Audio instellen
description: Meer informatie over hoe u uw Azure percept-audio apparaat verbindt met uw Azure percept DK
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: quickstart
ms.date: 03/25/2021
ms.openlocfilehash: fa3dad8cdd38e6db621d8194cc9472430c7c5008
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605787"
---
# <a name="azure-percept-audio-setup"></a>Setup van Azure percept-audio

Azure percept-audio werkt uit de doos met Azure percept DK. Er is geen unieke installatie vereist.

## <a name="prerequisites"></a>Vereisten

- Azure percept DK (Devkit)
- Azure percept-audio
- [Azure-abonnement](https://azure.microsoft.com/free/)
- [Setup van Azure PERCEPT DK](./quickstart-percept-dk-set-up.md): u hebt uw Devkit met een Wi-Fi-netwerk verbonden, een IOT hub gemaakt en uw Devkit verbonden met de IOT hub
- Spreker of hoofd telefoon die verbinding kan maken met een 3,5 mm audio-stekker (optioneel)

## <a name="connecting-your-devices"></a>Verbinding maken met uw apparaten

1. Verbind het Azure percept-audio apparaat met de Azure percept DK-carrier board met de meegeleverde micro USB naar USB-type-A-kabel. Sluit het micro USB-einde van de kabel aan op het bord van de audio-interposeer (Developer) en het type-A-end aan het percept DK-Carrier Board.

1. (Optioneel) Verbind uw spreker of koptelefoon met uw Azure percept-audio apparaat via de audio-stekker. dit wordt ' line out ' genoemd. Zo kunt u geluids reacties horen.

1. Schakel de Devkit uit. LED-L02 op het audio-interposeer-bord worden gewijzigd in knipperend wit om aan te geven dat het apparaat is ingeschakeld en dat het SoM van de audio wordt geverifieerd.

1. Wacht tot het verificatie proces is voltooid. Dit kan Maxi maal drie minuten duren.

1. U bent klaar om te beginnen met het maken van prototypen wanneer u een van de volgende ziet:

    - LED L02 wordt gewijzigd in effen wit: Dit geeft aan dat de verificatie is voltooid en dat de Devkit nog niet is geconfigureerd met een sleutel woord.
    - Alle drie Led's worden blauw weer gegeven: Dit geeft aan dat de verificatie is voltooid en dat de Devkit is geconfigureerd met een sleutel woord.

## <a name="next-steps"></a>Volgende stappen

Maak een [spraak oplossing zonder code](./tutorial-no-code-speech.md) in [Azure percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819).

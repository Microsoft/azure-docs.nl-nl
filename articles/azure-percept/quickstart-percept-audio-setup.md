---
title: Aan de slag met Azure percept-audio
description: Meer informatie over hoe u uw Azure percept-audio apparaat verbindt met uw Azure percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/18/2021
ms.custom: template-quickstart
ms.openlocfilehash: 660f03ce248a27a00fdd443964fbdba2fe3adeb0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179269"
---
# <a name="azure-percept-audio-setup"></a>Setup van Azure percept-audio

Azure percept-audio werkt uit de doos met Azure percept DK. Er is geen unieke installatie vereist.

## <a name="prerequisites"></a>Vereisten

- Azure percept DK (Devkit)
- Azure percept-audio
- [Azure-abonnement](https://azure.microsoft.com/free/)
- [Setup van Azure PERCEPT DK](./quickstart-percept-dk-set-up.md): u hebt uw Devkit met een Wi-Fi-netwerk verbonden, een IOT hub gemaakt en uw Devkit verbonden met de IOT hub
- Spreker of hoofd telefoon waarmee verbinding kan worden gemaakt met een audio aansluiting van 3,5 mm (optioneel)

## <a name="connecting-your-devices"></a>Verbinding maken met uw apparaten

1. Verbind het Azure percept-audio apparaat met de Azure percept DK-carrier board met de meegeleverde micro USB naar USB-type-A-kabel. Sluit het micro USB-einde van de kabel aan op het bord (ontwikkelaar) en het type-A-end aan het Percepta-Carrier Board.
1. (Optioneel) Verbind uw spreker of koptelefoon met uw Azure percept-audio via de audio-stekker. dit wordt ' line out ' genoemd. Hiermee kunt u de audio reacties van uw Voice-assistent horen. Als u geen spreker of hoofd telefoon verbindt, kunt u de antwoorden nog steeds zien als tekst in het demo venster. 

1. Schakel de Devkit uit. LED-L02 op het inschakel bord worden gewijzigd in knipperend wit om aan te geven dat het apparaat is ingeschakeld en dat het SoM van de audio wordt geverifieerd.

1. Wacht tot het verificatie proces is voltooid. Dit kan Maxi maal drie minuten duren.

1. U bent klaar om te beginnen met het maken van prototypen wanneer u een van de volgende ziet:

    - LED L02 wordt gewijzigd in effen wit. Dit geeft aan dat de verificatie is voltooid en dat de Devkit nog niet is geconfigureerd met een sleutel woord.
    - Alle drie de Led's draaien blauw. Dit geeft aan dat de verificatie is voltooid en dat de Devkit is geconfigureerd met een sleutel woord.

## <a name="next-steps"></a>Volgende stappen

Maak een [spraak oplossing zonder code](./tutorial-no-code-speech.md) in [Azure percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819).

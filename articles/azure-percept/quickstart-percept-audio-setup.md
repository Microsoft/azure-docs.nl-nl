---
title: Aan de slag met Azure percept-audio
description: Meer informatie over hoe u uw Azure percept-audio apparaat verbindt met uw Azure percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/18/2021
ms.custom: template-quickstart
ms.openlocfilehash: 575107859f56df742ab41a299269c250511022b3
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101665414"
---
# <a name="azure-percept-audio-setup"></a>Setup van Azure percept-audio

Azure percept-audio werkt uit de doos met Azure percept DK. Er is geen unieke installatie vereist.

## <a name="prerequisites"></a>Vereisten

- Azure percept DK (Devkit)
- Azure percept-audio
- [Azure-abonnement](https://azure.microsoft.com/free/)
- [Setup van Azure PERCEPT DK](./quickstart-percept-dk-set-up.md): u hebt uw Devkit met een Wi-Fi-netwerk verbonden, een IOT hub gemaakt en uw Devkit verbonden met de IOT hub

## <a name="connecting-your-devices"></a>Verbinding maken met uw apparaten

1. Verbind het Azure percept-audio apparaat aan de Azure percept DK-carrier board met het meegeleverde USB-type-B naar een USB-type-een kabel. Sluit het micro type-B-uiteinde van de kabel aan op audio SoM en het type-A-einde aan het Percepta-Carrier Board van de DK.

1. Schakel de Devkit uit.

    - LED L01 voor het SoM van de audio wordt gewijzigd in effen groen om aan te geven dat het apparaat is ingeschakeld.
    - LED-L02 wordt gewijzigd in knipperend groen om aan te geven dat het audio-SoM wordt geverifieerd.

1. Wacht tot het verificatie proces is voltooid. Dit kan Maxi maal drie minuten duren.

1. U bent klaar om te beginnen met het maken van prototypen wanneer u een van de volgende ziet:

    - LED-L01 uitgeschakeld en L02 wordt wit. Dit geeft aan dat de verificatie is voltooid en dat de Devkit nog niet is geconfigureerd met een sleutel woord.
    - Alle drie de Led's draaien blauw. Dit geeft aan dat de verificatie is voltooid en dat de Devkit is geconfigureerd met een sleutel woord.

    > [!NOTE]
    > Neem contact op met de ondersteuning als uw Devkit niet verifieert.

## <a name="next-steps"></a>Volgende stappen

Maak een [spraak oplossing zonder code](./tutorial-no-code-speech.md).
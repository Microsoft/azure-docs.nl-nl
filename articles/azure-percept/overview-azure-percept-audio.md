---
title: Overzicht van Azure percept-audio apparaten
description: Meer informatie over Azure percept-audio
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: a63f471498667f58fc80f89323ad93b0feb8bc95
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662687"
---
# <a name="introduction-to-azure-percept-audio"></a>Inleiding tot Azure percept-audio

Azure percept-audio is een accessoire apparaat waarmee u spraak AI-mogelijkheden kunt toevoegen aan Azure percept DK. Het bevat een vooraf geconfigureerde spraak-AI-Accelerator en een lineaire matrix met vier microfoons waarmee u aangepaste opdrachten, tref woorden herkennen en uiterst veld spraak kunt Toep assen op lokale luisterende apparaten. Met Azure percept-audio kunnen hardwarefabrikanten Azure percept DK-visuele mogelijkheden uitbreiden naar nieuwe, slimme spraak geactiveerde apparaten. Het is volledig geïntegreerd met Azure percept DK, Azure percept Studio en andere Azure Edge-beheer Services. Het is beschikbaar voor aankoop in de [online winkel van micro soft](https://go.microsoft.com/fwlink/p/?LinkId=2155270).

![Azure percept-audio apparaat.](./media/overview-azure-percept-audio/percept-audio.png)


## <a name="azure-percept-audio-components"></a>Azure percept audio-onderdelen

Azure percept-audio bevat de volgende belang rijke onderdelen:

- Productie-gereed Azure percept-audio apparaat (SoM) met vier-microfoons lineaire matrix
- Developer Board (bevat 2x knoppen, 3x Led's, USB micro en 3,5 mm audio-stekker)
- Vereiste kabels: flex-kabel, USB-micro type-B naar USB-A
- Welkomst kaart
- Mechanische koppel plaat met geïntegreerde 80/20 1010-serie koppelen


<!---

## How it works

Azure Percept Audio passes the audio input to the Azure Percept DK carrier board in a hybrid edge-cloud manner. Specifically,

- The Azure Percept Audio device: processes the incoming speech input to the clearest format by executing beam forming and echo cancellation befor sending the input to the Azure Percept DK. 
- The Azure Percept DK uses edge processing to perform keyword spotting and then sends the relevant inputs to Azure speech services.
- Cloud: Processing of natural language commands and phrases, in addition to keyword verification and retraining.
- Offline: If the device is offline it will detect the keyword and capture telemetry that there is no internet connection at the time of the command. It will not be able to weed out false accepts since it cannot perform keyword verification.

-->

## <a name="getting-started"></a>Aan de slag

- [Uw Azure percept DK samen stellen](./quickstart-percept-dk-unboxing.md)
- [De Setup-ervaring van Azure percept DK volt ooien](./quickstart-percept-dk-set-up.md)
- [Uw Azure percept-audio apparaat verbinden met uw Devkit](./quickstart-percept-audio-setup.md)

### <a name="build-a-no-code-prototype"></a>Een prototype zonder code maken

Bouw een [oplossing zonder code](./tutorial-no-code-speech.md) met behulp van Azure percept Voice Assistant-sjablonen voor horeca-, gezondheids zorg-, inventaris-en automobiel scenario's.

## <a name="additional-technical-information"></a>Aanvullende technische informatie

- [Azure percept-audio gegevens blad](./azure-percept-audio-datasheet.md)

## <a name="next-steps"></a>Volgende stappen

Bestel een Azure percept-audio apparaat in de [online winkel van micro soft](https://go.microsoft.com/fwlink/p/?LinkId=2155270).
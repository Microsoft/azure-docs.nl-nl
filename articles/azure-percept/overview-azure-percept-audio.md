---
title: Overzicht van Azure percept-audio apparaten
description: Meer informatie over Azure percept-audio
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: template-concept
ms.openlocfilehash: 23fae249cfceec75af0b7c49a3875ab447ecbafd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105564259"
---
# <a name="introduction-to-azure-percept-audio"></a>Inleiding tot Azure percept-audio

Azure percept-audio is een accessoire apparaat waarmee u spraak AI-mogelijkheden kunt toevoegen aan [Azure PERCEPT DK](./overview-azure-percept-dk.md). Het bevat een vooraf geconfigureerde geluids processor en een lineaire matrix met vier microfoons waarmee u spraak opdrachten, trefwoord herkennen en far field speech kunt gebruiken met behulp van Azure Cognitive Services. Het is volledig geïntegreerd met Azure percept DK, [Azure percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)en andere Azure Edge-beheer Services. Azure percept-audio is beschikbaar voor aankopen in de [online winkel van micro soft](https://go.microsoft.com/fwlink/p/?LinkId=2155270).

> [!div class="nextstepaction"]
> [Nu kopen](https://go.microsoft.com/fwlink/p/?LinkId=2155270)

</br>

> [!VIDEO https://www.youtube.com/embed/Qj8NGn-7s5A]

## <a name="azure-percept-audio-components"></a>Azure percept audio-onderdelen

Azure percept-audio bevat de volgende belang rijke onderdelen:

- Productie-klaar Azure percept-audio apparaat (SoM) met een lineaire matrix en geluids verwerking met vier microfoons via XMOS-codec
- Ontwikkel aars (tussen begeleider): 2x knoppen, 3x Led's, micro USB en 3,5 mm audio-stekker
- Vereiste kabels: FPC-kabel, USB-micro type-B naar USB-A
- Welkomst kaart
- Mechanische koppel plaat met geïntegreerde 80/20 1010-serie koppelen

## <a name="compute-capabilities"></a>Reken mogelijkheden 

Met Azure percept audio wordt audio-invoer door gegeven via de spraak stack die wordt uitgevoerd op de CPU van de Azure percept DK-vervoerders kaart in een hybride Edge-Cloud-manier. Daarom vereist Azure percept-audio een draaggolf kaart met een besturings systeem dat ondersteuning biedt voor de spraak stack om uit te voeren. 

De geluids verwerking wordt als volgt uitgevoerd: 

- Azure percept-audio: Hiermee wordt de audio vastgelegd en geconverteerd en verzonden naar de DK en audio-stekker.

- Azure percept DK: de spraak stack voert formulier-en Echo annulering uit en verwerkt de inkomende audio om te optimaliseren voor spraak. Na de verwerking voert het een trefwoord herkennen uit.

- Cloud: verwerkt opdrachten en zinsdelen in natuurlijke taal, trefwoord verificatie en retraining. 

- Offline: als het apparaat offline is, detecteert het het tref woord en de opname status van de Internet verbinding. Een verg root geaccepteerde acceptatie frequentie voor trefwoord herkennen kan worden waargenomen omdat de verificatie van het sleutel woord in de Cloud niet kan worden uitgevoerd. 

## <a name="getting-started"></a>Aan de slag

- [Uw Azure percept DK samen stellen](./quickstart-percept-dk-unboxing.md)
- [Uw Azure percept-audio apparaat verbinden met uw Devkit](./quickstart-percept-audio-setup.md)
- [De Setup-ervaring van Azure percept DK volt ooien](./quickstart-percept-dk-set-up.md)

## <a name="build-a-no-code-prototype"></a>Een prototype zonder code maken

Bouw een [oplossing zonder code voor spraak](./tutorial-no-code-speech.md) in [Azure percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) met behulp van Azure percept-sjablonen voor de horeca, de gezondheids zorg, de inventaris en de automobiel scenario's.

### <a name="manage-your-no-code-speech-solution"></a>Uw oplossing voor spraak op geen code beheren

- [Uw Voice Assistant configureren in azure percept Studio](./how-to-manage-voice-assistant.md)
- [Uw Voice Assistant configureren in IOT hub](./how-to-configure-voice-assistant.md)
- [Problemen met Azure percept-audio oplossen](./troubleshoot-audio-accessory-speech-module.md)

## <a name="additional-technical-information"></a>Aanvullende technische informatie

- [Azure percept-audio gegevens blad](./azure-percept-audio-datasheet.md)
- [Gedrag van knop en LED](./audio-button-led-behavior.md)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een Azure percept-audio apparaat kopen vanuit de online winkel van micro soft](https://go.microsoft.com/fwlink/p/?LinkId=2155270)

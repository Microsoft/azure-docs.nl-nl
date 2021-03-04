---
title: Overzicht van Azure percept-audio apparaten
description: Meer informatie over Azure percept-audio
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: 8884663b3f0e861e62f48c3aab680f0f31e74428
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102098380"
---
# <a name="introduction-to-azure-percept-audio"></a>Inleiding tot Azure percept-audio

Azure percept-audio is een accessoire apparaat waarmee u spraak AI-mogelijkheden kunt toevoegen aan Azure percept DK. Het bevat een vooraf geconfigureerde geluids processor en een lineaire matrix met vier microfoons, waarmee u spraak opdrachten, trefwoord herkennen en far field speech kunt Toep assen op lokale luisterende apparaten met behulp van Azure Cognitive Services. Met Azure percept-audio kunnen apparaat-fabrikanten Azure percept DK uitbreiden tegen de visuele mogelijkheden tot nieuwe, slimme spraak geactiveerde apparaten. Het is volledig geïntegreerd met Azure percept DK, Azure percept Studio en andere Azure Edge-beheer Services. Het is beschikbaar voor aankoop in de [online winkel van micro soft](https://go.microsoft.com/fwlink/p/?LinkId=2155270).

:::image type="content" source="./media/overview-azure-percept-audio/percept-audio.png" alt-text="Azure percept-audio apparaat.":::

## <a name="azure-percept-audio-components"></a>Azure percept audio-onderdelen

Azure percept-audio bevat de volgende belang rijke onderdelen:

- Productie-klaar Azure percept-audio apparaat (SoM) met vier microfoons lineaire matrix en geluids verwerking door een XMOS-codec
- Ontwikkel aars (tussen het deel van een ontwikkelaar) (inclusief twee knoppen, 3x Led's, micro USB en 3,5 mm audio-stekker)
- Vereiste kabels: FPC-kabel, USB-micro type-B naar USB-A
- Welkomst kaart
- Mechanische koppel plaat met geïntegreerde 80/20 1010-serie koppelen

## <a name="compute-capabilities"></a>Reken mogelijkheden 

Met Azure percept audio wordt de audio-invoer door de spraak stack door gegeven die wordt uitgevoerd op de CPU van de vervoerders van Azure percept DK in een hybride Edge-Cloud-manier. Daarom vereist Azure percept-audio een draaggolf kaart met een besturings systeem dat ondersteuning biedt voor de spraak stack om uit te voeren. 

De verwerking wordt als volgt uitgevoerd: 

- Azure percept-audio: Hiermee wordt de functie voor het annuleren van echo's en ECHO uitgevoerd en wordt de inkomende audio verwerkt om te optimaliseren voor spraak en verzonden naar de DK.  

- Azure percept DK: de spraak stack voert het tref woord herkennen uit.  

- Cloud: verwerkt opdrachten en zinsdelen in natuurlijke taal, trefwoord verificatie en retraining. 

- Offline: als het apparaat offline is, detecteert het het tref woord en de opname status van de Internet verbinding. Een verg root geaccepteerde acceptatie frequentie voor trefwoord herkennen kan worden waargenomen omdat de verificatie van het sleutel woord in de Cloud niet kan worden uitgevoerd. 

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

## <a name="build-a-no-code-prototype"></a>Een prototype zonder code maken

Bouw een [oplossing zonder code](./tutorial-no-code-speech.md) met behulp van Azure percept Voice Assistant-sjablonen voor horeca-, gezondheids zorg-, inventaris-en automobiel scenario's.

### <a name="manage-your-no-code-speech-solution"></a>Uw oplossing voor spraak op geen code beheren

- [Uw Voice Assistant configureren in IOT hub](./how-to-manage-voice-assistant.md)
- [Uw Voice Assistant configureren in azure percept Studio](./how-to-configure-voice-assistant.md)
- [Problemen met Azure percept-audio oplossen](./troubleshoot-audio-accessory-speech-module.md)

## <a name="additional-technical-information"></a>Aanvullende technische informatie

- [Azure percept-audio gegevens blad](./azure-percept-audio-datasheet.md)
- [Gedrag van knop en LED](./audio-button-led-behavior.md)

## <a name="next-steps"></a>Volgende stappen

Bestel een Azure percept-audio apparaat in de [online winkel van micro soft](https://go.microsoft.com/fwlink/p/?LinkId=2155270).

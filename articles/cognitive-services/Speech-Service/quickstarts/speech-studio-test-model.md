---
title: Een model testen met behulp van audiobestanden - Speech Studio
titleSuffix: Azure Cognitive Services
description: In deze instructies gebruikt u Speech Studio om spraakherkenning in een audiobestand te testen.
services: cognitive-services
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 02/12/2021
ms.author: v-demjoh
ms.openlocfilehash: 93a0c171497faa9e87457a03da0b78003feaa533
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100362264"
---
# <a name="test-a-model-using-an-audio-file-in-speech-studio"></a>Een model testen met behulp van een audiobestand in Speech Studio

In deze instructies gebruikt u Speech Studio om spraak uit een audiobestand om te zetten in tekst. Met Speech Studio kunt u spraakherkenningsmodellen testen, vergelijken, verbeteren en implementeren, met behulp van gerelateerde tekst, audio met door mensen gelabelde transcripten, en uitspraakrichtlijnen die u opgeeft.

## <a name="prerequisites"></a>Vereisten

Voordat u Speech Studio gebruikt, [volgt u deze instructies om een Azure-account te maken en u te abonneren op de spraak service](../custom-speech-overview.md#set-up-your-azure-account). Dit uniforme abonnement geeft u toegang tot spraak naar tekst, tekst naar spraak, spraak omzetting en de speech Studio.

## <a name="download-an-audio-file"></a>Een audiobestand downloaden

Volg deze stappen om een audiobestand met spraak te downloaden en dit te verpakken als ZIP-bestand.

1. Download het **[wav-voorbeeldbestand](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav)** via deze koppeling door met de rechtermuisknop op de koppeling te klikken en **Koppeling opslaan als** te kiezen. Klik op **Opslaan** om het `whatstheweatherlike.wav`-bestand te downloaden.
2. Maak met behulp van een bestandenverkenner of terminalvenster met een ZIP-programma, een ZIP-bestand met de naam `whatstheweatherlike.zip` dat het `whatstheweatherlike.wav`-bestand bevat dat u hebt gedownload. In Windows opent u Windows Verkenner, gaat u naar de map `Downloads` en klikt u met de rechtermuisknop op `whatstheweatherliike.wav`. Klik op **Verzenden naar**, klik op **Gecomprimeerde (gezipte) map** en druk op Enter om de standaardbestandsnaam te accepteren.

## <a name="create-a-project-in-the-speech-studio"></a>Een project maken in de speech Studio

Volg deze stappen om een project te maken dat uw zip met één audiobestand bevat.

1. Open [Speech Studio](https://speech.microsoft.com/) en klik op **Nieuw project**. Typ een naam voor dit project en klik op **Maken**. Uw project wordt weergegeven in de Custom Speech-lijst.
2. Klik op de naam van uw project. Klik op het tabblad Gegevens op **Gegevens uploaden**.
3. Het type spraakgegevens is standaard **Alleen audio**, klik daarom op **Volgende**.
4. Geef de nieuwe spraakgegevensset de naam `MyZipOfAudio`, en klik op **Volgende**.
5. Klik op **Door bestanden bladeren...** , ga naar het `whatstheweatherlike.zip`-bestand en klik op **Openen**.
6. Klik op de knop **Uploaden**. Het ZIP-bestand wordt met de browser geüpload naar Speech Studio, en de inhoud wordt in Speech Studio verwerkt.

## <a name="test-a-model"></a>Een model testen

Nadat de inhoud van het ZIP-bestand is verwerkt in Speech Studio, kunt u de bronaudio afspelen tijdens het controleren van de transcriptie om fouten of weglatingen op te sporen. Volg deze stappen om de kwaliteit van de transcriptie te controleren in de browser.

1. Klik op het tabblad **Testen** en klik op **Test toevoegen**.
2. In deze test wordt de kwaliteit van gegevens met het kenmerk Alleen-audio geïnspecteerd. Klik daarom op **Volgende** om dit testtype te accepteren.
3. Geef deze test de naam `MyModelTest`, en klik op **Volgende**.
4. Klik op het keuzerondje links van `MyZipOfAudio`, en klik op **Volgende**.
5. De vervolgkeuzelijst **Model 1** is standaard ingesteld op het meest recente herkenningsmodel. Klik daarom op **Maken**. Nadat de inhoud van uw audiogegevensset is verwerkt, wordt de status van de test gewijzigd in **Geslaagd**.
6. Klik op **MyModelTest**. De resultaten van de spraakherkenning worden weergegeven. Klik op de naar rechts wijzende driehoek binnen de cirkel om de audio te beluisteren, en vergelijk wat u hoort, met de tekst bij de cirkel.

## <a name="download-detailed-results"></a>Gedetailleerde resultaten downloaden

U kunt bestanden die transcripties beschrijven, met veel meer details downloaden. De bestanden bevatten lexicale spraakvorm in uw audiobestanden, en JSON-bestanden die details over offset, duur en transcriptiebetrouwbaarheid bevatten voor elk woord. Volg deze stappen om deze bestanden te zien.

1. Klik op **Downloaden**.
2. Schakel in het dialoogvenster Downloaden het selectievakje **Audio** uit, en klik op **Downloaden**.
3. Pak het gedownloade ZIP-bestand uit, en controleer de uitgepakte bestanden.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het verbeteren van de nauwkeurigheid van spraakherkenning door [een aangepast model te trainen](../how-to-custom-speech-test-and-train.md).
---
title: De gegevens kwaliteit van de Custom Speech speech-service controleren
titleSuffix: Azure Cognitive Services
description: Custom Speech biedt hulp middelen waarmee u de herkennings kwaliteit van een model visueel kunt controleren door audio gegevens te vergelijken met het bijbehorende herkennings resultaat. U kunt geüploade audio afspelen en bepalen of het gegeven herkennings resultaat juist is.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: erhopf
ms.openlocfilehash: 9ce0d3a06846cbc3aa37ab836564150e6f2c34ee
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100362808"
---
# <a name="inspect-custom-speech-data"></a>Custom Speech-gegevens controleren

> [!NOTE]
> Op deze pagina wordt ervan uitgegaan dat u [test gegevens voorbereiden voor Custom speech](./how-to-custom-speech-test-and-train.md) hebt gelezen en een gegevensset hebt geüpload voor inspectie.

Custom Speech biedt hulp middelen waarmee u de herkennings kwaliteit van een model visueel kunt controleren door audio gegevens te vergelijken met het bijbehorende herkennings resultaat. Vanuit de [Speech Studio](https://speech.microsoft.com/customspeech)kunt u geüploade audio afspelen en bepalen of het gegeven herkennings resultaat juist is. Met dit hulp programma kunt u de kwaliteit van het spraak-naar-tekst model van micro soft-basis lijn controleren, een getraind aangepast model controleren of transcriptie vergelijken met twee modellen.

In dit document leert u hoe u de kwaliteit van het spraak-naar-tekst model van micro soft en/of aangepaste modellen die u hebt getraind, visueel kunt controleren. U leert ook hoe u de online transcriptie-editor gebruikt om gelabelde audio gegevens sets te maken en te verfijnen.

## <a name="create-a-test"></a>Een test maken

Volg deze instructies om een test te maken:

1. Meld u aan bij de [Speech Studio](https://speech.microsoft.com/customspeech).
2. Navigeer naar **spraak-naar-tekst > Custom Speech > [name of project] > te testen**.
3. Klik op **test toevoegen**.
4. Selecteer **kwaliteit controleren (alleen audio gegevens)**. Geef de test een naam, beschrijving en selecteer uw audio gegevensset.
5. Selecteer Maxi maal twee modellen die u wilt testen.
6. Klik op **Create**.

Nadat een test is gemaakt, kunt u zien hoe een model de door u opgegeven audio gegevensset transcribeert of resultaten vergelijken van twee modellen naast elkaar.

[!INCLUDE [service-pricing-advisory](includes/service-pricing-advisory.md)]

## <a name="side-by-side-model-comparisons"></a>Vergelijking van modellen naast elkaar

Wanneer de test status is _geslaagd_, klikt u op de naam van het test item om de details van de test te bekijken. Deze detail pagina bevat een lijst met alle uitingen in uw gegevensset en toont de herkennings resultaten van de twee modellen die u vergelijkt.

Als u de gelijktijdige vergelijking wilt controleren, kunt u verschillende fout typen met inbegrip van invoegen, verwijderen en vervangen. Als u naar de audio luistert en de herkennings resultaten in elke kolom vergelijkt (met transcriptie en de resultaten van twee spraak-naar-tekst modellen), kunt u bepalen welk model aan uw behoeften voldoet en waar verbeteringen nodig zijn.

Het is handig om te controleren welk spraakherkennings model het meest geschikt is voor een toepassing. Volg de instructies in [evalueren nauw keurigheid](how-to-custom-speech-evaluate-data.md)voor een objectieve maat regel voor nauw keurigheid, voor het vereisen van getranscribeerde audio.

## <a name="online-transcription-editor"></a>Online transcriptie-editor

Met de online transcriptie-editor kunt u eenvoudig werken met audio-transcripties in Custom Speech. De belangrijkste use cases van de editor zijn als volgt: 

* U hebt alleen audio gegevens, maar u wilt nauw keuriger audio + met menselijke labels gelabelde gegevens sets maken voor gebruik in een model training.
* U hebt al audio en gegevens sets met menselijke labels, maar er zijn fouten of defecten in de transcriptie. Met de editor kunt u de transcripties snel aanpassen om de beste nauw keurigheid van de training te krijgen.

De enige vereiste voor het gebruik van de transcriptie-editor is het uploaden van audio gegevens (alleen audio of audio + transcriptie).

### <a name="import-datasets-to-editor"></a>Gegevens sets importeren in editor

Als u gegevens wilt importeren in de editor, gaat u eerst naar **Custom Speech > [uw project] > editor**.

![Tabblad Editor](media/custom-speech/custom-speech-editor-detail.png)

Gebruik vervolgens de volgende stappen om gegevens te importeren.

1. Klik op **gegevens importeren**
1. Een nieuwe gegevensset maken en hieraan een beschrijving geven
1. Selecteer gegevens sets. Meerdere selecties worden ondersteund en u kunt alleen audio gegevens selecteren, maar ook audio en mensen met een label.
1. Voor alleen audio gegevens kunt u optioneel de standaard modellen gebruiken om automatisch machine transcriptie te genereren na het importeren in de editor
1. Klik op **importeren**

Nadat de gegevens zijn geïmporteerd, kunt u in de gegevens sets klikken en de bewerking starten.

> [!TIP]
> U kunt gegevens sets ook rechtstreeks importeren in de editor door gegevens sets te selecteren en te klikken op **exporteren naar editor**

### <a name="edit-transcription-by-listening-to-audio"></a>Transcriptie bewerken door naar audio te Luis teren

Nadat het uploaden van gegevens is geslaagd, klikt u op elke item naam om de details van de gegevens weer te geven. U kunt ook **vorige** en **volgende** gebruiken om tussen elk bestand te scha kelen.

De detail pagina bevat alle segmenten in elk audio bestand en u kunt klikken op de gewenste utterance. Voor elke utterance kunt u de audio afspelen en de transcripten bekijken, en de transcripties bewerken als u fouten bij het invoegen, verwijderen of vervangen hebt gevonden. Raadpleeg de [evaluatie van gegevens](how-to-custom-speech-evaluate-data.md) voor meer informatie over fout typen.

![Pagina editor](media/custom-speech/custom-speech-editor.png)

Nadat u wijzigingen hebt aangebracht, klikt u op de knop **Opslaan** .

### <a name="export-datasets-from-the-editor"></a>Gegevens sets exporteren uit de editor

Als u gegevens sets wilt exporteren naar het tabblad **gegevens** , gaat u naar de pagina gegevens details en klikt u op de knop **exporteren** om alle bestanden als een nieuwe gegevensset te exporteren. U kunt de bestanden ook filteren op de laatst bewerkte tijd, de duur van de audio, enzovoort, om de gewenste bestanden gedeeltelijk te selecteren. 

![Gegevens exporteren](media/custom-speech/custom-speech-editor-export.png)

De bestanden die naar gegevens worden geëxporteerd, worden gebruikt als een merk nieuwe gegevensset en zijn niet van invloed op de bestaande gegevens/training/test-entiteiten.

## <a name="next-steps"></a>Volgende stappen

- [Uw gegevens evalueren](how-to-custom-speech-evaluate-data.md)
- [Uw model trainen](how-to-custom-speech-train-model.md)
- [Uw model verbeteren](./how-to-custom-speech-evaluate-data.md)
- [Uw model implementeren](./how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>Aanvullende bronnen

- [Test gegevens voorbereiden voor Custom Speech](./how-to-custom-speech-test-and-train.md)
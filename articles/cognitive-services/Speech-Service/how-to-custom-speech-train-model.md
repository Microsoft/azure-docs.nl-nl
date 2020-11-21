---
title: Een Custom Speech model-spraak service trainen en implementeren
titleSuffix: Azure Cognitive Services
description: In dit artikel leert u hoe u Custom Speech modellen traint en implementeert. Als u een spraak-naar-tekst model wilt trainen, kunt u de nauw keurigheid van de herkenning voor het basislijn model van micro soft of een aangepast model verbeteren. Een model wordt getraind met behulp van transcripties en gerelateerde tekst.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: trbye
ms.openlocfilehash: 9193da2f3841bb94aa395399c31d7fe826c395e0
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025596"
---
# <a name="train-and-deploy-a-custom-speech-model"></a>Een Custom Speech-model trainen en implementeren

In dit artikel leert u hoe u Custom Speech modellen traint en implementeert. Training van een model voor spraak naar tekst kan de nauw keurigheid van de herkenning voor het basislijn model van micro soft verbeteren. Een model wordt getraind met behulp van transcripties en gerelateerde tekst. Deze gegevens sets en eerder geüploade audio gegevens worden gebruikt voor het verfijnen en trainen van het spraak-naar-tekst model.

## <a name="use-training-to-resolve-accuracy-issues"></a>Training gebruiken om nauw keurige problemen op te lossen

Als u herkennings problemen ondervindt met een basis model, kan het gebruik van transcripten met menselijke labels en gerelateerde gegevens voor het trainen van een aangepast model helpen de nauw keurigheid te verbeteren. Gebruik deze tabel om te bepalen welke gegevensset moet worden gebruikt voor het oplossen van uw probleem (en):

| Gebruiksvoorbeeld | Gegevenstype |
| -------- | --------- |
| Verbeter nauw keurigheid van herkenning op branchespecifieke woorden lijst en grammatica, zoals medische terminologie of het jargon. | Gerelateerde tekst (zinnen/uitingen) |
| Definieer de fonetische en weer gegeven vorm van een woord of term met een niet-standaard uitspraak, zoals product namen of acroniemen. | Gerelateerde tekst (uitspraak) |
| Verbeter de herkennings nauwkeurigheid op spraak stijlen, accenten of specifieke achtergrond ruis. | Audio en Transcripten met menselijke labels |

## <a name="train-and-evaluate-a-model"></a>Een model trainen en evalueren

De eerste stap voor het trainen van een model is het uploaden van trainings gegevens. Gebruik voor [bereiding en test uw gegevens](./how-to-custom-speech-test-and-train.md) voor stapsgewijze instructies voor het voorbereiden van transcripties en gerelateerde tekst met menselijke labels (uitingen en uitspraak). Nadat u de trainings gegevens hebt geüpload, volgt u deze instructies om te beginnen met het trainen van uw model:

1. Meld u aan bij de [Custom speech Portal](https://speech.microsoft.com/customspeech).
2. Navigeer naar **spraak-naar-tekst > Custom Speech > [name of project] > training**.
3. Klik op **model trainen**.
4. Geef vervolgens uw training een **naam** en **Beschrijving**.
5. Selecteer in de vervolg keuzelijst **scenario en basislijn model** het scenario dat het meest geschikt is voor uw domein. Als u niet zeker weet welk scenario u moet kiezen, selecteert u **Algemeen**. Het basis model is het begin punt voor training. Het meest recente model is doorgaans de beste keuze.
6. Kies op de pagina **trainings gegevens selecteren** een of meer transcriptie-gegevens sets met audio en menselijke labels die u wilt gebruiken voor de training.
7. Zodra de training is voltooid, kunt u ervoor kiezen om nauw keuriger tests uit te voeren op het nieuwe getrainde model. Deze stap is optioneel.
8. Selecteer **maken** om uw aangepaste model te maken.

In de tabel training wordt een nieuw item weer gegeven dat overeenkomt met dit nieuwe model. In de tabel wordt ook de volgende status weer gegeven: verwerken, geslaagd, mislukt.

Bekijk de nauw keurigheid van het Custom Speech model te evalueren en [te](how-to-custom-speech-evaluate-data.md) verbeteren. Als u ervoor hebt gekozen om nauw keurigheid te testen, is het belang rijk dat u een akoestische gegevensset selecteert die afwijkt van het model dat u hebt gebruikt voor de prestaties van het model.

## <a name="deploy-a-custom-model"></a>Aangepaste model implementeren

Nadat u gegevens hebt geüpload en geinspectet, de nauw keurigheid hebt geëvalueerd en een aangepast model hebt getraind, kunt u een aangepast eind punt implementeren voor gebruik met uw apps, hulpprogram ma's en producten. 

Als u een nieuw aangepast eind punt wilt maken, meldt u zich aan bij de [Custom speech Portal](https://speech.microsoft.com/customspeech) en selecteert u **implementatie** in het menu Custom speech boven aan de pagina. Als dit de eerste keer is dat u uitvoert, ziet u dat er geen eind punten in de tabel staan. Nadat u een eind punt hebt gemaakt, kunt u deze pagina gebruiken om elk geïmplementeerd eind punt bij te houden.

Selecteer vervolgens **eind punt toevoegen** en voer een **naam** en **Beschrijving** in voor het aangepaste eind punt. Selecteer vervolgens het aangepaste model dat u aan dit eind punt wilt koppelen. Op deze pagina kunt u ook logboek registratie inschakelen. Met logboek registratie kunt u eindpunt verkeer bewaken. Als deze is uitgeschakeld, wordt verkeer niet opgeslagen.

![Een model implementeren](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> Vergeet niet om akkoord te gaan met de gebruiks voorwaarden en prijs gegevens.

Selecteer vervolgens **maken**. Met deze actie keert u terug naar de **implementatie** pagina. De tabel bevat nu een vermelding die overeenkomt met uw aangepaste eind punt. De status van het eind punt toont de huidige status. Het kan tot 30 minuten duren om een nieuw eind punt te instantiëren met uw aangepaste modellen. Wanneer de status van de implementatie verandert in **voltooid**, is het eind punt klaar voor gebruik.

Nadat het eind punt is geïmplementeerd, wordt de naam van het eind punt weer gegeven als een koppeling. Klik op de koppeling om informatie weer te geven die specifiek is voor uw eind punt, zoals de eindpunt sleutel, eind punt-URL en voorbeeld code.

## <a name="view-logging-data"></a>Logboek gegevens weer geven

Logboek gegevens kunnen worden gedownload onder **eindpunt > Details**.
> [!NOTE]
>De logboek gegevens zijn 30 dagen beschikbaar op micro soft-opslag in eigendom en worden daarna verwijderd. Als een opslag account van een klant is gekoppeld aan het cognitieve Services-abonnement, worden de logboek gegevens niet automatisch verwijderd.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie [over het gebruik van uw aangepaste model](how-to-specify-source-language.md).

## <a name="additional-resources"></a>Aanvullende bronnen

- [Uw gegevens voorbereiden en testen](./how-to-custom-speech-test-and-train.md)
- [Uw gegevens controleren](how-to-custom-speech-inspect-data.md)
- [Uw gegevens evalueren](how-to-custom-speech-evaluate-data.md)

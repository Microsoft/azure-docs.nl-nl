---
title: Een Custom Speech model-spraak service trainen en implementeren
titleSuffix: Azure Cognitive Services
description: Leer hoe u Custom Speech modellen traint en implementeert. Als u een spraak-naar-tekst model wilt trainen, kunt u de nauw keurigheid van de herkenning verbeteren voor het micro soft-basislijn model of voor een aangepast model.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: trbye
ms.openlocfilehash: 4da93503c32e380adb82028e7c5e11dddb247d6f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100373365"
---
# <a name="train-and-deploy-a-custom-speech-model"></a>Een Custom Speech-model trainen en implementeren

In dit artikel leert u hoe u Custom Speech modellen traint en implementeert. Als u een spraak-naar-tekst model wilt trainen, kunt u de nauw keurigheid van herkenning voor het micro soft-basislijn model verbeteren. U gebruikt gelabelde transcripties en gerelateerde tekst voor het trainen van een model. Deze gegevens sets, samen met eerder geüploade audio gegevens, worden gebruikt voor het verfijnen en trainen van het spraak-naar-tekst model.

## <a name="use-training-to-resolve-accuracy-problems"></a>Training gebruiken om nauw keurige problemen op te lossen

Als u herkennings problemen ondervindt met een basis model, kunt u transcripten met menselijke labels en gerelateerde gegevens gebruiken om een aangepast model te trainen en de nauw keurigheid te verbeteren. Gebruik deze tabel om te bepalen welke gegevensset moet worden gebruikt om uw problemen op te lossen:

| Gebruiksvoorbeeld | Gegevenstype |
| -------- | --------- |
| Verbeter nauw keurigheid van herkenning op branchespecifieke vocabulaire en grammatica, zoals medische terminologie of IT-jargon | Gerelateerde tekst (zinnen/uitingen) |
| De fonetische en weer gegeven vorm definiëren van een woord of term met een niet-standaard uitspraak, zoals product namen of acroniemen | Gerelateerde tekst (uitspraak) |
| Nauw keurigheid van herkenning op spraak stijlen, accenten of specifieke achtergrond geluiden verbeteren | Audio en Transcripten met menselijke labels |

## <a name="train-and-evaluate-a-model"></a>Een model trainen en evalueren

De eerste stap voor het trainen van een model is het uploaden van trainings gegevens. Zie [uw gegevens voorbereiden en testen](./how-to-custom-speech-test-and-train.md) voor stapsgewijze instructies voor het voorbereiden van transcripties en gerelateerde tekst met menselijke labels (uitingen en uitspraak). Nadat u trainings gegevens uploadt, volgt u deze instructies om te beginnen met het trainen van uw model:

1. Meld u aan bij de [Custom speech Portal](https://speech.microsoft.com/customspeech). Als u van plan bent om een model met audio + Transcriptie gegevens sets met menselijke labels te trainen, kiest u een spraak abonnement in een [regio met speciale hardware](custom-speech-overview.md#set-up-your-azure-account) voor training.
2. Ga naar **spraak-naar-tekst**  >  **Custom speech**  >  **[name of project]**  >  **training**.
3. Selecteer **model voor Train**.
4. Geef uw training een **naam** en **Beschrijving**.
5. Selecteer in de lijst **scenario en basislijn model** het scenario dat het meest geschikt is voor uw domein. Als u niet zeker weet welk scenario u moet kiezen, selecteert u **Algemeen**. Het basis model is het begin punt voor training. Het meest recente model is doorgaans de beste keuze.
6. Kies op de pagina **trainings gegevens selecteren** een of meer gerelateerde tekst gegevens sets of audio en transcriptie gegevens sets met menselijke labels die u wilt gebruiken voor de training.

> [!NOTE]
> Wanneer u een nieuw model traint, begint u met Verwante tekst. training met audio en menselijk gelabelde transcriptie kan veel langer duren **(Maxi maal [enkele dagen](how-to-custom-speech-evaluate-data.md#add-audio-with-human-labeled-transcripts)**).

> [!NOTE]
> Niet alle basis modellen ondersteunen training met audio. Als een basis model dit niet ondersteunt, gebruikt de spraak service alleen de tekst uit de transcripten en wordt de audio genegeerd. Zie [taal ondersteuning](language-support.md#speech-to-text) voor een lijst met basis modellen die ondersteuning bieden voor training met audio gegevens.

> [!NOTE]
> Als u het basis model dat wordt gebruikt voor de training wijzigt en u audio hebt in de trainings-gegevensset, moet u *altijd* controleren of het nieuwe basis model [training voor audio gegevens ondersteunt](language-support.md#speech-to-text). Als het eerder gebruikte basis model geen training met audio gegevens ondersteunt, en de training-gegevensset bevat audio, wordt de trainings tijd met het nieuwe basis model **drastisch** verhoogd en kan het enkele uren tot enkele dagen en langer duren. Dit geldt vooral als uw abonnement op de spraak service zich **niet** in een regio bevindt [met de speciale hardware](custom-speech-overview.md#set-up-your-azure-account) voor training.
>
> Als u het probleem voor komt dat in de bovenstaande alinea wordt beschreven, kunt u de trainings tijd snel verlagen door de hoeveelheid audio in de gegevensset te verminderen of deze volledig te verwijderen en alleen de tekst te verlaten. De laatste optie wordt sterk aanbevolen als uw abonnement op de spraak service zich **niet** in een regio bevindt [met de speciale hardware](custom-speech-overview.md#set-up-your-azure-account) voor training.

7. Nadat de training is voltooid, kunt u nauw keurig testen uitvoeren op het nieuwe getrainde model. Deze stap is optioneel.
8. Selecteer **maken** om uw aangepaste model te maken.

In de tabel **training** wordt een nieuw item weer gegeven dat overeenkomt met het nieuwe model. In de tabel wordt ook de status weer gegeven: **verwerkt**, **geslaagd** of **mislukt**.

Bekijk de nauw keurigheid van het Custom Speech model te evalueren en [te](how-to-custom-speech-evaluate-data.md) verbeteren. Als u ervoor kiest om de nauw keurigheid te testen, is het belang rijk dat u een akoestische gegevensset selecteert die afwijkt van het model dat u hebt gebruikt bij uw modellen om een realistische indruk te krijgen van de prestaties van het model.

> [!NOTE]
> Zowel basis modellen als aangepaste modellen kunnen alleen tot een bepaalde datum worden gebruikt (Zie [levens cyclus van modellen](custom-speech-overview.md#model-lifecycle)). In speech Studio wordt deze datum weer gegeven in de kolom **verloop** voor elk model en eind punt. Na deze datum kan een eind punt of een batch-transcriptie mislukken of worden terugvallen op het basis model.
>
> Train uw model opnieuw met het meest recente basis model om te profiteren van nauw keurigere verbeteringen en om te voor komen dat uw model verloopt.

## <a name="deploy-a-custom-model"></a>Aangepaste model implementeren

Nadat u gegevens hebt geüpload en geïnspecteerd, de nauw keurigheid evalueert en een aangepast model traint, kunt u een aangepast eind punt implementeren voor gebruik met uw apps, hulpprogram ma's en producten. 

Als u een aangepast eind punt wilt maken, meldt u zich aan bij de [Custom speech Portal](https://speech.microsoft.com/customspeech). Selecteer **implementatie** in het menu **Custom speech** boven aan de pagina. Als dit de eerste keer is dat u uitvoert, ziet u dat er geen eind punten in de tabel staan. Nadat u een eind punt hebt gemaakt, gebruikt u deze pagina om elk geïmplementeerd eind punt bij te houden.

Selecteer vervolgens **eind punt toevoegen** en voer een **naam** en **Beschrijving** in voor het aangepaste eind punt. Selecteer vervolgens het aangepaste model dat u wilt koppelen aan het eind punt.  U kunt ook logboek registratie inschakelen op deze pagina. Met logboek registratie kunt u eindpunt verkeer bewaken. Als logboek registratie is uitgeschakeld, wordt verkeer niet opgeslagen.

![Scherm afbeelding met de nieuwe eindpunt pagina.](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> Vergeet niet om akkoord te gaan met de gebruiks voorwaarden en prijs gegevens.

Selecteer vervolgens **maken**. Met deze actie keert u terug naar de **implementatie** pagina. De tabel bevat nu een vermelding die overeenkomt met uw aangepaste eind punt. De status van het eind punt toont de huidige status. Het kan tot 30 minuten duren om een nieuw eind punt te instantiëren met uw aangepaste modellen. Wanneer de status van de implementatie verandert in **voltooid**, is het eind punt klaar voor gebruik.

Nadat het eind punt is geïmplementeerd, wordt de naam van het eind punt weer gegeven als een koppeling. Selecteer de koppeling om informatie weer te geven die specifiek is voor uw eind punt, zoals de eindpunt sleutel, eind punt-URL en voorbeeld code. Noteer de verval datum en werk het model van het eind punt vóór die datum bij om een ononderbroken service te garanderen.

## <a name="view-logging-data"></a>Logboek gegevens weer geven

Logboek gegevens kunnen alleen worden geëxporteerd als u naar de pagina van het eind punt gaat onder **implementaties**.
> [!NOTE]
>Logboek gegevens zijn 30 dagen beschikbaar op opslag van micro soft-eigendom. Deze wordt daarna verwijderd. Als een opslag account van een klant aan het Cognitive Services-abonnement is gekoppeld, worden de logboek gegevens niet automatisch verwijderd.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het gebruik van uw aangepaste model](how-to-specify-source-language.md)

## <a name="additional-resources"></a>Aanvullende bronnen

- [Uw gegevens voorbereiden en testen](./how-to-custom-speech-test-and-train.md)
- [Uw gegevens controleren](how-to-custom-speech-inspect-data.md)
- [Uw gegevens evalueren](how-to-custom-speech-evaluate-data.md)

---
title: Custom Speech nauw keurige spraak service evalueren en verbeteren
titleSuffix: Azure Cognitive Services
description: In dit document leert u hoe u de kwaliteit van het spraak-naar-tekst model of uw aangepaste model kwantitatief kunt meten en verbeteren. Audio en menselijk gelabelde transcriptie-gegevens zijn vereist om nauw keurigheid te testen en er moet een representatieve audio van 30 minuten tot 5 uur worden opgegeven.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: trbye
ms.openlocfilehash: 54a54dccd82e4f6cfd72a1cc8a71b51f9fd4ed95
ms.sourcegitcommit: 697638c20ceaf51ec4ebd8f929c719c1e630f06f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/04/2021
ms.locfileid: "97857355"
---
# <a name="evaluate-and-improve-custom-speech-accuracy"></a>Nauwkeurigheid van Custom Speech beoordelen en verbeteren

In dit artikel leert u hoe u de nauw keurigheid van de spraak-naar-tekst modellen of uw eigen aangepaste modellen van micro soft kunt verfijnen en verbeteren. Audio en menselijk gelabelde transcriptie-gegevens zijn vereist om nauw keurigheid te testen en er moet een representatieve audio van 30 minuten tot 5 uur worden opgegeven.

## <a name="evaluate-custom-speech-accuracy"></a>Nauwkeurigheid van Custom Speech evalueren

De industrie standaard om de nauw keurigheid van het model te meten is een [woord fout](https://en.wikipedia.org/wiki/Word_error_rate) (wer). WER telt het aantal onjuiste woorden dat is geïdentificeerd tijdens de herkenning. vervolgens wordt gedeeld door het totale aantal woorden dat is opgegeven in de transcripten met menselijke labels (hieronder weer gegeven als N). Ten slotte wordt dat aantal vermenigvuldigd met 100% om de WER te berekenen.

![WER-formule](./media/custom-speech/custom-speech-wer-formula.png)

Onjuist geïdentificeerde woorden vallen in drie categorieën:

* Invoegen (I): woorden die onjuist zijn toegevoegd in de transcripten voor hypo Thesen
* Verwijdering (D): woorden die niet worden gedetecteerd in de transcripten van hypo Thesen
* Vervanging (en): woorden die zijn vervangen tussen verwijzing en hypo these

Hier volgt een voorbeeld:

![Voor beeld van onjuist geïdentificeerde woorden](./media/custom-speech/custom-speech-dis-words.png)

Als u WER-metingen lokaal wilt repliceren, kunt u sclite van [SCTK](https://github.com/usnistgov/SCTK)gebruiken.

## <a name="resolve-errors-and-improve-wer"></a>Fouten oplossen en WER verbeteren

U kunt de WER van de resultaten van de computer herkenning gebruiken om de kwaliteit te evalueren van het model dat u gebruikt met uw app, hulp programma of product. Een WER van 5%-10% wordt beschouwd als een goede kwaliteit en is klaar voor gebruik. Een WER van 20% is acceptabel, maar u kunt ook extra training overwegen. Een WER van 30% of meer heeft een slechte kwaliteit en vereist aanpassing en training.

Hoe de fouten worden gedistribueerd, is belang rijk. Wanneer er veel verwijderings fouten optreden, is dit meestal het gevolg van een zwakke geluids signaal sterkte. Om dit probleem op te lossen, moet u audio gegevens dichter bij de bron verzamelen. Invoeg fouten betekenen dat de audio is vastgelegd in een rustige omgeving en dat er crosstalk mogelijk aanwezig zijn, waardoor herkennings problemen ontstaan. Vervangings fouten worden vaak aangetroffen wanneer er een ontoereikend voor beeld van specifieke domein termen is opgegeven als een transcripties of gerelateerde tekst.

Door afzonderlijke bestanden te analyseren, kunt u bepalen welk type fouten bestaan en welke fouten uniek zijn voor een bepaald bestand. Als u problemen ondervindt op bestands niveau, kunt u verbeteringen aanrichten.

## <a name="create-a-test"></a>Een test maken

Als u de kwaliteit van het spraak-naar-tekst basislijn model van micro soft of een aangepast model dat u hebt getraind wilt testen, kunt u twee modellen naast elkaar vergelijken om de nauw keurigheid te evalueren. De vergelijking omvat WER-en herkennings resultaten. Normaal gesp roken wordt een aangepast model vergeleken met het basis model van micro soft.

Modellen naast elkaar evalueren:

1. Meld u aan bij de [Custom speech Portal](https://speech.microsoft.com/customspeech).
2. Navigeer naar **spraak-naar-tekst > Custom Speech > [name of project] > te testen**.
3. Klik op **test toevoegen**.
4. Selecteer **nauw keurigheid evalueren**. Geef een naam en beschrijving op voor de test en selecteer uw transcriptie-gegevensset voor audio + met Human label.
5. Selecteer Maxi maal twee modellen die u wilt testen.
6. Klik op **Create**.

Nadat de test is gemaakt, kunt u de resultaten naast elkaar vergelijken.

### <a name="side-by-side-comparison"></a>Side-by-side-vergelijking

Zodra de test is voltooid, wordt aangegeven dat de status is gewijzigd in *geslaagd*. u vindt hier een wer-nummer voor beide modellen die in de test zijn opgenomen. Klik op de naam van de test om de detail pagina van de test te bekijken. Deze detail pagina bevat een lijst met alle uitingen in uw gegevensset, waarmee de herkennings resultaten van de twee modellen naast de transcriptie van de verzonden gegevensset worden aangegeven. Als u de gelijktijdige vergelijking wilt controleren, kunt u verschillende fout typen met inbegrip van invoegen, verwijderen en vervangen. Als u naar de audio luistert en de herkennings resultaten in elke kolom vergelijkt, waarin de transcriptie en de resultaten van twee spraak-naar-tekst modellen worden weer gegeven, kunt u bepalen welk model aan uw behoeften voldoet en waar extra training en verbeteringen zijn vereist.

## <a name="improve-custom-speech-accuracy"></a>Nauwkeurigheid van Custom Speech verbeteren

Scenario's voor spraak herkenning variëren per audio kwaliteit en taal (woordenlijst en spraak stijl). In de volgende tabel worden vier algemene scenario's besproken:

| Scenario | Audio kwaliteit | Vocabulaire | Stijl van spraak |
|----------|---------------|------------|----------------|
| Callcenter | 8 kHz kan twee mensen op 1 audio kanaal zijn, kan worden gecomprimeerd | Beperkt, uniek voor domein en producten | Gesprek, los gestructureerd |
| Voice Assistant (zoals Cortana of een station-through-venster) | Hoog, 16 kHz | Entiteits zwaar (song titels, producten, locaties) | Duidelijk geformuleerde woorden en zinsdelen |
| Dicteren (chat bericht, notities, zoeken) | Hoog, 16 kHz | Variabele | Notities maken |
| Video closed captioning | Variërend, inclusief gebruik van een microfoon, toegevoegde muziek | Variërend, van vergaderingen, geciteerde spraak, muzikale Song teksten | Lezen, voor bereid of los gestructureerd |

In verschillende scenario's worden verschillende kwaliteits resultaten gegenereerd. In de volgende tabel ziet u hoe de inhoud van deze vier scenario's de snelheid van het [woord fout (wer)](how-to-custom-speech-evaluate-data.md)in beslag nemen. In de tabel ziet u welke fout typen het meest worden gebruikt in elk scenario.

| Scenario | Kwaliteit van spraak herkenning | Invoeg fouten | Verwijderings fouten | Vervangings fouten |
|----------|----------------------------|------------------|-----------------|---------------------|
| Callcenter | Gemiddeld (< 30% WER) | Laag, behalve wanneer anderen op de achtergrond praten | Kan hoog zijn. Call centers kunnen ruis opdoen en overlappende luid sprekers kunnen het model verwarren | Gemiddeld. De namen van producten en personen kunnen deze fouten veroorzaken |
| Spraakassistent | Hoog (kan worden < 10% WER) | Beperkt | Beperkt | Gemiddeld, vanwege de titels van het nummer, product namen of locaties |
| Dicteren | Hoog (kan worden < 10% WER) | Beperkt | Beperkt | Hoog |
| Video closed captioning | Is afhankelijk van het video type (kan < 50% WER) | Beperkt | Kan hoog zijn als gevolg van muziek, ruis, microfoon kwaliteit | Jargon kan deze fouten veroorzaken |

Door de onderdelen van de WER te bepalen (aantal invoeg-, verwijderings-en vervangings fouten) kunt u bepalen welk soort gegevens u wilt toevoegen om het model te verbeteren. Gebruik de [Custom speech Portal](https://speech.microsoft.com/customspeech) om de kwaliteit van een basis model weer te geven. De portal rapporteert Invoeg-, vervangings-en verwijderings fout tarieven die worden gecombineerd in het WER-kwaliteits tarief.

## <a name="improve-model-recognition"></a>Model herkenning verbeteren

U kunt herkennings fouten verminderen door trainings gegevens toe te voegen aan de [Custom speech Portal](https://speech.microsoft.com/customspeech). 

Plan het aangepaste model hand matig door bron materiaal toe te voegen. Uw aangepaste model vereist extra training om u te informeren over wijzigingen aan uw entiteiten. U hebt bijvoorbeeld updates nodig voor product namen, namen van nummers of nieuwe service locaties.

In de volgende secties wordt beschreven hoe elk soort aanvullende trainings gegevens fouten kan verminderen.

### <a name="add-related-text-sentences"></a>Verwante tekst zinnen toevoegen

Wanneer u een nieuw aangepast model traint, begint u met het toevoegen van gerelateerde tekst om de erkenning van domein-specifieke woorden en zinsdelen te verbeteren. Verwante tekst zinnen kunnen de vervangings fouten met betrekking tot de fout herkenning van veelvoorkomende woorden en toepassingsspecifieke woorden in de context verminderen. Domein-specifieke woorden kunnen ongebruikelijk of opgemaakte woorden zijn, maar de uitspraak moet duidelijk zijn om te worden herkend.

> [!NOTE]
> Vermijd Verwante tekst zinnen die ruis bevatten, zoals niet-herken bare tekens of woorden.

### <a name="add-audio-with-human-labeled-transcripts"></a>Audio toevoegen met transcripten met menselijke labels

Audio met transcripten met menselijke labels biedt de grootste verbeteringen als de audio afkomstig is van de doel use case. Voor beelden moeten het volledige bereik van spraak hebben. Bijvoorbeeld, een Call Center voor een Retail Store krijgt de meeste oproepen over Swimwear en zonnebril tijdens zomer maanden. Controleer of uw voor beeld het volledige spraak bereik bevat dat u wilt detecteren.

Houd rekening met de volgende details:

* Custom Speech kan alleen een Word-context vastleggen om vervangings fouten, geen invoeg-of verwijderings fouten, te verminderen.
* Vermijd voor beelden die transcriptie-fouten bevatten, maar neem een verscheidenheid aan audio kwaliteit op.
* Vermijd zinnen die geen verband houden met uw probleem domein. Niet-verwante zinnen kunnen schadelijk zijn voor uw model.
* Wanneer de kwaliteit van transcripten verschilt, kunt u uitzonderlijk goede zinnen dupliceren (zoals uitstekende transcripties die sleutel zinnen bevatten) om het gewicht te verg Roten.
* De transcripten worden door de spraak service automatisch gebruikt voor het verbeteren van de herkenning van domein-specifieke woorden en zinsdelen, alsof ze zijn toegevoegd als gerelateerde tekst.
* Training met audio biedt de meeste voor delen als de audio ook moeilijk te begrijpen is voor mensen. In de meeste gevallen moet u training starten door alleen de bijbehorende tekst te gebruiken.
* Het kan enkele dagen duren voordat een trainings bewerking is voltooid. Zorg ervoor dat u uw spraak service-abonnement maakt in een [regio met speciale hardware](custom-speech-overview.md#set-up-your-azure-account) voor training om de snelheid van de training te verbeteren.

> [!NOTE]
> Niet alle basis modellen ondersteunen training met audio. Als een basis model dit niet ondersteunt, gebruikt de spraak service alleen de tekst uit de transcripten en wordt de audio genegeerd.

### <a name="add-new-words-with-pronunciation"></a>Nieuwe woorden met uitspraak toevoegen

Woorden die zijn gemaakt of die zeer gespecialiseerd zijn, kunnen unieke uitspraak hebben. Deze woorden kunnen worden herkend als het woord kan worden opgesplitst in kleinere woorden om het uit te spreken. Als u bijvoorbeeld **Xbox** wilt herkennen, spreekt u uit als **X-vak**. Met deze methode wordt de algehele nauw keurigheid niet verbeterd, maar kan de herkenning van deze tref woorden toenemen.

> [!NOTE]
> Deze techniek is op dit moment alleen beschikbaar voor sommige talen. Zie aanpassing voor uitspraak in [de tabel met spraak-naar-tekst](language-support.md) voor meer informatie.

## <a name="sources-by-scenario"></a>Bronnen op scenario

De volgende tabel geeft een overzicht van de scenario's voor spraak herkenning en een lijst met bron materialen die u kunt overwegen binnen de drie categorieën met trainings inhoud die hierboven worden vermeld.

| Scenario | Verwante tekst zinnen | Audio en Transcripten met menselijke labels | Nieuwe woorden met uitspraak |
|----------|------------------------|------------------------------|------------------------------|
| Callcenter             | Marketing documenten, website, product beoordelingen met betrekking tot Call Center-activiteit | Call Center-aanroepen, getranscribeerd door de mens | termen met een dubbel zinnige uitspraak (Zie Xbox hierboven) |
| Spraakassistent         | zinnen weer geven met alle combi Naties van opdrachten en entiteiten | Noteer de stem opdrachten op het apparaat en transcribeer naar tekst | namen (films, liedjes, producten) met unieke uitspraak |
| Dicteren               | geschreven invoer, zoals chat berichten of e-mails | vergelijkbaar met boven | vergelijkbaar met boven |
| Video closed captioning | TV-scripts, films, marketing inhoud, video overzichten | exacte transcripten van Video's | vergelijkbaar met boven |

## <a name="next-steps"></a>Volgende stappen

* [Een model trainen en implementeren](how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>Aanvullende bronnen

* [Uw gegevens voorbereiden en testen](./how-to-custom-speech-test-and-train.md)
* [Uw gegevens controleren](how-to-custom-speech-inspect-data.md)
---
title: Gegevens voorbereiden voor de Custom Speech-spraak service
titleSuffix: Azure Cognitive Services
description: Wanneer u de nauw keurigheid van micro soft-spraak herkenning wilt testen of uw aangepaste modellen wilt trainen, hebt u audio-en tekst gegevens nodig. Op deze pagina hebben we betrekking op de typen gegevens, het gebruik en het beheer ervan.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: trbye
ms.openlocfilehash: 2e6f79643493457a587f907f2649c7ab50b963f4
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100634733"
---
# <a name="prepare-data-for-custom-speech"></a>Gegevens voorbereiden voor Custom Speech

Wanneer u de nauw keurigheid van micro soft-spraak herkenning wilt testen of uw aangepaste modellen wilt trainen, hebt u audio-en tekst gegevens nodig. Op deze pagina hebben we betrekking op de typen gegevens die een aangepast spraak model nodig heeft.

## <a name="data-diversity"></a>Gegevensdiversiteit

Tekst en audio die worden gebruikt om een aangepast model te testen en te trainen, moeten steek proeven bevatten uit een verscheidenheid aan luid sprekers en scenario's die u nodig hebt om uw model te herkennen.
Houd rekening met deze factoren bij het verzamelen van gegevens voor aangepast model testen en training:

* Uw tekst-en spraak audio gegevens moeten voldoen aan de soorten mondelinge instructies die uw gebruikers doen wanneer ze met uw model werken. Zo kan een model dat de temperatuur behoeften voor de Tempe ratuur verhoogt en verlaagt dat mensen kunnen aandoen om dergelijke wijzigingen aan te vragen.
* Uw gegevens moeten alle spraak afwijkingen bevatten die door uw model moeten worden herkend. Veel factoren kunnen spraak variëren, waaronder accenten, dialecten, taal mixen, leeftijd, geslacht, stem pitch, stress niveau en tijdstip van de dag.
* U moet voor beelden uit verschillende omgevingen (binnen en buiten, weg-ruis) van uw model toevoegen.
* Audio moet worden verzameld met behulp van hardware-apparaten die door het productie systeem worden gebruikt. Als uw model spraak moet identificeren op registratie apparaten van verschillende kwaliteit, moeten de audio gegevens die u opgeeft voor het trainen van uw model ook deze verschillende scenario's vertegenwoordigen.
* U kunt later meer gegevens aan uw model toevoegen, maar zorg ervoor dat de gegevensset gevarieerd en representatief is voor de behoeften van uw project.
* Het opnemen van gegevens die zich *niet* in uw aangepaste model herkennings behoeften bevindt, kunnen een nadelige invloed hebben op de herkennings kwaliteit. u hoeft dus geen gegevens op te nemen die uw model niet hoeft te transcriberen.

Een model dat is getraind voor een subset van scenario's kan alleen goed worden uitgevoerd in deze scenario's. Kies zorgvuldig de gegevens die het volledige bereik van scenario's voor uw aangepaste model moeten herkennen.

> [!TIP]
> Begin met kleine sets voorbeeld gegevens die overeenkomen met de taal en akoestische die uw model zal tegen komen.
> Neem bijvoorbeeld een klein, maar representatieve steek proef van de audio op dezelfde hardware en in dezelfde akoestische omgeving in uw model vindt u in productie scenario's.
> Kleine gegevens sets van de mede werker van de vertegenwoordiger kunnen problemen bloot leggen voordat u hebt geïnvesteerd in het verzamelen van een veel grotere gegevens sets voor training.

## <a name="data-types"></a>Gegevenstypen

In deze tabel worden de geaccepteerde gegevens typen vermeld, wanneer elk gegevens type moet worden gebruikt en de aanbevolen hoeveelheid. Niet elk gegevens type is vereist voor het maken van een model. De gegevens vereisten variëren, afhankelijk van het feit of u een model maakt of een training uitvoert.

| Gegevenstype | Gebruikt voor testen | Aanbevolen aantal | Gebruikt voor training | Aanbevolen aantal |
|-----------|-----------------|----------|-------------------|----------|
| [Audio](#audio-data-for-testing) | Yes<br>Gebruikt voor visuele inspectie | 5 + audio bestanden | No | N.v.t. |
| [Audio en Transcripten met menselijke labels](#audio--human-labeled-transcript-data-for-testingtraining) | Yes<br>Wordt gebruikt om de nauw keurigheid te evalueren | 0,5-5 uur audio | Yes | 1-20 uur aan audio |
| [Gerelateerde tekst](#related-text-data-for-training) | No | N.v.t. | Yes | 1-200 MB aan Verwante tekst |

Wanneer u een nieuw model traint, begint u met [Verwante tekst](#related-text-data-for-training). Met deze gegevens wordt de herkenning van speciale termen en zinsdelen al verbeterd. Training met tekst is veel sneller dan training met audio (minuten versus dagen).

Bestanden moeten worden gegroepeerd op type in een gegevensset en worden geüpload als zip-bestand. Elke gegevensset kan slechts één gegevens type bevatten.

> [!TIP]
> Overweeg om voorbeeld gegevens te gebruiken om snel aan de slag te gaan. Bekijk deze GitHub-opslag plaats voor <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/sampledata/customspeech" target="_target">voorbeeld <span class="docon docon-navigate-external x-hidden-focus"></span> Custom speech gegevens</a>

> [!NOTE]
> Niet alle basis modellen ondersteunen training met audio. Als een basis model dit niet ondersteunt, gebruikt de spraak service alleen de tekst uit de transcripten en wordt de audio genegeerd. Zie [taal ondersteuning](language-support.md#speech-to-text) voor een lijst met basis modellen die ondersteuning bieden voor training met audio gegevens. Zelfs als een basis model training met audio gegevens ondersteunt, kan de service slechts een deel van de audio gebruiken. Nog steeds alle transcripten worden gebruikt.

> [!NOTE]
> Als u het basis model dat wordt gebruikt voor de training wijzigt en u audio hebt in de trainings-gegevensset, moet u *altijd* controleren of het nieuwe basis model [training voor audio gegevens ondersteunt](language-support.md#speech-to-text). Als het eerder gebruikte basis model geen training met audio gegevens ondersteunt, en de training-gegevensset bevat audio, wordt de trainings tijd met het nieuwe basis model **drastisch** verhoogd en kan het enkele uren tot enkele dagen en langer duren. Dit geldt vooral als uw abonnement op de spraak service zich **niet** in een regio bevindt [met de speciale hardware](custom-speech-overview.md#set-up-your-azure-account) voor training.
>
> Als u het probleem voor komt dat in de bovenstaande alinea wordt beschreven, kunt u de trainings tijd snel verlagen door de hoeveelheid audio in de gegevensset te verminderen of deze volledig te verwijderen en alleen de tekst te verlaten. De laatste optie wordt sterk aanbevolen als uw abonnement op de spraak service zich **niet** in een regio bevindt [met de speciale hardware](custom-speech-overview.md#set-up-your-azure-account) voor training.

## <a name="upload-data"></a>Gegevens uploaden

Als u uw gegevens wilt uploaden, gaat u naar de <a href="https://speech.microsoft.com/customspeech" target="_blank">Speech Studio <span class="docon docon-navigate-external x-hidden-focus"></span> </a>. Klik in de portal op **gegevens uploaden** om de wizard te starten en uw eerste gegevensset te maken. U wordt gevraagd een type spraak gegevens voor uw gegevensset te selecteren voordat u uw gegevens kunt uploaden.

![Scherm opname van de Audio-Upload optie van de spraak Portal.](./media/custom-speech/custom-speech-select-audio.png)

Elke gegevensset die u uploadt, moet voldoen aan de vereisten voor het gegevens type dat u kiest. Uw gegevens moeten correct zijn geformatteerd voordat ze worden geüpload. Correct opgemaakte gegevens zorgen ervoor dat deze nauw keurig worden verwerkt door de Custom Speech-Service. De vereisten worden weer gegeven in de volgende secties.

Nadat uw gegevensset is geüpload, hebt u een aantal opties:

* U kunt naar het tabblad **testen** navigeren en alleen audio visueel controleren of audio + Transcriptie-gegevens met menselijke labels.
* U kunt naar het tabblad **training** gaan en audio en gegevens van menselijk transcriptie of gerelateerde tekst gegevens gebruiken om een aangepast model te trainen.

## <a name="audio-data-for-testing"></a>Audio gegevens voor testen

Audio gegevens zijn optimaal voor het testen van de nauw keurigheid van het spraak-naar-tekst model van micro soft of een aangepast model. Houd er rekening mee dat audio gegevens worden gebruikt voor het inspecteren van de nauw keurigheid van spraak met betrekking tot de prestaties van een specifiek model. Als u wilt weten wat de nauw keurigheid van een model is, gebruik dan [audio + met Human labels transcriptie-gegevens](#audio--human-labeled-transcript-data-for-testingtraining).

Gebruik deze tabel om ervoor te zorgen dat uw audio bestanden correct zijn ingedeeld voor gebruik met Custom Speech:

| Eigenschap                 | Waarde                 |
|--------------------------|-----------------------|
| Bestandsindeling              | RIFF (WAV)            |
| Sample frequentie              | 8.000 Hz of 16.000 Hz |
| Kanalen                 | 1 (mono)              |
| Maximum lengte per audio | 2 uur               |
| Sample-indeling            | PCM, 16-bits           |
| Archief indeling           | .zip                  |
| Maximale archief grootte     | 2 GB                  |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!TIP]
> Bij het uploaden van training en het testen van gegevens, mag de zip-bestand niet groter zijn dan 2 GB. Als u meer gegevens nodig hebt voor de training, splitst u deze op in verschillende zip-bestanden en uploadt u deze afzonderlijk. Later kunt u kiezen voor het trainen van *meerdere* gegevens sets. U kunt echter alleen testen vanuit *één* gegevensset.

Gebruik <a href="http://sox.sourceforge.net" target="_blank" rel="noopener">Sox <span class="docon docon-navigate-external x-hidden-focus"></span> </a> om audio-eigenschappen te controleren of bestaande audio om te zetten in de juiste notaties. Hieronder ziet u enkele voor beelden van de manier waarop elk van deze activiteiten kan worden uitgevoerd via de SoX-opdracht regel:

| Activiteit | Description | SoX-opdracht |
|----------|-------------|-------------|
| Audio-indeling controleren | Gebruik deze opdracht om te controleren<br>de indeling van het audio bestand. | `sox --i <filename>` |
| Audio-indeling converteren | Gebruik deze opdracht om te converteren<br>het audio bestand op één kanaal, 16-bits, 16 KHz. | `sox <input> -b 16 -e signed-integer -c 1 -r 16k -t wav <output>.wav` |

## <a name="audio--human-labeled-transcript-data-for-testingtraining"></a>Audio en transcriptie gegevens met menselijke labels voor testen/training

Als u de nauw keurigheid van de spraak-naar-tekst nauwkeurigheid van micro soft tijdens het verwerken van uw audio bestanden wilt meten, moet u transcripties (woord voor woord) van de mens voorzien voor vergelijking. Hoewel menselijke labels transcriptie vaak tijdrovend zijn, is het nood zakelijk om nauw keurigheid te evalueren en het model te trainen voor uw gebruiks voorbeelden. Houd er rekening mee dat de verbeteringen in de herkenning alleen van belang zijn voor de gegevens. Daarom is het belang rijk dat alleen transcripten van hoogwaardige kwaliteit worden geüpload.

Audio bestanden kunnen stilte aan het begin en het einde van de opname hebben. Indien mogelijk moet u ten minste een halve seconde van stilte voor en na de spraak in elk voorbeeld bestand toevoegen. Hoewel audio met een laag opname volume of storende achtergrond ruis niet nuttig is, kan het niet goed zijn om uw aangepaste model te zien. Denk altijd aan het upgraden van uw hardware en signaal verwerkings apparatuur voordat u audio voorbeelden gaat verzamelen.

| Eigenschap                 | Waarde                               |
|--------------------------|-------------------------------------|
| Bestandsindeling              | RIFF (WAV)                          |
| Sample frequentie              | 8.000 Hz of 16.000 Hz               |
| Kanalen                 | 1 (mono)                            |
| Maximum lengte per audio | 2 uur (testen)/60 s (training) |
| Sample-indeling            | PCM, 16-bits                         |
| Archief indeling           | .zip                                |
| Maximale grootte van zip         | 2 GB                                |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!NOTE]
> Bij het uploaden van training en het testen van gegevens, mag de zip-bestand niet groter zijn dan 2 GB. U kunt alleen testen vanuit *één* gegevensset, zorg ervoor dat deze binnen de juiste bestands grootte blijft. Daarnaast kan elk trainings bestand niet groter zijn dan 60 seconden, anders wordt er een fout opgetreden.

Voor het oplossen van problemen zoals het verwijderen of vervangen van woorden, is een aanzienlijke hoeveelheid gegevens vereist om de herkenning te verbeteren. Over het algemeen is het raadzaam om per woord transcripties te bieden voor ongeveer 10 tot 20 uur aan audio. De transcripties voor alle WAV-bestanden moeten worden opgenomen in één bestand met tekst zonder opmaak. Elke regel van het transcriptiebestand moet de naam van een van de audiobestanden bevatten, gevolgd door de bijbehorende transcriptie. De bestandsnaam en transcriptie moeten worden gescheiden door een tab (\t).

Bijvoorbeeld:

<!-- The following example contains tabs. Don't accidentally convert these into spaces. -->

```input
speech01.wav    speech recognition is awesome
speech02.wav    the quick brown fox jumped all over the place
speech03.wav    the lazy dog was not amused
```

> [!IMPORTANT]
> Transcriptie moet worden gecodeerd als UTF-8 BOM (Byte Order Mark).

De tekst van de transcripties wordt genormaliseerd zodat ze door het systeem kunnen worden verwerkt. Er zijn echter enkele belang rijke normalisaties die moeten worden uitgevoerd voordat u de gegevens naar de speech Studio uploadt. Voor de juiste taal die moet worden gebruikt wanneer u uw transcripties voorbereidt, Zie [How to Create a Human-gelabeld transcriptie](how-to-custom-speech-human-labeled-transcriptions.md)

Nadat u uw audio bestanden en bijbehorende transcripties hebt verzameld, pakt u ze als één ZIP-bestand in voordat u het uploadt naar de <a href="https://speech.microsoft.com/customspeech" target="_blank">Speech Studio <span class="docon docon-navigate-external x-hidden-focus"></span> </a>. Hieronder ziet u een voor beeld van een gegevensset met drie audio bestanden en een transcriptie-bestand met menselijke Labels:

> [!div class="mx-imgBorder"]
> ![Audio selecteren in de spraak Portal](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

Zie [uw Azure-account instellen](custom-speech-overview.md#set-up-your-azure-account) voor een lijst met aanbevolen regio's voor uw speech service-abonnementen. Het instellen van de spraak abonnementen in een van deze regio's vermindert de tijd die nodig is om het model te trainen. In deze regio's kan training ongeveer 10 uur aan audio per dag worden verwerkt, vergeleken met slechts één uur per dag in andere regio's. Als model training niet binnen een week kan worden voltooid, wordt het model gemarkeerd als mislukt.

Niet alle basis modellen ondersteunen training met audio gegevens. Als het basis model dit niet ondersteunt, wordt de audio door de service genegeerd en wordt alleen de tekst van de transcriptieser getraind. In dit geval is de training hetzelfde als training met Verwante tekst. Zie [taal ondersteuning](language-support.md#speech-to-text) voor een lijst met basis modellen die ondersteuning bieden voor training met audio gegevens.

## <a name="related-text-data-for-training"></a>Gerelateerde tekst gegevens voor training

Product namen of-onderdelen die uniek zijn, moeten gerelateerde tekst gegevens bevatten voor training. Gerelateerde tekst zorgt voor een juiste herkenning. Er kunnen twee typen gerelateerde tekst gegevens worden gegeven om de herkenning te verbeteren:

| Gegevenstype | Hoe deze gegevens de herkenning verbeteren |
|-----------|------------------------------------|
| Zinnen (uitingen) | Verbeter de nauw keurigheid bij het herkennen van product namen of branchespecifieke vocabulaire in de context van een zin. |
| Uitspraak | De uitspraak van ongebruikelijke termen, acroniemen of andere woorden met niet-gedefinieerde uitspraaken verbeteren. |

Zinnen kunnen worden gegeven als één tekst bestand of meerdere tekst bestanden. Gebruik voor het verbeteren van de nauw keurigheid tekst gegevens die zich dichter bij de verwachte gesp roken uitingen bevindt. Uitspraak moet worden gegeven als één tekst bestand. Alles kan worden verpakt als één ZIP-bestand en worden geüpload naar de <a href="https://speech.microsoft.com/customspeech" target="_blank">Speech <span class="docon docon-navigate-external x-hidden-focus"></span> Studio </a>.

Training met Verwante tekst wordt doorgaans binnen een paar minuten voltooid.

### <a name="guidelines-to-create-a-sentences-file"></a>Richt lijnen voor het maken van een sentence-bestand

Als u een aangepast model met behulp van zinnen wilt maken, moet u een lijst met voorbeeld uitingen opgeven. Uitingen hoeft _niet_ volledig of grammaticaal correct te zijn, maar ze moeten de gesp roken invoer die u verwacht in productie nauw keurig weer spie gelen. Als u wilt dat bepaalde voor waarden een verhoogd gewicht hebben, voegt u meerdere zinnen toe die deze specifieke voor waarden bevatten.

Als algemene richt lijn is aanpassing van modellen het meest effectief wanneer de tekst van de training zo dicht mogelijk bij de werkelijke tekst van de productie wordt verwacht. Specifiek jargon en zinsdelen van het domein dat u als doel hebt gericht, moeten worden opgenomen in de trainings tekst. Als dat mogelijk is, kunt u op een afzonderlijke regel proberen om één zin of sleutel woord te beheren. Voor tref woorden en zinsdelen die belang rijk voor u zijn (bijvoorbeeld product namen), kunt u ze een paar keer kopiëren. Maar houd er rekening mee dat u niet te veel kunt kopiëren. Dit kan van invloed zijn op het algemene herkennings aantal.

Gebruik deze tabel om ervoor te zorgen dat het gerelateerde gegevens bestand voor uitingen correct is ingedeeld:

| Eigenschap | Waarde |
|----------|-------|
| Tekstcodering | UTF-8 BOM |
| Aantal utterances per regel | 1 |
| Maximale bestandsgrootte | 200 MB |

Daarnaast wilt u rekening met de volgende beperkingen:

* Vermijd het gebruik van herhaalde tekens, woorden of groepen woorden meer dan drie keer. Bijvoorbeeld: "AAAA", "ja ja ja ja" of "dat is het IT-bestand dat het is?". De spraak service kan regels met te veel herhalingen verwijderen.
* Gebruik geen speciale tekens of UTF-8-tekens hierboven `U+00A1` .
* Uri's worden geweigerd.

### <a name="guidelines-to-create-a-pronunciation-file"></a>Richt lijnen voor het maken van een uitspraak bestand

Als er ongebruikelijke voor waarden zijn zonder standaard uitspraak dat uw gebruikers zich kunnen voordoen of gebruiken, kunt u een aangepast uitspraak bestand opgeven om de herkenning te verbeteren.

> [!IMPORTANT]
> Het is niet raadzaam om aangepaste uitspraak bestanden te gebruiken om de uitspraak van veelvoorkomende woorden te wijzigen.

Dit omvat voor beelden van een gesp roken utterance en een aangepaste uitspraak voor elk:

| Herkend/weer gegeven formulier | Gesp roken formulier |
|--------------|--------------------------|
| 3CPO | drie c p |
| CNTK | c n t k |
| IEEE | drievoudige e |

Het gesp roken formulier is de fonetische volg orde die is gespeld. Het kan bestaan uit letter, woorden, letter grepen of een combi natie van alle drie.

Aangepaste uitspraak is beschikbaar in het Engels ( `en-US` ) en Duits ( `de-DE` ). In deze tabel worden de ondersteunde tekens per taal weer gegeven:

| Taal | Landinstelling | Aantal |
|----------|--------|------------|
| Engels | `en-US` | `a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |
| Duits | `de-DE` | `ä, ö, ü, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |

Gebruik de volgende tabel om ervoor te zorgen dat het gerelateerde gegevens bestand voor uitspraak de juiste indeling heeft. Uitspraak bestanden zijn klein en mogen slechts enkele kilo bytes groot zijn.

| Eigenschap | Waarde |
|----------|-------|
| Tekstcodering | UTF-8-stuk lijst (ANSI wordt ook ondersteund voor Engels) |
| aantal uitspraakingen per regel | 1 |
| Maximale bestandsgrootte | 1 MB (1 KB voor gratis laag) |

## <a name="next-steps"></a>Volgende stappen

* [Uw gegevens controleren](how-to-custom-speech-inspect-data.md)
* [Uw gegevens evalueren](how-to-custom-speech-evaluate-data.md)
* [Uw model trainen](how-to-custom-speech-train-model.md)
* [Uw model implementeren](./how-to-custom-speech-train-model.md)
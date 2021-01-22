---
title: Batchbewerkingen van de Spraak-CLI - Spraak-service
titleSuffix: Azure Cognitive Services
description: Meer informatie over het uitvoeren van batchgewijze spraak-naar-tekstbewerkingen (spraakherkenning), batchgewijze tekst-naar-spraakbewerkingen (spraaksynthese) met de Spraak-CLI.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.author: erhopf
ms.openlocfilehash: 60cacafc89f2335b87885e4ea11dcf8992d68c3f
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540221"
---
# <a name="speech-cli-batch-operations"></a>Batchbewerkingen voor Spraak-CLI

Veelvoorkomende taken bij het gebruik van Azure Spraak-services bestaan uit batchbewerkingen. In dit artikel leert u meer over het uitvoeren van batchgewijze spraak-naar-tekstbewerkingen (spraakherkenning), batchgewijze tekst-naar-spraakbewerkingen (spraaksynthese) met de Spraak-CLI. U leert specifiek het volgende:

* Batchgewijs spraakherkenning uitvoeren op een map met audiobestanden
* Batchgewijze spraaksynthese uitvoeren door herhaaldelijk een `.tsv`-bestand te doorlopen

## <a name="batch-speech-to-text-speech-recognition"></a>Batchgewijze spraak-naar-tekst (spraakherkenning)

De Spraak-service wordt vaak gebruikt om spraak in audiobestanden te onderscheiden. In dit voorbeeld leert u hoe u een directory bij herhaling doorloopt met behulp van de Spraak-CLI om de herkenningsuitvoer voor elk `.wav`-bestand vast te leggen. De vlag `--files` wordt gebruikt om te verwijzen naar de directory waarin de audiobestanden zijn opgeslagen. Het jokerteken `*.wav` wordt gebruikt om de Spraak-CLI te laten weten om herkenning uit te voeren op elk bestand met de extensie `.wav`. De uitvoer voor elk herkenningsbestand wordt geschreven als een door tabs gescheiden waarde in `speech_output.tsv`.

> [!NOTE]
> Het argument `--threads` kan ook worden gebruikt in de volgende sectie voor `spx synthesize`-opdrachten; de beschikbare threads zijn afhankelijk van de CPU en het huidige belastingspercentage.

```console
spx recognize --files C:\your_wav_file_dir\*.wav --output file C:\output_dir\speech_output.tsv --threads 10
```

Hierna volgt een voorbeeld van de bestandsstructuur van de uitvoer.

```output
audio.input.id  recognizer.session.started.sessionid    recognizer.recognized.result.text
sample_1    07baa2f8d9fd4fbcb9faea451ce05475    A sample wave file.
sample_2    8f9b378f6d0b42f99522f1173492f013    Sample text synthesized.
```

## <a name="batch-text-to-speech-speech-synthesis"></a>Batchgewijze tekst-naar-spraak (spraaksynthese)

De eenvoudigste manier om batchgewijze tekst-naar-spraak uit te voeren, is door een nieuw `.tsv`-bestand (met door tabs gescheiden waarden) te maken en in de Spraak-CLI gebruik te maken van de opdracht `--foreach`. U kunt een `.tsv`-bestand maken met behulp van uw favoriete teksteditor, bijvoorbeeld met de naam `text_synthesis.tsv`:

>[!IMPORTANT]
> Wanneer u de inhoud van dit tekstbestand kopieert, moet u ervoor zorgen dat het bestand **tabs** in plaats van spaties tussen de bestandslocatie en de tekst heeft. Wanneer u de inhoud uit dit voorbeeld kopieert, worden tabs soms naar spaties geconverteerd, waardoor de opdracht `spx` mislukt wanneer deze wordt uitgevoerd.

```Input
audio.output    text
C:\batch_wav_output\wav_1.wav   Sample text to synthesize.
C:\batch_wav_output\wav_2.wav   Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav   Some more text to test capabilities.
```

Vervolgens voert u een opdracht uit om naar `text_synthesis.tsv` te verwijzen, voert u synthese uit op elk `text`-veld en schrijft u het resultaat naar het overeenkomstige `audio.output`-pad als een `.wav`-bestand.

```console
spx synthesize --foreach in @C:\your\path\to\text_synthesis.tsv
```

Deze opdracht is gelijk aan het uitvoeren van `spx synthesize --text "Sample text to synthesize" --audio output C:\batch_wav_output\wav_1.wav` **voor elk** record in het bestand `.tsv`.

Enkele opmerkingen:

* De kolomkoppen, `audio.output` en `text`, komen overeen met respectievelijk de opdrachtregelargumenten `--audio output` en `--text`. Opdrachtregelargumenten met meerdere delen, zoals `--audio output`, moeten in het bestand worden opgemaakt zonder spaties, zonder voorloopstreepjes en met punten die tekenreeksen scheiden, bijvoorbeeld `audio.output`. Andere bestaande opdrachtregelargumenten kunnen met dit patroon worden toegevoegd aan het bestand als aanvullende kolommen.
* Wanneer het bestand op deze manier wordt geformatteerd, hoeven er geen aanvullende argumenten te worden doorgegeven aan `--foreach`.
* Vergeet niet elke waarde in het `.tsv` te scheiden door een **tab**.

Ga echter als volgt te werk als u een `.tsv`-bestand hebt, zoals in het volgende voorbeeld, met kolomkoppen die **niet overeenkomen met** opdrachtregelargumenten:

```Input
wav_path    str_text
C:\batch_wav_output\wav_1.wav   Sample text to synthesize.
C:\batch_wav_output\wav_2.wav   Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav   Some more text to test capabilities.
```

U kunt deze veldnamen vervangen door de juiste argumenten met behulp van de volgende syntaxis in de aanroep `--foreach`. Dit is dezelfde aanroep als hierboven.

```console
spx synthesize --foreach audio.output;text in @C:\your\path\to\text_synthesis.tsv
```

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van Speech-CLI](./spx-overview.md)
* [Quickstart van Speech-CLI](./spx-basics.md)

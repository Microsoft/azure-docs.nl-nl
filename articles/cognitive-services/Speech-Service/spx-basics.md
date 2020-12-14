---
title: Basisbeginselen van de Speech CLI
titleSuffix: Azure Cognitive Services
description: Meer informatie over het gebruik van het opdrachtregelprogramma Speech CLI om met de Spraakservice te werken zonder code en met minimale setup.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: acc19d9a04909dcf0e79c93e0c8a3fb8225ee1b4
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96546898"
---
# <a name="learn-the-basics-of-the-speech-cli"></a>Meer informatie over de basisbeginselen van de Speech CLI

In dit artikel leert u de basisgebruikspatronen van de Speech CLI, een opdrachtregelprogramma om de Spraakservice te gebruiken zonder code te schrijven. U kunt snel de belangrijkste functies van de Spraakservice testen zonder ontwikkelomgevingen te maken of code te schrijven om te controleren of op passende wijze aan uw gebruiksgevallen kan worden voldaan. De Speech CLI is gereed voor productie en kan deze worden gebruikt voor het automatiseren van eenvoudige werkstromen in de Spraakservice, met behulp van `.bat` of shell-scripts.

In dit artikel wordt ervan uitgegaan dat u kennis hebt van de opdrachtprompt, terminal of PowerShell.

[!INCLUDE [](includes/spx-setup.md)]

## <a name="basic-usage"></a>Basisgebruik

In deze sectie ziet u enkele eenvoudige SPX-opdrachten die vaak nuttig zijn als u voor de eerste keer gaat testen en experimenteren. Begin met het bekijken van de Help die is ingebouwd in het hulpprogramma door de volgende opdracht uit te voeren.

```shell
spx
```

Kijk ook naar de helponderwerpen onder **Zie:** rechts van de opdrachtparameters. U kunt deze opdrachten invoeren om gedetailleerde helpinformatie over subopdrachten te krijgen.

U kunt helponderwerpen doorzoeken op trefwoord. Voer bijvoorbeeld de volgende opdracht in om een lijst met gebruiksvoorbeelden van de Spraak-CLI te bekijken:

```shell
spx help find --topics "examples"
```

Voer de volgende opdracht in om de opties voor de opdracht 'herkennen' te bekijken:

```shell
spx help recognize
```

Nu gaan we de Speech CLI gebruiken om spraakherkenning uit te voeren met de standaardmicrofoon van uw systeem. 

>[!WARNING]
> Als u een Docker-container gebruikt, werkt deze opdracht niet.

Voer deze opdracht uit:

```shell
spx recognize --microphone
```

Met de Speech CLI kunt u ook spraak herkennen vanuit een audiobestand.

```shell
spx recognize --file /path/to/file.wav
```
> [!TIP]
> Als u spraak vanuit een audiobestand in een Docker-container wilt herkennen, moet u ervoor zorgen dat het audiobestand zich in de map bevindt die u in de vorige stap hebt gekoppeld.

Nadat u de opdracht hebt ingevoerd, luistert SPX op het huidige actieve invoerapparaat naar de audio en stopt wanneer u op `ENTER` drukt. De opgenomen spraak wordt vervolgens herkend en geconverteerd naar tekst in de console-uitvoer. Tekst-naar-spraak-synthese is ook eenvoudig met de Speech CLI. 

Als u de volgende opdracht uitvoert, wordt de ingevoerde tekst als invoer genomen en wordt de gesynthetiseerde spraak uitgevoerd naar het huidige actieve uitvoerapparaat.

```shell
spx synthesize --text "Testing synthesis using the Speech CLI" --speakers
```

Naast spraakherkenning en synthese kunt u ook spraakomzetting uitvoeren met de Speech CLI. Net als bij de opdracht voor spraakherkenning hierboven voert u de volgende opdracht uit om audio van de standaardmicrofoon vast te leggen en voert u een omzetting naar tekst uit in de doeltaal.

```shell
spx translate --microphone --source en-US --target ru-RU --output file C:\some\file\path\russian_translation.txt
```

In deze opdracht geeft u zowel de brontaal (taal om **vanaf** om te zetten) op als de doeltaal (de taal om **naar** om te zetten). Als u het argument `--microphone` gebruikt, wordt geluisterd naar audio op het huidige actieve invoerapparaat en wordt gestopt u nadat u op `ENTER` hebt gedrukt. De uitvoer is een tekstomzetting naar de doeltaal, geschreven naar een tekstbestand.

> [!NOTE]
> Zie het artikel [Taal en landinstelling](language-support.md) voor een lijst met alle ondersteunde talen met de bijbehorende landinstellingcodes.

### <a name="configuration-files-in-the-datastore"></a>Configuratiebestanden in het gegevensarchief

Het gedrag van de Speech CLI kan afhankelijk zijn van instellingen in configuratiebestanden, waarnaar u binnen Speech CLI-aanroepen kunt verwijzen met een @-teken.
De Speech CLI slaat een nieuwe instelling op in een nieuwe `./spx/data`-submap die wordt gemaakt in de huidige werkmap.
Bij het zoeken naar een configuratiewaarde zoekt de Speech CLI in uw huidige werkmap en vervolgens in het gegevensarchief in `./spx/data`, en vervolgens in andere gegevensarchieven, waaronder een gegevensarchief voor definitieve alleen-lezen versies in het binaire bestand `spx`.
U hebt eerder het gegevensarchief gebruikt om uw `@key` en `@region`-waarden op te slaan, zodat u deze niet hoeft op te geven bij elke opdrachtregelaanroep.
U kunt ook configuratiebestanden gebruiken om uw eigen configuratie-instellingen op te slaan, of zelfs gebruiken voor het doorgeven van URL's of andere dynamische inhoud die tijdens CLR is gegenereerd.

In deze sectie wordt het gebruik van een configuratiebestand in het lokale gegevensarchief weergegeven voor het opslaan en ophalen van de opdrachtinstellingen met behulp van `spx config`, en wordt de uitvoer van Spraak-CLI opgeslagen met behulp van de optie `--output`.

In het volgende voorbeeld wordt het configuratiebestand `@my.defaults` gewist, worden sleutelwaardeparen toegevoegd voor **sleutel** en **regio** in het bestand, en wordt de configuratie gebruikt in een aanroep van `spx recognize`.

```shell
spx config @my.defaults --clear
spx config @my.defaults --add key 000072626F6E20697320636F6F6C0000
spx config @my.defaults --add region westus

spx config @my.defaults

spx recognize --nodefaults @my.defaults --file hello.wav
```

U kunt ook dynamische inhoud naar een configuratiebestand schrijven. Met de volgende opdracht maakt u bijvoorbeeld een aangepast spraakmodel en slaat u de URL van het nieuwe model op in een configuratiebestand. De volgende opdracht wacht tot het model op die URL gereed is voor gebruik voordat het wordt geretourneerd.

```shell
spx csr model create --name "Example 4" --datasets @my.datasets.txt --output url @my.model.txt
spx csr model status --model @my.model.txt --wait
```

In het volgende voorbeeld worden twee URL's naar het configuratiebestand `@my.datasets.txt` geschreven.
In dit scenario kan `--output` een optioneel **toevoegen**-trefwoord bevatten om een configuratiebestand te maken of toe te voegen aan de bestaande.


```shell
spx csr dataset create --name "LM" --kind Language --content https://crbn.us/data.txt --output url @my.datasets.txt
spx csr dataset create --name "AM" --kind Acoustic --content https://crbn.us/audio.zip --output add url @my.datasets.txt

spx config @my.datasets.txt
```

Voer de volgende opdracht in voor meer informatie over gegevensarchiefbestanden, inclusief het gebruik van standaard configuratiebestanden (`@spx.default`, `@default.config`en `@*.default.config` voor opdracht-specifieke standaardinstellingen):

```shell
spx help advanced setup
```

## <a name="batch-operations"></a>Batchbewerkingen

De opdrachten in de vorige sectie zijn handig om snel te zien hoe de Spraakservice werkt. Wanneer u wilt beoordelen of wel of niet aan uw gebruiksgevallen kan worden voldaan, moet u echter waarschijnlijk batchbewerkingen uitvoeren op basis van een bereik met ingevoerde gegevens. Zo kunt u zien hoe de service een verscheidenheid aan scenario's afhandelt. In deze sectie leest u het volgende:

* Batchgewijs spraakherkenning uitvoeren op een map met audiobestanden
* Een `.tsv`-bestand herhalen en batchgewijs tekst-naar-spraak-synthese uitvoeren

## <a name="batch-speech-recognition"></a>Batchgewijze spraakherkenning

Als u een map met audiobestanden hebt, kunt u met de Speech CLI eenvoudig en snel batchgewijze spraakherkenning uitvoeren. Voer eenvoudig de volgende opdracht uit en wijs naar uw map met de opdracht `--files`. In dit voorbeeld voegt u `\*.wav` toe aan de map om alle `.wav`-bestanden in de map te herkennen. Geef daarnaast het argument `--threads` op om de herkenning uit te voeren op tien parallelle threads.

> [!NOTE]
> Het argument `--threads` kan ook worden gebruikt in de volgende sectie voor `spx synthesize`-opdrachten; de beschikbare threads zijn afhankelijk van de CPU en het huidige belastingspercentage.

```shell
spx recognize --files C:\your_wav_file_dir\*.wav --output file C:\output_dir\speech_output.tsv --threads 10
```

De herkende spraakuitvoer wordt naar `speech_output.tsv` geschreven met behulp van het argument `--output file`. Hierna volgt een voorbeeld van de bestandsstructuur van de uitvoer.

```output
audio.input.id    recognizer.session.started.sessionid    recognizer.recognized.result.text
sample_1    07baa2f8d9fd4fbcb9faea451ce05475    A sample wave file.
sample_2    8f9b378f6d0b42f99522f1173492f013    Sample text synthesized.
```

## <a name="synthesize-speech-to-a-file"></a>Spraak synthetiseren naar een bestand

Voer de volgende opdracht uit om de uitvoer van uw luidspreker te veranderen naar een `.wav`-bestand.

```bash
spx synthesize --text "The speech synthesizer greets you!" --audio output greetings.wav
```

De Speech CLI produceert natuurlijke taal in het Engels en slaat die op in het `greetings.wav`-geluidsbestand.
In Windows kunt u het geluidsbestand afspelen door `start greetings.wav`in te voeren.


## <a name="batch-text-to-speech-synthesis"></a>Batchgewijze tekst-naar-spraak-synthese

De eenvoudigste manier om batchgewijze tekst-naar-spraak uit te voeren, is door een nieuw `.tsv`-bestand (door tabs gescheiden waarden) te maken en in de Speech CLI gebruik te maken van de opdracht `--foreach`. Kijk eens naar het volgende bestand `text_synthesis.tsv`:

```output
audio.output    text
C:\batch_wav_output\wav_1.wav    Sample text to synthesize.
C:\batch_wav_output\wav_2.wav    Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav    Some more text to test capabilities.
```

 Vervolgens voert u een opdracht uit om naar `text_synthesis.tsv` te verwijzen, voert u synthese uit op elk `text`-veld en schrijft u het resultaat naar het overeenkomstige `audio.output`-pad als een `.wav`-bestand. 

```shell
spx synthesize --foreach in @C:\your\path\to\text_synthesis.tsv
```

Deze opdracht is gelijk aan het uitvoeren van `spx synthesize --text Sample text to synthesize --audio output C:\batch_wav_output\wav_1.wav` **voor elk** record in het bestand `.tsv`. Enkele opmerkingen:

* De kolomkoppen, `audio.output` en `text`, komen overeen met respectievelijk de opdrachtregelargumenten `--audio output` en `--text`. Opdrachtregelargumenten met meerdere delen, zoals `--audio output`, moeten in het bestand worden opgemaakt zonder spaties, geen voorloopstreepjes en punten die tekenreeksen scheiden, bijvoorbeeld `audio.output`. Andere bestaande opdrachtregelargumenten kunnen worden toegevoegd aan het bestand als aanvullende kolommen met dit patroon.
* Wanneer het bestand op deze manier wordt geformatteerd, hoeven er geen aanvullende argumenten te worden doorgegeven aan `--foreach`.
* Vergeet niet elke waarde in het `.tsv` te scheiden door een **tab**.

Als u echter een `.tsv`-bestand hebt, zoals in het volgende voorbeeld, met kolomkoppen die **niet overeenkomen met** opdrachtregelargumenten:

```output
wav_path    str_text
C:\batch_wav_output\wav_1.wav    Sample text to synthesize.
C:\batch_wav_output\wav_2.wav    Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav    Some more text to test capabilities.
```

U kunt deze veldnamen vervangen door de juiste argumenten met behulp van de volgende syntaxis in de aanroep `--foreach`. Dit is dezelfde aanroep als hierboven.

```shell
spx synthesize --foreach audio.output;text in @C:\your\path\to\text_synthesis.tsv
```

## <a name="next-steps"></a>Volgende stappen

* Voltooi de quickstarts over [spraakherkenning](get-started-speech-to-text.md?pivots=programmer-tool-spx) of [spraaksynthese](get-started-text-to-speech.md?pivots=programmer-tool-spx) met behulp van de Speech CLI.

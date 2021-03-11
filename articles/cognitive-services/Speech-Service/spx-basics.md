---
title: Speech CLI Quick Start-Speech Service
titleSuffix: Azure Cognitive Services
description: Ga aan de slag met de Azure speech-CLI. U kunt communiceren met spraak services zoals spraak op tekst, tekst naar spraak en spraak omzetting zonder code te schrijven.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.author: trbye
ms.openlocfilehash: 53138a22c58e89ade4af234630e9429a19738a6a
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102556465"
---
# <a name="get-started-with-the-azure-speech-cli"></a>Aan de slag met de Azure speech CLI

In dit artikel leert u hoe u de speech CLI, een opdracht regel interface, kunt gebruiken om toegang te krijgen tot spraak services zoals spraak op tekst, tekst naar spraak en spraak omzetting zonder code te schrijven. De Speech CLI is gereed voor productie en kan deze worden gebruikt voor het automatiseren van eenvoudige werkstromen in de Spraakservice, met behulp van `.bat` of shell-scripts.

In dit artikel wordt ervan uitgegaan dat u ervaring hebt met het werken met de opdracht prompt, Terminal of Power shell.

[!INCLUDE [](includes/spx-setup.md)]

## <a name="basic-usage"></a>Basisgebruik

In deze sectie ziet u enkele eenvoudige SPX-opdrachten die vaak nuttig zijn als u voor de eerste keer gaat testen en experimenteren. Begin met het bekijken van de Help die is ingebouwd in het hulpprogramma door de volgende opdracht uit te voeren.

```console
spx
```

U kunt helponderwerpen doorzoeken op trefwoord. Voer bijvoorbeeld de volgende opdracht in om een lijst met gebruiksvoorbeelden van de Spraak-CLI te bekijken:

```console
spx help find --topics "examples"
```

Voer de volgende opdracht in om de opties voor de opdracht 'herkennen' te bekijken:

```console
spx help recognize
```

Aanvullende Help-opdrachten worden weer gegeven in de rechter kolom. U kunt deze opdrachten invoeren om gedetailleerde Help over subopdrachten te krijgen.

## <a name="speech-to-text-speech-recognition"></a>Spraak naar tekst (spraak herkenning)

We gebruiken de speech CLI om spraak te converteren naar tekst (spraak herkenning) met behulp van de standaard microfoon van uw systeem. Nadat u de opdracht hebt ingevoerd, luistert SPX naar audio op het huidige actieve invoer apparaat en stopt dit wanneer u op **Enter** drukt. De opgenomen spraak wordt vervolgens herkend en geconverteerd naar tekst in de console-uitvoer.

>[!IMPORTANT]
> Als u een docker-container gebruikt, `--microphone` werkt niet.

Voer deze opdracht uit:

```console
spx recognize --microphone
```

Met de spraak-CLI kunt u ook spraak herkennen vanuit een audio bestand.

```console
spx recognize --file /path/to/file.wav
```

> [!TIP]
> Als u spraak vanuit een audiobestand in een Docker-container wilt herkennen, moet u ervoor zorgen dat het audiobestand zich in de map bevindt die u in de vorige stap hebt gekoppeld.

Vergeet niet, als u op de hoogte bent of als u meer wilt weten over de herkennings opties van de speech CLI, typt u:

```console
spx help recognize
```

## <a name="text-to-speech-speech-synthesis"></a>Tekst-naar-spraak (spraak-synthese)

Als u de volgende opdracht uitvoert, wordt tekst als invoer gebruikt en wordt de gesynthesizerde spraak naar het huidige actieve uitvoer apparaat uitgevoerd (bijvoorbeeld uw computer luidsprekers).

```console
spx synthesize --text "Testing synthesis using the Speech CLI" --speakers
```

U kunt de uitgesynthesizerde uitvoer ook opslaan in een bestand. In dit voor beeld maken we een bestand met `my-sample.wav` de naam in de map die met de opdracht wordt uitgevoerd.

```console
spx synthesize --text "Enjoy using the Speech CLI." --audio output my-sample.wav
```

In deze voor beelden wordt ervan uitgegaan dat u in het Engels test. We ondersteunen echter spraak synthese in veel talen. U kunt een volledige lijst met stemmen weer geven met deze opdracht of door de [pagina taal ondersteuning](./language-support.md)te bezoeken.

```console
spx synthesize --voices
```

Hier kunt u een van de stemmen gebruiken die u hebt gedetecteerd.

```console
spx synthesize --text "Bienvenue chez moi." --voice fr-CA-Caroline --speakers
```

Vergeet niet, als u op de hoogte bent of meer wilt weten over de opties voor de synthese van de speech CLI, typt u:

```console
spx help synthesize
```

## <a name="speech-to-text-translation"></a>Spraak naar tekst Vertaling

Met de spraak-CLI kunt u ook spraak maken op tekst vertaling. Voer deze opdracht uit om audio vast te leggen van de standaard microfoon en de vertaling als tekst uit te voeren. Houd er rekening mee dat u de-en-taal moet opgeven `source` `target` met de `translate` opdracht.

```console
spx translate --microphone --source en-US --target ru-RU
```

Bij het vertalen naar meerdere talen, afzonderlijke taal codes met `;` .

```console
spx translate --microphone --source en-US --target ru-RU;fr-FR;es-ES
```

Als u de uitvoer van uw vertaling wilt opslaan, gebruikt u de `--output` vlag. In dit voor beeld leest u ook uit een bestand.

```console
spx translate --file /some/file/path/input.wav --source en-US --target ru-RU --output file /some/file/path/russian_translation.txt
```

> [!NOTE]
> Zie het artikel [Taal en landinstelling](language-support.md) voor een lijst met alle ondersteunde talen met de bijbehorende landinstellingcodes.

Vergeet niet, als u op de hoogte bent of meer wilt weten over de Vertaal opties van de speech CLI, typt u:

```console
spx help translate
```

## <a name="next-steps"></a>Volgende stappen

* [Configuratieopties voor Speech-CLI](./spx-data-store-configuration.md)
* [Batchbewerkingen met de Spraak-CLI](./spx-batch-operations.md)

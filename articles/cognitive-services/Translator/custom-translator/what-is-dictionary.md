---
title: Wat is een woorden lijst? -Aangepaste vertaler
titleSuffix: Azure Cognitive Services
description: Een woorden lijst is een uitgelijnd document waarin een lijst met zinsdelen of zinnen (en de bijbehorende vertalingen) wordt opgegeven die u altijd wilt laten vertalen met micro soft Translator. Woorden lijsten worden soms ook wel Glossaries of term bases genoemd.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 160aa372cec2d018d5a3558f99e079624d965098
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895557"
---
# <a name="what-is-a-dictionary"></a>Wat is een woorden lijst?

Een woorden lijst is een uitgelijnd paar documenten waarin een lijst met zinsdelen of zinnen en de bijbehorende vertalingen wordt opgegeven. Gebruik een woorden lijst in uw training als u wilt dat micro soft Translator altijd alle exemplaren van de bron woord groep of zin moet vertalen met behulp van de vertaling die u in de woorden lijst hebt gegeven. Woorden lijsten worden soms Glossaries of term bases genoemd. U kunt de woorden lijst beschouwen als een brute kracht ' kopiëren en vervangen ' voor alle termen die u vermeldt. Daarnaast bouwt micro soft Custom Translator service samen met een eigen woorden boek voor algemeen gebruik om de kwaliteit van de vertaling te verbeteren. Een door de klant verschafte woorden lijst heeft echter een broncel en zal eerst zoeken naar woorden of zinnen.

Woorden boeken werken alleen voor projecten in taal paren met een volledig ondersteund micro soft General Neural-netwerk model achter de computers. [Bekijk de volledige lijst met talen](../language-support.md#customization).

## <a name="phrase-dictionary"></a>Woordgroepen lijst
Woordgroepen woordenlijst is hoofdletter gevoelig. Het is een exacte Zoek-en vervang bewerking. Wanneer u een woordgroepen lijst in uw model hebt opgenomen, wordt een wille keurig woord of zinsdeel omgezet in de manier die u hebt opgegeven. De rest van de zin wordt op de gebruikelijke manier vertaald. U kunt een woordgroepen woordenlijst gebruiken om woord groepen op te geven die niet moeten worden vertaald door dezelfde niet-vertaalde woord groep te bieden in het bron-en doel bestand in de woorden lijst.

## <a name="sentence-dictionary"></a>Woorden lijst voor zinnen
De woorden lijst voor zinnen is hoofdletter gevoelig. Met de woorden lijst voor zinnen kunt u een exacte doel omzetting voor een bron zin opgeven. Voor een zin die overeenkomt met de woorden lijst, moet de gehele verzonden zin overeenkomen met de vermelding van de bron woordenlijst. Als de vermelding van de bron woordenlijst eindigt op interpunctie, wordt deze genegeerd tijdens de overeenkomst. Als slechts een deel van de zin overeenkomt, komt de invoer niet overeen.  Wanneer een overeenkomst wordt gedetecteerd, wordt de doel vermelding van de woorden lijst geretourneerd.

## <a name="dictionary-only-trainings"></a>Alleen-woordenlijst trainingen
U kunt een model trainen met alleen woordenlijst gegevens. Als u dit wilt doen, selecteert u alleen het woordenlijst document (of meerdere woordenlijst documenten) die u wilt opnemen en tikt u op model maken. Aangezien dit een alleen-woordenlijst training is, is er geen minimum aantal trainings zinnen vereist. Het model voert doorgaans veel sneller training uit dan een standaard training.  De resulterende modellen gebruiken de micro soft Baseline-modellen voor vertaling, waarbij de toegevoegde woorden lijsten worden toegevoegd.  Het is niet mogelijk om een test rapport op te halen.

>[!Note]
>Aangepaste vertaler heeft geen zin om woordenlijst bestanden uit te lijnen. het is dus belang rijk dat er een gelijk aantal bron-en doel zinnen/zinnen in uw woordenlijst documenten staat en dat ze nauw keurig zijn uitgelijnd.

## <a name="recommendations"></a>Aanbevelingen

- Woorden boeken zijn geen vervanging voor het trainen van een model met behulp van trainings gegevens. Het wordt aanbevolen om deze te vermijden en het systeem te laten leren van uw trainings gegevens. Als er echter zinnen of samengestelde samen stellingen moeten worden weer gegeven als-is, gebruikt u een woorden lijst.
- De woorden lijst woordgroepen moet spaarzaam worden gebruikt. Houd er dus rekening mee dat wanneer een zin binnen een zin wordt vervangen, de context binnen die zin verloren gaat of beperkt is voor het vertalen van de rest van de zin. Het resultaat is dat als de woord groep of het woord in de zin wordt vertaald volgens de meegeleverde woorden lijst, de algehele Vertaal kwaliteit van de zin vaak in orde is.
- De woorden lijst woordgroepen werkt goed voor samengestelde naam woorden, zoals product namen ("Microsoft SQL Server"), juiste namen ("City of Hamburg") of functies van het product ("Pivot Table"). Deze werkt niet gelijk aan woorden of bijvoegingen, omdat deze doorgaans zeer verbuigd zijn in de bron of in de doel taal. De aanbevolen procedures zijn om te voor komen dat vermeldingen voor de woorden lijst in combi natie met samen woorden worden samengesteld.
- Wanneer u een woordgroepen woordenlijst gebruikt, zijn hoofdletter gebruik en interpunctie belang rijk. Woordenboek vermeldingen komen alleen overeen met woorden en zinsdelen in de invoer zin die exact hetzelfde hoofdletter gebruik en lees tekens bevatten zoals opgegeven in het bestand van de bron woordenlijst. De vertalingen hebben ook betrekking op het hoofdletter gebruik en interpunctie die zijn opgenomen in het woordenlijst bestand van de doel groep. Als u bijvoorbeeld een Engels hebt getraind voor het Spaanse systeem dat gebruikmaakt van een woordgroepen woordenlijst waarin ' US ' is opgegeven in het bron bestand en ' EE '. UU. " in het doel bestand. Wanneer u een vertaling aanvraagt van een zin die het woord ' US ' bevat (niet gekapitaliseerd), komt dit niet overeen met de woorden lijst. Als u echter een vertaling aanvraagt van een zin die het woord ' US ' bevat (gekapitaliseerd), zou dit overeenkomen met de woorden lijst en de vertaling zou ' EE ' bevatten. UU. " Houd er rekening mee dat het hoofdletter gebruik en interpunctie in de vertaling kan afwijken van het doel bestand van de woorden lijst en kan afwijken van het hoofdletter gebruik en interpunctie in de bron. De regels van de doel taal worden gevolgd.
- Wanneer u een zin-woorden lijst gebruikt, wordt het einde van de teken interpunctie genegeerd. Als uw bron woordenlijst bijvoorbeeld ' deze zin eindigt op interpunctie ' bevat, komt er een overeenkomst voor vertaal aanvragen met de tekst ' deze zin eindigt op interpunctie '.
- Als een woord meermaals wordt weer gegeven in een woordenlijst bestand, gebruikt het systeem altijd de laatste vermelding die is opgegeven. De woorden lijst moet daarom niet meerdere vertalingen van hetzelfde woord bevatten.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [richt lijnen voor document indelingen](document-formats-naming-convention.md).
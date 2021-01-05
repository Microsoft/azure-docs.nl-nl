---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2020
ms.author: trbye
ms.openlocfilehash: dc569050b78a5797808f2e2e000019ba516ba22e
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/23/2020
ms.locfileid: "97739230"
---
**Interactief**
- Bedoeld voor opdracht-en controle scenario's.
- Heeft een time-outwaarde van X.
- Aan het einde van een herkende utterance stopt de service de verwerking van de audio van die aanvraag-ID en eindigt de beurt. De verbinding is niet gesloten.
- De maximum limiet voor herkenning is 20s.
- Typische Carbon-aanroep naar invoke is `RecognizeOnceAsync` .

**Gesprek**
- Bedoeld om meer herkennings handelingen uit te voeren.
- Heeft een time-outwaarde voor de segmentatie van Y. (Y! = X)
- Er worden meerdere volledige uitingen verwerkt zonder de beurt te beëindigen.
- Beëindigt de turn voor te veel stilte.
- Carbon gaat door met een nieuwe aanvraag-ID en de audio opnieuw af te spelen als dat nodig is.
- De verbinding met de service wordt na 10 minuten van spraak herkenning afgedwongen.
- Kool wordt opnieuw verbonden en niet-bevestigde audio opnieuw afgespeeld.
- Aangeroepen in carbon with `StartContinuousRecognition` .

**Dicteren**
- Hiermee kunnen gebruikers interpunctie opgeven door het te spreken.
- Wordt aangeroepen in kooldioxyde door `EnableDictation` op het object op te geven `SpeechConfig` , ongeacht de API-aanroep die de herkenning start.
- De 1<sup>St</sup> partij-cluster retourneert `speech.fragment` berichten voor tussenliggende resultaten, de<sup></sup> drie retour berichten van de derde partij `speech.hypothesis` .
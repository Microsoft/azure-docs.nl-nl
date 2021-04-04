---
title: Gebruikers utterance bekijken-LUIS
description: Met actief leren kunt u uw eind punt uitingen controleren op juiste intentie en entiteit. LUIS kiest eind punt uitingen het is niet zeker van.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 82f228d5e6f801539c549e16faea371782ad4b59
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "91316440"
---
# <a name="concepts-for-enabling-active-learning-by-reviewing-endpoint-utterances"></a>Concepten voor het inschakelen van actief leren door eind punt uitingen te controleren
Actief leren is een van de drie strategieën om de nauw keurigheid van de voor spelling en de eenvoudigste implementatie te verbeteren. Met actief leren kunt u uw eind punt uitingen controleren op juiste intentie en entiteit. LUIS kiest eind punt uitingen het is niet zeker van.

## <a name="what-is-active-learning"></a>Wat is actief leren
Actief leren is een proces dat uit twee stappen bestaat. Eerst selecteert LUIS de uitingen die wordt ontvangen op het eind punt van de app waarvoor validatie nodig is. De tweede stap wordt uitgevoerd door de app-eigenaar of de samen werker om de geselecteerde uitingen te valideren voor [controle](luis-how-to-review-endpoint-utterances.md), met inbegrip van de juiste intentie en entiteiten binnen de intentie. Nadat u de uitingen hebt bekeken, Train en publiceert u de app opnieuw.

## <a name="which-utterances-are-on-the-review-list"></a>Welke uitingen bevinden zich in de controle lijst
LUIS voegt uitingen toe aan de beoordelings lijst wanneer de bovenste ontstekings intentie een lage score heeft of de bovenste twee intents-scores te dicht zijn.

## <a name="single-pool-for-utterances-per-app"></a>Eén groep voor uitingen per app
De lijst uitingen van het **controle punt** wordt niet gewijzigd op basis van de versie. Er is één groep uitingen om te beoordelen, ongeacht welke versie van de uiting u actief bewerkt of welke versie van de app wordt gepubliceerd op het eindpunt.

In de [rest API](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/58b6f32139e2bb139ce823c9)is de versie naam vereist en in de toepassing opgenomen, maar deze wordt niet meer gebruikt dan de validatie. De beoordelings uitingen zijn van toepassing op een hele toepassing. Als u uitingen uit de ene _versie_ verwijdert, worden alle versies beïnvloed.

## <a name="where-are-the-utterances-from"></a>Waar bevinden de uitingen van
Eind gebruikers van query's op het HTTP-eind punt van de toepassing worden uitingen. Als uw app niet is gepubliceerd of nog geen treffers heeft ontvangen, hebt u geen uitingen om te controleren. Als er geen eindpunt treffers zijn ontvangen voor een specifieke intentie of entiteit, hebt u geen uitingen om deze te controleren.

## <a name="schedule-review-periodically"></a>Periodiek een beoordeling plannen
Het bekijken van voorgestelde uitingen hoeft niet elke dag te worden uitgevoerd, maar moet deel uitmaken van uw regel matig onderhoud van LUIS.

## <a name="delete-review-items-programmatically"></a>Controle-items via een programma verwijderen
Gebruik de niet- **[gelabelde uitingen](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9)** -API verwijderen. Maak een back-up van deze uitingen voordat u deze verwijdert door **[de logboek bestanden te exporteren](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)**.

## <a name="enable-active-learning"></a>Actief leren inschakelen

Als u actief leren wilt inschakelen, moet u gebruikers query's registreren. Dit wordt bereikt door de query van het [eind punt](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) aan te roepen met de `log=true` para meter en waarde van de query reeks.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [controleren](luis-how-to-review-endpoint-utterances.md) van eind punten uitingen

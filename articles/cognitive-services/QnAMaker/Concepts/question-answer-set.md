---
title: Ontwerp kennis basis-QnA Maker concepten
description: Meer informatie over het ontwerpen van een Knowledge Base-QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: c21c82b5fd024598da30f4ac7b1ed01e64561e3b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96000110"
---
# <a name="question-and-answer-pair-concepts"></a>Concepten van vraag-en antwoord paren

Een Knowledge Base bestaat uit vraag-en antwoord paren (QnA).  Elk paar heeft één antwoord en een paar bevat alle informatie die aan het _antwoord_ is gekoppeld. Een antwoord kan een Data Base-rij of een gegevens structuur-exemplaar in een keer lijken.

## <a name="question-and-answer-pairs"></a>Vraag-en-antwoord-paren

De **vereiste** instellingen in een vraag-en-antwoord-paar (QnA) zijn:

* een **vraag** : tekst van de gebruikers query, die wordt gebruikt om de machine trainingen van de gebruiker uit te QnA Maker, om af te stemmen met de vraag van gebruikers met andere woorden, maar hetzelfde antwoord
* het **antwoord: het antwoord van** het paar is het antwoord dat wordt geretourneerd wanneer een gebruikers query overeenkomt met de bijbehorende vraag

Elk paar wordt vertegenwoordigd door een **id**.

De **optionele** instellingen voor een paar zijn onder andere:

* **Alternatieve vormen van de vraag** : dit helpt QnA Maker het juiste antwoord te retour neren voor een groter aantal vraag frasen
* **Meta gegevens**: meta gegevens zijn tags die zijn gekoppeld aan een QnA paar en worden weer gegeven als sleutel-waardeparen. Tags voor meta gegevens worden gebruikt voor het filteren van QnA-paren en het beperken van de set waarover overeenkomende query's worden uitgevoerd.
* **Vragen over meerdere schakelingen**, gebruikt om door te gaan met een gesprek op meerdere locaties

![Knowledge Base van QnA Maker](../media/qnamaker-concepts-knowledgebase/knowledgebase.png)

## <a name="editorially-add-to-knowledge-base"></a>Redactionel toevoegen aan de Knowledge Base

Als u geen vooraf bestaande inhoud hebt om de Knowledge Base te vullen, kunt u QnA paren op de QnA Maker-portal toevoegen. Meer informatie over hoe u uw kennis [database kunt](../How-To/edit-knowledge-base.md)bijwerken.

## <a name="editing-your-knowledge-base-locally"></a>Uw kennis database lokaal bewerken

Zodra een Knowledge Base is gemaakt, wordt het aanbevolen dat u wijzigingen aanbrengt in de Knowledge Base-tekst in de [QnA Maker Portal](https://qnamaker.ai)in plaats van het exporteren en opnieuw importeren via lokale bestanden. Het is echter mogelijk dat u een kennis database lokaal moet bewerken.

Exporteer de Knowledge Base van de pagina **instellingen** en bewerk vervolgens de Knowledge Base met micro soft Excel. Als u ervoor kiest om een andere toepassing te gebruiken om het geëxporteerde bestand te bewerken, kan de toepassing syntaxis fouten veroorzaken omdat deze niet volledig TSV-compatibel is. De TSV-bestanden van micro soft Excel voeren doorgaans geen opmaak fouten uit.

Wanneer u klaar bent met uw bewerkingen, importeert u het TSV-bestand opnieuw vanaf de pagina **instellingen** . Hiermee wordt de huidige Knowledge Base volledig vervangen door de geïmporteerde kennis database.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Levens cyclus van de Knowledge Base in QnA Maker](./development-lifecycle-knowledge-base.md)
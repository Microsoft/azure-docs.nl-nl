---
title: Kunstmatige intelligentie (AI)
description: LUIS maakt gebruik van kunst matige intelligentie (AI) om inzicht te krijgen in de taal van uw gegevens op basis van het schema dat u hebt gedefinieerd.
ms.topic: conceptual
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 08/07/2020
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 39f73dd002091451ae832516d525499eae98564d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "95021368"
---
# <a name="artificial-intelligence-in-language-understanding-luis"></a>Kunst matige intelligentie in Language Understanding (LUIS)

LUIS maakt gebruik van kunst matige intelligentie (AI) om inzicht te bieden in natuurlijke taal (NLU) aan uw gegevens op basis van het schema dat u hebt gedefinieerd.

## <a name="natural-language-processing-nlp"></a>Natuurlijke taal verwerking (NLP)

Natuurlijke Language Understanding (NLU) is een specifiek subonderwerp van natuurlijke taal verwerking (NLP).

De verwerking van natuurlijke taal is een breder concept dat elke vorm van verwerking van tekstuele gegevens verwerkt. Dit omvat zaken als:

* Token
* Onderdeel van spraak code ring (POS)
* Segmentatie
* Morfologische analyse
* Semantische gelijkenis
* Discours
* Omzetting

## <a name="natural-language-processing-in-luis"></a>Natuurlijke taal verwerking in LUIS

De verwerking van natuurlijke taal is op de volgende manieren beschikbaar voor uw LUIS-app:
* [Natuurlijk taal memorandum](#natural-language-processing-nlp) (Luis)
* Configureer bare NLP-aspecten in LUIS:
    * [Token](luis-language-support.md#tokenization)
    * Morphology via diakritische tekens, interpunctie en Word Forms [API-instellingen](luis-reference-application-settings.md)
* Vóór of na het verwerken van de query utterance van andere [Cognitive Services](../what-are-cognitive-services.md) , zoals:
    * [Omzetting](../translator/translator-info-overview.md)

## <a name="natural-language-understanding-nlu"></a>NLU (natuurlijk taalbegrip)

NLU is de mogelijkheid om een taal kundige verklaring te _Transformeren_ naar een representatie waarmee u uw gebruikers op natuurlijke wijze kunt begrijpen. In natuurlijke taal is er een probleem met de meeste problemen en wordt gedefinieerd als een _AI-hard_ probleem.

LUIS is bedoeld om zich te richten op beoogd en extractie, waaronder het identificeren van:
* Wat de gebruiker wil
* Waar ze over zijn.

LUIS heeft weinig of geen kennis van de bredere _NLP_ -aspecten, zoals semantische gelijkenis, zonder expliciete identificatie in voor beelden. De volgende tokens (woorden) zijn bijvoorbeeld drie verschillende dingen tot ze worden gebruikt in vergelijk bare contexten in de voor beelden:

* ze
* koopt
* hebben

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [ontwikkelings levenscyclus](luis-concept-app-iteration.md) voor een Luis-app
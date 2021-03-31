---
title: Veelgestelde vragen-aangepaste vertaler
titleSuffix: Azure Cognitive Services
description: In dit artikel vindt u antwoorden op veelgestelde vragen over de Cognitive Services aangepaste Translator van Azure.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: reference
ms.openlocfilehash: 001314817b0c18a8023258d01bcfb02eaaffe79b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "98895829"
---
# <a name="custom-translator-frequently-asked-questions"></a>Veelgestelde vragen over aangepaste vertalers

In dit artikel vindt u antwoorden op veelgestelde vragen over [aangepaste vertalers](https://portal.customtranslator.azure.ai).

## <a name="what-are-the-current-restrictions-in-custom-translator"></a>Wat zijn de huidige beperkingen in het aangepaste conversie programma?

Er gelden beperkingen en beperkingen ten aanzien van de bestands grootte, model training en model implementatie. Houd bij het instellen van uw training de volgende beperkingen in acht wanneer u een model in Custom Translator wilt maken.

- Verzonden bestanden moeten kleiner zijn dan 100 MB.
- Monolingual-gegevens worden niet ondersteund.

## <a name="when-should-i-request-deployment-for-a-translation-system-that-has-been-trained"></a>Wanneer moet ik de implementatie aanvragen voor een getraind Vertaal systeem?

Het kan een aantal trainingen nemen om het beste Vertaal systeem voor uw project te maken. U kunt proberen meer trainings gegevens of meer zorgvuldig gefilterde gegevens te gebruiken als de BLEU-Score en/of de test resultaten niet bevredigend zijn. Het is raadzaam om uw afstemmings en uw testset zo goed mogelijk te ontwerpen en zo een volledig representatief te maken voor de terminologie en de stijl van materiaal dat u wilt vertalen. U kunt meer vrijelijk zijn bij het opstellen van uw trainings gegevens en experimenteren met verschillende opties. Vraag een systeem implementatie aan wanneer u tevreden bent met de vertalingen in de test resultaten van uw systeem. u hoeft geen gegevens meer toe te voegen aan de training om uw getrainde systeem te verbeteren en u wilt toegang krijgen tot het getrainde model via Api's.

## <a name="how-many-trained-systems-can-be-deployed-in-a-project"></a>Hoeveel getrainde systemen kunnen in een project worden geïmplementeerd?

Er kan slechts één getraind systeem per project worden geïmplementeerd. Het kan enkele trainingen nemen om een geschikt Vertaal systeem te maken voor uw project en wij raden u aan de implementatie van een training aan te vragen die u het beste resultaat geeft. U kunt de kwaliteit van de training bepalen door de BLEU-Score (hoger is beter) en door te adviseren bij de controleurs voordat u besluit dat de kwaliteit van vertalingen geschikt is voor implementatie.

## <a name="when-can-i-expect-my-trainings-to-be-deployed"></a>Wanneer kan ik verwachten dat mijn trainingen worden geïmplementeerd?

De implementatie duurt over het algemeen minder dan een uur.

## <a name="how-do-you-access-a-deployed-system"></a>Hoe krijgt u toegang tot een geïmplementeerd systeem?

Via de micro soft Translator Text-API v3 kunnen geïmplementeerde systemen worden geopend door het CategoryID op te geven. Meer informatie over de Translator Text-API vindt u in de [API Reference](../reference/v3-0-reference.md) -webpagina.

## <a name="how-do-i-skip-alignment-and-sentence-breaking-if-my-data-is-already-sentence-aligned"></a>Hoe kan ik de uitlijning en de zin niet overs Laan als er al een zin van mijn gegevens is uitgelijnd?

De aangepaste vertaler slaat de uitlijning van de zin en de zin van zinnen voor TMX-bestanden en tekst bestanden met de `.align` extensie over. `.align` bestanden geven gebruikers een optie voor het overs laan van het afbreken en uitlijn proces van zinnen van de aangepaste vertaler voor de bestanden die perfect zijn uitgelijnd en waarvoor geen verdere verwerking nodig is. U wordt aangeraden `.align` alleen extensie te gebruiken voor bestanden die perfect zijn uitgelijnd.

Als het aantal geëxtraheerde zinnen niet overeenkomt met de twee bestanden met dezelfde basis naam, wordt de zin door aangepaste vertaler nog steeds uitgevoerd op `.align` bestanden.

## <a name="i-tried-uploading-my-tmx-but-it-says-document-processing-failed"></a>Ik heb geprobeerd mijn TMX te uploaden, maar de melding ' document verwerking is mislukt '


Zorg ervoor dat de TMX voldoet aan de TMX 1.4 b-specificatie op <https://www.gala-global.org/tmx-14b> .
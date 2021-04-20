---
title: Koppelen en uitlijnen van zinnen - Custom Translator
titleSuffix: Azure Cognitive Services
description: Tijdens de uitvoering van de training worden zinnen in parallelle documenten gekoppeld of uitgelijnd. Custom Translator leert vertalingen met één zin tegelijk, door een zin te lezen, de vertaling van deze zin. Vervolgens worden woorden en woordgroepen in deze twee zinnen op elkaar afgestemd.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 04/19/2021
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 43268afccbe66a21d2ce78709ba372a8a6682444
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727146"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>Koppelen en uitlijnen van zinnen in parallelle documenten

Nadat documenten zijn geüpload, worden zinnen in parallelle documenten gekoppeld of uitgelijnd. Custom Translator het aantal zinnen dat kan worden gekoppeld als de uitgelijnde zinnen in elk van de gegevenssets.

## <a name="pairing-and-alignment-process"></a>Koppelings- en uitlijningsproces

Custom Translator leert vertalingen van zinnen met één zin tegelijk. Er wordt een zin uit de brontekst gelezen en vervolgens de vertaling van deze zin uit de doeltekst. Vervolgens worden woorden en woordgroepen in deze twee zinnen op elkaar afgestemd. Met dit proces kan een kaart worden gemaakt van de woorden en woordgroepen in één zin met de equivalente woorden en zinnen in de vertaling van de zin. Uitlijning probeert ervoor te zorgen dat het systeem wordt traint op zinnen die vertalingen van elkaar zijn.

## <a name="pre-aligned-documents"></a>Vooraf uitgelijnde documenten

Als u weet dat u parallelle documenten hebt, kunt u de uitlijning van zinnen overschrijven door vooraf uitgelijnde tekstbestanden op te geven. U kunt alle zinnen uit beide documenten uitpakken in een tekstbestand, één zin per regel orden en uploaden met een `.align` extensie. De `.align` extensie geeft aan Custom Translator zinuitlijning moet worden overgeslagen.

Voor de beste resultaten moet u ervoor zorgen dat uw bestanden één zin per regel bevatten. Gebruik geen nieuwelijntekens binnen een zin, omdat dit tot slechte uitlijning zal leiden.

## <a name="suggested-minimum-number-of-sentences"></a>Voorgesteld minimumaantal zinnen

Om een training te laten slagen, toont de onderstaande tabel het minimale aantal zinnen dat is vereist voor elk documenttype.Deze beperking is een veiligheidsnet om ervoor te zorgen dat uw parallelle zinnen voldoende unieke woorden bevatten om een vertaalmodel te trainen. De algemene richtlijn is dat meer in-domain parallelle zinnen van menselijke vertaling modellen van hogere kwaliteit moeten produceren.

| Documenttype   | Voorgesteld minimumaantal zinnen | Maximum aantal zinnen |
|------------|--------------------------------------------|--------------------------------|
| Training   | 10.000                                     | Geen bovengrens                 |
| Afstemmen     | 500                                      | 2500       |
| Testen    | 500                                      | 2500  |
| Woordenlijst | 0                                          | 250.000                 |

> [!NOTE]
>
> - De training wordt niet start en mislukt als niet wordt voldaan aan het minimumaantal van 10.000 zinnen voor Training.
> - Afstemmen en testen zijn optioneel. Als u deze niet op geeft, verwijdert het systeem het juiste percentage uit Training om te gebruiken voor validatie en testen.
> - U kunt een model trainen met alleen woordenlijstgegevens. Raadpleeg Wat [is woordenlijst?](./what-is-dictionary.md).
> - Als uw woordenlijst meer dan 250.000 zinnen bevat, is **[Document Translator](https://docs.microsoft.com/azure/cognitive-services/translator/document-translation/overview)** waarschijnlijk een betere keuze.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het gebruik [van een woordenlijst](what-is-dictionary.md) in Custom Translator.

---
title: Zin en uitlijning-aangepaste vertaler
titleSuffix: Azure Cognitive Services
description: Tijdens de uitvoering van de training worden zinnen in parallelle documenten gekoppeld of uitgelijnd. Aangepaste Translator leert vertalingen per zin, door een zin te lezen, de vertaling van deze zin. Vervolgens worden woorden en zinsdelen in deze twee zinnen op elkaar uitgelijnd.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: e5d360933cbeb611046aede6164ec2e2fa497664
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97347792"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>Zin en uitlijning in parallelle documenten

Na het uploaden van documenten, worden de zinnen die aanwezig zijn in parallelle documenten gekoppeld of uitgelijnd. De aangepaste vertaler rapporteert het aantal zinnen dat kan worden gekoppeld als de uitgelijnde zinnen in elk van de gegevens sets.

## <a name="pairing-and-alignment-process"></a>Proces voor koppelen en uitlijnen

Aangepaste vertalers leren de vertalingen van zinnen per zin. Er wordt een zin van de bron gelezen en vervolgens de vertaling van deze zin vanaf het doel. Vervolgens worden woorden en zinsdelen in deze twee zinnen op elkaar uitgelijnd. Dit proces maakt het mogelijk om een kaart van de woorden en zinsdelen in één zin te maken naar de equivalente woorden en zinsdelen in de vertaling van deze zin. Uitlijning probeert ervoor te zorgen dat de systeem treinen op zinnen met vertalingen van elkaar worden uitgevoerd.

## <a name="pre-aligned-documents"></a>Vooraf uitgelijnde documenten

Als u weet dat u parallelle documenten hebt, kunt u de opmaak van de zin onderdrukken door vooraf uitgelijnde tekst bestanden op te geven. U kunt alle zinnen uit beide documenten uitpakken in een tekst bestand, één zin per regel geordend en uploaden met een `.align` uitbrei ding. De `.align` uitbrei ding signaleert aangepaste vertalers, waarna de uitlijning van de zin moet worden overgeslagen.

Voor de beste resultaten probeert u te controleren of u één zin per regel hebt in uw bestanden. U hebt geen nieuwe regel tekens binnen een zin, omdat dit resulteert in slechte uitlijning.

## <a name="suggested-minimum-number-of-sentences"></a>Voorgesteld minimum aantal zinnen

Voor een succes volle training bevat de onderstaande tabel het minimale aantal zinnen dat is vereist in elk document type.Deze beperking is een veiligheids netwerk om ervoor te zorgen dat uw parallelle zinnen voldoende unieke woorden lijst hebben om een omzettings model te kunnen trainen. De algemene richt lijn heeft meer parallelle zinnen in het domein van de vertaling van menselijke vertalingen en moet kwalitatief hoogwaardige modellen produceren.

| Document type   | Aanbevolen minimum aantal zinnen | Maximum aantal zinnen |
|------------|--------------------------------------------|--------------------------------|
| Training   | 10.000                                     | Geen bovengrens                 |
| Afstemmen     | 500                                      | 2500       |
| Testen    | 500                                      | 2500  |
| Woordenlijst | 0                                          | Geen bovengrens                 |

> [!NOTE]
> - De training kan niet worden gestart en mislukt als er niet wordt voldaan aan het minimum aantal zinnen van 10.000 voor training. 
> - Afstemmen en testen zijn optioneel. Als u deze niet opgeeft, zal het systeem een passend percentage verwijderen van de training die moet worden gebruikt voor validatie en testen. 
> - U kunt een model trainen met alleen woordenlijst gegevens. Raadpleeg [Wat is een woorden lijst](./what-is-dictionary.md).

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het gebruik van een [woorden lijst](what-is-dictionary.md) in Custom Translator.

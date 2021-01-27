---
title: Wat is een BLEU-Score? -Aangepaste vertaler
titleSuffix: Azure Cognitive Services
description: BLEU is een meting van de verschillen tussen machine vertalingen en door de mens gemaakte referentie vertalingen van dezelfde bron zin.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: lajanuar
ms.openlocfilehash: 6691c1b91a3d26312e279cadc97a1bb95838c04f
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895574"
---
# <a name="what-is-a-bleu-score"></a>Wat is een BLEU-Score?

[Bleu (tweetalige evaluatie-onderstudie)](https://en.wikipedia.org/wiki/BLEU) is een meting van de verschillen tussen een automatische vertaling en een of meer door de mens gemaakte referentie vertalingen van dezelfde bron zin.

## <a name="scoring-process"></a>Score proces

Het BLEU-algoritme vergelijkt opeenvolgende zinnen van de automatische vertaling met de opeenvolgende woord groepen die worden gevonden in de referentie conversie en telt het aantal overeenkomsten op een gewogen manier. Deze overeenkomst is onafhankelijk van positie. Een hogere nauw keurigheid duidt op een hogere mate van gelijkenis met de referentie vertaling en een hogere score. Er wordt geen rekening gehouden met intelligibility en grammatica correctie.

## <a name="how-bleu-works"></a>Hoe BLEU werkt?

De sterkte van BLEU is dat deze goed wordt afgestemd op de beslissing van de mens door het berekenen van de afzonderlijke beslissings fouten van de zinnen ten opzichte van een test verzameling, in plaats van dat er wordt geprobeerd om voor elke zin de exacte menselijke beslissing te nemen.

[Hier volgt](https://youtu.be/-UqDljMymMg)een uitgebreidere bespreking van Bleu-scores.

BLEU resultaten zijn sterk afhankelijk van de breedte van uw domein, de consistentie van de test gegevens met de training en het afstemmen van gegevens en de hoeveelheid gegevens die u beschikbaar hebt om te trainen. Als uw modellen zijn getraind op een beperkt domein en uw trainings gegevens consistent zijn met uw test gegevens, kunt u een hoge BLEU-Score verwachten.

>[!NOTE]
>Een vergelijking tussen BLEU-scores is alleen verantwoord wanneer BLEU-resultaten worden vergeleken met dezelfde testset, hetzelfde taal paar en dezelfde MT-engine. Een BLEU-Score van een andere testset is gebonden aan een andere set.

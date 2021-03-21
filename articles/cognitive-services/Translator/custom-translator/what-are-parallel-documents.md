---
title: Wat zijn parallelle documenten? -Aangepaste vertaler
titleSuffix: Azure Cognitive Services
description: Parallelle documenten zijn paren van documenten waarbij de ene vertaling van de andere is. Een document in het paar bevat zinnen in de bron taal en het andere document bevat de volgende zinnen die in de doel taal worden vertaald.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 64192014c9dfe5bb958e730dae62e504f0976fa1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98895591"
---
# <a name="what-are-parallel-documents"></a>Wat zijn parallelle documenten?

Parallelle documenten zijn paren van documenten waarbij de ene vertaling van de andere is. Een document in het paar bevat zinnen in de bron taal en het andere document bevat de volgende zinnen die in de doel taal worden vertaald.
Het maakt niet uit welke taal is gemarkeerd als ' Bron ' en welke taal is gemarkeerd als ' doel ': een parallel document kan worden gebruikt om een Vertaal systeem te trainen in een van beide richtingen.

## <a name="requirements"></a>Vereisten

U hebt mini maal 10.000 unieke, afgestemde parallelle zinnen nodig om een systeem te trainen. Deze beperking is een veiligheids netwerk om ervoor te zorgen dat uw parallelle zinnen voldoende unieke woorden lijst hebben om een omzettings model te kunnen trainen. Als best practice, voeg voortdurend meer parallelle inhoud toe en opnieuw te trainen om de kwaliteit van uw Vertaal systeem te verbeteren. Raadpleeg de uitlijning van een [zin](./sentence-alignment.md).

Micro soft vereist dat documenten die zijn geüpload naar de aangepaste vertaler geen inbreuk maken op de copyright-of intellectuele eigenschappen van derden. Voor meer informatie raadpleegt u de [gebruiks voorwaarden](https://azure.microsoft.com/support/legal/cognitive-services-terms/).
Het uploaden van een document via de portal heeft geen invloed op het eigendom van het intellectueel eigendom in het document zelf.

## <a name="use-of-parallel-documents"></a>Gebruik van parallelle documenten

Parallelle documenten worden gebruikt door het systeem:

1.  Voor meer informatie over hoe woorden, zinsdelen en zinnen worden vaak tussen de twee talen toegewezen.

2.  Voor meer informatie over het verwerken van de juiste context, afhankelijk van de omliggende woord groepen. Een woord wordt mogelijk niet altijd omgezet in exact hetzelfde woord in de andere taal.

Zorg er als best practice voor dat er 1:1 een overeenkomst is tussen de bron-en doel taal versies van de documenten.

Als uw project specifiek is voor een domein (categorie), moeten uw documenten in de terminologie in die categorie consistent zijn. De kwaliteit van het resulterende Vertaal systeem is afhankelijk van het aantal zinnen in uw documentenset en de kwaliteit van de zinnen. De meer voor beelden van uw documenten bevatten diverse gebruiks mogelijkheden voor een specifiek woord voor uw categorie, de betere taak die het systeem tijdens de omzetting kan doen.

Geüploade documenten zijn privé voor elke werk ruimte en kunnen worden gebruikt voor zoveel projecten of trainingen als u wilt. Zinnen die uit uw documenten zijn geëxtraheerd, worden afzonderlijk in uw opslag plaats opgeslagen als tekst bestanden zonder opmaak en kunnen worden verwijderd. Gebruik het aangepaste conversie programma niet als document opslagplaats. u kunt de documenten die u hebt geüpload, niet downloaden in de indeling die u hebt geüpload.



## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het gebruik van een [woorden lijst](what-is-dictionary.md) in Custom Translator.
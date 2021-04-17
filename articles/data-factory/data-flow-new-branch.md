---
title: Meerdere vertakkingen in toewijzingsgegevensstroom
description: Gegevensstromen repliceren in toewijzingsgegevensstroom met meerdere vertakkingen
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 04/16/2021
ms.openlocfilehash: f9f2bf2e2204e6b74bb8a31ac856dbe276a6e983
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588748"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>Een nieuwe vertakking maken in de toewijzingsgegevensstroom

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Voeg een nieuwe vertakking toe om meerdere sets bewerkingen en transformaties uit te voeren op dezelfde gegevensstroom. Het toevoegen van een nieuwe vertakking is handig als u dezelfde bron wilt gebruiken voor meerdere sinks of voor het samenvoegen van gegevens zelf.

Er kan een nieuwe vertakking worden toegevoegd vanuit de transformatielijst die vergelijkbaar is met andere transformaties. **Nieuwe vertakking** is alleen beschikbaar als actie wanneer er een bestaande transformatie is na de transformatie die u probeert te vertakken.

![Schermopname van de optie Nieuwe vertakking in het menu Meerdere invoer/uitvoer.](media/data-flow/new-branch2.png "Een nieuwe vertakking toevoegen")

In het onderstaande voorbeeld leest de gegevensstroom gegevens over taxiriten. Uitvoer die is geaggregeerd door zowel dag als leverancier is vereist. In plaats van twee afzonderlijke gegevensstromen te maken die uit dezelfde bron lezen, kan er een nieuwe vertakking worden toegevoegd. Op deze manier kunnen beide aggregaties worden uitgevoerd als onderdeel van dezelfde gegevensstroom. 

![Schermopname van de gegevensstroom met twee vertakkingen uit de bron.](media/data-flow/new-branch.png "Een nieuwe vertakking toevoegen")

> [!NOTE]
> Wanneer u op het plus-plus (+) klikt om transformaties aan uw grafiek toe te voegen, ziet u alleen de optie Nieuwe vertakking wanneer er volgende transformatieblokken zijn. Dit komt doordat New Branch een verwijzing naar de bestaande stroom maakt en verdere upstream-verwerking vereist om op te werken. Als u de optie Nieuwe vertakking niet ziet, voegt u eerst een afgeleide kolom of een andere transformatie toe, gaat u terug naar het vorige blok en ziet u Nieuwe vertakking als optie.

## <a name="next-steps"></a>Volgende stappen

Na het vertakken wilt u mogelijk de [gegevensstroomtransformaties gebruiken](data-flow-transformation-overview.md)

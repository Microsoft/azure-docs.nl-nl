---
title: Meerdere vertakkingen in toewijzing van gegevens stroom
description: Gegevens stromen repliceren in toewijzing van gegevens stroom met meerdere vertakkingen
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/08/2020
ms.openlocfilehash: a11dbfbd6d6510b5c421e54cd2547c3aedb1bfb6
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100378193"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>Een nieuwe vertakking maken in de stroom voor het toewijzen van gegevens

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Voeg een nieuwe vertakking toe om meerdere bewerkingen en trans formaties voor dezelfde gegevens stroom uit te voeren. Het toevoegen van een nieuwe vertakking is handig als u dezelfde bron wilt gebruiken voor meerdere sinks of voor het samen voegen van gegevens.

Een nieuwe vertakking kan vanuit de transformatie lijst worden toegevoegd, vergelijkbaar met andere trans formaties. **Nieuwe vertakkingen** zijn alleen beschikbaar als actie wanneer er een trans formatie is die volgt op de trans formatie die u wilt vertakkingen.

![Scherm afbeelding toont de optie nieuwe vertakking in het menu meerdere invoer/uitvoer.](media/data-flow/new-branch2.png "Een nieuwe vertakking toevoegen")

In het onderstaande voor beeld lezen de gegevens stroom de taxi reis gegevens. Uitvoer geaggregeerd per dag en leverancier is vereist. In plaats van twee afzonderlijke gegevens stromen te maken die van dezelfde bron worden gelezen, kan een nieuwe vertakking worden toegevoegd. Op deze manier kunnen beide aggregaties worden uitgevoerd als onderdeel van dezelfde gegevens stroom. 

![Scherm afbeelding toont de gegevens stroom met twee vertakkingen uit de bron.](media/data-flow/new-branch.png "Een nieuwe vertakking toevoegen")

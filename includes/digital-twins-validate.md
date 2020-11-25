---
author: baanders
description: bestand insluiten met een beschrijving van het valideren van Azure Digital Apparaatdubbels-modellen
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: 58ae9c187f50bb7eb3f1ccc2a618275885121267
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026200"
---
> [!TIP]
> Nadat u een model hebt gemaakt, is het raadzaam om uw modellen offline te valideren voordat u ze uploadt naar uw Azure Digital Apparaatdubbels-exemplaar.

Er is een taal-neutraal-voor beeld beschikbaar voor het valideren van model documenten om er zeker van te zijn dat de DTDL juist is voordat u deze uploadt naar uw exemplaar. Deze bevindt zich hier: [**DTDL validator**](/samples/azure-samples/dtdl-validator/dtdl-validator)-voor beeld.

* Het DTDL-voor beeld van de validatie functie is gebaseerd op een .NET DTDL-parser-bibliotheek, die beschikbaar is op NuGet als een bibliotheek aan de client zijde: [**micro soft. Azure. DigitalTwins. parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/). U kunt de bibliotheek ook rechtstreeks gebruiken om uw eigen validatie oplossing te ontwerpen. Wanneer u de parser-bibliotheek gebruikt, moet u ervoor zorgen dat u een versie gebruikt die compatibel is met de versie die door Azure Digital Apparaatdubbels wordt uitgevoerd. Dit is momenteel versie *3.12.4*.

Meer informatie over het voor beeld van de validator en de parser-bibliotheek, inclusief gebruiks voorbeelden, vindt u in [*procedures: modellen parseren en valideren*](../articles/digital-twins/how-to-parse-models.md).
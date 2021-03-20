---
title: Geheugenlek detecteren-Azure-toepassing Insights Slimme detectie
description: Bewaak toepassingen met Azure-toepassing inzichten voor mogelijke geheugen lekkages.
ms.topic: conceptual
ms.date: 12/12/2017
ms.openlocfilehash: 3fe58cd7d61246c5565cd89fa782c8a977f09499
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86539887"
---
# <a name="memory-leak-detection-preview"></a>Detectie van geheugenlek (preview-versie)

Application Insights analyseert automatisch het geheugen verbruik van elk proces in uw toepassing en kan u waarschuwen over mogelijke geheugen lekken of een groter geheugen gebruik.

Voor deze functie is geen speciale configuratie vereist, behalve het [configureren van prestatie meter items](./performance-counters.md) voor uw app. Het is actief wanneer uw app voldoende geheugen prestatie meter items telemetrie (bijvoorbeeld persoonlijke bytes) genereert.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Wanneer kan ik dit type Smart Detection-melding krijgen?
Een typische melding volgt een consistente toename van het geheugen verbruik gedurende een lange periode, in een of meer processen en/of een of meer machines, die deel uitmaken van uw toepassing. Machine learning-algoritmen worden gebruikt voor het detecteren van een groter geheugen verbruik dat overeenkomt met het patroon van een geheugenlek.

## <a name="does-my-app-really-have-a-problem"></a>Heeft mijn app echt een probleem?
Nee, een melding betekent niet dat uw app een probleem heeft. Hoewel er in het geval van geheugen lekken meestal een toepassings probleem duiden, kunnen deze patronen typisch zijn voor uw specifieke proces, of kunnen ze natuurlijk zakelijke rechtvaardiging hebben en kunnen ze worden genegeerd.

## <a name="how-do-i-fix-it"></a>Hoe kan ik dit probleem oplossen?
De meldingen bevatten diagnostische gegevens voor ondersteuning bij het diagnostische analyse proces:
1. **Sorteren.** In de melding ziet u de hoeveelheid geheugen toename (in GB) en het tijds bereik waarin het geheugen is toegenomen. Dit kan handig zijn bij het toewijzen van een prioriteit aan het probleem.
2. **Ligt.** Hoeveel computers hebben het geheugen lekkage patroon vertoond? Hoeveel uitzonde ringen zijn er geactiveerd tijdens het mogelijke geheugenlek? Deze informatie kan worden opgehaald uit de melding.
3. **Vaststellen.** De detectie bevat het geheugen lekkage patroon, waarbij het geheugen gebruik van het proces gedurende een periode wordt weer gegeven. U kunt ook de koppeling Verwante items en rapporten met ondersteunende informatie gebruiken om u te helpen het probleem verder te onderzoeken.

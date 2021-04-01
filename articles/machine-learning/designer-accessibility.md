---
title: Toegankelijkheids functies in de ontwerp functie gebruiken
titleSuffix: Azure Machine Learning
description: Meer informatie over de sneltoetsen en scherm lezers toegankelijkheids functies die beschikbaar zijn in de ontwerp functie.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: peterlu
author: peterclu
ms.date: 01/09/2020
ms.custom: designer
ms.openlocfilehash: 86cb5260a59f864658fbb7ac1c1da2d943c6253e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "90893420"
---
# <a name="use-a-keyboard-to-use-azure-machine-learning-designer"></a>Een toetsen bord gebruiken om Azure Machine Learning Designer te gebruiken

Meer informatie over het gebruik van een toetsen bord en scherm lezer om Azure Machine Learning Designer te gebruiken. Zie [sneltoetsen in de Azure Portal](../azure-portal/azure-portal-keyboard-shortcuts.md) voor een lijst met sneltoetsen die overal in de Azure Portal werken.

Deze werk stroom is getest met [Verteller](https://support.microsoft.com/help/22798/windows-10-complete-guide-to-narrator) en [Jaws](https://www.freedomscientific.com/products/software/jaws/), maar moet worden gebruikt met andere standaard scherm lezers.

## <a name="navigate-the-pipeline-graph"></a>Navigeren in de pipeline-grafiek

De pipeline-grafiek is ingedeeld als een geneste lijst. De buitenste lijst is een module lijst waarin alle modules in de pipeline-grafiek worden beschreven. De interne lijst is een lijst met verbindingen, waarmee alle verbindingen van een specifieke module worden beschreven.  

1. Gebruik in de lijst module de pijl toets om modules te scha kelen.
1. Tabblad gebruiken om de lijst met verbindingen voor de doel module te openen.
1. Gebruik de pijl toets om te scha kelen tussen de verbindings poorten voor de module.
1. Gebruik G om naar de doel module te gaan.

## <a name="edit-the-pipeline-graph"></a>De pijplijn grafiek bewerken

### <a name="add-a-module-to-the-graph"></a>Een module toevoegen aan de grafiek

1. Gebruik CTRL + F6 om de focus van het canvas naar de module structuur te verplaatsen.
1. Zoek de gewenste module in de module structuur met behulp van het standaard besturings element voor de structuur weergave.

### <a name="edit-a-module"></a>Een module bewerken

Een module verbinden met een andere module:

1. Gebruik CTRL + SHIFT + H bij het richten op een module in de module lijst om het hulp programma voor verbindingen te openen.
1. Bewerk de verbindings poorten voor de module.

De eigenschappen van een module aanpassen:

1. Gebruik CTRL + SHIFT + E bij het richten op een module om de module-eigenschappen te openen.
1. Bewerk de eigenschappen van de module.

## <a name="navigation-shortcuts"></a>Navigatie snelkoppelingen

| Toetsaanslag | Description |
|-|-|
| Ctrl + F6 | Scha kelen tussen de focus op het canvas en de module structuur |
| CTRL + F1   | De informatie kaart openen bij het richten op een knoop punt in de module structuur |
| CTRL + SHIFT + H | Het hulp programma voor de verbinding openen wanneer de focus zich op een knoop punt bevindt |
| CTRL + SHIFT + E | Module-eigenschappen openen wanneer de focus zich op een knoop punt bevindt |
| CTRL + G | Focus verplaatsen naar het eerste mislukte knoop punt als de uitvoering van de pijp lijn is mislukt |

## <a name="action-shortcuts"></a>Actie snelkoppelingen

Gebruik de volgende sneltoetsen met de toegangs sleutel. Zie voor meer informatie over toegangs sleutels https://en.wikipedia.org/wiki/Access_key .

| Toetsaanslag | Bewerking |
|-|-|
| Toegangs toets + R | Uitvoeren |
| Toegangs toets + P | Publiceren |
| Toegangs toets + C | Klonen |
| Toegangs toets + D | Implementeren |
| Toegangs toets + I | Pijp lijn voor de inleiding maken/bijwerken |
| Toegangs toets + B | Pijp lijn voor batch-interferentie maken/bijwerken |
| Toegangs toets + K | Vervolg keuzelijst voor het maken van een pipeline voor inactieve pijp lijn openen |
| Toegangs toets + U | Vervolg keuzelijst voor het bijwerken van de pipeline voor updates openen |
| Toegangs toets + M | Vervolg keuzelijst meer (...) openen |

## <a name="next-steps"></a>Volgende stappen

- [Hoog contrast inschakelen of het thema wijzigen](../azure-portal/set-preferences.md#choose-a-theme-or-enable-high-contrast)
- [Toegankelijkheids hulpmiddelen van micro soft](https://www.microsoft.com/accessibility)

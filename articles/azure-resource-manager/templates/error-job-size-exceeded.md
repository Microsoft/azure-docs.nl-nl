---
title: Fout door taak grootte overschreden
description: Hierin wordt beschreven hoe u fouten oplost wanneer de taak grootte of sjabloon te groot is.
ms.topic: troubleshooting
ms.date: 01/19/2021
ms.openlocfilehash: 1fde4918aff6e3bf494876f83c5b4313b3c5f3d2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98610400"
---
# <a name="resolve-errors-for-job-size-exceeded"></a>Fouten oplossen voor de taak grootte is overschreden

In dit artikel wordt beschreven hoe u de **JobSizeExceededException** -en **DeploymentJobSizeExceededException** -fouten kunt oplossen.

## <a name="symptom"></a>Symptoom

Wanneer u een sjabloon implementeert, wordt er een fout melding weer gegeven dat de implementatie limieten heeft overschreden.

## <a name="cause"></a>Oorzaak

U krijgt deze fout melding wanneer de implementatie een van de toegestane limieten overschrijdt. Normaal gesp roken ziet u deze fout wanneer uw sjabloon of de taak waarmee de implementatie wordt uitgevoerd te groot is.

De implementatie taak mag niet groter zijn dan 1 MB. De taak omvat metagegevens over de aanvraag. Voor grote sjablonen mogen de metagegevens in combinatie met de sjabloon de toegestane grootte van een taak niet overschrijden.


De sjabloon mag niet groter zijn dan 4 MB. De limiet van 4 MB is van toepassing op de uiteindelijke status van de sjabloon nadat deze is uitgebreid voor resource definities die gebruikmaken van [kopiëren](copy-resources.md) om veel instanties te maken. De uiteindelijke status bevat ook de opgeloste waarden voor variabelen en parameters.

Andere limieten voor de sjabloon zijn:

* 256-para meters
* 256 variabelen
* 800 bronnen (met inbegrip van het aantal kopieën)
* 64 uitvoer waarden
* 24.576 tekens in een sjabloon expressie

## <a name="solution-1---simplify-template"></a>Oplossing 1: sjabloon vereenvoudigen

Uw eerste optie is om de sjabloon te vereenvoudigen. Deze optie werkt als uw sjabloon veel verschillende resource typen implementeert. Overweeg om de sjabloon te verdelen in [gekoppelde sjablonen](linked-templates.md). Verdeel uw resource typen in logische groepen en voeg een gekoppelde sjabloon toe voor elke groep. Als u bijvoorbeeld veel netwerk bronnen wilt implementeren, kunt u deze resources naar een gekoppelde sjabloon verplaatsen.

U kunt andere resources instellen als afhankelijk van de gekoppelde sjabloon en [waarden ophalen uit de uitvoer van de gekoppelde sjabloon](linked-templates.md#get-values-from-linked-template).

## <a name="solution-2---reduce-name-size"></a>Oplossing 2: de naam grootte verlagen

Probeer de lengte van de namen die u gebruikt voor [para meters](template-parameters.md), [variabelen](template-variables.md)en [uitvoer](template-outputs.md)in te korten. Wanneer deze waarden worden herhaald via Kopieer lussen, wordt een grote naam meermaals vermenigvuldigd.

## <a name="solution-3---use-serial-copy"></a>Oplossing 3: een seriële kopie gebruiken

Overweeg om uw kopie-lus [te wijzigen van parallel naar seriële verwerking](copy-resources.md#serial-or-parallel). Gebruik deze optie alleen als u vermoedt dat de fout afkomstig is van het implementeren van een groot aantal resources via een kopie. Met deze wijziging kan uw implementatie tijd aanzienlijk toenemen omdat de resources niet parallel worden geïmplementeerd.

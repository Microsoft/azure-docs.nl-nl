---
title: De architectuur van een predikaat voor het gebruik van de API voor afwijkings detectie multidimensionale
titleSuffix: Azure Cognitive Services
description: Referentie architectuur voor het gebruik van de Anomaliey detector multidimensionale-Api's om anomalie detectie toe te passen op uw tijdreeks gegevens voor predictief onderhoud.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: mbullwin
keywords: afwijkingsdetectie, machine learning, algoritmes
ms.openlocfilehash: 4d379cfe01dcbd88531b98a32b45e0b30f6adeef
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315474"
---
# <a name="predictive-maintenance-solution-with-anomaly-detector-multivariate"></a>Oplossing voor voor speld onderhoud met anomalie detectie multidimensionale

Veel verschillende sectoren hebben voorspellende onderhouds oplossingen nodig om Risico's te verminderen en inzicht te krijgen in het verwerken van gegevens van hun apparatuur. Voor speld onderhoud wordt de voor waarde van apparatuur geëvalueerd door online bewaking uit te voeren. Het doel is om onderhoud uit te voeren voordat het apparaat wordt gedegradeerd of verbroken.

Het bewaken van de integriteits status van apparatuur kan lastig zijn, omdat elk onderdeel in het apparaat tien tallen signalen kan genereren, bijvoorbeeld trillingen, afdruk standen en rotatie.  Dit kan nog complexer zijn wanneer deze signalen een impliciete relatie hebben en moeten worden bewaakt en geanalyseerd. Het definiëren van verschillende regels voor die signalen en het hand matig correleren van elkaar, kan kostbaar zijn. Met de functie multidimensionale van anomalie detectie kunt u:

* Meerdere gecorreleerde signalen moeten samen worden bewaakt en de intercorrelaties ertussen worden verwerkt in het model.
* In elke vastgelegde afwijkingen kan de classificatie van de bijdrage van verschillende signalen helpen bij een afwijkende uitleg en analyse van de hoofd oorzaak van het incident.
* Het multidimensionale-model voor anomalie detectie is op een onbewaakte manier gebouwd. Modellen kunnen specifiek worden getraind voor verschillende soorten apparatuur.

Hier bieden we een referentie architectuur voor een oplossing voor voor speld onderhoud op basis van anomalie detectie multidimensionale.

## <a name="reference-architecture"></a>Referentiearchitectuur

[![Het architectuur diagram dat begint bij sensor gegevens die aan de rand worden verzameld met een stuk industriële apparatuur en houdt de verwerkings-en analyse pijplijn bij naar een eind uitvoer van een incident waarschuwing die wordt gegenereerd na afwijkende detector uitvoeringen. ](../media/multivariate-architecture/multivariate-architecture.png)](../media/multivariate-architecture/multivariate-architecture.png#lightbox)

In de bovenstaande architectuur worden streaming-gebeurtenissen die afkomstig zijn van sensor gegevens opgeslagen in Azure Data Lake en vervolgens verwerkt door een module voor het transformeren van gegevens die moet worden geconverteerd naar een indeling met een tijd reeks. Ondertussen zal de streaming-gebeurtenis realtime detectie activeren met het getrainde model. Over het algemeen is er een module voor het beheren van de levens cyclus van het multidimensionale-model, zoals *Bridge-service* in deze architectuur.

**Model training**: voordat u de anomalie detectie multidimensionale gebruikt om afwijkingen voor een onderdeel of apparatuur te detecteren. We moeten een model trainen op specifieke signalen (time-series) die zijn gegenereerd door deze entiteit. De *Bridge-service* haalt historische gegevens op en verzendt een trainings taak naar de afwijkings detector en behoudt de model-id in de meta opslag van het *model* .

**Model validatie**: de trainings tijd van een bepaald model kan variëren op basis van het gegevens volume van de training. De *Bridge-service* kan regel matig de model status en diagnostische gegevens opvragen. Het valideren van de model kwaliteit kan nood zakelijk zijn voordat u deze online zet. Als er labels in het scenario zijn, kunnen deze labels worden gebruikt om de kwaliteit van het model te controleren. Anders kunnen de diagnostische gegevens worden gebruikt om de model kwaliteit te evalueren en kunt u ook detectie van historische gegevens met het getrainde model uitvoeren en het resultaat evalueren om de geldigheid van het model te backtest.

**Model** decoderen: Online detectie wordt uitgevoerd met het geldige model en de resultaat-ID kan worden opgeslagen in de *tabel* voor decoderen. Het trainings proces en het proces voor het afwijzen van de interferentie worden op asynchrone wijze uitgevoerd. In het algemeen kan een detectie taak binnen enkele seconden worden voltooid. De signalen die voor detectie worden gebruikt, moeten dezelfde zijn die voor de training zijn gebruikt. Als we bijvoorbeeld trillingen, stand en rotatie voor training gebruiken, moet de drie signalen als invoer worden opgenomen in de detectie.

**Melding incidenten** De detectie resultaten kunnen worden opgevraagd met resultaat-Id's. Elk resultaat bevat de ernst van elke afwijkingen en de contributie positie. De positie van de contributie kan worden gebruikt om te begrijpen waarom deze afwijkend is gebeurd en welk signaal dit incident veroorzaakt. Er kunnen verschillende drempel waarden worden ingesteld op de ernst om waarschuwingen en meldingen te genereren die moeten worden verzonden naar veld engineers om onderhouds werkzaamheden uit te voeren.

## <a name="next-steps"></a>Volgende stappen

- [Quick](../quickstarts/client-libraries-multivariate.md)starts.
- [Best practices](../concepts/best-practices-multivariate.md): dit artikel is een aanbevolen patroon voor gebruik met de multidimensionale-api's.
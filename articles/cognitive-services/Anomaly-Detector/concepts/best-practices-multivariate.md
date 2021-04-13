---
title: Aanbevolen procedures voor het gebruik van de multidimensionale-API voor anomalie detectie
titleSuffix: Azure Cognitive Services
description: Best practices voor het gebruik van de Anomaliey detector multidimensionale-API om anomalie detectie toe te passen op uw time series-gegevens.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: mbullwin
keywords: afwijkingsdetectie, machine learning, algoritmes
ms.openlocfilehash: 7de25b4a099c706c05b32b52492096923033f822
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315486"
---
# <a name="multivariate-time-series-anomaly-detector-best-practices"></a>Best practices voor anomalie detectie multidimensionale time series

In dit artikel vindt u meer informatie over aanbevolen procedures voor het gebruik van de multidimensionale-anomalie detectie-Api's.

## <a name="how-to-prepare-data-for-training"></a>Gegevens voorbereiden voor training

Voor het gebruik van de anomalie detectie multidimensionale-Api's moeten we ons eigen model trainen voordat ze detectie kunnen gebruiken. Gegevens die worden gebruikt voor de training, zijn een batch van een tijd reeks, elke time series moet in CSV-indeling zijn met twee kolommen, tijds tempel en waarde. Alle tijd reeksen moeten worden ingepakt in één ZIP-bestand en geüpload naar Azure Blob-opslag. Standaard wordt de bestands naam gebruikt om de variabele voor de tijd reeks weer te geven. U kunt ook een extra meta.jsvoor het bestand opnemen in het zip-bestand als u wilt dat de naam van de variabele afwijkt van de naam van het zip-bestand. Zodra we een [URL voor BLOB SAS (Shared Access signatures)](../../../storage/common/storage-sas-overview.md)hebben gegenereerd, kunnen we deze gebruiken voor de training.

## <a name="data-quality-and-quantity"></a>Kwaliteit en hoeveelheid van de gegevens

De afwijkende detector multidimensionale-API maakt gebruik van geavanceerde diepe Neural-netwerken om normale patronen uit historische gegevens te ontdekken en te voors pellen of toekomstige waarden afwijkingen zijn. De kwaliteit en hoeveelheid trainings gegevens zijn belang rijk voor het trainen van een optimaal model. Naarmate het model normale patronen uit historische gegevens leert, moeten de opleidings gegevens de algemene normale status van het systeem vertegenwoordigen. Het model is moeilijk om deze typen patronen te ontdekken als de trainings gegevens vol afwijkingen zijn. Het model heeft ook miljoenen para meters en er is een minimum aantal gegevens punten nodig om een optimale set para meters te leren kennen. De regel algemeen is dat u ten minste 15.000 gegevens punten per variabele moet opgeven om het model op de juiste wijze te kunnen trainen. Hoe meer gegevens, hoe beter het model.

Het is gebruikelijk dat veel tijd reeksen waarden missen, wat van invloed kan zijn op de prestaties van getrainde modellen. De ontbrekende verhouding van elke tijd reeks moet worden beheerd onder een redelijke waarde. Een tijd reeks met 90% waarden ontbreekt, bevat weinig informatie over normale patronen van het systeem. Het model kan nog erger zijn, maar kan ook gevulde waarden als normale patronen beschouwen. Dit zijn doorgaans rechte segmenten of constante waarden. Wanneer nieuwe gegevens worden doorstromen in, kunnen de gegevens worden gedetecteerd als afwijkingen.

Een aanbevolen drempel waarde voor het maximum aantal ontbrekende waarden is 20%, maar een hogere drempel waarde kan in bepaalde omstandigheden acceptabel zijn. Als u bijvoorbeeld een tijd reeks hebt met een granulatie van één minuut en een andere tijd reeks met granulatie per uur.  Elk uur zijn er 60 gegevens punten per minuut aan gegevens en 1 gegevens punt voor elk uur, wat betekent dat de ontbrekende verhouding voor uur gegevens 98,33% is. Het is echter wel handig om de gegevens per uur in te vullen met de enige waarde als de tijd reeks van het uur niet te veel schommelt.

## <a name="parameters"></a>Parameters

### <a name="sliding-window"></a>Schuif venster

Multidimensionale anomalie detectie neemt een segment gegevens punten van lengte `slidingWindow` als invoer en beslist of het volgende gegevens punt een afwijkend is. Hoe groter de voorbeeld lengte, hoe meer gegevens er worden overwogen voor een beslissing. Houd bij het kiezen van een juiste waarde voor `slidingWindow` : eigenschappen van invoer gegevens en de afweging tussen training/afnemende tijd en mogelijke prestatie verbetering in acht. `slidingWindow` bestaat uit een geheel getal tussen 28 en 2880. U kunt bepalen hoeveel gegevens punten als invoer worden gebruikt, op basis van het feit of uw gegevens periodiek zijn en de sampling frequentie voor uw gegevens.

Als uw gegevens periodiek zijn, kunt u 1-3-cycli als invoer toevoegen en wanneer uw gegevens worden bemonsterd op basis van een hoge frequentie (kleine granulatie), zoals gegevens over het niveau van minuten of op het tweede niveau, kunt u meer gegevens als invoer selecteren. Een ander probleem is dat meer invoer kan leiden tot meer trainings-en inactiviteits tijd, en er is geen garantie dat de prestaties van een hoger niveau worden gegarandeerd. Terwijl er te weinig gegevens punten zijn, kan het model moeilijk worden geconvergeerd naar een optimale oplossing. Het is bijvoorbeeld moeilijk om afwijkingen op te sporen wanneer de invoer gegevens slechts twee punten hebben.

### <a name="align-mode"></a>Uitlijning modus

De para meter `alignMode` wordt gebruikt om aan te geven hoe u meerdere tijds reeksen op tijds tempels wilt uitlijnen. Dit komt omdat er in veel tijd reeksen waarden ontbreken en dat deze op dezelfde tijds tempels moeten worden uitgelijnd voordat verdere verwerking wordt uitgevoerd. Er zijn twee opties voor deze para meter `inner join` en `outer join` . `inner join` houdt in dat er detectie resultaten worden gerapporteerd voor tijds tempels waarop **elke tijd reeks** een waarde heeft, terwijl `outer join` we detectie resultaten rapporteren op tijds tempels voor **elke tijd reeks** met een waarde.  **De `alignMode` heeft ook invloed op de invoer volgorde van het model**, dus kies een geschikt `alignMode` voor uw scenario, omdat de resultaten aanzienlijk kunnen verschillen.

Hier ziet u een voor beeld waarin verschillende `alignModel` waarden worden uitgelegd.

#### <a name="series1"></a>Series1

|tijdstempel | waarde|
----------| -----|
|`2020-11-01`| 1  
|`2020-11-02`| 2  
|`2020-11-04`| 4  
|`2020-11-05`| 5

#### <a name="series2"></a>Series2

tijdstempel | waarde  
--------- | -
`2020-11-01`| 1  
`2020-11-02`| 2  
`2020-11-03`| 3  
`2020-11-04`| 4

#### <a name="inner-join-two-series"></a>Inner join twee reeksen
  
tijdstempel | Series1 | Series2
----------| - | -
`2020-11-01`| 1 | 1
`2020-11-02`| 2 | 2
`2020-11-04`| 4 | 4

#### <a name="outer-join-two-series"></a>Outer join twee reeksen

tijdstempel | series1 | series2
--------- | - | -
`2020-11-01`| 1 | 1
`2020-11-02`| 2 | 2
`2020-11-03`| NA | 3
`2020-11-04`| 4 | 4
`2020-11-05`| 5 | NA

### <a name="fill-not-available-na"></a>Opvulling niet beschikbaar (NA)

Nadat de variabelen zijn uitgelijnd op tijds tempel door outer join, is er mogelijk een `Not Available` ( `NA` )-waarde in sommige variabelen. U kunt de methode opgeven om deze nb-waarde op te vullen. De opties `fillNAMethod` zijn `Linear` ,,, `Previous` `Subsequent`  `Zero` en `Fixed` .

| Optie     | Methode                                                                                           |
| ---------- | -------------------------------------------------------------------------------------------------|
| Lineair     | NA een lineaire interpolatie waarden door voeren                                                           |
| Vorige   | Laatste geldige waarde door geven om hiaten op te vullen. Voorbeeld: `[1, 2, nan, 3, nan, 4]` -> `[1, 2, 2, 3, 3, 4]` |
| Gebracht | Gebruik de volgende geldige waarde om hiaten op te vullen. Voorbeeld: `[1, 2, nan, 3, nan, 4]` -> `[1, 2, 3, 3, 4, 4]`       |
| Nul       | Vul de N.V.T. waarden in met 0.                                                                           |
| Opgelost      | Vul de N.V.T. waarden in met een opgegeven geldige waarde die moet worden opgegeven in `paddingValue` .          |

## <a name="model-analysis"></a>Model analyse

### <a name="training-latency"></a>Trainings latentie

De detectie training voor multidimensionale afwijkingen kan tijdrovend zijn. Vooral wanneer u een groot aantal tijds tempels hebt die worden gebruikt voor de training. Daarom is het mogelijk dat een deel van het trainings proces asynchroon is. Normaal gesp roken verzenden gebruikers een Train-taak via Train model-API. Vervolgens kunt u de model status ophalen via de `Get Multivariate Model API` . Hier wordt gedemonstreerd hoe u het resterende tijdstip kunt extra heren voordat de training is voltooid. In de multidimensionale-model van de API Get bevat een item met de naam `diagnosticsInfo` . In dit item bevindt zich een `modelState` element. Voor het berekenen van de resterende tijd moeten we en worden gebruikt `epochIds` `latenciesInSeconds` . Een epoche vertegenwoordigt één volledige cyclus door middel van de trainings gegevens. Elke 10 epoches zullen de status gegevens uitvoeren. In totaal traint we voor 100-epochen, de latentie geeft aan hoe lang een epoche kost. Met deze informatie weten we nog resterende tijd om het model te trainen.

### <a name="model-performance"></a>Modelprestaties

Multidimensionale anomalie detectie als een niet-super visie model. De beste manier om dit te evalueren is om de afwijkings resultaten hand matig te controleren. In het antwoord op het multidimensionale-model ophalen bieden we enkele basis informatie voor ons om model prestaties te analyseren. In het `modelState` element dat wordt geretourneerd door de API Get multidimensionale model, kunnen we gebruiken `trainLosses` en `validationLosses` om te evalueren of het model is getraind zoals verwacht. In de meeste gevallen wordt de twee verliezen geleidelijk verminderd. Een deel van de informatie voor ons om model prestaties te analyseren is in `variableStates` . De status lijst variabelen wordt gerangschikt op `filledNARatio` aflopende volg orde. Hoe groter de prestaties ergerten, meestal moeten we dit zo `NA ratio` veel mogelijk verminderen. `NA` kan worden veroorzaakt door ontbrekende waarden of niet-uitgelijnde variabelen van een tijds tempel perspectief.

## <a name="next-steps"></a>Volgende stappen

- [Quick](../quickstarts/client-libraries-multivariate.md)starts.
- [Meer informatie over de onderliggende algoritmen die energie afwijkingen detectie multidimensionale](https://arxiv.org/abs/2009.02040)
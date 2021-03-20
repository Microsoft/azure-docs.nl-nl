---
title: Beperking in het Service Fabric cluster resource manager
description: Meer informatie over het configureren van de door de Service Fabric cluster resource manager beschik bare restricties.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: b4d78b339bab02b5c44a31939e0da769dc21c3ec
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "75452165"
---
# <a name="throttling-the-service-fabric-cluster-resource-manager"></a>De Service Fabric cluster resource manager beperken
Zelfs als u cluster resource manager correct hebt geconfigureerd, kan het cluster worden onderbroken. Er kunnen bijvoorbeeld gelijktijdige knooppunt-en fout domein fouten optreden, wat er zou gebeuren als het tijdens een upgrade is gebeurd? Cluster resource manager probeert altijd alles te herstellen, waarbij de bronnen van het cluster worden gebruikt om het cluster opnieuw te organiseren en te herstellen. Met behulp van gashendel kunt u een backstop bieden zodat het cluster bronnen kan gebruiken om te stabiliseren: de knoop punten worden teruggehaald, het herstellen van de netwerk partities, gecorrigeerde bits worden geïmplementeerd.

Om u te helpen bij het sorteren van situaties, bevat de Service Fabric cluster resource manager verschillende gashendel. Deze gashendel zijn allemaal tamelijk grote hamers. Over het algemeen mogen ze niet worden gewijzigd zonder zorgvuldige planning en tests.

Als u de beperkingen van cluster resource manager wijzigt, moet u deze afstemmen op de verwachte werkelijke belasting. U kunt er ook voor zorgen dat u bepaalde versnellingen moet hebben, zelfs als dit betekent dat het cluster in bepaalde situaties langer duurt om te stabiliseren. Testen is vereist om de juiste waarden voor gashendel te bepalen. Vertragingen moeten hoog genoeg zijn om ervoor te zorgen dat het cluster kan reageren op wijzigingen in een redelijke hoeveelheid tijd en dat er weinig ruimte is om te voor komen dat er te veel bronnen worden verbruikt. 

Het grootste deel van de tijd dat klanten de vertraging gebruiken, is dat ze al in een omgeving met een resource beperkt waren. Enkele voor beelden zijn een beperkt aantal netwerk bandbreedte voor afzonderlijke knoop punten of schijven die geen veel stateful replica's tegelijk kunnen bouwen vanwege doorvoer beperkingen. Zonder beperking kunnen de bewerkingen deze resources overbelasten, waardoor bewerkingen mislukken of traag zijn. In deze situaties gebruiken klanten beperkingen en wisten ze de hoeveelheid tijd die het cluster zou verg Roten om een stabiele status te bereiken. Klanten begrijpen ook dat ze op een lagere totale betrouw baarheid kunnen worden uitgevoerd terwijl ze zijn beperkt.


## <a name="configuring-the-throttles"></a>De gashendel configureren

Service Fabric heeft twee mechanismen voor het beperken van het aantal replica-verplaatsingen. Het standaard mechanisme dat bestond vóór Service Fabric 5,7 vertegenwoordigt het beperken van de beperking als een absoluut aantal verplaatsingen dat is toegestaan. Dit werkt niet voor clusters van elke grootte. Met name voor grote clusters kan de standaard waarde te klein zijn, zelfs als deze nood zakelijk is, aanzienlijk vertraagd worden, ook als dat nodig is, terwijl dit niet van invloed is op kleinere clusters. Dit vorige mechanisme is vervangen door beperking op basis van percentages, wat beter past bij dynamische clusters waarin het aantal services en knoop punten regel matig wordt gewijzigd.

De vertraging is gebaseerd op een percentage van het aantal replica's in de clusters. Met de op percentage gebaseerde gashendel kunt u de regel uitdrukken: ' niet meer dan 10% van replica's verplaatsen in een interval van 10 minuten ', bijvoorbeeld.

De configuratie-instellingen voor het beperken op basis van een percentage zijn:

  - GlobalMovementThrottleThresholdPercentage: het maximum aantal verkeer dat op elk gewenst moment in het cluster is toegestaan, uitgedrukt als percentage van het totale aantal replica's in het cluster. 0 geeft aan dat er geen limiet is. De standaardwaarde is 0. Als deze instelling en GlobalMovementThrottleThreshold zijn opgegeven, wordt de meer conservatieve limiet gebruikt.
  - GlobalMovementThrottleThresholdPercentageForPlacement: het maximum aantal verplaatsingen dat is toegestaan tijdens de plaatsings fase, uitgedrukt als percentage van het totale aantal replica's in het cluster. 0 geeft aan dat er geen limiet is. De standaardwaarde is 0. Als deze instelling en GlobalMovementThrottleThresholdForPlacement zijn opgegeven, wordt de meer conservatieve limiet gebruikt.
  - GlobalMovementThrottleThresholdPercentageForBalancing: het maximum aantal verplaatsingen dat is toegestaan tijdens de Balancing-fase, uitgedrukt als percentage van het totale aantal replica's in het cluster. 0 geeft aan dat er geen limiet is. De standaardwaarde is 0. Als deze instelling en GlobalMovementThrottleThresholdForBalancing zijn opgegeven, wordt de meer conservatieve limiet gebruikt.

Wanneer u het bandbreedte percentage opgeeft, geeft u 5% op als 0,05. Het interval waarmee deze vertragingen worden bepaald, is de GlobalMovementThrottleCountingInterval, die in seconden is opgegeven.


``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThresholdPercentage" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForPlacement" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForBalancing" Value="0" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
</Section>
```

via ClusterConfig.jsop voor zelfstandige implementaties of Template.jsop voor door Azure gehoste clusters:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "GlobalMovementThrottleThresholdPercentage",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleThresholdPercentageForPlacement",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleThresholdPercentageForBalancing",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleCountingInterval",
          "value": "600"
      }
    ]
  }
]
```

### <a name="default-count-based-throttles"></a>Standaard beperking op basis van aantal
Deze informatie wordt verstrekt als u oudere clusters hebt of als u deze configuraties nog behoudt in clusters die zijn bijgewerkt. Over het algemeen is het raadzaam om deze te vervangen door de op percentage gebaseerde restricties hierboven. Omdat op percentage gebaseerde beperking standaard is uitgeschakeld, blijven deze gashendel de standaard beperking voor een cluster totdat ze zijn uitgeschakeld en vervangen door de versnellingen op basis van een percentage. 

  - GlobalMovementThrottleThreshold: met deze instelling bepaalt u het totale aantal verplaatsingen in het cluster gedurende een bepaalde periode. De hoeveelheid tijd wordt in seconden opgegeven als de GlobalMovementThrottleCountingInterval. De standaard waarde voor de GlobalMovementThrottleThreshold is 1000 en de standaard waarde voor de GlobalMovementThrottleCountingInterval is 600.
  - MovementPerPartitionThrottleThreshold: met deze instelling bepaalt u het totale aantal verplaatsingen voor elke service partitie gedurende een bepaalde periode. De hoeveelheid tijd wordt in seconden opgegeven als de MovementPerPartitionThrottleCountingInterval. De standaard waarde voor de MovementPerPartitionThrottleThreshold is 50 en de standaard waarde voor de MovementPerPartitionThrottleCountingInterval is 600.

De configuratie voor deze vertragingen volgt hetzelfde patroon als op percentage gebaseerde beperking.

## <a name="next-steps"></a>Volgende stappen
- Raadpleeg het artikel over het [verdelen](service-fabric-cluster-resource-manager-balancing.md) van de taken in het cluster voor meer informatie over hoe de cluster resource manager de belasting beheert en balanceert.
- Cluster resource manager heeft veel opties voor het beschrijven van het cluster. Lees dit artikel over [het beschrijven van een service Fabric cluster](service-fabric-cluster-resource-manager-cluster-description.md) voor meer informatie.

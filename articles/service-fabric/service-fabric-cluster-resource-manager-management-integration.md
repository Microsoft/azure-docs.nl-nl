---
title: Cluster resource Manager-beheer integratie
description: Een overzicht van de integratie punten tussen cluster bron beheer en Service Fabric beheer.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: ae80ac5833e90164fc4ff92010fd1830ae932cd2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92174046"
---
# <a name="cluster-resource-manager-integration-with-service-fabric-cluster-management"></a>Cluster resource manager-integratie met Service Fabric Cluster beheer
Het Service Fabric cluster resource manager kan geen upgrades uitvoeren in Service Fabric, maar is wel betrokken. De eerste manier waarop cluster resource Manager helpt bij het beheer, is door de gewenste status van het cluster en de services erin bij te houden. De cluster resource manager verzendt status rapporten wanneer het cluster niet in de gewenste configuratie kan worden geplaatst. Als er bijvoorbeeld onvoldoende capaciteit is, verzendt cluster resource manager status waarschuwingen en fouten die het probleem aangeven. Een ander integratie pakket heeft te maken met de werking van upgrades. Het gedrag van cluster resource manager wordt enigszins gewijzigd tijdens de upgrade.  

## <a name="health-integration"></a>Status integratie
De regels die u hebt gedefinieerd voor het plaatsen van uw services worden door de cluster resource manager voortdurend bijgehouden. Ook wordt de resterende capaciteit bijgehouden voor elke metriek op de knoop punten en in het cluster en in het cluster als geheel. Als deze niet aan deze regels kan voldoen of als er onvoldoende capaciteit is, worden status waarschuwingen en fouten verzonden. Bijvoorbeeld, als een knoop punt over capaciteit is en de cluster resource manager probeert de situatie op te lossen door services te verplaatsen. Als de situatie niet kan worden opgelost, wordt er een status waarschuwing gegeven die aangeeft welk knoop punt de capaciteit overschrijdt, en wat de metrische gegevens zijn.

Een ander voor beeld van de status waarschuwingen van Resource Manager is schendingen van plaatsings beperkingen. Als u bijvoorbeeld een plaatsings beperking hebt gedefinieerd (zoals `“NodeColor == Blue”` ) en de Resource Manager een schending van die beperking detecteert, wordt er een status waarschuwing gegeven. Dit geldt voor aangepaste beperkingen en de standaard beperkingen (zoals het fout domein en de upgrade van domein beperkingen).

Hier volgt een voor beeld van een dergelijk status rapport. In dit geval is het status rapport een van de systeem service partities. In het status bericht wordt aangegeven dat de replica's van die partitie tijdelijk zijn verpakt in te weinig upgrade domeinen.

```posh
PS C:\Users\User > Get-ServiceFabricPartitionHealth -PartitionId '00000000-0000-0000-0000-000000000001'


PartitionId           : 00000000-0000-0000-0000-000000000001
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.PLB', Property='ReplicaConstraintViolation_UpgradeDomain', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 130766528804733380
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577821
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528854889931
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577822
                        AggregatedHealthState : Ok

                        ReplicaId             : 130837073190680024
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.PLB
                        Property              : ReplicaConstraintViolation_UpgradeDomain
                        HealthState           : Warning
                        SequenceNumber        : 130837100116930204
                        SentAt                : 8/10/2015 7:53:31 PM
                        ReceivedAt            : 8/10/2015 7:53:33 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer has detected a Constraint Violation for this Replica: fabric:/System/FailoverManagerService Secondary Partition 00000000-0000-0000-0000-000000000001 is
                        violating the Constraint: UpgradeDomain Details: UpgradeDomain ID -- 4, Replica on NodeName -- Node.8 Currently Upgrading -- false Distribution Policy -- Packing
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/10/2015 7:13:02 PM, LastError = 1/1/0001 12:00:00 AM
```

Dit status bericht vertelt ons het volgende:

1. Alle replica's zelf zijn in orde: elk heeft `AggregatedHealthState : Ok`
2. De distributie beperking voor het upgrade domein wordt momenteel geschonden. Dit betekent dat een bepaald upgrade domein meer replica's uit deze partitie heeft dan het zou moeten.
3. Welk knoop punt bevat de replica die de schending veroorzaakt. In dit geval is het het knoop punt met het naam *knooppunt. 8*
4. Hiermee wordt aangegeven of er momenteel een upgrade voor deze partitie plaatsvindt (' momenteel bijwerken--onwaar ')
5. Het distributie beleid voor deze service: "distributie beleid---pakken". Dit is onderworpen aan het `RequireDomainDistribution` [plaatsings beleid](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing). In dit geval geeft het *pakket* aan dat DomainDistribution _niet_ is vereist. Daarom weten we dat er geen plaatsings beleid is opgegeven voor deze service. 
6. Wanneer het rapport is opgetreden: 8/10/2015 7:13:02 uur

Informatie zoals deze bevoegdheid waarschuwt dat in productie wordt geactiveerd zodat u weet dat er iets mis is en ook wordt gebruikt voor het detecteren en stoppen van ongeldige upgrades. In dit geval willen we zien of we kunnen nagaan waarom de Resource Manager de replica's moest inpakken in het upgrade domein. Normaal gesp roken is verpakking tijdelijk, omdat de knoop punten in de andere upgrade domeinen bijvoorbeeld uitgeschakeld waren.

Stel dat cluster resource manager probeert bepaalde services te plaatsen, maar er zijn geen oplossingen die werken. Wanneer Services niet kunnen worden geplaatst, is dit meestal een van de volgende redenen:

1. Bij een bepaalde tijdelijke situatie is het onmogelijk om deze service-instantie of replica correct te plaatsen
2. De plaatsings vereisten van de service zijn Unsatisfiable.

In dergelijke gevallen kunt u met status rapporten van cluster resource manager bepalen waarom de service niet kan worden geplaatst. We noemen dit proces de eliminatie volgorde van de beperking. Tijdens deze stap passeren het systeem de geconfigureerde beperkingen die van invloed zijn op de service en wordt vastgelegd wat ze elimineren. Op deze manier kunt u zien welke knoop punten zijn geëlimineerd en waarom de services niet kunnen worden geplaatst.

## <a name="constraint-types"></a>Beperkings typen
Laten we de verschillende beperkingen in deze status rapporten bespreken. Er worden status berichten met betrekking tot deze beperkingen weer geven wanneer replica's niet kunnen worden geplaatst.

* **ReplicaExclusionStatic** en **ReplicaExclusionDynamic**: deze beperkingen geven aan dat een oplossing is afgewezen, omdat twee service objecten van dezelfde partitie op hetzelfde knoop punt moeten worden geplaatst. Dit is niet toegestaan omdat vervolgens het mislukken van het knoop punt deze partitie zou beïnvloeden. ReplicaExclusionStatic en ReplicaExclusionDynamic zijn bijna dezelfde regel en de verschillen zijn daar niet echt van belang. Als u een schrappings volgorde voor beperkingen ziet die de beperking ReplicaExclusionStatic of ReplicaExclusionDynamic bevat, denkt het cluster resource manager voor dat er onvoldoende knoop punten zijn. Hiervoor moeten de resterende oplossingen gebruikmaken van deze ongeldige plaatsingen, die niet zijn toegestaan. De andere beperkingen in de reeks geven meestal aan waarom knoop punten in de eerste plaats worden verwijderd.
* **PlacementConstraint**: als u dit bericht ziet, betekent dit dat er enkele knoop punten zijn verwijderd omdat ze niet overeenkomen met de plaatsings beperkingen van de service. We traceren de momenteel geconfigureerde plaatsings beperkingen als onderdeel van dit bericht. Dit is normaal als er een plaatsings beperking is gedefinieerd. Als de plaatsings beperking echter onjuist zorgt voor te veel knoop punten die moeten worden verwijderd, ziet u dat.
* **NodeCapacity**: deze beperking betekent dat de cluster resource manager de replica's op de aangegeven knoop punten niet kan plaatsen, omdat deze de capaciteit zou overschrijden.
* **Affiniteit**: deze beperking geeft aan dat de replica niet kan worden geplaatst op de betrokken knoop punten, omdat dit een schending van de affiniteits beperking zou veroorzaken. Meer informatie over affiniteit vindt u in [dit artikel](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
* **FaultDomain** en **upgrade Domain**: deze beperking elimineert knoop punten als het plaatsen van de replica op de aangegeven knoop punten zou leiden tot inpakken in een bepaald probleem of een upgrade domein. Enkele voor beelden die deze beperking volgen, worden weer gegeven in het onderwerp over [fout-en upgrade domein beperkingen en resulterende gedrag](service-fabric-cluster-resource-manager-cluster-description.md)
* **PreferredLocation**: u hoeft deze beperking normaal gesp roken niet te zien bij het verwijderen van knoop punten uit de oplossing omdat deze standaard als optimalisatie wordt uitgevoerd. De voorkeurs locatie beperking is ook aanwezig tijdens de upgrade. Tijdens de upgrade wordt dit gebruikt om services terug te verplaatsen naar waar ze zich bevonden toen de upgrade werd gestart.

## <a name="blocklisting-nodes"></a>Blocklisting-knoop punten
Een ander status bericht is dat cluster resource Manager-rapporten worden gegenereerd wanneer knoop punten blocklisted zijn. U kunt blocklisting beschouwen als een tijdelijke beperking die automatisch voor u wordt toegepast. Knoop punten krijgen blocklisted wanneer ze herhaalde fouten ondervinden bij het starten van instanties van dat Service type. Knoop punten zijn blocklisted per service-type. Een knoop punt kan blocklisted zijn voor één service type, maar niet een ander. 

Tijdens de ontwikkeling zult u zien dat blocklisting vaak aan de orde zijn. bij een bepaalde fout kan de servicehost vastlopen bij het opstarten. Service Fabric probeert de servicehost een paar keer te maken en de fout blijft optreden. Na een paar pogingen krijgt het knoop punt blocklisted en probeert de cluster resource manager de service elders te maken. Als deze fout zich blijft voordoen op meerdere knoop punten, is het mogelijk dat alle geldige knoop punten in het cluster worden beëindigd. Met Blocklisting kunnen ook zoveel knoop punten worden verwijderd die niet genoeg zijn om de service te starten om te voldoen aan de gewenste schaal. Normaal gesp roken worden er aanvullende fouten of waarschuwingen van de cluster resource manager weer gegeven die aangeven dat de service lager is dan de gewenste replica of het aantal instanties, evenals status berichten die aangeven wat de fout is in de eerste plaats van de blocklisting.

Blocklisting is geen permanente voor waarde. Na een paar minuten wordt het knoop punt uit de blokkerings lijst verwijderd en kunnen de services op dat knoop punt opnieuw worden geactiveerd door Service Fabric. Als de services blijven mislukken, wordt het knoop punt blocklisted voor dat Service type opnieuw. 

### <a name="constraint-priorities"></a>Prioriteiten voor beperkingen

> [!WARNING]
> Het wijzigen van beperkings prioriteiten wordt niet aanbevolen en kan aanzienlijke nadelige gevolgen hebben voor uw cluster. De onderstaande informatie is bedoeld voor referentie van de standaard prioriteiten voor beperkingen en hun gedrag. 
>

Met al deze beperkingen is het mogelijk dat u denken dat fout domein beperkingen het belangrijkst zijn in mijn systeem. Om ervoor te zorgen dat de beperking van het fout domein niet wordt geschonden, wil ik andere beperkingen schenden. "

Beperkingen kunnen worden geconfigureerd met verschillende prioriteits niveaus. Deze zijn:

   - "hard" (0)
   - "zacht" (1)
   - "optimalisatie" (2)
   - ' uit ' (-1). 
   
De meeste beperkingen zijn standaard geconfigureerd als vaste beperkingen.

Het wijzigen van de prioriteit van beperkingen is ongebruikelijk. Er zijn momenten waarop de prioriteits prioriteiten moeten worden gewijzigd, meestal om een andere bug of gedrag te omzeilen die van invloed is op de omgeving. Over het algemeen is de flexibiliteit van de infra structuur voor de beperkings prioriteit zeer goed geweest, maar het is vaak niet nodig. Het grootste deel van de tijd is dat alles de standaard prioriteiten heeft. 

De prioriteits niveaus betekenen niet dat een bepaalde beperking _wordt_ geschonden en of er altijd aan wordt voldaan. Prioriteiten van beperkingen definiëren een volg orde waarin de beperkingen worden afgedwongen. Prioriteiten definiëren de afwegingen wanneer het onmogelijk is om aan alle beperkingen te voldoen. Normaal gesp roken kunnen aan alle beperkingen worden voldaan, tenzij er iets anders gaat in de omgeving. Enkele voor beelden van scenario's die leiden tot beperkings schendingen zijn conflicterende beperkingen of grote aantallen gelijktijdige storingen.

In geavanceerde situaties kunt u de prioriteiten van de beperking wijzigen. Stel dat u ervoor wilt zorgen dat de affiniteit altijd wordt geschonden wanneer dit nodig is voor het oplossen van problemen met knooppunt capaciteit. Hiervoor kunt u de prioriteit van de affiniteits beperking instellen op ' zacht ' (1) en de capaciteits beperking ingesteld op ' hard ' (0).

De standaard prioriteits waarden voor de verschillende beperkingen worden opgegeven in de volgende configuratie:

ClusterManifest.xml

```xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PlacementConstraintPriority" Value="0" />
            <Parameter Name="CapacityConstraintPriority" Value="0" />
            <Parameter Name="AffinityConstraintPriority" Value="0" />
            <Parameter Name="FaultDomainConstraintPriority" Value="0" />
            <Parameter Name="UpgradeDomainConstraintPriority" Value="1" />
            <Parameter Name="PreferredLocationConstraintPriority" Value="2" />
        </Section>
```

via ClusterConfig.jsop voor zelfstandige implementaties of Template.jsop voor door Azure gehoste clusters:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "PlacementConstraintPriority",
          "value": "0"
      },
      {
          "name": "CapacityConstraintPriority",
          "value": "0"
      },
      {
          "name": "AffinityConstraintPriority",
          "value": "0"
      },
      {
          "name": "FaultDomainConstraintPriority",
          "value": "0"
      },
      {
          "name": "UpgradeDomainConstraintPriority",
          "value": "1"
      },
      {
          "name": "PreferredLocationConstraintPriority",
          "value": "2"
      }
    ]
  }
]
```

## <a name="fault-domain-and-upgrade-domain-constraints"></a>Domein beperkingen voor fout domeinen en upgrades
De cluster resource manager wil de services gelijkmatig over de fout-en upgrade domeinen laten vallen. Dit modeleert deze als een beperking binnen de engine van de cluster resource manager. Raadpleeg het artikel over [cluster configuratie](service-fabric-cluster-resource-manager-cluster-description.md#fault-and-upgrade-domain-constraints-and-resulting-behavior)voor meer informatie over hoe ze worden gebruikt en hun specifieke gedrag.

Het kan zijn dat cluster resource manager een aantal replica's moet inpakken in een upgrade domein om te kunnen omgaan met upgrades, fouten of andere beperkings schendingen. Het inpakken van een fout-of upgrade domein gebeurt normaal gesp roken alleen wanneer er sprake is van een aantal storingen of andere verloop bewerkingen in het systeem waardoor de juiste plaatsing wordt voor komen Als u wilt voor komen dat u zelfs tijdens deze situaties inpakt, kunt u het `RequireDomainDistribution` [plaatsings beleid](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing)gebruiken. Dit kan van invloed zijn op de beschik baarheid van de service en de betrouw baarheid als een neven effect. Overweeg daarom zorgvuldig.

Als de omgeving correct is geconfigureerd, worden alle beperkingen volledig geëerbiedigd, zelfs tijdens upgrades. Het belangrijkste is dat de cluster resource manager voor uw beperkingen bekijkt. Wanneer er een schending wordt gedetecteerd, wordt deze onmiddellijk gerapporteerd en wordt geprobeerd het probleem te verhelpen.

## <a name="the-preferred-location-constraint"></a>De voorkeurs locatie beperking
De beperking PreferredLocation is enigszins anders, omdat deze twee verschillende toepassingen heeft. Een gebruik van deze beperking is tijdens de upgrade van de toepassing. Deze beperking wordt door cluster resource manager automatisch beheerd tijdens upgrades. Dit wordt gebruikt om ervoor te zorgen dat de replica's worden teruggestuurd naar de oorspronkelijke locaties wanneer de upgrade is voltooid. Het andere gebruik van de PreferredLocation-beperking is voor het [ `PreferredPrimaryDomain` plaatsings beleid](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md). Beide zijn optimalisaties, en daarom is de beperking PreferredLocation de enige beperking die standaard is ingesteld op ' Optimization '.

## <a name="upgrades"></a>Uitvoeren
Het cluster resource Manager helpt ook tijdens het upgraden van toepassingen en clusters, waarbij het twee taken heeft:

* Zorg ervoor dat de regels van het cluster niet zijn aangetast
* Probeer de upgrade probleemloos te helpen

### <a name="keep-enforcing-the-rules"></a>De regels blijven afdwingen
Het belangrijkste om op de hoogte te zijn van de regels: de strikte beperkingen, zoals plaatsings beperkingen en capaciteit, worden nog steeds afgedwongen tijdens upgrades. Plaatsings beperkingen zorgen ervoor dat uw workloads alleen worden uitgevoerd wanneer ze worden toegestaan, zelfs tijdens upgrades. Wanneer Services zeer beperkt zijn, kunnen upgrades langer duren. Wanneer de service of het knoop punt wordt afgesloten voor een update, zijn er mogelijk enkele opties voor waar deze kunnen worden geplaatst.

### <a name="smart-replacements"></a>Slimme vervangingen
Wanneer een upgrade wordt gestart, neemt de Resource Manager een moment opname van de huidige rang schikking van het cluster. Wanneer elk upgrade domein is voltooid, probeert het de services te retour neren die zich in dat upgrade domein bevonden naar hun oorspronkelijke rang schikking. Op deze manier zijn er tijdens de upgrade Maxi maal twee overgangen voor een service. Er is een verplaatsing van het betrokken knoop punt en één zet terug in. Het retour neren van het cluster of de service naar de manier waarop de upgrade werd uitgevoerd, zorgt er ook voor dat de upgrade niet van invloed is op de indeling van het cluster. 

### <a name="reduced-churn"></a>Gereduceerd verloop
Een ander wat er gebeurt tijdens upgrades is dat cluster resource manager de taak verdeling uitschakelt. Voor komen van Balancing voor komt onnodige reacties op de upgrade zelf, zoals het verplaatsen van services in knoop punten die zijn leeg gemaakt voor de upgrade. Als de betreffende upgrade een cluster upgrade is, wordt het hele cluster niet in balans gebracht tijdens de upgrade. Beperkings controles blijven actief, alleen verplaatsing op basis van de proactieve verdeling van metrische gegevens is uitgeschakeld.

### <a name="buffered-capacity--upgrade"></a>Gebufferde capaciteit & upgrade
Over het algemeen wilt u de upgrade volt ooien, zelfs als het cluster beperkt is of bijna vol is. Het beheren van de capaciteit van het cluster is nog belang rijker tijdens de upgrade dan normaal. Afhankelijk van het aantal upgrade domeinen moet er tussen 5 en 20 procent van de capaciteit worden gemigreerd, omdat de upgrade via het cluster wordt uitgevoerd. Dat werk moet ergens naartoe gaan. Dit is de plaats waar het begrip van de [buffer capaciteit](service-fabric-cluster-resource-manager-cluster-description.md#node-buffer-and-overbooking-capacity) nuttig is. De gebufferde capaciteit wordt geëerbiedigd tijdens de normale werking. Cluster resource manager kan knoop punten opvullen tot de totale capaciteit (waarbij de buffer wordt verbruikt), indien nodig.

## <a name="next-steps"></a>Volgende stappen
* Begin vanaf het begin en [krijg een inleiding tot de service Fabric cluster resource manager](service-fabric-cluster-resource-manager-introduction.md)

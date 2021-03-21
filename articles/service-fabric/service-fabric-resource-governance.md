---
title: Resourcebeheer voor containers en services
description: Met Azure Service Fabric kunt u resource aanvragen en limieten opgeven voor services die als processen of containers worden uitgevoerd.
ms.topic: conceptual
ms.date: 8/9/2017
ms.openlocfilehash: d760766870c8c2be0a2d2384f6d012b75bc92fbd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101735655"
---
# <a name="resource-governance"></a>Resourcebeheer

Wanneer u meerdere services op hetzelfde knoop punt of cluster uitvoert, is het mogelijk dat een service meer bronnen kan gebruiken, Starving andere services in het proces. Dit probleem wordt aangeduid als het probleem ' ruis in de buur '. Met Azure Service Fabric kan de ontwikkelaar dit gedrag beheren door aanvragen en limieten per service op te geven om het resource gebruik te beperken.

> Voordat u verder gaat met dit artikel, raden we u aan om vertrouwd te raken met het [service Fabric-toepassings model][application-model-link] en het [model van service Fabric hosting][hosting-model-link].
>

## <a name="resource-governance-metrics"></a>Metrische gegevens van resource governance

Resource governance wordt ondersteund in Service Fabric conform het [service pakket][application-model-link]. De resources die zijn toegewezen aan het service pakket kunnen verder worden verdeeld tussen code pakketten. Service Fabric ondersteunt CPU en Memory governance per service pakket, met twee ingebouwde [metrische gegevens](service-fabric-cluster-resource-manager-metrics.md):

* *CPU* (metrische naam `servicefabric:/_CpuCores` ): een logische kern die beschikbaar is op de hostmachine. Alle kernen op alle knoop punten worden hetzelfde gewogen.

* *Geheugen* (metrische naam `servicefabric:/_MemoryInMB` ): het geheugen wordt uitgedrukt in mega bytes en wordt toegewezen aan het fysieke geheugen dat beschikbaar is op de computer.

Voor deze twee metrische gegevens houdt [cluster resource manager (CRM)][cluster-resource-manager-description-link] de totale cluster capaciteit bij, de belasting van elk knoop punt in het cluster en de resterende bronnen in het cluster. Deze twee meet waarden zijn gelijk aan die van andere gebruikers of aangepaste metrische gegevens. Alle bestaande functies kunnen met ze worden gebruikt:

* Het cluster kan worden [gesaldeerd](service-fabric-cluster-resource-manager-balancing.md) op basis van deze twee metrische gegevens (standaard gedrag).
* Het cluster kan worden [gedefragmenteerd](service-fabric-cluster-resource-manager-defragmentation-metrics.md) op basis van deze twee metrische gegevens.
* Bij [het beschrijven van een cluster][cluster-resource-manager-description-link]kan de gebufferde capaciteit worden ingesteld voor deze twee metrische gegevens.

> [!NOTE]
> [Dynamische belasting rapportage](service-fabric-cluster-resource-manager-metrics.md) wordt niet ondersteund voor deze metrische gegevens; belastingen voor deze metrische gegevens worden gedefinieerd tijdens de aanmaak fase.

## <a name="resource-governance-mechanism"></a>Resource governance-mechanisme

Vanaf versie 7,2 ondersteunt Service Fabric-runtime de specificatie van aanvragen en limieten voor CPU-en geheugen bronnen.

> [!NOTE]
> Service Fabric runtime-versies ouder dan 7,2 bieden alleen ondersteuning voor een model waarbij één waarde wordt gebruikt als de **aanvraag** en de **limiet** voor een bepaalde resource (CPU of geheugen). Dit wordt beschreven als de **RequestsOnly** -specificatie in dit document.

* *Aanvragen:* Waarden voor de CPU-en geheugen aanvraag vertegenwoordigen de belasting die wordt gebruikt door [cluster resource manager (CRM)][cluster-resource-manager-description-link] voor de `servicefabric:/_CpuCores` en `servicefabric:/_MemoryInMB` metrische gegevens. Met andere woorden, CRM beschouwt het Resource verbruik van een service gelijk aan de aanvraag waarden en gebruikt deze waarden bij het maken van beslissingen over de plaatsing.

* *Limieten:* CPU-en geheugen limiet waarden vertegenwoordigen de werkelijke resource limieten die worden toegepast wanneer een proces of container wordt geactiveerd op een knoop punt.

Service Fabric **zijn RequestsOnly, LimitsOnly** en beide **RequestsAndLimits** -specificaties toegestaan voor de CPU en het geheugen.
* Wanneer RequestsOnly-specificatie wordt gebruikt, gebruikt service Fabric ook de aanvraag waarden als limieten.
* Wanneer LimitsOnly-specificatie wordt gebruikt, beschouwt service Fabric de aanvraag waarden als 0.
* Wanneer RequestsAndLimits-specificatie wordt gebruikt, moeten de limiet waarden groter zijn dan of gelijk zijn aan de aanvraag waarden.

Voor een beter inzicht in het mechanisme voor het beheer van resources, bekijken we een voor beeld van een plaatsings scenario met een **RequestsOnly** -specificatie voor de CPU-resource (mechanisme voor Memory governance is gelijkwaardig). Overweeg een knoop punt met twee CPU-kernen en twee service pakketten die erop worden geplaatst. Het eerste service pakket dat moet worden geplaatst, bestaat uit slechts één container code pakket en geeft alleen een aanvraag van één CPU-kern op. Het tweede service pakket dat moet worden geplaatst, bestaat uit slechts één proces op basis van een code pakket en geeft ook alleen een aanvraag van één CPU-kern op. Omdat beide service pakketten een RequestsOnly-specificatie hebben, zijn de limiet waarden ingesteld op hun aanvraag waarden.

1. Eerst wordt het op de container gebaseerde service pakket dat een CPU-kern aanvragen, op het knoop punt geplaatst. Tijdens de runtime wordt de container geactiveerd en wordt de CPU-limiet ingesteld op één kern. De container kan niet meer dan één kern gebruiken.

2. Vervolgens wordt het op het proces gebaseerde service pakket dat één CPU-kern aanvraagt op het knoop punt geplaatst. De runtime activeert het service proces en stelt de CPU-limiet in op één kern.

Op dit moment is de som van aanvragen gelijk aan de capaciteit van het knoop punt. CRM plaatst geen containers of service processen meer met CPU-aanvragen op dit knoop punt. In het knoop punt worden een proces en een container met één kern uitgevoerd en worden ze niet concurreren met elkaar voor CPU.

We gaan nu ons voor beeld bekijken met een **RequestsAndLimits** -specificatie. Deze keer dat het service pakket op basis van de container een aanvraag van één CPU-kern en een limiet van twee CPU-kernen opgeeft. Het service pakket op basis van processen specificeert zowel een aanvraag als een limiet van één CPU-kern.
  1. Eerst wordt het op de container gebaseerde service pakket op het knoop punt geplaatst. Tijdens de runtime wordt de container geactiveerd en wordt de CPU-limiet ingesteld op twee kernen. De container kan niet meer dan twee kernen gebruiken.
  2. Vervolgens wordt het op het proces gebaseerde service pakket op het knoop punt geplaatst. De runtime activeert het service proces en stelt de CPU-limiet in op één kern.

  Op dit punt is de som van de CPU-aanvragen van service pakketten die op het knoop punt worden geplaatst, gelijk aan de CPU-capaciteit van het knoop punt. CRM plaatst geen containers of service processen meer met CPU-aanvragen op dit knoop punt. Op het knoop punt overschrijdt de som van limieten (twee kernen voor de container + een kern voor het proces) de capaciteit van twee kernen. Als de container en het proces burst gelijktijdig zijn, is er mogelijk sprake van conflicten voor de CPU-resource. Dergelijke conflicten worden beheerd door het onderliggende besturings systeem voor het platform. In dit voor beeld kan de container Maxi maal twee CPU-kernen beheersen, wat resulteert in de aanvraag van de proces van één CPU-kern niet gegarandeerd.

> [!NOTE]
> Zoals beschreven in het vorige voor beeld, kunnen de aanvraag waarden voor CPU en geheugen **niet leiden tot de reserve ring van resources op een knoop punt**. Deze waarden vertegenwoordigen het Resource verbruik dat door de cluster resource manager wordt beschouwd bij het maken van beslissingen over plaatsing. Grens waarden vertegenwoordigen de werkelijke resource limieten die worden toegepast wanneer een proces of container wordt geactiveerd op een knoop punt.


Er zijn een paar situaties waarin er sprake is van conflicten voor CPU. In deze situaties kan het proces en de container van het voor beeld een probleem met de ruis in de buur ondervinden:

* *Onder andere vallende en niet-geregeld Services en containers mengen*: als een gebruiker een service maakt zonder dat hiervoor resource beheer is opgegeven, ziet de runtime dat er geen resources worden gebruikt en kan deze in het knoop punt in het voor beeld worden geplaatst. In dit geval verbruikt dit nieuwe proces effectief een CPU voor de kosten van de services die al op het knoop punt worden uitgevoerd. Er zijn twee oplossingen voor dit probleem. Combi neer geen onderhevige en niet-onderhevige Services op hetzelfde cluster, of gebruik [plaatsings beperkingen](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) zodat deze twee typen services niet eindigen op dezelfde set knoop punten.

* *Wanneer een ander proces wordt gestart op het knoop punt, buiten service Fabric (bijvoorbeeld een OS-service)*: in dit geval is het proces buiten service Fabric ook voor CPU met bestaande services. De oplossing voor dit probleem is om de capaciteit van knoop punten correct in te stellen op account voor besturingssysteem overhead, zoals wordt weer gegeven in de volgende sectie.

* *Wanneer aanvragen niet gelijk zijn aan de limieten*: zoals beschreven in het RequestsAndLimits-voor beeld, hebben aanvragen niet tot voor behoud van bronnen op een knoop punt. Wanneer een service met een limiet van meer dan aanvragen wordt geplaatst op een knoop punt, kunnen er resources (indien beschikbaar) worden gebruikt. In dergelijke gevallen kunnen andere services op het knoop punt mogelijk geen resources verbruiken tot hun aanvraag waarden.

## <a name="cluster-setup-for-enabling-resource-governance"></a>Cluster installatie voor het inschakelen van resource governance

Wanneer een knoop punt wordt gestart en aan het cluster wordt toegevoegd, detecteert Service Fabric de beschik bare hoeveelheid geheugen en het beschik bare aantal kernen, en worden vervolgens de capaciteit van het knoop punt voor die twee resources ingesteld.

Service Fabric gebruikt slechts 80% van de beschik bare resources op het knoop punt om buffer ruimte voor het besturings systeem te laten en voor andere processen die op het knoop punt kunnen worden uitgevoerd. Dit percentage kan worden geconfigureerd en kan worden gewijzigd in het cluster manifest.

Hier volgt een voor beeld van het instrueren van Service Fabric om 50% van de beschik bare CPU en 70% van het beschik bare geheugen te gebruiken:

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

Voor de meeste klanten en scenario's is automatische detectie van knooppunt capaciteit voor CPU en geheugen de aanbevolen configuratie (automatische detectie is standaard ingeschakeld). Als u echter volledige hand matige configuratie van knooppunt capaciteit nodig hebt, kunt u ze per knooppunt type configureren met behulp van het mechanisme voor het beschrijven van knoop punten in het cluster. Hier volgt een voor beeld van het instellen van het knooppunt type met vier kernen en 2 GB aan geheugen:

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="servicefabric:/_CpuCores" Value="4"/>
        <Capacity Name="servicefabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```

Als automatische detectie van beschik bare bronnen is ingeschakeld en knooppunt capaciteit hand matig is gedefinieerd in het cluster manifest, Service Fabric controleert of het knoop punt voldoende bronnen heeft ter ondersteuning van de capaciteit die de gebruiker heeft gedefinieerd:

* Als knooppunt capaciteiten die in het manifest zijn gedefinieerd, kleiner zijn dan of gelijk zijn aan de beschik bare resources op het knoop punt, gebruikt Service Fabric de capaciteit die is opgegeven in het manifest.

* Als de knooppunt capaciteit die in het manifest is gedefinieerd, groter is dan de beschik bare resources, Service Fabric gebruikt de beschik bare resources als knooppunt capaciteit.

Het automatisch detecteren van beschik bare bronnen kan worden uitgeschakeld als dit niet vereist is. Als u deze functie wilt uitschakelen, wijzigt u de volgende instelling:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="AutoDetectAvailableResources" Value="false" />
</Section>
```

Voor optimale prestaties moet u ook de volgende instelling inschakelen in het cluster manifest:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" />
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```

> [!IMPORTANT]
> Vanaf Service Fabric versie 7,0 hebben we de regel voor het berekenen van de capaciteit van knooppunt resources bijgewerkt in de gevallen waarin de gebruiker de waarden voor knooppunt resource capaciteiten hand matig verstrekt. Laten we eens kijken naar het volgende scenario:
>
> * Er zijn in totaal 10 CPU-kernen op het knoop punt
> * SF is geconfigureerd voor het gebruik van 80% van de totale bronnen voor de gebruikers services (standaard instelling), waardoor een buffer van 20% wordt bereikt voor de andere services die worden uitgevoerd op het knoop punt (incl. Service Fabric systeem Services)
> * De gebruiker besluit de resource capaciteit van het knoop punt voor de metrische gegevens van de CPU-kernen hand matig te overschrijven en stelt deze in op 5 kernen
>
> We hebben de regel voor het berekenen van de beschik bare capaciteit voor Service Fabric gebruikers services op de volgende manier gewijzigd:
>
> * Vóór Service Fabric 7,0 wordt de beschik bare capaciteit voor gebruikers services berekend op **5 kernen** (capaciteits buffer van 20% wordt genegeerd)
> * Vanaf Service Fabric 7,0 wordt de beschik bare capaciteit voor gebruikers services berekend op **vier kernen** (capaciteits buffer van 20% wordt niet genegeerd)

## <a name="specify-resource-governance"></a>Resource governance opgeven

Aanvragen en limieten voor resource beheer worden opgegeven in het manifest van de toepassing (sectie ServiceManifestImport). Hier volgen enkele voorbeelden:

**Voor beeld 1: RequestsOnly-specificatie**
```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMB="1024" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMB="1024" />
    </Policies>
  </ServiceManifestImport>
```

In dit voor beeld `CpuCores` wordt het kenmerk gebruikt om een aanvraag van 1 CPU core voor **ServicePackageA** op te geven. Aangezien de CPU-limiet ( `CpuCoresLimit` kenmerk) niet is opgegeven, gebruikt service Fabric ook de opgegeven aanvraag waarde van 1 kern als CPU-limiet voor het service pakket.

**ServicePackageA** wordt alleen geplaatst op een knoop punt waar de resterende CPU-capaciteit na het aftrekken **van de som van de CPU-aanvragen van alle service pakketten die op het knoop punt worden geplaatst** , groter is dan of gelijk is aan 1 kern. Het service pakket op het knoop punt is beperkt tot één kern. Het service pakket bevat twee code pakketten (**CodeA1** en **CodeA2**) en beide het kenmerk opgeven `CpuShares` . Het aandeel van kunnen 512:256 wordt gebruikt om de CPU-limieten voor de afzonderlijke code pakketten te berekenen. Daarom is CodeA1 beperkt tot twee derde van een kern, en CodeA2 is beperkt tot een derde van een kern. Als kunnen niet zijn opgegeven voor alle code pakketten, wordt Service Fabric de CPU-limiet gelijkmatig verdeeld.

Hoewel kunnen die zijn opgegeven voor code pakketten het relatieve aandeel van de totale CPU-limiet van het service pakket vertegenwoordigen, worden de geheugen waarden voor code pakketten opgegeven in absolute voor waarden. In dit voor beeld `MemoryInMB` wordt het kenmerk gebruikt voor het opgeven van geheugen aanvragen van 1024 MB voor zowel CodeA1 als CodeA2. Aangezien de geheugen limiet ( `MemoryInMBLimit` kenmerk) niet is opgegeven, gebruikt service Fabric ook de opgegeven aanvraag waarden als de limieten voor de code pakketten. De geheugen aanvraag (en limiet) voor het service pakket wordt berekend als de som van de geheugen aanvraag-en limieten waarden van de code pakketten van de component. Daarom worden **de** geheugen aanvraag en de limiet berekend als 2048 MB.

**ServicePackageA** wordt alleen geplaatst op een knoop punt waarvan de resterende geheugen capaciteit na het aftrekken **van de som van geheugen aanvragen van alle service pakketten die op dat knoop punt zijn geplaatst** , groter is dan of gelijk is aan 2048 MB. Op het knoop punt worden beide code pakketten beperkt tot 1024 MB aan geheugen. Code pakketten (containers of processen) kunnen niet meer geheugen toewijzen dan deze limiet. Als u dit toch probeert, worden er uitzonde ringen buiten het geheugen.

**Voor beeld 2: LimitsOnly-specificatie**
```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCoresLimit="1"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMBLimit="1024" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMBLimit="1024" />
    </Policies>
  </ServiceManifestImport>
```
In dit voor beeld worden `CpuCoresLimit` `MemoryInMBLimit` -en-kenmerken gebruikt die alleen beschikbaar zijn in SF-versies 7,2 en hoger. Het kenmerk CpuCoresLimit wordt gebruikt om een CPU-limiet van 1 kern voor **ServicePackageA** op te geven. Omdat het CPU-verzoek ( `CpuCores` kenmerk) niet is opgegeven, wordt het beschouwd als 0. `MemoryInMBLimit` het kenmerk wordt gebruikt om geheugen limieten van 1024 MB op te geven voor CodeA1 en CodeA2 en omdat er geen aanvragen ( `MemoryInMB` kenmerk) zijn opgegeven, wordt ervan uitgegaan dat ze 0 zijn. De geheugen aanvraag en limiet voor **ServicePackageA** worden daarom berekend als respectievelijk 0 en 2048. Omdat de CPU-en geheugen aanvragen voor **ServicePackageA** 0 zijn, is er geen belasting voor CRM om te kunnen nadenken voor de `servicefabric:/_CpuCores` en `servicefabric:/_MemoryInMB` metrische gegevens. Daarom kan vanuit een resource governance-perspectief **ServicePackageA** op elk knoop punt worden geplaatst, **ongeacht de resterende capaciteit**. Net als bij voor beeld 1, op het knoop punt, is CodeA1 beperkt tot twee derde van een kern geheugen van 1024 MB en is CodeA2 beperkt tot een van de kernen en 1024 MB aan geheugen.

**Voor beeld 3: RequestsAndLimits-specificatie**
```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="1" CpuCoresLimit="2"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMB="1024" MemoryInMBLimit="3072" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMB="2048" MemoryInMBLimit="4096" />
    </Policies>
  </ServiceManifestImport>
```
Dit voor beeld is gebaseerd op de eerste twee voor beelden en illustreert het opgeven van beide aanvragen en limieten voor de CPU en het geheugen. **ServicePackageA** heeft CPU-en geheugen aanvragen van respectievelijk 1 kern en 3072 (1024 + 2048) MB. Het kan alleen worden geplaatst op een knoop punt met ten minste 1 kern (en 3072 MB) aan resterende capaciteit na het aftrekken van de som van alle CPU-aanvragen (en geheugen) van alle service pakketten die op het knoop punt worden geplaatst, van de totale CPU (en geheugen) capaciteit van het knoop punt. Op het knoop punt is CodeA1 beperkt tot twee derde van 2 kernen en 3072 MB aan geheugen, terwijl CodeA2 beperkt is tot een derde van 2 kernen en 4096 MB geheugen.

### <a name="using-application-parameters"></a>Toepassings parameters gebruiken

Wanneer u bron beheer instellingen opgeeft, is het mogelijk om [toepassings parameters](service-fabric-manage-multiple-environment-app-configuration.md) te gebruiken voor het beheren van meerdere app-configuraties. In het volgende voor beeld wordt het gebruik van toepassings parameters weer gegeven:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>

  <Parameters>
    <Parameter Name="CpuCores" DefaultValue="4" />
    <Parameter Name="CpuSharesA" DefaultValue="512" />
    <Parameter Name="CpuSharesB" DefaultValue="512" />
    <Parameter Name="MemoryA" DefaultValue="2048" />
    <Parameter Name="MemoryB" DefaultValue="2048" />
  </Parameters>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="[CpuSharesA]" MemoryInMB="[MemoryA]" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="[CpuSharesB]" MemoryInMB="[MemoryB]" />
    </Policies>
  </ServiceManifestImport>
```

In dit voor beeld worden standaard parameter waarden ingesteld voor de productie omgeving, waarbij elk service pakket 4 kernen en 2 GB geheugen krijgt. Het is mogelijk om standaard waarden te wijzigen met toepassings parameter bestanden. In dit voor beeld kan één parameter bestand worden gebruikt om de toepassing lokaal te testen, waarbij minder resources worden opgehaald dan in productie:

```xml
<!-- ApplicationParameters\Local.xml -->

<Application Name="fabric:/TestApplication1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="CpuSharesA" DefaultValue="512" />
    <Parameter Name="CpuSharesB" DefaultValue="512" />
    <Parameter Name="MemoryA" DefaultValue="1024" />
    <Parameter Name="MemoryB" DefaultValue="1024" />
  </Parameters>
</Application>
```

> [!IMPORTANT]
> Het opgeven van het resource governance met toepassings parameters is beschikbaar vanaf Service Fabric versie 6,1.<br>
>
> Wanneer toepassings parameters worden gebruikt om resource governance op te geven, kan Service Fabric niet naar een eerdere versie dan versie 6,1 worden gedowngraded.

## <a name="enforcing-the-resource-limits-for-user-services"></a>De resource limieten voor gebruikers services afdwingen

Bij het Toep assen van resource governance op uw Service Fabric Services zorgt u ervoor dat deze bronnen niet groter kunnen zijn dan het quotum van resources. veel gebruikers moeten nog steeds een aantal van hun Service Fabric Services uitvoeren in de modus zonder toezicht. Als u ongebruikte Service Fabric-Services gebruikt, is het mogelijk om te worden uitgevoerd in situaties waarbij ' overmatige ' ongebruikte services alle beschik bare bronnen op de Service Fabric knooppunten verbruiken, wat kan leiden tot ernstige problemen zoals:

* Resource beroving van andere services die worden uitgevoerd op de knoop punten (inclusief Service Fabric systeem Services)
* Knoop punten die eindigen met een slechte status
* Niet reageren Service Fabric-Api's voor cluster beheer

Om te voor komen dat deze situaties zich voordoen, kunt u met Service Fabric *de resource limieten afdwingen voor alle service Fabric gebruikers services die op het knoop punt worden uitgevoerd* (zowel onder-als onbepaald) om te garanderen dat gebruikers services nooit meer gebruiken dan de opgegeven hoeveelheid resources. Dit wordt bereikt door de waarde voor de EnforceUserServiceMetricCapacities-configuratie in de sectie PlacementAndLoadBalancing van de ClusterManifest in te stellen op True. Deze instelling is standaard uitgeschakeld.

```xml
<SectionName="PlacementAndLoadBalancing">
    <ParameterName="EnforceUserServiceMetricCapacities" Value="false"/>
</Section>
```

Aanvullende opmerkingen:

* Het afdwingen van de resource limiet is alleen van toepassing op de `servicefabric:/_CpuCores` `servicefabric:/_MemoryInMB` metrische gegevens en de resource.
* Het afdwingen van resources werkt alleen als de capaciteit van knoop punten voor de metrische gegevens van de resource beschikbaar is voor Service Fabric, hetzij via een mechanisme voor automatische detectie, hetzij via gebruikers hand matig de knooppunt capaciteit opgeven (zoals wordt uitgelegd in de sectie [cluster installatie voor het inschakelen van resource governance](service-fabric-resource-governance.md#cluster-setup-for-enabling-resource-governance) ). Als de knooppunt capaciteit niet is geconfigureerd, kan de functie voor het afdwingen van resources niet worden gebruikt omdat Service Fabric niet weet hoeveel bronnen er moeten worden gereserveerd voor gebruikers services. Service Fabric geeft een status waarschuwing als ' EnforceUserServiceMetricCapacities ' waar ' is, maar de knooppunt capaciteit niet is geconfigureerd.

## <a name="other-resources-for-containers"></a>Andere resources voor containers

Naast CPU en geheugen is het mogelijk om andere resource limieten op te geven voor containers. Deze limieten zijn opgegeven op het niveau van code-pakket en worden toegepast wanneer de container wordt gestart. In tegens telling tot de CPU en het geheugen, is cluster resource manager niet op de hoogte van deze resources en worden er geen capaciteits controles of taak verdeling voor hen uitgevoerd.

* *MemorySwapInMB*: de hoeveelheid wissel geheugen die een container kan gebruiken.
* *MemoryReservationInMB*: de zachte limiet voor Memory governance die alleen wordt afgedwongen wanneer geheugen conflicten worden gedetecteerd op het knoop punt.
* *CpuPercent*: het percentage CPU dat de container kan gebruiken. Als er CPU-aanvragen of-limieten voor het service pakket zijn opgegeven, wordt deze para meter in feite genegeerd.
* *MaximumIOps*: de maximale IOPS die een container kan gebruiken (lezen en schrijven).
* *MaximumIOBytesps*: de maximale io (bytes per seconde) die een container kan gebruiken (lezen en schrijven).
* *BlockIOWeight*: het blok-io-gewicht voor ten opzichte van andere containers.

Deze resources kunnen worden gecombineerd met de CPU en het geheugen. Hier volgt een voor beeld van hoe u aanvullende resources voor containers kunt opgeven:

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuPercent="5"
            MemorySwapInMB="4084" MemoryReservationInMB="1024" MaximumIOPS="20" />
        </Policies>
    </ServiceManifestImport>
```

## <a name="next-steps"></a>Volgende stappen

* Lees [Inleiding tot de service Fabric cluster resource manager](service-fabric-cluster-resource-manager-introduction.md)voor meer informatie over cluster resource manager.
* Als u meer wilt weten over het toepassings model, service pakketten en code pakketten, en hoe replica's aan hen zijn toegewezen, moet u [een toepassing in service Fabric model][application-model-link]lezen.

<!-- Links -->
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md
[cluster-resource-manager-description-link]: service-fabric-cluster-resource-manager-cluster-description.md

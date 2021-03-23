---
title: Status controle in Service Fabric
description: Een inleiding tot het Azure Service Fabric Health Monitoring-model, waarmee de cluster en de bijbehorende toepassingen en services kunnen worden bewaakt.
author: georgewallace
ms.topic: conceptual
ms.date: 2/28/2018
ms.author: gwallace
ms.openlocfilehash: 6c96651fa48acc2f88658148c7e60be2f3fa09da
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104800156"
---
# <a name="introduction-to-service-fabric-health-monitoring"></a>Inleiding tot de statuscontrole Service Fabric
Azure Service Fabric introduceert een status model dat voorziet in uitgebreide, flexibele en uitbreid bare status-en rapportage doeleinden. Het model staat bijna realtime bewaking toe van de status van het cluster en de services die hierop worden uitgevoerd. U kunt eenvoudig status informatie verkrijgen en mogelijke problemen corrigeren voordat ze trapsgewijs worden gecascaded en aanzienlijke storingen veroorzaken. In het typische model verzenden Services rapporten op basis van hun lokale weer gaven en wordt deze informatie geaggregeerd om een algemene weer gave op cluster niveau te bieden.

Service Fabric onderdelen gebruiken dit uitgebreide status model om hun huidige status te rapporteren. U kunt hetzelfde mechanisme gebruiken om de status van uw toepassingen te rapporteren. Als u investeert in status rapportage met hoge kwaliteit die uw aangepaste voor waarden vastlegt, kunt u veel eenvoudiger problemen vaststellen en oplossen voor uw toepassing die wordt uitgevoerd.

> [!NOTE]
> We hebben het Health Subsystem gestart om te voldoen aan de behoefte aan bewaakte upgrades. Service Fabric biedt bewaakte toepassingen en cluster upgrades die zorgen voor volledige Beschik baarheid, geen downtime en minimale voor geen tussen komst van de gebruiker. Om deze doel stellingen te bereiken, controleert de upgrade de status op basis van het geconfigureerde upgrade beleid. Een upgrade kan alleen door gaan als de drempel waarden voor de status worden bepaald. Anders wordt de upgrade automatisch teruggedraaid of onderbroken om beheerders de kans te geven de problemen op te lossen. Zie [dit artikel](service-fabric-application-upgrade.md)voor meer informatie over toepassings upgrades.
> 
> 

## <a name="health-store"></a>Health Store
De Health Store houdt informatie over de status van entiteiten in het cluster eenvoudig op te halen en te evalueren. Het wordt geïmplementeerd als een Service Fabric permanente stateful service om hoge Beschik baarheid en schaal baarheid te garanderen. De Health Store maakt deel uit van de **Fabric:/-systeem** toepassing en is beschikbaar wanneer het cluster actief is.

## <a name="health-entities-and-hierarchy"></a>Status entiteiten en hiërarchie
De status entiteiten zijn ingedeeld in een logische hiërarchie waarin interacties en afhankelijkheden tussen verschillende entiteiten worden vastgelegd. De Health Store maakt automatisch status entiteiten en hiërarchie op basis van de rapporten die van Service Fabric onderdelen zijn ontvangen.

De status entiteiten spie gelen de Service Fabric entiteiten. (De **entiteit status toepassing** komt bijvoorbeeld overeen met een toepassings exemplaar dat is geïmplementeerd in het cluster, terwijl de **entiteit status knooppunt** overeenkomt met een service Fabric cluster knooppunt.) De status hiërarchie legt de interacties van de systeem entiteiten vast en vormt de basis voor de geavanceerde status evaluatie. Meer informatie over belangrijkste Service Fabric concepten vindt u in [service Fabric technisch overzicht](service-fabric-technical-overview.md). Zie [service Fabric toepassings model](service-fabric-application-model.md)voor meer informatie over de toepassing.

Met de status entiteiten en-hiërarchie kunnen het cluster en de toepassingen effectief worden gerapporteerd, worden er fouten opgespoord en worden bewaakt. Het status model biedt een nauw keurige, *gedetailleerde* weer gave van de status van de vele bewegende onderdelen in het cluster.

![Status entiteiten.][1]
De status entiteiten, geordend in een hiërarchie op basis van relaties tussen bovenliggende en onderliggende items.

[1]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy.png

De status entiteiten zijn:

* **Cluster**. Hiermee wordt de status van een Service Fabric cluster aangeduid. In cluster status rapporten worden de voor waarden beschreven die van invloed zijn op het hele cluster. Deze voor waarden zijn van invloed op meerdere entiteiten in het cluster of het cluster zelf. Op basis van de voor waarde kan de rapporter het probleem niet verlagen tot een of meer niet-bevolkte onderliggende items. Voor beelden zijn het brein van het splitsen van clusters vanwege netwerk partities of communicatie problemen.
* **Knoop punt**. Hiermee wordt de status van een Service Fabric knoop punt aangeduid. Knooppunt status rapporten beschrijven voor waarden die van invloed zijn op de knooppunt functionaliteit. Deze zijn doorgaans van invloed op alle geïmplementeerde entiteiten die erop worden uitgevoerd. Voor beelden zijn het ontbreken van schijf ruimte op een knoop punt (of andere eigenschappen voor de hele computer, zoals geheugen, verbindingen) en wanneer een knoop punt niet beschikbaar is. De knooppunt entiteit wordt geïdentificeerd aan de hand van de naam van het knoop punt (teken reeks).
* **Toepassing**. Vertegenwoordigt de status van een toepassings exemplaar dat in het cluster wordt uitgevoerd. In de status rapporten van de toepassing worden de voor waarden beschreven die van invloed zijn op de algemene status van de toepassing. Ze kunnen niet worden beperkt tot afzonderlijke onderliggende items (Services of geïmplementeerde toepassingen). Voor beelden zijn de end-to-end-interactie tussen de verschillende services in de toepassing. De toepassings entiteit wordt geïdentificeerd aan de hand van de toepassings naam (URI).
* **Service**. Hiermee wordt de status aangegeven van een service die in het cluster wordt uitgevoerd. In service status rapporten worden de voor waarden beschreven die van invloed zijn op de algemene status van de service. De rapporter kan het probleem niet beperken tot een beschadigde partitie of replica. Voor beelden zijn onder andere een service configuratie (zoals poort of externe bestands share) die problemen voor alle partities veroorzaakt. De service-entiteit wordt geïdentificeerd door de service naam (URI).
* **Partitie**. Hiermee wordt de status van een service partitie aangeduid. Bij partitie status rapporten worden de voor waarden beschreven die van invloed zijn op de volledige replicaset. Voor beelden zijn onder meer wanneer het aantal replica's lager is dan het aantal doelen en wanneer een partitie zich in quorum verlies bevindt. De partitie-entiteit wordt geïdentificeerd door de partitie-ID (GUID).
* **Replica**. Hiermee wordt de status van een stateful service replica of een stateless service exemplaar aangeduid. De replica is de kleinste eenheid die door watchdog en systeem onderdelen kan worden gerapporteerd voor een toepassing. Voor stateful services bevatten voor beelden een primaire replica waarmee bewerkingen niet kunnen worden gerepliceerd naar secundaire zones en langzame replicatie. Daarnaast kan een staatloze exemplaar rapporteren wanneer er onvoldoende bronnen zijn of verbindings problemen zijn. De replica-entiteit wordt geïdentificeerd door de partitie-ID (GUID) en de replica-of exemplaar-ID (Long).
* **DeployedApplication**. Vertegenwoordigt de status van een *toepassing die op een knoop punt wordt uitgevoerd*. Geïmplementeerde toepassings status rapporten beschrijven voor waarden die specifiek zijn voor de toepassing op het knoop punt dat niet kan worden beperkt tot service pakketten die op hetzelfde knoop punt zijn geïmplementeerd. Voor beelden zijn fouten wanneer het toepassings pakket niet kan worden gedownload op dat knoop punt en problemen met het instellen van beveiligings-principals voor toepassingen op het knoop punt. De geïmplementeerde toepassing wordt geïdentificeerd aan de hand van de toepassings naam (URI) en de naam van het knoop punt (teken reeks).
* **DeployedServicePackage**. Hiermee wordt de status van een service pakket aangeduid dat wordt uitgevoerd op een knoop punt in het cluster. Hierin worden de voor waarden beschreven die specifiek zijn voor een service pakket die geen invloed hebben op de andere service pakketten op hetzelfde knoop punt voor dezelfde toepassing. Voor beelden zijn een code pakket in het service pakket dat niet kan worden gestart en een configuratie pakket dat niet kan worden gelezen. Het geïmplementeerde service pakket wordt geïdentificeerd door de toepassings naam (URI), de knooppunt naam (de teken reeks), de naam van het service manifest (teken reeks) en de activerings-ID van het service pakket (teken reeks).

De granulatie van het status model maakt het eenvoudig om problemen op te sporen en te verhelpen. Als een service bijvoorbeeld niet reageert, is het haalbaar om te rapporteren dat het toepassings exemplaar een slechte status heeft. Rapportage op dat niveau is echter niet ideaal, omdat het probleem mogelijk niet van invloed is op alle services in de toepassing. Het rapport moet worden toegepast op de beschadigde service of op een specifieke onderliggende partitie, als er meer informatie naar die partitie verwijst. De gegevens worden automatisch door de hiërarchie geoppereerd en er wordt een beschadigde partitie zichtbaar gemaakt op service-en toepassings niveau. Deze aggregatie helpt om de hoofd oorzaak van het probleem sneller te lokaliseren en op te lossen.

De status hiërarchie bestaat uit bovenliggende en onderliggende relaties. Een cluster bestaat uit knoop punten en toepassingen. Toepassingen hebben Services en geïmplementeerde toepassingen. Geïmplementeerde toepassingen hebben geïmplementeerde service pakketten. Services hebben partities en elke partitie heeft een of meer replica's. Er is een speciale relatie tussen knoop punten en geïmplementeerde entiteiten. Een beschadigd knoop punt zoals gerapporteerd door het systeem onderdeel van de instantie, de Failover Manager-service, is van invloed op de geïmplementeerde toepassingen, service pakketten en replica's die erop zijn geïmplementeerd.

De status hiërarchie vertegenwoordigt de meest recente status van het systeem op basis van de meest recente status rapporten. Dit is bijna realtime-informatie.
Interne en externe watchdog kunnen op dezelfde entiteiten rapporteren op basis van toepassingsspecifieke logica of aangepaste bewaakte voor waarden. Gebruikers rapporten worden samen met de systeem rapporten gebruikt.

Plan in te investeren in hoe de status moet worden gerapporteerd en gereageerd tijdens het ontwerpen van een grote Cloud service. Met deze investering vooraf maakt u de service eenvoudiger om fouten op te sporen, te bewaken en te beheren.

## <a name="health-states"></a>Statussen
Service Fabric gebruikt drie statussen om te beschrijven of een entiteit in orde is of niet: OK, waarschuwing en fout. Elk rapport dat naar de Health Store wordt verzonden, moet een van deze statussen opgeven. Het resultaat van de status evaluatie is een van deze statussen.

De mogelijke [statussen](/dotnet/api/system.fabric.health.healthstate) zijn:

* **OK**. De entiteit is in orde. Er zijn geen bekende problemen die zijn gerapporteerd voor de service of de onderliggende items (indien van toepassing).
* **Waarschuwing**. De entiteit heeft enkele problemen, maar kan nog wel goed functioneren. Er zijn bijvoorbeeld vertragingen, maar er zijn nog geen functie problemen. In sommige gevallen kan de waarschuwings voorwaarde zichzelf oplossen zonder externe interventie. In dergelijke gevallen worden de status rapporten gebewustmakingd en wordt er inzicht geboden in wat er gebeurt. In andere gevallen kan de waarschuwings voorwaarde afnemen in een ernstig probleem zonder tussen komst van de gebruiker.
* **Fout**. De entiteit is niet in orde. U moet actie ondernemen om de status van de entiteit te herstellen, omdat deze niet goed werkt.
* **Onbekend**. De entiteit bestaat niet in de Health Store. Dit resultaat kan worden verkregen uit de gedistribueerde query's die resultaten van meerdere onderdelen samen voegen. De query voor het ophalen van een knooppunt lijst gaat bijvoorbeeld naar **FailoverManager**, **ClusterManager** en **HealthManager**; de lijst met toepassings lijsten ophalen gaat naar **ClusterManager** en **HealthManager**. Deze query's kunnen resultaten van meerdere systeem onderdelen samen voegen. Als een ander systeem onderdeel een entiteit retourneert die niet aanwezig is in Health Store, heeft het samengevoegde resultaat een onbekende status. Een entiteit bevindt zich niet in het archief omdat de status rapporten nog niet zijn verwerkt of de entiteit na het verwijderen is opgeruimd.

## <a name="health-policies"></a>Status beleid
De Health Store past status beleid toe om te bepalen of een entiteit in orde is op basis van de rapporten en de onderliggende items.

> [!NOTE]
> Status beleid kan worden opgegeven in het cluster manifest (voor de cluster-en knooppunt status evaluatie) of in het manifest van de toepassing (voor evaluatie van de toepassing en alle onderliggende items). Status evaluatie aanvragen kunnen ook worden door gegeven in een aangepast beleid voor status evaluatie dat alleen wordt gebruikt voor die evaluatie.
> 
> 

Service Fabric worden standaard strikte regels toegepast (alles moet in orde zijn) voor de hiërarchische relatie bovenliggende en onderliggende. Als zelfs een van de onderliggende items een slechte gebeurtenis heeft, wordt het bovenliggende item als slecht beschouwd.

### <a name="cluster-health-policy"></a>Cluster status beleid
Het [cluster status beleid](/dotnet/api/system.fabric.health.clusterhealthpolicy) wordt gebruikt om de status van de cluster status en de status van het knoop punt te evalueren. Het beleid kan worden gedefinieerd in het cluster manifest. Als deze niet aanwezig is, wordt het standaard beleid (nul niet-toegestaan) gebruikt.

Het cluster status beleid bevat:

* [ConsiderWarningAsError](/dotnet/api/system.fabric.health.clusterhealthpolicy.considerwarningaserror). Hiermee geeft u op of waarschuwing status rapporten moeten worden behandeld als fouten tijdens de status evaluatie. Standaard: onwaar.
* [MaxPercentUnhealthyApplications](/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthyapplications). Hiermee geeft u het Maxi maal toegestane percentage aan van toepassingen die een slechte status kunnen hebben voordat het cluster als fout wordt beschouwd.
* [MaxPercentUnhealthyNodes](/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthynodes). Hiermee geeft u het Maxi maal toegestane percentage van knoop punten op waarvan de status niet in orde kan zijn voordat het cluster als fout wordt beschouwd. In grote clusters zijn bepaalde knoop punten altijd beschikbaar voor reparaties, dus dit percentage moet zo worden geconfigureerd dat dit wordt toegestaan.
* [ApplicationTypeHealthPolicyMap](/dotnet/api/system.fabric.health.clusterhealthpolicy.applicationtypehealthpolicymap). De toewijzing van het status beleid van het toepassings type kan worden gebruikt tijdens de cluster status evaluatie om speciale toepassings typen te beschrijven. Standaard worden alle toepassingen in een groep geplaatst en geëvalueerd met MaxPercentUnhealthyApplications. Als sommige toepassings typen anders moeten worden behandeld, kunnen ze uit de globale groep worden gehaald. In plaats daarvan worden ze geëvalueerd op basis van de percentages die zijn gekoppeld aan hun toepassings type naam in de kaart. In een cluster zijn bijvoorbeeld duizenden toepassingen van verschillende typen en enkele exemplaren van de controle toepassingen van een speciaal toepassings type. De besturings toepassingen moeten nooit een fout hebben. U kunt globale MaxPercentUnhealthyApplications opgeven tot 20% om bepaalde fouten te verdragen, maar voor het toepassings type ' ControlApplicationType ' is de MaxPercentUnhealthyApplications ingesteld op 0. Op deze manier, als sommige toepassingen niet in orde zijn, maar onder het globale percentage van de status, wordt het cluster geëvalueerd op waarschuwing. Een waarschuwings status heeft geen invloed op de cluster upgrade of andere bewaking die wordt geactiveerd door de status van de fout. Maar zelfs als er een fout optreedt in een besturings toepassing, wordt het cluster beschadigd, waardoor de cluster upgrade wordt teruggedraaid of onderbroken, afhankelijk van de upgrade configuratie.
  Voor de toepassings typen die in de kaart zijn gedefinieerd, worden alle exemplaren van de toepassing uit de algemene groep toepassingen gehaald. Ze worden geëvalueerd op basis van het totale aantal toepassingen van het toepassings type, met behulp van de specifieke MaxPercentUnhealthyApplications van de kaart. Alle overige toepassingen blijven aanwezig in de globale groep en worden geëvalueerd met MaxPercentUnhealthyApplications.

  Het volgende voor beeld is een fragment uit een cluster manifest. Als u vermeldingen wilt definiëren in de toewijzing van het toepassings type, typt u de parameter naam met ' ApplicationTypeMaxPercentUnhealthyApplications-', gevolgd door de naam van het toepassings type.

  ```xml
  <FabricSettings>
    <Section Name="HealthManager/ClusterHealthPolicy">
      <Parameter Name="ConsiderWarningAsError" Value="False" />
      <Parameter Name="MaxPercentUnhealthyApplications" Value="20" />
      <Parameter Name="MaxPercentUnhealthyNodes" Value="20" />
      <Parameter Name="ApplicationTypeMaxPercentUnhealthyApplications-ControlApplicationType" Value="0" />
    </Section>
  </FabricSettings>
  ```

* [NodeTypeHealthPolicyMap](/dotnet/api/system.fabric.health.clusterhealthpolicy.nodetypehealthpolicymap). Het knooppunt type status beleids toewijzing kan worden gebruikt tijdens de cluster status evaluatie om speciale knooppunt typen te beschrijven. De knooppunt typen worden geëvalueerd op basis van de percentages die zijn gekoppeld aan de knooppunt type naam in de kaart. Het instellen van deze waarde heeft geen invloed op de globale groep knoop punten die worden gebruikt voor `MaxPercentUnhealthyNodes` . Zo beschikt een cluster over honderden knoop punten van verschillende typen en enkele typen knoop punten die belang rijk zijn voor de hand. Er moeten geen knoop punten in dat type actief zijn. U kunt globaal `MaxPercentUnhealthyNodes` tot 20% opgeven om sommige storingen voor alle knoop punten te verdragen, maar voor het knooppunt type `SpecialNodeType` stelt `MaxPercentUnhealthyNodes` u de waarde in op 0. Op deze manier, als sommige van de verschillende knoop punten niet in orde zijn, maar onder het globale percentage van de status, wordt het cluster geëvalueerd als in de status van de waarschuwing. Een waarschuwings status van de waarschuwing heeft geen invloed op de cluster upgrade of andere bewaking die wordt geactiveerd door een fout status. Maar zelfs als één knoop punt van `SpecialNodeType` het type een fout status heeft, zou het cluster niet meer in orde zijn en de cluster upgrade terugdraaien of pauzeren, afhankelijk van de upgrade configuratie. Als u het algemene `MaxPercentUnhealthyNodes` percentage instelt op 0 en het `SpecialNodeType` maximum aantal knoop punten met een slechte status instelt op 100 met één knoop punt van `SpecialNodeType` het type in een fout status, zou het cluster nog steeds een fout status hebben omdat de globale beperking in dit geval strikter is. 

  Het volgende voor beeld is een fragment uit een cluster manifest. Als u vermeldingen wilt definiëren in de toewijzing van het knooppunt type, typt u de parameter naam met ' NodeTypeMaxPercentUnhealthyNodes-', gevolgd door de naam van het knooppunt type.

  ```xml
  <FabricSettings>
    <Section Name="HealthManager/ClusterHealthPolicy">
      <Parameter Name="ConsiderWarningAsError" Value="False" />
      <Parameter Name="MaxPercentUnhealthyApplications" Value="20" />
      <Parameter Name="MaxPercentUnhealthyNodes" Value="20" />
      <Parameter Name="NodeTypeMaxPercentUnhealthyNodes-SpecialNodeType" Value="0" />
    </Section>
  </FabricSettings>
  ```

### <a name="application-health-policy"></a>Beleid voor toepassings status
Het [beleid voor toepassings status](/dotnet/api/system.fabric.health.applicationhealthpolicy) beschrijft hoe de evaluatie van gebeurtenissen en aggregatie van de onderliggende status wordt uitgevoerd voor toepassingen en hun kinderen. Het kan worden gedefinieerd in het toepassings manifest **ApplicationManifest.xml**, in het toepassings pakket. Als er geen beleid is opgegeven, wordt door Service Fabric aangenomen dat de entiteit een slechte status heeft als deze een status rapport of een onderliggend item met de waarschuwings-of fout status bevat.
De Configureer bare beleids regels zijn:

* [ConsiderWarningAsError](/dotnet/api/system.fabric.health.clusterhealthpolicy.considerwarningaserror). Hiermee geeft u op of waarschuwing status rapporten moeten worden behandeld als fouten tijdens de status evaluatie. Standaard: onwaar.
* [MaxPercentUnhealthyDeployedApplications](/dotnet/api/system.fabric.health.applicationhealthpolicy.maxpercentunhealthydeployedapplications). Hiermee geeft u het Maxi maal toegestane percentage van geïmplementeerde toepassingen op dat slecht kan zijn voordat de toepassing wordt beschouwd als fout. Dit percentage wordt berekend door het aantal beschadigde geïmplementeerde toepassingen te delen via het aantal knoop punten waarop de toepassingen momenteel zijn geïmplementeerd in het cluster. De berekening wordt afgerond om één fout te verdragen op een klein aantal knoop punten. Standaard percentage: nul.
* [DefaultServiceTypeHealthPolicy](/dotnet/api/system.fabric.health.applicationhealthpolicy.defaultservicetypehealthpolicy). Hiermee geeft u het standaard status beleid voor het Service type op, waarmee het standaard status beleid wordt vervangen voor alle service typen in de toepassing.
* [ServiceTypeHealthPolicyMap](/dotnet/api/system.fabric.health.applicationhealthpolicy.servicetypehealthpolicymap). Biedt een overzicht van service status beleid per service type. Deze beleids regels vervangen het standaard status beleid voor service typen voor elk opgegeven service type. Als een toepassing bijvoorbeeld een stateless Gateway Service type heeft en een stateful Engine service-type, kunt u de status beleidsregels voor de evaluatie anders configureren. Wanneer u beleid per service type opgeeft, kunt u een nauw keurigere controle krijgen over de status van de service.

### <a name="service-type-health-policy"></a>Status beleid Service type
In het [status beleid Service type](/dotnet/api/system.fabric.health.servicetypehealthpolicy) wordt aangegeven hoe u de services en de onderliggende services ervan evalueert en samenvoegt. Het beleid bevat:

* [MaxPercentUnhealthyPartitionsPerService](/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthypartitionsperservice). Hiermee geeft u het Maxi maal toegestane percentage van beschadigde partities op voordat een service wordt beschouwd als beschadigd. Standaard percentage: nul.
* [MaxPercentUnhealthyReplicasPerPartition](/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyreplicasperpartition). Hiermee geeft u het Maxi maal toegestane percentage van beschadigde replica's op voordat een partitie als slecht wordt beschouwd. Standaard percentage: nul.
* [MaxPercentUnhealthyServices](/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyservices). Hiermee geeft u het Maxi maal toegestane percentage van slechte services op voordat de toepassing wordt beschouwd als beschadigd. Standaard percentage: nul.

Het volgende voor beeld is een fragment uit een toepassings manifest:

```xml
    <Policies>
        <HealthPolicy ConsiderWarningAsError="true" MaxPercentUnhealthyDeployedApplications="20">
            <DefaultServiceTypeHealthPolicy
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="10"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="FrontEndServiceType"
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="20"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="BackEndServiceType"
                   MaxPercentUnhealthyServices="20"
                   MaxPercentUnhealthyPartitionsPerService="0"
                   MaxPercentUnhealthyReplicasPerPartition="0">
            </ServiceTypeHealthPolicy>
        </HealthPolicy>
    </Policies>
```

## <a name="health-evaluation"></a>Status evaluatie
Gebruikers en geautomatiseerde Services kunnen op elk gewenst moment de status voor elke entiteit evalueren. Om de status van een entiteit te evalueren, worden alle status rapporten op de entiteit door de Health Store geaggregeerd en worden alle onderliggende items geëvalueerd (indien van toepassing). Het algoritme voor status aggregatie maakt gebruik van status beleid waarmee wordt aangegeven hoe status rapporten moeten worden geëvalueerd en hoe onderliggende gezondheids statussen moeten worden geaggregeerd (indien van toepassing).

### <a name="health-report-aggregation"></a>Aggregatie van status rapporten
Eén entiteit kan meerdere status rapporten verzenden door verschillende reporters (systeem onderdelen of watchdog) op verschillende eigenschappen. De aggregatie maakt gebruik van het bijbehorende status beleid, met name het ConsiderWarningAsError-lid van de toepassing of het cluster status beleid. ConsiderWarningAsError geeft aan hoe waarschuwingen moeten worden geëvalueerd.

De geaggregeerde status wordt geactiveerd door de *slechtste* status rapporten voor de entiteit. Als er ten minste één fout status rapport is, is de geaggregeerde status fout.

![Aggregatie van status rapporten met fouten rapport.][2]

Een status entiteit met een of meer fouten status rapporten wordt geëvalueerd als fout. Hetzelfde geldt voor een verlopen status rapport, ongeacht de status.

[2]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-error.png

Als er geen fout rapporten en een of meer waarschuwingen zijn, is de geaggregeerde status een waarschuwing of fout, afhankelijk van de ConsiderWarningAsError-beleids vlag.

![Aggregatie van status rapporten met waarschuwings rapport en ConsiderWarningAsError false.][3]

Aggregatie van status rapporten met waarschuwings rapport en ConsiderWarningAsError ingesteld op False (standaard).

[3]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-warning.png

### <a name="child-health-aggregation"></a>Aggregatie van onderliggende status
De geaggregeerde status van een entiteit weerspiegelt de onderliggende gezondheids status (indien van toepassing). Het algoritme voor het samen voegen van onderliggende statussen maakt gebruik van de status beleidsregels die van toepassing zijn op basis van het entiteits type.

![Aggregatie van de status van onderliggende entiteiten.][4]

Onderliggende aggregatie op basis van status beleid.

[4]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy-eval.png

Nadat de Health Store alle onderliggende items heeft geëvalueerd, worden de statussen geaggregeerd op basis van het geconfigureerde maximum percentage van onderliggende items met een onjuiste status. Dit percentage wordt uit het beleid gehaald op basis van het type entiteit en onderliggend element.

* Als alle onderliggende objecten de status OK hebben, is de geaggregeerde status van het onderliggende element OK.
* Als kinderen zowel OK als waarschuwings status hebben, is de geaggregeerde status van het onderliggende element een waarschuwing.
* Als er kinderen zijn met fout statussen die het Maxi maal toegestane percentage van beschadigde onderliggende elementen niet eerbiedigen, is de geaggregeerde bovenliggende status fout.
* Als de onderliggende items met fout status het Maxi maal toegestane percentage van beschadigde onderliggende elementen eerbiedigen, is de geaggregeerde bovenliggende status waarschuwing.

## <a name="health-reporting"></a>Status rapportage
Systeem onderdelen, systeem infrastructuur toepassingen en interne/externe watchdog kunnen rapporteren aan Service Fabric entiteiten. De rapporten maken *lokale* bepalingen van de status van de bewaakte entiteiten, op basis van de voor waarden die ze volgen. Ze hoeven geen globale staats-of aggregatie gegevens te bekijken. Het gewenste gedrag is om eenvoudige rapporten te hebben, en geen complexe organismen die een groot aantal dingen moeten bekijken om te bepalen welke gegevens moeten worden verzonden.

Een rapporter moet de betrokken entiteit identificeren en een status rapport maken om status gegevens naar het Health Store te verzenden. Als u het rapport wilt verzenden, gebruikt u de API [FabricClient. HealthClient. ReportHealth](/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth) , rapporteert u de status-api's die worden weer gegeven in de `Partition` `CodePackageActivationContext` objecten, Power shell-cmdlets of de rest.

### <a name="health-reports"></a>Status rapporten
De [status rapporten](/dotnet/api/system.fabric.health.healthreport) voor elk van de entiteiten in het cluster bevatten de volgende informatie:

* **SourceId**. Een teken reeks die een unieke identificatie vormt van het rapport van de status gebeurtenis.
* **Entiteits-ID**. Hiermee wordt de entiteit geïdentificeerd waarop het rapport wordt toegepast. Het verschil is afhankelijk van het [entiteits type](service-fabric-health-introduction.md#health-entities-and-hierarchy):
  
  * Cluster. Geen.
  * Subknooppuntsleutels. Knooppunt naam (teken reeks).
  * Modules. Toepassings naam (URI). Vertegenwoordigt de naam van het toepassings exemplaar dat in het cluster is geïmplementeerd.
  * Service. Service naam (URI). Vertegenwoordigt de naam van het service-exemplaar dat in het cluster is geïmplementeerd.
  * Partitie. Partitie-ID (GUID). Vertegenwoordigt de unieke partitie-id.
  * Exacte. De stateful service replica-ID of de stateless service exemplaar-ID (INT64).
  * DeployedApplication. De naam van de toepassing (URI) en de naam van het knoop punt (teken reeks).
  * DeployedServicePackage. Toepassings naam (URI), knooppunt naam (teken reeks) en naam service manifest (teken reeks).
* **Eigenschap**. Een *teken reeks* (geen vaste opsomming) waarmee de rapporter de status gebeurtenis kan categoriseren voor een specifieke eigenschap van de entiteit. Bijvoorbeeld, rapporter A kunnen de status van de Node01-eigenschap ' Storage ' rapporteren en de reporter B kan de status van de eigenschap ' connectiviteit ' van de Node01 rapporteren. In de Health Store worden deze rapporten behandeld als afzonderlijke status gebeurtenissen voor de entiteit Node01.
* **Beschrijving**. Een teken reeks waarmee een rapporter gedetailleerde informatie kan geven over de status gebeurtenis. Voor **SourceId**, **Property** en **HealthState** moet het rapport volledig worden beschreven. De beschrijving bevat informatie over iedereen-lees bare gegevens over het rapport. De tekst maakt het gemakkelijker voor beheerders en gebruikers om het status rapport te begrijpen.
* **HealthState**. Een [opsomming](service-fabric-health-introduction.md#health-states) die de status van het rapport beschrijft. De geaccepteerde waarden zijn OK, waarschuwing en fout.
* **TimeToLive**. Een time span die aangeeft hoe lang het status rapport geldig is. In combi natie met **RemoveWhenExpired** kan het Health Store weten hoe verlopen gebeurtenissen moeten worden geëvalueerd. Standaard is de waarde oneindig en is het rapport altijd geldig.
* **RemoveWhenExpired**. Een Booleaanse waarde. Als deze eigenschap is ingesteld op True, wordt het verlopen status rapport automatisch verwijderd uit de Health Store. het rapport heeft geen invloed op de evaluatie van de entiteits status. Wordt gebruikt wanneer het rapport alleen gedurende een opgegeven periode geldig is en de representatieer het niet expliciet hoeft te wissen. Het wordt ook gebruikt om rapporten te verwijderen uit de Health Store (een watchdog wordt bijvoorbeeld gewijzigd en stopt met het verzenden van rapporten met de vorige bron en eigenschap). Het kan een rapport verzenden met een korte TimeToLive samen met RemoveWhenExpired om een eerdere status te wissen uit de Health Store. Als de waarde is ingesteld op ONWAAR, wordt het verlopen rapport beschouwd als een fout bij de status evaluatie. De waarde False geeft aan de Health Store door dat de bron regel matig moet worden gerapporteerd voor deze eigenschap. Als dat niet het geval is, moet er iets mis zijn met de watchdog. De status van de watchdog wordt vastgelegd door de gebeurtenis als een fout te beschouwen.
* **SequenceNumber**. Een positief geheel getal dat steeds groter moet worden, het vertegenwoordigt de volg orde van de rapporten. De Health Store wordt gebruikt voor het detecteren van verouderde rapporten die te laat worden ontvangen vanwege vertragingen in het netwerk of andere problemen. Een rapport wordt afgewezen als het Volg nummer kleiner is dan of gelijk is aan het meest recent toegepaste nummer voor dezelfde entiteit, bron en eigenschap. Als deze niet is opgegeven, wordt het Volg nummer automatisch gegenereerd. U hoeft alleen maar een Volg nummer in te stellen bij het rapporteren van status overgangen. In dit geval moet de bron onthouden welk rapport het verzendt en de gegevens voor herstel op failover bewaren.

Deze vier stukjes informatie:-SourceId, entiteits-id, eigenschap en HealthState zijn vereist voor elk status rapport. De SourceId-teken reeks mag niet beginnen met het voor voegsel '**System.**', dat is gereserveerd voor systeem rapporten. Voor dezelfde entiteit is er slechts één rapport voor dezelfde bron en eigenschap. Meerdere rapporten voor dezelfde bron en eigenschap worden elkaar overschreven, hetzij op de status client (als deze batches zijn) of aan de Health Store zijde. De vervanging is gebaseerd op Volg nummers; nieuwere rapporten (met hogere Volg nummers) vervangen oudere rapporten.

### <a name="health-events"></a>Status gebeurtenissen
Intern blijven de Health Store [status gebeurtenissen](/dotnet/api/system.fabric.health.healthevent), die alle informatie uit de rapporten bevatten, en aanvullende meta gegevens. De meta gegevens bevatten het tijdstip waarop het rapport is gegeven aan de Health-client en de tijd waarop het aan de server zijde is gewijzigd. De status gebeurtenissen worden geretourneerd door [status query's](service-fabric-view-entities-aggregated-health.md#health-queries).

De toegevoegde meta gegevens bevatten:

* **SourceUtcTimestamp**. De tijd waarop het rapport is gegeven aan de Health-client (Coordinated Universal Time).
* **LastModifiedUtcTimestamp**. De tijd waarop het rapport voor het laatst is gewijzigd aan de server zijde (Coordinated Universal Time).
* **IsExpired**. Een markering om aan te geven of het rapport is verlopen tijdens het uitvoeren van de query door de Health Store. Een gebeurtenis kan alleen worden verlopen als RemoveWhenExpired False is. Anders wordt de gebeurtenis niet geretourneerd door de query en uit het archief verwijderd.
* **LastOkTransitionAt**, **LastWarningTransitionAt**, **LastErrorTransitionAt**. De laatste keer dat er overgangen voor OK/waarschuwing/fouten zijn. Deze velden geven de geschiedenis van de status overgangen voor de gebeurtenis.

De status overgangs velden kunnen worden gebruikt voor Smarter-waarschuwingen of historische status informatie over de gebeurtenis. Ze bieden scenario's zoals:

* Waarschuwen wanneer een eigenschap meer dan X minuten een waarschuwing/fout heeft. Als u de voor waarde voor een bepaalde periode controleert, voor komt u waarschuwingen voor tijdelijke voor waarden. Bijvoorbeeld: een waarschuwing als de status voor meer dan vijf minuten een waarschuwing kan worden omgezet in (HealthState = = Warning and now-LastWarningTransitionTime > 5 minuten).
* Waarschuw alleen voor voor waarden die in de afgelopen X minuten zijn gewijzigd. Als een rapport al eerder is dan de opgegeven tijd, kan dit worden genegeerd omdat het al eerder is gesignaleerd.
* Als er een waarschuwing en fout wordt weer gegeven in een eigenschap, moet u bepalen hoe lang het slecht is (dat wil zeggen, niet OK). Bijvoorbeeld: een waarschuwing als de eigenschap meer dan vijf minuten niet in orde is, kan worden vertaald naar (HealthState! = OK en Now-LastOkTransitionTime > 5 minuten).

## <a name="example-report-and-evaluate-application-health"></a>Voor beeld: de status van een toepassing rapporteren en evalueren
In het volgende voor beeld wordt een status rapport verzonden via Power shell op de Application **Fabric:/WordCount** uit de bron- **MyWatchdog**. Het status rapport bevat informatie over de status van de eigenschap Beschik baarheid in een fout status, met oneindige TimeToLive. Vervolgens wordt een query uitgevoerd op de status van de toepassing, die de geaggregeerde status fouten en de gerapporteerde status gebeurtenissen in de lijst met status gebeurtenissen retourneert.

```powershell
PS C:\> Send-ServiceFabricApplicationHealthReport –ApplicationName fabric:/WordCount –SourceId "MyWatchdog" –HealthProperty "Availability" –HealthState Error

PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ExcludeHealthStatistics


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Error event: SourceId='MyWatchdog', Property='Availability'.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error

                                  ServiceName           : fabric:/WordCount/WordCountWebService
                                  AggregatedHealthState : Ok

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_0
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_3
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_1
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 360
                                  SentAt                : 3/22/2016 7:56:53 PM
                                  ReceivedAt            : 3/22/2016 7:56:53 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 3/22/2016 7:56:53 PM, LastWarning = 1/1/0001 12:00:00 AM

                                  SourceId              : MyWatchdog
                                  Property              : Availability
                                  HealthState           : Error
                                  SequenceNumber        : 131032204762818013
                                  SentAt                : 3/23/2016 3:27:56 PM
                                  ReceivedAt            : 3/23/2016 3:27:56 PM
                                  TTL                   : Infinite
                                  Description           :
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Ok->Error = 3/23/2016 3:27:56 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## <a name="health-model-usage"></a>Gebruik van het status model
Met het status model kunnen Cloud Services en de onderliggende Service Fabric platform worden geschaald, omdat de controle-en status bepaling worden gedistribueerd tussen de verschillende monitors in het cluster.
Andere systemen hebben één gecentraliseerde service op het cluster niveau waarmee alle *potentieel* nuttige informatie wordt geparseerd die door Services wordt gegenereerd. Deze aanpak belemmert de schaal baarheid. Ook kunnen ze geen specifieke informatie verzamelen om problemen en mogelijke problemen zo dicht mogelijk bij de hoofd oorzaak te identificeren.

Het status model wordt intensief gebruikt voor het bewaken en diagnosticeren, voor het evalueren van de cluster-en toepassings status en voor bewaakte upgrades. Andere services gebruiken status gegevens voor het automatisch herstellen, het samen stellen van de cluster status en het uitgeven van waarschuwingen voor bepaalde voor waarden.

## <a name="next-steps"></a>Volgende stappen
[Service Fabric status rapporten weer geven](service-fabric-view-entities-aggregated-health.md)

[Systeem status rapporten gebruiken om problemen op te lossen](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Service status rapporteren en controleren](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Aangepaste Service Fabric status rapporten toevoegen](service-fabric-report-health.md)

[Services lokaal controleren en een diagnose uitvoeren](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Upgrade van toepassing Service Fabric](service-fabric-application-upgrade.md)

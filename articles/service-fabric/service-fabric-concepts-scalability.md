---
title: Schaal baarheid van Service Fabric Services
description: Meer informatie over schalen in azure Service Fabric en de verschillende technieken die worden gebruikt om toepassingen te schalen.
author: masnider
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: masnider
ms.custom: devx-track-csharp
ms.openlocfilehash: 87ac89edc1c9996afc03e7c2bd6743202fdfcb52
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98786188"
---
# <a name="scaling-in-service-fabric"></a>Schalen in Service Fabric
Azure Service Fabric maakt het eenvoudig om schaal bare toepassingen te bouwen door de services, partities en replica's op de knoop punten van een cluster te beheren. Door veel werk belastingen op dezelfde hardware uit te voeren, is het mogelijk om Maxi maal het resource gebruik te maken, maar biedt ook flexibiliteit voor het schalen van uw workloads. In deze Channel 9-video wordt beschreven hoe u schaal bare micro Services-toepassingen kunt bouwen:

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T116/player]

Het schalen van Service Fabric wordt op verschillende manieren uitgevoerd:

1. Schalen door stateless service instanties te maken of te verwijderen
2. Schalen door nieuwe benoemde services te maken of te verwijderen
3. Schalen door nieuwe benoemde toepassings exemplaren te maken of te verwijderen
4. Schalen met behulp van gepartitioneerde Services
5. Schalen door knoop punten toe te voegen aan en te verwijderen uit het cluster 
6. Schalen met behulp van de metrische gegevens van cluster resource manager

## <a name="scaling-by-creating-or-removing-stateless-service-instances"></a>Schalen door stateless service instanties te maken of te verwijderen
Een van de eenvoudigste manieren om te schalen binnen Service Fabric werkt met stateless Services. Wanneer u een stateless service maakt, krijgt u de mogelijkheid om een te definiëren `InstanceCount` . `InstanceCount` Hiermee definieert u hoeveel exemplaren van de code van de service worden gemaakt wanneer de service wordt gestart. Stel bijvoorbeeld dat er 100-knoop punten in het cluster zijn. U kunt er ook voor zorgen dat een service wordt gemaakt met een `InstanceCount` van de 10. Tijdens runtime kunnen deze 10 uitgevoerde exemplaren van de code bezet raken (of er is niet genoeg bezet). Een manier om de werk belasting te schalen, is om het aantal exemplaren te wijzigen. Een voor beeld: sommige bewakings-of beheer code kunnen het bestaande aantal exemplaren wijzigen in 50 of op 5, afhankelijk van of de werk belasting moet worden in-of uitgeschaald op basis van de belasting. 

C#

```csharp
StatelessServiceUpdateDescription updateDescription = new StatelessServiceUpdateDescription(); 
updateDescription.InstanceCount = 50;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

Zo

```posh
Update-ServiceFabricService -Stateless -ServiceName $serviceName -InstanceCount 50
```
### <a name="using-dynamic-instance-count"></a>Aantal dynamische instanties gebruiken
Service Fabric biedt met name stateless Services een automatische manier om het aantal instanties te wijzigen. Zo kan de service dynamisch worden geschaald met het aantal beschik bare knoop punten. De manier waarop u dit gedrag kunt doen, is het instellen van het aantal instanties =-1. InstanceCount =-1 is een instructie voor het Service Fabric met de tekst ' deze stateless service op elk knoop punt uitvoeren '. Als het aantal knoop punten verandert, wijzigt Service Fabric automatisch het aantal exemplaren dat overeenkomt, zodat de service wordt uitgevoerd op alle geldige knoop punten. 

C#

```csharp
StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
//Set other service properties necessary for creation....
serviceDescription.InstanceCount = -1;
await fc.ServiceManager.CreateServiceAsync(serviceDescription);
```

Zo

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName -Stateless -PartitionSchemeSingleton -InstanceCount "-1"
```

## <a name="scaling-by-creating-or-removing-new-named-services"></a>Schalen door nieuwe benoemde services te maken of te verwijderen
Een benoemd service-exemplaar is een specifiek exemplaar van een service type (Zie [service Fabric levens cyclus](service-fabric-application-lifecycle.md)van de toepassing) binnen een benoemd toepassings exemplaar in het cluster. 

Nieuwe benoemde service-exemplaren kunnen worden gemaakt (of verwijderd) omdat services meer of minder bezet worden. Hierdoor kunnen aanvragen worden gespreid over meer service-exemplaren, waardoor het mogelijk is dat de belasting van bestaande services afneemt. Wanneer u Services maakt, plaatst de Service Fabric cluster resource manager de services in het cluster op gedistribueerde wijze. De exacte beslissingen gelden voor de [metrische gegevens](service-fabric-cluster-resource-manager-metrics.md) in het cluster en andere plaatsings regels. Services kunnen op verschillende manieren worden gemaakt, maar het meest voorkomende is door beheer acties zoals iemand die u aanroept [`New-ServiceFabricService`](/powershell/module/servicefabric/new-servicefabricservice) of door middel van het aanroepen van code [`CreateServiceAsync`](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) . `CreateServiceAsync` kan zelfs worden aangeroepen binnen andere services die in het cluster worden uitgevoerd.

Het dynamisch maken van Services kan worden gebruikt in allerlei scenario's en is een gemeen schappelijk patroon. Denk bijvoorbeeld aan een stateful service die een bepaalde werk stroom vertegenwoordigt. Oproepen die werk vertegenwoordigen, worden weer gegeven aan deze service. deze service gaat door met het uitvoeren van de stappen voor die werk stroom en bij het registreren van de voortgang. 

Hoe kunt u deze specifieke service schaal maken? De service kan meerdere tenants in een formulier zijn, en kan aanroepen accepteren en stappen voor veel verschillende exemplaren van dezelfde werk stroom allemaal tegelijk. Het is echter mogelijk dat de code complexer wordt, omdat u nu zich zorgen maakt over een groot aantal verschillende exemplaren van dezelfde werk stroom, allemaal in verschillende fasen en van verschillende klanten. Ook wordt het schaal probleem niet opgelost door het verwerken van meerdere werk stromen tegelijk. Dit is omdat deze service op een bepaald moment te veel resources verbruikt voor een bepaalde computer. Veel services die niet voor dit patroon in de eerste plaats zijn gebouwd, ondervinden ook problemen als gevolg van een inherente bottleneck of vertraging in de code. Door deze typen problemen wordt de service niet meer gebruikt wanneer het aantal gelijktijdige werk stromen dat het volgt, groter wordt.  

Een oplossing is het maken van een exemplaar van deze service voor elk ander exemplaar van de werk stroom die u wilt bijhouden. Dit is een geweldig patroon en werkt of de service stateless of stateful is. Voor een goede werking van dit patroon is er meestal een andere service die fungeert als een workload Manager-service. De taak van deze service is het ontvangen van aanvragen en voor het door sturen van deze aanvragen naar andere services. De Manager kan dynamisch een exemplaar van de workload-service maken wanneer het het bericht ontvangt en vervolgens aanvragen aan deze services door geven. De Manager-service kan ook retour aanroepen ontvangen wanneer een bepaalde werk stroom service de taak heeft voltooid. Wanneer de Manager deze retour aanroepen ontvangt, kan dat exemplaar van de werk stroom service worden verwijderd of kan het worden overgelaten als er meer aanroepen worden verwacht. 

Geavanceerde versies van dit type Manager kunnen zelfs Pools maken van de services die worden beheerd. De groep helpt ervoor te zorgen dat een nieuwe aanvraag niet hoeft te wachten tot de service is opgedeeld. De Manager kan in plaats daarvan gewoon een werk stroom service kiezen die momenteel niet in de pool voor komt, of wille keurig omleiden. Als u een groep van services beschikbaar houdt, worden nieuwe aanvragen sneller verwerkt, omdat het niet waarschijnlijk is dat de aanvraag wordt gewacht op een nieuwe service. Het maken van nieuwe services is snel, maar niet gratis of direct. De pool helpt de hoeveelheid tijd te beperken dat de aanvraag moet wachten voordat deze wordt verwerkt. Vaak ziet u deze manager en het groeps patroon wanneer de reactie tijd het meest voor komt. De aanvraag in de wachtrij plaatsen en de service op de achtergrond maken en deze _vervolgens_ door geven aan is ook een populair beheer patroon, net zoals het maken en verwijderen van services op basis van het bijhouden van de hoeveelheid werk die momenteel in behandeling is. 

## <a name="scaling-by-creating-or-removing-new-named-application-instances"></a>Schalen door nieuwe benoemde toepassings exemplaren te maken of te verwijderen
Het maken en verwijderen van hele toepassings exemplaren is vergelijkbaar met het patroon van het maken en verwijderen van services. Voor dit patroon is er een aantal Manager-services die de beslissing nemen op basis van de aanvragen die worden weer gegeven en de informatie die het ontvangt van de andere services in het cluster. 

Wanneer moet er een nieuw benoemd toepassings exemplaar worden gebruikt in plaats van het maken van een nieuwe benoemde service-instance in een bestaande toepassing? Er zijn enkele gevallen:

  * Het nieuwe exemplaar van de toepassing is voor een klant waarvoor code moet worden uitgevoerd met bepaalde identiteits-of beveiligings instellingen.
    * Met Service Fabric kunt u verschillende code pakketten definiëren die onder bepaalde identiteiten worden uitgevoerd. Als u hetzelfde code pakket onder verschillende identiteiten wilt starten, moeten de activeringen zich in verschillende exemplaren van de toepassing voordoen. Bekijk een geval waarin u de werk belastingen van een bestaande klant hebt geïmplementeerd. Deze kunnen worden uitgevoerd onder een bepaalde identiteit, zodat u de toegang tot andere resources, zoals externe data bases of andere systemen, kunt bewaken en beheren. Als er in dit geval een nieuwe klant zich aanmeldt, wilt u waarschijnlijk de code niet activeren in dezelfde context (proces ruimte). U kunt dit in de context van een bepaalde identiteit moeilijker maken voor uw service code. Normaal gesp roken moet u meer beveiliging, isolatie en identiteits beheer code hebben. In plaats van verschillende benoemde service-instanties binnen hetzelfde toepassings exemplaar te gebruiken en dus dezelfde proces ruimte, kunt u verschillende benoemde Service Fabric exemplaren van de toepassing gebruiken. Dit maakt het gemakkelijker om verschillende identiteits contexten te definiëren.
  * Het nieuwe toepassings exemplaar fungeert ook als een configuratie middel
    * Standaard worden alle benoemde service-exemplaren van een bepaald service type binnen een toepassings exemplaar in hetzelfde proces op een bepaald knoop punt uitgevoerd. Dit houdt in dat u elk service-exemplaar op een andere manier kunt configureren, waardoor dit gecompliceerd is. Services moeten een token hebben dat ze gebruiken om hun configuratie op te zoeken in een configuratie pakket. Normaal gesp roken is dit alleen de naam van de service. Dit werkt prima, maar het Couples van de configuratie naar de namen van de afzonderlijke benoemde service-exemplaren binnen het toepassings exemplaar. Dit kan verwarrend en moeilijk te beheren zijn, omdat de configuratie doorgaans een ontwerp tijd artefact met specifieke waarden voor het toepassings exemplaar is. Het maken van meer services betekent dat er meer toepassings upgrades zijn om de informatie in de configuratie pakketten te wijzigen of nieuwe te implementeren, zodat de nieuwe services hun specifieke informatie kunnen opzoeken. Het is vaak gemakkelijker om een geheel nieuw benoemd toepassings exemplaar te maken. Vervolgens kunt u de para meters van de toepassing gebruiken om in te stellen welke configuratie nodig is voor de services. Op deze manier kunnen alle services die zijn gemaakt in die een benoemd toepassings exemplaar hebben, bepaalde configuratie-instellingen overnemen. In plaats van een configuratie bestand te hebben met de instellingen en aanpassingen voor elke klant, zoals geheimen of de resource limieten per klant, hebt u in plaats daarvan voor elke klant een ander exemplaar van de toepassing met deze instellingen overschreven. 
  * De nieuwe toepassing fungeert als een upgrade grens
    * Binnen Service Fabric fungeren verschillende benoemde toepassings exemplaren als grenzen voor de upgrade. Een upgrade van het ene benoemde toepassings exemplaar heeft geen invloed op de code die door een andere benoemde instantie van een toepassing wordt uitgevoerd. De verschillende toepassingen zullen eindigen op het uitvoeren van verschillende versies van dezelfde code op dezelfde knoop punten. Dit kan een factor zijn wanneer u een beslissing over een schaal aanpassing wilt maken, omdat u kunt kiezen of de nieuwe code dezelfde upgrades moet volgen als een andere service. Stel bijvoorbeeld dat een aanroep arriveert bij de Manager-service die verantwoordelijk is voor het schalen van de werk belasting van een bepaalde klant door het dynamisch maken en verwijderen van services. In dit geval is de oproep echter voor een werk belasting die aan een _nieuwe_ klant is gekoppeld. De meeste klanten zijn niet alleen van elkaar geïsoleerd, niet alleen voor de hierboven genoemde beveiligings-en configuratie redenen, maar omdat het een grotere flexibiliteit biedt bij het uitvoeren van specifieke versies van de software en kiezen wanneer deze worden bijgewerkt. U kunt ook een nieuw exemplaar van de toepassing maken en de service daar eenvoudigweg voor maken om de hoeveelheid van uw services te partitioneren die een upgrade zal aanraken. Afzonderlijke toepassings exemplaren bieden een grotere granulatie bij het uitvoeren van upgrades van toepassingen en het inschakelen van een/B-tests en blauw/groen-implementaties. 
  * Het bestaande toepassings exemplaar is vol
    * In Service Fabric is [toepassings capaciteit](service-fabric-cluster-resource-manager-application-groups.md) een concept dat u kunt gebruiken om de hoeveelheid beschik bare resources voor bepaalde toepassings exemplaren te bepalen. U kunt bijvoorbeeld besluiten dat voor een bepaalde service een ander exemplaar moet worden gemaakt om te kunnen schalen. Dit toepassings exemplaar heeft echter niet de capaciteit voor een bepaalde metriek. Als aan deze bepaalde klant of werk belasting nog steeds meer resources moeten worden verleend, kunt u de bestaande capaciteit voor die toepassing verg Roten of een nieuwe toepassing maken. 

## <a name="scaling-at-the-partition-level"></a>Schalen op partitie niveau
Service Fabric ondersteunt partitioneren. Partitioneren van een service in meerdere logische en fysieke secties, die elk onafhankelijk van elkaar functioneren. Dit is nuttig bij stateful Services, omdat er geen enkele set replica's is om alle aanroepen te verwerken en alle statussen tegelijk te bewerken. In het [overzicht voor partitionering](service-fabric-concepts-partitioning.md) vindt u informatie over de typen partitie schema's die worden ondersteund. De replica's van elke partitie worden verdeeld over de knoop punten in een cluster, waardoor de belasting van de service wordt verdeeld en ervoor gezorgd dat noch de service als geheel of op een andere partitie een Single Point of Failure heeft. 

Overweeg een service die gebruikmaakt van een gepartitioneerd partitie schema met een lage sleutel van 0, een hoge sleutel van 99 en een aantal partities van 4. In een cluster met drie knoop punten kan de service worden ingedeeld met vier replica's die de resources op elk knoop punt delen, zoals hier wordt weer gegeven:

<center>

![Partitie-indeling met drie knoop punten](./media/service-fabric-concepts-scalability/layout-three-nodes.png)
</center>

Als u het aantal knoop punten verhoogt, worden er door Service Fabric een aantal van de bestaande replica's verplaatst. Stel bijvoorbeeld dat het aantal knoop punten toeneemt op vier en dat de replica's opnieuw worden gedistribueerd. De service heeft nu nu drie replica's die worden uitgevoerd op elk knoop punt, die elk deel uitmaken van verschillende partities. Hierdoor is het gebruik van resources beter omdat het nieuwe knoop punt niet koud is. Normaal gesp roken worden de prestaties verbeterd naarmate er meer resources beschikbaar zijn voor elke service.

<center>

![Partitie-indeling met vier knoop punten](./media/service-fabric-concepts-scalability/layout-four-nodes.png)
</center>

## <a name="scaling-by-using-the-service-fabric-cluster-resource-manager-and-metrics"></a>Schalen met behulp van de Service Fabric cluster resource manager en metrische gegevens
[Metrische gegevens](service-fabric-cluster-resource-manager-metrics.md) zijn de manier waarop Services het Resource verbruik van service Fabric uitdrukken. Met metrische gegevens krijgt cluster resource manager de mogelijkheid om de indeling van het cluster opnieuw in te delen en te optimaliseren. Er kunnen bijvoorbeeld voldoende resources in het cluster zijn, maar ze kunnen niet worden toegewezen aan de services die momenteel worden uitgevoerd. Met metrische gegevens kan cluster resource manager het cluster opnieuw indelen om ervoor te zorgen dat services toegang hebben tot de beschik bare resources. 


## <a name="scaling-by-adding-and-removing-nodes-from-the-cluster"></a>Schalen door knoop punten toe te voegen aan en te verwijderen uit het cluster 
Een andere optie voor schalen met Service Fabric is het wijzigen van de grootte van het cluster. Het wijzigen van de grootte van het cluster betekent dat knoop punten voor een of meer van de knooppunt typen in het cluster worden toegevoegd of verwijderd. Neem bijvoorbeeld een geval waarbij alle knoop punten in het cluster warm zijn. Dit betekent dat de resources van het cluster bijna allemaal worden verbruikt. In dit geval is het toevoegen van meer knoop punten aan het cluster de beste manier om te schalen. Zodra de nieuwe knoop punten aan het cluster zijn toegevoegd, worden de services door de cluster resource manager van Service Fabric verplaatst, wat resulteert in minder totale belasting op de bestaande knoop punten. Voor stateless Services met het aantal instanties =-1 worden automatisch meer service-exemplaren gemaakt. Hierdoor kunnen sommige aanroepen van de bestaande knoop punten worden verplaatst naar de nieuwe knoop punten. 

Zie [clusters schalen](service-fabric-cluster-scaling.md)voor meer informatie.

## <a name="choosing-a-platform"></a>Een platform kiezen

Als gevolg van de implementatie verschillen tussen besturings systemen, kunt u het gebruik van Service Fabric met Windows of Linux een essentieel onderdeel zijn om uw toepassing te schalen. Een mogelijke dam is de manier waarop de logboek registratie wordt uitgevoerd. Service Fabric in Windows gebruikt een kernelstuurprogramma voor een logboek van één computer dat wordt gedeeld tussen stateful service replica's. Dit logboek weegt af op ongeveer 8 GB. Linux maakt daarentegen gebruik van een staging-logboek van 256 MB voor elke replica, waardoor het minder ideaal is voor toepassingen die het aantal Lightweight service-replica's die worden uitgevoerd op een bepaald knoop punt, willen maximaliseren. Deze verschillen in de tijdelijke opslag vereisten kunnen het gewenste platform op de hoogte brengen van Service Fabric cluster implementatie.

## <a name="putting-it-all-together"></a>Alles samenvoegen
We gaan nu alle ideeën volgen die hier zijn besproken en praten met een voor beeld. Houd rekening met de volgende service: u probeert een service te maken die als een adres boek fungeert, waarbij u aan namen en contact gegevens houdt. 

Aan de rechter kant hebt u meer vragen over schalen: hoeveel gebruikers hebt u nodig? Hoeveel contact personen wordt elke gebruiker opgeslagen? Er wordt geprobeerd om deze alles uit te zetten wanneer u de service voor de eerste keer uitkomt. Stel dat u met één statische service met een specifiek aantal partities gaat gaan. De gevolgen van het verzamelen van het verkeerde aantal partities kunnen ertoe leiden dat u later schaal problemen ondervindt. Ook als u het juiste aantal kiest, hebt u mogelijk niet alle informatie die u nodig hebt. U moet bijvoorbeeld ook de grootte van het cluster vooraf bepalen, zowel in termen van het aantal knoop punten en hun grootte. Het is doorgaans moeilijk te voors pellen hoeveel resources een service gedurende de levens duur gaat gebruiken. Het kan ook lastig zijn om te weten hoe lang het verkeers patroon is dat de service werkelijk ziet. Het is bijvoorbeeld mogelijk dat mensen hun contact personen eerst op de morgen toevoegen en verwijderen, of dat ze gelijkmatig over de loop van de dag worden gedistribueerd. Op basis van dit moet u mogelijk dynamisch uitschalen en op dynamische wijze door gaan. Misschien kunt u leren om te voors pellen wanneer u wilt uitschalen en in, maar hoe waarschijnlijk het is om te reageren op het wijzigen van het resource gebruik door uw service. Dit kan betekenen dat de grootte van het cluster kan worden gewijzigd, zodat er meer resources beschikbaar zijn wanneer het gebruik van bestaande resources niet voldoende is. 

Maar u kunt zelfs proberen om één partitie schema voor alle gebruikers te kiezen? Waarom beperkt u de ene service en één statisch cluster? De werkelijke situatie is doorgaans dynamischer. 

Houd rekening met het volgende dynamische patroon bij het bouwen voor schaal. Mogelijk moet u het aanpassen aan uw situatie:

1. In plaats van een partitie schema voor iedereen op de voor grond te kiezen, moet u een ' Manager-service ' bouwen.
2. De taak van de Manager-service is om klant gegevens te bekijken wanneer ze zich aanmelden voor uw service. Afhankelijk van die informatie maakt de Manager-service een exemplaar van uw _daad werkelijke_ contact-opslag service _voor die klant_. Als er een bepaalde configuratie, isolatie of upgrades vereist zijn, kunt u ook besluiten om een toepassings exemplaar voor deze klant in te stellen. 

Dit dynamische patroon voor het maken van veel voor delen:

  - U probeert niet het juiste aantal partities te raden voor alle gebruikers, maar u kunt ook één service bouwen die oneindig schaalbaar is. 
  - Verschillende gebruikers hoeven niet hetzelfde partitie aantal, aantal replica's, plaatsings beperkingen, metrische gegevens, standaard belastingen, service namen, DNS-instellingen of een van de andere eigenschappen op het niveau van de service of toepassing te hebben. 
  - U krijgt aanvullende gegevens segmenten. Elke klant heeft een eigen kopie van de service
    - Elke klanten service kan anders worden geconfigureerd en er kunnen meer of minder resources worden verleend, met meer of minder partities of replica's, afhankelijk van hun verwachte schaal.
      - Stel bijvoorbeeld dat de klant heeft betaald voor de ' gouden ' laag: ze kunnen meer replica's of meer partitie aantallen verkrijgen, en mogelijk bronnen die zijn toegewezen aan hun services via metrische gegevens en toepassings capaciteiten.
      - Of ondervindt ze informatie over het aantal contacten dat ze nodig hebben: ze zouden slechts een aantal partities ontvangen of zelfs in een gedeelde service groep met andere klanten worden geplaatst.
  - U voert geen service-exemplaren of-replica's uit terwijl u wacht op het weer geven van klanten
  - Als een klant ooit verlaat en vervolgens de gegevens van uw service verwijdert, is het net zo eenvoudig als de manager die service of toepassing die deze heeft gemaakt, verwijdert.

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen voor meer informatie over Service Fabric concepten:

* [Beschik baarheid van Service Fabric Services](service-fabric-availability-services.md)
* [Service Fabric Services partitioneren](service-fabric-concepts-partitioning.md)

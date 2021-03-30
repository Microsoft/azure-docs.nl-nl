---
title: Statusloze Services voor het testen van eenheden in azure Service Fabric
description: Meer informatie over de concepten en procedures voor het testen van eenheden Service Fabric stateful Services.
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 12e8a47d9685dee12594f4e2afaa848d9688d185
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "75433911"
---
# <a name="unit-testing-stateful-services-in-service-fabric"></a>Statusloze Services voor het testen van eenheden in Service Fabric

In dit artikel worden de concepten en procedures beschreven voor het testen van eenheden Service Fabric stateful Services. Bij het testen van de eenheid binnen Service Fabric verdient het een eigen afweging, omdat de toepassings code actief onder meerdere verschillende contexten wordt uitgevoerd. In dit artikel worden de procedures beschreven om te controleren of toepassings code wordt gedekt onder elk van de verschillende contexten die kan worden uitgevoerd.

## <a name="unit-testing-and-mocking"></a>Eenheid testen en detrekken
Eenheids tests in de context van dit artikel zijn geautomatiseerde tests die kunnen worden uitgevoerd in de context van een test loper zoals MSTest of NUnit. De eenheids tests in dit artikel voeren geen bewerkingen uit op een externe bron, zoals een Data Base of een REST API. Deze externe resources moeten worden gesimuleerd. In de context van dit artikel worden de retour waarden voor externe bronnen vervalst, geregistreerd en beheerd.

### <a name="service-fabric-considerations"></a>Service Fabric overwegingen
Het testen van de eenheid van een Service Fabric stateful service heeft verschillende overwegingen. Ten eerste wordt de service code uitgevoerd op meerdere knoop punten, maar onder verschillende rollen. Eenheids tests moeten de code onder elke rol evalueren om de volledige dekking te garanderen. De verschillende rollen zijn primair, actief secundair, niet-actief secundair en onbekend. Voor de rol none is doorgaans geen speciale dekking vereist, omdat Service Fabric deze rol beschouwt als void of null-service. Ten tweede zal elk knoop punt zijn rol op een bepaald moment wijzigen. Voor een volledige dekking moet het pad voor code-uitvoering worden getest met de gewijzigde rollen.

## <a name="why-unit-test-stateful-services"></a>Waarom state-by-unit-test stateful Services? 
Stateful Services voor het testen van eenheden kunnen helpen bij het opsporen van een aantal veelvoorkomende fouten die niet noodzakelijkerwijs worden onderschept door conventionele toepassingen of specifieke eenheids tests op basis van een domein. Als de stateful service bijvoorbeeld een in-memory status heeft, kan dit type tests controleren of deze in-memory status synchroon is over elke replica. Dit type tests kan er ook voor zorgen dat een stateful service reageert op de annulerings tokens die zijn door gegeven door de Service Fabric-indeling. Wanneer annuleringen worden geactiveerd, moet de service langlopende en/of asynchrone bewerkingen stoppen.  

## <a name="common-practices"></a>Algemene procedures

In de volgende sectie wordt geadviseerd over de meest voorkomende procedures voor het testen van de eenheid van een stateful service. Er wordt ook geadviseerd wat een aftrek laag moet zijn om nauw keurig af te stemmen op de Service Fabric Orchestration en status beheer. [ServiceFabric.](https://www.nuget.org/packages/ServiceFabric.Mocks/) 3.3.0 of hoger is een van de bibliotheken die de gesimuleerde functionaliteit biedt die wordt aanbevolen en volgt de procedures die hieronder worden beschreven.

### <a name="arrangement"></a>Orde

#### <a name="use-multiple-service-instances"></a>Meerdere service-exemplaren gebruiken
Bij het testen van de eenheid moeten meerdere exemplaren van een stateful service worden uitgevoerd. Hiermee simuleert u wat er werkelijk gebeurt op het cluster waar Service Fabric meerdere replica's met uw service op verschillende knoop punten. Elk van deze instanties wordt echter uitgevoerd onder een andere context. Bij het uitvoeren van de test moet elke instantie worden geprimet met de functie configuratie verwacht op het cluster. Als de service bijvoorbeeld naar verwachting de doel replica grootte van 3 heeft, kan Service Fabric drie replica's op verschillende knoop punten inrichten. Een van de primaire en tweede twee actieve secundaire.

In de meeste gevallen is het verschil tussen de service-uitvoerings paden voor elk van deze rollen. Als de service bijvoorbeeld geen aanvragen van een actief secundair mag accepteren, kan de service voor deze aanvraag een controle uitvoeren om een informatieve uitzonde ring te genereren die aangeeft dat een aanvraag is uitgevoerd op een secundaire. Als u meerdere exemplaren hebt, is het mogelijk dat deze situatie wordt getest.

Daarnaast kunnen de tests met meerdere instanties de rollen van elk van deze instanties wijzigen om te controleren of de reacties consistent zijn, ondanks dat de rol verandert.

#### <a name="mock-the-state-manager"></a>De status Manager demodeln
De status manager moet worden behandeld als een externe resource en daarom worden gesimuleerd. Wanneer de status Manager wordt getraceerd, moet er een onderliggende opslag ruimte in het geheugen zijn voor het bijhouden van wat wordt opgeslagen in de status Manager, zodat deze kan worden gelezen en geverifieerd. Een eenvoudige manier om dit te doen is door exemplaren van de modellen van elk van de typen betrouw bare verzamelingen te maken. In deze gegevens typen kunt u het type Data gebruiken dat nauw keurig wordt uitgelijnd met de bewerkingen die voor die verzameling worden uitgevoerd. Hieronder vindt u enkele aanbevolen gegevens typen voor elke betrouw bare verzameling

- IReliableDictionary<TKey, TValue>-> System. Collections. gelijktijdige. ConcurrentDictionary<TKey, TValue>
- IReliableQueue \<T> -> System. Collections. generic. Queue\<T>
- IReliableConcurrentQueue \<T> -> System. Collections. gelijktijdige. ConcurrentQueue\<T>

#### <a name="many-state-manager-instances-single-storage"></a>Veel instanties van State Manager, één opslag
Zoals eerder vermeld, moeten de status Manager en de betrouw bare verzamelingen worden behandeld als een externe bron. Deze resources moeten daarom worden gesimuleerd binnen de eenheids testen. Bij het uitvoeren van meerdere exemplaren van een stateful service is het echter een uitdaging om elke gesimuleerde status Manager te synchroniseren in verschillende stateful service exemplaren. Wanneer de stateful service op het cluster wordt uitgevoerd, zorgt de Service Fabric ervoor dat de status Manager van de secundaire replica consistent blijft met de primaire replica. Daarom moeten de tests hetzelfde zijn, zodat ze wijzigingen in de rol kunnen simuleren.

Een eenvoudige manier om deze synchronisatie te bereiken, is het gebruik van een singleton patroon voor het onderliggende object waarin de gegevens worden opgeslagen die naar elke betrouw bare verzameling zijn geschreven. Als een stateful service bijvoorbeeld een gebruikt `IReliableDictionary<string, string>` . De model State manager moet een model van hebben geretourneerd `IReliableDictionary<string, string>` . Deze Modeler kan gebruikmaken `ConcurrentDictionary<string, string>` van een om de geschreven sleutel/waarde-paren bij te houden. De `ConcurrentDictionary<string, string>` moet een singleton zijn die wordt gebruikt door alle instanties van de status managers die zijn door gegeven aan de service.

#### <a name="keep-track-of-cancellation-tokens"></a>Annulerings tokens bijhouden
Annulerings tokens zijn een belang rijk, nog veel overziend aspect van stateful Services. Wanneer Service Fabric een primaire replica voor een stateful service start, wordt er een annulerings token gegeven. Dit annulerings token is bedoeld om te Signa leren op de service wanneer deze wordt verwijderd of verlaagd naar een andere rol. Het stateful service moet alle langdurige of asynchrone bewerkingen stoppen, zodat Service Fabric de werk stroom voor de functie wijziging kan volt ooien.

Bij het uitvoeren van de eenheids tests moeten eventuele annulerings tokens die aan RunAsync, ChangeRoleAsync, openasync en CloseAsync worden gegeven, worden bewaard tijdens de test uitvoering. Als u op deze tokens houdt, kan de test het afsluiten of degraderen van een service simuleren en controleren of de service op de juiste wijze reageert.

#### <a name="test-end-to-end-with-mocked-remote-resources"></a>End-to-end testen met gesimuleerde externe bronnen
Eenheids tests moeten zo veel mogelijk van de toepassings code worden uitgevoerd waarmee de status van de stateful service kan worden gewijzigd. Het is raadzaam om de tests end-to-end te maken. De enige modellen die bestaan, zijn het vastleggen, simuleren en/of controleren van interacties voor externe resources. Dit omvat interacties met de status Manager en de betrouw bare verzamelingen. Het volgende code fragment is een voor beeld van Gherkin voor een test waarbij End-to-end tests worden gedemonstreerd:

```
    Given stateful service named "fabric:/MyApp/MyService" is created
    And a new replica is created as "Primary" with id "111"
    And a new replica is created as "IdleSecondary" with id "222"
    And a new replica is created as "IdleSecondary" with id "333"
    And all idle secondary replicas are promoted to active secondary
    When a request is made to add the an employee "John Smith"
    And the active secondary replica "222" is promoted to primary
    And a request is made to get all employees
    Then the request should should return the "John Smith" employee
```

Deze test beweringt dat de gegevens die op één replica worden vastgelegd, beschikbaar zijn voor een secundaire replica wanneer deze wordt gepromoveerd tot primaire. Ervan uitgaande dat een betrouw bare verzameling de back-upopslag voor de gegevens van de werk nemer is, geldt een mogelijke storing van AA die bij deze test kan worden onderschept als de toepassings code niet is uitgevoerd `CommitAsync` op de trans actie om de nieuwe werk nemer op te slaan. In dat geval retourneert de tweede aanvraag om werk nemers op te halen geen werk nemer die is toegevoegd door de eerste aanvraag.

### <a name="acting"></a>Raad
#### <a name="mimic-service-fabric-replica-orchestration"></a>Service Fabric replica-indeling nabootsen
Bij het beheren van meerdere service-exemplaren, moeten de tests deze services initialiseren en afbreken op dezelfde manier als de Service Fabric indeling. Wanneer een service bijvoorbeeld wordt gemaakt op een nieuwe primaire replica, roept Service Fabric CreateServiceReplicaListener, openasync, ChangeRoleAsync en RunAsync aan. De levens cyclus gebeurtenissen worden beschreven in de volgende artikelen:

- [Stateful service starten](service-fabric-reliable-services-lifecycle.md#stateful-service-startup)
- [Stateful service afsluiten](service-fabric-reliable-services-lifecycle.md#stateful-service-shutdown)
- [Primaire swaps van stateful service](service-fabric-reliable-services-lifecycle.md#stateful-service-primary-swaps)

#### <a name="run-replica-role-changes"></a>Replica-functie wijzigingen uitvoeren
De eenheids tests moeten de rollen van de service-exemplaren op dezelfde manier wijzigen als de Service Fabric-indeling. De functie status computer wordt beschreven in het volgende artikel:

[Status computer van replica functie](service-fabric-concepts-replica-lifecycle.md#replica-role)

Het simuleren van functie wijzigingen is een van de kritieke aspecten van het testen en kan problemen opsporen waarbij de status van de replica niet consistent is. Er kan een inconsistente replica status optreden als gevolg van het opslaan van de status in het geheugen in statisch of op instantie niveau-variabelen. Voor beelden hiervan zijn het annuleren van tokens, opsommingen en configuratie objecten/-waarden. Dit zorgt er ook voor dat de service de door de RunAsync gegeven annulerings tokens in acht neemt zodat de rol kan worden gewijzigd. Het simuleren van gewijzigde rollen kan ook problemen opsporen die zich kunnen voordoen als code niet wordt geschreven om een aanroep van RunAsync meerdere keren mogelijk te maken.

#### <a name="cancel-cancellation-tokens"></a>Annulerings tokens annuleren
Er moeten eenheids tests aanwezig zijn waarbij het annulerings token dat aan RunAsync is gegeven, is geannuleerd. Op deze manier kan de test controleren of de service correct wordt afgesloten. Tijdens deze afsluiten moeten langlopende of asynchrone bewerkingen worden gestopt. Voor beeld van een langlopend proces dat in een service kan bestaan, is een van de services die Luis teren naar berichten in een betrouw bare wachtrij. Dit kan direct bestaan in RunAsync of een achtergrond thread. De implementatie moet logica bevatten voor het afsluiten van de bewerking als dit annulerings token wordt geannuleerd.

Als de stateful-services gebruik maken van een cache of in de geheugen status die alleen op de primaire server aanwezig mag zijn, moet deze op dit moment worden verwijderd. Dit is om ervoor te zorgen dat deze status consistent is als het knoop punt op een later tijdstip een primair wordt. Met het annuleren van de test kunt u controleren of deze status juist is verwijderd.

#### <a name="execute-requests-against-multiple-replicas"></a>Aanvragen voor meerdere replica's uitvoeren
Bij bevestigings tests moet dezelfde aanvraag worden uitgevoerd voor verschillende replica's. Wanneer met de functie wijzigingen zijn gekoppeld, kunnen consistentie problemen worden gedetecteerd. Een voor beeld van een test kan de volgende stappen uitvoeren:
1. Een schrijf aanvraag uitvoeren op basis van de huidige primaire
2. Voer een lees aanvraag uit waarmee de gegevens die in stap 1 zijn geschreven, worden geretourneerd op basis van de huidige primaire
3. Promo veren van secundair naar primair. Dit moet ook het niveau van de huidige primaire naar de secundaire laag verlagen
4. Voer dezelfde Lees aanvraag uit stap 2 uit op de nieuwe secundaire.

In de laatste stap kan de test bevestigen dat de geretourneerde gegevens consistent zijn. Een mogelijk probleem waarbij dit kan worden gedetecteerd is dat de gegevens die door de service worden geretourneerd, in het geheugen kunnen worden opgeslagen, maar uiteindelijk een betrouw bare verzameling hebben. De gegevens in het geheugen worden mogelijk niet goed gesynchroniseerd met wat er in de betrouw bare verzameling bestaat.

In-Memory gegevens worden meestal gebruikt voor het maken van secundaire indexen of aggregaties van gegevens die in een betrouw bare verzameling bestaan.

### <a name="asserting"></a>Laten
#### <a name="ensure-responses-match-across-replicas"></a>Zorg ervoor dat antwoorden in replica's overeenkomen
Eenheids tests moeten bevestigen dat een reactie op een bepaalde aanvraag consistent is tussen meerdere replica's nadat deze is overgezet naar de primaire. Dit kan duiden op mogelijke problemen waarbij gegevens die in het antwoord worden verstrekt, niet worden ondersteund door een betrouw bare verzameling of in het geheugen worden bewaard zonder een mechanisme voor het synchroniseren van die gegevens over replica's. Dit zorgt ervoor dat de service consistente reacties terugstuurt nadat Service Fabric de herverdeling of een failover naar een nieuwe primaire replica.

#### <a name="verify-service-respects-cancellation"></a>Annulering van service punten controleren
Langdurige of asynchrone processen die moeten worden beëindigd wanneer een annulerings token wordt geannuleerd, moeten worden gecontroleerd of ze na het annuleren daad werkelijk zijn beëindigd. Dit zorgt ervoor dat ondanks de replica die de rollen wijzigt, processen die niet zijn bedoeld om te worden uitgevoerd op een niet-primaire replica, stoppen voordat de overgang is voltooid. Dit kan ook problemen opsporen wanneer een dergelijk proces een rol of afsluitings aanvraag blokkeert van Service Fabric wordt voltooid.

#### <a name="verify-which-replicas-should-serve-requests"></a>Controleren welke replica's aanvragen moeten verwerken
De tests moeten het verwachte gedrag bevestigen als een aanvraag wordt doorgestuurd naar een niet-primaire replica. Service Fabric biedt de mogelijkheid om secundaire replica's aanvragen te laten bezorgen. Schrijf bewerkingen naar betrouw bare verzamelingen kunnen echter alleen worden uitgevoerd vanuit de primaire replica. Als uw toepassing alleen is bedoeld voor primaire replica's om aanvragen te behandelen of, kan slechts een subset van aanvragen worden verwerkt door een secundaire, moeten de tests het verwachte gedrag voor zowel de positieve als de negatieve gevallen bevestigen. Het negatieve geval dat een aanvraag wordt verzonden naar een replica die de aanvraag niet moet verwerken en, de positieve waarde die het tegenovergestelde is.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [eenheden testen van stateful Services](service-fabric-how-to-unit-test-stateful-services.md).

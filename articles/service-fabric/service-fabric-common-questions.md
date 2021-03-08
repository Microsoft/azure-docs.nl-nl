---
title: Veelgestelde vragen over Microsoft Azure Service Fabric
description: Veelgestelde vragen over Service Fabric, inclusief mogelijkheden, use cases en algemene scenario's.
ms.topic: troubleshooting
ms.date: 08/18/2017
ms.author: pepogors
ms.openlocfilehash: 4e03ccf432852a5fc9ee700ba0e39dfe2e64fcc9
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102456093"
---
# <a name="commonly-asked-service-fabric-questions"></a>Veelgestelde vragen over Service Fabric

Er zijn veel Veelgestelde vragen over wat Service Fabric kan doen en hoe het moet worden gebruikt. In dit document worden veel van deze veelgestelde vragen en de antwoorden beschreven.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cluster-setup-and-management"></a>Cluster installatie en-beheer

### <a name="how-do-i-roll-back-my-service-fabric-cluster-certificate"></a>Hoe kan ik ik mijn Service Fabric cluster certificaat terug?

Bij het terugdraaien van een upgrade naar uw toepassing is een status detectie voor de Health-fout vereist voordat uw Service Fabric cluster quorum de wijziging heeft doorgevoerd. doorgevoerde wijzigingen kunnen alleen naar voren worden gemigreerd. Escalatie-Engineer door de klant ondersteuning is mogelijk vereist om uw cluster te herstellen als er een niet-bewaakte wijziging van het certificaat is geïntroduceerd.  [De upgrade van de toepassing van service Fabric](./service-fabric-application-upgrade.md) past [para meters](./service-fabric-application-upgrade-parameters.md)voor de upgrade van de toepassing toe en levert een installatie van geen downtime.  Na de aanbevolen modus voor de upgrade van de toepassing, is automatische voortgang via update domeinen gebaseerd op status controles, waarbij automatisch wordt teruggedraaid als het bijwerken van een standaard service mislukt.
 
Als uw cluster nog steeds gebruikmaakt van de klassieke eigenschap vinger afdruk van het certificaat in uw Resource Manager-sjabloon, kunt u het beste het [cluster van de vinger afdruk van een certificaat wijzigen in een algemene naam](./service-fabric-cluster-change-cert-thumbprint-to-cn.md)om gebruik te maken van de beheer functies van moderne geheimen.

### <a name="can-i-create-a-cluster-that-spans-multiple-azure-regions-or-my-own-datacenters"></a>Kan ik een cluster maken dat meerdere Azure-regio's of mijn eigen data centers omvat?

Ja. 

De kern Service Fabric clustering-technologie kan worden gebruikt om computers die overal ter wereld worden uitgevoerd, te combi neren, zolang ze een netwerk verbinding met elkaar hebben. Het bouwen en uitvoeren van een dergelijk cluster kan echter gecompliceerd zijn.

Als u geïnteresseerd bent in dit scenario, raden we u aan contact op te nemen via de [lijst met Service Fabric problemen met github](https://github.com/azure/service-fabric-issues) of door uw ondersteunings medewerker om aanvullende richt lijnen te verkrijgen. Het Service Fabric-team is bezig met het bieden van aanvullende duidelijkheid, richt lijnen en aanbevelingen voor dit scenario. 

Enkele dingen die u moet overwegen: 

1. De Service Fabric cluster resource in Azure is nu regionaal, evenals de schaal sets voor virtuele machines waarop het cluster is gebaseerd. Dit betekent dat u in het geval van een regionale fout de mogelijkheid tot het beheren van het cluster via de Azure Resource Manager of de Azure Portal kan verliezen. Dit kan gebeuren, zelfs als het cluster nog steeds actief is en u rechtstreeks met de app kunt werken. Bovendien biedt Azure vandaag geen mogelijkheid om één virtueel netwerk te hebben dat kan worden gebruikt in verschillende regio's. Dit betekent dat een cluster met meerdere regio's in azure een [openbare IP-adressen vereist voor elke VM in de schaal sets voor virtuele machines](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine) of [Azure VPN-gateways](../vpn-gateway/vpn-gateway-about-vpngateways.md). Deze netwerk opties hebben verschillende gevolgen voor de kosten, prestaties en een zekere mate van toepassings ontwerp, dus zorgvuldige analyse en planning is vereist voordat deze omgeving wordt ingeschakeld.
2. Het onderhoud, het beheer en de bewaking van deze computers kunnen gecompliceerd worden, vooral wanneer het wordt verdeeld over verschillende _soorten_ omgevingen, zoals tussen de diverse cloud providers of tussen on-premises resources en Azure. Het is belang rijk om ervoor te zorgen dat upgrades, bewaking, beheer en diagnostische gegevens worden begrepen voor zowel het cluster als voor de toepassingen voordat productie werkbelastingen in een dergelijke omgeving worden uitgevoerd. Als u al ervaring hebt met het oplossen van deze problemen in azure of in uw eigen data centers, is het waarschijnlijk dat dezelfde oplossingen kunnen worden toegepast wanneer u uw Service Fabric-cluster bouwt of uitvoert. 

### <a name="do-service-fabric-nodes-automatically-receive-os-updates"></a>Ontvangen Service Fabric knoop punten automatisch updates van het besturings systeem?

U kunt de [schaalset voor virtuele machines gebruiken automatische update van de besturingssysteem installatie kopie standaard](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) beschikbaar onderdeel.

Voor clusters die niet worden uitgevoerd in azure, beschikken we over [een toepassing](service-fabric-patch-orchestration-application.md) voor het patchen van de besturings systemen onder uw service Fabric-knoop punten.

### <a name="can-i-use-large-virtual-machine-scale-sets-in-my-sf-cluster"></a>Kan ik grote schaal sets voor virtuele machines in mijn SF-cluster gebruiken? 

**Kort antwoord** : Nee. 

**Lang antwoord** : Hoewel u met de grote schaal sets voor virtuele machines een schaalset van virtuele machines tot 1000 VM-exemplaren kunt schalen, doet u dit door het gebruik van plaatsings groepen (PGs). Fout domeinen (Fd's) en upgrade domeinen (UDs) zijn alleen consistent binnen een plaatsings groep service Fabric gebruikt Fd's en UDs om plaatsings beslissingen van uw service replica's/service-exemplaren te maken. Omdat de Fd's en de UDs alleen in een plaatsings groep vergelijkbaar zijn, kan SF deze niet gebruiken. Als VM1 in PG1 bijvoorbeeld een topologie van FD = 0 en VM9 in PG2 een topologie van FD = 4 heeft, betekent dit niet dat VM1 en VM2 zich op twee verschillende hardware-racks bevinden, dus kan SF de FD-waarden in dit geval niet gebruiken om plaatsings beslissingen te nemen.

Er zijn op dit moment andere problemen met grote schaal sets voor virtuele machines, zoals het ontbreken van ondersteuning voor taak verdeling op niveau 4. Raadpleeg voor [meer informatie over grote schaal sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md)



### <a name="what-is-the-minimum-size-of-a-service-fabric-cluster-why-cant-it-be-smaller"></a>Wat is de minimale grootte van een Service Fabric cluster? Waarom kan het niet kleiner worden?

De mini maal ondersteunde grootte voor een Service Fabric cluster met productie werkbelastingen is vijf knoop punten. Voor dev-scenario's ondersteunen we één knoop punt (geoptimaliseerd voor snelle ontwikkel ervaring in Visual Studio) en vijf knooppunt clusters.

We hebben een productie cluster nodig om ten minste vijf knoop punten te hebben om de volgende drie redenen:
1. Zelfs als er geen gebruikers services worden uitgevoerd, voert een Service Fabric cluster een reeks stateful systeem services uit, met inbegrip van de naamgevings service en de failover Manager-service. Deze systeem services zijn essentieel voor een goede werking van het cluster.
2. We plaatsen altijd één replica van een service per knoop punt, dus de cluster grootte is de bovengrens voor het aantal replica's dat een service (in werkelijkheid een partitie) kan hebben.
3. Omdat een cluster upgrade ten minste één knoop punt uitvalt, willen we een buffer van ten minste één knoop punt hebben. Daarom willen we dat een productie cluster ten minste twee knoop punten bevat *naast* de Maxi maal bewaarde. De minimum waarde is de quorum grootte van een systeem service zoals hieronder wordt uitgelegd.  

We willen dat het cluster beschikbaar is in het vlak van gelijktijdige storingen van twee knoop punten. Voor een Service Fabric cluster beschikbaar is, moeten de systeem services beschikbaar zijn. Stateful systeem services, zoals naamgevings service en failover Manager-service, die bijhouden welke services zijn geïmplementeerd in het cluster en waar ze momenteel worden gehost, zijn afhankelijk van sterke consistentie. Deze sterke consistentie is op zijn beurt afhankelijk van de mogelijkheid om een *quorum* te verkrijgen voor een bepaalde update in de status van die services, waarbij een quorum een strikte meerderheid van de Replica's (N/2 + 1) voor een bepaalde service vertegenwoordigt. Daarom moeten we ClusterSize-QuorumSize >= 2 hebben, waardoor de minimale grootte vijf moet zijn om te voor komen dat er meerdere knoop punten verloren gaan (dus simultaan verlies van twee replica's van een systeem service). Houd er rekening mee dat het cluster N knoop punten heeft en dat er N replica's van een systeem service zijn, één op elk knoop punt. De quorum grootte voor een systeem service is (N/2 + 1). De bovenstaande ongelijkheid ziet eruit als N-(N/2 + 1) >= 2. Er zijn twee situaties waarin u rekening moet houden: wanneer N even is en wanneer N oneven is. Als N even is, zegt N = 2 \* m, waarbij m >= 1, is de ongelijkheid hetzelfde als 2 \* m-(2 \* m/2 + 1) >= 2 of m >= 3. Het minimum voor N is 6 en dat wordt behaald wanneer m = 3. Als N daarentegen oneven is, zegt N = 2 \* m + 1 waarbij m >= 1, is de ongelijkheid hetzelfde als 2 \* m + 1-((2 \* m + 1)/2 + 1) >= 2 of 2 \* m + 1-(m + 1) >= 2 of m >= 2. Het minimum voor N is 5 en dat wordt behaald wanneer m = 2. Daarom is voor alle waarden van N die voldoen aan de ClusterSize-QuorumSize >= 2 het minimum 5.

In het bovenstaande argument hebben we aangenomen dat elk knoop punt een replica van een systeem service heeft, waardoor de quorum grootte wordt berekend op basis van het aantal knoop punten in het cluster. Door *TargetReplicaSetSize* te wijzigen, kunnen we echter de grootte van het quorum kleiner maken dan (N/2 + 1). Dit kan de indruk geven dat een cluster kleiner is dan vijf knoop punten en nog twee extra knoop punten boven de quorum grootte heeft. Als we in een cluster met vier knoop punten bijvoorbeeld TargetReplicaSetSize op 3 hebben ingesteld, is de quorum grootte gebaseerd op TargetReplicaSetSize (3/2 + 1) of 2, dus hebben we ClusterSize-QuorumSize = 4-2 >= 2. We kunnen echter niet garanderen dat de systeem service zich op of boven het quorum bevindt als er een paar knoop punten tegelijk verloren gaan. het kan zijn dat de twee knoop punten die verloren zijn gegaan, twee replica's hosten, zodat de systeem service in quorum verlies werkt (met slechts één replica) en is niet meer beschikbaar.

Met deze achtergrond gaan we een aantal mogelijke cluster configuraties bekijken:

**Eén knoop punt**: deze optie biedt geen hoge Beschik baarheid, omdat het verlies van het ene knoop punt om welke reden dan ook het verlies van het hele cluster betekent.

**Twee knoop punten**: een quorum voor een service die is geïmplementeerd op twee knoop punten (N = 2) is 2 (2/2 + 1 = 2). Wanneer één replica verloren gaat, is het onmogelijk om een quorum te maken. Omdat het uitvoeren van een service-upgrade tijdelijk een replica vergt, is dit geen handige configuratie.

**Drie knoop** punten: met drie knoop punten (N = 3) is de vereiste voor het maken van een quorum nog steeds twee knoop punten (3/2 + 1 = 2). Dit betekent dat u een afzonderlijk knoop punt kwijtraakt en nog steeds quorum kunt behouden, maar gelijktijdige storingen van twee knoop punten zullen de systeem services naar quorum verlies sturen en ertoe leiden dat het cluster niet meer beschikbaar is.

**Vier knoop punten**: met vier knoop punten (N = 4) is de vereiste voor het maken van een quorum drie knoop punten (4/2 + 1 = 3). Dit betekent dat u een afzonderlijk knoop punt kwijtraakt en nog steeds quorum kunt behouden, maar gelijktijdige storingen van twee knoop punten zullen de systeem services naar quorum verlies sturen en ertoe leiden dat het cluster niet meer beschikbaar is.

**Vijf knoop punten**: met vijf knoop punten (N = 5) is de vereiste voor het maken van een quorum nog steeds drie knoop punten (5/2 + 1 = 3). Dit betekent dat u twee knoop punten tegelijk kunt verliezen en nog steeds quorum voor de systeem services behoudt.

Voor werk belastingen van de productie moet u overstappen op gelijktijdige storingen van ten minste twee knoop punten (bijvoorbeeld één vanwege een cluster upgrade, een reden om andere redenen), dus vijf knoop punten zijn vereist.

### <a name="can-i-turn-off-my-cluster-at-nightweekends-to-save-costs"></a>Kan ik mijn cluster 's nachts of in het weekend uitschakelen om kosten te besparen?

In het algemeen niet. Als de virtuele machine wordt verplaatst naar een andere host, worden de gegevens in de lokale, tijdelijke schijven Service Fabric opgeslagen. Bij een normale werking is dat geen probleem omdat het nieuwe knoop punt op andere knoop punten up-to-date wordt gezet. Als u echter alle knoop punten stopt en deze later opnieuw opstart, is er een aanzienlijke kans dat de meeste knoop punten op nieuwe hosts worden gestart en dat het systeem niet kan worden hersteld.

Als u clusters wilt maken voor het testen van uw toepassing voordat deze wordt geïmplementeerd, raden we u aan om die clusters dynamisch te maken als onderdeel van de [pijp lijn voor continue integratie/continue implementatie](service-fabric-tutorial-deploy-app-with-cicd-vsts.md).


### <a name="how-do-i-upgrade-my-operating-system-for-example-from-windows-server-2012-to-windows-server-2016"></a>Hoe kan ik upgrade van mijn besturings systeem (bijvoorbeeld van Windows Server 2012 naar Windows Server 2016)?

Terwijl we aan de slag gaan met een verbeterde ervaring, bent u zelf verantwoordelijk voor de upgrade. U moet de installatie kopie van het besturings systeem op de virtuele machines van de cluster per VM bijwerken. 

### <a name="can-i-encrypt-attached-data-disks-in-a-cluster-node-type-virtual-machine-scale-set"></a>Kan ik gekoppelde gegevens schijven versleutelen in een cluster knooppunt type (virtuele-machine schaalset)?
Ja.  Zie [een cluster met gekoppelde gegevens schijven](../virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks.md#create-a-service-fabric-cluster-with-attached-data-disks) en [Azure Disk Encryption voor Virtual Machine Scale sets](../virtual-machine-scale-sets/disk-encryption-overview.md)maken voor meer informatie.

### <a name="can-i-use-low-priority-vms-in-a-cluster-node-type-virtual-machine-scale-set"></a>Kan ik virtuele machines met lage prioriteit gebruiken in een cluster knooppunt type (virtuele-machine schaalset)?
Nee. Vm's met een lage prioriteit worden niet ondersteund. 

### <a name="what-are-the-directories-and-processes-that-i-need-to-exclude-when-running-an-anti-virus-program-in-my-cluster"></a>Wat zijn de mappen en processen die ik moet uitsluiten bij het uitvoeren van een antivirus programma in mijn cluster?

| **Uitgesloten mappen van anti virus** |
| --- |
| Program Files\Microsoft Service Fabric |
| FabricDataRoot (uit de cluster configuratie) |
| FabricLogRoot (uit de cluster configuratie) |

| **Uitgesloten processen van anti virus** |
| --- |
| Fabric.exe |
| FabricHost.exe |
| FabricInstallerService.exe |
| FabricSetup.exe |
| FabricDeployer.exe |
| ImageBuilder.exe |
| FabricGateway.exe |
| FabricDCA.exe |
| FabricFAS.exe |
| FabricUOS.exe |
| FabricRM.exe |
| FileStoreService.exe |
 
### <a name="how-can-my-application-authenticate-to-key-vault-to-get-secrets"></a>Hoe kan mijn toepassing worden geverifieerd bij Key Vault om geheimen te verkrijgen?
Als uw toepassing referenties voor verificatie Key Vault, kunt u het volgende doen:

A. Tijdens de build/Packing-taak van uw toepassingen kunt u een certificaat in het gegevens pakket van uw SF-app halen en dit gebruiken om te verifiëren bij Key Vault.
B. Voor virtuele-machine schaal sets MSI ingeschakelde hosts kunt u een eenvoudige Power shell-SetupEntryPoint voor uw SF-App ontwikkelen om [een toegangs token van het MSI-eind punt](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)op te halen en vervolgens [uw geheimen uit Key Vault op te halen](/powershell/module/azurerm.keyvault/get-azurekeyvaultsecret).

## <a name="application-design"></a>Toepassings ontwerp

### <a name="whats-the-best-way-to-query-data-across-partitions-of-a-reliable-collection"></a>Wat is de beste manier om gegevens op te vragen over partities van een betrouw bare verzameling?

Betrouw bare verzamelingen zijn doorgaans [gepartitioneerd](service-fabric-concepts-partitioning.md) om uitschalen mogelijk te maken voor betere prestaties en door voer. Dit betekent dat de status van een bepaalde service over tien tallen of honderden machines kan worden verdeeld. U hebt een aantal opties om bewerkingen uit te voeren via die volledige gegevensset:

- Maak een service die een query uitvoert op alle partities van een andere service om de vereiste gegevens op te halen.
- Maak een service die gegevens kan ontvangen van alle partities van een andere service.
- Regel matig push gegevens van elke service naar een externe opslag. Deze methode is alleen geschikt als de query's die u uitvoert, geen deel uitmaken van de bedrijfs logica van uw bedrijf, omdat de gegevens van het externe archief verouderd zijn.
- U kunt ook gegevens opslaan die ondersteuning bieden voor het uitvoeren van query's voor alle records in een gegevens archief in plaats van in een betrouw bare verzameling. Hiermee elimineert u het probleem met verouderde gegevens, maar is het niet mogelijk om de voor delen van betrouw bare verzamelingen te benutten.


### <a name="whats-the-best-way-to-query-data-across-my-actors"></a>Wat is de beste manier om gegevens op te vragen over mijn Actors?

Actors zijn ontworpen als onafhankelijke eenheden van status en compute, zodat het niet wordt aanbevolen om brede query's in de actor-status uit te voeren tijdens runtime. Als u een query op de volledige set actor-status wilt uitvoeren, kunt u het volgende overwegen:

- Vervang uw actor Services door stateful reliable Services, zodat het aantal netwerk aanvragen voor het verzamelen van alle gegevens van het aantal actors tot het aantal partities in uw service.
- Het ontwerpen van uw actors om de status regel matig te pushen naar een externe opslag voor eenvoudiger query's. Net als hierboven is deze methode alleen haalbaar als de query's die u uitvoert niet vereist zijn voor uw runtime-gedrag.

### <a name="how-much-data-can-i-store-in-a-reliable-collection"></a>Hoeveel gegevens kan ik opslaan in een betrouw bare verzameling?

Betrouw bare Services zijn doorgaans gepartitioneerd, dus de hoeveelheid die u kunt opslaan is beperkt tot het aantal machines in het cluster en de hoeveelheid beschikbaar geheugen op deze computers.

Stel dat u een betrouw bare verzameling hebt in een service met 100-partities en drie replica's, waarbij u objecten opslaat die gemiddeld 1 KB groot zijn. Stel nu dat u een 10 computer cluster hebt met 16 GB geheugen per computer. Voor eenvoud en om te worden besteld, wordt ervan uitgegaan dat het besturings systeem en de systeem services, de Service Fabric runtime en uw services 6gb van die worden gebruikt, waardoor er 10 GB beschikbaar wordt gesteld per machine 100 of voor het cluster.

Houd er rekening mee dat elk object drie keer moet worden opgeslagen (één primaire en twee replica's), dat u voldoende geheugen hebt voor ongeveer 35.000.000 objecten in uw verzameling als u de volledige capaciteit hebt. We raden u echter aan om het gelijktijdige verlies van een fout domein en een upgrade domein te maken, die ongeveer 1/3 aan capaciteit opleveren. Dit betekent dat het aantal ongeveer 23.000.000 wordt verminderd.

Deze berekening gaat ook uit van het volgende:

- Dat de distributie van gegevens over de partities ongeveer uniform is of dat u de belasting statistieken aan het cluster resource manager rapporteert. Service Fabric taak verdeling wordt standaard berekend op basis van het aantal replica's. In het voor gaande voor beeld, waarmee 10 primaire replica's en 20 secundaire replica's op elk knoop punt in het cluster worden geplaatst. Dat werkt goed voor de belasting die gelijkmatig over de partities kan worden verdeeld. Als de belasting niet even is, moet u de belasting rapporteren zodat de Resource Manager kleinere replica's kan verpakken en dat grotere replica's meer geheugen op een afzonderlijk knoop punt kunnen verbruiken.

- Of de betrouw bare service in kwestie de enige opslag status in het cluster is. Aangezien u meerdere services naar een cluster kunt implementeren, moet u mindful van de resources die elke moet worden uitgevoerd en de status ervan beheren.

- Dat het cluster zelf niet groeit of verkleint. Als u meer computers toevoegt, worden uw replica's door Service Fabric opnieuw gebalanceerd om gebruik te maken van de extra capaciteit totdat het aantal machines het aantal partities in uw service overschrijdt, omdat een afzonderlijke replica geen machines kan omvatten. Als u daarentegen de grootte van het cluster reduceert door machines te verwijderen, worden uw replica's nauw keuriger verpakt en hebben ze minder algemene capaciteit.

### <a name="how-much-data-can-i-store-in-an-actor"></a>Hoeveel gegevens kan ik opslaan in een actor?

Net als bij betrouw bare Services wordt de hoeveelheid gegevens die u in een actor service kunt opslaan, alleen beperkt door de totale schijf ruimte en het beschik bare geheugen over de knoop punten in uw cluster. Individuele Actors zijn echter het meest effectief wanneer ze worden gebruikt voor het inkapselen van een kleine hoeveelheid status en gekoppelde bedrijfs logica. Als algemene regel moet een individuele actor een status hebben die wordt gemeten in kilo bytes.


### <a name="where-does-azure-service-fabric-resource-provider-store-customer-data"></a>Waar worden klant gegevens opgeslagen in azure Service Fabric resource provider?

De resource provider van Azure Service Fabric verplaatst of slaat klant gegevens niet op uit de regio waarin deze is geïmplementeerd.


## <a name="other-questions"></a>Andere vragen

### <a name="how-does-service-fabric-relate-to-containers"></a>Wat is Service Fabric gekoppeld aan containers?

Containers bieden een eenvoudige manier voor het inpakken van services en hun afhankelijkheden, zodat ze consistent worden uitgevoerd in alle omgevingen en op een afzonderlijke machine op een geïsoleerde manier kunnen werken. Service Fabric biedt een manier om services te implementeren en te beheren, met inbegrip [van services die zijn verpakt in een container](service-fabric-containers-overview.md).

### <a name="are-you-planning-to-open-source-service-fabric"></a>Bent u van plan om Service Fabric van de bron te openen?

Er zijn open-source onderdelen van Service Fabric ([reliable Services Framework](https://github.com/Azure/service-fabric-services-and-actors-dotnet), [reliable actors Framework](https://github.com/Azure/service-fabric-services-and-actors-dotnet), [ASP.net core integration libraries](https://github.com/Azure/service-fabric-aspnetcore), [service Fabric Explorer](https://github.com/Azure/service-fabric-explorer)en [service Fabric cli](https://github.com/Azure/service-fabric-cli)) op github en accepteren de bijdragen van de community voor deze projecten. 

We hebben [onlangs aangekondigd](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) dat de service Fabric-runtime wordt geopend. Op dit moment hebben we de [service Fabric opslag plaats](https://github.com/Microsoft/service-fabric/) op github met Linux-hulpprogram ma's voor bouwen en testen. Dit betekent dat u de opslag plaats kunt klonen, service Fabric voor Linux moet bouwen, basis tests moet uitvoeren, problemen kunt openen en pull-aanvragen moet verzenden. Er wordt hard gewerkt om de Windows build-omgeving te verkrijgen, samen met een volledige CI-omgeving.

Volg de [service Fabric blog](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) voor meer informatie over hoe deze worden aangekondigd.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [belangrijkste service Fabric concepten](service-fabric-technical-overview.md) en [Aanbevolen procedures](service-fabric-best-practices-overview.md)

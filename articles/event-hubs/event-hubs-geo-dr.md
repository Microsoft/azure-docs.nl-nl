---
title: Geo-nood herstel-Azure Event Hubs | Microsoft Docs
description: Over het gebruik van geografische regio's om een failover uit te voeren en herstel na nood gevallen in azure Event Hubs
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: e10ac5847a38190c8feaae5e51f9b55bee4c4fbc
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/04/2021
ms.locfileid: "97861479"
---
# <a name="azure-event-hubs---geo-disaster-recovery"></a>Azure-Event Hubs-geo-nood herstel 

Een tolerantie voor disastrous storingen bij het verwerken van gegevens bronnen is een vereiste voor veel ondernemingen en in sommige gevallen, zelfs door industriële voor Schriften. 

Azure Event Hubs verspreidt het risico op onherstelbare storingen van afzonderlijke machines of zelfs volledige racks in clusters die meerdere fout domeinen binnen een Data Center omspannen en implementeert transparante fout detectie en failover-mechanismen, zodat de service zal blijven functioneren binnen de gegarandeerde service niveaus en normaal gesp roken zonder merk bare onderbrekingen in het geval van dergelijke storingen. Als er een Event Hubs naam ruimte is gemaakt met de optie ingeschakeld voor [beschikbaarheids zones](../availability-zones/az-overview.md), wordt het risico van uitval risico verder verdeeld over drie fysiek gescheiden faciliteiten en is de service voldoende capaciteits reserves om direct te kunnen omgaan met het volledige, onherstelbaar verlies van de volledige faciliteit. 

Het Azure Event Hubs-cluster model met de ondersteuning voor de beschikbaarheids zone biedt een tolerantie voor problemen met de hardware en zelfs tot een onherstelbaar verlies van de volledige datacenter faciliteiten. Het kan nog steeds bestaan uit een grote fysieke vernietiging, waardoor zelfs deze maat regelen niet voldoende kunnen worden beschermd. 

De Event Hubs geo-nood herstel functie is zodanig ontworpen dat het herstellen van een ramp van deze omvang eenvoudiger is en dat de Azure-regio niet goed kan worden gerepareerd en zonder dat de configuratie van uw toepassingen hoeft te worden gewijzigd. Het afbreken van een Azure-regio omvat doorgaans diverse services en deze functie is vooral gericht op het behoud van de integriteit van de samengestelde toepassings configuratie.  

Met de functie voor het Geo-Disaster herstellen zorgt u ervoor dat de volledige configuratie van een naam ruimte (Event Hubs, consumenten groepen en instellingen) voortdurend wordt gerepliceerd van een primaire naam ruimte naar een secundaire naam ruimte wanneer deze is gekoppeld, en u kunt een eenmalige failover op elk gewenst moment initiëren van de primaire naar de secundaire. Bij het verplaatsen van de failover wordt de gekozen alias naam voor de naam ruimte opnieuw ingesteld op de secundaire naam ruimte en wordt de koppeling verbroken. De failover is bijna onmiddellijk eenmaal gestart. 

> [!IMPORTANT]
> Met de functie wordt een onmiddellijke continuïteit van bewerkingen met dezelfde configuratie ingeschakeld, maar **worden de gebeurtenis gegevens niet gerepliceerd**. Tenzij de nood situatie het verlies van alle zones heeft veroorzaakt, blijven de gebeurtenis gegevens bewaard in de primaire Event hub nadat de failover kan worden hersteld en de historische gebeurtenissen kunnen worden verkregen vanaf het moment dat de toegang tot een keer is teruggezet. Voor het repliceren van gebeurtenis gegevens en het uitvoeren van overeenkomende naam ruimten in actieve/actieve configuraties om te voldoen aan storingen en rampen, hoeft u niet te voldoen aan deze geo-nood herstel functie set, maar volgt u de [richt lijnen voor replicatie](event-hubs-federation-overview.md).  

## <a name="outages-and-disasters"></a>Storingen en rampen

Het is belang rijk om het onderscheid tussen ' storingen ' en ' rampen ' te noteren. Een **storing** is de tijdelijke niet-beschik baarheid van Azure Event hubs en kan van invloed zijn op sommige onderdelen van de service, zoals een berichten archief of zelfs het hele Data Center. Nadat het probleem is opgelost, wordt Event Hubs echter weer beschikbaar. Normaal gesp roken veroorzaakt een storing geen verlies van berichten of andere gegevens. Een voor beeld van een dergelijke storing kan een stroom storing in het Data Center zijn. Sommige storingen zijn alleen korte verbindings verliezen vanwege tijdelijke of netwerk problemen. 

Een *nood* geval wordt gedefinieerd als het permanente verlies of een langere periode van een event hubs cluster, Azure-regio of Data Center. De regio of het Data Center kan al dan niet meer beschikbaar zijn, of is mogelijk niet actief voor uren of dagen. Voor beelden van dergelijke nood gevallen zijn brand, overstroming of aard beving. Een nood herstel bewerking die permanent wordt, kan leiden tot verlies van sommige berichten, gebeurtenissen of andere gegevens. In de meeste gevallen moeten er echter geen gegevens verloren gaan en kunnen berichten worden hersteld wanneer er een back-up van het Data Center wordt gemaakt.

De functie voor het terugzetten van de geo-nood herstel van Azure Event Hubs is een oplossing voor herstel na nood gevallen. De concepten en werk stroom die in dit artikel worden beschreven, zijn van toepassing op scenario's voor nood gevallen en niet op tijdelijke storingen. Raadpleeg [dit artikel](/azure/architecture/resiliency/disaster-recovery-azure-applications)voor een gedetailleerde bespreking van herstel na nood gevallen in Microsoft Azure.

## <a name="basic-concepts-and-terms"></a>Basis concepten en termen

De functie nood herstel implementeert herstel na nood gevallen van meta gegevens en is afhankelijk van de primaire en secundaire naam ruimten voor nood herstel. 

De functie voor het opnieuw gebruiken van geo-nood herstel is alleen beschikbaar voor de [Standard-en speciale sku's](https://azure.microsoft.com/pricing/details/event-hubs/) . U hoeft geen connection string wijzigingen aan te brengen, omdat de verbinding wordt gemaakt via een alias.

In dit artikel worden de volgende termen gebruikt:

-  *Alias*: de naam voor een nood herstel configuratie die u hebt ingesteld. De alias biedt een enkele stabiele FQDN-naam (Fully Qualified Domain Name) connection string. Toepassingen gebruiken deze alias connection string om verbinding te maken met een naam ruimte. 

-  *Primaire/secundaire naam ruimte*: de naam ruimten die overeenkomen met de alias. De primaire naam ruimte is actief en ontvangt berichten (dit kan een bestaande of nieuwe naam ruimte zijn). De secundaire naam ruimte is ' passief ' en ontvangt geen berichten. De meta gegevens tussen beide zijn synchroon, zodat beide berichten zonder toepassings code of connection string wijzigingen naadloos kunnen accepteren. Om ervoor te zorgen dat alleen de actieve naam ruimte berichten ontvangt, moet u de alias gebruiken. 

-  *Meta gegevens*: entiteiten zoals Event hubs en consumenten groepen; en hun eigenschappen van de service die aan de naam ruimte zijn gekoppeld. Alleen entiteiten en hun instellingen worden automatisch gerepliceerd. Berichten en gebeurtenissen worden niet gerepliceerd. 

-  *Failover*: het proces van het activeren van de secundaire naam ruimte.

## <a name="supported-namespace-pairs"></a>Ondersteunde naam ruimte paren
De volgende combi Naties van primaire en secundaire naam ruimten worden ondersteund:  

| Primaire naam ruimte | Secundaire naam ruimte | Ondersteund | 
| ----------------- | -------------------- | ---------- |
| Standard | Standard | Ja | 
| Standard | Toegewezen | Ja | 
| Toegewezen | Toegewezen | Ja | 
| Toegewezen | Standard | Nee | 

> [!NOTE]
> U kunt geen naam ruimten koppelen die zich in hetzelfde toegewezen cluster bevinden. U kunt naam ruimten in afzonderlijke clusters koppelen. 

## <a name="setup-and-failover-flow"></a>Stroom instellen en failover

De volgende sectie bevat een overzicht van het failoverproces en legt uit hoe de eerste failover moet worden ingesteld. 

![1][]

### <a name="setup"></a>Instellen

U maakt of gebruikt eerst een bestaande primaire naam ruimte en een nieuwe secundaire naam ruimte en koppelt deze twee. Met deze koppeling krijgt u een alias die u kunt gebruiken om verbinding te maken. Omdat u een alias gebruikt, hoeft u geen verbindings reeksen te wijzigen. U kunt alleen nieuwe naam ruimten toevoegen aan uw failover-koppeling. Ten slotte moet u bewaking toevoegen om te detecteren of een failover nood zakelijk is. In de meeste gevallen is de service een deel van een groot ecosysteem, waardoor automatische failovers zelden mogelijk zijn, omdat vaak failovers moeten worden uitgevoerd in synchronisatie met het resterende subsysteem of infra structuur.

### <a name="example"></a>Voorbeeld

In een voor beeld van dit scenario moet u een POS-oplossing (Point of Sale) overwegen waarmee berichten of gebeurtenissen worden verzonden. Event Hubs geeft deze gebeurtenissen door aan een toewijzing of het opnieuw Format teren van een oplossing, waarna toegewezen gegevens worden doorgestuurd naar een ander systeem voor verdere verwerking. Op dat moment kunnen al deze systemen worden gehost in dezelfde Azure-regio. De beslissing van wanneer en welk deel failover moet worden uitgevoerd, is afhankelijk van de gegevens stroom in uw infra structuur. 

U kunt de failover automatiseren met bewakings systemen of met aangepaste bewakings oplossingen. Een dergelijke automatisering vergt echter wel extra planning en werk, wat buiten het bereik van dit artikel valt.

### <a name="failover-flow"></a>Failover-stroom

Als u de failover initieert, zijn twee stappen vereist:

1. Als er zich een andere storing voordoet, wilt u opnieuw een failover kunnen uitvoeren. Stel daarom een andere passieve naam ruimte in en werk de koppeling bij. 

2. Pull-berichten van de voormalige primaire naam ruimte zodra deze weer beschikbaar is. Daarna gebruikt u die naam ruimte voor normale berichten buiten uw Geo-herstel configuratie of verwijdert u de oude primaire naam ruimte.

> [!NOTE]
> Alleen mislukte doorstuur semantiek worden ondersteund. In dit scenario kunt u een failover uitvoeren en vervolgens opnieuw koppelen met een nieuwe naam ruimte. Failback wordt niet ondersteund; bijvoorbeeld in een SQL-cluster. 

![2][]

## <a name="management"></a>Beheer

Als u een fout hebt gemaakt, u koppelt bijvoorbeeld de verkeerde regio's tijdens de eerste installatie. u kunt het koppelen van de twee naam ruimten op elk gewenst moment verstoren. Als u de gekoppelde naam ruimten als gewone naam ruimten wilt gebruiken, verwijdert u de alias.

## <a name="samples"></a>Voorbeelden

Het [voor beeld op github](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) laat zien hoe u een failover instelt en initieert. In dit voor beeld worden de volgende concepten gedemonstreerd:

- Instellingen die vereist zijn in Azure Active Directory om Azure Resource Manager te gebruiken met Event Hubs. 
- Stappen die nodig zijn om de voorbeeld code uit te voeren. 
- Verzenden en ontvangen van de huidige primaire naam ruimte. 

## <a name="considerations"></a>Overwegingen

Let op de volgende punten als u rekening moet houden:

1. Event Hubs met geo-nood herstel worden geen gegevens gerepliceerd, waardoor u de oude offset waarde van uw primaire Event Hub niet opnieuw kunt gebruiken op uw secundaire Event Hub. Het wordt aangeraden om de ontvanger van uw gebeurtenis opnieuw te starten met een van de volgende methoden:

- *EventPosition. FromStart ()* : als u alle gegevens op de secundaire Event hub wilt lezen.
- *EventPosition. FromEnd ()* : als u alle nieuwe gegevens wilt lezen vanaf het moment van verbinding met uw secundaire Event hub.
- *EventPosition. FromEnqueuedTime (datetime)* : als u alle ontvangen gegevens in uw secundaire Event hub wilt lezen vanaf een bepaalde datum en tijd.

2. Bij het plannen van de failover moet u ook rekening houden met de tijds factor. Als u bijvoorbeeld langer dan 15 tot 20 minuten geen verbinding meer hebt, kunt u ervoor kiezen om de failover te initiëren. 
 
3. Het feit dat er geen gegevens worden gerepliceerd, betekent dat huidige actieve sessies niet worden gerepliceerd. Bovendien werken duplicaten detectie en geplande berichten mogelijk niet. Nieuwe sessies, geplande berichten en nieuwe duplicaten werken wel. 

4. Failover van een complexe gedistribueerde infra structuur moet ten minste één keer worden [gereageerd](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) . 

5. Het synchroniseren van entiteiten kan enige tijd duren, ongeveer 50-100 entiteiten per minuut.

## <a name="availability-zones"></a>Beschikbaarheidszones 

De Event Hubs standaard-SKU biedt ondersteuning voor [Beschikbaarheidszones](../availability-zones/az-overview.md), waardoor fout geïsoleerde locaties binnen een Azure-regio worden geboden. 

> [!NOTE]
> De Beschikbaarheidszones ondersteuning voor Azure Event Hubs Standard is alleen beschikbaar in [Azure-regio's](../availability-zones/az-region.md) waar beschikbaarheids zones aanwezig zijn.

U kunt Beschikbaarheidszones alleen inschakelen voor nieuwe naam ruimten, met behulp van de Azure Portal. Event Hubs biedt geen ondersteuning voor de migratie van bestaande naam ruimten. U kunt zone redundantie niet uitschakelen nadat u deze in uw naam ruimte hebt ingeschakeld.

![3][]

## <a name="private-endpoints"></a>Privé-eindpunten
Deze sectie bevat aanvullende overwegingen bij het gebruik van geo-nood herstel met naam ruimten die persoonlijke eind punten gebruiken. Zie [persoonlijke eind punten configureren](private-link-service.md)voor meer informatie over het gebruik van privé-eind punten met Event hubs in het algemeen.

### <a name="new-pairings"></a>Nieuwe paren
Als u probeert een koppeling te maken tussen een primaire naam ruimte met een persoonlijk eind punt en een secundaire naam ruimte zonder persoonlijk eind punt, mislukt de koppeling. De koppeling kan alleen worden uitgevoerd als zowel de primaire als de secundaire naam ruimte persoonlijke eind punten hebben. U wordt aangeraden dezelfde configuraties te gebruiken voor de primaire en secundaire naam ruimten en op virtuele netwerken waarin privé-eind punten worden gemaakt.  

> [!NOTE]
> Wanneer u probeert de primaire naam ruimte te koppelen aan een persoonlijk eind punt en een secundaire naam ruimte, controleert het validatie proces alleen of er een persoonlijk eind punt bestaat op de secundaire naam ruimte. Er wordt niet gecontroleerd of het eind punt werkt of werkt na een failover. Het is uw verantwoordelijkheid om ervoor te zorgen dat de secundaire naam ruimte met het persoonlijke eind punt op de verwachte manier werkt na een failover.
>
> Als u wilt testen of de configuraties van het particuliere eind punt hetzelfde zijn op de primaire en secundaire naam ruimten, verzendt u een lees aanvraag (bijvoorbeeld: [Get Event hub](/rest/api/eventhub/get-event-hub)) naar de secundaire naam ruimte van buiten het virtuele netwerk en controleert u of u een fout bericht ontvangt van de service.

### <a name="existing-pairings"></a>Bestaande paren
Als er al een koppeling tussen de primaire en secundaire naam ruimte bestaat, mislukt het maken van een persoonlijk eind punt in de primaire naam ruimte. Als u wilt oplossen, maakt u eerst een persoonlijk eind punt in de secundaire naam ruimte en maakt u er er een voor de primaire naam ruimte.

> [!NOTE]
> Hoewel we alleen-lezen toegang tot de secundaire naam ruimte toestaan, worden updates voor de configuraties van particuliere endpoints toegestaan. 

### <a name="recommended-configuration"></a>Aanbevolen configuratie
Bij het maken van een configuratie voor herstel na nood gevallen voor uw toepassing en Event Hubs naam ruimten, moet u persoonlijke eind punten maken voor zowel de primaire als de secundaire Event Hubs naam ruimten voor virtuele netwerken die zowel de primaire als de secundaire exemplaren van uw toepassing hosten. 

Stel dat u twee virtuele netwerken hebt: VNET-1, VNET-2 en deze primaire en secundaire naam ruimten: Event hubs-Namespace1-Primary, Event hubs-Namespace2-secundair. U moet de volgende stappen uitvoeren: 

- Maak op Event hubs-Namespace1-Primary twee persoonlijke eind punten die gebruikmaken van subnetten van VNET-1 en VNET-2
- Maak op Event hubs-Namespace2-secundair twee persoonlijke eind punten die gebruikmaken van dezelfde subnetten van VNET-1 en VNET-2 

![Persoonlijke eind punten en virtuele netwerken](./media/event-hubs-geo-dr/private-endpoints-virtual-networks.png)

Voor deel van deze benadering is dat failover kan plaatsvinden op de toepassingslaag, onafhankelijk van Event Hubs naam ruimte. Denk eens na over de volgende scenario's: 

**Failover van toepassing:** Hier komt de toepassing niet voor in VNET-1 maar wordt deze verplaatst naar VNET-2. Als beide persoonlijke eind punten zijn geconfigureerd op zowel VNET-1 als VNET-2 voor zowel primaire als secundaire naam ruimten, werkt de toepassing gewoon. 

**Event hubs failover van de naam ruimte**: hier wordt de toepassing opnieuw uitgevoerd, omdat beide particuliere eind punten zijn geconfigureerd voor virtuele netwerken voor zowel de primaire als de secundaire naam ruimte. 

> [!NOTE]
> Zie [Virtual Network-Business continuïteit](../virtual-network/virtual-network-disaster-recovery-guidance.md)(Engelstalig) voor meer informatie over het herstel van geo-nood gevallen van een virtueel netwerk.
 
## <a name="next-steps"></a>Volgende stappen

* Het [voor beeld op github gaat over](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) een eenvoudige werk stroom die een geo-koppeling maakt en initieert een failover voor een nood herstel scenario.
* In de [rest API-verwijzing](/rest/api/eventhub/) worden api's beschreven voor het uitvoeren van de configuratie voor geo-nood herstel.

Voor meer informatie over Event Hubs gaat u naar de volgende koppelingen:

- Aan de slag met Event Hubs
    - [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-java-get-started-send.md)
* [Veelgestelde vragen over Event Hubs](event-hubs-faq.md)
* [Voorbeeldtoepassingen die gebruikmaken van Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-geo-dr/geo1.png
[2]: ./media/event-hubs-geo-dr/geo2.png
[3]: ./media/event-hubs-geo-dr/eh-az.png

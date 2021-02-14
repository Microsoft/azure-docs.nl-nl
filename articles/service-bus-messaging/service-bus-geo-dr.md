---
title: Azure Service Bus geo-nood herstel | Microsoft Docs
description: Over het gebruik van geografische regio's voor failover en herstel na nood gevallen in Azure Service Bus
ms.topic: article
ms.date: 02/10/2021
ms.openlocfilehash: 86d35465e5b31514f4d215095932b857ce7dcb35
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100384295"
---
# <a name="azure-service-bus-geo-disaster-recovery"></a>Azure Service Bus geo-nood herstel

Een tolerantie voor disastrous storingen bij het verwerken van gegevens bronnen is een vereiste voor veel ondernemingen en in sommige gevallen, zelfs door industriële voor Schriften. 

Azure Service Bus verspreidt het risico op onherstelbare storingen van afzonderlijke machines of zelfs complete racks voor clusters die meerdere fout domeinen binnen een Data Center omspannen en implementeert transparante fout detectie en failover-mechanismen, zodat de service binnen de gegarandeerde service niveaus blijft functioneren en normaal gesp roken zonder merkbaar onderbrekingen wanneer dergelijke fouten optreden. Als er een Service Bus naam ruimte is gemaakt met de optie ingeschakeld voor [beschikbaarheids zones](../availability-zones/az-overview.md), wordt het risico van uitval risico verder verdeeld over drie fysiek gescheiden faciliteiten en is de service voldoende capaciteits reserves om direct te kunnen omgaan met het volledige, onherstelbaar verlies van de volledige faciliteit. 

Het cluster model voor alle Active Azure Service Bus met de ondersteuning van de beschikbaarheids zone is van toepassing op alle on-premises Message Broker-producten in termen van tolerantie tegen problemen met de hardwarestoringen en zelfs tot onherstelbare verlies van volledige datacenter faciliteiten. Het kan nog steeds bestaan uit een grote fysieke vernietiging, waardoor zelfs deze maat regelen niet voldoende kunnen worden beschermd. 

De Service Bus geo-nood herstel functie is zodanig ontworpen dat het herstellen van een ramp van deze omvang eenvoudiger is en dat de Azure-regio niet goed kan worden gerepareerd en zonder dat de configuratie van uw toepassingen hoeft te worden gewijzigd. Het afbreken van een Azure-regio omvat doorgaans diverse services en deze functie is vooral gericht op het behoud van de integriteit van de samengestelde toepassings configuratie. De functie is wereld wijd beschikbaar voor de Service Bus Premium-SKU. 

De functie voor het Geo-Disaster herstellen zorgt ervoor dat de volledige configuratie van een naam ruimte (wacht rijen, onderwerpen, abonnementen, filters) voortdurend wordt gerepliceerd van een primaire naam ruimte naar een secundaire naam ruimte, en Hiermee kunt u een eenmalige failover van de primaire naar de secundaire, op elk gewenst moment starten. Bij het verplaatsen van de failover wordt de gekozen alias naam voor de naam ruimte opnieuw ingesteld op de secundaire naam ruimte en wordt de koppeling verbroken. De failover is bijna onmiddellijk eenmaal gestart. 

> [!IMPORTANT]
> Met deze functie kunt u een onmiddellijke continuïteit van bewerkingen met dezelfde configuratie uitvoeren, maar worden **de berichten in wacht rijen of onderwerp-abonnementen of wacht rijen voor onbestel berichten niet gerepliceerd**. Als u de wachtrij semantiek wilt behouden, is voor deze replicatie niet alleen de replicatie van bericht gegevens vereist, maar van elke wijziging in de status in de Broker. Voor de meeste Service Bus-naam ruimten, overschrijdt het vereiste replicatie verkeer het verkeer van de toepassing en de wacht rijen met een hoge door Voer, de meeste berichten worden gerepliceerd naar de secundaire terwijl ze al van de primaire worden verwijderd, waardoor overmatig verspilling verkeer ontstaat. Voor replicatie routes met een hoge latentie die van toepassing zijn op een groot aantal paars dat u zou kiezen voor een geo-nood herstel, is het mogelijk dat het replicatie verkeer niet langer kan worden bewaard met het toepassings verkeer als gevolg van latentie-veroorzaakte beperkings effecten.
 
> [!TIP]
> Voor het repliceren van de inhoud van wacht rijen en onderwerpen, en het uitvoeren van overeenkomende naam ruimten in actieve/actieve configuraties om te voldoen aan storingen en rampen, hoeft u zich niet aan deze functieset voor geo-nood herstel te houden, maar volgt u de [richt lijnen voor replicatie](service-bus-federation-overview.md).  

## <a name="outages-and-disasters"></a>Storingen en rampen

Het is belang rijk om het onderscheid tussen ' storingen ' en ' rampen ' te noteren. 

Een *storing* is de tijdelijke niet-beschik baarheid van Azure service bus en kan van invloed zijn op sommige onderdelen van de service, zoals een berichten archief of zelfs het hele Data Center. Nadat het probleem is opgelost, wordt Service Bus echter weer beschikbaar. Normaal gesp roken veroorzaakt een storing geen verlies van berichten of andere gegevens. Een voor beeld van een dergelijke storing kan een stroom storing in het Data Center zijn. Sommige storingen zijn alleen korte verbindings verliezen vanwege tijdelijke of netwerk problemen. 

Een *nood* geval wordt gedefinieerd als het permanente verlies of een langere periode van een service bus cluster, Azure-regio of Data Center. De regio of het Data Center kan al dan niet meer beschikbaar zijn, of is mogelijk niet actief voor uren of dagen. Voor beelden van dergelijke nood gevallen zijn brand, overstroming of aard beving. Een nood herstel bewerking die permanent wordt, kan leiden tot verlies van sommige berichten, gebeurtenissen of andere gegevens. In de meeste gevallen moeten er echter geen gegevens verloren gaan en kunnen berichten worden hersteld wanneer er een back-up van het Data Center wordt gemaakt.

De functie voor het opnieuw gebruiken van geo-nood herstel van Azure Service Bus is een oplossing voor nood herstel. De concepten en werk stroom die in dit artikel worden beschreven, zijn van toepassing op scenario's voor nood gevallen en niet op tijdelijke storingen. Raadpleeg [dit artikel](/azure/architecture/resiliency/disaster-recovery-azure-applications)voor een gedetailleerde bespreking van herstel na nood gevallen in Microsoft Azure.   

## <a name="basic-concepts-and-terms"></a>Basis concepten en termen

De functie nood herstel implementeert herstel na nood gevallen van meta gegevens en is afhankelijk van de primaire en secundaire naam ruimten voor nood herstel. De functie voor het opnieuw gebruiken van geo-nood herstel is alleen beschikbaar voor de [Premium-SKU](service-bus-premium-messaging.md) . U hoeft geen connection string wijzigingen aan te brengen, omdat de verbinding wordt gemaakt via een alias.

In dit artikel worden de volgende termen gebruikt:

-  *Alias*: de naam voor een nood herstel configuratie die u hebt ingesteld. De alias biedt een enkele stabiele FQDN-naam (Fully Qualified Domain Name) connection string. Toepassingen gebruiken deze alias connection string om verbinding te maken met een naam ruimte. Als u een alias gebruikt, zorgt u ervoor dat de connection string niet wordt gewijzigd wanneer de failover wordt geactiveerd.

-  *Primaire/secundaire naam ruimte*: de naam ruimten die overeenkomen met de alias. De primaire naam ruimte is actief en ontvangt berichten (dit kan een bestaande of nieuwe naam ruimte zijn). De secundaire naam ruimte is ' passief ' en ontvangt geen berichten. De meta gegevens tussen beide zijn synchroon, zodat beide berichten zonder toepassings code of connection string wijzigingen naadloos kunnen accepteren. Om ervoor te zorgen dat alleen de actieve naam ruimte berichten ontvangt, moet u de alias gebruiken. 

    > [!IMPORTANT]
    > Voor de functie voor het maken van een geo-nood herstel moet het abonnement en de resource groep hetzelfde zijn voor de primaire en secundaire naam ruimten.
-  *Meta gegevens*: entiteiten zoals wacht rijen, onderwerpen en abonnementen; en hun eigenschappen van de service die aan de naam ruimte zijn gekoppeld. Alleen entiteiten en hun instellingen worden automatisch gerepliceerd. Berichten worden niet gerepliceerd.

-  *Failover*: het proces van het activeren van de secundaire naam ruimte.

## <a name="setup"></a>Instellen

In het volgende gedeelte vindt u een overzicht van het instellen van een koppeling tussen de naam ruimten.

![1][]

U maakt of gebruikt eerst een bestaande primaire naam ruimte en een nieuwe secundaire naam ruimte en koppelt deze twee. Met deze koppeling krijgt u een alias die u kunt gebruiken om verbinding te maken. Omdat u een alias gebruikt, hoeft u geen verbindings reeksen te wijzigen. U kunt alleen nieuwe naam ruimten toevoegen aan uw failover-koppeling. 

1. Maak de primaire naam ruimte.
1. Maak de secundaire naam ruimte in het abonnement en de resource groep die de primaire naam ruimte heeft, maar in een andere regio. Deze stap is optioneel. U kunt de secundaire naam ruimte maken terwijl u de koppeling in de volgende stap maakt. 
1. Ga in het Azure Portal naar uw primaire naam ruimte.
1. Selecteer **geo-Recovery** in het menu links en selecteer **koppelen starten** op de werk balk. 

    :::image type="content" source="./media/service-bus-geo-dr/primary-namspace-initiate-pairing-button.png" alt-text="Koppeling vanuit de primaire naam ruimte initiëren":::    
1. Voer de volgende stappen uit op de pagina **koppeling initiëren** :
    1. Selecteer een bestaande secundaire naam ruimte of maak er een in het abonnement en de resource groep die de primaire naam ruimte heeft. In dit voor beeld wordt een bestaande naam ruimte gebruikt als secundaire naam ruimte.  
    1. Voer bij **alias** een alias in voor de geo-Dr-koppeling. 
    1. Ten slotte selecteert u **Create**. 

        :::image type="content" source="./media/service-bus-geo-dr/initiate-pairing-page.png" alt-text="Secundaire naam ruimte selecteren":::        
1. U ziet de **Service Bus geo-Dr-alias** pagina, zoals wordt weer gegeven in de volgende afbeelding. U kunt ook naar de **geo-Dr-alias** pagina van de primaire naam ruimte pagina navigeren door het **geo-herstel** te selecteren in het menu links. 

    :::image type="content" source="./media/service-bus-geo-dr/service-bus-geo-dr-alias-page.png" alt-text="Service Bus geo-DR-alias pagina":::
1. Selecteer op de pagina **geo-Dr-alias** de optie **beleid voor gedeelde toegang** in het linkermenu om toegang te krijgen tot de primaire Connection String voor de alias. Gebruik deze connection string in plaats van de connection string rechtstreeks naar de primaire/secundaire naam ruimte te gebruiken. In eerste instantie wijst de alias naar de primaire naam ruimte.
1. Ga naar de pagina **overzicht** . U kunt de volgende acties uitvoeren: 
    1. Verbreek de koppeling tussen de primaire en secundaire naam ruimte. Selecteer **koppeling verbreekt** op de werk balk. 
    1. Hand matig een failover naar de secundaire naam ruimte. 
        1. Selecteer **failover** in de werk balk. 
        1. Bevestig dat u een failover wilt uitvoeren naar de secundaire naam ruimte door in uw alias te typen. 
        1. Schakel de optie **veilige failover** in om een failover uit te scha kelen naar de secundaire naam ruimte. Met deze functie zorgt u ervoor dat er geo-DR-replicaties in behandeling zijn voltooid voordat u overschakelt naar de secundaire. 
        1. Selecteer vervolgens **failover**. 
        
            :::image type="content" source="./media/service-bus-geo-dr/failover-page.png" alt-text="{alt-text}":::
    
            > [!IMPORTANT]
            > Als er een failover wordt uitgevoerd, wordt de secundaire naam ruimte geactiveerd en wordt de primaire naam ruimte verwijderd uit de Geo-Disaster herstel koppeling. Maak een andere naam ruimte om een nieuw geo-nood herstel paar te maken. 

1. Ten slotte moet u bewaking toevoegen om te detecteren of een failover nood zakelijk is. In de meeste gevallen is de service een deel van een groot ecosysteem, waardoor automatische failovers zelden mogelijk zijn, omdat vaak failovers moeten worden uitgevoerd in synchronisatie met het resterende subsysteem of infra structuur.

### <a name="service-bus-standard-to-premium"></a>Service Bus standaard voor Premium
Als u [uw Azure service bus Standard-naam ruimte hebt gemigreerd naar Azure service bus Premium](service-bus-migrate-standard-premium.md), moet u de bestaande alias (dat wil zeggen, uw service bus Standard-naam ruimte Connection String) gebruiken om de configuratie voor herstel na nood gevallen te maken via de **PS/cli** of **rest API**.

De reden hiervoor is dat uw Azure Service Bus standaard naam ruimte van connection string/DNS zelf een alias voor uw Azure Service Bus Premium-naam ruimte wordt.

Uw client toepassingen moeten gebruikmaken van deze alias (dat wil zeggen, de Azure Service Bus standaard naam ruimte connection string) om verbinding te maken met de Premium-naam ruimte waarin de nood herstel koppeling is ingesteld.

Als u de portal gebruikt voor het instellen van de configuratie voor herstel na nood gevallen, wordt dit voor behoud door de portal van u afgeleid.


## <a name="failover-flow"></a>Failover-stroom

Een failover wordt hand matig geactiveerd door de klant (expliciet via een opdracht of door de bedrijfs logica van de client waarmee de opdracht wordt geactiveerd) en nooit door Azure. Het biedt het volledige eigendom van de klant en de zicht baarheid van de uitval oplossing op de backbone van Azure.

![4][]

Nadat de failover is geactiveerd-

1. De ***alias*** Connection String wordt bijgewerkt zodat deze verwijst naar de secundaire Premium-naam ruimte.

2. Clients (afzenders en ontvangers) maken automatisch verbinding met de secundaire naam ruimte.

3. De bestaande koppeling tussen de primaire en secundaire Premium-naam ruimte is verbroken.

Zodra de failover is gestart,

1. Als er zich een andere storing voordoet, wilt u opnieuw een failover kunnen uitvoeren. Stel daarom een andere passieve naam ruimte in en werk de koppeling bij. 

2. Pull-berichten van de voormalige primaire naam ruimte zodra deze weer beschikbaar is. Daarna gebruikt u die naam ruimte voor normale berichten buiten uw Geo-herstel configuratie of verwijdert u de oude primaire naam ruimte.

> [!NOTE]
> Alleen mislukte doorstuur semantiek worden ondersteund. In dit scenario kunt u een failover uitvoeren en vervolgens opnieuw koppelen met een nieuwe naam ruimte. Failback wordt niet ondersteund; bijvoorbeeld in een SQL-cluster. 

U kunt de failover automatiseren met bewakings systemen of met aangepaste bewakings oplossingen. Een dergelijke automatisering vergt echter wel extra planning en werk, wat buiten het bereik van dit artikel valt.

![2][]

## <a name="management"></a>Beheer

Als u een fout hebt gemaakt, u koppelt bijvoorbeeld de verkeerde regio's tijdens de eerste installatie. u kunt het koppelen van de twee naam ruimten op elk gewenst moment verstoren. Als u de gekoppelde naam ruimten als gewone naam ruimten wilt gebruiken, verwijdert u de alias.

## <a name="use-existing-namespace-as-alias"></a>Bestaande naam ruimte als alias gebruiken

Als u een scenario hebt waarin u de verbindingen van de producenten en consumenten niet kunt wijzigen, kunt u de naam van de naam ruimte opnieuw gebruiken als alias naam. Bekijk [hier de voorbeeld code op github](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR_existing_namespace_name).

## <a name="samples"></a>Voorbeelden

De [voor beelden op github](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/) laten zien hoe u een failover instelt en initieert. In deze voor beelden worden de volgende concepten gedemonstreerd:

- Een .NET-voor beeld en-instellingen die vereist zijn in Azure Active Directory om Azure Resource Manager te gebruiken met Service Bus, voor het instellen en inschakelen van geo-nood herstel.
- Stappen die nodig zijn om de voorbeeld code uit te voeren.
- Een bestaande naam ruimte gebruiken als alias.
- Stappen voor het inschakelen van een geo-nood herstel via Power shell of CLI.
- [Verzend en ontvang berichten](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1) van de huidige primaire of secundaire naam ruimte met behulp van de alias.

## <a name="considerations"></a>Overwegingen

Let op de volgende punten als u rekening moet houden met deze release:

1. Bij het plannen van de failover moet u ook rekening houden met de tijds factor. Als u bijvoorbeeld langer dan 15 tot 20 minuten geen verbinding meer hebt, kunt u ervoor kiezen om de failover te initiëren.

2. Het feit dat er geen gegevens worden gerepliceerd, betekent dat momenteel actieve sessies niet worden gerepliceerd. Bovendien werken duplicaten detectie en geplande berichten mogelijk niet. Nieuwe sessies, nieuwe geplande berichten en nieuwe duplicaten werken wel. 

3. Failover van een complexe gedistribueerde infra structuur moet ten minste één keer worden [gereageerd](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) .

4. Het synchroniseren van entiteiten kan enige tijd duren, ongeveer 50-100 entiteiten per minuut. Abonnementen en regels tellen ook als entiteiten.

### <a name="availability-zones"></a>Beschikbaarheidszones

De Service Bus Premium SKU ondersteunt [Beschikbaarheidszones](../availability-zones/az-overview.md), waardoor er in dezelfde Azure-regio fout geïsoleerde locaties worden geboden. Service Bus beheert drie kopieën van het berichten archief (1 primaire en 2 secundaire). Service Bus houdt alle drie kopieën synchroon voor gegevens-en beheer bewerkingen. Als de primaire kopie mislukt, wordt een van de secundaire kopieën gepromoveerd tot primair zonder waargenomen uitval tijd. Als de toepassingen tijdelijke verbreken van Service Bus zien, wordt met de logica voor opnieuw proberen in de SDK automatisch opnieuw verbinding gemaakt met Service Bus. 

Wanneer u beschikbaarheids zones gebruikt, worden zowel meta gegevens als gegevens (berichten) gerepliceerd tussen data centers in de beschikbaarheids zone. 

> [!NOTE]
> De Beschikbaarheidszones ondersteuning voor Azure Service Bus Premium is alleen beschikbaar in [Azure-regio's](../availability-zones/az-region.md) waar beschikbaarheids zones aanwezig zijn.

U kunt Beschikbaarheidszones alleen inschakelen voor nieuwe naam ruimten, met behulp van de Azure Portal. Service Bus biedt geen ondersteuning voor de migratie van bestaande naam ruimten. U kunt zone redundantie niet uitschakelen nadat u deze in uw naam ruimte hebt ingeschakeld.

![3][]

## <a name="private-endpoints"></a>Privé-eindpunten
In deze sectie vindt u meer aandachtspunten bij het gebruik van geo-nood herstel met naam ruimten die persoonlijke eind punten gebruiken. Zie [Azure service bus met persoonlijke Azure-koppeling integreren](private-link-service.md)voor meer informatie over het gebruik van privé-eind punten met Service Bus in het algemeen.

### <a name="new-pairings"></a>Nieuwe paren
Als u probeert een koppeling te maken tussen een primaire naam ruimte met een persoonlijk eind punt en een secundaire naam ruimte zonder persoonlijk eind punt, mislukt de koppeling. De koppeling kan alleen worden uitgevoerd als zowel de primaire als de secundaire naam ruimte persoonlijke eind punten hebben. U wordt aangeraden dezelfde configuraties te gebruiken voor de primaire en secundaire naam ruimten en op virtuele netwerken waarin privé-eind punten worden gemaakt. 

> [!NOTE]
> Wanneer u probeert de primaire naam ruimte te koppelen aan een persoonlijk eind punt en de secundaire naam ruimte, controleert het validatie proces alleen of er een persoonlijk eind punt bestaat op de secundaire naam ruimte. Er wordt niet gecontroleerd of het eind punt werkt of werkt na een failover. Het is uw verantwoordelijkheid om ervoor te zorgen dat de secundaire naam ruimte met het persoonlijke eind punt op de verwachte manier werkt na een failover.
>
> Als u wilt testen of de configuraties van het particuliere eind punt hetzelfde zijn, verzendt u een aanvraag voor het [ophalen van wacht rijen](/rest/api/servicebus/stable/queues/get) naar de secundaire naam ruimte van buiten het virtuele netwerk en controleert u of u een fout bericht van de service ontvangt.

### <a name="existing-pairings"></a>Bestaande paren
Als er al een koppeling tussen de primaire en secundaire naam ruimte bestaat, mislukt het maken van een persoonlijk eind punt in de primaire naam ruimte. Als u wilt oplossen, maakt u eerst een persoonlijk eind punt in de secundaire naam ruimte en maakt u er er een voor de primaire naam ruimte.

> [!NOTE]
> Hoewel we alleen-lezen toegang tot de secundaire naam ruimte toestaan, worden updates voor de configuraties van particuliere endpoints toegestaan. 

### <a name="recommended-configuration"></a>Aanbevolen configuratie
Bij het maken van een configuratie voor herstel na nood gevallen voor uw toepassing en Service Bus, moet u persoonlijke eind punten maken voor zowel de primaire als de secundaire Service Bus naam ruimten voor virtuele netwerken die zowel de primaire als de secundaire exemplaren van uw toepassing hosten.

Stel dat u twee virtuele netwerken hebt: VNET-1, VNET-2 en deze primaire en tweede naam ruimten: ServiceBus-Namespace1-Primary, ServiceBus-Namespace2-secundair. U moet de volgende stappen uitvoeren: 

- Maak op ServiceBus-Namespace1-Primary twee persoonlijke eind punten die gebruikmaken van subnetten van VNET-1 en VNET-2
- Maak op ServiceBus-Namespace2-secundair twee persoonlijke eind punten die gebruikmaken van dezelfde subnetten van VNET-1 en VNET-2 

![Persoonlijke eind punten en virtuele netwerken](./media/service-bus-geo-dr/private-endpoints-virtual-networks.png)


Voor deel van deze benadering is dat failover kan plaatsvinden op de toepassingslaag, onafhankelijk van Service Bus naam ruimte. Denk eens na over de volgende scenario's: 

**Failover van toepassing:** Hier komt de toepassing niet voor in VNET-1 maar wordt deze verplaatst naar VNET-2. Als beide persoonlijke eind punten zijn geconfigureerd op zowel VNET-1 als VNET-2 voor zowel primaire als secundaire naam ruimten, werkt de toepassing gewoon. 

**Service Bus failover van de naam ruimte**: hier wordt de toepassing opnieuw uitgevoerd, omdat beide particuliere eind punten zijn geconfigureerd voor virtuele netwerken voor zowel de primaire als de secundaire naam ruimte. 

> [!NOTE]
> Zie [Virtual Network-Business continuïteit](../virtual-network/virtual-network-disaster-recovery-guidance.md)(Engelstalig) voor meer informatie over het herstel van geo-nood gevallen van een virtueel netwerk.

## <a name="next-steps"></a>Volgende stappen

- Zie de [rest API referentie](/rest/api/servicebus/stable/disasterrecoveryconfigs)voor geo-nood herstel hier.
- Voer het [voor beeld van](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2)een geo-nood herstel uit op github.
- Zie het voor beeld van geo-nood herstel [dat berichten naar een alias verzendt](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1).

Raadpleeg de volgende artikelen voor meer informatie over Service Bus Messa ging:

* [Service Bus-wachtrijen, -onderwerpen en -abonnementen](service-bus-queues-topics-subscriptions.md)
* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus-onderwerpen en -abonnementen gebruiken](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [Rest API](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geodr_setup_pairing.png
[2]: ./media/service-bus-geo-dr/geo2.png
[3]: ./media/service-bus-geo-dr/az.png
[4]: ./media/service-bus-geo-dr/geodr_failover_alias_update.png

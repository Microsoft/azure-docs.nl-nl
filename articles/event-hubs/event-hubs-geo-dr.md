---
title: Geo-noodherstel - Azure Event Hubs| Microsoft Docs
description: Geografische regio's gebruiken om een fail over te brengen en herstel na noodherstel uit te voeren in Azure Event Hubs
ms.topic: article
ms.date: 04/14/2021
ms.openlocfilehash: 504a83772c2ac8e3afc86465899357d0eda4eb92
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478655"
---
# <a name="azure-event-hubs---geo-disaster-recovery"></a>Azure Event Hubs- Herstel na geo-nood 

Tolerantie tegen uitval van gegevensverwerkingsresources is een vereiste voor veel ondernemingen en in sommige gevallen zelfs vereist door branchevoorschriften. 

Azure Event Hubs verspreidt al het risico op onherstelbare storingen van afzonderlijke machines of zelfs volledige rekken over clusters die meerdere foutdomeinen in een datacenter omvatten en implementeert transparante foutdetectie en failover-mechanismen, zodat de service blijft werken binnen de gegarandeerde serviceniveaus en meestal zonder merkbare onderbrekingen in het geval van dergelijke fouten. Als een Event Hubs-naamruimte is gemaakt met de ingeschakelde optie voor [beschikbaarheidszones,](../availability-zones/az-overview.md)wordt het storingsrisico verder verdeeld over drie fysiek gescheiden faciliteiten en heeft de service voldoende capaciteitsreserves om direct te kunnen omgaan met het volledige, onherstelbare verlies van de hele faciliteit. 

Het volledig actieve Azure Event Hubs met ondersteuning voor beschikbaarheidszone biedt tolerantie tegen ernstige hardwarestoringen en zelfs onherstelbare verlies van volledige datacenterfaciliteiten. Toch kunnen er ernstige situaties zijn met wijdverbreide fysieke vernietigende maatregelen die zelfs met deze maatregelen niet voldoende kunnen worden beschermd. 

De functie Event Hubs geo-herstel na noodgevallen is ontworpen om het gemakkelijker te maken om te herstellen van een noodgevallen van deze omvang en om een mislukte Azure-regio te verlaten voor een goede en zonder dat u de configuraties van uw toepassing moet wijzigen. Het verlaten van een Azure-regio omvat doorgaans verschillende services en deze functie is voornamelijk bedoeld om de integriteit van de samengestelde toepassingsconfiguratie te behouden.  

De Geo-Disaster-herstelfunctie zorgt ervoor dat de volledige configuratie van een naamruimte (Event Hubs, consumentengroepen en instellingen) continu wordt gerepliceerd van een primaire naamruimte naar een secundaire naamruimte wanneer deze is gekoppeld, en dat u op elk moment een eensgevolgde failover kunt initiëren van de primaire naar de secundaire. De failover verplaatst de gekozen aliasnaam voor de naamruimte opnieuw naar de secundaire naamruimte en verbreekt vervolgens de koppeling. De failover wordt bijna onmiddellijk gestart. 

> [!IMPORTANT]
> De functie maakt onmiddellijke continuïteit van bewerkingen met dezelfde configuratie mogelijk, maar repliceert **de gebeurtenisgegevens niet.** Tenzij het noodlot het verlies van alle zones heeft veroorzaakt, kunnen de gebeurtenisgegevens die na een failover in de primaire Event Hub worden bewaard, worden hersteld en kunnen de historische gebeurtenissen daar worden verkregen zodra de toegang is hersteld. Voor het repliceren van gebeurtenisgegevens en het gebruiken van bijbehorende naamruimten in actieve/actieve configuraties om te kunnen omgaan [](event-hubs-federation-overview.md)met uitval en noodherstel, moet u niet afhankelijk zijn van deze functieset voor geo-noodherstel, maar volgt u de replicatie-richtlijnen.  

## <a name="outages-and-disasters"></a>Uitval en noodscenario's

Het is belangrijk te weten dat er onderscheid wordt gemaakt tussen 'uitval' en 'noodscenario's'. Een **storing** is de tijdelijke onbeschikbaarheid van Azure Event Hubs en kan van invloed zijn op bepaalde onderdelen van de service, zoals een berichtenopslag of zelfs het hele datacenter. Nadat het probleem is opgelost, Event Hubs weer beschikbaar. Normaal gesproken veroorzaakt een storing niet het verlies van berichten of andere gegevens. Een voorbeeld van een dergelijke storing kan een stroomstoring in het datacenter zijn. Sommige uitval heeft alleen korte verbindingsverlies als gevolg van tijdelijke problemen of netwerkproblemen. 

Een *noodlot* wordt gedefinieerd als het permanente of langere verlies van een Event Hubs cluster, Azure-regio of datacenter. De regio of het datacenter is mogelijk al dan niet weer beschikbaar of is mogelijk uren of dagen niet beschikbaar. Voorbeelden van dergelijke rampen zijn brand, overstromingen of aardbevingen. Een noodlot dat permanent wordt, kan leiden tot het verlies van bepaalde berichten, gebeurtenissen of andere gegevens. In de meeste gevallen mag er echter geen gegevens verloren gaan en kunnen berichten worden hersteld zodra er een back-up van het datacenter is.

De functie geo-noodherstel van Azure Event Hubs is een oplossing voor herstel na noodherstel. De concepten en werkstroom die in dit artikel worden beschreven, zijn van toepassing op noodscenario's en niet op tijdelijke of tijdelijke uitval. Zie dit artikel voor een gedetailleerde beschrijving van Microsoft Azure herstel [na noodherstel.](/azure/architecture/resiliency/disaster-recovery-azure-applications)

## <a name="basic-concepts-and-terms"></a>Basisconcepten en -termen

De functie voor herstel na noodherstel implementeert metagegevens voor noodherstel en is afhankelijk van primaire en secundaire naamruimten voor herstel na noodherstel. 

De functie Geo-noodherstel is alleen beschikbaar voor de [standaard- en toegewezen SKU's.](https://azure.microsoft.com/pricing/details/event-hubs/) U hoeft geen wijzigingen aan te connection string, omdat de verbinding wordt gemaakt via een alias.

In dit artikel worden de volgende termen gebruikt:

-  *Alias:* de naam voor een configuratie voor herstel na noodherstel die u hebt ingesteld. De alias biedt één stabiele FQDN-naam (Fully Qualified Domain Name) connection string. Toepassingen gebruiken deze alias connection string verbinding te maken met een naamruimte. 

-  *Primaire/secundaire naamruimte:* de naamruimten die overeenkomen met de alias. De primaire naamruimte is 'actief' en ontvangt berichten (kan een bestaande of nieuwe naamruimte zijn). De secundaire naamruimte is 'passief' en ontvangt geen berichten. De metagegevens tussen beide zijn gesynchroniseerd, zodat beide naadloos berichten kunnen accepteren zonder toepassingscode of connection string wijzigingen. Om ervoor te zorgen dat alleen de actieve naamruimte berichten ontvangt, moet u de alias gebruiken.
-  *Metagegevens:* entiteiten zoals Event Hubs en consumentengroepen; en hun eigenschappen van de service die zijn gekoppeld aan de naamruimte. Alleen entiteiten en hun instellingen worden automatisch gerepliceerd. Berichten en gebeurtenissen worden niet gerepliceerd. 
-  *Failover:* het proces van het activeren van de secundaire naamruimte.

## <a name="supported-namespace-pairs"></a>Ondersteunde naamruimteparen
De volgende combinaties van primaire en secundaire naamruimten worden ondersteund:  

| Primaire naamruimte | Secundaire naamruimte | Ondersteund | 
| ----------------- | -------------------- | ---------- |
| Standard | Standard | Ja | 
| Standard | Toegewezen | Ja | 
| Toegewezen | Toegewezen | Ja | 
| Toegewezen | Standard | Nee | 

> [!NOTE]
> U kunt geen naamruimten koppelen die zich in hetzelfde toegewezen cluster. U kunt naamruimten in afzonderlijke clusters koppelen. 

## <a name="setup-and-failover-flow"></a>Installatie- en failoverstroom

In de volgende sectie wordt een overzicht gegeven van het failoverproces en wordt uitgelegd hoe u de eerste failover in kunt stellen. 

![1][]

### <a name="setup"></a>Instellen

Eerst maakt of gebruikt u een bestaande primaire naamruimte en een nieuwe secundaire naamruimte en vervolgens koppelt u de twee. Deze koppeling biedt u een alias die u kunt gebruiken om verbinding te maken. Omdat u een alias gebruikt, hoeft u de verbindingsreeksen niet te wijzigen. Alleen nieuwe naamruimten kunnen worden toegevoegd aan uw failover-koppeling. 

1. Maak de primaire naamruimte.
1. Maak de secundaire naamruimte in een andere regio. Deze stap is optioneel. U kunt de secundaire naamruimte maken tijdens het maken van de koppeling in de volgende stap. 
1. Ga in Azure Portal naar uw primaire naamruimte.
1. Selecteer **Geo-herstel** in het menu links en selecteer **Koppelen initiëren** op de werkbalk. 

    :::image type="content" source="./media/event-hubs-geo-dr/primary-namspace-initiate-pairing-button.png" alt-text="Koppelen starten vanuit de primaire naamruimte":::    
1. Volg deze **stappen op de** pagina Koppelen initiëren:
    1. Selecteer een bestaande secundaire naamruimte of maak er een in een andere regio. In dit voorbeeld wordt een bestaande naamruimte geselecteerd.  
    1. Voer **voor Alias** een alias in voor de geo-dr-koppeling. 
    1. Ten slotte selecteert u **Create**. 

    :::image type="content" source="./media/event-hubs-geo-dr/initiate-pairing-page.png" alt-text="Selecteer de secundaire naamruimte":::        
1. De pagina **Geo-DR Alias wordt** weergegeven. U kunt ook naar deze pagina navigeren vanuit de primaire naamruimte door **Geo-herstel te selecteren** in het menu links.

    :::image type="content" source="./media/event-hubs-geo-dr/geo-dr-alias-page.png" alt-text="Pagina Geo-DR-alias":::    
1. Selecteer op **de pagina Geo-DR Alias** de optie Beleid voor gedeelde toegang in het menu links voor toegang tot de primaire connection string voor de alias.  Gebruik deze connection string in plaats van de connection string rechtstreeks naar de primaire/secundaire naamruimte te gaan. 
1. Op deze **pagina** Overzicht kunt u de volgende acties uitvoeren: 
    1. Verbreed de koppeling tussen primaire en secundaire naamruimten. Selecteer **Break pairing op** de werkbalk. 
    1. Handmatige failover naar de secundaire naamruimte. Selecteer **Failover op** de werkbalk. 
    
        > [!WARNING]
        > Als u een andere fout maakt, wordt de secundaire naamruimte geactiveerd en wordt de primaire naamruimte verwijderd uit Geo-Disaster Recovery-koppeling. Maak een andere naamruimte om een nieuw geo-noodherstelpaar te hebben. 

Ten slotte moet u bewaking toevoegen om te detecteren of een failover nodig is. In de meeste gevallen maakt de service deel uit van een groot ecosysteem, waardoor automatische failovers zelden mogelijk zijn, omdat failovers vaak synchroon moeten worden uitgevoerd met het resterende subsysteem of de resterende infrastructuur.

### <a name="example"></a>Voorbeeld

In een voorbeeld van dit scenario kunt u een POS-oplossing (Point of Sale) gebruiken om berichten of gebeurtenissen te verzenden. Event Hubs gebeurtenissen worden doorgestuurd naar een toewijzings- of herformatteringsoplossing, die vervolgens toegewezen gegevens doorstuurt naar een ander systeem voor verdere verwerking. Op dat moment worden al deze systemen mogelijk gehost in dezelfde Azure-regio. De beslissing wanneer en welk onderdeel van de fail over moet worden genomen, is afhankelijk van de gegevensstroom in uw infrastructuur. 

U kunt failover automatiseren met bewakingssystemen of met aangepaste bewakingsoplossingen. Voor dergelijke automatisering is echter extra planning en werk nodig, wat buiten het bereik van dit artikel valt.

### <a name="failover-flow"></a>Failoverstroom

Als u de failover start, zijn er twee stappen vereist:

1. Als er een andere storing optreedt, wilt u een fail over kunnen doen. Stel daarom een andere passieve naamruimte in en werk de koppeling bij. 

2. Haal berichten op uit de voormalige primaire naamruimte zodra deze weer beschikbaar is. Daarna gebruikt u die naamruimte voor reguliere berichten buiten uw geo-herstelconfiguratie of verwijdert u de oude primaire naamruimte.

> [!NOTE]
> Alleen fail forward-semantiek wordt ondersteund. In dit scenario kunt u een fail over en vervolgens opnieuw koppelen met een nieuwe naamruimte. Een mislukte back wordt niet ondersteund; bijvoorbeeld in een SQL-cluster. 

![2][]

## <a name="management"></a>Beheer

Als u een fout hebt gemaakt; Als u bijvoorbeeld de verkeerde regio's hebt gekoppeld tijdens de eerste installatie, kunt u het koppelen van de twee naamruimten op elk moment breken. Als u de gekoppelde naamruimten als gewone naamruimten wilt gebruiken, verwijdert u de alias.

## <a name="samples"></a>Voorbeelden

In [het voorbeeld op GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) ziet u hoe u een failover kunt instellen en initiëren. In dit voorbeeld worden de volgende concepten gedemonstreerd:

- Instellingen die zijn vereist in Azure Active Directory voor het gebruik van Azure Resource Manager met Event Hubs. 
- Stappen die nodig zijn om de voorbeeldcode uit te voeren. 
- Verzenden en ontvangen van de huidige primaire naamruimte. 

## <a name="considerations"></a>Overwegingen

Houd rekening met de volgende overwegingen om rekening mee te houden:

1. Met geo-noodherstel Event Hubs geen gegevens gerepliceerd. Daarom kunt u de oude offsetwaarde van uw primaire Event Hub niet opnieuw gebruiken op uw secundaire Event Hub. U wordt aangeraden de gebeurtenisontvanger opnieuw op te starten met een van de volgende methoden:

   - *EventPosition.FromStart() :* als u alle gegevens op uw secundaire Event Hub wilt lezen.
   - *EventPosition.FromEnd() :* als u alle nieuwe gegevens wilt lezen vanaf het tijdstip van de verbinding met uw secundaire Event Hub.
   - *EventPosition.FromEnqueuedTime(dateTime)* : als u alle gegevens wilt lezen die in uw secundaire Event Hub zijn ontvangen vanaf een bepaalde datum en tijd.

2. In uw failover-planning moet u ook rekening houden met de tijdsfactor. Als u bijvoorbeeld de verbinding langer dan 15 tot 20 minuten verliest, kunt u besluiten om de failover te starten. 
 
3. Het feit dat er geen gegevens worden gerepliceerd, betekent dat de huidige actieve sessies niet worden gerepliceerd. Bovendien werken detectie van duplicaten en geplande berichten mogelijk niet. Nieuwe sessies, geplande berichten en nieuwe duplicaten werken. 

4. Als u een complexe gedistribueerde infrastructuur niet goed doorstaat, [moet deze](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) ten minste één keer worden geseed. 

5. Het synchroniseren van entiteiten kan enige tijd duren, ongeveer 50-100 entiteiten per minuut.

## <a name="availability-zones"></a>Beschikbaarheidszones 

De Event Hubs Standard-SKU ondersteunt [Beschikbaarheidszones](../availability-zones/az-overview.md)en biedt foutgeisoleerde locaties binnen een Azure-regio. 

> [!NOTE]
> De Beschikbaarheidszones voor Azure Event Hubs Standard is alleen beschikbaar in [Azure-regio's](../availability-zones/az-region.md) waar beschikbaarheidszones aanwezig zijn.

U kunt de Beschikbaarheidszones alleen voor nieuwe naamruimten inschakelen met behulp van de Azure Portal. Event Hubs biedt geen ondersteuning voor de migratie van bestaande naamruimten. U kunt zone-redundantie niet uitschakelen nadat u deze hebt inschakelen in uw naamruimte.

Wanneer u beschikbaarheidszones gebruikt, worden metagegevens en gegevens (gebeurtenissen) gerepliceerd in datacenters in de beschikbaarheidszone. 

![3][]

## <a name="private-endpoints"></a>Privé-eindpunten
Deze sectie bevat meer overwegingen bij het gebruik van geo-noodherstel met naamruimten die gebruikmaken van privé-eindpunten. Zie Privé-eindpunten configureren voor meer informatie Event Hubs het gebruik van [privé-eindpunten met een](private-link-service.md)Event Hubs in het algemeen.

### <a name="new-pairings"></a>Nieuwe koppelen
Als u een koppeling probeert te maken tussen een primaire naamruimte met een privé-eindpunt en een secundaire naamruimte zonder een privé-eindpunt, mislukt het koppelen. Het koppelen slaagt alleen als zowel primaire als secundaire naamruimten privé-eindpunten hebben. U wordt aangeraden dezelfde configuraties te gebruiken in de primaire en secundaire naamruimten en in virtuele netwerken waarin privé-eindpunten worden gemaakt.  

> [!NOTE]
> Wanneer u de primaire naamruimte probeert te koppelen aan een privé-eindpunt en een secundaire naamruimte, controleert het validatieproces alleen of er een privé-eindpunt bestaat in de secundaire naamruimte. Er wordt niet gekeken of het eindpunt werkt of werkt na een failover. Het is uw verantwoordelijkheid om ervoor te zorgen dat de secundaire naamruimte met een privé-eindpunt werkt zoals verwacht na een failover.
>
> Als u wilt testen of de configuraties van de privé-eindpunten op primaire en secundaire naamruimten hetzelfde zijn, verzendt u een leesaanvraag (bijvoorbeeld Event [Hub](/rest/api/eventhub/get-event-hub)get ) naar de secundaire naamruimte van buiten het virtuele netwerk en controleert u of u een foutbericht van de service ontvangt.

### <a name="existing-pairings"></a>Bestaande paren
Als het koppelen tussen de primaire en secundaire naamruimte al bestaat, mislukt het maken van een privé-eindpunt in de primaire naamruimte. Als u dit wilt oplossen, maakt u eerst een privé-eindpunt op de secundaire naamruimte en maakt u er vervolgens een voor de primaire naamruimte.

> [!NOTE]
> Hoewel alleen-lezentoegang tot de secundaire naamruimte is toegestaan, zijn updates van de configuraties van het privé-eindpunt toegestaan. 

### <a name="recommended-configuration"></a>Aanbevolen configuratie
Wanneer u een configuratie voor herstel na noodherstel maakt voor uw toepassing en Event Hubs-naamruimten, moet u privé-eindpunten maken voor zowel primaire als secundaire Event Hubs-naamruimten voor virtuele netwerken die als host dienen voor zowel primaire als secundaire exemplaren van uw toepassing. 

Stel dat u twee virtuele netwerken hebt: VNET-1, VNET-2 en deze primaire en secundaire naamruimten: EventHubs-Namespace1-Primary, EventHubs-Namespace2-Secondary. U moet de volgende stappen volgen: 

- Maak op EventHubs-Namespace1-Primary twee privé-eindpunten die gebruikmaken van subnetten van VNET-1 en VNET-2
- Maak op EventHubs-Namespace2-Secondary twee privé-eindpunten die gebruikmaken van dezelfde subnetten van VNET-1 en VNET-2 

![Privé-eindpunten en virtuele netwerken](./media/event-hubs-geo-dr/private-endpoints-virtual-networks.png)

Het voordeel van deze benadering is dat failover kan plaatsvinden op de toepassingslaag, onafhankelijk van Event Hubs naamruimte. Denk eens na over de volgende scenario's: 

**Failover alleen voor toepassingen:** Hier bestaat de toepassing niet in VNET-1, maar wordt deze verplaatst naar VNET-2. Omdat beide privé-eindpunten zijn geconfigureerd op zowel VNET-1 als VNET-2 voor zowel primaire als secundaire naamruimten, werkt de toepassing gewoon. 

**Event Hubs alleen-naamruimte-failover:** hier opnieuw, omdat beide privé-eindpunten zijn geconfigureerd op beide virtuele netwerken voor zowel primaire als secundaire naamruimten, werkt de toepassing gewoon. 

> [!NOTE]
> Zie Virtual Network [- Bedrijfscontinuïteit voor](../virtual-network/virtual-network-disaster-recovery-guidance.md)hulp bij geo-noodherstel van een virtueel netwerk.
 
## <a name="next-steps"></a>Volgende stappen
Bekijk de volgende voorbeelden of referentiedocumentatie. 
- [.NET GeoDR-voorbeeld](https://github.com/Azure/azure-event-hubs/tree/master/samples/Management/DotNet/GeoDRClient) 
- [Java GeoDR-voorbeeld](https://github.com/Azure-Samples/eventhub-java-manage-event-hub-geo-disaster-recovery)
- [.NET- Voorbeelden van Azure.Messaging.EventHubs](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples)
- [.NET- Voorbeelden van Microsoft.Azure.EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet)
- [Java - voorbeelden van azure-messaging-eventhubs](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)
- [Java - azure-eventhubs-voorbeelden](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java)
- [Python-voorbeelden](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples)
- [JavaScript-voorbeelden](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript)
- [TypeScript-voorbeelden](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
- [Naslaginformatie over REST API](/rest/api/eventhub/)

[1]: ./media/event-hubs-geo-dr/geo1.png
[2]: ./media/event-hubs-geo-dr/geo2.png
[3]: ./media/event-hubs-geo-dr/eh-az.png

---
title: Azure Service Bus toepassingen isoleren tegen storingen en rampen
description: In deze artikelen vindt u technieken voor het beveiligen van toepassingen tegen een mogelijke Azure Service Bus storing.
ms.topic: article
ms.date: 02/10/2021
ms.openlocfilehash: b9090a54cd58788dbd13f528af4dda4aa96005b7
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100374589"
---
# <a name="best-practices-for-insulating-applications-against-service-bus-outages-and-disasters"></a>Best practices voor de isolatie van toepassingen tegen Service Bus-uitval en -noodgevallen

Essentiële toepassingen moeten voortdurend worden gebruikt, zelfs in aanwezigheid van ongeplande storingen of rampen. In dit artikel worden technieken beschreven die u kunt gebruiken om Service Bus-toepassingen te beschermen tegen een mogelijke service storing of nood geval.

Een storing wordt gedefinieerd als de tijdelijke niet-beschik baarheid van Azure Service Bus. De storing kan invloed hebben op sommige onderdelen van Service Bus, zoals een berichten archief of zelfs het hele Data Center. Nadat het probleem is opgelost, wordt Service Bus weer beschikbaar. Normaal gesp roken veroorzaakt een storing geen verlies van berichten of andere gegevens. Een voor beeld van een onderdeel fout is de niet-beschik baarheid van een bepaalde berichten opslag. Een voor beeld van een storing in het Data Center is een stroom storing in het Data Center of een defecte datacenter netwerk switch. Een onderbreking kan een paar minuten tot enkele dagen duren.

Een nood geval wordt gedefinieerd als permanent verlies van een Service Bus Scale-eenheid of Data Center. Het Data Center kan al dan niet meer beschikbaar zijn. Normaal gesp roken leidt een nood geval ertoe dat sommige of alle berichten of andere gegevens verloren gaan. Voor beelden van calamiteiten zijn brand, overstroming of aard beving.

## <a name="protecting-against-outages-and-disasters---service-bus-premium"></a>Bescherming tegen storingen en rampen-Service Bus Premium
Concepten voor hoge Beschik baarheid en herstel na nood gevallen zijn ingebouwd in de Azure Service Bus Premium-laag, zowel binnen dezelfde regio (via Beschikbaarheidszones) als in verschillende regio's (via Geo-Disaster Recovery).

### <a name="geo-disaster-recovery"></a>Geo-Disaster herstel

Service Bus Premium ondersteunt geo-nood herstel op het niveau van de naam ruimte. Zie [Azure Service Bus Geo-disaster recovery](service-bus-geo-dr.md) (Geo-herstel na noodgeval in Azure Service Bus) voor meer informatie. De functie voor nood herstel, alleen beschikbaar voor de [Premium-SKU](service-bus-premium-messaging.md) , implementeert herstel na nood gevallen van meta gegevens en maakt gebruik van primaire en secundaire naam ruimten voor nood herstel. Met Geo-Disaster herstel worden alleen meta gegevens voor entiteiten gerepliceerd tussen de primaire en secundaire naam ruimten.  

### <a name="availability-zones"></a>Beschikbaarheidszones

De Service Bus Premium SKU ondersteunt [Beschikbaarheidszones](../availability-zones/az-overview.md), waardoor er in dezelfde Azure-regio fout geïsoleerde locaties worden geboden. Service Bus beheert drie kopieën van het berichten archief (1 primaire en 2 secundaire). Service Bus houdt alle drie kopieën synchroon voor gegevens-en beheer bewerkingen. Als de primaire kopie mislukt, wordt een van de secundaire kopieën gepromoveerd tot primair zonder waargenomen uitval tijd. Als de toepassingen tijdelijke verbreken van Service Bus zien, wordt met de logica voor opnieuw proberen in de SDK automatisch opnieuw verbinding gemaakt met Service Bus. 

Wanneer u beschikbaarheids zones gebruikt, worden zowel meta gegevens als gegevens (berichten) gerepliceerd tussen data centers in de beschikbaarheids zone. 

> [!NOTE]
> De Beschikbaarheidszones ondersteuning voor Azure Service Bus Premium is alleen beschikbaar in [Azure-regio's](../availability-zones/az-region.md) waar beschikbaarheids zones aanwezig zijn.

U kunt Beschikbaarheidszones alleen inschakelen voor nieuwe naam ruimten, met behulp van de Azure Portal. Service Bus biedt geen ondersteuning voor de migratie van bestaande naam ruimten. U kunt zone redundantie niet uitschakelen nadat u deze in uw naam ruimte hebt ingeschakeld.

![1][]


## <a name="protecting-against-outages-and-disasters---service-bus-standard"></a>Bescherming tegen storingen en rampen-Service Bus Standard
Service Bus ondersteunt twee benaderingen: *actieve* en *passieve* replicatie om de flexibiliteit te verzorgen tegen Data Center-uitval bij het gebruik van de Standard-prijs categorie voor berichten. Als een bepaalde wachtrij of onderwerp voor elke benadering toegankelijk moet blijven bij aanwezigheid van een storing in het Data Center, kunt u deze in beide naam ruimten maken. Beide entiteiten kunnen dezelfde naam hebben. Een primaire wachtrij kan bijvoorbeeld worden bereikt onder **contosoPrimary.servicebus.Windows.net/myQueue**, terwijl de secundaire tegen hanger kan worden bereikt onder **contosoSecondary.servicebus.Windows.net/myQueue**.

>[!NOTE]
> De **actieve replicatie** -en **passieve replicatie** -instellingen zijn oplossingen voor algemeen gebruik en geen specifieke functies van service bus. De replicatie logica (verzenden naar twee verschillende naam ruimten) bevindt zich in de toepassingen van de afzender en de ontvanger moet aangepaste logica hebben voor duplicaten detectie.

Als voor de toepassing geen permanente communicatie van sender-to-receiver is vereist, kan de toepassing een duurzame wachtrij voor client zijde implementeren om verlies van berichten te voor komen en de afzender af te schermn van tijdelijke Service Bus fouten.

### <a name="active-replication"></a>Actieve replicatie
Actieve replicatie maakt gebruik van entiteiten in beide naam ruimten voor elke bewerking. Elke client die een bericht verzendt, verzendt twee kopieën van hetzelfde bericht. De eerste kopie wordt verzonden naar de primaire entiteit (bijvoorbeeld **contosoPrimary.servicebus.Windows.net/Sales**) en de tweede kopie van het bericht wordt verzonden naar de secundaire entiteit (bijvoorbeeld **contosoSecondary.servicebus.Windows.net/Sales**).

Een client ontvangt berichten van beide wacht rijen. De ontvanger verwerkt de eerste kopie van een bericht en de tweede kopie wordt onderdrukt. Om dubbele berichten te onderdrukken, moet de afzender elk bericht labelen met een unieke id. Beide exemplaren van het bericht moeten zijn gelabeld met dezelfde id. U kunt de eigenschappen [BrokeredMessage. MessageId][BrokeredMessage.MessageId] of [BrokeredMessage. label][BrokeredMessage.Label] of een aangepaste eigenschap gebruiken om het bericht te labelen. De ontvanger moet een lijst met berichten onderhouden die deze al heeft ontvangen.

De [geo-replicatie met Service Bus Standard-laag][Geo-replication with Service Bus Standard Tier] voor beeld toont actieve replicatie van Messa ging-entiteiten.

> [!NOTE]
> De actieve replicatie aanpak verdubbelt het aantal bewerkingen, waardoor deze benadering kan leiden tot hogere kosten.
> 
> 

### <a name="passive-replication"></a>Passieve replicatie
In het geval van een niet-beschik bare fout gebruikt passieve replicatie slechts één van de twee Messa ging-entiteiten. Een client verzendt het bericht naar de actieve entiteit. Als de bewerking op de actieve entiteit mislukt met een fout code die aangeeft dat het Data Center dat als host fungeert voor de actieve entiteit mogelijk niet beschikbaar is, stuurt de client een kopie van het bericht naar de back-upentiteit. Op dat moment worden de rollen actief en back-upentiteiten omgewisseld: de verzendende client beschouwt de oude actieve entiteit als nieuwe back-upentiteit en de oude back-upentiteit is de nieuwe actieve entiteit. Als beide verzend bewerkingen mislukken, blijven de rollen van de twee entiteiten ongewijzigd en wordt er een fout geretourneerd.

Een client ontvangt berichten van beide wacht rijen. Omdat er een kans is dat de ontvanger twee exemplaren van hetzelfde bericht ontvangt, moet de ontvanger dubbele berichten onderdrukken. U kunt dubbele waarden op dezelfde manier onderdrukken als voor actieve replicatie.

Over het algemeen is passieve replicatie rendabeler dan actieve replicatie, omdat in de meeste gevallen slechts één bewerking wordt uitgevoerd. De latentie, door Voer en de monetaire kosten zijn identiek aan het niet-gerepliceerde scenario.

Bij het gebruik van passieve replicatie kunnen berichten in de volgende scenario's worden verloren of twee keer worden ontvangen:

* **Bericht vertraging of-verlies**: aangenomen dat de afzender een bericht B1 heeft verzonden naar de primaire wachtrij. vervolgens wordt de wachtrij pas weer beschikbaar als de ontvanger M1 ontvangt. De afzender verzendt een volgend bericht m2 naar de secundaire wachtrij. Als de primaire wachtrij tijdelijk niet beschikbaar is, wordt de ontvanger M1 ontvangen nadat de wachtrij weer beschikbaar is. In het geval van een ramp mag de ontvanger nooit M1 ontvangen.
* **Dubbele ontvangst**: Stel dat de afzender een bericht m naar de primaire wachtrij verzendt. Service Bus verwerkt m, maar er is geen antwoord verzonden. Als er een time-out optreedt voor de verzend bewerking, verzendt de afzender een identieke kopie van m naar de secundaire wachtrij. Als de ontvanger de eerste kopie van m kan ontvangen voordat de primaire wachtrij niet beschikbaar is, ontvangt de ontvanger beide exemplaren van de m ongeveer hetzelfde tijdstip. Als de ontvanger niet in staat is om de eerste kopie van m te ontvangen voordat de primaire wachtrij niet meer beschikbaar is, ontvangt de ontvanger aanvankelijk alleen de tweede kopie van m, maar ontvangt hij een tweede exemplaar van m wanneer de primaire wachtrij beschikbaar wordt.

De [geo-replicatie met Service Bus Standard-laag][Geo-replication with Service Bus Standard Tier] voor beeld toont de passieve replicatie van Messa ging-entiteiten.

## <a name="protecting-relay-endpoints-against-datacenter-outages-or-disasters"></a>Relay-eind punten beveiligen tegen data centers storingen of nood gevallen
Met geo-replicatie van [Azure relay](../azure-relay/relay-what-is-it.md) -eind punten kan een service die een relay-eind punt beschikbaar maakt, bereikbaar zijn in de aanwezigheid van service bus storingen. Om geo-replicatie te kunnen garanderen, moet de service twee relay-eind punten in verschillende naam ruimten maken. De naam ruimten moeten zich in verschillende data centers bevinden en de twee eind punten moeten verschillende namen hebben. U kunt bijvoorbeeld een primair eind punt bereiken onder **contosoPrimary.servicebus.Windows.net/myPrimaryService**, terwijl de secundaire tegen hanger kan worden bereikt onder **contosoSecondary.servicebus.Windows.net/mySecondaryService**.

De service luistert vervolgens op beide eind punten en een client kan de service via een van beide eind punten aanroepen. Een client toepassing kiest wille keurig een van de relays als het primaire eind punt en verzendt de aanvraag naar het actieve eind punt. Als de bewerking mislukt met een fout code, geeft deze fout aan dat het relay-eind punt niet beschikbaar is. De toepassing opent een kanaal naar het back-upeindpunt en geeft de aanvraag opnieuw uit. Op dat moment worden de switch rollen actief en back-upeindpunt: de client toepassing beschouwt het oude actieve eind punt als het nieuwe back-upeindpunt en het oude back-upeindpunt als nieuw actief eind punt. Als beide verzend bewerkingen mislukken, blijven de rollen van de twee entiteiten ongewijzigd en wordt er een fout geretourneerd.

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen voor meer informatie over herstel na nood gevallen:

* [Azure Service Bus geo-nood herstel](service-bus-geo-dr.md)
* [Bedrijfs continuïteit Azure SQL Database][Azure SQL Database Business Continuity]
* [Tolerante toepassingen ontwerpen voor Azure][Azure resiliency technical guidance]

[Service Bus Authentication]: service-bus-authentication-and-authorization.md
[Partitioned messaging entities]: service-bus-partitioning.md
[Asynchronous messaging patterns and high availability]: service-bus-async-messaging.md#failure-of-service-bus-within-an-azure-datacenter
[BrokeredMessage.MessageId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[BrokeredMessage.Label]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Geo-replication with Service Bus Standard Tier]: https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoReplication
[Azure SQL Database Business Continuity]:../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md
[Azure resiliency technical guidance]: /azure/architecture/resiliency

[1]: ./media/service-bus-outages-disasters/az.png
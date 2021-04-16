---
title: Azure Service Bus Premium- en Standard-lagen
description: In dit artikel worden de Standard- en Premium-lagen van Azure Service Bus. Vergelijkt deze lagen en biedt technische verschillen.
ms.topic: conceptual
ms.date: 02/17/2021
ms.openlocfilehash: b7117da6a959181704dd136c6d5be5ab62edef55
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389482"
---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>Prijscategorieën voor Service Bus Premium en Standard Messaging

Service Bus Messaging, dat entiteiten zoals wachtrijen en onderwerpen omvat, combineert functies voor bedrijfsberichten met krachtige semantiek voor publiceren/abonneren in de cloud. Service Bus Messaging wordt gebruikt als de communicatie-backbone voor veel geavanceerde cloudoplossingen.

De *Premium*-laag van de Service Bus Messaging-service zorgt voor de afhandeling van algemene klantaanvragen met betrekking tot de schaal, prestaties en beschikbaarheid van essentiële toepassingen. Voor productiescenario's wordt de laag Premium aanbevolen. Hoewel de functiesets bijna identiek zijn, zijn deze twee lagen van de Service Bus Messaging-service ontworpen voor verschillende gebruiksscenario’s.

In de volgende tabel worden enkele belangrijke verschillen uitgelicht.

| Premium | Standard |
| --- | --- |
| Hoge doorvoersnelheid |Variabele doorvoersnelheid |
| Voorspelbare prestaties |Variabele latentie |
| Vaste prijzen |Variabel omslagstelsel voor betalen per gebruik |
| Mogelijkheid om de workload omhoog en omlaag te schalen |N.v.t. |
| Berichtgrootte tot 1 MB. Deze limiet kan in de toekomst worden verhoogd. Zie Messaging on Azure blog (Berichten op [Azure-blog) voor de](https://techcommunity.microsoft.com/t5/messaging-on-azure/bg-p/MessagingonAzureBlog)meest recente belangrijke updates voor de service. |Berichtformaat tot maximaal 256 kB |

**Service Bus Premium Messaging** biedt isolatie van resources op het niveau van de CPU en het geheugen, zodat elke workload van een klant geïsoleerd wordt uitgevoerd. Deze resourcecontainer wordt een *messaging-eenheid genoemd.* Aan elke Premium-naamruimte wordt ten minste één Messaging-eenheid toegewezen. U kunt 1, 2, 4, 8 of 16 berichteneenheden kopen voor elke Service Bus Premium-naamruimte. Eén workload of entiteit kan meerdere berichteenheden overspannen en het aantal berichteneenheden kan naar eigen goedheid worden gewijzigd. Dit resulteert in voorspelbare en herhaalbare prestaties voor uw Service Bus-oplossing.

Niet alleen zijn de prestaties beter voorspelbaar en beschikbaar, ze zijn ook sneller. Met de Premium-laag zijn de piekprestaties veel sneller dan met de Standard-laag.

> [!NOTE]
> De batchgroottelimiet voor Premium Messaging is 1 MB.

## <a name="premium-messaging-technical-differences"></a>Technische verschillen Premium Messaging

In de volgende secties wordt een aantal verschillen besproken tussen Premium en Standard Messaging.

### <a name="partitioned-queues-and-topics"></a>Gepartitioneerde wachtrijen en onderwerpen

Gepartitiede wachtrijen en onderwerpen worden niet ondersteund in Premium Messaging. Zie [Gepartitioneerde wachtrijen en onderwerpen](service-bus-partitioning.md) voor meer informatie over partitioneren.

### <a name="express-entities"></a>Express-entiteiten

Omdat Premium Messaging wordt uitgevoerd in een geïsoleerde run time-omgeving, worden express-entiteiten niet ondersteund in Premium-naamruimten. Een express-entiteit bevat een bericht tijdelijk in het geheugen voordat het naar de permanente opslag wordt geschreven. Als u code hebt die wordt uitgevoerd onder Standard Messaging en u deze wilt over dragen naar de Premium-laag, moet u ervoor zorgen dat de express-entiteitsfunctie is uitgeschakeld.

## <a name="premium-messaging-resource-usage"></a>Resourcegebruik voor Premium Messaging
Over het algemeen kan elke bewerking op een entiteit cpu- en geheugengebruik veroorzaken. Hier zijn enkele van deze bewerkingen: 

- Beheerbewerkingen zoals CRUD-bewerkingen (Maken, Ophalen, Bijwerken en Verwijderen) voor wachtrijen, onderwerpen en abonnementen.
- Runtimebewerkingen (berichten verzenden en ontvangen)
- Bewerkingen en waarschuwingen bewaken

Het extra CPU- en geheugengebruik is echter niet extra geprijsd. Voor de Premium Messaging-laag is er één prijs voor de berichteenheid.

Het CPU- en geheugengebruik worden om de volgende redenen bij u bijgespoord en weergegeven: 

- Transparantie bieden in de interne gegevens van het systeem
- Inzicht in de capaciteit van aangeschafte resources.
- Capaciteitsplanning die u helpt om omhoog/omlaag te schalen.

## <a name="messaging-unit---how-many-are-needed"></a>Messaging-eenheid: hoeveel zijn er nodig?

Bij het inrichten van Azure Service Bus Premium-naamruimte moet het aantal toegewezen berichteneenheden worden opgegeven. Deze berichteneenheden zijn toegewezen resources die worden toegewezen aan de naamruimte.

Het aantal berichteneenheden dat is toegewezen aan de Service Bus  Premium-naamruimte kan dynamisch worden aangepast om rekening te houden met de wijziging (toename of afname) van workloads.

Er zijn een aantal factoren om rekening mee te houden bij het bepalen van het aantal berichteneenheden voor uw architectuur:

- Begin met ***1 of 2 berichteneenheden die zijn*** toegewezen aan uw naamruimte.
- Bestudeer de metrische gegevens over CPU-gebruik in [de metrische gegevens over resourcegebruik](service-bus-metrics-azure-monitor.md#resource-usage-metrics) voor uw naamruimte.
    - Als het CPU-gebruik * lager is dan **20%** _, kunt u *_mogelijk_* _ omlaag schalen * het aantal berichteneenheden dat is toegewezen aan uw naamruimte.
    - Als het CPU-gebruik hoger is dan **70%** _, profiteert uw toepassing van *___* omhoog schalen * het aantal berichteneenheden dat is toegewezen aan uw naamruimte.

Zie Berichteneenheden automatisch bijwerken voor meer informatie over het configureren van een Service Bus-naamruimte om automatisch te schalen (messaging-eenheden vergroten of [verlagen).](automate-update-messaging-units.md)

> [!NOTE]
> **Het schalen** van de resources die aan de naamruimte zijn toegewezen, kan preventief of reactief zijn.
>
>  * **Preventief:** als er een extra workload wordt verwacht (vanwege seizoensgebondenheid of trends), kunt u doorgaan met het toewijzen van meer berichteneenheden aan de naamruimte voordat de workloads worden getroffen.
>
>  * **Reactieve:** als extra workloads worden geïdentificeerd door de metrische gegevens over resourcegebruik te bestuderen, kunnen er extra resources worden toegewezen aan de naamruimte om de toenemende vraag op te nemen.
>
> De factureringsmeters voor Service Bus zijn per uur. In het geval van omhoog schalen betaalt u alleen voor de extra resources voor de uren dat deze zijn gebruikt.
>

## <a name="get-started-with-premium-messaging"></a>Aan de slag met Premium Messaging

U kunt snel aan de slag met Premium Messaging. Het proces is vergelijkbaar met dat van Standard Messaging. [Maak eerst een naamruimte](service-bus-create-namespace-portal.md) in [Azure Portal](https://portal.azure.com). Zorg ervoor dat bij **Prijscategorie** de optie **Premium** is geselecteerd. Klik op **Volledige prijsgegevens weergeven** voor meer informatie over de verschillende lagen.

![maken-premium-naamruimte][create-premium-namespace]

U kunt ook [Premium-naamruimtes maken met behulp van Azure Resource Manager-sjablonen](https://azure.microsoft.com/resources/templates/101-servicebus-pn-ar/).

## <a name="next-steps"></a>Volgende stappen

Zie de volgende koppelingen voor meer informatie over de Service Bus Messaging-service:

- [Berichten-eenheden automatisch bijwerken.](automate-update-messaging-units.md)
- [Introductie van Azure Service Bus Premium Messaging (blogpost)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
- [Introducing Azure Service Bus Premium messaging (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)

<!--Image references-->

[create-premium-namespace]: ./media/service-bus-premium-messaging/select-premium-tier.png

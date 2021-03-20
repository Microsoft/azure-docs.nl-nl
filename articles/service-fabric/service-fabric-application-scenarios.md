---
title: Toepassings scenario's en-ontwerp
description: Overzicht van categorieën Cloud toepassingen in Service Fabric. Beschrijft een toepassings ontwerp dat gebruikmaakt van stateful en stateless Services.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 6c3cc931a85b91fc02b8086ca5c2481153691e54
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96575666"
---
# <a name="service-fabric-application-scenarios"></a>Toepassings scenario's Service Fabric

Azure Service Fabric biedt een betrouwbaar en flexibel platform waar u veel soorten bedrijfs toepassingen en-services kunt schrijven en uitvoeren. Deze toepassingen en micro Services kunnen stateless of stateful zijn, en de resources worden verdeeld over virtuele machines om de efficiëntie te maximaliseren.

Met de unieke architectuur van Service Fabric kunt u bijna realtime gegevens analyses uitvoeren, berekeningen in het geheugen, parallelle trans acties en gebeurtenis verwerking in uw toepassingen. U kunt uw toepassingen eenvoudig in-of uitschalen, afhankelijk van de veranderende resource vereisten.

Lees de [architectuur micro Services in Azure service Fabric](/azure/architecture/reference-architectures/microservices/service-fabric) en [Aanbevolen procedures voor het ontwerpen van toepassingen met behulp van service Fabric](service-fabric-best-practices-applications.md)voor ontwerp richtlijnen voor het bouwen van apps.

Overweeg het gebruik van het Service Fabric platform voor de volgende typen toepassingen:

* **Gegevens verzameling, verwerking en IOT**: Service Fabric verwerkt grote schaal en heeft een lage latentie door de stateful Services. Hiermee kunnen gegevens worden verwerkt op miljoenen apparaten waar de gegevens voor het apparaat en de berekening worden geplaatst.

    Klanten die IoT-Services hebben gebouwd met behulp van Service Fabric, zijn [Honeywell](https://customers.microsoft.com/story/honeywell-builds-microservices-based-thermostats-on-azure), [PCL-constructie](https://customers.microsoft.com/story/pcl-construction-professional-services-azure), [Crestron](https://customers.microsoft.com/story/crestron-partner-professional-services-azure),  [BMW](https://customers.microsoft.com/story/bmw-enables-driver-mobility-via-azure-service-fabric/), [Schneidere Electric](https://customers.microsoft.com/story/schneider-electric-powers-engergy-solutions-on-azure-service-fabric)en [net Systems](https://customers.microsoft.com/story/mesh-systems-lights-up-the-market-with-iot-based-azure-solutions).

* **Interactieve toepassingen op het niveau van games en sessies**: Service Fabric is handig als uw toepassing Lees-en schrijf bewerkingen met een lage latentie vereist, zoals in online gaming of chat berichten. Met Service Fabric kunt u deze interactieve, stateful toepassingen bouwen zonder dat u een afzonderlijke Store of cache hoeft te maken. Ga naar [Azure gaming oplossingen](https://azure.microsoft.com/solutions/gaming/) voor ontwerp richtlijnen over het [gebruik van service fabric in gaming services](/gaming/azure/reference-architectures/multiplayer-synchronous-sf).

    Klanten die gaming-services hebben gebouwd, zijn [volgende games](https://customers.microsoft.com/story/next-games-media-telecommunications-azure) en [Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/). Klanten die interactieve sessies hebben gebouwd, zijn [Honeywell met Hololens](https://customers.microsoft.com/story/honeywell-manufacturing-hololens).

* **Gegevens analyse en werk stroom verwerking**: toepassingen die op betrouw bare wijze gebeurtenissen of gegevens stromen moeten verwerken van de geoptimaliseerde Lees-en schrijf bewerkingen in service Fabric. Service Fabric ondersteunt ook pijp lijnen voor toepassings verwerking, waarbij resultaten betrouwbaar moeten zijn en moeten worden door gegeven aan de volgende verwerkings fase zonder verlies. Deze pijp lijnen bevatten transactionele en financiële systemen, waarbij de consistentie van gegevens en de berekenings garanties essentieel zijn.

    Klanten die zakelijke werk stroom Services hebben gebouwd, zijn onder andere [Zeiss Group](https://customers.microsoft.com/story/zeiss-group-focuses-on-azure-service-fabric-for-key-integration-platform), [quorum Business Solutions](https://customers.microsoft.com/en-us/story/quorum-business-solutions-expand-energy-managemant-solutions-using-azure-service-fabric)en [Société General](https://customers.microsoft.com/en-us/story/societe-generale-speeds-real-time-market-quotes-using-azure-service-fabric).

* **Berekening van gegevens**: met Service Fabric kunt u stateful toepassingen bouwen die intensieve gegevens berekening uitvoeren. Service Fabric kunt de verwerkings locatie en gegevens in toepassingen. 

   Normaal gesp roken geldt dat als uw toepassing toegang tot gegevens vereist, de netwerk latentie die aan een externe gegevens cache of opslaglaag is gekoppeld, de tijd van de berekening beperkt. Stateful Service Fabric services elimineren die latentie, waardoor er meer geoptimaliseerde Lees-en schrijf bewerkingen mogelijk zijn.

   Denk bijvoorbeeld aan een toepassing die bijna in realtime aanbevolen selecties voor klanten uitvoert, met een vereiste voor een round-trip tijd van minder dan 100 milliseconden. De latentie-en prestatie kenmerken van Service Fabric services bieden een reactie op de gebruiker, vergeleken met het standaard implementatie model van om de benodigde gegevens uit de externe opslag op te halen. Het systeem reageert meer omdat de berekening van de aanbevelings selectie is gekoppeld aan de gegevens en regels.

    Klanten die reken Services hebben gebouwd, zijn [Solid Soft reply](https://customers.microsoft.com/story/solidsoft-reply-platform-powers-e-verification-of-pharmaceuticals) en [Infosupport](https://customers.microsoft.com/story/service-fabric-customer-profile-info-support-and-fudura).

* **Maxi maal beschik bare Services**: Service Fabric biedt snelle failover door meerdere secundaire service replica's te maken. Als een knoop punt, proces of afzonderlijke service uitvalt vanwege een hardware-of andere fout, wordt een van de secundaire replica's gepromoveerd tot een primaire replica met mini maal verlies van service.

* **Schaal bare Services**: afzonderlijke services kunnen worden gepartitioneerd, zodat de status van het hele cluster kan worden uitgeschaald. U kunt ook afzonderlijke services maken en verwijderen. U kunt services van slechts enkele exemplaren op een paar knoop punten uitschalen naar duizenden exemplaren op meerdere knoop punten en deze vervolgens naar behoefte schalen. U kunt Service Fabric gebruiken om deze services te bouwen en hun volledige levens cyclus te beheren.

## <a name="application-design-case-studies"></a>Case-study's over toepassings ontwerp

Casestudy's die laten zien hoe Service Fabric wordt gebruikt voor het ontwerpen van toepassingen, worden gepubliceerd op de [klant verhalen](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=2&so=story_publish_date%20desc) en micro [Services in azure](https://azure.microsoft.com/solutions/microservice-applications/) -sites.

## <a name="designing-applications-composed-of-stateless-and-stateful-microservices"></a>Toepassingen ontwerpen die bestaan uit stateless en stateful micro Services

Het maken van toepassingen met Azure Cloud Services worker-werk rollen is een voor beeld van een stateless service. Stateful micro Services behouden daarentegen hun gezaghebbende status naast de aanvraag en de reactie. Deze functionaliteit biedt hoge Beschik baarheid en consistentie van de status via eenvoudige Api's die transactionele garanties bieden die door replicatie worden ondersteund.

Stateful Services in Service Fabric hoge Beschik baarheid bieden voor alle soorten toepassingen, niet alleen voor data bases en andere gegevens archieven. Dit is een natuurlijke voortgang. Toepassingen zijn al verplaatst van het gebruik van louter relationele data bases voor hoge Beschik baarheid tot NoSQL-data bases. Nu kunnen de toepassingen zelf hun ' hot ' status hebben en gegevens die worden beheerd in hun voor extra prestatie voordelen zonder dat er wordt geofferd dat betrouw baarheid, consistentie of Beschik baarheid.

Wanneer u toepassingen bouwt die bestaan uit micro Services, hebt u normaal gesp roken een combi natie van stateless web-apps (zoals ASP.NET en Node.js) die worden aangeroepen op stateless en stateful bedrijfs Services voor de middelste laag. De apps en services worden allemaal geïmplementeerd in hetzelfde Service Fabric cluster via de Service Fabric-implementatie opdrachten. Elk van deze services is onafhankelijk van de schaal, betrouw baarheid en het resource gebruik. Deze onafhankelijkheid verbetert de flexibiliteit en flexibiliteit bij het ontwikkelen en het levenscyclus beheer.

Stateful micro Services is van toepassing op toepassings ontwerpen omdat ze de nood zaak van de extra wacht rijen en caches die traditioneel zijn vereist voor het oplossen van de vereisten voor Beschik baarheid en latentie van louter stateless toepassingen, verwijderen. Omdat stateful Services hoge Beschik baarheid en lage latentie hebben, zijn er minder details die in uw toepassing kunnen worden beheerd.

In de volgende diagrammen ziet u de verschillen tussen het ontwerpen van een toepassing die staatloos is en die de statussen heeft. Door gebruik te maken van de [reliable Services](service-fabric-reliable-services-introduction.md) -en [reliable actors](service-fabric-reliable-actors-introduction.md) -programmeer modellen, kunnen stateful Services de complexiteit van de toepassing beperken tijdens hoge door Voer en lage latentie.

Hier volgt een voor beeld van een toepassing die stateless Services gebruikt: ![ toepassing die stateless Services gebruikt.][Image1]

Hier volgt een voorbeeld toepassing die gebruikmaakt van stateful Services: ![ de toepassing die gebruikmaakt van stateful Services][Image2]

## <a name="next-steps"></a>Volgende stappen

* Ga aan de slag met het bouwen van stateless en stateful Services met de Service Fabric [reliable Services](service-fabric-reliable-services-quick-start.md) en [reliable actors](service-fabric-reliable-actors-get-started.md) programmeer modellen.
* Ga naar de Azure Architecture Center voor hulp bij het bouwen van micro [Services in azure](/azure/architecture/microservices/).
* Ga naar de [Aanbevolen procedures voor Azure service Fabric-toepassingen en-clusters](service-fabric-best-practices-overview.md) voor hulp bij het ontwerpen van toepassingen.

* Zie ook:
  * [Uitleg over microservices](service-fabric-overview-microservices.md)
  * [Service status definiëren en beheren](service-fabric-concepts-state.md)
  * [Beschikbaarheid van services](service-fabric-availability-services.md)
  * [Services schalen](service-fabric-concepts-scalability.md)
  * [Partitie Services](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.png
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.png

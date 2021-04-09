---
title: Bedrijfscontinuïteit en herstel na noodgevallen
description: Ontwerp uw strategie om gegevens te beveiligen, snel te herstellen van storende gebeurtenissen, resources te herstellen die vereist zijn voor kritieke bedrijfs functies en bedrijfs continuïteit voor Azure Logic Apps te hand haven
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 0a36cb468ebcb77c0614bffd0afc392df3655c20
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89658206"
---
# <a name="business-continuity-and-disaster-recovery-for-azure-logic-apps"></a>Bedrijfs continuïteit en herstel na nood gevallen voor Azure Logic Apps

Zorg ervoor dat u een oplossing voor [ *herstel na nood*](https://en.wikipedia.org/wiki/Disaster_recovery) geval hebt, zodat u gegevens kunt beveiligen, snel de resources die essentiële bedrijfs functies ondersteunen en bewerkingen die worden uitgevoerd voor het onderhouden van [ *bedrijfs continuïteit* (BC)](https://en.wikipedia.org/wiki/Business_continuity_planning), om de gevolgen en gevolgen te verminderen die onvoorspelbare gebeurtenissen op uw bedrijf en klanten hebben. Onderbrekingen kunnen bijvoorbeeld uitval bevatten, verlies in onderliggende infra structuur of onderdelen, zoals opslag-, netwerk-of reken bronnen, onherstelbare toepassings fouten of zelfs een volledig Data Center-verlies. Als u een oplossing voor bedrijfs continuïteit en herstel na nood gevallen (BCDR) kunt maken, kan uw bedrijf of organisatie sneller reageren op onderbrekingen, gepland of niet-gepland, en kan de downtime voor uw klanten worden verminderd.

Dit artikel bevat BCDR-richt lijnen en-strategieën die u kunt Toep assen wanneer u geautomatiseerde werk stromen bouwt met behulp van [Azure Logic apps](../logic-apps/logic-apps-overview.md). Met werk stromen voor logische apps kunt u eenvoudig gegevens integreren en organiseren tussen apps, Cloud Services en on-premises systemen door te verminderen hoeveel code u moet schrijven. Wanneer u van plan bent BCDR te gebruiken, moet u ervoor zorgen dat u niet alleen uw logische apps beschouwt, maar ook deze Azure-resources die u gebruikt met uw Logic apps:

* [Verbindingen](../connectors/apis-list.md) die u maakt vanuit logische apps naar andere apps, services en systemen. Zie [verbindingen met resources](#resource-connections) verderop in dit onderwerp voor meer informatie.

* [On-premises gegevens gateways](../logic-apps/logic-apps-gateway-connection.md) die Azure-resources zijn die u maakt en gebruikt in uw Logic apps om toegang te krijgen tot gegevens in on-premises systemen. Elke gateway bron vertegenwoordigt een afzonderlijke [installatie van de gegevens gateway](../logic-apps/logic-apps-gateway-install.md) op een lokale computer. Zie [on-premises gegevens gateways](#on-premises-data-gateways) verderop in dit onderwerp voor meer informatie.

* [Integratie accounts](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) waar u de artefacten definieert en opslaat die door Logic apps worden gebruikt voor [Business-to-Business (B2B)-integratie](../logic-apps/logic-apps-enterprise-integration-overview.md) scenario's. U kunt bijvoorbeeld [een nood herstel voor meerdere regio's instellen voor integratie accounts](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md).

* [Integratie service omgevingen (ISEs)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) waar u logische apps maakt die worden uitgevoerd in een geïsoleerd Logic apps runtime-exemplaar binnen een virtueel Azure-netwerk. Deze Logic apps kunnen vervolgens toegang krijgen tot bronnen die worden beveiligd achter een firewall in dat virtuele netwerk.

<a name="primary-secondary-locations"></a>

## <a name="primary-and-secondary-locations"></a>Primaire en secundaire locaties

Elke logische app moet de locatie opgeven die u wilt gebruiken voor de implementatie. Deze locatie is een open bare regio in wereld wijde multi tenant Azure, zoals ' vs-West ', of een ISE-integratie service omgeving die u eerder hebt gemaakt en geïmplementeerd in een virtueel Azure-netwerk. Het uitvoeren van Logic apps in een ISE is vergelijkbaar met het uitvoeren van Logic apps in een wereld wijde Azure-regio. Dit betekent dat uw strategie voor herstel na nood gevallen kan worden toegepast op beide scenario's. ISEs hebben echter andere overwegingen, zoals het configureren van toegang tot bronnen die alleen beschikbaar zijn voor ISEs.

> [!NOTE]
> Als uw logische app ook werkt met B2B-artefacten, zoals handels partners, overeenkomsten, schema's, kaarten en certificaten, die zijn opgeslagen in een integratie account, moeten zowel het integratie account als de Logic apps dezelfde locatie opgeven.

Deze strategie voor herstel na nood gevallen is gericht op het instellen van uw primaire logische app voor [*failover*](https://en.wikipedia.org/wiki/Failover) naar een stand-by-of back-uplogische app op een alternatieve locatie waar Azure Logic apps ook beschikbaar is. Op die manier kunt u, als de primaire verliezen, onderbrekingen of storingen lijden, de secundaire op het werk uitvoeren. Deze strategie vereist dat uw secundaire logische app en afhankelijke bronnen al zijn geïmplementeerd en gereed zijn op de alternatieve locatie.

Als u goede DevOps-procedures volgt, gebruikt u [Azure Resource Manager sjablonen](../azure-resource-manager/management/overview.md) al om uw Logic apps en hun afhankelijke resources te definiëren en implementeren. Resource Manager-sjablonen bieden u de mogelijkheid om één implementatie definitie te gebruiken en vervolgens parameter bestanden te gebruiken om de configuratie waarden te bieden die voor elke implementatie bestemming moeten worden gebruikt. Deze mogelijkheid betekent dat u dezelfde logische app kunt implementeren in verschillende omgevingen, zoals ontwikkeling, testen en productie. U kunt ook dezelfde logische app implementeren in verschillende Azure-regio's of ISEs, die ondersteuning biedt voor nood herstel strategieën die gebruikmaken van [gekoppelde regio's](../best-practices-availability-paired-regions.md).

Voor de failover-strategie moeten uw logische apps en locaties voldoen aan deze vereisten:

* Het exemplaar van de secundaire logische app heeft toegang tot dezelfde apps, services en systemen als het primaire exemplaar van de logische app.

* Beide logische app-exemplaren hebben hetzelfde type host. Beide exemplaren worden dus geïmplementeerd in regio's in wereld wijde multi tenant-Azure, of beide exemplaren worden geïmplementeerd op ISEs, waardoor uw Logic apps rechtstreeks toegang hebben tot resources in een virtueel Azure-netwerk. Zie voor aanbevolen procedures en meer informatie over gekoppelde regio's voor BCDR [bedrijfs continuïteit en herstel na nood gevallen (BCDR): gekoppelde Azure-regio's](../best-practices-availability-paired-regions.md).

  Zowel de primaire als de secundaire locatie moeten bijvoorbeeld ISEs zijn wanneer de primaire logische app wordt uitgevoerd in een ISE en gebruikmaakt van [connectors met ISE-versie](../connectors/apis-list.md#ise-connectors), http-acties voor het aanroepen van resources in het virtuele Azure-netwerk, of beide. In dit scenario moet uw secundaire logische app ook een vergelijk bare instelling hebben op de secundaire locatie als de primaire logische app.

  > [!NOTE]
  > Voor meer geavanceerde scenario's kunt u zowel multi tenant Azure als een ISE combi neren als locaties. Zorg er echter voor dat u rekening houdt met de [verschillen tussen hoe Logic apps worden uitgevoerd in een ISE versus multi tenant Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference).

* Als u ISEs gebruikt, [moet u ervoor zorgen dat ze zijn geschaald of voldoende capaciteit hebben](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) om de belasting te kunnen afhandelen.

#### <a name="example-multi-tenant-azure"></a>Voor beeld: multi tenant Azure

In dit voor beeld worden de primaire en secundaire logische app-exemplaren weer gegeven die zijn geïmplementeerd in afzonderlijke regio's in de wereld wijde multi tenant Azure voor dit scenario. Een enkele [Resource Manager-sjabloon](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) definieert zowel logische app-exemplaren als de afhankelijke resources die vereist zijn voor deze Logic apps. Afzonderlijke parameter bestanden Geef de configuratie waarden op die voor elke implementatie locatie moeten worden gebruikt:

![Primaire en secundaire logische app-exemplaren op afzonderlijke locaties](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations.png)

#### <a name="example-integration-service-environment"></a>Voor beeld: integratie service omgeving

In dit voor beeld worden de vorige instanties van de primaire en secundaire logische app weer gegeven, maar geïmplementeerd in afzonderlijke ISEs. Een enkele Resource Manager-sjabloon definieert zowel logische app-exemplaren, de afhankelijke resources die zijn vereist voor deze Logic apps en de ISEs als de implementatie locaties. Met afzonderlijke parameter bestanden worden de configuratie waarden gedefinieerd die voor de implementatie op elke locatie moeten worden gebruikt:

![Primaire en secundaire Logic apps op verschillende locaties](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations-ise.png)

<a name="resource-connections"></a>

## <a name="connections-to-resources"></a>Verbindingen met bronnen

Azure Logic Apps biedt [ingebouwde triggers en acties plus honderden beheerde connectors](../connectors/apis-list.md) die uw logische app kan gebruiken om te werken met andere apps, services, systemen en andere resources, zoals Azure Storage accounts, SQL server data bases, werk-of school-e-mail accounts, enzovoort. Als uw logische app toegang moet hebben tot deze bronnen, maakt u verbindingen die de toegang tot deze bronnen verifiëren. Elke verbinding is een afzonderlijke Azure-resource die bestaat op een specifieke locatie en niet kan worden gebruikt door resources op andere locaties.

Voor de strategie voor herstel na nood gevallen moet u rekening houden met de locaties waar afhankelijke resources zich bevinden ten opzichte van uw logische app-exemplaren:

* Uw primaire exemplaar en afhankelijke resources bevinden zich op verschillende locaties. In dit geval kan uw secundaire exemplaar verbinding maken met dezelfde afhankelijke resources of eind punten. U moet echter wel specifieke verbindingen maken voor uw secundaire exemplaar. Op die manier, als uw primaire locatie niet beschikbaar is, worden de verbindingen van uw secundaire niet beïnvloed.

  Stel bijvoorbeeld dat uw primaire logische app verbinding maakt met een externe service, zoals Sales Force. Normaal gesp roken zijn de beschik baarheid en locatie van de externe service onafhankelijk van de beschik baarheid van uw logische app. In dit geval kan de secundaire instantie verbinding maken met dezelfde service, maar moet hij een eigen verbinding hebben.

* Zowel uw primaire exemplaar als afhankelijke resources bevinden zich op dezelfde locatie. In dit geval moeten afhankelijke resources back-ups of gerepliceerde versies op een andere locatie hebben, zodat uw secundaire instantie nog steeds toegang heeft tot die bronnen.

  Stel bijvoorbeeld dat uw primaire logische app verbinding maakt met een service die zich op dezelfde locatie of regio bevinden, bijvoorbeeld Azure SQL Database. Als deze hele regio niet meer beschikbaar is, is de Azure SQL Database-Service in die regio waarschijnlijk ook niet beschikbaar. In dit geval wilt u dat uw secundaire instantie een gerepliceerde of back-updatabase gebruikt, samen met een afzonderlijke verbinding naar die data base.

<a name="on-premises-data-gateways"></a>

## <a name="on-premises-data-gateways"></a>On-premises gegevensgateways

Als uw logische app wordt uitgevoerd in een multi tenant Azure en toegang moet hebben tot on-premises resources, zoals SQL Server data bases, moet u de [on-premises gegevens gateway](../logic-apps/logic-apps-gateway-install.md) installeren op een lokale computer. U kunt vervolgens een gegevens gateway bron maken in de Azure Portal zodat uw logische app de gateway kan gebruiken bij het maken van een verbinding met de bron.

De gegevens gateway resource is gekoppeld aan een locatie of Azure-regio, net als bij de logische app-resource. Zorg er in uw strategie voor herstel na nood gevallen voor dat de gegevens gateway beschikbaar blijft voor de logische app die u wilt gebruiken. U kunt [hoge Beschik baarheid voor uw gateway inschakelen](../logic-apps/logic-apps-gateway-install.md#high-availability) wanneer u meerdere gateway-installaties hebt.

> [!NOTE]
> Als uw logische app wordt uitgevoerd in een integratie service omgeving (ISE) en alleen connectors met ISE-versie gebruikt voor on-premises gegevens bronnen, hebt u de gegevens gateway niet nodig, omdat ISE-connectors rechtstreeks toegang bieden tot de on-premises resource.
>
> Als er geen connector met ISE-versie beschikbaar is voor de on-premises resource die u wilt, kunt u met uw logische app nog steeds verbinding maken met behulp van een niet-ISE-connector, die wordt uitgevoerd in de globale multi-tenant Azure, niet op uw ISE. Voor deze verbinding is echter de on-premises gegevens gateway vereist.

<a name="roles"></a>

## <a name="active-active-versus-active-passive-roles"></a>Actief-actief versus actief/passieve rollen

U kunt uw primaire en secundaire locaties instellen, zodat de logische app-exemplaren op deze locaties deze rollen kunnen afspelen:

| Primaire-secundaire rol | Description |
|------------------------|-------------|
| *Actief-actief* | De primaire en secundaire logische app-exemplaren op beide locaties voeren aanvragen actief uit met behulp van de volgende patronen: <p><p>- *Taak verdeling*: u kunt beide exemplaren Luis teren naar een eind punt en zo nodig taak verdeling voor elk exemplaar naar behoefte hebben. <p>- *Concurrerende consumenten*: u kunt beide instanties als concurrerende consumenten fungeren, zodat de instanties concurreren voor berichten uit een wachtrij. Als één exemplaar mislukt, neemt de andere instantie de werk belasting over. |
| *Actief-passief* | Het primaire exemplaar van de logische app verwerkt de hele werk belasting actief, terwijl het secundaire exemplaar passief is (uitgeschakeld of inactief). De secundaire wacht tijd op een signaal dat de primaire niet beschikbaar is of niet werkt vanwege onderbrekingen of fouten en de werk belasting als het actieve exemplaar neemt. |
| Neren | Sommige Logic apps spelen een actief/actief-rol, terwijl andere logische apps een actief-passieve rol spelen. |
|||

<a name="active-active-examples"></a>

### <a name="active-active-examples"></a>Active-Active-voor beelden

In deze voor beelden ziet u de Active-Active Setup waarbij de logische app-exemplaren actief aanvragen of berichten verwerken. Een ander systeem of service distribueert de aanvragen of berichten tussen instanties, bijvoorbeeld een van de volgende opties:

* Een ' fysieke ' load balancer, zoals een apparaat dat verkeer routeert

* Een ' zacht ' load balancer, zoals [Azure Load Balancer](../load-balancer/load-balancer-overview.md) of [Azure API Management](../api-management/api-management-key-concepts.md). Met API Management kunt u beleid opgeven dat bepaalt hoe inkomend verkeer wordt verdeeld. U kunt ook een service gebruiken die het bijhouden van status ondersteunt, bijvoorbeeld [Azure service bus](../service-bus-messaging/service-bus-messaging-overview.md).

  Hoewel in dit voor beeld voornamelijk Azure Load Balancer wordt weer gegeven, kunt u de optie gebruiken die het beste past bij de behoeften van uw scenario:

  ![' Actief-actief ' Setup die gebruikmaakt van een load balancer of stateful service](./media/business-continuity-disaster-recovery-guidance/active-active-setup-load-balancer.png)

* Elk logisch app-exemplaar fungeert als een consumer en beide instanties concurreren voor berichten van een wachtrij:

  ![' Actief-actief ' Setup die gebruikmaakt van ' concurrerende gebruikers '](./media/business-continuity-disaster-recovery-guidance/active-active-competing-consumers-pattern.png)

<a name="active-passive-examples"></a>

### <a name="active-passive-examples"></a>Voor beelden van actief/passief

In dit voor beeld ziet u de Active-passieve installatie waarbij het primaire exemplaar van de logische app actief is op één locatie, terwijl het secundaire exemplaar inactief is op een andere locatie. Als de primaire storing een onderbreking of storing ondervindt, kunt u een-operator gebruiken om een script uit te voeren dat de secundaire activeert om de werk belasting te ondernemen.

![' Actief-passief ' Setup die gebruikmaakt van ' concurrerende gebruikers '](./media/business-continuity-disaster-recovery-guidance/active-passive-setup.png)

<a name="active-active-active-passive-examples"></a>

### <a name="combination-with-active-active-and-active-passive"></a>Combi natie met actief-actief en actief-passief

In dit voor beeld ziet u een gecombineerde installatie waarbij de primaire locatie zowel actieve logische app-exemplaren heeft, terwijl de secundaire locatie actieve-passieve Logic app-exemplaren heeft. Als de primaire locatie een onderbreking of storing ondervindt, kan de actieve logische app op de secundaire locatie, die al een gedeeltelijke werk belasting verwerkt, de hele werk belasting overnemen.

* Op de primaire locatie luistert een actieve logische app naar een Azure Service Bus wachtrij voor berichten, terwijl een andere actieve logische app controleert op e-mails met behulp van een Office 365 Outlook polling-trigger.

* Op de secundaire locatie werkt een actieve logische app met de logische app op de primaire locatie door te Luis teren en te concurreren met berichten uit dezelfde Service Bus wachtrij. Ondertussen wacht een passieve inactieve logische app op stand-by om te controleren op e-mail berichten wanneer de primaire locatie niet beschikbaar is, maar is *uitgeschakeld* om e-mail berichten niet opnieuw te lezen.

![De combi natie actief-passief en actief-passief waarbij gebruik wordt gemaakt van terugkeer triggers](./media/business-continuity-disaster-recovery-guidance/combo-active-active-active-passive-setup.png)

<a name="state-history"></a>

## <a name="logic-app-state-and-history"></a>Status en geschiedenis van de logische app

Wanneer uw logische app wordt geactiveerd en wordt gestart, wordt de status van de app opgeslagen op dezelfde locatie waar de app is gestart en niet kan worden overgezet naar een andere locatie. Als er een storing of onderbreking optreedt, worden alle werk stroom exemplaren in uitvoering afgebroken. Wanneer u een primaire en secundaire locatie hebt ingesteld, worden nieuwe werk stroom exemplaren gestart op de secundaire locatie.

<a name="reduce-abandoned-in-progress"></a>

### <a name="reduce-abandoned-in-progress-instances"></a>Afgebroken instanties met verloop bewerkingen verminderen

Als u het aantal afgebroken werk stroom exemplaren wilt minimaliseren, kunt u kiezen uit verschillende bericht patronen die u kunt implementeren, bijvoorbeeld:

* [Patroon vaste circulatie lijst](/biztalk/esb-toolkit/message-routing-patterns#routing-slip)

  Dit ondernemings bericht patroon waarmee een bedrijfs proces in kleinere fasen wordt gesplitst. Voor elke fase stelt u een logische app in waarmee de werk belasting voor die fase wordt afgehandeld. Om met elkaar te communiceren, gebruiken uw Logic apps een asynchroon berichten protocol, zoals Azure Service Bus wacht rijen of onderwerpen. Wanneer u een proces in kleinere stadia verdeelt, verkleint u het aantal bedrijfs processen dat kan vastlopen op een mislukt logische app-exemplaar. Zie [Enter prise Integration Patterns-Routing Slip (](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RoutingTable.html)Engelstalig) voor meer algemene informatie over dit patroon.

  In dit voor beeld ziet u een patroon circulatie lijst waarbij elke logische app een fase vertegenwoordigt en een Service Bus wachtrij gebruikt om te communiceren met de volgende logische app in het proces.

  ![Een bedrijfs proces splitsen in fasen die worden vertegenwoordigd door Logic apps, die met elkaar communiceren met behulp van Azure Service Bus wachtrijen](./media/business-continuity-disaster-recovery-guidance/fixed-routing-slip-pattern.png)

  Als zowel de primaire als de secundaire Logic-app-exemplaren hetzelfde circulatie lijst patroon op hun locaties volgen, kunt u het [patroon voor concurrerende gebruikers](/azure/architecture/patterns/competing-consumers) implementeren door [actief-actief rollen](#roles) voor die instanties in te stellen.

* [Patroon van proces beheerder (Broker)](https://www.enterpriseintegrationpatterns.com/patterns/messaging/ProcessManager.html)

* [Kort weer geven/vergren delen zonder time-outpatroon](https://social.technet.microsoft.com/wiki/contents/articles/50022.azure-service-bus-how-to-peek-lock-a-message-from-queue-using-azure-logic-apps.aspx)

<a name="access-trigger-runs-history"></a>

### <a name="access-to-trigger-and-runs-history"></a>Toegang tot trigger-en uitvoerings geschiedenis

Als u meer informatie wilt over de afgelopen werk stroom uitvoeringen van de logische app, kunt u de trigger en uitvoeringen van de app bekijken. De geschiedenis uitvoerings geschiedenis van een logische app wordt opgeslagen op dezelfde locatie of regio waar de logische app werd uitgevoerd, wat betekent dat u deze geschiedenis niet kunt migreren naar een andere locatie. Als er een failover van uw primaire instantie naar een secundair exemplaar wordt uitgevoerd, kunt u alleen toegang krijgen tot de trigger van elk exemplaar en wordt de geschiedenis van de betreffende locaties waar die instanties zijn uitgevoerd. U kunt echter neutraal informatie over de geschiedenis van uw logische app ophalen door uw Logic apps in te stellen voor het verzenden van diagnostische gebeurtenissen naar een Azure Log Analytics-werk ruimte. U kunt vervolgens de status en geschiedenis controleren over Logic apps die op meerdere locaties worden uitgevoerd.

<a name="trigger-types-guidance"></a>

## <a name="trigger-type-guidance"></a>Hulp voor trigger type

Het trigger type dat u in uw Logic apps gebruikt, bepaalt uw opties voor het instellen van logische apps op verschillende locaties in uw strategie voor herstel na nood gevallen. Dit zijn de beschik bare trigger typen die u kunt gebruiken in Logic apps:

* [Trigger voor terugkeer patroon](#recurrence-trigger)
* [Polling trigger](#polling-trigger)
* [Trigger voor aanvragen](#request-trigger)
* [Webhook-trigger](#webhook-trigger)

<a name="recurrence-trigger"></a>

### <a name="recurrence-trigger"></a>Trigger voor terugkeer patroon

De **terugkeer patroon** trigger is onafhankelijk van een specifieke service of elk eind punt, en wordt alleen geactiveerd op basis van een opgegeven schema en geen andere criteria, bijvoorbeeld:

* Een vaste frequentie en een interval, bijvoorbeeld elke 10 minuten
* Een meer geavanceerde planning, zoals de laatste maandag van elke maand om 5:00 uur

Wanneer uw logische app begint met een terugkeer patroon trigger, moet u uw primaire en secundaire logische app-exemplaren instellen met de [actieve passieve rollen](#roles). Als u de *beoogde herstel tijd* (RTO), die verwijst naar de doel duur voor het herstellen van een bedrijfs proces na een onderbreking of nood geval, wilt beperken, kunt u uw logische app-instanties instellen met een combi natie van [actief/passieve rollen](#roles) en [passieve actieve rollen](#roles). In deze installatie splitst u de planning op verschillende locaties.

Stel bijvoorbeeld dat u een logische app hebt die om de 10 minuten moet worden uitgevoerd. U kunt uw logische apps en locaties zo instellen dat als de primaire locatie niet beschikbaar is, de secundaire locatie het werk kan overnemen:

![De combi natie actief-passief en passief actief waarbij gebruik wordt gemaakt van terugkeer triggers](./media/business-continuity-disaster-recovery-guidance/combo-active-passive-passive-active-setup.png)

* Stel op de primaire locatie actieve, [passieve rollen](#roles) in voor deze Logic apps:

  * Voor de *actieve* logica-app stelt u de terugkeer patroon trigger in op begin van het uur en herhaalt u elke 20 minuten, bijvoorbeeld 9:00 am, 9:20 uur, enzovoort.

  * Voor de *passieve* , gehandicapte logische app stelt u de terugkeer patroon trigger in op hetzelfde schema, maar begint u 10 minuten na het hele uur en herhaalt u elke 20 minuten, bijvoorbeeld 9:10 am, 9:30 uur, enzovoort.

* Stel op de secundaire locatie [passieve actief](#roles) in voor deze Logic apps:

  * Voor de *passieve* , gehandicapte logische app stelt u de terugkeer patroon trigger in op dezelfde planning als de actieve logische app op de primaire locatie, die zich boven aan het uur bevindt en elke 20 minuten herhaalt, bijvoorbeeld 9:00 am, 9:10 am, enzovoort.

  * Voor de *actieve* logica-app stelt u de terugkeer patroon trigger in op dezelfde planning als de passieve logische app op de primaire locatie, die begint om 10 minuten na het uur en elke 20 minuten herhalen, bijvoorbeeld 9:10 uur, 9:20 uur, enzovoort.

Als er nu een storende gebeurtenis op de primaire locatie plaatsvindt, activeert u de passieve logische app op de alternatieve locatie. Op die manier wordt bij het zoeken naar de uitval tijd het aantal gemiste terugkeer patronen tijdens die vertraging beperkt.

<a name="polling-trigger"></a>

### <a name="polling-trigger"></a>Polling trigger

Om regel matig te controleren of nieuwe gegevens voor verwerking beschikbaar zijn vanuit een specifieke service of eind punt, kan uw logische app een *polling* trigger gebruiken die herhaaldelijk de service of het eind punt aanroept op basis van een vast terugkeer patroon schema. De gegevens die de service of het eind punt biedt, kunnen een van de volgende typen hebben:

* Statische gegevens, waarmee gegevens worden beschreven die altijd beschikbaar zijn voor lezen
* Vluchtige gegevens, waarmee gegevens worden beschreven die niet meer beschikbaar zijn na het lezen

Om te voor komen dat dezelfde gegevens herhaaldelijk worden gelezen, moet uw logische app weten welke gegevens eerder werden gelezen door de status aan de client zijde of op de server, service of systeem zijde te bewaren.

* Logic apps die samen werken met de status van de client-side gebruiken triggers die de status kunnen behouden.

  Een trigger die een nieuw bericht van een postvak in leest, vereist bijvoorbeeld dat de trigger het laatst gelezen bericht kan onthouden. Op die manier start de trigger de logische app alleen wanneer het volgende ongelezen bericht binnenkomt.

* Logic apps die samen werken met de status van de server, de service of het systeem, maken gebruik van eigenschaps waarden of-instellingen die zich op de server, service of systeem zijde bevinden.

  Een op query's gebaseerde trigger die een rij uit een Data Base leest, vereist bijvoorbeeld dat de rij een `isRead` kolom bevat die is ingesteld op `FALSE` . Telkens wanneer de trigger een rij leest, wordt die rij door de logische app bijgewerkt door de `isRead` kolom te wijzigen van `FALSE` in `TRUE` .

  Deze aanpak op de server werkt op dezelfde manier als Service Bus-wacht rijen of-onderwerpen die een wachtrij semantiek hebben waarbij een trigger een bericht kan lezen en vergren delen terwijl het bericht door de logische app wordt verwerkt. Wanneer de logische app is verwerkt, verwijdert de trigger het bericht uit de wachtrij of het onderwerp.

Wanneer u de primaire en secundaire exemplaren van de logische app instelt, moet u bij het maken van een nood herstel perspectief ervoor zorgen dat u rekening houdt met deze gedragingen op basis van het feit of uw logische app de status van de client aan de zijde of aan de server zijde volgt:

* Voor een logische app die werkt met de status van de client-side, zorgt u ervoor dat het bericht niet meer dan één keer in uw logische app wordt gelezen. Op elk gewenst moment kan slechts één locatie een actieve logische app-instantie hebben. Zorg ervoor dat het logische app-exemplaar op de alternatieve locatie inactief is of is uitgeschakeld totdat de primaire instantie een failover naar de alternatieve locatie uitvoert.

  Bijvoorbeeld: de trigger Office 365 Outlook onderhoudt de status van client-side en houdt de tijds tempel bij voor de meest recent gelezen e-mail om te voor komen dat er een duplicaat wordt gelezen.

* Voor een logische app die werkt met de status aan de server zijde, kunt u uw logische app-exemplaren zo instellen dat deze [actief-actief zijn](#roles) , waar ze werken als concurrerende gebruikers of [actieve passieve rollen](#roles) waarbij de alternatieve instantie wacht totdat de primaire instantie een failover naar de alternatieve locatie kan uitvoeren.

  Als u bijvoorbeeld een bericht wachtrij leest, zoals een Azure Service Bus wachtrij, wordt de status aan server zijde gebruikt, omdat de Queuing-service vergrendelde berichten bijhoudt om te voor komen dat andere clients dezelfde berichten lezen.

  > [!NOTE]
  > Als uw logische app berichten in een specifieke volg orde moet lezen, bijvoorbeeld van een Service Bus wachtrij, kunt u het concurrerende consumenten patroon gebruiken, maar alleen in combi natie met Service Bus-sessies, ook wel bekend als het [ *sequentiële verwerkings* patroon](/azure/architecture/patterns/sequential-convoy). Anders moet u de logische app-exemplaren instellen met de actieve passieve rollen.

<a name="request-trigger"></a>

### <a name="request-trigger"></a>Trigger voor aanvragen

Met de **aanvraag** trigger kan uw logische app aanroepen van andere apps, services en systemen, en wordt doorgaans gebruikt om deze mogelijkheden te bieden:

* Een directe REST API voor uw logische app die anderen kunnen aanroepen

  Gebruik bijvoorbeeld de trigger voor aanvragen om uw logische app te starten, zodat andere logische apps de trigger kunnen aanroepen met behulp van de actie **werk stroom aanroepen-Logic apps** .

* Een [webhook](#webhook-trigger) of call back-mechanisme voor uw logische app

* Een manier waarop u gebruikers bewerkingen of routines hand matig kunt uitvoeren om uw logische app aan te roepen, bijvoorbeeld door gebruik te maken van een Power shell-script waarmee een specifieke taak wordt uitgevoerd

Vanuit een nood herstel perspectief is de aanvraag trigger een passieve ontvanger, omdat de logische app geen werk doet en wacht totdat een andere service of systeem de trigger expliciet aanroept. Als passief eind punt kunt u uw primaire en secundaire instanties op de volgende manieren instellen:

* [Actief-actief](#roles): beide exemplaren verwerken aanvragen of aanroepen. De aanroeper of router is in balans of verdeelt verkeer tussen deze instanties.

* [Actief-passief](#roles): alleen het primaire exemplaar is actief en verwerkt alle werk, terwijl het secundaire exemplaar wacht totdat de primaire storing optreedt of de fout optreedt. De aanroeper of router bepaalt wanneer het secundaire exemplaar moet worden aangeroepen.

Als een aanbevolen architectuur kunt u Azure API Management gebruiken als proxy voor de Logic apps die gebruikmaken van aanvraag triggers. API Management biedt [ingebouwde Kruis-regionale tolerantie en de mogelijkheid om verkeer te routeren over meerdere eind punten](../api-management/api-management-howto-deploy-multi-region.md).

<a name="webhook-trigger"></a>

### <a name="webhook-trigger"></a>Webhook-trigger

Een *webhook* -trigger biedt de mogelijkheid om uw logische app te abonneren op een service door een *call back-URL* naar die service door te geven. Uw logische app kan vervolgens Luis teren en wachten tot een specifieke gebeurtenis plaatsvindt op dat service-eind punt. Wanneer de gebeurtenis plaatsvindt, roept de service de webhook-trigger aan met behulp van de call back-URL, waarna de logische app wordt uitgevoerd. Wanneer deze functie is ingeschakeld, wordt de webhook geactiveerd op de service. Wanneer dit is uitgeschakeld, wordt de trigger afgemeld bij de service.

Stel vanuit een perspectief voor nood herstel primaire en secundaire exemplaren in die gebruikmaken van webhook-triggers om actieve passieve rollen af te spelen, omdat er slechts één instantie gebeurtenissen of berichten van het geabonneerde eind punt moet ontvangen.

## <a name="assess-primary-instance-health"></a>Status van primair exemplaar evalueren

De strategie voor herstel na nood gevallen werkt alleen als uw oplossing manieren heeft om deze taken uit te voeren:

* [De beschik baarheid van het primaire exemplaar controleren](#check-primary-availability)
* [De status van het primaire exemplaar controleren](#monitor-primary-health)
* [Het secundaire exemplaar activeren](#activate-secondary)

In deze sectie wordt één oplossing beschreven die u kunt gebruiken of als basis voor uw eigen ontwerp. Hier volgt een visueel overzicht van een hoog niveau voor deze oplossing:

![Een watchdog Logic-app maken die een logische app voor status controle op de primaire locatie bewaakt](./media/business-continuity-disaster-recovery-guidance/check-location-health-watchdog.png)

<a name="check-primary-availability"></a>

### <a name="check-primary-instance-availability"></a>Beschik baarheid van primair exemplaar controleren

Als u wilt bepalen of het primaire exemplaar beschikbaar is, wordt uitgevoerd en dat kan worden gebruikt, kunt u een logische app voor status controle maken die zich op dezelfde locatie bevindt als het primaire exemplaar. Vervolgens kunt u deze app voor de status controle aanroepen vanaf een alternatieve locatie. Als de status controle-app reageert, is de onderliggende infra structuur voor de Azure Logic Apps-service in die regio beschikbaar en werkt deze. Als de status controle-app niet reageert, kunt u aannemen dat de locatie niet meer in orde is.

Voor deze taak maakt u een basis logische app voor het controleren van de status die deze taken uitvoert:

1. Ontvangt een aanroep van de watchdog-app met behulp van de aanvraag trigger.

1. Reageer op een status die aangeeft of de gecontroleerde logische app nog steeds werkt met behulp van de reactie actie.

   > [!IMPORTANT]
   > De logische app voor status controle moet een reactie actie gebruiken zodat de app synchroon en niet asynchroon reageert.

1. U kunt er ook voor kiezen om verder te bepalen of de primaire locatie in orde is, of u de status van andere services die communiceren met de doel logische app op deze locatie. Vouw alleen de logische app voor status controle uit om de status voor deze andere services te beoordelen.

<a name="monitor-primary-health"></a>

### <a name="create-a-watchdog-logic-app"></a>Een watchdog Logic-app maken

Als u de status van het primaire exemplaar wilt controleren en de logische app voor status controle aanroept, maakt u een ' watchdog ' logische app op een *alternatieve locatie*. U kunt bijvoorbeeld de watchdog Logic-app instellen, zodat als het aanroepen van de logica voor de status controle mislukt, de watchdog een waarschuwing kan verzenden naar uw Operations-team zodat deze de fout kan onderzoeken en waarom het primaire exemplaar niet reageert.

> [!IMPORTANT]
> Zorg ervoor dat uw watchdog Logic app zich op een locatie bevindt *die verschilt van de primaire locatie*. Als de Logic Apps-service op de primaire locatie problemen ondervindt, kan uw watchdog-logische app mogelijk niet worden uitgevoerd.

Voor deze taak maakt u op de secundaire locatie een watchdog Logic-app waarmee u de volgende taken uitvoert:

1. Voer uit op basis van een vast of gepland terugkeer patroon met behulp van de terugkeer patroon trigger.

   U kunt het terugkeer patroon instellen op een waarde die lager is dan het tolerantie niveau voor uw beoogde herstel tijd (RTO).

1. Roep de logische app voor status controle op de primaire locatie aan met behulp van de HTTP-actie, bijvoorbeeld:

<a name="activate-secondary"></a>

### <a name="activate-your-secondary-instance"></a>Activeer uw secundaire exemplaar

Als u de secundaire instantie automatisch wilt activeren, kunt u een logische app maken die de beheer-API aanroept, zoals de [Azure Resource Manager connector](/connectors/arm/) om de juiste Logic apps te activeren op de secundaire locatie. U kunt uw watchdog-app uitbreiden om deze activerings logische app aan te roepen nadat een bepaald aantal fouten is opgetreden.

<a name="collect-diagnostic-data"></a>

## <a name="collect-diagnostic-data"></a>Diagnostische gegevens verzamelen

U kunt logboek registratie instellen voor uw logische app-uitvoeringen en de resulterende diagnostische gegevens verzenden naar Services als Azure Storage, Azure Event Hubs en Azure Log Analytics voor verdere verwerking en verwerking.

* Als u deze gegevens wilt gebruiken met Azure Log Analytics, kunt u de gegevens beschikbaar maken voor zowel de primaire als de secundaire locatie door de **Diagnostische instellingen** van uw logische app in te stellen en de gegevens naar meerdere log Analytics-werk ruimten te verzenden. Zie [Azure monitor-logboeken instellen en diagnostische gegevens verzamelen voor Azure Logic apps](../logic-apps/monitor-logic-apps-log-analytics.md)voor meer informatie.

* Als u de gegevens naar Azure Storage of Azure Event Hubs wilt verzenden, kunt u de gegevens beschikbaar maken voor zowel de primaire als de secundaire locatie door geografische redundantie in te stellen. Raadpleeg deze artikelen voor meer informatie:<p>

  * [Azure Blob Storage herstel na nood gevallen en failover van account](../storage/common/storage-disaster-recovery-guidance.md)
  * [Azure Event Hubs geo-nood herstel](../event-hubs/event-hubs-geo-dr.md)

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van tolerantie voor Azure](/azure/architecture/framework/resiliency/overview)
* [Controlelijst voor tolerantie voor specifieke Azure-services](/azure/architecture/checklist/resiliency-per-service)
* [Gegevens beheer voor tolerantie in azure](/azure/architecture/framework/resiliency/data-management)
* [Back-ups en herstel na nood gevallen voor Azure-toepassingen](/azure/architecture/framework/resiliency/backup-and-recovery)
* [Herstel na een serviceonderbreking in de gehele regio](/azure/architecture/resiliency/recovery-loss-azure-region)
* [Micro soft Service Level Agreements (Sla's) voor Azure-Services](https://azure.microsoft.com/support/legal/sla/)

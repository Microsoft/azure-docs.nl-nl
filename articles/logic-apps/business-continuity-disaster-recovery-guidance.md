---
title: Bedrijfscontinuïteit en herstel na noodgevallen
description: Ontwerp uw strategie om gegevens te beschermen, snel te herstellen van verstorende gebeurtenissen, resources te herstellen die nodig zijn voor kritieke bedrijfsfuncties en bedrijfscontinuïteit te waarborgen voor Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: f974a99c59b19b5df7bf6ffcc66c2dc133743f0a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790535"
---
# <a name="business-continuity-and-disaster-recovery-for-azure-logic-apps"></a>Bedrijfscontinuïteit en herstel na nood Azure Logic Apps

Om de impact en effecten van onvoorspelbare gebeurtenissen op uw bedrijf en klanten te verminderen, moet u ervoor zorgen dat u een noodhersteloplossing [  (DR)](https://en.wikipedia.org/wiki/Disaster_recovery) hebt, zodat u gegevens kunt beveiligen, snel de resources kunt herstellen die essentiële bedrijfsfuncties ondersteunen, en ervoor kunt zorgen dat bewerkingen worden uitgevoerd om bedrijfscontinuïteit [  (BC)](https://en.wikipedia.org/wiki/Business_continuity_planning)te behouden. Onderbrekingen kunnen bijvoorbeeld storingen, verliezen in de onderliggende infrastructuur of onderdelen, zoals opslag, netwerk of rekenresources, onherkenbare toepassingsfouten of zelfs een volledig datacentrumverlies omvatten. Door een BCDR-oplossing (Business Continuity and Disaster Recovery) gereed te hebben, kan uw onderneming of organisatie sneller reageren op onderbrekingen, gepland of ongepland, en downtime voor uw klanten verminderen.

Dit artikel bevat BCDR-richtlijnen en strategieën die u kunt toepassen wanneer u geautomatiseerde werkstromen bouwt met behulp van [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Met werkstromen voor logische apps kunt u gegevens gemakkelijker integreren en beheren tussen apps, cloudservices en on-premises systemen door de code die u moet schrijven te verminderen. Wanneer u bcdr van plan bent, moet u niet alleen rekening houden met uw logische apps, maar ook met deze Azure-resources die u met uw logische apps gebruikt:

* [Verbindingen die](../connectors/apis-list.md) u maakt van logische apps naar andere apps, services en systemen. Zie Verbindingen met resources verder in dit [onderwerp](#resource-connections) voor meer informatie.

* [On-premises gegevensgateways zijn](../logic-apps/logic-apps-gateway-connection.md) Azure-resources die u in uw logische apps maakt en gebruikt voor toegang tot gegevens in on-premises systemen. Elke gatewayresource vertegenwoordigt een afzonderlijke [installatie van een gegevensgateway](../logic-apps/logic-apps-gateway-install.md) op een lokale computer. Zie [On-premises gegevensgateways](#on-premises-data-gateways) verder op dit onderwerp voor meer informatie.

* [Integratieaccounts](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) waarin u de artefacten definieert en opgeslagen die logische apps gebruiken voor [B2B-integratiescenario's (Business-to-Business).](../logic-apps/logic-apps-enterprise-integration-overview.md) U kunt bijvoorbeeld herstel na [noodherstel tussen regio's instellen voor integratieaccounts.](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md)

* [Integratieserviceomgevingen (ISE's)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) waarin u logische apps maakt die worden uitgevoerd in een geïsoleerd Logic Apps runtime-exemplaar binnen een virtueel Azure-netwerk. Deze logische apps hebben vervolgens toegang tot resources die worden beveiligd achter een firewall in dat virtuele netwerk.

<a name="primary-secondary-locations"></a>

## <a name="primary-and-secondary-locations"></a>Primaire en secundaire locaties

Elke logische app moet de locatie opgeven die u wilt gebruiken voor implementatie. Deze locatie is een openbare regio in azure met meerdere tenants wereldwijd, zoals 'VS - west', of een ISE (Integration Service Environment) die u eerder hebt gemaakt en geïmplementeerd in een virtueel Azure-netwerk. Het uitvoeren van logische apps in een ISE is vergelijkbaar met het uitvoeren van logische apps in een wereldwijde Azure-regio, wat betekent dat uw strategie voor herstel na nood gevallen van toepassing kan zijn op beide scenario's. ISE's hebben echter andere overwegingen, zoals het configureren van toegang tot resources die alleen beschikbaar zijn voor ISE's.

> [!NOTE]
> Als uw logische app ook werkt met B2B-artefacten, zoals handelspartners, overeenkomsten, schema's, kaarten en certificaten, die zijn opgeslagen in een integratieaccount, moeten uw integratieaccount en logische apps dezelfde locatie opgeven.

Deze strategie voor herstel na noodherstel is gericht op het instellen van uw primaire logische app voor [*failover*](https://en.wikipedia.org/wiki/Failover) naar een stand-by- of back-uplogica-app op een alternatieve locatie waar Azure Logic Apps ook beschikbaar is. Op die manier kan de secundaire taak worden voortgezet als de primaire te maken heeft met verliezen, onderbrekingen of fouten. Voor deze strategie moeten uw secundaire logische app en afhankelijke resources al zijn geïmplementeerd en gereed zijn op de alternatieve locatie.

Als u goede DevOps-procedures volgt, gebruikt u al [Azure Resource Manager](../azure-resource-manager/management/overview.md) sjablonen om uw logische apps en hun afhankelijke resources te definiëren en te implementeren. Resource Manager sjablonen bieden u de mogelijkheid om één implementatiedefinitie te gebruiken en vervolgens parameterbestanden te gebruiken om de configuratiewaarden op te geven die voor elke implementatiebestemming moeten worden gebruikt. Deze mogelijkheid betekent dat u dezelfde logische app kunt implementeren in verschillende omgevingen, bijvoorbeeld ontwikkeling, testen en productie. U kunt dezelfde logische app ook implementeren in verschillende Azure-regio's of ISE's, die strategieën voor herstel na noodherstel ondersteunen die gebruikmaken van [gekoppelde regio's.](../best-practices-availability-paired-regions.md)

Voor de failoverstrategie moeten uw logische apps en locaties voldoen aan deze vereisten:

* Het secundaire exemplaar van de logische app heeft toegang tot dezelfde apps, services en systemen als het primaire exemplaar van de logische app.

* Beide exemplaren van logische apps hebben hetzelfde hosttype. Beide exemplaren worden dus geïmplementeerd in regio's in azure met meerdere tenants wereldwijd, of beide exemplaren worden geïmplementeerd in ISE's, waardoor uw logische apps rechtstreeks toegang hebben tot resources in een virtueel Azure-netwerk. Zie Bedrijfscontinuïteit en herstel na noodherstel [(BCDR): gekoppelde](../best-practices-availability-paired-regions.md)Azure-regio's voor best practices en meer informatie over gekoppelde regio's voor BCDR.

  De primaire en secundaire locaties moeten bijvoorbeeld ISE's zijn wanneer de primaire logische app wordt uitgevoerd in een ISE en connectors met [ISE-versies, HTTP-acties](../connectors/managed.md#ise-connectors)om resources aan te roepen in het virtuele Azure-netwerk of beide. In dit scenario moet uw secundaire logische app ook een vergelijkbare installatie hebben op de secundaire locatie als de primaire logische app.

  > [!NOTE]
  > Voor geavanceerdere scenario's kunt u zowel Azure met meerdere tenants als een ISE als locaties combineren. Zorg er echter voor dat u rekening moet houden met de verschillen tussen de manier waarop logische apps worden uitgevoerd in een ISE en [azure met meerdere tenants.](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference)

* Als u ISE's gebruikt, moet u ervoor zorgen dat deze zijn uitschalen of voldoende capaciteit [hebben om](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) de belasting te verwerken.

#### <a name="example-multi-tenant-azure"></a>Voorbeeld: Azure met meerdere tenants

In dit voorbeeld ziet u primaire en secundaire instanties van logische apps, die voor dit scenario worden geïmplementeerd in afzonderlijke regio's in de globale Azure met meerdere tenants. Met één [Resource Manager definieert](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) u zowel exemplaren van logische apps als de afhankelijke resources die vereist zijn voor deze logische apps. Afzonderlijke parameterbestanden geven de configuratiewaarden op die moeten worden gebruikt voor elke implementatielocatie:

![Primaire en secundaire instanties van logische apps op afzonderlijke locaties](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations.png)

#### <a name="example-integration-service-environment"></a>Voorbeeld: Integratieserviceomgeving

In dit voorbeeld ziet u de vorige primaire en secundaire instanties van logische apps, maar geïmplementeerd op afzonderlijke ISE's. Met één Resource Manager definieert u zowel exemplaren van logische apps, de afhankelijke resources die vereist zijn voor deze logische apps als de implementatielocaties, als de ISE's. Afzonderlijke parameterbestanden definiëren de configuratiewaarden die moeten worden gebruikt voor implementatie op elke locatie:

![Primaire en secundaire logische apps op verschillende locaties](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations-ise.png)

<a name="resource-connections"></a>

## <a name="connections-to-resources"></a>Verbindingen met resources

Azure Logic Apps biedt ingebouwde triggers en acties, plus honderden beheerde connectors die uw logische app kan gebruiken om te werken met andere apps, services, systemen en andere resources, zoals Azure Storage-accounts, SQL Server-databases, [e-mailaccounts](../connectors/apis-list.md) voor werk of school, en meer. Als uw logische app toegang tot deze resources nodig heeft, maakt u verbindingen die toegang tot deze resources verifiëren. Elke verbinding is een afzonderlijke Azure-resource die zich op een specifieke locatie bevindt en niet kan worden gebruikt door resources op andere locaties.

Houd voor uw strategie voor herstel na noodherstel rekening met de locaties waar afhankelijke resources bestaan ten opzichte van uw logische app-instanties:

* Uw primaire exemplaar en afhankelijke resources bevinden zich op verschillende locaties. In dit geval kan uw secundaire exemplaar verbinding maken met dezelfde afhankelijke resources of eindpunten. U moet echter verbindingen maken die specifiek zijn voor uw secundaire exemplaar. Op die manier worden de verbindingen van uw secundaire niet meer beïnvloed als uw primaire locatie niet meer beschikbaar is.

  Stel bijvoorbeeld dat uw primaire logische app verbinding maakt met een externe service, zoals Salesforce. Meestal zijn de beschikbaarheid en locatie van de externe service onafhankelijk van de beschikbaarheid van uw logische app. In dit geval kan uw secundaire exemplaar verbinding maken met dezelfde service, maar moet deze een eigen verbinding hebben.

* Zowel uw primaire exemplaar als afhankelijke resources bevinden zich op dezelfde locatie. In dit geval moeten afhankelijke resources back-ups of gerepliceerde versies op een andere locatie hebben, zodat uw secundaire exemplaar nog steeds toegang heeft tot deze resources.

  Stel bijvoorbeeld dat uw primaire logische app verbinding maakt met een service die zich op dezelfde locatie of regio bevindt, bijvoorbeeld Azure SQL Database. Als deze hele regio niet meer beschikbaar is, is Azure SQL Database service in die regio waarschijnlijk ook niet beschikbaar. In dit geval wilt u dat uw secundaire exemplaar een gerepliceerde of back-updatabase gebruikt, samen met een afzonderlijke verbinding met die database.

<a name="on-premises-data-gateways"></a>

## <a name="on-premises-data-gateways"></a>On-premises gegevensgateways

Als uw logische app wordt uitgevoerd in Azure met meerdere tenants en toegang nodig heeft tot on-premises resources, zoals SQL Server-databases, moet u de [on-premises](../logic-apps/logic-apps-gateway-install.md) gegevensgateway op een lokale computer installeren. Vervolgens kunt u een gegevensgatewayresource maken in de Azure Portal zodat uw logische app de gateway kan gebruiken wanneer u een verbinding met de resource maakt.

De gegevensgatewayresource is gekoppeld aan een locatie of Azure-regio, net als uw logische app-resource. Zorg er in uw strategie voor herstel na noodherstel voor dat de gegevensgateway beschikbaar blijft voor gebruik door uw logische app. U kunt [hoge beschikbaarheid voor uw gateway inschakelen](../logic-apps/logic-apps-gateway-install.md#high-availability) wanneer u meerdere gatewayinstallaties hebt.

> [!NOTE]
> Als uw logische app wordt uitgevoerd in een ISE (Integration Service Environment) en alleen connectors met ISE-versie gebruikt voor on-premises gegevensbronnen, hebt u de gegevensgateway niet nodig omdat ISE-connectors directe toegang bieden tot de on-premises resource.
>
> Als er geen connector met ISE-versie beschikbaar is voor de on-premises resource die u wilt, kan uw logische app nog steeds de verbinding maken met behulp van een niet-ISE-connector, die wordt uitgevoerd in de globale Azure met meerdere tenants, niet uw ISE. Voor deze verbinding is echter de on-premises gegevensgateway vereist.

<a name="roles"></a>

## <a name="active-active-versus-active-passive-roles"></a>Actief-actief versus actief-passief-rollen

U kunt uw primaire en secundaire locaties zo instellen dat de instanties van uw logische app op deze locaties de volgende rollen kunnen spelen:

| Primaire en secundaire rol | Description |
|------------------------|-------------|
| *Actief-actief* | De primaire en secundaire instanties van logische apps op beide locaties verwerken aanvragen actief door een van deze patronen te volgen: <p><p>- *Load balancer:* u kunt beide exemplaren naar een eindpunt laten luisteren en zo nodig verkeer naar elk exemplaar laten balanceren. <p>- *Concurrerende consumenten:* u kunt beide exemplaren laten fungeren als concurrerende consumenten, zodat de exemplaren concurreren om berichten uit een wachtrij. Als het ene exemplaar uitvalt, neemt het andere exemplaar de workload over. |
| *Actief-passief* | Het primaire exemplaar van de logische app verwerkt actief de volledige workload, terwijl het secundaire exemplaar passief is (uitgeschakeld of inactief). De secundaire wacht op een signaal dat de primaire niet beschikbaar is of niet werkt vanwege een onderbreking of storing en neemt de workload over als het actieve exemplaar. |
| Combinatie | Sommige logische apps spelen een actief-actief-rol, terwijl andere logische apps een actief-passief-rol spelen. |
|||

<a name="active-active-examples"></a>

### <a name="active-active-examples"></a>Actief-actief-voorbeelden

Deze voorbeelden tonen de actief-actief-installatie waarbij beide exemplaren van logische apps actief aanvragen of berichten verwerken. Een ander systeem of een andere service distribueert de aanvragen of berichten tussen exemplaren, bijvoorbeeld een van deze opties:

* Een 'fysieke' load balancer, zoals hardware die verkeer routeert

* Een 'zachte' load balancer zoals [Azure Load Balancer](../load-balancer/load-balancer-overview.md) of [Azure API Management.](../api-management/api-management-key-concepts.md) Met API Management kunt u beleidsregels opgeven die bepalen hoe binnenkomende verkeer moet worden geseed. U kunt ook een service gebruiken die ondersteuning biedt voor het bijhouden van statussen, bijvoorbeeld [Azure Service Bus.](../service-bus-messaging/service-bus-messaging-overview.md)

  Hoewel dit voorbeeld voornamelijk een Azure Load Balancer laat zien, kunt u de optie gebruiken die het beste past bij de behoeften van uw scenario:

  !['Actief-actief'-installatie die gebruikmaakt van een load balancer of stateful service](./media/business-continuity-disaster-recovery-guidance/active-active-setup-load-balancer.png)

* Elk exemplaar van een logische app fungeert als een consument en beide exemplaren concurreren om berichten uit een wachtrij:

  !['Actief-actief'-installatie die gebruikmaakt van 'concurrerende consumenten'](./media/business-continuity-disaster-recovery-guidance/active-active-competing-consumers-pattern.png)

<a name="active-passive-examples"></a>

### <a name="active-passive-examples"></a>Voorbeelden van actief-passief

In dit voorbeeld ziet u de actief-passieve installatie waarbij het primaire exemplaar van de logische app op de ene locatie actief is, terwijl het secundaire exemplaar inactief blijft op een andere locatie. Als de primaire een onderbreking of fout ervaart, kunt u een operator een script laten uitvoeren dat de secundaire activeert om de workload op te nemen.

!['Actief-passief'-installatie die gebruikmaakt van 'concurrerende consumenten'](./media/business-continuity-disaster-recovery-guidance/active-passive-setup.png)

<a name="active-active-active-passive-examples"></a>

### <a name="combination-with-active-active-and-active-passive"></a>Combinatie met actief-actief en actief-passief

In dit voorbeeld ziet u een gecombineerde installatie waarbij de primaire locatie beide actieve exemplaren van logische apps heeft, terwijl de secundaire locatie actief-passieve logische app-exemplaren heeft. Als de primaire locatie een onderbreking of fout ervaart, kan de actieve logische app op de secundaire locatie, die al een gedeeltelijke werkbelasting afhandelt, de volledige workload overnemen.

* Op de primaire locatie luistert een actieve logische app naar een Azure Service Bus-wachtrij voor berichten, terwijl een andere actieve logische app controleert op e-mailberichten met behulp van een Office 365 Outlook-pollingtrigger.

* Op de secundaire locatie werkt een actieve logische app met de logische app op de primaire locatie door te luisteren naar berichten uit dezelfde Service Bus wachtrij. Ondertussen wacht een passieve inactieve logische app op stand-by om te controleren op e-mailberichten wanneer de primaire locatie niet meer beschikbaar *is,* maar is uitgeschakeld om te voorkomen dat e-mailberichten opnieuw worden gelezen.

![Combinatie 'Actief-passief' en 'actief-passief' die gebruikmaakt van terugkeertriggers](./media/business-continuity-disaster-recovery-guidance/combo-active-active-active-passive-setup.png)

<a name="state-history"></a>

## <a name="logic-app-state-and-history"></a>Status en geschiedenis van logische app

Wanneer uw logische app wordt geactiveerd en wordt uitgevoerd, wordt de status van de app opgeslagen op dezelfde locatie waar de app is gestart en kan deze niet worden overdraagbaar naar een andere locatie. Als er een fout of onderbreking is, worden alle werkstroomprocessen die worden uitgevoerd, stopgezet. Wanneer u een primaire en secundaire locatie hebt ingesteld, worden nieuwe werkstroom-exemplaren op de secundaire locatie uitgevoerd.

<a name="reduce-abandoned-in-progress"></a>

### <a name="reduce-abandoned-in-progress-instances"></a>Verlaten exemplaren die worden uitgevoerd verminderen

Als u het aantal verlaten werkstroomprocessen wilt minimaliseren, kunt u kiezen uit verschillende berichtpatronen die u kunt implementeren, bijvoorbeeld:

* [Probleem met routeringspatroon opgelost](/biztalk/esb-toolkit/message-routing-patterns#routing-slip)

  Dit bedrijfsberichtpatroon dat een bedrijfsproces splitst in kleinere fasen. Voor elke fase stelt u een logische app in die de workload voor die fase verwerkt. Om met elkaar te communiceren, gebruiken uw logische apps een asynchrone berichtenprotocol zoals Azure Service Bus wachtrijen of onderwerpen. Wanneer u een proces in kleinere fasen opsplitst, vermindert u het aantal bedrijfsprocessen dat kan vast komen te zitten in een mislukt exemplaar van een logische app. Zie Enterprise integration [patterns - Routinglip (Enterprise-integratiepatronen - Routeringsslip) voor](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RoutingTable.html)meer algemene informatie over dit patroon.

  In dit voorbeeld ziet u een routeringspatroon waarbij elke logische app een fase vertegenwoordigt en een Service Bus wachtrij gebruikt om te communiceren met de volgende logische app in het proces.

  ![Een bedrijfsproces splitsen in fasen die worden vertegenwoordigd door logische apps, die met elkaar communiceren met behulp van Azure Service Bus wachtrijen](./media/business-continuity-disaster-recovery-guidance/fixed-routing-slip-pattern.png)

  Als zowel primaire als secundaire instanties van logische apps op hun [](/azure/architecture/patterns/competing-consumers) locaties hetzelfde routeringspatroon volgen, kunt u het concurrerende consumentenpatroon implementeren door [actief-actief-rollen](#roles) in te stellen voor deze instanties.

* [Procesbeheerpatroon (broker)](https://www.enterpriseintegrationpatterns.com/patterns/messaging/ProcessManager.html)

* [Peek-lock zonder time-outpatroon](https://social.technet.microsoft.com/wiki/contents/articles/50022.azure-service-bus-how-to-peek-lock-a-message-from-queue-using-azure-logic-apps.aspx)

<a name="access-trigger-runs-history"></a>

### <a name="access-to-trigger-and-runs-history"></a>Toegang tot trigger- en runs-geschiedenis

Als u meer informatie wilt over de eerdere werkstroomuitvoeringen van uw logische app, kunt u de trigger- en uitvoeringsgeschiedenis van de app bekijken. De uitvoeringsgeschiedenis van een logische app wordt opgeslagen op dezelfde locatie of regio waar die logische app werd uitgevoerd. Dit betekent dat u deze geschiedenis niet naar een andere locatie kunt migreren. Als uw primaire exemplaar een fout maakt naar een secundair exemplaar, hebt u alleen toegang tot de trigger van elk exemplaar en de geschiedenis van de runs op de respectieve locaties waar deze exemplaren zijn uitgevoerd. U kunt echter locatieagnostische informatie krijgen over de geschiedenis van uw logische app door uw logische apps in te stellen voor het verzenden van diagnostische gebeurtenissen naar een Azure Log Analytics-werkruimte. Vervolgens kunt u de status en geschiedenis bekijken van logische apps die op meerdere locaties worden uitgevoerd.

<a name="trigger-types-guidance"></a>

## <a name="trigger-type-guidance"></a>Richtlijnen voor triggertype

Het triggertype dat u in uw logische apps gebruikt, bepaalt uw opties voor het instellen van logische apps op verschillende locaties in uw strategie voor herstel na noodherstel. Dit zijn de beschikbare triggertypen die u kunt gebruiken in logische apps:

* [Trigger terugkeerpatroon](#recurrence-trigger)
* [Poll-trigger](#polling-trigger)
* [Aanvraagtrigger](#request-trigger)
* [Webhooktrigger](#webhook-trigger)

<a name="recurrence-trigger"></a>

### <a name="recurrence-trigger"></a>Trigger terugkeerpatroon

De **trigger Terugkeerpatroon** is onafhankelijk van een specifieke service of eindpunt en wordt alleen op basis van een opgegeven planning en geen andere criteria, bijvoorbeeld:

* Een vaste frequentie en interval, zoals elke 10 minuten
* Een geavanceerdere planning, zoals de laatste maandag van elke maand om 17:00 uur

Wanneer uw logische app begint met een terugkeerpatroontrigger, moet u uw primaire en secundaire instanties van logische apps instellen met de [actief-passieve rollen](#roles). Als u  de beoogde hersteltijd (RTO) wilt verminderen, die verwijst naar de doelduur voor het herstellen van een bedrijfsproces na een onderbreking of nood, kunt u uw instanties van logische apps instellen met een combinatie van [actief-passief-rollen](#roles) en [passief-actieve rollen.](#roles) In deze installatie splitst u de planning over verschillende locaties.

Stel bijvoorbeeld dat u een logische app hebt die elke 10 minuten moet worden uitgevoerd. U kunt uw logische apps en locaties zo instellen dat als de primaire locatie niet meer beschikbaar is, de secundaire locatie het werk kan overnemen:

![Combinatie 'Actief-passief' en 'passief-actief' die gebruikmaakt van terugkeertriggers](./media/business-continuity-disaster-recovery-guidance/combo-active-passive-passive-active-setup.png)

* Stel op de primaire locatie [actief-passief-rollen in](#roles) voor deze logische apps:

  * Voor  de actieve logische app stelt u de trigger Terugkeerpatroon in om boven aan het uur te starten en elke 20 minuten te herhalen, bijvoorbeeld om 9:00 uur, 9:20 uur, bijvoorbeeld.

  * Voor  de passieve uitgeschakelde logische app stelt u de trigger Terugkeerpatroon in op hetzelfde schema, maar begint u om 10 minuten na het uur en herhaalt u deze om de 20 minuten, bijvoorbeeld om 9:10 uur, 9:30 uur, bijvoorbeeld.

* Stel op de secundaire locatie [passief-actief in](#roles) voor deze logische apps:

  * Stel  voor de passieve, uitgeschakelde logische app de trigger Terugkeerpatroon in op hetzelfde schema als de actieve logische app op de primaire locatie, die zich boven aan het uur bevindt en herhaal deze om de 20 minuten, bijvoorbeeld om 9:00 uur, 9:10 uur, en meer.

  * Voor  de actieve logische app stelt u de trigger Terugkeerpatroon in op hetzelfde schema als de passieve logische app op de primaire locatie. Dit is om te beginnen bij 10 minuten na het uur en elke 20 minuten te herhalen, bijvoorbeeld om 9:10 uur, 9:20 uur, bijvoorbeeld.

Als er nu een verstorende gebeurtenis op de primaire locatie gebeurt, activeert u de passieve logische app op de alternatieve locatie. Als het vinden van de fout tijd kost, beperkt deze instelling het aantal gemiste terugkeerpatroon tijdens die vertraging.

<a name="polling-trigger"></a>

### <a name="polling-trigger"></a>Poll-trigger

Als u regelmatig wilt controleren of nieuwe gegevens voor verwerking beschikbaar zijn via een specifieke service of een specifiek eindpunt, kan uw logische app een *poll-trigger* gebruiken die de service of het eindpunt herhaaldelijk aanroept op basis van een vast terugkeerschema. De gegevens die de service of het eindpunt biedt, kunnen een van de volgende typen hebben:

* Statische gegevens, waarin gegevens worden beschreven die altijd beschikbaar zijn om te lezen
* Vluchtige gegevens, waarin gegevens worden beschreven die na het lezen niet meer beschikbaar zijn

Om te voorkomen dat dezelfde gegevens herhaaldelijk worden gelezen, moet uw logische app onthouden welke gegevens eerder zijn gelezen door de status te onderhouden aan de clientzijde of aan de server-, service- of systeemzijde.

* Logische apps die met de status aan de clientzijde werken, gebruiken triggers die de status kunnen behouden.

  Een trigger die bijvoorbeeld een nieuw bericht uit een postvak IN voor e-mail leest, vereist dat de trigger het laatst gelezen bericht kan onthouden. Op die manier start de trigger de logische app alleen wanneer het volgende ongelezen bericht binnenkomt.

* Logische apps die met de status van de server, de service of het systeem werken, gebruiken eigenschapswaarden of -instellingen die zich aan de server-, service- of systeemzijde hebben.

  Een trigger op basis van een query die bijvoorbeeld een rij uit een database leest, vereist dat de rij een kolom bevat die `isRead` is ingesteld op `FALSE` . Telkens als de trigger een rij leest, werkt de logische app die rij bij door de kolom te `isRead` wijzigen van `FALSE` in `TRUE` .

  Deze methode aan de serverzijde werkt op dezelfde manier voor Service Bus wachtrijen of onderwerpen met semantiek voor wachtrijen waarbij een trigger een bericht kan lezen en vergrendelen terwijl het bericht door de logische app wordt verwerkt. Wanneer de logische app klaar is met verwerken, verwijdert de trigger het bericht uit de wachtrij of het onderwerp.

Wanneer u de primaire en secundaire exemplaren van uw logische app in het perspectief van herstel na noodherstel in stelt, moet u rekening houden met dit gedrag op basis van het feit of uw logische app de status bij houdt aan de clientzijde of aan de serverzijde:

* Voor een logische app die met de status aan de clientzijde werkt, moet u ervoor zorgen dat uw logische app niet meer dan één keer hetzelfde bericht leest. Slechts één locatie kan op een bepaald moment een actief exemplaar van een logische app hebben. Zorg ervoor dat het exemplaar van de logische app op de alternatieve locatie inactief of uitgeschakeld is totdat de primaire instantie een uitvalt naar de alternatieve locatie.

  De Outlook-trigger van Office 365 onderhoudt bijvoorbeeld de status aan de clientzijde en houdt de tijdstempel bij voor de meest recent gelezen e-mail om te voorkomen dat een duplicaat wordt gelezen.

* Voor een logische app die met de status aan de serverzijde werkt, kunt u uw exemplaren van logische apps zo instellen dat ze [actief-actief-rollen](#roles) spelen waarbij ze als concurrerende consumenten werken of als [actief-passief-rollen](#roles) waarbij het alternatieve exemplaar wacht tot de primaire instantie een uitvalt naar de alternatieve locatie.

  Het lezen vanuit een berichtenwachtrij, zoals een Azure Service Bus-wachtrij, maakt bijvoorbeeld gebruik van de status aan de serverzijde, omdat de wachtrijservice berichten vergrendeld houdt om te voorkomen dat andere clients dezelfde berichten kunnen lezen.

  > [!NOTE]
  > Als uw logische app berichten in een specifieke volgorde moet lezen, bijvoorbeeld vanuit een Service Bus-wachtrij, kunt u het concurrerende consumentenpatroon gebruiken, maar alleen in combinatie met Service Bus-sessies, ook wel bekend als het [ *sequentiële*](/azure/architecture/patterns/sequential-convoy)patroon . Anders moet u de exemplaren van uw logische app instellen met de actief-passieve rollen.

<a name="request-trigger"></a>

### <a name="request-trigger"></a>Aanvraagtrigger

De **aanvraagtrigger** maakt uw logische app aanroepbaar vanuit andere apps, services en systemen en wordt doorgaans gebruikt om deze mogelijkheden te bieden:

* Een directe REST API voor uw logische app die anderen kunnen aanroepen

  Gebruik bijvoorbeeld de trigger Aanvraag om uw logische app te starten, zodat andere logische apps de trigger kunnen aanroepen met behulp van de actie Werkstroom aanroepen **- Logic Apps** roepen.

* Een [webhook- of](#webhook-trigger) callback-mechanisme voor uw logische app

* Een manier waarop u handmatig gebruikersbewerkingen of routines kunt uitvoeren om uw logische app aan te roepen, bijvoorbeeld door een PowerShell-script te gebruiken waarmee een specifieke taak wordt uitgevoerd

Vanuit het perspectief van herstel na noodherstel is de aanvraagtrigger een passieve ontvanger omdat de logische app geen werk doet en wacht tot een andere service of systeem de trigger expliciet aanroept. Als passief eindpunt kunt u uw primaire en secundaire exemplaren op de volgende manieren instellen:

* [Actief-actief:](#roles)beide exemplaren verwerken actief aanvragen of aanroepen. De aanroeper of router verdeelt het verkeer over deze exemplaren.

* [Actief-passief:](#roles)alleen de primaire instantie is actief en verwerkt al het werk, terwijl de secundaire instantie wacht tot de primaire onderbreking of fout is. De aanroeper of router bepaalt wanneer het secundaire exemplaar moet worden aanroepen.

Als een aanbevolen architectuur kunt u Azure API Management als proxy voor de logische apps die gebruikmaken van aanvraagtriggers. API Management biedt [ingebouwde cross-regional tolerantie](../api-management/api-management-howto-deploy-multi-region.md)en de mogelijkheid om verkeer om te brengen naar meerdere eindpunten.

<a name="webhook-trigger"></a>

### <a name="webhook-trigger"></a>Webhooktrigger

Een *webhooktrigger* biedt uw logische app de mogelijkheid om zich te abonneren op een service door een *callback-URL door* te geven aan die service. Uw logische app kan vervolgens luisteren en wachten op een specifieke gebeurtenis op dat service-eindpunt. Wanneer de gebeurtenis zich voordeed, roept de service de webhooktrigger aan met behulp van de callback-URL, waarmee vervolgens de logische app wordt uitgevoerd. Wanneer deze functie is ingeschakeld, abonneert de webhooktrigger zich op de service. Als deze is uitgeschakeld, wordt de trigger afgemeld voor de service.

Stel vanuit het perspectief van herstel na noodherstel primaire en secundaire exemplaren in die gebruikmaken van webhooktriggers om actief-passief-rollen uit te spelen, omdat slechts één exemplaar gebeurtenissen of berichten van het geabonneerde eindpunt mag ontvangen.

## <a name="assess-primary-instance-health"></a>De status van het primaire exemplaar beoordelen

Uw strategie voor herstel na noodherstel werkt alleen als uw oplossing manieren heeft om deze taken uit te voeren:

* [De beschikbaarheid van het primaire exemplaar controleren](#check-primary-availability)
* [De status van het primaire exemplaar bewaken](#monitor-primary-health)
* [Het secundaire exemplaar activeren](#activate-secondary)

In deze sectie wordt één oplossing beschreven die u direct kunt gebruiken of als basis voor uw eigen ontwerp. Hier is een visueel overzicht op hoog niveau voor deze oplossing:

![Een logische watchdog-app maken waarmee een logische app voor statuscontrole op de primaire locatie wordt bewaakt](./media/business-continuity-disaster-recovery-guidance/check-location-health-watchdog.png)

<a name="check-primary-availability"></a>

### <a name="check-primary-instance-availability"></a>Beschikbaarheid van primaire exemplaren controleren

Als u wilt bepalen of het primaire exemplaar beschikbaar is, wordt uitgevoerd en werkt, kunt u een logische app voor statuscontrole maken die zich op dezelfde locatie bevindt als het primaire exemplaar. Vervolgens kunt u deze statuscontrole-app aanroepen vanaf een alternatieve locatie. Als de statuscontrole-app reageert, is de onderliggende infrastructuur voor de Azure Logic Apps-service in die regio beschikbaar en werkt deze. Als de statuscontrole-app niet reageert, kunt u ervan uitgaan dat de locatie niet meer in orde is.

Voor deze taak maakt u een eenvoudige logische app voor statuscontrole waarmee deze taken worden uitgevoerd:

1. Ontvangt een aanroep van de watchdog-app met behulp van de aanvraagtrigger.

1. Reageer met een status die aangeeft of de gecontroleerd logische app nog steeds werkt met behulp van de actie Antwoord.

   > [!IMPORTANT]
   > De logische app voor statuscontrole moet een actie Antwoord gebruiken, zodat de app synchroon reageert, niet asynchroon.

1. Als u verder wilt bepalen of de primaire locatie in orde is, kunt u desgewenst rekening houden met de status van andere services die communiceren met de doellogica-app op deze locatie. Vouw de logische app voor statuscontrole uit om ook de status van deze andere services te beoordelen.

<a name="monitor-primary-health"></a>

### <a name="create-a-watchdog-logic-app"></a>Een logische watchdog-app maken

Als u de status van het primaire exemplaar wilt bewaken en de logische app voor statuscontrole wilt aanroepen, maakt u een logische watchdog-app op een *alternatieve locatie.* U kunt bijvoorbeeld de logische watchdog-app zo instellen dat als het aanroepen van de statuscontrolelogica mislukt, de watchdog een waarschuwing kan verzenden naar uw operationele team, zodat deze de fout kan onderzoeken en waarom het primaire exemplaar niet reageert.

> [!IMPORTANT]
> Zorg ervoor dat uw logische watchdog-app zich op een andere locatie bevindt *dan de primaire locatie*. Als de Logic Apps-service op de primaire locatie problemen ervaart, wordt uw logische watchdog-app mogelijk niet uitgevoerd.

Voor deze taak maakt u op de secundaire locatie een logische watchdog-app waarmee deze taken worden uitgevoerd:

1. Wordt uitgevoerd op basis van een vast of gepland terugkeerpatroon met behulp van de trigger Terugkeerpatroon.

   U kunt het terugkeerpatroon instellen op een waarde die lager is dan het tolerantieniveau voor uw RTO (Recovery Time Objective).

1. Roep de logische app voor statuscontrole aan op de primaire locatie met behulp van de HTTP-actie, bijvoorbeeld:

<a name="activate-secondary"></a>

### <a name="activate-your-secondary-instance"></a>Uw secundaire exemplaar activeren

Als u het secundaire exemplaar automatisch wilt activeren, kunt u een logische app maken die de beheer-API aanroept, zoals de [Azure Resource Manager-connector,](/connectors/arm/) om de juiste logische apps op de secundaire locatie te activeren. U kunt uw watchdog-app uitbreiden om deze logische activerings-app aan te roepen nadat een bepaald aantal fouten zich voordeed.

<a name="collect-diagnostic-data"></a>

## <a name="collect-diagnostic-data"></a>Diagnostische gegevens verzamelen

U kunt logboekregistratie instellen voor uw logische app-runs en de resulterende diagnostische gegevens verzenden naar services zoals Azure Storage, Azure Event Hubs en Azure Log Analytics voor verdere verwerking en verwerking.

* Als u deze gegevens wilt gebruiken met Azure Log Analytics, kunt u de gegevens beschikbaar maken  voor zowel de primaire als de secundaire locatie door de diagnostische instellingen van uw logische app in te stellen en de gegevens naar meerdere Log Analytics-werkruimten te verzenden. Zie Set up Azure Monitor logs and collect diagnostics data for Azure Logic Apps (Logboeken instellen en diagnostische gegevens verzamelen voor [Azure Logic Apps) voor meer Azure Logic Apps.](../logic-apps/monitor-logic-apps-log-analytics.md)

* Als u de gegevens naar Azure Storage of Azure Event Hubs wilt verzenden, kunt u de gegevens beschikbaar maken voor zowel de primaire als de secundaire locatie door geo-redundantie in te stellen. Raadpleeg deze artikelen voor meer informatie:<p>

  * [Azure Blob Storage en failover van account herstellen](../storage/common/storage-disaster-recovery-guidance.md)
  * [Azure Event Hubs geo-noodherstel](../event-hubs/event-hubs-geo-dr.md)

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van tolerantie voor Azure](/azure/architecture/framework/resiliency/overview)
* [Controlelijst voor tolerantie voor specifieke Azure-services](/azure/architecture/checklist/resiliency-per-service)
* [Gegevensbeheer voor tolerantie in Azure](/azure/architecture/framework/resiliency/data-management)
* [Back-up en herstel na noodherstel voor Azure-toepassingen](/azure/architecture/framework/resiliency/backup-and-recovery)
* [Herstel na een serviceonderbreking in de gehele regio](/azure/architecture/resiliency/recovery-loss-azure-region)
* [Microsoft Service Level Agreements (SLA's) voor Azure-services](https://azure.microsoft.com/support/legal/sla/)

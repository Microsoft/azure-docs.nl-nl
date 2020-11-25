---
title: Overzicht van architectuur-Azure Active Directory | Microsoft Docs
description: Meer informatie over wat een Azure Active Directory Tenant is en hoe u Azure beheert met behulp van Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 476cf8013f5dc8b5d54efb573cf305d81fc690b1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996709"
---
# <a name="what-is-the-azure-active-directory-architecture"></a>Wat is de Azure Active Directory architectuur?

Met Azure AD (Azure Active Directory) kunt u veilig de toegang tot Azure-services en -resources beheren voor uw gebruikers. Azure AD omvat een volledige suite met mogelijkheden voor identiteitsbeheer. Zie [Wat is Azure Active Directory?](active-directory-whatis.md) voor meer informatie over de functies van Azure AD.

Met Azure AD kunt u gebruikers en groepen maken en beheren, en machtigingen inschakelen om toegang tot bedrijfsresources te verlenen of te weigeren. Zie [The fundamentals of Azure identity management](active-directory-whatis.md) (De grondbeginselen van Azure-identiteitsbeheer) voor meer informatie over identiteitsbeheer.

## <a name="azure-ad-architecture"></a>Azure AD-architectuur

De geografisch gedistribueerde architectuur van Azure AD combineert uitgebreide bewaking, geautomatiseerde herroute ring, failover en herstel mogelijkheden, waarmee klanten de beschik baarheid en prestaties van de hele onderneming kunnen leveren.

In dit artikel worden de volgende elementen van de architectuur besproken:

*   Servicearchitectuurontwerp
*   Schaalbaarheid
*   Continue beschikbaarheid
*   Datacenters

### <a name="service-architecture-design"></a>Servicearchitectuurontwerp

De meest voorkomende manier om een toegankelijk en bruikbaar, gegevens systeem te maken, is via onafhankelijke bouw stenen of schaal eenheden. Schaal eenheden van de Azure AD-gegevenslaag worden *partities* genoemd.

De gegevenslaag heeft meerdere front-end-services die mogelijkheden bieden voor lezen/schrijven. In het onderstaande diagram ziet u hoe de onderdelen van een partitie met één map worden geleverd in geografisch verspreide data centers.

  ![Diagram met één mappartitie](./media/active-directory-architecture/active-directory-architecture.png)

De onderdelen van Azure AD-architectuur omvatten een primaire replica en secundaire replica's.

#### <a name="primary-replica"></a>Primaire replica

De *primaire replica* ontvangt alle *schrijfbewerkingen* voor de partitie waarbij deze hoort. Alle schrijfbewerkingen worden onmiddellijk gerepliceerd naar een secundaire replica in een ander datacenter, voordat de aanroeper een melding van slagen ontvangt. Op deze manier wordt de geografisch redundante duurzaamheid van schrijfbewerkingen verzekerd.

#### <a name="secondary-replicas"></a>Secundaire replica's

Alle Active Directory- *Lees bewerkingen* worden uitgevoerd vanuit *secundaire replica's*, die zich bevinden in data centers die zich fysiek in verschillende geografs. Er zijn veel secundaire replica's, omdat gegevens asynchroon worden gerepliceerd. Directory-Lees bewerkingen, zoals verificatie aanvragen, worden verwerkt vanuit data centers die zich dicht bij klanten bevinden. De secundaire replica's zijn verantwoordelijk voor de schaalbaarheid van leesbewerkingen.

### <a name="scalability"></a>Schaalbaarheid

Schaalbaarheid is de mogelijkheid van een service om uit te breiden en zo te voldoen aan groeiende prestatievereisten. Schaalbaarheid van schrijfbewerkingen wordt bereikt door de gegevens te partitioneren. Schaalbaarheid van leesbewerkingen wordt bereikt door gegevens uit één partitie te repliceren naar meerdere secundaire replica's over de hele wereld.

Aanvragen van Directory-toepassingen worden doorgestuurd naar het Data Center waar ze zich het dichtst in de buurt van bevinden. Schrijfbewerkingen worden transparant omgeleid naar de primaire replica voor lees-/schrijfconsistentie. Secundaire replica's breiden de schaal van partities aanzienlijk uit, omdat in de mappen doorgaans leesbewerkingen worden afgehandeld.

Directory-toepassingen maken verbinding met de dichtstbijzijnde datacenters. Deze verbinding verbetert de prestaties en kan daarom worden uitgeschaald. Omdat een mappartitie meerdere secundaire replica's kan hebben, kunnen deze secundaire replica's dichter bij Directory-clients worden geplaatst. Alleen interne Directory-serviceonderdelen die veel schrijfbewerkingen afhandelen, zijn rechtstreeks gericht op de actieve primaire replica.

### <a name="continuous-availability"></a>Continue beschikbaarheid

Beschikbaarheid (of bedrijfstijd) definieert de mogelijkheid van een systeem om ononderbroken actief te zijn. De sleutel tot de hoge Beschik baarheid van Azure AD is dat de services snel verkeer kunnen verplaatsen over meerdere geografisch gedistribueerde data centers. Elk Data Center is onafhankelijk, waarmee niet-gerelateerde fout modi worden ingeschakeld. Azure AD heeft via dit ontwerp voor hoge Beschik baarheid geen downtime voor onderhouds activiteiten.

Het ontwerp van Azure AD-partities is vereenvoudigd in vergelijking met het ontwerp van de bedrijfs advertentie, met behulp van een model met één model dat een zorgvuldig georganiseerd en deterministisch primaire failover voor het uitvoeren van de replica heeft.

#### <a name="fault-tolerance"></a>Fouttolerantie

Een systeem is beschikbaarder als het tolerant is voor fouten in hardware, software en het netwerk. Voor elke mappartitie bestaat een maximaal beschikbare hoofdreplica: de primaire replica. Op deze replica worden alleen schrijfbewerkingen naar de partitie uitgevoerd. Deze replica wordt voortdurend en nauwlettend gecontroleerd. Indien er een fout wordt gedetecteerd, kunnen schrijfbewerkingen onmiddellijk worden verplaatst naar een andere replica (die dan de nieuwe primaire replica wordt). Tijdens de failover kan er een verlies van schrijfbeschikbaarheid optreden. Dit duurt meestal maar 1-2 minuten. De leesbeschikbaarheid wordt gedurende deze tijd niet beïnvloed.

Leesbewerkingen (die vele malen vaker voorkomen dan schrijfbewerkingen) worden alleen opgeslagen in secundaire replica's. Aangezien secundaire replica's idempotent zijn, kan het verlies van een van de replica's in een bepaalde partitie eenvoudig worden gecompenseerd door de leesbewerkingen naar een andere replica te leiden. Meestal is dit dan een replica in hetzelfde datacenter.

#### <a name="data-durability"></a>Duurzaamheid van gegevens

Er wordt een schrijf bewerking naar ten minste twee data centers doorgevoerd voordat deze wordt bevestigd. Dit gebeurt door eerst de schrijf bewerking op de primaire uit te voeren en vervolgens onmiddellijk de schrijf bewerking naar ten minste één ander Data Center te repliceren. Met deze schrijf actie zorgt u ervoor dat het Data Center dat als host optreedt van de primaire computer geen gegevens verlies oplevert.

Azure AD bewaart een [beoogde herstel tijd (RTO)](https://en.wikipedia.org/wiki/Recovery_time_objective) om geen gegevens over failovers te verliezen. Dit omvat:

* Token uitgifte en Directory-Lees bewerkingen
* Alleen ongeveer 5 minuten RTO voor het schrijven van mappen toestaan

### <a name="datacenters"></a>Datacenters

Azure AD-replica's worden opgeslagen in datacenters over de hele wereld. Zie [Azure Global Infrastructure](https://azure.microsoft.com/global-infrastructure/)(Engelstalig) voor meer informatie.

Azure AD werkt in data centers met de volgende kenmerken:

* Verificatie, Graph en andere AD-Services bevinden zich achter de Gateway Service. De taakverdeling van deze services wordt via de gateway beheerd. Failover wordt automatisch uitgevoerd als er beschadigde servers worden gedetecteerd met behulp van transactionele status tests. Op basis van deze status tests stuurt de gateway dynamisch verkeer naar gezonde data centers.
* Voor *Lees bewerkingen* heeft de Directory secundaire replica's en bijbehorende front-end-services in een actief-actief configuratie in meerdere data centers. Als er een fout optreedt in een volledig Data Center, wordt verkeer automatisch doorgestuurd naar een ander Data Center.
 * Voor *schrijf bewerkingen* zal de Directory failover uitvoeren via de primaire (hoofd) replica in data centers via gepland (nieuwe primaire server wordt gesynchroniseerd met oude primaire) of procedures voor nood failover. De duurzaamheid van gegevens wordt bereikt door een door voering te repliceren naar ten minste twee data centers.

#### <a name="data-consistency"></a>Gegevensconsistentie

Het Directory model is een van de mogelijke consistenties. Een typisch probleem met gedistribueerde asynchroon replicerende systemen is dat de gegevens die worden geretourneerd door een ' bepaalde ' replica mogelijk niet up-to-date zijn. 

Met behulp van een secundaire replica biedt Azure AD lees-/schrijfconsistentie voor toepassingen door schrijfbewerkingen naar de primaire replica te leiden en ze tegelijkertijd terug te halen naar de secundaire replica.

Het schrijven van toepassingen met behulp van de Microsoft Graph-API van Azure AD is afgeleid van het bijhouden van affiniteit in een Directory replica voor consistentie van het Lees-en schrijf programma. De Microsoft Graph API-service onderhoudt een logische sessie die affiniteit heeft met een secundaire replica die wordt gebruikt voor lees bewerkingen. affiniteit wordt vastgelegd in een ' replica token ' dat door de service in de cache wordt opgeslagen met behulp van een gedistribueerde cache in het Data Center van de secundaire replica. Dit token wordt vervolgens gebruikt voor verdere bewerkingen in dezelfde logische sessie. Om dezelfde logische sessie te blijven gebruiken, moeten volgende aanvragen worden doorgestuurd naar hetzelfde Azure AD-Data Center. Het is niet mogelijk om door te gaan met een logische sessie als de aanvragen van de Directory-client worden doorgestuurd naar meerdere Azure AD-data centers. Als dit gebeurt, heeft de client meerdere logische sessies met een onafhankelijke consistenties voor lezen/schrijven.

 >[!NOTE]
 >Schrijfbewerkingen worden onmiddellijk gerepliceerd naar de secundaire replica waarop de leesbewerkingen van de logische sessie zijn weggeschreven.

#### <a name="backup-protection"></a>Back-upbeveiliging

De map implementeert voorlopig verwijderen, in plaats van definitief, voor gebruikers en tenants, wat eenvoudig herstel mogelijk maakt wanneer items per ongeluk worden verwijderd door een klant. Als uw Tenant beheerder per ongeluk gebruikers verwijdert, kunnen ze de verwijderde gebruikers eenvoudig ongedaan maken en herstellen.

Met Azure AD worden dagelijkse back-ups van alle gegevens geïmplementeerd. Daarom kunnen gegevens bindend worden teruggezet in het geval van logische verwijderingen of beschadigingen. De gegevenslaag maakt gebruik van code voor het corrigeren van fouten, zodat deze kan controleren op fouten en bepaalde typen schijf fouten automatisch corrigeert.

#### <a name="metrics-and-monitors"></a>Metrische gegevens en controles

Voor het uitvoeren van een service met een hoge beschikbaarheid zijn uitstekende mogelijkheden voor metrische gegevens en controle vereist. Met Azure AD worden belangrijke metrische gegevens met betrekking tot de status van de service voortdurend geanalyseerd voor elk van de services. Er is ook doorlopend ontwikkelen en afstemmen van metrische gegevens en bewakings-en waarschuwings functies voor elk scenario, binnen elke Azure AD-service en in alle services.

Als een Azure AD-service niet werkt zoals verwacht, wordt er onmiddellijk actie ondernomen om de functionaliteit zo snel mogelijk te herstellen. De belangrijkste metrische gegevens van Azure AD sporen zijn hoe snel problemen met live site kunnen worden gedetecteerd en verminderd voor klanten. We investeren veel in controle en waarschuwingen om de detectietijd (TTD-doel: < 5 minuten) te minimaliseren en in operationele paraatheid om de hersteltijd (TTM-doel: < 30 minuten) zo kort mogelijk te houden.

#### <a name="secure-operations"></a>Veilige bewerkingen

Het gebruik van operationele besturings elementen zoals multi-factor Authentication (MFA) voor elke bewerking, en het controleren van alle bewerkingen. Daarnaast kunt u met behulp van een just-in-time-uitbrei ding systeem de benodigde tijdelijke toegang verlenen voor elke operationele taak-on-demand. Zie [De vertrouwde cloud](https://azure.microsoft.com/support/trust-center) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

[Ontwikkelaars handleiding Azure Active Directory](../develop/index.yml)
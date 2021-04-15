---
title: Automatische failover-groepen
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Met groepen voor automatische failover kunt u replicatie en automatische/gecoördineerde failover van een groep databases op een server of alle databases in een beheerd exemplaar beheren.
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 03/26/2021
ms.openlocfilehash: c0149dbb5f17af87229d951cf744c285e54835af
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375947"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>Groepen voor automatische failover gebruiken om transparante en gecoördineerde failover van meerdere databases mogelijk te maken
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Met de functie groepen voor automatische failover kunt u de replicatie en failover van een groep databases op een server of alle databases in een beheerd exemplaar naar een andere regio beheren. Het is een declaratieve abstractie boven op de bestaande functie voor actieve [geo-replicatie,](active-geo-replication-overview.md) ontworpen om de implementatie en het beheer van geo-gerepliceerde databases op schaal te vereenvoudigen. U kunt failover handmatig initiëren of u kunt deze delegeren aan de Azure-service op basis van een door de gebruiker gedefinieerd beleid. Met de laatste optie kunt u automatisch meerdere gerelateerde databases in een secundaire regio herstellen na een onherstelbare fout of andere ongeplande gebeurtenis die resulteert in volledig of gedeeltelijk verlies van de beschikbaarheid van SQL Database of SQL Managed Instance in de primaire regio. Een failovergroep kan een of meer databases bevatten, die doorgaans door dezelfde toepassing worden gebruikt. Daarnaast kunt u de leesbare secundaire databases gebruiken om alleen-lezen querywerkbelastingen te offloaden. Omdat voor groepen voor automatische failover meerdere databases zijn betrokken, moeten deze databases worden geconfigureerd op de primaire server. Groepen voor automatische failover ondersteunen replicatie van alle databases in de groep naar slechts één secundaire server of instantie in een andere regio.

> [!NOTE]
> Als u meerdere Azure SQL Database in dezelfde of verschillende regio's wilt, gebruikt u [actieve geo-replicatie.](active-geo-replication-overview.md)

Wanneer u gebruik maakt van groepen voor automatische failover met beleid voor automatische failover, leidt elke storing die van invloed is op een of meer databases in de groep tot automatische failover. Dit zijn doorgaans incidenten die niet zelf kunnen worden beperkt door de ingebouwde automatische bewerkingen voor hoge beschikbaarheid. Voorbeelden van failovertriggers zijn een incident dat wordt veroorzaakt doordat een SQL Database-tenantring of besturingsring niet actief is vanwege een kernelgeheugenlek in het besturingssysteem op verschillende rekenknooppunten, of een incident dat wordt veroorzaakt doordat een of meer tenantringen uitvallen omdat een verkeerde netwerkkabel is geknipt tijdens het buiten gebruik stellen van routinematige hardware.  Zie Hoge beschikbaarheid SQL Database [voor meer informatie.](high-availability-sla.md)

Bovendien bieden groepen voor automatische failover eindpunten voor lezen/schrijven en alleen-lezen listeners die ongewijzigd blijven tijdens failovers. Of u nu handmatige of automatische failoveractivering gebruikt, failover schakelt alle secundaire databases in de groep over naar de primaire database. Nadat de failover van de database is voltooid, wordt de DNS-record automatisch bijgewerkt om de eindpunten om te leiden naar de nieuwe regio. Zie Overzicht van bedrijfscontinuïteit voor de specifieke RPO- en [RTO-gegevens.](business-continuity-high-availability-disaster-recover-hadr-overview.md)

Wanneer u groepen voor automatische failover met beleid voor automatische failover gebruikt, leidt elke storing die van invloed is op databases op een server of beheerd exemplaar tot automatische failover. U kunt een groep voor automatische failover beheren met behulp van:

- [Azure-portal](geo-distributed-application-configure-tutorial.md)
- [Azure CLI: Failovergroep](scripts/add-database-to-failover-group-cli.md)
- [PowerShell: failovergroep](scripts/add-database-to-failover-group-powershell.md)
- [REST API: Failovergroep](/rest/api/sql/failovergroups)

Na een failover moet u ervoor zorgen dat de verificatievereisten voor uw database en server of instantie zijn geconfigureerd op de nieuwe primaire server. Zie Beveiliging na SQL Database [na noodherstel voor meer informatie.](active-geo-replication-security-configure.md)

Voor echte bedrijfscontinuïteit maakt het toevoegen van database-redundantie tussen datacenters slechts deel uit van de oplossing. Het end-to-end herstellen van een toepassing (service) na een onherstelbare fout vereist herstel van alle onderdelen waar de service deel van vormt en alle afhankelijke services. Voorbeelden van deze onderdelen zijn de clientsoftware (bijvoorbeeld een browser met een aangepaste JavaScript), webfront-ends, opslag en DNS. Het is essentieel dat alle onderdelen bestand zijn tegen dezelfde fouten en beschikbaar komen binnen de RTO (Recovery Time Objective) van uw toepassing. Daarom moet u alle afhankelijke services identificeren en inzicht krijgen in de garanties en mogelijkheden die ze bieden. Vervolgens moet u voldoende stappen ondernemen om ervoor te zorgen dat uw service functioneert tijdens de failover van de services waarvan deze afhankelijk is. Zie Cloudoplossingen ontwerpen voor herstel na noodherstel met actieve geo-replicatie voor meer informatie over het ontwerpen van [oplossingen voor herstel na noodherstel.](designing-cloud-solutions-for-disaster-recovery.md)

## <a name="terminology-and-capabilities"></a>Terminologie en mogelijkheden

- **Failovergroep (VOOR)**

  Een failovergroep is een benoemde groep databases die wordt beheerd door één server of binnen een beheerd exemplaar dat als een failover naar een andere regio kan worden overgeslagen als alle of sommige primaire databases niet beschikbaar zijn vanwege een storing in de primaire regio. Wanneer deze voor een SQL Managed Instance gemaakt, bevat een failovergroep alle gebruikersdatabases in het exemplaar en kan daarom slechts één failovergroep op een exemplaar worden geconfigureerd.
  
  > [!IMPORTANT]
  > De naam van de failovergroep moet wereldwijd uniek zijn binnen het `.database.windows.net` domein.

- **Servers**

     Met servers kunnen sommige of alle gebruikersdatabases op een server in een failovergroep worden geplaatst. Een server ondersteunt ook meerdere failovergroepen op één server.

- **Primair**

  De server of het beheerde exemplaar dat de primaire databases in de failovergroep host.

- **Secundair**

  De server of het beheerde exemplaar dat de secundaire databases in de failovergroep host. De secundaire kan zich niet in dezelfde regio als de primaire regio.

- **Individuele databases toevoegen aan failovergroep**

  U kunt meerdere individuele databases op dezelfde server in dezelfde failovergroep zetten. Als u één database aan de failovergroep toevoegt, wordt er automatisch een secundaire database gemaakt met dezelfde editie en rekenkracht op de secundaire server.  U hebt die server opgegeven toen de failovergroep werd gemaakt. Als u een database toevoegt die al een secundaire database op de secundaire server heeft, wordt die geo-replicatiekoppeling overgenomen door de groep. Wanneer u een database toevoegt die al een secundaire database heeft op een server die geen deel uitmaakt van de failovergroep, wordt er een nieuwe secundaire database gemaakt op de secundaire server.

  > [!IMPORTANT]
  > Zorg ervoor dat de secundaire server geen database met dezelfde naam heeft, tenzij het een bestaande secundaire database is. In failovergroepen voor SQL Managed Instance worden alle gebruikersdatabases gerepliceerd. U kunt geen subset van gebruikersdatabases kiezen voor replicatie in de failovergroep.

- **Databases toevoegen aan een failovergroep in een elastische pool**

  U kunt alle of meerdere databases binnen een elastische pool in dezelfde failovergroep zetten. Als de primaire database zich in een elastische pool, wordt de secundaire database automatisch gemaakt in de elastische pool met dezelfde naam (secundaire pool). U moet ervoor zorgen dat de secundaire server een elastische pool bevat met dezelfde exacte naam en voldoende vrije capaciteit om de secundaire databases te hosten die door de failovergroep worden gemaakt. Als u een database toevoegt aan de pool die al een secundaire database in de secundaire pool heeft, wordt die geo-replicatiekoppeling overgenomen door de groep. Wanneer u een database toevoegt die al een secundaire database heeft op een server die geen deel uitmaakt van de failovergroep, wordt er een nieuwe secundaire database gemaakt in de secundaire pool.
  
- **Eerste seeding**

  Wanneer u databases, elastische pools of beheerde exemplaren toevoegt aan een failovergroep, is er een eerste seedingfase voordat de gegevensreplicatie wordt gestart. De eerste seedingfase is de langste en duurste bewerking. Zodra de eerste seeding is voltooid, worden gegevens gesynchroniseerd en worden alleen de volgende gegevenswijzigingen gerepliceerd. De tijd die nodig is om de eerste seed te voltooien, is afhankelijk van de grootte van uw gegevens, het aantal gerepliceerde databases en de snelheid van de koppeling tussen de entiteiten in de failovergroep. Onder normale omstandigheden is de mogelijke seeding-snelheid maximaal 500 GB per uur voor SQL Database en maximaal 360 GB per uur voor SQL Managed Instance. Seeding wordt parallel uitgevoerd voor alle databases.

  Houd SQL Managed Instance snelheid van de Express Route-koppeling tussen de twee exemplaren in overweging bij het schatten van de tijd van de eerste seedingfase. Als de snelheid van de koppeling tussen de twee exemplaren langzamer is dan nodig is, wordt de tijd om te seeden waarschijnlijk aanzienlijk beïnvloed. U kunt de vermelde seedingsnelheid, het aantal databases, de totale grootte van gegevens en de snelheid van de koppeling gebruiken om te schatten hoelang de eerste seedingfase duurt voordat de gegevensreplicatie begint. Voor één database van 100 GB duurt de eerste seedfase bijvoorbeeld ongeveer 1,2 uur als de koppeling 84 GB per uur kan pushen en als er geen andere databases worden geseed. Als de koppeling slechts 10 GB per uur kan overdragen, duurt het ongeveer 10 uur om een database van 100 GB te seeden. Als er meerdere databases moeten worden gerepliceerd, wordt seeding parallel uitgevoerd. In combinatie met een trage verbindingssnelheid kan de eerste seedingfase aanzienlijk langer duren, met name als het parallel seeden van gegevens uit alle databases de beschikbare bandbreedte voor de koppeling overschrijdt. Als de netwerkbandbreedte tussen twee exemplaren beperkt is en u meerdere beheerde exemplaren aan een failovergroep toevoegt, kunt u overwegen meerdere beheerde exemplaren opeenvolgend toe te voegen aan de failovergroep, één voor één. Gezien een gateway-SKU van de juiste grootte tussen de twee beheerde exemplaren en als de bandbreedte van het bedrijfsnetwerk dit toestaat, is het mogelijk om snelheden van 360 GB per uur te bereiken.  

- **DNS-zone**

  Een unieke id die automatisch wordt gegenereerd wanneer er een nieuwe SQL Managed Instance wordt gemaakt. Een SAN-certificaat (multi-domain) voor dit exemplaar is ingericht voor het verifiëren van de clientverbindingen met elk exemplaar in dezelfde DNS-zone. De twee beheerde exemplaren in dezelfde failovergroep moeten de DNS-zone delen.
  
  > [!NOTE]
  > Een DNS-zone-id is niet vereist voor failovergroepen die zijn gemaakt voor SQL Database.

- **Lees-/schrijflistener voor failovergroep**

  Een DNS CNAME-record die naar de URL van de huidige primaire server wijst. Deze wordt automatisch gemaakt wanneer de failovergroep wordt gemaakt en maakt het mogelijk om de lees-/schrijfworkload transparant opnieuw verbinding te laten maken met de primaire database wanneer de primaire wordt gewijzigd na een failover. Wanneer de failovergroep op een server wordt gemaakt, wordt de DNS CNAME-record voor de listener-URL gevormd als `<fog-name>.database.windows.net` . Wanneer de failovergroep wordt gemaakt op een SQL Managed Instance, wordt de DNS CNAME-record voor de listener-URL gevormd als `<fog-name>.<zone_id>.database.windows.net` .

- **Alleen-lezen listener voor failovergroep**

  Er wordt een DNS CNAME-record gevormd die naar de alleen-lezen listener wijst die naar de URL van de secundaire server wijst. Deze wordt automatisch gemaakt wanneer de failovergroep wordt gemaakt en zorgt ervoor dat de alleen-lezen SQL-workload transparant verbinding kan maken met de secundaire database met behulp van de opgegeven taakverdelingsregels. Wanneer de failovergroep op een server wordt gemaakt, wordt de DNS CNAME-record voor de listener-URL gevormd als `<fog-name>.secondary.database.windows.net` . Wanneer de failovergroep wordt gemaakt op een SQL Managed Instance, wordt de DNS CNAME-record voor de listener-URL gevormd als `<fog-name>.secondary.<zone_id>.database.windows.net` .

- **Beleid voor automatische failover**

  Standaard wordt een failovergroep geconfigureerd met een beleid voor automatische failover. Azure activeert een failover nadat de fout is gedetecteerd en de respijtperiode is verlopen. Het systeem moet controleren of de storing niet kan worden vereend door de [ingebouwde](high-availability-sla.md) infrastructuur voor hoge beschikbaarheid vanwege de schaal van de impact. Als u de failoverwerkstroom wilt beheren vanuit de toepassing of handmatig, kunt u automatische failover uitschakelen.
  
  > [!NOTE]
  > Omdat verificatie van de schaal van de storing en hoe snel deze kan worden beperkt menselijke acties door het operationele team omvat, kan de respijtperiode niet onder één uur worden ingesteld. Deze beperking geldt voor alle databases in de failovergroep, ongeacht hun gegevenssynchronisatie.

- **Failoverbeleid voor alleen-lezen**

  De failover van de alleen-lezen listener is standaard uitgeschakeld. Het zorgt ervoor dat de prestaties van de primaire niet worden beïnvloed wanneer de secundaire offline is. Dit betekent echter ook dat de alleen-lezensessies geen verbinding kunnen maken totdat de secundaire is hersteld. Als u downtime voor de alleen-lezensessies niet kunt tolereren en het primaire bestand voor zowel alleen-lezen- als lees-schrijfverkeer kunt gebruiken ten koste van de potentiële prestatievermindering van de primaire, kunt u failover inschakelen voor de alleen-lezen listener door de eigenschap te `AllowReadOnlyFailoverToPrimary` configureren. In dat geval wordt het alleen-lezenverkeer automatisch omgeleid naar de primaire als de secundaire niet beschikbaar is.

  > [!NOTE]
  > De `AllowReadOnlyFailoverToPrimary` eigenschap heeft alleen effect als automatisch failoverbeleid is ingeschakeld en een automatische failover is geactiveerd door Azure. Als in dat geval de eigenschap is ingesteld op Waar, dient de nieuwe primaire functie zowel voor lezen/schrijven als voor alleen-lezensessies.

- **Geplande failover**

   Geplande failover voert volledige synchronisatie uit tussen primaire en secundaire databases voordat de secundaire database overschakelt naar de primaire rol. Dit garandeert geen gegevensverlies. Geplande failover wordt gebruikt in de volgende scenario's:

  - Dr-hersteloefeningen uitvoeren in productie wanneer het gegevensverlies niet acceptabel is
  - De databases verplaatsen naar een andere regio
  - De databases terug te brengen naar de primaire regio nadat de storing is vereengeld (failback)

- **Niet-geplande failover**

   Niet-geplande of geforceerd failover schakelt onmiddellijk over naar de primaire rol zonder synchronisatie met de primaire rol. Deze bewerking leidt tot gegevensverlies. Niet-geplande failover wordt gebruikt als een herstelmethode tijdens uitval wanneer de primaire niet toegankelijk is. Wanneer de oorspronkelijke primaire versie weer online is, maakt deze automatisch opnieuw verbinding zonder synchronisatie en wordt deze een nieuwe secundaire.

- **Handmatige failover**

  U kunt failover op elk moment handmatig initiëren, ongeacht de configuratie van de automatische failover. Als er geen beleid voor automatische failover is geconfigureerd, is handmatige failover vereist om databases in de failovergroep naar de secundaire database te herstellen. U kunt geforceerd of gebruiksvriendelijke failover initiëren (met volledige gegevenssynchronisatie). De laatste kan worden gebruikt om de primaire naar de secundaire regio te verplaatsen. Wanneer de failover is voltooid, worden de DNS-records automatisch bijgewerkt om de verbinding met de nieuwe primaire server te garanderen.

- **Respijtperiode met gegevensverlies**

  Omdat de primaire en secundaire databases worden gesynchroniseerd met behulp van asynchrone replicatie, kan de failover leiden tot gegevensverlies. U kunt het beleid voor automatische failover aanpassen aan de tolerantie van uw toepassing voor gegevensverlies. Door te configureren, kunt u bepalen hoe lang het systeem wacht voordat de failover wordt geïnitieerd, wat waarschijnlijk leidt `GracePeriodWithDataLossHours` tot gegevensverlies.

- **Meerdere failovergroepen**

  U kunt meerdere failovergroepen configureren voor hetzelfde paar servers om het bereik van failovers te beheren. Voor elke groep wordt onafhankelijk van elkaar een slaagt-over-over-ing-over-ing- Als uw toepassing voor meerdere tenants elastische pools gebruikt, kunt u deze mogelijkheid gebruiken om primaire en secundaire databases in elke pool te combineren. Op deze manier kunt u de impact van een storing beperken tot slechts de helft van de tenants.

  > [!NOTE]
  > SQL Managed Instance biedt geen ondersteuning voor meerdere failovergroepen.
  
## <a name="permissions"></a>Machtigingen

Machtigingen voor een failovergroep worden beheerd via op rollen gebaseerd toegangsbeheer [van Azure (Azure RBAC).](../../role-based-access-control/overview.md) De [SQL Server inzender](../../role-based-access-control/built-in-roles.md#sql-server-contributor) heeft alle benodigde machtigingen om failovergroepen te beheren.

### <a name="create-failover-group"></a>Failovergroep maken

Als u een failovergroep wilt maken, hebt u Azure RBAC-schrijftoegang nodig tot zowel de primaire als de secundaire servers en tot alle databases in de failovergroep. Voor een SQL Managed Instance hebt u Azure RBAC-schrijftoegang nodig tot zowel de primaire als de secundaire SQL Managed Instance, maar machtigingen voor afzonderlijke databases zijn niet relevant, omdat afzonderlijke SQL Managed Instance-databases niet kunnen worden toegevoegd aan of verwijderd uit een failovergroep.

### <a name="update-a-failover-group"></a>Een failovergroep bijwerken

Als u een failovergroep wilt bijwerken, hebt u schrijftoegang van Azure RBAC nodig tot de failovergroep en alle databases op de huidige primaire server of het huidige beheerde exemplaar.  

### <a name="fail-over-a-failover-group"></a>Failover van een failovergroep

Als u een failovergroep wilt uitvoeren, hebt u Azure RBAC-schrijftoegang nodig tot de failovergroep op de nieuwe primaire server of het beheerde exemplaar.

## <a name="best-practices-for-sql-database"></a>Best practices voor SQL Database

De groep voor automatische failover moet worden geconfigureerd op de primaire server en deze verbinden met de secundaire server in een andere Azure-regio. De groepen kunnen alle of sommige databases in deze servers bevatten. Het volgende diagram illustreert een typische configuratie van een geografisch redundante cloudtoepassing met behulp van meerdere databases en een groep voor automatische failover.

![Diagram met een typische configuratie van een geografisch redundante cloudtoepassing met meerdere databases en een groep voor automatische failover.](./media/auto-failover-group-overview/auto-failover-group.png)

> [!NOTE]
> Zie Add SQL Database to a failover group (Een database SQL Database aan een [failovergroep](failover-group-add-single-database-tutorial.md) toevoegen) voor een gedetailleerde stapsgewijse zelfstudie waarin u een database aan SQL Database failovergroep toevoegt.

Volg deze algemene richtlijnen bij het ontwerpen van een service met bedrijfscontinuïteit:

### <a name="using-one-or-several-failover-groups-to-manage-failover-of-multiple-databases"></a>Een of meer failovergroepen gebruiken om failovers van meerdere databases te beheren

Er kunnen een of meer failovergroepen worden gemaakt tussen twee servers in verschillende regio's (primaire en secundaire servers). Elke groep kan een of meer databases bevatten die als eenheid worden hersteld voor het geval alle of sommige primaire databases niet meer beschikbaar zijn vanwege een storing in de primaire regio. De failovergroep maakt een geo-secundaire database met dezelfde servicedoelstelling als de primaire database. Als u een bestaande geo-replicatierelatie toevoegt aan de failovergroep, moet u ervoor zorgen dat de geo-secundaire laag is geconfigureerd met dezelfde servicelaag en rekenkracht als de primaire.
  
> [!IMPORTANT]
> Het maken van failovergroepen tussen twee servers in verschillende abonnementen wordt momenteel niet ondersteund voor Azure SQL Database. Als u de primaire of secundaire server naar een ander abonnement verplaatst nadat de failovergroep is gemaakt, kan dit leiden tot fouten in de failover-aanvragen en andere bewerkingen.

### <a name="using-read-write-listener-for-oltp-workload"></a>Lezen/schrijven-listener gebruiken voor OLTP-workload

Bij het uitvoeren van OLTP-bewerkingen gebruikt u als de server-URL en worden de verbindingen `<fog-name>.database.windows.net` automatisch omgeleid naar de primaire server. Deze URL verandert niet na de failover. De failover omvat het bijwerken van de DNS-record, zodat de clientverbindingen pas worden omgeleid naar de nieuwe primaire server nadat de DNS-cache van de client is vernieuwd.

### <a name="using-read-only-listener-for-read-only-workload"></a>Alleen-lezen listener gebruiken voor alleen-lezen workload

Als u een logisch geïsoleerde alleen-lezen workload hebt die tolerant is voor bepaalde veroudering van gegevens, kunt u de secundaire database in de toepassing gebruiken. Gebruik voor alleen-lezensessies `<fog-name>.secondary.database.windows.net` als server-URL en de verbinding wordt automatisch omgeleid naar de secundaire server. Het wordt ook aanbevolen dat u de leesintentie in de connection string met behulp van `ApplicationIntent=ReadOnly` .

> [!NOTE]
> In premium-, Bedrijfskritiek- en Hyperscale-servicelagen ondersteunt SQL Database het gebruik van [alleen-lezen replica's om alleen-lezen](read-scale-out.md) querywerkbelastingen te offloaden, met behulp van de `ApplicationIntent=ReadOnly` parameter in de connection string. Wanneer u een secundaire geo-replicatie hebt geconfigureerd, kunt u deze mogelijkheid gebruiken om verbinding te maken met een alleen-lezen-replica op de primaire locatie of op de geo-gerepliceerde locatie.
>
> - Als u verbinding wilt maken met een alleen-lezen replica op de primaire locatie, gebruikt u `ApplicationIntent=ReadOnly` en `<fog-name>.database.windows.net` .
> - Als u verbinding wilt maken met een alleen-lezen replica op de secundaire locatie, gebruikt u `ApplicationIntent=ReadOnly` en `<fog-name>.secondary.database.windows.net` .

### <a name="preparing-for-performance-degradation"></a>Prestatievermindering voorbereiden

Een typische Azure-toepassing maakt gebruik van meerdere Azure-services en bestaat uit meerdere onderdelen. De automatische failover van de failovergroep wordt geactiveerd op basis van de status Azure SQL onderdelen. Andere Azure-services in de primaire regio worden mogelijk niet beïnvloed door de storing en de onderdelen ervan zijn mogelijk nog steeds beschikbaar in die regio. Zodra de primaire databases overschakelen naar de DR-regio, kan de latentie tussen de afhankelijke onderdelen toenemen. Om de gevolgen van een hogere latentie op de prestaties van de toepassing te voorkomen, moet u de redundantie van alle onderdelen van de toepassing in de DR-regio garanderen en deze [netwerkbeveiligingsrichtlijnen volgen.](#failover-groups-and-network-security)

### <a name="preparing-for-data-loss"></a>Voorbereiden op gegevensverlies

Als er een storing wordt gedetecteerd, wacht Azure op de periode die u hebt opgegeven door `GracePeriodWithDataLossHours` . De standaardwaarde is 1 uur. Als u zich geen gegevensverlies kunt veroorloven, zorg er dan voor dat u een voldoende groot aantal in `GracePeriodWithDataLossHours` stelt, zoals 24 uur. Gebruik handmatige groeps-failover om een failback uit te keren van de secundaire naar de primaire.

> [!IMPORTANT]
> Elastische pools met 800 of minder DTUs en meer dan 250 databases die gebruikmaken van geo-replicatie kunnen problemen ondervinden, waaronder langere geplande failovers en slechtere prestaties.  Deze problemen treden vaker op bij schrijfintensieve werkbelastingen, wanneer geo-replicatie-eindpunten breed worden gescheiden op basis van geografie of wanneer er meerdere secundaire eindpunten worden gebruikt voor elke database.  Symptomen van deze problemen worden aangegeven wanneer de geo-replicatievertraging na een periode toeneemt.  Deze vertraging kan worden bewaakt met [behulp sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).  Als deze problemen zich voordoen, omvatten oplossingen het verhogen van het aantal pool-DTUS's of het verminderen van het aantal geo-gerepliceerde databases in dezelfde pool.

### <a name="changing-secondary-region-of-the-failover-group"></a>Secundaire regio van de failovergroep wijzigen

Ter illustratie van de wijzigingsreeks gaan we ervan uit dat server A de primaire server is, server B de bestaande secundaire server is en server C de nieuwe secundaire server in de derde regio.  Volg deze stappen om de overgang te maken:

1. Maak aanvullende secondaries van elke database op server A naar server C met behulp [van actieve geo-replicatie.](active-geo-replication-overview.md) Elke database op server A heeft twee secondaries, één op server B en één op server C. Dit garandeert dat de primaire databases tijdens de overgang beveiligd blijven.
2. Verwijder de failovergroep. Op dit moment mislukken de aanmeldingen. Dit komt doordat de SQL-aliassen voor de listeners voor de failovergroep zijn verwijderd en de gateway de naam van de failovergroep niet herkent.
3. Maak de failovergroep opnieuw met dezelfde naam tussen servers A en C. Op dit moment mislukken de aanmeldingen niet meer.
4. Voeg alle primaire databases op server A toe aan de nieuwe failovergroep.
5. Server B. neerzetten Alle databases op B worden automatisch verwijderd.

### <a name="changing-primary-region-of-the-failover-group"></a>Primaire regio van de failovergroep wijzigen

Ter illustratie van de wijzigingsreeks gaan we ervan uit dat server A de primaire server is, server B de bestaande secundaire server is en server C de nieuwe primaire server in de derde regio.  Volg deze stappen om de overgang te maken:

1. Voer een geplande failover uit om de primaire server over te schakelen naar B. Server A wordt de nieuwe secundaire server. De failover kan enkele minuten downtime tot gevolg hebben. De werkelijke tijd is afhankelijk van de grootte van de failovergroep.
2. Maak aanvullende secondaries van elke database op server B naar server C met behulp [van actieve geo-replicatie.](active-geo-replication-overview.md) Elke database op server B heeft twee secondaries, één op server A en één op server C. Dit garandeert dat de primaire databases tijdens de overgang beveiligd blijven.
3. Verwijder de failovergroep. Op dit moment mislukken de aanmeldingen. Dit komt doordat de SQL-aliassen voor de listeners voor de failovergroep zijn verwijderd en de gateway de naam van de failovergroep niet herkent.
4. Maak de failovergroep opnieuw met dezelfde naam tussen servers B en C. Op dit moment mislukken de aanmeldingen niet meer.
5. Voeg alle primaire databases op B toe aan de nieuwe failovergroep.
6. Voer een geplande failover van de failovergroep uit om over te schakelen naar B en C. Server C wordt nu de primaire en B- de secundaire server. Alle secundaire databases op server A worden automatisch gekoppeld aan de primaireries op C. Net als in stap 1 kan de failover enkele minuten downtime veroorzaken.
7. De server A. Alle databases op A worden automatisch verwijderd.

> [!IMPORTANT]
> Wanneer de failovergroep wordt verwijderd, worden ook de DNS-records voor de listener-eindpunten verwijderd. Op dat moment is er een kans dat iemand anders een failovergroep of serveralias met dezelfde naam maakt, waardoor u deze niet opnieuw kunt gebruiken. Gebruik geen algemene namen van failover-groepen om het risico te minimaliseren.

## <a name="best-practices-for-sql-managed-instance"></a>Best practices voor SQL Managed Instance

De groep voor automatische failover moet worden geconfigureerd op het primaire exemplaar en wordt verbonden met het secundaire exemplaar in een andere Azure-regio.  Alle databases in het exemplaar worden gerepliceerd naar het secundaire exemplaar.

Het volgende diagram illustreert een typische configuratie van een geografisch redundante cloudtoepassing met behulp van een beheerd exemplaar en een groep voor automatische failover.

![diagram voor automatische failover](./media/auto-failover-group-overview/auto-failover-group-mi.png)

> [!NOTE]
> Zie [Beheerd exemplaar toevoegen aan een failovergroep](../managed-instance/failover-group-add-instance-tutorial.md) voor een gedetailleerde stapsgewijse zelfstudie over het toevoegen van een SQL Managed Instance failovergroep te gebruiken.

Als uw toepassing gebruikmaakt SQL Managed Instance als de gegevenslaag, volgt u deze algemene richtlijnen bij het ontwerpen voor bedrijfscontinuïteit:

### <a name="creating-the-secondary-instance"></a>Het secundaire exemplaar maken

Om ervoor te zorgen dat de verbinding met de primaire SQL Managed Instance na een failover moeten zowel de primaire als de secundaire instantie zich in dezelfde DNS-zone bevindt. Het garandeert dat hetzelfde SAN-certificaat (multi-domain) kan worden gebruikt om de clientverbindingen met een van de twee exemplaren in de failovergroep te verifiëren. Wanneer uw toepassing gereed is voor productie-implementatie, maakt u een secundaire SQL Managed Instance in een andere regio en zorgt u ervoor dat de DNS-zone wordt gedeeld met de primaire SQL Managed Instance. U kunt dit doen door de optionele parameter op te geven tijdens het maken. Als u PowerShell of de REST API gebruikt, is de naam van de optionele parameter en is de naam van het bijbehorende optionele veld in de Azure Portal `DNS Zone Partner` Primair beheerd exemplaar.

> [!IMPORTANT]
> Het eerste beheerde exemplaar dat in het subnet wordt gemaakt, bepaalt de DNS-zone voor alle volgende exemplaren in hetzelfde subnet. Dit betekent dat twee exemplaren van hetzelfde subnet niet tot verschillende DNS-zones kunnen behoren.

Zie Een secundair beheerd exemplaar maken SQL Managed Instance meer informatie over het maken van de secundaire server in dezelfde [DNS-zone als het primaire exemplaar.](../managed-instance/failover-group-add-instance-tutorial.md#create-a-secondary-managed-instance)

### <a name="using-geo-paired-regions"></a>Geografisch gekoppelde regio's gebruiken

Implementeer beide beheerde exemplaren in [gekoppelde regio's](../../best-practices-availability-paired-regions.md), om prestatieredenen. Beheerde exemplaren die zich in geografisch gekoppelde regio's bevinden, presteren veel beter dan die in niet-gekoppelde regio's. 

### <a name="enabling-replication-traffic-between-two-instances"></a>Replicatieverkeer tussen twee exemplaren inschakelen

Omdat elke instantie is geïsoleerd in een eigen VNet, moet verkeer in twee richtingen tussen deze VNets worden toegestaan. Zie [Azure VPN-gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="creating-a-failover-group-between-managed-instances-in-different-subscriptions"></a>Een failovergroep maken tussen beheerde exemplaren in verschillende abonnementen

U kunt een failovergroep maken tussen SQL Managed Instances in twee verschillende [abonnementen,](../../active-directory/fundamentals/active-directory-whatis.md#terminology)zolang abonnementen zijn gekoppeld aan dezelfde Azure Active Directory tenant . Wanneer u de PowerShell-API gebruikt, kunt u dit doen door de parameter op te `PartnerSubscriptionId` geven voor de secundaire SQL Managed Instance. Wanneer u REST API, kan elke exemplaar-id die is opgenomen in de `properties.managedInstancePairs` parameter een eigen subscriptionID hebben.
  
> [!IMPORTANT]
> Azure Portal biedt geen ondersteuning voor het maken van failovergroepen in verschillende abonnementen. Voor de bestaande failovergroepen in verschillende abonnementen en/of resourcegroepen kan failover ook niet handmatig worden gestart via de portal vanuit de primaire SQL Managed Instance. Start deze vanuit het geo-secundaire exemplaar.

### <a name="managing-failover-to-secondary-instance"></a>Failover naar een secundair exemplaar beheren

De failovergroep beheert de failover van alle databases in de SQL Managed Instance. Wanneer een groep wordt gemaakt, wordt elke database in het exemplaar automatisch geo-gerepliceerd naar de secundaire SQL Managed Instance. U kunt geen failover-groepen gebruiken om een gedeeltelijke failover van een subset van de databases te initiëren.

> [!IMPORTANT]
> Als een database wordt verwijderd uit de primaire SQL Managed Instance, wordt deze ook automatisch verwijderd op de geo-secundaire SQL Managed Instance.

### <a name="using-read-write-listener-for-oltp-workload"></a>Lezen/schrijven-listener gebruiken voor OLTP-workload

Bij het uitvoeren van OLTP-bewerkingen gebruikt u als de server-URL en worden de verbindingen `<fog-name>.zone_id.database.windows.net` automatisch omgeleid naar de primaire server. Deze URL verandert niet na de failover. De failover omvat het bijwerken van de DNS-record, zodat de clientverbindingen pas worden omgeleid naar de nieuwe primaire server nadat de DNS-cache van de client is vernieuwd. Omdat het secundaire exemplaar de DNS-zone deelt met de primaire, kan de clienttoepassing opnieuw verbinding maken met de zone met behulp van hetzelfde SAN-certificaat.

### <a name="using-read-only-listener-to-connect-to-the-secondary-instance"></a>Alleen-lezen listener gebruiken om verbinding te maken met het secundaire exemplaar

Als u een logisch geïsoleerde alleen-lezen workload hebt die tolerant is voor bepaalde veroudering van gegevens, kunt u de secundaire database in de toepassing gebruiken. Als u rechtstreeks verbinding wilt maken met de secundaire geo-replicatie, gebruikt u als de server-URL en wordt de verbinding rechtstreeks gemaakt met `<fog-name>.secondary.<zone_id>.database.windows.net` de secundaire geo-replicatie.

> [!NOTE]
> In Bedrijfskritiek laag ondersteunt SQL Managed Instance het gebruik van [alleen-lezen replica's om alleen-lezen](read-scale-out.md) querywerkbelastingen te offloaden met behulp van de parameter in de `ApplicationIntent=ReadOnly` connection string. Wanneer u een secundaire geo-replicatie hebt geconfigureerd, kunt u deze mogelijkheid gebruiken om verbinding te maken met een alleen-lezen-replica op de primaire locatie of op de geo-gerepliceerde locatie.
>
> - Gebruik en om verbinding te maken met een alleen-lezen replica op de `ApplicationIntent=ReadOnly` primaire `<fog-name>.<zone_id>.database.windows.net` locatie.
> - Gebruik en om verbinding te maken met een alleen-lezen replica op de `ApplicationIntent=ReadOnly` secundaire `<fog-name>.secondary.<zone_id>.database.windows.net` locatie.

### <a name="preparing-for-performance-degradation"></a>Prestatievermindering voorbereiden

Een typische Azure-toepassing maakt gebruik van meerdere Azure-services en bestaat uit meerdere onderdelen. De automatische failover van de failovergroep wordt geactiveerd op basis van de status Azure SQL onderdelen. Andere Azure-services in de primaire regio worden mogelijk niet beïnvloed door de storing en de onderdelen ervan zijn mogelijk nog steeds beschikbaar in die regio. Zodra de primaire databases overschakelen naar de secundaire regio, kan de latentie tussen de afhankelijke onderdelen toenemen. Om de gevolgen van een hogere latentie op de prestaties van de toepassing te voorkomen, moet u de redundantie van alle onderdelen van de toepassing in de secundaire regio garanderen en fail over toepassingsonderdelen samen met de database. Volg tijdens de configuratie de [richtlijnen voor netwerkbeveiliging](#failover-groups-and-network-security) om de verbinding met de database in de secundaire regio te garanderen.

### <a name="preparing-for-data-loss"></a>Voorbereiden op gegevensverlies

Als er een storing wordt gedetecteerd, wordt voor onze weten een failover voor lezen/schrijven geactiveerd als er geen gegevens verloren gaan. Anders wordt failover uitgesteld voor de periode die u opgeeft met `GracePeriodWithDataLossHours` . Als u hebt `GracePeriodWithDataLossHours` opgegeven, moet u voorbereid zijn op gegevensverlies. Over het algemeen geeft Azure tijdens uitval de voorkeur aan beschikbaarheid. Als u zich geen gegevensverlies kunt veroorloven, moet u GracePeriodWithDataLossHours instellen op een voldoende groot aantal, zoals 24 uur, of automatische failover uitschakelen.

De DNS-update van de lees-/schrijf-listener vindt onmiddellijk plaats nadat de failover is gestart. Deze bewerking leidt niet tot gegevensverlies. Het proces van het overschakelen van databaserollen kan onder normale omstandigheden echter wel vijf minuten duren. Totdat dit is voltooid, zijn sommige databases in het nieuwe primaire exemplaar nog steeds alleen-lezen. Als een failover wordt gestart met behulp van PowerShell, is de bewerking om over te schakelen naar de primaire replicarol synchroon. Als de gebruikersinterface wordt gestart met behulp van Azure Portal, geeft de gebruikersinterface de voltooiingsstatus aan. Als het wordt geïnitieerd met behulp van REST API, gebruikt u het pollingmechanisme Azure Resource Manager standard-Azure Resource Manager te controleren op voltooiing.

> [!IMPORTANT]
> Gebruik handmatige groeps-failover om voorvallen terug te verplaatsen naar de oorspronkelijke locatie. Wanneer de storing die de failover heeft veroorzaakt, wordt beperkt, kunt u uw primaire databases naar de oorspronkelijke locatie verplaatsen. Om dit te doen, moet u de handmatige failover van de groep initiëren.
  
### <a name="changing-secondary-region-of-the-failover-group"></a>Secundaire regio van de failovergroep wijzigen

Stel dat instantie A het primaire exemplaar is, exemplaar B het bestaande secundaire exemplaar is en exemplaar C het nieuwe secundaire exemplaar in de derde regio.  Volg deze stappen om de overgang te maken:

1. Maak exemplaar C met dezelfde grootte als A en in dezelfde DNS-zone.
2. Verwijder de failovergroep tussen exemplaren A en B. Op dit moment mislukken de aanmeldingen omdat de SQL-aliassen voor de failovergroeplisteners zijn verwijderd en de gateway de naam van de failovergroep niet herkent. De secundaire databases worden losgekoppeld van de primaireries en worden lees-/schrijfdatabases.
3. Maak een failovergroep met dezelfde naam tussen exemplaar A en C. Volg de instructies in [failovergroep met SQL Managed Instance zelfstudie](../managed-instance/failover-group-add-instance-tutorial.md). Dit is een bewerking voor de grootte van de gegevens en wordt voltooid wanneer alle databases van exemplaar A worden geseed en gesynchroniseerd.
4. Verwijder instantie B als dat niet nodig is om onnodige kosten te voorkomen.

> [!NOTE]
> Nadat stap 2 en tot stap 3 is voltooid, blijven de databases in instantie A onbeveiligd tegen een onherstelbare fout van exemplaar A.

### <a name="changing-primary-region-of-the-failover-group"></a>Primaire regio van de failovergroep wijzigen

Stel dat exemplaar A het primaire exemplaar is, exemplaar B het bestaande secundaire exemplaar is en exemplaar C het nieuwe primaire exemplaar in de derde regio.  Volg deze stappen om de overgang te maken:

1. Maak exemplaar C met dezelfde grootte als B en in dezelfde DNS-zone.
2. Maak verbinding met instantie B en maak handmatig een failover om het primaire exemplaar over te schakelen naar B. Instantie A wordt automatisch het nieuwe secundaire exemplaar.
3. Verwijder de failovergroep tussen exemplaren A en B. Op dit moment mislukken de aanmeldingen omdat de SQL-aliassen voor de failovergroeplisteners zijn verwijderd en de gateway de naam van de failovergroep niet herkent. De secundaire databases worden losgekoppeld van de primaireries en worden lees-/schrijfdatabases.
4. Maak een failovergroep met dezelfde naam tussen exemplaar A en C. Volg de instructies in de [zelfstudie failovergroep met beheerd exemplaar](../managed-instance/failover-group-add-instance-tutorial.md). Dit is een bewerking voor de grootte van de gegevens en wordt voltooid wanneer alle databases van exemplaar A worden geseed en gesynchroniseerd.
5. Verwijder instantie A als dat niet nodig is om onnodige kosten te voorkomen.

> [!CAUTION]
> Na stap 3 en tot stap 4 is voltooid, blijven de databases in exemplaar A onbeveiligd tegen een onherstelbare fout van exemplaar A.

> [!IMPORTANT]
> Wanneer de failovergroep wordt verwijderd, worden ook de DNS-records voor de listener-eindpunten verwijderd. Op dat moment is er een niet-nul kans dat iemand anders een failovergroep of serveralias met dezelfde naam maakt, waardoor u deze niet opnieuw kunt gebruiken. Gebruik geen algemene namen van failover-groepen om het risico te minimaliseren.

### <a name="enable-scenarios-dependent-on-objects-from-the-system-databases"></a>Scenario's inschakelen die afhankelijk zijn van objecten uit de systeemdatabases
Systeemdatabases worden niet gerepliceerd naar het secundaire exemplaar in een failovergroep. Als u scenario's wilt inschakelen die afhankelijk zijn van objecten uit de systeemdatabases, moet u op het secundaire exemplaar dezelfde objecten maken op de secundaire database. Als u bijvoorbeeld van plan bent om dezelfde aanmeldingen op het secundaire exemplaar te gebruiken, moet u deze maken met de identieke SID. 
```SQL
-- Code to create login on the secondary instance
CREATE LOGIN foo WITH PASSWORD = '<enterStrongPasswordHere>', SID = <login_sid>;
``` 


## <a name="failover-groups-and-network-security"></a>Failovergroepen en netwerkbeveiliging

Voor sommige toepassingen vereisen de beveiligingsregels dat de netwerktoegang tot de gegevenslaag wordt beperkt tot een specifiek onderdeel of specifieke onderdelen, zoals een VM, webservice, enzovoort. Deze vereiste brengt enkele uitdagingen met zich mee voor het ontwerpen van bedrijfscontinuïteit en het gebruik van de failovergroepen. Houd rekening met de volgende opties bij het implementeren van dergelijke beperkte toegang.

### <a name="using-failover-groups-and-virtual-network-rules"></a>Failovergroepen en regels voor virtuele netwerken gebruiken

Als u [Virtual Network-service-eindpunten](vnet-service-endpoint-rule-overview.md) en -regels gebruikt om de toegang tot uw database in SQL Database of SQL Managed Instance te beperken, moet u er rekening mee houden dat elk service-eindpunt voor virtuele netwerken van toepassing is op slechts één Azure-regio. Met het eindpunt kunnen andere regio's geen communicatie van het subnet accepteren. Daarom kunnen alleen de clienttoepassingen die in dezelfde regio zijn geïmplementeerd, verbinding maken met de primaire database. Omdat de failover tot gevolg heeft dat de SQL Database-clientsessies worden omgeleid naar een server in een andere (secundaire) regio, mislukken deze sessies als ze afkomstig zijn van een client buiten die regio. Daarom kan het beleid voor automatische failover niet worden ingeschakeld als de deelnemende servers of exemplaren zijn opgenomen in de Virtual Network regels. Volg deze stappen om handmatige failover te ondersteunen:

1. De redundante kopieën van de front-endonderdelen van uw toepassing (webservice, virtuele machines, enzovoort) inrichten in de secundaire regio
2. De regels [voor virtuele netwerken afzonderlijk](vnet-service-endpoint-rule-overview.md) configureren voor de primaire en secundaire server
3. De [front-end-failover inschakelen met behulp van een Traffic Manager-configuratie](designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)
4. Start handmatige failover wanneer de storing wordt gedetecteerd. Deze optie is geoptimaliseerd voor de toepassingen waarvoor consistente latentie tussen de front-end en de gegevenslaag is vereist en biedt ondersteuning voor herstel wanneer de front-end, gegevenslaag of beide worden beïnvloed door de storing.

> [!NOTE]
> Als u de **alleen-lezen** listener gebruikt om een alleen-lezen workload te verdelen, moet u ervoor zorgen dat deze workload wordt uitgevoerd in een VM of een andere resource in de secundaire regio, zodat deze verbinding kan maken met de secundaire database.

### <a name="use-failover-groups-and-firewall-rules"></a>Failovergroepen en firewallregels gebruiken

Als voor uw bedrijfscontinuïteitsplan failover is vereist met behulp van groepen met automatische failover, kunt u de toegang tot uw database in SQL Database met behulp van de traditionele firewallregels. Volg deze stappen om automatische failover te ondersteunen:

1. [Een openbaar IP-adres maken](../../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)
2. [Maak een openbaar load balancer](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) en wijs het openbare IP-adres toe.
3. [Een virtueel netwerk en de virtuele machines voor](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) uw front-endonderdelen maken
4. [Maak een netwerkbeveiligingsgroep](../../virtual-network/network-security-groups-overview.md) en configureer binnenkomende verbindingen.
5. Zorg ervoor dat de uitgaande verbindingen zijn geopend voor Azure SQL Database met behulp van de [servicetag SQL](../../virtual-network/network-security-groups-overview.md#service-tags).
6. Maak een [SQL Database firewallregel om](firewall-configure.md) inkomende verkeer toe te staan vanaf het openbare IP-adres dat u in stap 1 hebt gemaakt.

Zie Uitgaande verbindingen voor load balancer voor meer informatie over het configureren van uitgaande toegang en welk IP-adres moet worden gebruikt in [de firewallregels.](../../load-balancer/load-balancer-outbound-connections.md)

De bovenstaande configuratie zorgt ervoor dat de automatische failover verbindingen van de front-endonderdelen niet blokkeert en gaat ervan uit dat de toepassing de langere latentie tussen de front-end en de gegevenslaag kan tolereren.

> [!IMPORTANT]
> Om bedrijfscontinuïteit te garanderen voor regionale uitval, moet u zorgen voor geografische redundantie voor zowel front-endonderdelen als de databases.

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>Geo-replicatie tussen beheerde exemplaren en hun VNets inschakelen

Wanneer u een failovergroep tussen primaire en secundaire SQL Managed Instances in twee verschillende regio's in stelt, wordt elk exemplaar geïsoleerd met behulp van een onafhankelijk virtueel netwerk. Als u replicatieverkeer tussen deze VNets wilt toestaan, moet aan deze vereisten worden voldaan:

- De twee exemplaren van SQL Managed Instance moeten zich in verschillende Azure-regio's.
- De twee exemplaren van SQL Managed Instance moeten dezelfde servicelaag zijn en dezelfde opslaggrootte hebben.
- Uw secundaire exemplaar van SQL Managed Instance moet leeg zijn (geen gebruikersdatabases).
- De virtuele netwerken die worden gebruikt door de exemplaren van SQL Managed Instance moeten worden verbonden via [een VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) of [Express Route.](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md) Wanneer twee virtuele netwerken verbinding maken via een on-premises netwerk, zorg er dan voor dat er geen firewallregel is die poorten 5022 en 11000-11999 blokkeert. Wereldwijde VNet-peering wordt ondersteund met de beperking die wordt beschreven in de opmerking hieronder.

   > [!IMPORTANT]
   > [Op 22-9-2020](https://azure.microsoft.com/en-us/updates/global-virtual-network-peering-support-for-azure-sql-managed-instance-now-available/)hebben we wereldwijde peering voor virtuele netwerken aangekondigd voor nieuw gemaakte virtuele clusters. Dit betekent dat wereldwijde peering voor virtuele netwerken wordt ondersteund voor SQL Managed Instances die zijn gemaakt in lege subnetten na de aankondigingsdatum, evenals voor alle volgende beheerde exemplaren die in deze subnetten zijn gemaakt. Voor alle andere SQL Managed Instances is peeringondersteuning beperkt tot de netwerken in dezelfde regio vanwege de beperkingen van wereldwijde [peering voor virtuele netwerken.](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) Zie ook de relevante sectie van het artikel [met veelgestelde](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) vragen over Azure Virtual Networks voor meer informatie. 

- De twee SQL Managed Instance VNets mogen geen overlappende IP-adressen hebben.
- U moet uw netwerkbeveiligingsgroepen (NSG) zodanig instellen dat poort 5022 en het bereik 11000–12000 zijn geopend voor inkomende en uitgaande verbindingen van het subnet van het andere beheerde exemplaar. Dit is om replicatieverkeer tussen de exemplaren mogelijk te maken.

   > [!IMPORTANT]
   > Onjuist geconfigureerde NSG-beveiligingsregels leiden tot vastgelopen databasekopiebewerkingen.

- De secundaire SQL Managed Instance is geconfigureerd met de juiste DNS-zone-id. DNS-zone is een eigenschap van een SQL Managed Instance en onderliggend virtueel cluster, en de id ervan is opgenomen in het hostnaamadres. De zone-id wordt gegenereerd als een willekeurige tekenreeks wanneer de eerste SQL Managed Instance in elk VNet wordt gemaakt en dezelfde id wordt toegewezen aan alle andere exemplaren in hetzelfde subnet. Zodra de DNS-zone is toegewezen, kan deze niet meer worden gewijzigd. SQL Managed Instances die zijn opgenomen in dezelfde failovergroep, moeten de DNS-zone delen. U doet dit door de zone-id van het primaire exemplaar door te geven als de waarde van de parameter DnsZonePartner bij het maken van het secundaire exemplaar.

   > [!NOTE]
   > Zie een SQL Managed Instance toevoegen aan een failovergroep voor een gedetailleerde zelfstudie over het configureren van [failovergroepen](../managed-instance/failover-group-add-instance-tutorial.md)met SQL Managed Instance.

## <a name="upgrading-or-downgrading-a-primary-database"></a>Een primaire database upgraden of downgraden

U kunt een primaire database upgraden of downgraden naar een andere rekenkracht (binnen dezelfde servicelaag, niet tussen Algemeen en Bedrijfskritiek) zonder de verbinding met secundaire databases te verbreken. Bij het upgraden raden we u aan eerst alle secundaire databases bij te upgraden en vervolgens de primaire database bij te upgraden. Bij het downgraden keert u de volgorde om: downgrade eerst de primaire database en vervolgens alle secundaire databases. Bij het upgraden of downgraden van de database naar een andere servicelaag wordt deze aanbeveling afgedwongen.

Deze volgorde wordt aangeraden om te voorkomen dat het secundaire exemplaar bij een lagere SKU overbelast raakt en opnieuw moet worden geseed tijdens een upgrade- of downgradeproces. U kunt dit probleem ook vermijden door het primaire exemplaar alleen-lezen te maken. Dit gaat ten koste van alle lezen/schrijven-werkbelastingen die zijn gekoppeld aan het primaire exemplaar.

> [!NOTE]
> Als u een secundaire database hebt gemaakt als onderdeel van de configuratie van de failovergroep, wordt het niet aanbevolen om de secundaire database te downgraden. Dit is om ervoor te zorgen dat uw gegevenslaag voldoende capaciteit heeft om uw normale workload te verwerken nadat de failover is geactiveerd.

## <a name="preventing-the-loss-of-critical-data"></a>Voorkomen dat kritieke gegevens verloren gaan

Vanwege de hoge latentie van Wide Area Networks maakt continue kopie gebruik van een asynchrone replicatiemechanisme. Asynchrone replicatie zorgt ervoor dat gegevensverlies onvermijdelijk is als er een fout optreedt. Voor sommige toepassingen is echter mogelijk geen gegevensverlies vereist. Ter bescherming van deze essentiële updates [](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) kan een toepassingsontwikkelaar de sp_wait_for_database_copy_sync onmiddellijk na het uitvoeren van de transactie aanroepen. Het `sp_wait_for_database_copy_sync` aanroepen van blokkeert de aanroepthread totdat de laatste vastgelegde transactie is verzonden naar de secundaire database. Er wordt echter niet gewacht tot de verzonden transacties opnieuw zijn afgespeeld en op de secundaire worden vastgelegd. `sp_wait_for_database_copy_sync` is gericht op een specifieke continue kopieerkoppeling. Elke gebruiker met de verbindingsrechten voor de primaire database kan deze procedure aanroepen.

> [!NOTE]
> `sp_wait_for_database_copy_sync` voorkomt gegevensverlies na een failover, maar garandeert geen volledige synchronisatie voor leestoegang. De vertraging die wordt veroorzaakt door een procedureoproep kan aanzienlijk zijn en is afhankelijk van de grootte van het transactielogboek `sp_wait_for_database_copy_sync` op het moment van de aanroep.

## <a name="failover-groups-and-point-in-time-restore"></a>Failovergroepen en herstel naar een bepaald tijdstip

Zie Herstel naar een bepaald tijdstip [(PITR)](recovery-using-backups.md#point-in-time-restore)voor meer informatie over het gebruik van herstel naar een bepaald tijdstip met failovergroepen.

## <a name="limitations-of-failover-groups"></a>Beperkingen van failovergroepen

Let op de volgende beperkingen:

- Failovergroepen kunnen niet worden gemaakt tussen twee servers of exemplaren in dezelfde Azure-regio's.
- De naam van failovergroepen kan niet worden gewijzigd. U moet de groep verwijderen en opnieuw maken met een andere naam.
- De naam van de database wordt niet ondersteund voor exemplaren in de failovergroep. U moet de failovergroep tijdelijk verwijderen om de naam van een database te kunnen wijzigen.
- Systeemdatabases worden niet gerepliceerd naar het secundaire exemplaar in een failovergroep. Daarom zijn scenario's die afhankelijk zijn van objecten uit de systeemdatabases onmogelijk op het secundaire exemplaar, tenzij de objecten handmatig op de secundaire worden gemaakt.

## <a name="programmatically-managing-failover-groups"></a>Failovergroepen programmatisch beheren

Zoals eerder besproken, kunnen groepen voor automatische failover en actieve geo-replicatie ook programmatisch worden beheerd met behulp van Azure PowerShell en de REST API. In de volgende tabellen wordt de set beschikbare opdrachten beschreven. Actieve geo-replicatie bevat een set Azure Resource Manager API's voor beheer, waaronder de [cmdlets](/powershell/azure/) [Azure SQL Database REST API](/rest/api/sql/) en Azure PowerShell. Deze API's vereisen het gebruik van resourcegroepen en ondersteunen op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC). Zie Op rollen gebaseerd toegangsbeheer van [Azure (Azure RBAC)](../../role-based-access-control/overview.md)voor meer informatie over het implementeren van toegangsrollen.

### <a name="manage-sql-database-failover"></a>Failover SQL Database beheren

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

| Cmdlet | Beschrijving |
| --- | --- |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) |Met deze opdracht maakt u een failovergroep en registreert u deze op zowel primaire als secundaire servers|
| [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | Hiermee verwijdert u een failovergroep van de server |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Hiermee haalt u de configuratie van een failovergroep op |
| [Set-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/set-azsqldatabasefailovergroup) |Wijzigt de configuratie van een failovergroep |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | Activeert failover van een failovergroep naar de secundaire server |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|Voegt een of meer databases toe aan een failovergroep|

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

| Opdracht | Beschrijving |
| --- | --- |
| [az sql failover-group create](/cli/azure/sql/failover-group#az-sql-failover-group-create) |Met deze opdracht maakt u een failovergroep en registreert u deze op zowel primaire als secundaire servers|
| [az sql failover-group delete](/cli/azure/sql/failover-group#az-sql-failover-group-delete) | Hiermee verwijdert u een failovergroep van de server |
| [az sql failover-group show](/cli/azure/sql/failover-group#az-sql-failover-group-show) | Hiermee haalt u een failovergroepconfiguratie op |
| [az sql failover-group update](/cli/azure/sql/failover-group#az-sql-failover-group-update) |Hiermee wijzigt u de configuratie van een failovergroep en/of voegt u een of meer databases toe aan een failovergroep|
| [az sql failover-group set-primary](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | Activeert failover van een failovergroep naar de secundaire server |

# <a name="rest-api"></a>[Rest API](#tab/rest-api)

| API | Beschrijving |
| --- | --- |
| [Failovergroep maken of bijwerken](/rest/api/sql/failovergroups/createorupdate) | Hiermee wordt een failovergroep gemaakt of bijgewerkt |
| [Failovergroep verwijderen](/rest/api/sql/failovergroups/delete) | Hiermee verwijdert u een failovergroep van de server |
| [Failover (gepland)](/rest/api/sql/failovergroups/failover) | Activeert failover van de huidige primaire server naar de secundaire server met volledige gegevenssynchronisatie.|
| [Failover forceer gegevensverlies toestaan](/rest/api/sql/failovergroups/forcefailoverallowdataloss) | Activeert failover van de huidige primaire server naar de secundaire server zonder gegevens te synchroniseren. Deze bewerking kan leiden tot gegevensverlies. |
| [Failovergroep krijgen](/rest/api/sql/failovergroups/get) | Hiermee haalt u de configuratie van een failovergroep op. |
| [Failovergroepen per server opneren](/rest/api/sql/failovergroups/listbyserver) | Hiermee worden de failovergroepen op een server vermeld. |
| [Failovergroep bijwerken](/rest/api/sql/failovergroups/update) | Werkt de configuratie van een failovergroep bij. |

---

### <a name="manage-sql-managed-instance-failover"></a>Failover SQL Managed Instance beheren


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

| Cmdlet | Beschrijving |
| --- | --- |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup) |Met deze opdracht maakt u een failovergroep en registreert u deze op zowel primaire als secundaire exemplaren|
| [Set-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/set-azsqldatabaseinstancefailovergroup) |Wijzigt de configuratie van een failovergroep|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) |Hiermee haalt u de configuratie van een failovergroep op|
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) |Activeert failover van een failovergroep naar het secundaire exemplaar|
| [Remove-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/remove-azsqldatabaseinstancefailovergroup) | Hiermee verwijdert u een failovergroep|


# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

| Opdracht | Beschrijving |
| --- | --- |
| [az sql failover-group create](/cli/azure/sql/failover-group#az-sql-failover-group-create) |Met deze opdracht maakt u een failovergroep en registreert u deze op zowel primaire als secundaire servers|
| [az sql failover-group delete](/cli/azure/sql/failover-group#az-sql-failover-group-delete) | Hiermee verwijdert u een failovergroep van de server |
| [az sql failover-group show](/cli/azure/sql/failover-group#az-sql-failover-group-show) | Hiermee haalt u een failovergroepconfiguratie op |
| [az sql failover-group update](/cli/azure/sql/failover-group#az-sql-failover-group-update) |Hiermee wijzigt u de configuratie van een failovergroep en/of voegt u een of meer databases toe aan een failovergroep|
| [az sql failover-group set-primary](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | Activeert failover van een failovergroep naar de secundaire server |

# <a name="rest-api"></a>[Rest API](#tab/rest-api)

| API | Beschrijving |
| --- | --- |
| [Failovergroep maken of bijwerken](/rest/api/sql/instancefailovergroups/createorupdate) | Hiermee maakt of werkt u de configuratie van een failovergroep bij |
| [Failovergroep verwijderen](/rest/api/sql/instancefailovergroups/delete) | Hiermee verwijdert u een failovergroep uit het exemplaar |
| [Failover (gepland)](/rest/api/sql/instancefailovergroups/failover) | Activeert failover van het huidige primaire exemplaar naar dit exemplaar met volledige gegevenssynchronisatie. |
| [Failover forceer gegevensverlies toestaan](/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) | Activeert failover van het huidige primaire exemplaar naar het secundaire exemplaar zonder gegevens te synchroniseren. Deze bewerking kan leiden tot gegevensverlies. |
| [Failovergroep krijgen](/rest/api/sql/instancefailovergroups/get) | haalt de configuratie van een failovergroep op. |
| [Failovergroepen op een lijst zetten - Lijst op locatie](/rest/api/sql/instancefailovergroups/listbylocation) | Hiermee worden de failovergroepen op een locatie vermeld. |

---

## <a name="next-steps"></a>Volgende stappen

- Zie voor gedetailleerde zelfstudies
  - [Een SQL Database toevoegen aan een failovergroep](failover-group-add-single-database-tutorial.md)
  - [Een elastische pool aan een failovergroep toevoegen](failover-group-add-elastic-pool-tutorial.md)
  - [Een SQL Managed Instance toevoegen aan een failovergroep](../managed-instance/failover-group-add-instance-tutorial.md)
- Zie voor voorbeeldscripts:
  - [PowerShell gebruiken voor het configureren van actieve geo-replicatie voor Azure SQL Database](scripts/setup-geodr-and-failover-database-powershell.md)
  - [PowerShell gebruiken voor het configureren van actieve geo-replicatie voor een pooldatabase in Azure SQL Database](scripts/setup-geodr-and-failover-elastic-pool-powershell.md)
  - [PowerShell gebruiken om een Azure SQL Database aan een failovergroep toe te voegen](scripts/add-database-to-failover-group-powershell.md)
- Zie Overzicht van bedrijfscontinuïteit voor een overzicht van [bedrijfscontinuïteit en scenario's](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- Zie voor meer Azure SQL Database automatische back-ups [SQL Database automatische back-ups.](automated-backups-overview.md)
- Zie Restore [a database from the service-initiated backups (Een database](recovery-using-backups.md)herstellen vanuit de door de service geïnitieerde back-ups) voor meer informatie over het gebruik van automatische back-ups voor herstel.
- Zie Beveiliging na herstel na noodherstel voor meer informatie over de verificatievereisten voor een nieuwe primaire server [SQL Database database.](active-geo-replication-security-configure.md)

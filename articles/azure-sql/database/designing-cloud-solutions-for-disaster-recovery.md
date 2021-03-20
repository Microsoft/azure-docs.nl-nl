---
title: Wereld wijd beschik bare Services ontwerpen
description: Meer informatie over het ontwerpen van toepassingen voor Maxi maal beschik bare Services met behulp van Azure SQL Database.
keywords: herstel na nood geval in de Cloud, oplossingen voor herstel na nood gevallen, back-ups van app-gegevens, geo-replicatie, planning voor bedrijfs continuïteit
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein
ms.date: 07/28/2020
ms.openlocfilehash: be632ba06edc858e7eadcd6e57a4f7769f69f2cb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91321676"
---
# <a name="designing-globally-available-services-using-azure-sql-database"></a>Wereld wijd beschik bare Services ontwerpen met behulp van Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Wanneer u Cloud Services bouwt en implementeert met Azure SQL Database, gebruikt u actieve groepen voor [geo-replicatie](active-geo-replication-overview.md) of [automatische failover](auto-failover-group-overview.md) om te voorzien in tolerantie voor regionale storingen en crashes. Met deze functie kunt u wereld wijd gedistribueerde toepassingen maken die zijn geoptimaliseerd voor lokale toegang tot de gegevens. In dit artikel worden algemene toepassings patronen beschreven, met inbegrip van de voor delen en de afweging van elke optie.

> [!NOTE]
> Als u Premium-of Bedrijfskritiek-data bases en elastische Pools gebruikt, kunt u ze voor het maken van regionale storingen gebruiken door ze te converteren naar zone redundante implementatie configuratie. Zie [zone-redundante data bases](high-availability-sla.md).  

## <a name="scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime"></a>Scenario 1: twee Azure-regio's gebruiken voor bedrijfs continuïteit met minimale downtime

In dit scenario hebben de toepassingen de volgende kenmerken:

* De toepassing is actief in een Azure-regio
* Alle database sessies vereisen lees-en schrijf toegang (RW) naar gegevens
* De weblaag en de gegevenslaag moeten co zijn om latentie en verkeers kosten te beperken
* In het fundamenteel is uitval tijd een hoger bedrijfs risico voor deze toepassingen dan gegevens verlies

In dit geval is de implementatie topologie van de toepassing geoptimaliseerd voor het afhandelen van regionale rampen wanneer alle toepassings onderdelen met elkaar moeten worden uitgevoerd. In het onderstaande diagram ziet u deze topologie. Voor geografische redundantie worden de bronnen van de toepassing geïmplementeerd in regio A en B. De resources in regio B worden echter pas gebruikt als de regio A mislukt. Er wordt een failovergroep geconfigureerd tussen de twee regio's voor het beheren van de database connectiviteit, replicatie en failover. De webservice in beide regio's is geconfigureerd voor toegang tot de data base via de Read-Write listener **&lt; failover-group-name &gt; . database.Windows.net** (1). Azure Traffic Manager is ingesteld voor het gebruik van een [prioriteits routerings methode](../../traffic-manager/traffic-manager-configure-priority-routing-method.md) (2).  

> [!NOTE]
> [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) wordt alleen in dit artikel gebruikt voor illustratie doeleinden. U kunt elke oplossing voor taak verdeling gebruiken die ondersteuning biedt voor een routerings methode met prioriteit.

In het volgende diagram ziet u deze configuratie vóór een storing:

![Scenario 1. Configuratie vóór de storing.](./media/designing-cloud-solutions-for-disaster-recovery/scenario1-a.png)

Na een storing in de primaire regio, SQL Database detecteert dat de primaire data base niet toegankelijk is en de failover naar de secundaire regio wordt geactiveerd op basis van de para meters van het beleid voor automatische failover (1). Afhankelijk van de SLA van uw toepassing kunt u een respijt periode configureren die de tijd bepaalt tussen de detectie van de storing en de failover zelf. Het is mogelijk dat Azure Traffic Manager de failover van het eind punt initieert voordat de failover-groep de failover van de data base activeert. In dat geval kan de webtoepassing niet onmiddellijk opnieuw verbinding maken met de data base. De verbindingen worden echter automatisch uitgevoerd zodra de data base-failover is voltooid. Wanneer de regio mislukt en weer online wordt gezet, wordt de oude primaire automatisch opnieuw verbonden als een nieuwe secundaire. In het onderstaande diagram ziet u de configuratie na een failover.

> [!NOTE]
> Alle trans acties die zijn doorgevoerd na de failover, gaan verloren tijdens het opnieuw verbinden. Nadat de failover is voltooid, kan de toepassing in regio B opnieuw verbinding maken en opnieuw starten met het verwerken van de gebruikers aanvragen. Zowel de webtoepassing als de primaire data base bevinden zich nu in regio B en blijven co-locatie.

![Scenario 1. Configuratie na failover](./media/designing-cloud-solutions-for-disaster-recovery/scenario1-b.png)

Als er een storing optreedt in regio B, wordt het replicatie proces tussen de primaire en de secundaire data base onderbroken, maar de koppeling tussen de twee blijft intact (1). Traffic Manager detecteert dat de verbinding met regio B wordt verbroken en markeert de eind punt-web-app 2 als verslechterd (2). De prestaties van de toepassing worden in dit geval niet beïnvloed, maar de data base wordt weer gegeven en daarom is er een hoger risico op gegevens verlies in het geval van regio A mislukt.

> [!NOTE]
> Voor herstel na nood gevallen is het raadzaam om de configuratie met toepassings implementatie beperkt tot twee regio's. Dit komt omdat de meeste Azure-grafieken slechts twee regio's hebben. Deze configuratie beveiligt uw toepassing niet tegen een gelijktijdige storing van beide regio's. In het onwaarschijnlijke geval van een dergelijke fout kunt u uw data bases herstellen in een derde regio met behulp van [geo-restore-bewerking](disaster-recovery-guidance.md#recover-using-geo-restore).
>

 Zodra de storing is verholpen, wordt de secundaire data base automatisch opnieuw gesynchroniseerd met de primaire. Tijdens de synchronisatie kan dit van invloed zijn op de prestaties van de primaire. De specifieke impact is afhankelijk van de hoeveelheid gegevens die de nieuwe primaire heeft verkregen sinds de failover. 

> [!NOTE]
> Nadat de storing is gereduceerd, wordt de verbinding met de verbindingen met de toepassing in regio A door Traffic Manager gerouteerd naar een eind punt met een hogere prioriteit. Als u van plan bent om de primaire in regio B een tijdje te houden, moet u de prioriteits tabel in het Trafic Manager-profiel dienovereenkomstig wijzigen. 
>
 
 In het volgende diagram ziet u een storing in de secundaire regio:

![Scenario 1. Configuratie na een storing in de secundaire regio.](./media/designing-cloud-solutions-for-disaster-recovery/scenario1-c.png)

De belangrijkste **voor delen** van dit ontwerp patroon zijn:

* Dezelfde webtoepassing wordt geïmplementeerd naar beide regio's zonder enige specifieke configuratie van de regio en vereist geen aanvullende logica voor het beheren van failover.
* De prestaties van de toepassing worden niet beïnvloed door failover, omdat de webtoepassing en de data base altijd naast elkaar staan.

De belangrijkste **balans** is dat de toepassings bronnen in regio B de meeste tijd worden benut.

## <a name="scenario-2-azure-regions-for-business-continuity-with-maximum-data-preservation"></a>Scenario 2: Azure-regio's voor bedrijfs continuïteit met maximale gegevens behoud

Deze optie is het meest geschikt voor toepassingen met de volgende kenmerken:

* Gegevens verlies is een hoog bedrijfs risico. De failover van de data base kan alleen worden gebruikt als een laatste redmiddel als de storing wordt veroorzaakt door een onherstelbare fout.
* De toepassing biedt ondersteuning voor alleen-lezen en lezen/schrijven-modi van bewerkingen en kan gedurende een bepaalde tijd worden uitgevoerd in de modus alleen-lezen.

In dit patroon wordt de toepassing overgeschakeld naar de modus alleen-lezen wanneer er time-outfouten worden opgehaald met de lees-en schrijf bewerkingen. De webtoepassing wordt geïmplementeerd naar beide regio's en bevat een verbinding met het listener-eind punt voor lezen en schrijven en een andere verbinding met het alleen-lezen listener-eind punt (1). Het Traffic Manager profiel moet [prioriteits routering](../../traffic-manager/traffic-manager-configure-priority-routing-method.md)gebruiken. [Eind punt bewaking](../../traffic-manager/traffic-manager-monitoring.md) moet zijn ingeschakeld voor het toepassings eindpunt in elke regio (2).

Het volgende diagram illustreert deze configuratie vóór een storing:

![Scenario 2. Configuratie vóór de storing.](./media/designing-cloud-solutions-for-disaster-recovery/scenario2-a.png)

Wanneer Traffic Manager een verbindings fout naar regio A detecteert, wordt gebruikers verkeer automatisch overgeschakeld naar het toepassings exemplaar in regio B. Met dit patroon is het belang rijk dat u de respijt periode met gegevens verlies instelt op een voldoende hoge waarde, bijvoorbeeld 24 uur. Het zorgt ervoor dat gegevens verlies wordt voor komen als de storing binnen die tijd wordt verholpen. Wanneer de webtoepassing in regio B wordt geactiveerd, mislukken de lees-en schrijf bewerkingen. Op dat moment moet het overschakelen naar de modus alleen-lezen (1). In deze modus worden de aanvragen automatisch doorgestuurd naar de secundaire data base. Als de storing wordt veroorzaakt door een onherstelbare fout, kan dit waarschijnlijk niet worden verholpen binnen de respijt periode. Wanneer de failover-groep wordt verloopt, wordt de failover geactiveerd. Daarna wordt de lees-/schrijftoegang beschikbaar en worden de verbindingen met de listener niet meer uitgevoerd (2). In het volgende diagram ziet u de twee fasen van het herstel proces.

> [!NOTE]
> Als de onderbreking in de primaire regio binnen de respijt periode wordt verholpen, Traffic Manager detecteert het herstel van de verbinding in de primaire regio en wordt het gebruikers verkeer naar de instantie van de toepassing in regio A geswitcheerd. Het toepassings exemplaar wordt hervat en werkt in de modus lezen-schrijven met behulp van de primaire data base in regio A zoals geïllustreerd door het vorige diagram.

![Scenario 2. Nood herstel fasen.](./media/designing-cloud-solutions-for-disaster-recovery/scenario2-b.png)

Als er een storing optreedt in regio B, Traffic Manager detecteert de fout van de web-app-2 van het eind punt in regio B en markeert deze (1). In de tussen tijd schakelt de failover-groep de alleen-lezen listener over naar regio A (2). Deze storing heeft geen invloed op de ervaring van de eind gebruiker, maar de primaire data base wordt weer gegeven tijdens de onderbreking. In het volgende diagram ziet u een fout in de secundaire regio:

![Scenario 2. Storing van de secundaire regio.](./media/designing-cloud-solutions-for-disaster-recovery/scenario2-c.png)

Zodra de storing is verholpen, wordt de secundaire data base onmiddellijk gesynchroniseerd met de primaire en de alleen-lezen listener wordt weer teruggeschakeld naar de secundaire data base in regio B. Tijdens de synchronisatie prestaties van de primaire kan dit enigszins worden beïnvloed, afhankelijk van de hoeveelheid gegevens die moeten worden gesynchroniseerd.

Dit ontwerp patroon heeft verschillende **voor delen**:

* Er wordt voor komen dat gegevens verloren gaan tijdens de tijdelijke uitval.
* Uitval tijd is alleen afhankelijk van hoe snel Traffic Manager de verbindings fout detecteert die kan worden geconfigureerd.

De **balans** is dat de toepassing in de modus alleen-lezen moet kunnen worden uitgevoerd.

## <a name="scenario-3-application-relocation-to-a-different-geography-without-data-loss-and-near-zero-downtime"></a>Scenario 3: herlocatie van de toepassing naar een andere geografie zonder gegevens verlies en bijna nul downtime

In dit scenario heeft de toepassing de volgende kenmerken:

* De eind gebruikers hebben toegang tot de toepassing vanuit verschillende geografische gebieden
* De toepassing bevat alleen-lezen workloads die niet afhankelijk zijn van een volledige synchronisatie met de nieuwste updates
* Schrijf toegang tot gegevens moet worden ondersteund in dezelfde geografie voor het meren deel van de gebruikers
* Lees latentie is essentieel voor de ervaring van de eind gebruiker

Om te voldoen aan deze vereisten moet worden gegarandeerd dat het gebruikers apparaat **altijd** verbinding maakt met de toepassing die is geïmplementeerd in dezelfde geografie voor de alleen-lezen bewerkingen, zoals het bladeren door gegevens, analyses, enzovoort. Terwijl de OLTP-bewerkingen in de **meeste tijd** worden verwerkt. Gedurende de tijd dat de OLTP-bewerkingen van de dag worden uitgevoerd, worden deze in dezelfde geografie verwerkt, maar gedurende de buitenste uren die ze in een andere Geografie kunnen verwerken. Als de activiteit van de eind gebruiker voornamelijk plaatsvindt tijdens de werk uren, kunt u de optimale prestaties voor de meeste gebruikers van de tijd garanderen. In het volgende diagram ziet u deze topologie.

De resources van de toepassing moeten in elke geografie worden geïmplementeerd, waar u een substantiële gebruiks vraag hebt. Als uw toepassing bijvoorbeeld actief wordt gebruikt in de Verenigde Staten, moeten de Europese Unie en Zuid Azië-oost de toepassing worden geïmplementeerd in al deze geografische gebieden. De primaire data base moet dynamisch worden overgeschakeld van het ene geografie naar het volgende aan het einde van de werk uren. Deze methode wordt ' de zon volgen ' genoemd. De OLTP-werk belasting maakt altijd verbinding met de data base via de Read-Write-listener **&lt; failover-group-name &gt; . database.Windows.net** (1). De alleen-lezen workload maakt rechtstreeks verbinding met de lokale data base met behulp van de server **&lt; naam &gt; . database.Windows.net** (2) van de data baseserver. Traffic Manager is geconfigureerd met de [methode voor de route ring van prestaties](../../traffic-manager/traffic-manager-configure-performance-routing-method.md). Hiermee zorgt u ervoor dat het apparaat van de eind gebruiker is verbonden met de webservice in de dichtstbijzijnde regio. Traffic Manager moet worden ingesteld als de eindpunt bewaking is ingeschakeld voor elk eind punt van de webservice (3).

> [!NOTE]
> De configuratie van de failovergroep definieert welke regio voor failover wordt gebruikt. Omdat de nieuwe primaire locatie zich in een andere geografie bevindt, resulteert de failover in langere latentie voor zowel OLTP-als alleen-lezen workloads totdat de betrokken regio weer online is.

![Scenario 3. Configuratie met primair in VS-Oost.](./media/designing-cloud-solutions-for-disaster-recovery/scenario3-a.png)

Aan het einde van de dag, bijvoorbeeld om 11 uur lokale tijd, moeten de actieve data bases worden overgeschakeld naar de volgende regio (Europa-noord). Deze taak kan volledig worden geautomatiseerd met behulp van [Azure Logic apps](../../logic-apps/logic-apps-overview.md). De taak omvat de volgende stappen:

* Primaire server in de failovergroep overschakelen naar Europa-noord met behulp van een beschrijvende failover (1)
* De failovergroep verwijderen tussen de VS-Oost en het Europa-noord
* Maak een nieuwe failovergroep met dezelfde naam, maar tussen Europa-noord en Azië-oost (2).
* Voeg de primaire in Europa-noord en secundaire in Azië-oost toe aan deze failovergroep (3).

In het volgende diagram ziet u de nieuwe configuratie na de geplande failover:

![Scenario 3. Overgang van de primaire naar Europa-noord.](./media/designing-cloud-solutions-for-disaster-recovery/scenario3-b.png)

Als er zich een storing voordoet in Europa-noord bijvoorbeeld, wordt de failover van de automatische data base geïnitieerd door de failoverrelatie, waardoor de toepassing effectief wordt verplaatst naar de volgende regio voor planning (1).  In dat geval is de VS-Oost de enige resterende secundaire regio totdat Europa-noord weer online is. De overige twee regio's leveren de klanten in alle drie de geografische gebieden door rollen te scha kelen. Azure Logic Apps moet dienovereenkomstig worden aangepast. Omdat de resterende regio's extra gebruikers verkeer van Europa verkrijgen, worden de prestaties van de toepassing niet alleen beïnvloed door extra latentie, maar ook door een verhoogd aantal verbindingen voor eind gebruikers. Zodra de onderbreking in Europa-noord is verholpen, wordt de secundaire data base onmiddellijk gesynchroniseerd met de huidige primaire. Het volgende diagram illustreert een onderbreking in Europa-noord:

![Scenario 3. Uitval in Europa-noord.](./media/designing-cloud-solutions-for-disaster-recovery/scenario3-c.png)

> [!NOTE]
> U kunt de tijd beperken waarop de ervaring van de eind gebruiker in Europa wordt gedegradeerd door de lange latentie. U moet proactief een toepassings kopie implementeren en de secundaire data base (s) in een andere lokale regio (Europa-west) maken als vervanging van het exemplaar van de offline toepassing in Europa-noord. Wanneer de laatste weer online is, kunt u bepalen of u Europa-west wilt blijven gebruiken of dat u de kopie van de toepassing daar wilt verwijderen en terug wilt gaan naar het gebruik van Europa-noord.

De belangrijkste **voor delen** van dit ontwerp zijn:

* De werk belasting van de alleen-lezen toepassing heeft altijd toegang tot de gegevens in de behuizing-regio.
* De werk belasting van de toepassing lezen-schrijven heeft toegang tot gegevens in de dichtstbijzijnde regio tijdens de periode van de hoogste activiteit in elke Geografie
* Omdat de toepassing wordt geïmplementeerd in meerdere regio's, kan het een verlies van een van de regio's belopen zonder aanzienlijke uitval tijd.

Maar er zijn een aantal voor **afwegingen**:

* Een regionale storing leidt ertoe dat de geografie wordt beïnvloed door langere latentie. Alleen werk belastingen met lees-en schrijf bewerkingen en alleen-lezen taken worden door de toepassing in een andere geografie bediend.
* De alleen-lezen workloads moeten verbinding maken met een ander eind punt in elke regio.

## <a name="business-continuity-planning-choose-an-application-design-for-cloud-disaster-recovery"></a>Planning voor bedrijfs continuïteit: Kies een toepassings ontwerp voor nood herstel in de Cloud

Met uw specifieke strategie voor nood herstel in de cloud kunt u deze ontwerp patronen combi neren of uitbreiden om optimaal te voldoen aan de behoeften van uw toepassing.  Zoals eerder vermeld, is de strategie die u kiest, gebaseerd op de SLA die u aan uw klanten wilt aanbieden en de topologie voor de implementatie van de toepassing. Om u te helpen uw beslissing te nemen, vergelijkt de volgende tabel de keuzes op basis van Recovery Point Objective (RPO) en geschatte herstel tijd (ERT).

| Patroon | RPO | ERT |
|:--- |:--- |:--- |
| Actieve-passieve implementatie voor herstel na nood gevallen met toegang tot de Data Base op basis van co-locaties |Lees-/schrijftoegang < 5 sec |Fout detectie tijd + DNS-TTL |
| Actief-actief-implementatie voor taak verdeling van toepassingen |Lees-/schrijftoegang < 5 sec |Fout detectie tijd + DNS-TTL |
| Actieve-passieve implementatie voor gegevens behoud |Alleen-lezen toegang < 5 sec | Alleen-lezen toegang = 0 |
||Lees-/schrijftoegang = nul | Lezen-schrijven toegang = fout detectie tijd + respijt periode met gegevens verlies |
|||

## <a name="next-steps"></a>Volgende stappen

* Zie [overzicht van bedrijfs continuïteit](business-continuity-high-availability-disaster-recover-hadr-overview.md) voor een overzicht en scenario's voor bedrijfs continuïteit
* Zie [actieve geo-replicatie](active-geo-replication-overview.md)voor meer informatie over actieve geo-replicatie.
* Zie [groepen met automatische failover](auto-failover-group-overview.md)voor meer informatie over groepen met automatische failover.
* Zie [strategieën voor nood herstel voor elastische Pools](disaster-recovery-strategies-for-applications-with-elastic-pool.md)voor meer informatie over actieve geo-replicatie met elastische Pools.

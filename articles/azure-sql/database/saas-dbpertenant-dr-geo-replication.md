---
title: Herstel na noodgeval voor SaaS-apps met geo-replicatie
description: Meer informatie over het gebruik van geo-replica's van Azure SQL Database om een multitenant-SaaS-app te herstellen in het geval van een storing
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: d2c1ddd1e0b5a080050e1ffeb28eded98dbfea3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97652092"
---
# <a name="disaster-recovery-for-a-multi-tenant-saas-application-using-database-geo-replication"></a>Herstel na noodgeval voor een multitenant-SaaS-toepassing met geo-replicatie van databases
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In deze zelfstudie verkent u een scenario voor volledig herstel na een noodgeval voor een multitenant-SaaS-toepassing die is geïmplementeerd met behulp van het database-per-tenant-model. Als u de app wilt beveiligen tegen storingen, gebruikt u [_geo-replicatie_](active-geo-replication-overview.md) om replica's voor de catalogus en tenantdatabases te maken in een alternatieve herstelregio. Als er een storing optreedt, kunt u snel een failover uitvoeren naar deze replica's om de normale werking te hervatten. Bij een failover worden de databases in de oorspronkelijke regio secundaire replica's van de databases in de herstelregio. Zodra deze replica's weer online zijn, worden ze automatisch bijgewerkt naar de status van de databases in de herstelregio. Als de storing is opgelost, wordt er een failback uitgevoerd naar de databases in de oorspronkelijke productieregio.

In deze zelfstudie worden de werkstromen voor failover-en failback verkend. U leert het volgende:
> [!div class="checklist"]
> 
> * Configuratiegegevens voor databases en elastische pools synchroniseren met de tenantcatalogus
> * Een herstelomgeving instellen in een alternatieve regio, met inbegrip van de toepassing, servers en pools
> * _Geo-replicatie_ gebruiken om de catalogus- en tenantdatabases te repliceren naar de herstelregio
> * Failover van de toepassings-, catalogus- en tenantdatabases naar de herstelregio uitvoeren 
> * Failback van de toepassings-, catalogus- en tenantdatabases naar de oorspronkelijke regio uitvoeren als de storing is opgelost
> * De catalogus bijwerken tijdens het uitvoeren van een failover van elke tenantdatabase om de primaire locatie van elke tenantdatabase bij te houden
> * Ervoor zorgen dat de toepassings- en de primaire tenantdatabase zich altijd allebei in dezelfde Azure-regio bevinden om de latentie te verminderen  


Voordat u aan deze zelfstudie begint, moet aan de volgende vereisten zijn voldaan:
* De Wingtip Tickets-SaaS-database-per-tenant-app is geïmplementeerd. Zie [De Wingtip Tickets SaaS-database-per-tenant-toepassing implementeren en verkennen](saas-dbpertenant-get-started-deploy.md) om in minder dan vijf minuten te implementeren  
* Azure PowerShell is geïnstalleerd. Zie [Aan de slag met Azure PowerShell](/powershell/azure/get-started-azureps) voor meer informatie.

## <a name="introduction-to-the-geo-replication-recovery-pattern"></a>Inleiding tot het herstelpatroon met geo-replicatie

![Architectuur voor herstel](./media/saas-dbpertenant-dr-geo-replication/recovery-architecture.png)

Herstel na noodgevallen is een belangrijke overweging voor veel toepassingen, ongeacht of dit om nalevingsredenen of voor de bedrijfscontinuïteit is. Als er sprake is van een langdurige serviceonderbreking, kan een goed voorbereid plan voor herstel na noodgevallen de bedrijfsonderbreking tot een minimum terugbrengen. Het gebruik van geo-replicatie biedt de laagste RPO en RTO door databasereplica's in een herstelregio te onderhouden waarvoor op korte termijn failover kan worden uitgevoerd.

Een plan voor herstel na noodgevallen op basis van geo-replicatie bestaat uit drie afzonderlijke onderdelen:
* Instellen: het maken en onderhouden van de herstelomgeving
* Herstel: failover van de app en databases naar de herstelomgeving als er een storing optreedt
* Repatriëring: failback van de app en databases naar de oorspronkelijke regio als de storing is opgelost 

Alle onderdelen moeten zorgvuldig worden gepland, met name bij het werken op schaal. Over het algemeen moeten met het plan verschillende doelen worden bereikt:

* Instellen
    * Stel een omgeving in voor het maken en onderhouden van een gespiegelde installatiekopie in de herstelregio. Door de elastische pools te maken en alle databases in deze herstelomgeving te repliceren, wordt capaciteit in de herstelregio gereserveerd. Het onderhouden van deze omgeving omvat het repliceren van nieuwe tenantdatabases wanneer deze worden ingericht.  
* Herstel
    * Wanneer een omlaag geschaalde herstelomgeving wordt gebruikt om de dagelijkse kosten te minimaliseren, moeten pools en databases omhoog worden geschaald om volledige operationele capaciteit te verkrijgen in de herstelregio
     * Inrichting van nieuwe tenants in de herstelregio zo snel mogelijk inschakelen  
     * Geoptimaliseerd zijn om tenants te herstellen in volgorde van prioriteit
     * Geoptimaliseerd zijn om tenants zo snel mogelijk online te krijgen door stappen parallel uit te voeren als dit praktisch is
     * Flexibel zijn met betrekking tot fouten, herstartbaar en idempotent
     * In staat zijn om het herstelproces vóór voltooiing te annuleren als de oorspronkelijke regio weer online komt.
* Repatriëring 
     * Failover van databases in de herstelregio naar replica's in de oorspronkelijke regio met minimale gevolgen voor tenants: zonder gegevensverlies en met een minimale offlineperiode per tenant.

In deze zelfstudie worden functies van Azure SQL Database en het Azure-platform gebruikt om deze uitdagingen het hoofd te bieden:

* [Azure Resource Manager-sjablonen](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) om zo snel mogelijk alle benodigde capaciteit te reserveren. Azure Resource Manager-sjablonen worden gebruikt om een gespiegelde installatiekopie van de productieservers en elastische pools in de herstelregio in te richten.
* [Geo-replicatie](active-geo-replication-overview.md) om asynchroon gerepliceerde, alleen-lezen-secundaire replica's voor alle databases te maken. Bij een storing wordt een failover uitgevoerd naar de replica's in de herstelregio.  Als de storing is opgelost, wordt een failback uitgevoerd naar de databases in de oorspronkelijke productieregio.
* [Asynchrone](../../azure-resource-manager/management/async-operations.md) failover-bewerkingen worden verzonden in de volgorde van de tenantprioriteit om de failover-tijd voor veel databases te minimaliseren.
* [Shard-beheerherstelfuncties](elastic-database-recovery-manager.md) om databasevermeldingen in de catalogus te wijzigen tijdens het herstel en de repatriëring. Met deze functies kan de app verbinding maken met tenantdatabases, ongeacht de locatie, zonder de app opnieuw te hoeven configureren.
* [DNS-aliassen van SQL Server](./dns-alias-overview.md) om naadloze inrichting van nieuwe tenants mogelijk te maken, ongeacht in welke regio de app wordt uitgevoerd. DNS-aliassen worden ook gebruikt om het catalogussynchronisatieproces toe te staan om verbinding te maken met de actieve catalogus, ongeacht de locatie.

## <a name="get-the-disaster-recovery-scripts"></a>De scripts voor herstel na noodgevallen ophalen 

> [!IMPORTANT]
> Net als alle Wingtip Tickets-beheerscripts zijn de scripts voor herstel na noodgevallen slechts een voorbeeld. Ze zijn niet geschikt voor een productieomgeving. 

De herstelscripts en de broncode van de Wingtip-toepassing die in deze zelfstudie worden gebruikt, zijn beschikbaar in de [Wingtip Tickets-SaaS-database-per-tenant-GitHub-opslagplaats](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/). Bekijk de [algemene richtlijnen](saas-tenancy-wingtip-app-guidance-tips.md) voor stappen om de Wingtip Tickets-beheerscripts te downloaden en deblokkeren.

## <a name="tutorial-overview"></a>Overzicht van de zelfstudie
In deze zelfstudie gebruikt u geo-replicatie voor het maken van replica's van de Wingtip Tickets-toepassing en de bijbehorende databases in een andere regio. Vervolgens voert u een failover uit naar deze regio om het herstellen van een storing te simuleren. Wanneer dit is voltooid, is de toepassing volledig functioneel in de herstelregio.

Later voert u in een afzonderlijke repatriëringsstap een failback uit van de catalogus- en tenantdatabases in de herstelregio naar de oorspronkelijke regio. De toepassing en de databases blijven beschikbaar tijdens de repatriëring. Wanneer dit is voltooid, is de toepassing volledig functioneel in de oorspronkelijke regio.

> [!Note]
> De toepassing wordt hersteld in de _gekoppelde regio_ van de regio waarin de toepassing is geïmplementeerd. Zie [Gekoppelde Azure-regio's](../../best-practices-availability-paired-regions.md) voor meer informatie.

## <a name="review-the-healthy-state-of-the-application"></a>De status van de toepassing controleren

Voordat u het herstelproces start, controleert u de normale status van de toepassing.
1. Open in uw webbrowser de Wingtip Tickets-events hub (http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.neten vervang &lt; user&gt; door de gebruikerswaarde van uw implementatie).
    * Ga naar de onderkant van de pagina. U ziet hier de naam en locatie van de catalogusserver in de voettekst. De locatie is de regio waarin u de app hebt geïmplementeerd.
    *TIP: Plaats de muisaanwijzer op de locatie om de weergave te vergroten.* 
    ![Status van een events hub in de oorspronkelijke regio](./media/saas-dbpertenant-dr-geo-replication/events-hub-original-region.png)

2. Klik op de tenant Contoso Concert Hall en open de bijbehorende gebeurtenispagina.
    * In de voettekst ziet u de servernaam van de tenant. De locatie is gelijk aan de locatie van de catalogusserver.

3. Ga naar [Azure Portal](https://portal.azure.com) en open de resourcegroep waarin de app is geïmplementeerd
    * Let op de regio waarin de servers zijn geïmplementeerd. 

## <a name="sync-tenant-configuration-into-catalog"></a>De tenantconfiguratie synchroniseren met de catalogus

In deze taak start u een proces voor het synchroniseren van de configuratie van de servers, elastische pools en databases met de tenantcatalogus. Het proces houdt deze informatie up-to-date in de catalogus.  Het proces werkt met de actieve catalogus, in de oorspronkelijke regio of in de herstelregio. De configuratie-informatie wordt gebruikt in het herstelproces om ervoor te zorgen dat de herstelomgeving consistent is met de oorspronkelijke omgeving, en later bij de repatriëring om ervoor te zorgen dat de oorspronkelijke regio consistent wordt gemaakt met de wijzigingen die zijn aangebracht in de herstelomgeving. De catalogus wordt ook gebruikt om de herstelstatus van tenantresources bij te houden

> [!IMPORTANT]
> Ter vereenvoudiging worden het synchronisatieproces en andere langlopende herstel- en repatriëringsprocessen in deze zelfstudies geïmplementeerd als lokale PowerShell-taken of -sessies die worden uitgevoerd onder de aanmeldingsgegeven van de client. De verificatietokens die worden uitgegeven wanneer u zich aanmeldt, verlopen na enkele uren verstrijkt. Hierna mislukken de taken. In een productiescenario moeten langlopende processen worden geïmplementeerd als betrouwbare Azure-services van een bepaald type die worden uitgevoerd onder een service-principal. Zie [Azure PowerShell gebruiken om een service-principal met een certificaat te maken](../../active-directory/develop/howto-authenticate-service-principal-powershell.md).

1. Open in _PowerShell ISE_ het bestand ...\Learning Modules\UserConfig.psm1. Vervang `<resourcegroup>` en `<user>` op de regels 10 en 11 door de waarde die is gebruikt bij het implementeren van de app.  Sla het bestand op.

2. Open in *PowerShell ISE* het script ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 en stel het volgende in:
    * **$DemoScenario = 1**: start een achtergrondtaak waarmee de tenantserver- en poolconfiguratiegegevens met de catalogus worden gesynchroniseerd

3. Druk op **F5** om het synchronisatiescript uit te voeren. Er wordt een nieuwe PowerShell-sessie geopend voor het synchroniseren van de configuratie van tenantresources.
![Schermopname met de nieuwe PowerShell-sessie, geopend voor het synchroniseren van de configuratie van tenantresources.](./media/saas-dbpertenant-dr-geo-replication/sync-process.png)

Laat het PowerShell-venster actief op de achtergrond en ga verder met de rest van de zelfstudie. 

> [!Note]
> In het synchronisatieproces komt er een verbinding met de catalogus tot stand via een DNS-alias. Deze alias wordt gewijzigd tijdens herstellen en repatriëren om naar de actieve catalogus te verwijzen. Het synchronisatieproces houdt de catalogus up-to-date met alle wijzigingen aan de database- of poolconfiguratie die in de herstelregio worden aangebracht.  Tijdens de repatriëring worden deze wijzigingen toegepast op de equivalente resources in de oorspronkelijke regio.

## <a name="create-secondary-database-replicas-in-the-recovery-region"></a>Secundaire databasereplica's maken in de herstelregio

In deze taak start u een proces dat een duplicaat van een app-instantie implementeert en de catalogus en alle tenantdatabases repliceert naar een herstelregio.

> [!Note]
> In deze zelfstudie wordt beveiligde geo-replicatie toegevoegd aan de voorbeeldtoepassing Wingtip Tickets. In een productieomgeving zou voor een toepassing die gebruikmaakt van geo-replicatie, elke tenant vanaf het begin worden ingericht met een database met geo-replicatie. Zie [Services met hoge beschikbaarheid ontwerpen met behulp van Azure SQL Database](designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)

1. Open in *PowerShell ISE* het script ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 en stel de volgende waarden in:
    * **$DemoScenario = 2**: de herstelomgeving voor gespiegelde installatiekopieën maken en de catalogus en tenantdatabases repliceren

2. Druk op **F5** om het script uit te voeren. Er wordt een nieuwe PowerShell-sessie geopend om de replica's te maken.
![Synchronisatieproces](./media/saas-dbpertenant-dr-geo-replication/replication-process.png)  

## <a name="review-the-normal-application-state"></a>De normale toepassingsstatus controleren

De toepassing wordt op dit moment normaal uitgevoerd in de oorspronkelijke regio en wordt nu beveiligd door geo-replicatie.  Er zijn alleen-lezen secundaire replica's in de herstelregio voor alle databases. 

1. Als u uw resourcegroepen in Azure Portal bekijkt, ziet u dat er een resourcegroep met het achtervoegsel -recovery (herstel) is gemaakt in de herstelregio. 

2. Verken de resources in de herstelresourcegroep.  

3. Klik op de database Contoso Concert Hall op de server _tenants1-dpt-&lt;gebruiker&gt;-recovery_.  Klik op geo-replicatie aan de linkerkant. 

    ![Geo-replicatiekoppeling voor Contoso Concert](./media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication.png) 

Noteer in de kaart met Azure-regio's de geo-replicatiekoppeling tussen de primaire replica in de oorspronkelijke regio en de secundaire replica in de herstelregio.  

## <a name="fail-over-the-application-into-the-recovery-region"></a>Failover van de toepassing naar de herstelregio

### <a name="geo-replication-recovery-process-overview"></a>Overzicht van het herstelproces met geo-replicatie

Met het herstelscript worden de volgende taken uitgevoerd:

1. Het Traffic Manager-eindpunt voor de web-app wordt uitgeschakeld in de oorspronkelijke regio. Door het eindpunt uit te schakelen, wordt voorkomen dat gebruikers verbinding maken met de app in een ongeldige status als de oorspronkelijke regio tijdens het herstel online komt.

1. Gebruikt een geforceerde failover van de catalogusdatabase in de herstelregio om hiervan de primaire database te maken en werkt de alias _activecatalog_ bij zodat deze verwijst naar de herstelde catalogusserver.

1. Werkt de alias _newtenant_ bij om te verwijzen naar de tenantserver in de herstelregio. Door deze alias te wijzigen wordt gegarandeerd dat de databases voor nieuwe tenants in de herstelregio worden ingericht. 

1. Markeert alle bestaande tenants in de herstelcatalogus als offline om te voorkomen dat er toegang tot de tenantdatabases wordt verkregen voordat de failover is uitgevoerd.

1. Werkt de configuratie van alle elastische pools en gerepliceerde afzonderlijke databases in de herstelregio bij om de configuratie in de oorspronkelijke regio te spiegelen. (Deze taak is alleen nodig als pools of gerepliceerde databases in de herstelomgeving omlaag worden geschaald tijdens normale bewerkingen om de kosten te verlagen.)

1. Schakelt het Traffic Manager-eindpunt voor de web-app in de herstelregio in. Door het inschakelen van dit eindpunt kan de toepassing nieuwe tenants inrichten. In deze fase zijn bestaande tenants nog steeds offline.

1. Verzendt batches met aanvragen om een geforceerde failover van de databases in volgorde van prioriteit uit te voeren.
    * Doordat databases zijn georganiseerd, kunnen de failovers van de databases parallel worden uitgevoerd in alle pools.
    * Failover-aanvragen worden verzonden via asynchrone bewerkingen, zodat ze snel worden verzonden en meerdere aanvragen tegelijk kunnen worden verwerkt.

   > [!Note]
   > In een storingsscenario zijn de primaire databases in de oorspronkelijke regio offline.  Bij geforceerde failover op de secundaire replica wordt de verbinding met de primaire replica verbroken zonder dat er wordt geprobeerd om de resterende transacties in de wachtrij toe te passen. Als er in een herstel-na-noodgeval-analysescenario zoals in deze zelfstudie bijwerkactiviteit is op het moment van de failover, kunnen er gegevens verloren gaan. Wanneer u later tijdens repatriëring de failback van databases in de herstelregio naar de oorspronkelijke regio uitvoert, wordt een normale failover gebruikt om ervoor te zorgen dat er geen gegevens verloren gaan.

1. Controleert de service om te bepalen wanneer de failover van databases is uitgevoerd. Als er een failover van een tenantdatabase is uitgevoerd, wordt de catalogus bijgewerkt om de herstelstatus van de tenantdatabase vast te leggen en de tenant als online te markeren.
    * Tenantdatabases kunnen worden geopend door de toepassing zodra ze in de catalogus als online worden aangeduid.
    * De som van de rowversion-waarden in de tenantdatabase wordt opgeslagen in de catalogus. Deze waarde fungeert als een vingerafdruk waarmee het repatriëringsproces kan bepalen of de database in de herstelregio is bijgewerkt.

### <a name="run-the-script-to-fail-over-to-the-recovery-region"></a>Het script voor een failover naar de herstelregio uitvoeren

Stel nu dat er een storing optreedt in de regio waarin de toepassing is geïmplementeerd en voer het herstelscript uit:

1. Open in *PowerShell ISE* het script ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 en stel de volgende waarden in:
    * **$DemoScenario = 3**: de app herstellen in een herstelregio door failover naar replica's uit te voeren

2. Druk op **F5** om het script uit te voeren.  
    * Het script wordt geopend in een nieuw PowerShell-venster en vervolgens wordt een reeks PowerShell-taken gestart die parallel worden uitgevoerd. Met deze taken wordt failover van tenantdatabases naar de herstelregio uitgevoerd.
    * De herstelregio is de _gekoppelde regio_ die hoort bij de Azure-regio waarin u de toepassing hebt geïmplementeerd. Zie [Gekoppelde Azure-regio's](../../best-practices-availability-paired-regions.md) voor meer informatie. 

3. Bewaak de status van het herstel proces in het PowerShell-venster.
    ![failoverproces](./media/saas-dbpertenant-dr-geo-replication/failover-process.png)

> [!Note]
> Als u de code voor de hersteltaken wilt verkennen, bekijkt u de PowerShell-scripts in de map ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\RecoveryJobs.

### <a name="review-the-application-state-during-recovery"></a>De toepassingsstatus controleren tijdens het herstel

Wanneer het toepassingseindpunt is uitgeschakeld in Traffic Manager, is de toepassing niet beschikbaar. Nadat de failover van de catalogus naar de herstelregio is uitgevoerd en alle tenants als offline zijn gemarkeerd, wordt de toepassing weer online gebracht. Hoewel de toepassing beschikbaar is, lijkt elke tenant offline in de events hub totdat een failover van de database is uitgevoerd. Het is belangrijk dat u uw toepassing ontwerpt voor het verwerken van offline-tenantdatabases.

1. Direct nadat de catalogusdatabase is hersteld, vernieuwt u de Wingtip Tickets-events hub in uw webbrowser.
   * De naam van de catalogusserver in de voettekst heeft nu het achtervoegsel _-recovery_ en bevindt zich in de herstelregio.
   * U ziet dat tenants die nog niet zijn hersteld, als offline worden aangeduid. Deze kunnen niet worden geselecteerd.  

     > [!Note]
     > Mogelijk kunt u de browser niet vernieuwen voordat alles is hersteld, ook al moeten er nog maar enkele databases worden hersteld, en ziet u de tenants niet als ze offline zijn. 

     ![Events hub offline](./media/saas-dbpertenant-dr-geo-replication/events-hub-offlinemode.png) 

   * Als u de gebeurtenissenpagina van een tenant rechtstreeks opent terwijl de tenant offline is, wordt op de pagina gemeld dat de tenant offline is. Als bijvoorbeeld Contoso Concert Hall offline is, probeert u http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall ![ Pagina Contoso Offline](./media/saas-dbpertenant-dr-geo-replication/dr-in-progress-offline-contosoconcerthall.png) 

### <a name="provision-a-new-tenant-in-the-recovery-region"></a>Een nieuwe tenant in de herstelregio inrichten
Zelfs voordat failover van alle bestaande tenantdatabases is uitgevoerd, kunt u nieuwe tenants in de herstelregio inrichten.  

1. Open in *PowerShell ISE* het script ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 en stel de volgende eigenschap in:
    * **$DemoScenario = 4**: een nieuwe tenant in de herstelregio inrichten

2. Druk op **F5** om het script uit te voeren en de nieuwe tenant in te richten. 

3. De gebeurtenissenpagina Hawthorn Hall wordt geopend in de browser wanneer het inrichten is voltooid. Zoals u ziet in de voettekst, bevindt de Hawthorn Hall-database zich in de herstelregio.
    ![Pagina Hawthorn Hall Events](./media/saas-dbpertenant-dr-geo-replication/hawthornhallevents.png) 

4. Vernieuw de Wingtip Tickets-events hub-pagina om te bekijken dat Hawthorn Hall is opgenomen. 
    * Als u Hawthorn Hall hebt ingericht zonder te wachten tot de andere tenants zijn hersteld, kunnen andere tenants nog steeds offline zijn.


## <a name="review-the-recovered-state-of-the-application"></a>De herstelde status van de toepassing controleren

Wanneer het herstelproces is voltooid, zijn de toepassing en alle tenants volledig functioneel in de herstelregio. 

1. Zodra u in de weergave in het PowerShell-consolevenster hebt gezien dat alle tenants zijn hersteld, vernieuwt u de events hub.  De tenants worden allemaal als online aangeduid, met inbegrip van de nieuwe tenant, Hawthorn Hall.

    ![herstelde en nieuwe tenants in de events hub](./media/saas-dbpertenant-dr-geo-replication/events-hub-with-hawthorn-hall.png)

2. Open in [Azure Portal](https://portal.azure.com) de lijst met resourcegroepen.  
    * U ziet de resourcegroep die u hebt geïmplementeerd, plus de herstelresourcegroep, met het achtervoegsel _-recovery_.  De herstelresourcegroep bevat alle resources die zijn gemaakt tijdens het herstelproces, plus nieuwe resources die zijn gemaakt tijdens de storing.  

3. Als u de herstelresourcegroep opent, ziet u de volgende items:
   * De herstelversies van de catalogus- en tenants1-servers met het achtervoegsel _-recovery_.  De herstelde catalogus- en tenantdatabases op deze servers hebben allemaal de namen die in de oorspronkelijke regio worden gebruikt.

   * De SQL-server _tenants2-dpt-&lt;user&gt;-recovery_.  Deze server wordt gebruikt voor het inrichten van nieuwe tenants tijdens de storing.
   * De app-service met de naam _events-wingtip-dpt-&lt;recoveryregion&gt;-&lt;user&gt_;, het herstelexemplaar van de gebeurtenissen-app. 

     ![Azure Recovery-resources](./media/saas-dbpertenant-dr-geo-replication/resources-in-recovery-region.png) 

4. Open de SQL-server _tenants2-dpt-&lt;user&gt;-recovery_.  U ziet dat deze de database _hawthornhall_ en de elastische pool _pool1_ bevat.  De database _hawthornhall_ is geconfigureerd als een elastische database in de elastische pool _Pool1_.

5. Ga terug naar de resourcegroep en klik op de database Contoso Concert Hall op de server _tenants1-dpt-&lt;user&gt;-recovery_. Klik op geo-replicatie aan de linkerkant.

    ![Contoso-database na failover](./media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication-after-failover.png)

## <a name="change-tenant-data"></a>Tenantgegevens wijzigen 
In deze taak werkt u een van de tenantdatabases bij. 

1. Ga in uw browser naar de lijst met gebeurtenissen voor Contoso Concert Hall en noteer de naam van de laatste gebeurtenis.
2. Open in *PowerShell ISE* het script ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 en stel de volgende waarde in:
    * **$DemoScenario = 5**: een gebeurtenis uit een tenant in de herstelregio verwijderen
3. Druk op **F5** om het script uit te voeren
4. Vernieuw de pagina met gebeurtenissen voor Contoso Concert Hall (http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall - vervang &lt; user&gt; door de gebruikerswaarde van uw implementatie) en u ziet dat de laatste gebeurtenis is verwijderd.

## <a name="repatriate-the-application-to-its-original-production-region"></a>De toepassing repatriëren naar de oorspronkelijke productieregio

Met deze taak wordt de toepassing gerepatrieerd naar de oorspronkelijke regio. In het echt zou u repatriëring initiëren wanneer de storing is opgelost.

### <a name="repatriation-process-overview"></a>Overzicht van het repatriëringsproces

![Architectuur voor repatriëring](./media/saas-dbpertenant-dr-geo-replication/repatriation-architecture.png)

Het repatriëringsproces:
1. Annuleert alle uitstaande of in-flight databaseherstelaanvragen.
2. Werkt de alias _newtenant_ bij zodat deze verwijst naar de tenantserver in de oorspronkelijke regio. Door deze alias te wijzigen wordt gegarandeerd dat de databases voor nieuwe tenants in de oorspronkelijke regio worden ingericht.
3. Voorziet de oorspronkelijke regio van alle gewijzigde tenantgegevens
4. Voert failover van tenantdatabases uit in volgorde van prioriteit.

Bij de failover wordt de database daadwerkelijk verplaatst naar de oorspronkelijke regio. Wanneer de failover van de database wordt uitgevoerd, worden alle openstaande verbindingen verbroken en is de database enkele seconden niet beschikbaar. Toepassingen moeten worden geschreven met logica voor opnieuw proberen om ervoor te zorgen dat ze weer verbinding maken.  Hoewel deze korte verbreking vaak niet wordt opgemerkt, kunt u ervoor kiezen om databases buiten kantooruren te repatriëren. 


### <a name="run-the-repatriation-script"></a>Het repatriëringsscript uitvoeren
Stel nu dat de storing is verholpen. U voert dan vervolgens het repatriëringsscript uit.

1. Open in *PowerShell ISE* het script ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1.

2. Controleer of het catalogussynchronisatieproces nog steeds wordt uitgevoerd in het PowerShell-exemplaar.  Start het zo nodig opnieuw door het volgende in te stellen:
    * **$DemoScenario = 1**: beginnen met het synchroniseren van de server-, pool- en databaseconfiguratiegegevens van de tenant in de catalogus
    * Druk op **F5** om het script uit te voeren.

3.  Start vervolgens het repatriëringsproces door het volgende in te stellen:
    * **$DemoScenario = 6**: de app repatriëren naar de oorspronkelijke regio
    * Druk op **F5** om het herstelscript in een nieuw PowerShell-venster uit te voeren.  De repatriëring duurt enkele minuten en kan worden gevolgd in het PowerShell-venster.
    ![Repatriëringsproces](./media/saas-dbpertenant-dr-geo-replication/repatriation-process.png)

4. Wanneer het script wordt uitgevoerd, vernieuwt u de events hub-pagina (http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net)
    * U ziet dat alle tenants online zijn en dat ze tijdens dit proces toegankelijk zijn.

5. Nadat de bewerking is voltooid, vernieuwt u de events hub en opent u de gebeurtenissenpagina voor Hawthorn Hall. U ziet dat deze database is gerepatrieerd naar de oorspronkelijke regio.
    ![Events hub gerepatrieerd](./media/saas-dbpertenant-dr-geo-replication/events-hub-repatriated.png)


## <a name="designing-the-application-to-ensure-app-and-database-are-colocated"></a>De toepassing zo ontwerpen dat de app en de database zich op dezelfde locatie bevinden 
De toepassing wordt zo ontworpen dat er altijd verbinding wordt gemaakt vanaf een instantie in dezelfde regio als de database van de tenant. Dit ontwerp vermindert de latentie tussen de toepassing en de database. Bij deze optimalisatie wordt ervan uitgegaan dat de app-naar-database-interactie van de app-naar-data base intensiever is dan de gebruiker-naar-app-interactie.  

Tenantdatabases kunnen gedurende enige tijd tijdens de repatriëring over herstel- en oorspronkelijke regio's worden verspreid. Voor elke database wordt de regio gezocht waarin de database zich bevindt door een DNS-zoekopdracht voor de naam van de tenantserver uit te voeren. In SQL Database is de servernaam een alias. De servernaam met alias bevat de regionaam. Als de toepassing zich niet in dezelfde regio bevindt als de database, wordt er doorgestuurd naar de instantie in dezelfde regio als de server. Door door te sturen naar de instantie in dezelfde regio als de database wordt de latentie tussen de app en de database geminimaliseerd. 

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:
> [!div class="checklist"]
> 
> * Configuratiegegevens voor databases en elastische pools synchroniseren met de tenantcatalogus
> * Een herstelomgeving instellen in een alternatieve regio, met inbegrip van de toepassing, servers en pools
> * _Geo-replicatie_ gebruiken om de catalogus- en tenantdatabases te repliceren naar de herstelregio
> * Failover van de toepassings-, catalogus- en tenantdatabases naar de herstelregio uitvoeren 
> * Failback van de toepassings-, catalogus- en tenantdatabases naar de oorspronkelijke regio uitvoeren als de storing is opgelost

Meer informatie over de technologieën van Azure SQL Database die bedrijfscontinuïteit mogelijk maken, vindt u in de documentatie van dit [overzicht van bedrijfscontinuïteit](business-continuity-high-availability-disaster-recover-hadr-overview.md).

## <a name="additional-resources"></a>Aanvullende bronnen

* [Aanvullende zelfstudies waarbij wordt voortgebouwd op de Wingtip-SaaS-toepassing](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
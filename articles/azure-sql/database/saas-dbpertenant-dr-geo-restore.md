---
title: 'SaaS-apps: Geografisch redundante back-ups voor herstel na een noodgeval'
description: Meer informatie over het gebruik van geografisch redundante Azure SQL Database-back-ups om een multitenant-SaaS-app te herstellen in het geval van een storing
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/14/2019
ms.openlocfilehash: 3fe6095595f5270b18536e6ef46afe4a0a5b3268
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97607708"
---
# <a name="use-geo-restore-to-recover-a-multitenant-saas-application-from-database-backups"></a>Geo-herstel gebruiken om een multitenant-SaaS-toepassing te herstellen vanuit databaseback-ups
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In deze zelfstudie verkent u een scenario voor volledig herstel na een noodgeval voor een multitenant-SaaS-toepassing die is geïmplementeerd met het database-per-tenant-model. U gebruikt [geo-herstel](recovery-using-backups.md) om de catalogus- en tenantdatabases te herstellen vanuit automatisch onderhouden geografisch redundante back-ups in een alternatieve herstelregio. Nadat de storing is verholpen, gebruikt u [geo-replicatie](active-geo-replication-overview.md) om gewijzigde databases naar hun oorspronkelijke regio te repatriëren.

![In het diagram ziet u de oorspronkelijke regio en de herstelregio, beide met een app, catalogus, oorspronkelijke of mirror-installatiekopieën van servers en pools, en automatische back-ups naar de opslag, waarbij de herstelregio geo-replicatie van back-ups accepteert en een server en pool voor nieuwe tenants heeft.](./media/saas-dbpertenant-dr-geo-restore/geo-restore-architecture.png)

Geo-herstel is de voordeligste oplossing voor herstel na een noodgeval voor Azure SQL Database. Het herstellen met geografisch redundante back-ups kan echter tot gegevensverlies van maximaal één uur leiden. Het kan veel tijd in beslag nemen, afhankelijk van de grootte van elke database. 

> [!NOTE]
> Herstel toepassingen met de laagst mogelijke RPO en RTO met behulp van geo-replicatie in plaats van geo-herstel.

In deze zelfstudie verkent u zowel de herstel- als de repatriëringswerkstroom. In deze zelfstudie leert u procedures om het volgende te doen:
> [!div class="checklist"]
> 
> * Configuratiegegevens voor databases en elastische pools synchroniseren met de tenantcatalogus.
> * Een omgeving met mirror-installatiekopieën instellen in een herstelregio die toepassingen, servers en pools bevat.   
> * Catalogus- en tenantdatabases herstellen met behulp van geo-herstel.
> * Geo-replicatie gebruiken om de tenantcatalogus en gewijzigde tenantdatabases te repatriëren nadat de storing is verholpen.
> * De catalogus bijwerken wanneer een database is hersteld (of gerepatrieerd) om de huidige locatie van de actieve kopie van de databases van elke tenant bij te houden.
> * Ervoor zorgen dat de toepassing en de tenantdatabase zich altijd allebei in dezelfde Azure-regio bevinden om de latentie te verminderen. 
 

Voordat u deze zelfstudie start, doet u het volgende:
* Implementeer de Wingtip Tickets-SaaS-database-per-tenant-app. Zie [De SaaS-app Wingtip Tickets met een database per tenant implementeren en verkennen](saas-dbpertenant-get-started-deploy.md) om de app in minder dan vijf minuten te implementeren. 
* Installeer Azure PowerShell. Zie [Aan de slag met Azure PowerShell](/powershell/azure/get-started-azureps) voor meer informatie.

## <a name="introduction-to-the-geo-restore-recovery-pattern"></a>Inleiding tot het herstelpatroon met geo-herstel

Herstel na noodgevallen is een belangrijke overweging voor veel toepassingen, ongeacht of dit om nalevingsredenen of voor de bedrijfscontinuïteit is. Als er sprake is van een langdurige serviceonderbreking, kan een goed voorbereid plan voor herstel na noodgevallen de bedrijfsonderbreking tot een minimum terugbrengen. Met een plan voor herstel na een noodgeval op basis van geo-herstel moeten verschillende doelen worden bereikt:
 * Zo snel mogelijk alle benodigde capaciteit in de gekozen herstelregio reserveren om ervoor te zorgen dat deze beschikbaar is voor het herstellen van tenantdatabases.
 * Een herstelomgeving met mirror-installatiekopieën instellen die de oorspronkelijke pool- en databaseconfiguratie weerspiegelt. 
 * Toestaan dat het herstelproces vóór voltooiing wordt geannuleerd als de oorspronkelijke regio weer online komt.
 * Snelle inrichting van tenants mogelijk maken zodat het onboarden van nieuwe tenants zo snel mogelijk opnieuw kan worden gestart.
 * Geoptimaliseerd zijn om tenants in volgorde van prioriteit te herstellen.
 * Geoptimaliseerd zijn om tenants zo snel mogelijk online te krijgen door stappen parallel uit te voeren als dit praktisch is.
 * Flexibel zijn met betrekking tot fouten, en herstartbaar en idempotent zijn.
 * Databases repatriëren naar hun oorspronkelijke regio met minimale gevolgen voor tenants wanneer de storing is verholpen.  

> [!NOTE]
> De toepassing wordt hersteld in de gekoppelde regio van de regio waarin de toepassing is geïmplementeerd. Zie [Gekoppelde Azure-regio's](../../best-practices-availability-paired-regions.md) voor meer informatie.   

In deze zelfstudie worden functies van Azure SQL Database en het Azure-platform gebruikt om deze uitdagingen het hoofd te bieden:

* [Azure Resource Manager-sjablonen](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) om zo snel mogelijk alle benodigde capaciteit te reserveren. Azure Resource Manager-sjablonen worden gebruikt om een mirror-installatiekopie van de oorspronkelijke servers en elastische pools in de herstelregio in te richten. Er worden ook een afzonderlijke server en pool gemaakt voor het inrichten van nieuwe tenants.
* [Elastic Database Client Library](elastic-database-client-library.md) (EDCL) om een tenantdatabasecatalogus te maken en te onderhouden. Aan de uitgebreide catalogus worden regelmatig vernieuwde pool- en databaseconfiguratiegegevens toegevoegd.
* [Shard-beheerherstelfuncties](elastic-database-recovery-manager.md) van de EDCL om databaselocatievermeldingen in de catalogus te onderhouden tijdens het herstel en de repatriëring.  
* [Geo-herstel](../../key-vault/general/disaster-recovery-guidance.md) om de catalogus- en tenantdatabases te herstellen van automatisch onderhouden, geografisch redundante back-ups. 
* [Asynchrone herstelbewerkingen](../../azure-resource-manager/management/async-operations.md), die zijn verzonden in volgorde van tenantprioriteit, worden voor elke pool door het systeem in de wachtrij geplaatst en verwerkt in batches, zodat de pool niet overbelast raakt. Deze bewerkingen kunnen zo nodig vóór of tijdens de uitvoering worden geannuleerd.   
* [Geo-replicatie](active-geo-replication-overview.md) om databases na de storing te repatriëren naar de oorspronkelijke regio. Er gaan geen gegevens verloren en de gevolgen voor de tenant zijn minimaal wanneer u geo-replicatie gebruikt.
* [SQL Server-DNS-aliassen](./dns-alias-overview.md) om het catalogussynchronisatieproces toe te staan om verbinding te maken met de actieve catalogus, ongeacht de locatie.  

## <a name="get-the-disaster-recovery-scripts"></a>De scripts voor herstel na noodgevallen ophalen

De scripts voor herstel na noodgevallen die in deze zelfstudie worden gebruikt, zijn beschikbaar in de [Wingtip Tickets-SaaS-database-per-tenant-GitHub-opslagplaats](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant). Bekijk de [algemene richtlijnen](saas-tenancy-wingtip-app-guidance-tips.md) voor stappen om de Wingtip Tickets-beheerscripts te downloaden en deblokkeren.

> [!IMPORTANT]
> Net als alle Wingtip Tickets-beheerscripts zijn de scripts voor herstel na noodgevallen slechts een voorbeeld. Ze zijn niet geschikt voor een productieomgeving.

## <a name="review-the-healthy-state-of-the-application"></a>De status van de toepassing controleren
Voordat u het herstelproces start, controleert u de normale status van de toepassing.

1. Open in uw webbrowser de Wingtip Tickets-events hub (http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net, vervang &lt; user&gt; door de gebruikerswaarde van uw implementatie).
    
   Ga naar onderaan de pagina. U ziet hier de naam en locatie van de catalogusserver in de voettekst. De locatie is de regio waarin u de app hebt geïmplementeerd.    

   > [!TIP]
   > Plaats de muisaanwijzer op de locatie om de weergave te vergroten.

   ![Status van Events Hub in de oorspronkelijke regio](./media/saas-dbpertenant-dr-geo-restore/events-hub-original-region.png)

2. Selecteer de tenant Contoso Concert Hall en open de bijbehorende gebeurtenispagina.

   In de voettekst ziet u de servernaam van de tenant. De locatie is gelijk aan de locatie van de catalogusserver.

   ![Oorspronkelijke regio voor Contoso Concert Hall](./media/saas-dbpertenant-dr-geo-restore/contoso-original-location.png)   

3. Ga naar [Azure Portal](https://portal.azure.com) en bekijk en open de resourcegroep waarin u de app hebt geïmplementeerd.

   Bekijk de resources en de regio waarin de app-service-onderdelen en SQL Database zijn geïmplementeerd.

## <a name="sync-the-tenant-configuration-into-the-catalog"></a>De tenantconfiguratie synchroniseren met de catalogus

In deze taak start u een proces voor het synchroniseren van de configuratie van de servers, elastische pools en databases met de tenantcatalogus. Deze informatie wordt later gebruikt om een omgeving met mirror-installatiekopieën in de herstelregio te configureren.

> [!IMPORTANT]
> Ter vereenvoudiging worden het synchronisatieproces en andere langlopende herstel- en repatriëringsprocessen in deze voorbeelden geïmplementeerd als lokale PowerShell-taken of -sessies die worden uitgevoerd onder de aanmeldingsgegevens van de client. De verificatietokens die worden uitgegeven wanneer u zich aanmeldt, verlopen na enkele uren. Hierna mislukken de taken. In een productiescenario moeten langlopende processen worden geïmplementeerd als betrouwbare Azure-services van een bepaald type die worden uitgevoerd onder een service-principal. Zie [Azure PowerShell gebruiken om een service-principal met een certificaat te maken](../../active-directory/develop/howto-authenticate-service-principal-powershell.md). 

1. Open in PowerShell ISE het bestand ...\Learning Modules\UserConfig.psm1. Vervang `<resourcegroup>` en `<user>` op de regels 10 en 11 door de waarde die is gebruikt bij het implementeren van de app. Sla het bestand op.

2. Open in PowerShell ISE het script...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1.

    In deze zelfstudie voert u alle scenario's in dit PowerShell-script uit. Houd dit bestand daarom open.

3. Stel het volgende in:

    $DemoScenario = 1: Start een achtergrondtaak waarmee de tenantserver- en poolconfiguratiegegevens met de catalogus worden gesynchroniseerd.

4. Druk op F5 om het synchronisatiescript uit te voeren. 

    Deze informatie wordt later gebruikt om ervoor te zorgen dat bij het herstel een mirror-installatiekopie van de servers, pools en databases in de herstelregio wordt gemaakt.  

    ![Synchronisatieproces](./media/saas-dbpertenant-dr-geo-restore/sync-process.png)

Laat het PowerShell-venster actief op de achtergrond en ga verder met de rest van deze zelfstudie.

> [!NOTE]
> In het synchronisatieproces komt er een verbinding met de catalogus tot stand via een DNS-alias. De alias wordt gewijzigd tijdens het herstellen en repatriëren om naar de actieve catalogus te verwijzen. Het synchronisatieproces houdt de catalogus up-to-date met alle wijzigingen aan de database- of poolconfiguratie die in de herstelregio worden aangebracht. Tijdens de repatriëring worden deze wijzigingen toegepast op de equivalente resources in de oorspronkelijke regio.

## <a name="geo-restore-recovery-process-overview"></a>Overzicht van het herstelproces met geo-herstel

Bij het herstelproces met geo-herstel wordt de toepassing geïmplementeerd en worden databases van back-ups hersteld in de herstelregio.

Het herstelproces doet het volgende:

1. Het Azure Traffic Manager-eindpunt uitschakelen voor de web-app in de oorspronkelijke regio. Met het uitschakelen van het eindpunt wordt voorkomen dat gebruikers verbinding maken met de app in een ongeldige status als de oorspronkelijke regio tijdens het herstel online komt.

2. Een herstelcatalogusserver inrichten in de herstelregio, geo-herstel uitvoeren voor de catalogusdatabase en de activecatalog-alias bijwerken zodat deze verwijst naar de herstelde catalogusserver. Met het wijzigen van de catalogusalias wordt gegarandeerd dat het catalogussynchronisatieproces altijd synchroniseert met de actieve catalogus.

3. Alle bestaande tenants in de herstelcatalogus als offline markeren om te voorkomen dat er toegang tot tenantdatabases wordt verkregen voordat deze zijn hersteld.

4. Een exemplaar van de app inrichten in de herstelregio en deze configureren voor gebruik van de herstelde catalogus in die regio. Om de latentie tot een minimum te beperken is de voorbeeld-app ontworpen om altijd verbinding te maken met een tenantdatabase in dezelfde regio.

5. Een server en elastische pool inrichten waarin nieuwe tenants worden ingericht. Met het maken van deze resources wordt ervoor gezorgd dat het inrichten van nieuwe tenants het herstel van bestaande tenants niet verstoort.

6. De nieuwe tenantalias bijwerken om te verwijzen naar de server voor nieuwe tenantdatabases in de herstelregio. Met het wijzigen van deze alias wordt gegarandeerd dat databases voor nieuwe tenants in de herstelregio worden ingericht.
        
7. Servers en elastische pools in de herstelregio inrichten voor het herstellen van tenantdatabases. Deze servers en pools zijn een mirror-installatiekopie van de configuratie in de oorspronkelijke regio. Bij het vooraf inrichten van pools wordt de capaciteit gereserveerd die nodig is om alle databases te herstellen.

    Een storing in een regio kan de beschikbare resources in de gekoppelde regio aanzienlijk onder druk zetten. Als u voor herstel na noodgevallen gebruikmaakt van geo-herstel, is het raadzaam om snel resources te reserveren. Overweeg geo-replicatie als het van essentieel belang is dat een toepassing wordt hersteld in een bepaalde regio. 

8. Het Traffic Manager-eindpunt voor de web-app in de herstelregio inschakelen. Door het inschakelen van dit eindpunt kan de toepassing nieuwe tenants inrichten. In deze fase zijn bestaande tenants nog steeds offline.

9. Batches met aanvragen verzenden om databases in volgorde van prioriteit te herstellen. 

    * Batches zijn zo georganiseerd dat databases parallel worden hersteld in meerdere pools.  

    * Herstelaanvragen worden asynchroon verzonden, zodat ze snel worden verzonden en in de wachtrij worden geplaatst voor uitvoering in elke pool.

    * Omdat herstelaanvragen parallel worden verwerkt in alle pools, is het beter om belangrijke tenants over veel pools te verdelen. 

10. De service controleren om te bepalen wanneer databases zijn hersteld. Nadat een tenantdatabase is hersteld, wordt deze als online aangeduid in de catalogus en wordt een rowversion-som voor de tenantdatabase vastgelegd. 

    * Tenantdatabases kunnen worden geopend door de toepassing zodra ze in de catalogus als online worden aangeduid.

    * De som van de rowversion-waarden in de tenantdatabase wordt opgeslagen in de catalogus. Deze som fungeert als een vingerafdruk waarmee het repatriëringsproces kan bepalen of de database in de herstelregio is bijgewerkt.

## <a name="run-the-recovery-script"></a>Het herstelscript uitvoeren

> [!IMPORTANT]
> In deze zelfstudie worden databases hersteld met geografisch redundante back-ups. Deze back-ups zijn doorgaans binnen tien minuten beschikbaar, maar het kan ook langer duren (maximaal een uur). Het script wordt onderbroken totdat ze beschikbaar zijn.

Stel dat er een storing optreedt in de regio waarin de toepassing is geïmplementeerd. Voer dan het volgende herstelscript uit:

1. Stel in PowerShell ISE in het script ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 de volgende waarde in:

    $DemoScenario = 2: Herstel de app in een herstelregio door deze te herstellen van geografisch redundante back-ups.

2. Druk op F5 om het script uit te voeren.  

    * Het script wordt geopend in een nieuw PowerShell-venster en vervolgens wordt een set PowerShell-taken gestart die parallel worden uitgevoerd. Met deze taken worden servers, pools en databases hersteld naar de herstelregio.

    * De herstelregio is de gekoppelde regio die hoort bij de Azure-regio waarin u de toepassing hebt geïmplementeerd. Zie [Gekoppelde Azure-regio's](../../best-practices-availability-paired-regions.md) voor meer informatie. 

3. Bewaak de status van het herstelproces in het PowerShell-venster.

    ![Schermopname van het PowerShell-venster waar u de status van het herstelproces kunt bewaken.](./media/saas-dbpertenant-dr-geo-restore/dr-in-progress.png)

> [!NOTE]
> Als u de code voor de hersteltaken wilt verkennen, bekijkt u de PowerShell-scripts in de map ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\RecoveryJobs.

## <a name="review-the-application-state-during-recovery"></a>De toepassingsstatus controleren tijdens het herstel
Wanneer het toepassingseindpunt is uitgeschakeld in Traffic Manager, is de toepassing niet beschikbaar. De catalogus wordt hersteld en alle tenants worden als offline aangeduid. Het toepassingseindpunt in de herstelregio wordt vervolgens ingeschakeld en de toepassing is weer online. Hoewel de toepassing beschikbaar is, worden tenants als offline weergegeven in de events hub totdat de bijbehorende databases zijn hersteld. Het is belangrijk dat u uw toepassing ontwerpt voor het verwerken van offline-tenantdatabases.

* Nadat de catalogusdatabase is hersteld, maar voordat de tenants weer online zijn, vernieuwt u de Wingtip Tickets-events hub in uw webbrowser.

  * De naam van de catalogusserver in de voettekst heeft nu het achtervoegsel -recovery en bevindt zich in de herstelregio.

  * U ziet dat tenants die nog niet zijn hersteld, als offline worden aangeduid. Deze kunnen niet worden geselecteerd.   
 
    ![Herstelproces](./media/saas-dbpertenant-dr-geo-restore/events-hub-tenants-offline-in-recovery-region.png)  

  * Als u de gebeurtenissenpagina van een tenant rechtstreeks opent terwijl de tenant offline is, wordt op de pagina gemeld dat de tenant offline is. Als bijvoorbeeld Contoso Concert Hall offline is, probeert u http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall te openen.

    ![Schermopname met een offlinegebeurtenissenpagina.](./media/saas-dbpertenant-dr-geo-restore/dr-in-progress-offline-contosoconcerthall.png)

## <a name="provision-a-new-tenant-in-the-recovery-region"></a>Een nieuwe tenant in de herstelregio inrichten
Zelfs voordat tenantdatabases zijn hersteld, kunt u in de herstelregio nieuwe tenants inrichten. Nieuwe tenantdatabases die in de herstelregio worden ingericht, worden later gerepatrieerd met de herstelde databases.   

1. Stel in PowerShell ISE in het script ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 de volgende eigenschap in:

    $DemoScenario = 3: Richt een nieuwe tenant in de herstelregio in.

2. Druk op F5 om het script uit te voeren.

3. De Hawthorn Hall-gebeurtenissenpagina wordt geopend in de browser wanneer het inrichten is voltooid. 

    Zoals u ziet, bevindt de Hawthorn Hall-database zich in de herstelregio.

    ![Hawthorn Hall ingericht in de herstelregio](./media/saas-dbpertenant-dr-geo-restore/hawthorn-hall-provisioned-in-recovery-region.png)

4. Vernieuw de Wingtip Tickets-events hub-pagina in de browser om te bekijken of Hawthorn Hall is opgenomen. 

    Als u Hawthorn Hall hebt ingericht zonder te wachten tot de andere tenants zijn hersteld, kunnen andere tenants nog steeds offline zijn.

## <a name="review-the-recovered-state-of-the-application"></a>De herstelde status van de toepassing controleren

Wanneer het herstelproces is voltooid, zijn de toepassing en alle tenants volledig functioneel in de herstelregio. 

1. Nadat u in de weergave in het PowerShell-consolevenster hebt gezien dat alle tenants zijn hersteld, vernieuwt u de events hub. 

    De tenants worden allemaal als online aangeduid, met inbegrip van de nieuwe tenant, Hawthorn Hall.

    ![Herstelde en nieuwe tenants in de events hub](./media/saas-dbpertenant-dr-geo-restore/events-hub-with-hawthorn-hall.png)

2. Klik op Contoso Concert Hall en open de bijbehorende gebeurtenissenpagina. 

    U ziet in de voettekst dat de database zich bevindt op de herstelserver die zich in de herstelregio bevindt.

    ![Contoso in de herstelregio](./media/saas-dbpertenant-dr-geo-restore/contoso-recovery-location.png)

3. Open in [Azure Portal](https://portal.azure.com) de lijst met resourcegroepen.  

    U ziet de resourcegroep die u hebt geïmplementeerd, plus de herstelresourcegroep, met het achtervoegsel -recovery. De herstelresourcegroep bevat alle resources die zijn gemaakt tijdens het herstelproces, plus nieuwe resources die zijn gemaakt tijdens de storing. 

4. Als u de herstelresourcegroep opent, ziet u de volgende items:

   * De herstelversies van de catalogus- en tenants1-servers met het achtervoegsel -recovery. De herstelde catalogus- en tenantdatabases op deze servers hebben allemaal de namen die in de oorspronkelijke regio worden gebruikt.

   * De tenants2-dpt-&lt;user&gt;-recovery-SQL-server. Deze server wordt gebruikt voor het inrichten van nieuwe tenants tijdens de storing.

   * De app-service met de naam events-wingtip-dpt-&lt;recoveryregion&gt;-&lt;user&gt;, het herstelexemplaar van de gebeurtenissen-app.

     ![Contoso-resources in de herstelregio](./media/saas-dbpertenant-dr-geo-restore/resources-in-recovery-region.png) 
    
5. Open de tenants2-dpt-&lt;user&gt;-recovery-SQL-server. U ziet dat deze de database hawthornhall en de elastische pool Pool1 bevat. De hawthornhall-database is geconfigureerd als een elastische database in de elastische pool Pool1.

## <a name="change-the-tenant-data"></a>De tenantgegevens wijzigen 
In deze taak werkt u een van de herstelde tenantdatabases bij. In het repatriëringsproces worden herstelde databases die zijn gewijzigd, naar de oorspronkelijke regio gekopieerd. 

1. Ga in uw browser naar de lijst met gebeurtenissen voor de Contoso Concert Hall. Als u door de gebeurtenissen bladert, ziet u dat de laatste gebeurtenis, Seriously Strauss, wordt vermeld.

2. Stel in PowerShell ISE in het script ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 de volgende waarde in:

    $DemoScenario = 4: Verwijder een gebeurtenis uit een tenant in de herstelregio.

3. Druk op F5 om het script uit te voeren.

4. Vernieuw de Contoso Concert Hall-gebeurtenissenpagina (http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall). Zoals u ziet, ontbreekt de gebeurtenis Seriously Strauss.

Op dit punt in de zelfstudie hebt u de toepassing hersteld die nu wordt uitgevoerd in de herstelregio. U hebt een nieuwe tenant ingericht in de herstelregio en u hebt de gegevens van een van de herstelde tenants gewijzigd.  

> [!NOTE]
> Andere zelfstudies in het voorbeeld zijn niet ontworpen om te worden uitgevoerd met de app in de herstelstatus. Als u andere zelfstudies wilt verkennen, moet u de toepassing eerst repatriëren.

## <a name="repatriation-process-overview"></a>Overzicht van het repatriëringsproces

Tijdens het repatriëringsproces worden de toepassing en de bijbehorende databases teruggezet naar de oorspronkelijke regio nadat een storing is verholpen.

![Repatriëring met geo-herstel](./media/saas-dbpertenant-dr-geo-restore/geo-restore-repatriation.png)   

Het proces:

1. Stopt alle lopende herstelactiviteiten en annuleert alle openstaande of deels uitgevoerde databaseherstelaanvragen.

2. Reactiveert in de oorspronkelijke regio tenantdatabases die niet zijn gewijzigd sinds de storing. Deze databases omvatten de databases die nog niet zijn hersteld en de databases die zijn hersteld maar die daarna niet zijn gewijzigd. De opnieuw geactiveerde databases zijn precies hetzelfde als toen ze de laatste keer door hun tenants werden geopend.

3. Richt in de oorspronkelijke regio een mirror-installatiekopie van de server en elastische pool van de nieuwe tenant in. Nadat deze actie is voltooid, wordt de alias van de nieuwe tenant bijgewerkt zodat deze naar deze server verwijst. Het bijwerken van de alias zorgt voor onboarding van de nieuwe tenant in de oorspronkelijke regio in plaats van de herstelregio.

3. Gebruikt geo-replicatie om de catalogus vanuit de herstelregio naar de oorspronkelijke regio te verplaatsen.

4. Werkt de poolconfiguratie in de oorspronkelijke regio bij, zodat deze consistent is met de wijzigingen die tijdens de storing in de herstelregio zijn aangebracht.

5. Maakt de vereiste servers en pools voor het hosten van nieuwe databases die tijdens de storing worden gemaakt.

6. Gebruikt geo-replicatie voor het repatriëren van herstelde tenantdatabases die na het herstellen zijn bijgewerkt, en alle nieuwe tenantdatabases die tijdens de storing zijn ingericht. 

7. Schoont resources op die tijdens het herstelproces in de herstelregio zijn gemaakt.

Als u het aantal tenantdatabases wilt beperken dat moet worden gerepatrieerd, worden de stappen 1 tot en met 3 onmiddellijk uitgevoerd.  

Stap 4 wordt alleen uitgevoerd als de catalogus in de herstelregio tijdens de storing is gewijzigd. De catalogus wordt bijgewerkt als er nieuwe tenants worden gemaakt of als een database- of poolconfiguratie wordt gewijzigd in de herstelregio.

Het is belangrijk dat stap 7 een minimale onderbreking van tenants veroorzaakt en dat er geen gegevens verloren gaan. Het proces maakt gebruik van geo-replicatie om dit doel te behalen.

Voordat een database geografisch wordt gerepliceerd, wordt de bijbehorende database in de oorspronkelijke regio verwijderd. De database in de herstelregio wordt vervolgens geografisch gerepliceerd en er wordt een secundaire replica in de oorspronkelijke regio gemaakt. Nadat de replicatie is voltooid, wordt de tenant in de catalogus als offline aangeduid, waardoor de verbindingen met de database in de herstelregio worden verbroken. Er wordt vervolgens een failover uitgevoerd voor de database, waardoor alle in behandeling zijnde transacties op de secundaire replica worden verwerkt zodat er geen gegevens verloren gaan. 

Bij een failover worden de databaserollen omgekeerd. De secundaire replica in de oorspronkelijke regio wordt de primaire database voor lezen en schrijven en de database in de herstelregio wordt een secundaire replica voor alleen-lezen. De tenantvermelding in de catalogus wordt bijgewerkt zodat deze verwijst naar de database in de oorspronkelijke regio en de tenant wordt als online aangeduid. Op dit punt is de repatriëring van de database voltooid. 

Toepassingen moeten worden geschreven met logica voor opnieuw proberen om ervoor te zorgen dat ze automatisch opnieuw verbinding maken wanneer verbindingen worden verbroken. Wanneer ze de catalogus gebruiken om de nieuwe verbinding tot stand te brengen, maken ze verbinding met de gerepatrieerde database in de oorspronkelijke regio. Hoewel de korte onderbreking vaak niet wordt opgemerkt, kunt u ervoor kiezen om databases buiten kantooruren te repatriëren.

Nadat een database is gerepatrieerd, kan de secundaire database in de herstelregio worden verwijderd. De database in de oorspronkelijke regio maakt dan opnieuw gebruik van geo-herstel voor herstel na een noodgeval.

In stap 8 worden resources in de herstelregio, met inbegrip van de herstelservers en -pools, verwijderd.

## <a name="run-the-repatriation-script"></a>Het repatriëringsscript uitvoeren
Ga ervan uit dat de storing is verholpen. U voert dan vervolgens het repatriëringsscript uit.

Als u de zelfstudie hebt gevolgd, worden Fabrikam Jazz Club en Dogwood Dojo met het script onmiddellijk opnieuw geactiveerd in de oorspronkelijke regio omdat er geen wijzigingen zijn aangebracht. Vervolgens worden de nieuwe tenant, Hawthorn Hall, en Contoso Concert Hall gerepatrieerd, omdat er wijzigingen zijn aangebracht. Met het script wordt ook de catalogus gerepatrieerd die is bijgewerkt toen Hawthorn Hall werd ingericht.
  
1. Controleer in PowerShell ISE in het script ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 of het proces Catalogus synchroniseren nog steeds wordt uitgevoerd in de betreffende PowerShell-instantie. Start het zo nodig opnieuw door het volgende in te stellen:

    $DemoScenario = 1: Begin met het synchroniseren van de server-, pool- en databaseconfiguratiegegevens van de tenant in de catalogus.

    Druk op F5 om het script uit te voeren.

2.  Start vervolgens het repatriëringsproces door het volgende in te stellen:

    $DemoScenario = 5: Repatrieer de app naar de oorspronkelijke regio.

    Druk op F5 om het herstelscript in een nieuw PowerShell-venster uit te voeren. De repatriëring duurt enkele minuten en kan worden gevolgd in het PowerShell-venster.

3. Wanneer het script wordt uitgevoerd, vernieuwt u de events hub-pagina (http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net).

    U ziet dat alle tenants online zijn en dat ze tijdens dit proces toegankelijk zijn.

4. Selecteer de Fabrikam Jazz Club om deze te openen. Als u deze tenant niet hebt gewijzigd, ziet u in de voettekst dat de server al is hersteld naar de oorspronkelijke server.

5. Open of vernieuw de Contoso Concert Hall-gebeurtenissenpagina. U ziet in de voettekst dat de database zich aanvankelijk nog steeds op de herstelserver bevindt. 

6. Vernieuw de Contoso Concert Hall-gebeurtenissenpagina als de repatriëring is voltooid. U ziet nu dat de database zich in de oorspronkelijke regio bevindt.

7. Vernieuw de Events Hub opnieuw en open Hawthorn Hall. U ziet dat de database zich ook in de oorspronkelijke regio bevindt. 

## <a name="clean-up-recovery-region-resources-after-repatriation"></a>Resources van de herstelregio opschonen na repatriëring
Nadat de repatriëring is voltooid, is het veilig om de resources in de herstelregio te verwijderen. 

> [!IMPORTANT]
> Verwijder deze resources onmiddellijk om alle facturering voor de resources te stoppen.

Bij het herstelproces worden alle herstelresources in een herstelresourcegroep gemaakt. Met het opschoningsproces worden deze resourcegroep en alle verwijzingen naar de resources uit de catalogus verwijderd. 

1. Stel in PowerShell ISE in het script...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 het volgende in:
    
    $DemoScenario = 6: Verwijder verouderde resources uit de herstelregio.

2. Druk op F5 om het script uit te voeren.

Nadat de scripts zijn opgeschoond, is de toepassing terug bij het beginpunt. Op dit punt kunt u het script opnieuw uitvoeren of andere zelfstudies proberen.

## <a name="designing-the-application-to-ensure-that-the-app-and-the-database-are-co-located"></a>De toepassing zo ontwerpen dat de app en de database zich op dezelfde locatie bevinden 
De toepassing wordt zo ontworpen dat er altijd verbinding wordt gemaakt vanaf een instantie in dezelfde regio als de database van de tenant. Dit ontwerp vermindert de latentie tussen de toepassing en de database. Bij deze optimalisatie wordt ervan uitgegaan dat de app-naar-database-interactie intensiever is dan de gebruiker-naar-app-interactie.  

Tenantdatabases kunnen gedurende enige tijd tijdens de repatriëring over herstel- en oorspronkelijke regio's worden verspreid. Voor elke database wordt de regio gezocht waarin de database zich bevindt door een DNS-zoekopdracht voor de naam van de tenantserver uit te voeren. De servernaam is een alias. De servernaam met alias bevat de regionaam. Als de toepassing zich niet in dezelfde regio bevindt als de database, wordt er omgeleid naar de instantie in dezelfde regio als de server. Door om te leiden naar de instantie in dezelfde regio als de database wordt de latentie tussen de app en de database geminimaliseerd.  

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:
> [!div class="checklist"]
> 
> * De tenantcatalogus gebruiken om configuratiegegevens te bewaren die periodiek worden vernieuwd. Hierdoor kan er een herstelomgeving met mirror-installatiekopieën in een andere regio worden gemaakt.
> * Databases herstellen in de herstelregio met behulp van geo-herstel.
> * De tenantcatalogus bijwerken om herstelde tenantdatabaselocaties te weerspiegelen. 
> * Een DNS-alias gebruiken om ervoor te zorgen dat een toepassing altijd zonder herconfiguratie verbinding kan maken met de tenantcatalogus.
> * Geo-replicatie gebruiken om de herstelde databases naar hun oorspronkelijke regio te repatriëren nadat een storing is verholpen.

Probeer de zelfstudie [Herstel na noodgevallen voor een multitenant-SaaS-toepassing met behulp van geo-replicatie van databases](./saas-dbpertenant-dr-geo-replication.md) voor meer informatie over het gebruik van geo-replicatie om een grootschalige multitenant-toepassing aanzienlijk sneller te herstellen.

## <a name="additional-resources"></a>Aanvullende bronnen

[Aanvullende zelfstudies waarbij wordt voortgebouwd op de Wingtip-SaaS-toepassing](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
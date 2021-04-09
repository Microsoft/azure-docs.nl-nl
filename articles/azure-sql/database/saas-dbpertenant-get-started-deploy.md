---
title: Zelfstudie voor SaaS-app met een database per tenant
description: Implementeer en verken de Wingtip Tickets SaaS-toepassing met meerdere tenants, waarmee het database-per-tenantpatroon en andere SaaS-patronen worden gedemonstreerd met behulp van Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 497e714289c834e026c6b9b767ed2b7af5442783
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92780832"
---
# <a name="deploy-and-explore-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-azure-sql-database"></a>Implementeer en verken een SaaS-app met meerdere tenants die het database-per-tenantpatroon gebruikt met Azure SQL Database

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In deze zelfstudie implementeert en verkent u de Wingtip Tickets SaaS-database-per-tenanttoepassing (Wingtip). Deze app maakt gebruik van een database-per-tenantpatroon om de gegevens van meerdere tenants op te kunnen slaan. De app is ontworpen om functies van Azure SQL Database weer te geven die de inschakeling van SaaS-scenario's vereenvoudigen.

Vijf minuten nadat u **Implementeren naar Azure** hebt geselecteerd, hebt u een SaaS-toepassing met meerdere tenants. De app bevat een database die wordt uitgevoerd in Azure SQL Database. De app wordt geïmplementeerd met drie voorbeeldtenants, elk met hun eigen database. Alle databases worden geïmplementeerd in een elastische SQL-pool. De app is geïmplementeerd in uw Azure-abonnement. U hebt volledige toegang om te verkennen en te werken met de afzonderlijke app-onderdelen. De C#-broncode en beheerscripts van de app zijn beschikbaar in de GitHub-opslagplaats [WingtipTicketsSaaS-DbPerTenant][github-wingtip-dpt].

In deze zelfstudie komen deze onderwerpen aan bod:

> [!div class="checklist"]
> - Wingtip SaaS-toepassing implementeren.
> - Waar u de broncode en beheerscripts van de app ophaalt.
> - Informatie over de servers, pools en databases waaruit de app bestaat.
> - Tenants via de *catalogus* toewijzen aan de bijbehorende gegevens.
> - Een nieuwe tenant inrichten.
> - Tenantactiviteiten in de app bewaken.

Een [reeks gerelateerde zelfstudies](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) aanbiedingen om verschillende SaaS-ontwerp en -beheerpatronen te verkennen. De zelfstudies bouwen verder op de initiële implementatie. Wanneer u de zelfstudies gebruikt, wordt u aangeraden de meegeleverde scripts door te nemen om te zien hoe de verschillende SaaS-patronen worden geïmplementeerd. De scripts laten zien hoe functies van SQL Database de ontwikkeling van SaaS-toepassingen vereenvoudigen.

## <a name="prerequisites"></a>Vereisten

Om deze zelfstudie te voltooien, zorgt u ervoor dat Azure PowerShell geïnstalleerd is. Zie [Aan de slag met Azure PowerShell](/powershell/azure/get-started-azureps) voor meer informatie.

## <a name="deploy-the-wingtip-tickets-saas-application"></a>Wingtip Tickets SaaS-toepassing implementeren

### <a name="plan-the-names"></a>De namen plannen

In de stappen van deze sectie geeft u een gebruikerswaarde op die wordt gebruikt om te controleren of de resourcenamen wereldwijd uniek zijn. U geeft ook een naam op voor de resourcegroep met alle resources die zijn gemaakt door een implementatie van de app. Voor een fictieve persoon met de naam Ann Finley wordt het volgende voorgesteld:

- **Gebruiker**: *af1* bestaat uit de initialen van Anne Finley plus een cijfer. Gebruik een andere waarde als u de app een tweede keer implementeert. Een voorbeeld is af2.
- **Resourcegroep**: *wingtip-dpt-af1* geeft aan dat dit de database-per-tenant-app is. Voeg gebruikersnaam af1 toe om de naam van de resourcegroep te correleren met de namen van de resources die deze bevat.

Kies uw namen nu en noteer ze.

### <a name="steps"></a>Stappen

1. Selecteer **Implementeren naar Azure** om de sjabloon voor de implementatie van Wingtip Tickets SaaS-database-per-tenant te openen in het Azure-portaal.

   [![Afbeelding met een knop met het label Implementeren naar Azure.](https://azuredeploy.net/deploybutton.png)](https://aka.ms/deploywingtipdpt)

1. Voer waarden in de sjabloon in voor de vereiste parameters.

    > [!IMPORTANT]
    > Om het overzichtelijk te houden zijn bepaalde verificatieprocessen weggelaten. Ook zijn de firewalls op servers uitgeschakeld voor deze zelfstudie. Het wordt aangeraden om een nieuwe resourcegroep te maken. Gebruik geen bestaande resourcegroepen, -servers of -pools. Gebruik deze toepassing, scripts of enige geïmplementeerde resources niet voor productie. Verwijder deze resourcegroep wanneer u klaar bent om gerelateerde facturering te stoppen.

    - **Resourcegroep**: Selecteer **Nieuwe maken** en geef de unieke naam op die u eerder hebt gekozen voor de resourcegroep.
    - **Locatie**: Selecteer een locatie in de vervolgkeuzelijst.
    - **Gebruiker**: Gebruik de waarde voor de gebruikersnaam die u eerder hebt gekozen.

1. De toepassing implementeren.

    a. Selecteer om akkoord te gaan met de voorwaarden.

    b. Selecteer **Aankoop**.

1. Om de voortgang van de implementatie te controleren, selecteert u **Meldingen** (het belpictogram naast het zoekvak). Het implementeren van de Wingtip Tickets SaaS-app duurt ongeveer vijf minuten.

   ![Implementatie geslaagd](./media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-tickets-management-scripts"></a>De Wingtip Tickets-beheerscripts downloaden en deblokkeren

Terwijl de toepassing wordt geïmplementeerd, downloadt u de broncode en beheerscripts.

> [!IMPORTANT]
> Uitvoerbare inhoud (scripts en DLL's) wordt mogelijk door Windows geblokkeerd wanneer zip-bestanden vanaf een externe bron worden gedownload en uitgepakt. Volg de stappen voor het deblokkeren van het .zip-bestand voordat u de scripts uitpakt. Als u de blokkering opheffen, zorgt u ervoor dat de scripts mogen worden uitgevoerd.

1. Ga naar de opslagplaats voor [WingtipTicketsSaaS-DbPerTenant GitHub-opslagplaats][github-wingtip-dpt].
1. Selecteer **Klonen of downloaden**.
1. Selecteer **Zip-bestand downloaden** en sla vervolgens het bestand op.
1. Klik met de rechtermuisknop op het bestand **WingtipTicketsSaaS-DbPerTenant-master.zip** en selecteer **Eigenschappen**.
1. Op het tabblad **Algemeen** selecteert u **Deblokkeren** > **Toepassen**.
1. Selecteer **OK** en pak de bestanden uit

Scripts bevinden zich in de map \\WingtipTicketsSaaS-DbPerTenant-master\\Learning Modules.

## <a name="update-the-user-configuration-file-for-this-deployment"></a>Het gebruikersconfiguratiebestand voor deze implementatie bijwerken

Voordat u scripts uitvoert, moet u de waarden van de resourcegroep en de gebruiker in het gebruikersconfiguratiebestand bijwerken. Stel deze variabelen in op de waarden die u tijdens de implementatie hebt ingesteld.

1. Open in de PowerShell ISE ...\\Learning Modules\\**UserConfig.psm1**
1. Werk **ResourceGroupName** en **Name** bij met de specifieke waarden voor uw implementatie (alleen in regels 10 en 11).
1. Sla de wijzigingen op.

Er wordt in bijna elk script naar deze waarden verwezen.

## <a name="run-the-application"></a>De toepassing uitvoeren

De app geeft een overzicht van de locaties waarop evenementen plaatsvinden. Voorbeelden van locatietypen zijn concertzalen, jazzclubs en sportverenigingen. In Wingtip Tickets worden locaties geregistreerd als tenants. Een tenant geeft u een eenvoudige manier om gebeurtenissen weer te geven en om tickets aan hun klanten te verkopen. Elke locatie krijgt een gepersonaliseerde website waarop de evenementen voor die locatie worden vermeld en kaartjes worden verkocht.

In de app wordt intern in elke tenant een database geïmplementeerd in een elastische pool.

Een centrale **Events Hub**-pagina bevat een lijst met koppelingen naar de tenants in uw implementatie.

1. Gebruik de URL om de Events Hub in uw webbrowser te openen: http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net. Vervang &lt;user&gt; door de gebruikerswaarde van uw implementatie.

    ![Events Hub](./media/saas-dbpertenant-get-started-deploy/events-hub.png)

2. Klik in de Events Hub op **Fabrikam Jazz Club**.

    ![Gebeurtenissen](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)

### <a name="azure-traffic-manager"></a>Azure Traffic Manager

De Wingtip-app maakt gebruik van [*Azure Traffic Manager*](../../traffic-manager/traffic-manager-overview.md) om de distributie van inkomende aanvragen te regelen. De URL voor toegang tot de gebeurtenissenpagina voor een specifieke tenant gebruikt de volgende indeling:

- http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/fabrikamjazzclub

    De delen van de voorgaande indeling worden beschreven in de volgende tabel.

    | URL-onderdeel        | Beschrijving       |
    | :-------------- | :---------------- |
    | events.wingtip-dpt | De gebeurtenisdelen van de Wingtip-app.<br /><br /> *-dpt* onderscheidt *database-per-tenant*-implementatie van Wingtip Tickets van andere implementaties. Voorbeelden zijn de implementaties met *één* app-per-tenant ( *-sa*) of de *database met meerdere tenants* ( *-mt*). |
    | . *&lt;user&gt;* | *af1* in het voorbeeld. |
    | .trafficmanager.net/ | Traffic manager, basis-URL. |
    | fabrikamjazzclub | Identificeert de tenant met de naam Fabrikam Jazz Club. |
    | &nbsp; | &nbsp; |

- De tenantnaam wordt geparseerd uit de URL door de evenementenapp.
- De tenantnaam wordt gebruikt om een sleutel te maken.
- De sleutel wordt gebruikt om toegang te krijgen tot de catalogus om de locatie van de database van de tenant op te halen.
  - De catalogus wordt geïmplementeerd met behulp van *shard-toewijzingsbeheer*
- De Events Hub gebruikt uitgebreide metagegevens in de catalogus om URL's voor de pagina met de lijst met evenementen voor elke tenant op te bouwen.

In een productieomgeving maakt u doorgaans een CNAME DNS-record om [*een internetdomein van het bedrijf te laten verwijzen*](../../traffic-manager/traffic-manager-point-internet-domain.md) naar de DNS-naam van Traffic Manager.

> [!NOTE]
> Het is misschien niet onmiddellijk duidelijk wat het nut van Traffic Manager is in deze zelfstudie. Het doel van deze reeks zelfstudies is het demonstreren van patronen geschikt voor een complexe productie-omgeving op grote schaal. In een dergelijk geval, wanneer u bijvoorbeeld meerdere web-apps hebt verspreid over de hele wereld, dan heeft u samen met databases Traffic Manager nodig als verbinding tussen deze instanties.
Een andere set zelfstudies die het gebruik van Traffic Manager illustreren zijn de zelfstudies voor [geo-restore](./saas-dbpertenant-dr-geo-restore.md) en [geo-replication](./saas-dbpertenant-dr-geo-replication.md). In deze zelfstudies wordt Traffic Manager gebruikt om over te schakelen naar een herstelinstantie van de SaaS-app in het geval van een regionale storing.

## <a name="start-generating-load-on-the-tenant-databases"></a>Workloads genereren voor de tenant-databases

Nu de app is geïmplementeerd, kunt u ermee aan de slag.

Met het PowerShell-script *Demo-LoadGenerator* wordt een workload voor alle tenantdatabases opgestart. De werkelijke belasting voor veel SaaS-apps is sporadisch en onvoorspelbaar. Om dit soort belasting te simuleren, produceert de generator een belasting met willekeurige pieken van de activiteit voor elke tenant. De pieken treden op in willekeurige intervallen. Het duurt enkele minuten voordat het laadpatroon zich voordoet. Laat de generator ten minste drie of vier minuten draaien voordat u de belasting controleert.

1. In PowerShell ISE opent u het script ...\\Learning Modules\\Utilities\\*Demo-LoadGenerator.ps1*.
2. Druk op F5 om het script uit te voeren en de loadgenerator te starten. Laat de standaard parameterwaarden voorlopig ongewijzigd.
3. Meld u aan bij uw Azure-account en selecteer het abonnement dat u wilt gebruiken, indien nodig.

Met het script voor de loadgenerator wordt een achtergrondtaak voor elke database in de catalogus gestart en vervolgens gestopt. Als u het script voor de loadgenerator opnieuw uitvoert, worden alle achtergrondtaken die momenteel worden uitgevoerd gestopt voordat er nieuwe worden opgestart.

### <a name="monitor-the-background-jobs"></a>De achtergrondtaken controleren

Als u de achtergrondtaken wilt beheren en controleren, gebruikt u de volgende cmdlets:

- `Get-Job`
- `Receive-Job`
- `Stop-Job`

### <a name="demo-loadgeneratorps1-actions"></a>Demo-LoadGenerator.ps1-acties

*Demo-LoadGenerator.ps1* imiteert een actieve workload van klanttransacties. De volgende stappen beschrijven de volgorde van de acties die *Demo-LoadGenerator.ps1* initieert:

1. *Demo-LoadGenerator.ps1* start *LoadGenerator.ps1* op de voorgrond.

    - Beide. ps1-bestanden worden opgeslagen in de mappen onder Learning Modules\\Utilities\\.

2. *LoadGenerator.ps1* doorloop alle tenantdatabases in de catalogus.

3. *LoadGenerator.ps1* start een Powershell-taak op de achtergrond voor elke tenantdatabase:

    - De achtergrondtaken worden standaard gedurende 120 minuten uitgevoerd.
    - Elke taak veroorzaakt een belasting voor de CPU voor één tenantdatabase door *sp_CpuLoadGenerator* uit te voeren. De intensiteit en duur van de belasting variëren afhankelijk van `$DemoScenario`.
    - *sp_CpuLoadGenerator* herhaalt een SQL SELECT-instructie die een hoge CPU-belasting veroorzaakt. Het tijdsinterval tussen de uitgiftes van de SELECT is afhankelijk van de parameterwaarden om een beheerbare CPU-belasting te maken. Laadniveaus en -intervallen zijn willekeurig om meer realistische belastingen te simuleren.
    - Dit .sql-bestand wordt opgeslagen onder *WingtipTenantDB\\dbo\\StoredProcedures\\* .

4. Als `$OneTime = $false`, dan start de loadgenerator de achtergrondtaken en blijft deze draaien. Elke 10 seconden controleert hij of er nieuwe tenants ingericht zijn. Als u `$OneTime = $true` instelt, start de LoadGenerator de achtergrondtaken en stopt hij met in de voorgrond te draaien. Laat voor deze zelfstudie `$OneTime = $false` staan.

   Gebruik CTRL + C of stop de bewerking met CTRL-Break als u de loadgenerator wilt stoppen of opnieuw wilt opstarten.

   Als u de loadgenerator op de voorgrond laat draaien, gebruik dan een PowerShell ISE-instantie om andere PowerShell-script uit te voeren.

&nbsp;

Voordat u verdergaat met het volgende onderdeel, moet u de loadgenerator laten draaien in de toestand waarbij hij taken aanroept.

## <a name="provision-a-new-tenant"></a>Een nieuwe tenant inrichten

Bij de eerste implementatie worden drie voorbeelden van tenants gemaakt. U maakt nu een andere tenant om de impact op de geïmplementeerde toepassing te bekijken. In de Wingtip-app wordt de werkstroom voor het inrichten van nieuwe tenants uitgelegd in de [zelfstudie ](saas-dbpertenant-provision-and-catalog.md)Inrichten en in catalogus opnemen. In deze fase maakt u een nieuwe tenant, wat minder dan één minuut duurt.

1. Open een nieuwe PowerShell ISE.
2. Open ...\\Learning Modules\Provision and Catalog\\*Demo-ProvisionAndCatalog.ps1*.
3. Druk op F5 om het script uit te voeren. Laat nu de standaardwaarden staan.

   > [!NOTE]
   > Veel Wingtip SaaS-scripts gebruiken *$PSScriptRoot* om te bladeren door mappen om functies in andere scripts aan te roepen. Deze variabele wordt alleen geëvalueerd wanneer het volledige script wordt uitgevoerd door op F5 te drukken.  Een selectie markeren en uitvoeren met F8 kan fouten veroorzaken. Druk op F5 om de scripts uit te voeren.

De nieuwe tenantdatabase is:

- Aangemaakt in een elastische SQL-pool.
- Geïnitialiseerd.
- Geregistreerd in de catalogus.

Als de inrichting is voltooid, wordt de site *Events* van de nieuwe tenant in uw browser weergegeven.

![Nieuwe tenant](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

Vernieuw Events Hub om de nieuwe tenant in de lijst weer te geven.

## <a name="explore-the-servers-pools-and-tenant-databases"></a>Servers, pools en tenant-databases

Laten we, nu u een belasting bent beginnen uitvoeren voor een collectie tenants, eens kijken welke resources er allemaal zijn geïmplementeerd.

1. Ga in het [Azure-portaal](https://portal.azure.com) naar de lijst met SQL-servers. Open de server **catalog-dpt-&lt;USER&gt;** .
    - De catalogusserver bevat twee databases, **tenantcatalog** en **basetenantdb** (een sjabloondatabase die is gekopieerd om nieuwe tenants te maken).

   ![Schermafbeelding met een Overzichtspagina voor catalogusservers met de twee databases.](./media/saas-dbpertenant-get-started-deploy/databases.png)

2. Ga terug naar uw lijst met SQL-servers.

3. Open de server **tenants1-dpt-&lt;USER&gt;** waarop de tenantdatabases zijn opgeslagen.

4. Bekijk de volgende items:

    - Elke tenantdatabase iis een database van het type **Standard - elastisch** in een standaardpool van 50 eDTU.
    - De database Red Maple Racing is de tenantdatabase die u eerder hebt ingericht.

   ![Server met databases](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>De pool bewaken

Nadat *LoadGenerator.ps1* enkele minuten wordt uitgevoerd, zoudener voldoende gegevens beschikbaar moeten zijn om enkele controlemogelijkheden te bekijken. Deze mogelijkheden zijn ingebouwd in pools en data bases.

Ga naar de server **tenants1-dpt-&lt;user&gt;** en selecteer **Pool1** om het resourcegebruik voor de pool weer te geven. In de volgende grafieken werd de loadgenerator één uur uitgevoerd.

   ![Pool bewaken](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

- De eerste grafiek, met het label **Resourcegebruik**, toont het eDTU-gebruik van de pool.
- De tweede grafiek toont het eDTU-gebruik van de vijf meest actieve databases in de pool.

Wat deze twee grafieken laten zien, is dat elastische pools en SQL Database uitermate geschikt zijn voor de workloads van onvoorspelbare SaaS-toepassingen. Op de grafieken ziet u dat vier databases elk pieken hebben tot 40 eDTU's en dat alle databases probleemloos ondersteund kunnen worden door een pool met 50-eDTU. De pool met 50-eDTU kan zelfs zwaardere workloads aan. Als de databases zijn ingericht als afzonderlijke databases, moet elke database een S2 (50 DTU) zijn om de pieken aan te kunnen. De kosten voor vier enkelvoudige S2-databases zijn bijna driemaal zo hoog als de prijs van de pool. In praktijksituaties draaien SQL Database-klanten momenteel maximaal 500 databases in pools van 200 eDTU. Zie voor meer informatie de [zelfstudie over Prestatiebewaking](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Aanvullende bronnen

- Zie voor meer informatie aanvullende [zelfstudies over de Wingtip Tickets-SaaS-database-per-tenant-toepassing](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
- Informatie over elastische pools vindt u in [Wat is een elastische pool van Azure SQL?](elastic-pool-overview.md).
- Informatie over elastische taken vindt u in [Uitgeschaalde clouddatabases beheren](./elastic-jobs-overview.md).
- Raadpleeg [Ontwerppatronen voor SaaS-apps voor meerdere tenants](saas-tenancy-app-design-patterns.md) voor meer informatie over SaaS-apps voor meerdere tenants.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> - Wingtip Tickets SaaS-toepassing implementeren.
> - Informatie over de servers, pools en databases waaruit de app bestaat.
> - Tenants via de *catalogus* toewijzen aan de bijbehorende gegevens.
> - Inrichten van nieuwe tenants.
> - Poolgebruik bekijken om de activiteit van een tenant te bewaken.
> - Voorbeeldresources verwijderen om gerelateerde facturering te stoppen.

Hierna kunt u [de zelfstudie over het inrichten en catalogiseren van tenants](saas-dbpertenant-provision-and-catalog.md) volgen.

<!-- Link references. -->

[github-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant
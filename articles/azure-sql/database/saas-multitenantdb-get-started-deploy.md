---
title: Een SaaS-app voor een shard-database met meerdere tenants implementeren
description: Implementeer en verken de Wingtip Tickets SaaS-databasetoepassing met meerdere tenants, waarmee SaaS-patronen worden gedemonstreerd met behulp van Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/16/2018
ms.openlocfilehash: 4de7e428bff0feaafdec00b0c0014bbaf6acb917
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92790964"
---
# <a name="deploy-and-explore-a-sharded-multi-tenant-application"></a>Een shard-toepassing met meerdere tenants implementeren en verkennen
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In deze zelfstudie implementeert en bekijkt u een voorbeeld van een SaaS-toepassing met meerdere tenants met de naam Wingtip Tickets. De Wingtip Tickets-app is ontworpen om functies van Azure SQL Database weer te geven die de implementatie van SaaS-scenario's vereenvoudigen.

Deze implementatie van de Wingtip Tickets-app maakt gebruik van een shard-databasepatroon voor meerdere tenants. De sharding is op basis van tenant-id. Tenantgegevens worden naar een bepaalde database gedistribueerd op basis van de waarden van de tenant-id. 

Met dit databasepatroon kunt u een of meer tenants in elke shard of database opslaan. U kunt een optimalisering uitvoeren om zoveel mogelijk kosten te besparen door elke database met meerdere tenants te delen. U kunt ook voor isolatie optimaliseren door in elke database slechts één tenant op te slaan. Uw optimalisatiekeuze kan onafhankelijk voor elke specifieke tenant worden gemaakt. Uw keuze kan worden gemaakt wanneer de tenant voor het eerst wordt opgeslagen. U kunt eventueel later van gedachten veranderen. De toepassing is zo ontworpen dat deze in beide gevallen goed werkt.

## <a name="app-deploys-quickly"></a>Snel apps implementeren

De app wordt uitgevoerd in de Azure-cloud en maakt gebruik van Azure SQL Database. In de implementatiesectie die volgt, ziet u de blauwe knop **Implementeren naar Azure**. Wanneer u op de knop drukt, wordt de app binnen vijf minuten volledig in uw Azure-abonnement geïmplementeerd. U hebt volledige toegang om met de afzonderlijke app-onderdelen te werken.

De toepassing wordt geïmplementeerd met gegevens voor drie voorbeeldtenants. De tenants worden samen in één database voor meerdere tenants opgeslagen.

Iedereen kan de C#- en PowerShell-broncode voor Wingtip Tickets downloaden van [de GitHub-opslagplaats][link-github-wingtip-multitenantdb-55g].

## <a name="learn-in-this-tutorial"></a>Wat u in deze zelfstudie leert

> [!div class="checklist"]
> - Wingtip Tickets SaaS-toepassing implementeren.
> - De broncode en beheerscripts voor de toepassing ophalen.
> - Informatie over de servers en databases die deel uitmaken van de app.
> - Tenants via de *catalogus* toewijzen aan de bijbehorende gegevens.
> - Een nieuwe tenant inrichten.
> - Tenantactiviteiten in de app bewaken.

Er is een reeks verwante zelfstudies beschikbaar die op deze eerste implementatie voortbouwen. In de zelfstudies wordt een reeks SaaS-ontwerp- en beheerpatronen beschreven. Wanneer u de zelfstudies doorloopt, wordt u aangeraden de meegeleverde scripts door te nemen om te zien hoe de verschillende SaaS-patronen worden geïmplementeerd.

## <a name="prerequisites"></a>Vereisten

U kunt deze zelfstudie alleen voltooien als aan de volgende vereisten wordt voldaan:

- Meest recente Azure PowerShell is geïnstalleerd. Zie [Aan de slag met Azure PowerShell][link-azure-get-started-powershell-41q] voor meer informatie.

## <a name="deploy-the-wingtip-tickets-app"></a>De Wingtip Tickets-app implementeren

### <a name="plan-the-names"></a>De namen plannen

In de stappen van deze sectie geeft u waarde aan een *gebruiker*. Deze waarde wordt gebruikt om te controleren of resourcenamen globaal uniek zijn. U geeft ook een naam aan de *resourcegroep* die alle resources bevat die tijdens een implementatie van de app zijn gemaakt. Voor een persoon met de naam *Fenna Plant* wordt het volgende voorgesteld:
- *Gebruiker:* **fp1**  *(De initialen plus een cijfer. Gebruik een andere waarde (bijvoorbeeld fp2) als u de app een tweede keer implementeert.)*
- *Resourcegroep:* **wingtip-mt-af1** *(wingtip-mt geeft aan dat dit de shard-app voor meerdere tenants is. Het toevoegen van de gebruikersnaam fp1 correleert de naam van de resourcegroep met de namen van de resources die deze bevat.)*

Kies uw namen nu en noteer ze. 

### <a name="steps"></a>Stappen

1. Klik op de volgende blauwe knop: **Implementeren in Azure**.
   - U komt terecht in Azure Portal met de Wingtip Ticket SaaS-implementatiesjabloon.

     [![Knop voor implementeren in Azure.][image-deploy-to-azure-blue-48d]][link-aka-ms-deploywtp-mtapp-52k]

1. Voer de vereiste parameterwaarden voor de implementatie in.

    > [!IMPORTANT]
    > Voor deze demonstratie mag u geen reeds bestaande resourcegroepen, servers of pools gebruiken. Kies in plaats daarvan **Een nieuwe resourcegroep maken**. Verwijder deze resourcegroep wanneer u klaar bent om gerelateerde facturering te stoppen.
    > Gebruik deze app of de resources die hiermee worden gemaakt, niet in een productieomgeving. Sommige aspecten van de verificatie alsmede de firewallinstellingen voor de server zijn opzettelijk onveilig in de app om de demonstratie te vergemakkelijken.

    - Selecteer voor **Resourcegroep** de optie **Nieuwe maken** en geef een **naam** in kleine letters voor de resourcegroep op (hoofdlettergevoelig).
        - Selecteer een **locatie** in de vervolgkeuzelijst.
    - Voor **Gebruiker** wordt u aangeraden een korte waarde te gebruiken.

1. **Implementeer de toepassing**.

    - Klik om akkoord te gaan met de voorwaarden.
    - Klik op **Kopen**.

1. Controleer de voortgang van de implementatie door op **Meldingen** te klikken (het belpictogram rechts van het zoekvak). Het implementeren van de Wingtip-app duurt ongeveer vijf minuten.

   ![implementatie gelukt](./media/saas-multitenantdb-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-management-scripts"></a>De beheerscripts downloaden en deblokkeren

Terwijl de toepassing wordt geïmplementeerd, downloadt u de broncode en beheerscripts voor de toepassing.

> [!NOTE]
> Uitvoerbare inhoud (scripts, DLL's) wordt mogelijk door Windows geblokkeerd wanneer zip-bestanden vanaf een externe bron worden gedownload en uitgepakt. Wanneer u de scripts uit een zip-bestand uitpakt, gebruikt u de volgende stappen om het zip-bestand te deblokkeren voordat u het uitpakt. Als u het zip-bestand deblokkeert, kunnen de scripts worden uitgevoerd.

1. Ga naar de opslagplaats voor [ WingtipTicketsSaaS-MultiTenantDb GitHub](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb).
2. Klik **Klonen of downloaden**.
3. Klik op **Zip-bestand downloaden** en sla het bestand op.
4. Klik met de rechtermuisknop op het bestand **WingtipTicketsSaaS-MultiTenantDb-master.zip** en selecteer **Eigenschappen**.
5. Op het tabblad **Algemeen** selecteert u **Deblokkeren** en klikt u op **Toepassen**.
6. Klik op **OK**.
7. Pak de bestanden uit.

De scripts bevinden zich in de map *..\\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\* .

## <a name="update-the-configuration-file-for-this-deployment"></a>Het configuratiebestand voor deze implementatie bijwerken

Voordat u scripts gaat uitvoeren, stelt u de waarden voor *resourcegroup* en *user* in op **UserConfig.psm1**. Stel deze variabelen in op dezelfde waarden die u tijdens de implementatie hebt ingesteld.

1. Open ...\\Learning Modules\\*UserConfig.psm1* in *PowerShell ISE*.
2. Werk *ResourceGroupName* en *Name* bij met de specifieke waarden voor uw implementatie (alleen in regels 10 en 11).
3. Sla de wijzigingen op.

De waarden die in dit bestand zijn ingesteld, worden in alle scripts gebruikt. Het is dus belangrijk dat ze kloppen. Als u de app opnieuw implementeert, moet u verschillende waarden kiezen voor User en Resourcegroup. Werk vervolgens het bestand UserConfig.psm1 opnieuw bij met de nieuwe waarden.

## <a name="run-the-application"></a>De toepassing uitvoeren

In de Wingtip-app zijn de tenants locaties. Een locatie kan een concertzaal, een sportclub of een andere locatie zijn waar een evenement plaatsvindt. De locaties registreren zich in Wingtip als klanten en voor elke locatie wordt een tenant-id gegenereerd. Elke locatie bevat een lijst met de aanstaande evenementen in Wingtip, zodat het publiek er tickets voor kan kopen.

Elke locatie krijgt een gepersonaliseerde web-app waarop de evenementen voor die locatie worden vermeld en kaartjes worden verkocht. Elke web-app is onafhankelijk en geïsoleerd van andere tenants. In Azure SQL Database worden de gegevens voor elke tenant intern opgeslagen in een shard-database voor meerdere tenants. Alle gegevens worden getagd met de tenant-id.

Een centrale **Events Hub**-webpagina bevat een lijst met koppelingen naar de tenants in uw specifieke implementatie. Gebruik de volgende stappen om kennis te maken met de **Events Hub**-webpagina en een afzonderlijke web-app:

1. Open **Events Hub** in uw webbrowser:
   - http://events.wingtip-mt.&lt;user&gt;.trafficmanager.net &nbsp; *(Vervang &lt; user&gt; door uw gebruikerswaarde van de implementatie.)*

     ![events hub](./media/saas-multitenantdb-get-started-deploy/events-hub.png)

2. Klik in de **Events Hub** op **Fabrikam Jazz Club**.

   ![Gebeurtenissen](./media/saas-multitenantdb-get-started-deploy/fabrikam.png)

### <a name="azure-traffic-manager"></a>Azure Traffic Manager

De Wingtip-app maakt gebruik van [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) om de distributie van inkomende aanvragen te regelen. De pagina met evenementen voor elke tenant bevat de tenantnaam in de desbetreffende URL. Elke URL bevat ook uw specifieke gebruikerswaarde. Elke URL voldoet aan de weergegeven indeling door de volgende stappen te volgen:

- http://events.wingtip-mt.&lt;user&gt;.trafficmanager.net/*fabrikamjazzclub*

1. De evenementen-app parseert de tenantnaam van de URL. In de voorgaande voorbeeld-URL is *fabrikamjazzclub* de naam van de tenant.
2. De app past een hashfunctie toe op de tenantnaam om een sleutel te maken en via [Shard-toewijzingsbeheer](elastic-scale-shard-map-management.md) toegang te krijgen tot een catalogus.
3. De app zoekt de sleutel in de catalogus en haalt de bijbehorende locatie op uit de database van de tenant.
4. De app gebruikt de locatiegegevens voor het zoeken en openen van de database die alle gegevens voor de tenant bevat.

### <a name="events-hub"></a>Events Hub

1. **Events Hub** bevat uitgebreide metagegevens die met de bijbehorende locaties in de catalogus worden geregistreerd.
2. **Events Hub** maakt gebruik van uitgebreide metagegevens in de catalogus om de naam op te halen van de tenant die aan elke toewijzing is gekoppeld. Zodoende kunnen de URL's worden geconstrueerd.

In een productieomgeving wordt er meestal een CNAME DNS-record gemaakt om [het internetdomein van uw bedrijf te laten verwijzen](../../traffic-manager/traffic-manager-point-internet-domain.md) naar het profiel van Traffic Manager.

## <a name="start-generating-load-on-the-tenant-databases"></a>Workloads genereren voor de tenant-databases

Nu de app is geïmplementeerd, kunt u ermee aan de slag. Met het PowerShell-script *Demo-LoadGenerator* wordt een workload voor elke tenant gestart. De werkelijke belasting voor veel SaaS-apps is doorgaans sporadisch en onvoorspelbaar. Om dit type belasting te simuleren, produceert de generator een belasting die over alle tenants wordt verdeeld. De belasting bevat willekeurige bursts op elke tenant die zich met willekeurige intervallen voordoen. Het duurt enkele minuten voordat het laadpatroon wordt weergegeven. U kunt de generator het beste gedurende ten minste drie of vier minuten uitvoeren voordat u de belasting gaat bewaken.

1. In *PowerShell ISE* opent u het script ...\\Learning Modules\\Utilities\\*Demo-LoadGenerator.ps1*.
2. Druk op **F5** om het script uit te voeren en de load-generator te starten (gebruik voorlopig de standaardwaarden van het script).

Met het script *Demo-LoadGenerator.ps1* wordt een nieuwe PowerShell-sessie geopend waarin de generator wordt uitgevoerd. De generator wordt in deze sessie als een voorgrondtaak uitgevoerd die voor elke tenant op de achtergrond een belasting genereert.

Nadat de voorgrondtaak is gestart, blijft deze in de status voor het aanroepen van taken. Met de taak worden extra achtergrondtaken gestart voor nieuwe tenants die vervolgens worden ingericht.

Als de PowerShell-sessie wordt afgesloten, worden alle taken gestopt.

Als u andere parameterwaarden wilt gebruiken, kunt u de sessie met de belastinggenerator opnieuw starten. Als u dit wilt, sluit u de sessie van de PowerShell-generatie en voert u *Demo-LoadGenerator.ps1* opnieuw uit.

## <a name="provision-a-new-tenant-into-the-sharded-database"></a>Een nieuwe tenant inrichten in de shard-database

De initiële implementatie bevat drie voorbeelden van tenants in de database *Tenants1*. U gaat een andere tenant maken en kijken wat de gevolgen ervan zijn voor de geïmplementeerde toepassing. In deze stap drukt u op één knop om een nieuwe tenant te maken:

1. Open ...\\Learning Modules\\Provision and Catalog\\*Demo-ProvisionTenants.ps1* in *PowerShell ISE*.
2. Druk op **F5** (niet op **F8**) om het script uit te voeren (laat de standaardwaarden voorlopig staan).

   > [!NOTE]
   > U moet de PowerShell-scripts alleen uitvoeren met toets **F5**, niet met toets **F8** als u een bepaald deel van het script wilt uitvoeren. Het probleem met **F8** is dat de variabele *$PSScriptRoot* niet wordt geëvalueerd. Deze variabele is voor veel scripts nodig om door mappen te navigeren, andere scripts aan te roepen of modules te importeren.

De nieuwe Red Maple Racing-tenant wordt toegevoegd aan de database *Tenants1* en in de catalogus geregistreerd. De nieuwe site voor de ticketverkoop van de tenant, **Evenementen**, wordt in de browser geopend:

![Nieuwe tenant](./media/saas-multitenantdb-get-started-deploy/red-maple-racing.png)

Vernieuw **Events Hub**. De nieuwe tenant wordt nu in de lijst weergegeven.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Een nieuwe tenant in de eigen database inrichten

Met het shard-model voor meerdere tenants kunt u kiezen of u een nieuwe tenant wilt inrichten in een database met andere tenants of in een eigen database. Een tenant die in een eigen database is geïsoleerd, heeft de volgende voordelen:

- De prestaties van de database van de tenant kunnen worden beheerd zonder dat er rekening hoeft te worden gehouden met de behoeften van andere tenants.
- De database kan, indien nodig, worden hersteld naar een eerder tijdstip, omdat er geen andere tenants worden beïnvloed.

U kunt ervoor kiezen om klanten met een gratis proefversie of een goedkope versie in databases voor meerdere tenants te plaatsen. Elke Premium-tenant kan in een eigen, toegewezen database worden geplaatst. Als u veel databases maakt die slechts één tenant bevatten, kunt u ze allemaal gezamenlijk beheren in een elastische pool om de resourcekosten te optimaliseren.

Vervolgens gaat u een andere tenant inrichten, deze keer in een eigen database:

1. In ...\\Learning Modules\\Provision and Catalog\\*Demo-ProvisionTenants.ps1* wijzigt u *$TenantName* in **Salix Salsa**,  *$VenueType* in **dance** en *$Scenario* in **2**.

2. Druk op **F5** om het script nogmaals uit te voeren.
    - Met **F5** wordt de nieuwe tenant in een afzonderlijke database ingericht. De database en de tenant worden in de catalogus geregistreerd. Vervolgens wordt de browser geopend op de evenementenpagina van de tenant.

   ![Evenementenpagina Salix Salsa](./media/saas-multitenantdb-get-started-deploy/salix-salsa.png)

   - Scrol naar de onderkant van de pagina. In de banner ziet u de naam van de database waarin de tenantgegevens zijn opgeslagen.

3. Vernieuw **Events Hub**. De twee nieuwe tenants worden nu in de lijst weergegeven.

## <a name="explore-the-servers-and-tenant-databases"></a>De servers en tenantdatabases verkennen

U gaat nu enkele resources bekijken die zijn geïmplementeerd:

1. Blader in [Azure Portal](https://portal.azure.com) naar de lijst met resourcegroepen. Open de resource groep die u hebt gemaakt bij het implementeren van de toepassing.

   ![resourcegroep](./media/saas-multitenantdb-get-started-deploy/resource-group.png)

2. Klik op de server **catalog-mt&lt;user&gt;** . De catalogusserver bevat twee databases: *tenantcatalog* en *basetenantdb*. Database *basetenantdb* is een lege sjabloondatabase. Deze wordt gekopieerd om een nieuwe tenantdatabase te maken, ongeacht of deze voor veel tenants of slechts een wordt gebruikt.

   ![catalogusserver](./media/saas-multitenantdb-get-started-deploy/catalog-server.png)

3. Ga terug naar de resourcegroep en selecteer server *tenants1-mt*, die de tenantdatabases bevat.
    - Database tenants1 is een database voor meerdere tenants, waarin de oorspronkelijke drie tenants, plus de eerste tenant die u hebt toegevoegd, zijn opgeslagen. De database is geconfigureerd als een standaarddatabase van 50 DTU's.
    - Database **salixsalsa** bevat danslocatie Salix Salsa als enige tenant. Deze is geconfigureerd als een Standard Edition-database van 50 DTU's.

   ![tenantsserver](./media/saas-multitenantdb-get-started-deploy/tenants-server.png)

## <a name="monitor-the-performance-of-the-database"></a>De prestaties van de database bewaken

Als de belastinggenerator enkele minuten actief is, moeten er voldoende telemetrische gegevens beschikbaar zijn om eens naar de bewakingsfuncties van de database te kijken die in Azure Portal zijn ingebouwd.

1. Ga naar de server **tenants1-mt&lt;user&gt;** en klik op **tenants1** om het resourcegebruik voor de database met vier tenants te bekijken. Elke tenant ondervindt af en toe een zware belasting van de belastinggenerator:

   ![tenants1 bewaken](./media/saas-multitenantdb-get-started-deploy/monitor-tenants1.png)

   De grafiek met het gebruik in DTU's laat zien hoe een database voor meerdere tenants een onvoorspelbare workload voor veel tenants kan verdragen. In dit geval legt de belastinggenerator sporadisch een belasting op van ongeveer 30 DTU's per tenant. Deze belasting komt overeen met een database van 50 DTU's die voor 60% wordt gebruikt. Pieken die de 60% overschrijden, zijn het gevolg van het feit dat de belasting op hetzelfde moment op meer dan één tenant wordt toegepast.

2. Ga naar de server **tenants1-mt&lt;user&gt;** en klik op de **salixsalsa**-database. U kunt het resourcegebruik van deze database zien die slechts één tenant bevat.

   ![database salixsalsa](./media/saas-multitenantdb-get-started-deploy/monitor-salix.png)

De belastinggenerator past een soortgelijke belasting toe op elke tenant, ongeacht in welke database de tenants zich bevinden. Nu de **salixsalsa**-database slechts één tenant bevat, ziet u dat de database een veel hogere belasting kan verdragen dan de database met meerdere tenants. 

### <a name="resource-allocations-vary-by-workload"></a>Resourcetoewijzingen variëren per workload

Soms zijn voor een database voor meerdere tenants meer resources nodig om goed te kunnen presteren dan voor een database voor één tenant, maar niet altijd. De optimale toewijzing van resources is afhankelijk van de specifieke eigenschappen van de workload voor de tenants in uw systeem.

De workloads die door het belastinggeneratorscript worden gegenereerd, zijn alleen bedoeld ter illustratie.

## <a name="additional-resources"></a>Aanvullende bronnen

- Informatie over SaaS-toepassingen voor meerdere tenants vindt u in [Ontwerppatronen voor SaaS-toepassingen voor meerdere tenants](saas-tenancy-app-design-patterns.md).

- Voor informatie over elastische pools raadpleegt u:

  - [Meerdere databases in Azure SQL Database beheren en schalen met elastische pools](elastic-pool-overview.md)
  - [Uitbreiden met Azure SQL Database](elastic-scale-introduction.md)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> - De toepassing Wingtip Tickets SaaS-database voor meerdere tenants implementeren.
> - Informatie over de servers en databases die deel uitmaken van de app.
> - Tenants worden via de *catalogus* toegewezen aan hun gegevens.
> - Nieuwe tenants inrichten in een database voor meerdere tenants en in een database voor één tenant.
> - Poolgebruik bekijken om de activiteit van een tenant te bewaken.
> - Voorbeeldresources verwijderen om gerelateerde facturering te stoppen.

U kunt nu [de zelfstudie over het inrichten en catalogiseren van tenants](saas-multitenantdb-provision-and-catalog.md) volgen.


<!--  Link references.

A [series of related tutorials] is available that build upon this initial deployment.
[link-wtp-overivew-jumpto-saas-tutorials-97j]: saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials

-->

[link-aka-ms-deploywtp-mtapp-52k]: https://aka.ms/deploywtp-mtapp


[link-azure-get-started-powershell-41q]: /powershell/azure/get-started-azureps

[link-github-wingtip-multitenantdb-55g]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB/



<!--  Image references.

[image-deploy-to-azure-blue-48d]: https://aka.ms/deploywtp-mtapp "Button for Deploy to Azure."
-->

[image-deploy-to-azure-blue-48d]: media/saas-multitenantdb-get-started-deploy/deploy.png "Knop voor het implementeren in Azure."
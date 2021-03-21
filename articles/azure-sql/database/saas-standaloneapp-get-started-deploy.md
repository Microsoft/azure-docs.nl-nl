---
title: Zelfstudie voor SaaS-app met één tenant
description: Een SaaS-app met één tenant implementeren en verkennen die gebruikmaakt van Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 11/07/2018
ms.openlocfilehash: 896f4edb1e94c1eca06b046382727a5042375e3b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92793276"
---
# <a name="deploy-and-explore-a-standalone-single-tenant-application-that-uses-azure-sql-database"></a>Een zelfstandige app met één tenant implementeren en verkennen die gebruikmaakt van Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In deze zelfstudie implementeert en verkent u de SaaS-voorbeeldapp Wingtip Tickets die is ontwikkeld met behulp van de zelfstandige app of het app-per-tenant-patroon.  De app is ontworpen om functies van Azure SQL Database weer te geven zodat SaaS-scenario's met meerdere tenants eenvoudiger mogelijk kunnen worden gemaakt.

Met de zelfstandige app of het app-per-tenant-patroon wordt een app-instantie voor elke tenant geïmplementeerd.  Elke app wordt geconfigureerd voor een specifieke tenant en geïmplementeerd in een afzonderlijke Azure-resourcegroep. Meerdere instanties van de app worden ingericht om een oplossing met meerdere tenants te bieden. Dit patroon is het geschiktst voor kleinere aantallen tenants waarbij de isolatie van de tenants topprioriteit heeft. Azure heeft partnerprogramma's waarbij resources kunnen worden geïmplementeerd in het abonnement van een tenant en worden beheerd door een serviceprovider namens de tenant. 

In deze zelfstudie implementeert u drie zelfstandige apps voor drie tenants in uw Azure-abonnement.  U hebt volledige toegang om te verkennen en te werken met de afzonderlijke app-onderdelen.

De broncode en beheerscripts van de app zijn beschikbaar in de GitHub-opslagplaats voor de [zelfstandige SaaS-app Wingtip Tickets](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp). De app is gemaakt met Visual Studio 2015 en kan niet worden geopend en gecompileerd in Visual Studio 2019 zonder te worden bijgewerkt.


In deze zelfstudie komen deze onderwerpen aan bod:

> [!div class="checklist"]
> * De zelfstandige SaaS-app Wingtip Tickets implementeren.
> * Waar u de broncode en beheerscripts van de app ophaalt.
> * Informatie over de servers en databases die deel uitmaken van de app.

Aanvullende zelfstudies worden uitgebracht. U kunt hierin een aantal beheerscenario's verkennen op basis van dit app-patroon.   

## <a name="deploy-the-wingtip-tickets-saas-standalone-application"></a>De zelfstandige SaaS-app Wingtip Tickets implementeren

Implementeer de app voor de drie opgegeven tenants:

1. Klik op elke blauwe knop **Implementeren in Azure** om de implementatiesjabloon in [Azure Portal](https://portal.azure.com) te openen. Voor elke sjabloon zijn twee parameterwaarden vereist. Een naam voor een nieuwe resourcegroep en een gebruikersnaam die deze implementatie onderscheidt van andere implementaties van de app. In de volgende stap worden de details voor het instellen van deze waarden beschreven.

   **Contoso Concert Hall**   
   [![Afbeelding met een knop met het label Implementeren naar Azure.](media/saas-standaloneapp-get-started-deploy/deploy.png)](https://aka.ms/deploywingtipsa-contoso)

   **Dogwood Dojo**   
   [![Afbeelding met een knop met het label Implementeren naar Azure.](media/saas-standaloneapp-get-started-deploy/deploy.png)](https://aka.ms/deploywingtipsa-dogwood)

   **Fabrikam Jazz Club**   
   [![Afbeelding met een knop met het label Implementeren naar Azure.](media/saas-standaloneapp-get-started-deploy/deploy.png)](https://aka.ms/deploywingtipsa-fabrikam)
 
2. Voer de vereiste parameterwaarden in voor elke implementatie.

    > [!IMPORTANT]
    > Om het overzichtelijk te houden zijn bepaalde verificatieprocessen weggelaten. Ook zijn de firewalls op servers uitgeschakeld voor deze zelfstudie. **Maak een nieuwe resourcegroep** voor elke app-implementatie.  Gebruik geen bestaande resourcegroep. Gebruik deze app of resources die hiermee worden gemaakt niet in een productieomgeving. Verwijder alle resourcegroepen wanneer u klaar bent met de apps om de facturering hiervoor te stoppen.

    U kunt in de resourcenamen het beste alleen kleine letters, cijfers en afbreekstreepjes gebruiken.
    * Selecteer voor **Resourcegroep** de optie Nieuwe maken en geef een naam in kleine letters op voor de resourcegroep. **wingtip-sa-\<venueName\>-\<user\>** is het aanbevolen patroon.  Voor \<venueName\> vervangt u de naam van de locatie zonder spaties. Voor \<user\> vervangt u de hieronder vermelde gebruikerswaarde.  Met dit patroon kunnen namen van resourcegroepen er als volgt uitzien: *wingtip-sa-contosoconcerthall-af1*, *wingtip-sa-dogwooddojo-af1*, *wingtip-sa-fabrikamjazzclub-af1*.
    * Selecteer een **locatie** in de vervolgkeuzelijst.

    * Voor **Gebruiker** wordt een korte waarde aanbevolen, zoals uw initialen plus een cijfer: bijvoorbeeld *af1*.


3. **Implementeer de toepassing**.

    * Klik om akkoord te gaan met de voorwaarden.
    * Klik op **Kopen**.

4. Controleer de status van de drie implementaties door te klikken op **Meldingen** (het belpictogram rechts van het zoekvak). Het implementeren van de apps duurt ongeveer vijf minuten.


## <a name="run-the-applications"></a>De toepassingen uitvoeren

De app geeft een overzicht van de locaties waarop evenementen plaatsvinden.  De locaties zijn de tenants van de app. Elke locatie krijgt een gepersonaliseerde website waarop de evenementen voor die locatie worden vermeld en kaartjes worden verkocht. Voorbeelden van locatietypen zijn concertzalen, jazzclubs en sportverenigingen. In het voorbeeld bepaalt het type locatie de achtergrondfoto die wordt weergegeven op de website van de locatie.   In het zelfstandige app-model heeft elke locatie een afzonderlijke app-instantie met een eigen zelfstandige Azure SQL Database.

1. Open de pagina met evenementen voor elk van de drie tenants in afzonderlijke browsertabbladen:

   - http://events.contosoconcerthall.&lt;user&gt;.trafficmanager.net
   - http://events.dogwooddojo.&lt;user&gt;.trafficmanager.net
   - http://events.fabrikamjazzclub.&lt;user&gt;.trafficmanager.net

     (Vervang in elke URL &lt;user&gt; door de gebruikerswaarde van uw implementatie.)

   ![Gebeurtenissen](./media/saas-standaloneapp-get-started-deploy/fabrikam.png)

De app maakt gebruik van [*Azure Traffic Manager*](../../traffic-manager/traffic-manager-overview.md) om de distributie van inkomend verkeer te regelen. Elke app-instantie voor een bepaalde tenant bevat de tenantnaam als onderdeel van de domeinnaam in de URL. Alle tenant-URL's bevatten uw specifieke waarde voor **Gebruiker**. De URL's hebben de volgende notatie:
- http://events.&lt;venuename&gt;.&lt; user&gt;.trafficmanager.net

De **locatie** van de database van elke tenant is opgenomen in de app-instellingen van de betreffende geïmplementeerde app.

In een productieomgeving maakt u doorgaans een CNAME DNS-record om [*een internetdomein van het bedrijf te laten verwijzen*](../../traffic-manager/traffic-manager-point-internet-domain.md) naar de URL van het Traffic Manager-profiel.


## <a name="explore-the-servers-and-tenant-databases"></a>De servers en tenantdatabases verkennen

Laten we eens enkele resources bekijken die zijn geïmplementeerd:

1. Blader in [Azure Portal](https://portal.azure.com) naar de lijst met resourcegroepen.
2. Er worden drie tenant-resourcegroepen weergegeven.
3. Open de resourcegroep **wingtip-sa-fabrikam-&lt;user&gt;** , die de resources bevat voor de implementatie van Fabrikam Jazz Club.  De server **fabrikamjazzclub-&lt;user&gt;** bevat de database **fabrikamjazzclub**.

Elke tenantdatabase is een *zelfstandige* 50 DTU-database.

## <a name="additional-resources"></a>Aanvullende bronnen

<!--
* Additional [tutorials that build on the Wingtip SaaS application](./saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* To learn about elastic pools, see [*What is an Azure SQL elastic pool*](elastic-pool-overview.md)
* To learn about elastic jobs, see [*Managing scaled-out cloud databases*](./elastic-jobs-overview.md)
-->

- Raadpleeg [Ontwerppatronen voor SaaS-apps voor meerdere tenants](saas-tenancy-app-design-patterns.md) voor meer informatie over SaaS-apps voor meerdere tenants.

 
## <a name="delete-resource-groups-to-stop-billing"></a>Resourcegroepen verwijderen om de facturering te stoppen ##

Wanneer u klaar bent met het gebruik van het voorbeeld, verwijdert u alle resourcegroepen die u hebt gemaakt om de facturering hiervoor te stoppen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * De zelfstandige SaaS-app Wingtip Tickets implementeren.
> * Informatie over de servers en databases die deel uitmaken van de app.
> * Voorbeeldresources verwijderen om gerelateerde facturering te stoppen.

Bekijk vervolgens de zelfstudie [Inrichten en catalogiseren](saas-standaloneapp-provision-and-catalog.md), waarin u het gebruik van een catalogus van tenants verkent die een reeks scenario's voor meerdere tenants, zoals schemabeheer en tenant-analyses, mogelijk maakt.

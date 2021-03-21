---
title: Een database in een SaaS-app voor meerdere tenants herstellen
description: Lees hoe u een Azure SQL Database voor één tenant herstelt nadat per ongeluk gegevens zijn verwijderd
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: 88496a39b0186cefb7c64e227530b5d73e693094
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92780475"
---
# <a name="restore-a-single-tenant-with-a-database-per-tenant-saas-application"></a>Eén tenant herstellen met een SaaS-app met een database per tenant
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Met het model van een database per tenant kunt u eenvoudig één tenant herstellen naar een eerder tijdstip zonder dat dit van invloed is op andere tenants.

In deze zelfstudie worden twee patronen voor gegevensherstel beschreven:

> [!div class="checklist"]
> * Een database herstellen in een parallelle database (naast elkaar).
> * Een database ter plekke herstellen, waarbij de bestaande database wordt vervangen.

| Patroon | Beschrijving |
|:--|:--|
| Herstellen in een parallelle database | Dit patroon kan worden gebruikt voor taken zoals evaluatie, controle en naleving zodat een tenant de betreffende gegevens vanaf een eerder tijdstip kan inspecteren. De huidige database van de tenant blijft online en ongewijzigd. |
| Ter plekke herstellen | Dit patroon wordt doorgaans gebruikt om een tenant te herstellen naar een eerder tijdstip, nadat een tenant per ongeluk gegevens heeft verwijderd of beschadigd. De oorspronkelijke database wordt offline gezet en vervangen door de herstelde database. |
|||

U kunt deze zelfstudie alleen voltooien als aan de volgende vereisten wordt voldaan:

* De SaaS-app Wingtip wordt geïmplementeerd. Zie [De Wingtip SaaS-app implementeren en verkennen](./saas-dbpertenant-get-started-deploy.md) als u de app in minder dan vijf minuten wilt implementeren.
* Azure PowerShell is geïnstalleerd. Zie [Aan de slag met Azure PowerShell](/powershell/azure/get-started-azureps) voor meer informatie.

## <a name="introduction-to-the-saas-tenant-restore-patterns"></a>Inleiding tot de herstelpatronen voor SaaS-tenants

Er zijn twee eenvoudige patronen voor het herstellen van de gegevens van een afzonderlijke tenant. Omdat de tenantdatabases van elkaar zijn geïsoleerd, heeft het herstellen van één tenant geen invloed op de gegevens van andere tenants. De Azure SQL Database-functie voor herstellen naar een bepaald tijdstip (Point-in-time-Restore, PITR) wordt in beide patronen gebruikt. Met PITR wordt altijd een nieuwe database gemaakt.

* **Parallel herstellen**: In het eerste patroon wordt een nieuwe parallelle database gemaakt naast de huidige database van de tenant. De tenant krijgt vervolgens alleen-lezen toegang tot de herstelde database. De herstelde gegevens kunnen worden gecontroleerd en mogelijk worden gebruikt om de huidige gegevens te overschrijven. De ontwerper van de app bepaalt hoe de tenant toegang verkrijgt tot de herstelde database en welke opties voor herstel worden geboden. Het kan in sommige scenario's volstaan dat de tenant wordt toegestaan om de gegevens op een eerder tijdstip te controleren.

* **Ter plekke herstellen**: Het tweede patroon is nuttig als gegevens verloren zijn gegaan of beschadigd zijn en men wil dat met de tenant wordt hersteld naar een eerder tijdstip. De tenant wordt offline geplaatst terwijl de database wordt hersteld. De oorspronkelijke database wordt verwijderd en de naam van de herstelde database wordt gewijzigd. De back-upketen van de oorspronkelijke database blijft toegankelijk na het verwijderen, zodat u de database zo nodig kunt herstellen naar een eerder tijdstip.

Als de database gebruikmaakt van [actieve geo-replicatie](active-geo-replication-overview.md) en een parallel herstel, wordt aangeraden de vereiste gegevens van de herstelde database naar de oorspronkelijke database te kopiëren. Als u de oorspronkelijke database vervangt door de herstelde database, moet u geo-replicatie opnieuw configureren en opnieuw synchroniseren.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>De Wingtip Tickets-SaaS-database-per-tenant-toepassingsscripts ophalen

De databasescripts en app-broncode van de SaaS-app met meerdere tenants Wingtip Tickets vindt u in de GitHub-opslagplaats [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant). Raadpleeg de [algemene richtlijnen](saas-tenancy-wingtip-app-guidance-tips.md) voor stappen voor het downloaden en deblokkeren van de scripts voor de SaaS-app Wingtip Tickets.

## <a name="before-you-start"></a>Voordat u begint

Wanneer een database wordt gemaakt, kan het 10 tot 15 minuten duren voordat de eerste volledige back-up beschikbaar is vanwaaruit kan worden hersteld. Als u de app zojuist hebt geïnstalleerd, moet u mogelijk enkele minuten wachten voordat u dit scenario kunt uitvoeren.

## <a name="simulate-a-tenant-accidentally-deleting-data"></a>Simuleren dat een tenant per ongeluk gegevens verwijdert

Als u deze herstelscenario's wilt demonstreren, moet u eerst 'per ongeluk' een gebeurtenis in een van de tenantdatabases verwijderen. 

### <a name="open-the-events-app-to-review-the-current-events"></a>De app Gebeurtenissen openen om de huidige gebeurtenissen te bekijken

1. Open de Event Hub (http://events.wtp.&lt;user&gt;.trafficmanager.net) en selecteer **Contoso Concert Hall**.

   ![Event Hub](./media/saas-dbpertenant-restore-single-tenant/events-hub.png)

2. Schuif door de lijst met gebeurtenissen en noteer de laatste gebeurtenis in de lijst.

   ![De laatste gebeurtenis wordt weergegeven](./media/saas-dbpertenant-restore-single-tenant/last-event.png)

### <a name="accidentally-delete-the-last-event"></a>Verwijder 'per ongeluk' de laatste gebeurtenis

1. Open in PowerShell ISE ...\\Leermodules\\Bedrijfscontinuïteit en herstel na noodgevallen\\RestoreTenant\\*Demo-RestoreTenant.ps1* en stel de volgende waarde in:

   * **$DemoScenario** = **1**, *De laatste gebeurtenis verwijderen (zonder ticketverkoop)* .
2. Druk op F5 om het script uit te voeren en de laatste gebeurtenis te verwijderen. Het volgende bevestigingsbericht wordt weergegeven:

   ```Console
   Deleting last unsold event from Contoso Concert Hall ...
   Deleted event 'Seriously Strauss' from Contoso Concert Hall venue.
   ```

3. De pagina met Contoso-gebeurtenissen wordt geopend. Schuif naar beneden en controleer of de gebeurtenis is verdwenen. Als de gebeurtenis nog steeds in de lijst staat, selecteert u **Vernieuwen** en controleert u of de gebeurtenis is verdwenen.
   ![Laatste gebeurtenis is verwijderd](./media/saas-dbpertenant-restore-single-tenant/last-event-deleted.png)

## <a name="restore-a-tenant-database-in-parallel-with-the-production-database"></a>Een tenantdatabase parallel met de productiedatabase herstellen

In deze oefening wordt de Contoso Concert Hall-database hersteld naar een bepaald tijdstip voordat de gebeurtenis werd verwijderd. In dit scenario wordt ervan uitgegaan dat u de verwijderde gegevens in een parallelle database wilt controleren.

 Met het script *Restore-TenantInParallel.ps1* wordt een parallelle tenantdatabase met de naam *ContosoConcertHall\_old* gemaakt, samen met een parallelle catalogusvermelding. Dit herstelpatroon is geschikt voor herstel bij beperkt gegevensverlies. U kunt dit patroon ook gebruiken als u gegevens moet controleren voor nalevings- of controledoeleinden. Het is de aanbevolen methode wanneer u gebruikmaakt van [actieve geo-replicatie](active-geo-replication-overview.md).

1. Voer het gedeelte [Simuleren dat een tenant per ongeluk gegevens verwijdert](#simulate-a-tenant-accidentally-deleting-data) uit.
2. Open in PowerShell ISE ...\\Leermodules\\Bedrijfscontinuïteit en herstel na noodgevallen\\RestoreTenant\\_Demo-RestoreTenant.ps1_.
3. Stel het volgende in: **$DemoScenario** = **2**, *De tenant parallel herstellen*.
4. Druk op F5 om het script uit te voeren.

Met het script wordt de tenantdatabase hersteld naar een tijdstip voordat u de gebeurtenis hebt verwijderd. De database wordt hersteld in een nieuwe database met de naam _ContosoConcertHall\_old_. De metagegevens van de catalogus die voorkomen in deze herstelde database worden verwijderd en vervolgens wordt de database toegevoegd aan de catalogus met behulp van een sleutel die is gemaakt uit de naam *ContosoConcertHall\_old*.

Met het demoscript wordt de pagina met gebeurtenissen voor deze nieuwe tenantdatabase geopend in uw browser. Uit de URL ```http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall_old``` kunt u opmaken dat op deze pagina gegevens worden weergegeven uit de herstelde database waarvoor *_old* aan de naam is toegevoegd.

Schuif door de gebeurtenissen die worden vermeld in de browser om te controleren of de gebeurtenis die is verwijderd in de vorige sectie is hersteld.

De herstelde tenant beschikbaar maken als een aanvullende tenant, met een eigen Gebeurtenissen-app, is waarschijnlijk niet de manier waarop u een tenant toegang biedt tot herstelde gegevens. Het dient om het herstelpatroon te illustreren. Normaal gesproken verleent u alleen-lezen toegang tot de oude gegevens en behoudt u de herstelde database gedurende een bepaalde periode. In het voorbeeld kunt u de vermelding voor de herstelde tenant verwijderen nadat u klaar bent door het scenario _Herstelde tenant verwijderen_ uit te voeren.

1. Stel het volgende in: **$DemoScenario** = **4**, *Herstelde tenant verwijderen*.
2. Druk op F5 om het script uit te voeren.
3. De vermelding *ContosoConcertHall\_old* wordt nu verwijderd uit de catalogus. Sluit de pagina met gebeurtenissen voor deze tenant in uw browser.

## <a name="restore-a-tenant-in-place-replacing-the-existing-tenant-database"></a>Een tenant ter plekke herstellen, waarbij de bestaande tenantdatabase wordt vervangen

In deze oefening wordt de Contoso Concert Hall-tenant hersteld naar een bepaald tijdstip voordat de gebeurtenis werd verwijderd. Met het script *Restore-TenantInPlace* wordt een tenantdatabase hersteld in een nieuwe database en wordt de oorspronkelijke database verwijderd. Dit herstelpatroon is geschikt voor herstel bij ernstige gegevensbeschadiging en de tenant moet mogelijk een aanzienlijk gegevensverlies opvangen.

1. Open in PowerShell ISE het bestand **Demo-RestoreTenant.ps1**.
2. Stel het volgende in: **$DemoScenario** = **5**, *De tenant ter plekke herstellen*.
3. Druk op F5 om het script uit te voeren.

Met het script wordt de tenantdatabase hersteld naar een tijdstip voordat de gebeurtenis werd verwijderd. Eerst wordt de Contoso Concert Hall-tenant offline gehaald om verdere updates te voorkomen. Vervolgens wordt er een parallelle database gemaakt door de database te herstellen vanaf het herstelpunt. De herstelde database krijgt een naam met een tijdstempel om ervoor te zorgen dat de databasenaam geen conflict veroorzaakt met de naam van de bestaande tenantdatabase. Vervolgens wordt de oude tenantdatabase verwijderd en wordt de naam van de herstelde database gewijzigd in de naam van de oorspronkelijke database. Ten slotte wordt Contoso Concert Hall online geplaatst zodat de app toegang kan krijgen tot de herstelde database.

U hebt de database hersteld naar een tijdstip voordat de gebeurtenis is verwijderd. Wanneer de pagina **Gebeurtenissen** wordt geopend, controleert u of de laatste gebeurtenis is hersteld.

Nadat u de database hebt hersteld, kan het 10 tot 15 minuten duren voordat de eerste volledige back-up beschikbaar is vanwaaruit weer kan worden hersteld.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Een database herstellen in een parallelle database (naast elkaar).
> * Een database ter plekke herstellen.

Lees de zelfstudie [Het tenantdatabaseschema beheren](saas-tenancy-schema-management.md).

## <a name="additional-resources"></a>Aanvullende bronnen

* [Aanvullende zelfstudies waarbij wordt voortgebouwd op de SaaS-app Wingtip](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Overzicht van bedrijfscontinuïteit met Azure SQL Database](business-continuity-high-availability-disaster-recover-hadr-overview.md)
* [Meer informatie over back-ups van Azure SQL Database](automated-backups-overview.md)
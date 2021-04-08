---
title: Voor beeld van multi tenant-app-Wingtip SaaS
description: Bevat stappen en richt lijnen voor het installeren en uitvoeren van de voor beeld-multi tenant toepassing die gebruikmaakt van Azure SQL Database, het SaaS-voor beeld van Wingtip tickets.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: aa9215fa001fb117000eb6a68867ddd46fac9b92
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92780322"
---
# <a name="general-guidance-for-working-with-wingtip-tickets-sample-saas-apps"></a>Algemene richt lijnen voor het werken met Wingtip tickets voor beelden van SaaS-apps
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Dit artikel bevat algemene richt lijnen voor het uitvoeren van de voor beelden van de SaaS-toepassingen van Wingtip tickets die gebruikmaken van Azure SQL Database.

## <a name="download-and-unblock-the-wingtip-tickets-saas-scripts"></a>De SaaS-scripts van de Wingtip tickets downloaden en de blok kering opheffen

Uitvoer bare inhoud (scripts, dll's) wordt mogelijk door Windows geblokkeerd wanneer zip-bestanden worden gedownload vanaf een externe bron en geëxtraheerd. Wanneer u de scripts uit een zip-bestand uitpakt, **volgt u de onderstaande stappen om het zip-bestand te deblokkeren voordat het wordt geëxtraheerd**. Dit zorgt ervoor dat de scripts mogen worden uitgevoerd.

1. Blader naar de Wingtip tickets SaaS GitHub opslag plaats voor het data base-pacht patroon dat u wilt verkennen:
    - [WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp)
    - [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant)
    - [WingtipTicketsSaaS-MultiTenantDb](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb)
2. Klik **Klonen of downloaden**.
3. Klik op **zip downloaden** en sla het bestand op.
4. Klik met de rechter muisknop op het zip-bestand en selecteer **Eigenschappen**. De naam van het zip-bestand komt overeen met de naam van de opslag plaats. kade. _WingtipTicketsSaaS-DbPerTenant-master.zip_)
5. Selecteer op het tabblad **Algemeen** de optie **blok kering opheffen**.
6. Klik op **OK**.
7. Pak de bestanden uit.

De scripts bevinden zich in de *.. \\ Map learning modules* .


## <a name="working-with-the-wingtip-tickets-powershell-scripts"></a>Werken met de Power shell-scripts voor Wingtip tickets

Als u het voor beeld optimaal wilt benutten, moet u zich in de meegeleverde scripts voordoen. Gebruik onderbrekings punten en bestudeer de scripts wanneer ze worden uitgevoerd en onderzoek hoe de verschillende SaaS-patronen worden geïmplementeerd. We raden u aan de [Power shell-ISE](/powershell/scripting/components/ise/introducing-the-windows-powershell-ise)te gebruiken om eenvoudig de meegeleverde scripts en modules voor de beste uitleg te door lopen.

### <a name="update-the-configuration-file-for-your-deployment"></a>Het configuratie bestand voor uw implementatie bijwerken

Bewerk het bestand **userconfig. psm1** met de resource groep en gebruikers waarde die u tijdens de implementatie hebt ingesteld:

1. Open de *Power shell-ISE* en laad... \\ Learning modules \\ *userconfig. psm1*
2. Werk *ResourceGroupName* en *Name* bij met de specifieke waarden voor uw implementatie (alleen in regels 10 en 11).
3. Sla de wijzigingen op.

Als u deze waarden hier instelt, hoeft u deze specifieke implementatie waarden in elk script niet bij te werken.

### <a name="execute-the-scripts-by-pressing-f5"></a>Voer de scripts uit door op F5 te drukken

Verschillende scripts gebruiken *$PSScriptRoot* om door mappen te navigeren en *$PSScriptRoot* wordt alleen geëvalueerd wanneer scripts worden uitgevoerd door op **F5** te drukken.  Als u een selectie markeert en uitvoert (**F8**) kan dit leiden tot fouten, dus druk op **F5** bij het uitvoeren van scripts.

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>Doorloop de scripts stapsgewijs om de implementatie te kunnen bekijken

De beste manier om de scripts te begrijpen, is door ze stapsgewijs te door lopen om te zien wat ze doen. Bekijk de meegeleverde **demo** scripts waarmee u een werk stroom op hoog niveau kunt volgen. De **demo** scripts tonen de stappen die nodig zijn om elke taak uit te voeren, dus stel onderbrekings punten in en zoom dieper in op de afzonderlijke aanroepen om de implementatie Details voor de verschillende SaaS-patronen te bekijken.

Tips voor het verkennen en stapsgewijs door lopen van Power shell-scripts:

- Open **demo-** scripts in de Power shell ISE.
- Uitvoeren of door gaan met **F5** (het gebruik van **F8** wordt niet aanbevolen omdat *$PSScriptRoot* niet wordt geëvalueerd bij het uitvoeren van selecties van een script).
- Plaats onderbrekingspunten door op een regel te klikken of een regel te selecteren en op **F9** te drukken.
- Stap over een functie of scriptaanroep heen met **F10**.
- Stap in een functie of scriptaanroep met **F11**.
- Stap uit de huidige functie of scriptaanroep met **Shift + F11**.


## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>Bekijk het databaseschema en voer SQL-query’s uit met SSMS

Gebruik [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) om verbinding te maken en te bladeren door de toepassings servers en data bases.

De implementatie heeft in eerste instantie tenants en catalogus servers om verbinding mee te maken. De naam van de servers is afhankelijk van het data base-pacht patroon (zie hieronder voor specifieke informatie).

   - **Zelfstandige toepassing:** servers voor elke Tenant (bijvoorbeeld *contosoconcerthall- &lt; Gebruikers &gt;* server) en *catalogus-sa- &lt; gebruiker &gt;*
   - **Data Base per Tenant:** *tenants1-dpt &lt; - &gt; gebruikers* -en *catalogus-dpt- &lt; gebruikers &gt;* servers
   - **Multi tenant-Data Base:** *tenants1-MT &lt; - &gt; User* and *Catalog-MT &lt; - &gt; gebruikers* servers

Om ervoor te zorgen dat de demo verbinding is geslaagd, hebben alle servers een [firewall regel](firewall-configure.md) voor het toestaan van alle IP-adressen via.


1. Open *SSMS* en maak verbinding met de tenants. De server naam is afhankelijk van het patroon van de data base dat u hebt geselecteerd (zie hieronder voor specifieke informatie):
    - **Zelfstandige toepassing:** servers van afzonderlijke tenants (bv. *contosoconcerthall- &lt; User &gt; . database.Windows.net*)
    - **Data Base per Tenant:** *tenants1-dpt- &lt; User &gt; . database.Windows.net*
    - **Multi tenant-Data Base:** *tenants1-MT- &lt; User &gt; . database.Windows.net*
2. Klik op  >  **Data base-engine verbinden...**:

   ![catalogusserver](./media/saas-tenancy-wingtip-app-guidance-tips/connect.png)

3. Demo referenties zijn: Login = *Developer*, password = *P \@ ssword1*

    In de onderstaande afbeelding ziet u de aanmeldings gegevens voor de *Data Base per Tenant* patroon.
    ![Combi](./media/saas-tenancy-wingtip-app-guidance-tips/tenants1-connect.png)



4. Herhaal de stappen 2-3 en maak verbinding met de catalogus server (zie hieronder voor specifieke server namen op basis van het patroon voor de data base-pacht geselecteerd)
    - **Zelfstandige toepassing:** *Catalog-sa- &lt; User &gt; . database.Windows.net*
    - **Data Base per Tenant:** *Catalog-dpt- &lt; User &gt; . database.Windows.net*
    - **Multi tenant-Data Base:** *Catalog-MT- &lt; User &gt; . database.Windows.net*


Nadat u verbinding hebt gemaakt, ziet u alle servers. De lijst met data bases kan afwijken, afhankelijk van de tenants die u hebt ingericht.

In de onderstaande afbeelding ziet u het logboek voor de *Data Base per Tenant* patroon.

![objectverkenner](./media/saas-tenancy-wingtip-app-guidance-tips/object-explorer.png)



## <a name="next-steps"></a>Volgende stappen
- [De zelfstandige SaaS-app Wingtip Tickets implementeren](./saas-standaloneapp-get-started-deploy.md)
- [De SaaS-data base van Wingtip tickets per Tenant toepassing implementeren](./saas-dbpertenant-get-started-deploy.md)
- [De Wingtip tickets SaaS-toepassing voor meerdere tenants implementeren](./saas-multitenantdb-get-started-deploy.md)
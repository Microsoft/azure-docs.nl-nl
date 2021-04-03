---
title: Inrichten in SaaS-multitenant
description: Meer informatie over het inrichten en catalogiseren van nieuwe tenants in een Azure SQL Database-multi-tenant-SaaS-app
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/24/2018
ms.openlocfilehash: eddb0c8339069025f0742e9bcbc371efbef094ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92793327"
---
# <a name="provision-and-catalog-new-tenants-in-a-saas-application-using-a-sharded-multi-tenant-azure-sql-database"></a>Nieuwe tenants inrichten en catalogiseren in een SaaS-toepassing met behulp van een shard-multitenant-Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In dit artikel vindt u informatie over het inrichten en catalogiseren van nieuwe tenants in een *multitenant-shard-database*-model of -patroon.

Dit artikel heeft twee belangrijke onderdelen:

- [Conceptuele discussie](#goto_2_conceptual) over het inrichten en catalogiseren van nieuwe tenants.

- [Zelfstudie](#goto_1_tutorial) die de nadruk legt op de PowerShell-scriptcode die het inrichten en catalogiseren uitvoert.
  - In de zelfstudie wordt gebruikgemaakt van de SaaS-toepassing Wingtip Tickets, aangepast aan het multitenant-shard-databasepatroon.

<a name="goto_2_conceptual"></a>

## <a name="database-pattern"></a>Databasepatroon

In deze sectie vindt u meer informatie over de concepten van het multitenant-shard-databasepatroon.

In dit multitenant-shard-model bevatten de tabelschema's in elke database een tenantsleutel in de primaire sleutel van tabellen waarin tenantgegevens worden opgeslagen. De tenantsleutel maakt het mogelijk dat er in elke afzonderlijke database 0, 1 of veel tenants kunnen worden opgeslagen. Het gebruik van shard-databases maakt het eenvoudig voor het toepassingssysteem om een zeer groot aantal tenants te ondersteunen. Alle gegevens voor een tenant worden opgeslagen in één database. Het grote aantal tenants wordt gedistribueerd over de vele shard-databases. Een catalogusdatabase slaat de toewijzing van elke tenant op in de database.

#### <a name="isolation-versus-lower-cost"></a>Isolatie tegenover lagere kosten

Een tenant die een database voor zichzelf heeft, kan gebruikmaken van de voordelen van isolatie. De tenant kan de database herstellen naar een eerdere datum zonder dat dit wordt beperkt door de invloed die dit heeft op andere tenants. De prestaties van de database kunnen worden afgestemd op de optimalisatie van de ene tenant, wederom zonder dat u rekening hoeft te houden met andere tenants. Het probleem is dat de isolatie meer kost dan het kost om een database te delen met andere tenants.

Wanneer een nieuwe tenant is ingericht, kan deze een database met andere tenants delen of in een eigen nieuwe database worden geplaatst. Later kunt u van gedachten veranderen en de database verplaatsen naar de andere situatie.

Databases met meerdere tenants en afzonderlijke tenants worden in dezelfde SaaS-toepassing gemengd om de kosten of isolatie van elke tenant te optimaliseren.

   ![Shard-multitenant-database-app met tenantcatalogus](./media/saas-multitenantdb-provision-and-catalog/MultiTenantCatalog.png)

## <a name="tenant-catalog-pattern"></a>Tenantcataloguspatroon

Wanneer u twee of meer databases hebt die elk ten minste één tenant bevatten, moet de toepassing een manier hebben om te ontdekken in welke database de tenant van de huidige interesse is opgeslagen. Deze toewijzing wordt opgeslagen in een catalogusdatabase.

#### <a name="tenant-key"></a>Tenantsleutel

Voor elke tenant kan de toepassing Wingtip een unieke sleutel afleiden, de tenantsleutel. De app haalt de tenantnaam op uit de URL van de webpagina. De app hasht de naam om de sleutel te verkrijgen. De app gebruikt de sleutel voor toegang tot de catalogus. De catalogus bevat kruisverwijzingen naar informatie over de database waarin de tenant is opgeslagen. De app maakt gebruik van de databasegegevens om verbinding te maken. Er kunnen ook andere tenantsleutelschema's worden gebruikt.

Als u een catalogus gebruikt, kan de naam of locatie van een tenantdatabase na het inrichten worden gewijzigd zonder de toepassing te onderbreken. In een multitenant-databasemodel is de catalogus geschikt voor het verplaatsen van een tenant tussen databases.

#### <a name="tenant-metadata-beyond-location"></a>Metagegevens van tenant buiten locatie

De catalogus kan ook aangeven of een tenant offline is voor onderhoud of andere acties. En de catalogus kan worden uitgebreid om aanvullende tenant- of metagegevens van de database op te slaan, zoals de volgende items:
- De servicelaag of editie van een database.
- De versie van het databaseschema.
- De naam van de tenant en de SLA (service level agreement).
- Informatie over het inschakelen van toepassingsbeheer, klantondersteuning of devops-processen.

De catalogus kan ook worden gebruikt om cross-tenant-rapportage, schemabeheer en gegevensextractie voor analysedoeleinden in te schakelen.

### <a name="elastic-database-client-library"></a>Clientbibliotheek voor Elastische database

In Wingtip wordt de catalogus geïmplementeerd in de database *tenantcatalog*. De *tenantcatalog* wordt gemaakt met behulp van de Shard-beheerfuncties van de [Clientbibliotheek voor Elastische database (EDCL)](elastic-database-client-library.md). Met de bibliotheek kan een toepassing een *shard-toewijzing* maken, beheren en gebruiken die wordt opgeslagen in een database. Een shard-toewijzing vormt een kruisverwijzing naar de tenantsleutel met de shard, namelijk die shard-database.

Tijdens het inrichten van de tenant kunnen EDCL-functies worden gebruikt vanuit toepassingen of PowerShell-scripts om de vermeldingen in de shard-toewijzing te maken. Later kunt u de EDCL-functies gebruiken om verbinding te maken met de juiste database. De EDCL slaat de verbindingsgegevens op in de cache om het verkeer in de catalogusdatabase zo klein mogelijk te maken en het verbindingsproces te versnellen.

> [!IMPORTANT]
> Bewerk de gegevens in de catalogusdatabase *niet* via directe toegang! Directe updates worden niet ondersteund vanwege het hoge risico op beschadiging van gegevens. Bewerk in plaats daarvan de toewijzingsgegevens met behulp van EDCL-API's.

## <a name="tenant-provisioning-pattern"></a>Inrichtingspatroon voor tenant

#### <a name="checklist"></a>Controlelijst

Wanneer u een nieuwe tenant in een bestaande gedeelde database wilt inrichten, moet u de volgende vragen stellen:
- Is er voldoende ruimte over voor de nieuwe tenant?
- Bevat deze tabellen met de vereiste verwijzingsgegevens voor de nieuwe tenant of kunnen de gegevens worden toegevoegd?
- Heeft het de juiste variatie van het basisschema voor de nieuwe tenant?
- Bevindt het zich op de juiste geografische locatie, dicht bij de nieuwe tenant?
- Bevindt het zich op de juiste servicelaag voor de nieuwe tenant?

Als u wilt dat de nieuwe tenant wordt geïsoleerd in een eigen database, kunt u deze maken om te voldoen aan de specificaties voor de tenant.

Nadat het inrichten is voltooid, moet u de tenant registreren in de catalogus. Ten slotte kan de tenanttoewijzing worden toegevoegd om te verwijzen naar de juiste shard.

#### <a name="template-database"></a>Sjabloondatabase

Richt de database in door SQL-scripts uit te voeren, een Bacpac te implementeren of een sjabloondatabase te kopiëren. De Wingtip-apps kopiëren een sjabloondatabase om nieuwe tenantdatabases te maken.

Net als bij elke toepassing zal Wingtip zich na verloop van tijd ontwikkelen. Af en toe is het voor Wingtip vereist dat er wijzigingen in de database worden aangebracht. Wijzigingen kunnen de volgende items bevatten:
- Nieuw of gewijzigd schema.
- Nieuwe of gewijzigde referentiegegevens.
- Routineuze onderhoudstaken voor de database om optimale prestaties van apps te garanderen.

Bij een SaaS-toepassing moeten deze wijzigingen op een gecoördineerde wijze worden geïmplementeerd in een mogelijk enorme reeks tenantdatabases. De wijzigingen moeten ook worden opgenomen in het inrichtingsproces om beschikbaar te zijn in toekomstige tenantdatabases. Deze uitdaging vindt u verderop in de [zelfstudie schemabeheer](saas-tenancy-schema-management.md).

#### <a name="scripts"></a>Scripts

De scripts voor het inrichten van tenants in deze zelfstudie ondersteunen de volgende scenario's:
- Het inrichten van een tenant in een bestaande database die wordt gedeeld met andere tenants.
- Een tenant inrichten in een eigen database.

Tenantgegevens worden vervolgens geïnitialiseerd en geregistreerd in de catalogus-shard-toewijzing. In de voorbeeld-app krijgen databases die meerdere tenants bevatten een algemene naam, zoals *tenants1* of *tenants2*. Databases die één tenant bevatten, krijgen de naam van de tenant. De specifieke naamgevingsregels die in het voorbeeld worden gebruikt, zijn geen essentieel onderdeel van het patroon. Als u een catalogus gebruikt, kan elke naam worden toegewezen aan de database.

<a name="goto_1_tutorial"></a>

## <a name="tutorial-begins"></a>Zelfstudie begint

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een tenant inrichten in een multitenant-database
> * Een tenant inrichten in een database met één tenant
> * Een batch met tenants inrichten in zowel multitenant-databases als databases met één tenant
> * Een database- en tenanttoewijzing registreren in een catalogus

#### <a name="prerequisites"></a>Vereisten

U kunt deze zelfstudie alleen voltooien als aan de volgende vereisten wordt voldaan:

- Azure PowerShell is geïnstalleerd. Zie [Aan de slag met Azure PowerShell](/powershell/azure/get-started-azureps) voor meer informatie.

- De Wingtip Tickets SaaS Multi-tenant Database-app wordt geïmplementeerd. Zie [De Wingtip Tickets SaaS Multi-tenant Database-toepassing implementeren en verkennen](./saas-multitenantdb-get-started-deploy.md) om deze binnen vijf minuten te implementeren

- De Wingtip-scripts en -broncode ophalen:
    - De Wingtip Tickets SaaS-multitenantdatabasescripts en broncode van de toepassing zijn beschikbaar in de GitHub-opslagplaats [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB).
    - Zie de [algemene richtlijnen](saas-tenancy-wingtip-app-guidance-tips.md) voor stappen om de Wingtip-scripts te downloaden en deblokkeren.

## <a name="provision-a-tenant-into-a-database-shared-with-other-tenants"></a>Een tenant inrichten in een database die wordt *gedeeld* met andere tenants

In deze sectie ziet u een lijst met de belangrijkste acties voor het inrichten die worden uitgevoerd door de PowerShell-scripts. Vervolgens gebruikt u het foutopsporingsprogramma PowerShell ISE om de scripts te doorlopen om de acties in de code te zien.

#### <a name="major-actions-of-provisioning"></a>Belangrijkste acties voor het inrichten

Hieronder ziet u de belangrijkste elementen van de werkstroom voor het inrichten:

- **De nieuwe tenantsleutel berekenen**: Er wordt een hash-functie gebruikt om de tenant-sleutel te maken op basis van de naam van de tenant.
- **Controleren of de tenantsleutel al bestaat**: De catalogus wordt gecontroleerd om na te gaan of de sleutel nog niet is geregistreerd.
- **Tenant in de standaard-tenantdatabase initialiseren**: De tenantdatabase wordt bijgewerkt, zodat de nieuwe tenantgegevens kunnen worden toegevoegd.
- **Tenant registreren in de catalogus**: De toewijzing tussen de nieuwe tenantsleutel en de bestaande tenants1-database wordt toegevoegd aan de catalogus.
- **Voeg de naam van de tenant toe aan een tabel met catalogusuitbreidingen**: De naam van de locatie wordt toegevoegd aan de tabel met tenants in de catalogus.  In deze aanvulling ziet u hoe de Catalogusdatabase kan worden uitgebreid om aanvullende toepassingsspecifieke gegevens te ondersteunen.
- **Open Events-pagina voor de nieuwe tenant**: De evenementenpagina *Bushwillow Blues* wordt geopend in de browser.

   ![Schermopname van de pagina Gebeurtenissen voor een nieuwe tenant.](./media/saas-multitenantdb-provision-and-catalog/bushwillow.png)

#### <a name="debugger-steps"></a>Stappen van het foutopsporingsprogramma

Als u wilt weten hoe de Wingtip-app nieuwe tenant-inrichtingen implementeert in een gedeelde database, voegt u een onderbrekingspunt toe en voert u de volgende stappen uit:

1. Open in *PowerShell ISE* ...\\Learning Modules\\ProvisionTenants\\*Demo-ProvisionTenants.ps1* en stel de volgende parameters in:
   - **$TenantName** = **Bushwillow Blues**, de naam van een nieuwe locatie.
   - **$VenueType** = **blues**, een van de vooraf gedefinieerde typen venues: blues, classicalmusic, dance, jazz, judo, motorracing, multipurpose, opera, rockmusic, soccer (kleine letters, geen spaties).
   - **$DemoScenario** = **1** om een tenant in een gedeelde database in te richten met andere tenants.

2. Voeg een onderbrekingspunt toe door de cursor ergens op regel 38 te plaatsen, dat is de regel met de volgende tekst: *New-Tenant `* en druk vervolgens op **F9**.

   ![Schermopname van de regel die de nieuwe tenant bevat.](./media/saas-multitenantdb-provision-and-catalog/breakpoint.png)

3. Voer het script uit door op **F5** te drukken.

4. Wanneer de uitvoering van het script stopt bij het onderbrekingspunt, drukt u op **F11** om de code te bekijken.

   ![Schermopname met de Windows PowerShell ISE met het menu Fouten opsporen geopend en Step Into geselecteerd.](./media/saas-multitenantdb-provision-and-catalog/debug.png)

5. Bekijk de uitvoering van het script via de opties van het menu **Fouten opsporen**, **F10** en **F11**, om aangeroepen functies over te slaan of weer te geven.

Zie [Tips voor het werken met en opsporen van fouten in PowerShell-scripts](/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise) voor meer informatie over foutopsporing in PowerShell-scripts.

## <a name="provision-a-tenant-in-its-own-database"></a>Een tenant inrichten in zijn *eigen* database

#### <a name="major-actions-of-provisioning"></a>Belangrijkste acties voor het inrichten

Hieronder vindt u sleutelelementen van de werkstroom die u moet doorlopen tijdens het traceren van het script:

- **De nieuwe tenantsleutel berekenen**: Er wordt een hash-functie gebruikt om de tenant-sleutel te maken op basis van de naam van de tenant.
- **Controleren of de tenantsleutel al bestaat**: De catalogus wordt gecontroleerd om na te gaan of de sleutel nog niet is geregistreerd.
- **Maak een nieuwe tenantdatabase**: De database wordt gemaakt door de *basetenantdb*-database te kopiëren met behulp van een Resource Manager-sjabloon.  De naam van de nieuwe database is gebaseerd op de naam van de tenant.
- **Database toevoegen aan catalogus**: De nieuwe tenantdatabase is als een shard geregistreerd in de catalogus.
- **Tenant in de standaard-tenantdatabase initialiseren**: De tenantdatabase wordt bijgewerkt, zodat de nieuwe tenantgegevens kunnen worden toegevoegd.
- **Tenant registreren in de catalogus**: De toewijzing tussen de nieuwe tenantsleutel en de *sequoiasoccer*-database wordt toegevoegd aan de catalogus.
- **De tenantnaam wordt toegevoegd aan de catalogus**: De naam van de locatie wordt toegevoegd aan de tabel met tenantuitbreidingen in de catalogus.
- **Open Events-pagina voor de nieuwe tenant**: De Events-pagina *Sequoia Soccer* wordt geopend in de browser.

   ![events](./media/saas-multitenantdb-provision-and-catalog/sequoiasoccer.png)

#### <a name="debugger-steps"></a>Stappen van het foutopsporingsprogramma

Doorloop nu het scriptproces bij het maken van een tenant in een eigen database:

1. U bevindt zich nog steeds in ...\\Learning Modules\\ProvisionTenants\\*Demo-ProvisionTenants.ps1*. Stel daar de volgende parameters in:
   - **$TenantName** = **Sequoia Soccer**, de naam van een nieuwe locatie.
   - **$VenueType** = **soccer**, een van de vooraf gedefinieerde typen venues: blues, classicalmusic, dance, jazz, judo, motorracing, multipurpose, opera, rockmusic, soccer (kleine letters, geen spaties).
   - **$DemoScenario** = **2** om een tenant in te richten in een eigen database.

2. Voeg een nieuwe onderbrekingspunt toe door de cursor ergens op regel 57 te plaatsen, dat is de regel met de volgende tekst: *&&nbsp;$PSScriptRoot \New-TenantAndDatabase `* en druk op **F9**.

   ![onderbrekingspunt](./media/saas-multitenantdb-provision-and-catalog/breakpoint2.png)

3. Voer het script uit door op **F5** te drukken.

4. Wanneer de uitvoering van het script stopt bij het onderbrekingspunt, drukt u op **F11** om de code te bekijken.  Gebruik **F10** en **F11** om functies te gebruiken om de uitvoering te traceren.

## <a name="provision-a-batch-of-tenants"></a>Verschillende tenants tegelijk inrichten

In deze oefening gaat u een batch met 17 tenants inrichten. Het is raadzaam om deze batch met tenants in te richten voordat u andere zelfstudies voor Wingtip Tickets start. Zo zijn er meer databases om mee te werken.

1. Open in *PowerShell ISE* ...\\Learning Modules\\ProvisionTenants\\*Demo-ProvisionTenants.ps1* en wijzig de parameter *$DemoScenario* in 4:
   - **$DemoScenario** = **4** voor het inrichten van een batch tenants in een gedeelde database.

2. Druk op **F5** om het script uit te voeren.

### <a name="verify-the-deployed-set-of-tenants"></a>De geïmplementeerde set tenants controleren

In deze fase hebt u een combinatie van tenants die zijn geïmplementeerd in een gedeelde database en tenants die zijn geïmplementeerd in hun eigen databases. U kunt Azure Portal gebruiken om de gemaakte databases te controleren. Open in [Azure Portal](https://portal.azure.com) de server **tenants1-mt\<USER\>** door naar de lijst met SQL-servers te bladeren.  De lijst met **SQL-databases** moet de gedeelde database **tenants1** en de databases voor de tenants in hun eigen database bevatten:

   ![databaselijst](./media/saas-multitenantdb-provision-and-catalog/Databases.png)

Alhoewel Azure Portal de tenantdatabases toont, kunt u de tenants *in* de gedeelde database niet zien. De volledige lijst met tenants kan worden weergegeven op de webpagina **Events Hub** van Wingtip en door te bladeren in de catalogus.

#### <a name="using-wingtip-tickets-events-hub-page"></a>De Event Hub-pagina van Wingtip Tickets gebruiken

Open de Event Hub-pagina in de browser (http:events.wingtip-mt.\<USER\>.trafficmanager.net)

#### <a name="using-catalog-database"></a>Catalogusdatabase gebruiken

De volledige lijst met tenants en de bijbehorende database voor elke tenant is beschikbaar in de catalogus. U ziet een SQL-weergave die de naam van de tenant koppelt aan de naam van de database. De weergave toont de waarde voor het uitbreiden van de metagegevens die zijn opgeslagen in de catalogus.
- De SQL-weergave is beschikbaar in de database tenantcatalogus.
- De naam van de tenant wordt opgeslagen in de tabel met tenants.
- De naam van de database wordt opgeslagen in de Shard Management-tabellen.

1. In SQL Server Management Studio (SSMS) maakt u verbinding met de server van de tenant op **catalogus-mt.\<USER\>.database.windows.net**, met Login = **developer** en wachtwoord = **P\@ssword1**

    ![Dialoogvenster voor SSMS-verbinding](./media/saas-multitenantdb-provision-and-catalog/SSMSConnection.png)

2. In de SSMS Objectverkenner bladert u naar de weergaven in de database *tenantcatalog*.

3. Klik met de rechtermuisknop op de weergave *TenantsExtended* en kies **Bovenste 1000 rijen selecteren**. Let op de toewijzing tussen de naam van de tenant en de database voor de verschillende tenants.

    ![Weergave ExtendedTenants in SSMS](./media/saas-multitenantdb-provision-and-catalog/extendedtenantsview.png)

## <a name="other-provisioning-patterns"></a>Andere inrichtingspatronen

In deze sectie worden andere interessante inrichtingspatronen besproken.

#### <a name="pre-provisioning-databases-in-elastic-pools"></a>Databases vooraf inrichten in elastische pools

Met het patroon voor vooraf inrichten wordt gebruikgemaakt van het feit dat bij het gebruik van elastische pools wordt gefactureerd voor de groep, en niet voor de databases. Databases kunnen daarom, zonder extra kosten, worden toegevoegd aan een elastische pool voordat ze nodig zijn. Dit vooraf inrichten vermindert de tijd die nodig is om een tenant in te richten in een database aanzienlijk. Het aantal databases dat vooraf wordt ingericht, kan naar wens worden aangepast om zo te beschikken over een buffer die overeenkomt met de verwachte vraag naar nieuwe tenants.

#### <a name="auto-provisioning"></a>Automatische inrichting

In het patroon voor automatisch inrichten worden met een toegewezen inrichtingsservice automatisch zo nodig servers, pools en databases ingericht. Deze automatisering omvat het vooraf inrichten van databases in elastische pools. Als databases buiten gebruik worden gesteld en verwijderd, kunnen de hiaten die hierdoor ontstaan in elastische pools indien nodig met de inrichtingsservice worden opgevuld.

Dit type geautomatiseerde service kan eenvoudig of complex zijn. Met de automatisering kan bijvoorbeeld het inrichten voor meerdere geografische grafieken worden afgehandeld en geo-replicatie voor herstel na noodgevallen worden ingesteld. In het geval van het patroon voor automatische inrichting wordt door een clienttoepassing of -script een aanvraag voor inrichting in de wachtrij gezet voor verwerking door een inrichtingsservice. Er wordt vervolgens een poll op het script uitgevoerd om de voltooiing ervan te detecteren. Als inrichting vooraf wordt gebruikt, worden aanvragen snel verwerkt, waarbij een service op de achtergrond zorgt voor het inrichten van een vervangende database.

## <a name="additional-resources"></a>Aanvullende bronnen

<!-- - Additional [tutorials that build upon the Wingtip SaaS application](./saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
- [Clientbibliotheek voor Elastic Database](elastic-database-client-library.md)
- [How to Debug Scripts in Windows PowerShell ISE](/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise) (Fouten opsporen in scripts met Windows PowerShell ISE)


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Eén nieuwe tenant inrichten in een gedeelde multitenant-database en een eigen database
> * Verschillende tenants tegelijk inrichten
> * De details van het inrichten van tenants en het registreren in de catalogus doorlopen

Ga naar de [zelfstudie Prestatiebewaking](./saas-multitenantdb-performance-monitoring.md).
---
title: Uitschalen
description: SaaS-ontwikkel aars (Software as a Service) kunnen eenvoudig elastische, schaal bare data bases in de Cloud maken met behulp van deze hulpprogram ma's
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.topic: conceptual
ms.custom: sqldbrb=1
author: stevestein
ms.author: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 3f00b2c1a8a8264267aa8ae68d80890adeb642ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98059124"
---
# <a name="scaling-out-with-azure-sql-database"></a>Uitbreiden met Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

U kunt data bases eenvoudig uitschalen in Azure SQL Database met behulp van de **Elastic database** -hulpprogram ma's. Met deze hulpprogram ma's en functies kunt u de database resources van **Azure SQL database** gebruiken om oplossingen te maken voor transactionele werk belastingen, en met name SaaS-toepassingen (Software as a Service). Elastic Database-functies bestaan uit het volgende:

* [Elastic database-client bibliotheek](elastic-database-client-library.md): de client bibliotheek is een functie waarmee u Shard-data bases kunt maken en onderhouden.  Zie [aan de slag met Elastic database-hulpprogram ma's](elastic-scale-get-started.md).
* [Elastic database hulp programma voor splitsen en samen voegen](elastic-scale-overview-split-and-merge.md): verplaatst gegevens tussen Shard-data bases. Dit hulp programma is nuttig voor het verplaatsen van gegevens van een Data Base met meerdere tenants naar een Data Base met één Tenant (of andersom). Zie de [zelf studie voor Elastic data base Split-Merge tool](elastic-scale-configure-deploy-split-and-merge.md).
* [Taak voor Elastic database](elastic-jobs-overview.md) (preview): gebruik taken voor het beheren van grote aantallen data bases in Azure SQL database. U kunt eenvoudig beheer bewerkingen uitvoeren zoals schema wijzigingen, referentie beheer, referentie gegevens updates, verzameling van prestatie gegevens of Tenant-telemetrie verzameling met behulp van taken.
* [Elastic database query](elastic-query-overview.md) (preview): Hiermee kunt u een Transact-SQL-query uitvoeren die meerdere data bases omspant. Dit maakt het mogelijk om verbinding te maken met rapportage Programma's zoals Excel, Power BI, tableau, enzovoort.
* [Elastische trans acties](elastic-transactions-overview.md): met deze functie kunt u trans acties uitvoeren die meerdere data bases omvatten. Elastic data base-trans acties zijn beschikbaar voor .NET-toepassingen met behulp van ADO .NET en kunnen worden geïntegreerd met de bekende programmeer ervaring met behulp van de [System. Trans Action-klassen](/dotnet/api/system.transactions).

In de volgende afbeelding ziet u een architectuur die de **Elastic database-functies** bevat ten opzichte van een verzameling data bases.

In deze afbeelding vertegenwoordigen de kleuren van de data base schema's. Data bases met dezelfde kleur delen hetzelfde schema.

1. Een set **SQL-data bases** wordt gehost op Azure met behulp van de sharding-architectuur.
2. De **Elastic database-client bibliotheek** wordt gebruikt voor het beheren van een Shard-set.
3. Een subset van de data bases wordt in een **elastische pool** geplaatst. (Zie [Wat is een pool?](elastic-pool-overview.md)).
4. Met een **Elastic database taak** worden geplande of ad hoc T-SQL-scripts uitgevoerd voor alle data bases.
5. Het **hulp programma splitsen** wordt gebruikt om gegevens van een Shard naar een andere te verplaatsen.
6. Met de **Elastic database query** kunt u een query schrijven die alle data bases in de Shard-set omspant.
7. Met **elastische trans acties** kunt u trans acties uitvoeren die meerdere data bases omvatten. 

![Hulpprogramma's voor elastische databases][1]

## <a name="why-use-the-tools"></a>Waarom zou u de hulpprogram ma's gebruiken?

Het bereiken van de elasticiteit en schaal voor Cloud toepassingen is eenvoudig voor Vm's en Blob Storage. u hoeft alleen maar de eenheden toe te voegen of af te trekken, of de stroom te verg Roten. Maar er is wel een uitdaging voor het verwerken van stateful gegevens in relationele data bases. De uitdagingen in deze scenario's:

* Groei en krimping van capaciteit voor het deel van de relationele data base van uw werk belasting.
* Het beheren van HOTS pots die mogelijk van invloed zijn op een specifieke subset van gegevens, zoals een drukke eind gebruiker (Tenant).

In de meeste gevallen zijn scenario's zoals deze beschreven door investeren in grotere servers voor de ondersteuning van de toepassing. Deze optie is echter beperkt in de Cloud, waar alle verwerking plaatsvindt op vooraf gedefinieerde basishardware. In plaats daarvan is het distribueren van gegevens en verwerking in veel identieke gestructureerde data bases (een scale-out patroon bekend als ' sharding ') een alternatief voor traditionele scale-up benaderingen op het gebied van kosten en elasticiteit.

## <a name="horizontal-and-vertical-scaling"></a>Horizon taal en verticaal schalen

In de volgende afbeelding ziet u de horizontale en verticale afmetingen van schalen. Dit zijn de basis manieren waarop de elastische data bases kunnen worden geschaald.

![Horizon taal versus verticaal uitschalen][2]

Horizon taal schalen verwijst naar het toevoegen of verwijderen van data bases voor het aanpassen van de capaciteit of de algehele prestaties, ook wel ' uitschalen ' genoemd. Sharding, waarin gegevens worden gepartitioneerd over een verzameling van identieke gestructureerde data bases, is een gemeen schappelijke manier om horizon taal schalen te implementeren.  

Verticaal schalen verwijst naar het verg Roten of verkleinen van de reken grootte van een afzonderlijke Data Base, ook wel bekend als ' omhoog schalen '.

De meeste database toepassingen in de Cloud gebruiken een combi natie van deze twee strategieën. Een software als een service toepassing kan bijvoorbeeld horizon taal schalen gebruiken om nieuwe eind gebruikers en verticale schaling in te richten, zodat de data base van elke eind gebruiker de resources kan verg Roten of verkleinen als dat nodig is voor de werk belasting.

* Horizon taal schalen wordt beheerd met behulp van de [Elastic database-client bibliotheek](elastic-database-client-library.md).
* Verticaal schalen wordt uitgevoerd met behulp van Azure PowerShell-cmdlets om de servicelaag te wijzigen of door data bases in een elastische pool te plaatsen.

## <a name="sharding"></a>Sharding

*Sharding* is een techniek om grote hoeveel heden identieke, gestructureerde gegevens over een aantal onafhankelijke data bases te verdelen. Het is met name populair bij Cloud ontwikkelaars die software as a Service (SAAS)-aanbiedingen voor eind klanten of bedrijven maken. Deze eind klanten worden vaak ' tenants ' genoemd. Sharding kan om verschillende redenen vereist zijn:  

* De totale hoeveelheid gegevens is te groot om te passen binnen de beperkingen van een afzonderlijke data base
* De trans actie-door Voer van de totale werk belasting overschrijdt de mogelijkheden van een afzonderlijke data base
* Tenants kunnen fysieke isolatie van elkaar vereisen, zodat afzonderlijke data bases nodig zijn voor elke Tenant
* Verschillende secties van een Data Base moeten mogelijk in verschillende geografische grafieken worden geplaatst om te voldoen aan de vereisten, de prestaties of de geopolitieke redenen.

In andere scenario's, zoals het opnemen van gegevens van gedistribueerde apparaten, kan sharding worden gebruikt voor het vullen van een set data bases die tijdelijk zijn geordend. Een afzonderlijke Data Base kan bijvoorbeeld worden toegewezen aan elke dag of week. In dat geval kan de sharding-sleutel een geheel getal zijn dat de datum vertegenwoordigt (aanwezig in alle rijen van de Shard-tabellen) en moeten query's die informatie ophalen voor een datum bereik door de toepassing worden gerouteerd naar de subset van data bases die het desbetreffende bereik hebben.

Sharding werkt het beste wanneer elke trans actie in een toepassing kan worden beperkt tot één waarde van een sharding-sleutel. Hiermee zorgt u ervoor dat alle trans acties lokaal zijn voor een specifieke data base.

## <a name="multi-tenant-and-single-tenant"></a>Multi tenant en single-tenant

Sommige toepassingen gebruiken de eenvoudigste benadering van het maken van een afzonderlijke Data Base voor elke Tenant. Deze benadering is het **sharding-patroon met één Tenant** dat isolatie, back-ups maken/herstellen en het schalen van resources biedt bij de granulatie van de Tenant. Bij één Tenant sharding wordt elke Data Base gekoppeld aan een specifieke Tenant-ID-waarde (of de waarde van de klant sleutel), maar deze sleutel hoeft niet altijd aanwezig te zijn in de gegevens zelf. Het is de verantwoordelijkheid van de toepassing om elke aanvraag te routeren naar de juiste data base en de client bibliotheek kan dit vereenvoudigen.

![Eén Tenant versus multi tenant][4]

Andere scenario's verpakken meerdere tenants in data bases in plaats van ze te isoleren in afzonderlijke data bases. Dit patroon is een typisch **sharding patroon met meerdere tenants** en kan worden aangestuurd door het feit dat een toepassing een groot aantal kleine tenants beheert. In multi tenant-sharding zijn de rijen in de database tabellen allemaal ontworpen om een sleutel te bieden voor het identificeren van de Tenant-ID of de sharding-sleutel. Daarnaast is de toepassingslaag verantwoordelijk voor het routeren van de aanvraag van een Tenant naar de juiste data base. Dit kan worden ondersteund door de client bibliotheek voor Elastic data base. Daarnaast kan beveiliging op rijniveau worden gebruikt om te filteren tot welke rijen elke Tenant toegang heeft. Zie [multi tenant-toepassingen met Elastic data base-hulpprogram ma's en beveiliging op rijniveau](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)voor meer informatie. Het opnieuw distribueren van gegevens tussen data bases is mogelijk nodig met het sharding-patroon met meerdere tenants en wordt vergemakkelijkt door het Elastic data base-hulp programma voor splitsen en samen voegen. Zie [Ontwerppatronen voor SaaS-toepassingen met meerdere tenants met behulp van Azure SQL Database](saas-tenancy-app-design-patterns.md) voor meer informatie over ontwerppatronen voor SaaS-toepassingen met elastische pools.

### <a name="move-data-from-multiple-to-single-tenancy-databases"></a>Gegevens verplaatsen van meerdere naar single-pacht data bases
Bij het maken van een SaaS-toepassing is het gebruikelijk om potentiële klanten een evaluatie versie van de software aan te bieden. In dit geval is het rendabel om een Data Base met meerdere tenants te gebruiken voor de gegevens. Wanneer een prospect echter een klant wordt, is een Data Base met één Tenant beter omdat deze betere prestaties levert. Als de klant tijdens de proef periode gegevens heeft gemaakt, gebruikt u het [hulp programma voor splitsen en samen voegen](elastic-scale-overview-split-and-merge.md) om de gegevens van de multi tenant naar de nieuwe Data Base met één Tenant te verplaatsen.

## <a name="next-steps"></a>Volgende stappen
Zie [aan de slag met Elastic database-hulpprogram ma's](elastic-scale-get-started.md)voor een voor beeld-app die de client bibliotheek laat zien.

Zie [bestaande data bases migreren om uit te breiden](elastic-convert-to-use-elastic-tools.md)voor het converteren van bestaande data bases om de hulpprogram ma's te gebruiken.

Zie [prijs-en prestatie overwegingen voor een elastische pool](elastic-pool-overview.md)of een nieuwe groep maken met [elastische](elastic-pool-manage.md)Pools voor een overzicht van de details van de elastische pool.  

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/elastic-scale-introduction/tools.png
[2]:./media/elastic-scale-introduction/h_versus_vert.png
[3]:./media/elastic-scale-introduction/overview.png
[4]:./media/elastic-scale-introduction/single_v_multi_tenant.png

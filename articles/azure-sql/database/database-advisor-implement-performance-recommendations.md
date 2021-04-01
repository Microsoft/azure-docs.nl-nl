---
title: Aanbevelingen voor de prestaties van data base Advisor voor Azure SQL Database
description: Azure SQL Database biedt aanbevelingen voor data bases die de prestaties van query's in Azure SQL Database kunnen verbeteren.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: fasttrack-edit, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 03/10/2020
ms.openlocfilehash: 79ccf0f8aae7e915601081f875cea294de52d787
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96500849"
---
# <a name="database-advisor-performance-recommendations-for-azure-sql-database"></a>Aanbevelingen voor Database Advisor prestaties voor Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database leren en aanpassen met uw toepassing. Azure SQL Database heeft een aantal database adviseurs die aangepaste aanbevelingen bieden waarmee u de prestaties optimaal kunt benutten. Deze data base Advisor beoordelen en analyseren de gebruiks geschiedenis en bieden aanbevelingen op basis van de werkbelasting patronen die helpen de prestaties te verbeteren.

## <a name="performance-overview"></a>Overzicht van prestaties

Overzicht van prestaties biedt een samen vatting van de prestaties van uw data base en helpt u bij het afstemmen van prestaties en het oplossen van problemen.

![Overzicht van prestaties voor Azure SQL Database](./media/database-advisor-implement-performance-recommendations/performance-overview-annotated.png)

- De tegel **aanbevelingen** biedt een overzicht van de aanbevelingen voor het afstemmen van uw data base (de eerste drie aanbevelingen worden weer gegeven als er meer zijn). Als u op deze tegel klikt, gaat u naar **[Opties voor prestatie aanbeveling](database-advisor-find-recommendations-portal.md#viewing-recommendations)**.
- De tegel **activiteit afstemmen** biedt een samen vatting van de lopende en voltooide afstemmings acties voor uw data base, waarmee u een kort overzicht krijgt van de geschiedenis van de afstemmings activiteit. Als u op deze tegel klikt, gaat u naar de weer gave volledige afstemmings geschiedenis voor uw data base.
- De tegel **automatisch afstemmen** toont de **[configuratie voor automatisch afstemmen](automatic-tuning-enable.md)** van uw data base (afstemmings opties die automatisch worden toegepast op uw data base). Als u op deze tegel klikt, wordt het dialoog venster automatiserings configuratie geopend.
- De tegel **database query's** bevat een samen vatting van de query prestaties voor uw data base (algemeen DTU-gebruik en meest voorkomende query's in de resource). Als u op deze tegel klikt, gaat u **[query Performance Insight](query-performance-insight-use.md)**.

## <a name="performance-recommendation-options"></a>Opties voor prestatie aanbeveling

Beschik bare opties voor prestatie aanbeveling in Azure SQL Database zijn:

| Aanbevolen prestatie | Ondersteuning voor één data base en gepoolde data base | Ondersteuning voor instance data base |
| :----------------------------- | ----- | ----- |
| **Aanbevelingen voor Create Index** : Hiermee wordt het maken van indexen aanbevolen die de prestaties van uw werk belasting kunnen verbeteren. | Ja | Nee |
| **Aanbevelingen voor drop index** : Aanbevolen wordt om dagelijks overbodige en dubbele indexen te verwijderen, met uitzonde ring van unieke indexen en indexen die gedurende een lange periode niet zijn gebruikt (>90 dagen). Houd er rekening mee dat deze optie niet compatibel is met toepassingen die gebruikmaken van partitie switches en index hints. Het verwijderen van niet-gebruikte indexen wordt niet ondersteund voor Premium-en Bedrijfskritiek-service lagen. | Ja | Nee |
| **Aanbevelingen voor para meters query's (preview)** : beveelt geforceerde parameterisering aan wanneer u een of meer query's hebt die voortdurend worden gecompileerd, maar wel met hetzelfde query-uitvoerings plan. | Ja | Nee |
| **Aanbevelingen voor het oplossen van schema problemen (preview)** : aanbevelingen voor schema correctie worden weer gegeven wanneer Azure SQL database een afwijkings probleem ondervindt in het aantal aan het schema gerelateerde SQL-fouten dat op uw data base plaatsvindt. Micro soft maakt momenteel geen aanbevelingen voor het oplossen van schema problemen. | Ja | Nee |

![Prestatie aanbevelingen voor Azure SQL Database](./media/database-advisor-implement-performance-recommendations/performance-recommendations-annotated.png)

Zie [aanbevelingen Toep assen](database-advisor-find-recommendations-portal.md#applying-recommendations)om aanbevelingen voor prestaties toe te passen. Zie [bewerkingen bewaken](database-advisor-find-recommendations-portal.md#monitoring-operations)om de status van aanbevelingen weer te geven.

U kunt ook de volledige geschiedenis van afstemmings acties vinden die in het verleden zijn toegepast.

## <a name="create-index-recommendations"></a>Aanbevelingen voor index maken

Azure SQL Database bewaakt voortdurend de query's die worden uitgevoerd en identificeert de indexen waarmee de prestaties kunnen worden verbeterd. Wanneer er voldoende betrouw baarheid is dat een bepaalde index ontbreekt, wordt een nieuwe aanbeveling voor **Create Index** gemaakt.

Azure SQL Database bouwt de betrouw baarheid door de prestaties van de index in de loop van de tijd te schatten. Afhankelijk van de geschatte prestatieverbetering worden aanbevelingen gecategoriseerd als hoog, gemiddeld of laag.

Indexen die worden gemaakt met behulp van aanbevelingen, worden altijd gemarkeerd als indexen die automatisch worden gemaakt. U kunt zien welke indexen automatisch worden gemaakt door te kijken naar de [weer gave sys. Indexes](/sql/relational-databases/system-catalog-views/sys-indexes-transact-sql). Automatisch gemaakte indexen blok keren/wijzigen niet.

Als u probeert de kolom met een automatisch gemaakte index te verwijderen, wordt de opdracht door gegeven. De automatisch gemaakte index wordt ook met de opdracht verwijderd. Met reguliere indexen wordt de opdracht ALTER/RENAME voor geïndexeerde kolommen geblokkeerd.

Nadat de aanbeveling voor het maken van een index is toegepast, vergelijkt Azure SQL Database de prestaties van de query's met de basislijn prestaties. Als de nieuwe index betere prestaties heeft, wordt de aanbeveling als geslaagd gemarkeerd en is het impact rapport beschikbaar. Als de index geen prestaties heeft verbeterd, wordt deze automatisch hersteld. Azure SQL Database gebruikt dit proces om ervoor te zorgen dat aanbevelingen de database prestaties verbeteren.

Een aanbeveling voor het maken van een index heeft een **uitstel** beleid dat het Toep assen van de aanbeveling niet toestaat als het resource gebruik van een Data Base of groep hoog is. Het back-out-beleid houdt rekening met CPU, data IO, logboek-IO en beschik bare opslag.

Als CPU, data IO of logboek-IO hoger is dan 80% in de afgelopen 30 minuten, wordt de aanbeveling Create Index uitgesteld. Als de beschik bare opslag lager is dan 10% nadat de index is gemaakt, wordt de aanbeveling een fout status. Als, na een paar dagen, de automatische afstemming nog steeds meent dat de index nuttig zou zijn, wordt het proces opnieuw gestart.

Dit proces wordt herhaald totdat er voldoende beschik bare opslag ruimte is om een index te maken, of totdat de index niet meer wordt gezien als nuttig.

## <a name="drop-index-recommendations"></a>Aanbevelingen voor drop index

Naast het detecteren van ontbrekende indexen analyseert Azure SQL Database voortdurend de prestaties van bestaande indexen. Als een index niet wordt gebruikt, is het Azure SQL Database aan te raden deze te verwijderen. Het verwijderen van een index wordt in twee gevallen aanbevolen:

- De index is een duplicaat van een andere index (dezelfde geïndexeerde en opgenomen kolom, partitie schema en filters).
- De index is niet gebruikt voor een lange periode (93 dagen).

De aanbevelingen voor het verwijderen van de index passeren ook de verificatie na de implementatie. Als de prestaties verbeteren, is het impact rapport beschikbaar. Als de prestaties afnemen, wordt de aanbeveling hersteld.

## <a name="parameterize-queries-recommendations-preview"></a>Aanbevelingen voor para meters-query's (preview-versie)

Aanbevelingen voor *para meters-query's* worden weer gegeven wanneer u een of meer query's hebt die voortdurend worden gecompileerd, maar wel met hetzelfde query-uitvoerings plan. Met deze voor waarde wordt een kans gemaakt om geforceerde parameterisering toe te passen. Met geforceerde parameterisering kunnen query plannen in de cache worden opgeslagen en opnieuw worden gebruikt, waardoor de prestaties worden verbeterd en het resource gebruik wordt verminderd.

Elke query moet eerst worden gecompileerd om een uitvoerings plan te genereren. Elk gegenereerd plan wordt toegevoegd aan de plannings cache. Volgende uitvoeringen van dezelfde query kunnen dit plan opnieuw gebruiken uit de cache, waardoor er geen verdere compilatie meer nodig is.

Query's met waarden zonder parameters kunnen leiden tot prestatie-overhead, omdat het uitvoeringsplan opnieuw wordt gecompileerd telkens wanneer de waarden zonder parameters verschillend zijn. In veel gevallen genereren dezelfde query's met verschillende parameterwaarden dezelfde uitvoeringsplannen. Deze plannen worden echter nog steeds afzonderlijk toegevoegd aan de plancache.

Het proces van het opnieuw compileren van uitvoeringsplannen maakt gebruik van databaseresources, verhoogt de duur van de query en overschrijdt de plancache. Deze gebeurtenissen zorgen ervoor dat er plannen uit de cache worden verwijderd. Dit gedrag kan worden gewijzigd door de optie geforceerde parameterisering in de data base in te stellen.

Om u te helpen bij het schatten van de impact van deze aanbeveling, krijgt u een vergelijking van het werkelijke CPU-gebruik en het verwachte CPU-gebruik (alsof de aanbeveling is toegepast). Deze aanbeveling kan u helpen CPU-besparingen te realiseren. Het kan ook helpen de duur en overhead van de query te verlagen voor de plannings cache, wat betekent dat meer van de plannen in de cache kunnen blijven en opnieuw worden gebruikt. U kunt deze aanbeveling snel Toep assen door de opdracht **Toep assen** te selecteren.

Nadat u deze aanbeveling hebt toegepast, wordt geforceerde parameterisering binnen enkele minuten in uw data base ingeschakeld. Het bewakings proces wordt gestart. dit duurt ongeveer 24 uur. Na deze periode kunt u het validatie rapport bekijken. In dit rapport wordt het CPU-gebruik van uw data base 24 uur voor en na het Toep assen van de aanbeveling weer gegeven. Azure SQL Database Advisor heeft een veiligheids mechanisme waarmee automatisch de toegepaste aanbeveling wordt hersteld als er prestatie regressie is gedetecteerd.

## <a name="fix-schema-issues-recommendations-preview"></a>Aanbevelingen voor het oplossen van schema problemen (preview-versie)

> [!IMPORTANT]
> Micro soft maakt momenteel geen aanbevelingen voor het oplossen van schema problemen. We raden u aan [intelligent Insights](intelligent-insights-overview.md) te gebruiken voor het bewaken van de prestatie problemen met uw data base, zoals schema problemen die eerder zijn besproken in de aanbevelingen voor het oplossen van het schema probleem.

Aanbevelingen voor **schema problemen oplossen** wordt weer gegeven wanneer Azure SQL database een afwijkings probleem ondervindt in het aantal aan het schema gerelateerde SQL-fouten in uw data base. Deze aanbeveling wordt doorgaans weer gegeven wanneer in uw data base meerdere schema fouten optreden (ongeldige kolom naam, ongeldige object naam, enzovoort) binnen een uur.

"Schema problemen" zijn een klasse syntaxis fouten. Ze treden op wanneer de definitie van de SQL-query en de definitie van het database schema niet zijn uitgelijnd. Een van de kolommen die door de query wordt verwacht, ontbreekt mogelijk in de doel tabel of vice versa.

De aanbeveling schema probleem oplossen wordt weer gegeven wanneer Azure SQL Database een afwijking van het aantal aan schema gerelateerde SQL-fouten in de data base ondervindt. De volgende tabel bevat de fouten die betrekking hebben op schema problemen:

| SQL-fout code | Bericht |
| --- | --- |
| 201 |De procedure of functie *verwacht para meter*, maar deze is niet opgegeven. |
| 207 |Ongeldige kolom naam *. |
| 208 |Ongeldige object naam ' * '. |
| 213 |Kolom naam of-nummer van opgegeven waarden komt niet overeen met tabel definitie. |
| 2812 |Kan opgeslagen procedure ' * ' niet vinden. |
| 8144 |Er zijn te veel argumenten opgegeven voor de procedure of functie *. |

## <a name="custom-applications"></a>Aangepaste toepassingen

Ontwikkel aars kunnen overwegen aangepaste toepassingen te ontwikkelen met behulp van prestatie aanbevelingen voor Azure SQL Database. Alle aanbevelingen die in de portal voor een Data Base worden vermeld, zijn toegankelijk via de [Get-AzSqlDatabaseRecommendedAction-](/powershell/module/az.sql/get-azsqldatabaserecommendedaction) API.

## <a name="next-steps"></a>Volgende stappen

- Zie [Azure SQL database Automatic tuning](automatic-tuning-overview.md)(Engelstalig) voor meer informatie over het automatisch afstemmen van database indexen en uitvoer plannen voor query's.
- Zie [Azure SQL intelligent Insights](intelligent-insights-overview.md)voor meer informatie over het automatisch bewaken van database prestaties met automatische diagnose en de oorzaak van prestatie problemen.
- Zie [query performance Insights](query-performance-insight-use.md) voor meer informatie over de invloed van de prestaties van uw meest voorkomende query's en weer geven.
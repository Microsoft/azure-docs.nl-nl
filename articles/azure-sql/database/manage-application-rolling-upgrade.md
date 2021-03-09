---
title: Rolling upgrades van toepassingen
description: Meer informatie over het gebruik van Azure SQL Database geo-replicatie voor het ondersteunen van roulerende upgrades van uw Cloud toepassing
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 02/13/2019
ms.openlocfilehash: b7d21852ad684782fa1cb917442fee236d3c882b
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102502141"
---
# <a name="manage-rolling-upgrades-of-cloud-applications-by-using-sql-database-active-geo-replication"></a>Rolling upgrades van Cloud toepassingen beheren door gebruik te maken van SQL Database actieve geo-replicatie
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Meer informatie over het gebruik van [actieve geo-replicatie](auto-failover-group-overview.md) in Azure SQL database om rolling upgrades van uw Cloud toepassing in te scha kelen. Omdat upgrades storend zijn, moeten ze deel uitmaken van uw planning en ontwerp van uw bedrijfs continuïteit. In dit artikel kijken we naar twee verschillende methoden om het upgrade proces te organiseren en de voor delen en afwegingen van elke optie te bespreken. Voor de doel einden van dit artikel verwijzen we naar een toepassing die bestaat uit een website die is verbonden met één Data Base als gegevenslaag. Het doel is om versie 1 (v1) van de toepassing te upgraden naar versie 2 (v2) zonder dat dit invloed heeft op de gebruikers ervaring.

Houd rekening met de volgende factoren bij het evalueren van upgrade opties:

* Gevolgen voor de beschik baarheid van toepassingen tijdens upgrades, zoals hoe lang toepassings functies kunnen worden beperkt of gedegradeerd.
* De mogelijkheid om terug te draaien als de upgrade mislukt.
* Beveiligings probleem van de toepassing als er sprake is van een niet-gerelateerde, fatale fout tijdens de upgrade.
* Totale dollar kosten. Deze factor bevat extra database redundantie en incrementele kosten van de tijdelijke onderdelen die worden gebruikt door het upgrade proces.

## <a name="upgrade-applications-that-rely-on-database-backups-for-disaster-recovery"></a>Toepassingen bijwerken die afhankelijk zijn van database back-ups voor herstel na nood gevallen

Als uw toepassing gebruikmaakt van automatische database back-ups en geo-herstel gebruikt voor herstel na nood gevallen, wordt deze geïmplementeerd in één Azure-regio. Als u de gebruikers onderbreking wilt minimaliseren, maakt u een faserings omgeving in die regio met alle toepassings onderdelen die bij de upgrade horen. Het eerste diagram illustreert de operationele omgeving vóór het upgrade proces. Het eind punt `contoso.azurewebsites.net` vertegenwoordigt een productie omgeving van de web-app. Als u de upgrade wilt herstellen, moet u een faserings omgeving met een volledig gesynchroniseerde kopie van de data base maken. Volg deze stappen voor het maken van een faserings omgeving voor de upgrade:

1. Maak een secundaire data base in dezelfde Azure-regio. Controleer de secundaire om te zien of het seeding-proces is voltooid (1).
2. Maak een nieuwe omgeving voor uw web-app en roep deze ' staging ' aan. Het wordt geregistreerd in Azure DNS met de URL `contoso-staging.azurewebsites.net` (2).

> [!NOTE]
> Deze voorbereidings stappen hebben geen invloed op de productie omgeving, die in de volledige toegangs modus kan functioneren.

![Diagram toont de SQL Database geo-replicatie Configuratie voor herstel na nood gevallen in de Cloud.](./media/manage-application-rolling-upgrade/option1-1.png)

Wanneer de voorbereidende stappen zijn voltooid, is de toepassing gereed voor de daad werkelijke upgrade. In het volgende diagram ziet u de stappen die bij het upgrade proces zijn betrokken:

1. Stel de primaire data base in op de modus alleen-lezen (3). In deze modus wordt gegarandeerd dat de productie omgeving van de web-app (v1) alleen-lezen blijft tijdens de upgrade, waardoor gegevens verschillen tussen de data base-exemplaren van v1 en v2 worden voor komen.
2. Verbreek de verbinding van de secundaire data base met behulp van de geplande beëindigings modus (4). Met deze actie maakt u een volledig gesynchroniseerde, onafhankelijke kopie van de primaire data base. Er wordt een upgrade voor deze data base uitgevoerd.
3. Schakel de secundaire data base in op de modus lezen/schrijven en voer het upgrade script uit (5).

![In het diagram wordt SQL Database geo-replicatie Configuratie weer gegeven voor herstel na nood gevallen voor de cloud die het upgrade script uitvoert.](./media/manage-application-rolling-upgrade/option1-2.png)

Als de upgrade is voltooid, bent u nu klaar om over te scha kelen naar de bijgewerkte versie van de toepassing, die een productie omgeving wordt. Wisseling omvat een aantal stappen, zoals wordt geïllustreerd in het volgende diagram:

1. Activeer een wissel bewerking tussen productie-en faserings omgevingen van de web-app (6). Met deze bewerking worden de Url's van de twee omgevingen omgeschakeld. `contoso.azurewebsites.net`Wijst nu naar versie v2 van de website en de data base (productie omgeving). 
2. Als u de V1-versie niet meer nodig hebt, die na het wisselen een staging-kopie werd geworden, kunt u de faserings omgeving (7) buiten gebruik stellen.

![SQL Database geo-replicatie Configuratie voor herstel na nood gevallen in de Cloud.](./media/manage-application-rolling-upgrade/option1-3.png)

Als het upgrade proces mislukt (bijvoorbeeld vanwege een fout in het upgrade script), moet u overwegen dat de faserings omgeving is aangetast. Als u de toepassing wilt terugdraaien naar de status van vóór de upgrade, herstelt u de toepassing in de productie omgeving naar volledige toegang. In het volgende diagram ziet u de stappen voor het opnieuw versie nummer:

1. Stel de database kopie in op lees-schrijf modus (8). Met deze actie wordt de volledige v1-functionaliteit van de productie kopie hersteld.
2. Voer de analyse van de hoofd oorzaak uit en maak de faserings omgeving buiten gebruik (9).

Op dit moment is de toepassing volledig functioneel en kunt u de upgrade stappen herhalen.

> [!NOTE]
> Voor het terugdraaien zijn geen DNS-wijzigingen vereist omdat u nog geen swap bewerking hebt uitgevoerd.

![In het diagram wordt SQL Database geo-replicatie Configuratie weer gegeven voor herstel na nood gevallen met de faserings omgeving buiten gebruik gesteld.](./media/manage-application-rolling-upgrade/option1-4.png)

Het belangrijkste voor deel van deze optie is dat u een toepassing in één regio kunt bijwerken door een aantal eenvoudige stappen te volgen. De kosten voor de dollar van de upgrade zijn relatief laag. 

Als er sprake is van een onherstelbare fout tijdens de upgrade, moet u de toepassing opnieuw implementeren in een andere regio en de data base vanuit een back-up terugzetten met behulp van geo-Restore. Dit proces resulteert in aanzienlijke downtime.

## <a name="upgrade-applications-that-rely-on-database-geo-replication-for-disaster-recovery"></a>Toepassingen bijwerken die gebruikmaken van geo-replicatie van de Data Base voor nood herstel

Als uw toepassing actieve groepen met geo-replicatie of automatische failover gebruikt voor bedrijfs continuïteit, wordt deze geïmplementeerd in ten minste twee verschillende regio's. Er is een actieve, primaire data base in een primaire regio en een secundaire data base met het kenmerk alleen-lezen in een back-upregio. Naast de factoren die aan het begin van dit artikel worden vermeld, moet het upgrade proces ook garanderen dat:

* De toepassing blijft beveiligd tegen onherstelbare storingen tijdens het upgrade proces.
* De geo-redundante onderdelen van de toepassing worden parallel geüpgraded met de actieve onderdelen.

Om deze doel stellingen te bereiken, kunt u, naast het gebruik van de Web Apps omgevingen, gebruikmaken van Azure Traffic Manager met behulp van een failover-profiel met één actief eind punt en één back-upeindpunt. In het volgende diagram ziet u de operationele omgeving voorafgaand aan het upgrade proces. De websites `contoso-1.azurewebsites.net` en `contoso-dr.azurewebsites.net` vertegenwoordigen een productie omgeving van de toepassing met volledige geografische redundantie. De productie omgeving omvat de volgende onderdelen:

* De productie omgeving van de web `contoso-1.azurewebsites.net` -app in de primaire regio (1)
* De primaire data base in de primaire regio (2)
* Een stand-by-exemplaar van de web-app in de back-upregio (3)
* De geo-gerepliceerde secundaire data base in de back-upregio (4)
* Een Traffic Manager prestatie profiel met een online eind punt met de naam `contoso-1.azurewebsites.net` en een offline-eind punt met de naam `contoso-dr.azurewebsites.net`

Om het mogelijk te maken om de upgrade te herstellen, moet u een faserings omgeving maken met een volledig gesynchroniseerde kopie van de toepassing. Omdat u er zeker van wilt zijn dat de toepassing snel kan worden hersteld wanneer er tijdens het upgrade proces een onherstelbare fout optreedt, moet de faserings omgeving ook geografisch redundant zijn. De volgende stappen zijn vereist voor het maken van een faserings omgeving voor de upgrade:

1. Implementeer een faserings omgeving van de web-app in de primaire regio (6).
2. Maak een secundaire data base in de primaire Azure-regio (7). Configureer de faserings omgeving van de web-app om er verbinding mee te maken. 
3. Maak een andere geo-redundante, secundaire data base in de back-upregio door de secundaire data base te repliceren in de primaire regio. (Deze methode wordt *aaneengeschakelde geo-replicatie* genoemd.) (8).
4. Implementeer een faserings omgeving van het web-app-exemplaar in de back-upregio (9) en configureer deze om verbinding te maken met de geo-redundante secundaire data base die is gemaakt om (8).

> [!NOTE]
> Deze voorbereidings stappen hebben geen invloed op de toepassing in de productie omgeving. Deze functie blijft volledig functioneel in de modus lezen-schrijven.

![Diagram toont SQL Database geo-replicatie Configuratie voor nood herstel in de Cloud met een volledig gesynchroniseerde kopie van de toepassing.](./media/manage-application-rolling-upgrade/option2-1.png)

Wanneer de voorbereidende stappen zijn voltooid, is de faserings omgeving gereed voor de upgrade. In het volgende diagram ziet u deze upgrade stappen:

1. Stel de primaire data base in de productie omgeving in op de modus alleen-lezen (10). In deze modus wordt gegarandeerd dat de productie database (v1) niet verandert tijdens de upgrade, waardoor de gegevens verschillen tussen de data base-exemplaren van v1 en v2 worden voor komen.

```sql
-- Set the production database to read-only mode
ALTER DATABASE <Prod_DB>
SET (ALLOW_CONNECTIONS = NO)
```

2. Beëindig geo-replicatie door de verbinding van de secundaire te verbreken (11). Met deze actie wordt een onafhankelijke, volledig gesynchroniseerde kopie van de productie database gemaakt. Er wordt een upgrade voor deze data base uitgevoerd. In het volgende voor beeld wordt Transact-SQL gebruikt, maar [Power shell](/powershell/module/az.sql/remove-azsqldatabasesecondary) is ook beschikbaar. 

```sql
-- Disconnect the secondary, terminating geo-replication
ALTER DATABASE <Prod_DB>
REMOVE SECONDARY ON SERVER <Partner-Server>
```

3. Voer het upgrade script uit `contoso-1-staging.azurewebsites.net` `contoso-dr-staging.azurewebsites.net` met, en de primaire Data Base voor fase ring (12). De wijzigingen in de Data Base worden automatisch gerepliceerd naar de fase ring secundair.

![In het diagram wordt SQL Database geo-replicatie Configuratie weer gegeven voor nood herstel in de Cloud met database wijzigingen die zijn gerepliceerd naar fase ring.](./media/manage-application-rolling-upgrade/option2-2.png)

Als de upgrade is voltooid, bent u nu klaar om te scha kelen tussen gebruikers en de versie v2 van de toepassing. In het volgende diagram ziet u de stappen die worden beschreven:

1. Activeer een wissel bewerking tussen productie-en faserings omgevingen van de web-app in de primaire regio (13) en in de back-upregio (14). V2 van de toepassing wordt nu een productie omgeving, met een redundante kopie in de back-upregio.
2. Als u de V1-toepassing (15 en 16) niet meer nodig hebt, kunt u de faserings omgeving buiten gebruik stellen.

![In het diagram wordt SQL Database geo-replicatie Configuratie weer gegeven voor herstel na nood gevallen met optionele buiten gebruik stellen van de faserings omgeving.](./media/manage-application-rolling-upgrade/option2-3.png)

Als het upgrade proces mislukt (bijvoorbeeld vanwege een fout in het upgrade script), moet u overwegen dat de faserings omgeving een inconsistente status heeft. Als u de toepassing wilt terugdraaien naar de status van vóór de upgrade, keert u terug naar het gebruik van v1 van de toepassing in de productie omgeving. De vereiste stappen worden in het volgende diagram weer gegeven:

1. Stel de primaire database kopie in de productie omgeving in op de modus lezen/schrijven (17). Met deze actie wordt de volledige v1-functionaliteit hersteld in de productie omgeving.
2. Voer de analyse van de hoofd oorzaak uit en herstel of verwijder de faserings omgeving (18 en 19).

Op dit moment is de toepassing volledig functioneel en kunt u de upgrade stappen herhalen.

> [!NOTE]
> Voor het terugdraaien zijn geen DNS-wijzigingen vereist omdat u geen swap bewerking hebt uitgevoerd.

![In het diagram wordt SQL Database geo-replicatie Configuratie weer gegeven voor herstel na nood geval met het upgrade proces teruggedraaid.](./media/manage-application-rolling-upgrade/option2-4.png)

Het belangrijkste voor deel van deze optie is dat u zowel de toepassing als de geo-redundante kopie tegelijkertijd kunt upgraden zonder in te leveren op uw bedrijfs continuïteit tijdens de upgrade.

De belangrijkste balans is dat hiervoor dubbele redundantie van elk toepassings onderdeel is vereist, waardoor de kosten voor de dollar hoger worden. Het omvat ook een complexere werk stroom.

## <a name="summary"></a>Samenvatting

De twee upgrade methoden die in het artikel worden beschreven, verschillen in complexiteits-en dollar kosten, maar ze zijn vooral gericht op het minimaliseren van de duur van de gebruiker tot alleen-lezen bewerkingen. Deze tijd wordt rechtstreeks gedefinieerd door de duur van het upgrade script. Het is niet afhankelijk van de grootte van de data base, de servicelaag die u hebt gekozen, de website configuratie of andere factoren die u niet eenvoudig kunt beheren. Alle voorbereidings stappen zijn losgekoppeld van de upgrade stappen en zijn niet van invloed op de productie toepassing. De efficiëntie van het upgrade script is een belang rijke factor waarmee de gebruikers ervaring tijdens upgrades wordt bepaald. De beste manier om deze ervaring te verbeteren, is de nadruk op uw inspanningen om het upgrade script zo efficiënt mogelijk te maken.

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg [Overzicht van bedrijfscontinuïteit](business-continuity-high-availability-disaster-recover-hadr-overview.md) voor een bedrijfscontinuïteitsoverzicht en bedrijfscontinuïteitsscenario's.
* Zie voor meer informatie over Azure SQL Database actieve geo-replicatie, [Lees bare secundaire data bases maken met behulp van actieve geo-replicatie](active-geo-replication-overview.md).
* Zie voor meer informatie over Azure SQL Database groepen voor automatische failover de [optie groepen voor automatische failover gebruiken om transparante en gecoördineerde failover van meerdere data bases mogelijk te maken](auto-failover-group-overview.md).
* Zie [faserings omgevingen instellen in azure app service](../../app-service/deploy-staging-slots.md)voor meer informatie over faserings omgevingen in azure app service.
* Zie [een azure Traffic Manager-profiel beheren](../../traffic-manager/traffic-manager-manage-profiles.md)voor meer informatie over Azure Traffic Manager-profielen.

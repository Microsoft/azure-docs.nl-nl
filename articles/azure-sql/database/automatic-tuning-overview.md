---
title: Overzicht van automatische afstemming
description: Azure SQL Database en Azure SQL Managed instance analyseren SQL-query's en past automatisch aan de werk belasting van de gebruiker aan.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 03/23/2021
ms.openlocfilehash: 6bd8d6001fcd3bfa487259aa219ff771f26a8a94
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "104951280"
---
# <a name="automatic-tuning-in-azure-sql-database-and-azure-sql-managed-instance"></a>Automatische afstemming in Azure SQL Database en Azure SQL Managed instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

De automatische afstemming van Azure SQL Managed instance biedt piek prestaties en stabiele workloads door continue prestatie afstemming op basis van AI en machine learning. Azure SQL Database

Automatisch afstemmen is een volledig beheerde intelligente prestatieservice die gebruikmaakt van ingebouwde intelligentie om continu query's te controleren die worden uitgevoerd voor een database en die automatisch de prestaties van deze query's verbetert. Dit wordt bereikt door de data base dynamisch aan te passen aan de veranderende werk belastingen en de aanbevelingen voor het afstemmen toe te passen. Automatische afstemming leert horizon taal van alle data bases op Azure tot en met AI, waardoor de afstemmings acties dynamisch worden verbeterd. Hoe meer data bases worden uitgevoerd met automatisch afstemmen op, hoe beter het werkt.

Azure SQL Database en het automatisch afstemmen van Azure SQL Managed instance kunnen een van de belangrijkste functies zijn die u kunt inschakelen om stabiele en piek uren voor de data base uit te voeren.

## <a name="what-can-automatic-tuning-do-for-you"></a>Wat kan automatisch afstemmen voor u doen

- Geautomatiseerde prestaties van data bases afstemmen
- Automatische verificatie van prestatie verbeteringen
- Automatische terugdraai actie en automatische correctie
- Afstemmings geschiedenis
- Afstemmings acties Transact-SQL (T-SQL)-scripts voor hand matige implementaties
- Bewaking van proactieve werk belasting prestaties
- Mogelijkheden voor uitschalen op honderd duizenden data bases
- Positieve impact op DevOps resources en de total cost of ownership

## <a name="safe-reliable-and-proven"></a>Veilig, betrouwbaar en bewezen

Afstemmings bewerkingen die worden toegepast op data bases in Azure SQL Database zijn volledig veilig voor de prestaties van uw meest intense workloads. Het systeem is zo ontworpen dat de werk belasting van de gebruiker niet wordt verstoord. Automatische afstemmings aanbevelingen worden alleen toegepast op het tijdstip van een laag gebruik. Het systeem kan ook tijdelijk automatisch afstemmen uitschakelen om de prestaties van de werk belasting te beveiligen. In dit geval wordt het bericht ' uitgeschakeld door het systeem ' weer gegeven in Azure Portal. Automatisch afstemmen met werk belastingen met de hoogste resource prioriteit.

Automatische afstemmings mechanismen zijn verouderd en zijn perfect in verschillende miljoen data bases die worden uitgevoerd op Azure. Automatische afstemmings bewerkingen die worden toegepast, worden automatisch gecontroleerd om te zorgen voor een positieve verbetering van de prestaties van de werk belasting. Aanbevelingen voor teruggedraaide-prestaties worden dynamisch gedetecteerd en hersteld. Tijdens de registratie van de afstemmings geschiedenis bestaat een duidelijke tracering van de aanpassings verbeteringen die zijn aangebracht aan elke data base in Azure SQL Database en Azure SQL Managed instance.

![Hoe werkt automatisch afstemmen?](./media/automatic-tuning-overview/how-does-automatic-tuning-work.png)

Azure SQL Database automatisch afstemmen deelt de kern logica met de SQL Server functie voor automatisch afstemmen in de data base-engine. Zie [SQL Server Automatic tuning](/sql/relational-databases/automatic-tuning/automatic-tuning)(Engelstalig) voor aanvullende technische informatie over het ingebouwde intelligentie-mechanisme.

Voor een overzicht van de werking van automatisch afstemmen en voor typische gebruiks scenario's raadpleegt u de Inge sloten video:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-automatic-tuning/player]

## <a name="enable-automatic-tuning"></a>Automatisch instellen inschakelen

- U [schakelt automatisch afstemmen in voor Azure SQL database in het Azure Portal](automatic-tuning-enable.md) of met behulp van de T-SQL-instructie [ALTER data base](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current&preserve-view=true) .
- U schakelt automatisch afstemmen in voor Azure SQL Managed instance met behulp van de T-SQL-instructie [ALTER data base](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-mi-current&preserve-view=true) .

## <a name="automatic-tuning-options"></a>Opties voor automatisch afstemmen

De opties voor automatisch afstemmen die beschikbaar zijn in Azure SQL Database en Azure SQL Managed instance zijn:

| Optie voor automatisch afstemmen | Ondersteuning voor één data base en gepoolde data base | Ondersteuning voor instance data base |
| :----------------------------- | ----- | ----- |
| **Create Index** : identificeert indexen die de prestaties van uw workload kunnen verbeteren, maakt indexen en controleert automatisch of de prestaties van query's zijn verbeterd. | Ja | Nee |
| **Drop Index** -wordt ongebruikt (in de afgelopen 90 dagen) en dubbele indexen. Unieke indexen, inclusief indexen die primaire sleutel en unieke beperkingen ondersteunen, worden nooit verwijderd. Deze optie kan automatisch worden uitgeschakeld wanneer query's met index hints aanwezig zijn in de werk belasting of wanneer de werk belasting partitie switching uitvoert. In Premium-en Bedrijfskritiek-service lagen verwijdert deze optie niet-gebruikte indexen, maar worden dubbele indexen verwijderd, indien van toepassing. | Ja | Nee |
| **Laatste goede planning afdwingen** (automatische plan correctie): identificeert Azure SQL-query's met behulp van een uitvoerings plan dat lager is dan het vorige goede plan en query's met behulp van het laatst bekende goede plan in plaats van het teruggedraaide-abonnement. | Ja | Ja |

### <a name="automatic-tuning-for-sql-database"></a>SQL Database automatisch afstemmen

Automatisch afstemmen voor Azure SQL Database maakt gebruik van de **Create Index**, **Drop Index** en het **afdwingen van de laatste goede plan** database Advisor-aanbevelingen om uw database prestaties te optimaliseren. Zie [aanbevelingen voor data base Advisor in de Azure Portal](database-advisor-find-recommendations-portal.md), in [Power shell](/powershell/module/az.sql/get-azsqldatabaserecommendedaction)en in de [rest API](/rest/api/sql/serverautomatictuning)voor meer informatie.

U kunt de aanbevelingen voor het afstemmen hand matig Toep assen met behulp van de Azure Portal of u kunt automatisch afstemmen op zichzelf laten afstemmen voor u. De voor delen van het autonoom Toep assen van het systeem om automatisch afstemmings aanbevelingen voor u toe te passen, is dat er een positieve toename van de prestaties van de werk belasting bestaat en als er geen aanzienlijke verbetering van de prestaties wordt gedetecteerd, wordt de aanbeveling voor het afstemmen automatisch hersteld. In het geval van query's die worden beïnvloed door het afstemmen van aanbevelingen die niet vaak worden uitgevoerd, kan de validatie fase tot 72 uur duren.

Als u aanbevelingen voor het afstemmen toepast via T-SQL, zijn de mechanismen voor automatische prestatie validatie en omkering niet beschikbaar. Aanbevelingen die op een dergelijke manier worden toegepast, blijven actief en worden weer gegeven in de lijst met aanbevelingen voor 24-48 uur. voordat het systeem ze automatisch intrekt. Als u een aanbeveling eerder wilt verwijderen, kunt u deze uit Azure Portal verwijderen.

Opties voor automatisch afstemmen kunnen afzonderlijk worden in-of uitgeschakeld per data base, maar ze kunnen worden geconfigureerd op server niveau en toegepast op elke Data Base die instellingen overneemt van de server. Servers kunnen Azure-standaard waarden overnemen voor instellingen voor automatisch afstemmen. De standaard instellingen van Azure op dit moment worden ingesteld op FORCE_LAST_GOOD_PLAN is ingeschakeld, CREATE_INDEX is ingeschakeld en DROP_INDEX is uitgeschakeld.

> [!IMPORTANT]
> Vanaf maart worden 2020 wijzigingen in de standaard instellingen van Azure voor automatische afstemming als volgt toegepast:
>
> - Nieuwe standaard waarden van Azure worden FORCE_LAST_GOOD_PLAN = ingeschakeld, CREATE_INDEX = uitgeschakeld en DROP_INDEX = uitgeschakeld.
> - Bestaande servers zonder automatisch afstemmen geconfigureerd voor keuren worden automatisch geconfigureerd om de nieuwe standaard waarden van Azure te overnemen. Dit geldt voor alle klanten die momenteel server instellingen voor automatisch afstemmen in een niet-gedefinieerde status hebben.
> - Nieuwe servers die worden gemaakt, worden automatisch geconfigureerd voor het overnemen van de nieuwe standaard waarden van Azure (in tegens telling tot eerdere versies van de configuratie voor automatisch afstemmen bij het maken van een nieuwe server).

Het configureren van opties voor automatisch afstemmen op een server en het overnemen van instellingen voor data bases die deel uitmaken van de bovenliggende server is een aanbevolen methode voor het configureren van automatische afstemming, omdat het beheer van automatische afstemmings opties voor een groot aantal data bases vereenvoudigt.

Zie [e-mail meldingen voor automatisch afstemmen](automatic-tuning-email-notifications-configure.md)voor meer informatie over het bouwen van e-mail meldingen voor aanbevelingen voor automatisch afstemmen.

### <a name="automatic-tuning-for-azure-sql-managed-instance"></a>Automatisch afstemmen voor Azure SQL Managed instance

Automatisch afstemmen voor SQL Managed instance ondersteunt alleen **GEFORCEERD plan forceren**. Zie voor meer informatie over het configureren van opties voor automatisch afstemmen via T-SQL [automatisch afstemmen introduceert automatisch corrigeren van plannen](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) en [automatische correctie van plannen](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction).

## <a name="next-steps"></a>Volgende stappen

- Zie [kunst matige intelligentie](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/)-afstemming Azure SQL database voor meer informatie over de ingebouwde intelligentie die wordt gebruikt voor automatisch afstemmen.
- Zie voor meer informatie over hoe automatische afstemming werkt onder de motorkap [automatisch de indexering van miljoenen data bases in Microsoft Azure SQL database](https://www.microsoft.com/research/uploads/prod/2019/02/autoindexing_azuredb.pdf).
---
title: Voor beeld van multi tenant-app-Wingtip SaaS
description: Meer informatie over het gebruik van een voor beeld van een multi tenant-toepassing die gebruikmaakt van Azure SQL Database, het Wingtip SaaS-voor beeld
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/24/2018
ms.openlocfilehash: 09651ed5c7472c12d82108fdbd450cca6e84349f
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96447084"
---
# <a name="introduction-to-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-azure-sql-database"></a>Inleiding tot een multi tenant SaaS-app die gebruikmaakt van het data base-per-Tenant patroon met Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

De Wingtip SaaS-toepassing is een voor beeld van een multi tenant-app. De app maakt gebruik van het SaaS-toepassings patroon data base-per-Tenant om meerdere tenants te onderhouden. De app biedt een overzicht van de functies van Azure SQL Database waarmee SaaS-scenario's kunnen worden ingeschakeld met behulp van verschillende SaaS-ontwerp-en beheer patronen. Om snel aan de slag te gaan, implementeert de Wingtip SaaS-app in minder dan vijf minuten.

Broncode-en beheer scripts van de toepassings bron zijn beschikbaar in de [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) github opslag plaats. Voordat u begint, raadpleegt u de [algemene richt lijnen](saas-tenancy-wingtip-app-guidance-tips.md) voor de stappen voor het downloaden en deblokkeren van de Wingtip tickets-beheer scripts.

## <a name="application-architecture"></a>Toepassingsarchitectuur

De Wingtip SaaS-app maakt gebruik van het model data base-per-Tenant. Het maakt gebruik van elastische SQL-Pools om de efficiëntie te maximaliseren. Voor het inrichten en toewijzen van tenants aan hun gegevens wordt een catalogus database gebruikt. De kern Wingtip SaaS-toepassing maakt gebruik van een pool met drie voor beelden van tenants, plus de catalogus database. De catalogus-en Tenant servers zijn ingericht met DNS-aliassen. Deze aliassen worden gebruikt voor het onderhouden van een verwijzing naar de actieve resources die worden gebruikt door de Wingtip-toepassing. Deze aliassen worden bijgewerkt naar herstel bronnen in de zelf studies voor herstel na nood gevallen. Het volt ooien van veel van de Wingtip SaaS-zelf studies resulteert in invoeg toepassingen voor de eerste implementatie. Invoeg toepassingen, zoals analytische data bases en schema's voor het beheer van meerdere data bases, worden geïntroduceerd.


![Wingtip SaaS-architectuur](./media/saas-dbpertenant-wingtip-app-overview/app-architecture.png)


Als u de zelf studies doorloopt en met de app werkt, kunt u zich richten op de SaaS-patronen die betrekking hebben op de gegevenslaag. Met andere woorden, focus op de gegevenslaag en de app zelf niet te deanalyseren. Meer informatie over de implementatie van deze SaaS-patronen is essentieel voor het implementeren van deze patronen in uw toepassingen. Houd ook rekening met de nodige wijzigingen voor uw specifieke bedrijfs vereisten.

## <a name="sql-database-wingtip-saas-tutorials"></a>SQL Database Wingtip SaaS-zelf studies

Nadat u de app hebt geïmplementeerd, moet u de volgende zelf studies voor het maken van de eerste implementatie verkennen. Deze zelf studies verkennen algemene SaaS-patronen die gebruikmaken van ingebouwde functies van SQL Database, Azure Synapse Analytics en andere Azure-Services. Zelf studies bevatten Power shell-scripts met gedetailleerde uitleg. De uitleg vereenvoudigt de inzichten en implementatie van dezelfde SaaS-beheer patronen in uw toepassingen.


| Zelfstudie | Beschrijving |
|:--|:--|
| [Richt lijnen en tips voor het SQL Database voor beeld van multi tenant SaaS-apps](saas-tenancy-wingtip-app-guidance-tips.md) | Down load en voer Power shell-scripts uit om delen van de toepassing voor te bereiden. |
|[De Wingtip SaaS-toepassing implementeren en verkennen](./saas-dbpertenant-get-started-deploy.md)|  Implementeer en verken de Wingtip SaaS-toepassing met uw Azure-abonnement. |
|[Tenants inrichten en catalogiseren](./saas-dbpertenant-provision-and-catalog.md)| Meer informatie over hoe de toepassing verbinding maakt met tenants met behulp van een catalogus database en hoe de catalogus tenants aan hun gegevens toewijst. |
|[Prestaties bewaken en beheren](./saas-dbpertenant-performance-monitoring.md)| Meer informatie over het gebruik van controle functies van SQL Database en het instellen van waarschuwingen wanneer prestatie drempels worden overschreden. |
|[Bewaken met Azure Monitor-logboeken](./saas-dbpertenant-log-analytics.md) | Meer informatie over het gebruik van [Azure monitor logboeken](../../azure-monitor/log-query/log-query-overview.md) voor het bewaken van grote hoeveel heden resources in meerdere groepen. |
|[Een individuele tenant terugzetten](./saas-dbpertenant-restore-single-tenant.md)| Meer informatie over hoe u een Tenant database herstelt naar een eerder tijdstip. Meer informatie over het herstellen naar een parallelle data base, waardoor de bestaande Tenant database online blijft. |
|[Tenant database schema beheren](saas-tenancy-schema-management.md)| Meer informatie over het bijwerken van schema en het bijwerken van referentie gegevens in alle Tenant databases. |
|[Met cross-Tenant gedistribueerde query's uitvoeren](saas-tenancy-cross-tenant-reporting.md) | Maak een ad hoc Analytics-Data Base en voer realtime gedistribueerde query's uit op alle tenants.  |
|[Analyses uitvoeren op geëxtraheerde Tenant gegevens](saas-tenancy-tenant-analytics.md) | Tenant gegevens uitpakken naar een Analytics-Data Base of Data Warehouse voor offline analyse query's. |


## <a name="next-steps"></a>Volgende stappen

- [Algemene richt lijnen en tips voor het implementeren en gebruiken van het voor beeld van de SaaS-app Wingtip tickets](saas-tenancy-wingtip-app-guidance-tips.md)
- [De Wingtip SaaS-toepassing implementeren](./saas-dbpertenant-get-started-deploy.md)
---
title: Server concepten-Azure Database for MySQL flexibele server
description: In dit onderwerp vindt u overwegingen en richt lijnen voor het werken met Azure Database for MySQL flexibele server
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: b664dd406a1ab90b4ea5e85005a69935f345c609
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102034656"
---
# <a name="server-concepts-in-azure-database-for-mysql-flexible-server-preview"></a>Server concepten in Azure Database for MySQL flexibele server (preview-versie)

> [!IMPORTANT] 
> Azure Database for MySQL - Flexible Server is momenteel beschikbaar als openbare preview.

In dit artikel vindt u overwegingen en richt lijnen voor het werken met Azure Database for MySQL flexibele servers.

## <a name="what-is-an-azure-database-for-mysql-flexible-server"></a>Wat is een Azure Database for MySQL flexibele server?

Azure Database for MySQL flexibele server is een volledig beheerde database service die de Community-versie van MySQL uitvoert. Over het algemeen is de service ontworpen voor flexibiliteit en configuratie aanpassingen op basis van de gebruikers vereisten. Het is dezelfde MySQL-server constructie die u mogelijk kent in de on-premises wereld. De flexibele server wordt met name beheerd, biedt de beste prestaties, een betere server beheer baarheid en controle, en geeft toegang en functies op server niveau.

Een Azure Database for MySQL flexibele server:

- Wordt gemaakt binnen een Azure-abonnement.
- Is de bovenliggende resource voor data bases.
- Hiermee kan MySQL-configuratie worden weer gegeven via server parameters (koppeling naar server parameter concepten).
- Voert automatische back-ups uit en ondersteunt herstel naar een bepaald tijdstip.
- Biedt een naam ruimte voor data bases.
- Is een container met sterke levens duur semantiek: een server verwijderen en de Inge sloten data bases verwijdert.
- Groepeert bronnen in een regio.
- Ondersteuning voor de door de klant verschafte onderhouds planning voor servers
- Mogelijkheid om flexibele servers te implementeren in een zone-redundante installatie voor verbeterde hoge Beschik baarheid
- Biedt een virtuele netwerk integratie voor de toegang tot de database server
- Biedt een manier om kosten te besparen door de flexibele server te onderbreken wanneer deze niet wordt gebruikt
- Voorziet in het bereik voor beheer beleid dat van toepassing is op de bijbehorende data bases: aanmelden, firewall, gebruikers, rollen, configuraties, enzovoort.
- Ondersteunt primaire versie MySQL 5,7 en MySQL 8,0. Zie [ondersteunde Azure database for MySQL Engine-versies](./../concepts-supported-versions.md)voor meer informatie.

Binnen een Azure Database for MySQL flexibele server kunt u een of meer data bases maken. U kunt ervoor kiezen om één data base per server te maken voor het gebruik van alle resources of om meerdere data bases te maken om de resources te delen. De prijzen zijn gestructureerd per server, op basis van de configuratie van Compute-laag, vCores en opslag (GB). Zie [Compute and Storage](./concepts-compute-storage.md)voor meer informatie.

## <a name="stopstart-an-azure-database-for-mysql-flexible-server"></a>Een Azure Database for MySQL flexibele server stoppen/starten

Azure Database for MySQL flexibele server biedt u de mogelijkheid om de server te **stoppen** wanneer deze niet wordt gebruikt en de server **te starten** wanneer u de activiteit hervat. Dit is in feite van toepassing op het besparen van kosten op de database servers en betaalt alleen voor de resource wanneer deze wordt gebruikt. Dit wordt nog belang rijker voor werk belastingen voor dev-test en wanneer u alleen de-server gebruikt voor een deel van de dag. Wanneer u de server stopt, worden alle actieve verbindingen verwijderd. Wanneer u de server later weer online wilt zetten, kunt u de [Azure Portal](how-to-stop-start-server-portal.md) of CLI gebruiken.

Wanneer de server zich in de status **gestopt** bevindt, wordt de compute van de server niet gefactureerd. Opslag blijft echter gefactureerd omdat de opslag ruimte van de server bewaard blijft om ervoor te zorgen dat gegevens bestanden beschikbaar zijn wanneer de server weer wordt gestart.

> [!IMPORTANT]
> Wanneer u de server **stopt** , blijft deze voor de volgende zeven dagen in een stretch-status. Als u deze niet hand matig **Start** , wordt de server aan het eind van 7 dagen automatisch gestart. U kunt ervoor kiezen om deze opnieuw te **stoppen** als u de-server niet gebruikt.

Tijdens de tijd server wordt gestopt, kunnen er geen beheer bewerkingen worden uitgevoerd op de server. Als u de configuratie-instellingen op de server wilt wijzigen, moet u [de server starten](how-to-stop-start-server-portal.md). Raadpleeg de [beperkingen voor stoppen/starten](./concepts-limitations.md#stopstart-operation).

## <a name="how-do-i-manage-a-server"></a>Hoe kan ik een server beheren?

U kunt Azure Database for MySQL flexibele server beheren door gebruik te maken van de [Azure Portal](./quickstart-create-server-portal.md) of de [Azure cli](./quickstart-create-server-cli.md).

## <a name="next-steps"></a>Volgende stappen

-   Meer informatie over het maken van een [Server](./quickstart-create-server-portal.md)
-   Meer informatie over [controle en waarschuwingen](./how-to-alert-on-metric.md)


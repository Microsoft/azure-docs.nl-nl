---
title: Upgrade van primaire versie-Azure Portal-Azure Database for MySQL-één server
description: In dit artikel wordt beschreven hoe u een upgrade kunt uitvoeren voor de primaire versie van Azure Database for MySQL-één server met behulp van Azure Portal
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 11/16/2020
ms.openlocfilehash: 080d7c09b180d5943216793119718eb6a2add79e
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95753048"
---
# <a name="major-version-upgrade-in-azure-database-for-mysql-single-server-using-the-azure-portal"></a>Upgrade van de primaire versie in Azure Database for MySQL één server met behulp van de Azure Portal

> [!IMPORTANT]
> De upgrade van de primaire versie van Azure Data Base voor MySQL met één server is in open bare preview.

In dit artikel wordt beschreven hoe u uw primaire MySQL-versie in-place kunt bijwerken in Azure Database for MySQL één server.

Met deze functie kunnen klanten in-place Upgrades uitvoeren van hun MySQL 5,6-servers naar MySQL 5,7 met een klik op de knop zonder dat er gegevens worden verplaatst of dat een toepassing connection string wijzigingen kan aanbrengen.

> [!Note]
> * Upgrade van primaire versie is alleen beschikbaar voor de upgrade van de primaire versie van MySQL 5,6 naar MySQL 5,7<br>
> * De upgrade van de primaire versie wordt nog niet ondersteund op de replica server.
> * De server is niet beschikbaar tijdens de upgrade bewerking. Het wordt daarom aangeraden upgrades uit te voeren tijdens het geplande onderhouds venster.

## <a name="prerequisites"></a>Vereisten
U hebt het volgende nodig om deze hand leiding te volt ooien:
- Een [Azure database for MySQL enkele server](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57"></a>Voer een upgrade uit van MySQL 5,6 naar MySQL 5,7 van de primaire versie

Volg deze stappen voor het uitvoeren van een primaire versie-upgrade voor uw Azure-data base van MySQL 5,6 server

> [!IMPORTANT]
> We raden u aan om eerst een upgrade uit te voeren op de herstelde kopie van de server in plaats van de productie rechtstreeks te upgraden. Zie [herstel naar een bepaald tijdstip uitvoeren](howto-restore-server-portal.md#point-in-time-restore). 

1. Selecteer uw bestaande Azure Database for MySQL 5,6-server in het [Azure Portal](https://portal.azure.com/).

2. Klik op de pagina **overzicht** op de knop **bijwerken** op de werk balk.

3. Selecteer in de sectie **bijwerken** de optie **OK** om Azure data base for MySQL 5,6-server te upgraden naar 5,7 server.

    :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Azure Database for MySQL-overzicht-upgrade":::

4. Er wordt een melding bevestigd dat de upgrade is geslaagd.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

**1. Wanneer wordt de upgrade functie van MySQL v 5.6 in onze productie omgeving die we moeten bijwerken?**

Het GA van deze functie is gepland voor MySQL v 5.6 buiten gebruik gesteld. De functie is echter gereed voor productie en volledig wordt ondersteund door Azure, zodat u deze kunt uitvoeren met vertrouwen in uw omgeving. Als aanbevolen best practice raden wij u ten zeerste aan om dit eerst uit te voeren en te testen op een herstelde kopie van de server, zodat u de downtime kunt schatten tijdens de upgrade en de compatibiliteits test voor toepassingen moet uitvoeren voordat u deze uitvoert op productie. Zie [How to time Restore uitvoeren](howto-restore-server-portal.md#point-in-time-restore) voor het maken van een Point-in-time kopie van uw server voor meer informatie. 

**2. heeft dit tot uitval tijd van de server en zo ja, hoe lang?**

Ja, de server is tijdens het upgrade proces niet beschikbaar. u wordt aangeraden deze bewerking uit te voeren tijdens het geplande onderhouds venster. De geschatte uitval tijd is afhankelijk van de grootte van de data base, de opslag grootte (IOPs ingericht) en het aantal tabellen in de data base. De upgrade tijd is direct evenredig met het aantal tabellen op de server. De upgrades van de Basic SKU-servers worden naar verwachting meer tijd in beslag nemen op het standaard-opslag platform. Als u de downtime voor uw server omgeving wilt schatten, raden we u aan om eerst een upgrade uit te voeren op de herstelde kopie van de server.  

**3. er wordt opgemerkt dat deze nog niet wordt ondersteund op de replica server. Wat betekent dat concreet?**

Op dit moment wordt de upgrade van de primaire versie niet ondersteund voor de replica server. Dit betekent dat u deze niet hoeft uit te voeren voor servers die betrokken zijn bij de replicatie (bron-of replica server). Als u de upgrade van de servers die betrokken zijn bij de replicatie wilt testen voordat we de replica-ondersteuning voor de upgrade functie toevoegen, kunt u het beste de volgende stappen uitvoeren:

1. Tijdens uw geplande onderhoud [stopt u replicatie en verwijdert u de replica server](howto-read-replicas-portal.md) na het vastleggen van de naam en alle configuratie gegevens (Firewall instellingen, configuratie van de server parameter als deze verschilt van de bron server).
2. Voer een upgrade uit van de bron server.
3. Richt een nieuwe Lees replica-server in met dezelfde naam en configuratie-instellingen die u in stap 1 hebt vastgelegd. De nieuwe replica server bevindt zich automatisch op v 5.7 nadat de bron server is bijgewerkt naar v 5.7.

**4. Wat gebeurt er als we niet vóór 5 februari 2021 een upgrade van onze MySQL-server zullen uitvoeren.**

U kunt uw MySQL v 5.6-server net zo eerder blijven uitvoeren. Azure voert **geen** geforceerde upgrade op de server uit. De beperkingen die worden beschreven in [Azure database for MySQL-versie beleid](concepts-version-policy.md) zijn echter van toepassing.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Azure database for MySQL-versie beleid](concepts-version-policy.md).

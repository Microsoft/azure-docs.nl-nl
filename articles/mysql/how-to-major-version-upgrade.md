---
title: Upgrade van primaire versie in Azure Database for MySQL-één server
description: In dit artikel wordt beschreven hoe u een upgrade kunt uitvoeren voor de primaire versie van Azure Database for MySQL-één server
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 1/13/2021
ms.openlocfilehash: b62f4ebc61ac27478788d8b2bae5e4145f87ac8b
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98630197"
---
# <a name="major-version-upgrade-in-azure-database-for-mysql-single-server"></a>Upgrade van primaire versie in Azure Database for MySQL één server

> [!IMPORTANT]
> De upgrade van de primaire versie van Azure Data Base voor MySQL met één server is in open bare preview.

In dit artikel wordt beschreven hoe u uw primaire MySQL-versie in-place kunt bijwerken in Azure Database for MySQL één server.

Met deze functie kunnen klanten in-place Upgrades uitvoeren van hun MySQL 5,6-servers naar MySQL 5,7 met een klik op de knop zonder dat er gegevens worden verplaatst of dat een toepassing connection string wijzigingen kan aanbrengen.

> [!Note]
> * Upgrade van primaire versie is alleen beschikbaar voor de upgrade van de primaire versie van MySQL 5,6 naar MySQL 5,7<br>
> * De upgrade van de primaire versie wordt nog niet ondersteund op de replica server.
> * De server is niet beschikbaar tijdens de upgrade bewerking. Het wordt daarom aangeraden upgrades uit te voeren tijdens het geplande onderhouds venster.

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-portal"></a>Een upgrade uitvoeren van de primaire versie van MySQL 5,6 naar MySQL 5,7 met Azure Portal

Volg deze stappen voor het uitvoeren van een primaire versie-upgrade voor uw Azure-data base van MySQL 5,6-server met Azure Portal

> [!IMPORTANT]
> We raden u aan om eerst een upgrade uit te voeren op de herstelde kopie van de server in plaats van de productie rechtstreeks te upgraden. Zie [herstel naar een bepaald tijdstip uitvoeren](howto-restore-server-portal.md#point-in-time-restore).

1. Selecteer uw bestaande Azure Database for MySQL 5,6-server in het [Azure Portal](https://portal.azure.com/).

2. Klik op de pagina **overzicht** op de knop **bijwerken** op de werk balk.

3. Selecteer in de sectie **bijwerken** de optie **OK** om Azure data base for MySQL 5,6-server te upgraden naar 5,7 server.

   :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Azure Database for MySQL-overzicht-upgrade":::

4. Er wordt een melding bevestigd dat de upgrade is geslaagd.


## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-cli"></a>Een upgrade uitvoeren van de primaire versie van MySQL 5,6 naar MySQL 5,7 met behulp van Azure CLI

Volg deze stappen voor het uitvoeren van een primaire versie-upgrade voor uw Azure-data base van MySQL 5,6-server met behulp van Azure CLI

> [!IMPORTANT]
> We raden u aan om eerst een upgrade uit te voeren op de herstelde kopie van de server in plaats van de productie rechtstreeks te upgraden. Zie [herstel naar een bepaald tijdstip uitvoeren](howto-restore-server-cli.md#server-point-in-time-restore).

1. Installeer [Azure CLI voor Windows](/cli/azure/install-azure-cli) of gebruik Azure cli in [Azure Cloud shell](../cloud-shell/overview.md) om de upgrade-opdrachten uit te voeren. 
 
   Voor deze upgrade is versie 2.16.0 of hoger van de Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd. Voer az version uit om de geïnstalleerde versie en afhankelijke bibliotheken te vinden. Voer az upgrade uit om te upgraden naar de nieuwste versie.

2. Nadat u zich hebt aangemeld, voert u de opdracht [AZ mysql server upgrade](https://docs.microsoft.com/cli/azure/mysql/server?view=azure-cli-latest#az_mysql_server_upgrade&preserve-view=true) uit:
    
   ```azurecli
   az mysql server upgrade --name testsvr --resource-group testgroup --subscription MySubscription --target-server-version 5.7"
   ```
   
   In de opdracht prompt wordt het bericht '-running ' weer gegeven. Nadat dit bericht niet meer wordt weer gegeven, is de versie-upgrade voltooid.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="when-will-this-upgrade-feature-be-ga-as-we-have-mysql-v56-in-our-production-environment-that-we-need-to-upgrade"></a>Wanneer wordt in onze productie omgeving MySQL v 5.6 uitgevoerd, wanneer we een upgrade voor de upgrade functie uitvoeren?

Het GA van deze functie is gepland voor MySQL v 5.6 buiten gebruik gesteld. De functie is echter gereed voor productie en volledig wordt ondersteund door Azure, zodat u deze kunt uitvoeren met vertrouwen in uw omgeving. Als aanbevolen best practice raden wij u ten zeerste aan om dit eerst uit te voeren en te testen op een herstelde kopie van de server, zodat u de downtime kunt schatten tijdens de upgrade en de compatibiliteits test voor toepassingen moet uitvoeren voordat u deze uitvoert op productie. Zie [How to time Restore uitvoeren](howto-restore-server-portal.md#point-in-time-restore) voor het maken van een Point-in-time kopie van uw server voor meer informatie. 

### <a name="will-this-cause-downtime-of-the-server-and-if-so-how-long"></a>Leidt dit tot uitval tijd van de server en zo ja, hoe lang?

Ja, de server is tijdens het upgrade proces niet beschikbaar. u wordt aangeraden deze bewerking uit te voeren tijdens het geplande onderhouds venster. De geschatte uitval tijd is afhankelijk van de grootte van de data base, de opslag grootte (IOPs ingericht) en het aantal tabellen in de data base. De upgrade tijd is direct evenredig met het aantal tabellen op de server. De upgrades van de Basic SKU-servers worden naar verwachting meer tijd in beslag nemen op het standaard-opslag platform. Als u de downtime voor uw server omgeving wilt schatten, raden we u aan om eerst een upgrade uit te voeren op de herstelde kopie van de server.  

### <a name="it-is-noted-that-it-is-not-supported-on-replica-server-yet-what-does-that-mean-concrete"></a>Het wordt opgemerkt dat deze nog niet wordt ondersteund op de replica server. Wat betekent dat concreet?

Op dit moment wordt de upgrade van de primaire versie niet ondersteund voor de replica server. Dit betekent dat u deze niet hoeft uit te voeren voor servers die betrokken zijn bij de replicatie (bron-of replica server). Als u de upgrade van de servers die betrokken zijn bij de replicatie wilt testen voordat we de replica-ondersteuning voor de upgrade functie toevoegen, kunt u het beste de volgende stappen uitvoeren:

1. Tijdens uw geplande onderhoud [stopt u replicatie en verwijdert u de replica server](howto-read-replicas-portal.md) na het vastleggen van de naam en alle configuratie gegevens (Firewall instellingen, configuratie van de server parameter als deze verschilt van de bron server).
2. Voer een upgrade uit van de bron server.
3. Richt een nieuwe Lees replica-server in met dezelfde naam en configuratie-instellingen die u in stap 1 hebt vastgelegd. De nieuwe replica server bevindt zich automatisch op v 5.7 nadat de bron server is bijgewerkt naar v 5.7.

### <a name="what-will-happen-if-we-do-not-choose-to-upgrade-our-mysql-v56-server-before-february-5-2021"></a>Wat gebeurt er als we niet vóór 5 februari 2021 een upgrade van onze MySQL-server zullen uitvoeren?

U kunt uw MySQL v 5.6-server net zo eerder blijven uitvoeren. Azure voert **geen** geforceerde upgrade op de server uit. De beperkingen die worden beschreven in [Azure database for MySQL-versie beleid](concepts-version-policy.md) zijn echter van toepassing.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Azure database for MySQL-versie beleid](concepts-version-policy.md).

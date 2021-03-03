---
title: Upgrade van primaire versie in Azure Database for MySQL-één server
description: In dit artikel wordt beschreven hoe u een upgrade kunt uitvoeren voor de primaire versie van Azure Database for MySQL-één server
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: how-to
ms.date: 1/28/2021
ms.openlocfilehash: 471ccd6176bd8821ce7e40fde6d961bd9bcf7f0c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101702144"
---
# <a name="major-version-upgrade-in-azure-database-for-mysql-single-server"></a>Upgrade van primaire versie in Azure Database for MySQL één server

> [!NOTE]
> Dit artikel bevat verwijzingen naar de term _Slave_, een term die door micro soft niet meer wordt gebruikt. Wanneer de periode van de software wordt verwijderd, worden deze uit dit artikel verwijderd.
>

> [!IMPORTANT]
> De upgrade van de primaire versie van Azure Data Base voor MySQL met één server is in open bare preview.

In dit artikel wordt beschreven hoe u uw primaire MySQL-versie in-place kunt bijwerken in Azure Database for MySQL één server.

Met deze functie kunnen klanten in-place Upgrades uitvoeren van hun MySQL 5,6-servers naar MySQL 5,7 met een klik op de knop zonder dat er gegevens worden verplaatst of dat een toepassing connection string wijzigingen kan aanbrengen.

> [!Note]
> * De upgrade van de primaire versie is alleen beschikbaar voor de upgrade van de primaire versie van MySQL 5,6 naar MySQL 5,7.
> * De server is niet beschikbaar tijdens de upgrade bewerking. Het wordt daarom aangeraden upgrades uit te voeren tijdens het geplande onderhouds venster. U kunt overwegen [minimale downtime van de primaire versie van MySQL 5,6 naar mysql 5,7 met lees replica te gebruiken.](#perform-minimal-downtime-major-version-upgrade-from-mysql-56-to-mysql-57-using-read-replicas)

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

2. Nadat u zich hebt aangemeld, voert u de opdracht [AZ mysql server upgrade](/cli/azure/mysql/server?preserve-view=true&view=azure-cli-latest#az_mysql_server_upgrade) uit:

   ```azurecli
   az mysql server upgrade --name testsvr --resource-group testgroup --subscription MySubscription --target-server-version 5.7"
   ```
   
   In de opdracht prompt wordt het bericht '-running ' weer gegeven. Nadat dit bericht niet meer wordt weer gegeven, is de versie-upgrade voltooid.

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-on-read-replica-using-azure-portal"></a>Upgrade van de primaire versie uitvoeren van MySQL 5,6 naar MySQL 5,7 op een lees replica met Azure Portal

1. Selecteer in de [Azure Portal](https://portal.azure.com/)uw bestaande Azure database for MySQL 5,6-Lees replica-server.

2. Klik op de pagina **overzicht** op de knop **bijwerken** op de werk balk.

3. Selecteer in de sectie **bijwerken** de optie **OK** om de upgrade van Azure data base for MySQL 5,6 van replica-server naar 5,7 server te upgraden.

   :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Azure Database for MySQL-overzicht-upgrade":::

4. Er wordt een melding bevestigd dat de upgrade is geslaagd.

5. Controleer op de pagina **overzicht** of de versie van de Azure data base for MySQL replica-server is 5,7.

6. Ga nu naar de primaire server en [Voer de primaire versie-upgrade](#perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-portal) uit.

## <a name="perform-minimal-downtime-major-version-upgrade-from-mysql-56-to-mysql-57-using-read-replicas"></a>Minimale downtime van de primaire versie bijwerken van MySQL 5,6 naar MySQL 5,7 met behulp van Lees replica's

U kunt minimale downtime van de primaire versie van MySQL 5,6 naar MySQL 5,7 door het gebruik van replica's te lezen. Het is belang rijk dat u de Lees replica van uw server bijwerkt naar 5,7 voor het eerst en later failover van uw toepassing om de replica te lezen en een nieuwe primaire te maken.

1. Selecteer uw bestaande Azure Database for MySQL 5,6 in het [Azure Portal](https://portal.azure.com/).

2. Maak een [Lees replica](./concepts-read-replicas.md#create-a-replica) van de primaire server.

3. Voer [een upgrade uit voor uw Lees replica](#perform-major-version-upgrade-from-mysql-56-to-mysql-57-on-read-replica-using-azure-portal) naar versie 5,7.

4. Wanneer u hebt gecontroleerd of de replica-server wordt uitgevoerd op versie 5,7, stopt u het maken van een verbinding met de primaire server van uw toepassing.
 
5. Controleer de replicatie status en zorg ervoor dat de replica helemaal aan het primaire deel is gekoppeld, zodat alle gegevens synchroon zijn en ervoor zorgen dat er geen nieuwe bewerkingen meer worden uitgevoerd.

   Roep de [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) opdracht op de replica server om de replicatie status weer te geven.

   ```sql
   SHOW SLAVE STATUS\G
   ```

   Als de status van `Slave_IO_Running` en `Slave_SQL_Running` Ja is en de waarde van `Seconds_Behind_Master` is 0, werkt replicatie goed. `Seconds_Behind_Master` Hiermee wordt aangegeven hoe laat de replica. Als de waarde niet 0 is, betekent dit dat de replica updates verwerkt. Nadat u hebt bevestigd, `Seconds_Behind_Master` is het veilig om de replicatie te stoppen.

6. Promoot uw Lees replica naar primair door de [replicatie te stoppen](./howto-read-replicas-portal.md#stop-replication-to-a-replica-server).

7. Wijs uw toepassing naar de nieuwe primaire (voormalige replica) met Server 5,7. Elke server heeft een unieke connection string. Werk uw toepassing bij zodat deze verwijst naar de (voormalige) replica in plaats van de bron.

> [!Note]
> In dit scenario is er sprake van downtime tijdens de stappen 4, 5 en 6.


## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="when-will-this-upgrade-feature-be-ga-as-we-have-mysql-v56-in-our-production-environment-that-we-need-to-upgrade"></a>Wanneer wordt in onze productie omgeving MySQL v 5.6 uitgevoerd, wanneer we een upgrade voor de upgrade functie uitvoeren?

Het GA van deze functie is gepland voor MySQL v 5.6 buiten gebruik gesteld. De functie is echter gereed voor productie en volledig wordt ondersteund door Azure, zodat u deze kunt uitvoeren met vertrouwen in uw omgeving. Als aanbevolen best practice raden wij u ten zeerste aan om dit eerst uit te voeren en te testen op een herstelde kopie van de server, zodat u de downtime kunt schatten tijdens de upgrade en de compatibiliteits test voor toepassingen moet uitvoeren voordat u deze uitvoert op productie. Zie [How to time Restore uitvoeren](howto-restore-server-portal.md#point-in-time-restore) voor het maken van een Point-in-time kopie van uw server voor meer informatie. 

### <a name="will-this-cause-downtime-of-the-server-and-if-so-how-long"></a>Leidt dit tot uitval tijd van de server en zo ja, hoe lang?

Ja, de server is tijdens het upgrade proces niet beschikbaar. u wordt aangeraden deze bewerking uit te voeren tijdens het geplande onderhouds venster. De geschatte uitval tijd is afhankelijk van de grootte van de data base, de opslag grootte (IOPs ingericht) en het aantal tabellen in de data base. De upgrade tijd is direct evenredig met het aantal tabellen op de server. De upgrades van de Basic SKU-servers worden naar verwachting meer tijd in beslag nemen op het standaard-opslag platform. Als u de downtime voor uw server omgeving wilt schatten, raden we u aan om eerst een upgrade uit te voeren op de herstelde kopie van de server. Overweeg [het uitvoeren van een minimale downtime van de primaire versie van mysql 5,6 naar MySQL 5,7 met lees replica.](#perform-minimal-downtime-major-version-upgrade-from-mysql-56-to-mysql-57-using-read-replicas)

### <a name="what-will-happen-if-we-do-not-choose-to-upgrade-our-mysql-v56-server-before-february-5-2021"></a>Wat gebeurt er als we niet vóór 5 februari 2021 een upgrade van onze MySQL-server zullen uitvoeren?

U kunt uw MySQL v 5.6-server net zo eerder blijven uitvoeren. Azure voert **geen** geforceerde upgrade op de server uit. De beperkingen die worden beschreven in [Azure database for MySQL-versie beleid](concepts-version-policy.md) zijn echter van toepassing.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Azure database for MySQL-versie beleid](concepts-version-policy.md).
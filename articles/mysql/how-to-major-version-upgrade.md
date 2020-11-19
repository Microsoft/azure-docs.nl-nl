---
title: Upgrade van primaire versie-Azure Portal-Azure Database for MySQL-één server
description: In dit artikel wordt beschreven hoe u een upgrade kunt uitvoeren voor de primaire versie van Azure Database for MySQL-één server met behulp van Azure Portal
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 11/16/2020
ms.openlocfilehash: 4dd4729589e429cb1b028b183fdfd144617d1d1b
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94920641"
---
# <a name="major-version-upgrade-in-azure-database-for-mysql-single-server-using-the-azure-portal"></a>Upgrade van de primaire versie in Azure Database for MySQL één server met behulp van de Azure Portal

> [!IMPORTANT]
> De upgrade van de primaire versie van Azure Data Base voor MySQL met één server is in open bare preview.

In dit artikel wordt beschreven hoe u uw primaire MySQL-versie in-place kunt bijwerken in Azure Database for MySQL één server.

Met deze functie kunnen klanten in-place Upgrades uitvoeren van hun MySQL 5,6-servers naar MySQL 5,7 met een klik op de knop zonder dat er gegevens worden verplaatst of dat een toepassing connection string wijzigingen kan aanbrengen.

> [!Note]
> * Upgrade van primaire versie is alleen beschikbaar voor de upgrade van de primaire versie van MySQL 5,6 naar MySQL 5,7<br>
> * De upgrade van de primaire versie wordt nog niet ondersteund op de replica server.

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

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Azure database for MySQL-versie beleid](concepts-version-policy.md).
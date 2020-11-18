---
title: Upgrade van primaire versie-Azure Portal-Azure Database for MySQL-één server
description: In dit artikel wordt beschreven hoe u een upgrade kunt uitvoeren voor de primaire versie van Azure Database for MySQL-één server met behulp van Azure Portal
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 11/16/2020
ms.openlocfilehash: 78c35e42cefa8897d9f93c3a941b4c0e8b81e5f9
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686775"
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

1. Selecteer uw bestaande Azure Database for MySQL 5,6-server in het [Azure Portal](https://portal.azure.com/).

2. Klik op de pagina **overzicht** op de knop **bijwerken** op de werk balk.

3. Selecteer in de sectie **bijwerken** de optie **OK** om Azure data base for MySQL 5,6-server te upgraden naar 5,7 server.

    :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Azure Database for MySQL-overzicht-upgrade":::

4. Er wordt een melding bevestigd dat de upgrade is geslaagd.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Azure database for MySQL-versie beleid](concepts-version-policy.md).
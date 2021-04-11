---
title: Server parameters configureren-Azure CLI-Azure Database for MySQL flexibele server
description: In dit artikel wordt beschreven hoe u de service parameters configureert in Azure Database for MySQL flexibele server met behulp van het opdracht regel hulpprogramma van Azure CLI.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 11/10/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 21f9b3dcb94be105c8b7279e6ac447540da65447
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105110097"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-flexible-server-using-the-azure-cli"></a>Server parameters configureren in Azure Database for MySQL flexibele server met behulp van de Azure CLI

> [!IMPORTANT] 
> Azure Database for MySQL - Flexible Server is momenteel beschikbaar als openbare preview.

U kunt de para meters voor een Azure Database for MySQL flexibele server weer geven, tonen en bijwerken met behulp van Azure CLI, het opdracht regel programma van Azure. De server parameters worden geconfigureerd met de standaard-en aanbevolen waarde bij het maken van de server.  

In dit artikel wordt beschreven hoe u server parameters kunt weer geven, tonen en bijwerken met behulp van de Azure CLI.

>[!Note]
> Server parameters kunnen globaal worden bijgewerkt op server niveau, gebruik de [Azure cli](./how-to-configure-server-parameters-cli.md) of [Azure Portal](./how-to-configure-server-parameters-portal.md)

## <a name="prerequisites"></a>Vereisten
Als u deze hand leiding wilt door lopen, hebt u het volgende nodig:
- [Een Azure Database for MySQL flexibele server](quickstart-create-server-cli.md)
- [Azure cli](/cli/azure/install-azure-cli) -opdracht regel programma of gebruik de Azure Cloud shell in de browser.

## <a name="list-server-parameters-for-azure-database-for-mysql-flexible-server"></a>Server parameters weer geven voor Azure Database for MySQL flexibele server
Als u alle para meters op een server en hun waarden wilt weer geven, voert u de opdracht [AZ mysql Flexible-server para meter List](/cli/azure/mysql/flexible-server/parameter) uit.

U kunt de server parameters weer geven voor de server **mydemoserver.mysql.database.Azure.com** onder resource groep **myresourcegroup**.
```azurecli-interactive
az mysql flexible-server parameter list --resource-group myresourcegroup --server-name mydemoserver
```
Zie het gedeelte MySQL-verwijzing in [Server systeem variabelen](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html)voor de definitie van elk van de vermelde para meters.

## <a name="show-server-parameter-details"></a>Details van server parameters weer geven
Als u details over een bepaalde para meter voor een server wilt weer geven, voert u de opdracht [AZ mysql Flexible-server para meter show](/cli/azure/mysql/flexible-server/parameter) uit.

In dit voor beeld worden details weer gegeven van de server parameter **langzame \_ query \_ logboek** voor server **mydemoserver.mysql.database.Azure.com** onder resource groep **myresourcegroup.**
```azurecli-interactive
az mysql flexible-server parameter show --name slow_query_log --resource-group myresourcegroup --server-name mydemoserver
```
## <a name="modify-a-server-parameter-value"></a>Een server parameter waarde wijzigen
U kunt ook de waarde van een bepaalde server parameter wijzigen, waardoor de onderliggende configuratie waarde wordt bijgewerkt voor de MySQL-server engine. Als u de server parameter wilt bijwerken, gebruikt u de opdracht [AZ mysql Flexible-server para meter set](/cli/azure/mysql/flexible-server/parameter) . 

Voor het bijwerken van de server parameter voor **langzame \_ query \_ Logboeken** van server **mydemoserver.mysql.database.Azure.com** onder resource groep **myresourcegroup.**
```azurecli-interactive
az mysql flexible-server parameter set --name slow_query_log --resource-group myresourcegroup --server-name mydemoserver --value ON
```
Als u de waarde van een para meter opnieuw wilt instellen, laat u de optionele `--value` para meter weg en de service wordt de standaard waarde. In het bovenstaande voor beeld zou er als volgt uitzien:
```azurecli-interactive
az mysql flexible-server parameter set --name slow_query_log --resource-group myresourcegroup --server-name mydemoserver
```
Met deze code wordt het **langzame \_ query \_ logboek** ingesteld op de standaard **waarde.** 

## <a name="setting-non-modifiable-server-parameters"></a>Niet-aanpas bare server parameters instellen

Als de para meter van de server die u wilt bijwerken niet kan worden gewijzigd, kunt u eventueel de para meter instellen op het verbindings niveau met `init_connect` . Hiermee stelt u de server parameters in voor elke client die verbinding maakt met de server. 

Werk de para meter **init \_ Connect** server van server **mydemoserver.mysql.database.Azure.com** onder resource groep **myresourcegroup** bij om waarden in te stellen, zoals een tekenset.
```azurecli-interactive
az mysql flexible-server parameter set --name init_connect --resource-group myresourcegroup --server-name mydemoserver --value "SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;"
```
>[!Note]
> `init_connect` kan worden gebruikt om parameters te wijzigen die geen SUPER-bevoegdhed(en) op sessieniveau vereisen. Als u wilt weten of u de parameter kunt instellen met `init_connect`, voert u de opdracht `set session parameter_name=YOUR_DESIRED_VALUE;` uit. Als er fouten optreden met het foutbericht **Toegang geweigerd; u hebt SUPER-privileges(s) nodig**, dan kunt u de parameter niet instellen met init_connect.

## <a name="working-with-the-time-zone-parameter"></a>Werken met de para meter tijd zone

### <a name="populating-the-time-zone-tables"></a>De tijd zone tabellen worden gevuld

De tijdzone tabellen op uw server kunnen worden gevuld door de opgeslagen procedure aan te roepen `mysql.az_load_timezone` vanuit een hulp programma zoals de MySQL-opdracht regel of MySQL Workbench.

> [!NOTE]
> Als u de opdracht uitvoert `mysql.az_load_timezone` vanuit MySQL Workbench, moet u de veilige update modus mogelijk eerst uitschakelen met `SET SQL_SAFE_UPDATES=0;` .

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> U moet de server opnieuw opstarten om te controleren of de tabellen van de tijd zone juist zijn ingevuld.<!-- fIX me To restart the server, use the [Azure portal](howto-restart-server-portal.md) or [CLI](howto-restart-server-cli.md). -->

Voer de volgende opdracht uit om de beschik bare tijd zone waarden weer te geven:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>De tijd zone van het globale niveau instellen

De tijd zone globaal niveau kan worden ingesteld met behulp van de opdracht [AZ mysql Flexible-server para meter set](/cli/azure/mysql/flexible-server/parameter) .

Met de volgende opdracht wordt **de \_ time zone** server-para meter van server **mydemoserver.mysql.database.Azure.com** onder resource groep **myresourcegroup** naar **VS/Pacific** bijgewerkt.

```azurecli-interactive
az mysql flexible-server parameter set --name time_zone --resource-group myresourcegroup --server-name mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>De tijd zone op sessie niveau instellen

De tijd zone op sessie niveau kan worden ingesteld door de `SET time_zone` opdracht uit te voeren vanuit een hulp programma zoals de MySQL-opdracht regel of MySQL Workbench. In het volgende voor beeld wordt de tijd zone ingesteld op de **Amerikaanse/Pacific-** tijd zone.  

```sql
SET time_zone = 'US/Pacific';
```

Raadpleeg de MySQL-documentatie voor [datum-en tijd functies](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).


## <a name="next-steps"></a>Volgende stappen

- [Server parameters configureren in azure Portal](./how-to-configure-server-parameters-portal.md)

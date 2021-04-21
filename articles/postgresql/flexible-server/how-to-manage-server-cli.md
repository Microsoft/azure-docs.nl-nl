---
title: Server beheren - Azure CLI - Azure Database for PostgreSQL - Flexible Server
description: Leer hoe u een Azure Database for PostgreSQL - Flexible Server beheert via de Azure CLI.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 9a7e16bf85293a412baf5015af825377438ebb7b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778495"
---
# <a name="manage-an-azure-database-for-postgresql---flexible-server-by-using-the-azure-cli"></a>Een Azure Database for PostgreSQL - Flexible Server beheren met behulp van de Azure CLI

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server is in preview.

In dit artikel wordt beschreven hoe u uw flexibele server beheert die in Azure is geïmplementeerd. Beheertaken omvatten het schalen van berekeningen en opslag, het opnieuw instellen van het beheerderswachtwoord en het weergeven van servergegevens.

## <a name="prerequisites"></a>Vereisten

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint. 

U moet azure CLI versie 2.0 of hoger lokaal uitvoeren. Voer de opdracht `az --version` uit om de geïnstalleerde versie te zien. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

Meld u aan bij uw account met de [opdracht az login.](/cli/azure/reference-index#az_login) 

```azurecli-interactive
az login
```

Selecteer uw abonnement met behulp van de [opdracht az account set.](/cli/azure/account) Noteer de **id-waarde uit** de **uitvoer az login** om te gebruiken als de waarde voor het argument **subscription** in de volgende opdracht. Als u meerdere abonnementen hebt, kiest u het abonnement waarvoor de resource moet worden gefactureerd. Gebruik de opdracht [az account list](/cli/azure/account#az_account_list) om al uw abonnementen te identificeren.

```azurecli
az account set --subscription <subscription id>
```

> [!Important]
> Als u nog geen flexibele server hebt gemaakt, moet u dit doen om deze handleiding te volgen.

## <a name="scale-compute-and-storage"></a>Rekenkracht en opslag schalen

U kunt uw rekenlaag, vCores en opslag eenvoudig omhoog schalen met behulp van de volgende opdracht. Zie het overzicht [az postgres flexible-server](/cli/azure/postgres/flexible-server) voor een lijst met alle serverbewerkingen die u kunt uitvoeren.

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v3 --storage-size 6144
```

Hieronder volgen de details voor de argumenten in de voorgaande code:

**Instelling** | **Voorbeeldwaarde** | **Beschrijving**
---|---|---
naam | mydemoserver | Voer een unieke naam in voor uw server. De servernaam mag alleen kleine letters, cijfers en het koppelteken (-) bevatten. De naam moet 3 tot 63 tekens bevatten.
resource-group | myResourceGroup | Geef de naam op van de Azure-resourcegroep.
sku-name|Standard_D4ds_v3|Voer de naam van de rekenlaag en grootte in. De waarde volgt de verkorte *Standard_{VM-grootte}.* Raadpleeg de [prijscategorieën](../concepts-pricing-tiers.md) voor meer informatie.
storage-size | 6144 | Voer de opslagcapaciteit van de server in megabytes in. Het minimum is 5120, oplopend in stappen van 1024.

> [!IMPORTANT]
> U kunt de opslag niet omlaag schalen. 

## <a name="manage-postgresql-databases-on-a-server"></a>PostgreSQL-databases op een server beheren

Er zijn een aantal toepassingen die kunt gebruiken om verbinding te maken met uw Azure Database voor PostgreSQL-server. Als op uw clientcomputer PostgreSQL is geïnstalleerd, kunt u een lokaal exemplaar van [psql gebruiken.](https://www.postgresql.org/docs/current/static/app-psql.html) We gaan nu het opdrachtregelprogramma psql gebruiken om verbinding te maken met de Azure Database for PostgreSQL server.

1. Voer de volgende **psql-opdracht** uit:

   ```bash
   psql --host=<servername> --port=<port> --username=<user> --dbname=<dbname>
   ```

   Met de volgende opdracht maakt u bijvoorbeeld verbinding met de standaarddatabase **postgres** op uw PostgreSQL-server **mydemoserver.postgres.database.azure.com** uw toegangsreferenties. Wanneer u hier om wordt gevraagd, voert u de in `<server_admin_password>` die u hebt gekozen.
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres
   ```

   Nadat u verbinding hebt, wordt in het hulpprogramma psql een **postgres-prompt** weergegeven waarin u SQL-opdrachten kunt invoeren. Er wordt een waarschuwing weergegeven in de uitvoer van de eerste verbinding als de versie van psql die u gebruikt, verschilt van de versie op de Azure Database for PostgreSQL server.

   Voorbeeld van psql-uitvoer:

   ```bash
   psql (11.3, server 12.1)
   WARNING: psql major version 11, server major version 12.
            Some psql features might not work.
   SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
   Type "help" for help.

   postgres=>
   ```

   > [!TIP]
   > Als de firewall niet is geconfigureerd voor het toestaan van het IP-adres van uw client, wordt het volgende foutbericht weergegeven:
   >
   > 'psql: FATAL:  no pg_hba.conf entry for host `<IP address>`, user "myadmin", database "postgres", SSL on FATAL: SSL connection is required. Geef SSL-opties op en opnieuw proberen.
   >
   > Controleer of het IP-adres van uw client is toegestaan in de firewallregels.

2. Maak een lege database met de **naam postgresdb door** de volgende opdracht te typen bij de prompt:

    ```bash
    CREATE DATABASE postgresdb;
    ```

3. Voer bij de prompt de volgende opdracht uit om verbinding te maken met de zojuist gemaakte database **postgresdb:**

    ```bash
    \c postgresdb
    ```

4. Typ  `\q` en selecteer Enter om psql af te sluit.

In deze sectie hebt u verbinding gemaakt met de Azure Database for PostgreSQL server via psql en hebt u een lege gebruikersdatabase gemaakt.

## <a name="reset-the-admin-password"></a>Het beheerderswachtwoord opnieuw instellen

U kunt het wachtwoord van de beheerdersrol wijzigen met de volgende opdracht:

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!IMPORTANT]
> Kies een wachtwoord van minimaal 8 tekens en maximaal 128 tekens. Het wachtwoord moet tekens bevatten uit drie van de volgende categorieën: 
> - Nederlandse hoofdletters
> - Nederlandse kleine letters
> - Getallen
> - Niet-alfanumerieke tekens

## <a name="delete-a-server"></a>Een server verwijderen

Voer de opdracht [az postgres flexible-server delete](/cli/azure/postgres/flexible-server#az_postgresql_flexible_server_delete) uit om de Azure Database for PostgreSQL flexibele server te verwijderen.

```azurecli-interactive
az postgres flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Volgende stappen

- [Concepten voor back-up en herstel begrijpen](concepts-backup-restore.md)
- [De server afstemmen en bewaken](concepts-monitoring.md)
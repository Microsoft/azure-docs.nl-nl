---
title: Server beheren-Azure CLI-Azure Database for PostgreSQL-flexibele server
description: Meer informatie over het beheren van een Azure Database for PostgreSQL flexibele server vanuit de Azure CLI.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 278f8f816909a7e365d7e45d04c5169950e79a65
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96493675"
---
# <a name="manage-an-azure-database-for-postgresql---flexible-server-by-using-the-azure-cli"></a>Een Azure Database for PostgreSQL flexibele server beheren met behulp van de Azure CLI

> [!IMPORTANT]
> Azure Database for PostgreSQL-flexibele server is in preview.

In dit artikel leest u hoe u uw flexibele server kunt beheren die in Azure is geïmplementeerd. Beheer taken zijn onder andere berekening en opslag schalen, beheerders wachtwoord opnieuw instellen en server details weer geven.

## <a name="prerequisites"></a>Vereisten

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint. 

U moet de Azure CLI-versie 2,0 of hoger lokaal uitvoeren. Voer de opdracht `az --version` uit om de geïnstalleerde versie te zien. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

Meld u aan bij uw account met de opdracht [AZ login](/cli/azure/reference-index#az-login) . 

```azurecli-interactive
az login
```

Selecteer uw abonnement met behulp van de opdracht [AZ account set](/cli/azure/account) . Noteer de **id-** waarde van de AZ- **aanmeld** uitvoer die moet worden gebruikt als de waarde voor het argument **abonnement** in de volgende opdracht. Als u meerdere abonnementen hebt, kiest u het abonnement waarin de resource moet worden gefactureerd. Als u al uw abonnementen wilt identificeren, gebruikt u de opdracht [AZ account list](/cli/azure/account#az-account-list) .

```azurecli
az account set --subscription <subscription id>
```

> [!Important]
> Als u nog geen flexibele server hebt gemaakt, moet u dit doen voordat u deze hand leiding volgt.

## <a name="scale-compute-and-storage"></a>Schaal berekening en opslag

U kunt uw Compute-laag, vCores en opslag eenvoudig opschalen met behulp van de volgende opdracht. Zie het overzicht [AZ post gres Flexible-server](/cli/azure/postgres/flexible-server) voor een lijst met alle server bewerkingen die u kunt uitvoeren.

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v3 --storage-size 6144
```

Hieronder vindt u de Details voor de argumenten in de voor gaande code:

**Instelling** | **Voorbeeldwaarde** | **Beschrijving**
---|---|---
naam | mydemoserver | Voer een unieke naam in voor uw server. De servernaam mag alleen kleine letters, cijfers en het koppelteken (-) bevatten. De naam moet 3 tot 63 tekens bevatten.
resource-group | myResourceGroup | Geef de naam op van de Azure-resourcegroep.
sku-name|Standard_D4ds_v3|Voer de naam van de compute-laag en de grootte in. De waarde volgt de Conventie *Standard_ {VM-grootte}* in steno. Raadpleeg de [prijscategorieën](../concepts-pricing-tiers.md) voor meer informatie.
storage-size | 6144 | Voer de opslag capaciteit van de server in mega bytes in. De minimum waarde is 5120, waardoor de toename van 1024 wordt verhoogd.

> [!IMPORTANT]
> U kunt de schaal van de opslag niet verlagen. 

## <a name="manage-postgresql-databases-on-a-server"></a>PostgreSQL-data bases op een server beheren

Er zijn een aantal toepassingen die kunt gebruiken om verbinding te maken met uw Azure Database voor PostgreSQL-server. Als op uw client computer PostgreSQL is geïnstalleerd, kunt u een lokale instantie van [psql](https://www.postgresql.org/docs/current/static/app-psql.html)gebruiken. We gaan nu het opdracht regel programma psql gebruiken om verbinding te maken met de Azure Database for PostgreSQL-server.

1. Voer de volgende **psql** -opdracht uit:

   ```bash
   psql --host=<servername> --port=<port> --username=<user> --dbname=<dbname>
   ```

   Met de volgende opdracht maakt u bijvoorbeeld verbinding met de standaard database met de naam **post gres** op uw postgresql-server **mydemoserver.postgres.database.Azure.com** via uw toegangs referenties. Wanneer u hierom wordt gevraagd, voert u het in `<server_admin_password>` dat u hebt gekozen.
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres
   ```

   Nadat u verbinding hebt gemaakt, wordt in het psql-hulp programma een **post gres** -prompt weer gegeven waarin u SQL-opdrachten kunt invoeren. Er wordt een waarschuwing weer gegeven in de eerste verbindings uitvoer als de versie van psql die u gebruikt, afwijkt van de versie op de Azure Database for PostgreSQL-server.

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
   > 'psql: FATAL:  no pg_hba.conf entry for host `<IP address>`, user "myadmin", database "postgres", SSL on FATAL: SSL connection is required. Geef SSL-opties op en probeer het opnieuw. "
   >
   > Controleer of het IP-adres van de client is toegestaan in de firewall regels.

2. Maak een lege data base met de naam **PostgresDB** door de volgende opdracht te typen bij de prompt:

    ```bash
    CREATE DATABASE postgresdb;
    ```

3. Voer bij de prompt de volgende opdracht uit om verbinding te maken met de zojuist gemaakte Data Base- **PostgresDB**:

    ```bash
    \c postgresdb
    ```

4. Typ  `\q` en selecteer ENTER om psql af te sluiten.

In deze sectie hebt u verbinding met de Azure Database for PostgreSQL-server via psql en hebt u een lege gebruikers database gemaakt.

## <a name="reset-the-admin-password"></a>Het beheerders wachtwoord opnieuw instellen

U kunt het wacht woord van de beheerdersrol wijzigen met de volgende opdracht:

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!IMPORTANT]
> Kies een wacht woord met mini maal acht tekens en Maxi maal 128 tekens. Het wacht woord moet tekens bevatten uit drie van de volgende categorieën: 
> - Nederlandse hoofdletters
> - Nederlandse kleine letters
> - Getallen
> - Niet-alfanumerieke tekens

## <a name="delete-a-server"></a>Een server verwijderen

Als u de Azure Database for PostgreSQL flexibele server wilt verwijderen, voert u de opdracht [AZ post gres Flexible-server delete](/cli/azure/postgres/flexible-server#az-PostgreSQL-flexible-server-delete) uit.

```azurecli-interactive
az postgres flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over back-up-en herstel concepten](concepts-backup-restore.md)
- [De server afstemmen en bewaken](concepts-monitoring.md)
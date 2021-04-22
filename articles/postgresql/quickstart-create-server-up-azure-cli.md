---
title: 'Quickstart: Server maken - az postgres up - Azure Database for PostgreSQL - Eén server'
description: Quickstart-handleiding voor het maken van Azure Database for PostgreSQL - Eén server met behulp van de up-opdracht in Azure CLI (opdrachtregelinterface).
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/06/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: f0f0dc56dcaad73ff945d319cf98dc30483ee07e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875085"
---
# <a name="quickstart-use-an-azure-cli-command-az-postgres-up-preview-to-create-an-azure-database-for-postgresql---single-server"></a>Quickstart: Een Azure CLI-opdracht, az postgres up (preview), gebruiken om een Azure Database for PostgreSQL te maken - Eén server

> [!IMPORTANT]
> De Azure CLI-opdracht [az postgres up](/cli/azure/postgres#az_postgres_up) is momenteel beschikbaar als preview-versie.

Azure Database voor PostgreSQL is een beheerde service waarmee u PostgreSQL-databases met hoge beschikbaarheid in de cloud kunt uitvoeren, beheren en schalen. De Azure CLI wordt gebruikt voor het maken en beheren van Azure-resources vanaf de opdrachtregel of in scripts. In deze quickstart ontdekt u hoe u de opdracht [az postgres up](/cli/azure/postgres#az_postgres_up) gebruikt om een Azure Database for PostgreSQL-server te maken met behulp van de Azure CLI. Naast het maken van de server worden met de opdracht `az postgres up` ook een voorbeelddatabase en een hoofdgebruiker in die database gemaakt, de firewall geopend voor Azure-services en standaardfirewallregels voor de clientcomputer gemaakt. Met deze standaardregels kunt u het ontwikkelingsproces versnellen.

## <a name="prerequisites"></a>Vereisten

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

In dit artikel moet u Azure CLI-versie 2.0 of later lokaal uitvoeren. Voer de opdracht `az --version` uit om de geïnstalleerde versie te zien. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

U moet zich aanmelden bij uw account met behulp van de opdracht [az login](/cli/azure/authenticate-azure-cli). Let op de **id**-eigenschap van de opdrachtuitvoer voor de naam van het desbetreffende abonnement.

```azurecli
az login
```

Als u meerdere abonnementen hebt, kiest u het juiste abonnement waarin de resource moet worden gefactureerd. Selecteer de specifieke abonnements-id in uw account met de opdracht [az account set](/cli/azure/account). Gebruik de eigenschap **abonnement-id** uit de **az login**-uitvoer voor uw abonnement in de tijdelijke aanduiding voor de abonnement-id.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Een Azure-database voor PostgreSQL-server maken

Als u de opdrachten wilt gebruiken, installeert u de extensie [db-up](/cli/azure/). Als een fout wordt geretourneerd, moet u controleren of de nieuwste versie van de Azure CLI is geïnstalleerd. Zie [Azure CLI installeren](/cli/azure/install-azure-cli).

```azurecli
az extension add --name db-up
```

Maak een Azure Database for PostgreSQL-server met behulp van de volgende opdracht:

```azurecli
az postgres up
```

De server wordt gemaakt met de volgende standaardwaarden (tenzij u deze handmatig overschrijft):

**Instelling** | **Standaardwaarde** | **Beschrijving**
---|---|---
servernaam | Door het systeem gegenereerd | Een unieke naam ter identificatie van uw Azure Database for PostgreSQL-server.
resource-group | Door het systeem gegenereerd | Een nieuwe Azure-resourcegroep.
sku-name | GP_Gen5_2 | De naam van de SKU. Volgt de verkorte notatie voor conventie {prijscategorie}\_{compute-generatie}\_{vCores}. De standaardinstelling is een Gen5-server voor algemeen gebruik, met 2 vCores. Zie onze [prijzenpagina](https://azure.microsoft.com/pricing/details/postgresql/) voor meer informatie over de prijscategorieën.
backup-retention | 7 | Hoelang een back-up wordt bewaard. De eenheid is dagen.
geo-redundant-backup | Uitgeschakeld | Of geografisch redundante back-ups moeten worden ingeschakeld voor deze server.
location | westus2 | De Azure-locatie voor de server.
ssl-enforcement | Uitgeschakeld | Of TLS/SSL moet worden ingeschakeld voor deze server.
storage-size | 5120 | De opslagcapaciteit van de server (eenheid is MB).
versie | 10 | De primaire versie van PostgreSQL.
admin-user | Door het systeem gegenereerd | De gebruikersnaam voor de beheerder.
admin-password | Door het systeem gegenereerd | Het wachtwoord van het beheerdersaccount.

> [!NOTE]
> Zie de [Azure CLI-documentatie](/cli/azure/postgres#az_postgres_up) voor meer informatie over de opdracht `az postgres up` en de aanvullende parameters.

Zodra uw server is gemaakt, zijn de volgende instellingen beschikbaar:

- De firewallregel 'devbox' wordt gemaakt. De Azure CLI probeert het IP-adres te detecteren van de machine van waaruit de `az postgres up`-opdracht wordt uitgevoerd en staat dat IP-adres toe.
- Toegang tot Azure-services toestaan is ingesteld op AAN. Met deze instelling wordt de firewall van de server geconfigureerd om verbindingen van alle Azure-resources te accepteren, inclusief resources die niet onderdeel zijn van uw abonnement.
- De lege database 'sampledb' wordt gemaakt
- De nieuwe gebruiker 'root', met privileges ingesteld op 'sampledb', wordt gemaakt

> [!NOTE]
> Azure Database for PostgreSQL communiceert via poort 5432. Als u verbinding maakt vanuit een bedrijfsnetwerk, wordt uitgaand verkeer via poort 5432 mogelijk niet toegestaan door de firewall van uw netwerk. Vraag aan de IT-afdeling om poort 5432 te openen, zodat u verbinding kunt maken met uw server.

## <a name="get-the-connection-information"></a>De verbindingsgegevens ophalen

Nadat de opdracht `az postgres up` is voltooid, wordt een lijst met verbindingstekenreeksen voor populaire programmeertalen geretourneerd. Deze verbindingstekenreeksen worden vooraf geconfigureerd met de specifieke kenmerken van uw zojuist gemaakte Azure Database for PostgreSQL-server.

U kunt de opdracht [az postgres show-connection-string](/cli/azure/postgres#az_postgres_show_connection_string) gebruiken om deze verbindingstekenreeksen opnieuw te vermelden.

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder alle resources die u hebt gemaakt in de quickstart met behulp van de volgende opdracht. Met deze opdracht worden de Azure Database for PostgreSQL-server en de resourcegroep verwijderd.

```azurecli
az postgres down --delete-group
```

Als u alleen de zojuist gemaakte server wilt verwijderen, kunt u de opdracht [az postgres down](/cli/azure/postgres#az_postgres_down) uitvoeren.

```azurecli
az postgres down
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een database migreren met behulp van Exporteren en importeren](./howto-migrate-using-export-and-import.md)

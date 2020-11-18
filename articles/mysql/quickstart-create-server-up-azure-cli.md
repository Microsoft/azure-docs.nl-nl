---
title: 'Quickstart: Azure Database for MySQL maken met behulp van az mysql up'
description: Quickstart voor het maken van een Azure Database for MySQL-server met behulp van Azure CLI (opdrachtregelinterface).
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 3/18/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: d89cc41ed26124ae4ad2e6689be6d59278c3d9da
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542164"
---
# <a name="quickstart-create-an-azure-database-for-mysql-using-a-simple-azure-cli-command---az-mysql-up-preview"></a>Quickstart: Een Azure Database for MySQL maken met behulp van de eenvoudige Azure CLI-opdracht az mysql up (preview-versie)

> [!IMPORTANT]
> De Azure CLI-opdracht [az mysql up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) is momenteel beschikbaar als preview-versie.

Azure Database voor MySQL is een beheerde service waarmee u MySQL-databases met hoge beschikbaarheid in de cloud kunt uitvoeren, beheren en schalen. De Azure CLI wordt gebruikt voor het maken en beheren van Azure-resources vanaf de opdrachtregel of in scripts. In deze quickstart ontdekt u hoe u de opdracht [az mysql up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) gebruikt om een Azure Database for MySQL-server te maken met behulp van de Azure CLI. Naast het maken van de server worden met de opdracht `az mysql up` ook een voorbeelddatabase en een hoofdgebruiker in die database gemaakt, de firewall geopend voor Azure-services en standaardfirewallregels voor de clientcomputer gemaakt. Hiermee kunt u het ontwikkelingsproces versnellen.

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

## <a name="create-an-azure-database-for-mysql-server"></a>Een Azure-database voor MySQL-server maken

Als u de opdrachten wilt gebruiken, installeert u de extensie [db-up](/cli/azure/ext/db-up). Als een fout wordt geretourneerd, moet u controleren of de nieuwste versie van de Azure CLI is geïnstalleerd. Zie [Azure CLI installeren](/cli/azure/install-azure-cli).

```azurecli
az extension add --name db-up
```

Maak een Azure Database for MySQL-server met behulp van de volgende opdracht:

```azurecli
az mysql up
```

De server wordt gemaakt met de volgende standaardwaarden (tenzij u deze handmatig overschrijft):

**Instelling** | **Standaardwaarde** | **Beschrijving**
---|---|---
servernaam | Door het systeem gegenereerd | Een unieke naam ter identificatie van de Azure Database voor MySQL-server.
resource-group | Door het systeem gegenereerd | Een nieuwe Azure-resourcegroep.
sku-name | GP_Gen5_2 | De naam van de SKU. Volgt de verkorte notatie voor conventie {prijscategorie}\_{compute-generatie}\_{vCores}. De standaardinstelling is een Gen5-server voor algemeen gebruik, met 2 vCores. Zie onze [prijzenpagina](https://azure.microsoft.com/pricing/details/mysql/) voor meer informatie over de prijscategorieën.
backup-retention | 7 | Hoe lang een back-up moet worden bewaard. De eenheid is dagen.
geo-redundant-backup | Uitgeschakeld | Of geografisch redundante back-ups moeten worden ingeschakeld voor deze server.
location | westus2 | De Azure-locatie voor de server.
ssl-enforcement | Ingeschakeld | Of SSL moet worden ingeschakeld voor deze server.
storage-size | 5120 | De opslagcapaciteit van de server (eenheid is MB).
versie | 5.7 | De hoofdversie van MySQL.
admin-user | Door het systeem gegenereerd | De gebruikersnaam voor aanmelding als beheerder.
admin-password | Door het systeem gegenereerd | Het wachtwoord van het beheerdersaccount.

> [!NOTE]
> Zie de [Azure CLI-documentatie](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) voor meer informatie over de opdracht `az mysql up` en de aanvullende parameters.

Zodra uw server is gemaakt, zijn de volgende instellingen beschikbaar:

- De firewallregel 'devbox' wordt gemaakt. De Azure CLI probeert het IP-adres te detecteren van de machine van waaruit de `az mysql up`-opdracht wordt uitgevoerd en staat dat IP-adres toe.
- Toegang tot Azure-services toestaan is ingesteld op AAN. Met deze instelling wordt de firewall van de server geconfigureerd om verbindingen van alle Azure-resources te accepteren, inclusief resources die niet onderdeel zijn van uw abonnement.
- De parameter voor `wait_timeout` is ingesteld op 8 uur
- De lege database 'sampledb' wordt gemaakt
- De nieuwe gebruiker 'root', met privileges ingesteld op 'sampledb', wordt gemaakt

> [!NOTE]
> Azure Database for MySQL-servers communiceren via poort 3306. Als u verbinding maakt vanuit een bedrijfsnetwerk, wordt uitgaand verkeer via poort 3306 mogelijk niet toegestaan door de firewall van uw netwerk. Vraag aan de IT-afdeling om poort 3306 te openen, zodat u verbinding kunt maken met uw server.

## <a name="get-the-connection-information"></a>De verbindingsgegevens ophalen

Nadat de opdracht `az mysql up` is voltooid, wordt een lijst met verbindingstekenreeksen voor populaire programmeertalen geretourneerd. Deze verbindingstekenreeksen worden vooraf geconfigureerd met de specifieke kenmerken van uw zojuist gemaakte Azure Database for MySQL-server.

U kunt de opdracht [az mysql show-connection-string](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-show-connection-string) gebruiken om deze verbindingstekenreeksen opnieuw te vermelden.

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder alle resources die u hebt gemaakt in de quickstart met behulp van de volgende opdracht. Met deze opdracht worden de Azure Database for MySQL-server en de resourcegroep verwijderd.

```azurecli
az mysql down --delete-group
```

Als u alleen de zojuist gemaakte server wilt verwijderen, kunt u de opdracht [az mysql down](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-down) uitvoeren.

```azurecli
az mysql down
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een MySQL-database ontwerpen met Azure CLI](./tutorial-design-database-using-cli.md)

---
title: 'Quickstart: Server maken - Azure CLI - Azure Database for PostgreSQL - Flexible Server'
description: In deze quickstart wordt beschreven hoe u met Azure CLI een Azure Database for PostgreSQL Flexible Server maakt in een Azure-resourcegroep.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 9/22/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: d43fab43b05ccfd88308390f58ba48ff9111b295
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842818"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-flexible-server-using-azure-cli"></a>Quickstart: Een Azure Database for PostgreSQL Flexible Server maken met behulp van Azure CLI

In deze quickstart wordt beschreven hoe u de [Azure CLI](/cli/azure/get-started-with-azure-cli)-opdrachten in [Azure Cloud Shell](https://shell.azure.com) gebruikt om binnen ongeveer vijf minuten een Azure Database for PostgreSQL Flexible Server te maken. Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

> [!IMPORTANT] 
> Azure Database for PostgreSQL Flexible Server is momenteel in preview.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell starten

[Azure Cloud Shell](../../cloud-shell/overview.md) is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account.

Als u Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van een codeblok. Als u naar [https://shell.azure.com/bash](https://shell.azure.com/bash) gaat, kunt u Cloud Shell ook openen in een afzonderlijk browsertabblad. Selecteer **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en selecteer vervolgens **Enter** om de code uit te voeren.

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, hebt u voor deze snelstart versie 2.0 of hoger van Azure CLI nodig. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="prerequisites"></a>Vereisten

U moet zich aanmelden bij uw account met behulp van de opdracht [az login](/cli/azure/reference-index#az-login). Let op de eigenschap **id**, die verwijst naar **abonnements-id** voor uw Azure-account.

```azurecli-interactive
az login
```

Selecteer het specifieke abonnement in uw account met de opdracht [az account set](/cli/azure/account#az-account-set). Noteer de **id**-waarde uit de uitvoer van **az login** en gebruik deze als de waarde voor het argument **abonnement** in de opdracht. Als u meerdere abonnementen hebt, kiest u het juiste abonnement waarin de resource moet worden gefactureerd. U kunt al uw abonnementen ophalen met de opdracht [az account list](/cli/azure/account#az-account-list).

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-a-flexible-server"></a>Een flexibele server maken

Maak een [Azure-resourcegroep](../../azure-resource-manager/management/overview.md) met behulp van de opdracht `az group create`, en maak vervolgens de flexibele PostgreSQL-server in deze resourcegroep. U moet een unieke naam opgeven. In het volgende voorbeeld wordt een resourcegroep met de naam `myresourcegroup` gemaakt op de locatie `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

Maak een flexibele server met de opdracht `az postgres flexible-server create`. Een server kan meerdere databases bevatten. Met de volgende opdracht maakt u een server met de standaardinstellingen van de service en waarden van de [lokale context](/cli/azure/local-context) van Azure CLI: 

```azurecli
az postgres flexible-server create
```

De gemaakte server heeft de volgende kenmerken: 
- Automatisch gegenereerde servernaam, beheerdersnaam, beheerderswachtwoord, resourcegroepnaam (indien nog niet opgegeven in de lokale context) en op dezelfde locatie als de resourcegroep 
- Servicestandaardwaarden voor resterende serverconfiguraties: compute-laag (Algemeen), compute-omvang/SKU (D2s_v3 - 2 vCore, 8 GB RAM), bewaartermijn voor back-ups (7 dagen) en PostgreSQL-versie (12)
- De standaard verbindingsmethode is privétoegang (VNet-integratie) met een automatisch gegenereerd virtueel netwerk en subnet

> [!NOTE] 
> De verbindingsmethode kan niet worden gewijzigd na het maken van de server. Als u bijvoorbeeld *Privétoegang (VNet-integratie)* hebt geselecteerd tijdens het maken, kunt u na het maken niet wijzigen naar *Openbare toegang (toegestane IP-adressen)* . U kunt het beste een server met privétoegang maken om veilig toegang te krijgen tot uw server met behulp van VNet-integratie. Meer informatie over privétoegang vindt u in het [artikel over concepten](./concepts-networking.md).

Als u de standaardinstellingen wilt wijzigen, raadpleegt u de Azure CLI-referentiedocumentatie <!--FIXME --> voor de volledige lijst met configureerbare CLI-parameters. 

> [!NOTE]
> Verbindingen met Azure Database for PostgreSQL lopen via poort 5432. Als u verbinding probeert te maken vanuit een bedrijfsnetwerk, wordt uitgaand verkeer via poort 5432 mogelijk niet toegestaan. In dat geval kunt u alleen verbinding maken met uw server als uw IT-afdeling poort 5432 openstelt.

## <a name="get-the-connection-information"></a>De verbindingsgegevens ophalen

Als u verbinding met uw server wilt maken, moet u hostgegevens en toegangsreferenties opgeven.

```azurecli-interactive
az postgres flexible-server show --resource-group myresourcegroup --name mydemoserver
```

Het resultaat wordt in JSON-indeling weergegeven. Noteer de **fullyQualifiedDomainName** en de **administratorLogin**.

<!--FIXME-->
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/flexibleServers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "name": "Standard_D2s_v3",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "publicAccess": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 131072
  },
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/flexibleServers",
  "userVisibleState": "Ready",
  "version": "12"
}
```

## <a name="connect-using-postgresql-command-line-client"></a>Verbinding maken met behulp van de PostgreSQL-opdrachtregelclient

Aangezien de flexibele server is gemaakt met *Privétoegang (VNet-integratie)* , moet u verbinding maken met uw server vanuit een resource binnen hetzelfde VNet als uw server. U kunt een virtuele machine maken en toevoegen aan het gemaakte virtuele netwerk. 

Nadat de VM i gemaakt, kunt u zich met SSH aanmelden bij de machine en het opdrachtregelprogramma **[psql](https://www.postgresql.org/download/)** installeren.

Maak verbinding met psql met behulp van de onderstaande opdracht. Vervang de waarden door uw werkelijke servernaam en wachtwoord. 

```bash
psql -h mydemoserver.postgres.database.azure.com -u mydemouser -p
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze resources niet voor een andere Quickstart of zelfstudie nodig hebt, kunt u ze verwijderen met de volgende opdracht:

```azurecli-interactive
az group delete --name myresourcegroup
```

Als u alleen de ene zojuist gemaakte server wilt verwijderen, kunt u de opdracht `az postgres flexible-server delete` uitvoeren.

```azurecli-interactive
az postgres flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
>[Een Django-app implementeren met App Service en PostgreSQL](tutorial-django-app-service-postgres.md)

---
title: 'Zelfstudie: Azure Database for PostgreSQL - Flexible Server en Azure App Service-web-app in hetzelfde virtuele netwerk maken'
description: Quickstart voor het maken van een Azure Database for PostgreSQL Flexible Server met web-app in een virtueel netwerk
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: ff9af90ca0b6b80ffece5ccd7d919c1d93e210c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104657583"
---
# <a name="tutorial-create-an-azure-database-for-postgresql---flexible-server-with-app-services-web-app-in-virtual-network"></a>Zelfstudie: Azure Database for PostgreSQL - Flexible Server met App Services-web-app in een virtueel netwerk maken

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server is als preview-versie beschikbaar

In deze zelfstudie leert u hoe u een Azure App Service-web-app met Azure Database for PostgreSQL - Flexible Server (Preview) in een [virtueel netwerk](../../virtual-network/virtual-networks-overview.md) maakt.

In deze zelfstudie leert u het volgende:
>[!div class="checklist"]
> * Een flexibele PostgreSQL-server maken in een virtueel netwerk
> * Een subnet maken om te delegeren aan App Service
> * Een webtoepassing maken
> * De web-app toevoegen aan het virtuele netwerk
> * Verbinding maken met Postgres vanuit de web-app 

## <a name="prerequisites"></a>Vereisten

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

In dit artikel moet u Azure CLI-versie 2.0 of later lokaal uitvoeren. Voer de opdracht `az --version` uit om de geïnstalleerde versie te zien. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

U moet zich aanmelden bij uw account met behulp van de opdracht [az login](/cli/azure/authenticate-azure-cli). Let op de **id**-eigenschap van de opdrachtuitvoer voor de naam van het desbetreffende abonnement.

```azurecli
az login
```

Als u meerdere abonnementen hebt, kiest u het juiste abonnement waarin de resource moet worden gefactureerd. Selecteer de specifieke abonnements-id in uw account met de opdracht [az account set](/cli/azure/account). Gebruik de eigenschap **abonnement-id** uit de **az login**-uitvoer voor uw abonnement in de tijdelijke aanduiding voor de abonnement-id.

```azurecli
az account set --subscription <subscription ID>
```

## <a name="create-a-postgresql-flexible-server-in-a-new-virtual-network"></a>Een flexibele PostgreSQL-server maken in een virtueel netwerk

Een flexibele privéserver in een virtueel netwerk (VNET) maken met behulp van de volgende opdracht:
```azurecli
az postgres flexible-server create --resource-group myresourcegroup --location westus2
```
Deze opdracht voert de volgende acties uit, dit kan enkele minuten duren:

- Maak de resourcegroep als deze nog niet bestaat.
- Hiermee wordt een servernaam gegenereerd als deze niet wordt opgegeven.
- Maak een nieuw virtueel netwerk voor uw nieuwe PostgreSQL-server. Noteer de naam van het virtuele netwerk en het subnet dat voor uw server is gemaakt. U moet de web-app namelijk aan hetzelfde virtuele netwerk toevoegen.
- Hiermee maakt u de gebruikersnaam van de beheerder en het wachtwoord voor uw server indien niet opgegeven.
- Hiermee maakt u een lege database met de naam **postgres**

> [!NOTE]
> - Noteer het wachtwoord dat voor u wordt gegenereerd als het niet is opgegeven. Als u het wachtwoord vergeet, kunt u het wachtwoord opnieuw instellen met behulp van de opdracht ``` az postgres flexible-server update```
> - Als u geen gebruik maakt van App Service Environment, moet u Toegang toestaan vanaf Azure-IP-adressen inschakelen met behulp van deze opdracht. 
>  ```azurecli
>  az postgres flexible-server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
>  ```

## <a name="create-subnet-for-app-service-endpoint"></a>Subnet maken voor App Service-eind punt
We moeten nu een subnet hebben dat is overgedragen aan App Service web-app-eind punt. Voer de volgende opdracht uit om een nieuw subnet te maken in hetzelfde virtuele netwerk als de database server die is gemaakt. 

```azurecli
az network vnet subnet create -g myresourcegroup --vnet-name VNETName --name webappsubnetName  --address-prefixes 10.0.1.0/24  --delegations Microsoft.Web/serverFarms --service-endpoints Microsoft.Web
```
Noteer de naam van het virtuele netwerk en het subnet nadat deze opdracht is vereist om de VNET-integratie regel voor de web-app toe te voegen nadat deze is gemaakt. 

## <a name="create-a-web-app"></a>Een web-app maken
In deze sectie maakt u een app-host in de App Service-app, koppelt u deze app aan de Postgres-database en implementeert u vervolgens uw code naar die host. Controleer in de terminal of u zich in de hoofdmap bevindt van de opslagplaats met de code van de app. Opmerking Basic-abonnement biedt geen ondersteuning voor VNET-integratie. Gebruik Standard of Premium. 

Een App Service-app maken (het hostproces) met de opdracht az webapp up

```azurecli
az webapp up --resource-group myresourcegroup --location westus2 --plan testappserviceplan --sku P2V2 --name mywebapp
```

> [!NOTE]
> - Gebruik voor het argument --location dezelfde locatie als voor de database in het vorige gedeelte.
> - Vervang <app-name> door een naam die uniek is in heel Azure (het servereindpunt is https://\<app-name>.azurewebsites.net). Geldige tekens voor <app-name> zijn: A-Z, 0-9, en -. Het is handig om een een combinatie van uw bedrijfsnaam en een app-id te gebruiken.

Deze opdracht voert de volgende acties uit, dit kan enkele minuten duren:

- Maak de resourcegroep als deze nog niet bestaat. (In deze opdracht gebruikt u dezelfde resourcegroep waarin u de database eerder hebt gemaakt.)
- Maak de App Service-app als deze nog niet bestaat.
- Schakel standaardlogboeken voor de app in, als die nog niet zijn ingeschakeld.
- Upload de opslagplaats met behulp van ZIP-implementatie, met ingeschakelde bouwautomatisering.

## <a name="add-the-web-app-to-the-virtual-network"></a>De web-app toevoegen aan het virtuele netwerk
Gebruik de opdracht **az webapp vnet-integration** om een regionale virtuele netwerkintegratie toe te voegen aan een webapp. Vervang <vnet-name> en <subnet-name> door de namen van het virtuele netwerk en subnet die de flexibele server gebruikt.

```azurecli
az webapp vnet-integration add -g myresourcegroup -n  mywebapp --vnet VNETName --subnet webappsubnetName
```

## <a name="configure-environment-variables-to-connect-the-database"></a>De omgevingsvariabelen configureren om de database te verbinden
Nu de code is geïmplementeerd naar App Service, is de volgende stap het verbinden van de app met de flexibele server in Azure. De code van de app verwacht om database-informatie te vinden in een aantal omgevingsvariabelen. Om omgevingsvariabelen in te stellen in App Service, maakt u 'app-instellingen' met de set-opdracht ```az webapp config appsettings```.

```azurecli
az webapp config appsettings set --settings DBHOST="<postgres-server-name>.postgres.database.azure.com" DBNAME="postgres" DBUSER="<username>" DBPASS="<password>"
```


- Vervang ```postgres-server-name```,```username``` en ```password``` voor de zojuist gemaakte flexibele-serveropdracht.
- Vervang <username> en <password> door de referenties die de opdracht eveneens voor u heeft gegenereerd.
- De resourcegroep en de naam van de app worden opgehaald uit de cachewaarden in het bestand . azure/config.
- Met de opdracht worden instellingen gemaakt met de namen ```DBHOST```, ```DBNAME```, ```DBUSER``` en ```DBPASS```. Als uw toepassingscode een andere naam gebruikt voor de databasegegevens, gebruikt u deze namen voor de app-instellingen zoals vermeld in de code.

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder alle resources die u hebt gemaakt in de zelfstudie met behulp van de volgende opdracht. Met deze opdracht verwijdert u alle resources in deze resourcegroep.

```azurecli
az group delete -n myresourcegroup
```


## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Een bestaande aangepaste DNS-naam toewijzen aan Azure App Service](../../app-service/app-service-web-tutorial-custom-domain.md)

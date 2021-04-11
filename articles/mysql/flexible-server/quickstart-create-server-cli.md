---
title: 'Quickstart: Een server maken - Azure CLI - Azure Database for MySQL - Flexible Server'
description: In deze snelstartgids wordt beschreven hoe u Azure CLI gebruikt om een Azure Database for MySQL Flexible Server in een Azure-resourcegroep te maken.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 9/21/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: a63c6f074178794db38b47950e176dd729344a54
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106492725"
---
# <a name="quickstart-create-an-azure-database-for-mysql-flexible-server-using-azure-cli"></a>Quickstart: Een Azure Database for MySQL Flexible Server maken met behulp van Azure CLI

In deze snelstartgids wordt beschreven hoe u de [Azure CLI](/cli/azure/get-started-with-azure-cli)-opdrachten in [Azure Cloud Shell](https://shell.azure.com) gebruikt om binnen ongeveer vijf minuten een Azure Database for MySQL Flexible Server te maken. Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

> [!IMPORTANT] 
> Azure Database for MySQL Flexible Server is momenteel beschikbaar als openbare preview

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

```azurecli-interactive
az account set --subscription <subscription id>
```

## <a name="create-a-flexible-server"></a>Een flexibele server maken

Maak een [Azure-resourcegroep](../../azure-resource-manager/management/overview.md) met behulp van de opdracht `az group create`, en maak vervolgens de flexibele MySQL-server in deze resourcegroep. U moet een unieke naam opgeven. In het volgende voorbeeld wordt een resourcegroep met de naam `myresourcegroup` gemaakt op de locatie `eastus2`.

```azurecli-interactive
az group create --name myresourcegroup --location eastus2
```

Maak een flexibele server met de opdracht `az mysql flexible-server create`. Een server kan meerdere databases bevatten. Met de volgende opdracht maakt u een server met de standaardinstellingen van de service en waarden van de [lokale context](/cli/azure/local-context) van Azure CLI: 

```azurecli-interactive
az mysql flexible-server create
```

De gemaakte server heeft de volgende kenmerken: 
- Automatisch gegenereerde servernaam, beheerdersnaam, beheerderswachtwoord, resourcegroepnaam (indien nog niet opgegeven in de lokale context) en op dezelfde locatie als de resourcegroep 
- Servicestandaardwaarden voor resterende serverconfiguraties: rekenlaag (Burstable), rekenomvang/SKU (B1MS), bewaartermijn voor back-ups (7 dagen) en MySQL-versie (5.7)
- De standaard verbindingsmethode is privétoegang (VNet-integratie) met een automatisch gegenereerd virtueel netwerk en subnet

> [!NOTE] 
> De verbindingsmethode kan niet worden gewijzigd na het maken van de server. Als u bijvoorbeeld *Privétoegang (VNet-integratie)* hebt geselecteerd tijdens het maken, kunt u na het maken niet wijzigen naar *Openbare toegang (toegestane IP-adressen)* . U kunt het beste een server met persoonlijke toegang maken om veilig toegang te krijgen tot uw server met behulp van VNet-integratie. Meer informatie over privétoegang vindt u in het [artikel over concepten](./concepts-networking.md).

Als u een standaardinstelling wilt wijzigen, raadpleegt u de [referentiedocumentatie](/cli/azure/mysql/flexible-server) van Azure CLI voor de complete lijst van configureerbare CLI-parameters. 

Hieronder ziet u een voorbeeld van uitvoer: 

```json
Command group 'mysql flexible-server' is in preview. It may be changed/removed in a future release.
Creating Resource Group 'groupXXXXXXXXXX'...
Creating new vnet "serverXXXXXXXXXVNET" in resource group "groupXXXXXXXXXX"...
Creating new subnet "serverXXXXXXXXXSubnet" in resource group "groupXXXXXXXXXX" and delegating it to "Microsoft.DBforMySQL/flexibleServers"...
Creating MySQL Server 'serverXXXXXXXXX' in group 'groupXXXXXXXXXX'...
Your server 'serverXXXXXXXXX' is using sku 'Standard_B1ms' (Paid Tier). Please refer to https://aka.ms/mysql-pricing for pricing details
Creating MySQL database 'flexibleserverdb'...
Make a note of your password. If you forget, you would have to reset your password with 'az mysql flexible-server update -n serverXXXXXXXXX -g groupXXXXXXXXXX -p <new-password>'.
{
  "connectionString": "server=serverXXXXXXXXX.mysql.database.azure.com;database=flexibleserverdb;uid=secureusername;pwd=securepasswordstring",
  "databaseName": "flexibleserverdb",
  "host": "serverXXXXXXXXX.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/groupXXXXXXXXXX/providers/Microsoft.DBforMySQL/flexibleServers/serverXXXXXXXXX",
  "location": "East US 2",
  "password": "securepasswordstring",
  "resourceGroup": "groupXXXXXXXXXX",
  "skuname": "Standard_B1ms",
  "subnetId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/groupXXXXXXXXXX/providers/Microsoft.Network/virtualNetworks/serverXXXXXXXXXVNET/subnets/serverXXXXXXXXXSubnet",
  "username": "secureusername",
  "version": "5.7"
}
```

Als u een standaardinstelling wilt wijzigen, raadpleegt u de [referentiedocumentatie](/cli/azure/mysql/flexible-server) van Azure CLI voor de complete lijst van configureerbare CLI-parameters. 

## <a name="create-a-database"></a>Een database maken
Voer de volgende opdracht uit om een Data Base te maken, **newdatabase** als u deze nog niet hebt gemaakt.

```azurecli-interactive
az mysql flexible-server db create -d newdatabase
```

> [!NOTE]
> Verbindingen met Azure Database voor MySQL communiceren via poort 3306. Als u verbinding probeert te maken vanuit een bedrijfsnetwerk, wordt uitgaand verkeer via poort 3306 mogelijk niet toegestaan. In dat geval kunt u alleen verbinding maken met uw server als uw IT-afdeling poort 3306 openstelt.

## <a name="get-the-connection-information"></a>De verbindingsgegevens ophalen

Als u verbinding met uw server wilt maken, moet u hostgegevens en toegangsreferenties opgeven.

```azurecli-interactive
az mysql flexible-server show --resource-group myresourcegroup --name mydemoserver
```

Het resultaat wordt in JSON-indeling weergegeven. Noteer de **fullyQualifiedDomainName** en de **administratorLogin**. Hieronder ziet u een voorbeeld van de JSON-uitvoer: 

```json
{
  "administratorLogin": "myadminusername",
  "administratorLoginPassword": null,
  "delegatedSubnetArguments": {
    "subnetArmResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/mydemoserverVNET/subnets/mydemoserverSubnet"
  },
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/flexibleServers/mydemoserver",
  "location": "East US 2",
  "name": "mydemoserver",
  "publicNetworkAccess": "Disabled",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 0,
    "name": "Standard_B1ms",
    "tier": "Burstable"
  },
  "storageProfile": {
    "backupRetentionDays": 7,
    "fileStorageSkuName": "Premium_LRS",
    "storageAutogrow": "Disabled",
    "storageIops": 0,
    "storageMb": 10240
  },
  "tags": null,
  "type": "Microsoft.DBforMySQL/flexibleServers",
  "version": "5.7"
}
```

## <a name="connect-and-test-the-connection-using-azure-cli"></a>Verbinding maken en testen met behulp van Azure CLI

Azure Database for MySQL flexibele server kunt u verbinding maken met uw MySQL-server met de Azure CLI- ```az mysql flexible-server connect``` opdracht. Met deze opdracht kunt u de verbinding met uw database server testen, een snelle start database maken en query's rechtstreeks op uw server uitvoeren zonder dat u mysql.exe of MySQL Workbench hoeft te installeren.  U kunt ook de opdracht uitvoeren in een interactieve modus gebruiken om meerdere query's uit te voeren.

Voer het volgende script uit om de verbinding met de data base te testen en te valideren vanuit uw ontwikkel omgeving.

```azurecli-interactive
az mysql flexible-server connect -n <servername> -u <username> -p <password> -d <databasename>
```
**Voorbeeld:**
```azurecli-interactive
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase
```
De volgende uitvoer wordt weer geven voor een geslaagde verbinding:

```output
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Connecting to newdatabase database.
Successfully connected to mysqldemoserver1.
```
Als de verbinding is mislukt, probeert u de volgende oplossingen:
- Controleer of poort 3306 is geopend op de client computer.
- Als de gebruikers naam en het wacht woord van de server beheerder juist zijn
- Als u de firewall regel voor uw client computer hebt geconfigureerd
- Als u uw server hebt geconfigureerd met persoonlijke toegang in virtuele netwerken, zorgt u ervoor dat de client computer zich in hetzelfde virtuele netwerk bevindt.

Voer de volgende opdracht uit om één query uit te voeren met behulp van het ```--querytext``` argument ```-q``` .

```azurecli-interactive
az mysql flexible-server connect -n <server-name> -u <username> -p "<password>" -d <database-name> --querytext "<query text>"
```

**Voorbeeld:**
```azurecli-interactive
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase -q "select * from table1;" --output table
```
```az mysql flexible-server connect```Raadpleeg de documentatie [verbinding maken en vragen](connect-azure-cli.md) voor meer informatie over het gebruik van de opdracht.

## <a name="connect-using-mysql-command-line-client"></a>Verbinding maken met de MySQL-opdrachtregel-client

Als u een flexibele server hebt gemaakt met Persoonlijke toegang (VNet-integratie), moet u verbinding maken met uw server vanuit een resource binnen hetzelfde virtuele netwerk als uw server. U kunt een virtuele machine maken en toevoegen aan het virtuele netwerk dat is gemaakt met uw flexibele server. Raadpleeg de documentatie over het configureren van [persoonlijke toegang](how-to-manage-virtual-network-portal.md) voor meer informatie.

Als u uw flexibele server met Openbare toegang (toegestane IP-adressen) hebt gemaakt, kunt u uw lokale IP-adres toevoegen aan de lijst met firewallregels op uw server. Raadpleeg de [documentatie over het maken of beheren van firewall regels](how-to-manage-firewall-portal.md) voor stapsgewijze instructies.

U kunt [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) of [MySQL Workbench](./connect-workbench.md) gebruiken om vanuit uw lokale omgeving verbinding met de server te maken. Azure Database for MySQL flexibele server ondersteunt het verbinden van uw client toepassingen met de MySQL-service met behulp van Transport Layer Security (TLS), voorheen bekend als Secure Sockets Layer (SSL). TLS is een industrie standaard protocol dat versleutelde netwerk verbindingen tussen uw database server-en client toepassingen waarborgt, zodat u kunt voldoen aan de nalevings vereisten. Als u verbinding wilt maken met uw flexibele MySQL-server, moet u het [open bare SSL-certificaat](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) voor verificatie van de certificerings instantie downloaden. Zie [verbinding maken met Azure database for MySQL-flexibele server met versleutelde verbindingen](how-to-connect-tls-ssl.md) voor meer informatie over het maken van verbinding met versleutelde verbindingen of het uitschakelen van SSL.

In het volgende voor beeld ziet u hoe u verbinding maakt met uw flexibele server met behulp van de MySQL-opdracht regel interface. U moet eerst de MySQL-opdracht regel installeren als deze nog niet is geïnstalleerd. U downloadt het DigiCertGlobalRootCA-certificaat dat vereist is voor SSL-verbindingen. Gebruik de instelling--SSL-modus = vereist connection string om TLS/SSL-certificaat verificatie af te dwingen. Geef het pad van het lokale certificaat bestand door aan de para meter--ssl-ca. Vervang de waarden door uw werkelijke servernaam en wachtwoord.

```bash
sudo apt-get install mysql-client
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl-mode=REQUIRED --ssl-ca=DigiCertGlobalRootCA.crt.pem
```

Als u uw flexibele server hebt ingericht met behulp van **open bare toegang**, kunt u ook [Azure Cloud shell](https://shell.azure.com/bash) gebruiken om verbinding te maken met uw flexibele server met behulp van een vooraf geïnstalleerde mysql-client, zoals hieronder wordt weer gegeven:

Als u Azure Cloud Shell wilt gebruiken om verbinding te maken met uw flexibele server, moet u toegang tot het netwerk van Azure Cloud Shell op uw flexibele server toestaan. Hiertoe gaat u naar de Blade **netwerk** op Azure portal voor uw flexibele mysql-server en schakelt u het selectie vakje onder **firewall** in, waarbij ' open bare toegang vanaf een Azure-service in azure op deze server toestaan ' wordt weer gegeven in de onderstaande scherm afbeelding. Klik vervolgens op Opslaan om de instelling te behouden.

 > :::image type="content" source="./media/quickstart-create-server-portal/allow-access-to-any-azure-service.png" alt-text="Scherm afbeelding die laat zien hoe u Azure Cloud Shell toegang tot MySQL flexibele server voor open bare netwerk configuratie kunt toestaan.":::
 
 
> [!NOTE]
> Het controleren of het toegankelijk is voor het gebruik **van een Azure-service in azure naar deze server** moet alleen worden gebruikt voor ontwikkelen of testen. Hiermee wordt de firewall geconfigureerd om verbindingen toe te staan van IP-adressen die zijn toegewezen aan een Azure-service of-Asset, met inbegrip van verbindingen van de abonnementen van andere klanten.

Klik op **proberen** om de Azure Cloud shell te starten en gebruik de volgende opdrachten om verbinding te maken met uw flexibele server. Gebruik de servernaam, de gebruikersnaam en het wachtwoord in de opdracht. 

```azurecli-interactive
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl=true --ssl-ca=DigiCertGlobalRootCA.crt.pem
```
> [!IMPORTANT]
> Wanneer u verbinding maakt met uw flexibele server met behulp van Azure Cloud Shell, moet u de para meter--SSL = True gebruiken en niet-SSL-modus = vereist.
> De primaire reden hiervoor is Azure Cloud Shell worden geleverd met een vooraf geïnstalleerde mysql.exe-client van een MariaDB-distributie waarvoor--SSL-para meter vereist is, terwijl de mysql-client van de distributie van Oracle vereist--SSL-modus para meter.

Als het volgende fout bericht wordt weer gegeven wanneer u verbinding maakt met uw flexibele server met behulp van de eerder genoemde opdracht, hebt u de firewall regel niet meer ingesteld met de optie ' open bare toegang vanaf een Azure-service toestaan in azure naar deze server ', maar eerder wel of niet opgeslagen. Stel de firewall opnieuw in en probeer het opnieuw.

FOUT 2002 (HY000): kan geen verbinding maken met MySQL-server op <servername> (115)

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze resources niet voor een andere Quickstart of zelfstudie nodig hebt, kunt u ze verwijderen met de volgende opdracht:

```azurecli-interactive
az group delete --name myresourcegroup
```

Als u alleen de ene zojuist gemaakte server wilt verwijderen, kunt u de opdracht `az mysql server delete` uitvoeren.

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Volgende stappen

>[!div class="nextstepaction"]
> [Verbinding maken en query's uitvoeren met behulp van Azure cli](connect-azure-cli.md) 
>  [Verbinding maken met Azure database for MySQL-flexibele server met versleutelde verbindingen](how-to-connect-tls-ssl.md) 
>  [Een php-web-app (Laravel) bouwen met MySQL](tutorial-php-database-app.md)

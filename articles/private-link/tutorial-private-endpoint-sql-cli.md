---
title: 'Zelf studie: verbinding maken met een Azure SQL-Server met behulp van een Azure-privé-eind punt-Azure CLI'
description: Gebruik deze zelfstudie om te leren hoe u een Azure SQL-server maakt met een privé-eindpunt met behulp van Azure CLI
services: private-link
author: asudbring
ms.service: private-link
ms.topic: tutorial
ms.date: 11/03/2020
ms.author: allensu
ms.custom: fasttrack-edit
ms.openlocfilehash: a5562c5f40a321f5737fea73f6d7964b402953cb
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889209"
---
# <a name="tutorial-connect-to-an-azure-sql-server-using-an-azure-private-endpoint---azure-cli"></a>Zelf studie: verbinding maken met een Azure SQL-Server met behulp van een Azure-privé-eind punt-Azure CLI

Een privé-eindpunt in Azure is de fundamentele bouwsteen voor een Private Link in Azure. Het biedt Azure-resources, zoals virtuele machines, de mogelijkheid om Private Link-resources te gebruiken om privé met elkaar communiceren.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een virtueel netwerk en een Bastion-host maken
> * Hiermee maakt u een virtuele machine.
> * Maak een Azure SQL-server en een privé-eindpunt.
> * Connectiviteit met het privé-eindpunt van SQL-server testen.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Meld u aan bij de Azure-portal en controleer of uw abonnement actief is door `az login` uit te voeren.
* Controleer uw Azure CLI-versie in een terminal- of opdrachtvenster door `az --version` uit te voeren. Bekijk de [meest recente releaseopmerkingen](/cli/azure/release-notes-azure-cli?tabs=azure-cli) voor de nieuwste versie.
  * Als u de nieuwste versie niet hebt, werkt u uw installatie bij door de [installatiehandleiding voor uw besturingssysteem of platform](/cli/azure/install-azure-cli) te volgen.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

Maak een resourcegroep maken met [az group create](/cli/azure/group#az_group_create):

* Met de naam **CreateSQLEndpointTutorial-rg**. 
* Op de locatie **eastus**.

```azurecli-interactive
az group create \
    --name CreateSQLEndpointTutorial-rg \
    --location eastus
```

## <a name="create-a-virtual-network-and-bastion-host"></a>Een virtueel netwerk en Bastion-host maken

In deze sectie leert u een virtueel netwerk, subnet en Bastion-host te maken. 

De Bastion-host wordt gebruikt om veilig verbinding te maken met de virtuele machine om het privé-eindpunt te testen.

Maak een virtueel netwerk met [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create)

* Met de naam **myVNet**.
* Adresvoorvoegsel van **10.0.0.0/16**.
* Subnet met de naam **myBackendSubnet**.
* Subnetvoorvoegsel van **10.0.0.0/24**.
* In de resourcegroep **CreateSQLEndpointTutorial-rg**.
* Locatie **VS - Oost**.

```azurecli-interactive
az network vnet create \
    --resource-group CreateSQLEndpointTutorial-rg\
    --location eastus \
    --name myVNet \
    --address-prefixes 10.0.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.0.0.0/24
```

Werk het subnet bij om het beleid voor het privé-eindpuntnetwerk uit te schakelen met [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
    --name myBackendSubnet \
    --resource-group CreateSQLEndpointTutorial-rg \
    --vnet-name myVNet \
    --disable-private-endpoint-network-policies true
```

Gebruik [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) om een openbaar IP-adres voor de Bastion-host te maken:

* Maak een standaard zoneredundant openbaar IP-adres met de naam **myBastionIP**.
* In **CreateSQLEndpointTutorial-rg**.

```azurecli-interactive
az network public-ip create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name myBastionIP \
    --sku Standard
```

Gebruik [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) om een bastionsubnet te maken:

* met de naam **AzureBastionSubnet**.
* Adresvoorvoegsel van **10.0.1.0/24**.
* In virtueel netwerk **myVnet**.
* In resourcegroep **CreateSQLEndpointTutorial-rg**.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.0.1.0/24
```

Gebruik [az network bastion create](/cli/azure/network/bastion#az-network-bastion-create) om een Bastion-host te maken:

* met de naam **myBastionHost**.
* In **CreateSQLEndpointTutorial-rg**.
* Gekoppeld aan het openbare IP-adres **myBastionIP**.
* Gekoppeld aan het virtuele netwerk **myVNet**.
* Op de locatie **eastus**.

```azurecli-interactive
az network bastion create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

De Azure Bastion kan een paar minuten nodig hebben om te implementeren.

## <a name="create-test-virtual-machine"></a>Virtuele testmachine maken

In deze sectie maakt u een virtuele machine die wordt gebruikt om het persoonlijke eindpunt te testen.

Maak een VM met  [az vm create](/cli/azure/vm#az_vm_create). Wanneer u hierom wordt gevraagd, geeft u een wachtwoord op dat moet worden gebruikt als de referenties voor de VM:

* Met de naam **myVM**.
* In **CreateSQLEndpointTutorial-rg**.
* In netwerk **myVNet**.
* In subnet **myBackendSubnet**.
* Serverinstallatiekopie **Win2019Datacenter**.

```azurecli-interactive
az vm create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name myVM \
    --image Win2019Datacenter \
    --public-ip-address "" \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --admin-username azureuser
```

## <a name="create-an-azure-sql-server"></a>Een Azure SQL-server maken

In deze sectie maakt u een SQL-server en een database.

Gebruik [az sql server create](/cli/azure/sql/server#az_sql_server_create) om een SQL-server te maken:

* Vervang **\<sql-server-name>** door uw unieke servernaam.
* Vervang **\<your-password>** door uw wachtwoord.
* In **CreateSQLEndpointTutorial-rg**.
* In regio **eastus**.

```azurecli-interactive
az sql server create \
    --name <sql-server-name> \
    --resource-group CreateSQLEndpointTutorial-rg \
    --location eastus \
    --admin-user sqladmin \
    --admin-password <your-password>
```

Gebruik [az sql db create](/cli/azure/sql/db#az_sql_db_create) om een database te maken:

* Met de naam **myDataBase**.
* In **CreateSQLEndpointTutorial-rg**.
* Vervang **\<sql-server-name>** door uw unieke servernaam.

```azurecli-interactive
az sql db create \
    --resource-group CreateSQLEndpointTutorial-rg  \
    --server <sql-server-name> \
    --name myDataBase \
    --sample-name AdventureWorksLT
```

## <a name="create-private-endpoint"></a>Privé-eindpunt maken

In deze sectie maakt u het privé-eindpunt.

Gebruik [az sql server list](/cli/azure/sql/server#az_sql_server_list) om de resource-id van de SQL-server in een shell-variabele te plaatsen.

Gebruik [az network private-endpoint create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) om het eindpunt en de verbinding te maken:

* Met de naam **myPrivateEndpoint**.
* In resourcegroep **CreateSQLEndpointTutorial-rg**.
* In virtueel netwerk **myVnet**.
* In subnet **myBackendSubnet**.
* Verbinding met de naam **myConnection**.

```azurecli-interactive
id=$(az sql server list \
    --resource-group CreateSQLEndpointTutorial-rg \
    --query '[].[id]' \
    --output tsv)

az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group CreateSQLEndpointTutorial-rg \
    --vnet-name myVNet --subnet myBackendSubnet \
    --private-connection-resource-id $id \
    --group-ids sqlServer \
    --connection-name myConnection  
```

## <a name="configure-the-private-dns-zone"></a>Privé-DNS-zone configureren

In dit gedeelte maakt en configureert u de privé-DNS-zone met behulp van [az network private-dns zone create](/cli/azure/network/private-dns/zone#ext_privatedns_az_network_private_dns_zone_create).  

U gebruikt [az network private-dns link vnet create](/cli/azure/network/private-dns/link/vnet#ext_privatedns_az_network_private_dns_link_vnet_create) om de koppeling van het virtuele netwerk met de DNS-zone te maken.

U maakt een DNS-zonegroep met [az network private-endpoint dns-zone-group create](/cli/azure/network/private-endpoint/dns-zone-group#az_network_private_endpoint_dns_zone_group_create).

* Zone met de naam **privatelink.database.windows.net**
* In virtueel netwerk **myVnet**.
* In resourcegroep **CreateSQLEndpointTutorial-rg**.
* DNS-koppeling met de naam **myDNSLink**.
* Gekoppeld aan **myPrivateEndpoint**.
* Zonegroep met de naam **MyZoneGroup**.

```azurecli-interactive
az network private-dns zone create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name "privatelink.database.windows.net"

az network private-dns link vnet create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --zone-name "privatelink.database.windows.net" \
    --name MyDNSLink \
    --virtual-network myVNet \
    --registration-enabled false

az network private-endpoint dns-zone-group create \
   --resource-group CreateSQLEndpointTutorial-rg \
   --endpoint-name myPrivateEndpoint \
   --name MyZoneGroup \
   --private-dns-zone "privatelink.database.windows.net" \
   --zone-name sql
```

## <a name="test-connectivity-to-private-endpoint"></a>Privé-eindpuntconnectiviteit testen

In deze sectie gebruikt u de virtuele machine die u in de vorige stap hebt gemaakt, om verbinding te maken met de SQL-server via het privé-eindpunt.

1. Meld u aan bij [Azure Portal](https://portal.azure.com) 
 
2. Selecteer **Resourcegroepen** in het linkernavigatievenster.

3. Selecteer **CreateSQLEndpointTutorial-rg**.

4. Selecteer **myVM**.

5. Selecteer op de overzichtspagina voor **myVM** de optie **Verbinding maken** en daarna **Bastion**.

6. Selecteer de blauwe knop **Bastion gebruiken**.

7. Voer de gebruikersnaam en het wachtwoord in die u hebt ingevoerd bij het maken van de virtuele machine.

8. Open Windows PowerShell op de server nadat u verbinding hebt gemaakt.

9. Voer `nslookup <sqlserver-name>.database.windows.net` in. Vervang **\<sqlserver-name>** door de naam van de SQL-server die u hebt gemaakt in de vorige stappen.  U ontvangt een bericht dat er ongeveer als volgt uitziet:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mysqlserver8675.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:  mysqlserver8675.database.windows.net
    ```

    Er wordt een privé-IP-adres **10.0.0.5** geretourneerd voor de naam van de SQL-server.  Dit adres bevindt zich in het subnet van het virtuele netwerk dat u eerder hebt gemaakt.


10. Installeer [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?preserve-view=true&view=sql-server-2017) op **myVM**.

11. Open **SQL Server Management Studio**.

12. Typ of selecteer in **Verbinding maken met server** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Servertype | Selecteer **Database Engine**.|
    | Servernaam | Voer **\<sql-server-name>.database.windows.net** in |
    | Verificatie | Selecteer **SQL Server-verificatie**. |
    | Gebruikersnaam | Voer de gebruikersnaam in die u hebt opgegeven tijdens het maken van de server |
    | Wachtwoord | Voer het wachtwoord in dat u hebt opgegeven tijdens het maken van de server |
    | Wachtwoord onthouden | Selecteer **Ja**. |

13. Selecteer **Verbinden**.

14. Blader in het linkermenu door databases.

15. (Optioneel) U kunt **mysqldatabase** maken of er een query op uitvoeren.

16. Verbreek de bastionverbinding met **myVM**. 

## <a name="clean-up-resources"></a>Resources opschonen 

Wanneer u klaar bent met het privé-eindpunt, de SQL-server en de VM, verwijdert u de resourcegroep en alle resources die deze bevat: 

```azurecli-interactive
az group delete \
    --name CreateSQLEndpointTutorial-rg
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende gemaakt:

* Een virtueel netwerk en Bastion-host.
* Een virtuele machine.
* Een Azure SQL-server met een privé-eindpunt.

U hebt de virtuele machine gebruikt om de connectiviteit met de SQL-server via het privé-eindpunt veilig te testen.

Als volgende stap hebt u mogelijk ook geïnteresseerd in de web- **app met een particuliere verbinding met het Azure SQL database** Architecture-scenario, waarmee een webtoepassing buiten het virtuele netwerk wordt verbonden met het persoonlijke eind punt van een Data Base.
> [!div class="nextstepaction"]
> [Web-app met persoonlijke connectiviteit met Azure SQL database](/azure/architecture/example-scenario/private-web-app/private-web-app)
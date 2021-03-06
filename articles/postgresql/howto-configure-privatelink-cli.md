---
title: Private Link - Azure CLI - Azure Database for PostgreSQL - Enkele server
description: Meer informatie over het configureren van private link voor Azure Database for PostgreSQL- Enkele server van Azure CLI
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 01/09/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 410da33b531524f4a6458df13a89807fedd739a2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773329"
---
# <a name="create-and-manage-private-link-for-azure-database-for-postgresql---single-server-using-cli"></a>Een Private Link voor Azure Database for PostgreSQL maken en beheren met behulp van CLI

Een privé-eindpunt is de fundamentele bouwsteen voor een Private Link in Azure. Het biedt Azure-resources, zoals virtuele machines, de mogelijkheid om Private Link-resources te gebruiken om privé met elkaar communiceren. In dit artikel leert u hoe u de Azure CLI gebruikt om een VM te maken in een Azure Virtual Network en een Azure Database for PostgreSQL Single-server met een Privé-eindpunt van Azure.

> [!NOTE]
> De private link-functie is alleen beschikbaar voor Azure Database for PostgreSQL servers in de prijscategorie Algemeen of geoptimaliseerd voor geheugen. Zorg ervoor dat de databaseserver zich in een van deze prijslagen.

## <a name="prerequisites"></a>Vereisten

Als u deze handleiding wilt door nemen, hebt u het volgende nodig:

- Een [Azure Database for PostgreSQL server en database](quickstart-create-server-database-azure-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om Azure CLI lokaal te installeren en te gebruiken, moet u voor deze snelstart versie 2.0.28 of hoger van Azure CLI uitvoeren. Voer `az --version` uit om na te gaan welke versie er is geïnstalleerd. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) voor installatie- of upgrade-informatie.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Voordat u een resource kunt maken, moet u een resourcegroep maken die het Virtual Network host. Maak een resourcegroep maken met [az group create](/cli/azure/group). In dit voorbeeld wordt een resourcegroep met de *naam myResourceGroup gemaakt* op de *locatie westeurope:*

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

## <a name="create-a-virtual-network"></a>Een Virtual Network maken
Maak een Virtual Network met [az network vnet create](/cli/azure/network/vnet). In dit voorbeeld wordt een standaard Virtual Network gemaakt met de naam *myVirtualNetwork* met één subnet genaamd *mySubnet*:

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```

## <a name="disable-subnet-private-endpoint-policies"></a>Beleid voor privé-eindpunten van subnet uitschakelen 
Azure implementeert resources in een subnet binnen een virtueel netwerk, dus u moet het subnet maken of bijwerken om beleid voor privé-eindpuntnetwerk [uit te schakelen.](../private-link/disable-private-endpoint-network-policy.md) Update een subnet-configuratie met de naam *mySubnet* met [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update):

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```
## <a name="create-the-vm"></a>De virtuele machine maken 
Maak een VM met az vm create. Wanneer u hierom wordt gevraagd, geeft u een wachtwoord op dat moet worden gebruikt als de aanmeldingsreferenties voor de VM. In het volgende voorbeeld wordt een VM gemaakt met de naam *myVm*: 
```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```

 Noteer het openbare IP-adres van de VM. U gebruikt dit adres om in de volgende stap verbinding te maken met de virtuele machine via internet.

## <a name="create-an-azure-database-for-postgresql---single-server"></a>Een Azure Database for PostgreSQL maken - Enkele server 
Maak een Azure Database for PostgreSQL met de opdracht az postgres server create. Houd er rekening mee dat de naam van uw PostgreSQL-server uniek moet zijn in Azure. Vervang daarom de tijdelijke aanduidingswaarde door uw eigen unieke waarden die u hierboven hebt gebruikt: 

```azurecli-interactive
# Create a server in the resource group 
az postgres server create \
--name mydemoserver \
--resource-group myresourcegroup \
--location westeurope \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2
```

## <a name="create-the-private-endpoint"></a>Privé-eindpunt maken 
Maak een privé-eindpunt voor de PostgreSQL-server in uw Virtual Network: 

```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id $(az resource show -g myResourcegroup -n mydemoserver --resource-type "Microsoft.DBforPostgreSQL/servers" --query "id" -o tsv) \    
    --group-id postgresqlServer \  
    --connection-name myConnection  
 ```

## <a name="configure-the-private-dns-zone"></a>Privé-DNS-zone configureren 
Maak een Privé-DNS Zone for PostgreSQL-serverdomein en maak een koppelingskoppeling met de Virtual Network. 

```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.postgres.database.azure.com" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.postgres.database.azure.com"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the Azure database for PostgreSQL name 
 
 
#Create DNS records 
az network private-dns record-set a create --name myserver --zone-name privatelink.postgres.database.azure.com --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name myserver --zone-name privatelink.postgres.database.azure.com --resource-group myResourceGroup -a <Private IP Address>
```

> [!NOTE] 
> De FQDN in de DNS-instelling van de klant wordt niet omgezet naar het geconfigureerde particuliere IP-adres. U moet een DNS-zone instellen voor de geconfigureerde FQDN, zoals hier [wordt weergegeven.](../dns/dns-operations-recordsets-portal.md)

> [!NOTE]
> In sommige gevallen zijn Azure Database for PostgreSQL en het VNet-subnet in verschillende abonnementen. In deze gevallen moet u de volgende configuraties controleren:
> - Zorg ervoor dat voor beide abonnementen de **resourceprovider Microsoft.DBforPostgreSQL** is geregistreerd. Raadpleeg resourceproviders voor [meer informatie.](../azure-resource-manager/management/resource-providers-and-types.md)

## <a name="connect-to-a-vm-from-the-internet"></a>Verbinding maken met een virtuele machine via internet

Maak als volgt verbinding met de VM *myVm* van Internet:

1. Voer in de zoekbalk van de portal *myVm* in.

1. Selecteer de knop **Verbinding maken**. Na het selecteren van de knop **Verbinden** wordt **Verbinden met virtuele machine** geopend.

1. Selecteer **RDP-bestand downloaden**. In Azure wordt een *RDP*-bestand (Remote Desktop Protocol) gemaakt en het bestand wordt gedownload naar de computer.

1. Open het *downloaded.rdp*-bestand.

    1. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd.

    1. Voer de gebruikersnaam en het wachtwoord in die u hebt opgegeven bij het maken van de virtuele machine.

        > [!NOTE]
        > Mogelijk moet u **Meer opties** > **Een ander account gebruiken** selecteren om de referenties op te geven die u hebt ingevoerd tijdens het maken van de VM.

1. Selecteer **OK**.

1. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Als er een certificaatwaarschuwing wordt weergegeven, selecteert u **Ja** of **Doorgaan**.

1. Wanneer het VM-bureaublad wordt weergegeven, minimaliseert u het om terug te gaan naar het lokale bureaublad.  

## <a name="access-the-postgresql-server-privately-from-the-vm"></a>Privé toegang tot de PostgreSQL-server vanaf de VM

1. Open PowerShell in het extern bureaublad van *myVM*.

2. Voer  `nslookup mydemopostgresserver.privatelink.postgres.database.azure.com` in. 

   U ontvangt een bericht dat er ongeveer als volgt uitziet:

   ```azurepowershell
   Server:  UnKnown
   Address:  168.63.129.16
   Non-authoritative answer:
   Name:    mydemopostgresserver.privatelink.postgres.database.azure.com
   Address:  10.1.3.4
   ```

3. Test de Private Link-verbinding voor de PostgreSQL-server met behulp van een beschikbare client. In het volgende voorbeeld wordt [Azure Data Studio gebruikt](/sql/azure-data-studio/download) om de bewerking uit te voeren.

4. Voer **in Nieuwe verbinding** de volgende gegevens in of selecteer deze:

   | Instelling | Waarde |
   | ------- | ----- |
   | Servertype| Selecteer **PostgreSQL**.|
   | Servernaam| Selecteer *mydemopostgresserver.privatelink.postgres.database.azure.com* |
   | Gebruikersnaam | Voer de gebruikersnaam in die wordt opgegeven tijdens het maken van de username@servername PostgreSQL-server. |
   |Wachtwoord |Voer een wachtwoord in dat is opgegeven tijdens het maken van de PostgreSQL-server. |
   |SSL|Selecteer **Vereist.**|
   ||

5. Selecteer Verbinding maken.

6. Blader in het menu aan de linkerkant door databases.

7. (Optioneel) Gegevens maken of opvragen op de postgreSQL-server.

8. Sluit de verbinding met het externe bureaublad met myVm.

## <a name="clean-up-resources"></a>Resources opschonen 
U kunt az group delete gebruiken om de resourcegroep en alle resources die deze bevat te verwijderen, als deze niet meer nodig zijn: 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [Wat is een privé-eindpunt in Azure?](../private-link/private-endpoint-overview.md)

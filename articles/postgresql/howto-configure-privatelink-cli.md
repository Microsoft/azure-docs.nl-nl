---
title: Persoonlijke koppeling-Azure CLI-Azure Database for PostgreSQL-één server
description: Meer informatie over het configureren van een persoonlijke koppeling voor Azure Database for PostgreSQL-één server van Azure CLI
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 01/09/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3452bfee1e9228926bb687d1b9dc7fb26dfff85a
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105642182"
---
# <a name="create-and-manage-private-link-for-azure-database-for-postgresql---single-server-using-cli"></a>Een persoonlijke koppeling voor Azure Database for PostgreSQL-één server maken en beheren met CLI

Een privé-eindpunt is de fundamentele bouwsteen voor een Private Link in Azure. Het biedt Azure-resources, zoals virtuele machines, de mogelijkheid om Private Link-resources te gebruiken om privé met elkaar communiceren. In dit artikel leert u hoe u de Azure CLI gebruikt om een virtuele machine te maken in een Azure-Virtual Network en een Azure Database for PostgreSQL enkele server met een persoonlijk Azure-eind punt.

> [!NOTE]
> De functie voor persoonlijke koppelingen is alleen beschikbaar voor Azure Database for PostgreSQL servers in de prijs Categorieën Algemeen of geoptimaliseerd voor geheugen. Zorg ervoor dat de database server zich in een van deze prijs categorieën bevindt.

## <a name="prerequisites"></a>Vereisten

Als u deze hand leiding wilt door lopen, hebt u het volgende nodig:

- Een [Azure database for postgresql-server en-data base](quickstart-create-server-database-azure-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om Azure CLI lokaal te installeren en te gebruiken, moet u voor deze snelstart versie 2.0.28 of hoger van Azure CLI uitvoeren. Voer `az --version` uit om na te gaan welke versie er is geïnstalleerd. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) voor installatie- of upgrade-informatie.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Voordat u een resource kunt maken, moet u een resourcegroep maken die het Virtual Network host. Maak een resourcegroep maken met [az group create](/cli/azure/group). In dit voor beeld wordt een resource groep met de naam *myResourceGroup* gemaakt op de locatie *Europa West* :

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
Azure implementeert resources in een subnet binnen een virtueel netwerk, dus u moet het subnet maken of bijwerken om beleid voor privé-eindpunt [netwerk](../private-link/disable-private-endpoint-network-policy.md)uit te scha kelen. Update een subnet-configuratie met de naam *mySubnet* met [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update):

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

 Noteer het open bare IP-adres van de virtuele machine. U gebruikt dit adres om in de volgende stap verbinding te maken met de virtuele machine via internet.

## <a name="create-an-azure-database-for-postgresql---single-server"></a>Een Azure Database for PostgreSQL-één-server maken 
Maak een Azure Database for PostgreSQL met de opdracht AZ post gres server Create. Houd er rekening mee dat de naam van uw PostgreSQL-server uniek moet zijn in azure, dus Vervang de waarde van de tijdelijke aanduiding door uw eigen unieke waarden die u hierboven hebt gebruikt: 

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
Maak een persoonlijk eind punt voor de PostgreSQL-server in uw Virtual Network: 

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
Maak een Privé-DNS zone voor het PostgreSQL-Server domein en maak een koppelings koppeling met de Virtual Network. 

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
> De FQDN in de DNS-instelling van de klant wordt niet omgezet naar het geconfigureerde particuliere IP-adres. U moet een DNS-zone voor de geconfigureerde FQDN instellen, zoals [hier](../dns/dns-operations-recordsets-portal.md)wordt weer gegeven.

> [!NOTE]
> In sommige gevallen bevinden de Azure Database for PostgreSQL en het VNet-subnet zich in verschillende abonnementen. In deze gevallen moet u ervoor zorgen dat u de volgende configuraties hebt:
> - Zorg ervoor dat voor beide abonnementen de resource provider **micro soft. DBforPostgreSQL** is geregistreerd. Raadpleeg [resource providers](../azure-resource-manager/management/resource-providers-and-types.md)voor meer informatie.

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

## <a name="access-the-postgresql-server-privately-from-the-vm"></a>De PostgreSQL-server privé openen vanuit de VM

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

3. Test de verbinding van de persoonlijke verbinding voor de PostgreSQL-server met behulp van elke beschik bare client. In het volgende voor beeld wordt [Azure Data Studio](/sql/azure-data-studio/download) gebruikt om de bewerking uit te voeren.

4. In **nieuwe verbinding** voert u de volgende gegevens in of selecteert u deze:

   | Instelling | Waarde |
   | ------- | ----- |
   | Servertype| Selecteer **postgresql**.|
   | Servernaam| *Mydemopostgresserver.privatelink.postgres.database.Azure.com* selecteren |
   | Gebruikersnaam | Voer de gebruikers naam in username@servername die wordt opgegeven tijdens het maken van de postgresql-server. |
   |Wachtwoord |Voer een wacht woord in dat u hebt opgegeven tijdens het maken van de PostgreSQL-server. |
   |SSL|Selecteer **vereist**.|
   ||

5. Selecteer Verbinding maken.

6. Blader in het menu aan de linkerkant door databases.

7. Eventueel Gegevens van de postgreSQL-server maken of er een query op uitvoeren.

8. Sluit de verbinding met extern bureau blad met myVm.

## <a name="clean-up-resources"></a>Resources opschonen 
U kunt az group delete gebruiken om de resourcegroep en alle resources die deze bevat te verwijderen, als deze niet meer nodig zijn: 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [Wat is Azure persoonlijk eind punt](../private-link/private-endpoint-overview.md) ?

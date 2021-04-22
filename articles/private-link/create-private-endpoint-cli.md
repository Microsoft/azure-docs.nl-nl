---
title: Quickstart – Een Azure-privé-eindpunt maken met behulp van Azure CLI
description: Gebruik deze quickstart om te leren hoe u een privé-eindpunt maakt met Azure CLI.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 11/07/2020
ms.author: allensu
ms.openlocfilehash: 036052dc45b8d029dac6e137b3a878b75e6e015c
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873393"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-cli"></a>Quickstart: Een privé-eindpunt maken met behulp van Azure CLI

Ga aan de slag met Azure Private Link door een privé-eindpunt te gebruiken om veilig verbinding te maken met een Azure-web-app.

In deze quickstart maakt u een privé-eindpunt voor een Azure-web-app en implementeert u een virtuele machine om de privé-verbinding te testen.  

Er kunnen privé-eindpunten worden gemaakt voor verschillende soorten Azure-services, zoals Azure SQL en Azure Storage.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Een Azure-web-app met een **PremiumV2**- of hoger app-serviceplan geïmplementeerd in uw Azure-abonnement.  
    * Zie voor meer informatie en een voorbeeld [Quickstart: Een ASP.NET Core-web-app maken in Azure](../app-service/quickstart-dotnetcore.md). 
    * Zie voor een gedetailleerde zelfstudie over het maken van een web-app en een eindpunt [Zelfstudie: Verbinding maken met een web-app met behulp van een privé-eindpunt in Azure](tutorial-private-endpoint-webapp-portal.md).
* Meld u aan bij de Azure-portal en controleer of uw abonnement actief is door `az login` uit te voeren.
* Controleer uw Azure CLI-versie in een terminal- of opdrachtvenster door `az --version` uit te voeren. Bekijk de [meest recente releaseopmerkingen](/cli/azure/release-notes-azure-cli?tabs=azure-cli) voor de nieuwste versie.
  * Als u de nieuwste versie niet hebt, werkt u uw installatie bij door de [installatiehandleiding voor uw besturingssysteem of platform](/cli/azure/install-azure-cli) te volgen.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

Maak een resourcegroep maken met [az group create](/cli/azure/group#az_group_create):

* Met de naam **CreatePrivateEndpointQS-rg**. 
* Op de locatie **eastus**.

```azurecli-interactive
az group create \
    --name CreatePrivateEndpointQS-rg \
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
* In de resourcegroep **CreatePrivateEndpointQS-rg**.
* Locatie **VS - Oost**.

```azurecli-interactive
az network vnet create \
    --resource-group CreatePrivateEndpointQS-rg\
    --location eastus \
    --name myVNet \
    --address-prefixes 10.0.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.0.0.0/24
```

Werk het subnet bij om het beleid voor het privé-eindpuntnetwerk uit te schakelen met [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update):

```azurecli-interactive
az network vnet subnet update \
    --name myBackendSubnet \
    --resource-group CreatePrivateEndpointQS-rg \
    --vnet-name myVNet \
    --disable-private-endpoint-network-policies true
```

Gebruik [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) om een openbaar IP-adres voor de Bastion-host te maken:

* Maak een standaard zoneredundant openbaar IP-adres met de naam **myBastionIP**.
* In **CreatePrivateEndpointQS-rg**.

```azurecli-interactive
az network public-ip create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name myBastionIP \
    --sku Standard
```

Gebruik [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) om een bastionsubnet te maken:

* met de naam **AzureBastionSubnet**.
* Adresvoorvoegsel van **10.0.1.0/24**.
* In virtueel netwerk **myVnet**.
* In de resourcegroep **CreatePrivateEndpointQS-rg**.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.0.1.0/24
```

Gebruik [az network bastion create](/cli/azure/network/bastion#az_network_bastion_create) om een Bastion-host te maken:

* met de naam **myBastionHost**.
* In **CreatePrivateEndpointQS-rg**.
* Gekoppeld aan het openbare IP-adres **myBastionIP**.
* Gekoppeld aan het virtuele netwerk **myVNet**.
* Op de locatie **eastus**.

```azurecli-interactive
az network bastion create \
    --resource-group CreatePrivateEndpointQS-rg \
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
* In **CreatePrivateEndpointQS-rg**.
* In netwerk **myVNet**.
* In subnet **myBackendSubnet**.
* Serverinstallatiekopie **Win2019Datacenter**.

```azurecli-interactive
az vm create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name myVM \
    --image Win2019Datacenter \
    --public-ip-address "" \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --admin-username azureuser
```

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="create-private-endpoint"></a>Privé-eindpunt maken

In dit gedeelte maakt u het privé-eindpunt.

Gebruik [az webapp list](/cli/azure/webapp#az_webapp_list) om de resource-id van de web-app die u eerder hebt gemaakt in een shellvariabele te plaatsen.

Gebruik [az network private-endpoint create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) om het eindpunt en de verbinding te maken:

* Met de naam **myPrivateEndpoint**.
* In de resourcegroep **CreatePrivateEndpointQS-rg**.
* In virtueel netwerk **myVnet**.
* In subnet **myBackendSubnet**.
* Verbinding met de naam **myConnection**.
* Uw web-app **\<webapp-resource-group-name>** .

```azurecli-interactive
id=$(az webapp list \
    --resource-group <webapp-resource-group-name> \
    --query '[].[id]' \
    --output tsv)

az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group CreatePrivateEndpointQS-rg \
    --vnet-name myVNet --subnet myBackendSubnet \
    --private-connection-resource-id $id \
    --group-id sites \
    --connection-name myConnection  
```

## <a name="configure-the-private-dns-zone"></a>Privé-DNS-zone configureren

In dit gedeelte maakt en configureert u de privé-DNS-zone met behulp van [az network private-dns zone create](/cli/azure/network/private-dns/zone#az_network_private_dns_zone_create).  

U gebruikt [az network private-dns link vnet create](/cli/azure/network/private-dns/link/vnet#az_network_private_dns_link_vnet_create) om de koppeling van het virtuele netwerk met de DNS-zone te maken.

U maakt een DNS-zonegroep met [az network private-endpoint dns-zone-group create](/cli/azure/network/private-endpoint/dns-zone-group#az_network_private_endpoint_dns_zone_group_create).

* Zone met de naam **privatelink.azurewebsites.net**
* In virtueel netwerk **myVnet**.
* In de resourcegroep **CreatePrivateEndpointQS-rg**.
* DNS-koppeling met de naam **myDNSLink**.
* Gekoppeld aan **myPrivateEndpoint**.
* Zonegroep met de naam **MyZoneGroup**.

```azurecli-interactive
az network private-dns zone create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name "privatelink.azurewebsites.net"

az network private-dns link vnet create \
    --resource-group CreatePrivateEndpointQS-rg \
    --zone-name "privatelink.azurewebsites.net" \
    --name MyDNSLink \
    --virtual-network myVNet \
    --registration-enabled false

az network private-endpoint dns-zone-group create \
   --resource-group CreatePrivateEndpointQS-rg \
   --endpoint-name myPrivateEndpoint \
   --name MyZoneGroup \
   --private-dns-zone "privatelink.azurewebsites.net" \
   --zone-name webapp
```

## <a name="test-connectivity-to-private-endpoint"></a>Privé-eindpuntconnectiviteit testen

In deze sectie gebruikt u de virtuele machine die u in de vorige stap hebt gemaakt, om verbinding te maken met de SQL-server via het privé-eindpunt.

1. Meld u aan bij [Azure Portal](https://portal.azure.com) 
 
2. Selecteer **Resourcegroepen** in het linkernavigatievenster.

3. Selecteer **CreatePrivateEndpointQS-rg**.

4. Selecteer **myVM**.

5. Selecteer op de overzichtspagina voor **myVM** de optie **Verbinding maken** en daarna **Bastion**.

6. Selecteer de blauwe knop **Bastion gebruiken**.

7. Voer de gebruikersnaam en het wachtwoord in die u hebt ingevoerd bij het maken van de virtuele machine.

8. Open Windows PowerShell op de server nadat u verbinding hebt gemaakt.

9. Voer `nslookup <your-webapp-name>.azurewebsites.net` in. Vervang **\<your-webapp-name>** door de naam van de web-app die u in de voorgaande stappen hebt gemaakt.  U ontvangt een bericht dat er ongeveer als volgt uitziet:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.0.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    Een privé IP-adres van **10.0.0.5** wordt voor de naam van de web-app geretourneerd.  Dit adres bevindt zich in het subnet van het virtuele netwerk dat u eerder hebt gemaakt.

10. Open Internet Explorer in de bastionverbinding met **myVM**.

11. Voer de URL van de web-app in: **https://\<your-webapp-name>.azurewebsites.net**.

12. U ziet de standaardpagina voor web-apps als uw toepassing niet is geïmplementeerd:

    :::image type="content" source="./media/create-private-endpoint-portal/web-app-default-page.png" alt-text="Standaardpagina van web-app." border="true":::

13. Verbreek de verbinding met **myVM**.

## <a name="clean-up-resources"></a>Resources opschonen 
Wanneer u klaar bent met het privé-eindpunt en de VM, gebruikt u [az group delete](/cli/azure/group#az_group_delete) om de resourcegroep en alle resources daarin te verwijderen:

```azurecli-interactive
az group delete \
    --name CreatePrivateEndpointQS-rg
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u het volgende gemaakt:

* Een virtueel netwerk en Bastion-host.
* Een virtuele machine.
* Een privé-eindpunt voor een Azure-web-app.

U hebt de virtuele machine gebruikt voor het veilig testen van de connectiviteit van de web-app via het privé-eindpunt.

Zie voor meer informatie over de services die een privé-eindpunt ondersteunen:
> [!div class="nextstepaction"]
> [Beschikbaarheid van Private Link](private-link-overview.md#availability)

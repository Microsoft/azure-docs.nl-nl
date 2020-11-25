---
title: Een NAT-gateway maken en testen - Azure CLI
titlesuffix: Azure Virtual Network NAT
description: In deze zelfstudie ziet u hoe u met behulp van de Azure CLI een NAT-gateway maakt en de NAT-service test
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to test a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/11/2020
ms.author: allensu
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0ec054d55432ad2680314b4ff91a067d37b629d4
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94734324"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-cli-and-test-the-nat-service"></a>Zelfstudie: Een NAT-gateway maken met behulp van de Azure CLI en de NAT-service testen

In deze zelfstudie maakt u een NAT-gateway om uitgaande connectiviteit te bieden voor virtuele machines in Azure. Als u de NAT-gateway wilt testen, implementeert u een virtuele bron- en doelmachine. U gaat de NAT-gateway testen door uitgaande verbindingen te maken met een openbaar IP-adres. Deze verbindingen zijn afkomstig van de bron naar de virtuele doelmachine. In deze zelfstudie worden de bron en het doel in twee verschillende virtuele netwerken in dezelfde resourcegroep geïmplementeerd om het eenvoudiger te maken.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.0.71 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een resourcegroep maken met [az group create](https://docs.microsoft.com/cli/azure/group). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

In het volgende voorbeeld wordt een resourcegroep met de naam **myResourceGroupNAT** gemaakt op de locatie **eastus2**:

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
    
```

## <a name="create-the-nat-gateway"></a>De NAT-gateway maken

### <a name="create-a-public-ip-address"></a>Een openbaar IP-adres maken

Voor toegang tot het openbare internet hebt u een of meer openbare IP-adressen nodig voor de NAT-gateway. Gebruik [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) om in **myResourceGroupNAT** een openbare IP-resource te maken met de naam **myPublicIPsource**.

```azurecli-interactive
  az network public-ip create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPsource \
  --sku standard
  
```

### <a name="create-a-public-ip-prefix"></a>Een openbaar IP-voorvoegsel maken

U kunt een of meer openbare IP-resources, openbare IP-voorvoegsels of beide met NAT-gateway gebruiken. Om dit te illustreren, voegt u een resource voor het openbare IP-voorvoegsel aan dit scenario toe.   Gebruik [az network public-ip prefix create](/cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-create) om in **myResourceGroupNAT** een resource voor het openbare IP-voorvoegsel te maken met de naam **myPublicIPprefixsource**.

```azurecli-interactive
  az network public-ip prefix create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPprefixsource \
  --length 31
  
```

### <a name="create-a-nat-gateway-resource"></a>Een NAT-gatewayresource maken

In deze sectie wordt beschreven hoe u de volgende onderdelen van de NAT-service met de NAT-gatewayresource kunt maken en configureren:
  - Een openbare IP-adresgroep en een openbaar IP-voorvoegsel die moeten worden gebruikt voor uitgaande stromen die worden vertaald door de NAT-gatewayresource.
  - Wijzig de time-out voor inactiviteit van de standaardwaarde van 4 minuten naar 10 minuten.

Maak met [az network nat gateway create](https://docs.microsoft.com/cli/azure/network/nat?view=azure-cli-latest) een globaal Azure NAT-gateway met de naam **myNATgateway**. De opdracht gebruikt zowel het openbare IP-adres **myPublicIP** als het openbare IP-voorvoegsel **myPublicIPprefix**. De opdracht verandert ook de time-out voor inactiviteit in 10 minuten.

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIPsource \
    --public-ip-prefixes myPublicIPprefixsource \
    --idle-timeout 10       
    
  ```

Nu is de NAT-gateway functioneel. Het enige wat u nog hoeft te doen is te configureren welke subnetten van een virtueel netwerk er gebruik van moeten maken.

## <a name="prepare-the-source-for-outbound-traffic"></a>De bron voorbereiden voor uitgaand verkeer

U krijgt stapsgewijze instructies voor het instellen van een volledige testomgeving. U gaat een test instellen met behulp van opensource-hulpprogramma's om de NAT-gateway te controleren. We beginnen met de bron, die de NAT-gateway zal gebruiken die we eerder hebben gemaakt.

### <a name="configure-virtual-network-for-source"></a>Virtueel netwerk configureren voor bron

Voordat u een virtuele machine implementeert en uw NAT-gateway kunt testen, moet u het virtuele netwerk maken.

Maak met [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) in **myResourceGroupNAT** een virtueel netwerk met de naam **myVnet** met een subnet met de naam **mySubnetsource**.  De IP-adresruimte voor het virtuele netwerk is **192.168.0.0/16**. Het subnet binnen het virtuele netwerk is **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --name myVnetsource \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetsource \
    --subnet-prefix 192.168.0.0/24
    
```

### <a name="configure-nat-service-for-source-subnet"></a>NAT-service voor bronsubnet configureren

Gebruik [az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet) om het bronsubnet **mySubnetsource** in het virtuele netwerk **myVnetsource** te configureren voor het gebruik van de specifieke NAT-gatewayresource **myNATgateway**. Met deze opdracht wordt de NAT-service in het opgegeven subnet geactiveerd.

```azurecli-interactive
    az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnetsource \
    --name mySubnetsource \
    --nat-gateway myNATgateway
    
```

Al het uitgaande verkeer naar internetbestemmingen maakt nu gebruik van de NAT-service.  Het is niet nodig om een UDR te configureren.

Voordat we de NAT-gateway kunnen testen, moeten we een bron-VM maken.  We wijzen een openbare IP-adresresource toe als een openbaar IP-adres op exemplaarniveau voor externe toegang tot deze VM. Dit adres wordt alleen gebruikt voor toegang tot de test.  We laten u zien hoe de NAT-service prioriteit krijgt boven andere uitgaande opties.

U kunt deze virtuele machine ook maken zonder een openbaar IP-adres en een andere virtuele machine maken om te gebruiken als jumpbox zonder openbare IP als oefening.

### <a name="create-public-ip-for-source-vm"></a>Een openbare IP voor de bron-VM maken

U maakt een openbaar IP-adres dat gebruikt gaat worden voor toegang tot de bron-VM. Gebruik [az network public ip create](https://docs.microsoft.com/cli/azure/network/public-ip) om in **myResourceGroupNAT** een openbare IP-adresresource te maken met de naam **myPublicIPsourceVM**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPsourceVM \
    --sku standard
    
```

### <a name="create-an-nsg-for-source-vm"></a>Een netwerkbeveiligingsgroep maken voor de bron-VM

Omdat standaard openbare IP-adressen 'standaard beveiligd zijn', moet u een netwerkbeveiligingsgroep (NSG) maken om inkomende toegang voor SSH-toegang toe te staan.  De Azure NAT-service kan de richting van een stroom detecteren. Deze NSG wordt niet gebruikt voor uitgaand verkeer wanneer de NAT-gateway is geconfigureerd in hetzelfde subnet. Gebruik [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) om in **myResourceGroupNAT** een NSG-resource met de naam **myNSGs** te maken.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSGsource 
    
```

### <a name="expose-ssh-endpoint-on-source-vm"></a>SSH-eindpunt beschikbaar maken op de bron-VM

U maakt een regel in de NSG voor SSH-toegang tot de bron-VM. Gebruik [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) om een NSG-regel met de naam **ssh** te maken. Deze regel wordt gemaakt in de NSG met de naam **myNSGsource** in de resourcegroep **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGsource \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
    
```

### <a name="create-nic-for-source-vm"></a>NIC maken voor bron-VM

Maak met [az network nic create](/cli/azure/network/nic#az-network-nic-create) een netwerkinterface en koppel deze aan het openbare IP-adres en de netwerkbeveiligingsgroep. 

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNicsource \
    --vnet-name myVnetsource \
    --subnet mySubnetsource \
    --public-ip-address myPublicIPSourceVM \
    --network-security-group myNSGsource
    
```

### <a name="create-a-source-vm"></a>Een bron-VM maken

Maak de virtuele machine met [az vm create](/cli/azure/vm#az-vm-create).  U genereert SSH-sleutels voor deze VM en slaat de persoonlijke sleutel op voor later gebruik.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupNAT \
    --name myVMsource \
    --nics myNicsource \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait
    
```

Hoewel de opdracht direct een resultaat retourneert, kan het een paar minuten duren voordat de VM is geïmplementeerd.

## <a name="prepare-destination-for-outbound-traffic"></a>Doel voorbereiden voor uitgaand verkeer

We gaan nu een doel maken voor het uitgaande verkeer dat door de NAT-service is vertaald, zodat u het kunt testen.

### <a name="configure-virtual-network-for-destination"></a>Virtueel netwerk configureren voor bestemming

 We moeten een virtueel netwerk maken voor de virtuele doelmachine.  Deze stappen zijn hetzelfde als voor de bron-VM, met enkele kleine wijzigingen om het eindpunt op de bestemming zichtbaar te maken.

Maak met [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) in **myResourceGroupNAT** een virtueel netwerk met de naam **myVnetdestination** met een subnet met de naam **mySubnetdestination**.  De IP-adresruimte voor het virtuele netwerk is **192.168.0.0/16**. Het subnet binnen het virtuele netwerk is **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --name myVnetdestination \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetdestination \
    --subnet-prefix 192.168.0.0/24
    
```

### <a name="create-public-ip-for-destination-vm"></a>Openbare IP maken voor doel-VM

U maakt een openbaar IP-adres dat gebruikt gaat worden voor toegang tot de bron-VM. Gebruik [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) om in **myResourceGroupNAT** een openbare IP-resource te maken met de naam **myPublicIPdestinationVM**. 

```azurecli-interactive
  az network public-ip create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPdestinationVM \
  --sku standard
  
```

### <a name="create-an-nsg-for-destination-vm"></a>Een NSG maken voor de doel-VM

Standaard openbare IP-adressen zijn 'standaard beveiligd' en dus moet u een NSG maken om inkomende toegang voor SSH-toegang toe te staan. De Azure NAT-service kan de richting van een stroom detecteren. Deze NSG wordt niet gebruikt voor uitgaand verkeer wanneer de NAT-gateway is geconfigureerd in hetzelfde subnet. Gebruik [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) om in **myResourceGroupNAT** een NSG-resource met de naam **myNSGdestination** te maken.

```azurecli-interactive
    az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSGdestination
    
```

### <a name="expose-ssh-endpoint-on-destination-vm"></a>SSH-eindpunt beschikbaar maken op de doel-VM

U maakt een regel in de NSG voor SSH-toegang tot de doel-VM. Gebruik [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) om een NSG-regel met de naam **ssh** te maken. Deze regel wordt gemaakt in de NSG met de naam **myNSGdestination** in de resourcegroep **myResourceGroupNAT**.

```azurecli-interactive
    az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGdestination \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
    
```

### <a name="expose-http-endpoint-on-destination-vm"></a>HTTP-eindpunt beschikbaar maken op doel-VM

U maakt een regel in de NSG voor SSH-toegang tot de doel-VM. Gebruik [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) om een NSG-regel met de naam **http** te maken in de NSG met de naam **myNSGdestination** in **myResourceGroupNAT**.

```azurecli-interactive
    az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGdestination \
    --priority 101 \
    --name http \
    --description "HTTP access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 80
    
```

### <a name="create-nic-for-destination-vm"></a>NIC maken voor doel-VM

Maak met [az network nic create](/cli/azure/network/nic#az-network-nic-create) een netwerkinterface en koppel deze aan het openbare IP-adres **myPublicIPdestinationVM** en de netwerkbeveiligingsgroep **myNSGdestination**. 

```azurecli-interactive
    az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNicdestination \
    --vnet-name myVnetdestination \
    --subnet mySubnetdestination \
    --public-ip-address myPublicIPdestinationVM \
    --network-security-group myNSGdestination
    
```

### <a name="create-a-destination-vm"></a>Een doel-VM maken

Maak de virtuele machine met [az vm create](/cli/azure/vm#az-vm-create).  U genereert SSH-sleutels voor deze VM en slaat de persoonlijke sleutel op voor later gebruik.

 ```azurecli-interactive
    az vm create \
    --resource-group myResourceGroupNAT \
    --name myVMdestination \
    --nics myNicdestination \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait
    
```
Hoewel de opdracht direct een resultaat retourneert, kan het een paar minuten duren voordat de VM is geïmplementeerd.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Een webserver voorbereiden en payload testen op de doel-VM

Eerst moet het IP-adres van de doel-VM worden gedetecteerd.  Gebruik [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) om het openbare IP-adres van de doel-VM op te halen. 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPdestinationVM \
    --query [ipAddress] \
    --output tsv
    
``` 

>[!IMPORTANT]
>Kopieer het openbare IP-adres en plak het in Kladblok, zodat u het kunt gebruiken in de volgende stappen. Geef aan dat dit de virtuele doelmachine is.

### <a name="sign-in-to-destination-vm"></a>Aanmelden bij de doel-VM

De SSH-aanmeldingsgegevens moeten worden opgeslagen in uw Cloud Shell van de vorige bewerking.  Open een [Azure Cloud Shell](https://shell.azure.com) in uw browser. Gebruik het IP-adres dat in de vorige stap is opgehaald om SSH naar de virtuele machine uit te voeren. 

```bash
ssh <ip-address-destination>
```

Kopieer en plak de volgende opdrachten zodra u bent aangemeld.  

```bash
sudo apt -y update && \
sudo apt -y upgrade && \
sudo apt -y install nginx && \
sudo ln -sf /dev/null /var/log/nginx/access.log && \
sudo touch /var/www/html/index.html && \
sudo rm /var/www/html/index.nginx-debian.html && \
sudo dd if=/dev/zero of=/var/www/html/100k bs=1024 count=100

```

Met deze opdrachten wordt uw virtuele machine bijgewerkt, nginx geïnstalleerd en een bestand van 100 KB gemaakt. Dit bestand wordt opgehaald van de bron-VM met behulp van de NAT-service.

Sluit de SSH-sessie met de doel-VM.

## <a name="prepare-test-on-source-vm"></a>Test voorbereiden op de bron-VM

Eerst moet het IP-adres van de bron-VM worden gedetecteerd.  Gebruik [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) om het openbare IP-adres van de bron-VM op te halen. 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPsourceVM \
    --query [ipAddress] \
    --output tsv
    
``` 

>[!IMPORTANT]
>Kopieer het openbare IP-adres en plak het in een Kladblok, zodat u het kunt gebruiken in de volgende stappen. Geef aan dat dit de virtuele bronmachine is.

### <a name="sign-in-to-source-vm"></a>Aanmelden bij de bron-VM

De SSH-referenties worden zoals gezegd opgeslagen in Cloud Shell. Open een nieuw tabblad voor [Azure Cloud Shell](https://shell.azure.com) in uw browser.  Gebruik het IP-adres dat in de vorige stap is opgehaald om SSH naar de virtuele machine uit te voeren. 

```bash
ssh <ip-address-source>
```

Kopieer en plak de volgende opdrachten om het testen van de NAT-service voor te bereiden.

```bash
sudo apt -y update && \
sudo apt -y upgrade && \
sudo apt install -y nload golang && \
echo 'export GOPATH=${HOME}/go' >> .bashrc && \
echo 'export PATH=${PATH}:${GOPATH}/bin' >> .bashrc && \
. ~/.bashrc &&
go get -u github.com/rakyll/hey

```

Met deze opdracht wordt uw virtuele machine bijgewerkt, installeert u go, installeert u [hey](https://github.com/rakyll/hey) van GitHub en werkt u uw shell-omgeving bij.

U bent nu klaar om de NAT-service te testen.

## <a name="validate-nat-service"></a>NAT-service valideren

Terwijl u bent aangemeld bij de bron-VM, kunt u **curl** en **hey** om aanvragen te genereren voor het doel-IP-adres.

Gebruik curl om het bestand van 100 KB op te halen.  Vervang **\<ip-address-destination>** in het onderstaande voorbeeld met het doel-IP-adres dat u eerder hebt gekopieerd.  De parameter **--output** geeft aan dat het opgehaalde bestand wordt verwijderd.

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

U kunt ook een reeks aanvragen genereren met **hey**. Vervang nogmaals **\<ip-address-destination>** met het doel-IP-adres dat u eerder hebt gekopieerd.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

Met deze opdracht worden 100 aanvragen gegenereerd, 10 gelijktijdig, met een time-out van 30 seconden. De TCP-verbinding wordt niet opnieuw gebruikt.  Elke aanvraag zal 100 KB ophalen.  Aan het einde van de uitvoering zal **hey** een aantal statistieken rapporteren over de juiste werking van de NAT-service.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [az group delete](/cli/azure/group#az-group-delete) gebruiken om de resourcegroep en alle gerelateerde resources daarin te verwijderen wanneer u ze niet meer nodig hebt.

```azurecli-interactive 
  az group delete --name myResourceGroupNAT
  
```

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u een NAT-gateway gemaakt, een bron- en doel-VM gemaakt en vervolgens de NAT-gateway getest.

Controleer de metrische gegevens in Azure Monitor om uw NAT-service weer te geven. Problemen vaststellen, zoals de resource-uitputting van de beschikbare SNAT-poorten.  Resource-uitputting van de SNAT-poorten kan gemakkelijk worden verholpen door extra openbare IP-adresresources of openbare IP-voorvoegselresources of beide toe te voegen.

- Meer informatie over [Virtual Network NAT](./nat-overview.md)
- Meer informatie over [NAT-gatewayresource](./nat-gateway-resource.md).
- Quickstart voor het implementeren van [NAT-gatewayresource met Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Quickstart voor het implementeren van [NAT-gatewayresource met behulp van Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Quickstart voor het implementeren van [NAT-gatewayresource met Azure Portal](./quickstart-create-nat-gateway-portal.md).

> [!div class="nextstepaction"]


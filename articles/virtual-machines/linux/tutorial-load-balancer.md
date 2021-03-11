---
title: 'Zelfstudie: Taakverdelingen maken voor virtuele Linux-machines in Azure'
description: In deze zelfstudie leert u hoe u Azure CLI kunt gebruiken om een load balancer te maken voor een maximaal beschikbare en veilige toepassing op drie virtuele Linux-machines
services: virtual-machines
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.subservice: networking
ms.assetid: ''
ms.service: virtual-machines
ms.collection: linux
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/13/2017
ms.author: cynthn
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 433bbd51618cfb5624c8ed2c549e1793488f0e81
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102553762"
---
# <a name="tutorial-load-balance-linux-virtual-machines-in-azure-to-create-a-highly-available-application-with-the-azure-cli"></a>Zelfstudie: Taakverdelingen maken voor virtuele Linux-machines in Azure om een maximaal beschikbare toepassing te maken met Azure CLI

Taakverdeling zorgt voor een hogere beschikbaarheid door binnenkomende aanvragen te spreiden over meerdere virtuele machines. In deze zelfstudie leert u meer over de verschillende onderdelen van Azure Load Balancer die het verkeer verdelen en zorgen voor hoge beschikbaarheid. In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Een Azure-load balancer maken
> * Een load balancer-statustest maken
> * Load balancer-verkeersregels maken
> * Cloud-init gebruiken voor het maken van een eenvoudige Node.js-app
> * Virtuele machines maken en koppelen aan een load balancer
> * Een load balancer in actie zien
> * VM's toevoegen aan en verwijderen uit een load balancer

In deze zelfstudie wordt gebruikgemaakt van de CLI in de [Azure Cloud Shell](../../cloud-shell/overview.md), die voortdurend wordt bijgewerkt naar de nieuwste versie. Als u de Cloud Shell wilt openen, selecteert u **Probeer het** bovenaan een willekeurig codeblok.

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u Azure CLI 2.0.30 of hoger gebruiken voor deze zelfstudie. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="azure-load-balancer-overview"></a>Overzicht van Azure Load Balancer
Een Azure-load balancer is een Layer-4-load balancer (TCP, UDP) die zorgt voor hoge beschikbaarheid door inkomend verkeer te distribueren tussen correct werkende virtuele machines. Tijdens een statustest van een load balancer wordt een bepaalde poort op elke VM gecontroleerd en wordt verkeer alleen naar een werkende VM gedistribueerd.

U definieert een front-end-IP-configuratie met een of meer openbare IP-adressen. Dankzij deze front-end-IP-configuratie zijn uw load balancer en toepassingen toegankelijk via internet. 

Virtuele machines maken verbinding met een load balancer via hun virtuele netwerkinterfacekaart (NIC). Om verkeer te distribueren naar de VM's bevat een back-end-adresgroep de IP-adressen van de virtuele netwerkinterfacekaarten (NIC's) die zijn verbonden met de load balancer.

Om de verkeersstroom te regelen, definieert u load balancer-regels voor specifieke poorten en protocollen die aan uw VM's worden toegewezen.

Als u de vorige zelfstudie over het [maken van een virtuele-machineschaalset](tutorial-create-vmss.md) hebt gevolgd, is er een load balancer voor u gemaakt. Al deze onderdelen zijn geconfigureerd als onderdeel van de schaalset.


## <a name="create-azure-load-balancer"></a>Azure-load balancer maken
In dit gedeelte wordt beschreven hoe u elk onderdeel van de load balancer kunt maken en configureren. Voordat u een load balancer kunt maken, moet u eerst een resourcegroep maken met [az group create](/cli/azure/group). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroupLoadBalancer* gemaakt op de locatie *VS Oost*:

```azurecli-interactive
az group create --name myResourceGroupLoadBalancer --location eastus
```

### <a name="create-a-public-ip-address"></a>Een openbaar IP-adres maken
Om toegang te krijgen tot uw app op internet, hebt u een openbaar IP-adres nodig voor de load balancer. Maak een openbaar IP-adres met [az network public-ip create](/cli/azure/network/public-ip). In het volgende voorbeeld wordt een openbaar IP-adres met de naam *myPublicIP* gemaakt in de resourcegroep *myResourceGroupLoadBalancer*:

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroupLoadBalancer \
    --name myPublicIP
```

### <a name="create-a-load-balancer"></a>Een load balancer maken
Maak een load balancer met [az network lb create](/cli/azure/network/lb). In het volgende voorbeeld wordt een load balancer met de naam *myLoadBalancer* gemaakt en wordt het adres *myPublicIP* aan de front-end-IP-configuratie toegewezen:

```azurecli-interactive
az network lb create \
    --resource-group myResourceGroupLoadBalancer \
    --name myLoadBalancer \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --public-ip-address myPublicIP
```

### <a name="create-a-health-probe"></a>Een statustest maken
U gebruikt een statustest om de load balancer de status van uw app te laten bewaken. De statustest voegt dynamisch VM's toe aan de load balancer-rotatie of verwijdert ze, op basis van hun reactie op statuscontroles. Standaard wordt een VM uit de load balancer-distributie verwijderd na twee opeenvolgende fouten met intervallen van 15 seconden. U maakt een statustest op basis van een protocol of een specifieke statuscontrolepagina voor uw app. 

In het volgende voorbeeld wordt een TCP-test gemaakt. U kunt ook aangepaste HTTP-tests maken voor meer fijnmazige statuscontroles. Wanneer u een aangepaste HTTP-test maakt, moet u de statuscontrolepagina maken, zoals *healthcheck.js*. De test moet het antwoord **HTTP 200 OK** voor de load balancer retourneren om de host in rotatie te houden.

U gebruikt [az network lb probe create](/cli/azure/network/lb/probe) om een TCP-statustest te maken. In het volgende voorbeeld wordt een statustest gemaakt met de naam *myHealthProbe*:

```azurecli-interactive
az network lb probe create \
    --resource-group myResourceGroupLoadBalancer \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-a-load-balancer-rule"></a>Een load balancer-regel maken
Een load balancer-regel wordt gebruikt om de verdeling van het verkeer over de VM's te definiëren. U definieert de front-end-IP-configuratie voor het inkomende verkeer en de back-end-IP-groep om het verkeer te ontvangen, samen met de gewenste bron- en doelpoort. Om ervoor te zorgen dat alleen VM's met een goede status verkeer ontvangen, moet u ook de te gebruiken statustest definiëren.

Gebruik [az network lb rule create](/cli/azure/network/lb/rule) om een load balancer-regel te maken. In het volgende voorbeeld wordt een regel met de naam *myLoadBalancerRule* gemaakt, de statustest *myHealthProbe* gebruikt en het verkeer verdeeld op poort *80*:

```azurecli-interactive
az network lb rule create \
    --resource-group myResourceGroupLoadBalancer \
    --lb-name myLoadBalancer \
    --name myLoadBalancerRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe
```


## <a name="configure-virtual-network"></a>Virtueel netwerk configureren
Voordat u enkele VM's implementeert en uw balancer test, maakt u de ondersteunende virtuele-netwerkbronnen. Zie de zelfstudie [Manage Azure Virtual Networks](tutorial-virtual-network.md) (Virtuele Azure-netwerken beheren) voor meer informatie over virtuele netwerken.

### <a name="create-network-resources"></a>Netwerkbronnen maken
Maak een virtueel netwerk met [az network vnet create](/cli/azure/network/vnet). In het volgende voorbeeld wordt een virtueel netwerk gemaakt met de naam *myVnet* met het subnet *mySubnet*:

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroupLoadBalancer \
    --name myVnet \
    --subnet-name mySubnet
```

Als u een netwerkbeveiligingsgroep wilt toevoegen, gebruikt u [az network nsg create](/cli/azure/network/nsg). In het volgende voorbeeld wordt een netwerkbeveiligingsgroep met de naam *myNetworkSecurityGroup* gemaakt:

```azurecli-interactive
az network nsg create \
    --resource-group myResourceGroupLoadBalancer \
    --name myNetworkSecurityGroup
```

Maak een netwerkbeveiligingsgroepregel met [az network nsg rule create](/cli/azure/network/nsg/rule). In het volgende voorbeeld wordt een netwerkbeveiligingsgroepregel met de naam *myNetworkSecurityGroupRule* gemaakt:

```azurecli-interactive
az network nsg rule create \
    --resource-group myResourceGroupLoadBalancer \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --priority 1001 \
    --protocol tcp \
    --destination-port-range 80
```

Virtuele NIC's zijn gemaakt met [az network nic create](/cli/azure/network/nic). In het volgende voorbeeld worden drie virtuele NIC's gemaakt. (Eén virtuele NIC voor elke VM die u in de volgende stappen voor uw app maakt). U kunt op elk gewenst moment extra virtuele NIC's en VM's maken en toevoegen aan de load balancer:

```azurecli
for i in `seq 1 3`; do
    az network nic create \
        --resource-group myResourceGroupLoadBalancer \
        --name myNic$i \
        --vnet-name myVnet \
        --subnet mySubnet \
        --network-security-group myNetworkSecurityGroup \
        --lb-name myLoadBalancer \
        --lb-address-pools myBackEndPool
done
```

Wanneer alle drie de virtuele NIC's zijn gemaakt, gaat u door naar de volgende stap


## <a name="create-virtual-machines"></a>Virtuele machines maken

### <a name="create-cloud-init-config"></a>Een cloud-init-configuratiebestand maken
In een vorige zelfstudie over [Het aanpassen van een virtuele Linux-machine bij de eerste keer opstarten](tutorial-automate-vm-deployment.md), hebt u geleerd hoe u de aanpassing van de virtuele machine met cloud-init moet automatiseren. U kunt hetzelfde cloud-init-configuratiebestand gebruiken om NGINX te installeren en een eenvoudige 'Hallo wereld' Node.js-app uit te voeren in de volgende stap. Als u de load balancer in actie wilt zien, opent u aan het einde van de zelfstudie deze simpele app in een webbrowser.

Maak in uw huidige shell een bestand met de naam *cloud-init.txt* en plak de volgende configuratie. Maak bijvoorbeeld het bestand in de Cloud Shell, niet op uw lokale computer. Voer `sensible-editor cloud-init.txt` in voor het maken van het bestand en om een overzicht van beschikbare editors te zien. Controleer of het hele cloud-init-bestand correct is gekopieerd, met name de eerste regel:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

### <a name="create-virtual-machines"></a>Virtuele machines maken
Verbeter de hoge beschikbaarheid van uw app door uw VM's in een beschikbaarheidsset te plaatsen. Zie de vorige zelfstudie [How to create highly available virtual machines](tutorial-availability-sets.md) (Maximaal beschikbare virtuele machines maken) voor meer informatie over beschikbaarheidssets.

Maak een beschikbaarheidsset met behulp van [az vm availability-set create](/cli/azure/vm/availability-set). In het volgende voorbeeld wordt een beschikbaarheidsset met de naam *myAvailabilitySet* gemaakt:

```azurecli-interactive 
az vm availability-set create \
    --resource-group myResourceGroupLoadBalancer \
    --name myAvailabilitySet
```

Nu kunt u de VM's maken met [az vm create](/cli/azure/vm). In het volgende voorbeeld worden drie VM's gemaakt en SSH-sleutels gegenereerd als deze nog niet bestaan:

```azurecli
for i in `seq 1 3`; do
    az vm create \
        --resource-group myResourceGroupLoadBalancer \
        --name myVM$i \
        --availability-set myAvailabilitySet \
        --nics myNic$i \
        --image UbuntuLTS \
        --admin-username azureuser \
        --generate-ssh-keys \
        --custom-data cloud-init.txt \
        --no-wait
done
```

Er zijn achtergrondtaken die nog worden uitgevoerd nadat u door de Azure CLI bent teruggeleid naar de prompt. De parameter `--no-wait` wacht niet totdat alle taken zijn voltooid. Het duurt mogelijk nog een paar minuten voordat u toegang hebt tot de app. De statustest van de load balancer wordt automatisch gedetecteerd wanneer de app wordt uitgevoerd op een virtuele machine. Wanneer de app wordt uitgevoerd, begint de load balancer-regel het verkeer te distribueren.


## <a name="test-load-balancer"></a>Load balancer testen
Achterhaal het openbare IP-adres van de load balancer met [az network public-ip show](/cli/azure/network/public-ip). In het volgende voorbeeld wordt het IP-adres opgehaald voor het eerder gemaakte *myPublicIP*:

```azurecli-interactive
az network public-ip show \
    --resource-group myResourceGroupLoadBalancer \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
```

Vervolgens kunt u het openbare IP-adres invoeren in een webbrowser. Het duurt enkele minuten voordat de virtuele machines gereed zijn en de load balancer begint met het distribueren van verkeer naar de VM's. De app wordt weergegeven met de hostnaam van de VM waarnaar de load balancer verkeer heeft gedistribueerd, zoals in het volgende voorbeeld:

![Node.js-app uitvoeren](./media/tutorial-load-balancer/running-nodejs-app.png)

Als u wilt zien hoe de load balancer verkeer distribueert naar alle drie de VM's waarop uw app wordt uitgevoerd, kunt u vernieuwing van uw webbrowser afdwingen.


## <a name="add-and-remove-vms"></a>VM's toevoegen en verwijderen
Het is mogelijk dat u onderhoud moet uitvoeren op de VM's waarop uw app wordt uitgevoerd, zoals het installeren van besturingssysteemupdates. U moet mogelijk extra VM's toevoegen vanwege toegenomen verkeer naar uw app. In dit gedeelte wordt beschreven hoe u een VM aan de load balancer toevoegt of ervan verwijdert.

### <a name="remove-a-vm-from-the-load-balancer"></a>Een VM van de load balancer verwijderen
U kunt een VM verwijderen uit de back-end-adresgroep met [az network nic ip-config address-pool remove](/cli/azure/network/nic/ip-config/address-pool). In het volgende voorbeeld wordt de virtuele NIC voor **myVM2** van *myLoadBalancer* verwijderd:

```azurecli-interactive
az network nic ip-config address-pool remove \
    --resource-group myResourceGroupLoadBalancer \
    --nic-name myNic2 \
    --ip-config-name ipConfig1 \
    --lb-name myLoadBalancer \
    --address-pool myBackEndPool 
```

Als u wilt zien hoe de load balancer verkeer distribueert naar de resterende twee VM's waarop uw app wordt uitgevoerd, kunt u vernieuwing van uw webbrowser afdwingen. U kunt nu onderhoud uitvoeren op de VM, zoals het installeren van updates voor het besturingssysteem of het opnieuw opstarten van de VM.

Gebruik [az network lb address-pool show](/cli/azure/network/lb/address-pool) om een overzicht van VM's met virtuele NIC's die zijn verbonden met de load balancer weer te geven. Voer als volgt query's uit en filter op de id van de virtuele NIC:

```azurecli-interactive
az network lb address-pool show \
    --resource-group myResourceGroupLoadBalancer \
    --lb-name myLoadBalancer \
    --name myBackEndPool \
    --query backendIpConfigurations \
    --output tsv | cut -f4
```

De uitvoer is vergelijkbaar met het volgende voorbeeld, waaruit blijkt dat de virtuele NIC voor VM 2 niet langer deel uitmaakt van de back-end-adresgroep:

```output
/subscriptions/<guid>/resourceGroups/myResourceGroupLoadBalancer/providers/Microsoft.Network/networkInterfaces/myNic1/ipConfigurations/ipconfig1
/subscriptions/<guid>/resourceGroups/myResourceGroupLoadBalancer/providers/Microsoft.Network/networkInterfaces/myNic3/ipConfigurations/ipconfig1
```

### <a name="add-a-vm-to-the-load-balancer"></a>Een VM toevoegen aan de load balancer
Na het uitvoeren van VM-onderhoud, of als u de capaciteit wilt uitbreiden, kunt u een VM toevoegen aan de back-end-adresgroep met [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool). In het volgende voorbeeld wordt de virtuele NIC voor **myVM2** aan *myLoadBalancer* toegevoegd:

```azurecli-interactive
az network nic ip-config address-pool add \
    --resource-group myResourceGroupLoadBalancer \
    --nic-name myNic2 \
    --ip-config-name ipConfig1 \
    --lb-name myLoadBalancer \
    --address-pool myBackEndPool
```

Gebruik opnieuw [az network lb address-pool show](/cli/azure/network/lb/address-pool) uit de vorige stap om te controleren of de virtuele NIC is verbonden met de back-end-adresgroep.


## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u een load balancer gemaakt en er VM's aan gekoppeld. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een Azure-load balancer maken
> * Een load balancer-statustest maken
> * Load balancer-verkeersregels maken
> * Cloud-init gebruiken voor het maken van een eenvoudige Node.js-app
> * Virtuele machines maken en koppelen aan een load balancer
> * Een load balancer in actie zien
> * VM's toevoegen aan en verwijderen uit een load balancer

Ga naar de volgende zelfstudie voor meer informatie over de onderdelen van het virtuele Azure-netwerk.

> [!div class="nextstepaction"]
> [Virtuele machines en virtuele netwerken beheren](tutorial-virtual-network.md)

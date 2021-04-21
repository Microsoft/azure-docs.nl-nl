---
title: 'Zelfstudie: Een schaalset voor virtuele Linux-machines maken'
description: U leert hoe u Azure CLI gebruikt om een maximaal beschikbare toepassing te maken en implementeren op virtuele Linux-machines met behulp van een virtuele-machineschaalset
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: linux
ms.date: 06/01/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 4d31bde05158e89168f2a67b820c8743d4cd2729
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769891"
---
# <a name="tutorial-create-a-virtual-machine-scale-set-and-deploy-a-highly-available-app-on-linux-with-the-azure-cli"></a>Zelfstudie: Een virtuele-machineschaalset maken en een toepassing met hoge beschikbaarheid implementeren in Linux met Azure CLI

Met een virtuele-machineschaalset kunt u een reeks identieke virtuele machines met automatisch schalen implementeren en beheren. U kunt het aantal VM’s in de schaalset handmatig schalen of regels voor automatisch schalen definiëren op basis van resourcegebruik zoals CPU, vraag naar geheugen, of netwerkverkeer. In deze zelfstudie implementeert u een virtuele-machineschaalset in Azure. In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Cloud-init gebruiken voor het maken van een app op schaal
> * Een virtuele-machineschaalset maken
> * Het aantal instanties in een schaalset vergroten of verkleinen
> * Regels voor automatisch schalen maken
> * Verbindingsgegevens voor schaalsetinstanties weergeven
> * Gegevensschijven in een schaalset gebruiken

In deze zelfstudie wordt gebruikgemaakt van de CLI in de [Azure Cloud Shell](../../cloud-shell/overview.md), die voortdurend wordt bijgewerkt naar de nieuwste versie. Als u de Cloud Shell wilt openen, selecteert u **Probeer het** bovenaan een willekeurig codeblok.

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u Azure CLI 2.0.30 of hoger gebruiken voor deze zelfstudie. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="scale-set-overview"></a>Overzicht van schaalsets
Met een virtuele-machineschaalset kunt u een reeks identieke virtuele machines met automatisch schalen implementeren en beheren. Virtuele machines in een schaalset worden verdeeld over logische fout- en updatedomeinen in een of meer *plaatsingsgroepen*. Dit zijn groepen van op soortgelijke wijze geconfigureerde virtuele machines, vergelijkbaar met [beschikbaarheidssets](tutorial-availability-sets.md).

Virtuele machines worden in een schaalset gemaakt als dat nodig is. U definieert regels voor automatisch schalen om te bepalen hoe en wanneer virtuele machines worden toegevoegd of verwijderd uit de schaalset. Deze regels kunnen worden geactiveerd op basis van de metrische gegevens zoals CPU-belasting, geheugengebruik of netwerkverkeer.

Schaalsets bieden ondersteuning voor maximaal 1000 VM’s wanneer u een Azure-platforminstallatiekopie gebruikt. Voor werkbelastingen met aanzienlijke installatie- of VM-aanpassingsvereisten wilt u mogelijk [een aangepaste VM-installatiekopie maken](tutorial-custom-images.md). U kunt maximaal 300 virtuele machines in een schaalset maken wanneer u een aangepaste installatiekopie gebruikt.


## <a name="create-an-app-to-scale"></a>Een app op schaal maken
Voor gebruik in een productieomgeving wilt u mogelijk [een aangepaste VM-installatiekopie maken](tutorial-custom-images.md) waarop uw toepassing is geïnstalleerd en geconfigureerd. Voor deze zelfstudie gaan we de virtuele machines aanpassen bij de eerste keer opstarten om snel een schaalset in actie te zien.

In een vorige zelfstudie hebt u geleerd hoe u [een virtuele Linux-machine kunt aanpassen bij de eerste keer opstarten](tutorial-automate-vm-deployment.md) met cloud-init. U kunt hetzelfde cloud-init-configuratiebestand gebruiken om NGINX te installeren en een eenvoudige 'Hallo wereld' Node.js-app uit te voeren.

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


## <a name="create-a-scale-set"></a>Een schaalset maken
Voordat u een schaalset kunt maken, moet u eerst een resourcegroep maken met [az group create](/cli/azure/group#az_group_create). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroupScaleSet* gemaakt op de locatie *VS Oost*:

```azurecli-interactive
az group create --name myResourceGroupScaleSet --location eastus
```

Maak nu een virtuele-machineschaalset met [az vmss create](/cli/azure/vmss#az_vmss_create). In het volgende voorbeeld wordt een schaalset gemaakt met de naam *myScaleSet*, wordt het cloud-init-bestand gebruikt voor het aanpassen van de VM en worden SSH-sleutels gegenereerd als deze nog niet bestaan:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroupScaleSet \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys
```

Het duurt enkele minuten om alle schaalsetresources en VM's te maken en te configureren. Er zijn achtergrondtaken die nog worden uitgevoerd nadat u door de Azure CLI bent teruggeleid naar de prompt. Het duurt mogelijk nog een paar minuten voordat u toegang hebt tot de app.


## <a name="allow-web-traffic"></a>Webverkeer toestaan
Er is automatisch een load balancer als onderdeel van de virtuele-machineschaalset gemaakt. De load balancer verdeelt verkeer over een reeks gedefinieerde virtuele machines met behulp van load balancer-regels. Meer informatie over concepten en configuratie van de load balancer vindt u in de volgende zelfstudie, [Werklasten verdelen over virtuele machines](tutorial-load-balancer.md).

Als u wilt dat verkeer de web-app kan bereiken, maakt u een regel met behulp van [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create). In het volgende voorbeeld wordt een regel met de naam *myLoadBalancerRuleWeb* gemaakt:

```azurecli-interactive
az network lb rule create \
  --resource-group myResourceGroupScaleSet \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

## <a name="test-your-app"></a>Uw app testen
Als u de Node.js app in actie wilt zien op het web, achterhaalt u het openbare IP-adres van de load balancer met [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show). In het volgende voorbeeld wordt het IP-adres voor *myScaleSetLBPublicIP* opgehaald, dat is gemaakt als onderdeel van de schaalset:

```azurecli-interactive
az network public-ip show \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSetLBPublicIP \
    --query [ipAddress] \
    --output tsv
```

Voer in een webbrowser het openbare IP-adres in. De app wordt weergegeven, inclusief de hostnaam van de virtuele machine waarnaar de load balancer verkeer heeft verdeeld:

![Node.js-app uitvoeren](./media/tutorial-create-vmss/running-nodejs-app.png)

Als u de schaalset in actie wilt zien, kunt u vernieuwing van uw webbrowser afdwingen om te zien dat de load balancer verkeer verdeelt over alle virtuele machines waarop uw app wordt uitgevoerd.


## <a name="management-tasks"></a>Beheertaken
Tijdens de levenscyclus van de schaalset moet u mogelijk een of meer beheertaken uitvoeren. Bovendien wilt u misschien scripts maken die verschillende levenscyclustaken automatiseren. De Azure CLI biedt een snelle manier om deze taken uit te voeren. Hier volgen enkele algemene taken.

### <a name="view-vms-in-a-scale-set"></a>Virtuele machines weergeven in een schaalset
Als u een lijst met virtuele machines die worden uitgevoerd in de schaalset wilt weergeven, gebruikt u [az vmss list-instances](/cli/azure/vmss#az_vmss_list_instances) als volgt:

```azurecli-interactive
az vmss list-instances \
  --resource-group myResourceGroupScaleSet \
  --name myScaleSet \
  --output table
```

De uitvoer lijkt op die in het volgende voorbeeld:

```bash
  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup            VmId
------------  --------------------  ----------  ------------  -------------------  -----------------------  ------------------------------------
           1  True                  eastus      myScaleSet_1  Succeeded            MYRESOURCEGROUPSCALESET  c72ddc34-6c41-4a53-b89e-dd24f27b30ab
           3  True                  eastus      myScaleSet_3  Succeeded            MYRESOURCEGROUPSCALESET  44266022-65c3-49c5-92dd-88ffa64f95da
```


### <a name="manually-increase-or-decrease-vm-instances"></a>VM-instanties handmatig vergroten of verkleinen
Als u het aantal instanties wilt weergeven dat zich momenteel in een schaalset bevindt, gebruikt u [az vmss show](/cli/azure/vmss#az_vmss_show) en voert u een query uit op *sku.capacity*:

```azurecli-interactive
az vmss show \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```

U kunt vervolgens het aantal virtuele machines in de schaalset handmatig vergroten of verkleinen met [az vmss scale](/cli/azure/vmss#az_vmss_scale). In het volgende voorbeeld wordt het aantal VM's in uw schaal ingesteld op *3*:

```azurecli-interactive
az vmss scale \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --new-capacity 3
```

### <a name="get-connection-info"></a>Verbindingsgegevens ophalen
Gebruik [az vmss list-instance-connection-info](/cli/azure/vmss#az_vmss_list_instance_connection_info) om verbindingsinformatie over de VM's in uw schaalsets op te halen. Deze opdracht levert het openbare IP-adres en de poort voor elke virtuele machine waarmee u verbinding kunt maken met SSH:

```azurecli-interactive
az vmss list-instance-connection-info \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet
```


## <a name="use-data-disks-with-scale-sets"></a>Gegevensschijven met schaalsets gebruiken
U kunt gegevensschijven maken en gebruiken met schaalsets. In een vorige zelfstudie hebt u geleerd hoe u [Azure-schijven beheert](tutorial-manage-disks.md) en zijn de aanbevolen procedures en prestatieverbeteringen voor het bouwen van apps op gegevensschijven in plaats van de besturingssysteemschijf beschreven.

### <a name="create-scale-set-with-data-disks"></a>Een schaalset met gegevensschijven maken
Als u een schaalset wilt maken en gegevensschijven wilt koppelen, voegt u de parameter `--data-disk-sizes-gb` toe aan de opdracht [az vmss create](/cli/azure/vmss#az_vmss_create). In het volgende voorbeeld wordt een schaalset gemaakt waarbij aan elke instantie een gegevensschijf van *50* GB is gekoppeld:

```azurecli-interactive
az vmss create \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSetDisks \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --custom-data cloud-init.txt \
    --admin-username azureuser \
    --generate-ssh-keys \
    --data-disk-sizes-gb 50
```

Wanneer instanties uit een schaalset worden verwijderd, worden ook eventuele gekoppelde gegevensschijven verwijderd.

### <a name="add-data-disks"></a>Gegevensschijven toevoegen
Als u een gegevensschijf wilt toevoegen aan instanties in uw schaalset, gebruikt u [az vmss disk attach](/cli/azure/vmss/disk#az_vmss_disk_attach). In het volgende voorbeeld wordt aan elke instantie een schijf van *50* GB toegevoegd:

```azurecli-interactive
az vmss disk attach \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --size-gb 50 \
    --lun 2
```

### <a name="detach-data-disks"></a>Gegevensschijven ontkoppelen
Als u een gegevensschijf wilt verwijderen uit instanties in uw schaalset, gebruikt u [az vmss disk detach](/cli/azure/vmss/disk#az_vmss_disk_detach). In het volgende voorbeeld wordt de gegevensschijf in LUN *2* uit elke instantie verwijderd:

```azurecli-interactive
az vmss disk detach \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --lun 2
```


## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u een virtuele-machineschaalset gemaakt. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Cloud-init gebruiken voor het maken van een app op schaal
> * Een virtuele-machineschaalset maken
> * Het aantal instanties in een schaalset vergroten of verkleinen
> * Regels voor automatisch schalen maken
> * Verbindingsgegevens voor schaalsetinstanties weergeven
> * Gegevensschijven in een schaalset gebruiken

Ga naar de volgende zelfstudie voor meer informatie over load balancing-concepten voor virtuele machines.

> [!div class="nextstepaction"]
> [Werklasten verdelen over virtuele machines](tutorial-load-balancer.md)

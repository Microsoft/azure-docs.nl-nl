---
title: 'Quickstart: Een schaalset voor virtuele machines maken met Azure CLI'
description: Ga aan de slag met uw implementaties door te leren hoe u snel een schaalset voor virtuele machines kunt maken met Azure CLI.
author: ju-shim
ms.author: jushiman
ms.topic: quickstart
ms.service: virtual-machine-scale-sets
ms.subservice: cli
ms.date: 03/27/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 5ed6ff00e2ec9a47cec0290fa88ef3b554287607
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94518155"
---
# <a name="quickstart-create-a-virtual-machine-scale-set-with-the-azure-cli"></a>Quickstart: Een virtuele-machineschaalset maken met Azure CLI
Met een virtuele-machineschaalset kunt u een reeks automatisch schalende virtuele machines implementeren en beheren. U kunt het aantal VM’s in de schaalset handmatig schalen of regels voor automatisch schalen definiëren op basis van resourcegebruik zoals CPU, vraag naar geheugen, of netwerkverkeer. Een Azure load balancer verdeelt het verkeer vervolgens naar de VM-exemplaren in de schaalset. In deze snelstart gebruikt u de Azure CLI om een schaalset voor virtuele machines te maken en een voorbeeldtoepassing te implementeren.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.0.29 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd. 


## <a name="create-a-scale-set"></a>Een schaalset maken
Voordat u een schaalset kunt maken, moet u eerst een resourcegroep maken met [az group create](/cli/azure/group). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Maak nu een virtuele-machineschaalset met [az vmss create](/cli/azure/vmss). In het volgende voorbeeld wordt een schaalset met de naam *myScaleSet* gemaakt die automatisch wordt bijgewerkt als er wijzigingen zijn. Daarnaast worden er SSH-sleutels gegenereerd als deze niet bestaan in *~/.ssh/id_rsa*. Deze SSH-sleutels worden gebruikt als u zich wilt aanmelden bij de VM-exemplaren. Als u een bestaande set SSH-sleutels wilt gebruiken, gebruikt u de parameter `--ssh-key-value` en geeft u de locatie van uw sleutels op.

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys
```

Het duurt enkele minuten om alle schaalsetresources en VM's te maken en te configureren.


## <a name="deploy-sample-application"></a>Voorbeeldtoepassing implementeren
Als u de schaalset wilt testen, installeert u een eenvoudige webtoepassing. U kunt de aangepaste scriptextensie van Azure downloaden en een script uitvoeren waarmee een toepassing wordt geïnstalleerd in de VM-exemplaren. Deze uitbreiding is handig voor post-implementatieconfiguraties, software-installaties of andere configuratie-/beheertaken. Zie voor meer informatie het [overzicht van de aangepaste scriptextensie](../virtual-machines/extensions/custom-script-linux.md).

Gebruik de aangepaste scriptextensie om een eenvoudige NGINX-webserver te installeren. Pas de aangepaste scriptextensie waarmee NGINX wordt geïnstalleerd als volgt toe met [az vmss extension set](/cli/azure/vmss/extension):

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"],"commandToExecute":"./automate_nginx.sh"}'
```


## <a name="allow-traffic-to-application"></a>Verkeer toestaan naar de toepassing
Op het moment dat de schaalset wordt gemaakt, wordt er ook automatisch een Azure load balancer geïmplementeerd. De load balancer verdeelt het verkeer naar de VM-exemplaren in de schaalset. Als u wilt dat verkeer de voorbeeldwebtoepassing kan bereiken, maakt u een load balancer-regel met behulp van [az network lb rule create](/cli/azure/network/lb/rule). In het volgende voorbeeld wordt een regel met de naam *myLoadBalancerRuleWeb* gemaakt:

```azurecli-interactive
az network lb rule create \
  --resource-group myResourceGroup \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```


## <a name="test-your-scale-set"></a>Uw schaalset testen
U kunt de schaalset in actie zien door in een webbrowser naar de voorbeeldwebtoepassing te gaan. Achterhaal het openbare IP-adres van de load balancer met [az network public-ip show](/cli/azure/network/public-ip). In het volgende voorbeeld wordt het IP-adres voor *myScaleSetLBPublicIP* opgehaald, dat is gemaakt als onderdeel van de schaalset:

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query '[ipAddress]' \
  --output tsv
```

Voer in een webbrowser het openbare IP-adres van de load balancer in. Via de load balancer wordt verkeer naar een van uw VM-instanties gedistribueerd, zoals wordt weergegeven in het volgende voorbeeld:

![Standaardwebpagina in NGINX](media/virtual-machine-scale-sets-create-cli/running-nginx-site.png)


## <a name="clean-up-resources"></a>Resources opschonen
Gebruik [az group delete](/cli/azure/group) als volgt om de resourcegroep, schaalset en alle gerelateerde resources te verwijderen wanneer u deze niet meer nodig hebt. De parameter `--no-wait` retourneert het besturingselement naar de prompt zonder te wachten totdat de bewerking is voltooid. De parameter `--yes` bevestigt dat u de resources wilt verwijderen, zonder een extra prompt om dit te doen.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```


## <a name="next-steps"></a>Volgende stappen
In deze snelstartgids hebt u een standaardschaalset gemaakt en de aangepaste scriptextensie gebruikt om een standaard-NGINX-webserver te installeren in de VM-exemplaren. Als u meer wilt weten over schaalsets, gaat u verder met de zelfstudie voor het maken en beheren van schaalsets voor virtuele Azure-machines.

> [!div class="nextstepaction"]
> [Schaalsets voor virtuele Azure-machines maken en beheren](tutorial-create-and-manage-cli.md)

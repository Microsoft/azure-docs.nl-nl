---
title: Zelfstudie - Toepassingen installeren in een schaalset met Azure CLI
description: Informatie over het gebruik van Azure CLI om toepassingen met de aangepaste scriptextensie te installeren in schaalsets voor virtuele machines
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.date: 03/27/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: cf383d41df5d41ea52065f3651b2068861631116
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105934611"
---
# <a name="tutorial-install-applications-in-virtual-machine-scale-sets-with-the-azure-cli"></a>Zelfstudie: Toepassingen installeren in schaalsets voor virtuele machines met Azure CLI
Als u toepassingen wilt uitvoeren op de exemplaren van een virtuele machine (VM) in een schaalset, moet u eerst de toepassingsonderdelen en de vereiste bestanden installeren. In een vorige zelfstudie hebt u geleerd om een aangepaste VM-installatiekopie te maken en te gebruiken voor het implementeren van uw VM-exemplaren. Deze aangepaste installatiekopie bevat handmatige installaties van toepassingen en configuraties. U kunt de installatie van toepassingen op een schaalset ook automatiseren nadat elk VM-exemplaar is geïmplementeerd. Bovendien kunt u toepassingen bijwerken die al worden uitgevoerd in een schaalset. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Automatisch toepassingen installeren in een schaalset
> * De aangepaste scriptextensie van Azure gebruiken
> * Een actieve toepassing in een schaalset bijwerken

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.0.29 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd. 


## <a name="what-is-the-azure-custom-script-extension"></a>Wat is de aangepaste scriptextensie van Azure?
Met de aangepaste scriptextensie kunnen scripts worden gedownload en uitgevoerd op virtuele machines in Azure. Deze uitbreiding is handig voor post-implementatieconfiguraties, software-installaties of andere configuratie-/beheertaken. Scripts kunnen worden gedownload uit Azure Storage of GitHub, of worden geleverd in Azure Portal tijdens de uitvoering van extensies.

De aangepaste scriptextensie kan worden geïntegreerd met Azure Resource Manager-sjablonen en ook worden uitgevoerd met Azure CLI, Azure PowerShell, de Azure-portal of de REST API. Zie voor meer informatie het [overzicht van de aangepaste scriptextensie](../virtual-machines/extensions/custom-script-linux.md).

Als u de aangepaste scriptextensie wilt gebruiken met de Azure CLI, moet u een JSON-bestand maken met daarin de bestanden die u wilt verkrijgen en de opdrachten die u wilt uitvoeren. Deze JSON-definities kunnen worden hergebruikt binnen implementaties van schaalsets om zo toepassingen op een consistente manier te installeren.


## <a name="create-custom-script-extension-definition"></a>Definitie van de aangepaste scriptextensie maken
Als u de aangepaste scriptextensie in actie wilt zien, maakt u een schaalset die de IIS-webserver installeert en de hostnaam levert van het VM-exemplaar in de schaalset. Met de volgende definitie van de aangepaste scriptextensie wordt er een voorbeeldscript gedownload vanuit GitHub, waarna de vereiste pakketten worden geïnstalleerd en de hostnaam van het VM-exemplaar wordt weggeschreven naar een standaard-HTML-pagina.

Maak in uw huidige shell een bestand met de naam *customConfig.json* en plak de volgende configuratie in het bestand. Maak bijvoorbeeld het bestand in de Cloud Shell, niet op uw lokale computer. U kunt elke editor die u wilt gebruiken. Voer in de Cloud Shell `sensible-editor customConfig.json` in voor het maken van het bestand en om een overzicht van beschikbare editors te zien.

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"],
  "commandToExecute": "./automate_nginx.sh"
}
```

> [!CAUTION]
> Mogelijk moet u het gebruik van enkele (') en dubbele aanhalingstekens (") in het JSON-blok omkeren als u besluit om rechtstreeks naar de JSON te verwijzen (in plaats van te verwijzen naar het bestand *customConfig.json*) in de parameter *--Instellingen* hieronder. 


## <a name="create-a-scale-set"></a>Een schaalset maken
Maak een resourcegroep maken met [az group create](/cli/azure/group). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Maak nu een virtuele-machineschaalset met [az vmss create](/cli/azure/vmss). Het volgende voorbeeld wordt een schaalset gemaakt met de naam *myScaleSet*, en worden SSH-sleutels gegenereerd als deze nog niet bestaan:

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


## <a name="apply-the-custom-script-extension"></a>Aangepaste scriptextensie toepassen
Pas de configuratie van de aangepaste scriptextensie toe op de VM-exemplaren in uw schaalset met [az vmss extension set](/cli/azure/vmss/extension). In het volgende voorbeeld wordt de configuratie *customConfig.json* toegepast op de VM-exemplaren *myScaleSet* in de resourcegroep met de naam *myResourceGroup*:

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings @customConfig.json
```

Elk VM-exemplaar in de schaalset downloadt het script vanuit GitHub en voert het uit. In een meer complex voorbeeld kunnen meerdere toepassingsonderdelen en bestanden worden geïnstalleerd. Als de schaalset omhoog wordt geschaald, passen de VM-exemplaren automatisch dezelfde definitie van de aangepaste scriptextensie toe en installeren deze de vereiste toepassing.


## <a name="test-your-scale-set"></a>Uw schaalset testen
Als u wilt dat verkeer de webserver kan bereiken, maakt u een load balancer-regel met behulp van [az network lb rule create](/cli/azure/network/lb/rule). In het volgende voorbeeld wordt een regel met de naam *myLoadBalancerRuleWeb* gemaakt:

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

Als u de webserver in actie wilt zien, achterhaalt u het openbare IP-adres van de load balancer met [az network public-ip show](/cli/azure/network/public-ip). In het volgende voorbeeld wordt het IP-adres voor *myScaleSetLBPublicIP* opgehaald, dat is gemaakt als onderdeel van de schaalset:

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

Voer in een webbrowser het openbare IP-adres van de load balancer in. Via de load balancer wordt verkeer naar een van uw VM-instanties gedistribueerd, zoals wordt weergegeven in het volgende voorbeeld:

![Standaardwebpagina in Nginx](media/tutorial-install-apps-cli/running-nginx.png)

Sluit de webbrowser niet af, zodat u in de volgende stap een bijgewerkte versie ziet.


## <a name="update-app-deployment"></a>App-implementatie bijwerken
Gedurende de levenscyclus van een schaalset moet u wellicht een bijgewerkte versie van uw toepassing implementeren. U kunt met de aangepaste scriptextensie verwijzen naar een bijgewerkt implementatiescript en vervolgens de extensie opnieuw op uw schaalset toepassen. Bij het maken van de schaalset in een vorige stap is `--upgrade-policy-mode` ingesteld op *Automatic*. Met deze instelling kunnen de VM-exemplaren in de schaalset automatisch de meest recente versie van uw toepassing bijwerken en toepassen.

Maak in uw huidige shell een bestand met de naam *customConfigv2.json* en plak de volgende configuratie in het bestand. Deze definitie voert een bijgewerkte versie *v2* van het installatiescript van de toepassing uit:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx_v2.sh"],
  "commandToExecute": "./automate_nginx_v2.sh"
}
```

Pas de configuratie van de aangepaste scriptextensie opnieuw toe op de VM-exemplaren in uw schaalset met [az vmss extension set](/cli/azure/vmss/extension). *customConfigv2.json* wordt gebruikt voor het toepassen van de bijgewerkte versie van de toepassing:

```azurecli-interactive
az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroup \
    --vmss-name myScaleSet \
    --settings @customConfigv2.json
```

Alle VM-exemplaren in de schaalset worden automatisch bijgewerkt met de meest recente versie van de voorbeeldwebpagina. Vernieuw de website in uw browser voor een overzicht van de bijgewerkte versie:

![Bijgewerkte webpagina in Nginx](media/tutorial-install-apps-cli/running-nginx-updated.png)


## <a name="clean-up-resources"></a>Resources opschonen
Als u de schaalset en aanvullende resources wilt verwijderen, verwijdert u de resourcegroep en alle bijbehorende resources met [az group delete](/cli/azure/group). De parameter `--no-wait` retourneert het besturingselement naar de prompt zonder te wachten totdat de bewerking is voltooid. De parameter `--yes` bevestigt dat u de resources wilt verwijderen, zonder een extra prompt om dit te doen.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u geleerd hoe u automatisch toepassingen kunt installeren of bijwerken in uw schaalset met Azure CLI:

> [!div class="checklist"]
> * Automatisch toepassingen installeren in een schaalset
> * De aangepaste scriptextensie van Azure gebruiken
> * Een actieve toepassing in een schaalset bijwerken

Ga door naar de volgende zelfstudie voor informatie over het automatisch schalen van uw schaalset.

> [!div class="nextstepaction"]
> [Uw schaalsets automatisch schalen](tutorial-autoscale-cli.md)

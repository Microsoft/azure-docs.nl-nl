---
title: Zelfstudie - Toepassingen installeren in een schaalset met Azure-sjablonen
description: Leer hoe u met behulp van sjablonen van Azure Resource Manager en de aangepaste scriptextensie toepassingen kunt installeren in schaalsets voor virtuele machines
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.date: 03/27/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 1ce7795f1cb823be7940f065c9dba4ecc51926c4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105934594"
---
# <a name="tutorial-install-applications-in-virtual-machine-scale-sets-with-an-azure-template"></a>Zelfstudie: Toepassingen installeren in een schaalset met een Azure-sjabloon
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

Als u de aangepaste scriptextensie in actie wilt zien, maakt u een schaalset die de IIS-webserver installeert en de hostnaam levert van het VM-exemplaar in de schaalset. Met de volgende definitie van de aangepaste scriptextensie wordt er een voorbeeldscript gedownload vanuit GitHub, waarna de vereiste pakketten worden geïnstalleerd en de hostnaam van het VM-exemplaar wordt weggeschreven naar een standaard-HTML-pagina.


## <a name="create-custom-script-extension-definition"></a>Definitie van de aangepaste scriptextensie maken
Wanneer u een schaalset voor virtuele machines definieert op basis van een Azure-sjabloon, kan de resourceprovider *Microsoft.Compute/virtualMachineScaleSets* een sectie over uitbreidingen bevatten. In de sectie *extensionsProfile* wordt beschreven wat er wordt toegepast op de VM-exemplaren in een schaalset. Als u de aangepaste scriptextensie wilt gebruiken, geeft u de uitgever *Microsoft.Azure.Extensions* op en het type *CustomScript*.

De eigenschap *fileUris* wordt gebruikt voor het definiëren van de bron-installatiescripts of -pakketten. U start de installatie door de vereiste scripts te definiëren in *commandToExecute*. In het volgende voorbeeld definieert u een voorbeeldscript van GitHub waarmee de NGINX-webserver wordt geïnstalleerd en geconfigureerd:

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "AppInstall",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"
          ],
          "commandToExecute": "bash automate_nginx.sh"
        }
      }
    }
  ]
}
```

Zie [https://github.com/Azure-Samples/compute-automation-configurations/blob/master/scale_sets/azuredeploy.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/scale_sets/azuredeploy.json) voor een compleet voorbeeld van een Azure-sjabloon waarmee een schaalset en de aangepaste scriptextensie wordt geïmplementeerd. Deze voorbeeldsjabloon wordt gebruikt in de volgende sectie.


## <a name="create-a-scale-set"></a>Een schaalset maken
We gaan de voorbeeldsjabloon gebruiken om een schaalset te maken en de aangepaste scriptextensie toe te passen. Maak eerst een resourcegroep met [az group create](/cli/azure/group). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Maak nu een schaalset voor virtuele machines met [az deployment group create](/cli/azure/deployment/group). Geef desgevraagd uw eigen gebruikersnaam en wachtwoord op voor gebruik als de referenties voor elk VM-exemplaar:

```azurecli-interactive
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/azuredeploy.json
```

Het duurt enkele minuten om alle schaalsetresources en VM's te maken en te configureren.

Elk VM-exemplaar in de schaalset downloadt het script vanuit GitHub en voert het uit. In een meer complex voorbeeld kunnen meerdere toepassingsonderdelen en bestanden worden geïnstalleerd. Als de schaalset omhoog wordt geschaald, passen de VM-exemplaren automatisch dezelfde definitie van de aangepaste scriptextensie toe en installeren deze de vereiste toepassing.


## <a name="test-your-scale-set"></a>Uw schaalset testen
Als u de webserver in actie wilt zien, achterhaalt u het openbare IP-adres van de load balancer met [az network public-ip show](/cli/azure/network/public-ip). In het volgende voorbeeld wordt het IP-adres voor *myScaleSetPublicIP* opgehaald, dat is gemaakt als onderdeel van de schaalset:

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetPublicIP \
  --query [ipAddress] \
  --output tsv
```

Voer in een webbrowser het openbare IP-adres van de load balancer in. Via de load balancer wordt verkeer naar een van uw VM-instanties gedistribueerd, zoals wordt weergegeven in het volgende voorbeeld:

![Standaardwebpagina in Nginx](media/tutorial-install-apps-template/running-nginx.png)

Sluit de webbrowser niet af, zodat u in de volgende stap een bijgewerkte versie ziet.


## <a name="update-app-deployment"></a>App-implementatie bijwerken
Gedurende de levenscyclus van een schaalset moet u wellicht een bijgewerkte versie van uw toepassing implementeren. U kunt met de aangepaste scriptextensie verwijzen naar een bijgewerkt implementatiescript en vervolgens de extensie opnieuw op uw schaalset toepassen. Bij het maken van de schaalset in een vorige stap is *upgradePolicy* ingesteld op *Automatic*. Met deze instelling kunnen de VM-exemplaren in de schaalset automatisch de meest recente versie van uw toepassing bijwerken en toepassen.

Als u de aangepaste scriptextensie wilt bijwerken, wijzigt u de sjabloon om deze te laten verwijzen naar een nieuw installatiescript. U moet een nieuwe bestandsnaam gebruiken, anders wordt de wijziging niet herkend door de aangepaste scriptextensie. De scriptextensie kijkt namelijk niet naar de inhoud van het script om eventuele wijzigingen vast te stellen. In de volgende definitie wordt een bijgewerkt installatiescript gebruikt waarbij *_v2* is toegevoegd aan de naam:

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "AppInstall",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx_v2.sh"
          ],
          "commandToExecute": "bash automate_nginx_v2.sh"
        }
      }
    }
  ]
}
```

Gebruik [az deployment group create](/cli/azure/deployment/group) om de configuratie van de aangepaste scriptextensie opnieuw toe te passen op de VM-exemplaren in uw schaalset. De sjabloon *azuredeployv2.json* wordt gebruikt voor het toepassen van de bijgewerkte versie van de toepassing. In de praktijk bewerkt u de bestaande sjabloon *azuredeploy.json* om deze te laten verwijzen naar het bijgewerkte installatiescript, zoals u kunt zien in de vorige sectie. Wanneer u daarom wordt gevraagd, voert u de gebruikersnaam en het wachtwoord in die u hebt gebruikt bij het maken van de schaalset:

```azurecli-interactive
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/azuredeploy_v2.json
```

Alle VM-exemplaren in de schaalset worden automatisch bijgewerkt met de meest recente versie van de voorbeeldwebpagina. Vernieuw de website in uw browser voor een overzicht van de bijgewerkte versie:

![Bijgewerkte webpagina in Nginx](media/tutorial-install-apps-template/running-nginx-updated.png)


## <a name="clean-up-resources"></a>Resources opschonen
Als u de schaalset en aanvullende resources wilt verwijderen, verwijdert u de resourcegroep en alle bijbehorende resources met [az group delete](/cli/azure/group). De parameter `--no-wait` retourneert het besturingselement naar de prompt zonder te wachten totdat de bewerking is voltooid. De parameter `--yes` bevestigt dat u de resources wilt verwijderen, zonder een extra prompt om dit te doen.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u geleerd hoe u automatisch toepassingen kunt installeren of bijwerken in uw schaalset met Azure-sjablonen:

> [!div class="checklist"]
> * Automatisch toepassingen installeren in een schaalset
> * De aangepaste scriptextensie van Azure gebruiken
> * Een actieve toepassing in een schaalset bijwerken

Ga door naar de volgende zelfstudie voor informatie over het automatisch schalen van uw schaalset.

> [!div class="nextstepaction"]
> [Uw schaalsets automatisch schalen](tutorial-autoscale-template.md)

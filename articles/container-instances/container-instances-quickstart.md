---
title: 'Quickstart: een Docker-container implementeren in een containerinstantie - Azure CLI'
description: In deze quickstart gebruikt u de Azure CLI om snel een in een container geplaatste web-app te implementeren die wordt uitgevoerd in een geïsoleerde Azure-containerinstantie
ms.topic: quickstart
ms.date: 03/21/2019
ms.custom:
- seo-python-october2019
- seodec18
- mvc
- devx-track-js
- devx-track-azurecli
ms.openlocfilehash: 1c327fc7fc067948b5022f989e6c86f99573bd1a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93100181"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-cli"></a>Quickstart: Een containerinstantie in Azure implementeren met behulp van de Azure CLI

Gebruik Azure Container Instances om snel en eenvoudig serverloze Docker-containers uit te voeren in Azure. Een toepassing implementeren in een containerinstantie op aanvraag, wanneer u geen volledig indelingsplatform voor containers nodig hebt zoals Azure Kubernetes Service.

In deze quickstart gebruikt u de Azure CLI om een geïsoleerde Docker-container te implementeren en maakt u de bijbehorende toepassing beschikbaar met een FQDN (Fully Qualified Domain Name). Een paar seconden na het uitvoeren van de opdracht voor één implementatie kunt u naar de toepassing browsen die wordt uitgevoerd in de container:

![Een app weergeven in de browser die is geïmplementeerd in Azure Container Instances][aci-app-browser]

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Voor deze quickstart is versie 2.0.55 of hoger van de Azure-CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Azure Container Instances moeten, zoals alle Azure-resources, worden geïmplementeerd in een resourcegroep. Met resourcegroepen kunt u gerelateerde Azure-resources organiseren en beheren.

Maak eerst een resourcegroep met de naam *myResourceGroup* in de locatie *eastus* met behulp van de opdracht [az group create][az-group-create]:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>Een container maken

Nu u een resourcegroep hebt, kunt u een container in Azure uitvoeren. Als u een containerinstantie met de Azure CLI wilt maken, geeft u de naam van een resourcegroep, de naam van een containerinstantie en een Docker-containerinstallatiekopie op voor de opdracht [az container create][az-container-create]. In deze quickstart gebruikt u de openbare installatiekopie `mcr.microsoft.com/azuredocs/aci-helloworld`. Deze installatiekopie bevat een kleine web-app die is geschreven in Node.js en die een statische HTML-pagina dient.

Als u uw containers beschikbaar wilt maken op internet, moet u een of meer poorten om te openen of een DNS-naamlabel opgeven, of beide. In deze quickstart implementeert u een container met een DNS-naamlabel zodat de web-app openbaar bereikbaar is.

Voer een opdracht uit die er als volgt uitziet om een containerinstantie te starten. Stel een `--dns-name-label`-waarde in die uniek is voor de Azure-regio waarin u de instantie maakt. Als u een foutbericht 'DNS-naamlabel niet beschikbaar' ontvangt, probeert u een ander DNS-naamlabel.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image mcr.microsoft.com/azuredocs/aci-helloworld --dns-name-label aci-demo --ports 80
```

Binnen enkele seconden krijgt u een reactie van de Azure-CLI die aangeeft dat de implementatie is voltooid. Controleer de status ervan met de opdracht [az container show][az-container-show]:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
```

Wanneer u de opdracht uitvoert, worden de FQDN (Fully Qualified Domain Name) en de inrichtingsstatus van de container weergegeven.

```output
FQDN                               ProvisioningState
---------------------------------  -------------------
aci-demo.eastus.azurecontainer.io  Succeeded
```

Als de `ProvisioningState` van de container **Geslaagd** is, gaat u naar de FQDN in de browser. U moet nu een webpagina zien die lijkt op de volgende. U hebt een toepassing geïmplementeerd die wordt uitgevoerd in een Docker-container voor Azure.

![Een app weergeven in de browser die is geïmplementeerd in Azure Container Instances][aci-app-browser]

Als de toepassing niet meteen wordt weergegeven, moet u mogelijk een paar seconden wachten terwijl DNS wordt doorgegeven. Probeer vervolgens de browser te vernieuwen.

## <a name="pull-the-container-logs"></a>De containerlogboeken ophalen

Wanneer u problemen moet oplossen voor een container of de toepassing die daarmee wordt uitgevoerd, of als u alleen de uitvoer wilt zien, kunt u eerst de logboeken van de containerinstantie bekijken.

Haal de logboeken van de containerinstantie op met de opdracht [az container logs][az-container-logs]:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

De uitvoer geeft de logboeken voor de container weer, evenals de HTTP GET-aanvragen die worden gegenereerd wanneer u de toepassing in uw browser weergeeft.

```output
listening on port 80
::ffff:10.240.255.55 - - [21/Mar/2019:17:43:53 +0000] "GET / HTTP/1.1&quot; 304 - &quot;-&quot; &quot;Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
```

## <a name="attach-output-streams"></a>Uitvoerstromen koppelen

Behalve het bekijken van de logboeken kunt u ook uw lokale standaarduitvoer- en standaardgegevensstromen met fouten koppelen aan die van de container.

Voer eerst de opdracht [az container attach][az-container-attach] uit om uw lokale console aan de uitvoerstromen van de container te koppelen:

```azurecli-interactive
az container attach --resource-group myResourceGroup --name mycontainer
```

Vernieuw de browser na het koppelen een paar keer om wat extra uitvoer te genereren. Wanneer u klaar bent, ontkoppelt u ten slotte de console met `Control+C`. De uitvoer ziet er als volgt uit:

```output
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 17:27:20+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-helloworld"
(count: 1) (last timestamp: 2019-03-21 17:27:24+00:00) Successfully pulled image "mcr.microsoft.com/azuredocs/aci-helloworld"
(count: 1) (last timestamp: 2019-03-21 17:27:27+00:00) Created container
(count: 1) (last timestamp: 2019-03-21 17:27:27+00:00) Started container

Start streaming logs:
listening on port 80

::ffff:10.240.255.55 - - [21/Mar/2019:17:43:53 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:47:01 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.56 - - [21/Mar/2019:17:47:12 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met de container, verwijdert u deze met de opdracht [az container delete][az-container-delete]:

```azurecli-interactive
az container delete --resource-group myResourceGroup --name mycontainer
```

Om te controleren of de container is verwijderd, voert u de opdracht [az container list](/cli/azure/container#az-container-list) uit:

```azurecli-interactive
az container list --resource-group myResourceGroup --output table
```

De container **mycontainer** mag dan niet worden weergegeven in de uitvoer van de opdracht. Als u geen andere containers in de resourcegroep hebt, wordt er geen uitvoer weergegeven.

Als u klaar bent met de resourcegroep *myResourceGroup* en alle resources hierin, verwijdert u deze met de opdracht [az group delete][az-group-delete]:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Azure-containerinstantie gemaakt met behulp van een openbare Microsoft-installatiekopie. Als u zelf een containerinstallatiekopie wilt bouwen en deze wilt implementeren met behulp van een privé Azure Container-register, gaat u verder met de zelfstudie voor Azure Container Instances.

> [!div class="nextstepaction"]
> [Zelfstudie voor Azure Container Instances](./container-instances-tutorial-prepare-app.md)

Raadpleeg de quickstarts voor [Azure Kubernetes Service (AKS)][container-service], als u opties wilt proberen voor het uitvoeren van containers in een indelingssysteem in Azure.

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/view-an-application-running-in-an-azure-container-instance.png

<!-- LINKS - External -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git
[azure-account]: https://azure.microsoft.com/free/
[node-js]: https://nodejs.org

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-create]: /cli/azure/container#az-container-create
[az-container-delete]: /cli/azure/container#az-container-delete
[az-container-list]: /cli/azure/container#az-container-list
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[container-service]: ../aks/kubernetes-walkthrough.md

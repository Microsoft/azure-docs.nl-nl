---
title: 'Quickstart: Docker-container implementeren naar een containerinstantie met de Docker CLI'
description: In deze quickstart gebruikt u de Docker CLI om snel een in een container geplaatste web-app te implementeren die wordt uitgevoerd in een geïsoleerde instantie van een Azure-container.
ms.topic: quickstart
ms.date: 09/14/2020
ms.custom: devx-track-js
ms.openlocfilehash: 9904fb8c2142816196a1939a16445318bdb245d0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91262311"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-docker-cli"></a>Quickstart: Een containerinstantie in Azure implementeren met behulp van de Docker CLI

Gebruik Azure Container Instances om snel en eenvoudig serverloze Docker-containers uit te voeren in Azure. Implementeer op aanvraag naar een containerinstantie wanneer u cloud-apps ontwikkelt en u naadloos wilt kunnen overschakelen van lokaal ontwikkelen naar cloudimplementatie.

In deze quickstart gebruikt u opdrachten van de ingebouwde Docker CLI voor het implementeren van een Docker-container, om daarna de bijbehorende toepassing beschikbaar te maken in Azure Container Instances. Deze mogelijkheid wordt ingeschakeld door [integratie tussen Docker en Azure](https://docs.docker.com/engine/context/aci-integration/). Een paar seconden na het uitvoeren van de opdracht `docker run` kunt u browsen naar de toepassing die wordt uitgevoerd in de container:

:::image type="content" source="media/quickstart-docker-cli/view-application-running-in-an-azure-container-instance.png" alt-text="App die is geïmplementeerd met Azure Container Instances, weergegeven in de browser":::

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account][azure-account] aan voordat u begint.

Voor deze quickstart hebt u Docker Desktop versie 2.3.0.5 of hoger nodig. Deze versie is beschikbaar voor [Windows](https://desktop.docker.com/win/edge/Docker%20Desktop%20Installer.exe) of [macOS](https://desktop.docker.com/mac/edge/Docker.dmg). U kunt ook de [Docker ACI Integration CLI voor Linux](https://docs.docker.com/engine/context/aci-integration/#install-the-docker-aci-integration-cli-on-linux) installeren. 

> [!IMPORTANT]
> Niet alle functies van Azure Container Instances worden ondersteund. Geef feedback over de integratie van Docker en Azure door een probleem aan te maken in de GitHub-opslagplaats [aci-integration-beta](https://github.com/docker/aci-integration-beta).

[!INCLUDE [container-instances-create-docker-context](../../includes/container-instances-create-docker-context.md)]

## <a name="create-a-container"></a>Een container maken

Nadat u een Docker-context hebt gemaakt, kunt u een container maken in Azure. In deze quickstart gebruikt u de openbare installatiekopie `mcr.microsoft.com/azuredocs/aci-helloworld`. Deze installatiekopie bevat een kleine web-app die is geschreven in Node.js en die een statische HTML-pagina dient.

Ga eerst naar de ACI-context. Alle volgende Docker-opdrachten worden uitgevoerd in deze context.

```
docker context use myacicontext
```

Voer de volgende `docker run`-opdracht uit om de Azure-containerinstantie te maken met poort 80 toegankelijk op internet:

```
docker run -p 80:80 mcr.microsoft.com/azuredocs/aci-helloworld
```

Voorbeeld van uitvoer bij een geslaagde implementatie:

```
[+] Running 2/2
 ⠿ hungry-kirch            Created                                                                               5.1s
 ⠿ single--container--aci  Done                                                                                 11.3s
hungry-kirch
```

Voer `docker ps` uit om meer informatie te krijgen over de container die wordt uitgevoerd, zoals het openbare IP-adres:

```
docker ps
```


Voorbeeld van uitvoer met een openbaar IP-adres, in dit geval *52.230.225.232*:

```
CONTAINER ID        IMAGE                                        COMMAND             STATUS              PORTS
hungry-kirch        mcr.microsoft.com/azuredocs/aci-helloworld                       Running             52.230.225.232:80->80/tcp
```

 Ga nu naar het IP-adres in uw browser. U moet nu een webpagina zien die lijkt op de volgende. U hebt een toepassing geïmplementeerd die wordt uitgevoerd in een Docker-container voor Azure.

:::image type="content" source="media/quickstart-docker-cli/view-application-running-in-an-azure-container-instance.png" alt-text="App die is geïmplementeerd met Azure Container Instances, weergegeven in de browser":::

## <a name="pull-the-container-logs"></a>De containerlogboeken ophalen

Wanneer u problemen moet oplossen voor een container of de toepassing die daarmee wordt uitgevoerd, of als u alleen de uitvoer wilt zien, kunt u eerst de logboeken van de containerinstantie bekijken.

Voer bijvoorbeeld de `docker logs` opdracht uit om de logboeken van de container *hungry-kirch* te bekijken in de ACI-context:

```azurecli-interactive
docker logs hungry-kirch
```

De uitvoer geeft de logboeken voor de container weer, evenals de HTTP GET-aanvragen die worden gegenereerd wanneer u de toepassing in uw browser weergeeft.

```output
listening on port 80
::ffff:10.240.255.55 - - [07/Jul/2020:17:43:53 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [07/Jul/2020:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [07/Jul/2020:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
```


## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met de container, voert u `docker rm` uit om deze te verwijderen. Met deze opdracht wordt de Azure-containerinstantie gestopt en verwijderd.

```
docker rm hungry-kirch
```


## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Azure-containerinstantie van een openbare installatiekopie gemaakt door gebruik te maken van de integratie tussen Docker en Azure. Meer informatie over integratiescenario's vindt u in de [documentatie van Docker](https://docs.docker.com/engine/context/aci-integration/). 

U kunt ook de [Docker-extensie](https://aka.ms/VSCodeDocker) voor Visual Studio Code gebruiken om te werken met een geïntegreerde ervaring voor het ontwikkelen, uitvoeren en beheren van containers, installatiekopieën en contexten.

Als u de Azure-hulpprogramma's wilt gebruiken om containerinstanties te maken en beheren, raadpleegt u de quickstarts voor de [Azure CLI](container-instances-quickstart.md), [Azure PowerShell](container-instances-quickstart-powershell.md), de [Azure-portal](container-instances-quickstart-portal.md)en de [Azure Resource Manager-sjabloon](container-instances-quickstart-template.md).

Als u Docker Compose wilt gebruiken om lokaal een toepassing met meerdere containers te definiëren en uit te voeren en vervolgens wilt schakelen naar Azure Container Instances, gaat u door naar de zelfstudie.

> [!div class="nextstepaction"]
> [Zelfstudie voor Docker Compose](./tutorial-docker-compose.md)

<!-- LINKS - External -->

[azure-account]: https://azure.microsoft.com/free/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/


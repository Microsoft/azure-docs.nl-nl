---
title: Zelfstudie- Een groep met meerdere containers implementeren - YAML
description: In deze zelfstudie leert u hoe u een containergroep met meerdere containers in Azure Container Instances implementeert met behulp van een YAML-bestand met de Azure CLI.
ms.topic: article
ms.date: 07/01/2020
ms.openlocfilehash: 74269440357ee2d7ae36661618a31293346fa712
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771259"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-yaml-file"></a>Zelfstudie: Een groep met meerdere containers implementeren met behulp van een YAML-bestand

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Resource Manager](container-instances-multi-container-group.md)
>

Azure Container Instances ondersteunt de implementatie van meerdere containers op één host met behulp van een [containergroep](container-instances-container-groups.md). Een containergroep is handig bij het bouwen van een sidecar van een toepassing voor logboekregistratie, bewaking of een andere configuratie waarbij een service een tweede gekoppeld proces nodig heeft.

In deze zelfstudie volgt u de stappen voor het uitvoeren van een eenvoudige sidecar-configuratie met twee containers door een [YAML-bestand](container-instances-reference-yaml.md) te implementeren met behulp van de Azure CLI. Een YAML-bestand biedt een beknopte indeling voor het opgeven van de instantie-instellingen. In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Een YAML-bestand configureren
> * De containergroep implementeren
> * De logboeken van de containers weergeven

> [!NOTE]
> Groepen met meerdere containers zijn momenteel beperkt tot Linux-containers.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="configure-a-yaml-file"></a>Een YAML-bestand configureren

Als u een groep met meerdere containers wilt implementeren met de [opdracht az container create][az-container-create] in de Azure CLI, moet u de configuratie van de containergroep opgeven in een YAML-bestand. Geef vervolgens het YAML-bestand als parameter door aan de opdracht .

Begin met het kopiëren van de volgende YAML naar een nieuw bestand met de **naam deploy-aci.yaml**. In Azure Cloud Shell kunt u Visual Studio Code gebruiken om het bestand in uw werkmap te maken:

```
code deploy-aci.yaml
```

Dit YAML-bestand definieert een containergroep met de naam 'myContainerGroup' met twee containers, een openbaar IP-adres en twee opengelichte poorten. De containers worden geïmplementeerd vanuit openbare Microsoft-afbeeldingen. Met de eerste container in de groep wordt een internet-gerichte webtoepassing uitgevoerd. De tweede container, de sidecar, doet regelmatig HTTP-aanvragen naar de webtoepassing die in de eerste container wordt uitgevoerd via het lokale netwerk van de containergroep.

```YAML
apiVersion: 2019-12-01
location: eastus
name: myContainerGroup
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld:latest
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
      ports:
      - port: 80
      - port: 8080
  - name: aci-tutorial-sidecar
    properties:
      image: mcr.microsoft.com/azuredocs/aci-tutorial-sidecar
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
  osType: Linux
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: 80
    - protocol: tcp
      port: 8080
tags: {exampleTag: tutorial}
type: Microsoft.ContainerInstance/containerGroups
```

Als u een register met persoonlijke containerafbeeldingen wilt gebruiken, voegt u de eigenschap toe aan `imageRegistryCredentials` de containergroep, met waarden die zijn gewijzigd voor uw omgeving:

```YAML
  imageRegistryCredentials:
  - server: imageRegistryLoginServer
    username: imageRegistryUsername
    password: imageRegistryPassword
```

## <a name="deploy-the-container-group"></a>De containergroep implementeren

Maak een resourcegroep met de [opdracht az group create:][az-group-create]

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Implementeer de containergroep met de [opdracht az container create,][az-container-create] en voer het YAML-bestand door als argument:

```azurecli-interactive
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```

U ontvangt binnen enkele seconden een eerste reactie van Azure.

## <a name="view-deployment-state"></a>Implementatietoestand weergeven

Gebruik de volgende opdracht [az container show][az-container-show] om de status van de implementatie weer te geven:

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

Als u de toepassing die wordt uitgevoerd wilt weergeven, gaat u naar het IP-adres in uw browser. Het IP-adres staat `52.168.26.124` bijvoorbeeld in deze voorbeelduitvoer:

```console
Name              ResourceGroup    Status    Image                                                                                               IP:ports              Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  --------------------------------------------------------------------------------------------------  --------------------  ---------  ---------------  --------  ----------
myContainerGroup  danlep0318r      Running   mcr.microsoft.com/azuredocs/aci-tutorial-sidecar,mcr.microsoft.com/azuredocs/aci-helloworld:latest  20.42.26.114:80,8080  Public     1.0 core/1.5 gb  Linux     eastus
```

## <a name="view-container-logs"></a>Containerlogboeken ophalen

Bekijk de logboekuitvoer van een container met behulp van [de opdracht az container logs.][az-container-logs] Met `--container-name` het argument geeft u de container op waaruit logboeken moeten worden op halen. In dit voorbeeld wordt `aci-tutorial-app` de container opgegeven.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

Uitvoer:

```console
listening on port 80
::1 - - [02/Jul/2020:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [02/Jul/2020:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [02/Jul/2020:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

Als u de logboeken voor de sidecar-container wilt bekijken, moet u een vergelijkbare opdracht uitvoeren om de container op te `aci-tutorial-sidecar` geven.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

Uitvoer:

```console
Every 3s: curl -I http://localhost                          2020-07-02 20:36:41

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0  1663    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
HTTP/1.1 200 OK
X-Powered-By: Express
Accept-Ranges: bytes
Cache-Control: public, max-age=0
Last-Modified: Wed, 29 Nov 2017 06:40:40 GMT
ETag: W/"67f-16006818640"
Content-Type: text/html; charset=UTF-8
Content-Length: 1663
Date: Thu, 02 Jul 2020 20:36:41 GMT
Connection: keep-alive
```

Zoals u ziet, maakt de sidecar periodiek een HTTP-aanvraag naar de hoofdwebtoepassing via het lokale netwerk van de groep om ervoor te zorgen dat deze wordt uitgevoerd. Dit sidecar-voorbeeld kan worden uitgebreid om een waarschuwing te activeren als deze een andere HTTP-antwoordcode dan heeft `200 OK` ontvangen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een YAML-bestand gebruikt om een groep met meerdere containers te implementeren in Azure Container Instances. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een YAML-bestand configureren voor een groep met meerdere containers
> * De containergroep implementeren
> * De logboeken van de containers weergeven

U kunt ook een groep met meerdere containers opgeven met behulp van [Resource Manager sjabloon](container-instances-multi-container-group.md). Een Resource Manager kan gemakkelijk worden aangepast voor scenario's waarin u extra Azure-serviceresources met de containergroep moet implementeren.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-create]: /cli/azure/container#az_container_create
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
[az-group-create]: /cli/azure/group#az_group_create
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
[template-reference]: /azure/templates/microsoft.containerinstance/containergroups

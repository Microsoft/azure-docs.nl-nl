---
title: Zelf studie-een groep met meerdere containers implementeren-YAML
description: In deze zelf studie leert u hoe u een container groep met meerdere containers in Azure Container Instances kunt implementeren met behulp van een YAML-bestand met de Azure CLI.
ms.topic: article
ms.date: 07/01/2020
ms.openlocfilehash: 6f9dda7735587dfee1dde86c85375efcf057daa7
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97605158"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-yaml-file"></a>Zelf studie: een groep met meerdere containers implementeren met behulp van een YAML-bestand

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Resource Manager](container-instances-multi-container-group.md)
>

Azure Container Instances ondersteunt de implementatie van meerdere containers op één host met behulp van een [container groep](container-instances-container-groups.md). Een container groep is handig bij het bouwen van een sollicitatie stick voor logboek registratie, bewaking of andere configuraties waarbij een service een tweede gekoppelde procedure nodig heeft.

In deze zelf studie voert u de stappen uit voor het uitvoeren van een eenvoudige configuratie voor twee containers door een [yaml-bestand](container-instances-reference-yaml.md) te implementeren met behulp van de Azure cli. Een YAML-bestand biedt een beknopt formaat voor het opgeven van de instantie-instellingen. In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Een YAML-bestand configureren
> * De container groep implementeren
> * De logboeken van de containers weer geven

> [!NOTE]
> Groepen met meerdere containers zijn momenteel beperkt tot Linux-containers.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="configure-a-yaml-file"></a>Een YAML-bestand configureren

Als u een groep met meerdere containers wilt implementeren met de opdracht [AZ container Create][az-container-create] in de Azure CLI, moet u de configuratie van de container groep opgeven in een yaml-bestand. Geef het YAML-bestand vervolgens als een para meter aan de opdracht door.

Begin door de volgende YAML te kopiëren naar een nieuw bestand met de naam **Deploy-ACI. yaml**. In Azure Cloud Shell kunt u Visual Studio code gebruiken om het bestand in uw werkmap te maken:

```
code deploy-aci.yaml
```

Dit YAML-bestand definieert een container groep met de naam ' myContainerGroup ' met twee containers, een openbaar IP-adres en twee blootgestelde poorten. De containers worden geïmplementeerd vanuit open bare micro soft-installatie kopieën. De eerste container in de groep voert een Internet gerichte webtoepassing uit. De tweede container, de zijspan wagen, maakt periodiek HTTP-aanvragen voor de webtoepassing die in de eerste container wordt uitgevoerd via het lokale netwerk van de container groep.

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

Als u een REGI ster van een persoonlijke container installatie kopie wilt gebruiken, voegt `imageRegistryCredentials` u de eigenschap toe aan de container groep, met de waarden die zijn aangepast voor uw omgeving:

```YAML
  imageRegistryCredentials:
  - server: imageRegistryLoginServer
    username: imageRegistryUsername
    password: imageRegistryPassword
```

## <a name="deploy-the-container-group"></a>De container groep implementeren

Maak een resource groep met de opdracht [AZ Group Create][az-group-create] :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Implementeer de container groep met de opdracht [AZ container Create][az-container-create] , waarbij het yaml-bestand als een argument wordt door gegeven:

```azurecli-interactive
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```

U ontvangt binnen enkele seconden een eerste reactie van Azure.

## <a name="view-deployment-state"></a>Implementatie status weer geven

Als u de status van de implementatie wilt weer geven, gebruikt u de volgende opdracht [AZ container show][az-container-show] :

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

Als u de actieve toepassing wilt bekijken, gaat u naar het IP-adres in uw browser. Het IP-adres is bijvoorbeeld `52.168.26.124` in deze voorbeeld uitvoer:

```console
Name              ResourceGroup    Status    Image                                                                                               IP:ports              Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  --------------------------------------------------------------------------------------------------  --------------------  ---------  ---------------  --------  ----------
myContainerGroup  danlep0318r      Running   mcr.microsoft.com/azuredocs/aci-tutorial-sidecar,mcr.microsoft.com/azuredocs/aci-helloworld:latest  20.42.26.114:80,8080  Public     1.0 core/1.5 gb  Linux     eastus
```

## <a name="view-container-logs"></a>Containerlogboeken ophalen

Bekijk de logboek uitvoer van een container met behulp van de opdracht [AZ container logs][az-container-logs] . Het `--container-name` argument geeft de container aan waaruit logboeken moeten worden opgehaald. In dit voor beeld `aci-tutorial-app` is de container opgegeven.

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

Als u de logboeken voor de container voor de zijspan wagen wilt weer geven, voert u een vergelijk bare opdracht uit die de `aci-tutorial-sidecar` container opgeeft.

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

Zoals u ziet, maakt de zijspan wagen regel matig een HTTP-aanvraag voor de hoofd webtoepassing via het lokale netwerk van de groep om ervoor te zorgen dat deze wordt uitgevoerd. Dit voor beeld van een zijspan wagen kan worden uitgebreid om een waarschuwing te activeren als het een andere HTTP-antwoord code dan heeft ontvangen `200 OK` .

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een YAML-bestand gebruikt voor het implementeren van een groep met meerdere containers in Azure Container Instances. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een YAML-bestand voor een groep met meerdere containers configureren
> * De container groep implementeren
> * De logboeken van de containers weer geven

U kunt ook een groep met meerdere containers opgeven met behulp van een [Resource Manager-sjabloon](container-instances-multi-container-group.md). Een resource manager-sjabloon kan gemakkelijk worden aangepast voor scenario's waarin u aanvullende Azure-service resources met de container groep moet implementeren.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[template-reference]: /azure/templates/microsoft.containerinstance/containergroups

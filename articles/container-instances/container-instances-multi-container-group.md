---
title: 'Zelfstudie: Een groep met meerdere containers implementeren - sjabloon'
description: In deze zelfstudie leert u hoe u een containergroep met meerdere containers in Azure Container Instances implementeert met behulp van een Azure Resource Manager-sjabloon met de Azure CLI.
ms.topic: article
ms.date: 07/02/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 93ab139342795634a968cd538672e0e1b9bb08ae
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763897"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-resource-manager-template"></a>Zelfstudie: Een groep met meerdere containers implementeren met behulp van Resource Manager sjabloon

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Resource Manager](container-instances-multi-container-group.md)

Azure Container Instances ondersteunt de implementatie van meerdere containers op één host met behulp van een [containergroep](container-instances-container-groups.md). Een containergroep is handig bij het bouwen van een sidecar voor toepassingen voor logboekregistratie, bewaking of een andere configuratie waarbij een service een tweede gekoppeld proces nodig heeft.

In deze zelfstudie volgt u de stappen voor het uitvoeren van een eenvoudige sidecar-configuratie met twee containers door een Azure Resource Manager implementeren met behulp van de Azure CLI. In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Een groepssjabloon met meerdere containers configureren
> * De containergroep implementeren
> * De logboeken van de containers weergeven

Een Resource Manager-sjabloon kan direct worden aangepast voor scenario's waarin u extra Azure-serviceresources (bijvoorbeeld een Azure Files-share of een virtueel netwerk) met de containergroep moet implementeren. 

> [!NOTE]
> Groepen met meerdere containers zijn momenteel beperkt tot Linux-containers. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="configure-a-template"></a>Een sjabloon configureren

Begin met het kopiëren van de volgende JSON naar een nieuw bestand met de naam `azuredeploy.json` . In Azure Cloud Shell kunt u Visual Studio Code gebruiken om het bestand in uw werkmap te maken:

```
code azuredeploy.json
```

Deze Resource Manager definieert een containergroep met twee containers, een openbaar IP-adres en twee openstaat poorten. Met de eerste container in de groep wordt een internet-gerichte webtoepassing uitgevoerd. De tweede container, de sidecar, maakt een HTTP-aanvraag naar de hoofdwebtoepassing via het lokale netwerk van de groep.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerGroupName": {
      "type": "string",
      "defaultValue": "myContainerGroup",
      "metadata": {
        "description": "Container Group name."
      }
    }
  },
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "mcr.microsoft.com/azuredocs/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "mcr.microsoft.com/azuredocs/aci-tutorial-sidecar"
  },
  "resources": [
    {
      "name": "[parameters('containerGroupName')]",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                },
                {
                  "port": 8080
                }
              ]
            }
          },
          {
            "name": "[variables('container2name')]",
            "properties": {
              "image": "[variables('container2image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": 80
            },
            {
                "protocol": "tcp",
                "port": 8080
            }
          ]
        }
      }
    }
  ],
  "outputs": {
    "containerIPv4Address": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', parameters('containerGroupName'))).ipAddress.ip]"
    }
  }
}
```

Als u een privéregister met containerafbeeldingen wilt gebruiken, voegt u een -object toe aan het JSON-document met de volgende indeling. Zie de referentiedocumentatie [ACI][template-reference] Resource Manager-sjabloon voor een voorbeeld van de implementatie van deze configuratie.

```JSON
"imageRegistryCredentials": [
  {
    "server": "[parameters('imageRegistryLoginServer')]",
    "username": "[parameters('imageRegistryUsername')]",
    "password": "[parameters('imageRegistryPassword')]"
  }
]
```

## <a name="deploy-the-template"></a>De sjabloon implementeren

Een resourcegroep maken met de opdracht [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Implementeer de sjabloon met de [opdracht az deployment group create.][az-deployment-group-create]

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file azuredeploy.json
```

U ontvangt binnen enkele seconden een eerste reactie van Azure.

## <a name="view-deployment-state"></a>Implementatietoestand weergeven

Als u de status van de implementatie wilt weergeven, gebruikt u de [volgende opdracht az container show:][az-container-show]

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

Als u de logboeken voor de sidecar-container wilt zien, moet u een vergelijkbare opdracht uitvoeren om de container op te `aci-tutorial-sidecar` geven.

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

In deze zelfstudie hebt u een Azure Resource Manager gebruikt voor het implementeren van een groep met meerdere containers in Azure Container Instances. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een groepssjabloon met meerdere containers configureren
> * De containergroep implementeren
> * De logboeken van de containers weergeven

Zie voor meer voorbeelden van [sjablonen Azure Resource Manager sjablonen voor Azure Container Instances.](container-instances-samples-rm.md)

U kunt ook een groep met meerdere containers opgeven met behulp van een [YAML-bestand](container-instances-multi-container-yaml.md). Vanwege de beknoptere aard van de YAML-indeling is implementatie met een YAML-bestand een goede keuze wanneer uw implementatie alleen container-exemplaren bevat.


<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
[az-group-create]: /cli/azure/group#az_group_create
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
[template-reference]: /azure/templates/microsoft.containerinstance/containergroups

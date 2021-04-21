---
title: Geheim volume aan containergroep maken
description: Leer hoe u een geheim volume kunt monteren om gevoelige informatie op te slaan voor toegang door uw container-exemplaren
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: cd8bd4d59b5e53a0db2455bdfbaf56c05c93d65f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770995"
---
# <a name="mount-a-secret-volume-in-azure-container-instances"></a>Een geheim volume in Azure Container Instances

Gebruik een *geheim* volume om gevoelige informatie te verstrekken aan de containers in een containergroep. Het *geheime* volume slaat uw geheimen op in bestanden binnen het volume, die toegankelijk zijn voor de containers in de containergroep. Door geheimen op te slaan in *een geheim* volume, kunt u voorkomen dat gevoelige gegevens, zoals SSH-sleutels of databasereferenties, aan uw toepassingscode worden toegevoegd.

* Zodra een geheim volume is geïmplementeerd met geheimen in een containergroep, is *dit alleen-lezen.*
* Alle geheime volumes worden gebacked door [tmpfs,][tmpfs]een bestandssysteem met RAM-geheugen; hun inhoud wordt nooit naar niet-vluchtige opslag geschreven.

> [!NOTE]
> *Geheime* volumes zijn momenteel beperkt tot Linux-containers. Meer informatie over het doorgeven van beveiligde omgevingsvariabelen voor Windows- en Linux-containers in [Omgevingsvariabelen instellen.](container-instances-environment-variables.md) Terwijl we werken aan het toevoegen van alle functies aan Windows-containers, kunt u de huidige platformverschillen vinden in het [overzicht](container-instances-overview.md#linux-and-windows-containers).

## <a name="mount-secret-volume---azure-cli"></a>Geheim volume monteren - Azure CLI

Als u een container met een of meer geheimen wilt implementeren met behulp van de Azure CLI, neemt u de `--secrets` parameters en op in de opdracht az container `--secrets-mount-path` [create.][az-container-create] In dit voorbeeld wordt een *geheim* volume dat bestaat uit twee bestanden met geheimen, "mysecret1" en "mysecret2", op `/mnt/secrets` :

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name secret-volume-demo \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --secrets mysecret1="My first secret FOO" mysecret2="My second secret BAR" \
    --secrets-mount-path /mnt/secrets
```

In de [volgende az container exec output][az-container-exec] ziet u hoe u een shell opent in de container die wordt uitgevoerd, waarin de bestanden in het geheime volume worden weergegeven en de inhoud ervan wordt weergegeven:

```azurecli
az container exec \
  --resource-group myResourceGroup \
  --name secret-volume-demo --exec-command "/bin/sh"
```

```output
/usr/src/app # ls /mnt/secrets
mysecret1
mysecret2
/usr/src/app # cat /mnt/secrets/mysecret1
My first secret FOO
/usr/src/app # cat /mnt/secrets/mysecret2
My second secret BAR
/usr/src/app # exit
Bye.
```

## <a name="mount-secret-volume---yaml"></a>Geheim volume mount - YAML

U kunt ook containergroepen implementeren met de Azure CLI en een [YAML-sjabloon](container-instances-multi-container-yaml.md). Het implementeren op basis van een YAML-sjabloon is de voorkeursmethode bij het implementeren van containergroepen die uit meerdere containers bestaan.

Wanneer u implementeert met een YAML-sjabloon, moeten de geheime waarden **base64-gecodeerd** zijn in de sjabloon. De geheime waarden worden echter weergegeven in tekst zonder tekst in de bestanden in de container.

Met de volgende YAML-sjabloon definieert u een containergroep met één container die een *geheim* volume op `/mnt/secrets` mountt. Het geheime volume bevat twee bestanden met geheimen, 'mysecret1' en 'mysecret2'.

```yaml
apiVersion: '2019-12-01'
location: eastus
name: secret-volume-demo
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      environmentVariables: []
      image: mcr.microsoft.com/azuredocs/aci-helloworld:latest
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - mountPath: /mnt/secrets
        name: secretvolume1
  osType: Linux
  restartPolicy: Always
  volumes:
  - name: secretvolume1
    secret:
      mysecret1: TXkgZmlyc3Qgc2VjcmV0IEZPTwo=
      mysecret2: TXkgc2Vjb25kIHNlY3JldCBCQVIK
tags: {}
type: Microsoft.ContainerInstance/containerGroups
```

Als u wilt implementeren met de YAML-sjabloon, moet u de voorgaande YAML opslaan in een bestand met de naam en vervolgens de `deploy-aci.yaml` opdracht az container [create][az-container-create] uitvoeren met de `--file` parameter :

```azurecli-interactive
# Deploy with YAML template
az container create \
  --resource-group myResourceGroup \
  --file deploy-aci.yaml
```

## <a name="mount-secret-volume---resource-manager"></a>Geheim volume Resource Manager

Naast cli- en YAML-implementatie kunt u een containergroep implementeren met behulp van een Azure [Resource Manager sjabloon](/azure/templates/microsoft.containerinstance/containergroups).

Vul eerst de matrix `volumes` in de sectie containergroep van de `properties` sjabloon. Wanneer u implementeert met Resource Manager sjabloon, moeten de geheime waarden **base64-gecodeerd** zijn in de sjabloon. De geheime waarden worden echter weergegeven in tekst zonder tekst in de bestanden in de container.

Vul vervolgens voor elke container in de containergroep waarin u het geheime *volume* wilt monteren de matrix in de sectie van de `volumeMounts` `properties` containerdefinitie.

De volgende Resource Manager definieert een containergroep met één container die een *geheim* volume op mounts `/mnt/secrets` . Het geheime volume heeft twee geheimen: 'mysecret1' en 'mysecret2'.

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-secret.json -->
[!code-json[volume-secret](~/azure-docs-json-samples/container-instances/aci-deploy-volume-secret.json)]

Als u wilt implementeren met Resource Manager sjabloon, moet u de voorgaande JSON opslaan in een bestand met de naam en vervolgens de `deploy-aci.json` opdracht az deployment group [create][az-deployment-group-create] uitvoeren met de `--template-file` parameter :

```azurecli-interactive
# Deploy with Resource Manager template
az deployment group create \
  --resource-group myResourceGroup \
  --template-file deploy-aci.json
```

## <a name="next-steps"></a>Volgende stappen

### <a name="volumes"></a>Volumes

Meer informatie over het aan elkaar Azure Container Instances:

* [Een Azure-bestandsshare koppelen in Azure Container Instances](container-instances-volume-azure-files.md)
* [Een leegDir-volume in Azure Container Instances](container-instances-volume-emptydir.md)
* [Een gitRepo-volume in een Azure Container Instances](container-instances-volume-gitrepo.md)

### <a name="secure-environment-variables"></a>Omgevingsvariabelen beveiligen

Een andere methode voor het verstrekken van gevoelige informatie aan containers (inclusief Windows-containers) is het gebruik van [beveiligde omgevingsvariabelen.](container-instances-environment-variables.md#secure-values)

<!-- LINKS - External -->
[tmpfs]: https://wikipedia.org/wiki/Tmpfs

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-exec]: /cli/azure/container#az_container_exec
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create

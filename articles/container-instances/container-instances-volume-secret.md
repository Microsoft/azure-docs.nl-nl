---
title: Geheim volume aan container groep koppelen
description: Meer informatie over het koppelen van een geheim volume voor het opslaan van gevoelige informatie voor toegang door uw container instanties
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: ea82ba5994feaf102d4622eada284df431e004d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86169558"
---
# <a name="mount-a-secret-volume-in-azure-container-instances"></a>Een geheim volume koppelen in Azure Container Instances

Gebruik een *geheim* volume om gevoelige informatie op te geven voor de containers in een container groep. Met het *geheime* volume worden uw geheimen opgeslagen in bestanden in het volume, die toegankelijk zijn voor de containers in de container groep. Door geheimen op een *geheim* volume op te slaan, kunt u voor komen dat gevoelige gegevens zoals SSH-sleutels of database referenties aan uw toepassings code worden toegevoegd.

* Zodra de implementatie met geheimen in een container groep is geïmplementeerd, is een geheim volume *alleen-lezen*.
* Alle geheime volumes worden ondersteund door [tmpfs][tmpfs], een bestands systeem dat met een RAM-geheugen wordt ondersteund. de inhoud wordt nooit geschreven naar niet-vluchtige opslag.

> [!NOTE]
> *Geheime* volumes zijn momenteel beperkt tot Linux-containers. Meer informatie over het door geven van veilige omgevings variabelen voor Windows-en Linux-containers in [omgevings variabelen instellen](container-instances-environment-variables.md). Hoewel we aan de slag gaan met het toevoegen van alle functies aan Windows-containers, kunt u de huidige platform verschillen vinden in het [overzicht](container-instances-overview.md#linux-and-windows-containers).

## <a name="mount-secret-volume---azure-cli"></a>Geheim volume koppelen-Azure CLI

Als u een container met een of meer geheimen wilt implementeren met behulp van de Azure CLI, neemt u de `--secrets` `--secrets-mount-path` para meters en op in de opdracht [AZ container Create][az-container-create] . In dit voor beeld wordt een *geheim* volume dat bestaat uit twee bestanden met geheimen, ' mysecret1 ' en ' mysecret2 ', gekoppeld aan `/mnt/secrets` :

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name secret-volume-demo \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --secrets mysecret1="My first secret FOO" mysecret2="My second secret BAR" \
    --secrets-mount-path /mnt/secrets
```

In de volgende uitvoer van [AZ container exec][az-container-exec] ziet u een shell in de container die wordt uitgevoerd, waarin de bestanden in het geheime volume worden weer gegeven. vervolgens wordt de inhoud van het bestand geopend:

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

## <a name="mount-secret-volume---yaml"></a>Geheim volume koppelen-YAML

U kunt ook container groepen implementeren met de Azure CLI en een [yaml-sjabloon](container-instances-multi-container-yaml.md). Implementeren op basis van de YAML-sjabloon is de voorkeurs methode bij het implementeren van container groepen die uit meerdere containers bestaan.

Wanneer u implementeert met een YAML-sjabloon, moeten de geheime waarden in de sjabloon **Base64-gecodeerd** zijn. De geheime waarden worden echter weer gegeven als tekst zonder opmaak in de bestanden in de container.

De volgende YAML-sjabloon definieert een container groep met één container die een *geheim* volume koppelt op `/mnt/secrets` . Het geheime volume heeft twee bestanden met geheimen: ' mysecret1 ' en ' mysecret2 '.

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

Als u wilt implementeren met de YAML-sjabloon, slaat u de voor gaande YAML op in een bestand met de naam `deploy-aci.yaml` en voert u de opdracht [AZ container Create][az-container-create] uit met de `--file` para meter:

```azurecli-interactive
# Deploy with YAML template
az container create \
  --resource-group myResourceGroup \
  --file deploy-aci.yaml
```

## <a name="mount-secret-volume---resource-manager"></a>Geheim volume koppelen-Resource Manager

Naast de implementatie van CLI en YAML kunt u een container groep implementeren met behulp van een Azure [Resource Manager-sjabloon](/azure/templates/microsoft.containerinstance/containergroups).

Vul eerst de `volumes` matrix in het gedeelte container Group `properties` van de sjabloon. Wanneer u implementeert met een resource manager-sjabloon, moeten de geheime waarden in de sjabloon **Base64-gecodeerd** zijn. De geheime waarden worden echter weer gegeven als tekst zonder opmaak in de bestanden in de container.

Vervolgens vult u voor elke container in de container groep waarin u het *geheime* volume wilt koppelen, de `volumeMounts` matrix in de `properties` sectie van de container definitie.

De volgende Resource Manager-sjabloon definieert een container groep met één container die een *geheim* volume koppelt op `/mnt/secrets` . Het geheime volume heeft twee geheimen: ' mysecret1 ' en ' mysecret2 '.

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-secret.json -->
[!code-json[volume-secret](~/azure-docs-json-samples/container-instances/aci-deploy-volume-secret.json)]

Als u wilt implementeren met de Resource Manager-sjabloon, slaat u de voor gaande JSON op in een bestand met de naam `deploy-aci.json` en voert u de opdracht [AZ Deployment Group Create][az-deployment-group-create] uit met de `--template-file` para meter:

```azurecli-interactive
# Deploy with Resource Manager template
az deployment group create \
  --resource-group myResourceGroup \
  --template-file deploy-aci.json
```

## <a name="next-steps"></a>Volgende stappen

### <a name="volumes"></a>Volumes

Meer informatie over het koppelen van andere volume typen in Azure Container Instances:

* [Een Azure-bestandsshare koppelen in Azure Container Instances](container-instances-volume-azure-files.md)
* [Een emptyDir-volume koppelen in Azure Container Instances](container-instances-volume-emptydir.md)
* [Een gitRepo-volume koppelen in Azure Container Instances](container-instances-volume-gitrepo.md)

### <a name="secure-environment-variables"></a>Beveiligde omgevings variabelen

Een andere methode voor het bieden van gevoelige informatie aan containers (inclusief Windows-containers) is het gebruik van [beveiligde omgevings variabelen](container-instances-environment-variables.md#secure-values).

<!-- LINKS - External -->
[tmpfs]: https://wikipedia.org/wiki/Tmpfs

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create

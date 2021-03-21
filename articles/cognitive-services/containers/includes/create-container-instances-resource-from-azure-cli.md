---
title: Ondersteuning voor containers
titleSuffix: Azure Cognitive Services
description: Meer informatie over het maken van een Azure container Instance-bron vanuit de Azure CLI.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 87007d3df3fe44ab04a330b09b8e495ec4b47e54
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97866088"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>Een Azure container Instance-bron maken vanuit de Azure CLI

In de onderstaande YAML wordt de resource van het Azure-container exemplaar gedefinieerd. Kopieer de inhoud en plak deze in een nieuw bestand met de naam `my-aci.yaml` en vervang de waarden voor de opmerkingen door uw eigen. Raadpleeg de indeling van de [sjabloon][template-format] voor geldige YAML. Raadpleeg de [container opslagplaatsen en installatie kopieën][repositories-and-images] voor de beschik bare afbeeldings namen en de bijbehorende opslag plaats. Zie [yaml Reference: Azure container instances][aci-yaml-ref]voor meer informatie over de yaml-verwijzing voor container exemplaren.

```YAML
apiVersion: 2018-10-01
location: # < Valid location >
name: # < Container Group name >
properties:
  imageRegistryCredentials: # This is only required if you are pulling a non-public image that requires authentication to access. For example Text Analytics for health.
  - server: containerpreview.azurecr.io
    username: # < The username for the preview container registry >
    password: # < The password for the preview container registry >
  containers:
  - name: # < Container name >
    properties:
      image: # < Repository/Image name >
      environmentVariables: # These env vars are required
        - name: eula
          value: accept
        - name: billing
          value: # < Service specific Endpoint URL >
        - name: apikey
          value: # < Service specific API key >
      resources:
        requests:
          cpu: 4 # Always refer to recommended minimal resources
          memoryInGb: 8 # Always refer to recommended minimal resources
      ports:
        - port: 5000
  osType: Linux
  volumes: # This node, is only required for container instances that pull their model in at runtime, such as LUIS.
  - name: aci-file-share
    azureFile:
      shareName: # < File share name >
      storageAccountName: # < Storage account name>
      storageAccountKey: # < Storage account key >
  restartPolicy: OnFailure
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: 5000
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

> [!NOTE]
> Niet alle locaties hebben dezelfde Beschik baarheid van de CPU en het geheugen. Raadpleeg de tabel [locatie en resources][location-to-resource] voor de lijst met beschik bare resources voor containers per locatie en besturings systeem.

We vertrouwen op het YAML-bestand dat we voor de [`az container create`][azure-container-create] opdracht hebben gemaakt. Voer vanuit de Azure CLI de opdracht uit, waarbij u de kunt `az container create` vervangen `<resource-group>` door uw eigen. Voor het beveiligen van waarden binnen een implementatie van een YAML raadpleegt u ook [beveiligde waarden][secure-values].

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

De uitvoer van de opdracht is `Running...` als geldig, nadat de uitvoer is gewijzigd in een JSON-teken reeks die de zojuist gemaakte ACI-resource vertegenwoordigt. De container installatie kopie is meer dan waarschijnlijk niet beschikbaar voor een tijdje, maar de resource wordt nu geïmplementeerd.

> [!TIP]
> Let op de locatie van de open bare preview-versie van Azure cognitieve Services, aangezien de YAML moet worden aangepast aan de plaats waar ze overeenkomen.

[azure-container-create]: /cli/azure/container#az-container-create
[template-format]: /azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format
[aci-yaml-ref]: ../../../container-instances/container-instances-reference-yaml.md
[repositories-and-images]: ../container-image-tags.md
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values

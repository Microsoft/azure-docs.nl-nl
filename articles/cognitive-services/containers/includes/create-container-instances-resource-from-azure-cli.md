---
title: Ondersteuning voor containers
titleSuffix: Azure Cognitive Services
description: Meer informatie over het maken van een Azure-containerresource vanuit de Azure CLI.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 32b00e031e3cf865093c267084117a8704b6e272
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800099"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>Een Azure Container Instance-resource maken vanuit de Azure CLI

De yaml hieronder definieert de Azure Container Instance-resource. Kopieer en plak de inhoud in een nieuw bestand met de naam en `my-aci.yaml` vervang de opmerkingen door uw eigen waarden. Raadpleeg de [sjabloonindeling voor][template-format] geldige YAML. Raadpleeg de [containeropslagplaats en -afbeeldingen voor][repositories-and-images] de beschikbare namen van de afbeeldingen en de bijbehorende opslagplaats. Zie YAML-verwijzing: Azure Container Instances voor meer informatie over de [YAML-verwijzing voor container Azure Container Instances.][aci-yaml-ref]

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
> Niet alle locaties hebben dezelfde CPU- en geheugenbeschikbaarheid. Raadpleeg de tabel [locatie en resources][location-to-resource] voor de lijst met beschikbare resources voor containers per locatie en besturingssysteem.

We vertrouwen op het YAML-bestand dat we voor de opdracht hebben [`az container create`][azure-container-create] gemaakt. Voer vanuit de Azure CLI de opdracht `az container create` uit en vervang de door uw eigen `<resource-group>` opdracht. Voor het beveiligen van waarden binnen een YAML-implementatie raadpleegt u [bovendien beveiligde waarden][secure-values].

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

De uitvoer van de opdracht is indien geldig. Na enige tijd wordt de uitvoer gewijzigd in een JSON-tekenreeks die de zojuist gemaakte `Running...` ACI-resource vertegenwoordigt. De containerafbeelding is waarschijnlijk een tijdje niet beschikbaar, maar de resource is nu geïmplementeerd.

> [!TIP]
> Let goed op de locaties van de openbare preview-versie van Azure Cognitive Service-aanbiedingen, omdat de YAML dienovereenkomstig moet worden aangepast om aan de locatie te komen.

[azure-container-create]: /cli/azure/container#az_container_create
[template-format]: /azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format
[aci-yaml-ref]: ../../../container-instances/container-instances-reference-yaml.md
[repositories-and-images]: ../container-image-tags.md
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values

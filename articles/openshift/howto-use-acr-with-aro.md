---
title: Gebruik Azure Container Registry met Azure Red Hat open Shift
description: Meer informatie over het ophalen en uitvoeren van een container van Azure Container Registry in uw Azure Red Hat open Shift-cluster.
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 01/10/2021
ms.openlocfilehash: 651b73db084e8090f59faeffa9991c2ac468ca08
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100634410"
---
# <a name="use-azure-container-registry-with-azure-red-hat-openshift-aro"></a>Gebruik Azure Container Registry met Azure Red Hat open Shift (ARO)

Azure Container Registry (ACR) is een beheerde container register service die u kunt gebruiken om persoonlijke docker-container installatie kopieën op te slaan met bedrijfs mogelijkheden zoals geo-replicatie. Voor toegang tot de ACR van een ARO-cluster kan het cluster worden geverifieerd met ACR door docker-aanmeldings referenties op te slaan in een Kubernetes-geheim.  Op dezelfde manier kan een ARO-cluster een imagePullSecret in de pod spec gebruiken om te verifiëren bij het REGI ster bij het ophalen van de installatie kopie. In dit artikel leert u hoe u een Azure Container Registry kunt instellen met een Azure Red Hat open Shift-cluster om privé-docker-container installatie kopieën op te slaan en uit te pakken.

## <a name="prerequisites"></a>Vereisten

In deze hand leiding wordt ervan uitgegaan dat u een bestaande Azure Container Registry hebt. Als u dit niet doet, gebruikt u de [instructies Azure portal of Azure cli](../container-registry/container-registry-get-started-azure-cli.md) om een container register te maken.

In dit artikel wordt ook ervan uitgegaan dat u een bestaand Azure Red Hat open Shift-cluster hebt en dat de `oc` cli is geïnstalleerd. Als dat niet het geval is, volgt u de instructies in de [zelf studie Aro-cluster maken](tutorial-create-cluster.md).

## <a name="get-a-pull-secret"></a>Een pull-geheim ophalen

U hebt een pull-geheim van ACR nodig om toegang te krijgen tot het REGI ster vanuit uw ARO-cluster.

Als u uw pull-geheime referenties wilt ophalen, kunt u de Azure Portal of de Azure CLI gebruiken.

Als u de Azure Portal gebruikt, gaat u naar uw ACR-exemplaar en selecteert u **toegangs sleutels**.  Uw `docker-username` is de naam van het container register, gebruik daarom een wacht woord of password2 voor `docker-password` .

![Toegangs sleutels](./media/acr-access-keys.png)

In plaats daarvan kunt u de Azure CLI gebruiken om deze referenties op te halen:
```azurecli
az acr credential show -n <your registry name>
```

## <a name="create-the-kubernetes-secret"></a>Het Kubernetes-geheim maken

Nu gaan we deze referenties gebruiken om een Kubernetes-geheim te maken. Voer de volgende opdracht uit met uw ACR-referenties:

```
oc create secret docker-registry \
    --docker-server=<your registry name>.azurecr.io \
    --docker-username=<your registry name> \
    --docker-password=******** \
    --docker-email=unused \
    acr-secret
```

>[!NOTE]
>Dit geheim wordt opgeslagen in het huidige open Shift-project (Kubernetes-naam ruimte) en er wordt alleen naar verwezen door een Peul dat in het project is gemaakt.  Raadpleeg dit [document](https://docs.openshift.com/container-platform/4.4/openshift_images/managing_images/using-image-pull-secrets.html) voor meer instructies voor het maken van een cluster Wide pull Secret.

## <a name="create-a-pod-using-a-private-registry-image"></a>Een pod maken met behulp van een persoonlijke register installatie kopie

Nu u uw ARO-cluster hebt aangesloten op uw ACR, kunt u een installatie kopie van uw ACR halen om een pod te maken.

Begin met een podSpec en geef het geheim op dat u hebt gemaakt als een imagePullSecret:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hello-world
spec:
  containers:
  - name: hello-world
    image: <your registry name>.azurecr.io/hello-world:v1
  imagePullSecrets:
  - name: acr-secret
```

Als u wilt testen of uw Pod actief is, voert u deze opdracht uit en wacht u totdat de status wordt **uitgevoerd**:

```bash
$ oc get pods --watch
NAME         READY   STATUS    RESTARTS   AGE
hello-world  1/1     Running   0          30s
```

## <a name="next-steps"></a>Volgende stappen

* [Azure Container Registry](../container-registry/container-registry-concepts.md)
* [Quickstart: Een privé-containerregister maken met Azure CLI](../container-registry/container-registry-get-started-azure-cli.md)

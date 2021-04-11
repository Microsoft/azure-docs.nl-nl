---
title: Certificaten in azure Kubernetes service (AKS) draaien
description: Meer informatie over het draaien van uw certificaten in een Azure Kubernetes service (AKS)-cluster.
services: container-service
ms.topic: article
ms.date: 11/15/2019
ms.openlocfilehash: fa26762c54ad54835b174b8d814a2e77cb38b885
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102619032"
---
# <a name="rotate-certificates-in-azure-kubernetes-service-aks"></a>Certificaten in azure Kubernetes service (AKS) draaien

Azure Kubernetes service (AKS) gebruikt certificaten voor verificatie met veel onderdelen. Het kan zijn dat u deze certificaten regel matig moet draaien om beveiligings-of beleids redenen. U kunt bijvoorbeeld een beleid hebben om elke 90 dagen alle certificaten te draaien.

In dit artikel wordt beschreven hoe u de certificaten in uw AKS-cluster roteert.

## <a name="before-you-begin"></a>Voordat u begint

Voor dit artikel moet u de Azure CLI-versie 2.0.77 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli-install] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="aks-certificates-certificate-authorities-and-service-accounts"></a>AKS certificaten, certificerings instanties en service accounts

AKS genereert en gebruikt de volgende certificaten, certificerings instanties en service accounts:

* De AKS API-server maakt een certificerings instantie (CA) die de cluster-CA wordt genoemd.
* De API-server heeft een cluster-CA, die certificaten ondertekent voor eenrichtings communicatie van de API-server naar kubelets.
* Elke kubelet maakt ook een aanvraag voor certificaat ondertekening (CSR), die door de cluster-CA is ondertekend, voor communicatie van de kubelet naar de API-server.
* De API aggregator gebruikt de cluster-CA om certificaten uit te geven voor communicatie met andere Api's. De API aggregator kan ook een eigen certificerings instantie hebben voor het uitgeven van die certificaten, maar maakt momenteel gebruik van de cluster-CA.
* Elk knoop punt maakt gebruik van een token voor service accounts (SA), dat is ondertekend door de cluster-CA.
* De `kubectl` client heeft een certificaat voor communicatie met het AKS-cluster.

> [!NOTE]
> AKS-clusters die vóór maart 2019 zijn gemaakt, hebben certificaten die na twee jaar verlopen. Elk cluster dat is gemaakt na 2019 maart of een cluster met de geroteerde certificaten heeft cluster-CA-certificaten die na 30 jaar verlopen. Alle andere certificaten verlopen na twee jaar. Als u wilt controleren wanneer het cluster is gemaakt, gebruikt `kubectl get nodes` u om de *leeftijd* van de knooppunt groepen weer te geven.
> 
> Daarnaast kunt u de verval datum van het certificaat van uw cluster controleren. Met de volgende bash-opdracht worden bijvoorbeeld de certificaat gegevens voor het *myAKSCluster* -cluster weer gegeven.
> ```console
> kubectl config view --raw -o jsonpath="{.clusters[?(@.name == 'myAKSCluster')].cluster.certificate-authority-data}" | base64 -d | openssl x509 -text | grep -A2 Validity
> ```

## <a name="rotate-your-cluster-certificates"></a>Uw cluster certificaten draaien

> [!WARNING]
> Het draaien van uw certificaten met `az aks rotate-certs` kan tot 30 minuten uitval tijd voor uw AKS-cluster leiden.

Gebruik [AZ AKS Get-referenties][az-aks-get-credentials] om u aan te melden bij uw AKS-cluster. Met deze opdracht wordt ook het `kubectl` client certificaat op uw lokale computer gedownload en geconfigureerd.

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

Gebruiken `az aks rotate-certs` om alle certificaten, ca's en sa's in uw cluster te draaien.

```azurecli
az aks rotate-certs -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

> [!IMPORTANT]
> Het kan tot 30 minuten duren voordat de bewerking `az aks rotate-certs` is voltooid. Als de opdracht mislukt voordat deze wordt voltooid, gebruikt `az aks show` u om te controleren of de status van het cluster is *gedraaid*. Als het cluster zich in een mislukte status bevindt, voert `az aks rotate-certs` u de bewerking opnieuw uit om uw certificaten opnieuw te draaien.

Controleer of de oude certificaten niet meer geldig zijn door een opdracht uit te voeren `kubectl` . Omdat u de gebruikte certificaten niet hebt bijgewerkt `kubectl` , wordt er een fout weer geven.  Bijvoorbeeld:

```console
$ kubectl get no
Unable to connect to the server: x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "ca")
```

Update het certificaat dat wordt gebruikt door `kubectl` uit te voeren `az aks get-credentials` .

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME --overwrite-existing
```

Controleer of de certificaten zijn bijgewerkt door een `kubectl` opdracht uit te voeren die nu slaagt. Bijvoorbeeld:

```console
kubectl get no
```

> [!NOTE]
> Als u Services hebt die boven op AKS worden uitgevoerd, zoals [Azure dev Spaces][dev-spaces], moet u mogelijk ook [certificaten bijwerken die betrekking hebben op deze services][dev-spaces-rotate] .

## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt uitgelegd hoe u de certificaten, Ca's en Sa's van uw cluster automatisch kunt draaien. U kunt [Best practices voor cluster beveiliging en upgrades in azure Kubernetes service (AKS)][aks-best-practices-security-upgrades] bekijken voor meer informatie over de aanbevolen procedures voor beveiliging van AKS.


[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[aks-best-practices-security-upgrades]: operator-best-practices-cluster-security.md
[dev-spaces]: ../dev-spaces/index.yml
[dev-spaces-rotate]: ../dev-spaces/troubleshooting.md#error-using-dev-spaces-after-rotating-aks-certificates

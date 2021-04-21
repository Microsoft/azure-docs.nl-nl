---
title: Certificaten roteren in Azure Kubernetes Service (AKS)
description: Meer informatie over het roteren van uw certificaten in een Azure Kubernetes Service cluster (AKS).
services: container-service
ms.topic: article
ms.date: 11/15/2019
ms.openlocfilehash: 6baad681a9d629c397c53ab90057cc5746fc3b85
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776011"
---
# <a name="rotate-certificates-in-azure-kubernetes-service-aks"></a>Certificaten roteren in Azure Kubernetes Service (AKS)

Azure Kubernetes Service (AKS) gebruikt certificaten voor verificatie met veel van de onderdelen. Om beveiligings- of beleidsredenen moet u deze certificaten mogelijk periodiek roteren. U kunt bijvoorbeeld een beleid hebben om al uw certificaten elke 90 dagen te roteren.

In dit artikel wordt beschreven hoe u de certificaten in uw AKS-cluster roteert.

## <a name="before-you-begin"></a>Voordat u begint

Voor dit artikel moet u Azure CLI versie 2.0.77 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli-install] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="aks-certificates-certificate-authorities-and-service-accounts"></a>AKS-certificaten, certificeringsinstanties en serviceaccounts

AKS genereert en gebruikt de volgende certificaten, certificeringsinstanties en serviceaccounts:

* De AKS API-server maakt een certificeringsinstantie (CA) met de naam cluster-CA.
* De API-server heeft een cluster-CA, die certificaten ondertekent voor een een way communication van de API-server naar kubelets.
* Elke kubelet maakt ook een Certificate Signing Request (CSR), die is ondertekend door de cluster-CA, voor communicatie van de kubelet naar de API-server.
* De API-aggregator gebruikt de cluster-CA om certificaten uit te geven voor communicatie met andere API's. De API-aggregator kan ook een eigen CA hebben voor het uitgeven van deze certificaten, maar gebruikt momenteel de cluster-CA.
* Elk knooppunt maakt gebruik van een SA-token (Service Account), dat is ondertekend door de cluster-CA.
* De `kubectl` client heeft een certificaat voor communicatie met het AKS-cluster.

> [!NOTE]
> AKS-clusters die vóór maart 2019 zijn gemaakt, hebben certificaten die na twee jaar verlopen. Elk cluster dat is gemaakt na maart 2019 of een cluster dat de certificaten roteert, heeft CA-clustercertificaten die na 30 jaar verlopen. Alle andere certificaten verlopen na twee jaar. Als u wilt controleren wanneer uw cluster is gemaakt, gebruikt `kubectl get nodes` u om de Leeftijd van uw knooppuntgroepen te bekijken. 
> 
> Daarnaast kunt u de vervaldatum van het certificaat van uw cluster controleren. Met de volgende Bash-opdracht worden bijvoorbeeld de certificaatgegevens voor het *cluster myAKSCluster* weergegeven.
> ```console
> kubectl config view --raw -o jsonpath="{.clusters[?(@.name == 'myAKSCluster')].cluster.certificate-authority-data}" | base64 -d | openssl x509 -text | grep -A2 Validity
> ```

## <a name="rotate-your-cluster-certificates"></a>Uw clustercertificaten roteren

> [!WARNING]
> Het roteren van uw certificaten `az aks rotate-certs` met behulp van kan tot 30 minuten downtime voor uw AKS-cluster veroorzaken.

Gebruik [az aks get-credentials om][az-aks-get-credentials] u aan te melden bij uw AKS-cluster. Met deze opdracht wordt ook het clientcertificaat op `kubectl` uw lokale computer gedownload en geconfigureerd.

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

Gebruik `az aks rotate-certs` om alle certificaten, CERTIFICERINGscertificaten en SA's in uw cluster te roteren.

```azurecli
az aks rotate-certs -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

> [!IMPORTANT]
> Het kan tot 30 minuten duren voordat `az aks rotate-certs` de taak is voltooid. Als de opdracht mislukt voordat u deze voltooit, gebruikt u `az aks show` om te controleren of de status van het cluster Certificaat *roteren* is. Als het cluster de status Mislukt heeft, moet u opnieuw proberen `az aks rotate-certs` om uw certificaten opnieuw te roteren.

Controleer of de oude certificaten niet meer geldig zijn door een opdracht uit te `kubectl` voeren. Omdat u de certificaten die worden gebruikt door niet hebt `kubectl` bijgewerkt, wordt er een foutbericht weergegeven.  Bijvoorbeeld:

```console
$ kubectl get no
Unable to connect to the server: x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "ca")
```

Werk het certificaat bij dat wordt gebruikt `kubectl` door uit te `az aks get-credentials` laten.

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME --overwrite-existing
```

Controleer of de certificaten zijn bijgewerkt door een opdracht uit `kubectl` te voeren, die nu slaagt. Bijvoorbeeld:

```console
kubectl get no
```

> [!NOTE]
> Als u services hebt die boven op AKS worden uitgevoerd, zoals [Azure Dev Spaces,][dev-spaces]moet u mogelijk ook certificaten bijwerken die betrekking hebben op deze [services.][dev-spaces-rotate]

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u laten zien hoe u de certificaten, CERTIFICERINGscertificaten en SA's van uw cluster automatisch kunt roteren. Zie Best [practices for cluster security and upgrades in Azure Kubernetes Service (AKS) (Best practices voor AKS-beveiliging)][aks-best-practices-security-upgrades] voor meer informatie.


[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[aks-best-practices-security-upgrades]: operator-best-practices-cluster-security.md
[dev-spaces]: ../dev-spaces/index.yml
[dev-spaces-rotate]: ../dev-spaces/troubleshooting.md#error-using-dev-spaces-after-rotating-aks-certificates

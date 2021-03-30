---
title: Het opnieuw opstarten van Linux-knoop punten met kured
titleSuffix: Azure Kubernetes Service
description: Meer informatie over het bijwerken van Linux-knoop punten en het automatisch opnieuw opstarten met kured in azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 02/28/2019
ms.openlocfilehash: 35c9e76c234e4b09fbb090eda363506ee3e11130
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88164237"
---
# <a name="apply-security-and-kernel-updates-to-linux-nodes-in-azure-kubernetes-service-aks"></a>Beveiligings-en kernel-updates Toep assen op Linux-knoop punten in azure Kubernetes service (AKS)

Voor het beveiligen van uw clusters worden beveiligings updates automatisch toegepast op Linux-knoop punten in AKS. Deze updates omvatten beveiligings correcties voor het besturings systeem of kernel-updates. Voor sommige van deze updates moet het knoop punt opnieuw worden opgestart om het proces te volt ooien. AKS start deze Linux-knoop punten niet automatisch opnieuw op om het update proces te volt ooien.

Het proces voor het up-to-date houden van Windows Server-knoop punten is iets anders. Windows Server-knoop punten ontvangen geen dagelijkse updates. In plaats daarvan voert u een AKS-upgrade uit waarmee nieuwe knoop punten worden geïmplementeerd met de meest recente installatie kopie van het basis venster en patches. Zie [een knooppunt groep bijwerken in AKS][nodepool-upgrade]voor AKS-clusters die gebruikmaken van Windows Server-knoop punten.

Dit artikel laat u zien hoe u de open-source- [kured (KUbernetes REboot daemon)][kured] kunt gebruiken om te kijken naar Linux-knoop punten die opnieuw moeten worden opgestart. vervolgens wordt de herplanning van het uitvoeren van het proces van peulen en het opnieuw opstarten van het knoop punt automatisch verwerkt.

> [!NOTE]
> `Kured` is een open-source project door Weaveworks. Ondersteuning voor dit project in AKS wordt op de beste manier geboden. Aanvullende ondersteuning vindt u in het #weave-communautaire vertragings kanaal.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u beschikt over een bestaand AKS-cluster. Als u een AKS-cluster nodig hebt, raadpleegt u de AKS Quick Start [met behulp van de Azure cli][aks-quickstart-cli] of [met behulp van de Azure Portal][aks-quickstart-portal].

Ook moet de Azure CLI-versie 2.0.59 of hoger zijn geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][install-azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="understand-the-aks-node-update-experience"></a>Meer informatie over de update-ervaring van het AKS-knoop punt

In een AKS-cluster worden de Kubernetes-knoop punten uitgevoerd als Azure virtual machines (Vm's). Deze Vm's op basis van Linux gebruiken een Ubuntu-installatie kopie, waarbij het besturings systeem zo is geconfigureerd dat elke nacht automatisch wordt gecontroleerd op updates. Als er beveiligings-of kernel-updates beschikbaar zijn, worden deze automatisch gedownload en geïnstalleerd.

![AKS-knoop punt bijwerken en opnieuw opstarten met kured](media/node-updates-kured/node-reboot-process.png)

Voor sommige beveiligings updates, zoals kernel-updates, moet een knoop punt opnieuw worden opgestart om het proces te volt ooien. Voor een Linux-knoop punt dat opnieuw moet worden opgestart, wordt een bestand met de naam */var/run/reboot-required* gemaakt. Het opnieuw opstarten wordt niet automatisch uitgevoerd.

U kunt uw eigen werk stromen en processen gebruiken om het opnieuw opstarten van knoop punten af te handelen of `kured` om het proces te organiseren. Met `kured` is een [daemonset][DaemonSet] geïmplementeerd waarbij een pod wordt uitgevoerd op elk Linux-knoop punt in het cluster. Deze peuling in de Daemonset-horloge voor aanwezigheid van het */var/run/reboot-required* -bestand en initieert vervolgens een proces om de knoop punten opnieuw op te starten.

### <a name="node-upgrades"></a>Knooppunt upgrades

Er is een aanvullend proces in AKS waarmee u een cluster kunt *upgraden* . Een upgrade is doorgaans om over te stappen op een nieuwere versie van Kubernetes, niet alleen de beveiligings updates van knoop punten toe te passen. Met een AKS-upgrade worden de volgende acties uitgevoerd:

* Er wordt een nieuw knoop punt geïmplementeerd met de meest recente beveiligings updates en Kubernetes-versie toegepast.
* Een oud knoop punt is afgebakend en is leeg.
* Er worden peulen gepland op het nieuwe knoop punt.
* Het oude knoop punt wordt verwijderd.

Tijdens een upgrade gebeurtenis kunt u niet op dezelfde Kubernetes-versie blijven. U moet een nieuwere versie van Kubernetes opgeven. Als u een upgrade wilt uitvoeren naar de nieuwste versie van Kubernetes, kunt u [uw AKS-cluster bijwerken][aks-upgrade].

## <a name="deploy-kured-in-an-aks-cluster"></a>Kured implementeren in een AKS-cluster

Als u de `kured` daemonset wilt implementeren, installeert u de volgende officiële Kured helm-grafiek. Hiermee maakt u een rol en cluster functie, bindingen en een service account en implementeert vervolgens de Daemonset met `kured` .

```console
# Add the Kured Helm repository
helm repo add kured https://weaveworks.github.io/kured

# Update your local Helm chart repository cache
helm repo update

# Create a dedicated namespace where you would like to deploy kured into
kubectl create namespace kured

# Install kured in that namespace with Helm 3 (only on Linux nodes, kured is not working on Windows nodes)
helm install kured kured/kured --namespace kured --set nodeSelector."beta\.kubernetes\.io/os"=linux
```

U kunt ook aanvullende para meters voor configureren `kured` , zoals integratie met Prometheus of toegestane vertraging. Zie de [Kured helm-grafiek][kured-install]voor meer informatie over aanvullende configuratie parameters.

## <a name="update-cluster-nodes"></a>Cluster knooppunten bijwerken

Linux-knoop punten in AKS controleren standaard elke avond op updates. Als u niet wilt wachten, kunt u hand matig een update uitvoeren om te controleren of deze `kured` correct wordt uitgevoerd. Volg eerst de stappen voor [SSH naar een van uw AKS-knoop punten][aks-ssh]. Zodra u een SSH-verbinding met het Linux-knoop punt hebt, controleert u of er updates zijn en past u deze als volgt toe:

```console
sudo apt-get update && sudo apt-get upgrade -y
```

Als er updates zijn toegepast waarvoor het opnieuw opstarten van een knoop punt is vereist, wordt een bestand naar */var/run/reboot-required* geschreven. `Kured` Hiermee wordt gecontroleerd op knoop punten die standaard elke 60 minuten opnieuw moeten worden opgestart.

## <a name="monitor-and-review-reboot-process"></a>Het proces voor het opnieuw opstarten controleren en controleren

Wanneer een van de replica's in de Daemonset heeft gedetecteerd dat het opnieuw opstarten van een knoop punt vereist is, wordt er een vergren deling op het knoop punt geplaatst via de Kubernetes-API. Deze vergren deling voor komt dat er meer peulen op het knoop punt worden gepland. Met de vergren deling wordt ook aangegeven dat er slechts één knoop punt tegelijk opnieuw moet worden opgestart. Wanneer het knoop punt afgebakend uit wordt uitgevoerd, wordt het uitvoeren van peulen uit het knoop punt verleegd en wordt het knoop punt opnieuw opgestart.

U kunt de status van de knoop punten bewaken met behulp van de kubectl-opdracht [knoop punten ophalen][kubectl-get-nodes] . In de volgende voorbeeld uitvoer ziet u een knoop punt met de status *SchedulingDisabled* , omdat het knoop punt wordt voor bereid voor het opstart proces:

```
NAME                       STATUS                     ROLES     AGE       VERSION
aks-nodepool1-28993262-0   Ready,SchedulingDisabled   agent     1h        v1.11.7
```

Zodra het update proces is voltooid, kunt u de status van de knoop punten weer geven met behulp van de kubectl-opdracht [knoop punten ophalen][kubectl-get-nodes] met de `--output wide` para meter. Met deze aanvullende uitvoer ziet u een verschil in de *kernel-versie* van de onderliggende knoop punten, zoals wordt weer gegeven in de volgende voorbeeld uitvoer. De *AKS-nodepool1-28993262-0* is in een vorige stap bijgewerkt en toont de kernel *-versie 4.15.0-1039-Azure*. Het knoop punt *AKS-nodepool1-28993262-1* dat niet is bijgewerkt, toont de kernel *-versie 4.15.0-1037-Azure*.

```
NAME                       STATUS    ROLES     AGE       VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-28993262-0   Ready     agent     1h        v1.11.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS   4.15.0-1039-azure   docker://3.0.4
aks-nodepool1-28993262-1   Ready     agent     1h        v1.11.7   10.240.0.5    <none>        Ubuntu 16.04.6 LTS   4.15.0-1037-azure   docker://3.0.4
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt beschreven hoe u `kured` Linux-knoop punten automatisch opnieuw kunt opstarten als onderdeel van het beveiligings update proces. Als u een upgrade wilt uitvoeren naar de nieuwste versie van Kubernetes, kunt u [uw AKS-cluster bijwerken][aks-upgrade].

Zie [een knooppunt groep bijwerken in AKS][nodepool-upgrade]voor AKS-clusters die gebruikmaken van Windows Server-knoop punten.

<!-- LINKS - external -->
[kured]: https://github.com/weaveworks/kured
[kured-install]: https://github.com/weaveworks/kured/tree/master/charts/kured
[kubectl-get-nodes]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[DaemonSet]: concepts-clusters-workloads.md#statefulsets-and-daemonsets
[aks-ssh]: ssh.md
[aks-upgrade]: upgrade-cluster.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool

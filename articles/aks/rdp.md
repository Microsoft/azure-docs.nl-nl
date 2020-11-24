---
title: RDP naar AKS Windows Server-knoop punten
titleSuffix: Azure Kubernetes Service
description: Meer informatie over het maken van een RDP-verbinding met Windows Server-knoop punten van Azure Kubernetes service (AKS) voor het oplossen van problemen en onderhouds taken.
services: container-service
ms.topic: article
ms.date: 06/04/2019
ms.openlocfilehash: c774e2287d0540c73cdd2234843d6766e7f2fb91
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95792254"
---
# <a name="connect-with-rdp-to-azure-kubernetes-service-aks-cluster-windows-server-nodes-for-maintenance-or-troubleshooting"></a>Verbinding maken met de cluster Windows Server-knoop punten van RDP naar Azure Kubernetes service (AKS) voor onderhoud of probleem oplossing

Gedurende de levens cyclus van uw Azure Kubernetes service (AKS)-cluster hebt u mogelijk toegang tot een AKS Windows Server-knoop punt. Deze toegang kan voor onderhoud, logboek verzameling of andere bewerkingen voor het oplossen van problemen zijn. U kunt toegang krijgen tot de AKS Windows Server-knoop punten met behulp van RDP. Als u SSH wilt gebruiken om toegang te krijgen tot de AKS Windows Server-knoop punten en u toegang hebt tot hetzelfde sleutel paar dat is gebruikt tijdens het maken van het cluster, kunt u de stappen in SSH volgen in [Azure Kubernetes service (AKS)-cluster knooppunten][ssh-steps]. Uit veiligheids overwegingen worden de AKS-knoop punten niet blootgesteld aan Internet.

In dit artikel wordt beschreven hoe u een RDP-verbinding met een AKS-knoop punt maakt met behulp van hun privé-IP-adressen.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u een bestaand AKS-cluster hebt met een Windows Server-knoop punt. Als u een AKS-cluster nodig hebt, raadpleegt u het artikel over het [maken van een AKS-cluster met een Windows-container met behulp van de Azure cli][aks-windows-cli]. U hebt de gebruikers naam en het wacht woord van de Windows-beheerder nodig voor het Windows Server-knoop punt dat u wilt oplossen. Als u ze niet kent, kunt u ze opnieuw instellen door [extern bureaublad-services of het beheerders wachtwoord opnieuw in te stellen in een Windows-VM ](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/reset-rdp). U hebt ook een RDP-client nodig, zoals [Microsoft extern bureaublad][rdp-mac].

Ook moet de Azure CLI-versie 2.0.61 of hoger zijn geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][install-azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="deploy-a-virtual-machine-to-the-same-subnet-as-your-cluster"></a>Een virtuele machine implementeren op hetzelfde subnet als uw cluster

De Windows Server-knoop punten van uw AKS-cluster hebben geen extern toegankelijke IP-adressen. Als u een RDP-verbinding wilt maken, kunt u een virtuele machine met een openbaar toegankelijk IP-adres implementeren op hetzelfde subnet als uw Windows Server-knoop punten.

In het volgende voor beeld wordt een virtuele machine met de naam *myVM* gemaakt in de resource groep *myResourceGroup* .

Haal eerst het subnet op dat wordt gebruikt door de Windows Server-knooppunt groep. U hebt de naam van het subnet nodig om de subnet-id op te halen. Als u de naam van het subnet wilt ophalen, moet u de naam van het vnet hebben. Haal de vnet-naam op door een query uit te geven op uw cluster voor de lijst met netwerken. Als u een query wilt uitvoeren op het cluster, hebt u de naam nodig. U kunt dit allemaal doen door het volgende in het Azure Cloud Shell uit te voeren:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
VNET_NAME=$(az network vnet list -g $CLUSTER_RG --query [0].name -o tsv)
SUBNET_NAME=$(az network vnet subnet list -g $CLUSTER_RG --vnet-name $VNET_NAME --query [0].name -o tsv)
SUBNET_ID=$(az network vnet subnet show -g $CLUSTER_RG --vnet-name $VNET_NAME --name $SUBNET_NAME --query id -o tsv)
```

Nu u de SUBNET_ID hebt, voert u de volgende opdracht uit in hetzelfde Azure Cloud Shell venster om de VM te maken:

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2019datacenter \
    --admin-username azureuser \
    --admin-password myP@ssw0rd12 \
    --subnet $SUBNET_ID \
    --query publicIpAddress -o tsv
```

In de volgende voorbeeld uitvoer ziet u dat de VM is gemaakt en wordt het open bare IP-adres van de virtuele machine weer gegeven.

```console
13.62.204.18
```

Registreer het open bare IP-adres van de virtuele machine. U gebruikt dit adres in een latere stap.

## <a name="allow-access-to-the-virtual-machine"></a>Toegang tot de virtuele machine toestaan

Subnetten van AKS-knooppunt groep worden standaard beveiligd met Nsg's (netwerk beveiligings groepen). Als u toegang wilt krijgen tot de virtuele machine, moet u toegang inschakelen in de NSG.

> [!NOTE]
> De Nsg's worden bepaald door de AKS-service. Alle wijzigingen die u in de NSG aanbrengt, worden op elk gewenst moment overschreven door het besturings vlak.
>

Haal eerst de resource groep en de naam van de NSG op van de NSG om de regel toe te voegen aan:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NSG_NAME=$(az network nsg list -g $CLUSTER_RG --query [].name -o tsv)
```

Maak vervolgens de NSG-regel:

```azurecli-interactive
az network nsg rule create --name tempRDPAccess --resource-group $CLUSTER_RG --nsg-name $NSG_NAME --priority 100 --destination-port-range 3389 --protocol Tcp --description "Temporary RDP access to Windows nodes"
```

## <a name="get-the-node-address"></a>Het knooppunt adres ophalen

Als u een Kubernetes-cluster wilt beheren, gebruikt u [kubectl][kubectl], de Kubernetes-opdrachtregelclient. Als u Azure Cloud Shell gebruikt, is `kubectl` al geïnstalleerd. Als u `kubectl` lokaal wilt installeren, gebruikt u de opdracht [az aks install-cli][az-aks-install-cli]:
    
```azurecli-interactive
az aks install-cli
```

Gebruik de opdracht [az aks get-credentials][az-aks-get-credentials] om `kubectl` zodanig te configureren dat er verbinding wordt gemaakt met het Kubernetes-cluster. Bij deze opdracht worden referenties gedownload en wordt Kubernetes CLI geconfigureerd voor het gebruik van deze referenties.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Het interne IP-adres van de Windows Server-knoop punten weer geven met behulp van de opdracht [kubectl Get][kubectl-get] :

```console
kubectl get nodes -o wide
```

In de uitvoer van het volgende voor beeld ziet u de interne IP-adressen van alle knoop punten in het cluster, met inbegrip van de Windows Server-knoop punten.

```console
$ kubectl get nodes -o wide
NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Registreer het interne IP-adres van het Windows Server-knoop punt dat u wilt oplossen. U gebruikt dit adres in een latere stap.

## <a name="connect-to-the-virtual-machine-and-node"></a>Verbinding maken met de virtuele machine en het knoop punt

Maak verbinding met het open bare IP-adres van de virtuele machine die u eerder hebt gemaakt met behulp van een RDP-client, zoals [Microsoft extern bureaublad][rdp-mac].

![Afbeelding van het maken van verbinding met de virtuele machine met behulp van een RDP-client](media/rdp/vm-rdp.png)

Nadat u verbinding hebt gemaakt met uw virtuele machine, maakt u verbinding met het *interne IP-adres* van het Windows Server-knoop punt dat u wilt gebruiken om problemen op te lossen met een RDP-client vanuit de virtuele machine.

![Afbeelding van het maken van verbinding met het Windows Server-knoop punt met behulp van een RDP-client](media/rdp/node-rdp.png)

U bent nu verbonden met uw Windows Server-knoop punt.

![Afbeelding van CMD-venster in het Windows Server-knoop punt](media/rdp/node-session.png)

U kunt nu alle probleemoplossings opdrachten uitvoeren in het *cmd* -venster. Omdat Windows Server-knoop punten Windows Server Core gebruiken, is er geen volledige GUI of andere GUI-hulpprogram ma's wanneer u verbinding maakt met een Windows Server-knoop punt via RDP.

## <a name="remove-rdp-access"></a>RDP-toegang verwijderen

Als u klaar bent, sluit u de RDP-verbinding met het Windows Server-knoop punt en sluit u de RDP-sessie naar de virtuele machine. Nadat u beide RDP-sessies hebt afgesloten, verwijdert u de virtuele machine met de opdracht [AZ VM delete][az-vm-delete] :

```azurecli-interactive
az vm delete --resource-group myResourceGroup --name myVM
```

En de NSG-regel:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NSG_NAME=$(az network nsg list -g $CLUSTER_RG --query [].name -o tsv)
```

```azurecli-interactive
az network nsg rule delete --resource-group $CLUSTER_RG --nsg-name $NSG_NAME --name tempRDPAccess
```

## <a name="next-steps"></a>Volgende stappen

Als u extra gegevens over het oplossen van problemen nodig hebt, kunt u [de logboeken van de hoofd knooppunten van het Kubernetes][view-master-logs] of [Azure monitor][azure-monitor-containers]weer geven.

<!-- EXTERNAL LINKS -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[rdp-mac]: https://aka.ms/rdmac

<!-- INTERNAL LINKS -->
[aks-windows-cli]: windows-container-cli.md
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-vm-delete]: /cli/azure/vm#az-vm-delete
[azure-monitor-containers]: ../azure-monitor/insights/container-insights-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ssh-steps]: ssh.md
[view-master-logs]: view-master-logs.md

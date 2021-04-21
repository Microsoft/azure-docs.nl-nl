---
title: RDP naar AKS Windows Server-knooppunten
titleSuffix: Azure Kubernetes Service
description: Meer informatie over het maken van een RDP-verbinding Azure Kubernetes Service Windows Server-knooppunten (AKS) voor probleemoplossings- en onderhoudstaken.
services: container-service
ms.topic: article
ms.date: 06/04/2019
ms.openlocfilehash: 62f29c0550b858e34d888da61f1bd7fbd358f82d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782923"
---
# <a name="connect-with-rdp-to-azure-kubernetes-service-aks-cluster-windows-server-nodes-for-maintenance-or-troubleshooting"></a>Verbinding maken met RDP om Windows Server Azure Kubernetes Service(AKS)-clusterknooppunten te koppelen voor onderhoud of probleemoplossing

Gedurende de levenscyclus van uw AKS Azure Kubernetes Service cluster moet u mogelijk toegang hebben tot een AKS Windows Server-knooppunt. Deze toegang kan zijn voor onderhoud, logboekverzameling of andere bewerkingen voor probleemoplossing. U hebt toegang tot de Windows Server-knooppunten van AKS met behulp van RDP. Als u SSH wilt gebruiken voor toegang tot de AKS Windows Server-knooppunten en u toegang hebt tot dezelfde sleutel die is gebruikt tijdens het maken van het cluster, kunt u ook de stappen in SSH volgen in [Azure Kubernetes Service -clusterknooppunten (AKS).][ssh-steps] Uit veiligheidsdoeleinden worden de AKS-knooppunten niet blootgesteld aan internet.

In dit artikel wordt beschreven hoe u een RDP-verbinding maakt met een AKS-knooppunt met behulp van hun privé-IP-adressen.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgenomen dat u een bestaand AKS-cluster met een Windows Server-knooppunt hebt. Als u een AKS-cluster nodig hebt, zie dan het artikel over het maken van een AKS-cluster met een [Windows-container met behulp van de Azure CLI.][aks-windows-cli] U hebt de gebruikersnaam en het wachtwoord van de Windows-beheerder nodig voor het Windows Server-knooppunt dat u wilt oplossen. Als u ze niet kent, kunt u ze opnieuw instellen door Reset Extern bureaublad-services of het beheerderswachtwoord in een [Windows-VM te volgen. ](/troubleshoot/azure/virtual-machines/reset-rdp) U hebt ook een RDP-client nodig, [zoals Microsoft Extern bureaublad.][rdp-mac]

Ook moet Azure CLI versie 2.0.61 of hoger zijn geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][install-azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="deploy-a-virtual-machine-to-the-same-subnet-as-your-cluster"></a>Een virtuele machine implementeren in hetzelfde subnet als uw cluster

De Windows Server-knooppunten van uw AKS-cluster hebben geen extern toegankelijke IP-adressen. Als u een RDP-verbinding wilt maken, kunt u een virtuele machine met een openbaar toegankelijk IP-adres implementeren in hetzelfde subnet als uw Windows Server-knooppunten.

In het volgende voorbeeld wordt een virtuele machine met de *naam myVM* gemaakt in de resourcegroep *myResourceGroup.*

Haal eerst het subnet op dat wordt gebruikt door uw Windows Server-knooppuntgroep. U hebt de naam van het subnet nodig om de subnet-id op te halen. Als u de naam van het subnet wilt weten, hebt u de naam van het vnet nodig. Haal de naam van het vnet op door in uw cluster een query uit te voeren voor de lijst met netwerken. Als u een query wilt uitvoeren op het cluster, hebt u de naam nodig. U kunt deze allemaal krijgen door het volgende uit te Azure Cloud Shell:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
VNET_NAME=$(az network vnet list -g $CLUSTER_RG --query [0].name -o tsv)
SUBNET_NAME=$(az network vnet subnet list -g $CLUSTER_RG --vnet-name $VNET_NAME --query [0].name -o tsv)
SUBNET_ID=$(az network vnet subnet show -g $CLUSTER_RG --vnet-name $VNET_NAME --name $SUBNET_NAME --query id -o tsv)
```

Nu u de virtuele SUBNET_ID hebt, kunt u de volgende opdracht in hetzelfde venster Azure Cloud Shell om de VM te maken:

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

In de volgende voorbeelduitvoer ziet u dat de virtuele machine is gemaakt en dat het openbare IP-adres van de virtuele machine wordt weergegeven.

```console
13.62.204.18
```

Neem het openbare IP-adres van de virtuele machine op. U gebruikt dit adres in een latere stap.

## <a name="allow-access-to-the-virtual-machine"></a>Toegang tot de virtuele machine toestaan

Subnetten van AKS-knooppuntgroepen worden standaard beveiligd met NSG's (netwerkbeveiligingsgroepen). Als u toegang wilt krijgen tot de virtuele machine, moet u toegang inschakelen in de NSG.

> [!NOTE]
> De NSG's worden beheerd door de AKS-service. Elke wijziging die u aan de NSG aangeert, wordt op elk moment overschreven door het besturingsvlak.
>

Haal eerst de resourcegroep en nsg-naam van de nsg op om de regel toe te voegen aan:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NSG_NAME=$(az network nsg list -g $CLUSTER_RG --query [].name -o tsv)
```

Maak vervolgens de NSG-regel:

```azurecli-interactive
az network nsg rule create --name tempRDPAccess --resource-group $CLUSTER_RG --nsg-name $NSG_NAME --priority 100 --destination-port-range 3389 --protocol Tcp --description "Temporary RDP access to Windows nodes"
```

## <a name="get-the-node-address"></a>Het knooppuntadres op halen

Als u een Kubernetes-cluster wilt beheren, gebruikt u [kubectl][kubectl], de Kubernetes-opdrachtregelclient. Als u Azure Cloud Shell gebruikt, is `kubectl` al geïnstalleerd. Als u `kubectl` lokaal wilt installeren, gebruikt u de opdracht [az aks install-cli][az-aks-install-cli]:
    
```azurecli-interactive
az aks install-cli
```

Gebruik de opdracht [az aks get-credentials][az-aks-get-credentials] om `kubectl` zodanig te configureren dat er verbinding wordt gemaakt met het Kubernetes-cluster. Bij deze opdracht worden referenties gedownload en wordt Kubernetes CLI geconfigureerd voor het gebruik van deze referenties.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Vermeld het interne IP-adres van de Windows Server-knooppunten met behulp van [de opdracht kubectl get:][kubectl-get]

```console
kubectl get nodes -o wide
```

In de volgende voorbeelduitvoer ziet u de interne IP-adressen van alle knooppunten in het cluster, inclusief de Windows Server-knooppunten.

```console
$ kubectl get nodes -o wide
NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Neem het interne IP-adres op van het Windows Server-knooppunt dat u wilt oplossen. U gebruikt dit adres in een latere stap.

## <a name="connect-to-the-virtual-machine-and-node"></a>Verbinding maken met de virtuele machine en het knooppunt

Maak verbinding met het openbare IP-adres van de virtuele machine die u eerder hebt gemaakt met behulp van een RDP-client zoals [Microsoft Extern bureaublad][rdp-mac].

![Afbeelding van het maken van verbinding met de virtuele machine met behulp van een RDP-client](media/rdp/vm-rdp.png)

Nadat u verbinding hebt gemaakt met uw virtuele machine, maakt u verbinding met het interne *IP-adres* van het Windows Server-knooppunt dat u wilt oplossen met behulp van een RDP-client vanuit uw virtuele machine.

![Afbeelding van het maken van verbinding met het Windows Server-knooppunt met behulp van een RDP-client](media/rdp/node-rdp.png)

U bent nu verbonden met uw Windows Server-knooppunt.

![Afbeelding van het cmd-venster in het Windows Server-knooppunt](media/rdp/node-session.png)

U kunt nu opdrachten voor probleemoplossing uitvoeren in het *cmd-venster.* Omdat Windows Server-knooppunten Gebruikmaken van Windows Server Core, is er geen volledige GUI of andere GUI-hulpprogramma's wanneer u via RDP verbinding maakt met een Windows Server-knooppunt.

## <a name="remove-rdp-access"></a>RDP-toegang verwijderen

Wanneer u klaar bent, sluit u de RDP-verbinding met het Windows Server-knooppunt af en sluit u vervolgens de RDP-sessie af naar de virtuele machine. Nadat u beide RDP-sessies hebt afgesloten, verwijdert u de virtuele machine met de [opdracht az vm delete:][az-vm-delete]

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

Als u aanvullende probleemoplossingsgegevens nodig hebt, kunt u de logboeken van het [Kubernetes-hoofd-knooppunt][view-master-logs] bekijken of [Azure Monitor.][azure-monitor-containers]

<!-- EXTERNAL LINKS -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[rdp-mac]: https://aka.ms/rdmac

<!-- INTERNAL LINKS -->
[aks-windows-cli]: windows-container-cli.md
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-vm-delete]: /cli/azure/vm#az_vm_delete
[azure-monitor-containers]: ../azure-monitor/containers/container-insights-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ssh-steps]: ssh.md
[view-master-logs]: view-master-logs.md
---
title: SSH in AKS-clusterknooppunten (Azure Kubernetes Service)
description: Meer informatie over het maken van een SSH-verbinding met Azure Kubernetes service-cluster knooppunten (AKS) voor het oplossen van problemen en onderhouds taken.
services: container-service
ms.topic: article
ms.date: 07/31/2019
ms.openlocfilehash: c044b552cd0c28a7073364c48b9572045a290331
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98662868"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>Via SSH verbinding maken met AKS-clusterknooppunten (Azure Kubernetes Service) voor onderhoud of probleemoplossing

Tijdens de levens cyclus van uw Azure Kubernetes service (AKS)-cluster hebt u mogelijk toegang tot een AKS-knoop punt. Deze toegang kan voor onderhoud, logboek verzameling of andere bewerkingen voor het oplossen van problemen zijn. U kunt AKS-knoop punten benaderen met SSH, met inbegrip van Windows Server-knoop punten. U kunt ook [verbinding maken met Windows Server-knoop punten met behulp van RDP-verbindingen (Remote Desktop Protocol)][aks-windows-rdp]. Uit veiligheids overwegingen zijn de AKS-knoop punten niet beschikbaar op internet. Als u de AKS-knoop punten wilt SSHen, gebruikt u het privé-IP-adres.

In dit artikel wordt beschreven hoe u een SSH-verbinding met een AKS-knoop punt maakt met behulp van hun privé-IP-adressen.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u beschikt over een bestaand AKS-cluster. Als u een AKS-cluster nodig hebt, raadpleegt u de AKS Quick Start [met behulp van de Azure cli][aks-quickstart-cli] of [met behulp van de Azure Portal][aks-quickstart-portal].

Standaard worden SSH-sleutels verkregen of gegenereerd en vervolgens toegevoegd aan de knoop punten wanneer u een AKS-cluster maakt. In dit artikel wordt beschreven hoe u verschillende SSH-sleutels opgeeft dan de SSH-sleutels die worden gebruikt bij het maken van uw AKS-cluster. In dit artikel leest u ook hoe u het privé-IP-adres van uw knoop punt kunt bepalen en hoe u er verbinding mee maakt met behulp van SSH. Als u geen andere SSH-sleutel hoeft op te geven, kunt u de stap voor het toevoegen van de open bare SSH-sleutel voor het knoop punt overs Laan.

In dit artikel wordt ook ervan uitgegaan dat u een SSH-sleutel hebt. U kunt een SSH-sleutel maken met [macOS of Linux][ssh-nix] of [Windows][ssh-windows]. Als u PuTTy gen gebruikt om het sleutel paar te maken, slaat u het sleutel paar op in een OpenSSH in plaats van de standaard persoonlijke sleutel indeling PuTTy (. ppk-bestand).

Ook moet de Azure CLI-versie 2.0.64 of hoger zijn geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][install-azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="configure-virtual-machine-scale-set-based-aks-clusters-for-ssh-access"></a>AKS-clusters van virtuele-machine schaal sets configureren voor SSH-toegang

Als u de schaalset voor virtuele machines wilt configureren op basis van SSH-toegang, zoekt u de naam van de virtuele-machineset van uw cluster en voegt u uw open bare SSH-sleutel toe aan deze schaalset.

Gebruik de opdracht [AZ AKS show][az-aks-show] om de naam van de resource groep van uw AKS-cluster op te halen en vervolgens de opdracht [AZ vmss List][az-vmss-list] om de naam van uw schaalset op te halen.

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
SCALE_SET_NAME=$(az vmss list --resource-group $CLUSTER_RESOURCE_GROUP --query '[0].name' -o tsv)
```

In het bovenstaande voor beeld wordt de naam van de cluster resource groep voor de *myAKSCluster* in *myResourceGroup* toegewezen aan *CLUSTER_RESOURCE_GROUP*. In het voor beeld wordt *CLUSTER_RESOURCE_GROUP* gebruikt om de naam van de schaalset weer te geven en toe te wijzen aan *SCALE_SET_NAME*.

> [!IMPORTANT]
> Op dit moment moet u de SSH-sleutels voor uw AKS-clusters op basis van virtuele machines alleen bijwerken met behulp van de Azure CLI.
> 
> Voor Linux-knoop punten kunnen SSH-sleutels momenteel alleen worden toegevoegd met behulp van de Azure CLI. Als u via SSH verbinding wilt maken met een Windows Server-knoop punt, gebruikt u de SSH-sleutels die zijn opgegeven tijdens het maken van het AKS-cluster en slaat u de volgende reeks opdrachten op voor het toevoegen van uw open bare SSH-sleutel. U hebt nog steeds het IP-adres nodig van het knoop punt dat u wilt oplossen. dit wordt weer gegeven in de laatste opdracht van deze sectie. U kunt ook [verbinding maken met Windows Server-knoop punten met RDP-verbindingen (Remote Desktop Protocol)][aks-windows-rdp] in plaats van SSH te gebruiken.

Als u uw SSH-sleutels wilt toevoegen aan de knoop punten in een schaalset voor virtuele machines, gebruikt u de opdracht [AZ vmss extension set][az-vmss-extension-set] en [AZ vmss update-instances][az-vmss-update-instances] .

```azurecli-interactive
az vmss extension set  \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --vmss-name $SCALE_SET_NAME \
    --name VMAccessForLinux \
    --publisher Microsoft.OSTCExtensions \
    --version 1.4 \
    --protected-settings "{\"username\":\"azureuser\", \"ssh_key\":\"$(cat ~/.ssh/id_rsa.pub)\"}"

az vmss update-instances --instance-ids '*' \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --name $SCALE_SET_NAME
```

In het bovenstaande voor beeld worden de variabelen *CLUSTER_RESOURCE_GROUP* en *SCALE_SET_NAME* van de vorige opdrachten gebruikt. In het bovenstaande voor beeld wordt ook *~/.ssh/id_rsa. pub* gebruikt als locatie voor uw open bare SSH-sleutel.

> [!NOTE]
> De gebruikers naam voor de AKS-knoop punten is standaard *azureuser*.

Nadat u uw open bare SSH-sleutel aan de schaalset hebt toegevoegd, kunt u deze met behulp van het IP-adres van een virtuele machine in een knoop punt in die schaal instellen. Bekijk de privé-IP-adressen van de AKS-cluster knooppunten met behulp van de [kubectl Get-opdracht][kubectl-get].

```console
kubectl get nodes -o wide
```

In de uitvoer van het volgende voor beeld ziet u de interne IP-adressen van alle knoop punten in het cluster, met inbegrip van een Windows Server-knoop punt.

```console
$ kubectl get nodes -o wide

NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Registreer het interne IP-adres van het knoop punt dat u wilt oplossen.

Als u toegang wilt krijgen tot uw knoop punt via SSH, volgt u de stappen in [de SSH-verbinding maken](#create-the-ssh-connection).

## <a name="configure-virtual-machine-availability-set-based-aks-clusters-for-ssh-access"></a>AKS-clusters die zijn gebaseerd op de beschik baarheid van virtuele machines configureren voor SSH-toegang

Als u het AKS-cluster op basis van Beschik baarheid van virtuele machines wilt configureren voor SSH-toegang, zoekt u de naam van het Linux-knoop punt van uw cluster en voegt u uw open bare SSH-sleutel toe aan het knoop punt.

Gebruik de opdracht [AZ AKS show][az-aks-show] om de naam van de resource groep van uw AKS-cluster op te halen en vervolgens de opdracht [AZ VM List][az-vm-list] om de naam van de virtuele machine van het Linux-knoop punt van uw cluster weer te geven.

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az vm list --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

In het bovenstaande voor beeld wordt de naam van de cluster resource groep voor de *myAKSCluster* in *myResourceGroup* toegewezen aan *CLUSTER_RESOURCE_GROUP*. In het voor beeld wordt *CLUSTER_RESOURCE_GROUP* gebruikt om de naam van de virtuele machine weer te geven. In de voorbeeld uitvoer ziet u de naam van de virtuele machine:

```
Name                      ResourceGroup                                  Location
------------------------  ---------------------------------------------  ----------
aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_eastus  eastus
```

Gebruik de opdracht [AZ VM user update][az-vm-user-update] om uw SSH-sleutels aan het knoop punt toe te voegen.

```azurecli-interactive
az vm user update \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --name aks-nodepool1-79590246-0 \
    --username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

In het bovenstaande voor beeld worden de *CLUSTER_RESOURCE_GROUP* variabele en de naam van de virtuele machine van het knoop punt uit de vorige opdrachten gebruikt. In het bovenstaande voor beeld wordt ook *~/.ssh/id_rsa. pub* gebruikt als locatie voor uw open bare SSH-sleutel. U kunt ook de inhoud van uw open bare SSH-sleutel gebruiken in plaats van een pad op te geven.

> [!NOTE]
> De gebruikers naam voor de AKS-knoop punten is standaard *azureuser*.

Nadat u uw open bare SSH-sleutel aan de virtuele machine van het knoop punt hebt toegevoegd, kunt u met behulp van het IP-adres een SSH-verbinding maken met de virtuele machine. Bekijk het privé IP-adres van een AKS-cluster knooppunt met behulp van de opdracht [AZ VM List-IP-addresses][az-vm-list-ip-addresses] .

```azurecli-interactive
az vm list-ip-addresses --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

In het bovenstaande voor beeld wordt gebruikgemaakt van de variabele *CLUSTER_RESOURCE_GROUP* die in de vorige opdrachten is ingesteld. In de volgende voorbeeld uitvoer ziet u de privé-IP-adressen van de AKS-knoop punten:

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

## <a name="create-the-ssh-connection"></a>De SSH-verbinding maken

Als u een SSH-verbinding met een AKS-knoop punt wilt maken, voert u een helper-pod uit in uw AKS-cluster. Deze helper pod biedt u SSH-toegang tot het cluster en vervolgens extra toegang tot het SSH-knoop punt. Voer de volgende stappen uit om deze helper pod te maken en te gebruiken:

1. Voer een `debian` container installatie kopie uit en koppel hieraan een terminal sessie. Deze container kan worden gebruikt om een SSH-sessie te maken met een wille keurig knoop punt in het AKS-cluster:

    ```console
    kubectl run -it --rm aks-ssh --image=debian
    ```

    > [!TIP]
    > Als u Windows Server-knoop punten gebruikt, voegt u een knooppunt kiezer toe aan de opdracht om de Debian-container op een Linux-knoop punt te plannen:
    >
    > ```console
    > kubectl run -it --rm aks-ssh --image=debian --overrides='{"apiVersion":"v1","spec":{"nodeSelector":{"beta.kubernetes.io/os":"linux"}}}'
    > ```

1. Zodra de terminal sessie is verbonden met de container, installeert u een SSH-client met behulp van `apt-get` :

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. Open een nieuw terminal venster, dat niet is verbonden met uw container, Kopieer uw persoonlijke SSH-sleutel naar het helper-pod. Deze persoonlijke sleutel wordt gebruikt voor het maken van de SSH in het AKS-knoop punt. 

   Wijzig indien nodig *~/.ssh/id_rsa* naar de locatie van uw persoonlijke SSH-sleutel:

    ```console
    kubectl cp ~/.ssh/id_rsa $(kubectl get pod -l run=aks-ssh -o jsonpath='{.items[0].metadata.name}'):/id_rsa
    ```

1. Ga terug naar de terminal sessie naar uw container, werk de machtigingen voor de gekopieerde `id_rsa` persoonlijke SSH-sleutel zo in dat deze alleen-lezen is:

    ```console
    chmod 0600 id_rsa
    ```

1. Maak een SSH-verbinding met uw AKS-knoop punt. De standaard gebruikersnaam voor AKS-knoop punten is *azureuser*. Accepteer de prompt om door te gaan met de verbinding als de SSH-sleutel voor het eerst wordt vertrouwd. U wordt vervolgens weer gegeven met de bash-prompt van uw AKS-knoop punt:

    ```console
    $ ssh -i id_rsa azureuser@10.240.0.4

    ECDSA key fingerprint is SHA256:A6rnRkfpG21TaZ8XmQCCgdi9G/MYIMc+gFAuY9RUY70.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added '10.240.0.4' (ECDSA) to the list of known hosts.

    Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 4.15.0-1018-azure x86_64)

     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage

      Get cloud support with Ubuntu Advantage Cloud Guest:
        https://www.ubuntu.com/business/services/cloud

    [...]

    azureuser@aks-nodepool1-79590246-0:~$
    ```

## <a name="remove-ssh-access"></a>SSH-toegang verwijderen

Wanneer u klaar bent, wordt `exit` de SSH-sessie en vervolgens `exit` de interactieve container sessie uitgevoerd. Wanneer deze container sessie wordt gesloten, wordt de pod die wordt gebruikt voor SSH-toegang uit het AKS-cluster verwijderd.

## <a name="next-steps"></a>Volgende stappen

Als u extra gegevens voor probleem oplossing nodig hebt, kunt u [de kubelet-logboeken weer geven][view-kubelet-logs] of [de logboeken van de Kubernetes-hoofd knooppunt weer geven][view-master-logs].

<!-- EXTERNAL LINKS -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-vm-list]: /cli/azure/vm#az-vm-list
[az-vm-user-update]: /cli/azure/vm/user#az-vm-user-update
[az-vm-list-ip-addresses]: /cli/azure/vm#az-vm-list-ip-addresses
[view-kubelet-logs]: kubelet-logs.md
[view-master-logs]: view-master-logs.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-windows-rdp]: rdp.md
[ssh-nix]: ../virtual-machines/linux/mac-create-ssh-keys.md
[ssh-windows]: ../virtual-machines/linux/ssh-from-windows.md
[az-vmss-list]: /cli/azure/vmss#az-vmss-list
[az-vmss-extension-set]: /cli/azure/vmss/extension#az-vmss-extension-set
[az-vmss-update-instances]: /cli/azure/vmss#az-vmss-update-instances

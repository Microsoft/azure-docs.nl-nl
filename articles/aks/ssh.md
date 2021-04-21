---
title: SSH in AKS-clusterknooppunten (Azure Kubernetes Service)
description: Meer informatie over het maken van een SSH-verbinding met Azure Kubernetes Service (AKS)-clusterknooppunten voor het oplossen van problemen en onderhoudstaken.
services: container-service
ms.topic: article
ms.date: 07/31/2019
ms.openlocfilehash: 8df3e8be14e258aac34881014057dd7ee7ec3239
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769531"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>Via SSH verbinding maken met AKS-clusterknooppunten (Azure Kubernetes Service) voor onderhoud of probleemoplossing

Gedurende de levenscyclus van uw AKS Azure Kubernetes Service cluster moet u mogelijk toegang hebben tot een AKS-knooppunt. Deze toegang kan zijn voor onderhoud, logboekverzameling of andere bewerkingen voor probleemoplossing. U hebt toegang tot AKS-knooppunten via SSH, inclusief Windows Server-knooppunten. U kunt ook [verbinding maken met Windows Server-knooppunten met behulp van RDP-verbindingen (Remote Desktop Protocol).][aks-windows-rdp] Uit veiligheidsdoeleinden worden de AKS-knooppunten niet blootgesteld aan internet. Als u via SSH naar de AKS-knooppunten wilt gaan, gebruikt u het privé-IP-adres.

In dit artikel wordt beschreven hoe u een SSH-verbinding maakt met een AKS-knooppunt met behulp van hun privé-IP-adressen.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgenomen dat u een bestaand AKS-cluster hebt. Als u een AKS-cluster nodig hebt, bekijkt u de AKS-quickstart met behulp van [de Azure CLI][aks-quickstart-cli] of met behulp van de [Azure Portal][aks-quickstart-portal].

Standaard worden SSH-sleutels verkregen of gegenereerd en vervolgens toegevoegd aan knooppunten wanneer u een AKS-cluster maakt. In dit artikel wordt beschreven hoe u andere SSH-sleutels opgeeft dan de SSH-sleutels die u hebt gebruikt bij het maken van uw AKS-cluster. In het artikel wordt ook beschreven hoe u het privé-IP-adres van uw knooppunt bepaalt en hoe u er verbinding mee maakt met behulp van SSH. Als u geen andere SSH-sleutel hoeft op te geven, kunt u de stap voor het toevoegen van de openbare SSH-sleutel aan het knooppunt overslaan.

In dit artikel wordt ook ervan uitgenomen dat u een SSH-sleutel hebt. U kunt een SSH-sleutel maken met [macOS of Linux][ssh-nix] of [Windows.][ssh-windows] Als u PuTTY Gen gebruikt om het sleutelpaar te maken, moet u het sleutelpaar opslaan in een OpenSSH-indeling in plaats van de standaardindeling van de persoonlijke PuTTy-sleutel (PPK-bestand).

Ook moet Azure CLI versie 2.0.64 of hoger zijn geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][install-azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="configure-virtual-machine-scale-set-based-aks-clusters-for-ssh-access"></a>AKS-clusters op basis van virtuele-machineschaalsets configureren voor SSH-toegang

Als u de virtuele-machineschaalset wilt configureren voor SSH-toegang, gaat u naar de naam van de virtuele-machineschaalset van uw cluster en voegt u de openbare SSH-sleutel toe aan die schaalset.

Gebruik de [opdracht az aks show][az-aks-show] om de naam van de resourcegroep van uw AKS-cluster op te halen en gebruik vervolgens de opdracht az [vmss list][az-vmss-list] om de naam van uw schaalset op te halen.

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
SCALE_SET_NAME=$(az vmss list --resource-group $CLUSTER_RESOURCE_GROUP --query '[0].name' -o tsv)
```

In het bovenstaande voorbeeld wordt de naam van de clusterresourcegroep voor *myAKSCluster* in *myResourceGroup* toegewezen aan *CLUSTER_RESOURCE_GROUP*. In het voorbeeld wordt vervolgens *CLUSTER_RESOURCE_GROUP* om de naam van de schaalset weer te geven en deze toe te wijzen *aan SCALE_SET_NAME*.

> [!IMPORTANT]
> Op dit moment moet u alleen uw SSH-sleutels bijwerken voor AKS-clusters op basis van virtuele-machineschaalsets met behulp van de Azure CLI.
> 
> Voor Linux-knooppunten kunnen SSH-sleutels momenteel alleen worden toegevoegd met behulp van de Azure CLI. Als u via SSH verbinding wilt maken met een Windows Server-knooppunt, gebruikt u de SSH-sleutels die u hebt opgegeven bij het maken van het AKS-cluster en slaat u de volgende set opdrachten voor het toevoegen van uw openbare SSH-sleutel over. U hebt nog steeds het IP-adres nodig van het knooppunt dat u wilt oplossen. Dit wordt weergegeven in de laatste opdracht van deze sectie. U kunt ook verbinding maken met [Windows Server-knooppunten via RDP-verbindingen (Remote Desktop Protocol)][aks-windows-rdp] in plaats van SSH.

Als u uw SSH-sleutels wilt toevoegen aan de knooppunten in een virtuele-machineschaalset, gebruikt u de opdrachten [az vmss extension set][az-vmss-extension-set] en az [vmss update-instances.][az-vmss-update-instances]

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

In het bovenstaande voorbeeld worden *de CLUSTER_RESOURCE_GROUP* en *SCALE_SET_NAME* uit de vorige opdrachten gebruikt. In het bovenstaande voorbeeld wordt *ook ~/.ssh/id_rsa.pub* gebruikt als de locatie voor uw openbare SSH-sleutel.

> [!NOTE]
> De gebruikersnaam voor de AKS-knooppunten is *standaard azureuser.*

Nadat u de openbare SSH-sleutel aan de schaalset hebt toevoegen, kunt u via het IP-adres van de SSH een virtuele knooppuntmachine in die schaalset maken. Bekijk de privé-IP-adressen van de AKS-clusterknooppunten met behulp van [de opdracht kubectl get.][kubectl-get]

```console
kubectl get nodes -o wide
```

In de volgende voorbeelduitvoer ziet u de interne IP-adressen van alle knooppunten in het cluster, met inbegrip van een Windows Server-knooppunt.

```console
$ kubectl get nodes -o wide

NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Neem het interne IP-adres op van het knooppunt dat u wilt oplossen.

Volg de stappen in De SSH-verbinding maken om toegang te krijgen tot uw knooppunt met behulp van [SSH.](#create-the-ssh-connection)

## <a name="configure-virtual-machine-availability-set-based-aks-clusters-for-ssh-access"></a>AKS-clusters op basis van beschikbaarheidssets voor virtuele machines configureren voor SSH-toegang

Als u het AKS-cluster op basis van de beschikbaarheidsset van uw virtuele machine wilt configureren voor SSH-toegang, gaat u naar de naam van het Linux-knooppunt van uw cluster en voegt u de openbare SSH-sleutel toe aan dat knooppunt.

Gebruik de [opdracht az aks show][az-aks-show] om de naam van de resourcegroep van uw AKS-cluster op te halen en gebruik vervolgens de opdracht az [vm list][az-vm-list] om de naam van de virtuele machine van het Linux-knooppunt van uw cluster weer te geven.

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az vm list --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

In het bovenstaande voorbeeld wordt de naam van de clusterresourcegroep voor *myAKSCluster* in *myResourceGroup* toegewezen aan *CLUSTER_RESOURCE_GROUP*. In het voorbeeld wordt *vervolgens* CLUSTER_RESOURCE_GROUP de naam van de virtuele machine weer te geven. In de voorbeelduitvoer ziet u de naam van de virtuele machine:

```
Name                      ResourceGroup                                  Location
------------------------  ---------------------------------------------  ----------
aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_eastus  eastus
```

Gebruik de opdracht [az vm user update][az-vm-user-update] om uw SSH-sleutels toe te voegen aan het knooppunt.

```azurecli-interactive
az vm user update \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --name aks-nodepool1-79590246-0 \
    --username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

In het bovenstaande voorbeeld worden *de CLUSTER_RESOURCE_GROUP* en de naam van de virtuele knooppuntmachine uit eerdere opdrachten gebruikt. In het bovenstaande voorbeeld wordt *ook ~/.ssh/id_rsa.pub* gebruikt als de locatie voor uw openbare SSH-sleutel. U kunt ook de inhoud van uw openbare SSH-sleutel gebruiken in plaats van een pad op te geven.

> [!NOTE]
> De gebruikersnaam voor de AKS-knooppunten is *standaard azureuser.*

Nadat u de openbare SSH-sleutel aan de virtuele machine van het knooppunt hebt toevoegen, kunt u via het IP-adres SSH in die virtuele machine maken. Bekijk het privé-IP-adres van een AKS-clusterknooppunt met de [opdracht az vm list-ip-addresses.][az-vm-list-ip-addresses]

```azurecli-interactive
az vm list-ip-addresses --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

In het bovenstaande voorbeeld wordt *CLUSTER_RESOURCE_GROUP* variabele in de vorige opdrachten gebruikt. In de volgende voorbeelduitvoer ziet u de privé-IP-adressen van de AKS-knooppunten:

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

## <a name="create-the-ssh-connection"></a>De SSH-verbinding maken

Als u een SSH-verbinding met een AKS-knooppunt wilt maken, moet u een helperpod uitvoeren in uw AKS-cluster. Deze helperpod biedt u SSH-toegang tot het cluster en vervolgens extra SSH-knooppunttoegang. Voltooi de volgende stappen om deze helperpod te maken en te gebruiken:

1. Voer een `debian` containerafbeelding uit en koppel er een terminalsessie aan. Deze container kan worden gebruikt om een SSH-sessie te maken met elk knooppunt in het AKS-cluster:

    ```console
    kubectl run -it --rm aks-ssh --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
    ```

    > [!TIP]
    > Als u Windows Server-knooppunten gebruikt, voegt u een knooppunt selector toe aan de opdracht om de Debian-container op een Linux-knooppunt te plannen:
    >
    > ```console
    > kubectl run -it --rm aks-ssh --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --overrides='{"apiVersion":"v1","spec":{"nodeSelector":{"beta.kubernetes.io/os":"linux"}}}'
    > ```

1. Zodra de terminalsessie is verbonden met de container, installeert u een SSH-client met behulp van `apt-get` :

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. Open een nieuw terminalvenster, niet verbonden met uw container, kopieer uw persoonlijke SSH-sleutel naar de helperpod. Deze persoonlijke sleutel wordt gebruikt om de SSH te maken in het AKS-knooppunt. 

   Wijzig indien nodig *~/.ssh/id_rsa* naar de locatie van uw persoonlijke SSH-sleutel:

    ```console
    kubectl cp ~/.ssh/id_rsa $(kubectl get pod -l run=aks-ssh -o jsonpath='{.items[0].metadata.name}'):/id_rsa
    ```

1. Ga terug naar de terminalsessie naar uw container en werk de machtigingen voor de gekopieerde persoonlijke SSH-sleutel bij zodat deze `id_rsa` alleen-lezen is voor de gebruiker:

    ```console
    chmod 0400 id_rsa
    ```

1. Maak een SSH-verbinding met uw AKS-knooppunt. Ook hier is azureuser de standaardnaam voor *AKS-knooppunten.* Accepteer de prompt om door te gaan met de verbinding omdat de SSH-sleutel voor het eerst wordt vertrouwd. U wordt vervolgens voorzien van de bash-prompt van uw AKS-knooppunt:

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

Wanneer u klaar bent, `exit` de SSH-sessie en vervolgens `exit` de interactieve containersessie. Wanneer deze containersessie wordt gesloten, wordt de pod verwijderd die wordt gebruikt voor SSH-toegang vanuit het AKS-cluster.

## <a name="next-steps"></a>Volgende stappen

Als u aanvullende gegevens voor probleemoplossing nodig hebt, kunt u [de kubelet-logboeken][view-kubelet-logs] bekijken of de logboeken van het [Kubernetes-hoofd-knooppunt bekijken.][view-master-logs]

<!-- EXTERNAL LINKS -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-vm-list]: /cli/azure/vm#az_vm_list
[az-vm-user-update]: /cli/azure/vm/user#az_vm_user_update
[az-vm-list-ip-addresses]: /cli/azure/vm#az_vm_list_ip_addresses
[view-kubelet-logs]: kubelet-logs.md
[view-master-logs]: ./view-control-plane-logs.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-windows-rdp]: rdp.md
[ssh-nix]: ../virtual-machines/linux/mac-create-ssh-keys.md
[ssh-windows]: ../virtual-machines/linux/ssh-from-windows.md
[az-vmss-list]: /cli/azure/vmss#az_vmss_list
[az-vmss-extension-set]: /cli/azure/vmss/extension#az_vmss_extension_set
[az-vmss-update-instances]: /cli/azure/vmss#az_vmss_update_instances
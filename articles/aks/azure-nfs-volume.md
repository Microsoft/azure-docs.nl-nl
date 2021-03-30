---
title: NFS-Ubuntu Linux server volume maken
titleSuffix: Azure Kubernetes Service
description: Meer informatie over het hand matig maken van een NFS-Ubuntu Linux server volume voor gebruik met een Peul in azure Kubernetes service (AKS)
services: container-service
author: ozboms
ms.topic: article
ms.date: 4/25/2019
ms.author: obboms
ms.openlocfilehash: 4e817d572a98ffb8135adf58d13f50ccacbc8746
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86251991"
---
# <a name="manually-create-and-use-an-nfs-network-file-system-linux-server-volume-with-azure-kubernetes-service-aks"></a>Hand matig een NFS-server volume (Network File System) maken en gebruiken met Azure Kubernetes service (AKS)
Het delen van gegevens tussen containers is vaak een benodigd onderdeel van op containers gebaseerde services en toepassingen. Normaal gesp roken hebt u verschillende peulen die toegang nodig hebben tot dezelfde informatie op een extern permanent volume.    
Azure files is een optie, waardoor het maken van een NFS-server op een virtuele Azure-machine een andere vorm van permanente gedeelde opslag is. 

In dit artikel wordt uitgelegd hoe u een NFS-server op een virtuele Ubuntu-machine maakt. En geef uw AKS-containers ook toegang tot dit gedeelde bestands systeem.

## <a name="before-you-begin"></a>Voordat u begint
In dit artikel wordt ervan uitgegaan dat u beschikt over een bestaand AKS-cluster. Als u een AKS-cluster nodig hebt, raadpleegt u de AKS Quick Start [met behulp van de Azure cli][aks-quickstart-cli] of [met behulp van de Azure Portal][aks-quickstart-portal].

Uw AKS-cluster moet zich in dezelfde of gekoppelde virtuele netwerken bevinden als de NFS-server. Het cluster moet worden gemaakt in een bestaand VNET. Dit kan hetzelfde VNET zijn als uw virtuele machine.

De stappen voor het configureren van een bestaand VNET worden beschreven in de documentatie: [AKS-cluster maken in bestaande vnet][aks-virtual-network] en [virtuele netwerken verbinden met VNET-peering][peer-virtual-networks]

Ook wordt ervan uitgegaan dat u een Ubuntu Linux virtuele machine hebt gemaakt (bijvoorbeeld 18,04 LTS). Instellingen en grootte kunnen naar eigen wens zijn en kunnen worden geïmplementeerd via Azure. Zie Linux-VM- [beheer][linux-create]voor meer informatie over Linux.

Als u eerst uw AKS-cluster implementeert, vult Azure automatisch het veld virtueel netwerk in wanneer u uw Ubuntu-machine implementeert, zodat deze binnen hetzelfde VNET wonen. Als u in plaats daarvan met peered netwerken wilt werken, raadpleegt u de bovenstaande documentatie.

## <a name="deploying-the-nfs-server-onto-a-virtual-machine"></a>De NFS-server op een virtuele machine implementeren
Dit is het script voor het instellen van een NFS-server binnen uw virtuele Ubuntu-machine:
```bash
#!/bin/bash

# This script should be executed on Linux Ubuntu Virtual Machine

EXPORT_DIRECTORY=${1:-/export/data}
DATA_DIRECTORY=${2:-/data}
AKS_SUBNET=${3:-*}

echo "Updating packages"
apt-get -y update

echo "Installing NFS kernel server"

apt-get -y install nfs-kernel-server

echo "Making data directory ${DATA_DIRECTORY}"
mkdir -p ${DATA_DIRECTORY}

echo "Making new directory to be exported and linked to data directory: ${EXPORT_DIRECTORY}"
mkdir -p ${EXPORT_DIRECTORY}

echo "Mount binding ${DATA_DIRECTORY} to ${EXPORT_DIRECTORY}"
mount --bind ${DATA_DIRECTORY} ${EXPORT_DIRECTORY}

echo "Giving 777 permissions to ${EXPORT_DIRECTORY} directory"
chmod 777 ${EXPORT_DIRECTORY}

parentdir="$(dirname "$EXPORT_DIRECTORY")"
echo "Giving 777 permissions to parent: ${parentdir} directory"
chmod 777 $parentdir

echo "Appending bound directories into fstab"
echo "${DATA_DIRECTORY}    ${EXPORT_DIRECTORY}   none    bind  0  0" >> /etc/fstab

echo "Appending localhost and Kubernetes subnet address ${AKS_SUBNET} to exports configuration file"
echo "/export        ${AKS_SUBNET}(rw,async,insecure,fsid=0,crossmnt,no_subtree_check)" >> /etc/exports
echo "/export        localhost(rw,async,insecure,fsid=0,crossmnt,no_subtree_check)" >> /etc/exports

nohup service nfs-kernel-server restart
```
De server wordt opnieuw opgestart (vanwege het script) en u kunt de NFS-server koppelen aan AKS.

>[!IMPORTANT]  
>Zorg ervoor dat u de **AKS_SUBNET** vervangt door het juiste in het cluster of door ' * ' te vervangen door de NFS-server naar alle poorten en verbindingen.

Nadat u uw virtuele machine hebt gemaakt, kopieert u het bovenstaande script naar een bestand. Vervolgens kunt u de computer verplaatsen van de lokale machine of van de locatie van het script naar de VM met behulp van: 
```console
scp /path/to/script_file username@vm-ip-address:/home/{username}
```
Zodra het script zich in uw virtuele machine bevindt, kunt u een SSH-verbinding naar de virtuele machine uitvoeren via de opdracht:
```console
sudo ./nfs-server-setup.sh
```
Als de uitvoering is mislukt vanwege een fout met de machtiging geweigerd, stelt u de machtiging voor uitvoering in via de opdracht:
```console
chmod +x ~/nfs-server-setup.sh
```

## <a name="connecting-aks-cluster-to-nfs-server"></a>AKS-cluster verbinden met NFS-server
We kunnen de NFS-server koppelen aan het cluster door een permanent volume en een permanente volume claim in te richten waarmee wordt aangegeven hoe het volume moet worden geopend.

Het koppelen van de twee services in dezelfde of gekoppelde virtuele netwerken is nood zakelijk. Instructies voor het instellen van het cluster in hetzelfde VNET vindt u in het volgende: [AKS-cluster maken in bestaand vnet][aks-virtual-network]

Zodra ze zich in hetzelfde virtuele netwerk (of een peered) bevinden, moet u een permanent volume en een permanente volume claim inrichten in uw AKS-cluster. De containers kunnen vervolgens het NFS-station koppelen aan de lokale map.

Hier volgt een voor beeld van een Kubernetes-definitie voor het permanente volume (deze definitie gaat ervan uit dat uw cluster en virtuele machine zich in hetzelfde VNET bevinden):

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: <NFS_NAME>
  labels:
    type: nfs
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteMany
  nfs:
    server: <NFS_INTERNAL_IP>
    path: <NFS_EXPORT_FILE_PATH>
```
Vervang **NFS_INTERNAL_IP**, **NFS_NAME** en **NFS_EXPORT_FILE_PATH** met NFS-server gegevens.

U hebt ook een permanente volume claim bestand nodig. Hier volgt een voor beeld van wat u kunt gebruiken:

>[!IMPORTANT]  
>**' storageClassName '** moet een lege teken reeks blijven of de claim werkt niet.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: <NFS_NAME>
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
  selector: 
    matchLabels:
      type: nfs
```

## <a name="troubleshooting"></a>Problemen oplossen
Als u geen verbinding kunt maken met de server vanuit een cluster, is het mogelijk dat een probleem de geëxporteerde map of het bovenliggende item heeft, niet voldoende machtigingen heeft om toegang te krijgen tot de server.

Controleer of de export Directory en de bovenliggende map 777 machtigingen hebben.

U kunt de machtigingen controleren door de onderstaande opdracht uit te voeren en de mappen moeten de machtigingen *' drwxrwxrwx '* hebben:
```console
ls -l
```

## <a name="more-information"></a>Meer informatie
Hier volgt een uitgebreide zelf studie voor een volledige uitleg of over het oplossen van fouten in de installatie van de NFS-server:
  - [Zelf studie voor NFS][nfs-tutorial]

## <a name="next-steps"></a>Volgende stappen

Zie [Aanbevolen procedures voor opslag en back-ups in AKS][operator-best-practices-storage]voor gekoppelde aanbevolen procedures.

<!-- LINKS - external -->
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[linux-create]: ../virtual-machines/linux/tutorial-manage-vm.md
[nfs-tutorial]: https://help.ubuntu.com/community/SettingUpNFSHowTo#Pre-Installation_Setup
[aks-virtual-network]: ./configure-kubenet.md#create-an-aks-cluster-in-the-virtual-network
[peer-virtual-networks]: ../virtual-network/tutorial-connect-virtual-networks-portal.md

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[operator-best-practices-storage]: operator-best-practices-storage.md

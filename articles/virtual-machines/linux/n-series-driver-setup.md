---
title: Setup van het GPU-stuur programma voor Azure N-Series voor Linux
description: NVIDIA GPU-Stuur Programma's instellen voor virtuele machines uit de N-serie waarop Linux wordt uitgevoerd in azure
services: virtual-machines-linux
author: vikancha-MSFT
ms.service: virtual-machines-linux
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 01/09/2019
ms.author: vikancha
ms.openlocfilehash: b80a09c82b1e932fb93b4c85ee250773aa7d3c38
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016121"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-linux"></a>NVIDIA GPU-Stuur Programma's installeren op Vm's met N-serie waarop Linux wordt uitgevoerd

Als u gebruik wilt maken van de GPU-mogelijkheden van virtuele machines van Azure N-serie die worden ondersteund door NVIDIA-Gpu's, moet u NVIDIA GPU-Stuur Programma's installeren. Met de [uitbrei ding NVIDIA GPU-stuur programma](../extensions/hpccompute-gpu-linux.md) worden de juiste NVIDIA-CUDA of raster Stuur Programma's geïnstalleerd op een virtuele machine uit de N-serie. De uitbrei ding installeren of beheren met de Azure Portal of hulpprogram ma's, zoals de Azure CLI-of Azure Resource Manager-sjablonen. Zie de [documentatie voor NVIDIA GPU-Stuur Programma's](../extensions/hpccompute-gpu-linux.md) voor ondersteunde distributies en implementaties tappen.

Als u ervoor kiest om de NVIDIA GPU-Stuur Programma's hand matig te installeren, worden in dit artikel ondersteunde distributies, stuur Programma's en installatie-en verificatie stappen beschreven. Informatie over hand matige installatie van Stuur Programma's is ook beschikbaar voor [Windows-vm's](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Zie [GPU Linux VM-grootten](../sizes-gpu.md?toc=/azure/virtual-machines/linux/toc.json)voor virtuele machines van de N-serie, opslag capaciteit en schijf Details. 

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

## <a name="install-cuda-drivers-on-n-series-vms"></a>CUDA-Stuur Programma's installeren op Vm's uit de N-serie

Hier volgen stappen voor het installeren van CUDA-Stuur Programma's van de NVIDIA CUDA Toolkit op Vm's uit de N-serie. 


C-en C++-ontwikkel aars kunnen eventueel de volledige Toolkit installeren om GPU-versnelde toepassingen te bouwen. Zie de [installatie handleiding voor CUDA](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html)voor meer informatie.

Als u CUDA-Stuur Programma's wilt installeren, maakt u een SSH-verbinding met elke VM. Voer de volgende opdracht uit om te controleren of het systeem een GPU heeft die compatibel is met CUDA:

```bash
lspci | grep -i NVIDIA
```
U ziet uitvoer die vergelijkbaar is met het volgende voor beeld (met een NVIDIA Tesla K80-kaart):

![uitvoer van lspci-opdracht](./media/n-series-driver-setup/lspci.png)

Voer vervolgens de installatie opdrachten uit die specifiek zijn voor uw distributie.

### <a name="ubuntu"></a>Ubuntu 

1. Down load en installeer de CUDA-Stuur Programma's van de NVIDIA-website. Bijvoorbeeld voor Ubuntu 16,04 LTS:
   ```bash
   CUDA_REPO_PKG=cuda-repo-ubuntu1604_10.0.130-1_amd64.deb

   wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

   sudo dpkg -i /tmp/${CUDA_REPO_PKG}

   sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub 

   rm -f /tmp/${CUDA_REPO_PKG}

   sudo apt-get update

   sudo apt-get install cuda-drivers

   ```

   De installatie kan enkele minuten duren.

2. Als u de volledige CUDA Toolkit optioneel wilt installeren, typt u:

   ```bash
   sudo apt-get install cuda
   ```

3. Start de virtuele machine opnieuw op en ga door met het controleren van de installatie.

#### <a name="cuda-driver-updates"></a>Updates voor CUDA-Stuur Programma's

U wordt aangeraden CUDA-Stuur Programma's na de implementatie regel matig bij te werken.

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers

sudo reboot
```

### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS of Red Hat Enterprise Linux

1. Update de kernel (aanbevolen). Als u ervoor kiest om de kernel niet bij te werken, zorgt u ervoor dat de versies van `kernel-devel` en `dkms` geschikt zijn voor uw kernel.

   ```
   sudo yum install kernel kernel-tools kernel-headers kernel-devel
  
   sudo reboot

2. Install the latest [Linux Integration Services for Hyper-V and Azure](https://www.microsoft.com/download/details.aspx?id=55106). Check if LIS is required by verifying the results of lspci. If all GPU devices are listed as expected, installing LIS is not required.

Skip this step if you plan to use CentOS 7.8(or higher) as LIS is no longer required for these versions.

   ```bash
   wget https://aka.ms/lis
 
   tar xvzf lis
 
   cd LISISO
 
   sudo ./install.sh
 
   sudo reboot
   ```
 
3. Maak opnieuw verbinding met de virtuele machine en ga door met de installatie met de volgende opdrachten:

   ```bash
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

   sudo yum install dkms

   CUDA_REPO_PKG=cuda-repo-rhel7-10.0.130-1.x86_64.rpm

   wget http://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

   sudo rpm -ivh /tmp/${CUDA_REPO_PKG}

   rm -f /tmp/${CUDA_REPO_PKG}

   sudo yum install cuda-drivers
   ```

   De installatie kan enkele minuten duren. 

4. Als u de volledige CUDA Toolkit optioneel wilt installeren, typt u:

   ```bash
   sudo yum install cuda
   ```

5. Start de virtuele machine opnieuw op en ga door met het controleren van de installatie.

### <a name="verify-driver-installation"></a>Installatie van stuur programma verifiëren

Als u de status van het GPU-apparaat wilt opvragen, SSH naar de virtuele machine en voer het [NVIDIA-SMI-](https://developer.nvidia.com/nvidia-system-management-interface) opdracht regel programma uit dat met het stuur programma is geïnstalleerd. 

Als het stuur programma is geïnstalleerd, ziet u uitvoer die vergelijkbaar is met de volgende. Met **GPU-util** wordt 0% weer gegeven, tenzij u momenteel een GPU-werk belasting op de VM uitvoert. De versie-en GPU-Details van uw stuur programma kunnen afwijken van de gegevens die worden weer gegeven.

![Status van NVIDIA-apparaten](./media/n-series-driver-setup/smi.png)

## <a name="rdma-network-connectivity"></a>RDMA-netwerk verbinding

RDMA-netwerk connectiviteit kan worden ingeschakeld voor virtuele machines van de N-serie die geschikt zijn voor RDMA, zoals NC24r geïmplementeerd in dezelfde beschikbaarheidsset of in één plaatsings groep in een VM-schaalset (virtuele machine). Het RDMA-netwerk ondersteunt MPI-verkeer (Message Passing Interface) voor toepassingen die worden uitgevoerd met Intel MPI 5. x of een latere versie. Aanvullende vereisten zijn als volgt:

### <a name="distributions"></a>Distributies

Implementeer met RDMA geschikte Vm's van de N-serie vanaf een van de installatie kopieën in de Azure Marketplace die ondersteuning bieden voor RDMA-connectiviteit op Vm's uit de N-serie:
  
* **Ubuntu 16,04 LTS** -RDMA-Stuur Programma's op de VM configureren en registreren bij Intel om Intel mpi te downloaden:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **CentOS op basis van 7,4 HPC** -RDMA-Stuur Programma's en Intel mpi 5,1 zijn geïnstalleerd op de VM.

* **CENTOS HPC** -CENTOS-HPC 7,6 en hoger (voor Sku's waar InfiniBand wordt ondersteund op basis van SR-IOV). Deze installatie kopieën hebben Mellanox OFED-en MPI-bibliotheken die vooraf zijn geïnstalleerd.

> [!NOTE]
> CX3-Pro kaarten worden alleen ondersteund via LTS-versies van de Mellanox OFED. Gebruik LTS Mellanox OFED-versie (4.9-0.1.7.0) op de virtuele machines uit de N-serie met ConnectX3-Pro kaarten. Zie [Linux-Stuur Programma's](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed)voor meer informatie.
>
> Daarnaast hebben enkele van de nieuwste HPC-installatie kopieën van Azure Marketplace OFED 5,1 en hoger, die geen ondersteuning bieden voor ConnectX3-Pro kaarten. Controleer de Mellanox OFED-versie in de HPC-installatie kopie voordat u deze gebruikt op Vm's met ConnectX3-Pro-kaarten.
>
> De volgende installatie kopieën zijn de meest recente CentOS-HPC-installatie kopieën die ondersteuning bieden voor ConnectX3-Pro kaarten:
>
> - Open Logic: CentOS-HPC: 7,6:7.6.2020062900
> - Open Logic: CentOS-HPC: 7_6gen2:7.6.2020062901
> - Open Logic: CentOS-HPC: 7,7:7.7.2020062600
> - Open Logic: CentOS-HPC: 7_7-Gen2:7.7.2020062601
> - Open Logic: CentOS-HPC: 8_1:8.1.2020062400
> - Open Logic: CentOS-HPC: 8_1-Gen2:8.1.2020062401
>

## <a name="install-grid-drivers-on-nv-or-nvv3-series-vms"></a>GRID-Stuur Programma's installeren op NV-of NVv3-serie Vm's

Als u NVIDIA-raster Stuur Programma's wilt installeren op de virtuele machines van NV of NVv3, maakt u een SSH-verbinding met elke virtuele machine en volgt u de stappen voor uw Linux-distributie. 

### <a name="ubuntu"></a>Ubuntu 

1. Voer de opdracht `lspci` uit. Controleer of de NVIDIA M60-kaart of-kaarten als PCI-apparaten worden weer gegeven.

2. Updates installeren.

   ```bash
   sudo apt-get update

   sudo apt-get upgrade -y

   sudo apt-get dist-upgrade -y

   sudo apt-get install build-essential ubuntu-desktop -y
   
   sudo apt-get install linux-azure -y
   ```
3. Schakel het Nouveau-kernelstuurprogramma uit, dat niet compatibel is met het NVIDIA-stuur programma. (Gebruik alleen het NVIDIA-stuur programma op NV-of NVv2-Vm's.) Als u dit wilt doen, maakt u een bestand `/etc/modprobe.d` `nouveau.conf` met de naam met de volgende inhoud:

   ```
   blacklist nouveau

   blacklist lbm-nouveau
   ```


4. Start de VM opnieuw op en maak opnieuw verbinding. X-server afsluiten:

   ```bash
   sudo systemctl stop lightdm.service
   ```

5. Down load en installeer het raster stuur programma:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  

   chmod +x NVIDIA-Linux-x86_64-grid.run

   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 

6. Selecteer **Ja** als u wordt gevraagd of u het hulp programma voor nvidia-xconfig wilt uitvoeren om uw X-configuratie bestand bij te werken.

7. Nadat de installatie is voltooid, kopieert u/etc/NVIDIA/gridd.conf.template naar een nieuw bestand in raster. conf op locatie/etc/NVIDIA/

   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```

8. Voeg het volgende toe in `/etc/nvidia/gridd.conf`:
 
   ```
   IgnoreSP=FALSE
   EnableUI=FALSE
   ```
   
9. Verwijder het volgende uit `/etc/nvidia/gridd.conf` als het aanwezig is:
 
   ```
   FeatureType=0
   ```
10. Start de virtuele machine opnieuw op en ga door met het controleren van de installatie.


### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS of Red Hat Enterprise Linux 

1. Update de kernel-en DKMS (aanbevolen). Als u ervoor kiest om de kernel niet bij te werken, zorgt u ervoor dat de versies van `kernel-devel` en `dkms` geschikt zijn voor uw kernel.
 
   ```bash  
   sudo yum update
 
   sudo yum install kernel-devel
 
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
 
   sudo yum install dkms
   
   sudo yum install hyperv-daemons
   ```

2. Schakel het Nouveau-kernelstuurprogramma uit, dat niet compatibel is met het NVIDIA-stuur programma. (Gebruik alleen het NVIDIA-stuur programma op NV-of NV3-Vm's.) Als u dit wilt doen, maakt u een bestand `/etc/modprobe.d` `nouveau.conf` met de naam met de volgende inhoud:

   ```
   blacklist nouveau

   blacklist lbm-nouveau
   ```
 
3. Start de VM opnieuw op, maak opnieuw verbinding en installeer de meest recente [Linux-integratie Services voor Hyper-V en Azure](https://www.microsoft.com/download/details.aspx?id=55106). Controleer of LIS is vereist door de resultaten van lspci te controleren. Als alle GPU-apparaten worden weer gegeven zoals verwacht, is het installeren van LIS niet vereist. 

Deze stap overs laan is dat u CentOS/RHEL 7,8 en hoger gebruikt.
 
   ```bash
   wget https://aka.ms/lis

   tar xvzf lis

   cd LISISO

   sudo ./install.sh

   sudo reboot

   ```
 
4. Maak opnieuw verbinding met de virtuele machine en voer de `lspci` opdracht uit. Controleer of de NVIDIA M60-kaart of-kaarten als PCI-apparaten worden weer gegeven.
 
5. Down load en installeer het raster stuur programma:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  

   chmod +x NVIDIA-Linux-x86_64-grid.run

   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 
6. Selecteer **Ja** als u wordt gevraagd of u het hulp programma voor nvidia-xconfig wilt uitvoeren om uw X-configuratie bestand bij te werken.

7. Nadat de installatie is voltooid, kopieert u/etc/NVIDIA/gridd.conf.template naar een nieuw bestand in raster. conf op locatie/etc/NVIDIA/
  
   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```
  
8. Voeg het volgende toe in `/etc/nvidia/gridd.conf`:
 
   ```
   IgnoreSP=FALSE
   EnableUI=FALSE 
   ```
9. Verwijder het volgende uit `/etc/nvidia/gridd.conf` als het aanwezig is:
 
   ```
   FeatureType=0
   ```
10. Start de virtuele machine opnieuw op en ga door met het controleren van de installatie.


### <a name="verify-driver-installation"></a>Installatie van stuur programma verifiëren


Als u de status van het GPU-apparaat wilt opvragen, SSH naar de virtuele machine en voer het [NVIDIA-SMI-](https://developer.nvidia.com/nvidia-system-management-interface) opdracht regel programma uit dat met het stuur programma is geïnstalleerd. 

Als het stuur programma is geïnstalleerd, ziet u uitvoer die vergelijkbaar is met de volgende. Met **GPU-util** wordt 0% weer gegeven, tenzij u momenteel een GPU-werk belasting op de VM uitvoert. De versie-en GPU-Details van uw stuur programma kunnen afwijken van de gegevens die worden weer gegeven.

![Scherm opname waarin de uitvoer wordt weer gegeven wanneer de status van het GPU-apparaat wordt opgevraagd.](./media/n-series-driver-setup/smi-nv.png)
 

### <a name="x11-server"></a>X11-server
Als u een X11-server nodig hebt voor externe verbindingen met een NV of NVv2 VM, wordt [x11vnc](http://www.karlrunge.com/x11vnc/) aanbevolen omdat hiermee hardwareversnelling van afbeeldingen mogelijk is. De BusID van het M60-apparaat moet hand matig worden toegevoegd aan het X11-configuratie bestand (meestal, `etc/X11/xorg.conf` ). Voeg een `"Device"` sectie toe die er ongeveer als volgt uitziet:
 
```
Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "Tesla M60"
    BusID          "PCI:0@your-BusID:0:0"
EndSection
```
 
Werk uw sectie ook `"Screen"` bij om dit apparaat te gebruiken.
 
De decimale BusID kan worden gevonden door uit te voeren

```bash
nvidia-xconfig --query-gpu-info | awk '/PCI BusID/{print $4}'
```
 
De BusID kan worden gewijzigd wanneer een virtuele machine opnieuw wordt toegewezen of opnieuw wordt opgestart. Daarom wilt u mogelijk een script maken om de BusID in de X11-configuratie bij te werken wanneer een virtuele machine opnieuw wordt opgestart. U kunt bijvoorbeeld een script `busidupdate.sh` met de naam (of een andere naam die u hebt gekozen) maken met de inhoud vergelijkbaar met de volgende:

```bash 
#!/bin/bash
XCONFIG="/etc/X11/xorg.conf"
OLDBUSID=`awk '/BusID/{gsub(/"/, "", $2); print $2}' ${XCONFIG}`
NEWBUSID=`nvidia-xconfig --query-gpu-info | awk '/PCI BusID/{print $4}'`

if [[ "${OLDBUSID}" == "${NEWBUSID}" ]] ; then
        echo "NVIDIA BUSID not changed - nothing to do"
else
        echo "NVIDIA BUSID changed from \"${OLDBUSID}\" to \"${NEWBUSID}\": Updating ${XCONFIG}" 
        sed -e 's|BusID.*|BusID          '\"${NEWBUSID}\"'|' -i ${XCONFIG}
fi
```

Maak vervolgens een vermelding voor uw update script in `/etc/rc.d/rc3.d` zodat het script als basis bij het opstarten wordt aangeroepen.

## <a name="troubleshooting"></a>Problemen oplossen

* U kunt de modus voor persistentie instellen met behulp `nvidia-smi` van zodat de uitvoer van de opdracht sneller is als u behoefte hebt aan een query uit te voeren op kaarten. Voer uit om de modus voor persistentie in te stellen `nvidia-smi -pm 1` . Houd er rekening mee dat als de virtuele machine opnieuw wordt opgestart, de instelling van de modus verdwijnt. U kunt de modus instelling altijd scripteren om uit te voeren bij het opstarten.
* Als u de NVIDIA CUDA-Stuur Programma's hebt bijgewerkt naar de nieuwste versie en RDMA-connectiviteit niet meer werkt, [installeert u de RDMA-Stuur Programma's opnieuw](#rdma-network-connectivity) om die verbinding opnieuw tot stand te brengen. 
* Als een bepaalde versie van het besturings systeem CentOS/RHEL (of kernel) niet wordt ondersteund voor LIS, treedt er een fout op met de niet-ondersteunde kernel-versie. Meld deze fout samen met de versie van het besturings systeem en de kernel.

## <a name="next-steps"></a>Volgende stappen

* Zie [een virtuele Linux-machine generaliseren en vastleggen](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)voor het vastleggen van een Linux VM-installatie kopie met de geïnstalleerde NVIDIA-Stuur Programma's.

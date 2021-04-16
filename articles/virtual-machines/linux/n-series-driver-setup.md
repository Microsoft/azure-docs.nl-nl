---
title: Installatie van GPU-stuurprogramma's uit de Azure N-serie voor Linux
description: NVIDIA GPU-stuurprogramma's instellen voor VM's uit de N-serie waarop Linux wordt uitgevoerd in Azure
services: virtual-machines
author: vikancha-MSFT
ms.service: virtual-machines
ms.subervice: vm-sizes-gpu
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2019
ms.author: vikancha
ms.openlocfilehash: dd9461e30138ee1a59a93db45aa5f739bfe88f94
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565301"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-linux"></a>NVIDIA GPU-stuurprogramma's installeren op VM's uit de N-serie met Linux

Als u wilt profiteren van de GPU-mogelijkheden van VM's uit de Azure N-serie die worden gebacked door NVIDIA GPU's, moet u NVIDIA GPU-stuurprogramma's installeren. De [NVIDIA GPU-stuurprogramma-extensie](../extensions/hpccompute-gpu-linux.md) installeert de juiste NVIDIA CUDA- of GRID-stuurprogramma's op een VM uit de N-serie. Installeer of beheer de extensie met behulp van de Azure Portal of hulpprogramma's zoals de Azure CLI of Azure Resource Manager sjablonen. Zie de [documentatie voor de extensie voor NVIDIA GPU-stuurprogramma's](../extensions/hpccompute-gpu-linux.md) voor ondersteunde distributies en implementatiestappen.

Als u ervoor kiest om NVIDIA GPU-stuurprogramma's handmatig te installeren, bevat dit artikel ondersteunde distributies, stuurprogramma's en installatie- en verificatiestappen. Informatie over het handmatig instellen van stuurprogramma's is ook beschikbaar [voor Windows-VM's.](../windows/n-series-driver-setup.md)

Zie GPU [Linux VM-grootten](../sizes-gpu.md?toc=/azure/virtual-machines/linux/toc.json)voor specificaties, opslagcapaciteiten en schijfgegevens uit de N-serie. 

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

## <a name="install-cuda-drivers-on-n-series-vms"></a>CUDA-stuurprogramma's installeren op VM's uit de N-serie

Hier volgen stappen voor het installeren van CUDA-stuurprogramma's van de NVIDIA CUDA Toolkit op VM's uit de N-serie. 

C- en C++-ontwikkelaars kunnen desgewenst de volledige Toolkit installeren om GPU-versnelde toepassingen te bouwen. Zie de [CUDA Installation Guide (CUDA-installatiehandleiding) voor meer informatie.](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html)

Als u CUDA-stuurprogramma's wilt installeren, maakt u een SSH-verbinding met elke VM. Voer de volgende opdracht uit om te controleren of het systeem een GPU heeft die geschikt is voor CUDA:

```bash
lspci | grep -i NVIDIA
```
U ziet uitvoer die vergelijkbaar is met het volgende voorbeeld (met een NVIDIA Tesla K80-kaart):

![Uitvoer van lspci-opdracht](./media/n-series-driver-setup/lspci.png)

lspci geeft een lijst van de PCIe-apparaten op de VM, met inbegrip van de InfiniBand NIC en GPU's, indien van mij. Als lspci niet wordt terug gegeven, moet u mogelijk LIS installeren op CentOS/RHEL (onderstaande instructies).
Voer vervolgens installatieopdrachten uit die specifiek zijn voor uw distributie.

### <a name="ubuntu"></a>Ubuntu 

1. Download en installeer de CUDA-stuurprogramma's van de NVIDIA-website. 
    > [!NOTE]
   >  In het onderstaande voorbeeld ziet u het CUDA-pakketpad voor Ubuntu 16.04. Vervang het pad dat specifiek is voor de versie die u wilt gebruiken. 
   >  
   >  Ga naar het [Nvidia Downloadcentrum] ( https://developer.download.nvidia.com/compute/cuda/repos/) voor het volledige pad dat specifiek is voor elke versie. 
   > 
   ```bash
   CUDA_REPO_PKG=cuda-repo-ubuntu1604_10.0.130-1_amd64.deb
   wget -O /tmp/${CUDA_REPO_PKG} https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

   sudo dpkg -i /tmp/${CUDA_REPO_PKG}
   sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub 
   rm -f /tmp/${CUDA_REPO_PKG}

   sudo apt-get update
   sudo apt-get install cuda-drivers
   ```

   De installatie kan enkele minuten duren.
 

2. Als u de volledige CUDA-toolkit wilt installeren, typt u:

   ```bash
   sudo apt-get install cuda
   ```

3. Start de VM opnieuw op en ga door met het controleren van de installatie.

#### <a name="cuda-driver-updates"></a>Updates voor CUDA-stuurprogramma's

U wordt aangeraden CUDA-stuurprogramma's periodiek bij te werken na de implementatie.

```bash
sudo apt-get update
sudo apt-get upgrade -y
sudo apt-get dist-upgrade -y
sudo apt-get install cuda-drivers

sudo reboot
```

### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS of Red Hat Enterprise Linux

1. Werk de kernel bij (aanbevolen). Als u ervoor kiest om de kernel niet bij te werken, controleert u of de versies van `kernel-devel` en geschikt zijn voor uw `dkms` kernel.

   ```
   sudo yum install kernel kernel-tools kernel-headers kernel-devel
   sudo reboot
   ```

2. Installeer de nieuwste [Linux Integration Services voor Hyper-V en Azure.](https://www.microsoft.com/download/details.aspx?id=55106) Controleer of LIS is vereist door de resultaten van lspci te controleren. Als alle GPU-apparaten worden vermeld zoals verwacht (en hierboven beschreven), is het installeren van LIS niet vereist.

   Houd er rekening mee dat LIS van toepassing is op Red Hat Enterprise Linux, CentOS en de Oracle Linux Red Hat Compatible Kernel 5.2-5.11, 6.0-6.10 en 7.0-7.7. Raadpleeg de [Linux Integration Services-documentatie] ( https://www.microsoft.com/en-us/download/details.aspx?id=55106) voor meer informatie. 
   Sla deze stap over als u centOS/RHEL 7.8 (of hogere versies) wilt gebruiken, omdat LIS niet langer vereist is voor deze versies.

      ```bash
      wget https://aka.ms/lis
      tar xvzf lis
      cd LISISO

      sudo ./install.sh
      sudo reboot
      ```

3. Verbinding maken met de VM en doorgaan met de installatie met de volgende opdrachten:

   ```bash
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
   sudo yum install dkms
   
   CUDA_REPO_PKG=cuda-repo-rhel7-10.0.130-1.x86_64.rpm
   wget https://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

   sudo rpm -ivh /tmp/${CUDA_REPO_PKG}
   rm -f /tmp/${CUDA_REPO_PKG}

   sudo yum install cuda-drivers
   ```

   De installatie kan enkele minuten duren. 
   
    > [!NOTE]
   >  Ga [naar de Fedora-](https://dl.fedoraproject.org/pub/epel/) [en Nvidia CUDA-repo](https://developer.download.nvidia.com/compute/cuda/repos/) om het juiste pakket te kiezen voor de CentOS- of RHEL-versie die u wilt gebruiken.
   >  

CentOS 8 en RHEL 8 hebben bijvoorbeeld de volgende stappen nodig.

   ```bash
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
   sudo yum install dkms
   
   CUDA_REPO_PKG=cuda-repo-rhel8-10.2.89-1.x86_64.rpm
   wget https://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

   sudo rpm -ivh /tmp/${CUDA_REPO_PKG}
   rm -f /tmp/${CUDA_REPO_PKG}

   sudo yum install cuda-drivers
   ```

4. Als u wilt installeren optioneel de volledige CUDA toolkit, typt u:

   ```bash
   sudo yum install cuda
   ```
   > [!NOTE]
   >  Als u een foutbericht ziet met betrekking tot ontbrekende pakketten, zoals vulkan-filesystem, moet u mogelijk /etc/yum.repos.d/rh-cloud bewerken, optioneel-rpms zoeken en instellen op 1
   >  

5. Start de VM opnieuw op en ga door met het controleren van de installatie.

### <a name="verify-driver-installation"></a>Installatie van stuurprogramma controleren

Als u de status van het GPU-apparaat wilt opvragen, gaat u via SSH naar de VM en wordt het [opdrachtregelprogramma nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) uitgevoerd dat samen met het stuurprogramma is geïnstalleerd. 

Als het stuurprogramma is geïnstalleerd, ziet u uitvoer die er ongeveer als volgt uit ziet. Houd er rekening **mee dat GPU-Util** 0% laat zien, tenzij u momenteel een GPU-workload op de VM hebt. De stuurprogrammaversie en GPU-gegevens kunnen verschillen van de weergegeven stuurprogrammagegevens.

![NVIDIA-apparaatstatus](./media/n-series-driver-setup/smi.png)

## <a name="rdma-network-connectivity"></a>RDMA-netwerkverbinding

RDMA-netwerkconnectiviteit kan worden ingeschakeld op VM's uit de N-serie die geschikt zijn voor RDMA, zoals NC24r, die zijn geïmplementeerd in dezelfde beschikbaarheidsset of in één plaatsingsgroep in een virtuele-machineschaalset (VM). Het RDMA-netwerk ondersteunt Message Passing Interface (MPI) voor toepassingen die worden uitgevoerd met Intel MPI 5.x of een latere versie. Aanvullende vereisten volgen:

### <a name="distributions"></a>Distributies

Implementeer VM's uit de N-serie die geschikt zijn voor RDMA vanuit een van de afbeeldingen in de Azure Marketplace die ondersteuning biedt voor RDMA-connectiviteit op VM's uit de N-serie:
  
* **Ubuntu 16.04 LTS** : configureer RDMA-stuurprogramma's op de VM en registreer u bij Intel om Intel MPI te downloaden:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **Op CentOS gebaseerde 7.4 HPC** - RDMA-stuurprogramma's en Intel MPI 5.1 zijn geïnstalleerd op de VM.

* **HpC op basis van CentOS:** CentOS-HPC 7.6 en hoger (voor SKU's waarbij InfiniBand wordt ondersteund via SR-IOV). Op deze afbeeldingen zijn Mellanox OFED- en MPI-bibliotheken vooraf geïnstalleerd.

> [!NOTE]
> CX3-Pro-kaarten worden alleen ondersteund via LTS-versies van Mellanox OFED. Gebruik LTS Mellanox OFED versie (4.9-0.1.7.0) op de VM's uit de N-serie met ConnectX3-Pro kaarten. Zie Linux-stuurprogramma's [voor meer informatie.](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed)
>
> Bovendien hebben sommige van de meest recente Azure Marketplace HPC-afbeeldingen Mellanox OFED 5.1 en hoger, die geen ondersteuning bieden voor ConnectX3-Pro kaarten. Controleer de Mellanox OFED-versie in de HPC-afbeelding voordat u deze gebruikt op VM's met ConnectX3-Pro kaarten.
>
> De volgende afbeeldingen zijn de meest recente CentOS-HPC-afbeeldingen die ondersteuning bieden voor ConnectX3-Pro kaarten:
>
> - OpenLogic:CentOS-HPC:7.6:7.6.2020062900
> - OpenLogic:CentOS-HPC:7_6gen2:7.6.2020062901
> - OpenLogic:CentOS-HPC:7.7:7.7.2020062600
> - OpenLogic:CentOS-HPC:7_7-gen2:7.7.2020062601
> - OpenLogic:CentOS-HPC:8_1:8.1.2020062400
> - OpenLogic:CentOS-HPC:8_1-gen2:8.1.2020062401
>

## <a name="install-grid-drivers-on-nv-or-nvv3-series-vms"></a>GRID-stuurprogramma's installeren op VM's uit de NV- of NVv3-serie

Als u NVIDIA GRID-stuurprogramma's wilt installeren op VM's uit de NV- of NVv3-serie, maakt u een SSH-verbinding met elke VM en volgt u de stappen voor uw Linux-distributie. 

### <a name="ubuntu"></a>Ubuntu 

1. Voer de opdracht `lspci` uit. Controleer of de NVIDIA M60-kaart of -kaarten zichtbaar zijn als PCI-apparaten.

2. Updates installeren.

   ```bash
   sudo apt-get update
   sudo apt-get upgrade -y
   sudo apt-get dist-upgrade -y
   sudo apt-get install build-essential ubuntu-desktop -y
   sudo apt-get install linux-azure -y
   ```
3. Schakel het kernel-stuurprogramma Nouveau uit, dat niet compatibel is met het NVIDIA-stuurprogramma. (Gebruik alleen het NVIDIA-stuurprogramma op NV- of NVv2-VM's.) Maak hiervoor een bestand met de naam in `/etc/modprobe.d` `nouveau.conf` met de volgende inhoud:

   ```
   blacklist nouveau
   blacklist lbm-nouveau
   ```


4. Start de VM opnieuw op en verbinding maken. X-server afsluiten:

   ```bash
   sudo systemctl stop lightdm.service
   ```

5. Download en installeer het GRID-stuurprogramma:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  
   chmod +x NVIDIA-Linux-x86_64-grid.run
   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 

6. Wanneer u wordt gevraagd of u het hulpprogramma nvidia-xconfig wilt uitvoeren om uw X-configuratiebestand bij te werken, selecteert u **Ja.**

7. Nadat de installatie is voltooid, kopieert u /etc/nvidia/gridd.conf.template naar een nieuw bestand gridd.conf op locatie /etc/nvidia/

   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```

8. Voeg het volgende toe in `/etc/nvidia/gridd.conf`:
 
   ```
   IgnoreSP=FALSE
   EnableUI=FALSE
   ```
   
9. Verwijder het volgende uit `/etc/nvidia/gridd.conf` als deze aanwezig is:
 
   ```
   FeatureType=0
   ```
10. Start de VM opnieuw op en ga door met het controleren van de installatie.


### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS of Red Hat Enterprise Linux 

1. Werk de kernel en DKMS bij (aanbevolen). Als u ervoor kiest om de kernel niet bij te werken, moet u ervoor zorgen dat de versies van `kernel-devel` en geschikt zijn voor uw `dkms` kernel.
 
   ```bash  
   sudo yum update
   sudo yum install kernel-devel
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
   sudo yum install dkms
   sudo yum install hyperv-daemons
   ```

2. Schakel het stuurprogramma voor de Nouveau-kernel uit, dat niet compatibel is met het NVIDIA-stuurprogramma. (Gebruik alleen het NVIDIA-stuurprogramma op NV- of NV3-VM's.) Maak hiervoor een bestand met de naam in `/etc/modprobe.d` `nouveau.conf` met de volgende inhoud:

   ```
   blacklist nouveau
   blacklist lbm-nouveau
   ```

3. Start de VM opnieuw op, start opnieuw verbinding en installeer de nieuwste [Linux Integration Services voor Hyper-V en Azure.](https://www.microsoft.com/download/details.aspx?id=55106) Controleer of LIS is vereist door de resultaten van lspci te controleren. Als alle GPU-apparaten worden vermeld zoals verwacht (en hierboven beschreven), is het installeren van LIS niet vereist. 

   Sla deze stap over als u van plan bent CentOS/RHEL 7.8 (of hogere versies) te gebruiken, omdat LIS niet langer vereist is voor deze versies.

      ```bash
      wget https://aka.ms/lis
      tar xvzf lis
      cd LISISO

      sudo ./install.sh
      sudo reboot

      ```
 
4. Verbinding maken met de VM en de opdracht `lspci` uitvoeren. Controleer of de NVIDIA M60-kaart of -kaarten zichtbaar zijn als PCI-apparaten.
 
5. Download en installeer het GRID-stuurprogramma:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  
   chmod +x NVIDIA-Linux-x86_64-grid.run

   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 
6. Wanneer u wordt gevraagd of u het hulpprogramma nvidia-xconfig wilt uitvoeren om uw X-configuratiebestand bij te werken, selecteert u **Ja.**

7. Nadat de installatie is voltooid, kopieert u /etc/nvidia/gridd.conf.template naar een nieuw bestand gridd.conf op locatie /etc/nvidia/
  
   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```
  
8. Voeg het volgende toe in `/etc/nvidia/gridd.conf`:
 
   ```
   IgnoreSP=FALSE
   EnableUI=FALSE 
   ```
9. Verwijder het volgende uit `/etc/nvidia/gridd.conf` als deze aanwezig is:
 
   ```
   FeatureType=0
   ```
10. Start de VM opnieuw op en ga door met het controleren van de installatie.


### <a name="verify-driver-installation"></a>Installatie van stuurprogramma controleren


Als u de status van het GPU-apparaat wilt opvragen, gaat u met SSH naar de VM en voer u het [opdrachtregelprogramma nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) uit dat is geïnstalleerd met het stuurprogramma. 

Als het stuurprogramma is geïnstalleerd, ziet u uitvoer die er ongeveer als volgt uit ziet. Houd er rekening **mee dat GPU-Util** 0% toont, tenzij u momenteel een GPU-workload op de VM gebruikt. De stuurprogrammaversie en GPU-gegevens kunnen verschillen van de weergegeven versies.

![Schermopname van de uitvoer wanneer de status van het GPU-apparaat wordt opgevraagd.](./media/n-series-driver-setup/smi-nv.png)
 

### <a name="x11-server"></a>X11-server
Als u een X11-server nodig hebt voor externe verbindingen met een NV- of NVv2-VM, wordt [x11vnc](http://www.karlrunge.com/x11vnc/) aanbevolen omdat hardwareversnelling van grafische afbeeldingen is mogelijk. De BusID van het M60-apparaat moet handmatig worden toegevoegd aan het X11-configuratiebestand (meestal `etc/X11/xorg.conf` ). Voeg een `"Device"` sectie toe die lijkt op het volgende:
 
```
Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "Tesla M60"
    BusID          "PCI:0@your-BusID:0:0"
EndSection
```
 
Werk daarnaast uw sectie bij `"Screen"` om dit apparaat te gebruiken.
 
De decimale BusID kan worden gevonden door uit te

```bash
nvidia-xconfig --query-gpu-info | awk '/PCI BusID/{print $4}'
```
 
De BusID kan worden gewijzigd wanneer een VM opnieuw wordt toegewezen of opnieuw wordt opgestart. Daarom kunt u een script maken om de BusID in de X11-configuratie bij te werken wanneer een VM opnieuw wordt opgestart. Maak bijvoorbeeld een script met de naam (of een andere naam `busidupdate.sh` die u kiest) met inhoud die er ongeveer als volgt uit ziet:

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

Maak vervolgens een vermelding voor uw updatescript in zodat het script wordt aangeroepen `/etc/rc.d/rc3.d` als root bij het opstarten.

## <a name="troubleshooting"></a>Problemen oplossen

* U kunt de persistentiemodus instellen met , zodat de uitvoer van de opdracht `nvidia-smi` sneller is wanneer u query's op kaarten moet uitvoeren. Voer uit om de persistentiemodus in te `nvidia-smi -pm 1` stellen. Houd er rekening mee dat als de VM opnieuw wordt opgestart, de modus-instelling wordt verwijderd. U kunt altijd een script maken voor de modusinstelling die moet worden uitgevoerd bij het opstarten.
* Als u de NVIDIA CUDA-stuurprogramma's hebt bijgewerkt naar de nieuwste versie en u hebt gevonden dat de RDMA-connectiviteit niet meer werkt, installeert u de [RDMA-stuurprogramma's](#rdma-network-connectivity) opnieuw om die verbinding te herstellen. 
* Als tijdens de installatie van LIS een bepaalde CentOS/RHEL OS-versie (of kernel) niet wordt ondersteund voor LIS, wordt de fout 'Niet-ondersteunde kernelversie' weergegeven. Meld deze fout samen met de versies van het besturingssysteem en de kernel.

## <a name="next-steps"></a>Volgende stappen

* Zie How to generalize and capture a Linux virtual machine (Een virtuele [Linux-machine generaliseren](capture-image.md)en vastleggen) als u een linux-VM-installatier wilt vastleggen met uw geïnstalleerde NVIDIA-stuurprogramma's.

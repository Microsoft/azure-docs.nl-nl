---
title: VM-netwerk doorvoer optimaliseren | Microsoft Docs
description: Optimaliseer de netwerk doorvoer voor Microsoft Azure virtuele machines met Windows en Linux, met inbegrip van belang rijke distributies zoals Ubuntu, CentOS en Red Hat.
services: virtual-network
documentationcenter: na
author: steveesp
manager: Gerald DeGrace
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/06/2020
ms.author: steveesp
ms.openlocfilehash: bb9235f4d1190bf7f71ddc007f09c9666c353234
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98216798"
---
# <a name="optimize-network-throughput-for-azure-virtual-machines"></a>Netwerk doorvoer optimaliseren voor virtuele Azure-machines

Azure virtual machines (VM) hebben standaard netwerk instellingen die verder kunnen worden geoptimaliseerd voor netwerk doorvoer. In dit artikel wordt beschreven hoe u de netwerk doorvoer optimaliseert voor Microsoft Azure virtuele machines met Windows en Linux, met inbegrip van belang rijke distributies zoals Ubuntu, CentOS en Red Hat.

## <a name="windows-vm"></a>Windows-VM

Als uw Windows-VM [versneld netwerken](create-vm-accelerated-networking-powershell.md)ondersteunt, is het inschakelen van deze functie de optimale configuratie voor door voer. Voor alle andere Windows-Vm's kan het gebruik van schalen aan de ontvangst zijde (RSS) een hogere maximale door Voer bereiken dan een virtuele machine zonder RSS. RSS is mogelijk standaard uitgeschakeld in een Windows-VM. Voer de volgende stappen uit om te bepalen of RSS is ingeschakeld en schakel deze in als deze momenteel is uitgeschakeld:

1. Controleer of RSS is ingeschakeld voor een netwerk adapter met de `Get-NetAdapterRss` Power shell-opdracht. In het volgende voor beeld van de uitvoer die wordt geretourneerd door `Get-NetAdapterRss` , is RSS niet ingeschakeld.

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                 : False
    ```
2. Als u RSS wilt inschakelen, voert u de volgende opdracht in:

    ```powershell
    Get-NetAdapter | % {Enable-NetAdapterRss -Name $_.Name}
    ```
    De vorige opdracht heeft geen uitvoer. De opdracht heeft de NIC-instellingen gewijzigd, waardoor het tijdelijke connectiviteits verlies ongeveer één minuut wordt veroorzaakt. Een dialoog venster voor opnieuw verbinden wordt weer gegeven tijdens het verbindings verlies. De verbinding wordt doorgaans hersteld na de derde poging.
3. Controleer of RSS is ingeschakeld in de virtuele machine door de `Get-NetAdapterRss` opdracht opnieuw in te voeren. Als dit lukt, wordt de volgende voorbeeld uitvoer geretourneerd:

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                  : True
    ```

## <a name="linux-vm"></a>Linux-VM

RSS is standaard altijd ingeschakeld in een Azure Linux-VM. Linux-kernels die sinds oktober 2017 zijn uitgebracht, bevatten nieuwe netwerk optimalisatie opties die een Linux-VM in staat stellen om een hogere netwerk doorvoer te krijgen.

### <a name="ubuntu-for-new-deployments"></a>Ubuntu voor nieuwe implementaties

De Ubuntu Azure-kernel is het meest geoptimaliseerd voor netwerk prestaties op Azure. Als u de meest recente optimalisaties wilt downloaden, moet u eerst de nieuwste ondersteunde versie van 18,04-LTS als volgt installeren:

```json
"Publisher": "Canonical",
"Offer": "UbuntuServer",
"Sku": "18.04-LTS",
"Version": "latest"
```

Nadat het maken is voltooid, voert u de volgende opdrachten in om de meest recente updates op te halen. Deze stappen werken ook voor Vm's die momenteel de Azure-kernel Ubuntu uitvoeren.

```bash
#run as root or preface with sudo
apt-get -y update
apt-get -y upgrade
apt-get -y dist-upgrade
```

De volgende optionele opdracht set kan handig zijn voor bestaande Ubuntu-implementaties die al de Azure-kernel hebben, maar die geen verdere updates met fouten hebben kunnen uitvoeren.

```bash
#optional steps may be helpful in existing deployments with the Azure kernel
#run as root or preface with sudo
apt-get -f install
apt-get --fix-missing install
apt-get clean
apt-get -y update
apt-get -y upgrade
apt-get -y dist-upgrade
```

#### <a name="ubuntu-azure-kernel-upgrade-for-existing-vms"></a>Upgrade van Azure kernel Ubuntu voor bestaande Vm's

Belang rijke doorvoer prestaties kunnen worden bereikt door te upgraden naar de Azure Linux-kernel. Controleer of u deze kernel hebt door de versie van uw kernel te controleren. Deze moet gelijk zijn aan of hoger zijn dan het voor beeld.

```bash
#Azure kernel name ends with "-azure"
uname -r

#sample output on Azure kernel:
#4.13.0-1007-azure
```

Als uw virtuele machine niet de Azure-kernel heeft, begint het versie nummer meestal met ' 4,4 '. Als de virtuele machine niet de Azure-kernel heeft, voert u de volgende opdrachten uit als basis:

```bash
#run as root or preface with sudo
apt-get update
apt-get upgrade -y
apt-get dist-upgrade -y
apt-get install "linux-azure"
reboot
```

### <a name="centos"></a>CentOS

Als u de nieuwste optimalisaties wilt ophalen, kunt u het beste een virtuele machine maken met de nieuwste ondersteunde versie door de volgende para meters op te geven:

```json
"Publisher": "OpenLogic",
"Offer": "CentOS",
"Sku": "7.7",
"Version": "latest"
```

Nieuwe en bestaande Vm's kunnen profiteren van de installatie van de meest recente LIS (Linux Integration Services). De doorvoer optimalisatie bevindt zich in de LIS, beginnend bij 4.2.2-2, Hoewel latere versies nog meer verbeteringen bevatten. Voer de volgende opdrachten in voor het installeren van de meest recente LIS:

```bash
sudo yum update
sudo reboot
sudo yum install microsoft-hyper-v
```

### <a name="red-hat"></a>Red Hat

Als u de optimalisaties wilt ophalen, kunt u het beste een virtuele machine maken met de nieuwste ondersteunde versie door de volgende para meters op te geven:

```json
"Publisher": "RedHat"
"Offer": "RHEL"
"Sku": "7-RAW"
"Version": "latest"
```

Nieuwe en bestaande Vm's kunnen profiteren van de installatie van de meest recente LIS (Linux Integration Services). De doorvoer optimalisatie bevindt zich in LIS, vanaf 4,2. Voer de volgende opdrachten in om LIS te downloaden en te installeren:

```bash
wget https://aka.ms/lis
tar xvf lis
cd LISISO
sudo ./install.sh #or upgrade.sh if prior LIS was previously installed
```

Meer informatie over Linux Integration Services versie 4,2 voor Hyper-V door de [Download pagina](https://www.microsoft.com/download/details.aspx?id=55106)weer te geven.

## <a name="next-steps"></a>Volgende stappen
* Vm's op elkaar implementeren voor lage latentie met proximity- [plaatsings groep](../virtual-machines/co-location.md)
* Bekijk het geoptimaliseerde resultaat met [band breedte/door Voer Azure VM](virtual-network-bandwidth-testing.md) voor uw scenario.
* Meer informatie over hoe [band breedte wordt toegewezen aan virtuele machines](virtual-machine-network-throughput.md)
* Meer informatie met behulp van veelgestelde [vragen over Azure Virtual Network](virtual-networks-faq.md)
---
title: NP-serie-Azure Virtual Machines
description: Specificaties voor de virtuele machines in de NP-serie.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 02/09/2021
ms.author: vikancha
ms.openlocfilehash: aa67a858d0396badc25a625b23dc2f2fdf1bdff9
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551370"
---
# <a name="np-series"></a>NP-serie 
De virtuele machines van de NP-serie worden aangedreven door [Xilinx U250 ](https://www.xilinx.com/products/boards-and-kits/alveo/u250.html) fpga's voor het versnellen van werk belastingen, zoals machine learning interferentie, video transcode ring en zoek & analyses in data bases. Virtuele machines uit de NP-serie worden ook aangedreven door Cpu's van de Intel Xeon 8171M (Skylake) met alle core Turbo clock-snelheid van 3,2 GHz.

[Premium Storage](premium-storage-performance.md): ondersteund<br>
[Premium Storage caching](premium-storage-performance.md): ondersteund<br>
[Livemigratie](maintenance-and-updates.md): niet ondersteund<br>
[Updates](maintenance-and-updates.md)voor het behouden van geheugen: niet ondersteund<br>
Ondersteuning voor het genereren van VM'S: generatie 1<br>
[Versneld netwerken](../virtual-network/create-vm-accelerated-networking-cli.md): ondersteund<br>
[Tijdelijke besturingssysteem schijven](ephemeral-os-disks.md): niet ondersteund <br>
<br>

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | FPGA | FPGA-geheugen: GiB | Max. aantal gegevensschijven | Maximum aantal Nic's/verwachte netwerk bandbreedte (MBps) | 
|---|---|---|---|---|---|---|---|
| Standard_NP10s | 10 | 168 | 736  | 1 | 64  | 8 | 1 / 7500 | 
| Standard_NP20s | 20 | 336 | 1474 | 2 | 128 | 16 | 2 / 15000 | 
| Standard_NP40s | 40 | 672 | 2948 | 4 | 256 | 32 | 4 / 30000 | 



[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]


##  <a name="frequently-asked-questions"></a>Veelgestelde vragen

**V:** Welke versie van Vitis moet ik gebruiken? 

**A:** Xilinx raadt [Vitis 2020,2](https://www.xilinx.com/products/design-tools/vitis/vitis-platform.html)


**V:** Moet ik NP-Vm's gebruiken om mijn oplossing te ontwikkelen? 

**A:** Nee, u kunt on-premises ontwikkelen en implementeren in de Cloud. Zorg ervoor dat u de Attestation-documentatie volgt om te implementeren op NP Vm's. 

**V:** Welke versie van XRT moet ik gebruiken?

**A:** xrt_202020.2.8.832 

**V:** Wat is het doel platform voor implementatie?

**A:** Gebruik de volgende platforms.
- Xilinx-u250-gen3x16-xdma-platform-2.1-3_all
- Xilinx-u250-gen3x16-xdma-validate_2.1-3005608.1 

**V:** Welk platform moet ik richten voor ontwikkeling?

**A:** Xilinx-u250-gen3x16-xdma-2.1-202010-1-dev_1-2954688_all 

**V:** Wat zijn het ondersteunde besturings systeem (besturings systemen)? 

**A:** Xilinx en micro soft hebben Ubuntu 18,04 LTS en CentOS 7,8 gevalideerd.

 Xilinx heeft de volgende Marketplace-installatie kopieën gemaakt om de implementatie van deze virtuele machines te vereenvoudigen. 

[Xilinx Alveo U250 Deployment VM – Ubuntu 18.04](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/GalleryItemDetailsBladeNopdl/id/xilinx.xilinx_alveo_u250_deployment_vm_ubuntu1804_032321)

[Xilinx Alveo U250 Deployment VM – CentOS 7,8](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/GalleryItemDetailsBladeNopdl/id/xilinx.xilinx_alveo_u250_deployment_vm_centos78_032321)

**V:** Kan ik mijn eigen Ubuntu/CentOS-Vm's implementeren en XRT/Deployment-doel platform installeren? 

**A:** Klikt.

**V:** Als ik mijn eigen Ubuntu 18.04-VM Implementeer, wat zijn dan de vereiste pakketten en stappen?

**A:** Kernel 4.1 X per [Xilinx XRT-documentatie](https://www.xilinx.com/support/documentation/sw_manuals/xilinx2020_2/ug1451-xrt-release-notes.pdf) gebruiken
       
Installeer de volgende pakketten.
- xrt_202020 xrt_202020.2.8.832_18.04-amd64-XRT. deb
       
- xrt_202020 xrt_202020.2.8.832_18.04-amd64-Azure. deb
       
- Xilinx-u250-gen3x16-xdma-platform-2.1-3_all_18.04. deb. tar. gz
       
- Xilinx-u250-gen3x16-xdma-validate_2.1-3005608.1_all. deb  

**V:** Na het opnieuw opstarten van mijn VM in Ubuntu is het niet mogelijk om mijn FPGA (s) te vinden: 

**A:** Controleer of de kernel niet is bijgewerkt (uname-a). Als dat het geval is, moet u een downgrade uitvoeren naar kernel 4.1 X. 

**V:** Als ik mijn eigen CentOS 7,8 VM Implementeer, wat zijn dan de vereiste pakketten en stappen?

**A:** Kernel-versie gebruiken: 3.10.0-1160.15.2.el7.x86_64

 Installeer de volgende pakketten.
   
 - xrt_202020 xrt_202020.2.8.832_7.4.1708-x86_64-XRT. rpm 
      
 - xrt_202020 xrt_202020.2.8.832_7.4.1708-x86_64-Azure. rpm 
     
 - Xilinx-u250-gen3x16-xdma-platform-2.1 -3. = Arch. rpm. tar. gz 
      
 - Xilinx-u250-gen3x16-xdma-validate-2.1-3005608.1. Arch. rpm  

**V:** Bij het uitvoeren van xbutil validate in CentOS wordt deze waarschuwing weer gegeven: "waarschuwing: kernel-versie 3.10.0-1160.15.2.el7.x86_64 wordt niet officieel ondersteund. 4.18.0-193 is de meest recente ondersteunde versie. 

**A:** Dit kan veilig worden genegeerd. 

**V:** Wat zijn de verschillen tussen premises-en NP-Vm's met betrekking tot XRT? 

**A:** Op Azure ondersteunt XDMA 2,1-platform alleen Host_Mem-en DDR-functies voor het bewaren van gegevens. 

Host_Mem (SB) inschakelen (1 GB RAM): sudo xbutil host_mem--Enable--grootte 1g 

Host_Mem uitschakelen (SB): sudo xbutil host_mem--Disable 

**V:** Kan ik xbmgmt-opdrachten uitvoeren? 

**A:** Nee, op virtuele Azure-machines is er geen beheer ondersteuning rechtstreeks van de Azure-VM. 

 **V:** Moet ik een PLP laden? 

**A:** Nee, de PLP wordt automatisch voor u geladen. u hoeft dus niet te laden via xbmgmt-opdrachten. 

 
**V:** Ondersteunt Azure verschillende PLPs? 

**A:** Niet op dit moment. We ondersteunen alleen de PLP die zijn opgenomen in de implementatie platform pakketten. 

**V:** Hoe kan ik een query uitvoeren op de PLP-gegevens? 

**A:** U moet xbutil-query uitvoeren en het onderste gedeelte bekijken. 

## <a name="other-sizes"></a>Andere grootten

- [Algemeen gebruik](sizes-general.md)
- [Geoptimaliseerd voor geheugen](sizes-memory.md)
- [Geoptimaliseerd voor opslag](sizes-storage.md)
- [Geoptimaliseerde GPU](sizes-gpu.md)
- [Krachtig rekenvermogen](sizes-hpc.md)
- [Vorige generaties](sizes-previous-gen.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe [Azure Compute units (ACU)](acu.md) u kan helpen bij het vergelijken van de reken prestaties in azure-sku's.

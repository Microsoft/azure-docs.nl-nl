---
title: NP-serie - Azure Virtual Machines
description: Specificaties voor de VM's uit de NP-serie.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 02/09/2021
ms.author: vikancha
ms.openlocfilehash: 61488b88b00206cb78beed4fe773bf9377848701
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861045"
---
# <a name="np-series"></a>NP-serie 
De virtuele machines uit de NP-serie zijn powered by [Xilinx U250 ](https://www.xilinx.com/products/boards-and-kits/alveo/u250.html) FPGA's voor het versnellen van workloads, waaronder machine learning-deferentie, videotranscoderen en databasezoekanalyses & analyse. VM's uit de NP-serie powered by Intel Xeon 8171M -CPU's (Skylake) met een kloksnelheid van 3,2 GHz voor alle kernen.

[Premium Storage:](premium-storage-performance.md)ondersteund<br>
[Premium Storage in deaching:](premium-storage-performance.md)ondersteund<br>
[Livemigratie:](maintenance-and-updates.md)niet ondersteund<br>
[Updates met geheugenbehoud:](maintenance-and-updates.md)niet ondersteund<br>
VM-generatieondersteuning: generatie 1<br>
[Versneld netwerken:](../virtual-network/create-vm-accelerated-networking-cli.md)ondersteund<br>
[Kortstondige besturingssysteemschijven:](ephemeral-os-disks.md)niet ondersteund <br>
<br>

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Fpga | FPGA-geheugen: GiB | Max. aantal gegevensschijven | Maximale NIC's/verwachte netwerkbandbreedte (MBps) | 
|---|---|---|---|---|---|---|---|
| Standard_NP10s | 10 | 168 | 736  | 1 | 64  | 8 | 1 / 7500 | 
| Standard_NP20s | 20 | 336 | 1474 | 2 | 128 | 16 | 2 / 15000 | 
| Standard_NP40s | 40 | 672 | 2948 | 4 | 256 | 32 | 4 / 30000 | 



[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]


##  <a name="frequently-asked-questions"></a>Veelgestelde vragen

**V:** Welke versie van V version moet ik gebruiken? 

**A:** Xilinx raadt [Vmbo 2020.2 aan](https://www.xilinx.com/products/design-tools/vitis/vitis-platform.html)


**V:** Moet ik NP-VM's gebruiken om mijn oplossing te ontwikkelen? 

**A:** Nee, u kunt on-premises ontwikkelen en implementeren in de cloud. Zorg ervoor dat u de attestation-documentatie volgt om te implementeren op NP-VM's. 

**V:** Welke versie van XRT moet ik gebruiken?

**A:** xrt_202020.2.8.832 

**V:** Wat is het doelimplementatieplatform?

**A:** Gebruik de volgende platformen.
- xilinx-u250-gen3x16-xdma-platform-2.1-3_all
- xilinx-u250-gen3x16-xdma-validate_2.1-3005608.1 

**V:** Op welk platform moet ik zich richten voor ontwikkeling?

**A:** xilinx-u250-gen3x16-xdma-2.1-202010-1-dev_1-2954688_all 

**V:** Wat is het ondersteunde besturingssysteem (besturingssystemen)? 

**A:** Xilinx en Microsoft hebben Ubuntu 18.04 LTS en CentOS 7.8 gevalideerd.

 Xilinx heeft de volgende Marketplace-installatie afbeeldingen gemaakt om de implementatie van deze VM's te vereenvoudigen. 

[Xilinx Alveo U250 Deployment VM – Ubuntu18.04](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/GalleryItemDetailsBladeNopdl/id/xilinx.xilinx_alveo_u250_deployment_vm_ubuntu1804_032321)

[Xilinx Alveo U250 Deployment VM – CentOS7.8](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/GalleryItemDetailsBladeNopdl/id/xilinx.xilinx_alveo_u250_deployment_vm_centos78_032321)

**V:** Kan ik mijn eigen Ubuntu/CentOS-VM's implementeren en XRT/Deployment Target Platform installeren? 

**A:** Ja.

**V:** Als ik mijn eigen Ubuntu18.04-VM implementeer, wat zijn dan de vereiste pakketten en stappen?

**A:** Documentatie over Kernel 4.1X per [Xilinx XRT gebruiken](https://www.xilinx.com/support/documentation/sw_manuals/xilinx2020_2/ug1451-xrt-release-notes.pdf)
       
Installeer de volgende pakketten.
- xrt_202020.2.8.832_18.04-amd64-xrt.deb
       
- xrt_202020.2.8.832_18.04-amd64-azure.deb
       
- xilinx-u250-gen3x16-xdma-platform-2.1-3_all_18.04.deb.tar.gz
       
- xilinx-u250-gen3x16-xdma-validate_2.1-3005608.1_all.deb  

**V:** Op Ubuntu kan ik na het opnieuw opstarten van mijn VM mijn FPGA('s) niet vinden: 

**A:** Controleer of uw kernel niet is bijgewerkt (uname -a). Zo ja, downgrade dan naar kernel 4.1 X. 

**V:** Als ik mijn eigen CentOS7.8-VM implementeer, wat zijn dan de vereiste pakketten en stappen?

**A:** Kernelversie gebruiken: 3.10.0-1160.15.2.el7.x86_64

 Installeer de volgende pakketten.
   
 - xrt_202020.2.8.832_7.4.1708-x86_64-xrt.rpm 
      
 - xrt_202020.2.8.832_7.4.1708-x86_64-azure.rpm 
     
 - xilinx-u250-gen3x16-xdma-platform-2.1-3.noarch.rpm.tar.gz 
      
 - xilinx-u250-gen3x16-xdma-validate-2.1-3005608.1.noarch.rpm  

**V:** Bij het uitvoeren van xbutil validate op CentOS krijg ik de volgende waarschuwing: "WAARSCHUWING: Kernelversie 3.10.0-1160.15.2.el7.x86_64 wordt niet officieel ondersteund. 4.18.0-193 is de meest recente ondersteunde versie." 

**A:** Dit kan veilig worden genegeerd. 

**V:** Wat zijn de verschillen tussen OnPrem- en NP-VM's met betrekking tot XRT? 

**A:** Op Azure ondersteunt het XDMA 2.1-platform alleen Host_Mem(SB) en DDR-functies voor gegevensretentie. 

Als u Host_Mem (SB) (1 GB RAM): sudo xbutil host_mem --enable --size 1g 

Uitschakelen Host_Mem (SB): sudo xbutil host_mem --disable 

**V:** Kan ik xbmgmt-opdrachten uitvoeren? 

**A:** Nee, op azure-VM's is er geen beheerondersteuning rechtstreeks vanuit de Azure-VM. 

 **V:** Moet ik een PLP laden? 

**A:** Nee, de PLP wordt automatisch voor u geladen, dus u hoeft niet te laden via xbmgmt-opdrachten. 

 
**V:** Maakt ondersteuning voor Azure verschillende PLP's? 

**A:** Op dit moment niet. We ondersteunen alleen de PLP die wordt geleverd in de implementatieplatformpakketten. 

**V:** Hoe kan ik een query uitvoeren op de PLP-gegevens? 

**A:** U moet de xbutil-query uitvoeren en het onderste gedeelte bekijken. 

**V:** Welke aanvullende wijzigingen moet ik doen als ik mijn eigen VM maak en XRT handmatig implementeer? 

**A:** Voeg in /opt/xilinx/xrt/setup.sh een vermelding toe voor XRT_INI_PATH die verwijst naar /opt/xilinx/xrt/xrt.ini

 
De inhoud van /opt/xilinx/xrt/xrt.ini moet het volgende bevatten: <br>
[Runtime]<br>
ert =false <br>

## <a name="other-sizes"></a>Andere grootten

- [Algemeen gebruik](sizes-general.md)
- [Geoptimaliseerd voor geheugen](sizes-memory.md)
- [Geoptimaliseerd voor opslag](sizes-storage.md)
- [Geoptimaliseerde GPU](sizes-gpu.md)
- [Krachtig rekenvermogen](sizes-hpc.md)
- [Vorige generaties](sizes-previous-gen.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe [Azure Compute Units (ACU)](acu.md) u kan helpen bij het vergelijken van rekenprestaties tussen Azure-SKU's.

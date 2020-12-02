---
title: Een Lab instellen met Gpu's in Azure Lab Services | Microsoft Docs
description: Meer informatie over het instellen van een Lab met virtuele GPU-machines (graphics processing unit).
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: 8293ed1bfb53895b9631d9730fb75a2364457180
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452372"
---
# <a name="set-up-a-lab-with-gpu-virtual-machines"></a>Een Lab instellen met GPU virtual machines

In dit artikel leest u hoe u de volgende taken kunt uitvoeren:

- U kunt kiezen tussen *visualisatie* *en grafische gpu's (* graphics processing units).
- Zorg ervoor dat de juiste GPU-Stuur Programma's zijn geïnstalleerd.

## <a name="choose-between-visualization-and-compute-gpu-sizes"></a>Kiezen tussen visualisatie-en reken GPU-grootten
Selecteer op de eerste pagina van de wizard Lab maken in de vervolg keuzelijst **welke grootte van de virtuele machine nodig is?** de grootte van de virtuele machines die voor uw klasse nodig zijn.  

![Scherm afbeelding van het deel venster Nieuw Lab voor het selecteren van een VM-grootte](./media/how-to-setup-gpu/lab-gpu-selection.png)

In dit proces hebt u de mogelijkheid om een **visualisatie** of **reken** gpu's te selecteren.  Het is belang rijk om het type GPU te kiezen dat is gebaseerd op de software die uw studenten zullen gebruiken.  

Zoals beschreven in de volgende tabel is de *reken* GPU-grootte bedoeld voor computerintensieve toepassingen.  Het [diepe leer proces van het type natuurlijke taal verwerking](./class-type-deep-learning-natural-language-processing.md) maakt bijvoorbeeld gebruik van de grootte van de **kleine GPU (Compute)** .  De reken-GPU is geschikt voor dit type klasse, omdat studenten gebruikmaken van een diep geleerde frameworks en hulpprogram ma's die door de [Data Science virtual machine-afbeelding](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) worden verschaft om diepe leer modellen met grote gegevens sets te trainen.

| Grootte | Kernen | RAM | Beschrijving | 
| ---- | ----- | --- | ----------- | 
| Kleine GPU (Compute) | -&nbsp;6 &nbsp; kernen<br>-&nbsp;56 &nbsp; GB &nbsp; RAM-geheugen  | [Standard_NC6](../virtual-machines/nc-series.md) |Deze grootte is het meest geschikt voor computerintensieve toepassingen zoals kunst matige intelligentie (AI) en diep gaande lessen. |

De grootte van de *visualisatie* GPU is bedoeld voor grafische intensieve toepassingen.  Bijvoorbeeld, het [type SOLIDWORKS engineering](./class-type-solidworks.md) wordt weer gegeven met behulp van de grootte van de **kleine GPU (visualisatie)** .  De visualisatie GPU is geschikt voor dit type klasse, omdat studenten communiceren met de SOLIDWORKS 3D computer-aided design (CAD)-omgeving voor het model leren en visualiseren van effen objecten.

| Grootte | Kernen | RAM | Beschrijving | 
| ---- | ----- | --- | ----------- | 
| Kleine GPU (visualisatie) | -&nbsp;6 &nbsp; kernen<br>-&nbsp;56 &nbsp; GB &nbsp; RAM-geheugen  | [Standard_NV6](../virtual-machines/nv-series.md) | Deze grootte is het meest geschikt voor externe visualisatie, streaming, games en code ring waarbij frameworks zoals OpenGL en DirectX worden gebruikt. |
| Gemiddelde GPU (visualisatie) | -&nbsp;12 &nbsp; kernen<br>-&nbsp;112 &nbsp; GB &nbsp; RAM-geheugen  | [Standard_NV12](../virtual-machines/nv-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Deze grootte is het meest geschikt voor externe visualisatie, streaming, games en code ring waarbij frameworks zoals OpenGL en DirectX worden gebruikt. |

> [!NOTE]
> Sommige van deze VM-grootten worden niet weer geven in de lijst wanneer u een leslokaal Lab maakt. De lijst wordt ingevuld op basis van de huidige capaciteit van de locatie van het lab. Als de maker van het lab-account [een locatie voor het lab](allow-lab-creator-pick-lab-location.md)kan kiezen, kunt u proberen een andere locatie voor het lab te kiezen en te controleren of de VM-grootte beschikbaar is. Zie [producten beschikbaar per regio](https://azure.microsoft.com/regions/services/?products=virtual-machines)voor de beschik baarheid van vm's.

## <a name="ensure-that-the-appropriate-gpu-drivers-are-installed"></a>Zorg ervoor dat de juiste GPU-Stuur Programma's zijn geïnstalleerd
Zorg ervoor dat de juiste GPU-Stuur Programma's zijn geïnstalleerd om te profiteren van de GPU-mogelijkheden van uw Lab-Vm's.  Als u in de wizard Lab maken een GPU VM-grootte selecteert, kunt u de optie **GPU-Stuur Programma's installeren** selecteren.  

![Scherm afbeelding van de ' nieuwe Lab ' met de optie GPU-Stuur Programma's installeren](./media/how-to-setup-gpu/lab-gpu-drivers.png)

Zoals in de voor gaande afbeelding wordt weer gegeven, is deze optie standaard ingeschakeld, zodat u zeker weet dat de *meest recente* Stuur Programma's zijn geïnstalleerd voor het type GPU en de installatie kopie die u hebt geselecteerd.
- Wanneer u een *Compute* GPU-grootte selecteert, worden uw Lab-vm's aangedreven door de [Nvidia Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) GPU.  In dit geval worden de meest recente [CUDA-Stuur programma's (Unified Device Architecture)](http://developer.download.nvidia.com/compute/cuda/2_0/docs/CudaReferenceManual_2.0.pdf) geïnstalleerd, waardoor computers met hoge prestaties kunnen worden uitgevoerd.
- Wanneer u een grootte van een *visualisatie* -GPU selecteert, worden uw Lab-vm's aangedreven door de [Nvidia Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU en de [raster technologie](https://www.nvidia.com/content/dam/en-zz/Solutions/design-visualization/solutions/resources/documents1/NVIDIA_GRID_vPC_Solution_Overview.pdf).  In dit geval zijn de meest recente raster Stuur Programma's geïnstalleerd, waardoor het gebruik van grafische intensieve toepassingen mogelijk wordt.

### <a name="install-the-drivers-manually"></a>De Stuur Programma's hand matig installeren
Mogelijk moet u een andere versie van een stuur programma installeren dan de nieuwste versie.  In deze sectie wordt beschreven hoe u de juiste Stuur Programma's hand matig installeert, afhankelijk van of u een *Compute* GPU of een *visualisatie* -GPU gebruikt.

#### <a name="install-the-compute-gpu-drivers"></a>De compute GPU-Stuur Programma's installeren

Ga als volgt te werk om stuur Programma's hand matig te installeren voor de reken GPU-grootte:

1. Schakel in de wizard Lab maken, wanneer u [uw Lab maakt](./how-to-manage-classroom-labs.md), de instelling **GPU-Stuur Programma's installeren** uit.

1. Nadat uw Lab is gemaakt, maakt u verbinding met de VM van de sjabloon om de juiste Stuur Programma's te installeren.

   ![Scherm afbeelding van de pagina met Down loads voor NVIDIA-Stuur Programma's](./media/how-to-setup-gpu/nvidia-driver-download.png) 

   a. Ga in een browser naar de [pagina met Down loads voor nvidia-Stuur Programma's](https://www.nvidia.com/Download/index.aspx).  
   b. Stel het **product type** in op **Tesla**.  
   c. Stel de **product reeks** in op de **K-serie**.  
   d. Stel het **besturings systeem** in op basis van het type basis installatie kopie dat u hebt geselecteerd tijdens het maken van uw Lab.  
   e. Stel de **CUDA-werkset** in op de versie van het CUDA-stuur programma dat u nodig hebt.  
   f. Selecteer **zoeken** om uw stuur Programma's te zoeken.  
   g. Selecteer **downloaden** om het installatie programma te downloaden.  
   h. Voer het installatie programma uit zodat de Stuur Programma's op de sjabloon-VM worden geïnstalleerd.  
1. Controleer of de Stuur Programma's correct zijn geïnstalleerd door de instructies in de sectie [de geïnstalleerde Stuur Programma's valideren](how-to-setup-lab-gpu.md#validate-the-installed-drivers) te volgen. 
1. Nadat u de Stuur Programma's en andere software hebt geïnstalleerd die voor uw klasse zijn vereist, selecteert u **publiceren** om de vm's van uw studenten te maken.

> [!NOTE]
> Als u een Linux-installatie kopie gebruikt nadat u het installatie programma hebt gedownload, installeert u de Stuur Programma's door de instructies in [CUDA-Stuur Programma's installeren in Linux](../virtual-machines/linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#install-cuda-drivers-on-n-series-vms)te volgen.

#### <a name="install-the-visualization-gpu-drivers"></a>De visualisatie GPU-Stuur Programma's installeren

Ga als volgt te werk om stuur Programma's hand matig te installeren voor de grootte van de visualisatie GPU:

1. Schakel in de wizard Lab maken, wanneer u [uw Lab maakt](./how-to-manage-classroom-labs.md), de instelling **GPU-Stuur Programma's installeren** uit.
1. Nadat uw Lab is gemaakt, maakt u verbinding met de VM van de sjabloon om de juiste Stuur Programma's te installeren.
1. Installeer de raster Stuur Programma's die door micro soft worden meegeleverd met de sjabloon-VM door de instructies voor uw besturings systeem te volgen:
   -  [Windows NVIDIA-raster Stuur Programma's](../virtual-machines/windows/n-series-driver-setup.md#nvidia-grid-drivers)
   -  [Linux NVIDIA-raster Stuur Programma's](../virtual-machines/linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#nvidia-grid-drivers)
  
1. Start de sjabloon-VM opnieuw op.
1. Controleer of de Stuur Programma's correct zijn geïnstalleerd door de instructies in de sectie [de geïnstalleerde Stuur Programma's valideren](how-to-setup-lab-gpu.md#validate-the-installed-drivers) te volgen.
1. Nadat u de Stuur Programma's en andere software hebt geïnstalleerd die voor uw klasse zijn vereist, selecteert u **publiceren** om de vm's van uw studenten te maken.

### <a name="validate-the-installed-drivers"></a>De geïnstalleerde Stuur Programma's valideren
In deze sectie wordt beschreven hoe u controleert of uw GPU-Stuur Programma's goed zijn geïnstalleerd.

#### <a name="windows-images"></a>Windows-installatiekopieën
1.  Volg de instructies in de sectie ' installatie van Stuur Programma's controleren ' van [NVIDIA GPU-Stuur Programma's installeren op vm's uit de N-serie waarop Windows wordt uitgevoerd](../virtual-machines/windows/n-series-driver-setup.md#verify-driver-installation).
1.  Als u een *visualisatie* -GPU gebruikt, kunt u ook het volgende doen:
    - Bekijk en wijzig uw GPU-instellingen in het NVIDIA-configuratie scherm. Hiertoe selecteert u **Hardware** in **het configuratie scherm van Windows** en selecteert u vervolgens **NVIDIA-configuratie scherm**.

      ![Scherm afbeelding van het configuratie scherm van Windows met de koppeling van het NVIDIA-configuratie scherm](./media/how-to-setup-gpu/control-panel-nvidia-settings.png) 

     - Bekijk uw GPU-prestaties met behulp van **taak beheer**.  Hiertoe selecteert u het tabblad **prestaties** en selecteert u vervolgens de optie **GPU** .

       ![Scherm opname van het tabblad GPU-prestaties van taak beheer](./media/how-to-setup-gpu/task-manager-gpu.png) 

      > [!IMPORTANT]
      > De instellingen van het configuratie scherm van NVIDIA zijn alleen toegankelijk voor *visualisatie* gpu's.  Als u het NVIDIA-configuratie scherm probeert te openen voor een compute-GPU, wordt de volgende fout weer gegeven: "NVIDIA-weergave-instellingen zijn niet beschikbaar.  Er wordt momenteel geen weer gave gebruikt die is gekoppeld aan een NVIDIA-GPU.  Op dezelfde manier wordt de GPU-prestatie gegevens in taak beheer alleen gegeven voor visualisatie Gpu's.

#### <a name="linux-images"></a>Linux-installatie kopieën
Volg de instructies in de sectie ' installatie van Stuur Programma's controleren ' van [NVIDIA GPU-Stuur Programma's installeren op vm's met N-serie waarop Linux wordt uitgevoerd](../virtual-machines/linux/n-series-driver-setup.md#verify-driver-installation).

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- [Labs maken en beheren](how-to-manage-classroom-labs.md)
- [Type SOLIDWORKS computer-aided design (CAD)-klasse](class-type-solidworks.md)
- [Het klassen type MATLAB (matrix laboratorium)](class-type-matlab.md)
---
title: Setup van NVIDIA GPU-stuur programma voor Azure N-Series voor Windows
description: NVIDIA GPU-Stuur Programma's instellen voor virtuele machines uit de N-serie met Windows Server of Windows in azure
author: vikancha-MSFT
manager: jkabat
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.collection: windows
ms.topic: how-to
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: vikancha
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 05487db1d0522634eda606dcb2ba22876fc341db
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102557434"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-windows"></a>NVIDIA GPU-Stuur Programma's installeren op Vm's met N-serie waarop Windows wordt uitgevoerd 

Als u gebruik wilt maken van de GPU-mogelijkheden van virtuele machines van Azure N-serie die worden ondersteund door NVIDIA-Gpu's, moet u NVIDIA GPU-Stuur Programma's installeren. Met de [uitbrei ding NVIDIA GPU-stuur programma](../extensions/hpccompute-gpu-windows.md) worden de juiste NVIDIA-CUDA of raster Stuur Programma's geïnstalleerd op een virtuele machine uit de N-serie. De uitbrei ding installeren of beheren met de Azure Portal of hulpprogram ma's, zoals Azure PowerShell of Azure Resource Manager sjablonen. Zie de [documentatie over NVIDIA GPU-Stuur Programma's](../extensions/hpccompute-gpu-windows.md) voor ondersteunde besturings systemen en implementaties tappen.

Als u ervoor kiest om de NVIDIA GPU-Stuur Programma's hand matig te installeren, worden in dit artikel ondersteunde besturings systemen, stuur Programma's en installatie-en verificatie stappen beschreven. Informatie over hand matige installatie van Stuur Programma's is ook beschikbaar voor [Linux-vm's](../linux/n-series-driver-setup.md).

Zie [GPU Windows VM-grootten](../sizes-gpu.md?toc=/azure/virtual-machines/windows/toc.json)voor basis specificaties, opslag capaciteit en schijf Details. 

[!INCLUDE [virtual-machines-n-series-windows-support](../../../includes/virtual-machines-n-series-windows-support.md)]

## <a name="driver-installation"></a>Installatie van Stuur Programma's

1. Verbind door Extern bureaublad naar elke VM van de N-serie.

2. Down load, pak en installeer het ondersteunde stuur programma voor uw Windows-besturings systeem.

Nadat het raster stuur programma is geïnstalleerd op een VM, moet opnieuw worden opgestart. Na de installatie van het CUDA-stuur programma is het niet nodig om opnieuw op te starten.

## <a name="verify-driver-installation"></a>Installatie van stuur programma verifiëren

Houd er rekening mee dat het NVIDIA-configuratie scherm alleen toegankelijk is met de installatie van het raster stuur programma. Als u CUDA-Stuur Programma's hebt geïnstalleerd, wordt het NVIDIA-configuratie scherm niet weer gegeven.

U kunt de installatie van Stuur Programma's controleren in Apparaatbeheer. In het volgende voor beeld ziet u een geslaagde configuratie van de Tesla K80-kaart op een Azure NC-VM.

![Eigenschappen van GPU-stuur programma](./media/n-series-driver-setup/GPU_driver_properties.png)

Als u de status van het GPU-apparaat wilt opvragen, voert u het [NVIDIA-SMI-](https://developer.nvidia.com/nvidia-system-management-interface) opdracht regel programma uit dat is geïnstalleerd met het stuur programma.

1. Open een opdracht prompt en ga naar de map **C:\Program Files\NVIDIA Corporation\NVSMI** .

2. Voer `nvidia-smi` uit. Als het stuur programma is geïnstalleerd, ziet u uitvoer die vergelijkbaar is met de volgende. Met de **GPU-util** wordt **0%** weer gegeven, tenzij u momenteel een GPU-werk belasting op de VM uitvoert. De versie-en GPU-Details van uw stuur programma kunnen afwijken van de gegevens die worden weer gegeven.

![Status van NVIDIA-apparaten](./media/n-series-driver-setup/smi.png)  

## <a name="rdma-network-connectivity"></a>RDMA-netwerk verbinding

RDMA-netwerk connectiviteit kan worden ingeschakeld op met RDMA geschikte virtuele machines uit de N-serie, zoals NC24r geïmplementeerd in dezelfde beschikbaarheidsset of in één plaatsings groep in een virtuele-machine schaalset. De HpcVmDrivers-extensie moet worden toegevoegd om Windows-netwerk Stuur Programma's te installeren waarmee RDMA-connectiviteit mogelijk wordt gemaakt. Gebruik [Azure PowerShell](/powershell/azure/) -cmdlets voor Azure Resource Manager om de VM-extensie toe te voegen aan een RDMA-VM van de N-serie.

Als u de meest recente versie 1,1 HpcVMDrivers-extensie wilt installeren op een bestaande, met RDMA geschikte virtuele machine met de naam myVM in de regio vs-West:
  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  Zie voor meer informatie [virtuele-machine uitbreidingen en functies voor Windows](../extensions/features-windows.md).

Het RDMA-netwerk ondersteunt MPI-verkeer (Message Passing Interface) voor toepassingen die worden uitgevoerd met [micro soft mpi](/message-passing-interface/microsoft-mpi) of Intel mpi 5. x. 


## <a name="next-steps"></a>Volgende stappen

* Ontwikkel aars die GPU-versnelde toepassingen bouwen voor de NVIDIA Tesla-Gpu's, kunnen ook de meest recente [CUDA-Toolkit](https://developer.nvidia.com/cuda-downloads)downloaden en installeren. Zie de [installatie handleiding voor CUDA](https://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi)voor meer informatie.

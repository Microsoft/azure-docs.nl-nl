---
title: Setup voor Azure N-serie AMD GPU-stuur programma voor Windows
description: AMD GPU-Stuur Programma's instellen voor virtuele machines uit de N-serie met Windows Server of Windows in azure
author: vikancha-MSFT
manager: jkabat
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/4/2019
ms.author: vikancha
ms.openlocfilehash: 1e08d54b9467231233c62635dafc5135456a3843
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101695410"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>AMD GPU-Stuur Programma's installeren op Vm's met N-serie waarop Windows wordt uitgevoerd

Als u gebruik wilt maken van de GPU-mogelijkheden van de nieuwe virtuele machines uit de Azure NVv4-serie met Windows, moeten de AMD GPU-Stuur Programma's zijn geïnstalleerd. Met de [uitbrei ding van het AMD GPU-stuur programma](../extensions/hpccompute-amd-gpu-windows.md) worden AMD GPU-Stuur Programma's geïnstalleerd op een NVv4-serie. De uitbrei ding installeren of beheren met de Azure Portal of hulpprogram ma's, zoals Azure PowerShell of Azure Resource Manager sjablonen. Zie de [documentatie over de uitbrei ding van de AMD GPU-stuur programma](../extensions/hpccompute-amd-gpu-windows.md) voor ondersteunde besturings systemen en implementaties tappen.

Als u ervoor kiest om de AMD GPU-Stuur Programma's hand matig te installeren, worden in dit artikel ondersteunde besturings systemen, stuur Programma's en installatie-en verificatie stappen beschreven.

Alleen GPU-Stuur Programma's die door micro soft zijn gepubliceerd, worden ondersteund op NVv4 Vm's. Installeer geen GPU-Stuur Programma's vanuit een andere bron.

Zie [GPU Windows VM-grootten](../sizes-gpu.md?toc=/azure/virtual-machines/windows/toc.json)voor basis specificaties, opslag capaciteit en schijf Details.



## <a name="supported-operating-systems-and-drivers"></a>Ondersteunde besturingssystemen en stuurprogramma’s

| Besturingssysteem | Stuurprogramma |
| -------- |------------- |
| Windows 10 Enter prise multi-session-build 1909 <br/><br/>Windows 10-build 1909<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [20. Q4](https://download.microsoft.com/download/f/1/6/f16e6275-a718-40cd-a366-9382739ebd39/AMD-Azure-NVv4-Driver-20Q4.exe) (. exe) |

 > [!NOTE]
   >  Als u build 1903/1909 gebruikt, moet u mogelijk het volgende groeps beleid bijwerken voor optimale prestaties. Deze wijzigingen zijn niet nodig voor andere Windows-builds.
   >  
   >  [Computer configuratie->beleid->Windows-instellingen: >Beheersjablonen->Windows-onderdelen->Extern bureaublad-services->Extern bureaublad sessiemap->externe sessie omgeving], stel het beleid in [gebruik het WDDM-stuur programma voor het weer geven van Extern bureaublad-verbindingen] in op uitgeschakeld.
   >  


## <a name="driver-installation"></a>Installatie van Stuur Programma's

1. Verbind door Extern bureaublad naar elke VM van de NVv4-serie.

2. Als u de vorige versie van het stuur programma wilt verwijderen, moet u het hulp programma voor de AMD Cleanup [hier](https://download.microsoft.com/download/4/f/1/4f19b714-9304-410f-9c64-826404e07857/AMDCleanupUtilityni.exe) downloaden. gebruik het hulp programma dat bij de vorige versie van het stuur programma hoort.

3. Down load en installeer het meest recente stuur programma.

4. Start de VM opnieuw op.

## <a name="verify-driver-installation"></a>Installatie van stuur programma verifiëren

U kunt de installatie van Stuur Programma's controleren in Apparaatbeheer. In het volgende voor beeld ziet u een geslaagde configuratie van de Radeon instinct MI25-kaart op een Azure NVv4-VM.
<br />

![Scherm afbeelding met een geslaagde configuratie van de Radeon instinct MI25-kaart op een Azure NVv4 VM.](./media/n-series-amd-driver-setup/device-manager.png)

U kunt Dxdiag gebruiken om de eigenschappen van de GPU-weer gave te controleren, inclusief de video-RAM. In het volgende voor beeld ziet u een 1/2-partitie van de Radeon instinct MI25-kaart op een Azure NVv4-VM.
<br />
![Scherm afbeelding met een 1/2-partitie van de Radeon instinct MI25-kaart op een Azure NVv4 VM.](./media/n-series-amd-driver-setup/dxdiag-output-new.png)

Als u Windows 10 build 1903 of hoger gebruikt, wordt in Dxdiag geen informatie weer gegeven op het tabblad weer geven. Gebruik de optie ' alle gegevens opslaan ' onderaan en in het uitvoer bestand worden de gegevens weer gegeven die betrekking hebben op AMD MI25 GPU.

![Eigenschappen van GPU-stuur programma](./media/n-series-amd-driver-setup/dxdiag-details.png)

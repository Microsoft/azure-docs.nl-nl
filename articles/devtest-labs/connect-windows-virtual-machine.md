---
title: Verbinding maken met uw virtuele Windows-machines in Azure DevTest Labs
description: Meer informatie over hoe u verbinding maakt met uw virtuele Windows-machine in een Lab (Azure DevTest Labs)
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: e1e786daa396548030976159d1b150caa4b24396
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86540678"
---
# <a name="connect-to-a-windows-vm-in-your-lab-azure-devtest-labs"></a>Verbinding maken met een virtuele Windows-machine in uw Lab (Azure DevTest Labs)
In dit artikel wordt beschreven hoe u verbinding maakt met een virtuele Windows-machine in uw Lab. 

## <a name="connect-to-a-windows-vm"></a>Verbinding maken met een Windows-VM
1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Zoek en selecteer **DevTest Labs** in de zoek balk. 

    :::image type="content" source="./media/connect-windows-virtual-machine/search-select.png" alt-text="DevTest Labs zoeken en selecteren":::    
1. Selecteer in de lijst met Labs uw **Lab**.

    :::image type="content" source="./media/connect-windows-virtual-machine/select-lab.png" alt-text="Uw Lab selecteren":::            
1. Selecteer uw Windows-VM in de lijst **mijn virtuele machines** op de start pagina van uw Lab. 

    :::image type="content" source="./media/connect-windows-virtual-machine/select-windows-vm.png" alt-text="Uw Windows-VM selecteren":::                
1. Selecteer op de pagina **virtuele machine** voor uw VM de optie **verbinding maken** op de werk balk. Als de virtuele machine is gestopt, selecteert u eerst **starten** om de VM te starten.

    :::image type="content" source="./media/connect-windows-virtual-machine/select-connect.png" alt-text="Selecteer verbinding maken op de werk balk":::                    
1. Als u de Edge-browser gebruikt, ziet u de koppeling naar het **gedownloade RDP-bestand** onder aan de browser. 

    :::image type="content" source="./media/connect-windows-virtual-machine/rdp-download.png" alt-text="RDP gedownload":::                        
1. Open het RDP-bestand en voer uw VM-referenties in die u hebt getypt bij het maken van de virtuele machine. U moet nu zijn verbonden met de Windows-VM. 

## <a name="next-steps"></a>Volgende stappen
[Procedure: verbinding maken met een virtuele Linux-machine](connect-linux-virtual-machine.md)

---
title: Wijzig de grootte van Vm's op Azure Stack Edge Pro GPU, Pro R, mini maal R via de Azure Portal
description: Meer informatie over het wijzigen van de grootte van virtuele machines (VM) die worden uitgevoerd op uw Azure Stack Edge Pro GPU, Azure Stack Edge Pro R, Azure Stack Edge mini-R via de Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/30/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to resize VMs running on an Azure Stack Edge Pro device so that I can use it to run applications using Edge compute before sending it to Azure.
ms.openlocfilehash: bf2125a6e1d0b443202a036c52fdf845f79d11fa
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080299"
---
# <a name="use-the-azure-portal-to-resize-the-vms-on-your-azure-stack-edge-pro-gpu"></a>De Azure Portal gebruiken om de grootte van de virtuele machines op uw Azure Stack Edge Pro GPU te wijzigen

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In dit artikel wordt uitgelegd hoe u het formaat van de virtuele machines (Vm's) die op uw Azure Stack Edge Pro GPU-apparaat zijn geïmplementeerd, kunt wijzigen.

       
## <a name="about-vm-sizing"></a>Over VM-grootte

De VM-grootte bepaalt de hoeveelheid reken resources (zoals CPU, GPU en geheugen) die beschikbaar worden gesteld voor de virtuele machine. U moet virtuele machines maken met behulp van een VM-grootte die geschikt is voor de workload van uw toepassing. 

Hoewel alle computers worden uitgevoerd op dezelfde hardware, hebben computer grootten verschillende limieten voor schijf toegang. Dit kan u helpen bij het beheren van de algemene schijf toegang op uw Vm's. Als een werk belasting toeneemt, kunt u ook de grootte van een bestaande virtuele machine wijzigen.

Zie [ondersteunde VM-grootten voor uw apparaat](azure-stack-edge-gpu-virtual-machine-sizes.md)voor meer informatie.


## <a name="prerequisites"></a>Vereisten

Voordat u de grootte van een VM die op uw apparaat wordt uitgevoerd wijzigt via de Azure Portal, moet u ervoor zorgen dat:

1. Er is ten minste één virtuele machine op het apparaat geïmplementeerd. Als u deze virtuele machine wilt maken, raadpleegt u de instructies in [virtuele machine implementeren op uw Azure stack Edge Pro via de Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).

1. De VM moet de status **gestopt** hebben. Als u uw virtuele machine wilt stoppen, gaat u naar **virtuele machines > overzicht** en selecteert u de virtuele machine die u wilt stoppen. Selecteer op de pagina overzicht de optie **stoppen** en selecteer vervolgens **Ja** wanneer u wordt gevraagd om bevestiging. Voordat u de grootte van de virtuele machine wijzigt, moet u de virtuele machine stoppen.

    ![VM stoppen vanaf overzichts pagina](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/stop-vm-2.png)


## <a name="resize-a-vm"></a>De grootte van een virtuele machine wijzigen

Volg deze stappen om de grootte van een virtuele machine die op het apparaat is geïmplementeerd, te wijzigen. 

1. Ga naar de virtuele machine die u hebt gestopt en ga vervolgens naar de pagina **overzicht** . Selecteer de **VM-grootte (wijzigen)**.
    
    ![Wijziging van de VM-grootte op de overzichts pagina selecteren](./media/azure-stack-edge-gpu-manage-virtual-machine-resize-portal/change-vm-size-1.png)

2. Selecteer de **VM-grootte** in de Blade **VM-grootte wijzigen** van de opdracht balk en selecteer vervolgens **wijzigen**.

    ![Nieuwe VM-grootte selecteren](./media/azure-stack-edge-gpu-manage-virtual-machine-resize-portal/change-vm-size-2.png)

3. U ziet een melding dat de virtuele machine wordt bijgewerkt. Nadat de virtuele machine is bijgewerkt, wordt de pagina **overzicht** vernieuwd om de VM met de gewijzigde grootte weer te geven.

    ![Grootte van virtuele machine gewijzigd ](./media/azure-stack-edge-gpu-manage-virtual-machine-resize-portal/change-vm-size-3.png)


## <a name="next-steps"></a>Volgende stappen

Zie [virtuele machines implementeren via de Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)voor meer informatie over het implementeren van virtuele machines op uw Azure stack Edge Pro-apparaat.

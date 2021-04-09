---
title: Gebruik van een lab bijhouden in Azure Lab Services | Microsoft Docs
description: In deze zelfstudie houdt u, als maker/eigenaar van een lab, het gebruik van uw lab bij.
ms.topic: tutorial
ms.date: 06/26/2020
ms.openlocfilehash: 8a3ca9e011eb89b3db8b202bab11d14f10d74e7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96434614"
---
# <a name="tutorial-track-usage-of-a-lab-in-azure-lab-service"></a>Zelfstudie: gebruik van een lab bijhouden in Azure Lab Services
In deze zelfstudie leert u hoe een maker of eigenaar van een lab het gebruik van een lab kan bijhouden.

In deze zelfstudie voert u de volgende acties uit:

> [!div class="checklist"]
> * Gebruikers weergeven die zijn geregistreerd bij uw lab
> * Het gebruik van virtuele machines in het lab weergeven
> * Virtuele machines van studenten beheren 


## <a name="view-registered-users"></a>Geregistreerde gebruikers weergeven

1. Navigeer naar de [Azure Lab Services-website](https://labs.azure.com). 
2. Selecteer **Aanmelden** en voer uw referenties in. Azure Lab Services ondersteunt organisatieaccounts en Microsoft-accounts.
3. Op de pagina **Mijn labs** selecteert u het lab waarvoor u het gebruik wilt bijhouden. 
4. Selecteer **Gebruikers** in het menu links of in de tegel **Gebruikers**. U ziet studenten die zijn geregistreerd bij uw lab.  

    ![Geregistreerde gebruikers](./media/tutorial-track-usage/registered-users.png)

    Zie [Labgebruikers toevoegen en beheren](how-to-configure-student-usage.md) voor meer informatie over het toevoegen en beheren van gebruikers voor het lab.

## <a name="view-the-usage-of-vms"></a>Het gebruik van VM's weergeven

1. Selecteer **Virtuele machines** in het menu aan de linkerkant. 
2. Controleer of u de status van virtuele machines kunt zien en het aantal uur dat de virtuele machines actief zijn geweest. De tijd die een labeigenaar besteedt op de VM van een student, wordt niet meegeteld in de gebruikstijd die wordt weergegeven in de laatste kolom. 

    ![VM-gebruik](./media/tutorial-track-usage/vm-usage.png)

## <a name="manage-student-vms"></a>Virtuele machines van studenten beheren 
Op deze pagina kunt u student-VM's starten, stoppen of opnieuw instellen met behulp van besturingselementen in de kolom **Status** of op de werkbalk.

![VM-acties](./media/tutorial-track-usage/vm-controls.png)

Zie [Virtuele-machinegroep instellen en beheren](how-to-set-virtual-machine-passwords.md) voor meer informatie over het beheren van de virtuele-machinegroep voor het lab.

> [!NOTE]
> Wanneer een onderwijzer een student-VM inschakelt, wordt het quotum voor de student niet beïnvloed. Een quotum voor een gebruiker specificeert het aantal beschikbare laburen voor de gebruiker buiten de geplande lestijd. Voor meer informatie over quota raadpleegt u [Quota instellen voor gebruikers](how-to-configure-student-usage.md?#set-quotas-for-users).

## <a name="next-steps"></a>Volgende stappen
Zie de artikelen onder [Instructiegidsen](how-to-manage-lab-accounts.md) voor meer informatie over labs.

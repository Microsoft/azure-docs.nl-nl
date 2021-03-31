---
title: Het formaat van een virtuele machine in een lab in Azure DevTest Labs wijzigen
description: Meer informatie over het wijzigen van de grootte van een virtuele machine (VM) in Azure DevTest Labs op basis van uw behoeften voor CPU-, netwerk-of schijf prestaties.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 592be0862bc33ead86fe43d37753fb72fde120c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85482017"
---
# <a name="resize-a-vm-in-a-lab-in-azure-devtest-labs"></a>Het formaat van een virtuele machine in een lab in Azure DevTest Labs wijzigen
Een van de belang rijke functies van virtuele Azure-machines is dat hiermee de grootte van een virtuele machine (VM) kan worden gewijzigd op basis van uw behoeften voor CPU-, netwerk-of schijf prestaties. Azure DevTest Labs ondersteunt deze functie nu voor Vm's in een lab. De functie voor het wijzigen van het formaat voldoet aan het lab-beleid voor toegestane VM-grootten in het lab. Dat wil zeggen dat u de grootte van een virtuele machine kunt wijzigen in alleen toegestane grootten in het lab. 


## <a name="steps-to-resize-a-vm-in-a-lab"></a>Stappen voor het wijzigen van de grootte van een virtuele machine in een Lab 
Voer de volgende stappen uit om de grootte van een virtuele machine in een lab in Azure DevTest Labs te wijzigen: 

> [!NOTE]
> Als u verbinding hebt met de virtuele machine via een extern bureau blad-sessie (RDP), slaat u uw werk op en verbreekt u de verbinding met de virtuele machine voordat u het formaat ervan wijzigt.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **alle services** en selecteer vervolgens **DevTest Labs** in de lijst.
3. Selecteer in de lijst met Labs het lab dat de virtuele machine bevat waarvan u de grootte wilt wijzigen.  
4. Selecteer in het linkerdeel venster **mijn virtual machines**. 
5. Selecteer een virtuele machine in de lijst met Vm's.
6. Selecteer **stoppen** op de werk balk als de virtuele machine wordt uitgevoerd. Controleer de status van de bewerking in het venster **meldingen** . Wacht totdat de virtuele machine is gestopt en sluit het venster **meldingen** . 

    ![De virtuele machine stoppen](media/devtest-lab-resize-vm/stop-vm.png)
1. Selecteer op de pagina virtuele machine voor uw VM de optie **grootte** onder **instellingen** in het menu links.

    ![Menu grootte](media/devtest-lab-resize-vm/size-menu.png)
1. Blader in het venster **een grootte kiezen** en selecteer een grootte voor de virtuele machine en klik op **selecteren**.     
1. Controleer de status van de bewerking voor het wijzigen van de grootte in het venster **meldingen** .

    ![Status van formaat wijzigen](media/devtest-lab-resize-vm/resize-status.png)
10. Nadat de grootte is gewijzigd, sluit u het venster **meldingen** . 
11. Selecteer **overzicht** in het menu links en selecteer **opnieuw opstarten** op de werk balk om de virtuele machine opnieuw op te starten. 

## <a name="next-steps"></a>Volgende stappen
Zie [grootte van virtuele machines aanpassen](https://azure.microsoft.com/blog/resize-virtual-machines/)voor meer informatie over de functie voor het wijzigen van het formaat dat door Azure virtual machines wordt ondersteund.



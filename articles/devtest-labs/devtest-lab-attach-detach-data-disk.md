---
title: Een gegevens schijf koppelen aan of loskoppelen van een virtuele machine in Azure DevTest Labs
description: Meer informatie over het koppelen of ontkoppelen van een gegevens schijf aan een virtuele machine in Azure DevTest Labs
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 8eebfbda421233bcec780d441a4020acce740618
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91328510"
---
# <a name="attach-or-detach-a-data-disk-to-a-virtual-machine-in-azure-devtest-labs"></a>Een gegevens schijf koppelen aan of loskoppelen van een virtuele machine in Azure DevTest Labs
[Azure Managed disks](../virtual-machines/managed-disks-overview.md) beheert de opslag accounts die zijn gekoppeld aan gegevens schijven van virtuele machines. Een gebruiker koppelt een nieuwe gegevens schijf aan een virtuele machine, geeft het type en de grootte van de schijf op die nodig is, en Azure maakt en beheert de schijf automatisch. De gegevens schijf kan vervolgens worden losgekoppeld van de virtuele machine en later opnieuw worden gekoppeld aan dezelfde virtuele machine, of zijn gekoppeld aan een andere VM die tot dezelfde gebruiker behoort.

Deze functie is handig voor het beheren van opslag of software buiten elke afzonderlijke virtuele machine. Als de opslag of software al aanwezig is in een gegevens schijf, kan deze eenvoudig worden gekoppeld, losgekoppeld en opnieuw worden gekoppeld aan een VM die eigendom is van de gebruiker die eigenaar is van die gegevens schijf.

## <a name="attach-a-data-disk"></a>Een gegevensschijf koppelen
Lees de volgende tips voordat u een gegevens schijf aan een virtuele machine koppelt:

- De grootte van de virtuele machine bepaalt hoeveel gegevens schijven u kunt bijvoegen. Zie [grootten voor virtuele machines](../virtual-machines/sizes.md)voor meer informatie.
- U kunt alleen een gegevens schijf koppelen aan een virtuele machine waarop wordt uitgevoerd. Zorg ervoor dat de virtuele machine wordt uitgevoerd voordat u een gegevens schijf koppelt.

### <a name="attach-a-new-disk"></a>Een nieuwe schijf koppelen
Volg deze stappen om een nieuwe beheerde gegevens schijf te maken en te koppelen aan een virtuele machine in Azure DevTest Labs.

1. Meld u aan bij [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecteer **alle services** en selecteer vervolgens **DevTest Labs** in de lijst.
1. Selecteer in de lijst met Labs het gewenste Lab. 
1. Selecteer in de lijst met **mijn virtuele machines** een actieve virtuele machine.
1. Selecteer in het menu aan de linkerkant de optie **schijven**.
1. Kies **Nieuw toevoegen** om een nieuwe gegevens schijf te maken en deze te koppelen aan de virtuele machine.

    ![Nieuwe gegevens schijf aan een virtuele machine koppelen](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new.png)
1. Voltooi het deel venster **nieuwe schijf koppelen** door de naam, het type en de grootte van de gegevens schijf in te voeren.

    ![Het formulier ' nieuwe schijf koppelen ' volt ooien](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new-form.png)
1. Selecteer **OK**.

Na enkele ogen blikken wordt de nieuwe gegevens schijf gemaakt en gekoppeld aan de virtuele machine en wordt deze weer gegeven in de lijst met **gegevens schijven** voor die VM.

### <a name="attach-an-existing-disk"></a>Een bestaande schijf koppelen
Volg deze stappen om een bestaande beschik bare gegevens schijf opnieuw te koppelen aan een actieve virtuele machine. 

1. Selecteer een actieve virtuele machine waarvoor u een gegevens schijf opnieuw wilt koppelen.
1. Selecteer in het menu aan de linkerkant de optie **schijven**.
1. Selecteer **bestaande koppelen** om een beschik bare gegevens schijf aan de VM te koppelen.

    ![Scherm afbeelding waarin de instelling ' schijven ' is geselecteerd en ' koppelen ' is geselecteerd.](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing-button.png)

1. Selecteer OK in het deel venster **bestaande schijf koppelen** .

    ![Bestaande gegevens schijf koppelen aan een virtuele machine](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing.png)

Na enkele ogen blikken wordt de gegevens schijf aan de virtuele machine gekoppeld en wordt deze weer gegeven in de lijst met **gegevens schijven** voor die VM.

## <a name="detach-a-data-disk"></a>Een gegevensschijf ontkoppelen
Wanneer u een gegevens schijf die is gekoppeld aan een virtuele machine niet meer nodig hebt, kunt u deze eenvoudig loskoppelen. Als u de schijf loskoppelt, wordt deze verwijderd uit de virtuele machine, maar blijft deze in opslag voor later gebruik.

Als u de bestaande gegevens op de schijf opnieuw wilt gebruiken, kunt u deze opnieuw koppelen aan dezelfde virtuele machine of een andere.

### <a name="detach-from-the-vms-management-pane"></a>Loskoppelen van het beheer deel venster van de virtuele machine
1. Selecteer in de lijst met virtuele machines een virtuele machine waaraan een gegevens schijf is gekoppeld.
1. Selecteer in het menu aan de linkerkant de optie **schijven**.
1. Selecteer in de lijst met **gegevens schijven** de gegevens schijf die u wilt loskoppelen.

    ![Gegevens schijven voor een virtuele machine selecteren](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-button.png) 
1. Selecteer **loskoppelen** boven in het detail venster van de schijf.

    ![Scherm afbeelding toont het deel venster met details van de schijf waarvan de actie loskoppelen is gemarkeerd.](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk2.png)
1. Selecteer **Ja** om te bevestigen dat u de gegevens schijf wilt loskoppelen.

De schijf is losgekoppeld en is beschikbaar om aan een andere virtuele machine te koppelen. 
### <a name="detach-from-the-labs-main-pane"></a>Loskoppelen van het hoofd venster van het lab
1. Selecteer **mijn gegevens schijven** in het hoofd venster van uw Lab.
1. Klik met de rechter muisknop op de gegevens schijf die u wilt loskoppelen: of selecteer het weglatings teken (**...**), en kies **loskoppelen**.

    ![Een gegevensschijf ontkoppelen](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk.png)
1. Selecteer **Ja** om te bevestigen dat u de koppeling wilt verbreken.

   > [!NOTE]
   > Als een gegevens schijf al is losgekoppeld, kunt u ervoor kiezen om deze te verwijderen uit de lijst met beschik bare gegevens schijven door **verwijderen** te selecteren.
   >
   >

## <a name="upgrade-an-unmanaged-data-disk"></a>Een niet-beheerde gegevens schijf upgraden
Als u een bestaande VM hebt die gebruikmaakt van niet-beheerde gegevens schijven, kunt u de virtuele machine eenvoudig converteren voor het gebruik van beheerde schijven. Dit proces converteert zowel de besturingssysteem schijf als eventuele gekoppelde gegevens schijven.

Als u een niet-beheerde gegevens schijf wilt bijwerken, volgt u de stappen in dit artikel om [de gegevens schijf](#detach-a-data-disk) van een niet-beheerde virtuele machine te ontkoppelen. [Koppel de schijf](#attach-an-existing-disk) vervolgens opnieuw aan een beheerde virtuele machine om de gegevens schijf automatisch bij te werken van niet-beheerd naar beheerd.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het beheren van gegevens schijven voor [claim bare virtuele machines](devtest-lab-add-claimable-vm.md#unclaim-a-vm).

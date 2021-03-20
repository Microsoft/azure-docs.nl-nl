---
title: Een lab openen in Azure DevTest Labs | Microsoft Docs
description: In deze zelfstudie opent u het lab dat is gemaakt met Azure DevTest Labs, claimt u virtuele machines, gebruikt u deze en maakt u de claims op een virtuele machine weer ongedaan.
ms.topic: tutorial
ms.date: 06/26/2020
ms.author: spelluru
ms.openlocfilehash: b4477e0b98ef534b8170ee297edf88ac6fa62dd7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85476441"
---
# <a name="tutorial-access-a-lab-in-azure-devtest-labs"></a>Zelfstudie: Een lab openen in Azure DevTest Labs
In deze zelfstudie gebruikt u het lab dat is gemaakt in de [zelfstudie: Een lab maken in Azure DevTest Labs](tutorial-create-custom-lab.md).

In deze zelfstudie voert u de volgende acties uit:

> [!div class="checklist"]
> * Een virtuele machine (VM) in het lab claimen
> * Verbinding maken met de virtuele machine
> * De claim op de virtuele machine ongedaan maken

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="access-the-lab"></a>Toegang tot het lab

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer **Alle resources** in het menu aan de linkerkant. 
3. Selecteer **DevTest Labs** voor het resourcetype. 
4. Selecteer het lab. 

    ![Het lab selecteren](./media/tutorial-use-custom-lab/search-for-select-custom-lab.png)

## <a name="claim-a-vm"></a>Een virtuele machine claimen

1. Selecteer in de lijst met **Claimbare virtuele machines** **...** (beletselteken) en selecteer **Machine claimen**.

    ![Virtuele machine claimen](./media/tutorial-use-custom-lab/claim-virtual-machine.png)
1. Controleer of u de virtuele machine in de lijst **Mijn virtuele machines** ziet.

    ![Mijn virtuele machine](./media/tutorial-use-custom-lab/my-virtual-machines.png)

## <a name="connect-to-the-vm"></a>Verbinding maken met de virtuele machine

1. Selecteer de VM in de lijst. U ziet de **pagina Virtuele Machine** voor uw virtuele machine. Selecteer **Verbinding maken** op de werkbalk.

    ![Verbinding maken met de virtuele machine](./media/tutorial-use-custom-lab/connect-button.png)
2. Sla het gedownloade **RDP**-bestand van de harde schijf op en gebruik het om verbinding te maken met de virtuele machine. Geef de gebruikersnaam en het wachtwoord op die u bij het maken van de VM in de vorige sectie hebt opgegeven. 

    Als u verbinding wilt maken met een Linux-VM, moet SSH- en/of RDP-toegang worden ingeschakeld voor de virtuele machine. Zie, [Extern bureaublad installeren en configureren om verbinding te maken met een virtuele Linux-machine in Azure](../virtual-machines/linux/use-remote-desktop.md) voor stappen voor het maken van verbinding met een Linux-VM via RDP. 

    > [!NOTE]
    > U kunt de pagina Virtuele machine voor uw VM ook op andere manieren bereiken. Hier volgen een paar van deze manieren: 
    > 
    > 1. Zoek naar alle VM's in uw abonnement. Selecteer uw VM in de lijst met VM's om de pagina **Virtuele machine** te openen.
    > 2. Ga naar de pagina **Resourcegroep** voor de resourcegroep. Selecteer vervolgens uw VM in de lijst met resources in de resourcegroep om naar de pagina **Virtuele machine** te gaan. 
    >
    > Gebruik niet de werkbalkknop **Verbinding maken** op de pagina **Virtuele machine** die u opent met deze opties. Ga in plaats daarvan naar de pagina **Virtuele machine** vanuit de pagina **DevTest Labs**, zoals in dit artikel wordt weergegeven en gebruik nu de knop **Verbinding maken** op de werkbalk.


## <a name="unclaim-the-vm"></a>De claim op de virtuele machine ongedaan maken
Nadat u klaar bent met het gebruik van de virtuele machine, kunt u de claim op de virtuele machine ongedaan maken door de volgende stappen te volgen: 

1. Ga naar de pagina van de virtuele machine en selecteer **Claim ongedaan maken** op de werkbalk. 

    ![Claim op virtuele machine ongedaan maken](./media/tutorial-use-custom-lab/unclaim-vm-menu.png)
1. De virtuele machine wordt uitgeschakeld voordat de claim ongedaan wordt gemaakt. U kunt de status van deze bewerking zien in meldingen.  
3. Ga terug naar de DevTest Lab-pagina door in het breadcrumb-menu bovenaan op de naam van uw lab te klikken. 
    
    ![Terug navigeren naar lab](./media/tutorial-use-custom-lab/breadcrumb-to-lab.png)
1. Controleer of de virtuele machine wordt weergegeven in de lijst met **Claimbare virtuele machines** onderaan het scherm.

    
## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u gezien hoe u toegang kunt krijgen tot en gebruik kunt maken van een lab dat is gemaakt met Azure DevTest Labs. Voor meer informatie over toegang tot en het gebruik van virtuele machines in een lab raadpleegt u 

> [!div class="nextstepaction"]
> [Procedure: Virtuele machines gebruiken in een lab](devtest-lab-add-vm.md)


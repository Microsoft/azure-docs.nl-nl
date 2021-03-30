---
title: Een lab met Azure DevTest Labs maken | Microsoft Docs
description: In deze zelfstudie maakt u een lab in Azure DevTest Labs met behulp van de Microsoft Azure-portal. Een labbeheerder stelt een lab in, maakt virtuele machines in het lab en configureert beleidsregels.
ms.topic: tutorial
ms.date: 06/26/2020
ms.openlocfilehash: 4b59edd4ab1204d6683cf95a02070d10b1abe061
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91324277"
---
# <a name="tutorial-set-up-a-lab-by-using-azure-devtest-labs"></a>Zelfstudie: Een lab maken met Azure DevTest Labs
In deze zelfstudie maakt u een lab met Azure Portal. Een labbeheerder stelt in een organisatie een lab op, maakt virtuele machines in het lab en configureert beleidsregels. Labgebruikers (bijvoorbeeld ontwikkelaars en testers) claimen virtuele machines in het lab, maken hier verbinding mee en gebruiken deze. 

In deze zelfstudie voert u de volgende acties uit:

> [!div class="checklist"]
> * Een lab maken
> * Virtuele machines (VM) toevoegen aan het lab
> * Een gebruiker toevoegen aan de rol Labgebruiker

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="create-a-lab"></a>Een lab maken
In de volgende stappen ziet u hoe u Azure Portal kunt gebruiken om een lab te maken in Azure DevTest Labs. 

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer in het hoofdmenu aan de linkerkant **Een resource maken** (bovenaan de lijst), wijs **Ontwikkelhulpprogramma's** aan en klik op **DevTest Labs**. 

    ![Menu Nieuw DevTest Lab](./media/tutorial-create-custom-lab/new-custom-lab-menu.png)
1. Voer in het venster **Een DevTest Lab maken** de volgende acties uit: 
    1. Voer bij **labnaam** een naam in voor het lab. 
    2. Selecteer bij **Abonnement** het abonnement waarin u het lab wilt maken. 
    3. Selecteer voor **Resourcegroep** de optie **Nieuwe maken** en voer een naam in voor de resourcegroep. 
    4. Selecteer bij **Locatie** een locatie/regio waarin het lab moet worden gemaakt. 
    5. Selecteer **Maken**. 
    6. Selecteer **Vastmaken aan dashboard**. Nadat u het lab hebt gemaakt, wordt het lab weergegeven in het dashboard. 

        ![De sectie Een lab maken van DevTest Labs](./media/tutorial-create-custom-lab/create-custom-lab-blade.png)
2. Controleer of het lab is gemaakt door de meldingen te bekijken. Selecteer **Ga naar resource**.  

    ![Melding](./media/tutorial-create-custom-lab/creation-notification.png)
3. Controleer of de pagina **DevTest Lab** voor uw lab wordt weergegeven. 

    ![Startpagina voor uw lab](./media/tutorial-create-custom-lab/lab-home-page.png)

## <a name="add-a-vm-to-the-lab"></a>Een virtuele machine aan een lab toevoegen

1. Selecteer op de pagina **DevTest Lab** **+ toevoegen** op de werkbalk. 

    ![Knop Toevoegen](./media/tutorial-create-custom-lab/add-vm-to-lab-button.png)
1. Zoek op de pagina **Een basis kiezen** met behulp van een trefwoord (bijvoorbeeld Windows, Ubuntu) en selecteer een van de basisimages in de lijst. 
1. Voer op de pagina van de **virtuele machine** de volgende acties uit: 
    1. Voer bij **Naam virtuele machine** een naam in voor de virtuele machine. 
    2. Voer bij **Gebruikersnaam** een naam in voor de gebruiker die toegang heeft tot de virtuele machine. 
    3. Voer bij **Wachtwoord** het wachtwoord voor de gebruiker in. 

        ![Schermafbeelding van de basisinstellingen voor de pagina "Lab-resource maken".](./media/tutorial-create-custom-lab/new-virtual-machine.png)
1. Selecteer het tabblad **Geavanceerde instellingen**.
    1. Selecteer bij **Instellen dat deze machine kan worden geclaimd** **Ja**.
    2. Controleer of het **aantal exemplaren** is ingesteld op **1**. Als u deze instelt op **2**, worden 2 virtuele machines gemaakt met de namen: `<base image name>00' and <base image name>01`. Bijvoorbeeld: `win10vm00` en `win10vm01`.     
    3. Selecteer **Indienen**. 

        ![Een basis kiezen](./media/tutorial-create-custom-lab/new-vm-advanced-settings.png)
    9. U ziet de status van de virtuele machine in de lijst met **Claimbare virtuele machines**. Het maken van de virtuele machine duurt ongeveer 25 minuten. De virtuele machine wordt gemaakt in een afzonderlijke Azure-resourcegroep, waarvan de naam begint met de naam van de huidige resourcegroep die aan het lab is gekoppeld. Bijvoorbeeld, als het lab zich in `labrg` bevindt, kan de virtuele machine worden gemaakt in de resourcegroep `labrg3988722144002`. 

        ![Status van het maken van de virtuele machine](./media/tutorial-create-custom-lab/vm-creation-status.png)
1. Nadat de virtuele machine is gemaakt, ziet u deze in de lijst met **Claimbare virtuele machines**. 

    > [!NOTE] 
    > Op de pagina **Geavanceerde instellingen** kunt u een openbaar, persoonlijk of een gedeeld IP-adres configureren voor de virtuele machine. Wanneer de **gedeelde IP** is ingeschakeld, activeert Azure DevTest Labs automatisch RDP voor virtuele Windows-machines en SSH voor virtuele Linux-machines. Als u virtuele machines met **openbare IP**-adressen maakt, worden RDP en SSH ingeschakeld zonder wijzigingen van DevTest Labs.  

## <a name="add-a-user-to-the-lab-user-role"></a>Een gebruiker toevoegen aan de rol Labgebruiker

1. Selecteer **Configuratie en beleid** in het menu links. 

    ![Configuratie en beleid](./media/tutorial-create-custom-lab/configuration-and-policies-menu.png)
1. Selecteer **Toegangsbeheer (IAM)** in het menu en selecteer **+ Roltoewijzing toevoegen** in de werkbalk. 

    ![Roltoewijzing toevoegen - knop](./media/tutorial-create-custom-lab/add-role-assignment-button.png)
1. Voer op de pagina **Machtigingen toevoegen** de volgende acties uit:
    1. Selecteer bij **Rol** de optie **DevTest Labs-gebruiker**. 
    2. Selecteer de **gebruiker** die u wilt toevoegen. 
    3. Selecteer **Opslaan**.

        ![Gebruiker toevoegen](./media/tutorial-create-custom-lab/add-user.png)

## <a name="clean-up-resources"></a>Resources opschonen
In de volgende zelfstudie ziet u hoe een labgebruiker een virtuele machine kan claimen en verbinding kan maken met een virtuele machine in het lab. Als u deze zelfstudie niet wilt volgen, en de resources die zijn gemaakt als onderdeel van deze zelfstudie wilt opschonen, voert u de volgende stappen uit: 

1. Selecteer in Azure Portal **Resourcegroepen** in het menu. 

    ![Resourcegroepen](./media/tutorial-create-custom-lab/resource-groups.png)
1. Selecteer de resourcegroep waarin u het lab hebt gemaakt. 
1. Selecteer **Resourcegroep verwijderen** uit de taakbalk. Als u een resourcegroep verwijdert, worden alle resources in de resourcegroep inclusief het lab verwijderd. 

    ![Lab-resourcegroep](./media/tutorial-create-custom-lab/lab-resource-group.png)
1. Herhaal deze stappen voor het verwijderen van de aanvullende resourcegroep die voor u is gemaakt met de naam `<your resource group name><random numbers>`. Bijvoorbeeld: `splab3988722144001`. De virtuele machines worden gemaakt in deze resourcegroep in plaats van in de resourcegroep waarin het lab bestaat. 

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u een lab gemaakt met een virtuele machine en hebt u een gebruiker toegang gegeven tot het lab. Voor meer informatie over toegang tot het lab als labgebruiker gaat u door naar de volgende zelfstudie:

> [!div class="nextstepaction"]
> [Zelfstudie: Toegang tot het lab](tutorial-use-custom-lab.md)


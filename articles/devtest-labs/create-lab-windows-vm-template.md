---
title: Een lab maken met Azure DevTest Labs en een Azure Resource Manager-sjabloon
description: In deze quickstart maakt u een lab in Azure DevTest Labs met behulp van een Azure Resource Manager-sjabloon (ARM-sjabloon). Een labbeheerder stelt een lab in, maakt virtuele machines in het lab en configureert beleidsregels.
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 06/26/2020
ms.openlocfilehash: 2b825b4d4485f401199556b6faaef0017f583cc1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91461186"
---
# <a name="quickstart-set-up-a-lab-by-using-azure-devtest-labs-arm-template"></a>Quickstart: Een lab instellen met een ARM-sjabloon van Azure DevTest Labs
In deze snelstart maakt u een lab met een VM met Windows Server 2019 Datacenter met behulp van een Azure Resource Manager-sjabloon (ARM). 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

In deze quickstart voert u de volgende acties uit:

> [!div class="checklist"]
> * De sjabloon controleren 
> * De sjabloon implementeren
> * De sjabloon controleren
> * Resources opruimen

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-dtl-create-lab-windows-vm%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/101-dtl-create-lab-windows-vm/).

:::code language="json" source="~/quickstart-templates/101-dtl-create-lab-windows-vm/azuredeploy.json":::

Onder meer de volgende resources worden in de sjabloon gedefinieerd:

- [**Microsoft.DevTestLab/labs**](/azure/templates/microsoft.devtestlab/labs)
- [**Microsoft.DevTestLab labs/virtualnetworks**](/azure/templates/microsoft.devtestlab/labs/virtualnetworks)
- [**Microsoft.DevTestLab labs/virtualmachines**](/azure/templates/microsoft.devtestlab/labs/virtualmachines)

Zie [Azure-snelstartsjablonen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devtestlab) voor meer informatie over sjabloonvoorbeelden.

## <a name="deploy-the-template"></a>De sjabloon implementeren
Klik op de volgende knop om de implementatie automatisch uit te voeren. 

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-dtl-create-lab-windows-vm%2Fazuredeploy.json)

1. Maak een **nieuwe resourcegroep** zodat het later gemakkelijk op te schonen is.
1. Selecteer een **locatie** voor de resourcegroep. 
1. Voer een **naam voor het lab** in. 
1. Voer een **naam voor het lab** in. 
1. Voer een **gebruikersnaam** in die toegang heeft tot de virtuele machine. 
1. Voer het **wachtwoord** voor de gebruiker in. 
1. Selecteer **Ik ga akkoord met de bovenstaande voorwaarden**. 
1. Selecteer vervolgens **Aankoop**.

    :::image type="content" source="./media/create-lab-windows-vm-template/deploy-template-page.png" alt-text="Pagina Sjabloon implementeren":::

## <a name="validate-the-deployment"></a>De implementatie valideren
1. Selecteer **Meldingen** boven aan de pagina om de implementatiestatus en klik op de koppeling **De implementatie wordt uitgevoerd**.

    :::image type="content" source="./media/create-lab-windows-vm-template/deployment-notification.png" alt-text="Implementatiemelding":::
2. Wacht op de pagina **Implementatie - Overzicht** tot de implementatie is voltooid. Deze bewerking (vooral een VM maken) duurt even. Selecteer vervolgens **Naar resourcegroep gaan** of de **naam van de resourcegroep** zoals te zien is op de volgende afbeelding: 

    :::image type="content" source="./media/create-lab-windows-vm-template/navigate-resource-group.png" alt-text="Navigeren naar resourcegroep":::
3. Op de pagina **Resourcegroep** ziet u de lijst met resources in de resourcegroep. Bevestig dat u uw lab of type ziet:`DevTest Lab` in de resources. U ziet ook de afhankelijke resources, zoals het virtuele netwerk en de virtuele machine in de resourcegroep. 

    :::image type="content" source="./media/create-lab-windows-vm-template/resource-group-home-page.png" alt-text="Startpagina van de resourcegroep":::
4. Selecteer uw lab in de lijst met resources om de startpagina voor uw lab weer te geven. Controleer of de VM met Windows Server 2019 Data Center wordt weergeven in de lijst **Mijn virtuele machines**. In de volgende afbeelding is het onderdeel **Essentials** geminimaliseerd. 

    :::image type="content" source="./media/create-lab-windows-vm-template/lab-home-page.png" alt-text="Startpagina voor het lab":::

    > [!IMPORTANT] 
    > Houd deze pagina open en volg de instructies in het volgende onderdeel om resources op te schonen en zo kostenvoor de uitvoering van het lab en de VM in Azure te vermijden. Als u verder wilt gaan naar de volgende zelfstudie om de toegang tot n de VM in het lab te testen, schoon dan resources op na afloop van die zelfstudie. 

## <a name="clean-up-resources"></a>Resources opschonen

1. Verwijder eerst het lab zodat u de resourcegroep kunt verwijderen. U kunt de resourcegroep niet verwijderen als deze een lab bevat. Selecteer **Verwijderen** in de taakbalk om het lab te verwijderen. 

    :::image type="content" source="./media/create-lab-windows-vm-template/delete-lab-button.png" alt-text="Knop Lab verwijderen":::
 2. Typ op de bevestigingspagina de **labnaam** en selecteer **Verwijderen**. 
 3. Wacht tot het lab is verwijderd. Selecteer het pictogram van een **bel** om de melding over de verwijderbewerking weer te geven. Dit proces duurt enige tijd. Bevestig het verwijderen van het lab en selecteer vervolgens de **resourcegroep** in het koppelingenmenu. 
 
    :::image type="content" source="./media/create-lab-windows-vm-template/confirm-lab-deletion.png" alt-text="Verwijdering van VM in meldingen bevestigen":::
 1. Selecteer **Resourcegroep verwijderen** in de werkbalk op de pagina **Resourcegroep**. Typ op de bevestigingspagina de **naam van de resourcegroep** en selecteer **Verwijderen**. Controleer de meldingen om te bevestigen dat de resourcegroep is verwijderd.
 
    :::image type="content" source="./media/create-lab-windows-vm-template/delete-resource-group-button.png" alt-text="Knop Resourcegroep verwijderen":::

## <a name="next-steps"></a>Volgende stappen
In deze quickstart hebt u een lab gemaakt met een VM. Voor meer informatie over toegang tot het lab gaat u door naar de volgende zelfstudie:

> [!div class="nextstepaction"]
> [Zelfstudie: Toegang tot het lab](tutorial-use-custom-lab.md)

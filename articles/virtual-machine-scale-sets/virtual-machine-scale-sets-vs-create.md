---
title: Een Schaalset voor virtuele machines implementeren met behulp van Visual Studio
description: Virtual Machine Scale Sets implementeren met behulp van Visual Studio en een resource manager-sjabloon
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: visual-studio
ms.date: 09/09/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 2db281f1b3278e37deca8486971a7f7a83f85ccf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86503078"
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-visual-studio"></a>Een Schaalset voor virtuele machines maken met Visual Studio

In dit artikel wordt beschreven hoe u een Schaalset voor virtuele machines van Azure implementeert met behulp van de implementatie van een Visual Studio-resource groep.

[Azure virtual machine Scale sets](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) is een Azure Compute-resource voor het implementeren en beheren van een verzameling vergelijk bare virtuele machines met automatisch schalen en taak verdeling. U kunt Virtual Machine Scale Sets inrichten en implementeren met behulp van [Azure Resource Manager sjablonen](https://github.com/Azure/azure-quickstart-templates). Azure Resource Manager sjablonen kunnen worden geïmplementeerd met behulp van Azure CLI, Power shell, REST en direct vanuit Visual Studio. Visual Studio biedt een aantal voorbeeld sjablonen die u kunt implementeren als onderdeel van een implementatie project van een Azure-resource groep.

Implementaties van Azure-resource groepen vormen een manier om een set verwante Azure-resources te groeperen en te publiceren in één implementatie bewerking. Zie voor meer informatie [Azure-resource groepen maken en implementeren via Visual Studio](../azure-resource-manager/templates/create-visual-studio-deployment-project.md).

## <a name="prerequisites"></a>Vereisten

U hebt de volgende vereisten nodig om aan de slag te gaan met de implementatie van Virtual Machine Scale Sets in Visual Studio:

* Visual Studio 2013 of later
* Azure SDK 2,7, 2,8 of 2,9

>[!NOTE]
>In dit artikel wordt gebruikgemaakt van Visual Studio 2019 met [Azure SDK 2,8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## <a name="create-a-project"></a>Een project maken <a name="creating-a-project"></a> 

1. Open Visual Studio en selecteer **Een nieuw project maken**.

1. Kies in **een nieuw project maken de** optie **Azure-resource groep** voor C# en selecteer vervolgens **volgende**.

1. Voer in **uw nieuwe project configureren** een naam in en selecteer **maken**.

    ![Een naam en een project maken](media/virtual-machine-scale-sets-vs-create/configure-azure-resource-group.png)

1. Kies in de lijst met sjablonen ofwel de Windows-schaalset voor **virtuele machines** of virtuele- **machine schaal sets voor Linux** . Selecteer **OK**.

   ![Een virtuele-machine sjabloon selecteren](media/virtual-machine-scale-sets-vs-create/select-vm-template.png)

Nadat u het project hebt gemaakt, bevat **Solution Explorer** een Power shell-implementatie script, een Azure Resource Manager sjabloon en een parameter bestand voor de schaalset van de virtuele machine.

## <a name="customize-your-project"></a>Uw project aanpassen

Nu kunt u de sjabloon bewerken om deze aan te passen aan de behoeften van uw toepassing. U kunt eigenschappen van virtuele-machine uitbreiding toevoegen of regels voor taak verdeling bewerken. Standaard zijn de sjablonen voor de Schaalset voor virtuele machines geconfigureerd voor het implementeren van de **AzureDiagnostics** -extensie, waardoor het gemakkelijk is om regels voor automatisch schalen toe te voegen. De sjablonen implementeren ook een load balancer met een openbaar IP-adres, geconfigureerd met binnenkomende NAT-regels.

Met de load balancer kunt u verbinding maken met de virtuele-machine-instanties met SSH (Linux) of RDP (Windows). Het poort bereik van de front-end begint bij 50000. Als u voor Linux SSH naar poort 50000, stuurt taak verdeling u naar poort 22 van de eerste virtuele machine in de Schaalset. Verbinding maken met poort 50001 wordt doorgestuurd naar poort 22 van de tweede virtuele machine, enzovoort.

 Een goede manier om uw sjablonen te bewerken met Visual Studio is door de **JSON-overzicht** te gebruiken. U kunt de para meters, variabelen en resources ordenen. Met een goed idee van het schema kan Visual Studio fouten in uw sjabloon verwijzen voordat u deze implementeert.

![JSON-Verkenner](media/virtual-machine-scale-sets-vs-create/json-explorer.png)

## <a name="deploy-the-project"></a>Het project implementeren

Implementeer de Azure Resource Manager sjabloon om de resource voor de Schaalset voor virtuele machines te maken:

1. Klik in **Solution Explorer** met de rechter muisknop op het project en kies  >  **nieuwe** implementeren.

    ![Uw project implementeren](media/virtual-machine-scale-sets-vs-create/deploy-new-project.png)

1. Kies in de **resource groep implementeren** de optie welk abonnement u wilt gebruiken en selecteer een resource groep. U kunt, indien nodig, een resource groep maken.

1. Selecteer vervolgens **para meters bewerken** om para meters in te voeren die worden door gegeven aan uw sjabloon.

   ![Abonnement en resource groep invoeren](media/virtual-machine-scale-sets-vs-create/deploy-to-resource-group.png)

1. Geef de gebruikers naam en het wacht woord voor het besturings systeem op. Deze waarden zijn vereist om de implementatie te maken. Als u geen Power shell-Hulpprogram Ma's voor Visual Studio hebt geïnstalleerd, selecteert u **wacht woorden opslaan** om te voor komen dat een Power shell-opdracht prompt wordt verborgen of [Key Vault ondersteuning](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/)te gebruiken. Selecteer **Opslaan** om door te gaan.

    ![Implementatie parameters bewerken](media/virtual-machine-scale-sets-vs-create/edit-deployment-parameters.png)

1. Selecteer **implementeren** in **resource groep implementeren**. De actie voert het **Deploy-AzureResourceGroup.ps1** script uit. In het **uitvoer** venster wordt de voortgang van de implementatie weer gegeven.

   ![Uitvoer toont resultaten](media/virtual-machine-scale-sets-vs-create/deployment-output.png)

## <a name="explore-your-virtual-machine-scale-set"></a>Uw Schaalset voor virtuele machines verkennen <a name="exploring-your-virtual-machine-scale-set"></a>

Selecteer   >  **Cloud Verkenner** weer geven om de nieuwe schaalset voor virtuele machines weer te geven. Gebruik **Alles vernieuwen**, indien nodig.

![Cloud Explorer](media/virtual-machine-scale-sets-vs-create/cloud-explorer.png)

Met **Cloud Explorer** kunt u Azure-resources beheren in Visual Studio tijdens het ontwikkelen van toepassingen. U kunt de Schaalset voor virtuele machines ook weer geven in de [Azure Portal](https://portal.azure.com) en [Azure resource Explorer](https://resources.azure.com/).

 De portal biedt de beste manier om uw Azure-infra structuur te beheren met een webbrowser. Azure Resource Explorer biedt een eenvoudige manier om Azure-resources te verkennen en fouten op te sporen. Azure Resource Explorer biedt de instantie weergave en toont ook Power shell-opdrachten voor de resources die u bekijkt.

## <a name="next-steps"></a>Volgende stappen

Zodra u Virtual Machine Scale Sets met Visual Studio hebt geïmplementeerd, kunt u uw project verder aanpassen aan de vereisten van uw toepassing. Configureer bijvoorbeeld automatisch schalen door een **Insights** -resource toe te voegen. U kunt een infra structuur toevoegen aan uw sjabloon, zoals zelfstandige virtuele machines, of toepassingen implementeren met behulp van de aangepaste script extensie. Goede voorbeeld sjablonen vindt u in de GitHub-opslag plaats van de [Azure Quick](https://github.com/Azure/azure-quickstart-templates) start-sjablonen. Zoek naar `vmss`.

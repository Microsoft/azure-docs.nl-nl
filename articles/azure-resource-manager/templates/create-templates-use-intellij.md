---
title: Sjabloon implementeren-IntelliJ idee
description: Leer hoe u uw eerste Azure Resource Manager-sjabloon maakt (ARM-sjabloon) met behulp van het IntelliJ-idee en hoe u dit implementeert.
ms.devlang: java
ms.date: 08/01/2019
ms.topic: conceptual
ms.custom: devx-track-java
ms.openlocfilehash: f0e007fe8930b30cee1c95159d7e964e3792449f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96905958"
---
# <a name="create-and-deploy-arm-templates-by-using-the-intellij-idea"></a>ARM-sjablonen maken en implementeren met behulp van het IntelliJ-idee

Meer informatie over het implementeren van een Azure Resource Manager sjabloon (ARM-sjabloon) naar Azure met behulp van het IntelliJ-idee en het proces voor het bewerken en bijwerken van de sjabloon rechtstreeks vanuit de IDE. ARM-sjablonen zijn JSON-bestanden waarmee de resources worden gedefinieerd die u voor uw oplossing moet implementeren. Zie het [overzicht van sjabloon implementatie](overview.md)voor meer informatie over de concepten van het implementeren en beheren van uw Azure-oplossingen.

![Portal diagram van Resource Manager-sjabloon](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-export-deploy-template-portal.png)

Na het voltooien van de zelfstudie implementeert u een Azure Storage-account. Hetzelfde proces kan worden gebruikt voor het implementeren van andere Azure-resources.

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt voltooien, hebt u het volgende nodig:

* Een [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) Ultimate Edition of Community Edition geïnstalleerd
* [Azure-toolkit voor IntelliJ](https://plugins.jetbrains.com/plugin/8053) moet zijn geïnstalleerd. Raadpleeg de [beheerhandleiding voor IntelliJ-invoegtoepassingen](https://www.jetbrains.com/help/idea/managing-plugins.html) voor meer informatie
* U moet ook [zijn aangemeld](/azure/developer/java/toolkit-for-intellij/sign-in-instructions) bij uw Azure-account voor de Azure-toolkit voor IntelliJ

## <a name="deploy-a-quickstart-template"></a>Een Quick Start-sjabloon implementeren

In plaats van een sjabloon helemaal opnieuw te maken, opent u een sjabloon in [Azure-snelstartsjablonen](https://azure.microsoft.com/resources/templates/). Snelstartsjablonen voor Azure is een opslagplaats voor ARM-sjablonen. De sjabloon die in dit artikel wordt gebruikt, wordt [een standaard-opslag account maken](https://github.com/Azure/azure-quickstart-templates/tree/master/101-storage-account-create/)genoemd. Hiermee wordt een Azure Storage-account resource gedefinieerd.

1. Klik met de rechter muisknop en sla de [`azuredeploy.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) en [`azuredeploy.parameters.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json) op uw lokale computer op.

1. Als uw Azure-Toolkit correct is geïnstalleerd en aangemeld, ziet u Azure Explorer in de zijbalk van uw IntelliJ-idee. Klik met de rechter muisknop op het **resource beheer** en selecteer **implementatie maken**.

    ![Resource Manager-sjabloon met de rechter muisknop om implementatie te maken](./media/create-templates-use-intellij/resource-manager-create-deployment-right-click.png)

1. Configuratie van de **implementatie naam**, het **abonnement**, de **resource groep** en de **regio**. Hier implementeren we de sjabloon in een nieuwe resource groep `testRG` . Selecteer vervolgens pad voor **resource sjabloon** als `azuredeploy.json` en **resource parameters** tijdens `azuredeploy.parameters.json` het downloaden.

    ![Resource Manager-sjabloon bestanden selecteren om implementatie te maken](./media/create-templates-use-intellij/resource-manager-create-deployment-select-files.png)

1. Nadat u op OK hebt geklikt, wordt de implementatie gestart. Totdat de implementatie is voltooid, kunt u de voortgang van de **status balk** van het INTELLIJ-idee aan de onderkant vinden.

    ![Implementatie status van Resource Manager-sjabloon](./media/create-templates-use-intellij/resource-manager-create-deployment-status.png)

## <a name="browse-an-existing-deployment"></a>Door een bestaande implementatie bladeren

1. Nadat de implementatie is voltooid, ziet u de nieuwe resource groep `testRG` en een nieuwe implementatie die is gemaakt. Klik met de rechter muisknop op de implementatie om een lijst met mogelijke acties weer te geven. Selecteer nu **Eigenschappen weer geven**.

    ![Resource Manager-sjabloon bladeren implementatie](./media/create-templates-use-intellij/resource-manager-deployment-browse.png)

1. Er wordt een tabblad weergave geopend om enkele nuttige eigenschappen, zoals de implementatie status en sjabloon structuur, weer te geven.

    ![Resource Manager-sjabloon implementatie-eigenschappen weer geven](./media/create-templates-use-intellij/resource-manager-deployment-show-properties.png)

## <a name="edit-and-update-an-existing-deployment"></a>Een bestaande implementatie bewerken en bijwerken

1. Selecteer **implementatie bewerken** in het menu met de rechter muisknop of de weer gave eigenschappen weer geven voor. Er wordt nog een andere tabblad weergave geopend, met daarin de sjabloon en de parameter bestanden voor de implementatie op Azure. Als u deze bestanden op een lokale locatie wilt opslaan, kunt u klikken op **sjabloon bestand exporteren**  of **parameter bestanden exporteren**.

    ![Resource Manager-sjabloon implementatie bewerken](./media/create-templates-use-intellij/resource-manager-edit-deployment.png)

1. U kunt de twee bestanden op deze pagina bewerken en de wijzigingen in azure implementeren. Hier wijzigen we de waarde van **storageAccountType** in parameter bestanden van `Standard_LRS` tot `Standard_GRS` . Klik op **Update-implementatie** onder en bevestig de update.

    ![Scherm afbeelding toont de Resource Manager-sjabloon met de prompt update-implementatie weer gegeven.](./media/create-templates-use-intellij/resource-manager-edit-deployment-update.png)

1. Nadat de update-implementatie is voltooid, kunt u controleren op de portal waar het gemaakte opslag account is gewijzigd `Standard_GRS` .

## <a name="clean-up-resources"></a>Resources opschonen

1. Schoon de geïmplementeerd Azure-resources, wanneer u deze niet meer nodig hebt, op door de resourcegroep te verwijderen. U kunt dit doen vanuit Azure Portal of Azure CLI. In azure Verkenner van IntelliJ idee met de rechter muisknop op de gemaakte **resource groep** en selecteer verwijderen.

    ![De resource groep in azure Verkenner verwijderen uit het IntelliJ-idee](./media/create-templates-use-intellij/delete-resource-group.png)

> [!NOTE]
> U ziet dat bij het verwijderen van een implementatie geen resources worden verwijderd die zijn gemaakt met de implementatie. Verwijder de bijbehorende resource groep of specifieke resources als u deze niet meer nodig hebt.

## <a name="next-steps"></a>Volgende stappen

De belangrijkste focus van dit artikel is het gebruik van IntelliJ-idee voor het implementeren van een bestaande sjabloon vanuit Azure Quick Start-sjablonen. U hebt ook geleerd hoe u een bestaande implementatie op Azure weergeeft en bijwerkt. De sjablonen van de Azure-snelstartsjablonen voldoen mogelijk niet volledig aan uw behoeften. Zie voor meer informatie over het ontwikkelen van sjablonen onze nieuwe zelfstudiereeks voor beginners:

> [!div class="nextstepaction"]
> [Zelfstudies voor beginners](./template-tutorial-create-first-template.md)

> [!div class="nextstepaction"]
> [Ga naar java in het ontwikkel centrum van Azure](/azure/java)

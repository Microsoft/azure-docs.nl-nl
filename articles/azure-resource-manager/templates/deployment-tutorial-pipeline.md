---
title: Continue integratie met Azure-pijplijnen
description: Meer informatie over het continu bouwen, testen en implementeren van ARM-sjablonen (Azure Resource Manager).
ms.date: 03/02/2021
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 3ff98c1c033c6da4b6bdf40c3b8ecb3347601741
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101722795"
---
# <a name="tutorial-continuous-integration-of-arm-templates-with-azure-pipelines"></a>Zelfstudie: Continue integratie van ARM-sjablonen met Azure Pipelines

In de [vorige zelfstudie](./deployment-tutorial-linked-template.md) hebt u een gekoppelde sjabloon geïmplementeerd.  In deze zelfstudie leert u hoe u Azure Pipelines kunt gebruiken om voortdurend ARM-sjabloonprojecten (Azure Resource Manager) te bouwen en implementeren.

Azure DevOps biedt ontwikkelaars services om teams te ondersteunen bij het plannen van werk, het samenwerken aan de ontwikkeling van code en het bouwen en implementeren van toepassingen. Ontwikkelaars kunnen werken in de cloud met behulp van Azure DevOps-services. Azure DevOps biedt een geïntegreerde set functies die u via uw webbrowser of IDE-client kunt openen. Azure-pijplijnen is een van deze functies. Azure-pijplijnen is een volledig uitgeruste service voor continue integratie (CI) en continue levering (CD). Het werkt met uw favoriete Git-provider en kan worden geïmplementeerd in de meeste belangrijke cloudservices. Vervolgens kunt u het bouwen, testen en implementeren van uw code automatiseren naar Microsoft Azure, Google Cloud Platform of Amazon Web Services.

> [!NOTE]
> Kies een projectnaam. Wanneer u de zelfstudie doorloopt, vervangt u **AzureRmPipeline** overal door de naam van uw project.
> Deze projectnaam wordt gebruikt om resourcenamen te genereren.  Een van de resources is een opslagaccount. Opslagaccountnamen moeten tussen 3 en 24 tekens lang zijn en mogen alleen getallen en kleine letters bevatten. De naam moet uniek zijn. De opslagaccountnaam in de sjabloon is de projectnaam met achteraan toegevoegd: **store**. De projectnaam moet tussen 3 en 11 tekens lang zijn. De projectnaam moet dus voldoen aan de vereisten voor de opslagaccountnaam en minder dan 11 tekens lang zijn.

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Een GitHub-opslagplaats voorbereiden
> * Een Azure DevOps-project maken
> * Een Azure-pijplijn maken
> * De implementatie van de pijplijn controleren
> * De sjabloon bijwerken en opnieuw implementeren
> * Resources opschonen

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt voltooien, hebt u het volgende nodig:

* **Een GitHub-account**, dat u gebruikt om een opslagplaats voor uw sjablonen te maken. Als u nog geen account hebt, kunt u [gratis een account maken](https://github.com). Zie [GitHub-opslagplaatsen maken](/azure/devops/pipelines/repos/github) voor meer informatie over het gebruik van GitHub-opslagplaatsen.
* **Installeer Git**. In deze zelfstudie-instructie gebruikt u *Git Bash* of *Git Shell*. Zie [Install Git](https://www.atlassian.com/git/tutorials/install-git) (Engelstalig) voor instructies.
* **Een Azure DevOps-organisatie**. Als u nog geen account hebt, kunt u gratis een account maken. Zie [Een organisatie-of projectverzameling maken](/azure/devops/organizations/accounts/create-organization).
* (optioneel) **Visual Studio Code met de extensie Resource Manager Tools**. Zie [Quickstart: ARM-sjablonen maken met Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).

## <a name="prepare-a-github-repository"></a>Een GitHub-opslagplaats voorbereiden

GitHub wordt gebruikt voor het opslaan van de broncode van uw project, inclusief Resource Manager-sjablonen. Zie [opslagplaatsen die worden ondersteund door Azure DevOps](/azure/devops/pipelines/repos/) voor andere ondersteunde opslagplaatsen.

### <a name="create-a-github-repository"></a>Een GitHub-opslagplaats maken

Als u geen GitHub-account hebt, raadpleegt u [Vereisten](#prerequisites).

1. Meld u aan bij [GitHub](https://github.com).
1. Selecteer uw accountafbeelding in de rechterbovenhoek en selecteer vervolgens **Your repositories** (Uw opslagplaatsen).

    ![Azure Resource Manager - Azure DevOps - Azure Pipelines - GitHub-opslagplaats maken](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-github-repository.png)

1. Selecteer **New** (Nieuw), een groene knop.
1. Geef een naam voor de opslagplaats op in **Repository name**.  Bijvoorbeeld: **AzureRmPipeline-repo**. Vergeet niet om **AzureRmPipeline** overal te vervangen door de naam van uw project. U kunt **Public** (Openbaar) of **Private** (Privé) selecteren voor deze zelfstudie. Selecteer vervolgens **Create repository** (Opslagplaats maken).
1. Noteer de URL. De URL van de opslagplaats heeft de volgende indeling: `https://github.com/[YourAccountName]/[YourRepositoryName]` .

Deze opslagplaats wordt een *externe opslagplaats* genoemd. Alle ontwikkelaars van hetzelfde project kunnen hun eigen *lokale opslagplaats* klonen en de wijzigingen samenvoegen in de externe opslagplaats.

### <a name="clone-the-remote-repository"></a>De externe opslagplaats klonen

1. Open Git Shell of Git Bash. Zie [Vereisten](#prerequisites).
1. Controleer of uw huidige map **GitHub** is.
1. Voer de volgende opdracht uit:

    ```bash
    git clone https://github.com/[YourAccountName]/[YourGitHubRepositoryName]
    cd [YourGitHubRepositoryName]
    mkdir CreateWebApp
    cd CreateWebApp
    pwd
    ```

    Vervang `[YourAccountName]` door de naam van uw GitHub-account en vervang `[YourGitHubRepositoryName]` door de naam van uw opslagplaats die u in de vorige procedure hebt gemaakt.

De map _CreateWebApp_ is de map waarin de sjabloon is opgeslagen. Met de opdracht `pwd` wordt het mappad weergegeven. In de volgende procedure slaat u de sjabloon op in het pad.

### <a name="download-a-quickstart-template"></a>Een quickstart-sjabloon downloaden

In plaats van de sjablonen te maken, kunt u de sjablonen downloaden en opslaan in de map _CreateWebApp_.

* De hoofdsjabloon: https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/azuredeploy.json
* De gekoppelde sjabloon: https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json

Zowel de mapnaam als de bestandsnamen worden gebruikt zoals ze zijn in de pijplijn. Als u deze namen wijzigt, moet u de namen die worden gebruikt in de pijplijn bijwerken.

### <a name="push-the-template-to-the-remote-repository"></a>De sjabloon naar de externe opslagplaats pushen

Het bestand _azuredeploy.json_ is toegevoegd aan de lokale opslagplaats. Vervolgens uploadt u de sjabloon naar de externe opslagplaats.

1. Open *Git Shell* of *Git Bash*, als u dat nog niet gedaan hebt.
1. Wijzig de map in de map _CreateWebApp_ in de lokale opslagplaats.
1. Controleer of het bestand _azuredeploy.json_ zich in de map bevindt.
1. Voer de volgende opdracht uit:

    ```bash
    git add .
    git commit -m "Add web app templates."
    git push origin main
    ```

    Er kan een waarschuwing over LF worden weergegeven. U kunt de waarschuwing negeren. **Hoofd** is de hoofdvertakking.  Doorgaans maakt u een branch (vertakking) voor elke update. Om de zelfstudie te vereenvoudigen gebruikt u de hoofdvertakking rechtstreeks.

1. Blader in een browser naar uw GitHub-opslagplaats. De URL is `https://github.com/[YourAccountName]/[YourGitHubRepository]`. De map _CreateWebApp_ en de twee bestanden in de map worden weer geven.
1. Selecteer _azuredeploy.jsin_ om de sjabloon te openen.
1. Selecteer de knop **Raw** (Onbewerkt). De URL begint met `https://raw.githubusercontent.com`.
1. Maak een kopie van de URL. U moet deze waarde opgeven wanneer u de pijplijn verderop in de zelfstudie configureert.

U hebt een GitHub-opslagplaats gemaakt en de sjablonen naar de opslagplaats geüpload.

## <a name="create-a-devops-project"></a>Een DevOps-project maken

Er is een DevOps-organisatie vereist voordat u kunt doorgaan met de volgende procedure. Als u er nog geen hebt, raadpleegt u [Vereisten](#prerequisites).

1. Meld u aan bij [Azure DevOps](https://dev.azure.com).
1. Selecteer een DevOps-organisatie aan de linkerkant.

    ![Azure Resource Manager - Azure DevOps - Azure Pipelines - Azure DevOps-project maken](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-create-devops-project.png)

1. Selecteer **Nieuw project**. Als u nog geen projecten hebt, wordt de pagina voor het maken van een project automatisch geopend.
1. Voer de volgende waarden in:

    * **Projectnaam**: voer een projectnaam in. U kunt de projectnaam gebruiken die u aan het begin van de zelfstudie hebt gekozen.
    * **Versiebeheer**: Selecteer **Git**. Mogelijk moet u **Geavanceerd** uitvouwen om **Versiebeheer** te kunnen weergeven.

    Gebruik de standaardwaarden voor de overige eigenschappen.
1. Selecteer **Maken**.

Maak een serviceverbinding die wordt gebruikt voor het implementeren van projecten in Azure.

1. Selecteer **Projectinstellingen** onder in het menu links.
1. Selecteer **Serviceverbindingen** onder **Pijplijnen**.
1. Selecteer **service verbinding maken**, selecteer **Azure Resource Manager** en selecteer vervolgens **volgende**.
1. Selecteer **Service-principal** en vervolgens **Volgende**.
1. Voer de volgende waarden in:

    * **Bereikniveau**: selecteer **Abonnement**.
    * **Abonnement**: selecteer uw abonnement.
    * **Resourcegroep**: Laat dit leeg.
    * **Verbindingsnaam**: voer een naam voor de verbinding in. Bijvoorbeeld: **AzureRmPipeline-conn**. Noteer deze naam. U hebt deze nodig bij het maken van de pijplijn.
    * **Verleen toegang tot alle pijplijnen**. (geselecteerd)
1. Selecteer **Opslaan**.

## <a name="create-a-pipeline"></a>Een pijplijn maken

U hebt nu de volgende taken voltooid.  Als u de vorige secties hebt overgeslagen omdat u bekend bent met GitHub en DevOps, moet u deze taken voltooien voordat u verdergaat.

* Maak een GitHub-opslagplaats en sla de sjablonen op in de map _CreateWebApp_ in de opslagplaats.
* Maak een DevOps-project en maak een Azure Resource Manager-serviceverbinding.

Een pijplijn maken met een stap voor het implementeren van een sjabloon:

1. Selecteer **Pijplijnen** in het menu aan de linkerkant.
1. Selecteer **pijp lijn maken**.
1. Selecteer **GitHub** op het tabblad **Verbinding maken**. Voer, als u daarom wordt gevraagd, uw GitHub-referenties in en volg de instructies. Als het volgende scherm wordt weergegeven, selecteert u **Alleen opslagplaatsen selecteren** en controleert u of uw opslagplaats in de lijst voorkomt voordat u **Goedkeuren en installeren** selecteert.

    ![Azure Resource Manager - Azure DevOps - Azure Pipelines - Alleen opslagplaatsen selecteren](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-only-select-repositories.png)

1. Selecteer uw opslagplaats op het tabblad **Selecteren**. De standaardnaam is `[YourAccountName]/[YourGitHubRepositoryName]`.
1. Selecteer **Starter pipeline** (pijplijn Starter) op het tabblad **Configure** (Configureren). U ziet het pijplijnbestand _azure-pipelines.yml_ met twee scriptstappen.
1. Verwijder de twee scriptstappen uit het _.yml_-bestand.
1. Verplaats de cursor naar de regel na **steps:** .
1. Selecteer **Show Assistant** (Assistent weergeven) rechts in het scherm om het deelvenster **Tasks** (Taken) te openen.
1. Selecteer **ARM template deployment** (ARM-implementatiesjabloon).
1. Voer de volgende waarden in:

    * **deploymentScope**: Selecteer **Resourcegroep**. Zie [Implementatiebereiken](deploy-rest.md#deployment-scope) voor meer informatie over de bereiken.
    * **Azure Resource Manager-verbinding**: Selecteer de naam van de serviceverbinding die u eerder hebt gemaakt.
    * **Abonnement**:  Geef de doelabonnements-id op.
    * **Actie**: Als u **Resourcegroep maken of bijwerken** selecteert, worden er twee acties uitgevoerd. 1: er wordt een resourcegroep gemaakt als u een nieuwe resourcegroepsnaam opgeeft. 2: de opgegeven sjabloon wordt geïmplementeerd.
    * **Resourcegroep**: Voer een nieuwe resourcegroepsnaam in. Bijvoorbeeld: **AzureRmPipeline-rg**.
    * **Locatie**: Selecteer een locatie voor de resourcegroep, bijvoorbeeld **VS - centraal**.
    * **Locatie van sjabloon**: Selecteer de **URL van het bestand**, wat betekent dat de taak zoekt naar het sjabloon bestand met behulp van de URL. Omdat _relativePath_ wordt gebruikt in de hoofd sjabloon en _relativePath_ alleen wordt ondersteund voor implementaties op basis van een URI, moet u hier URL gebruiken.
    * **Sjabloon koppeling**: Voer de URL in die u aan het einde van de sectie [een github-opslag plaats](#prepare-a-github-repository) hebt opgegeven. Begint met `https://raw.githubusercontent.com` .
    * **Koppeling sjabloon parameters**: laat dit veld leeg. U geeft de parameterwaarden op in de parameters voor het **Overschrijven van de sjabloon**.
    * **Sjabloonparameters overschrijven**: Voer `-projectName [EnterAProjectName]` in.
    * **Implementatiemodus**: Selecteer **Incrementeel**.
    * **Naam van implementatie**: Voer **DeployPipelineTemplate** in. U moet **Geavanceerd** selecteren om **Naam van implementatie** weer te geven.

    ![Schermopname van de ARM-sjabloonimplementatiepagina waarop de vereiste waarden zijn ingevoerd.](./media/deployment-tutorial-pipeline/resource-manager-template-pipeline-configure.png)

1. Selecteer **Toevoegen**.

    Zie de [implementatietaak voor de Azure-resourcegroep](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment) en de [implementatietaak voor de Azure Resource Manager-sjabloon](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md) voor meer informatie over de taak

    Het _.yml_-bestand ziet er ongeveer als volgt uit:

    ![Schermopname van de controlepagina met de nieuwe pijplijn met de naam YAML voor uw pijplijn controleren.](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-yml.png)

1. Selecteer **Opslaan en uitvoeren**.
1. Selecteer in het deelvenster **Opslaan en uitvoeren** nogmaals **Opslaan en uitvoeren**. Er wordt een kopie van het YAML-bestand opgeslagen in de verbonden opslagplaats. U kunt het YAML-bestand weergeven door naar uw opslagplaats te bladeren.
1. Controleer of de pijplijn is uitgevoerd.

    ![Azure Resource Manager - Azure DevOps - Azure Pipelines - yaml](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-status.png)

## <a name="verify-the-deployment"></a>De implementatie controleren

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Open de resourcegroep. De naam is wat u hebt opgegeven in het YAML-bestand van de pijplijn. U ziet dat er één opslagaccount is gemaakt. De naam van het opslagaccount begint met **store**.
1. Selecteer het opslagaccount om het te openen.
1. Selecteer **Eigenschappen**. Merk op dat **Replicatie** is ingesteld op **Lokaal redundante opslag (LRS)** .

## <a name="update-and-redeploy"></a>Bijwerken en opnieuw implementeren

Wanneer u de sjabloon bijwerkt en de wijzigingen naar de externe opslagplaats pusht, worden de resources (in dit geval het opslagaccount) automatisch bijgewerkt door de pijplijn.

1. Open _linkedStorageAccount.json_ vanuit uw lokale opslagplaats in Visual Studio Code of een andere teksteditor.
1. Wijzig de **defaultValue** (standaardwaarde) van **storageAccountType** in **Standard_GRS**. Zie de volgende schermafbeelding:

    ![Azure Resource Manager - Azure DevOps - Azure Pipelines - yaml bijwerken](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-update-yml.png)

1. Sla de wijzigingen op.
1. Push de wijzigingen naar de externe opslagplaats door de volgende opdrachten in Git Bash/Shell uit te voeren.

    ```bash
    git pull origin main
    git add .
    git commit -m "Update the storage account type."
    git push origin main
    ```

    Met de eerste opdracht (`pull`) wordt de lokale opslagplaats gesynchroniseerd met de externe opslagplaats. Het YAML-bestand van de pijplijn is alleen toegevoegd aan de externe opslagplaats. Als u de opdracht `pull` uitvoert, wordt een kopie van het YAML-bestand naar de lokale vertakking gedownload.

    Met de vierde opdracht (`push`) wordt het herziene bestand _linkedStorageAccount.json_ geüpload naar de externe opslagplaats. Als de hoofdvertakking van de externe opslagplaats is bijgewerkt, wordt de pijplijn opnieuw geactiveerd.

U kunt de wijzigingen controleren door de replicatie-eigenschap van het opslagaccount te controleren. Zie [De implementatie controleren](#verify-the-deployment).

## <a name="clean-up-resources"></a>Resources opschonen

Schoon de geïmplementeerd Azure-resources, wanneer u deze niet meer nodig hebt, op door de resourcegroep te verwijderen.

1. Selecteer **Resourcegroep** in het linkermenu van Azure Portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **Resourcegroep verwijderen** in het bovenste menu.

Mogelijk wilt u ook de GitHub-opslagplaats en het Azure DevOps-project verwijderen.

## <a name="next-steps"></a>Volgende stappen

Gefeliciteerd. U hebt deze zelfstudie voor de implementatie van een Resource Manager-sjabloon voltooid. Laat het ons weten als u opmerkingen en suggesties hebt in de feedbacksectie. Bedankt!
U kunt nu doorgaan met meer geavanceerde concepten over sjablonen. De volgende zelfstudie gaat dieper in op het gebruik van documentatie voor sjabloonverwijzingen om u te helpen bij het definiëren van te implementeren resources.

> [!div class="nextstepaction"]
> [Sjabloonverwijzing gebruiken](./template-tutorial-use-template-reference.md)

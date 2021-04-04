---
title: Implementatiecentrum voor Azure Kubernetes
description: Het implementatiecentrum in Azure DevOps vereenvoudigt het instellen van een robuuste Azure DevOps-pijplijn voor uw toepassing
ms.author: puagarw
ms.topic: tutorial
ms.date: 07/12/2019
author: pulkitaggarwl
ms.openlocfilehash: 15b0413eabcfae7e3a4b28243caf2a708260ccae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932214"
---
# <a name="deployment-center-for-azure-kubernetes"></a>Implementatiecentrum voor Azure Kubernetes

Het implementatiecentrum in Azure DevOps vereenvoudigt het instellen van een robuuste Azure DevOps-pijplijn voor uw toepassing. Met het implementatiecentrum wordt standaard een Azure DevOps-pijplijn geconfigureerd om uw toepassingsupdates te implementeren op het Kubernetes-cluster. U kunt de standaard geconfigureerde Azure DevOps-pijplijn uitbreiden en ook rijkere mogelijkheden toevoegen: de mogelijkheid om goedkeuring te verkrijgen voordat u implementeert, extra Azure-resources implementeren, scripts uitvoeren, uw toepassing bijwerken en zelfs meer validatietests uitvoeren.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Azure DevOps-pijplijn configureren om uw toepassingsupdates in dit Kubernetes-cluster te implementeren.
> * Controleer de pijplijn voor continue integratie (CI).
> * Controleer de pijplijn voor continue levering (CD).
> * Resources opschonen.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. U kunt er een gratis krijgen via [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

* Een Azure Kubernetes Service (AKS) cluster.

## <a name="create-an-aks-cluster"></a>Een AKS-cluster maken

1. Meld u aan bij uw [Azure Portal](https://portal.azure.com/).

1. Selecteer de optie [Cloud Shell](../cloud-shell/overview.md) aan de rechterkant van de menubalk in de Azure-portal.

1. Voer de volgende opdrachten uit om het AKS-cluster te maken:

    ```azurecli
    # Create a resource group in the South India location:

    az group create --name azooaks --location southindia

    # Create a cluster named azookubectl with one node.

    az aks create --resource-group azooaks --name azookubectl --node-count 1 --enable-addons monitoring --generate-ssh-keys
    ```

## <a name="deploy-application-updates-to-a-kubernetes-cluster"></a>Implementeer toepassingsupdates in een Kubernetes-cluster

1. Ga naar de brongroep die u in de vorige sectie hebt gemaakt.

1. Selecteer het AKS-cluster en selecteer vervolgens **Deployment Center (preview)** op de linker blade. Selecteer **Aan de slag**.

   ![Schermopname van Azure Portal met een pijl die naar het Implementatiecentrum wijst.](media/deployment-center-launcher/settings.png)

1. Kies de locatie van de code en selecteer **Volgende**. Selecteer vervolgens een van de momenteel ondersteunde opslagplaatsen: **[Azure opslagplaats](/azure/devops/repos/index)** of **GitHub**.

    Azure-opslagplaats is een set hulpprogramma's voor versiebeheer waarmee u uw code kunt beheren. Ongeacht of uw software project groot of klein is, is een vroegtijdig gebruik van versiebeheer een goed idee.

    - **Azure-opslagplaats**: Kies een opslagplaats in uw bestaande project en organisatie.

        ![Azure-opslagplaatsen](media/deployment-center-launcher/azure-repos.gif)

    - **GitHub**: Autoriseer en selecteer de opslagplaats voor uw GitHub-account.

        ![Animatie van een proces in GitHub om eerst GitHub als de bron en vervolgens uw opslagplaats te selecteren.](media/deployment-center-launcher/github.gif)


1. Het Implementatiecentrum analyseert de opslagplaats en detecteert uw Dockerfile. Als u de Dockerfile wilt bijwerken, kunt u het geïdentificeerde poortnummer bewerken.

    ![Toepassingsinstellingen](media/deployment-center-launcher/application-settings.png)

    Als de opslagplaats de Dockerfile niet bevat, wordt in het systeem een bericht weergegeven dat er een moet worden doorgevoerd.

    ![Schermopname van het Implementatiecentrum met het bericht Kan geen Dockerfile vinden in de opslagplaats.](media/deployment-center-launcher/dockerfile.png)

1. Selecteer een bestaand containerregister of maak er een, en selecteer vervolgens **Voltooien**. De pijplijn wordt automatisch gemaakt en wacht op build-in [Azure-pijplijnen](/azure/devops/pipelines/index).

    Azure-pijplijnen is een cloudservice die u kunt gebruiken om automatisch uw codeproject te bouwen en te testen en het beschikbaar te maken voor andere gebruikers. Azure-pijplijnen combineren continue integratie en continue levering om uw code constant en consistent te testen en te bouwen en naar een doel te verzenden.

    ![Container Registry](media/deployment-center-launcher/container-registry.png)

1. Selecteer de link om de actieve pijplijn weer te geven.

1. U ziet de geslaagde logboeken nadat de implementatie is voltooid.

    ![Schermopname van het Implementatiecentrum waarin Release-1 is gemarkeerd met een groen vinkje.](media/deployment-center-launcher/logs.png)

## <a name="examine-the-ci-pipeline"></a>De CI-pijplijn onderzoeken

Het implementatiecentrum configureert automatisch uw CI/CD-pijplijn van uw Azure DevOps-organisatie. De pijplijn kan worden verkend en aangepast.

1. Ga naar het dashboard van het implementatiecentrum.  

1. Selecteer het buildnummer in de lijst met geslaagde logboeken om de build-pijplijn voor uw project weer te geven.

1. Selecteer het beletselteken (...) in de rechterbovenhoek. Een menu toont verschillende opties, bijvoorbeeld om een nieuwe build in de wachtrij te plaatsen, een build te onderbreken of de build-pipeline te bewerken. Selecteer **Pijplijn bewerken**. 

1. U kunt u de verschillende taken voor uw build-pijplijn in dit deelvenster onderzoeken. De build voert verschillende taken uit, zoals het verzamelen van bronnen uit de Git-opslagplaats, het maken van een installatiekopie, het pushen van een installatiekopie naar het containerregister en het publiceren van uitvoer die wordt gebruikt voor implementaties.

1. Selecteer bovenaan de pijplijn de naam van de build-pijplijn.

1. Wijzig de naam van de build-pijplijn in een gebruiksvriendelijkere naam. Selecteer **Opslaan en wachtrij** en selecteer vervolgens **Opslaan**.

1. Selecteer onder de naam van de build-pijplijn **Geschiedenis**. Dit deelvenster toont een audittrail van uw recente wijzigingen in de build. Azure DevOps bewaakt alle wijzigingen in de build-pijplijn uitgevoerd en geeft u de mogelijkheid om versies te vergelijken.

1. Selecteer **Triggers**. U kunt vertakkingen van het CI-proces opnemen of uitsluiten.

1. Selecteer **Retentie**. U kunt beleidsregels opgeven om een bepaald aantal builds te behouden of te verwijderen, afhankelijk van het scenario.

## <a name="examine-the-cd-pipeline"></a>De CD-pijplijn onderzoeken

Het implementatiecentrum maakt en configureert automatisch de relatie tussen uw Azure DevOps-organisatie en uw Azure-abonnement. Deze stappen omvatten het configureren van een Azure-serviceverbinding om uw Azure-abonnement te verifiëren bij Azure DevOps. Het geautomatiseerde proces maakt ook een release pijplijn die continue levering aan Azure biedt.

1. Selecteer **Pijplijnen** en selecteer vervolgens **Releases**.

1. Als u de release pijplijn wilt bewerken, selecteert u **Bewerken**.

1. Selecteer **Neerzetten** uit de lijst **Artefacten**. De constructiepijplijn die u in de vorige stappen hebt onderzocht, produceert de uitvoer die wordt gebruikt voor het artefact. 

1. Selecteer de trigger **Continue implementatie** rechts van de optie **Neerzetten**. Deze release-pijplijn heeft een ingeschakelde CD-trigger die een implementatie uitvoert telkens wanneer een nieuw build-artefact beschikbaar is. U kunt de trigger uitschakelen, zodat handmatige uitvoering is vereist voor uw implementaties.

1. Om alle taken voor uw pijplijn te onderzoeken, selecteer **Taken**. De release stelt de Tiller-omgeving in, configureert de parameter `imagePullSecrets`, installeert Helm-hulpprogramma's en implementeert de Helm-grafieken naar het Kubernetes-cluster.

1. Om de release geschiedenis weer te geven, selecteer **Releases weergeven**.

1. Om de samenvatting te zien, selecteer **Release**. Selecteer een van de fasen om verschillende menu's te verkennen, zoals een releaseoverzicht, gekoppelde werkitems en tests. 

1. Selecteer **Doorvoeringen**. Deze weergave geeft codedoorvoeren weer die betrekking hebben op deze implementatie. Vergelijk releases om de doorvoerverschillen tussen implementaties weer te geven.

1. Selecteer **Logboeken**. De logboeken bevatten nuttige informatie over de implementatie, die u tijdens en na de implementaties kunt bekijken.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de gerelateerde resources die u hebt gemaakt, verwijderen wanneer u ze niet meer nodig hebt. Gebruik de functionaliteit Verwijderen op het DevOps Projects-dashboard.

## <a name="next-steps"></a>Volgende stappen

U kunt deze build- en release-pipelines desgewenst wijzigen in overeenstemming met de behoeften van uw team. Of u kunt dit CI/CD-model ook als een sjabloon voor uw andere pijplijnen gebruiken.

---
title: Een containertoepassing implementeren met CI/CD
description: In deze zelfstudie leert u continue integratie en implementatie in te stellen voor een Azure Service Fabric-containertoepassing met behulp van Visual Studio Azure DevOps.
ms.topic: tutorial
ms.date: 08/29/2018
ms.custom: mvc
ms.openlocfilehash: bb0eb9226a99f139ff10a8da12a1e22017536c67
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018848"
---
# <a name="tutorial-deploy-a-container-application-with-cicd-to-a-service-fabric-cluster"></a>Zelfstudie: Een containertoepassing met CI/CD implementeren in een Service Fabric-cluster

Deze zelfstudie is deel twee van een reeks en beschrijft het instellen van continue integratie en implementatie voor een Azure Service Fabric-containertoepassing met behulp van Visual Studio Team Services en Azure DevOps.  Er is een bestaande Service Fabric-toepassing nodig. De toepassing die is gemaakt in [Een .NET-toepassing implementeren in een Windows-container in Azure Service Fabric](service-fabric-host-app-in-a-container.md) wordt als voorbeeld gebruikt.

In deel twee van de serie leert u het volgende:

> [!div class="checklist"]
> * Broncodebeheer aan uw project toevoegen
> * Een build-definitie maken in Visual Studio Team Explorer
> * Een release-definitie maken in Visual Studio Team Explorer
> * Automatisch een upgrade en een toepassing implementeren

## <a name="prerequisites"></a>Vereisten

Voor u met deze zelfstudie begint:

* Een cluster op Azure te hebben of [er een maken met behulp van deze zelfstudie](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Een containertoepassing erin te implementeren](service-fabric-host-app-in-a-container.md)

## <a name="prepare-a-publish-profile"></a>Een publicatieprofiel voorbereiden

Nu u een [containertoepassing hebt geïmplementeerd](service-fabric-host-app-in-a-container.md), bent u klaar om continue integratie in te stellen.  Bereid eerst een publicatieprofiel binnen uw toepassing voor, dat kan worden gebruikt door het implementatieproces dat in Azure DevOps wordt uitgevoerd.  Het publicatieprofiel moet worden geconfigureerd zodat het cluster dat u eerder hebt gemaakt als doel kan dienen.  Start Visual Studio en open een bestaand Service Fabric-toepassingsproject.  Klik in **Solution Explorer** met de rechtermuisknop op de toepassing en selecteer **Publish...** .

Kies een doelprofiel in het toepassingsproject, dat voor de werkstroom voor continue integratie kan worden gebruikt, bijvoorbeeld Cloud.  Geef het eindpunt voor de clusterverbinding op.  Schakel het selectievakje bij **Upgrade the Application** in, zodat voor elke implementatie in Azure DevOps een upgrade voor de toepassing wordt uitgevoerd.  Klik op de hyperlink **Save** om de instellingen op te slaan in het publicatieprofiel. Klik vervolgens op **Cancel** om het dialoogvenster te sluiten.

![Push-profiel][publish-app-profile]

## <a name="share-your-visual-studio-solution-to-a-new-azure-devops-git-repo"></a>Uw Visual Studio-oplossing delen met een nieuwe Git-opslagplaats van Azure DevOps

Deel de bronbestanden van uw toepassing met een teamproject in Azure DevOps zodat u builds kunt genereren.

Maak een nieuwe Git-opslagplaats voor uw project door op de statusbalk in de linkeronderhoek van Visual Studio **Add to Source Control** -> **Git** te selecteren.

Ga naar de **Push**-weergave in **Team Explorer** en selecteer onder **Push to Azure DevOps** de knop **Publish Git Repo**.

![Schermafbeelding van het venster Team Explorer - Synchronization in Visual Studio. De knop Publish Git Repo wordt is uitgelicht onder Push to Azure DevOps.][push-git-repo]

Controleer uw e-mail en selecteer uw organisatie in de vervolgkeuzelijst **Account**. U moet mogelijk een organisatie instellen als u er nog geen hebt. Voer de naam van de opslagplaats in en selecteer **Publish repository**.

![Schermopname van het venster Push to Azure DevOps. De instellingen voor het e-mailadres, het account, de naam van de opslagplaats en de knop Publish Repository zijn uitgelicht.][publish-code]

Als u de opslagplaats publiceert, wordt er een nieuw teamproject voor uw account gemaakt met dezelfde naam als de lokale opslagplaats. Als u de repro in een bestaand teamproject wilt maken, klikt u naast de naam van de **opslagplaats** op **Advanced** en selecteert u een teamproject. U kunt uw code op het web weergeven door **See it on the web** te selecteren.

## <a name="configure-continuous-delivery-with-azure-pipelines"></a>Continue levering configureren met Azure Pipelines

De definitie van Azure DevOps-build beschrijft een werkstroom die bestaat uit een reeks build-stappen die achtereenvolgens worden uitgevoerd. Maak een definitie van een build die een Service Fabric-toepassingspakket maakt en andere artefacten, om deze in een Service Fabric-cluster te implementeren. Meer informatie over [build-definities van Azure DevOps](https://www.visualstudio.com/docs/build/define/create). 

De definitie van een Azure DevOps-release beschrijft een werkstroom waarmee een toepassingspakket in een cluster wordt geïmplementeerd. Als de definities van de build en de release samen worden gebruikt, wordt hiermee de hele werkstroom uitgevoerd, te beginnen met bronbestanden en te eindigen met het uitvoeren van een toepassing in uw cluster. Meer informatie over [release-definities](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition) van Azure DevOps.

### <a name="create-a-build-definition"></a>Een build-definitie maken

Open uw nieuwe teamproject door in een web browser te navigeren naar https://dev.azure.com en uw organisatie selecteren, gevolgd door het nieuwe project. 

Selecteer in het linkerdeelvenster de optie **Pipelines** en klik op **New Pipeline**.

>[!NOTE]
>Als u de sjabloon voor de builddefinitie niet ziet, controleert u of de functie **New YAML pipeline creation experience** is uitgeschakeld. Deze functie wordt geconfigureerd in de sectie **Preview-functies** van uw DevOps-account.

![Nieuwe pijplijn][new-pipeline]

Selecteer **Azure Repos Git** als bron, de naam van uw Team-project, de opslagplaats van het project en de standaardbranch **master** of handmatige en geplande builds.  Klik vervolgens op **Doorgaan**.

Selecteer in **Select a template** de sjabloon **Azure Service Fabric Application met with Docker support** en klik op **Apply**.

![Build-sjabloon kiezen][select-build-template]

In **Tasks** selecteert u **Hosted VS2017** als de **Agent pool**.

![Taken selecteren][task-agent-pool]

Klik op **Tag images**.

In **Container Registry Type** selecteert u **Azure Container Registry**. Selecteer een **Azure Subscription** en klik vervolgens op **Authorize**. Selecteer een **Azure Container Registry**.

![Docker Tag-installatiekopieën selecteren][select-tag-images]

Klik op **Push images**.

In **Container Registry Type** selecteert u **Azure Container Registry**. Selecteer een **Azure Subscription** en klik vervolgens op **Authorize**. Selecteer een **Azure Container Registry**.

![Docker Push-installatiekopieën selecteren][select-push-images]

Schakel op het tabblad **Triggers** continue integratie in door **Enable continuous integration** in te schakelen. Binnen **Vertakkingsfilters** klikt u op **+ Toevoegen**, en de **Vertakkingsspecificatie** wordt standaard op **master** ingesteld.

Klik in het dialoogvenster **Save build pipeline and queue** op **Save & queue** om handmatig een build te starten.

![Triggers selecteren][save-and-queue]

Hiermee worden ook triggers gebouwd na pushen of inchecken. Als u de voortgang van de build wilt controleren, schakelt u over naar het tabblad **Builds**.  Als u hebt gecontroleerd dat de build correct wordt uitgevoerd, definieert u een release-definitie waarmee uw toepassing in een cluster wordt geïmplementeerd.

### <a name="create-a-release-definition"></a>Release-definitie maken

Selecteer in het linkerdeelvenster de optie **Pipelines**, daarna **Releases** en vervolgens **+ New Pipeline**.  Selecteer in **Select a template** de sjabloon **Azure Service Fabric Deployment** in de lijst en vervolgens **Apply**.

![Release-sjabloon kiezen][select-release-template]

Selecteer **Tasks**, vervolgens **Environment 1** en dan **+New** om een clusterverbinding toe te voegen.

![Clusterverbinding toevoegen][add-cluster-connection]

Selecteer in de weergave **Add new Service Fabric Connection** **Certificate Based**- of **Azure Active Directory**-verificatie.  Geef de verbindingsnaam mysftestcluster op en het clustereindpunt tcp://mysftestcluster.southcentralus.cloudapp.azure.com:19000 (of het eindpunt van het cluster waarin de implementatie wordt uitgevoerd).

Voor verificatie op basis van verificatie voegt u de **vingerafdruk voor servercertificaat** toe van het servercertificaat waarmee het cluster is gemaakt.  Voeg in het **clientcertificaat** de basis-64-codering van het certificaatbestand van de client toe. Zie het pop-upitem met de Help voor dat veld voor informatie over hoe u de basis-64-representatie van het certificaat ophaalt. Voeg ook het **wachtwoord** voor het certificaat toe.  U kunt het cluster- of het servercertificaat gebruiken als u geen apart clientcertificaat hebt.

Voor Azure Active Directory-referenties voegt u de **vingerafdruk voor servercertificaat** toe van het servercertificaat dat is gebruikt om het cluster te maken en in de velden **Username** en **Password** de referenties die u wilt gebruiken om het cluster te verbinden.

Klik op **Add** om de clusterverbinding op te slaan.



Klik onder Agent Phase op **Deploy Service Fabric Application**.
Klik op **Docker Settings** en klik vervolgens op **Configure Docker settings**. In **register referentiebron** selecteert u **Azure Resource Manager Service Connection**. Selecteer vervolgens uw **Azure-abonnement**.

![Agent voor release-pijplijn][release-pipeline-agent]

Voeg vervolgens een build-artefact toe aan de pijplijn, zodat met de releasedefinitie de uitvoer van de build kan worden gevonden. Selecteer **Pipeline** en **Artifacts**-> **+Add**.  Selecteer in **Source (Build definition)** de build-definitie die u eerder hebt gemaakt.  Klik op **Add** om het build-artefact op te slaan.

![Artefact toevoegen][add-artifact]

Schakel een trigger voor continue implementatie in, zodat automatisch een release wordt gemaakt als de build wordt voltooid. Klik op het bliksempictogram in het artefact, schakel de trigger in en klik op **Save** om de release-definitie op te slaan.

![Trigger inschakelen][enable-trigger]

Selecteer **+Release** -> **Create a Release** -> **Create** om handmatig een release te maken. U kunt de voortgang van de release volgen op het tabblad **Releases**.

Controleer of de implementatie is gelukt en de toepassing in het cluster wordt uitgevoerd.  Open een webbrowser en ga naar `http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/`.  Noteer de versie van de toepassing (in dit voorbeeld 1.0.0.20170616.3).

## <a name="commit-and-push-changes-trigger-a-release"></a>Wijzigingen doorvoeren en pushen, een release activeren

Controleer of de pijplijn voor continue integratie correct functioneert door enkele codewijzigingen aan te brengen in Azure DevOps.

Terwijl u de code schrijft, worden de wijzigingen automatisch in Visual Studio bijgehouden. Voer wijzigingen door in de lokale Git-opslagplaats door het pictogram voor wijzigingen in behandeling (![Pictogram Wijzigingen in behandeling met een potlood en een getal.][pending]) te selecteren op de statusbalk rechtsonder.

Voeg aan de weergave **Changes** in Team Explorer een bericht toe waarin u de update beschrijft en voer de wijzigingen door.

![Alles doorvoeren][changes]

Selecteer het pictogram voor de statusbalk met niet-gepubliceerde wijzigingen (![Unpublished changes][unpublished-changes]) of de synchronisatieweergave in Team Explorer. Selecteer **Push** om de code bij te werken in Azure DevOps.

![Wijzigingen pushen][push]

Als u de wijzigingen naar Azure DevOps pusht, wordt er automatisch een build geactiveerd.  Als de build-definitie wordt voltooid, wordt er automatisch een release gemaakt en wordt de toepassing in het cluster bijgewerkt.

Als u de voortgang van de build wilt controleren, schakelt u over naar het tabblad **Builds** in **Team Explorer** in Visual Studio.  Als u hebt gecontroleerd dat de build correct wordt uitgevoerd, definieert u een release-definitie waarmee uw toepassing in een cluster wordt geïmplementeerd.

Controleer of de implementatie is gelukt en de toepassing in het cluster wordt uitgevoerd.  Open een webbrowser en ga naar `http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/`.  Noteer de versie van de toepassing (in dit voorbeeld 1.0.0.20170815.3).

![Schermopname van de Voting-app in Service Fabric Explorer. Op het tabblad Essentials is de app-versie '1.0.0.20170815.3' uitgelicht.][sfx1]

## <a name="update-the-application"></a>De toepassing bijwerken

Breng in de toepassing wijzigingen aan de code aan.  Sla de wijzigingen op en voer ze door aan de hand van de voorgaande stappen.

Zodra de upgrade van de toepassing wordt uitgevoerd, kunt u de voortgang ervan volgen in Service Fabric Explorer:

![Schermopname van de Voting-app in Service Fabric Explorer. Het bericht 'Upgrade in Progress' wordt uitgelicht en de app-status is 'Upgrading'.][sfx2]

Het kan enkele minuten duren voordat de toepassing is bijgewerkt. Als het bijwerken is voltooid, wordt de volgende versie door de toepassing uitgevoerd.  In dit voorbeeld 1.0.0.20170815.4.

![Schermopname van de Voting-app in Service Fabric Explorer. Op het tabblad Essentials is de bijgewerkte app-versie '1.0.0.20170815.4' uitgelicht.][sfx3]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Broncodebeheer aan uw project toevoegen
> * Een build-definitie maken
> * Release-definitie maken
> * Automatisch een upgrade en een toepassing implementeren

In het volgende gedeelte van de zelfstudie krijgt u informatie over het instellen van [bewaking voor de container](service-fabric-tutorial-monitoring-wincontainers.md).

<!-- Image References -->
[publish-app-profile]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/PublishAppProfile.png
[push-git-repo]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/PublishGitRepo.png
[publish-code]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/PublishCode.png
[new-pipeline]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/NewPipeline.png
[select-build-template]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SelectBuildTemplate.png
[task-agent-pool]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/TaskAgentPool.png
[save-and-queue]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SaveAndQueue.png
[select-tag-images]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/DockerTagImages.png
[select-push-images]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/DockerPushImages.png
[select-release-template]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SelectReleaseTemplate.png
[set-continuous-integration]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SetContinuousIntegration.png
[add-cluster-connection]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/AddClusterConnection.png
[release-pipeline-agent]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/ReleasePipelineAgent.png
[add-artifact]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/AddArtifact.png
[enable-trigger]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/EnableTrigger.png
[sfx1]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SFX1.png
[sfx2]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SFX2.png
[sfx3]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SFX3.png
[pending]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/Pending.png
[changes]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/Changes.png
[unpublished-changes]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/UnpublishedChanges.png
[push]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/Push.png
[continuous-delivery-with-VSTS]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/VSTS-Dialog.png
[new-service-endpoint]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/NewServiceEndpoint.png
[new-service-endpoint-dialog]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/NewServiceEndpointDialog.png

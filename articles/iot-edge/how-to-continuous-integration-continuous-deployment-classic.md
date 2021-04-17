---
title: Continue integratie en continue implementatie op Azure IoT Edge apparaten (klassieke editor) - Azure IoT Edge
description: Continue integratie en continue implementatie instellen met behulp van de klassieke editor - Azure IoT Edge met Azure DevOps, Azure Pipelines
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/26/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f7c28ecbaa58731c528a9ecb5f869eba2bc0c99f
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484418"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge-devices-classic-editor"></a>Continue integratie en continue implementatie op Azure IoT Edge apparaten (klassieke editor)

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

U kunt DevOps eenvoudig gebruiken met uw Azure IoT Edge toepassingen met de ingebouwde Azure IoT Edge taken in Azure Pipelines. In dit artikel wordt gedemonstreerd hoe u de functies voor continue integratie en continue implementatie van Azure Pipelines kunt gebruiken om snel en efficiënt toepassingen te bouwen, testen en implementeren in uw Azure IoT Edge met behulp van de klassieke editor. U kunt ook [YAML gebruiken.](how-to-continuous-integration-continuous-deployment.md)

![Diagram - CI- en CD-vertakkingen voor ontwikkeling en productie](./media/how-to-continuous-integration-continuous-deployment-classic/model.png)

In dit artikel leert u hoe u de ingebouwde [Azure IoT Edge-taken](/azure/devops/pipelines/tasks/build/azure-iot-edge) voor Azure Pipelines gebruikt om build- en release-pijplijnen te maken voor uw IoT Edge oplossing. Elke Azure IoT Edge taak die aan uw pijplijn wordt toegevoegd, implementeert een van de volgende vier acties:

 | Bewerking | Beschrijving |
 | --- | --- |
 | Module-afbeeldingen bouwen | Neemt uw IoT Edge oplossingscode en bouwt de containerafbeeldingen.|
 | Module-afbeeldingen pushen | Hiermee pusht u module-afbeeldingen naar het containerregister dat u hebt opgegeven. |
 | Implementatiemanifest genereren | Neemt een deployment.template.jsbestand en de variabelen en genereert vervolgens het uiteindelijke IoT Edge manifestbestand voor de implementatie. |
 | Implementeren naar IoT Edge-apparaten | Maakt IoT Edge implementaties op een of meer IoT Edge apparaten. |

Tenzij anders aangegeven, verkennen de procedures in dit artikel niet alle functionaliteit die beschikbaar is via taakparameters. Raadpleeg de volgende artikelen voor meer informatie:

* [Taakversie](/azure/devops/pipelines/process/tasks?tabs=classic#task-versions)
* **Geavanceerd:** geef, indien van toepassing, modules op die u niet wilt maken.
* [Besturingselementopties](/azure/devops/pipelines/process/tasks?tabs=classic#task-control-options)
* [Omgevingsvariabelen](/azure/devops/pipelines/process/variables?tabs=classic#environment-variables)
* [Uitvoervariabelen](/azure/devops/pipelines/process/variables?tabs=classic#use-output-variables-from-tasks)

## <a name="prerequisites"></a>Vereisten

* Een opslagplaats voor Azure-opslagplaatsen. Als u nog geen git-repo hebt, kunt u [een nieuwe Git-repo maken in uw project](/azure/devops/repos/git/create-new-repo). Voor dit artikel hebben we een opslagplaats gemaakt met de **naam IoTEdgeRepo.**
* Een IoT Edge oplossing die is vastgelegd en naar uw opslagplaats wordt pushen. Als u een nieuwe voorbeeldoplossing wilt maken voor het testen van dit artikel, volgt u de stappen in Modules ontwikkelen en fouten opsporen [in Visual Studio Code of](how-to-vs-code-develop-module.md) [C#-modules](./how-to-visual-studio-develop-module.md)ontwikkelen en fouten opsporen in Visual Studio . Voor dit artikel hebben we een oplossing gemaakt in onze opslagplaats met de naam **IoTEdgeSolution,** die de code bevat voor een module met de **naam filtermodule**.

   Voor dit artikel hebt u alleen de oplossingsmap nodig die is gemaakt door de IoT Edge-sjablonen in Visual Studio Code of Visual Studio. U hoeft deze code niet te bouwen, pushen, implementeren of fouten op te sporen voordat u doorgaat. U stelt deze processen in Azure Pipelines in.

   Als u een nieuwe oplossing maakt, kloont u eerst uw opslagplaats lokaal. Wanneer u vervolgens de oplossing maakt, kunt u ervoor kiezen om deze rechtstreeks in de map opslagplaats te maken. U kunt de nieuwe bestanden daar eenvoudig commiten en pushen.

* Een containerregister waarin u module-afbeeldingen kunt pushen. U kunt [Azure Container Registry](../container-registry/index.yml) register van derden gebruiken.
* Een actieve Azure [IoT-hub](../iot-hub/iot-hub-create-through-portal.md) met ten minste twee IoT Edge voor het testen van de afzonderlijke test- en productie-implementatiefasen. U kunt de quickstart-artikelen volgen om een IoT Edge te maken in [Linux](quickstart-linux.md) of [Windows](quickstart.md)

## <a name="create-a-build-pipeline-for-continuous-integration"></a>Een build-pijplijn maken voor continue integratie

In deze sectie maakt u een nieuwe build-pipeline. U configureert de pijplijn zo dat deze automatisch wordt uitgevoerd wanneer u wijzigingen in de voorbeeldoplossing IoT Edge controleert en buildlogboeken publiceert.

1. Meld u aan bij uw Azure DevOps-organisatie ( ) en open het project dat uw opslagplaats `https://dev.azure.com/{your organization}` IoT Edge oplossing bevat.

    ![Open uw DevOps-project](./media/how-to-continuous-integration-continuous-deployment-classic/initial-project.png)

2. Selecteer pijplijnen in het menu van het **linkerdeelvenster in uw** project. Selecteer **Pijplijn maken** in het midden van de pagina. Als u al build-pijplijnen hebt, selecteert u **rechtsboven** de knop Nieuwe pijplijn.

    ![Een nieuwe build-pipeline maken](./media/how-to-continuous-integration-continuous-deployment-classic/add-new-pipeline.png)

3. Selecteer onder aan de **pagina Waar is uw code?** de optie De klassieke editor **gebruiken.** Zie de [YAML-handleiding](how-to-continuous-integration-continuous-deployment.md)als u YAML wilt gebruiken om de build-pijplijnen van uw project te maken.

    ![Selecteer De klassieke editor gebruiken](./media/how-to-continuous-integration-continuous-deployment-classic/create-without-yaml.png)

4. Volg de aanwijzingen om uw pijplijn te maken.

   1. Geef de broninformatie voor uw nieuwe build-pipeline op. Selecteer **Azure Repos Git** als de bron en selecteer vervolgens het project, de opslagplaats en de vertakking waar uw IoT Edge oplossingscode zich bevindt. Selecteer vervolgens **Doorgaan**.

      ![Uw pijplijnbron selecteren](./media/how-to-continuous-integration-continuous-deployment-classic/pipeline-source.png)

   2. Selecteer **Lege taak** in plaats van een sjabloon.

      ![Beginnen met een lege taak voor uw build-pijplijn](./media/how-to-continuous-integration-continuous-deployment-classic/start-with-empty-build-job.png)

5. Zodra de pijplijn is gemaakt, gaat u naar de pijplijneditor. Hier kunt u de naam van de pijplijn, de agentpool en de agentspecificatie wijzigen.

   U kunt een door Microsoft gehoste pool of een zelf-hostende pool selecteren die u beheert.

   Kies in de beschrijving van uw pijplijn de juiste agentspecificatie op basis van uw doelplatform:

   * Als u uw modules wilt bouwen in platform amd64 voor Linux-containers, kiest u **ubuntu-16.04**

   * Als u uw modules wilt bouwen in platform amd64 voor Windows 1809-containers, moet u een [zelf-hostende agent instellen op Windows](/azure/devops/pipelines/agents/v2-windows).

   * Als u uw modules wilt bouwen in platform arm32v7- of arm64 voor Linux-containers, moet u een [zelf-hostende agent](https://devblogs.microsoft.com/iotdev/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent)instellen op Linux .

    ![Specificatie van buildagent configureren](./media/how-to-continuous-integration-continuous-deployment-classic/configure-env.png)

6. Uw pijplijn is vooraf geconfigureerd met een taak met de naam **Agent job 1.** Selecteer het plusteken ( ) om vier taken aan de taak toe te voegen: twee keer Azure IoT Edge kopiëren, Bestanden één keer **+** kopiëren en **Buildartefacten één keer** publiceren.   Zoek naar elke taak en beweeg de muisaanwijzer over de naam van de taak om de knop **Toevoegen te** zien.

   ![Een Azure IoT Edge toevoegen](./media/how-to-continuous-integration-continuous-deployment-classic/add-iot-edge-task.png)

   Wanneer alle vier de taken worden toegevoegd, ziet uw Agent-taak eruit zoals in het volgende voorbeeld:

   ![Vier taken in de build-pipeline](./media/how-to-continuous-integration-continuous-deployment-classic/add-tasks.png)

7. Selecteer de eerste **Azure IoT Edge** om deze te bewerken. Met deze taak worden alle modules in de oplossing gebouwd met het doelplatform dat u opgeeft. Bewerk de taak met de volgende waarden:

    | Parameter | Beschrijving |
    | --- | --- |
    | Weergavenaam | De weergavenaam wordt automatisch bijgewerkt wanneer het veld Actie wordt gewijzigd. |
    | Bewerking | Selecteer **Build module images**. |
    | .template.jsin het bestand | Selecteer het beletselteken (**...**) en navigeer naardeployment.template.js **bestand** in de opslagplaats die uw IoT Edge oplossing bevat. |
    | Standaardplatform | Selecteer het juiste besturingssysteem voor uw modules op basis van uw IoT Edge apparaat. |
    | Uitvoervariabelen | Geef een referentienaam op om te koppelen aan het bestandspad deployment.jsbestand genereert, zoals **edge**. |

   Deze configuraties maken gebruik van de opslagplaats voor afbeeldingen en de tag die in het bestand zijn gedefinieerd om de moduleafbeelding een naam te geven `module.json` en te taggen. **Bouw module-afbeeldingen** en vervang de variabelen ook door de exacte waarde die u in het bestand `module.json` definieert. In Visual Studio of Visual Studio Code geeft u de werkelijke waarde op in een `.env` bestand. In Azure Pipelines stelt u de waarde in op het **tabblad Pijplijnvariabelen.** Selecteer het **tabblad Variabelen in** het menu van de pijplijneditor en configureer de naam en waarde als volgt:

    * **ACR_ADDRESS:** de waarde Azure Container Registry **aanmeldingsserver.** U vindt de aanmeldingsserver op de overzichtspagina van het containerregister in de Azure-portal.

    Als u andere variabelen in uw project hebt, kunt u de naam en waarde op dit tabblad opgeven. **Build module images recognizes** only variables that are in `${VARIABLE}` format. Zorg ervoor dat u deze indeling in uw bestanden `**/module.json` gebruikt.

8. Selecteer de tweede **Azure IoT Edge** om deze te bewerken. Deze taak pusht alle module-afbeeldingen naar het containerregister dat u selecteert.

    | Parameter | Beschrijving |
    | --- | --- |
    | Weergavenaam | De weergavenaam wordt automatisch bijgewerkt wanneer het veld Actie wordt gewijzigd. |
    | Bewerking | Selecteer **Push-module-afbeeldingen.** |
    | Type containerregister | Gebruik het standaardtype: `Azure Container Registry` . |
    | Azure-abonnement | Kies uw abonnement. |
    | Azure Container Registry | Selecteer het type containerregister dat u gebruikt om de module-afbeeldingen op te slaan. Afhankelijk van welk registertype u kiest, wordt het formulier gewijzigd. Als u een **Azure Container Registry,** gebruikt u de vervolgkeuzelijsten om het Azure-abonnement en de naam van uw containerregister te selecteren. Als u Algemene **Container Registry,** selecteert u **Nieuw om** een registerserviceverbinding te maken. |
    | .template.jsin het bestand | Selecteer het beletselteken (**...**) en navigeer naardeployment.template.js **bestand** in de opslagplaats die uw IoT Edge oplossing bevat. |
    | Standaardplatform | Selecteer het juiste besturingssysteem voor uw modules op basis van uw IoT Edge apparaat. |
    | Registerreferenties toevoegen aan implementatiemanifest | Geef waar op om de registerreferenties toe te voegen voor het pushen van Docker-installatie afbeeldingen naar het implementatiemanifest. |

   Als u meerdere containerregisters hebt om uw module-afbeeldingen te hosten, moet u deze taak dupliceren, verschillende containerregisters selecteren en **module(s) bypass** gebruiken in de Geavanceerde instellingen om de afbeeldingen die niet voor dit specifieke register zijn, over te nemen. 

9. Selecteer de **taak Bestanden kopiëren** om deze te bewerken. Gebruik deze taak om bestanden te kopiëren naar de map voor fasering van artefacten.

    | Parameter | Beschrijving |
    | --- | --- |
    | Weergavenaam | Gebruik de standaardnaam of pas deze aan |
    | Bronmap | De map met de bestanden die moeten worden gekopieerd. |
    | Inhoud | Voeg twee regels toe: `deployment.template.json` en `**/module.json` . Deze twee bestanden fungeren als invoer voor het genereren van het IoT Edge implementatiemanifest. |
    | Doelmap | Geef de variabele `$(Build.ArtifactStagingDirectory)` op. Zie [Variabelen bouwen voor](/azure/devops/pipelines/build/variables#build-variables) meer informatie over de beschrijving. |

10. Selecteer de **taak BuildArtefacten** publiceren om deze te bewerken. Geef het pad naar de map voor fasering van artefacten naar de taak op, zodat het pad kan worden gepubliceerd naar de release-pijplijn.

    | Parameter | Beschrijving |
    | --- | --- |
    | Weergavenaam | Gebruik de standaardnaam of pas deze aan. |
    | Te publiceren pad | Geef de variabele `$(Build.ArtifactStagingDirectory)` op. Zie [Variabelen bouwen voor](/azure/devops/pipelines/build/variables#build-variables) meer informatie. |
    | Naam van het artefact | Gebruik de standaardnaam: **drop** |
    | Publicatielocatie voor artefacten | De standaardlocatie gebruiken: **Azure Pipelines** |

11. Open het **tabblad Triggers** en schakel het selectievakje Continue **integratie inschakelen in.** Zorg ervoor dat de vertakking met uw code is opgenomen.

    ![Trigger voor continue integratie in-](./media/how-to-continuous-integration-continuous-deployment-classic/configure-trigger.png)

12. Selecteer **Opslaan** in de **vervolgkeuzelijst & wachtrij** opslaan.

Deze pijplijn is nu geconfigureerd om automatisch te worden uitgevoerd wanneer u nieuwe code naar uw repo pusht. De laatste taak, het publiceren van de pijplijnartefacten, activeert een release-pijplijn. Ga door naar de volgende sectie om de release-pijplijn te bouwen.

[!INCLUDE [iot-edge-create-release-pipeline-for-continuous-deployment](../../includes/iot-edge-create-release-pipeline-for-continuous-deployment.md)]

>[!NOTE]
>Als u gelaagde implementaties in uw pijplijn wilt gebruiken, worden gelaagde **implementaties** nog niet ondersteund in Azure IoT Edge taken in Azure DevOps.
>
>U kunt echter een [Azure CLI-taak in Azure DevOps](/azure/devops/pipelines/tasks/deploy/azure-cli) gebruiken om uw implementatie te maken als een gelaagde implementatie. Voor de **waarde Inline Script** kunt u de [opdracht az iot edge deployment create gebruiken:](/cli/azure/iot/edge/deployment)
>
>   ```azurecli-interactive
>   az iot edge deployment create -d {deployment_name} -n {hub_name} --content modules_content.json --layered true
>   ```

[!INCLUDE [iot-edge-verify-iot-edge-continuous-integration-continuous-deployment](../../includes/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment.md)]

## <a name="next-steps"></a>Volgende stappen

* IoT Edge DevOps best practices sample in [Azure DevOps Starter for IoT Edge](how-to-devops-starter.md)
* Inzicht in IoT Edge implementatie in Inzicht in [IoT Edge implementaties voor individuele apparaten of op schaal](module-deployment-monitoring.md)
* Doorloop de stappen voor het maken, bijwerken of verwijderen van een implementatie in [Deploy and monitor IoT Edge modules at scale (Implementatie](how-to-deploy-at-scale.md)en IoT Edge op schaal).
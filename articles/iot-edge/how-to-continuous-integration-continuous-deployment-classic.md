---
title: Continue integratie en continue implementatie naar Azure IoT Edge apparaten (klassieke editor)-Azure IoT Edge
description: Continue integratie en continue implementatie instellen met behulp van de klassieke editor-Azure IoT Edge met Azure DevOps, Azure-pijp lijnen
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/26/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: e38b3c617ded9c0001b01e481d4d3c1120be62ef
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100634750"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge-devices-classic-editor"></a>Continue integratie en continue implementatie naar Azure IoT Edge apparaten (klassieke editor)

U kunt eenvoudig DevOps met uw Azure IoT Edge-toepassingen met de ingebouwde Azure IoT Edge taken in azure-pijp lijnen. In dit artikel wordt beschreven hoe u de functies continue integratie en continue implementatie van Azure-pijp lijnen kunt gebruiken om toepassingen snel en efficiënt te bouwen, Azure IoT Edge te testen en te implementeren met behulp van de klassieke editor. U kunt ook [yaml gebruiken](how-to-continuous-integration-continuous-deployment.md).

![Diagram-CI-en CD-vertakkingen voor ontwikkeling en productie](./media/how-to-continuous-integration-continuous-deployment-classic/model.png)

In dit artikel leert u hoe u de ingebouwde [Azure IOT Edge taken](/azure/devops/pipelines/tasks/build/azure-iot-edge) voor Azure-pijp lijnen kunt gebruiken voor het maken van builds en release pijplijnen voor uw IOT EDGE oplossing. Elke Azure IoT Edge taak die aan uw pijp lijn is toegevoegd, implementeert een van de volgende vier acties:

 | Bewerking | Beschrijving |
 | --- | --- |
 | Module installatie kopieën bouwen | Neemt uw IoT Edge oplossings code en bouwt de container installatie kopieën.|
 | Installatie kopieën push module | Pusht installatie kopieën van module naar het container register dat u hebt opgegeven. |
 | Implementatie manifest genereren | Er wordt een deployment.template.jsin het bestand en de variabelen en vervolgens wordt het definitieve manifest bestand voor IoT Edge implementatie gegenereerd. |
 | Implementeren naar IoT Edge-apparaten | Hiermee maakt u IoT Edge-implementaties op een of meer IoT Edge apparaten. |

Tenzij anders aangegeven, verkennen de procedures in dit artikel niet alle beschik bare functies via taak parameters. Raadpleeg de volgende artikelen voor meer informatie:

* [Taak versie](/azure/devops/pipelines/process/tasks?tabs=classic#task-versions)
* **Geavanceerd** : Geef modules op die u niet wilt maken, indien van toepassing.
* [Beheer opties](/azure/devops/pipelines/process/tasks?tabs=classic#task-control-options)
* [Omgevings variabelen](/azure/devops/pipelines/process/variables?tabs=classic#environment-variables)
* [Uitvoer variabelen](/azure/devops/pipelines/process/variables?tabs=classic#use-output-variables-from-tasks)

## <a name="prerequisites"></a>Vereisten

* Een Azure opslag plaatsen-opslag plaats. Als u er nog geen hebt, kunt u [een nieuwe Git-opslag plaats in uw project maken](/azure/devops/repos/git/create-new-repo). Voor dit artikel hebt u een opslag plaats gemaakt met de naam **IoTEdgeRepo**.
* Een IoT Edge oplossing die is doorgevoerd en gepusht naar uw opslag plaats. Als u een nieuwe voorbeeld oplossing wilt maken voor het testen van dit artikel, volgt u de stappen in [modules voor ontwikkel-en fout opsporing in Visual Studio code](how-to-vs-code-develop-module.md) of [ontwikkel en debug C#-modules in Visual Studio](./how-to-visual-studio-develop-module.md). Voor dit artikel hebben we een oplossing in onze opslag plaats gemaakt met de naam **IoTEdgeSolution**, die de code bevat voor een module met de naam **filtermodule**.

   Voor dit artikel hoeft u alleen de map Solution te maken die is gemaakt door de IoT Edge sjablonen in Visual Studio code of Visual Studio. U hoeft deze code niet te bouwen, te pushen, te implementeren of te debuggen voordat u doorgaat. Deze processen worden ingesteld in azure-pijp lijnen.

   Als u een nieuwe oplossing wilt maken, moet u uw opslag plaats lokaal eerst klonen. Wanneer u de oplossing maakt, kunt u ervoor kiezen om deze rechtstreeks in de map opslag plaats te maken. U kunt de nieuwe bestanden eenvoudig van daaruit door voeren en pushen.

* Een container register waar u module installatie kopieën kunt pushen. U kunt [Azure container Registry](../container-registry/index.yml) of een REGI ster van derden gebruiken.
* Een actieve Azure [IOT-hub](../iot-hub/iot-hub-create-through-portal.md) met ten minste twee IOT edge-apparaten voor het testen van de afzonderlijke test-en productie-implementatie fasen. U kunt de Quick Start-artikelen volgen om een IoT Edge apparaat te maken in [Linux](quickstart-linux.md) of [Windows](quickstart.md)

## <a name="create-a-build-pipeline-for-continuous-integration"></a>Een build-pijp lijn maken voor continue integratie

In deze sectie maakt u een nieuwe build-pijp lijn. U configureert de pijp lijn zo dat deze automatisch wordt uitgevoerd wanneer u wijzigingen aanbrengt in de voor beeld-IoT Edge oplossing en het publiceren van build-logboeken publiceert.

1. Meld u aan bij uw Azure DevOps-organisatie ( `https://dev.azure.com/{your organization}` ) en open het project met uw IOT Edge Solution-opslag plaats.

    ![Open uw DevOps-project](./media/how-to-continuous-integration-continuous-deployment-classic/initial-project.png)

2. Selecteer in het menu van het linkerdeel venster in uw project **pijp lijnen**. Selecteer **pijp lijn maken** in het midden van de pagina. Als u al een pijp lijn hebt gemaakt, selecteert u de knop **nieuwe pijp lijn** in de rechter bovenhoek.

    ![Een nieuwe build-pipeline maken](./media/how-to-continuous-integration-continuous-deployment-classic/add-new-pipeline.png)

3. Selecteer aan de onderkant van de pagina **waar is uw code?** **de optie klassieke editor gebruiken**. Als u YAML wilt gebruiken om de build-pijp lijnen van uw project te maken, raadpleegt u de [hand leiding voor yaml](how-to-continuous-integration-continuous-deployment.md).

    ![De klassieke editor gebruiken selecteren](./media/how-to-continuous-integration-continuous-deployment-classic/create-without-yaml.png)

4. Volg de aanwijzingen om uw pijp lijn te maken.

   1. Geef de bron gegevens voor de nieuwe build-pijp lijn op. Selecteer **Azure opslag plaatsen Git** als bron en selecteer vervolgens het project, de opslag plaats en de vertakking waar uw IOT Edge oplossings code zich bevindt. Selecteer vervolgens **Doorgaan**.

      ![De pijplijn bron selecteren](./media/how-to-continuous-integration-continuous-deployment-classic/pipeline-source.png)

   2. Selecteer **lege taak** in plaats van een sjabloon.

      ![Beginnen met een lege taak voor uw build-pijp lijn](./media/how-to-continuous-integration-continuous-deployment-classic/start-with-empty-build-job.png)

5. Zodra de pijp lijn is gemaakt, wordt u naar de pijplijn editor geleid. Hier kunt u de naam, agent groep en agent specificatie van de pijp lijn wijzigen.

   U kunt een door micro soft gehoste groep of een zelf-hostende groep selecteren die u beheert.

   Kies in uw pijplijn beschrijving de juiste agent specificatie op basis van uw doel platform:

   * Als u uw modules wilt maken in platform amd64 voor Linux-containers, kiest u **Ubuntu-16,04**

   * Als u uw modules in platform amd64 voor Windows 1809-containers wilt maken, moet u [zelf-hostende agent instellen in Windows](/azure/devops/pipelines/agents/v2-windows).

   * Als u uw modules in platform arm32v7 of arm64 voor Linux-containers wilt maken, moet u [zelf-hostende agent instellen in Linux](https://devblogs.microsoft.com/iotdev/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent).

    ![Specificatie van build-agent configureren](./media/how-to-continuous-integration-continuous-deployment-classic/configure-env.png)

6. Uw pijp lijn wordt vooraf geconfigureerd met een taak genaamd **Agent taak 1**. Selecteer het plus teken ( **+** ) om vier taken toe te voegen aan de taak: **Azure IOT Edge** twee maal, **Kopieer bestanden** één keer en **Publiceer build-artefacten** . Zoek naar elke taak en beweeg de muis aanwijzer over de naam van de taak om de knop **toevoegen** weer te geven.

   ![Azure IoT Edge taak toevoegen](./media/how-to-continuous-integration-continuous-deployment-classic/add-iot-edge-task.png)

   Wanneer alle vier de taken zijn toegevoegd, ziet uw agent-taak eruit als in het volgende voor beeld:

   ![Vier taken in de build-pijp lijn](./media/how-to-continuous-integration-continuous-deployment-classic/add-tasks.png)

7. Selecteer de eerste **Azure IOT Edge** taak om deze te bewerken. Met deze taak worden alle modules in de oplossing gebaseerd op het doel platform dat u opgeeft. Bewerk de taak met de volgende waarden:

    | Parameter | Beschrijving |
    | --- | --- |
    | Weergavenaam | De weergave naam wordt automatisch bijgewerkt wanneer het actie veld wordt gewijzigd. |
    | Bewerking | Selecteer **installatie kopieën van module bouwen**. |
    | .template.jsvoor bestand | Selecteer het beletsel teken (**...**) en navigeer naar het **deployment.template.js** bestand in de opslag plaats met uw IOT EDGE oplossing. |
    | Standaard platform | Selecteer het juiste besturings systeem voor uw modules op basis van uw doel-IoT Edge apparaat. |
    | Uitvoer variabelen | Geef een referentie naam op die moet worden gekoppeld aan het pad naar het bestand waarin uw deployment.jsworden gegenereerd, zoals **Edge**. |

   Deze configuraties gebruiken de opslag plaats en label van de installatie kopie die in het bestand zijn gedefinieerd `module.json` om de module installatie kopie te labelen. Het **maken van module-installatie kopieën** helpt ook de variabelen te vervangen door de exacte waarde die u in het `module.json` bestand definieert. In Visual Studio of Visual Studio code geeft u de daad werkelijke waarde op in een `.env` bestand. In azure-pijp lijnen stelt u de waarde in op het tabblad **pijplijn variabelen** . Selecteer het tabblad **variabelen** in het menu van de pijplijn editor en configureer de naam en waarde als volgt:

    * **ACR_ADDRESS**: de waarde van de Azure container Registry- **aanmeldings server** . U vindt de aanmeldingsserver op de overzichtspagina van het containerregister in de Azure-portal.

    Als u andere variabelen in uw project hebt, kunt u de naam en waarde op dit tabblad opgeven. bij het **bouwen van module-installatie kopieën** worden alleen variabelen met een `${VARIABLE}` notatie herkend. Zorg ervoor dat u deze indeling in uw `**/module.json` bestanden gebruikt.

8. Selecteer de tweede **Azure IOT Edge** taak om deze te bewerken. Met deze taak worden alle module-installatie kopieën gepusht naar het container register dat u selecteert.

    | Parameter | Beschrijving |
    | --- | --- |
    | Weergavenaam | De weergave naam wordt automatisch bijgewerkt wanneer het actie veld wordt gewijzigd. |
    | Bewerking | Selecteer **installatie kopieën van push module**. |
    | Container register type | Gebruik het standaard type: `Azure Container Registry` . |
    | Azure-abonnement | Kies uw abonnement. |
    | Azure Container Registry | Selecteer het type container register dat u gebruikt om de module installatie kopieën op te slaan. Afhankelijk van het register type dat u kiest, verandert het formulier. Als u **Azure container Registry** selecteert, gebruikt u de vervolg keuzelijst om het Azure-abonnement en de naam van uw container register te selecteren. Als u **algemene container Registry** kiest, selecteert u **Nieuw** om een register service verbinding te maken. |
    | .template.jsvoor bestand | Selecteer het beletsel teken (**...**) en navigeer naar het **deployment.template.js** bestand in de opslag plaats met uw IOT EDGE oplossing. |
    | Standaard platform | Selecteer het juiste besturings systeem voor uw modules op basis van uw doel-IoT Edge apparaat. |
    | Register referentie toevoegen aan implementatie manifest | Geef waar op om de register referentie voor het pushen van docker-installatie kopieën naar het implementatie manifest toe te voegen. |

   Als u meerdere container registers hebt om uw module installatie kopieën te hosten, moet u deze taak dupliceren, een ander container register selecteren en **module (s) overs Laan** gebruiken in de **Geavanceerde** instellingen om de installatie kopieën die niet voor dit specifieke REGI ster zijn, over te slaan.

9. Selecteer de taak **bestanden kopiëren** om deze te bewerken. Gebruik deze taak om bestanden te kopiëren naar de map voor het voorbereiden van artefacten.

    | Parameter | Beschrijving |
    | --- | --- |
    | Weergavenaam | De standaard naam gebruiken of aanpassen |
    | Bronmap | De map met de bestanden die moeten worden gekopieerd. |
    | Inhoud | Twee regels toevoegen: `deployment.template.json` en `**/module.json` . Deze twee bestanden dienen als invoer voor het genereren van het IoT Edge implementatie manifest. |
    | Doelmap | Geef de variabele op `$(Build.ArtifactStagingDirectory)` . Zie [Build Varia bles](/azure/devops/pipelines/build/variables#build-variables) voor meer informatie over de beschrijving. |

10. Selecteer de taak **Build-artefacten publiceren** om deze te bewerken. Geef het pad naar de map voor het klaarzetten van artefacten naar de taak zodat het pad naar de release pijplijn kan worden gepubliceerd.

    | Parameter | Beschrijving |
    | --- | --- |
    | Weergavenaam | Gebruik de standaard naam of aanpassen. |
    | Pad voor publiceren | Geef de variabele op `$(Build.ArtifactStagingDirectory)` . Zie [Build Varia bles](/azure/devops/pipelines/build/variables#build-variables) voor meer informatie. |
    | Naam van het artefact | Standaard naam gebruiken: **verwijderen** |
    | Publicatie locatie artefact | De standaard locatie gebruiken: **Azure-pijp lijnen** |

11. Open het tabblad **Triggers** en schakel het selectie vakje in om **continue integratie in te scha kelen**. Zorg ervoor dat de vertakking met uw code is opgenomen.

    ![Trigger voor continue integratie inschakelen](./media/how-to-continuous-integration-continuous-deployment-classic/configure-trigger.png)

12. Selecteer **Opslaan** in de vervolg keuzelijst **& wachtrij opslaan** .

Deze pijp lijn is nu geconfigureerd om automatisch te worden uitgevoerd wanneer u nieuwe code naar uw opslag plaats pusht. De laatste taak, die pijp lijn artefacten publiceert, een release pijplijn wordt geactiveerd. Ga door naar de volgende sectie om de release pijplijn te bouwen.

[!INCLUDE [iot-edge-create-release-pipeline-for-continuous-deployment](../../includes/iot-edge-create-release-pipeline-for-continuous-deployment.md)]

>[!NOTE]
>Als u **gelaagde implementaties** wilt gebruiken in uw pijp lijn, worden gelaagde implementaties nog niet ondersteund in azure IOT Edge taken in azure DevOps.
>
>U kunt echter een [Azure cli-taak in azure DevOps](/azure/devops/pipelines/tasks/deploy/azure-cli) gebruiken om uw implementatie te maken als een gelaagde implementatie. Voor de **inline-script** waarde kunt u de [opdracht AZ IOT Edge Deployment Create](/cli/azure/ext/azure-iot/iot/edge/deployment)gebruiken:
>
>   ```azurecli-interactive
>   az iot edge deployment create -d {deployment_name} -n {hub_name} --content modules_content.json --layered true
>   ```

[!INCLUDE [iot-edge-verify-iot-edge-continuous-integration-continuous-deployment](../../includes/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment.md)]

## <a name="next-steps"></a>Volgende stappen

* IoT Edge DevOps best practices- [voor beeld in azure DevOps starter voor IOT Edge](how-to-devops-starter.md)
* Inzicht in de implementatie van IoT Edge in [IOT Edge implementaties voor één of op een bepaalde schaal](module-deployment-monitoring.md)
* Door loop de stappen om een implementatie te maken, bij te werken of te verwijderen in [IOT Edge modules implementeren en bewaken op schaal](how-to-deploy-at-scale.md).
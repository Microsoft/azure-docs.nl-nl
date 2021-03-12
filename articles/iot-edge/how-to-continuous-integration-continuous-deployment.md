---
title: Continue integratie en continue implementatie naar Azure IoT Edge-apparaten-Azure IoT Edge
description: Continue integratie en continue implementatie instellen met behulp van YAML-Azure IoT Edge met Azure DevOps, Azure-pijp lijnen
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d9db9997af20fee226214eb12ad32729cab55caa
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103199255"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge-devices"></a>Continue integratie en continue implementatie naar Azure IoT Edge-apparaten

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

U kunt eenvoudig DevOps met uw Azure IoT Edge-toepassingen met de ingebouwde Azure IoT Edge taken in azure-pijp lijnen. In dit artikel wordt beschreven hoe u de functies continue integratie en continue implementatie van Azure-pijp lijnen kunt gebruiken om toepassingen snel en efficiënt op uw Azure IoT Edge te bouwen, testen en implementeren met behulp van YAML. U kunt ook [de klassieke editor gebruiken](how-to-continuous-integration-continuous-deployment-classic.md).

![Diagram-CI-en CD-vertakkingen voor ontwikkeling en productie](./media/how-to-continuous-integration-continuous-deployment/model.png)

In dit artikel leert u hoe u de ingebouwde [Azure IOT Edge taken](/azure/devops/pipelines/tasks/build/azure-iot-edge) voor Azure-pijp lijnen kunt gebruiken voor het maken van builds en release pijplijnen voor uw IOT EDGE oplossing. Elke Azure IoT Edge taak die aan uw pijp lijn is toegevoegd, implementeert een van de volgende vier acties:

 | Bewerking | Beschrijving |
 | --- | --- |
 | Module installatie kopieën bouwen | Neemt uw IoT Edge oplossings code en bouwt de container installatie kopieën.|
 | Installatie kopieën push module | Pusht installatie kopieën van module naar het container register dat u hebt opgegeven. |
 | Implementatie manifest genereren | Er wordt een deployment.template.jsin het bestand en de variabelen en vervolgens wordt het definitieve manifest bestand voor IoT Edge implementatie gegenereerd. |
 | Implementeren naar IoT Edge-apparaten | Hiermee maakt u IoT Edge-implementaties op een of meer IoT Edge apparaten. |

Tenzij anders aangegeven, verkennen de procedures in dit artikel niet alle beschik bare functies via taak parameters. Raadpleeg de volgende artikelen voor meer informatie:

* [Taak versie](/azure/devops/pipelines/process/tasks?tabs=yaml#task-versions)
* **Geavanceerd** : Geef modules op die u niet wilt maken, indien van toepassing.
* [Beheer opties](/azure/devops/pipelines/process/tasks?tabs=yaml#task-control-options)
* [Omgevings variabelen](/azure/devops/pipelines/process/variables?tabs=yaml#environment-variables)
* [Uitvoer variabelen](/azure/devops/pipelines/process/variables?tabs=yaml#use-output-variables-from-tasks)

## <a name="prerequisites"></a>Vereisten

* Een Azure opslag plaatsen-opslag plaats. Als u er nog geen hebt, kunt u [een nieuwe Git-opslag plaats in uw project maken](/azure/devops/repos/git/create-new-repo). Voor dit artikel hebt u een opslag plaats gemaakt met de naam **IoTEdgeRepo**.
* Een IoT Edge oplossing die is doorgevoerd en gepusht naar uw opslag plaats. Als u een nieuwe voorbeeld oplossing wilt maken voor het testen van dit artikel, volgt u de stappen in [modules voor ontwikkel-en fout opsporing in Visual Studio code](how-to-vs-code-develop-module.md) of [ontwikkel en debug C#-modules in Visual Studio](./how-to-visual-studio-develop-module.md). Voor dit artikel hebben we een oplossing in onze opslag plaats gemaakt met de naam **IoTEdgeSolution**, die de code bevat voor een module met de naam **filtermodule**.

   Voor dit artikel hoeft u alleen de map Solution te maken die is gemaakt door de IoT Edge sjablonen in Visual Studio code of Visual Studio. U hoeft deze code niet te bouwen, te pushen, te implementeren of te debuggen voordat u doorgaat. Deze processen worden ingesteld in azure-pijp lijnen.

   Als u een nieuwe oplossing wilt maken, moet u uw opslag plaats lokaal eerst klonen. Wanneer u de oplossing maakt, kunt u ervoor kiezen om deze rechtstreeks in de map opslag plaats te maken. U kunt de nieuwe bestanden eenvoudig van daaruit door voeren en pushen.

* Een container register waar u module installatie kopieën kunt pushen. U kunt [Azure container Registry](../container-registry/index.yml) of een REGI ster van derden gebruiken.
* Een actieve Azure [IOT-hub](../iot-hub/iot-hub-create-through-portal.md) met ten minste twee IOT edge-apparaten voor het testen van de afzonderlijke test-en productie-implementatie fasen. U kunt de Quick Start-artikelen volgen om een IoT Edge apparaat te maken in [Linux](quickstart-linux.md) of [Windows](quickstart.md)

Zie [uw code delen met Visual Studio en Azure opslag plaatsen](/azure/devops/repos/git/share-your-code-in-git-vs) voor meer informatie over het gebruik van Azure opslag plaatsen

## <a name="create-a-build-pipeline-for-continuous-integration"></a>Een build-pijp lijn maken voor continue integratie

In deze sectie maakt u een nieuwe build-pijp lijn. U configureert de pijp lijn zo dat deze automatisch wordt uitgevoerd wanneer u wijzigingen aanbrengt in de voor beeld-IoT Edge oplossing en het publiceren van build-logboeken publiceert.

1. Meld u aan bij uw Azure DevOps-organisatie ( `https://dev.azure.com/{your organization}` ) en open het project met uw IOT Edge Solution-opslag plaats.

   ![Open uw DevOps-project](./media/how-to-continuous-integration-continuous-deployment/initial-project.png)

2. Selecteer in het menu van het linkerdeel venster in uw project **pijp lijnen**. Selecteer **pijp lijn maken** in het midden van de pagina. Als u al een pijp lijn hebt gemaakt, selecteert u de knop **nieuwe pijp lijn** in de rechter bovenhoek.

    ![Een nieuwe build-pijp lijn maken met behulp van de knop nieuwe pijp lijn](./media/how-to-continuous-integration-continuous-deployment/add-new-pipeline.png)

3. Selecteer op de pagina **waar is uw code? de** optie **Azure opslag plaatsen `YAML` Git**. Als u de klassieke editor wilt gebruiken om de build-pijp lijnen van uw project te maken, raadpleegt u de [klassieke hand leiding](how-to-continuous-integration-continuous-deployment-classic.md)voor de editor.

4. Selecteer de opslag plaats waarvoor u een pijp lijn maakt.

    ![Selecteer de opslag plaats voor uw build-pijp lijn](./media/how-to-continuous-integration-continuous-deployment/select-repository.png)

5. Selecteer op de pagina **uw pijplijn configureren** de optie **Start pijp lijn**. Als u een bestaand YAML-bestand van Azure-pijp lijnen hebt dat u wilt gebruiken om deze pijp lijn te maken, kunt u **bestaand yaml-bestand van de Azure-pijp lijnen** selecteren en de vertakking en het pad naar het bestand in de opslag plaats opgeven.

    ![Selecteer een starter-pijp lijn of een bestaand YAML-bestand van Azure-pijp lijnen om uw build-pijp lijn te starten](./media/how-to-continuous-integration-continuous-deployment/configure-pipeline.png)

6. Op de pagina **uw PIJPLIJN yaml controleren** kunt u op de standaard naam klikken `azure-pipelines.yml` om de naam van het configuratie bestand van de pijp lijn te wijzigen.

   Selecteer **assistent weer geven** om het palet **taken** te openen.

    ![Selecteer assistent weer geven voor het openen van taken palet](./media/how-to-continuous-integration-continuous-deployment/show-assistant.png)

7. Als u een taak wilt toevoegen, plaatst u de cursor aan het einde van de YAML of op de plaats waar u de instructies voor de taak wilt invoegen. Zoek en selecteer **Azure IOT Edge**. Vul de para meters van de taak als volgt in. Selecteer vervolgens **toevoegen**.

   | Parameter | Beschrijving |
   | --- | --- |
   | Actie | Selecteer **installatie kopieën van module bouwen**. |
   | .template.jsvoor bestand | Geef het pad op naar het **deployment.template.js** bestand in de opslag plaats met uw IOT EDGE oplossing. |
   | Standaard platform | Selecteer het juiste besturings systeem voor uw modules op basis van uw doel-IoT Edge apparaat. |

    ![Taken palet gebruiken om taken toe te voegen aan uw pijp lijn](./media/how-to-continuous-integration-continuous-deployment/add-build-task.png)

   >[!TIP]
   > Nadat elke taak is toegevoegd, worden de toegevoegde regels automatisch gemarkeerd door de editor. Als u onbedoeld overschrijven wilt voor komen, deselecteert u de regels en geeft u een nieuwe ruimte voor uw volgende taak op voordat u extra taken toevoegt.

8. Herhaal dit proces om nog drie taken toe te voegen met de volgende para meters:

   * Taak: **Azure IOT Edge**

       | Parameter | Beschrijving |
       | --- | --- |
       | Actie | Selecteer **installatie kopieën van push module**. |
       | Container register type | Gebruik het standaard type: **Azure container Registry**. |
       | Azure-abonnement | Selecteer uw abonnement. |
       | Azure Container Registry | Kies het REGI ster dat u wilt gebruiken voor de pijp lijn. |
       | .template.jsvoor bestand | Geef het pad op naar het **deployment.template.js** bestand in de opslag plaats met uw IOT EDGE oplossing. |
       | Standaard platform | Selecteer het juiste besturings systeem voor uw modules op basis van uw doel-IoT Edge apparaat. |

   * Taak: **bestanden kopiëren**

       | Parameter | Beschrijving |
       | --- | --- |
       | Bronmap | De bronmap waaruit moet worden gekopieerd. Empty is de hoofdmap van de opslag plaats. Variabelen gebruiken als bestanden zich niet in de opslag plaats bevinden. Bijvoorbeeld: `$(agent.builddirectory)`.
       | Inhoud | Twee regels toevoegen: `deployment.template.json` en `**/module.json` . |
       | Doelmap | Geef de variabele op `$(Build.ArtifactStagingDirectory)` . Zie [Build Varia bles](/azure/devops/pipelines/build/variables?tabs=yaml#build-variables) voor meer informatie over de beschrijving. |

   * Taak: **Build-artefacten publiceren**

       | Parameter | Beschrijving |
       | --- | --- |
       | Pad voor publiceren | Geef de variabele op `$(Build.ArtifactStagingDirectory)` . Zie [Build Varia bles](/azure/devops/pipelines/build/variables?tabs=yaml#build-variables) voor meer informatie over de beschrijving. |
       | Naam van het artefact | Geef de standaard naam op: `drop` |
       | Publicatie locatie artefact | De standaard locatie gebruiken: `Azure Pipelines` |

9. Selecteer **Opslaan** in de vervolg keuzelijst **opslaan en uitvoeren** in de rechter bovenhoek.

10. De trigger voor continue integratie is standaard ingeschakeld voor uw YAML-pijp lijn. Als u deze instellingen wilt bewerken, selecteert u de pijp lijn en klikt u op **bewerken** in de rechter bovenhoek. Selecteer **meer acties** naast de knop **uitvoeren** in de rechter bovenhoek en ga naar **Triggers**. **Continue integratie** wordt weer gegeven als ingeschakeld onder de naam van de pijp lijn. Als u de details van de trigger wilt weer geven, schakelt u het selectie vakje **de trigger voor continue integratie van yaml negeren van** het vak in.

    ![Zie Triggers onder meer acties om de trigger instellingen van uw pijp lijn te bekijken](./media/how-to-continuous-integration-continuous-deployment/check-trigger-settings.png)

Ga door naar de volgende sectie om de release pijplijn te bouwen.

[!INCLUDE [iot-edge-create-release-pipeline-for-continuous-deployment](../../includes/iot-edge-create-release-pipeline-for-continuous-deployment.md)]

[!INCLUDE [iot-edge-verify-iot-edge-continuous-integration-continuous-deployment](../../includes/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment.md)]

## <a name="next-steps"></a>Volgende stappen

* IoT Edge DevOps best practices- [voor beeld in azure DevOps starter voor IOT Edge](how-to-devops-starter.md)
* Inzicht in de implementatie van IoT Edge in [IOT Edge implementaties voor één of op een bepaalde schaal](module-deployment-monitoring.md)
* Door loop de stappen om een implementatie te maken, bij te werken of te verwijderen in [IOT Edge modules implementeren en bewaken op schaal](how-to-deploy-at-scale.md).
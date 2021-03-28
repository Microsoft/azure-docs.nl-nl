---
title: Azure DevOps gebruiken om een CI/CD-pijp lijn te maken voor een Stream Analytics-taak
description: In dit artikel wordt beschreven hoe u een doorlopende integratie-en implementatie pijplijn (CI/CD) instelt voor een Azure Stream Analytics-taak in azure DevOps
services: stream-analytics
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 09/10/2020
ms.openlocfilehash: 82a2c3047f851c9fbc273cd13e730572c38b6bcd
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105640381"
---
# <a name="use-azure-devops-to-create-a-cicd-pipeline-for-a-stream-analytics-job"></a>Azure DevOps gebruiken om een CI/CD-pijp lijn te maken voor een Stream Analytics-taak

In dit artikel leert u hoe u met Azure Stream Analytics CI/CD-hulpprogram ma's Azure DevOps [Build](/azure/devops/pipelines/get-started/pipelines-get-started) -en [release](/azure/devops/pipelines/release/define-multistage-release-process) -pijp lijnen kunt maken.

## <a name="commit-your-stream-analytics-project"></a>Uw Stream Analytics-project door voeren

Voordat u begint, moet u uw complete Stream Analytics-projecten als bron bestanden door voeren in een [Azure DevOps](/azure/devops/user-guide/source-control) -opslag plaats. U kunt verwijzen naar deze [voorbeeld opslagplaats](https://dev.azure.com/ASA-CICD-sample/azure-streamanalytics-cicd-demo) en [Stream Analytics project bron code](https://dev.azure.com/ASA-CICD-sample/_git/azure-streamanalytics-cicd-demo) in azure-pijp lijnen.

In de stappen in dit artikel wordt gebruikgemaakt van een Stream Analytics Visual Studio code-project. Als u een Visual Studio-project gebruikt, volgt u de stappen in [builds, tests en implementaties van een Azure stream Analytics-taak automatiseren met CI/cd-hulpprogram ma's](cicd-tools.md).

## <a name="create-a-build-pipeline"></a>Een build-pijplijn maken

In deze sectie leert u hoe u een build-pijp lijn maakt. 

1. Open een webbrowser en navigeer naar uw project in azure DevOps.  

2. Selecteer **builds** onder **pijp lijnen** in het navigatie menu aan de linkerkant. Selecteer vervolgens **nieuwe pijp lijn**.

   :::image type="content" source="media/set-up-cicd-pipeline/new-pipeline.png" alt-text="Nieuwe Azure-pijp lijn maken":::

3. Selecteer **de klassieke editor gebruiken** om een pijp lijn zonder yaml te maken.

4. Selecteer het bron type, het team project en de opslag plaats. Selecteer vervolgens **Doorgaan**.

   :::image type="content" source="media/set-up-cicd-pipeline/select-repo.png" alt-text="Azure Stream Analytics project selecteren":::

5. Selecteer op de pagina **een sjabloon kiezen** de optie **lege taak**.

## <a name="install-npm-package"></a>Npm-pakket installeren

1. Selecteer op de pagina **taken** het plus teken naast **Agent taak 1**. Voer *NPM* in de taak zoeken in en selecteer **NPM**.

   :::image type="content" source="media/set-up-cicd-pipeline/search-npm.png" alt-text="NPM-taak selecteren":::

2. Geef de taak een **weergave naam**. Wijzig de **opdracht** optie in *aangepast* en voer de volgende opdracht in: **opdracht en argumenten**. Wijzig de overige standaard opties.

   ```bash
   install -g azure-streamanalytics-cicd
   ```

   :::image type="content" source="media/set-up-cicd-pipeline/npm-config.png" alt-text="Configuraties voor NPM-taak invoeren":::

Gebruik de volgende stappen als u een gehoste Linux-agent moet gebruiken:
1.  De **agent specificatie** selecteren
   
    :::image type="content" source="media/set-up-cicd-pipeline/select-linux-agent.png" alt-text="Scherm opname van het selecteren van de agent specificatie.":::

2.  Selecteer op de pagina **taken** het plus teken naast **Agent taak 1**. Voer de *opdracht regel* in in de taak zoeken en selecteer **opdracht regel**.
   
    :::image type="content" source="media/set-up-cicd-pipeline/cmd-search.png" alt-text="Scherm opname van de opdracht regel voor zoeken. ":::

3.  Geef de taak een **weergave naam**. Voer de volgende opdracht in het **script** in. Wijzig de overige standaard opties.

      ```bash
      sudo npm install -g azure-streamanalytics-cicd --unsafe-perm=true --allow-root
      ```
      :::image type="content" source="media/set-up-cicd-pipeline/cmd-scripts.png" alt-text="Scherm opname van het invoeren van een script voor de cmd-taak.":::

## <a name="add-a-build-task"></a>Een bouw taak toevoegen

1. Selecteer op de pagina **variabelen** **+ toevoegen** in **pijplijn variabelen**. Voeg de volgende variabelen toe. Stel de volgende waarden in op basis van uw voor keur:

   |Naam van de variabele|Waarde|
   |-|-|
   |projectRootPath|YourProjectName|
   |outputPath|Uitvoer|
   |deployPath|Implementeren|

2. Selecteer op de pagina **taken** het plus teken naast **Agent taak 1**. Zoeken naar **opdracht regel**.

3. Geef de taak een **weergave naam** en voer het volgende script in. Wijzig het script met de naam van uw opslag plaats en de project naam.

   ```bash
   azure-streamanalytics-cicd build -project $(projectRootPath)/asaproj.json -outputpath $(projectRootPath)/$(outputPath)/$(deployPath)
   ```

   In de onderstaande afbeelding wordt een Stream Analytics Visual Studio-code project als voor beeld gebruikt.

   :::image type="content" source="media/set-up-cicd-pipeline/command-line-config-build.png" alt-text="Configuraties voor de opdracht regel taak Visual Studio code invoeren":::

## <a name="add-a-test-task"></a>Een test taak toevoegen

1. Selecteer op de pagina **variabelen** **+ toevoegen** in **pijplijn variabelen**. Voeg de volgende variabelen toe. Wijzig de waarden met het uitvoerpad en de naam van de opslag plaats.

   |Naam van de variabele|Waarde|
   |-|-|
   |testPath|Testen|

   :::image type="content" source="media/set-up-cicd-pipeline/pipeline-variables-test.png" alt-text="Pijplijn variabelen toevoegen":::

2. Selecteer op de pagina **taken** het plus teken naast **Agent taak 1**. Zoeken naar **opdracht regel**.

3. Geef de taak een **weergave naam** en voer het volgende script in. Wijzig het script met de bestands naam van uw project en het pad naar het configuratie bestand voor de test. 

   Zie [geautomatiseerde test instructies](cicd-tools.md#automated-test) voor meer informatie over het toevoegen en configureren van test cases.

   ```bash
   azure-streamanalytics-cicd test -project $(projectRootPath)/asaproj.json -outputpath $(projectRootPath)/$(outputPath)/$(testPath) -testConfigPath $(projectRootPath)/test/testConfig.json 
   ```

   :::image type="content" source="media/set-up-cicd-pipeline/command-line-config-test.png" alt-text="Configuraties voor de opdracht regel taak invoeren":::

## <a name="add-a-copy-files-task"></a>Een taak voor het kopiëren van bestanden toevoegen

U moet een bestands taak kopiëren toevoegen om het bestand met de test samenvatting en Azure Resource Manager sjabloon bestanden te kopiëren naar de map artefacten. 

1. Selecteer op de pagina **taken** de optie **+** naast **Agent taak 1**. Zoeken naar **Kopieer bestanden**. Voer vervolgens de volgende configuraties in. Door `**` toe te wijzen aan **inhoud**, worden alle bestanden van de test resultaten gekopieerd.

   |Parameter|Invoer|
   |-|-|
   |Weergavenaam|Bestanden kopiëren naar: $ (build. artifactstagingdirectory)|
   |Bronmap|`$(system.defaultworkingdirectory)/$(outputPath)/`|
   |Inhoud| `**` |
   |Doelmap| `$(build.artifactstagingdirectory)`|

2. Vouw de **besturings opties** uit. Selecteer **zelfs als een vorige taak is mislukt, tenzij de build is geannuleerd** tijdens het **uitvoeren van deze taak**.

   :::image type="content" source="media/set-up-cicd-pipeline/copy-config.png" alt-text="Configuraties voor kopieer taak invoeren":::

## <a name="add-a-publish-build-artifacts-task"></a>Een taak voor het bouwen van een publicatie maken toevoegen

1. Selecteer op de pagina **taken** het plus teken naast **Agent taak 1**. Zoek naar **Build-artefacten publiceren** en selecteer de optie met het pictogram met de zwarte pijl.

2. Vouw de **besturings opties** uit. Selecteer **zelfs als een vorige taak is mislukt, tenzij de build is geannuleerd** tijdens het **uitvoeren van deze taak**.

   :::image type="content" source="media/set-up-cicd-pipeline/publish-config.png" alt-text="Configuraties voor publicatie taak invoeren":::

## <a name="save-and-run"></a>Opslaan en uitvoeren

Wanneer u klaar bent met het toevoegen van het NPM-pakket, de opdracht regel, het kopiëren van bestanden en het publiceren van opbouw artefacten, selecteert u **& wachtrij opslaan**. Wanneer u hierom wordt gevraagd, voert u een opmerking opslaan in en selecteert u **opslaan en uitvoeren**. U kunt de test resultaten downloaden van de pagina **samen vatting** van de pijp lijn.

## <a name="check-the-build-and-test-results"></a>De resultaten van de build en test controleren

Het bestand met de test samenvatting en Azure Resource Manager sjabloon bestanden vindt u in de **gepubliceerde** map.

   :::image type="content" source="media/set-up-cicd-pipeline/check-build-test-result.png" alt-text="Het build-en test resultaat controleren":::

   :::image type="content" source="media/set-up-cicd-pipeline/check-drop-folder.png" alt-text="Artefacten controleren":::

## <a name="release-with-azure-pipelines"></a>Release met Azure-pijp lijnen

In deze sectie leert u hoe u een release pijplijn maakt. 

Open een webbrowser en navigeer naar uw Azure Stream Analytics Visual Studio code-project.

1. Selecteer onder **pijp lijnen** in het navigatie menu aan de linkerkant de optie **releases**. Selecteer vervolgens **nieuwe pijp lijn**.

2. Selecteer **beginnen met een lege taak**.

3. Selecteer in het vak **artefacten** **+ een artefact toevoegen**. Selecteer onder **bron** de build-pijp lijn die u hebt gemaakt en selecteer **toevoegen**.

   :::image type="content" source="media/set-up-cicd-pipeline/build-artifact.png" alt-text="Bouw pijplijn artefact invoeren":::

4. Wijzig de naam van **fase 1** in de implementatie van de **taak in de test omgeving**.

5. Voeg een nieuwe fase toe en geef deze de naam **implementatie taak aan de productie omgeving**.

### <a name="add-deploy-tasks"></a>Implementatie taken toevoegen

1. Selecteer in de vervolg keuzelijst taken de optie **taak implementeren naar test omgeving**.

2. Selecteer de **+** taak volgende bij **agent** en zoek naar **arm-sjabloon implementatie**. Voer de volgende parameters in:

   |Parameter|Waarde|
   |-|-|
   |Weergavenaam| *MyASAProject implementeren*|
   |Azure-abonnement| Kies uw abonnement.|
   |Bewerking| *Resourcegroep maken of bijwerken*|
   |Resourcegroep| Kies een naam voor de test resource groep die uw Stream Analytics-taak bevat.|
   |Locatie|Kies de locatie van de test resource groep.|
   |Sjabloonlocatie| Gekoppeld artefact|
   |Sjabloon| $ (System. DefaultWorkingDirectory)/_azure-streamanalytics-cicd-demo-CI-implementeren/drop/myASAProject.JobTemplate.jsop |
   |Sjabloonparameters|$ (System. DefaultWorkingDirectory)/_azure-streamanalytics-cicd-demo-CI-implementeren/drop/myASAProject.JobTemplate.parameters.jsop |
   |Sjabloonparameters overschrijven|-<arm_template_parameter> ' uw waarde '. U kunt de para meters definiëren met behulp van **variabelen**.|
   |Implementatiemodus|Incrementeel|

3. Selecteer in de vervolg keuzelijst taken de optie **taak op productie omgeving implementeren**.

4. Selecteer de **+** taak volgende bij **agent** en zoek naar *arm-sjabloon implementatie*. Voer de volgende parameters in:

   |Parameter|Waarde|
   |-|-|
   |Weergavenaam| *MyASAProject implementeren*|
   |Azure-abonnement| Kies uw abonnement.|
   |Bewerking| *Resourcegroep maken of bijwerken*|
   |Resourcegroep| Kies een naam voor de productie resource groep die uw Stream Analytics-taak bevat.|
   |Locatie|Kies de locatie van de productie resource groep.|
   |Sjabloonlocatie| *Gekoppeld artefact*|
   |Sjabloon| $ (System. DefaultWorkingDirectory)/_azure-streamanalytics-cicd-demo-CI-implementeren/drop/myASAProject.JobTemplate.jsop |
   |Sjabloonparameters|$ (System. DefaultWorkingDirectory)/_azure-streamanalytics-cicd-demo-CI-implementeren/drop/myASAProject.JobTemplate.parameters.jsop |
   |Sjabloonparameters overschrijven|-<arm_template_parameter> ' uw waarde '|
   |Implementatiemodus|Incrementeel|

### <a name="create-a-release"></a>Een release maken

Als u een release wilt maken, selecteert u **vrijgave maken** in de rechter bovenhoek.

:::image type="content" source="media/set-up-cicd-pipeline/create-release.png" alt-text="Een release maken met behulp van Azure-pijp lijnen":::

## <a name="next-steps"></a>Volgende stappen

* [Continue integratie en continue implementatie voor Azure Stream Analytics](cicd-overview.md)
* [Bouwen, testen en implementeren van een Azure Stream Analytics taak automatiseren met CI/CD-hulpprogram ma's](cicd-tools.md)
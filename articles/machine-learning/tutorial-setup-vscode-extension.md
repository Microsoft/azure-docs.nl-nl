---
title: 'Zelfstudie: De Go-extensie voor Visual Studio Code instellen (preview)'
titleSuffix: Azure Machine Learning
description: Meer informatie over het installeren en uitvoeren van trainingsscripts met de Azure Machine Learning Visual Studio Code-extensie.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: luisquintanilla
ms.author: luquinta
ms.date: 11/16/2020
ms.openlocfilehash: f485c12bf82dde798a77175a3fab4d76488150c3
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701198"
---
# <a name="set-up-azure-machine-learning-visual-studio-code-extension-preview"></a>Azure Machine Learning Visual Studio Code-extensie (preview) instellen

Meer informatie over het installeren en uitvoeren van scripts met de Azure Machine Learning Visual Studio Code-extensie.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De Azure Machine Learning Visual Studio Code-extensie installeren
> * Meld u aan bij uw Azure-account vanuit Visual Studio Code
> * De extensie Azure Machine Learning gebruiken om een voorbeeldscript uit te voeren

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement. Als u nog geen abonnement heeft, registreert u zich of probeer de [gratis of betaalde versie van Azure Machine Learning](https://aka.ms/AMLFree).
- Visual Studio Code. Als u het nog niet hebt, moet u dit eerst [installeren](https://code.visualstudio.com/docs/setup/setup-overview).
- [Python 3](https://www.python.org/downloads/)

## <a name="install-the-extension"></a>De extensie installeren

1. Open Visual Studio Code.
1. Selecteer het pictogram **Extensies** van de **Activiteitenbalk** om de weergave Extensies te openen.
1. Zoek in de weergave extensies naar 'Azure Machine Learning'.
1. Selecteer **Installeren**.

    > [!div class="mx-imgBorder"]
    > ![Azure Machine Learning VS Code-extensie installeren](./media/tutorial-setup-vscode-extension/install-aml-vscode-extension.PNG)

> [!NOTE]
> U kunt de Azure Machine Learning-extensie ook installeren via de Visual Studio Marketplace door het [installatieprogramma rechtstreeks te downloaden](https://aka.ms/vscodetoolsforai). 

De overige stappen in deze zelfstudie zijn getest met **versie 0.6.8** van de extensie.

## <a name="sign-in-to-your-azure-account"></a>Meld u aan bij uw Azure-account

Als u resources wilt inrichten en workloads op Azure wilt uitvoeren, moet u zich aanmelden met de referenties van uw Azure-account. Azure Machine Learning installeert automatisch de Azure-accountextensie om met het accountbeheer te helpen. Ga naar de volgende site om [meer te weten te komen over de extensie van het Azure-account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

1. Open het opdrachtenpalet in door **Beeld > Opdrachtenpalet** te selecteren in het menu. 
1. Voer de opdracht 'Azure' in: Meld u aan bij het opdrachtenpalet om het aanmeldingsproces te starten.

## <a name="run-a-machine-learning-model-training-script-in-azure"></a>Een trainingsscript voor Machine Learning-model uitvoeren in Azure

Nu u bent aangemeld bij Azure met uw accountreferenties, gebruikt u de stappen in deze sectie om te leren hoe u de extensie gebruikt om een Machine Learning-model te trainen.

1. Down load en pak de [VS Code-hulpprogramma's voor AI-opslagplaatsen](https://github.com/microsoft/vscode-tools-for-ai/archive/master.zip) uit op een willekeurige locatie op uw computer.
1. Open de map `mnist-vscode-docs-sample` in Visual Studio Code.
1. Selecteer het pictogram **Azure** op de Activiteitenbalk.
1. Selecteer het pictogram **Experiment uitvoeren** boven aan de weergave Azure Machine Learning.

    > [!div class="mx-imgBorder"]
    > ![Experiment uitvoeren](./media/tutorial-setup-vscode-extension/run-experiment.PNG)

1. Wanneer het opdrachtpalet wordt uitgevouwen, volgt u de aanwijzingen.

    > [!NOTE]
    > Als u al bestaande Azure Machine Learning-resources hebt ingericht, raadpleegt u de [Procedures voor het uitvoeren van experimenten in VS Code-gids](./how-to-manage-resources-vscode.md#run-experiment).

    1. Selecteer uw Azure-abonnement.
    1. Selecteer in de lijst met omgevingen **Conda-afhankelijkheidsbestand**.
    1. Druk op **Enter** om door het Conda-afhankelijkheidsbestand te bladeren. Dit bestand bevat de afhankelijkheden die nodig zijn om het script uit te voeren. In dit geval is het bestand met afhankelijkheden het bestand `env.yml` in de map `mnist-vscode-docs-sample`.
    1. Druk op **Enter** om door het trainingsscriptbestand te bladeren. Dit is het bestand dat code bevat voor een Machine Learning-model waarmee afbeeldingen van handgeschreven cijfers worden gecategoriseerd. In dit geval is het script om het model te trainen het bestand `train.py` in de map `mnist-vscode-docs-sample`.

1. Op dit moment wordt een configuratiebestand dat lijkt op het onderstaande weergegeven in de teksteditor. De configuratie bevat de informatie die nodig is om de trainingstaak uit te voeren, zoals het bestand dat de code bevat voor het trainen van het model en eventuele Python-afhankelijkheden die zijn opgegeven in de vorige stap.

    ```json
    {
        "workspace": "WS06271500",
        "resourceGroup": "WS06271500-rg2",
        "location": "South Central US",
        "experiment": "WS06271500-exp1",
        "compute": {
            "name": "WS06271500-com1",
            "vmSize": "Standard_D1_v2, Cores: 1; RAM: 3.5GB;"
        },
        "runConfiguration": {
            "filename": "WS06271500-com1-rc1",
            "environment": {
                "name": "WS06271500-env1",
                "conda_dependencies": [
                    "python=3.6.2",
                    "tensorflow=1.15.0",
                    "pip"
                ],
                "pip_dependencies": [
                    "azureml-defaults"
                ],
                "environment_variables": {}
            }
        }
    }
    ```

1. Wanneer u tevreden bent met uw configuratie, verzendt u uw experiment door het opdrachtpalet te openen en de volgende opdracht in te voeren:

    ```text
    Azure ML: Submit Experiment
    ```

    Hiermee wordt het `train.py` en het configuratiebestand naar de Azure Machine Learning-werkruimte verzonden. Vervolgens wordt de trainingstaak gestart op een rekenresource in Azure.

### <a name="track-the-progress-of-the-training-script"></a>De voortgang van het trainingsscript volgen

Het uitvoeren van uw script kan enkele minuten duren. De voortgang volgen:

1. Selecteer het pictogram **Azure** vanuit de activiteitenbalk.
1. Vouw uw abonnementknooppunt uit.
1. Vouw het knooppunt van uw lopende experiment uit. Dit bevindt zich in het knoppunt `{workspace}/Experiments/{experiment}` waar de waarden voor uw werkruimte en experiment hetzelfde zijn als de eigenschappen die zijn gedefinieerd in het configuratiebestand.
1. Alle uitvoeringen voor het experiment worden vermeld, evenals hun status. Als u de meest recente status wilt weten, klikt u op het pictogram ‘vernieuwen’ boven aan de weergave Azure Machine Learning.

    > [!div class="mx-imgBorder"]
    > ![Voortgang van experiment volgen](./media/tutorial-setup-vscode-extension/track-experiment-progress.PNG)

### <a name="download-the-trained-model"></a>Het getrainde model downloaden

Wanneer de uitvoering van het experiment is voltooid, is de uitvoer een getraind model. De uitvoer lokaal downloaden:

1. Klik met de rechtermuisknop op de meest recente uitvoering en selecteer **Uitvoer downloaden**.

    > [!div class="mx-imgBorder"]
    > ![Getraind model downloaden](./media/tutorial-setup-vscode-extension/download-trained-model.PNG)

1. Selecteer een locatie waar de uitvoer moet worden opgeslagen.
1. Een map met de naam van uw uitvoering wordt lokaal gedownload. Navigeer naar die pagina.
1. De modelbestanden bevinden zich in de map `outputs/outputs/model`.

## <a name="next-steps"></a>Volgende stappen

* [Zelfstudie: Train en implementeer een TensorFlow-model voor de classificatie van een installatiekopie met behulp van de Azure Machine Learning Visual Studio Code-extensie](tutorial-train-deploy-image-classification-model-vscode.md).

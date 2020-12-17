---
title: 'Zelfstudie: Modellen trainen en implementeren: VS-code (preview-versie)'
titleSuffix: Azure Machine Learning
description: Meer informatie over het trainen en implementeren van een model voor de classificatie van afbeeldingen met behulp van TensorFlow en de Azure Machine Learning Visual Studio Code-extensie
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: luisquintanilla
ms.author: luquinta
ms.date: 07/09/2020
ms.custom: contperf-fy20q4
ms.openlocfilehash: 9ad96bdb632e134cf3e3a0f82bb97f88c87e72e9
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033439"
---
# <a name="train-and-deploy-an-image-classification-tensorflow-model-using-the-azure-machine-learning-visual-studio-code-extension-preview"></a>Een TensorFlow-model trainen en implementeren voor de classificatie van afbeeldingen met behulp van de Azure Machine Learning Visual Studio Code-extensie (preview-versie)

Meer informatie over het trainen en implementeren van een model voor de classificatie van afbeeldingen om handgeschreven cijfers te herkennen met behulp van TensorFlow en de Azure Machine Learning Visual Studio Code-extensie.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De code begrijpen
> * Een werkruimte maken
> * Een experiment maken
> * Rekendoelen configureren
> * Een configuratiebestand uitvoeren
> * Een model trainen
> * Een model registreren
> * Een model implementeren

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement. Als u nog geen abonnement heeft, registreert u zich of probeer de [gratis of betaalde versie van Azure Machine Learning](https://aka.ms/AMLFree).
- [Visual Studio Code](https://code.visualstudio.com/docs/setup/setup-overview) installeren, een lichte, platformoverschrijdende code-editor.
- De Azure Machine Learning Visual Studio Code-extensie. Raadpleeg voor installatie-instructies de zelfstudie [De Azure Machine Learning Visual Studio Code-extensie instellen](./tutorial-setup-vscode-extension.md)

## <a name="understand-the-code"></a>De code begrijpen

De code voor deze zelfstudie maakt gebruik van TensorFlow voor het trainen van een Machine Learning-model voor de classificatie van afbeeldingen dat handgeschreven cijfers van 0-9 categoriseert. Dit wordt gedaan door een neuraal netwerk te maken dat de pixelwaarden van een afbeelding van 28 px x 28 px als invoer gebruikt en een lijst van tien waarschijnlijkheden uitvoert, één voor elk van de cijfers die worden geclassificeerd. Hieronder ziet u een voorbeeld van hoe de gegevens er uitzien.  

![MNIST-cijfers](./media/tutorial-train-deploy-image-classification-model-vscode/digits.png)

Haal de code voor deze zelfstudie op door de [opslagplaats VS Code-hulpprogramma's voor AI](https://github.com/microsoft/vscode-tools-for-ai/archive/master.zip) op een willekeurige locatie op uw computer te downloaden en uit te pakken.

## <a name="create-a-workspace"></a>Een werkruimte maken

Het eerste wat u moet doen om een toepassing in Azure Machine Learning te bouwen, is een werkruimte maken. Een werkruimte bevat de resources voor het trainen van modellen en de getrainde modellen zelf. Zie [Wat is een werkruimte?](./concept-workspace.md) voor meer informatie. 

1. Selecteer op de activiteitenbalk van Visual Studio het pictogram **Azure** om de weergave Azure Machine Learning te openen.
1. Klik met de rechtermuisknop op uw Azure-abonnement en selecteer **Werkruimte maken**. 
    
    > [!div class="mx-imgBorder"]
    > ![Een werkruimte maken](./media/tutorial-train-deploy-image-classification-model-vscode/create-workspace.png)

1. Er wordt standaard een naam gegenereerd die de datum en tijd bevat waarop de werkruimte is gemaakt. Wijzig in het tekstinvoervak de naam in 'TeamWorkspace' en druk op **Enter**.
1. Selecteer **Een nieuwe resourcegroep maken**. 
1. Geef de resourcegroep de naam 'TeamWorkspace-rg' en druk op **Enter**. 
1. Kies een locatie voor uw werkruimte. Het wordt aanbevolen om een locatie te kiezen die het dichtst bij de locatie ligt die u voor het implementeren van uw model wilt gebruiken. Bijvoorbeeld 'VS - west 2'.
1. Wanneer u wordt gevraagd het type werkruimte te selecteren, kiest u **basis**.

Op dit punt wordt een aanvraag aan Azure gedaan om een nieuwe werkruimte in uw account te maken. Na een paar minuten wordt de nieuwe werkruimte weergegeven in uw abonnementknooppunt. 

## <a name="create-an-experiment"></a>Een experiment maken

In uw werkruimte kunnen een of meer experimenten worden gemaakt om de uitvoering van afzonderlijke modeltrainingen bij te houden en te analyseren. Uitvoeringen kunnen worden gedaan in de Azure Cloud of op uw lokale machine.

1. Selecteer op de activiteitenbalk van Visual Studio het pictogram **Azure**. De weergave Azure Machine Learning wordt geopend.
1. Vouw uw abonnementknooppunt uit.
1. Vouw het knooppunt **TeamWorkspace** uit. 
1. Klik met de rechtermuisknop op het knooppunt **Experimenten**.
1. Selecteer **Experiment maken** in het contextmenu.

    > [!div class="mx-imgBorder"]
    > ![Een experiment maken](./media/tutorial-train-deploy-image-classification-model-vscode/create-experiment.png)

1. Noem het experiment 'MNIST' en druk op **Enter** om het nieuwe experiment te maken. 

Net als bij werkruimten wordt een aanvraag verzonden naar Azure om een experiment te maken met de meegeleverde configuraties. Na een paar minuten wordt het nieuwe experiment weergegeven in het knooppunt *Experimenten* van uw werkruimte. 

## <a name="configure-compute-targets"></a>Rekendoelen configureren

Een rekendoel is de rekenresource of -omgeving waarin u scripts uitvoert en getrainde modellen implementeert. Zie de [documentatie over rekendoelen van Azure Machine Learning](./concept-compute-target.md) voor meer informatie.

Ga als volgt te werk om een rekendoel te maken:

1. Selecteer op de activiteitenbalk van Visual Studio het pictogram **Azure**. De weergave Azure Machine Learning wordt geopend. 
1. Vouw uw abonnementknooppunt uit. 
1. Vouw het knooppunt **TeamWorkspace** uit. 
1. Klik onder het werkruimteknooppunt met de rechtermuisknop op het knooppunt **Rekenclusters** en kies **Rekendoel maken**. 

    > [!div class="mx-imgBorder"]
    > ![Een rekendoel maken](./media/tutorial-train-deploy-image-classification-model-vscode/create-compute.png)

1. Selecteer **Azure Machine Learning Compute (AmlCompute)** . Azure Machine Learning Compute is een beheerde-rekeninfrastructuur waarmee de gebruiker eenvoudig een rekenproces met één of meerdere knooppunten kan maken dat met andere gebruikers in uw werkruimte kan worden gebruikt.
1. Kies een VM-grootte. Selecteer **Standard_F2s_v2** in de lijst met opties. De grootte van uw virtuele machine beïnvloedt de hoeveelheid tijd die nodig is om uw modellen te trainen. Zie [Grootten voor Linux Virtual Machines in Azure](../virtual-machines/sizes.md) voor meer informatie over VM-grootten.
1. Noem de compute 'TeamWkspc-com' en druk op **Enter** om uw rekenproces te maken.

    Er wordt een bestand in VS Code weergegeven met inhoud die er ongeveer als volgt uitziet:

    ```json
    {
        "location": "westus2",
        "tags": {},
        "properties": {
            "computeType": "AmlCompute",
            "description": "",
            "properties": {
                "vmSize": "Standard_F2s_v2",
                "vmPriority": "dedicated",
                "scaleSettings": {
                    "maxNodeCount": 4,
                    "minNodeCount": 0,
                    "nodeIdleTimeBeforeScaleDown": "PT120S"
                }
            }
        }
    }
    ```

1. Wanneer u tevreden bent met de configuratie, opent u het opdrachtpalet door **Beeld > Opdrachtpalet** te selecteren.
1. Voer de volgende opdracht in het opdrachtpalet in om uw uitvoeringsconfiguratiebestand op te slaan.

    ```text
    Azure ML: Save and Continue
    ```

Na een paar minuten wordt het nieuwe rekendoel weergegeven in het knooppunt *Rekenclusters* van uw werkruimte.

## <a name="create-a-run-configuration"></a>Een uitvoeringsconfiguratie maken

Wanneer u een training naar een rekendoel verzendt, verzendt u ook de configuratie die nodig is om de trainingstaak uit te voeren. Bijvoorbeeld het script dat de trainingscode en de Python-afhankelijkheden bevat die nodig zijn om het uit te voeren.

Ga als volgt te werk om een uitvoeringsconfiguratie maken:

1. Selecteer op de activiteitenbalk van Visual Studio het pictogram **Azure**. De weergave Azure Machine Learning wordt geopend. 
1. Vouw uw abonnementknooppunt uit. 
1. Vouw het knooppunt **TeamWorkspace > Rekenclusters** uit. 
1. Klik onder het rekenknooppunt met de rechtermuisknop op het knooppunt **TeamWkspc-com** en kies **Uitvoeringsconfiguratie maken**.

    > [!div class="mx-imgBorder"]
    > ![Een uitvoeringsconfiguratie maken](./media/tutorial-train-deploy-image-classification-model-vscode/create-run-configuration.png)

1. Geef uw uitvoeringsconfiguratie de naam 'MNIST-rc' en druk op **Enter** om uw uitvoeringsconfiguratie te maken.
1. Selecteer vervolgens **Nieuwe Azure ML-omgeving maken**. Omgevingen definiëren de afhankelijkheden die nodig zijn om uw scripts uit te voeren.
1. Geef uw omgeving de naam 'MNIST-env' en druk op **Enter**.
1. Selecteer **Conda-afhankelijkheidsbestand** in de lijst.
1. Druk op **Enter** om door het Conda-afhankelijkheidsbestand te bladeren. In dit geval is het bestand met afhankelijkheden het bestand `env.yml` in de map `vscode-tools-for-ai/mnist-vscode-docs-sample`.

    Er wordt een bestand in VS Code weergegeven met inhoud die er ongeveer als volgt uitziet:

    ```json
    {
        "name": "MNIST-env",
        "version": "1",
        "python": {
            "interpreterPath": "python",
            "userManagedDependencies": false,
            "condaDependencies": {
                "name": "vs-code-azure-ml-tutorial",
                "channels": [
                    "defaults"
                ],
                "dependencies": [
                    "python=3.6.2",
                    "tensorflow=1.15.0",
                    "pip",
                    {
                        "pip": [
                            "azureml-defaults"
                        ]
                    }
                ]
            },
            "baseCondaEnvironment": null
        },
        "environmentVariables": {},
        "docker": {
            "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
            "baseDockerfile": null,
            "baseImageRegistry": {
                "address": null,
                "username": null,
                "password": null
            },
            "enabled": false,
            "arguments": []
        },
        "spark": {
            "repositories": [],
            "packages": [],
            "precachePackages": true
        },
        "inferencingStackVersion": null
    }
    ```

1. Wanneer u tevreden bent met uw configuratie, slaat u deze op door het opdrachtpalet te openen en de volgende opdracht in te voeren:

    ```text
    Azure ML: Save and Continue
    ```

1. In dit voorbeeld wordt geen gegevensset gebruikt die is geregistreerd in Azure Machine Learning. Hier wordt de gegevensset geladen wanneer *train.py* wordt uitgevoerd. Wanneer u wordt gevraagd een gegevensreferentie te maken voor uw trainingsuitvoering, typt u 'n' in de prompt en drukt u op **Enter**.
1. Druk op **Enter** om door het scriptbestand te bladeren dat op de computer moet worden uitgevoerd. In dit geval is het script om het model te trainen het bestand `train.py` in de map `vscode-tools-for-ai/mnist-vscode-docs-sample`.

    Er wordt een bestand met de naam `MNIST-rc.runconfig` in VS Code weergegeven met inhoud die er ongeveer als volgt uitziet:

    ```json
    {
        "script": "train.py",
        "arguments": [],
        "framework": "Python",
        "communicator": "None",
        "target": "TeamWkspc-com",
        "environment": {
            "name": "MNIST-env",
            "version": "1",
            "python": {
                "interpreterPath": "python",
                "userManagedDependencies": false,
                "condaDependencies": {
                    "name": "vs-code-azure-ml-tutorial",
                    "channels": [
                        "defaults"
                    ],
                    "dependencies": [
                        "python=3.6.2",
                        "tensorflow=1.15.0",
                        "pip",
                        {
                            "pip": [
                                "azureml-defaults"
                            ]
                        }
                    ]
                },
                "baseCondaEnvironment": null
            },
            "environmentVariables": {},
            "docker": {
                "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
                "baseDockerfile": null,
                "baseImageRegistry": {
                    "address": null,
                    "username": null,
                    "password": null
                },
                "enabled": false,
                "arguments": []
            },
            "spark": {
                "repositories": [],
                "packages": [],
                "precachePackages": true
            },
            "inferencingStackVersion": null
        },
        "history": {
            "outputCollection": true,
            "snapshotProject": false,
            "directoriesToWatch": [
                "logs"
            ]
        }
    }
    ```

1. Wanneer u tevreden bent met uw configuratie, slaat u deze op door het opdrachtpalet te openen en de volgende opdracht in te voeren:

    ```text
    Azure ML: Save and Continue
    ```

De `MNIST-rc`-uitvoeringsconfiguratie wordt toegevoegd onder het rekenknooppunt *TeamWkspc-com* en de `MNIST-env`-omgevingsconfiguratie wordt toegevoegd onder het knooppunt *Omgevingen*.

## <a name="train-the-model"></a>Het model trainen

Tijdens het trainingsproces wordt een TensorFlow-model gemaakt door de verwerking van de trainingsgegevens en leerpatronen die erin zijn ingesloten voor elk van de desbetreffende cijfers die wordt geclassificeerd. 

Ga als volgt te werk om een Azure Machine Learning-experiment uit te voeren:

1. Selecteer op de activiteitenbalk van Visual Studio het pictogram **Azure**. De weergave Azure Machine Learning wordt geopend. 
1. Vouw uw abonnementknooppunt uit. 
1. Vouw het knooppunt **TeamWorkspace > Experimenten** uit. 
1. Klik met de rechtermuisknop op het **MNIST**-experiment.
1. Selecteer **Experiment uitvoeren**.

    > [!div class="mx-imgBorder"]
    > ![Een experiment uitvoeren](./media/tutorial-train-deploy-image-classification-model-vscode/run-experiment.png)

1. Selecteer in de lijst met rekendoelopties het rekendoel **TeamWkspc-com**.
1. Selecteer vervolgens de **MNIST-RC-** -uitvoeringsconfiguratie.
1. Op dit punt wordt een aanvraag verzonden naar Azure om uw experiment in het geselecteerde rekendoel in uw werkruimte uit te voeren. Dit proces duurt enkele minuten. De hoeveelheid tijd die nodig is om de trainingstaak uit te voeren, wordt beïnvloed door diverse factoren, zoals het rekentype en de grootte van de trainingsgegevens. Als u de voortgang van uw experiment wilt volgen, klikt u met de rechtermuisknop op het huidige uitvoeringsknooppunt en selecteert u **Uitvoering weergeven in de Azure-portal**.
1. Wanneer het dialoogvenster voor het openen van een externe website wordt weergegeven, selecteert u **Openen**.

    > [!div class="mx-imgBorder"]
    > ![Voortgang van experiment volgen](./media/tutorial-train-deploy-image-classification-model-vscode/track-experiment-progress.png)

Wanneer de training van het model is voltooid, wordt het statuslabel naast het uitvoeringsknooppunt bijgewerkt in 'Voltooid'.

## <a name="register-the-model"></a>Het model registreren

Nu u uw model hebt getraind, kunt u het registreren in uw werkruimte. 

Ga als volgt te werk om uw model te registreren:

1. Selecteer op de activiteitenbalk van Visual Studio het pictogram **Azure**. De weergave Azure Machine Learning wordt geopend.
1. Vouw uw abonnementknooppunt uit. 
1. Vouw het knooppunt **TeamWorkspace > Experimenten > MNIST** uit.
1. Haal de modeluitvoer op die is gegenereerd door het trainen van het model. Klik met de rechtermuisknop op het uitvoeringsknooppunt **Run 1** en selecteer **Uitvoer downloaden**. 

    > [!div class="mx-imgBorder"]
    > ![Modeluitvoer downloaden](./media/tutorial-train-deploy-image-classification-model-vscode/download-outputs.png)

1. Kies de map waarin u de gedownloade uitvoer wilt opslaan. Standaard wordt de uitvoer geplaatst in de map die momenteel is geopend in Visual Studio Code.
1. Klik met de rechtermuisknop op het knooppunt **Modellen** en kies **Model registreren**.

    > [!div class="mx-imgBorder"]
    > ![Een model registreren](./media/tutorial-train-deploy-image-classification-model-vscode/register-model.png)

1. Geef het model de naam 'MNIST-TensorFlow-model' en druk op **Enter**.
1. Een TensorFlow-model bestaat uit verschillende bestanden. Selecteer **Modelmap** als de indeling van het modelpad in de lijst met opties. 
1. Selecteer de map `azureml_outputs/Run_1/outputs/outputs/model`.

    Een bestand met uw modelconfiguraties wordt weergegeven in Visual Studio Code met vergelijkbare inhoud als hieronder:

    ```json
    {
        "modelName": "MNIST-TensorFlow-model",
        "tags": {
            "": ""
        },
        "modelPath": "c:\\Dev\\vscode-tools-for-ai\\mnist-vscode-docs-sample\\azureml_outputs\\Run_1\\outputs\\outputs\\model",
        "description": ""
    }
    ```

1. Wanneer u tevreden bent met uw configuratie, slaat u deze op door het opdrachtpalet te openen en de volgende opdracht in te voeren:

    ```text
    Azure ML: Save and Continue
    ```

Na een paar minuten wordt het model weergegeven onder het knooppunt *Modellen*.

## <a name="deploy-the-model"></a>Het model implementeren

In Visual Studio Code kunt u uw model als een webservice implementeren in:

+ Azure Container Instances (ACI).
+ Azure Kubernetes Service (AKS).

U hoeft geen ACI-container te maken om vooraf te testen, omdat ACI-containers indien nodig worden gemaakt. U moet AKS-clusters echter wel vooraf configureren. Zie [Modellen implementeren met Azure Machine Learning](how-to-deploy-and-where.md) voor meer informatie over implementatieopties.

Ga als volgt te werk om een webservice te implementeren als een ACI:

1. Selecteer op de activiteitenbalk van Visual Studio het pictogram **Azure**. De weergave Azure Machine Learning wordt geopend.
1. Vouw uw abonnementknooppunt uit. 
1. Vouw het knooppunt **TeamWorkspace > Modellen** uit. 
1. Klik met de rechtermuisknop op **MNIST-TensorFlow-model** en selecteer **Service implementeren op basis van geregistreerde model**.

    > [!div class="mx-imgBorder"]
    > ![Model implementeren](./media/tutorial-train-deploy-image-classification-model-vscode/deploy-model.png)

1. Selecteer **Azure Container Instances**.
1. Geef uw service de naam 'mnist-tensorflow-svc' en druk op **Enter**.
1. Kies het script dat moet worden uitgevoerd in de container door in het invoervak op **Enter** te drukken en naar het bestand `score.py` te bladeren in de map `mnist-vscode-docs-sample`.
1. Geef de afhankelijkheden op die nodig zijn om het script uit te voeren door in het invoervak op **Enter** te drukken en naar het bestand `env.yml` te bladeren in de map `mnist-vscode-docs-sample`.

    Een bestand met uw modelconfiguraties wordt weergegeven in Visual Studio Code met vergelijkbare inhoud als hieronder:

    ```json
    {
        "name": "mnist-tensorflow-svc",
        "imageConfig": {
            "runtime": "python",
            "executionScript": "score.py",
            "dockerFile": null,
            "condaFile": "env.yml",
            "dependencies": [],
            "schemaFile": null,
            "enableGpu": false,
            "description": ""
        },
        "deploymentConfig": {
            "cpu_cores": 1,
            "memory_gb": 10,
            "tags": {
                "": ""
            },
            "description": ""
        },
        "deploymentType": "ACI",
        "modelIds": [
            "MNIST-TensorFlow-model:1"
        ]
    }
    ```

1. Wanneer u tevreden bent met uw configuratie, slaat u deze op door het opdrachtpalet te openen en de volgende opdracht in te voeren:

    ```text
    Azure ML: Save and Continue
    ```

Op dit punt wordt een aanvraag verzonden naar Azure om uw webservice te implementeren. Dit proces duurt enkele minuten. Na de implementatie wordt de nieuwe service weergegeven onder het knooppunt *Eindpunten*.

## <a name="next-steps"></a>Volgende stappen

* Zie voor een overzicht van het trainen van Azure Machine Learning buiten Visual Studio Code [Zelfstudie: Modellen trainen met Azure Machine Learning](tutorial-train-models-with-aml.md).
* Voor een overzicht van het lokaal bewerken, uitvoeren en foutopsporing van code raadpleegt u de [zelfstudie over Python hello-world](https://code.visualstudio.com/docs/Python/Python-tutorial).
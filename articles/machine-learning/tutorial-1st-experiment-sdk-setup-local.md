---
title: 'Zelfstudie: Aan de slag met machine learning - Python'
titleSuffix: Azure Machine Learning
description: In deze zelfstudie gaat u aan de slag met de Azure Machine Learning SDK voor Python die wordt uitgevoerd op uw persoonlijke ontwikkelsomgeving.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: 2f33fe4fafbe194238fcfbd4942807ed2fc4d6ff
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98183537"
---
# <a name="tutorial-get-started-with-azure-machine-learning-in-your-development-environment-part-1-of-4"></a>Zelfstudie: Aan de slag met Azure Machine Learning in uw ontwikkelomgeving (deel 1 van 4)

In deze *vierdelige zelfstudiereeks* leert u de grondbeginselen van Azure Machine Learning en kunt u op taken gebaseerde Python machine learning-taken uitvoeren op het Azure-cloudplatform. 

In deel 1 van deze zelfstudiereeks doet u het volgende:

> [!div class="checklist"]
> * De Azure Machine Learning-SDK installeren.
> * De mapstructuur voor code instellen
> * Een Azure Machine Learning-werkruimte maken.
> * Uw lokale ontwikkelomgeving configureren.
> * Een rekencluster instellen.

> [!NOTE]
> Deze zelfstudie is gericht op de Azure Machine Learning-concepten die vereist zijn voor het indienen van **batch taken**: dit is de locatie waar de code wordt verzonden naar de cloud om te worden uitgevoerd op de achtergrond zonder tussenkomst van de gebruiker. Dit is handig voor voltooide scripts of code die u herhaaldelijk wilt uitvoeren, of voor rekenintensieve machine learning-taken. Als u meer geïnteresseerd bent in een verkennende werkstroom, kunt u in plaats daarvan [Jupyter of RStudio gebruiken op een Azure Machine Learning-rekenproces](tutorial-1st-experiment-sdk-setup.md).

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer [Azure Machine Learning](https://aka.ms/AMLFree) uit.
- [Anaconda](https://www.anaconda.com/download/) of [Miniconda](https://www.anaconda.com/download/) voor het beheren van virtuele Python-omgevingen en het installeren van pakketten.

## <a name="install-the-azure-machine-learning-sdk"></a>De Azure Machine Learning-SDK installeren

In deze zelfstudie maakt u gebruik van de Azure Machine Learning-SDK voor Python. Als u problemen met Python-afhankelijkheden wilt voorkomen, maakt u een geïsoleerde omgeving. Deze reeks zelfstudies maakt gebruik van Conda om die omgeving te maken. Als u liever andere oplossingen gebruikt, zoals `venv`, `virtualenv` of docker, moet u een Python-versie gebruiken vanaf versie 3.5 tot 3.9.

Controleer of Conda op uw systeem is geïnstalleerd:
    
```bash
conda --version
```
    
Als met deze opdracht een `conda not found`-fout wordt geretourneerd, moet u [Miniconda downloaden en installeren](https://docs.conda.io/en/latest/miniconda.html). 

Nadat u Conda hebt geïnstalleerd, gebruikt u een Terminal- of Anaconda-promptvenster om een nieuwe omgeving te maken:

```bash
conda create -n tutorial python=3.8
```

Installeer vervolgens de Azure Machine Learning SDK in de Conda-omgeving die u hebt gemaakt:

```bash
conda activate tutorial
pip install azureml-core
```
    
> [!NOTE]
> Het duurt ongeveer twee minuten voordat de Azure Machine Learning SDK-installatie is voltooid.
>
> Als er een fout met betrekking tot een time-out optreedt, probeert u in plaats daarvan `pip install --default-timeout=100 azureml-core`.


> [!div class="nextstepaction"]
> [Ik heb de SDK geïnstalleerd](?success=install-sdk#dir) [Er is een probleem opgetreden](https://www.research.net/r/7C8Z3DN?issue=install-sdk)

## <a name="create-a-directory-structure-for-code"></a><a name="dir"></a>Mapstructuur voor code maken

We raden aan dat u de volgende eenvoudige mapstructuur instelt voor deze zelfstudie:

```markdown
tutorial
└──.azureml
```

- `tutorial`: Map op het hoogste niveau van het project.
- `.azureml`: Verborgen submap om Azure Machine Learning-configuratiebestanden op te slaan.

> [!TIP]
> U kunt de verborgen submap .azureml in een terminalvenster maken.  Of u volgt de onderstaande stappen:
>
> * Gebruik **Command + Shift + .** in een Mac Finder-venster om de mogelijkheid in/uit te schakelen om mappen te zien en te maken die beginnen met een punt (.).  
> * In een Windows 10-bestandenverkenner raadpleegt u [Hoe kan ik verborgen bestanden en mappen weergeven?](https://support.microsoft.com/en-us/windows/view-hidden-files-and-folders-in-windows-10-97fbc472-c603-9d90-91d0-1166d1d9f4b5). 
> * Gebruik in de grafische interface van Linux **CTRL + h** of het menu **Weergave** en schakel het selectievakje **Verborgen bestanden weergeven** in.

> [!div class="nextstepaction"]
> [Ik heb een map gemaakt](?success=create-dir#workspace) [Er is een probleem opgetreden](https://www.research.net/r/7C8Z3DN?issue=create-dir)

## <a name="create-an-azure-machine-learning-workspace"></a><a name="workspace"></a>Een Azure Machine Learning-werkruimte maken

Een werkruimte is een resource op het hoogste niveau voor Azure Machine Learning en is een centrale locatie voor het volgende:

- Resources zoals Compute beheren.
- Assets opslaan zoals notebooks, omgevingen, gegevenssets, pijplijnen, modellen, eindpunten, enzovoort.
- Samenwerken met andere teamleden.

Voeg in de map op het hoogste niveau `tutorial` een nieuw Python-bestand toe met de naam `01-create-workspace.py` met behulp van de volgende code. Pas de parameters (naam, abonnements-id, resourcegroep en [locatie](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service) aan met uw voorkeuren.

U kunt de code uitvoeren in een interactieve sessie of als een Python-bestand.

>[!NOTE]
> Wanneer u een lokale ontwikkelomgeving (bijvoorbeeld uw computer) gebruikt, wordt u de eerste keer dat u de volgende code uitvoert gevraagd om u te verifiëren bij uw werkruimte met behulp van een *apparaatcode*. Volg de instructies op het scherm.

```python
# tutorial/01-create-workspace.py
from azureml.core import Workspace

ws = Workspace.create(name='<my_workspace_name>', # provide a name for your workspace
                      subscription_id='<azure-subscription-id>', # provide your subscription ID
                      resource_group='<myresourcegroup>', # provide a resource group name
                      create_resource_group=True,
                      location='<NAME_OF_REGION>') # For example: 'westeurope' or 'eastus2' or 'westus2' or 'southeastasia'.

# write out the workspace details to a configuration file: .azureml/config.json
ws.write_config(path='.azureml')
```

Voer, in het venster met de geactiveerde Conda-omgeving van *tutorial1*, deze code uit vanuit de map `tutorial`.

```bash
cd <path/to/tutorial>
python ./01-create-workspace.py
```

> [!TIP]
> Als er bij het uitvoeren van deze code een foutmelding optreedt dat u geen toegang hebt tot het abonnement, gaat u naar [Een werkruimte maken](how-to-manage-workspace.md?tab=python#create-multi-tenant) voor informatie over verificatieopties.


Nadat u *01-create-workspace.py* hebt uitgevoerd, ziet uw mapstructuur er als volgt uit:

```markdown
tutorial
└──.azureml
|  └──config.json
└──01-create-workspace.py
```

Het bestand `.azureml/config.json` bevat de metagegevens die nodig zijn om verbinding te maken met uw Azure Machine Learning-werkruimte. Anders gezegd, het bevat uw abonnements-ID, resourcegroep en de naam van uw werkruimte. 

> [!NOTE]
> De inhoud van `config.json` is niet geheim. Deze details mogen gedeeld worden.
>
> Verificatie is wel nog vereist om te communiceren met uw Azure Machine Learning-werkruimte.

> [!div class="nextstepaction"]
> [Ik heb een werkruimte gemaakt](?success=create-workspace#cluster) [Er is een probleem opgetreden](https://www.research.net/r/7C8Z3DN?issue=create-workspace)

## <a name="create-an-azure-machine-learning-compute-cluster"></a><a name="cluster"></a> Een Azure Machine Learning-rekencluster maken

Maak een Python-script in de map `tutorial` op het hoogste niveau en geef deze de naam `02-create-compute.py`. Vul dit met de volgende code in om een Azure Machine Learning-rekencluster te maken dat automatisch wordt geschaald tussen nul en vier knooppunten:

```python
# tutorial/02-create-compute.py
from azureml.core import Workspace
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

ws = Workspace.from_config() # This automatically looks for a directory .azureml

# Choose a name for your CPU cluster
cpu_cluster_name = "cpu-cluster"

# Verify that the cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                           idle_seconds_before_scaledown=2400,
                                                           min_nodes=0,
                                                           max_nodes=4)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)
```

Voer, in het venster met de geactiveerde Conda-omgeving van *tutorial1*, het Python-bestand uit:

```bash
python ./02-create-compute.py
```


> [!NOTE]
> Wanneer de cluster is gemaakt, zijn er 0 knooppunten ingericht. Voor de worden cluster *geen* kosten in rekening gebracht totdat u een taak verzendt. Dit cluster wordt omlaag geschaald wanneer het gedurende 2,400 seconden inactief is geweest (40 minuten).

De mapstructuur ziet er nu als volgt uit:

```bash
tutorial
└──.azureml
|  └──config.json
└──01-create-workspace.py
└──02-create-compute.py
```

> [!div class="nextstepaction"]
> [Ik heb een rekencluster gemaakt](?success=create-compute-cluster#next-steps) [Er is een probleem opgetreden](https://www.research.net/r/7C8Z3DN?issue=create-compute-cluster)

## <a name="view-in-the-studio"></a>Weergeven in de studio

Meld u aan bij [Azure Machine Learning Studio](https://ml.azure.com) om de werkruimte en het rekenproces dat u hebt gemaakt weer te geven.

1. Selecteer het **Abonnement** waarmee u de werkruimte hebt gemaakt.
1. Selecteer de **Machine Learning-werkruimte** u hebt gemaakt, *tutorial-ws*.
1. Nadat de werkruimte is geladen, selecteert u aan de linkerkant **Berekenen**.
1. Selecteer, bovenaan, het tabblad **Rekenclusters**.

:::image type="content" source="media/tutorial-1st-experiment-sdk-local/compute-instance-in-studio.png" alt-text="Schermopname: Bekijk het rekenproces in uw werkruimte.":::

In deze weergave wordt het ingerichte rekencluster weergegeven, samen met het aantal niet-actieve knooppunten, bezette knooppunten en niet-ingerichte knooppunten.  Omdat u het cluster nog niet hebt gebruikt, zijn alle knooppunten momenteel niet ingericht.

## <a name="next-steps"></a>Volgende stappen

In deze installatiezelfstudie hebt u het volgende gedaan:

- Een Azure Machine Learning-werkruimte gemaakt.
- De lokale ontwikkelomgeving instellen.
- Een Azure Machine Learning-rekencluster gemaakt.

In de andere delen van deze zelfstudie leert u het volgende:

* Deel 2. Code uitvoeren in de cloud met behulp van de Azure Machine Learning-SDK voor Python.
* Deel 3. De Python-omgeving beheren die u gebruikt voor modeltraining.
* Deel 4. Gegevens uploaden naar Azure en die gegevens gebruiken in de training.

Ga verder met de volgende zelfstudie om een script te verzenden naar het Azure Machine Learning-rekencluster.

> [!div class="nextstepaction"]
> [Zelfstudie: Een Python-script voor 'Hallo wereld' uitvoeren Python-script in Azure](tutorial-1st-experiment-hello-world.md)

---
title: Python-ontwikkel omgeving instellen
titleSuffix: Azure Machine Learning
description: Stel Azure Machine Learning python-ontwikkel omgevingen in Jupyter notebooks, Visual Studio code, Azure Databricks en data Science Virtual Machines in.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.date: 03/22/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: 7f35f9196fb4ab67de724ba73982fed69ac81361
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107306837"
---
# <a name="set-up-a-python-development-environment-for-azure-machine-learning"></a>Stel een python-ontwikkel omgeving in voor Azure Machine Learning

Meer informatie over het configureren van een python-ontwikkel omgeving voor Azure Machine Learning.

In de volgende tabel ziet u elke ontwikkel omgeving die in dit artikel wordt behandeld, samen met de voor-en nadelen.

| Omgeving | Voordelen | Nadelen |
| --- | --- | --- |
| [Lokale omgeving](#local) | Volledig beheer van uw ontwikkel omgeving en afhankelijkheden. Voer uit met een hulp programma, omgeving of IDE van uw keuze. | Duurt langer om aan de slag te gaan. De benodigde SDK-pakketten moeten worden geïnstalleerd en er moet ook een omgeving worden geïnstalleerd als u er nog geen hebt. |
| [De Data Science Virtual Machine (DSVM)](#dsvm) | Vergelijkbaar met het Cloud Compute-exemplaar (python en de SDK zijn vooraf geïnstalleerd), maar met extra populaire gegevens wetenschap en machine learning-hulpprogram ma's vooraf geïnstalleerd. Eenvoudig te schalen en combi neren met andere aangepaste hulp middelen en werk stromen. | Een langzamere aan de slag-ervaring in vergelijking met het Cloud Compute-exemplaar. |
| [Azure Machine Learning-rekeninstantie](#compute-instance) | Eenvoudigste manier om aan de slag te gaan. De volledige SDK is al geïnstalleerd in uw werk ruimte-VM en de notitieblok zelfstudies zijn vooraf gekloond en klaar om te worden uitgevoerd. | Geen controle over uw ontwikkel omgeving en afhankelijkheden. Er zijn extra kosten verbonden aan de virtuele Linux-machine (VM kan worden gestopt wanneer deze niet wordt gebruikt om kosten te voor komen). Zie de [prijs informatie](https://azure.microsoft.com/pricing/details/virtual-machines/linux/). |
| [Azure Databricks](how-to-configure-databricks-automl-environment.md) | Ideaal voor het uitvoeren van grootschalige intensieve machine learning werk stromen op het schaal bare Apache Spark platform. | Overbodig voor experimentele machine learning, of kleinschalige experimenten en werk stromen. Er zijn extra kosten gemaakt voor Azure Databricks. Zie de [prijs informatie](https://azure.microsoft.com/pricing/details/databricks/). |

Dit artikel bevat ook extra tips voor het gebruik van de volgende hulpprogram ma's:

* Jupyter-notebooks: als u al Jupyter-notebooks gebruikt, heeft de SDK een aantal extra's die u moet installeren.

* Visual Studio code: als u Visual Studio code gebruikt, bevat de [uitbrei ding](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) van de Azure machine learning uitgebreide taal ondersteuning voor python, evenals functies om met de Azure machine learning veel handiger en productief te werken.

## <a name="prerequisites"></a>Vereisten

* Azure Machine Learning werk ruimte. Als u er geen hebt, kunt u een Azure Machine Learning-werk ruimte maken met behulp van de [Azure Portal](how-to-manage-workspace.md)-, [Azure CLI](how-to-manage-workspace-cli.md#create-a-workspace)-en [Azure Resource Manager-sjablonen](how-to-create-workspace-template.md).

### <a name="local-and-dsvm-only-create-a-workspace-configuration-file"></a><a id="workspace"></a> Alleen lokale en DSVM: een configuratie bestand voor een werk ruimte maken

Het configuratie bestand van de werk ruimte is een JSON-bestand dat de SDK vertelt hoe er met uw Azure Machine Learning-werk ruimte moet worden gecommuniceerd. Het bestand heeft de naam *config.jsop* en heeft de volgende indeling:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

Dit JSON-bestand moet zich in de mapstructuur bevinden die uw python-scripts of Jupyter-notebooks bevat. Deze kan zich in dezelfde map bevindt, in een submap met de naam *. azureml* of in een bovenliggende map.

Als u dit bestand uit uw code wilt gebruiken, gebruikt u de- [`Workspace.from_config`](/python/api/azureml-core/azureml.core.workspace.workspace#from-config-path-none--auth-none---logger-none---file-name-none-) methode. Met deze code worden de gegevens uit het bestand geladen en wordt verbinding gemaakt met uw werk ruimte.

Maak een werkruimte configuratie bestand op een van de volgende manieren:

* Azure Portal

    **Down load het bestand**: In de [Azure Portal](https://ms.portal.azure.com)selecteert u  **config.jsdownloaden** in het gedeelte **overzicht** van uw werk ruimte.

    ![Azure Portal](./media/how-to-configure-environment/configure.png)

* Azure Machine Learning python-SDK

    Maak een script om verbinding te maken met uw Azure Machine Learning-werk ruimte en gebruik de [`write_config`](/python/api/azureml-core/azureml.core.workspace.workspace#write-config-path-none--file-name-none-) methode om uw bestand te genereren en op te slaan als *. azureml/config.jsop*. Zorg ervoor dat u `subscription_id` , `resource_group` en `workspace_name` met uw eigen vervangen.

    ```python
    from azureml.core import Workspace

    subscription_id = '<subscription-id>'
    resource_group  = '<resource-group>'
    workspace_name  = '<workspace-name>'

    try:
        ws = Workspace(subscription_id = subscription_id, resource_group = resource_group, workspace_name = workspace_name)
        ws.write_config()
        print('Library configuration succeeded')
    except:
        print('Workspace not found')
    ```

## <a name="local-computer-or-remote-vm-environment"></a><a id="local"></a>Lokale computer of externe VM-omgeving

U kunt een omgeving instellen op een lokale computer of op een externe virtuele machine, zoals een Azure Machine Learning Reken instantie of Data Science VM. 

Een lokale ontwikkel omgeving of externe VM configureren:

1. Maak een virtuele python-omgeving (virtualenv, Conda).

    > [!NOTE]
    > Hoewel dit niet vereist is, is het raadzaam om [Anaconda](https://www.anaconda.com/download/) of [Miniconda](https://www.anaconda.com/download/) te gebruiken voor het beheren van python virtuele omgevingen en het installeren van pakketten.

    > [!IMPORTANT]
    > Als u gebruikmaakt van Linux of macOS en een andere shell dan bash gebruikt (bijvoorbeeld zsh), kunnen er fouten optreden wanneer u sommige opdrachten uitvoert. U kunt dit probleem omzeilen door de `bash` opdracht te gebruiken om een nieuwe bash-shell te starten en de opdrachten daar uit te voeren.

1. Activeer de zojuist gemaakte virtuele python-omgeving.
1. Installeer de [Azure machine learning PYTHON SDK](/python/api/overview/azure/ml/install).
1. Als u uw lokale omgeving wilt configureren voor het gebruik van uw Azure Machine Learning-werk ruimte, [maakt u een configuratie bestand voor de werk ruimte](#workspace) of gebruikt u een bestaand.

Nu u uw lokale omgeving hebt ingesteld, kunt u aan de slag gaan met Azure Machine Learning. Raadpleeg de Azure Machine Learning aan de slag- [hand leiding voor python](tutorial-1st-experiment-sdk-setup-local.md) om aan de slag te gaan.

### <a name="jupyter-notebooks"></a><a id="jupyter"></a>Jupyter Notebooks

Wanneer u een lokale Jupyter Notebook server uitvoert, is het raadzaam om een IPython-kernel te maken voor uw python virtuele omgeving. Dit zorgt ervoor dat het import gedrag van de kernel en het pakket wordt gegarandeerd.

1. Omgevings specifieke IPython-kernels inschakelen

    ```bash
    conda install notebook ipykernel
    ```

1. Maak een kernel voor uw python virtuele omgeving. Zorg ervoor dat u vervangt door `<myenv>` de naam van uw python virtuele omgeving.

    ```bash
    ipython kernel install --user --name <myenv> --display-name "Python (myenv)"
    ```

1. De Jupyter Notebook-server starten

Raadpleeg de [opslag plaats Azure machine learning notitie blokken](https://github.com/Azure/MachineLearningNotebooks) om aan de slag te gaan met Azure machine learning-en Jupyter-notebooks.

> [!NOTE]
> Een door de community gestuurde opslagplaats met voorbeelden vindt u op https://github.com/Azure/azureml-examples.

### <a name="visual-studio-code"></a><a id="vscode"></a>Visual Studio Code

Visual Studio code gebruiken voor ontwikkeling:

1. Installeer [Visual Studio Code](https://code.visualstudio.com/Download).
1. Installeer de [Azure machine learning Visual Studio code extension](tutorial-setup-vscode-extension.md) (preview).

Als u de Visual Studio code-extensie hebt geïnstalleerd, gebruikt u deze voor het volgende:

* [Uw Azure Machine Learning-resources beheren](how-to-manage-resources-vscode.md)
* [Verbinding maken met een Azure Machine Learning Reken instantie](how-to-set-up-vs-code-remote.md)
* [Experimenten uitvoeren en fouten opsporen](how-to-debug-visual-studio-code.md)
* [Implementeer getrainde modellen](tutorial-train-deploy-image-classification-model-vscode.md).

## <a name="azure-machine-learning-compute-instance"></a><a id="compute-instance"></a>Reken instantie Azure Machine Learning

Het Azure Machine Learning [Compute-exemplaar](concept-compute-instance.md) is een veilig, op de cloud gebaseerd Azure-werk station dat gegevens wetenschappers biedt met een Jupyter notebook server, jjupyterlab en een volledig beheerde machine learning omgeving.

Er is niets om te installeren of te configureren voor een reken instantie.  

Maak een wille keurig moment in uw Azure Machine Learning-werk ruimte. Geef een naam op en geef een Azure VM-type op. Probeer het nu met deze [zelf studie: omgeving en werk ruimte voor installatie](tutorial-1st-experiment-sdk-setup.md).

Zie [een Azure machine learning Compute-instantie maken en beheren](how-to-create-manage-compute-instance.md)voor meer informatie over reken instanties, waaronder het installeren van pakketten.

> [!TIP]
> [Stop het reken](how-to-create-manage-compute-instance.md#manage)proces om te voor komen dat er kosten in rekening worden gebracht voor een ongebruikte Compute-instantie.

Naast een Jupyter Notebook-server en-Jjupyterlab kunt u reken instanties gebruiken in de [functie voor geïntegreerde notebooks in azure machine learning Studio](how-to-run-jupyter-notebooks.md).

U kunt ook de Azure Machine Learning Visual Studio code-extensie gebruiken om [verbinding te maken met een extern Compute-exemplaar met behulp van VS code](how-to-set-up-vs-code-remote.md).

## <a name="data-science-virtual-machine"></a><a id="dsvm"></a>Data Science Virtual Machine

De Data Science VM is een aangepaste installatie kopie van een virtuele machine (VM) die u kunt gebruiken als een ontwikkel omgeving. Het is ontworpen voor data wetenschappen werk dat vooraf geconfigureerde hulpprogram ma's en software zoals:

  - Pakketten zoals tensor flow, PyTorch, Scikit-learn, XGBoost en de Azure Machine Learning SDK
  - Populaire hulp middelen voor gegevens wetenschap, zoals zelfstandig en analyse van Spark
  - Azure-hulpprogram ma's zoals Azure CLI, AzCopy en Storage Explorer
  - Integrated Development Environments (Ide's) zoals Visual Studio code en PyCharm
  - Jupyter Notebook server

Zie de [hand leiding voor data Science VM-hulpprogram](data-science-virtual-machine/tools-included.md)ma's voor een uitgebreidere lijst met hulpprogram ma's.

> [!IMPORTANT]
> Als u van plan bent om de Data Science VM te gebruiken als een [reken doel](concept-compute-target.md) voor uw training of voor het afleiden van taken, wordt alleen Ubuntu ondersteund.

Als u de Data Science VM als een ontwikkel omgeving wilt gebruiken:

1. Maak een Data Science VM met een van de volgende methoden:

    * Gebruik de Azure Portal voor het maken van een [Ubuntu](data-science-virtual-machine/dsvm-ubuntu-intro.md) -of [Windows](data-science-virtual-machine/provision-vm.md) DSVM.
    * [Maak een Data Science VM met arm-sjablonen](data-science-virtual-machine/dsvm-tutorial-resource-manager.md).
    * Azure CLI gebruiken

        Als u een Ubuntu-Data Science VM wilt maken, gebruikt u de volgende opdracht:

        ```azurecli-interactive
        # create a Ubuntu Data Science VM in your resource group
        # note you need to be at least a contributor to the resource group in order to execute this command successfully
        # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
        az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
        ```

        Als u een Windows-DSVM wilt maken, gebruikt u de volgende opdracht:

        ```azurecli-interactive
        # create a Windows Server 2016 DSVM in your resource group
        # note you need to be at least a contributor to the resource group in order to execute this command successfully
        az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
        ```

1. Activeer de Conda-omgeving met de Azure Machine Learning SDK.

    * Voor Ubuntu Data Science VM:

        ```bash
        conda activate py36
        ```

    * Voor Windows Data Science VM:

        ```bash
        conda activate AzureML
        ```

1. Als u de Data Science VM wilt configureren voor het gebruik van uw Azure Machine Learning-werk ruimte, [maakt u een configuratie bestand voor de werk ruimte](#workspace) of gebruikt u een bestaande.

Net als bij lokale omgevingen kunt u Visual Studio code en de [Azure machine learning Visual Studio code-extensie](#vscode) gebruiken om te communiceren met Azure machine learning.

Zie [Data Science virtual machines](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)voor meer informatie.


## <a name="next-steps"></a>Volgende stappen

- [Train een model](tutorial-train-models-with-aml.md) op Azure machine learning met de MNIST-gegevensset.
- Zie de [Naslag informatie voor de Azure machine learning SDK voor python](/python/api/overview/azure/ml/intro). 

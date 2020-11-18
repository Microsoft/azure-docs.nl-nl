---
title: Een Jupyter Notebook-project exporteren vanuit de Azure Notebooks Preview
description: Exporteer snel een Jupyter Notebook-project.
ms.topic: quickstart
ms.date: 06/29/2020
ms.openlocfilehash: ea16bd61b542217fb6f2d5ba1d926a0bc19d4cce
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844417"
---
# <a name="quickstart-export-a-jupyter-notebook-project-in-azure-notebooks-preview"></a>Een Jupyter Notebook-project exporteren in Azure Notebooks Preview

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

In deze quickstart gaat u een Azure Notebooks-project downloaden voor gebruik in andere Jupyter Notebook-oplossingen. 

## <a name="prerequisites"></a>Vereisten

Een bestaand Azure Notebooks-project.

## <a name="export-an-azure-notebooks-project"></a>Een Azure Notebooks-project exporteren

1. Ga naar [Azure Notebooks](https://notebooks.azure.com) en meld u aan. Zie [Quickstart: Aanmelden bij Azure Notebooks](quickstart-sign-in-azure-notebooks.md) voor meer informatie.

1. Selecteer vanuit uw openbare profielpagina de optie **Mijn projecten** bovenaan de pagina:

    ![Koppeling naar Mijn projecten bovenaan het browservenster](media/quickstarts/my-projects-link.png)

1. Selecteer een project.
1. Klik op de knop Downloaden om een zip-bestand te activeren dat al uw projectbestanden bevat.
1. U kunt ook op een specifieke projectpagina klikken op de knop Project downloaden om alle bestanden van een bepaald project te downloaden.

Nadat u uw projectbestanden hebt gedownload, kunt u deze gebruiken met andere Jupyter Notebook-oplossingen. Enkele van de opties die in de onderstaande secties worden beschreven, zijn: 
- [Visual Studio Code](#use-notebooks-in-visual-studio-code)
- [GitHub Codespaces](#use-notebooks-in-github-codespaces)
- [Azure Machine Learning](#use-notebooks-with-azure-machine-learning)
- [Azure Lab Services](#use-azure-lab-services)
- [GitHub](#use-github)

## <a name="create-an-environment-for-notebooks"></a>Een omgeving voor notebooks maken

Als u een omgeving wilt maken die overeenkomt met die van de Azure Notebooks Preview, kunt u het scriptbestand gebruiken dat in GitHub wordt geleverd.

1. Ga naar de [GitHub-opslagplaats](https://github.com/microsoft/AzureNotebooks) van Azure Notebooks of [benader de omgevingsmap rechtstreeks](https://aka.ms/aznbrequirementstxt).
1. Ga vanaf een opdrachtprompt naar de map die u voor uw projecten wilt gebruiken.
1. Download de inhoud van de omgevingsmap en volg de LEESMIJ-instructies om de Azure Notebooks-pakketafhankelijkheden te installeren.


## <a name="use-notebooks-in-visual-studio-code"></a>Notebooks gebruiken in Visual Studio Code

[VS code](https://code.visualstudio.com/) is een gratis code-editor die u lokaal kunt gebruiken of verbonden met externe compute. In combinatie met de Python-extensie biedt het een volledige omgeving voor Python-ontwikkeling, waaronder een rijke systeemeigen ervaring voor het werken met Jupyter Notebooks. 

![Ondersteuning van VS code Jupyter Notebook](media/vs-code-jupyter-notebook.png)

Nadat u uw projectbestanden hebt [gedownload](#export-an-azure-notebooks-project), kunt u deze gebruiken met VS Code. Voor hulp bij het gebruik van VS Code met Jupyter Notebooks raadpleegt u de zelfstudies [Werken met Jupyter Notebooks in Visual Studio code](https://code.visualstudio.com/docs/python/jupyter-support) en [Data science in Visual Studio Code](https://code.visualstudio.com/docs/python/data-science-tutorial).

U kunt ook het [Azure Notebooks-omgevingsscript](#create-an-environment-for-notebooks) met Visual Studio Code gebruiken om een omgeving te maken die overeenkomt met de Azure Notebooks Preview.

## <a name="use-notebooks-in-github-codespaces"></a>Notebooks gebruiken in GitHub Codespaces

GitHub Codespaces biedt in de cloud gehoste omgevingen waar u uw notebooks kunt bewerken met behulp van Visual Studio Code of in uw webbrowser. Het biedt dezelfde geweldige Jupyter-ervaring als VS Code, maar zonder dat u iets hoeft te installeren op uw apparaat. Als u geen lokale omgeving wilt instellen en de voorkeur geeft aan een oplossing die door de cloud wordt ondersteund, is het maken van een codespace een uitstekende optie. Aan de slag:
1. [Download](#export-an-azure-notebooks-project) uw projectbestanden.
1. [Maak een GitHub-opslagplaats](https://help.github.com/github/getting-started-with-github/create-a-repo) om uw notebooks op te slaan.   
1. [Voeg uw bestanden toe ](https://help.github.com/github/managing-files-in-a-repository/adding-a-file-to-a-repository) aan de opslagplaats.
1. [Toegang aanvragen tot de preview van GitHub Codespaces](https://github.com/features/codespaces)

## <a name="use-notebooks-with-azure-machine-learning"></a>Notebooks gebruiken met Azure Machine Learning

Azure Machine Learning biedt een end-to-end machine learning-platform waarmee gebruikers sneller modellen kunnen bouwen en implementeren op Azure. Met Azure ML kunt u Jupyter Notebooks uitvoeren op een virtuele machine of in een gedeelde clusteromgeving. Als u behoefte hebt aan een cloudoplossing voor uw ML-workload voor het bijhouden van experimenten, het beheren van gegevenssets en meer, raden we u Azure Machine Learning aan. Aan de slag met Azure ML:

1. [Download](#export-an-azure-notebooks-project) uw projectbestanden.
1. [Maak een werkruimte](../machine-learning/how-to-manage-workspace.md) in Azure Portal.

   ![Een werkruimte maken](../machine-learning/media/how-to-manage-workspace/create-workspace.gif)
 
1. Open de [Azure Studio (preview)](https://ml.azure.com/).
1. Selecteer in de navigatiebalk aan de linkerkant **Notebooks**.
1. Klik op de knop **Bestanden uploaden** en upload de projectbestanden die u hebt gedownload van Azure Notebooks.

Voor aanvullende informatie over Azure ML en het uitvoeren van Jupyter Notebooks, kunt u de [documentatie](../machine-learning/how-to-run-jupyter-notebooks.md) raadplegen of de module [Inleiding tot machine learning](/learn/modules/intro-to-azure-machine-learning-service/) op Microsoft Learn proberen.


## <a name="use-azure-lab-services"></a>Azure Lab Services gebruiken

Met [Azure Lab Services](https://azure.microsoft.com/services/lab-services/) kunnen onderwijzers eenvoudig toegang krijgen tot vooraf geconfigureerde VM's voor een heel klaslokaal. Als u op zoek bent naar een manier om te werken met Jupyter Notebooks en Cloud Compute in een op maat gemaakte klaslokaalomgeving, is Lab Services een uitstekende optie.

![image](../lab-services/media/tutorial-setup-classroom-lab/new-lab-button.png)

 Nadat u uw projectbestanden hebt [gedownload](#export-an-azure-notebooks-project), kunt u deze gebruiken met Azure Lab Services. Voor hulp bij het instellen van een lab raadpleegt u [Een lab instellen om data science te leren met Python en Jupyter Notebooks](../lab-services/class-type-jupyter-notebook.md)

## <a name="use-github"></a>GitHub gebruiken

GitHub biedt een gratis, met broncodebeheer gemaakte manier om notebooks (en andere bestanden) op te slaan, uw notebooks te delen met anderen en samen te werken. Als u op zoek bent naar een manier om uw projecten te delen en samen te werken met anderen, is GitHub een fantastische optie. GitHub kan worden gecombineerd met [GitHub Codespaces](#use-notebooks-in-github-codespaces) voor een fantastische ontwikkelervaring. Aan de slag met GitHub

1. [Download](#export-an-azure-notebooks-project) uw projectbestanden.
1. [Maak een GitHub-opslagplaats](https://help.github.com/github/getting-started-with-github/create-a-repo) om uw notebooks op te slaan. 
1. [Voeg uw bestanden toe ](https://help.github.com/github/managing-files-in-a-repository/adding-a-file-to-a-repository) aan de opslagplaats.

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over Python in Visual Studio Code](https://code.visualstudio.com/docs/python/python-tutorial)
- [Meer informatie over Azure Machine Learning en Jupyter Notebooks](../machine-learning/how-to-run-jupyter-notebooks.md)
- [Meer informatie over GitHub Codespaces](https://github.com/features/codespaces)
- [Meer informatie over Azure Lab Services](https://azure.microsoft.com/services/lab-services/)
- [Meer informatie over GitHub](https://help.github.com/github/getting-started-with-github/)
---
title: Toegang krijgen tot een reken-exemplaarterminal in uw werkruimte
titleSuffix: Azure Machine Learning
description: Gebruik de terminal op een reken-exemplaar voor Git-bewerkingen, om pakketten te installeren en kernels toe te voegen.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: how-to
ms.date: 02/05/2021
ms.openlocfilehash: 44167abd3c602b62dc101298d227744c6bc485b6
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107890279"
---
# <a name="access-a-compute-instance-terminal-in-your-workspace"></a>Toegang tot een reken-exemplaarterminal in uw werkruimte

Toegang tot de terminal van een reken-exemplaar in uw werkruimte voor het volgende:

* Gebruik bestanden uit Git en versiebestanden. Deze bestanden worden opgeslagen in uw bestandssysteem van de werkruimte, niet beperkt tot één reken-exemplaar.
* Installeer pakketten op het reken-exemplaar.
* Maak extra kernels op de reken-instantie.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://aka.ms/AMLFree) aan voordat u begint.
* Een Machine Learning-werkruimte. Raadpleeg [Een Azure Machine Learning-werkruimte maken](how-to-manage-workspace.md).

## <a name="access-a-terminal"></a>Toegang tot een terminal

Toegang krijgen tot de terminal:

1. Open uw werkruimte in [Azure Machine Learning-studio](https://ml.azure.com).
1. Selecteer aan de linkerkant **Notebooks.**
1. Selecteer de **afbeelding Terminal openen.**

    :::image type="content" source="media/how-to-use-terminal/open-terminal-window.png" alt-text="Terminalvenster openen":::

1. Wanneer een reken-exemplaar wordt uitgevoerd, wordt het terminalvenster voor dat reken exemplaar weergegeven.
1. Wanneer er geen reken-exemplaar wordt uitgevoerd, gebruikt u de **sectie Compute** aan de rechterkant om een reken-exemplaar te starten of te maken.
    :::image type="content" source="media/how-to-use-terminal/start-or-create-compute.png" alt-text="Een reken-exemplaar starten of maken":::

Naast de bovenstaande stappen hebt u ook toegang tot de terminal vanuit:

* RStudio: selecteer **linksboven het** tabblad Terminal.
* Jupyter Lab: selecteer de **tegel Terminal** onder de **kop Overige** op het tabblad Start.
* Jupyter: selecteer **Nieuwe>Terminal** rechtsboven in het tabblad Bestanden.
* SSH naar de computer, als u SSH-toegang hebt ingeschakeld toen het rekenin exemplaar werd gemaakt.

## <a name="copy-and-paste-in-the-terminal"></a>Kopiëren en plakken in de terminal

> * Windows: `Ctrl-Insert` kopiëren en gebruiken of `Ctrl-Shift-v` `Shift-Insert` plakken.
> * Mac OS: `Cmd-c` kopiëren en `Cmd-v` plakken.
> * FireFox/IE biedt mogelijk geen goede ondersteuning voor klembordmachtigingen.

## <a name="use-files-from-git-and-version-files"></a><a name=git></a> Bestanden uit Git en versiebestanden gebruiken

Toegang tot alle Git-bewerkingen vanuit de terminal. Alle Git-bestanden en -mappen worden opgeslagen in het bestandssysteem van uw werkruimte. Met deze opslag kunt u deze bestanden gebruiken vanuit elk reken exemplaar in uw werkruimte.

> [!NOTE]
> Voeg uw bestanden en mappen overal toe onder de **map ~/cloudfiles/code/Users,** zodat ze zichtbaar zijn in al uw Jupyter-omgevingen.

Meer informatie over [het klonen van Git-opslagplaatsen in het bestandssysteem van uw werkruimte.](concept-train-model-git-integration.md#clone-git-repositories-into-your-workspace-file-system)

## <a name="install-packages"></a>Pakketten installeren

 Installeer pakketten vanuit een terminalvenster. Installeer Python-pakketten in de **Python 3.6 - AzureML-omgeving.**  Installeer R-pakketten in de **R-omgeving.**

U kunt pakketten ook rechtstreeks in Jupyter Notebook of RStudio installeren:

* RStudio Gebruik het **tabblad Pakketten** rechtsonder of het **tabblad Console** linksboven.  
* Python: voeg installatiecode toe en voer deze uit in Jupyter Notebook cel.

> [!NOTE]
> Voor pakketbeheer in een notebook gebruikt u **%pip** of **%conda** magic-functies om automatisch pakketten te installeren in de **kernel** die momenteel wordt uitgevoerd, in plaats van **!pip** of **!conda,** die verwijst naar alle pakketten (inclusief pakketten buiten de huidige kernel)

## <a name="add-new-kernels"></a>Nieuwe kernels toevoegen

> [!WARNING]
>  Zorg er tijdens het aanpassen van het reken exemplaar voor dat u de **azureml_py36** conda-omgeving of **Python 3.6 - AzureML-kernel niet** verwijdert. Dit is nodig voor jupyter-/JupyterLab-functionaliteit

Een nieuwe Jupyter-kernel toevoegen aan het reken-exemplaar:

1. Gebruik het terminalvenster om een nieuwe omgeving te maken.  Met de onderstaande code wordt bijvoorbeeld `newenv` gemaakt:

    ```shell
    conda create --name newenv
    ```

1. Activeer de omgeving.  Bijvoorbeeld na het maken `newenv` van :

    ```shell
    conda activate newenv
    ```

1. Installeer het pip- en ipykernel-pakket in de nieuwe omgeving en maak een kernel voor die conda-env

    ```shell
    conda install pip
    conda install ipykernel
    python -m ipykernel install --user --name newenv --display-name "Python (newenv)"
    ```

Een van de [beschikbare Jupyter Kernels kan](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels) worden geïnstalleerd.

## <a name="manage-terminal-sessions"></a>Terminalsessies beheren

 Selecteer **Actieve sessies weergeven** in de terminalwerkbalk voor een lijst met alle actieve terminalsessies. Als er geen actieve sessies zijn, wordt dit tabblad uitgeschakeld.

Sluit ongebruikte sessies om de resources van uw rekenin exemplaar te behouden.
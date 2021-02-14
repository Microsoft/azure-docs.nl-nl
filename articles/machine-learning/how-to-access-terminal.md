---
title: Toegang tot een compute instance-terminal in uw werk ruimte
titleSuffix: Azure Machine Learning
description: Gebruik de Terminal op een reken instantie voor git-bewerkingen, om pakketten te installeren en kernels toe te voegen.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 02/05/2021
ms.openlocfilehash: e42d6d53e4d06e5ec7a33ea4b8ca18233dcf1dd7
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100101332"
---
# <a name="access-a-compute-instance-terminal-in-your-workspace"></a>Een reken instantie-terminal openen in uw werk ruimte

Toegang tot de terminal van een reken instantie in uw werk ruimte:

* Gebruik bestanden uit Git-en versie bestanden. Deze bestanden worden opgeslagen in het bestands systeem van de werk ruimte, niet beperkt tot één reken instantie.
* Pakketten installeren op het reken exemplaar.
* Maak extra kernels op het reken exemplaar.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://aka.ms/AMLFree) aan voordat u begint.
* Een Machine Learning-werkruimte. Raadpleeg [Een Azure Machine Learning-werkruimte maken](how-to-manage-workspace.md).

## <a name="access-a-terminal"></a>Toegang tot een Terminal

Voor toegang tot de terminal:

1. Open uw werk ruimte in [Azure machine learning Studio](https://ml.azure.com).
1. Selecteer aan de linkerkant **notitie blokken**.
1. Selecteer de **Open Terminal** -installatie kopie.

    :::image type="content" source="media/how-to-use-terminal/open-terminal-window.png" alt-text="Terminal venster openen":::

1. Wanneer een reken instantie wordt uitgevoerd, wordt het Terminal venster voor dat Compute-exemplaar weer gegeven.
1. Als er geen reken instantie wordt uitgevoerd, gebruikt u de sectie **Compute** aan de rechter kant om een reken instantie te starten of te maken.
    :::image type="content" source="media/how-to-use-terminal/start-or-create-compute.png" alt-text="Een reken instantie starten of maken":::

Naast de bovenstaande stappen kunt u ook toegang krijgen tot de Terminal vanuit:

* RStudio: Selecteer het tabblad **Terminal** aan de rechter bovenhoek.
* Jupyter Lab: Selecteer de tegel **Terminal** onder de **andere** kop op het tabblad Start.
* Jupyter: Selecteer **nieuwe>Terminal** rechtsboven op het tabblad bestanden.
* SSH naar de computer als u SSH-toegang hebt ingeschakeld toen het reken exemplaar werd gemaakt.

## <a name="copy-and-paste-in-the-terminal"></a>Kopiëren en plakken in de Terminal

> * Windows: `Ctrl-Insert` kopiëren en gebruiken `Ctrl-Shift-v` of `Shift-Insert` Plakken.
> * Mac OS: `Cmd-c` om te kopiëren en `Cmd-v` te plakken.
> * FireFox/IE ondersteunt mogelijk geen juiste Klembord machtigingen.

## <a name="use-files-from-git-and-version-files"></a><a name=git></a> Bestanden van Git-en versie bestanden gebruiken

Toegang tot alle Git-bewerkingen van de Terminal. Alle Git-bestanden en-mappen worden opgeslagen in het bestands systeem van de werk ruimte. Met deze opslag kunt u deze bestanden van een reken instantie in uw werk ruimte gebruiken.

> [!NOTE]
> Voeg uw bestanden en mappen toe aan de map **~/cloudfiles/code/users** , zodat deze in al uw Jupyter-omgevingen zichtbaar zijn.

Meer informatie over [het klonen van Git-opslag plaatsen in het bestands systeem van de werk ruimte](concept-train-model-git-integration.md#clone-git-repositories-into-your-workspace-file-system).

## <a name="install-packages"></a>Pakketten installeren

 Pakketten installeren vanuit een Terminal venster. Installeer Python-pakketten in de **Python 3,6-AzureML-** omgeving.  R-pakketten installeren in de **R** -omgeving.

U kunt pakketten ook rechtstreeks installeren in Jupyter Notebook of RStudio:

* RStudio gebruik het tabblad **pakketten** aan de rechter kant of op het tabblad **console** linksboven.  
* Python: Voeg een installatie code toe en voer deze uit in een Jupyter Notebook-cel.

> [!NOTE]
> Voor pakket beheer binnen een notebook gebruikt u **% PIP** of **% Conda** Magic functions om pakketten automatisch te installeren in de **kernel die momenteel wordt uitgevoerd**, in plaats van **! PIP** of **! Conda** die verwijst naar alle pakketten (inclusief pakketten buiten de actieve kernel)

## <a name="add-new-kernels"></a>Nieuwe kernels toevoegen

> [!WARNING]
>  Zorg er tijdens het aanpassen van het reken proces voor dat u de **azureml_py36** Conda-omgeving of de **python 3,6-azureml-** kernel niet verwijdert. Dit is nodig voor de functionaliteit van Jupyter/Jjupyterlab

Een nieuwe Jupyter-kernel toevoegen aan het reken exemplaar:

1. Gebruik het Terminal venster om een nieuwe omgeving te maken.  De onderstaande code maakt bijvoorbeeld `newenv` :

    ```shell
    conda create --name newenv
    ```

1. Activeer de omgeving.  Bijvoorbeeld na het maken van `newenv` :

    ```shell
    conda activate newenv
    ```

1. PIP-en ipykernel-pakket installeren in de nieuwe omgeving en een kernel maken voor die Conda env

    ```shell
    conda install pip
    conda install ipykernel
    python -m ipykernel install --user --name newenv --display-name "Python (newenv)"
    ```

Een van de [beschik bare Jupyter-kernels](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels) kan worden geïnstalleerd.

## <a name="manage-terminal-sessions"></a>Terminal sessies beheren

 Selecteer **actieve sessies weer geven** in de Terminal-werk balk om een lijst weer te geven met alle actieve Terminal sessies. Als er geen actieve sessies zijn, wordt dit tabblad uitgeschakeld.

Sluit alle ongebruikte sessies om de resources van uw reken instantie te bewaren.
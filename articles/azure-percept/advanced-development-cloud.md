---
title: Aan de slag met Azure percept Advanced Development in de Cloud
description: Ga aan de slag met geavanceerde ontwikkeling in de Cloud via Jupyter-notebooks en Azure Machine Learning
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/10/2021
ms.custom: template-tutorial
ms.openlocfilehash: 408040ea68273a29ed9be87b60bd0cc6da736a05
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101664638"
---
# <a name="getting-started-with-advanced-development-in-the-cloud-via-jupyter-notebooks-and-azure-machine-learning"></a>Aan de slag met geavanceerde ontwikkeling in de Cloud via Jupyter-notebooks en Azure Machine Learning

Dit artikel begeleidt u bij het instellen van een Azure Machine Learning-werk ruimte, het uploaden van een vooraf geconfigureerd voor beeld Jupyter Notebook naar de werk ruimte, het maken van een reken instantie en het uitvoeren van de cellen van de notebook in de werk ruimte.

Het [notitie blok](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb) voert overboeking Learning uit met behulp van een vooraf getraind tensor flow model (MobileNetSSDV2Lite) op AzureML in python met een aangepaste gegevensset om Bowlen te detecteren.

In het notitie blok ziet u hoe u begint met [Coco](https://cocodataset.org/#home), het filtert u op alleen de klasse van belang (Bowlen) en converteert u deze naar de juiste indeling. U kunt ook het hulp programma voor het labelen van open-source [VoTT 2](https://github.com/microsoft/VoTT) gebruiken om selectie vakjes in de Pascal VOC-indeling te maken en aan te labelen.

Nadat het model op de aangepaste gegevensset is getraind, kan het model vervolgens worden geïmplementeerd in uw Azure percept DK met behulp van de module dubbele update methode.

U kunt vervolgens de methode voor het afleiden van het model controleren door de Live RTSP-stroom van het Azure percept Vision-SoM weer te geven. Model retraining en-implementatie worden uitgevoerd binnen het notitie blok in de Cloud.

## <a name="prerequisites"></a>Vereisten

- [Azure Machine Learning abonnement](https://azure.microsoft.com/free/services/machine-learning/)
- [Azure percept DK met Azure percept Vision SoM verbonden](./overview-azure-percept-dk.md)
- De [on-boarding-ervaring van Azure PERCEPT DK](./quickstart-percept-dk-set-up.md) is voltooid

## <a name="download-azure-percept-github-repository"></a>Azure percept GitHub-opslag plaats downloaden

1. Ga naar de [Azure percept github-opslag plaats](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview).

1. Kloon de opslag plaats of down load het ZIP-bestand. Klik aan de bovenkant van het scherm op   ->  **Post code downloaden**.

    :::image type="content" source="./media/advanced-development-cloud/download-zip.png" alt-text="GitHub-Download scherm.":::

## <a name="set-up-azure-machine-learning-portal-and-notebook"></a>Azure Machine Learning Portal en notebook instellen

1. Ga naar de [Azure machine learning portal](https://ml.azure.com).

1. Selecteer uw directory, Azure-abonnement en Machine Learning werk ruimte in de vervolg keuzelijsten en klik op **aan de slag**.

    :::image type="content" source="./media/advanced-development-cloud/machine-learning-portal-get-started.png" alt-text="Het scherm aan de slag van Azure ML.":::

    Als u nog geen Azure Machine Learning-werk ruimte hebt, klikt u op **een nieuwe werk ruimte maken**. Ga als volgt te werk op het tabblad nieuwe browser:

    1. Selecteer uw Azure-abonnement.
    1. Selecteer uw resourcegroep.
    1. Voer een naam in voor uw werk ruimte.
    1. Kies uw regio.
    1. Selecteer uw werkruimte versie.
    1. Klik op **Controleren en maken**.
    1. Controleer uw selecties op de volgende pagina en klik op **maken**.

        :::image type="content" source="./media/advanced-development-cloud/workspace-review-and-create.png" alt-text="Scherm voor het maken van de werk ruimte in azure ML.":::

    Wacht enkele minuten tot het maken van de werk ruimte is toegestaan. Nadat het maken van de werk ruimte is voltooid, ziet u groene vinkjes naast uw resources en **is uw implementatie** aan de bovenkant van de pagina overzicht van Machine Learning Services.

    :::image type="content" source="./media/advanced-development-cloud/workspace-creation-complete-inline.png" alt-text="Bevestiging van het maken van de werk ruimte." lightbox= "./media/advanced-development-cloud/workspace-creation-complete.png":::

    Nadat het maken van de werk ruimte is voltooid, gaat u terug naar het tabblad machine learning portal en klikt u op **aan de slag**.

1. Klik op de start pagina van de werk ruimte machine learning op **notitie blokken** in het linkerdeel venster.

    :::image type="content" source="./media/advanced-development-cloud/notebook.png" alt-text="Start pagina van Azure ML.":::

1. Klik op het tabblad **mijn bestanden** op de verticale pijl om uw ipynb-bestand te uploaden.

    :::image type="content" source="./media/advanced-development-cloud/upload-files.png" alt-text="Start pagina Markeer het pictogram bestand uploaden.":::

1. Ga naar en selecteer het [Transferlearningusing_SSDLiteV2 model. ipynb-bestand](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb) in het lokale exemplaar van de Azure percept github-opslag plaats. Klik op **Openen**. Schakel in het venster **bestanden uploaden** het selectie vakje naast **inhoud van dit bestand vertrouwen uit** en klik op **uploaden**.

    :::image type="content" source="./media/advanced-development-cloud/select-file.png" alt-text="Scherm voor bestands selectie.":::

1. Controleer de **reken** status op de bovenste menu balk. Als er geen berekeningen worden gevonden, klikt u op het **+** pictogram om een nieuwe reken proces te maken.

    :::image type="content" source="./media/advanced-development-cloud/no-computes-found-inline.png" alt-text="Compute-status met + icon gemarkeerd." lightbox= "./media/advanced-development-cloud/no-computes-found.png":::

1. Voer in het venster **New Compute instance** een **Compute name** in, kies een **type virtuele machine** en selecteer de grootte van een **virtuele machine**. Klik op **Create**.

    :::image type="content" source="./media/advanced-development-cloud/new-compute-instance.png" alt-text="Nieuw scherm voor het maken van reken instanties.":::

    Nadat u op **maken** hebt geklikt, wordt in de **reken** status een blauwe cirkel en **\<your compute name> -maken** weer gegeven

    :::image type="content" source="./media/advanced-development-cloud/compute-creating.png" alt-text="Compute-status wanneer het maken van een compute nog wordt uitgevoerd.":::

    **\<your compute name> Met** de **reken** status wordt een groene cirkel weer gegeven wanneer het maken van de berekening is voltooid.

    :::image type="content" source="./media/advanced-development-cloud/compute-running.png" alt-text="De compute-status geeft aan dat het maken van de berekening is voltooid.":::

1. Zodra de compute actief is, selecteert u de **Python 3,6-AzureML-** kernel in het vervolg keuzemenu naast het **+** pictogram.

## <a name="working-with-the-notebook"></a>Werken met het notitie blok

U bent nu klaar om het notitie blok uit te voeren om uw aangepaste Bowl detector te trainen en te implementeren in uw Devkit. Zorg ervoor dat elke cel van de notebook afzonderlijk wordt uitgevoerd, aangezien sommige cellen invoer parameters vereisen voordat het script wordt uitgevoerd. Cellen waarvoor invoer parameters zijn vereist, kunnen rechtstreeks in het notitie blok worden bewerkt. Als u een cel wilt uitvoeren, klikt u op het pictogram afspelen aan de linkerkant van de cel:

:::image type="content" source="./media/advanced-development-cloud/run-cell-inline.png" alt-text="Pictogram voor het markeren van een notitie blok." lightbox= "./media/advanced-development-cloud/run-cell.png":::

## <a name="next-steps"></a>Volgende stappen

Ga voor aanvullende Azure Machine Learning service-voorbeeld notitieblokken naar deze [opslag plaats](https://github.com/Azure/MachineLearningNotebooks/tree/2aa7c53b0ce84e67565d77e484987714fdaed36e/how-to-use-azureml)

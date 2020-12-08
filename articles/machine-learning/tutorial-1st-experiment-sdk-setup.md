---
title: 'Zelfstudie: Aan de slag in Jupyter Notebooks (Python)'
titleSuffix: Azure Machine Learning
description: Installatie voor Jupyter Notebook-zelfstudies. Maak een werkruimte, kloon notebooks in de werkruimte en maak een rekenproces waarin u de notebooks uitvoert.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 02/10/2020
ms.custom: devx-track-python
ms.openlocfilehash: ebde8048c26364d77d95b9c7b34f9d1d7a4a20ee
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96326257"
---
# <a name="tutorial-get-started-with-azure-machine-learning-in-jupyter-notebooks"></a>Zelfstudie: Aan de slag met de Azure Machine Learning in Jupyter Notebooks

In deze zelfstudie voert u de stappen uit om aan de slag te gaan met de Azure Machine Learning met behulp van Jupyter Notebooks op een [beheerd werkstation in de cloud (rekenproces)](concept-compute-instance.md). Deze zelfstudie is een voorloper van alle andere Jupyter Notebook-zelfstudies.

In deze zelfstudie hebt u:

> [!div class="checklist"]
> * Maak een [Azure Machine Learning-werkruimte](concept-workspace.md) om in andere Jupyter Notebook-zelfstudies te gebruiken.
> * Kloon het zelfstudie-notebook naar uw map in de werkruimte.
> * Maak een cloudgebaseerd rekenproces met de Python SDK voor Azure Machine Learning geïnstalleerd en vooraf geconfigureerd.

Als u nog geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="create-a-workspace"></a>Een werkruimte maken

Een Azure Machine Learning-werkruimte is een basisblok in de cloud dat u gebruikt voor het experimenteren, trainen en implementeren van machine learning-modellen. De klasse bindt uw Azure-abonnement en resourcegroep aan een eenvoudig te verbruiken object in de service.

Ga verder naar [Een notebook-map klonen](#clone) als u al een Azure Machine Learning-werkruimte hebt.  

Er zijn verschillende manieren om een [werkruimte te maken](how-to-manage-workspace.md).  In deze zelfstudie maakt u een werkruimte via Azure Portal, een webconsole om uw Azure-resources te beheren.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT]
> Noteer uw *werkruimte* en *abonnement*. U hebt deze informatie nodig om ervoor te zorgen dat u uw experiment op de juiste plek maakt.

## <a name="run-a-notebook-in-your-workspace"></a><a name="azure"></a>Een notebook uitvoeren in uw werkruimte

Azure Machine Learning bevat een cloudnotebook-server in uw werkruimte voor een installatieloze en vooraf geconfigureerde ervaring. Gebruik [uw eigen omgeving](tutorial-1st-experiment-sdk-setup-local.md) als u liever controle over uw omgeving, pakketten en afhankelijkheden hebt.

 Volg deze video of gebruik de gedetailleerde stappen om de zelfstudienotebook te klonen en uit te voeren vanuit uw werkruimte.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4mTUr]

### <a name="clone-a-notebook-folder"></a><a name="clone"></a> Een notebook-map klonen

U voltooit de volgende instellingen voor het experiment en voert stappen uit in Azure Machine Learning-studio. Deze geconsolideerde interface bevat machine learning-hulpprogramma's voor het uitvoeren van datawetenschap-scenario's voor datawetenschappers van alle vaardigheidsniveaus.

1. Meld u aan bij [Azure Machine Learning Studio](https://ml.azure.com/).

1. Selecteer uw abonnement en de werkruimte die u hebt gecreëerd.

1. Selecteer aan de linkerkant **Notebooks**.

1. Selecteer bovenaan het tabblad **Voorbeelden**.

1. Open de map **Python**.

1. Open de map met een versienummer erop. Dit nummer vertegenwoordigt de huidige release voor de Python SDK.

1. Selecteer de knop **...** rechts van de map **zelfstudies** en selecteer vervolgens **Klonen**.

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/clone-tutorials.png" alt-text="Schermopname van de map zelfstudies voor Klonen.":::

1. Er wordt een lijst met mappen weergegeven, met de verschillende gebruikers die toegang hebben tot de werkruimte. Selecteer uw map om de map **Zelfstudies**  daar te klonen.

### <a name="open-the-cloned-notebook"></a><a name="open"></a>Het gekloonde notebook openen

1. Open de map **Zelfstudies** die naar uw sectie **Gebruikersbestanden** is gekloond.

    > [!IMPORTANT]
    > U kunt notebooks bekijken in de map **Voorbeelden**, maar u kunt een notebook niet uitvoeren vandaaruit. Als u een notebook wilt uitvoeren, moet u de gekloonde versie van het notebook in de sectie **Gebruikersbestanden** openen.
    
1. Selecteer het bestand **img-classification-part1-training.ipynb** in de map **tutorials/image-classification-mnist-data**.

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/expand-user-folder.png" alt-text="Schermopname van de map Open zelfstudies.":::

1. Selecteer in de bovenste balk een rekenproces om te gebruiken voor het uitvoeren van het notebook. Deze virtuele machines (VM's) zijn vooraf geconfigureerd met [alles wat u nodig hebt om Azure Machine Learning uit te voeren](concept-compute-instance.md#contents).

1. Als er geen VM’s zijn gevonden, selecteert u **+ Toevoegen** om de rekenproces-VM te maken.

    1. Volg deze regels wanneer u een VM maakt:
 
        + Een naam is verplicht en het veld mag niet leeg zijn.
        + De naam moet uniek zijn (en is hoofdlettergevoelig) voor alle bestaande rekenprocessen in de Azure-regio van de werkruimte of het rekenproces. U krijgt een waarschuwing als de naam die u kiest niet uniek is.
        + Geldige tekens zijn hoofdletters, kleine letters, cijfers van 0 t/m 9 en het streepje (-).
        + De naam moet tussen 3 en 24 tekens lang zijn.
        + De naam moet met een letter beginnen, niet met een cijfer of streepje.
        + Als er een streepje wordt gebruikt, moet dit worden gevolgd door ten minste één letter. Test-, test-0 en test-01 zijn bijvoorbeeld ongeldig, maar test-a0 en test-0a zijn geldig.

    1. Selecteer de VM-grootte in de beschikbare keuzes. Voor de zelfstudies is de standaard-VM een goede keuze.

    1. Selecteer vervolgens **Maken**. Het kan ongeveer vijf minuten duren voordat uw VM is voorbereid.

1. Wanneer de VM beschikbaar is, wordt deze weergegeven op de bovenste werkbalk. U kunt het notebook nu uitvoeren door **Alles uitvoeren** op de werkbalk te gebruiken of door **Shift+Enter** in de codecellen van het notebook te gebruiken.

Als u aangepaste widgets hebt of liever Jupyter of JupyterLab gebruikt, selecteert u de vervolgkeuzelijst **Jupyter** helemaal rechts. Selecteer vervolgens **Jupyter** of **JupyterLab**. Het nieuwe browservenster wordt geopend.

## <a name="next-steps"></a>Volgende stappen

Nu u een ontwikkelomgeving hebt ingesteld, gaat u verder met het trainen van een model in een Jupyter Notebook.

> [!div class="nextstepaction"]
> [Zelfstudie: Modellen voor de classificatie van afbeeldingen trainen met MNIST-gegevens en scikit-learn](tutorial-train-models-with-aml.md)

<a name="stop-compute-instance"></a> Als u nu niet van plan bent om andere zelfstudies te volgen, moet u de VM van de cloudnotebook-server stoppen wanneer u deze niet gebruikt, om de kosten te verlagen.

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

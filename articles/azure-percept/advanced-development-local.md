---
title: Aan de slag met Azure percept Advanced Development lokaal
description: Aan de slag met lokale machine learning ontwikkeling met behulp van VS code + Jupyter-notebooks op AzureML
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/10/2021
ms.custom: template-tutorial
ms.openlocfilehash: 88e71ac21177a13d30176212e97442c63272eca6
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101664661"
---
# <a name="getting-started-with-machine-learning-development-using-vs-code--jupyter-notebooks-on-azureml"></a>Aan de slag met machine learning ontwikkeling met behulp van VS code + Jupyter-notebooks op AzureML

Dit artikel begeleidt u bij het instellen van een Azure Machine Learning-werk ruimte, het maken van een reken instantie, het instellen van een Visual Studio code Development Environment op uw lokale computer en het uitvoeren van de cellen van een vooraf geconfigureerde Jupyter-notebook in VS code.

Het [notitie blok](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb) voert overboeking Learning uit met behulp van een vooraf getraind tensor flow model (MobileNetSSDV2Lite) op AzureML in python met een aangepaste gegevensset om Bowlen te detecteren.

In het notitie blok ziet u hoe u begint met de [Coco-gegevensset](https://cocodataset.org/#home), hoe u deze kunt filteren op alleen de klasse van belang (Bowlen) en deze vervolgens converteren naar de juiste indeling. U kunt ook het hulp programma voor het labelen van open-source [VoTT 2](https://github.com/microsoft/VoTT) gebruiken om selectie vakjes in de Pascal VOC-indeling te maken en aan te labelen.

Nadat het model op de aangepaste gegevensset is getraind, kan het model worden geïmplementeerd in uw Azure percept DK met behulp van de module dubbele update methode. U kunt vervolgens de methode voor het afleiden van het model controleren door de Live RTSP-stroom van het Azure percept Vision-SoM weer te geven. Model retraining en-implementatie worden binnen het notitie blok uitgevoerd via uw externe Compute-instantie.

## <a name="prerequisites"></a>Vereisten

- [Azure Machine Learning abonnement](https://azure.microsoft.com/free/services/machine-learning/)
- [Azure percept DK met Azure percept Vision SoM verbonden](./overview-azure-percept-dk.md)
- De [on-boarding-ervaring van Azure PERCEPT DK](./quickstart-percept-dk-set-up.md) is voltooid

## <a name="download-azure-percept-github-repository"></a>Azure percept GitHub-opslag plaats downloaden

1. Ga naar de [github-opslag plaats van Azure PERCEPT DK](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview).
1. Kloon de opslag plaats of down load het ZIP-bestand. Klik aan de bovenkant van het scherm op   ->  **Post code downloaden**.

    :::image type="content" source="./media/advanced-development-local/download-zip.png" alt-text="GitHub-Download scherm.":::

## <a name="create-an-azure-machine-learning-workspace-and-remote-compute-instance"></a>Een Azure Machine Learning-werk ruimte en een extern reken exemplaar maken

1. Ga naar de [Azure machine learning portal](https://ml.azure.com).
1. Selecteer uw directory, Azure-abonnement en Machine Learning werk ruimte in de vervolg keuzelijsten en klik op **aan de slag**.

    :::image type="content" source="./media/advanced-development-local/machine-learning-portal-get-started.png" alt-text="Het scherm aan de slag van Azure ML.":::

    Als u nog geen Azure Machine Learning-werk ruimte hebt, klikt u op **een nieuwe werk ruimte maken**. Ga als volgt te werk op het tabblad nieuwe browser:

    1. Selecteer uw Azure-abonnement.
    1. Selecteer uw resourcegroep.
    1. Voer een naam in voor uw werk ruimte.
    1. Kies uw regio.
    1. Selecteer uw werkruimte versie.
    1. Klik op **Controleren en maken**.
    1. Controleer uw selecties op de volgende pagina en klik op **maken**.

        :::image type="content" source="./media/advanced-development-local/workspace-review-and-create.png" alt-text="Scherm voor het maken van de werk ruimte in azure ML.":::

    Wacht enkele minuten tot het maken van de werk ruimte is toegestaan. Nadat het maken van de werk ruimte is voltooid, ziet u groene vinkjes naast uw resources en **is uw implementatie** aan de bovenkant van de pagina overzicht van Machine Learning Services.

    :::image type="content" source="./media/advanced-development-local/workspace-creation-complete-inline.png" alt-text="Bevestiging van het maken van de werk ruimte." lightbox= "./media/advanced-development-local/workspace-creation-complete.png":::

    Nadat het maken van de werk ruimte is voltooid, gaat u terug naar het tabblad machine learning portal en klikt u op **aan de slag**.

1. Klik op de start pagina van de werk ruimte machine learning op **Compute** in het linkerdeel venster.

1. Als er geen bestaand reken exemplaar is, maakt u een nieuwe CPU of GPU door te klikken op **Nieuw**. Voer in het venster **New Compute instance** een **Compute name** in, kies een **type virtuele machine** en selecteer de grootte van een **virtuele machine**. Klik op **Create**.

    :::image type="content" source="./media/advanced-development-local/new-compute-instance.png" alt-text="Nieuw scherm voor het maken van reken instanties.":::

    Nadat u op **maken** hebt geklikt, duurt het enkele minuten om het reken exemplaar te maken. **\<your compute name> Met** de **reken** status wordt een groene cirkel weer gegeven wanneer het maken van de berekening is voltooid. Deze reken instantie voert de Jupyter-server uit en wordt gebruikt voor deze zelf studie.

## <a name="visual-studio-code-development-environment-setup"></a>Setup van Visual Studio code Development Environment

1. Installeer de vereiste hulpprogram ma's.

    1. Optie 1:

        Gebruik het [installatie programma dev tools pack](./dev-tools-installer.md) om de volgende pakketten te installeren:

        - Visual Studio Code
        - Python 3,5, 3,6 of 3,7
        - Miniconda3
        - Intel openwijn Toolkit 2020,2

        Opmerking: de Intel-Toolkit wordt vermeld als een optioneel pakket in het installatie programma dev tools pack, maar is wel vereist voor het werken met het Azure percept Vision som-som van de Azure percept DK Development Kit.

    1. Optie 2:

        Als u liever geen gebruik wilt maken van het installatie programma dev tools pack, installeert u de volgende pakketten hand matig door te klikken op de onderstaande koppelingen en de opgegeven down load-en installatie richtlijnen te volgen:

        - [Visual Studio Code](https://code.visualstudio.com/)
        - [Python 3,5, 3,6 of 3,7](https://www.python.org/)
        - [Miniconda3](https://docs.conda.io/en/latest/miniconda.html)
        - [Intel openwijn Toolkit 2020,2](https://docs.openvinotoolkit.org/)

    Opmerking: als u de volledige Anaconda-distributie al hebt geïnstalleerd, hoeft u Miniconda niet te installeren. Als u liever Anaconda of Miniconda wilt gebruiken, kunt u ook een virtuele python-omgeving maken en de pakketten installeren die nodig zijn voor het uitvoeren van uw AI-model ontwikkeling met behulp van PIP.

1. Start Visual Studio Code.
1. Een Data Science-omgeving instellen:

    - Optie 1: Maak verbinding met een bestaande of nieuwe machine learning externe Compute-instantie die al de pakketten met de curator heeft. **Dit is de optie die wordt gebruikt in deze zelf studie**.

    - Optie 2: Stel een Conda-omgeving in op een lokale machine.
        1. Open een Anaconda-of Miniconda-opdracht prompt en voer de volgende opdracht uit om een omgeving met de naam **myenv** te maken met de opgegeven pakketten:

            `conda create -n myenv python=3.7 pandas jupyter seaborn scikit-learn keras tensorflow=1.15`

            Zie de [Anaconda-documentatie](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html)voor meer informatie over het maken en beheren van Anaconda-omgevingen.

1. Een VS code-werk ruimte maken:

    1. Maak een map op een handige locatie die u kunt gebruiken als uw Visual Studio code-werk ruimte. Noem deze **myworkspace**.
    1. Open **myworkspace** in Visual Studio code door te klikken op **bestand**  >  **openen** map  >  **map selecteren**.
    1. Ga in Verkenner naar en selecteer het [Transferlearningusing_SSDLiteV2 model. ipynbb-bestand](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb) van uw lokale kopie van de Azure percept DK github-opslag plaats. Kopieer dit notitieblok bestand naar de map myworkspace.

## <a name="azure-integration-with-visual-studio-code"></a>Azure-integratie met Visual Studio code

1. Als u dat nog niet hebt gedaan, kunt u zich aanmelden voor de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree).

1. Installeer de volgende Azure-extensies voor VS code:
    - [Azure machine learning extensie](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai).
    - Extensie van python-insiders. In VS code gaat u naar het  ->  **opdracht palet** weer geven. Voer in het opdracht palet het volgende in en selecteer **python: overschakelen naar een dagelijks kanaal voor insiders**.
    - [Azure-account extensie](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account). Laad uw venster opnieuw in VS code wanneer u hierom wordt gevraagd.
    - [Extensie van Azure IOT hub Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).
    - [Azure IOT Edge extensie](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).

1. Meld u aan bij uw Azure-account in Visual Studio code om resources in te richten en werk belastingen uit te voeren op Azure.
    1. Open het opdracht palet in Visual Studio code door opdracht **palet weer geven** te selecteren in  >   de menu balk.
    1. Voer **Azure in: Meld** u aan bij het opdracht palet om het aanmeldings proces te starten.

        :::image type="content" source="./media/advanced-development-local/transfer-learning-azure-sign-in-inline.png" alt-text="Azure-aanmeldings scherm." lightbox= "./media/advanced-development-local/transfer-learning-azure-sign-in.png":::

    1. Activeer de python-uitbrei ding en het Azure-account door te klikken op het pictogram van Azure aan de linkerkant van VS code.

        :::image type="content" source="./media/advanced-development-local/azure-icon.png" alt-text="Azure-pictogram in VS code.":::

    1. Open de Transferlearningusing_SSDLiteV2-notebook Model_VSCode. ipynb uit de map **myworkspace** .
    1. Open het opdracht palet. Selecteer **python: Geef een lokale of externe Jupyter server op voor verbindingen**.
    1. U ziet een lijst met verbindings opties waaruit u kunt kiezen. Selecteer **Azure ml Compute exemplaren**.

        :::image type="content" source="./media/advanced-development-local/azure-machine-learning-compute-instances.png" alt-text="Opties voor Compute-exemplaren van Azure ML in VS code.":::

    1. Volg de begeleide prompts om een abonnement, werk ruimte en extern Compute-exemplaar te kiezen. Selecteer de werk ruimte en het externe Compute-exemplaar dat eerder in deze zelf studie is gemaakt. U kunt ook een nieuw reken exemplaar maken.
    1. Nadat u een reken instantie hebt geselecteerd, wordt u gevraagd het VS code-venster opnieuw te laden. Wanneer u opnieuw hebt geladen, selecteert u de **Python 3,6-AzureML-** kernel. U kunt nu een van de cellen in uw notitie blok uitvoeren. Als u een notebook-cel uitvoert, wordt de verbinding tussen uw notitie blok en uw reken exemplaar gemaakt. De werk balk van het notitie blok wordt bijgewerkt, zodat deze overeenkomt met het reken exemplaar waaraan u werkt.

        :::image type="content" source="./media/advanced-development-local/kernel-inline.png" alt-text="De kernel is geselecteerd in VS code." lightbox= "./media/advanced-development-local/kernel.png":::

## <a name="working-with-the-notebook"></a>Werken met het notitie blok

U bent nu klaar om het notitie blok uit te voeren om uw aangepaste Bowl detector in VS code te trainen en te implementeren in uw Devkit. Zorg ervoor dat elke cel van de notebook afzonderlijk wordt uitgevoerd, aangezien sommige cellen invoer parameters vereisen voordat het script wordt uitgevoerd. Cellen waarvoor invoer parameters zijn vereist, kunnen rechtstreeks in het notitie blok worden bewerkt. Als u een cel wilt uitvoeren, klikt u op het pictogram afspelen aan de linkerkant van de cel:

:::image type="content" source="./media/advanced-development-local/run-cell-1.png" alt-text="Pictogram voor het markeren van een notitie blok.":::

## <a name="next-steps"></a>Volgende stappen

Ga naar deze [opslag plaats](https://github.com/Azure/MachineLearningNotebooks/tree/2aa7c53b0ce84e67565d77e484987714fdaed36e/how-to-use-azureml)voor meer Azure machine learning service-voorbeeld notitieblokken.

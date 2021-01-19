---
title: 'Zelfstudie: Een model trainen en implementeren - Machine Learning op Azure IoT Edge'
description: In deze zelfstudie traint u een machine learning-model met behulp van Azure Machine Learning en verpakt u het model vervolgens als een containerinstallatiekopie die kan worden geïmplementeerd als een Azure IoT Edge-module.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/24/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 2cc96db88d9a2aec02de5e2fc4ed18b445972e7b
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121129"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>Zelfstudie: Een Azure Machine Learning-model trainen en implementeren

In dit artikel voeren we de volgende taken uit:

* Gebruik Azure Machine Learning Studio om een machine learning-model te trainen.
* Het getrainde model verpakken als een containerinstallatiekopie.
* De containerinstallatiekopie implementeren als een Azure IoT Edge-module.

De Azure Machine Learning Studio is een basisblok dat u gebruikt voor het experimenteren, trainen en implementeren van machine learning-modellen.

De stappen in dit artikel worden doorgaans uitgevoerd door gegevenswetenschappers.

In dit deel van de zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Maak Jupyter-notebooks in Azure Machine Learning-werkruimte om een machine learning-model te trainen.
> * Het getrainde model in een container plaatsen.
> * Maak een Azure IoT Edge-module vanuit het machine learning-model in de container.

## <a name="prerequisites"></a>Vereisten

Dit artikel maakt deel uit van een reeks voor een zelfstudie over het gebruik van Azure Machine Learning in IoT Edge. Elk artikel in de reeks is gebaseerd op het werk in het vorige artikel. Als u rechtstreeks bij dit artikel terecht bent gekomen, gaat u naar de [eerste artikel](tutorial-machine-learning-edge-01-intro.md) in de reeks.

## <a name="set-up-azure-machine-learning"></a>Azure Machine Learning instellen 

We gebruiken Azure Machine Learning Studio om de twee Jupyter Notebooks en ondersteunende bestanden te hosten. Hier maken en configureren we een Azure Machine Learning-project. Als u Jupyter en/of Azure Machine Learning Studio niet eerder hebt gebruikt, volgen hier enkele inleidende documenten:

* **Jupyter-notebooks:** [Werken met Jupyter Notebooks in Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support)
* **Azure Machine Learning:** [Aan de slag met de Azure Machine Learning in Jupyter Notebooks](../machine-learning/tutorial-1st-experiment-sdk-setup.md)


> [!NOTE]
> Zodra de Azure Machine Learning service is ingesteld, kan deze vanaf elke computer worden geopend. Tijdens het instellen moet u de ontwikkelings-VM gebruiken. Deze bevat alle bestanden die u nodig hebt.

### <a name="install-azure-machine-learning-visual-studio-code-extension"></a>De Azure Machine Learning Visual Studio Code-extensie installeren
VS-code op de ontwikkelings-VM moet deze extensie hebben geïnstalleerd. Als u werkt met een ander exemplaar, moet u de extensie opnieuw installeren zoals [hier](../machine-learning/tutorial-setup-vscode-extension.md) wordt beschreven.

### <a name="create-an-azure-machine-learning-account"></a>Een Azure Machine Learning-account maken  
Als u resources wilt inrichten en workloads op Azure wilt uitvoeren, moet u zich aanmelden met de referenties van uw Azure-account.

1. Open in Visual Studio Code het opdrachtpalet door **Beeld** > **Opdrachtpalet** uit de menubalk te selecteren. 

1. Voer de opdracht `Azure: Sign In` in in het opdrachtpalet om het aanmeldingsproces te starten. Volg de instructies om het aanmelden te voltooien. 

1. Maak een Azure ML rekenproces om uw werkbelasting uit te voeren. Opdrachtpalet gebruiken om de opdracht `Azure ML: Create Compute` in te voeren. 
1. Selecteer uw Azure-abonnement
1. Selecteer **+ Nieuwe Azure ML-werkruimte maken** en voer de naam `turbofandemo` in.
1. Selecteer de resourcegroep die u voor deze demo hebt gebruikt.
1. U moet de voortgang kunnen zien van het maken van de werkruimte in de rechterbenedenhoek van het VS Code-venster: **Werkruimte 'turobofandemo' wordt gemaakt** (dit kan een of twee minuten duren). 
1. Wacht tot de werkruimte succesvol is gemaakt. Er zou moeten staan: **Azure ML-werkruimte 'turbofandemo' is gemaakt**.


### <a name="upload-jupyter-notebook-files"></a>Jupyter-notebookbestanden uploaden

Er worden voorbeeldnotebookbestanden geüpload naar een nieuwe Azure ML-werkruimte.

1. Navigeer naar ml.azure.com en meld u aan.
1. Selecteer uw Microsoft-map, Azure-abonnement en de zojuist gemaakte Azure ML-werkruimte.

    :::image type="content" source="media/tutorial-machine-learning-edge-04-train-model/select-studio-workspace.png" alt-text="Selecteer uw Azure ML-werkruimte." :::

1. Wanneer u bent aangemeld bij uw Azure ML-werkruimte, gaat u naar de sectie **Notebooks** in het menu aan de linkerkant.
1. Selecteer het tabblad **Mijn bestanden**.

1. **Uploaden** selecteren (het pictogram pijl-omhoog) 


1. Ga naar **C:\source\IoTEdgeAndMlSample\AzureNotebooks**. Selecteer alle bestanden in de lijst en klik op **Openen**.

1. Schakel **Ik vertrouw de inhoud van deze bestanden** in.

1. Selecteer **Uploaden** om te beginnen met uploaden en selecteer vervolgens **Gereed** zodra het proces is voltooid.

### <a name="jupyter-notebook-files"></a>Jupyter-notebookbestanden

Laten we kijken welke bestanden u hebt geüpload naar uw Azure ML-werkruimte. De activiteiten in dit deel van de zelfstudie beslaan twee notebook-bestanden, die enkele ondersteunende bestanden gebruiken.

* **01-turbofan\_regression.ipynb:** Dit notebook gebruikt de Machine Learning Service-werkruimte om een machine learning-experiment te maken en uit te voeren. De notebook voert grofweg de volgende stappen uit:

  1. Het downloadt gegevens van de Azure Storage-account die werd gegenereerd door het apparaatharnas.
  1. Verkent de gegevens, bereid deze voor, en gebruikt de gegevens vervolgens om het classificatiemodel te trainen.
  1. Evalueer het model vanuit het experiment met een testgegevensset (test\_FD003.txt).
  1. Publiceert het beste classificatiemodel naar de Machine Learning Service-werkruimte.

* **02-turbofan\_deploy\_model.ipynb:** Dit notebook haalt het model op dat in het vorige notebook is gemaakt en gebruikt dit om een containerinstallatiekopie te maken die kan worden geïmplementeerd op een Azure IoT Edge-apparaat. Het notebook voert de volgende stappen uit:

  1. Maakt een scorescript voor het model.
  1. Produceert een containerinstallatiekopie met behulp van het classificatiemodel dat werd opgeslagen in de Machine Learning Service-werkruimte.
  1. Implementeert de installatiekopie als een webservice op Azure-containerinstantie.
  1. Maakt gebruik van de webservice om te valideren of het model en de installatiekopie werken zoals verwacht. De gevalideerde installatiekopie wordt geïmplementeerd op het IoT Edge-apparaat in het gedeelte [Aangepaste IoT Edge-modules maken en implementeren](tutorial-machine-learning-edge-06-custom-modules.md) van deze zelfstudie.

* **Test\_FD003.txt:** Dit bestand bevat de gegevens die we als onze testset gaan gebruiken bij het valideren van onze getrainde classificatie. We hebben er voor het gemak voor gekozen om de testgegevens te gebruiken zoals die werden opgegeven voor de oorspronkelijke wedstrijd.

* **RUL\_FD003.txt:** Dit bestand bevat de Resterende levensduur (RUL) voor de laatste cyclus van elk apparaat in het bestand Test\_FD003.txt. Raadpleeg de readme.txt en de Damage Propagation Modeling.pdf-bestanden in C:\\source\\IoTEdgeAndMlSample\\data\\Turbofan voor een gedetailleerde uitleg van de gegevens.

* **Utils.py:** Bevat een set Python-hulpprogramma's voor het werken met gegevens. Het eerste notebook bevat een gedetailleerde uitleg van de functies.

* **README.md:** Leesmij-bestand met een beschrijving van het gebruik van de notebooks.  

## <a name="run-jupyter-notebooks"></a>Jupyter Notebooks uitvoeren

Nu de werkruimte is gemaakt, kunt u de notebooks uitvoeren. 

1. Selecteer, op uw pagina **Mijn bestanden**, **01-turbofan\_ regression.ipynb**.

    :::image type="content" source="media/tutorial-machine-learning-edge-04-train-model/select-turbofan-notebook.png" alt-text="Selecteer het eerste notebook dat moet worden uitgevoerd. ":::

1. Als het notebook wordt weergegeven als **Niet vertrouwd**, klikt u op de widget **Niet vertrouwd** in de rechter bovenhoek van het notebook. Wanneer het dialoogvenster wordt geopend, selecteert u **Vertrouwen**.

1. Voor de beste resultaten raadpleegt u de documentatie voor elke cel en voert u deze afzonderlijk uit. Selecteer **Uitvoeren** op de werkbalk. Later kan het handig zijn om meerdere cellen uit te voeren. U kunt waarschuwingen voor upgrades en afschaffing negeren.

    Wanneer een cel wordt uitgevoerd, wordt een sterretje weergegeven tussen vierkante haken ([\*]). Wanneer de bewerking van de cel is voltooid, wordt het sterretje vervangen door een getal en wordt de relevante uitvoer weergegeven. De cellen in een notebook worden opeenvolgend opgebouwd en er kan maar één cel tegelijk draaien.

    U kunt ook uitvoeropties gebruiken in het menu **Cel**, `Ctrl` + `Enter` om een cel uit te voeren en `Shift` + `Enter` om een cel uit te voeren en door te gaan naar de volgende cel.

    > [!TIP]
    > Vermijd het uitvoeren van hetzelfde notebook vanaf meerdere tabbladen in uw browser, voor consistente bewerkingen van cellen.

1. In de cel die volgt op de instructies **Globale eigenschappen instellen**, schrijft u de waarden voor uw Azure-abonnement, instellingen en resources. Vervolgens voert u de cel uit.

    ![Globale eigenschappen instellen in het notebook](media/tutorial-machine-learning-edge-04-train-model/set-global-properties.png)

1. Ga in de cel vóór **Details werkruimte**, nadat deze is uitgevoerd, op zoek naar de link die u opdracht geeft om aan te melden om een verificatie uit te voeren:

    ![Aanmeldingsprompt voor verificatie van apparaat](media/tutorial-machine-learning-edge-04-train-model/sign-in-prompt.png)

    Open de link en voer de opgegeven code in. Met deze aanmeldingsprocedure wordt de Jupyter-notebook geverifieerd voor toegang tot Azure-resources met behulp van de platformoverschrijdende opdrachtregelinterface van Microsoft Azure.  

    ![Toepassing verifiëren bij bevestiging van apparaat](media/tutorial-machine-learning-edge-04-train-model/cross-platform-cli.png)

1. Kopieer de waarde uit de run-id in de cel voorafgaand aan **De resultaten verkennen** en plak deze voor de run-id in de cel die volgt op **Een run opnieuw samenstellen**.

   ![Kopieer de run-id tussen cellen](media/tutorial-machine-learning-edge-04-train-model/automl-id.png)

1. Voer de resterende cellen in het notebook uit.

1. Sla het notebook op en ga terug naar de projectpagina.

1. Open **02-turbofan\_deploy\_model.ipynb** en voer elke cel uit. U moet zich aanmelden om te verifiëren in de cel die volgt op **Werkruimte configureren**.

1. Sla het notebook op en ga terug naar de projectpagina.

### <a name="verify-success"></a>Controleren geslaagd

Controleer of er enkele items zijn gemaakt om te controleren of de notebooks zijn voltooid.

1. Ga naar uw Azure ML-notebooks. In het tabblad **Mijn bestanden** selecteert u vervolgens **Vernieuwen**.

1. Controleer of de volgende bestanden zijn gemaakt:

    | File | Beschrijving |
    | --- | --- |
    | ./aml_config/.azureml/config.json | Het configuratiebestand dat wordt gebruikt om de Azure Machine Learning-werkruimte te maken. |
    | ./aml_config/model_config.json | Het configuratiebestand dat u nodig hebt om het model te implementeren in de **turbofanDemo** Machine Learning-werkruimte in Azure. |
    | myenv.yml| Bevat informatie over de afhankelijkheden voor het geïmplementeerde Machine Learning-model.|

1. Controleer of de volgende Azure-resources zijn gemaakt. Aan sommige namen van resources zijn willekeurige tekens toegevoegd.

    | Azure-resource | Name |
    | --- | --- |
    | Werkruimte voor Machine Learning | turborfanDemo |
    | Container Registry | turbofandemoxxxxxxxx |
    | Application Insights | turbofaninsightxxxxxxxx |
    | Key Vault | turbofankeyvaultbxxxxxxxx |
    | Storage | turbofanstoragexxxxxxxxx |

### <a name="debugging"></a>Foutopsporing

U kunt een Python-instructie invoegen in het notebook voor foutopsporing, zoals de opdracht `print()` om waarden weer te geven. Als er variabelen of objecten worden weergeven die niet zijn gedefinieerd, voert u de cellen uit waar ze voor het eerst zijn gedeclareerd of geïnstantieerd.

Mogelijk moet u eerder gemaakte bestanden en Azure-resources verwijderen als u de notebooks opnieuw moet uitvoeren.

## <a name="clean-up-resources"></a>Resources opschonen

Deze zelfstudie maakt deel uit van een reeks, waarvan elk artikel is gebaseerd op het werk dat in de voorgaande artikelen is uitgevoerd. Wacht met het opschonen van resources totdat u de laatste zelfstudie hebt uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebben we twee Jupyter-notebooks gebruikt die worden uitgevoerd in Azure ML Studio. De gegevens van de turbofan-apparaten zijn gebruikt om een resterende levensduur (RUL)-classificatie te trainen, om de classificatie als een model op te slaan, om een containerinstallatiekopie te maken en de installatiekopie te testen als een webservice.

Ga verder naar het volgende artikel om een IoT Edge-apparaat te maken.

> [!div class="nextstepaction"]
> [Een IoT Edge-apparaat configureren](tutorial-machine-learning-edge-05-configure-edge-device.md)
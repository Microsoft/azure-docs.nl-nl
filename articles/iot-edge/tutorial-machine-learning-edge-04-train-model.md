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
ms.openlocfilehash: 757e34fd45b7d3d9703aa09daa7f040c5f605637
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932384"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>Zelfstudie: Een Azure Machine Learning-model trainen en implementeren

In dit artikel voeren we de volgende taken uit:

* Azure Notebooks gebruiken om een machine learning-model te trainen.
* Het getrainde model verpakken als een containerinstallatiekopie.
* De containerinstallatiekopie implementeren als een Azure IoT Edge-module.

De Azure Notebooks profiteren van een Azure Machine Learning-werkruimte, een basisblok dat u gebruikt voor het experimenteren, trainen en implementeren van machine learning-modellen.

De stappen in dit artikel worden doorgaans uitgevoerd door gegevenswetenschappers.

In dit deel van de zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Een Azure Notebooks-project maken om een machine learning-model te trainen.
> * Het getrainde model in een container plaatsen.
> * Maak een Azure IoT Edge-module vanuit het machine learning-model in de container.

## <a name="prerequisites"></a>Vereisten

Dit artikel maakt deel uit van een reeks voor een zelfstudie over het gebruik van Azure Machine Learning in IoT Edge. Elk artikel in de reeks is gebaseerd op het werk in het vorige artikel. Als u rechtstreeks bij dit artikel terecht bent gekomen, gaat u naar de [eerste artikel](tutorial-machine-learning-edge-01-intro.md) in de reeks.

## <a name="set-up-azure-notebooks"></a>Azure Notebooks instellen

We gebruiken Azure Notebooks om de twee Jupyter Notebooks en ondersteunende bestanden te hosten. Hier maken en configureren we een Azure Notebooks-project. Als u Jupyter en/of Azure Notebooks niet eerder hebt gebruikt, volgen hier enkele inleidende documenten:

* **Snelstart:** [Een notebook maken en delen](../notebooks/quickstart-create-share-jupyter-notebook.md)
* **Zelfstudie:** [Een Jupyter-notebook maken en uitvoeren met Python](../notebooks/tutorial-create-run-jupyter-notebook.md)

Het gebruik van Azure Notebooks zorgt voor een consistente omgeving voor de oefening.

> [!NOTE]
> Zodra de Azure Notebooks-service is ingesteld, kan deze vanaf elke computer worden geopend. Tijdens het instellen moet u de ontwikkelings-VM gebruiken. Deze bevat alle bestanden die u nodig hebt.

### <a name="create-an-azure-notebooks-account"></a>Een Azure Notebooks-account maken

Als u Azure Notebooks wilt gebruiken, moet u een account maken. Azure notebook-accounts zijn onafhankelijk van Azure-abonnementen.

1. Ga naar [Azure Notebooks](https://notebooks.azure.com).

1. Klik in de rechterbovenhoek van de pagina op **Aanmelden**.

1. Meld u aan met uw werk- of schoolaccount (Azure Active Directory) of uw persoonlijke account (Microsoft-account).

1. Als u Azure Notebooks nog niet eerder hebt gebruikt, wordt u gevraagd toegang te verlenen voor de Azure Notebooks-app.

1. Een gebruikers-id voor Azure Notebooks maken.

### <a name="upload-jupyter-notebook-files"></a>Jupyter-notebookbestanden uploaden

Er worden voorbeeldnotebookbestanden geüpload naar een nieuw Azure Notebooks-project.

1. Selecteer op de gebruikerspagina van uw nieuwe account **Mijn projecten** in de bovenste menubalk.

1. Voeg een nieuw project toe door de knop **+** te selecteren.

1. Geef een **Projectnaam** op in het dialoogvenster **Nieuw project maken**. 

1. Laat **Openbaar** en **LEESMIJ** uitgeschakeld omdat het project niet openbaar hoeft te zijn of een leesmij hoeft te hebben.

1. Selecteer **Maken**.

1. Selecteer **Uploaden** (het pictogram pijl omhoog) en kies **Van computer**.

1. Selecteer **Bestanden kiezen**.

1. Ga naar **C:\source\IoTEdgeAndMlSample\AzureNotebooks**. Selecteer alle bestanden in de lijst en klik op **Openen**.

1. Schakel **Ik vertrouw de inhoud van deze bestanden** in.

1. Selecteer **Uploaden** om te beginnen met uploaden en selecteer vervolgens **Gereed** zodra het proces is voltooid.

### <a name="azure-notebook-files"></a>Azure Notebook-bestanden

Laten we eens kijken welke bestanden u hebt geüpload naar uw Azure Notebooks-project. De activiteiten in dit deel van de zelfstudie beslaan twee notebook-bestanden, die enkele ondersteunende bestanden gebruiken.

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

## <a name="run-azure-notebooks"></a>Azure Notebooks uitvoeren

Nu het project is gemaakt, kunt u de notebooks uitvoeren. 

1. Selecteer vanaf uw projectpagina **01-turbofan\_regression.ipynb**.

    ![Selecteer het eerste notebook dat moet worden uitgevoerd](media/tutorial-machine-learning-edge-04-train-model/select-turbofan-regression-notebook.png)

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

1. Selecteer op de projectpagina van Azure Notebooks **Verborgen items weergeven** zodat itemnamen die met een punt beginnen, worden weergegeven.

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

In dit artikel hebben we twee Jupyter-notebooks gebruikt die worden uitgevoerd in Azure Notebooks om de gegevens van de turbofan-apparaten te gebruiken om een resterende levensduur (RUL)-classificatie te trainen, om de classificatie als een model op te slaan, om een containerinstallatiekopie te maken en de installatiekopie te testen als een webservice.

Ga verder naar het volgende artikel om een IoT Edge-apparaat te maken.

> [!div class="nextstepaction"]
> [Een IoT Edge-apparaat configureren](tutorial-machine-learning-edge-05-configure-edge-device.md)

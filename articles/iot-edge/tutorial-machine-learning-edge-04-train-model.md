---
title: 'Zelfstudie: Een model trainen en implementeren - Machine Learning op Azure IoT Edge'
description: In deze zelf studie traint u een machine learning model met behulp van Azure Machine Learning en verpakken u het model als een container installatie kopie die kan worden geïmplementeerd als een Azure IoT Edge-module.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/24/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5129cc71dd3edae14350225e9c9dd944b05a6b4a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101740168"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>Zelfstudie: Een Azure Machine Learning-model trainen en implementeren

In dit artikel voeren we de volgende taken uit:

* Gebruik Azure Machine Learning Studio om een machine learning-model te trainen.
* Het getrainde model verpakken als een containerinstallatiekopie.
* De containerinstallatiekopie implementeren als een Azure IoT Edge-module.

Machine Learning Studio is een basis blok dat wordt gebruikt om machine learning modellen te experimenteren, te trainen en te implementeren.

De stappen in dit artikel worden doorgaans uitgevoerd door gegevenswetenschappers.

In deze sectie van de zelf studie leert u het volgende:

> [!div class="checklist"]
> * Maak Jupyter-notebooks in een Azure Machine Learning-werk ruimte om een machine learning model te trainen.
> * Het getrainde model in een container plaatsen.
> * Maak een IoT Edge module vanuit het model machine learning in de container.

## <a name="prerequisites"></a>Vereisten

Dit artikel maakt deel uit van een reeks voor een zelf studie over het gebruik van Machine Learning op IoT Edge. Elk artikel in de reeks is gebaseerd op het werk in het vorige artikel. Als u in dit artikel rechtstreeks hebt gearriveerd, raadpleegt u het [eerste artikel](tutorial-machine-learning-edge-01-intro.md) in de reeks.

## <a name="set-up-azure-machine-learning"></a>Azure Machine Learning instellen

We gebruiken Machine Learning Studio om de twee Jupyter-notebooks en ondersteunende bestanden te hosten. Hier maken en configureren we een Machine Learning project. Als u geen Jupyter of Machine Learning Studio hebt gebruikt, zijn hier twee inleidende documenten:

* **Jupyter notebook**: [werken met Jupyter-notebooks in Visual Studio code](https://code.visualstudio.com/docs/python/jupyter-support)
* **Azure machine learning**: [aan de slag met Azure machine learning in Jupyter-notebooks](../machine-learning/tutorial-1st-experiment-sdk-setup.md)

> [!NOTE]
> Nadat de service is ingesteld, kunt u vanaf elke computer toegang tot Machine Learning krijgen. Tijdens de installatie moet u de ontwikkelings-VM gebruiken. Deze bevat alle bestanden die u nodig hebt.

### <a name="install-azure-machine-learning-visual-studio-code-extension"></a>De Azure Machine Learning Visual Studio Code-extensie installeren

Voor Visual Studio code op de ontwikkelings-VM moet deze extensie zijn geïnstalleerd. Als u werkt met een ander exemplaar, installeert u de extensie opnieuw zoals beschreven in [de Visual Studio code extension instellen](../machine-learning/tutorial-setup-vscode-extension.md).

### <a name="create-an-azure-machine-learning-account"></a>Een Azure Machine Learning-account maken

Als u resources wilt inrichten en werk belastingen wilt uitvoeren op Azure, meldt u zich aan met de referenties van uw Azure-account.

1. Open in Visual Studio Code het opdrachtpalet door **Beeld** > **Opdrachtpalet** uit de menubalk te selecteren.

1. Voer de opdracht `Azure: Sign In` in het opdracht palet in om het aanmeldings proces te starten. Volg de instructies om het aanmelden te voltooien.

1. Maak een Machine Learning Compute-exemplaar om uw werk belasting uit te voeren. Voer de opdracht in het palet opdracht in `Azure ML: Create Compute` .
1. Selecteer uw Azure-abonnement.
1. Selecteer **+ nieuwe Azure-ml-werkruimte maken** en voer de naam **turbofandemo** in.
1. Selecteer de resource groep die u voor deze demo hebt gebruikt.
1. U ziet de voortgang van het maken van de werk ruimte in de rechter benedenhoek van het venster Visual Studio code: **werk ruimte maken: turobofandemo**. Deze stap kan een paar minuten duren.
1. Wacht totdat de werk ruimte is gemaakt. Er zou moeten staan: **Azure ML-werkruimte 'turbofandemo' is gemaakt**.

### <a name="upload-jupyter-notebook-files"></a>Jupyter-notebookbestanden uploaden

Er worden voorbeeld notitieblok bestanden geüpload naar een nieuwe Machine Learning-werk ruimte.

1. Ga naar ml.azure.com en meld u aan.
1. Selecteer uw micro soft-Directory, Azure-abonnement en de zojuist gemaakte Machine Learning-werk ruimte.

    :::image type="content" source="media/tutorial-machine-learning-edge-04-train-model/select-studio-workspace.png" alt-text="Scherm opname van het selecteren van uw Azure Machine Learning-werk ruimte." :::

1. Nadat u zich hebt aangemeld bij uw Machine Learning-werk ruimte, gaat u naar de sectie **notebooks** in het menu aan de linkerkant.
1. Selecteer het tabblad **Mijn bestanden**.

1. Selecteer **uploaden** (het pictogram pijl-omhoog).

1. Ga naar **C:\source\IoTEdgeAndMlSample\AzureNotebooks**. Selecteer alle bestanden in de lijst en selecteer **openen**.

1. Schakel het selectie vakje **Ik vertrouw de inhoud van deze bestanden** in.

1. Selecteer **uploaden** om te beginnen met uploaden. Selecteer vervolgens **gereed** nadat het proces is voltooid.

### <a name="jupyter-notebook-files"></a>Jupyter-notebookbestanden

Laten we de bestanden bekijken die u hebt geüpload naar uw Machine Learning-werk ruimte. De activiteiten in dit deel van de zelfstudie beslaan twee notebook-bestanden, die enkele ondersteunende bestanden gebruiken.

* **01-turbofan \_ regressie. ipynb**: dit notitie blok gebruikt de machine learning-werk ruimte om een machine learning experiment te maken en uit te voeren. De notebook voert grofweg de volgende stappen uit:

  1. Het downloadt gegevens van de Azure Storage-account die werd gegenereerd door het apparaatharnas.
  1. Hiermee worden de gegevens verkend en voor bereid, en worden de gegevens gebruikt om het classificatie model te trainen.
  1. Evalueert het model uit het experiment met behulp van een test gegevensset (test \_FD003.txt).
  1. Hiermee wordt het beste classificatie model gepubliceerd naar de Machine Learning-werk ruimte.

* **02-turbofan \_ implementeren \_ model. ipynb**: dit notitie blok haalt het model op dat in het vorige notitie blok is gemaakt en gebruikt dit om een container installatie kopie te maken die kan worden geïmplementeerd op een IOT edge apparaat. Het notebook voert de volgende stappen uit:

  1. Maakt een scorescript voor het model.
  1. Produceert een container installatie kopie met behulp van het classificatie model dat is opgeslagen in de werk ruimte Machine Learning.
  1. Implementeert de installatie kopie als een webservice op Azure Container Instances.
  1. Maakt gebruik van de webservice om te valideren of het model en de installatiekopie werken zoals verwacht. De gevalideerde installatiekopie wordt geïmplementeerd op het IoT Edge-apparaat in het gedeelte [Aangepaste IoT Edge-modules maken en implementeren](tutorial-machine-learning-edge-06-custom-modules.md) van deze zelfstudie.

* **Test \_FD003.txt**: dit bestand bevat de gegevens die we als onze test instellen wanneer we onze getrainde classificatie valideren. We hebben er voor het gemak voor gekozen om de testgegevens te gebruiken zoals die werden opgegeven voor de oorspronkelijke wedstrijd.
* **Resterende levens duur \_FD003.txt**: dit bestand bevat de resterende levens duur (resterende levens duur) voor de laatste cyclus van elk apparaat in de test \_FD003.txt bestand. Raadpleeg het readme.txt en de Modeling.pdf bestanden voor Beschadigings doorgifte in C: \\ Source \\ IoTEdgeAndMlSample \\ Data \\ turbofan voor een gedetailleerde uitleg van de gegevens.
* **Utils.py**: dit bestand bevat een set python-hulp programma functies voor het werken met gegevens. Het eerste notebook bevat een gedetailleerde uitleg van de functies.
* **README.MD**: in dit Leesmij-bestand wordt het gebruik van de notitie blokken beschreven.

## <a name="run-the-jupyter-notebooks"></a>De Jupyter-notebooks uitvoeren

Nu de werkruimte is gemaakt, kunt u de notebooks uitvoeren.

1. Selecteer, op uw pagina **Mijn bestanden**, **01-turbofan\_ regression.ipynb**.

    :::image type="content" source="media/tutorial-machine-learning-edge-04-train-model/select-turbofan-notebook.png" alt-text="Scherm afbeelding met de selectie van het eerste notitie blok dat moet worden uitgevoerd.":::

1. Als het notitie blok niet wordt **vertrouwd**, selecteert u het **niet-vertrouwde** object in de rechter bovenhoek van het notitie blok. Wanneer het dialoog venster wordt weer gegeven, selecteert u **vertrouwen**.

1. Voor de beste resultaten raadpleegt u de documentatie voor elke cel en voert u deze afzonderlijk uit. Selecteer **Uitvoeren** op de werkbalk. Later vindt u het verstandig om meerdere cellen uit te voeren. U kunt waarschuwingen voor upgrades en afschaffing negeren.

    Wanneer een cel wordt uitgevoerd, wordt een sterretje weergegeven tussen vierkante haken ([\*]). Wanneer de bewerking van de cel is voltooid, wordt het sterretje vervangen door een getal en de relevante uitvoer kan worden weer gegeven. De cellen in een notitie blok worden opeenvolgend samengesteld en er kan maar één cel tegelijk worden uitgevoerd.

    U kunt ook de uitvoer opties in het menu **cel** gebruiken. Selecteer **CTRL + ENTER** om een cel uit te voeren en selecteer **SHIFT + ENTER** om een cel uit te voeren en door te gaan naar de volgende cel.

    > [!TIP]
    > Vermijd het uitvoeren van hetzelfde notebook vanaf meerdere tabbladen in uw browser, voor consistente bewerkingen van cellen.

1. Voer in de cel die voldoet aan de instructies **globale eigenschappen instellen** de waarden in voor uw Azure-abonnement, instellingen en resources. Vervolgens voert u de cel uit.

    ![Scherm opname van de instelling van algemene eigenschappen in het notitie blok.](media/tutorial-machine-learning-edge-04-train-model/set-global-properties.png)

1. Zoek in de cel vorige naar **werkruimte Details**, nadat deze is uitgevoerd, naar de koppeling waarmee u zich aanmeldt om u aan te melden bij verificatie.

    ![Scherm opname van de aanmeldings prompt voor de verificatie van het apparaat.](media/tutorial-machine-learning-edge-04-train-model/sign-in-prompt.png)

    Open de koppeling en voer de opgegeven code in. Met deze aanmeldings procedure wordt de Jupyter-notebook geverifieerd voor toegang tot Azure-resources met behulp van de platformoverschrijdende opdracht regel interface van Microsoft Azure.

    ![Scherm afbeelding van de verificatie toepassing bij bevestiging van het apparaat.](media/tutorial-machine-learning-edge-04-train-model/cross-platform-cli.png)

1. Kopieer de waarde uit de run-id in de cel voorafgaand aan **De resultaten verkennen** en plak deze voor de run-id in de cel die volgt op **Een run opnieuw samenstellen**.

   ![Scherm opname van het kopiëren van de uitvoerings-ID tussen cellen.](media/tutorial-machine-learning-edge-04-train-model/automl-id.png)

1. Voer de resterende cellen in het notebook uit.

1. Sla het notitie blok op en ga terug naar de pagina van uw project.

1. Open **02-turbofan \_ Deploy \_ model. ipynb** en voer elke cel uit. U moet zich aanmelden om te verifiëren in de cel die volgt op **werk ruimte configureren**.

1. Sla het notitie blok op en ga terug naar de pagina van uw project.

### <a name="verify-success"></a>Controleren geslaagd

Controleer of er enkele items zijn gemaakt om te controleren of de notebooks zijn voltooid.

1. Selecteer op uw Machine Learning notitie blokken **mijn bestanden** tabblad **vernieuwen**.

1. Controleer of de volgende bestanden zijn gemaakt.

    | File | Beschrijving |
    | --- | --- |
    | ./aml_config/.azureml/config.json | Configuratie bestand dat wordt gebruikt om de Machine Learning-werk ruimte te maken. |
    | ./aml_config/model_config.json | Configuratie bestand dat u nodig hebt om het model in de **turbofanDemo** machine learning-werk ruimte in azure te implementeren. |
    | myenv.yml| Bevat informatie over de afhankelijkheden voor het geïmplementeerde Machine Learning-model.|

1. Controleer of de volgende Azure-resources zijn gemaakt. Sommige resource namen worden toegevoegd met wille keurige tekens.

    | Azure-resource | Name |
    | --- | --- |
    | Azure Machine Learning-werkruimte | turborfanDemo |
    | Azure Container Registry | turbofandemoxxxxxxxx |
    | Application Insights | turbofaninsightxxxxxxxx |
    | Azure Key Vault | turbofankeyvaultbxxxxxxxx |
    | Azure Storage | turbofanstoragexxxxxxxxx |

### <a name="debugging"></a>Foutopsporing

U kunt een Python-instructie invoegen in het notebook voor foutopsporing, zoals de opdracht `print()` om waarden weer te geven. Als er variabelen of objecten worden weer geven die niet zijn gedefinieerd, voert u de cellen uit waar ze voor het eerst zijn gedeclareerd of geïnstantieerd.

Mogelijk moet u eerder gemaakte bestanden en Azure-resources verwijderen als u de notitie blokken opnieuw wilt uitvoeren.

## <a name="clean-up-resources"></a>Resources opschonen

Deze zelfstudie maakt deel uit van een reeks, waarvan elk artikel is gebaseerd op het werk dat in de voorgaande artikelen is uitgevoerd. Wacht tot de resources zijn opgeruimd totdat u de laatste zelf studie hebt voltooid.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebben we twee Jupyter-notebooks gebruikt die in Machine Learning Studio worden uitgevoerd om de gegevens van de turbofan-apparaten te gebruiken om het volgende te doen:

- Train een resterende levens duur-classificatie.
- Sla de classificatie op als een model.
- Een container installatie kopie maken.
- Implementeer en test de installatie kopie als een webservice.

Ga verder naar het volgende artikel om een IoT Edge-apparaat te maken.

> [!div class="nextstepaction"]
> [Een IoT Edge-apparaat configureren](tutorial-machine-learning-edge-05-configure-edge-device.md)
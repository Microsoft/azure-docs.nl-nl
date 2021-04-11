---
title: 'Zelfstudie: Azure Machine Learning implementeren op een apparaat met Azure IoT Edge'
description: In deze zelfstudie maakt u een Azure Machine Learning-model en implementeert u deze als een module op een edge-apparaat
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/29/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: b3192c3ee8458623f3724da5f875cc09032a42bf
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106219413"
---
# <a name="tutorial-deploy-azure-machine-learning-as-an-iot-edge-module-preview"></a>Zelfstudie: Azure Machine Learning als een IoT Edge-module implementeren (preview)

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Gebruik Azure Notebooks om een machine learning module te ontwikkelen en te implementeren op een apparaat met Azure IoT Edge met Linux-containers.
U kunt IoT Edge-modules gebruiken voor het implementeren van code die uw bedrijfslogica rechtstreeks op uw IoT Edge-apparaten implementeert. Deze zelfstudie laat u stapsgewijs zien hoe u een Azure Machine Learning-module kunt implementeren waarmee wordt voorspeld wanneer een apparaat mislukt op basis van gesimuleerde machinetemperatuurgegevens. Zie [Documentatie voor Azure Machine Learning](../machine-learning/how-to-deploy-and-where.md) voor meer informatie over Azure Machine Learning op IoT Edge.

>[!NOTE]
>Azure Machine Learning-modules in Azure IoT Edge zijn in de openbare preview.

De Azure Machine Learning-module die u in deze zelfstudie maakt, leest de omgevingsgegevens die door uw apparaat zijn gegenereerd en markeert de berichten als afwijkend of niet.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Een Azure Machine Learning-module maken.
> * Een modulecontainer naar een Azure-containerregister pushen.
> * Een Azure Machine Learning-module implementeren op uw IoT Edge-apparaat.
> * Gegenereerde gegevens weergeven.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Een Azure IoT Edge-apparaat:

* U kunt de Quick starts gebruiken om een [Linux-apparaat](quickstart-linux.md) of [Windows-apparaat](quickstart.md)in te stellen.
* De Azure Machine Learning-module ondersteunt geen Windows-containers.
* De Azure Machine Learning-module ondersteunt geen ARM-processoren.

Cloudresources:

* Een gratis of standaard [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)-laag in Azure.
* Een Azure Machine Learning-werkruimte. Volg de instructies in [Azure Portal gebruiken om aan de slag te gaan met Azure Machine Learning](../machine-learning/tutorial-1st-experiment-sdk-setup.md) om er een te maken en te leren hoe u deze kunt gebruiken.
  * Noteer de naam van de werkruimte, de resourcegroep en de abonnements-id. Deze waarden zijn allemaal beschikbaar in het overzicht van de werkruimte in de Azure-portal. U gebruikt deze waarden later in de zelfstudie om een Azure Notebooks-bestand aan uw werkruimteresources te koppelen.

## <a name="create-and-deploy-azure-machine-learning-module"></a>Een Azure Machine Learning-module maken en implementeren

In deze sectie converteert u bestanden van het getrainde Machine Learning-model en plaatst u deze in een Azure Machine Learning-container. Alle onderdelen die vereist zijn voor de Docker-installatiekopie bevinden zich in de [AI-werkset voor Azure IoT Edge Git-repo](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial). Volg deze stappen om de opslagplaats te uploaden naar Microsoft Azure Notebooks om de container te maken en deze naar Azure Container Registry te pushen.

1. Navigeer naar uw Azure Notebooks-projecten. U kunt dit doen vanuit uw Azure Machine Learning-werkruimte in de [Azure-portal](https://portal.azure.com) of door u aan te melden bij [Microsoft Azure Notebooks](https://notebooks.azure.com/home/projects) met uw Azure-account.

2. Selecteer **GitHub-opslagplaats uploaden**.

3. Geef de volgende naam op voor de GitHub-opslagplaats: `Azure/ai-toolkit-iot-edge`. Schakel het selectievakje **Openbaar** uit als u uw project privé wilt houden. Selecteer **Importeren**.

4. Als het importeren is voltooid, gaat u naar het nieuwe **ai-toolkit-iot-edge**-project en opent u de map **IoT Edge anomaly detection tutorial**.

5. Controleer of het project actief is. Als dat niet het geval is, selecteert u **Uitvoeren op gratis Compute**.

   ![Uitvoeren op gratis Compute](./media/tutorial-deploy-machine-learning/run-on-free-compute.png)

6. Open het bestand **aml_config/config.json**.

7. Bewerk het configuratiebestand om de waarden voor uw Azure-abonnements-id, een resourcegroep in uw abonnement en de naam van uw Azure Machine Learning-werkruimte op te nemen. U kunt al deze waarden ophalen uit de sectie **Overzicht** van uw werkruimte in Azure.

8. Sla het configuratiebestand op.

9. Open het bestand **00-anomaly-detection-tutorial.ipynb**.

10. Als u hierom wordt gevraagd, selecteert u de kernel **Python 3.6** en selecteert u vervolgens **Kernel instellen**.

11. Bewerk de eerste cel in de notebook volgens de instructies in de opmerkingen. Gebruik dezelfde resourcegroep, abonnements-id en werkruimtenaam die u aan het configuratiebestand hebt toegevoegd.

12. Voer de cellen in de notebook uit door ze te selecteren en **Uitvoeren** te kiezen of op `Shift + Enter` te drukken.

    >[!TIP]
    >Sommige van de cellen in de notebook voor de zelfstudie over anomaliedetectie zijn optioneel, omdat ze resources maken die sommige gebruikers al dan niet kunnen hebben, zoals een IoT-hub. Als u de bestaande resourcegegevens in de eerste cel plaatst, ontvangt u fouten als u de cellen uitvoert die nieuwe resources maken, omdat Azure geen dubbele resources maakt. Dit is een geen probleem en u kunt de fouten negeren of deze optionele secties overslaan.

Door alle stappen in de notebook te voltooien, hebt u een model voor anomaliedetectie getraind, het gebouwd als een Docker-containerinstallatiekopie en die installatiekopie naar Azure Container Registry gepusht. Vervolgens hebt u het model getest en het geïmplementeerd op uw IoT Edge-apparaat.

## <a name="view-container-repository"></a>Containeropslagplaats bekijken

Controleer of de containerinstallatiekopie is gemaakt en opgeslagen in het Azure-containerregister dat is gekoppeld aan de machine learning-omgeving. De notebook die u in de vorige sectie hebt gebruikt, heeft automatisch de containerinstallatiekopie en de registerreferenties aan uw IoT Edge-apparaat verstrekt. Het is wel handig om te weten waar ze zijn opgeslagen, zodat u de informatie later kunt vinden.

1. Ga in de [Azure-portal](https://portal.azure.com) naar uw Machine Learning Service-werkruimte.

2. In de sectie **Overzicht** worden de werkruimtegegevens en de bijbehorende resources weergegeven. Selecteer de waarde **Register**, die uw werkruimtenaam gevolgd door willekeurige getallen zou moeten zijn.

3. Selecteer in het containerregister onder **Services** de optie **Opslagplaatsen**. Als het goed is, ziet u een opslagplaats met de naam **tempanomalydetection** die is gemaakt door de notebook die u in de vorige sectie hebt uitgevoerd.

4. Selecteer **tempanomalydetection**. De opslagplaats zou één tag moeten hebben: **1**.

   Nu u de naam van het register, de naam van de opslagplaats en de tag kent, weet u het volledige pad naar de installatiekopie van de container. Installatiekopiepaden zien eruit als **\<registry_name\>.azurecr.io/tempanomalydetection:1**. U kunt het pad naar de installatiekopie gebruiken om deze container naar IoT Edge-apparaten te implementeren.

5. Selecteer in het containerregister onder **Instellingen** de optie **Toegangssleutels**. U zou nu een aantal toegangsreferenties moeten zien, waaronder de **aanmeldingsserver** en de **gebruikersnaam** en het **wachtwoord** voor een gebruiker met beheerdersrechten.

   Deze referenties kunnen worden opgenomen in het distributiemanifest om uw IoT Edge-apparaat toestemming te geven containerinstallatiekopieën uit het register op te halen.

U weet nu waar de Machine Learning-containerinstallatie kopie is opgeslagen. In de volgende sectie worden de stappen beschreven voor het weergeven van de container die wordt uitgevoerd als een module op uw IoT Edge-apparaat.

## <a name="view-the-generated-data"></a>De gegenereerde gegevens weergeven

U kunt berichten weergeven die worden gegenereerd vanaf elke IoT Edge-module, en u kunt berichten weergeven die worden bezorgd bij de IoT Edge-hub.

### <a name="view-data-on-your-iot-edge-device"></a>Gegevens weergeven op het IoT Edge-apparaat

Op het IoT Edge-apparaat kunt u de berichten weergeven die worden verzonden vanaf elke afzonderlijke module.

Mogelijk moet u `sudo` gebruiken voor verhoogde machtigingen om `iotedge`-opdrachten uit te voeren. Als u zich afmeldt en opnieuw aanmeldt bij uw apparaat, worden uw machtigingen automatisch bijgewerkt.

1. Geef alle modules op het IoT Edge-apparaat weer.

   ```cmd/sh
   iotedge list
   ```

2. Geef de berichten weer die zijn verzonden vanaf een specifiek apparaat. Gebruik de modulenaam uit de uitvoer van de vorige opdracht.

   ```cmd/sh
   iotedge logs <module_name> -f
   ```

### <a name="view-data-arriving-at-your-iot-hub"></a>Gegevens weergeven die binnenkomen bij de IoT-hub

U kunt de apparaat-naar-cloud-berichten die het IoT Edge-apparaat ontvangt, weergeven met behulp van de [Azure IoT Hub-extensie voor Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

In de volgende stappen ziet u hoe u Visual Studio Code kunt instellen om apparaat-naar-cloud-berichten te controleren die binnenkomen bij de IoT-hub.

1. Vouw in de Visual Studio Code Explorer, onder de sectie **Azure IoT Hub**, de optie **Apparaten** uit om de lijst met IoT-apparaten weer te geven.

2. Klik met de rechtermuisknop op de naam van uw IoT Edge-apparaat en selecteer **Bewaking van ingebouwd gebeurteniseindpunt starten**.

3. Bekijk de berichten die de tempSensor elke vijf seconden verzendt. De berichttekst bevat een eigenschap genaamd **afwijking** die de machinelearningmodule voorziet van een waarde 'waar' of 'onwaar'. De eigenschap **AzureMLResponse** bevat de waarde ‘OK’ als het model is uitgevoerd.

   ![Reactie van Azure Machine Learning in de hoofdtekst van het bericht](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent door te gaan met het volgende aanbevolen artikel, kunt u de resources en configuraties die u hebt gemaakt behouden en opnieuw gebruiken. U kunt ook hetzelfde IoT Edge-apparaat blijven gebruiken als een testapparaat.

Anders kunt u de lokale configuraties en Azure-resources die u in dit artikel hebt gemaakt, verwijderen om kosten te voorkomen.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een IoT Edge-module geïmplementeerd die werd aangedreven door Azure Machine Learning. U kunt doorgaan met elke andere zelfstudie om te leren waarmee Azure IoT Edge u nog meer kan helpen uw gegevens om te zetten in bedrijfsinzichten.

> [!div class="nextstepaction"]
> [Afbeeldingen classificeren met Custom Vision Service](tutorial-deploy-custom-vision.md)

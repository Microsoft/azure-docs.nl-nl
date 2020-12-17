---
title: Bekende problemen bij het oplossen van problemen &
titleSuffix: Azure Machine Learning
description: Ontvang hulp bij het vinden en corrigeren van fouten of fouten in Azure Machine Learning. Meer informatie over bekende problemen, probleem oplossing en tijdelijke oplossingen.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: troubleshooting
ms.custom: troubleshooting, contperf-fy20q4
ms.date: 11/09/2020
ms.openlocfilehash: aa0a14d57db932ef6cfb17df84b3204d3dec9e4d
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97616997"
---
# <a name="known-issues-and-troubleshooting-in-azure-machine-learning"></a>Bekende problemen en probleemoplossing in Azure Machine Learning

Dit artikel helpt u bij het oplossen van bekende problemen die kunnen optreden bij het gebruik van Azure Machine Learning. 

Zie [volgende stappen](#next-steps) aan het einde van dit artikel voor meer informatie over het oplossen van problemen.

> [!TIP]
> Fouten of andere problemen kunnen het resultaat zijn van [resource quota](how-to-manage-quotas.md) die u tegen komt bij het werken met Azure machine learning. 

## <a name="access-diagnostic-logs"></a>Toegang tot diagnostische logboeken

Soms kan het nuttig zijn als u Diagnostische gegevens kunt opgeven wanneer u om hulp vraagt. Voor een overzicht van de logboeken: 
1. Ga naar [Azure machine learning Studio](https://ml.azure.com). 
1. Selecteer aan de linkerkant **experimenteren** 
1. Selecteer een experiment.
1. Selecteer een run.
1. Selecteer bovenaan **uitvoer en logboeken**.

> [!NOTE]
> Azure Machine Learning registreert gegevens uit verschillende bronnen tijdens de training, zoals AutoML of de docker-container die de trainings taak uitvoert. Veel van deze logboeken zijn niet gedocumenteerd. Als u problemen ondervindt en contact opneemt met micro soft ondersteuning, kunnen ze deze logboeken gebruiken tijdens het oplossen van problemen.


## <a name="installation-and-import"></a>Installatie en import
                           
* **PIP-installatie: afhankelijkheden zijn niet gegarandeerd consistent met de installatie van één regel:** 

   Dit is een bekende beperking van PIP, omdat er geen werkende afhankelijkheids conflict Oplosser is wanneer u als één regel installeert. De eerste unieke afhankelijkheid is de enige die er wordt weer gegeven. 

   In de volgende code `azureml-datadrift` en `azureml-train-automl` worden beide geïnstalleerd met behulp van een PIP-installatie van één regel. 
     ```
       pip install azureml-datadrift, azureml-train-automl
     ```
   Voor dit voor beeld `azureml-datadrift` is versie > 1,0 vereist en `azureml-train-automl` is versie < 1,2 vereist. Als de meest recente versie van `azureml-datadrift` is 1,3, worden beide pakketten bijgewerkt naar 1,3, ongeacht de vereiste van het `azureml-train-automl` pakket voor een oudere versie. 

   Als u ervoor wilt zorgen dat de juiste versies voor uw pakketten zijn geïnstalleerd, installeert u met meerdere regels, zoals in de volgende code. Volg orde is hier geen probleem, omdat PIP expliciet downgradet als onderdeel van de volgende regel aanroep. Dit betekent dat de juiste versie afhankelijkheden worden toegepast.
    
     ```
        pip install azureml-datadrift
        pip install azureml-train-automl 
     ```
     
* **Het uitleg pakket wordt niet gegarandeerd geïnstalleerd tijdens de installatie van de azureml-Train-automl-client:** 
   
   Wanneer u een externe AutoML uitvoert met model uitleg is ingeschakeld, wordt er een fout bericht weer gegeven: ' Installeer het pakket met de azureml-uitleg-model voor de beschrijving van het model '. Dit is een bekend probleem. Volg een van de onderstaande stappen als tijdelijke oplossing:
  
  1. Installeer het model voor azureml-uitleg-lokaal.
   ```
      pip install azureml-explain-model
   ```
  2. Schakel de uitleg functie volledig uit door model_explainability = False door te geven in de configuratie van AutoML.
   ```
      automl_config = AutoMLConfig(task = 'classification',
                             path = '.',
                             debug_log = 'automated_ml_errors.log',
                             compute_target = compute_target,
                             run_configuration = aml_run_config,
                             featurization = 'auto',
                             model_explainability=False,
                             training_data = prepped_data,
                             label_column_name = 'Survived',
                             **automl_settings)
    ``` 
    
* **Panda-fouten: normaal gezien tijdens het AutoML-experiment:**
   
   Wanneer u uw omgeving hand matig kunt instellen met behulp van PIP, kunt u kenmerk fouten (met name van Panda) ondervinden omdat er niet-ondersteunde pakket versies worden geïnstalleerd. Als u dergelijke fouten wilt voor komen, [installeert u de AUTOML SDK met behulp van de automl_setup. cmd](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md):
   
    1. Open een Anaconda-prompt en kloon de GitHub-opslag plaats voor een aantal voorbeeld notitieblokken.

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```
    
    2. cd naar de map How-to-use-azureml/automated machine learning waarin de voorbeeld notitieblokken zijn geëxtraheerd en vervolgens worden uitgevoerd:
    
    ```bash
    automl_setup
    ```
    
* **Fout: ' brand ' bij het uitvoeren van AutoML op een lokale Compute-of Azure Databricks-cluster**

    Als er na 10 juni 2020 een nieuwe omgeving is gemaakt met behulp van SDK 1.7.0 of eerder, kan de training mislukken met deze fout vanwege een update in het py-cpuinfo-pakket. (Omgevingen die zijn gemaakt op of vóór 10 juni 2020, worden niet beïnvloed, evenals experimenten die op externe Compute worden uitgevoerd, omdat er trainings afbeeldingen in de cache worden gebruikt.) U kunt dit probleem omzeilen door een van de volgende twee stappen uit te voeren:
    
    * Werk de SDK-versie bij naar 1.8.0 of hoger (dit is ook een downgrade van py-cpuinfo naar 5.0.0):
    
      ```bash
      pip install --upgrade azureml-sdk[automl]
      ```
    
    * Downgrade de geïnstalleerde versie van py-cpuinfo naar 5.0.0:
    
      ```bash
      pip install py-cpuinfo==5.0.0
      ```
  
* **Fout bericht: kan PyYAML niet verwijderen**

    Azure Machine Learning SDK voor python: PyYAML is een `distutils` geïnstalleerd project. Daarom kunnen we niet nauw keurig bepalen welke bestanden er bij gedeeltelijk verwijderen horen. Gebruik het volgende om door te gaan met het installeren van de SDK en het negeren van deze fout:
    
    ```Python
    pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
    ```

* **Azure Machine Learning SDK-installatie mislukt met een uitzonde ring: ModuleNotFoundError: geen module met de naam ruamel of ImportError: geen module met de naam ruamel. yaml**
   
   Dit probleem wordt ontdekt met de installatie van Azure Machine Learning SDK voor python op de laatste PIP (>20.1.1) in de Conda-basis omgeving voor alle uitgebrachte versies van Azure Machine Learning SDK voor python. Raadpleeg de volgende tijdelijke oplossingen:

    * Vermijd het installeren van python SDK in de Conda-basis omgeving, in plaats daarvan uw Conda-omgeving te maken en SDK te installeren op die zojuist gemaakte gebruikers omgeving. De meest recente PIP moet werken op die nieuwe Conda-omgeving.

    * Voor het maken van installatie kopieën in docker, waar u niet uit de Conda-basis omgeving kunt scha kelen, moet u 20.1.1 in het docker-bestand vast<maken.

    ```Python
    conda install -c r -y conda python=3.6.2 pip=20.1.1
    ```
    
* **Databricks-fout bij het installeren van pakketten**

    Azure Machine Learning SDK-installatie mislukt op Azure Databricks wanneer er meer pakketten zijn geïnstalleerd. Sommige pakketten, zoals `psutil` , kunnen conflicten veroorzaken. Installeer pakketten door de bibliotheek versie te blok keren om installatie fouten te voor komen. Dit probleem heeft betrekking op Databricks en niet op de Azure Machine Learning SDK. Dit probleem kan ook optreden met andere bibliotheken. Voorbeeld:
    
    ```python
    psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
    ```

    U kunt ook init-scripts gebruiken als u problemen ondervindt bij de installatie van python-bibliotheken. Deze aanpak wordt niet officieel ondersteund. Zie voor meer informatie [cluster-scoped init-scripts](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

* **Databricks import-fout: kan naam niet importeren `Timedelta` van `pandas._libs.tslibs`**: als u deze fout ziet wanneer u automatische machine learning gebruikt, voert u de twee volgende regels uit in uw notitie blok:
    ```
    %sh rm -rf /databricks/python/lib/python3.7/site-packages/pandas-0.23.4.dist-info /databricks/python/lib/python3.7/site-packages/pandas
    %sh /databricks/python/bin/pip install pandas==0.23.4
    ```

* **Databricks-import fout: geen module met de naam ' Pandas. core. indices '**: als u deze fout ziet wanneer u gebruikmaakt van automatische machine learning:

    1. Voer deze opdracht uit om twee pakketten te installeren in uw Azure Databricks cluster:
    
       ```bash
       scikit-learn==0.19.1
       pandas==0.22.0
       ```
    
    1. Ontkoppel het cluster en koppel het vervolgens opnieuw aan uw notitie blok.
    
    Als u met deze stappen het probleem niet kunt oplossen, probeert u het cluster opnieuw op te starten.

* **Databricks FailToSendFeather**: als er een `FailToSendFeather` fout wordt weer gegeven bij het lezen van gegevens op Azure Databricks cluster, raadpleegt u de volgende oplossingen:
    
    * Upgrade `azureml-sdk[automl]` pakket naar de nieuwste versie.
    * Voeg `azureml-dataprep` versie 1.1.8 of hoger toe.
    * Voeg `pyarrow` versie 0,11 of hoger toe.
    
## <a name="create-and-manage-workspaces"></a>Werk ruimten maken en beheren

> [!WARNING]
> Het verplaatsen van uw Azure Machine Learning-werk ruimte naar een ander abonnement of het verplaatsen van het abonnement dat eigenaar is naar een nieuwe Tenant, wordt niet ondersteund. Dit kan fouten veroorzaken.

* **Azure Portal**: 
  * Als u rechtstreeks naar uw werk ruimte gaat vanuit een koppeling voor delen vanuit de SDK of de Azure Portal, kunt u de standaard **overzichts** pagina met abonnements gegevens in de uitbrei ding niet weer geven. In dit scenario kunt u ook niet overschakelen naar een andere werk ruimte. Als u een andere werk ruimte wilt weer geven, gaat u rechtstreeks naar [Azure machine learning Studio](https://ml.azure.com) en zoekt u naar de naam van de werk ruimte.
  * Alle assets (gegevens sets, experimenten, reken processen, enzovoort) zijn alleen beschikbaar in [Azure machine learning Studio](https://ml.azure.com). Deze zijn *niet* beschikbaar via de Azure Portal.

* **Ondersteunde browsers in azure machine learning Studio-webportal**: we raden u aan om de meest recente browser te gebruiken die compatibel is met uw besturings systeem. De volgende browsers worden ondersteund:
  * Micro soft Edge (de nieuwe micro soft Edge, nieuwste versie. Geen micro soft Edge verouderd)
  * Safari (meest recente versie, alleen Mac)
  * Chrome (meest recente versie)
  * Firefox (meest recente versie)

## <a name="set-up-your-environment"></a>Uw omgeving instellen

* **Problemen** met het maken van AmlCompute: er is een zeldzame kans dat sommige gebruikers die hun Azure machine learning werk ruimte hebben gemaakt vanaf de Azure Portal vóór de Ga-release mogelijk geen AmlCompute in die werk ruimte kunnen maken. U kunt een ondersteunings aanvraag voor de service verhogen of een nieuwe werk ruimte maken via de portal of de SDK om uzelf direct te deblokkeren.

* **Azure container Registry biedt momenteel geen ondersteuning voor Unicode-tekens in namen van resource groepen**: het is mogelijk dat ACR-aanvragen mislukken omdat de naam van de resource groep Unicode-tekens bevat. Om dit probleem te verhelpen, raden we u aan om een ACR te maken in een resource groep met een andere naam.

## <a name="work-with-data"></a>Werken met gegevens

### <a name="overloaded-azurefile-storage"></a>Overbelaste AzureFile-opslag

Als er een fout bericht wordt weer gegeven `Unable to upload project files to working directory in AzureFile because the storage is overloaded` , moet u de volgende tijdelijke oplossingen Toep assen.

Als u bestands share gebruikt voor andere werk belastingen, zoals gegevens overdracht, is de aanbeveling om blobs te gebruiken zodat de bestands share gratis kan worden gebruikt voor het verzenden van uitvoeringen. U kunt de werk belasting ook splitsen tussen twee verschillende werk ruimten.

### <a name="passing-data-as-input"></a>Gegevens door geven als invoer

*  **TypeError: FileNotFound: bestand of map**: deze fout treedt op als het bestandspad dat u opgeeft niet waar het bestand zich bevindt. U moet ervoor zorgen dat de manier waarop u naar het bestand verwijst consistent is met de locatie waar u uw gegevensset op het reken doel hebt gekoppeld. We raden u aan het abstracte pad te gebruiken bij het koppelen van een gegevensset aan een reken doel om te zorgen voor een deterministische status. In de volgende code koppelen we bijvoorbeeld de gegevensset aan onder de hoofdmap van het bestands systeem van het berekenings doel `/tmp` . 
    
    ```python
    # Note the leading / in '/tmp/dataset'
    script_params = {
        '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
    } 
    ```

    Als u de voorloop back slash (/) niet opneemt, moet u voor voegsel van de werkmap opgeven, bijvoorbeeld `/mnt/batch/.../tmp/dataset` op het berekenings doel om aan te geven waar u de gegevensset wilt koppelen.

### <a name="mount-dataset"></a>Gegevensset koppelen
* **Het initialiseren van de gegevensset is mislukt: er is een time-out opgetreden bij het wachten op een koppelings punt**: 
  * Als u geen regels voor uitgaande [netwerk beveiligings groepen](https://docs.microsoft.com/azure/virtual-network/network-security-groups-overview) hebt en deze wilt gebruiken, moet u `azureml-sdk>=1.12.0` bijwerken `azureml-dataset-runtime` en zijn afhankelijkheden het meest recent voor de specifieke secundaire versie, of als u deze in een uitvoering gebruikt, moet u de omgeving opnieuw maken zodat deze de meest recente patch kan hebben met de oplossing. 
  * Als u gebruikt, moet u een `azureml-sdk<1.12.0` upgrade uitvoeren naar de nieuwste versie.
  * Als u regels voor uitgaande NSG hebt, moet u ervoor zorgen dat er een regel voor uitgaande verbindingen is waarmee al het verkeer voor de servicetag kan worden toegestaan `AzureResourceMonitor` .

### <a name="data-labeling-projects"></a>Projecten voor gegevenslabels

|Probleem  |Oplossing  |
|---------|---------|
|Alleen gegevens sets die zijn gemaakt op BLOB-gegevens opslag kunnen worden gebruikt.     |  Dit is een bekende beperking van de huidige release.       |
|Nadat het project is gemaakt, wordt de ' initialisatie ' gedurende lange tijd weer gegeven.     | Vernieuw de pagina hand matig. De initialisatie moet ongeveer 20 data Points per seconde door lopen. Het ontbreken van automatisch vernieuwen is een bekend probleem.         |
|Bij het controleren van afbeeldingen worden nieuwe gelabelde afbeeldingen niet weer gegeven.     |   Als u alle gelabelde afbeeldingen wilt laden, kiest u de **eerste** knop. Met de **eerste** knop gaat u terug naar de voor kant van de lijst, maar worden alle gelabelde gegevens geladen.      |
|Druk op ESC-toets terwijl labels voor object detectie een label met een grootte van nul maken in de linkerbovenhoek. Het verzenden van labels met deze status mislukt.     |   Verwijder het label door te klikken op de kruis markering ernaast.  |

### <a name="data-drift-monitors"></a><a name="data-drift"></a> Data drift-monitors

Beperkingen en bekende problemen voor gegevens drift-monitors:

* Het tijds bereik voor het analyseren van historische gegevens is beperkt tot 31 intervallen van de frequentie-instelling van de monitor. 
* Beperking van 200-functies, tenzij er geen functie lijst is opgegeven (alle gebruikte functies).
* De reken grootte moet groot genoeg zijn om de gegevens te kunnen verwerken.
* Zorg ervoor dat uw gegevensset gegevens bevat binnen de begin-en eind datum voor een bepaalde monitor uitvoering.
* De monitors van de gegevensset werken alleen voor gegevens sets die 50 rijen of meer bevatten.
* Kolommen, of functies, worden in de gegevensset geclassificeerd als categorische of numeriek op basis van de voor waarden in de volgende tabel. Als de functie niet aan deze voor waarden voldoet, bijvoorbeeld een kolom van het type teken reeks met de unieke waarden van >100, wordt de functie verwijderd uit onze data drift-algoritme, maar is nog steeds profileeerd. 

    | Onderdeel type | Gegevenstype | Voorwaarde | Beperkingen | 
    | ------------ | --------- | --------- | ----------- |
    | Categorische gegevens | teken reeks, BOOL, int, float | Het aantal unieke waarden in de functie is kleiner dan 100 en minder dan 5% van het aantal rijen. | NULL wordt beschouwd als een eigen categorie. | 
    | Cijfer | int, float | De waarden in de functie zijn van een numeriek gegevens type en voldoen niet aan de voor waarde voor een categorische-functie. | De functie is verwijderd als >15% van de waarden null zijn. | 

* Wanneer u [een gegevensdrijf monitor hebt gemaakt](how-to-monitor-datasets.md) , maar de gegevens op de pagina **gegevensset** controleren in azure machine learning Studio niet kunt zien, kunt u het volgende proberen.

    1. Controleer of u het juiste datum bereik hebt geselecteerd boven aan de pagina.  
    1. Op het tabblad **gegevensset monitors** selecteert u de koppeling experiment om de uitvoerings status te controleren.  Deze koppeling bevindt zich helemaal rechts in de tabel.
    1. Als de uitvoering is voltooid, controleert u de logboeken van het stuur programma om te zien hoeveel metrische gegevens er zijn gegenereerd of of er waarschuwingen zijn.  Zoek het stuur programma-Logboeken op het tabblad **uitvoer en logboeken** nadat u op een experiment hebt geklikt.

* Als de SDK `backfill()` -functie de verwachte uitvoer niet genereert, kan dit worden veroorzaakt door een verificatie probleem.  Wanneer u de compute maakt om deze functie door te geven, moet u niet gebruiken `Run.get_context().experiment.workspace.compute_targets` .  Gebruik in plaats daarvan [ServicePrincipalAuthentication](/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication?preserve-view=true&view=azure-ml-py) zoals de volgende om de compute te maken die u doorgeeft aan die `backfill()` functie: 

  ```python
   auth = ServicePrincipalAuthentication(
          tenant_id=tenant_id,
          service_principal_id=app_id,
          service_principal_password=client_secret
          )
   ws = Workspace.get("xxx", auth=auth, subscription_id="xxx", resource_group"xxx")
   compute = ws.compute_targets.get("xxx")
   ```

## <a name="azure-machine-learning-designer"></a>Azure Machine Learning-ontwerpprogramma

### <a name="dataset-visualization-in-the-designer"></a>Visualisatie van gegevensset in de ontwerp functie

Nadat u een gegevensset hebt geregistreerd op de Asset-pagina **gegevens sets** of met behulp van SDK, kunt u deze vinden onder **gegevens sets** categorie in de lijst links naar het canvas design.

Wanneer u de gegevensset echter naar het canvas sleept en visualiseren, kan het zijn dat deze niet kan worden gevisualiseerd om een van de volgende redenen:

- Momenteel kunt u tabellaire gegevensset alleen visualiseren in de ontwerp functie. Als u een bestands gegevensset buiten Designer registreert, kunt u deze niet visualiseren op het canvas van de ontwerp functie.
- Uw gegevensset wordt opgeslagen in het virtuele netwerk (VNet). Als u wilt visualiseren, moet u de werk ruimte beheerde identiteit van het gegevens archief inschakelen.
    1. Ga naar de gerelateerde gegevens opslag en **Klik op referenties bijwerken** 
     :::image type="content" source="./media/resource-known-issues/datastore-update-credential.png" alt-text="gegevens bijwerken":::
    1. Selecteer **Ja** om de beheerde identiteit van de werk ruimte in te scha kelen.
    :::image type="content" source="./media/resource-known-issues/enable-workspace-managed-identity.png" alt-text="Beheerde identiteit van werk ruimte inschakelen":::

### <a name="long-compute-preparation-time"></a>Tijd van lange reken voorbereiding

Het kan enkele minuten of zelfs langer duren wanneer u voor het eerst verbinding maakt of een compute-doel maakt. 

Vanuit de model gegevens verzamelaar kan het tot (maar meestal minder dan) 10 minuten duren voordat gegevens in uw Blob Storage-account arriveren. Wacht tien minuten om ervoor te zorgen dat de onderstaande cellen worden uitgevoerd.

```python
import time
time.sleep(600)
```

### <a name="log-for-real-time-endpoints"></a>Logboek voor realtime-eind punten

Logboeken van real-time eind punten zijn klant gegevens. Voor het oplossen van real-time-eind punten kunt u de volgende code gebruiken om Logboeken in te scha kelen. 

Zie voor meer informatie over het bewaken van webservice-eind punten in [dit artikel](./how-to-enable-app-insights.md#query-logs-for-deployed-models).

```python
from azureml.core import Workspace
from azureml.core.webservice import Webservice

ws = Workspace.from_config()
service = Webservice(name="service-name", workspace=ws)
logs = service.get_logs()
```
Als u meerdere tenants hebt, moet u mogelijk de volgende verificatie code toevoegen voordat `ws = Workspace.from_config()`

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="the tenant_id in which your workspace resides")
```

## <a name="train-models"></a>Modellen trainen

* **ModuleErrors (geen module met de naam)**: als u in ModuleErrors uitvoert terwijl experimenten in azure ml worden ingediend, betekent dit dat het trainings script verwacht dat er een pakket wordt geïnstalleerd, maar dit wordt niet toegevoegd. Wanneer u de naam van het pakket opgeeft, installeert Azure ML het pakket in de omgeving die wordt gebruikt voor de uitvoering van uw training. 

    Als u schattingen gebruikt om experimenten in te dienen, kunt u een pakket naam opgeven via `pip_packages` of `conda_packages` para meter in de Estimator op basis van de bron die u wilt installeren van het pakket. U kunt ook een yml-bestand met al uw afhankelijkheden opgeven `conda_dependencies_file` of al uw PIP-vereisten in een txt-bestand met behulp van `pip_requirements_file` para meter. Als u uw eigen Azure ML-omgevings object hebt dat u de standaard installatie kopie wilt overschrijven die wordt gebruikt door de Estimator, kunt u die omgeving opgeven via de `environment` para meter van de Estimator-constructor.

    Azure ML biedt ook Framework-specifieke schattingen voor tensor flow, PyTorch, Chainer en SKLearn. Door deze ramingen te gebruiken, moet u ervoor zorgen dat de basis raamwerk afhankelijkheden namens u zijn geïnstalleerd in de omgeving die wordt gebruikt voor de training. U hebt de optie om extra afhankelijkheden op te geven zoals hierboven wordt beschreven. 
 
    Azure ML bewaart docker-installatie kopieën en de inhoud ervan kunnen worden weer gegeven in [AzureML-containers](https://github.com/Azure/AzureML-Containers).
    Framework-specifieke afhankelijkheden worden weer gegeven in de bijbehorende Framework-documentatie- [Chainer](/python/api/azureml-train-core/azureml.train.dnn.chainer?preserve-view=true&view=azure-ml-py#&preserve-view=trueremarks), [PyTorch](/python/api/azureml-train-core/azureml.train.dnn.pytorch?preserve-view=true&view=azure-ml-py#&preserve-view=trueremarks), [tensor flow](/python/api/azureml-train-core/azureml.train.dnn.tensorflow?preserve-view=true&view=azure-ml-py#&preserve-view=trueremarks), [SKLearn](/python/api/azureml-train-core/azureml.train.sklearn.sklearn?preserve-view=true&view=azure-ml-py#&preserve-view=trueremarks).

    > [!Note]
    > Als u denkt dat een bepaald pakket algemeen genoeg is om te worden toegevoegd aan de bewaarde afbeeldingen en omgevingen van Azure ML, kunt u een GitHub-probleem veroorzaken in [AzureML-containers](https://github.com/Azure/AzureML-Containers). 
 
* **NameError (naam niet gedefinieerd), AttributeError (object heeft geen kenmerk)**: Deze uitzonde ring moet afkomstig zijn uit uw trainings scripts. U kunt de logboek bestanden van Azure Portal bekijken voor meer informatie over de specifieke naam niet gedefinieerd of kenmerk fout. U kunt de SDK gebruiken `run.get_details()` om het fout bericht te bekijken. Hiermee worden ook alle logboek bestanden weer geven die zijn gegenereerd voor de uitvoering. Bekijk uw trainings script en los het probleem op voordat u de uitvoering opnieuw verzendt. 

* **Horovod is uitgeschakeld**: in de meeste gevallen is het mogelijk dat er een onderliggende uitzonde ring is opgetreden in een van de processen waardoor Horovod werd afgesloten als u problemen ondervindt met ' AbortedError: Horovod is afgesloten '. Elke classificatie in de MPI-taak krijgt een eigen toegewezen logboekbestand in Azure ML. Deze logboeken hebben de naam `70_driver_logs`. In het geval van gedistribueerde trainingen worden de logboeknamen aangevuld met het achtervoegsel `_rank` om het onderscheiden van de logboeken gemakkelijker te maken. Als u de exacte fout wilt vinden die ervoor heeft gezorgd dat Horovod wordt afgesloten, gaat u naar alle logboek bestanden en zoekt u `Traceback` aan het einde van de driver_log bestanden. Met een van deze bestanden krijgt u de daad werkelijke onderliggende uitzonde ring. 

* **Verwijdering van de uitvoering of het experiment**: experimenten kunnen worden gearchiveerd met behulp van de methode [experiment. Archive](/python/api/azureml-core/azureml.core.experiment%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truearchive--) of vanuit de weer gave van het tabblad experiment in azure machine learning Studio-client via de knop ' Archief experiment '. Met deze actie wordt het experiment verborgen in lijst query's en weer gaven, maar wordt het niet verwijderd.

    Het definitief verwijderen van afzonderlijke experimenten of uitvoeringen wordt momenteel niet ondersteund. Zie [uw machine learning service-werkruimte gegevens exporteren of verwijderen](how-to-export-delete-data.md)voor meer informatie over het verwijderen van werk ruimte-assets.

* Het **metrieke document is te groot**: Azure machine learning heeft interne limieten voor de grootte van metrische objecten die tegelijk kunnen worden geregistreerd vanuit een training-uitvoering. Als er een foutbericht wordt weergegeven dat het metrische document te groot is bij het vastleggen van een metrische waarde voor een lijst, kunt u de lijst in kleinere segmenten splitsen, bijvoorbeeld:

    ```python
    run.log_list("my metric name", my_metric[:N])
    run.log_list("my metric name", my_metric[N:])
    ```

    Azure ML voegt de blokken met dezelfde metrische-gegevensnaam intern samen tot een aaneengesloten lijst.

## <a name="automated-machine-learning"></a>Geautomatiseerde Machine Learning

* De **recente upgrade van AutoML-afhankelijkheden voor nieuwere versies leidt tot een afbreuk** aan de compatibiliteit: vanaf versie 1.13.0 van de SDK worden modellen niet geladen in oudere sdk's vanwege incompatibiliteit tussen de oudere versies die we in onze vorige pakketten hebben vastgemaakt, en de nieuwere versies die we nu vastmaken. U ziet de fout, zoals:
  * Module niet gevonden: ex. `No module named 'sklearn.decomposition._truncated_svd` ,
  * Import fouten: ex. `ImportError: cannot import name 'RollingOriginValidator'` ,
  * Kenmerk fouten: ex. `AttributeError: 'SimpleImputer' object has no attribute 'add_indicator`
  
  U kunt dit probleem omzeilen door een van de volgende twee stappen uit te voeren, afhankelijk van uw AutoML SDK-trainings versie:
  1. Als uw AutoML SDK-trainings versie hoger is dan 1.13.0, hebt u `pandas == 0.25.1` en nodig `sckit-learn==0.22.1` . Als er sprake is van een versie die niet overeenkomt, upgradet u scikit-Learn en/of Pandas naar de juiste versie, zoals hieronder wordt weer gegeven:
  
  ```bash
     pip install --upgrade pandas==0.25.1
     pip install --upgrade scikit-learn==0.22.1
  ```
  
  2. Als uw AutoML SDK-trainings versie lager is dan of gelijk is aan 1.12.0, hebt u `pandas == 0.23.4` en nodig `sckit-learn==0.20.3` . Als er een versie conflict is, downgrade scikit-Learn en/of Pandas om de versie te corrigeren, zoals hieronder wordt weer gegeven:
  
  ```bash
    pip install --upgrade pandas==0.23.4
    pip install --upgrade scikit-learn==0.20.3
  ```

* **Mislukte implementatie**: voor versies <= 1.18.0 van de SDK kan de basis installatie kopie die is gemaakt voor de implementatie mislukken met de volgende fout: "ImportError: kan geen naam importeren `cached_property` uit `werkzeug` ". 

  De volgende stappen kunnen het probleem omzeilen:
  1. Het model pakket downloaden
  2. Het pakket verwijderen
  3. Implementeren met behulp van de niet-gezipte activa

* **Forecasting R2-Score is altijd nul**: dit probleem doet zich voor als de verstrekte trainings gegevens een tijd reeks hebben die dezelfde waarde voor de laatste `n_cv_splits`  +  `forecasting_horizon` gegevens punten bevat. Als dit patroon in uw tijd reeks wordt verwacht, kunt u de primaire metriek overschakelen naar een genormaliseerd wortel fout.
 
* **Tensor flow**: vanaf versie 1.5.0 van de SDK installeert automatische machine learning standaard geen tensor flow-modellen. Als u tensor flow wilt installeren en wilt gebruiken met uw geautomatiseerde ML experimenten, installeert u tensor flow = = 1.12.0 via CondaDependecies. 
 
   ```python
   from azureml.core.runconfig import RunConfiguration
   from azureml.core.conda_dependencies import CondaDependencies
   run_config = RunConfiguration()
   run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['tensorflow==1.12.0'])
  ```
* **Experimenteer grafieken**: binaire classificatie grafieken (Precision-intrekken, Roc, winst curve enz.) die worden weer gegeven in automatische ml-experimenten, worden niet correct gerenderd in de gebruikers interface sinds 4/12. In grafiek grafieken worden momenteel inverse resultaten weer gegeven, waarbij betere modellen worden weer gegeven met lagere resultaten. Een oplossing wordt onderzocht.

* **Databricks annuleren automatische uitvoering van een machine learning**: wanneer u gebruikmaakt van geautomatiseerde machine learning mogelijkheden op Azure Databricks, kunt u een uitvoering annuleren en een nieuwe uitvoering van het experiment starten door het Azure Databricks-cluster opnieuw op te starten.

* **Databricks >tien herhalingen voor automatische machine learning** machine learning: als u meer dan 10 iteraties hebt, moet u instellen `show_output` op `False` Wanneer u de uitvoering verzendt.

* **Databricks-widget voor de Azure machine learning SDK en geautomatiseerde machine learning**: de SDK-widget Azure machine learning wordt niet ondersteund in een Databricks-notebook omdat de notebooks geen HTML-widgets kunnen parseren. U kunt de widget in de portal weer geven met behulp van deze python-code in uw Azure Databricks notebook-cel:

    ```
    displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
    ```
* **automl_setup mislukt**: 
    * Voer automl_setup uit vanaf een Anaconda-prompt in Windows. Gebruik deze koppeling om [Miniconda te installeren](https://docs.conda.io/en/latest/miniconda.html).
    * Zorg ervoor dat de Conda 64-bits is geïnstalleerd in plaats van 32-bits door de opdracht uit te voeren `conda info` . De `platform` moet `win-64` voor Windows of `osx-64` voor Mac zijn.
    * Zorg ervoor dat Conda 4.4.10 of hoger is geïnstalleerd. U kunt de versie controleren met de opdracht `conda -V` . Als u een vorige versie hebt geïnstalleerd, kunt u deze bijwerken met behulp van de opdracht: `conda update conda` .
    * Spreek `gcc: error trying to exec 'cc1plus'`
      *  Als de `gcc: error trying to exec 'cc1plus': execvp: No such file or directory` fout is opgetreden, installeert u build Essentials met behulp van de opdracht `sudo apt-get install build-essential` .
      * Geef een nieuwe naam als de eerste para meter op automl_setup om een nieuwe Conda-omgeving te maken. Bekijk bestaande Conda-omgevingen met `conda env list` en verwijder ze met `conda env remove -n <environmentname>` .
      
* **automl_setup_linux. sh mislukt**: als automl_setup_linus. sh mislukt bij Ubuntu Linux met de fout: `unable to execute 'gcc': No such file or directory`-
  1. Zorg ervoor dat de uitgaande poorten 53 en 80 zijn ingeschakeld. Op een virtuele machine van Azure kunt u dit doen vanuit de Azure Portal door de virtuele machine te selecteren en op netwerken te klikken.
  2. Voer de volgende opdracht uit: `sudo apt-get update`
  3. Voer de volgende opdracht uit: `sudo apt-get install build-essential --fix-missing`
  4. `automl_setup_linux.sh`Opnieuw uitvoeren

* **Configuration. ipynb mislukt**:
  * Voor lokale Conda moet u er eerst voor zorgen dat automl_setup is uitgevoerd.
  * Zorg ervoor dat de subscription_id juist is. Zoek de subscription_id in het Azure Portal door alle services te selecteren en vervolgens op abonnementen. De tekens ' < ' en ' > ' mogen niet worden opgenomen in de subscription_id waarde. `subscription_id = "12345678-90ab-1234-5678-1234567890abcd"`Heeft bijvoorbeeld een geldige indeling.
  * Zorg ervoor dat Inzender of eigenaar toegang heeft tot het abonnement.
  * Controleer of de regio een van de ondersteunde regio's is: `eastus2` , `eastus` , `westcentralus` , `southeastasia` , `westeurope` , `australiaeast` , `westus2` , `southcentralus` .
  * Zorg ervoor dat u toegang tot de regio hebt met behulp van de Azure Portal.
  
* **importeren AutoMLConfig mislukt**: er zijn pakket wijzigingen in de geautomatiseerde machine learning versie 1.0.76, waarvoor de vorige versie moet worden verwijderd voordat u de nieuwe versie bijwerkt. Als de `ImportError: cannot import name AutoMLConfig` is aangetroffen na een upgrade van een SDK-versie vóór v 1.0.76 naar v 1.0.76 of hoger, lost u de fout op door het volgende uit te voeren: `pip uninstall azureml-train automl` en vervolgens `pip install azureml-train-auotml` . Het script automl_setup. cmd doet dit automatisch. 

* **Workspace.from_config mislukt**: als de aanroepen ws = Workspace.from_config () mislukken
  1. Controleer of de configuratie. ipynb-notebook is uitgevoerd.
  2. Als het notitie blok wordt uitgevoerd vanuit een map die zich niet in de map bevindt waar de `configuration.ipynb` was uitgevoerd, kopieert u de map aml_config en het bestand config.jsop dat item zich in de nieuwe map. Workspace.from_config leest de config.jsop voor de notitieblokmap of de bovenliggende map.
  3. Als er een nieuw abonnement, een resource groep, werk ruimte of regio wordt gebruikt, moet u ervoor zorgen dat u het `configuration.ipynb` notitie blok opnieuw uitvoert. Het is niet mogelijk om config.jsrechtstreeks te wijzigen als de werk ruimte al bestaat in de opgegeven resource groep onder het opgegeven abonnement.
  4. Als u de regio wilt wijzigen, wijzigt u de werk ruimte, de resource groep of het abonnement. `Workspace.create` Er wordt geen werk ruimte gemaakt of bijgewerkt als deze al bestaat, zelfs als de opgegeven regio verschillend is.
  
* **Voorbeeld notitieblok mislukt**: als een voor beeld van een notebook mislukt met een fout die eigenschap, methode of bibliotheek niet bestaat:
  * Zorg ervoor dat de juiste kernel is geselecteerd in de jupyter-notebook. De kernel wordt weer gegeven in de rechter bovenhoek van de notitie blok pagina. De standaard waarde is azure_automl. Houd er rekening mee dat de kernel wordt opgeslagen als onderdeel van het notitie blok. Als u overschakelt naar een nieuwe Conda-omgeving, moet u dus de nieuwe kernel in het notitie Blok selecteren.
      * Voor Azure Notebooks moet het python 3,6 zijn. 
      * Voor lokale Conda-omgevingen moet dit de Conda-omgevings naam zijn die u hebt opgegeven in automl_setup.
  * Zorg ervoor dat het notitie blok voor de SDK-versie is die u gebruikt. U kunt de SDK-versie controleren door `azureml.core.VERSION` in een jupyter notebook-cel uit te voeren. U kunt de vorige versie van de voorbeeld notitieblokken downloaden van GitHub door op de knop te klikken `Branch` , het tabblad te selecteren `Tags` en vervolgens de versie te selecteren.

* **Numpy importeren mislukt in Windows: in** sommige Windows-omgevingen wordt een fout weer geven bij het laden van numpy met de laatste python-versie 3.6.8. Als u dit probleem ziet, probeert u met python-versie 3.6.7.

* **Numpy importeren mislukt**: Controleer de tensor flow-versie in de omgeving Automated ml Conda. Ondersteunde versies zijn < 1,13. Tensor flow uit de omgeving verwijderen als versie >= 1,13, kunt u de versie van tensor flow controleren en als volgt verwijderen:
  1. Start een opdracht shell, activeer de Conda-omgeving waar automatisch ml-pakketten worden geïnstalleerd.
  2. Voer `pip freeze` in en zoek naar `tensorflow` , indien gevonden, de weer gegeven versie moet < 1,13
  3. Als de vermelde versie een niet-ondersteunde versie is, `pip uninstall tensorflow` typt u in de opdracht shell en voert u y in voor bevestiging.

## <a name="model-explanations"></a>Model uitleg

* **Sparse gegevens niet ondersteund**: het model uitleg van het dash board wordt aanzienlijk vertraagd of langzamer met een groot aantal functies. Daarom bieden we momenteel geen ondersteuning voor sparse gegevens. Daarnaast ontstaan er algemene geheugen problemen met grote gegevens sets en een groot aantal functies. 

* **Prognose modellen worden niet ondersteund met model verklaringen**: interpreteer baarheid, aanbevolen model uitleg is niet beschikbaar voor AutoML-prognose experimenten waarbij de volgende algoritmen worden aanbevolen als het beste model: TCNForecaster, AutoArima, ExponentialSmoothing, Average, Naive, seizoen Average en seizoen Naive. AutoML-prognose heeft regressie modellen die ondersteuning bieden voor uitleg. In de uitleg dashbord wordt echter het tabblad belang rijke functie urgentie alleen niet ondersteund voor prognoses vanwege de complexiteit van de gegevens pijplijnen.

* **Lokale uitleg voor de gegevens index**: het uitleg-dash board biedt geen ondersteuning voor gerelateerde lokale urgentie waarden in een rij-id van de oorspronkelijke validatie gegevensset als die gegevensset groter is dan 5000 data Points als het dash board de gegevens wille keurig downsamplen. Het dash board toont echter de waarden van de onbewerkte gegevensset voor elk data Point dat wordt door gegeven aan het dash board onder het tabblad prioriteit van afzonderlijke functie. Gebruikers kunnen lokale belang rijke gegevens weer toewijzen aan de oorspronkelijke gegevensset door te voldoen aan de waarden van de onbewerkte gegevensset-onderdelen. Als de grootte van de validatie gegevensset kleiner is dan 5000 steek proeven, `index` komt de functie in AzureML Studio overeen met de index in de validatie-gegevensset.

* **Wat-als/ijs-grafieken worden niet ondersteund in AML Studio**: What-If en afzonderlijke Runtimes voor voorwaardelijke verwachting (ijs) worden niet ondersteund in AzureML studio onder het tabblad uitleg, omdat de geüploade uitleg een actieve Compute nodig heeft om voor spellingen en kansen van perturbed-functies opnieuw te berekenen. De functie wordt momenteel ondersteund in Jupyter-notebooks wanneer deze wordt uitgevoerd als een widget met de SDK.

## <a name="deploy--serve-models"></a>Modellen implementeren & bedienen

Voer deze acties uit voor de volgende fouten:

|Fout  | Oplossing  |
|---------|---------|
|Fout bij het maken van de installatie kopie bij het implementeren van de webservice     |  Voeg ' pynacl = = 1.2.1 ' toe als een PIP-afhankelijkheid voor het Conda-bestand voor installatie kopie configuratie       |
|`['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`     |   Wijzig de SKU voor virtuele machines die in uw implementatie worden gebruikt naar een met meer geheugen. |
|FPGA-fout     |  U kunt geen modellen implementeren op Fpga's totdat u hebt aangevraagd en goedgekeurd voor FPGA-quotum. Vul het quotum aanvraag formulier in om toegang aan te vragen: https://aka.ms/aml-real-time-ai       |

### <a name="updating-azure-machine-learning-components-in-aks-cluster"></a>Azure Machine Learning-onderdelen in AKS-cluster bijwerken

Updates voor Azure Machine Learning onderdelen die zijn geïnstalleerd in een Azure Kubernetes service-cluster, moeten hand matig worden toegepast. 

U kunt deze updates Toep assen door het cluster te ontkoppelen van de Azure Machine Learning-werk ruimte en vervolgens het cluster opnieuw te koppelen aan de werk ruimte. Als TLS is ingeschakeld in het cluster, moet u het TLS/SSL-certificaat en de persoonlijke sleutel opgeven wanneer u het cluster opnieuw koppelt. 

```python
compute_target = ComputeTarget(workspace=ws, name=clusterWorkspaceName)
compute_target.detach()
compute_target.wait_for_completion(show_output=True)

attach_config = AksCompute.attach_configuration(resource_group=resourceGroup, cluster_name=kubernetesClusterName)

## If SSL is enabled.
attach_config.enable_ssl(
    ssl_cert_pem_file="cert.pem",
    ssl_key_pem_file="key.pem",
    ssl_cname=sslCname)

attach_config.validate_configuration()

compute_target = ComputeTarget.attach(workspace=ws, name=args.clusterWorkspaceName, attach_configuration=attach_config)
compute_target.wait_for_completion(show_output=True)
```

Als u het TLS/SSL-certificaat en de persoonlijke sleutel niet meer hebt of als u een certificaat gebruikt dat is gegenereerd door Azure Machine Learning, kunt u de bestanden ophalen voordat u het cluster ontkoppelt door verbinding te maken met het cluster met behulp `kubectl` van en het geheim op te halen `azuremlfessl` .

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes slaat de geheimen op in de indeling basis-64-code ring. U moet base-64 decoderen van de- `cert.pem` en- `key.pem` onderdelen van de geheimen voordat u deze opgeeft `attach_config.enable_ssl` . 

### <a name="detaching-azure-kubernetes-service"></a>De Azure Kubernetes-service loskoppelen

Als u de Azure Machine Learning Studio, SDK of de Azure CLI-extensie gebruikt voor machine learning om een AKS-cluster los te koppelen, wordt het AKS-cluster niet verwijderd. Zie [de Azure CLI gebruiken met AKS](../aks/kubernetes-walkthrough.md#delete-the-cluster)voor het verwijderen van het cluster.

### <a name="webservices-in-azure-kubernetes-service-failures"></a>Webservices in azure Kubernetes-service fouten

Veel webservicefouten in Azure Kubernetes Service kunnen worden opgespoord door verbinding te maken met het cluster met behulp van `kubectl`. U kunt het `kubeconfig.json` voor een Azure Kubernetes-service cluster verkrijgen door het uit te voeren

```azurecli-interactive
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="authentication-errors"></a>Verificatiefouten

Als u een beheer bewerking uitvoert op een reken doel van een externe taak, wordt een van de volgende fouten weer gegeven: 

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

U ontvangt bijvoorbeeld een fout melding als u een reken doel probeert te maken of koppelen van een ML-pijp lijn die wordt ingediend voor externe uitvoering.

## <a name="missing-user-interface-items-in-studio"></a>Ontbrekende gebruikers interface-items in Studio

Toegangs beheer op basis van rollen kan worden gebruikt om acties te beperken die u kunt uitvoeren met Azure Machine Learning. Deze beperkingen kunnen voor komen dat gebruikers interface-items in de Azure Machine Learning Studio worden weer gegeven. Als u bijvoorbeeld een rol krijgt die geen reken exemplaar kan maken, wordt de optie voor het maken van een reken instantie niet weer gegeven in de Studio.

Zie [Gebruikers en rollen beheren](how-to-assign-roles.md)voor meer informatie.

## <a name="compute-cluster-wont-resize"></a>Grootte van berekenings cluster wordt niet gewijzigd

Als uw Azure Machine Learning Compute-Cluster vastloopt bij het wijzigen van de grootte (0-> 0) voor de status van het knoop punt, kan dit worden veroorzaakt door Azure-resource vergrendelingen.

[!INCLUDE [resource locks](../../includes/machine-learning-resource-lock.md)]

## <a name="next-steps"></a>Volgende stappen

Meer artikelen over het oplossen van problemen met Azure Machine Learning bekijken:

* [Problemen met docker-implementatie oplossen met Azure Machine Learning](how-to-troubleshoot-deployment.md)
* [Fout opsporing machine learning pijp lijnen](how-to-debug-pipelines.md)
* [Fouten opsporen in de ParallelRunStep-klasse vanuit de Azure Machine Learning SDK](how-to-debug-parallel-run-step.md)
* [Interactieve fout opsporing van een machine learning Compute-exemplaar met VS-code](how-to-debug-visual-studio-code.md)
* [Application Insights gebruiken om fouten in machine learning pijp lijnen op te sporen](./how-to-log-pipelines-application-insights.md)

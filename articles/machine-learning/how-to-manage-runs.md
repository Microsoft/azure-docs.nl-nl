---
title: Trainings uitvoeringen in python starten, controleren en annuleren
titleSuffix: Azure Machine Learning
description: Meer informatie over het starten, status en beheren van uw machine learning-experiment met de Azure Machine Learning python SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 03/04/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, devx-track-azurecli
ms.openlocfilehash: d142c523862d61bf56723726be50cd6f095c5ee9
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102520333"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>Trainings uitvoeringen in python starten, controleren en annuleren

De [Azure machine learning SDK voor python](/python/api/overview/azure/ml/intro), [Machine Learning cli](reference-azure-machine-learning-cli.md)en [Azure machine learning Studio](https://ml.azure.com) bieden verschillende methoden om uw uitvoeringen te controleren, te organiseren en te beheren voor training en experimenten.

In dit artikel vindt u voor beelden van de volgende taken:

* Prestaties van uitvoering bewaken.
* Annuleren of niet uitvoeren.
* Onderliggende uitvoeringen maken.
* Uitvoeringen en zoeken.

> [!TIP]
> Zie [Azure machine learning bewaken](monitor-azure-machine-learning.md)als u informatie zoekt over het bewaken van de Azure machine learning-service en de bijbehorende Azure-Services.
> Als u op zoek bent naar informatie over bewakings modellen die zijn geïmplementeerd als webservices of IoT Edge modules, raadpleegt u [model gegevens verzamelen](how-to-enable-data-collection.md) en [controleren met Application Insights](how-to-enable-app-insights.md).

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig:

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure Machine Learning](https://aka.ms/AMLFree).

* Een [Azure machine learning-werk ruimte](how-to-manage-workspace.md).

* De Azure Machine Learning SDK voor python (versie 1.0.21 of hoger). Zie [de SDK installeren of bijwerken](/python/api/overview/azure/ml/install)om de nieuwste versie van de SDK te installeren of bij te werken.

    Als u uw versie van de Azure Machine Learning SDK wilt controleren, gebruikt u de volgende code:

    ```python
    print(azureml.core.VERSION)
    ```

* De [Azure cli](/cli/azure/) -en [cli-extensie voor Azure machine learning](reference-azure-machine-learning-cli.md).

## <a name="monitor-run-performance"></a>Prestaties van de uitvoering bewaken

* Een uitvoering en het bijbehorende logboek registratie proces starten

    # <a name="python"></a>[Python](#tab/python)
    
    1. Stel uw experiment in door de klassen [werk ruimte](/python/api/azureml-core/azureml.core.workspace.workspace), [experiment](/python/api/azureml-core/azureml.core.experiment.experiment), [Run](/python/api/azureml-core/azureml.core.run%28class%29)en [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) te importeren vanuit het pakket [azureml. core](/python/api/azureml-core/azureml.core) .
    
        ```python
        import azureml.core
        from azureml.core import Workspace, Experiment, Run
        from azureml.core import ScriptRunConfig
        
        ws = Workspace.from_config()
        exp = Experiment(workspace=ws, name="explore-runs")
        ```
    
    1. Start een run en het bijbehorende logboek registratie proces met de- [`start_logging()`](/python/api/azureml-core/azureml.core.experiment%28class%29#start-logging--args----kwargs-) methode.
    
        ```python
        notebook_run = exp.start_logging()
        notebook_run.log(name="message", value="Hello from run!")
        ```
        
    # <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
    
    Gebruik de volgende stappen om een uitvoering van uw experiment te starten:
    
    1. Gebruik vanuit een shell of opdracht prompt de Azure CLI om u te verifiëren bij uw Azure-abonnement:
    
        ```azurecli-interactive
        az login
        ```
        
        [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 
    
    1. Een werkruimte configuratie koppelen aan de map die uw trainings script bevat. Vervang door `myworkspace` uw Azure machine learning-werk ruimte. Vervang door `myresourcegroup` de Azure-resource groep die uw werk ruimte bevat:
    
        ```azurecli-interactive
        az ml folder attach -w myworkspace -g myresourcegroup
        ```
    
        Met deze opdracht maakt u een `.azureml` submap die voor beelden van runconfig-en Conda-omgevings bestanden bevat. Het bevat ook een `config.json` bestand dat wordt gebruikt om te communiceren met uw Azure machine learning-werk ruimte.
    
        Zie voor meer informatie [AZ ml map attach](/cli/azure/ext/azure-cli-ml/ml/folder#ext-azure-cli-ml-az-ml-folder-attach).
    
    2. Gebruik de volgende opdracht om de uitvoering te starten. Wanneer u deze opdracht gebruikt, geeft u de naam op van het runconfig-bestand (de tekst voor \* . runconfig als u uw bestands systeem bekijkt) op basis van de para meter-c.
    
        ```azurecli-interactive
        az ml run submit-script -c sklearn -e testexperiment train.py
        ```
    
        > [!TIP]
        > De `az ml folder attach` opdracht heeft een `.azureml` submap gemaakt die twee voor beelden van runconfig-bestanden bevat.
        >
        > Als u een python-script hebt waarmee een configuratie object voor een uitvoering programmatisch wordt gemaakt, kunt u [RunConfig. Save ()](/python/api/azureml-core/azureml.core.runconfiguration#save-path-none--name-none--separate-environment-yaml-false-) gebruiken om het op te slaan als een RunConfig-bestand.
        >
        > Zie voor meer voor beelden van runconfig-bestanden [https://github.com/MicrosoftDocs/pipelines-azureml/](https://github.com/MicrosoftDocs/pipelines-azureml/) .
    
        Zie [AZ ml run-script uitvoeren](/cli/azure/ext/azure-cli-ml/ml/run#ext-azure-cli-ml-az-ml-run-submit-script)voor meer informatie.

    # <a name="studio"></a>[Studio](#tab/azure-studio)

    Voor een voor beeld van het trainen van een model in de Azure Machine Learning Designer raadpleegt u [zelf studie: prijs auto Mobile met de ontwerp functie](tutorial-designer-automobile-price-train-score.md).

    ---

* De status van een uitvoering controleren

    # <a name="python"></a>[Python](#tab/python)
    
    * De status ophalen van een run met de- [`get_status()`](/python/api/azureml-core/azureml.core.run%28class%29#get-status--) methode.
    
        ```python
        print(notebook_run.get_status())
        ```
    
    * Als u de uitvoerings-ID, uitvoerings tijd en aanvullende details over de uitvoering wilt ophalen, gebruikt u de- [`get_details()`](/python/api/azureml-core/azureml.core.workspace.workspace#get-details--) methode.
    
        ```python
        print(notebook_run.get_details())
        ```
    
    * Wanneer de uitvoering is voltooid, gebruikt u de [`complete()`](/python/api/azureml-core/azureml.core.run%28class%29#complete--set-status-true-) methode om deze te markeren als voltooid.
    
        ```python
        notebook_run.complete()
        print(notebook_run.get_status())
        ```
    
    * Als u het ontwerp patroon van python gebruikt `with...as` , wordt de uitvoering automatisch als voltooid gemarkeerd wanneer de uitvoering buiten het bereik valt. U hoeft de uitvoering niet hand matig te markeren als voltooid.
        
        ```python
        with exp.start_logging() as notebook_run:
            notebook_run.log(name="message", value="Hello from run!")
            print(notebook_run.get_status())
        
        print(notebook_run.get_status())
        ```
    
    # <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
    
    * Gebruik de volgende opdracht om een lijst met uitvoeringen voor uw experiment weer te geven. Vervang door `experiment` de naam van uw experiment:
    
        ```azurecli-interactive
        az ml run list --experiment-name experiment
        ```
    
        Met deze opdracht wordt een JSON-document geretourneerd dat informatie bevat over de uitvoeringen van dit experiment.
    
        Zie voor meer informatie [AZ ml experimenten List](/cli/azure/ext/azure-cli-ml/ml/experiment#ext-azure-cli-ml-az-ml-experiment-list).
    
    * Als u informatie wilt weer geven over een specifieke uitvoering, gebruikt u de volgende opdracht. Vervang door `runid` de id van de run:
    
        ```azurecli-interactive
        az ml run show -r runid
        ```
    
        Met deze opdracht wordt een JSON-document geretourneerd dat informatie bevat over de uitvoering.
    
        Zie voor meer informatie [AZ ml run show](/cli/azure/ext/azure-cli-ml/ml/run#ext-azure-cli-ml-az-ml-run-show).
    
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    U kunt als volgt uw uitvoeringen weer geven in Studio: 
    
    1. Navigeer naar het tabblad **experimenten** .
    
    1. Selecteer **alle experimenten** om alle uitvoeringen in een experiment weer te geven of selecteer **alle uitvoeringen** om alle uitvoeringen weer te geven die in de werk ruimte zijn verzonden.
    
        Op de pagina **alle uitvoeringen** kunt u de lijsten met uitvoeringen filteren op Tags, experimenten, compute target en meer om uw werk beter te organiseren en te bereiken.  
    
    1. Maak aanpassingen aan de pagina door uitvoeringen te selecteren om te vergelijken, grafieken toe te voegen of filters toe te passen. Deze wijzigingen kunnen worden opgeslagen als een **aangepaste weer gave** , zodat u gemakkelijk kunt terugkeren naar uw werk. Gebruikers met werkruimte machtigingen kunnen de aangepaste weer gave bewerken of weer geven. U kunt ook de aangepaste weer gave delen met team leden voor verbeterde samen werking door de **weer gave delen** te selecteren.   
    
        :::image type="content" source="media/how-to-manage-runs/custom-views.gif" alt-text="Scherm afbeelding: een aangepaste weer gave maken":::
    
    1. Als u de uitvoerings logboeken wilt weer geven, selecteert u een specifieke uitvoering en op het tabblad **uitvoer en logboeken** kunt u diagnostische en fout logboeken vinden voor de uitvoering.
    
    ---

## <a name="run-description"></a>Beschrijving van de uitvoering 

Een uitvoerings beschrijving kan worden toegevoegd aan een uitvoering om meer context en informatie te bieden aan de uitvoering. U kunt ook in de lijst met uitvoeringen zoeken naar deze beschrijvingen en de beschrijving van de uitvoeringsrun toevoegen als een kolom in de lijst met uitvoeringen. 

Navigeer naar de pagina **uitvoerings Details** voor de uitvoering en selecteer het pictogram bewerken of potlood om beschrijvingen voor uw uitvoering toe te voegen, te bewerken of te verwijderen. Als u de wijzigingen in de lijst met uitvoeringen wilt behouden, slaat u de wijzigingen in uw bestaande aangepaste weer gave of een nieuwe aangepaste weer gave op. De indeling voor prijs verlaging wordt ondersteund voor uitvoerings beschrijvingen waarmee de installatie kopie kan worden inge sloten en deep linking zoals hieronder wordt weer gegeven.

:::image type="content" source="media/how-to-manage-runs/run-description.gif" alt-text="Scherm afbeelding: een beschrijving van de uitvoeringsrun maken"::: 

## <a name="tag-and-find-runs"></a>Uitvoeringen coderen en zoeken

In Azure Machine Learning kunt u eigenschappen en tags gebruiken om uw uitvoeringen te organiseren en query's uit te voeren op belang rijke informatie.

* Eigenschappen en Tags toevoegen

    # <a name="python"></a>[Python](#tab/python)
    
    Als u Doorzoek bare meta gegevens aan uw uitvoeringen wilt toevoegen, gebruikt u de- [`add_properties()`](/python/api/azureml-core/azureml.core.run%28class%29#add-properties-properties-) methode. Met de volgende code wordt de eigenschap bijvoorbeeld toegevoegd `"author"` aan de uitvoeren:
    
    ```Python
    local_run.add_properties({"author":"azureml-user"})
    print(local_run.get_properties())
    ```
    
    Eigenschappen zijn onveranderbaar, zodat ze een permanente record voor controle doeleinden maken. In het volgende code voorbeeld wordt een fout veroorzaakt, omdat we `"azureml-user"` `"author"` in de voor gaande code al zijn toegevoegd als eigenschaps waarde:
    
    ```Python
    try:
        local_run.add_properties({"author":"different-user"})
    except Exception as e:
        print(e)
    ```
    
    In tegens telling tot eigenschappen zijn Tags onveranderbaar. Gebruik de-methode om Doorzoek bare en nuttige informatie voor gebruikers van uw experiment toe te voegen [`tag()`](/python/api/azureml-core/azureml.core.run%28class%29#tag-key--value-none-) .
    
    ```Python
    local_run.tag("quality", "great run")
    print(local_run.get_tags())
    
    local_run.tag("quality", "fantastic run")
    print(local_run.get_tags())
    ```
    
    U kunt ook eenvoudige teken reeks Tags toevoegen. Wanneer deze tags worden weer gegeven in de code woordenlijst als sleutels, hebben ze een waarde van `None` .
    
    ```Python
    local_run.tag("worth another look")
    print(local_run.get_tags())
    ```
    
    # <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
    
    > [!NOTE]
    > Met de CLI kunt u alleen Tags toevoegen of bijwerken.
    
    Gebruik de volgende opdracht om een tag toe te voegen of bij te werken:
    
    ```azurecli-interactive
    az ml run update -r runid --add-tag quality='fantastic run'
    ```
    
    Zie [AZ ml run update](/cli/azure/ext/azure-cli-ml/ml/run#ext-azure-cli-ml-az-ml-run-update)(Engelstalig) voor meer informatie.
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    U kunt uit de Studio run-Tags toevoegen, bewerken of verwijderen. Navigeer naar de pagina **uitvoerings Details** voor de uitvoering en selecteer het pictogram bewerken of potlood om labels voor uw uitvoeringen toe te voegen, te bewerken of te verwijderen. U kunt deze labels ook zoeken en filteren op de pagina uitvoerings lijst.
    
    :::image type="content" source="media/how-to-manage-runs/run-tags.gif" alt-text="Scherm opname: uitvoer Tags toevoegen, bewerken of verwijderen":::
    
    ---

* Query-eigenschappen en-labels

    U kunt binnen een experiment een query uitvoeren om een lijst met uitvoeringen te retour neren die overeenkomen met specifieke eigenschappen en tags.

    # <a name="python"></a>[Python](#tab/python)
    
    ```Python
    list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
    list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
    ```
    
    # <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
    
    De Azure CLI ondersteunt [JMESPath](http://jmespath.org) -query's, die kunnen worden gebruikt voor het filteren van uitvoeringen op basis van eigenschappen en labels. Als u een JMESPath-query wilt gebruiken met de Azure CLI, geeft u deze op met de `--query` para meter. In de volgende voor beelden ziet u enkele query's met behulp van eigenschappen en Tags:
    
    ```azurecli-interactive
    # list runs where the author property = 'azureml-user'
    az ml run list --experiment-name experiment [?properties.author=='azureml-user']
    # list runs where the tag contains a key that starts with 'worth another look'
    az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
    # list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
    az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
    ```
    
    Zie voor meer informatie over het uitvoeren van query's in azure CLI-resultaten query uitvoeren op [uitvoer van Azure cli-opdracht](/cli/azure/query-azure-cli).
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    1. Ga naar de lijst  **alle uitvoeringen** .
    
    1. Gebruik de zoek balk om de meta gegevens van de uitvoering te filteren, zoals de tags, beschrijvingen, namen van experimenten en naam indiener. Het filter tags kan ook worden gebruikt om op de tags te filteren. 
    
    ---


## <a name="cancel-or-fail-runs"></a>Annuleren of mislukken wordt uitgevoerd

Als u een fout melding krijgt of als de uitvoering te lang duurt om te volt ooien, kunt u de uitvoering annuleren.

# <a name="python"></a>[Python](#tab/python)

Als u een uitvoering met de SDK wilt annuleren, gebruikt u de [`cancel()`](/python/api/azureml-core/azureml.core.run%28class%29#cancel--) volgende methode:

```python
src = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')
local_run = exp.submit(src)
print(local_run.get_status())

local_run.cancel()
print(local_run.get_status())
```

Als de uitvoering is voltooid, maar er een fout is opgetreden (bijvoorbeeld het onjuiste trainings script is gebruikt), kunt u de [`fail()`](/python/api/azureml-core/azureml.core.run%28class%29#fail-error-details-none--error-code-none---set-status-true-) methode gebruiken om deze te markeren als mislukt.

```python
local_run = exp.submit(src)
local_run.fail()
print(local_run.get_status())
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u een uitvoering wilt annuleren met de CLI, gebruikt u de volgende opdracht. Vervangen `runid` door de id van de uitvoering

```azurecli-interactive
az ml run cancel -r runid -w workspace_name -e experiment_name
```

Zie voor meer informatie [AZ ml run Cancel](/cli/azure/ext/azure-cli-ml/ml/run#ext-azure-cli-ml-az-ml-run-cancel).

# <a name="studio"></a>[Studio](#tab/azure-studio)

Als u een uitvoering in de Studio wilt annuleren, gebruikt u de volgende stappen:

1. Ga naar de actieve pijp lijn in de sectie **experimenten** of **pijp lijnen** . 

1. Selecteer het nummer van de pijplijn uitvoering dat u wilt annuleren.

1. Selecteer op de werk balk **Annuleren**

---

## <a name="create-child-runs"></a>Onderliggende uitvoeringen maken

Onderliggende uitvoeringen maken om gerelateerde uitvoeringen samen te voegen, zoals voor verschillende afstemming-afstemmings herhalingen.

> [!NOTE]
> Onderliggende uitvoeringen kunnen alleen worden gemaakt met de SDK.

In dit code voorbeeld wordt het `hello_with_children.py` script gebruikt voor het maken van een batch van vijf onderliggende uitgevoerd vanuit een ingediende uitvoering met behulp van de [`child_run()`](/python/api/azureml-core/azureml.core.run%28class%29#child-run-name-none--run-id-none--outputs-none-) methode:

```python
!more hello_with_children.py
src = ScriptRunConfig(source_directory='.', script='hello_with_children.py')

local_run = exp.submit(src)
local_run.wait_for_completion(show_output=True)
print(local_run.get_status())

with exp.start_logging() as parent_run:
    for c,count in enumerate(range(5)):
        with parent_run.child_run() as child:
            child.log(name="Hello from child run", value=c)
```

> [!NOTE]
> Als ze buiten het bereik worden verplaatst, worden onderliggende uitvoeringen automatisch gemarkeerd als voltooid.

Als u veel onderliggende items efficiënt wilt maken, gebruikt u de- [`create_children()`](/python/api/azureml-core/azureml.core.run.run#create-children-count-none--tag-key-none--tag-values-none-) methode. Omdat bij het maken van een netwerk aanroep een batch-uitvoer wordt gemaakt, is het maken van een reeks uitvoeringen efficiënter dan één voor één.

### <a name="submit-child-runs"></a>Onderliggende uitvoeringen verzenden

Onderliggende uitvoeringen kunnen ook worden verzonden vanuit een bovenliggende run. Hierdoor kunt u hiërarchieën van bovenliggende en onderliggende uitvoeringen maken. U kunt geen bovenliggend element zonder onderliggende items maken: zelfs als de bovenliggende run niets doet maar geen onderliggende uitvoeringen start, is het nog steeds nodig om de hiërarchie te maken. De status van alle uitvoeringen is onafhankelijk: een bovenliggend item kan de `"Completed"` status geslaagd hebben, zelfs als een of meer onderliggende uitvoeringen zijn geannuleerd of mislukt.  

Mogelijk wilt u dat uw kind een andere uitvoerings configuratie gebruikt dan de bovenliggende run. U kunt bijvoorbeeld een minder krachtige, op CPU gebaseerde configuratie voor het bovenliggende knoop punt gebruiken, terwijl u op GPU gebaseerde configuraties voor uw kinderen gebruikt. Een andere gang bare wens is om elke onderliggende andere argumenten en gegevens door te geven. Als u een onderliggende uitvoering wilt aanpassen, maakt u een- `ScriptRunConfig` object voor de onderliggende run. De onderstaande code doet het volgende:

- Een compute-resource ophalen die wordt benoemd `"gpu-cluster"` vanuit de werk ruimte `ws`
- Herhaalt de verschillende argument waarden die moeten worden door gegeven aan de onderliggende `ScriptRunConfig` objecten
- Hiermee wordt een nieuwe onderliggende run gemaakt en verzonden met behulp van de aangepaste Compute-resource en het argument
- Blokken totdat alle onderliggende bewerkingen zijn voltooid

```python
# parent.py
# This script controls the launching of child scripts
from azureml.core import Run, ScriptRunConfig

compute_target = ws.compute_targets["gpu-cluster"]

run = Run.get_context()

child_args = ['Apple', 'Banana', 'Orange']
for arg in child_args: 
    run.log('Status', f'Launching {arg}')
    child_config = ScriptRunConfig(source_directory=".", script='child.py', arguments=['--fruit', arg], compute_target=compute_target)
    # Starts the run asynchronously
    run.submit_child(child_config)

# Experiment will "complete" successfully at this point. 
# Instead of returning immediately, block until child runs complete

for child in run.get_children():
    child.wait_for_completion()
```

Gebruik de-methode om veel onderliggende uitvoeringen met identieke configuraties, argumenten en invoer op efficiënte wijze te maken [`create_children()`](/python/api/azureml-core/azureml.core.run.run#create-children-count-none--tag-key-none--tag-values-none-) . Omdat bij het maken van een netwerk aanroep een batch-uitvoer wordt gemaakt, is het maken van een reeks uitvoeringen efficiënter dan één voor één.

Binnen een onderliggende run kunt u de bovenliggende run-ID bekijken:

```python
## In child run script
child_run = Run.get_context()
child_run.parent.id
```

### <a name="query-child-runs"></a>Query's uitvoeren op onderliggende items

Gebruik de-methode om een query uit te voeren op de onderliggende uitvoeringen van een specifiek bovenliggend element [`get_children()`](/python/api/azureml-core/azureml.core.run%28class%29#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) . ``recursive = True``Met het argument kunt u een geneste structuur van onderliggende items en grandchildren opvragen.

```python
print(parent_run.get_children())
```

### <a name="log-to-parent-or-root-run"></a>Aanmelden bij bovenliggend item of uitvoering van hoofdmap

U kunt het `Run.parent` veld gebruiken om toegang te krijgen tot de uitvoering die de huidige onderliggende uitvoering heeft gestart. Een veelvoorkomend gebruik: dit is het geval wanneer u de resultaten van het logboek op één plek wilt consolideren. Houd er rekening mee dat onderliggende worden asynchroon uitgevoerd en dat er geen garantie is dat de volg orde of synchronisatie wordt gewacht op het volt ooien van de onderliggende uitvoering van het bovenliggende item.

```python
# in child (or even grandchild) run

def root_run(self : Run) -> Run :
    if self.parent is None : 
        return self
    return root_run(self.parent)

current_child_run = Run.get_context()
root_run(current_child_run).log("MyMetric", f"Data from child run {current_child_run.id}")

```

## <a name="example-notebooks"></a>Voorbeeldnotebooks

De volgende notitie blokken illustreren de concepten in dit artikel:

* Zie de [API-notebook voor logboek registratie](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api/logging-api.ipynb)voor meer informatie over de logboek registratie-api's.

* Zie voor meer informatie over het beheren van uitvoeringen met de Azure Machine Learning SDK, het [notitie blok voor uitvoeringen beheren](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/manage-runs/manage-runs.ipynb).

## <a name="next-steps"></a>Volgende stappen

* Voor informatie over het vastleggen van metrische gegevens voor uw experimenten, Zie [metrische logboek gegevens tijdens trainings uitvoeringen](how-to-track-experiments.md).
* Zie [Azure machine learning bewaken](monitor-azure-machine-learning.md)voor meer informatie over het bewaken van resources en logboeken van Azure machine learning.

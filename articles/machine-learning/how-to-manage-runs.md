---
title: Trainings runs starten, bewaken en annuleren in Python
titleSuffix: Azure Machine Learning
description: Meer informatie over het starten, bewaken en bijhouden van uw machine learning experiment met de python Azure Machine Learning-SDK.
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
ms.openlocfilehash: 202278d6436439a212dda229f209ab8fb75db596
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752332"
---
# <a name="start-monitor-and-track-run-history"></a>De geschiedenis van de run starten, bewaken en bijhouden 

De [Azure Machine Learning SDK](/python/api/overview/azure/ml/intro)voor Python, [Machine Learning CLI](reference-azure-machine-learning-cli.md)en [Azure Machine Learning-studio](https://ml.azure.com) bieden verschillende methoden om uw runs voor training en experimenten te bewaken, te organiseren en bij te houden. Uw ML-run history is een belangrijk onderdeel van een uitlegbaar en herhaalbaar ML-ontwikkelingsproces.

In dit artikel wordt beschreven hoe u de volgende taken uitvoert:

* Prestaties van uitvoering bewaken.
* Controleer de status van de run per e-mailmelding.
* Runs taggen en zoeken.
* Voeg een beschrijving van de run toe. 
* Voer een zoekopdracht uit in de geschiedenis van uw run. 
* Annuleren of mislukken van runs.
* Onderliggende runs maken.
 

> [!TIP]
> Als u op zoek bent naar informatie over het bewaken van de Azure Machine Learning Service en bijbehorende Azure-services, zie How [to monitor Azure Machine Learning](monitor-azure-machine-learning.md).
> Zie Modelgegevens verzamelen en Bewaken met Application Insights als u op zoek [](how-to-enable-data-collection.md) bent naar informatie over bewakingsmodellen die zijn geïmplementeerd [als](how-to-enable-app-insights.md)webservices IoT Edge modules.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig:

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure Machine Learning](https://aka.ms/AMLFree).

* Een [Azure Machine Learning werkruimte](how-to-manage-workspace.md).

* De Azure Machine Learning SDK voor Python (versie 1.0.21 of hoger). Zie De SDK installeren of bijwerken als u de nieuwste versie van de [SDK wilt installeren](/python/api/overview/azure/ml/install)of bijwerken.

    Gebruik de volgende code om uw versie van Azure Machine Learning SDK te controleren:

    ```python
    print(azureml.core.VERSION)
    ```

* De [Azure CLI en](/cli/azure/?preserve-view=true&view=azure-cli-latest) [CLI-extensie voor Azure Machine Learning.](reference-azure-machine-learning-cli.md)


## <a name="monitor-run-performance"></a>Prestaties van uitvoering bewaken

* Een run en het logboekregistratieproces starten

    # <a name="python"></a>[Python](#tab/python)
    
    1. Stel uw experiment in door de klassen [Werkruimte,](/python/api/azureml-core/azureml.core.workspace.workspace) [Experiment,](/python/api/azureml-core/azureml.core.experiment.experiment) [Uitvoeren](/python/api/azureml-core/azureml.core.run%28class%29)en [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) te importeren uit het [pakket azureml.core.](/python/api/azureml-core/azureml.core)
    
        ```python
        import azureml.core
        from azureml.core import Workspace, Experiment, Run
        from azureml.core import ScriptRunConfig
        
        ws = Workspace.from_config()
        exp = Experiment(workspace=ws, name="explore-runs")
        ```
    
    1. Start een run en het logboekregistratieproces met de [`start_logging()`](/python/api/azureml-core/azureml.core.experiment%28class%29#start-logging--args----kwargs-) methode .
    
        ```python
        notebook_run = exp.start_logging()
        notebook_run.log(name="message", value="Hello from run!")
        ```
        
    # <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
    
    Voer de volgende stappen uit om een experiment uit te voeren:
    
    1. Gebruik de Azure CLI vanuit een shell of opdrachtprompt om te verifiëren bij uw Azure-abonnement:
    
        ```azurecli-interactive
        az login
        ```
        
        [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 
    
    1. Koppel een werkruimteconfiguratie aan de map met uw trainingsscript. Vervang `myworkspace` door uw Azure Machine Learning werkruimte. Vervang `myresourcegroup` door de Azure-resourcegroep die uw werkruimte bevat:
    
        ```azurecli-interactive
        az ml folder attach -w myworkspace -g myresourcegroup
        ```
    
        Met deze opdracht maakt u `.azureml` een subdirectory met voorbeeld runconfig- en conda-omgevingsbestanden. Het bevat ook een `config.json` bestand dat wordt gebruikt om te communiceren met uw Azure Machine Learning werkruimte.
    
        Zie az ml folder attach voor [meer informatie.](/cli/azure/ext/azure-cli-ml/ml/folder?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach)
    
    2. Gebruik de volgende opdracht om de run te starten. Wanneer u deze opdracht gebruikt, geeft u de naam op van het runconfig-bestand (de tekst vóór .runconfig als u naar uw bestandssysteem kijkt) op basis van de \* parameter -c.
    
        ```azurecli-interactive
        az ml run submit-script -c sklearn -e testexperiment train.py
        ```
    
        > [!TIP]
        > Met `az ml folder attach` de opdracht is een `.azureml` subdirectory gemaakt, die twee voorbeeld-runconfig-bestanden bevat.
        >
        > Als u een Python-script hebt dat programmatisch een run configuration-object maakt, kunt u [RunConfig.save()](/python/api/azureml-core/azureml.core.runconfiguration#save-path-none--name-none--separate-environment-yaml-false-) gebruiken om het op te slaan als een runconfig-bestand.
        >
        > Zie voor meer voorbeeldbestanden van runconfig. [https://github.com/MicrosoftDocs/pipelines-azureml/](https://github.com/MicrosoftDocs/pipelines-azureml/)
    
        Zie az [ml run submit-script voor meer informatie.](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script)

    # <a name="studio"></a>[Studio](#tab/azure-studio)

    Voor een voorbeeld van het trainen van een model in de Azure Machine Learning designer, zie [Zelfstudie: Autoprijs voorspellen met de ontwerpfunctie](tutorial-designer-automobile-price-train-score.md).

    ---

* De status van een run controleren

    # <a name="python"></a>[Python](#tab/python)
    
    * Haal de status van een run op met de [`get_status()`](/python/api/azureml-core/azureml.core.run%28class%29#get-status--) methode .
    
        ```python
        print(notebook_run.get_status())
        ```
    
    * Gebruik de methode om de uitvoerings-id, uitvoeringstijd en andere details over de uitvoering op te [`get_details()`](/python/api/azureml-core/azureml.core.workspace.workspace#get-details--) halen.
    
        ```python
        print(notebook_run.get_details())
        ```
    
    * Wanneer de run is voltooid, gebruikt u de [`complete()`](/python/api/azureml-core/azureml.core.run%28class%29#complete--set-status-true-) methode om deze te markeren als voltooid.
    
        ```python
        notebook_run.complete()
        print(notebook_run.get_status())
        ```
    
    * Als u het ontwerppatroon van Python gebruikt, wordt de uitvoering automatisch als voltooid markeren wanneer de `with...as` uitvoering buiten het bereik valt. U hoeft de run niet handmatig als voltooid te markeren.
        
        ```python
        with exp.start_logging() as notebook_run:
            notebook_run.log(name="message", value="Hello from run!")
            print(notebook_run.get_status())
        
        print(notebook_run.get_status())
        ```
    
    # <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
    
    * Gebruik de volgende opdracht om een lijst met runs voor uw experiment weer te geven. Vervang `experiment` door de naam van uw experiment:
    
        ```azurecli-interactive
        az ml run list --experiment-name experiment
        ```
    
        Deze opdracht retourneert een JSON-document met informatie over de runs voor dit experiment.
    
        Zie az [ml experiment list voor meer informatie.](/cli/azure/ext/azure-cli-ml/ml/experiment?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list)
    
    * Gebruik de volgende opdracht om informatie over een specifieke run weer te geven. Vervang `runid` door de id van de run:
    
        ```azurecli-interactive
        az ml run show -r runid
        ```
    
        Deze opdracht retourneert een JSON-document met informatie over de run.
    
        Zie az [ml run show voor meer informatie.](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-show)
    
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    Uw runs weergeven in de studio: 
    
    1. Navigeer naar **het tabblad Experimenten.**
    
    1. Selecteer Alle **experimenten om alle** runs in een experiment te bekijken of selecteer **Alle** runs om alle in de werkruimte verzonden runs te bekijken.
    
        Op de **pagina Alle runs kunt** u de lijst met runs filteren op tags, experimenten, rekendoel en meer om uw werk beter te organiseren en te bereiken.  
    
    1. Breng aanpassingen aan op de pagina door runs te selecteren om grafieken te vergelijken, toe te voegen of filters toe te passen. Deze wijzigingen kunnen worden opgeslagen als **een aangepaste weergave,** zodat u eenvoudig kunt terugkeren naar uw werk. Gebruikers met werkruimtemachtigingen kunnen de aangepaste weergave bewerken of weergeven. Deel ook de aangepaste weergave met teamleden voor verbeterde samenwerking door **Weergave delen te selecteren.**   
    
        :::image type="content" source="media/how-to-manage-runs/custom-views.gif" alt-text="Schermopname: een aangepaste weergave maken":::
    
    1. Als u de runlogboeken wilt weergeven, selecteert u een specifieke run en op het tabblad **Uitvoer en** logboeken vindt u diagnostische logboeken en foutlogboeken voor uw run.
    
    ---

## <a name="monitor-the-run-status-by-email-notification"></a>De status van de run controleren via e-mailmelding

1. Selecteer in [Azure Portal](https://ms.portal.azure.com/)in de linkernavigatiebalk het **tabblad** Controleren. 

1. Selecteer **Diagnostische instellingen** en selecteer **vervolgens + Diagnostische instelling toevoegen.**

    ![Schermopname van diagnostische instellingen voor e-mailmeldingen](./media/how-to-manage-runs/diagnostic-setting.png)

1. In de diagnostische instelling 
    1. selecteer onder **Categoriedetails** de **optie AmlRunStatusChangedEvent**. 
    1. Selecteer in **Doeldetails** de werkruimte **Verzenden naar Log Analytics en** geef de werkruimte **Abonnement** en Log **Analytics op.** 

    > [!NOTE]
    > De **Azure Log Analytics-werkruimte** is een ander type Azure-resource dan **Azure Machine Learning Service werkruimte**. Als er geen opties in die lijst staan, kunt u [een Log Analytics-werkruimte maken.](../azure-monitor/logs/quick-create-workspace.md) 
    
    ![Waar kunt u een e-mailmelding opslaan?](./media/how-to-manage-runs/log-location.png)

1. Voeg op **het tabblad** Logboeken een **nieuwe waarschuwingsregel toe.** 

    ![Nieuwe waarschuwingsregel](./media/how-to-manage-runs/new-alert-rule.png)

1. Zie [logboekwaarschuwingen maken en beheren met behulp van Azure Monitor.](../azure-monitor/alerts/alerts-log.md)

## <a name="run-description"></a>Beschrijving van de run 

Een beschrijving van de run kan worden toegevoegd aan een run om meer context en informatie te bieden voor de run. U kunt ook zoeken op deze beschrijvingen in de lijst met runs en de beschrijving van de run toevoegen als een kolom in de lijst met runs. 

Navigeer naar **de pagina Details** uitvoeren voor uw run en selecteer het bewerkings- of potloodpictogram om beschrijvingen voor de run toe te voegen, te bewerken of te verwijderen. Als u de wijzigingen in de lijst met runs wilt persistent maken, moet u de wijzigingen in uw bestaande aangepaste weergave of een nieuwe aangepaste weergave opslaan. De Markdown-indeling wordt ondersteund voor run-beschrijvingen, zodat afbeeldingen kunnen worden ingesloten en deep linking zoals hieronder wordt weergegeven.

:::image type="content" source="media/how-to-manage-runs/run-description.gif" alt-text="Schermopname: een beschrijving van de run maken"::: 

## <a name="tag-and-find-runs"></a>Runs taggen en zoeken

In Azure Machine Learning kunt u eigenschappen en tags gebruiken om uw runs te organiseren en op te vragen voor belangrijke informatie.

* Eigenschappen en tags toevoegen

    # <a name="python"></a>[Python](#tab/python)
    
    Als u doorzoekbare metagegevens wilt toevoegen aan uw runs, gebruikt u de [`add_properties()`](/python/api/azureml-core/azureml.core.run%28class%29#add-properties-properties-) methode . Met de volgende code wordt bijvoorbeeld de eigenschap `"author"` toegevoegd aan de run:
    
    ```Python
    local_run.add_properties({"author":"azureml-user"})
    print(local_run.get_properties())
    ```
    
    Eigenschappen zijn onveranderbaar, zodat ze een permanente record maken voor controledoeleinden. Het volgende codevoorbeeld resulteert in een fout, omdat we al als de eigenschapswaarde `"azureml-user"` in de voorgaande code hebben `"author"` toegevoegd:
    
    ```Python
    try:
        local_run.add_properties({"author":"different-user"})
    except Exception as e:
        print(e)
    ```
    
    In tegenstelling tot eigenschappen zijn tags veranderlijk. Gebruik de methode om doorzoekbare en zinvolle informatie toe te voegen voor gebruikers van uw [`tag()`](/python/api/azureml-core/azureml.core.run%28class%29#tag-key--value-none-) experiment.
    
    ```Python
    local_run.tag("quality", "great run")
    print(local_run.get_tags())
    
    local_run.tag("quality", "fantastic run")
    print(local_run.get_tags())
    ```
    
    U kunt ook eenvoudige tekenreekstags toevoegen. Wanneer deze tags in de tagwoordenlijst als sleutels worden weergegeven, hebben ze de waarde `None` .
    
    ```Python
    local_run.tag("worth another look")
    print(local_run.get_tags())
    ```
    
    # <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
    
    > [!NOTE]
    > Met de CLI kunt u alleen tags toevoegen of bijwerken.
    
    Gebruik de volgende opdracht om een tag toe te voegen of bij te werken:
    
    ```azurecli-interactive
    az ml run update -r runid --add-tag quality='fantastic run'
    ```
    
    Zie az [ml run update voor meer informatie.](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-update)
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    U kunt runtags toevoegen, bewerken of verwijderen vanuit de studio. Navigeer naar **de pagina Details** uitvoeren voor uw run en selecteer het bewerkings- of potloodpictogram om tags voor uw runs toe te voegen, te bewerken of te verwijderen. U kunt deze tags ook zoeken en filteren op de pagina met de lijst met runs.
    
    :::image type="content" source="media/how-to-manage-runs/run-tags.gif" alt-text="Schermopname: Runtags toevoegen, bewerken of verwijderen":::
    
    ---

* Query-eigenschappen en -tags

    U kunt query's uitvoeren binnen een experiment om een lijst met runs te retourneren die overeenkomen met specifieke eigenschappen en tags.

    # <a name="python"></a>[Python](#tab/python)
    
    ```Python
    list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
    list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
    ```
    
    # <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
    
    De Azure CLI ondersteunt [JMESPath-query's,](http://jmespath.org) die kunnen worden gebruikt om runs te filteren op basis van eigenschappen en tags. Als u een JMESPath-query wilt gebruiken met de Azure CLI, geeft u deze op met de `--query` parameter . In de volgende voorbeelden worden enkele query's met eigenschappen en tags weergeven:
    
    ```azurecli-interactive
    # list runs where the author property = 'azureml-user'
    az ml run list --experiment-name experiment [?properties.author=='azureml-user']
    # list runs where the tag contains a key that starts with 'worth another look'
    az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
    # list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
    az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
    ```
    
    Zie Query's uitvoeren op Azure CLI-opdrachtuitvoer voor meer informatie over het uitvoeren van [query's op Azure CLI-resultaten.](/cli/azure/query-azure-cli?preserve-view=true&view=azure-cli-latest)
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    Als u wilt zoeken naar specifieke runs, gaat u naar de **lijst Alle runs.** Hier hebt u twee opties:
    
    1. Gebruik de **knop Filter toevoegen** en selecteer Filteren op tags om uw runs te filteren op tag die is toegewezen aan de run(s). <br><br>
    OF
    
    1. Gebruik de zoekbalk om snel runs te vinden door te zoeken op de metagegevens van de run, zoals de status van de run, beschrijvingen, experimentnamen en de naam van de inzender. 
    
## <a name="cancel-or-fail-runs"></a>Annuleren of mislukken van runs

Als u een fout ziet of als het te lang duurt om de run te voltooien, kunt u de run annuleren.

# <a name="python"></a>[Python](#tab/python)

Als u een run wilt annuleren met behulp van de SDK, gebruikt u de [`cancel()`](/python/api/azureml-core/azureml.core.run%28class%29#cancel--) methode :

```python
src = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')
local_run = exp.submit(src)
print(local_run.get_status())

local_run.cancel()
print(local_run.get_status())
```

Als de run is uitgevoerd, maar deze een fout bevat (bijvoorbeeld het onjuiste trainingsscript is gebruikt), kunt u de methode gebruiken om deze te markeren [`fail()`](/python/api/azureml-core/azureml.core.run%28class%29#fail-error-details-none--error-code-none---set-status-true-) als mislukt.

```python
local_run = exp.submit(src)
local_run.fail()
print(local_run.get_status())
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u een run wilt annuleren met behulp van de CLI, gebruikt u de volgende opdracht. Vervang `runid` door de id van de run

```azurecli-interactive
az ml run cancel -r runid -w workspace_name -e experiment_name
```

Zie az [ml run cancel voor meer informatie.](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-cancel)

# <a name="studio"></a>[Studio](#tab/azure-studio)

Voer de volgende stappen uit om een run in de studio te annuleren:

1. Ga naar de pijplijn die wordt uitgevoerd in **de sectie Experimenten** of **Pijplijnen.** 

1. Selecteer het pijplijnrunnummer dat u wilt annuleren.

1. Selecteer Annuleren op de **werkbalk**

---

## <a name="create-child-runs"></a>Onderliggende runs maken

Onderliggende runs maken om gerelateerde runs te groeperen, zoals voor verschillende hyperparameter-afstemmings iteraties.

> [!NOTE]
> Onderliggende runs kunnen alleen worden gemaakt met behulp van de SDK.

In dit codevoorbeeld wordt het script gebruikt om een batch van vijf onderliggende runs te maken vanuit een `hello_with_children.py` ingediende run met behulp van de methode [`child_run()`](/python/api/azureml-core/azureml.core.run%28class%29#child-run-name-none--run-id-none--outputs-none-) :

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
> Wanneer onderliggende runs buiten het bereik vallen, worden ze automatisch gemarkeerd als voltooid.

Als u veel onderliggende runs efficiënt wilt maken, gebruikt u de [`create_children()`](/python/api/azureml-core/azureml.core.run.run#create-children-count-none--tag-key-none--tag-values-none-) methode . Omdat elke aanroep resulteert in een netwerk-aanroep, is het efficiënter om een batch met runs te maken dan ze een voor een te maken.

### <a name="submit-child-runs"></a>Onderliggende runs verzenden

Onderliggende runs kunnen ook worden verzonden vanuit een bovenliggende run. Hiermee kunt u hiërarchieën maken van bovenliggende en onderliggende runs. U kunt geen onderliggende run zonder bovenliggende gegevens maken: zelfs als de bovenliggende run alleen onderliggende runs start, is het nog steeds nodig om de hiërarchie te maken. De statussen van alle runs zijn onafhankelijk: een bovenliggend abonnement kan de status Geslaagd hebben, zelfs als een of meer onderliggende runs `"Completed"` zijn geannuleerd of mislukt.  

Mogelijk wilt u dat uw onderliggende uitvoeringen een andere uitvoeringsconfiguratie gebruiken dan de bovenliggende uitvoering. U kunt bijvoorbeeld een minder krachtige, op CPU gebaseerde configuratie gebruiken voor het bovenliggende exemplaar, terwijl u gpu-configuraties gebruikt voor uw kinderen. Een andere veelvoorkomende wens is om alle onderliggende verschillende argumenten en gegevens door te geven. Als u een onderliggende run wilt aanpassen, maakt u `ScriptRunConfig` een -object voor de onderliggende run. 

> [!IMPORTANT]
> Als u een onderliggende run wilt verzenden vanaf een bovenliggende run op een externe berekening, moet u zich eerst aanmelden bij de werkruimte in de bovenliggende runcode. Het contextobject voor de run in een externe run heeft standaard geen referenties voor het verzenden van onderliggende runs. Gebruik een service-principal of referenties voor beheerde identiteit om u aan te melden. Zie Verificatie instellen voor meer informatie [over verificatie.](how-to-setup-authentication.md)

De onderstaande code:

- Haalt een rekenresource met de `"gpu-cluster"` naam op uit de werkruimte `ws`
- Itereert verschillende argumentwaarden die moeten worden doorgegeven aan de objecten van de `ScriptRunConfig` kinderen
- Hiermee maakt en verstuurt u een nieuwe onderliggende run met behulp van de aangepaste rekenresource en het aangepaste argument
- Blokkeert totdat alle onderliggende runs zijn voltooid

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

Als u veel onderliggende uitvoeringen met identieke configuraties, argumenten en invoer efficiënt wilt maken, gebruikt u de [`create_children()`](/python/api/azureml-core/azureml.core.run.run#create-children-count-none--tag-key-none--tag-values-none-) methode . Omdat elke aanmaak resulteert in een netwerkoproep, is het maken van een batch met runs efficiënter dan het maken ervan een voor een.

Binnen een onderliggende run kunt u de bovenliggende run-id weergeven:

```python
## In child run script
child_run = Run.get_context()
child_run.parent.id
```

### <a name="query-child-runs"></a>Onderliggende query's uitvoeren

Als u een query wilt uitvoeren op de onderliggende runs van een specifiek bovenliggend, gebruikt u de [`get_children()`](/python/api/azureml-core/azureml.core.run%28class%29#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) methode . Met ``recursive = True`` het argument kunt u een query uitvoeren op een geneste structuur van kinderen en grootten.

```python
print(parent_run.get_children())
```

### <a name="log-to-parent-or-root-run"></a>Aanmelden bij bovenliggende of hoofdmap

U kunt het veld gebruiken `Run.parent` voor toegang tot de run die de huidige onderliggende run heeft gestart. Een veelvoorkomende use-case voor het `Run.parent` gebruik van is het combineren van logboekresultaten op één plek. Onderliggende uitvoeringen worden asynchroon uitgevoerd en er is geen garantie voor volgorde of synchronisatie, behalve dat de bovenliggende taak kan wachten tot de onderliggende uitvoeringen zijn voltooid.

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

In de volgende notebooks worden de concepten in dit artikel gedemonstreerd:

* Zie het [logboekregistratie-API-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api/logging-api.ipynb)voor meer informatie over de API's voor logboekregistratie.

* Zie voor meer informatie over het beheren van runs met Azure Machine Learning SDK het [notebook voor het beheren van runs.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/manage-runs/manage-runs.ipynb)

## <a name="next-steps"></a>Volgende stappen

* Zie Metrische logboekgegevens tijdens trainings runs voor meer informatie over het logboeken van [metrische gegevens voor uw experimenten.](how-to-track-experiments.md)
* Zie Bewaking van resources en logboeken van Azure Machine Learning voor meer informatie [Azure Machine Learning.](monitor-azure-machine-learning.md)